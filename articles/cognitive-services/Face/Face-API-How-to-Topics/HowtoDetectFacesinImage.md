---
title: Arcok észlelése a képen - Arc
titleSuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, hogyan használhatja az arcfelismerést olyan attribútumok kinyerésére, mint a nem, az életkor vagy az adott képből való póz.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169878"
---
# <a name="get-face-detection-data"></a>Arcfelismerési adatok beszereznie

Ez az útmutató bemutatja, hogyan használhatja az arcfelismerést olyan attribútumok kinyerésére, mint a nem, az életkor vagy az adott képből való póz. Az ebben az útmutatóban szereplő kódrészletek C# nyelven vannak megírva az Azure Cognitive Services Face ügyfélkódtár használatával. Ugyanez a funkció érhető el a [REST API-n](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)keresztül is.

Ez az útmutató bemutatja, hogyan:

- Az arcok helyeése és méretei a képen.
- A különböző arcjellegzetes ségeket, például a tanulókat, az orrot és a szájat egy képen kaphatja meg.
- Találd meg a nemét, korát, érzelmét és egyéb tulajdonságait az észlelt arc.

## <a name="setup"></a>Telepítés

Ez az útmutató feltételezi, hogy már készített `faceClient`egy [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) objektumot, amelynek neve Face-előfizetési kulccsal és végpont URL-címével rendelkezik. Innen használhatja az arcfelismerő funkciót a [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)( az ebben az útmutatóban használt ) vagy a [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)hívásával. A szolgáltatás beállításával kapcsolatos tudnivalókért kövesse az egyik rövid útmutatót.

Ez az útmutató a hívás észlelése sajátosságaira összpontosít, például arra, hogy milyen argumentumokat adhat át, és mit tehet a visszaadott adatokkal. Azt javasoljuk, hogy csak a szükséges funkciókat keresse le. Minden művelet végrehajtásához több időre van szükség.

## <a name="get-basic-face-data"></a>Alapvető arcadatok beszerezni

Az arcok megkereséséhez és a helyük képbe való bekerüléséhez hívja meg a metódust úgy, hogy a _returnFaceId_ paraméter **értéke igaz**legyen. Ez az alapértelmezett beállítás.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Lekérdezheti a visszaadott [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) objektumokat az egyedi azonosítóikról és egy téglalapról, amely megadja az arc képpontkoordinátáit.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Az arc helyének és méretének elemzésével kapcsolatos további tudnivalókért olvassa el a [FaceRectangle című témakört.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet) Általában ez a téglalap tartalmazza a szemeket, a szemöldököt, az orrot és a szájat. A fej, a fül ek és az áll nem feltétlenül szerepelnek benne. Ha az arc téglalapjával egy teljes fejet vághat le, vagy egy közepes méretű portrét szeretne kapni, például fényképes azonosító típusú kép esetén, mindkét irányban kibonthatja a téglalapot.

## <a name="get-face-landmarks"></a>Arctereptárgyak beszereznie

[Face tereptárgyak](../concepts/face-detection.md#face-landmarks) egy sor könnyen megtalálható pontokat az arcon, mint például a diákok vagy az orr csúcsa. Az arctájéki mérföldkő adatainak lehívásához állítsa a _returnFaceLandmarks_ paramétert **true**értékre.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

A következő kód bemutatja, hogyan lehet letölteni a helyét az orr és a diákok:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

A face tereptárgyak adataival pontosan kiszámíthatja a lap irányát. Megadhatja például az arc elforgatását vektorként a száj közepétől a szem középpontjáig. A következő kód kiszámítja ezt a vektort:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Ha ismeri az arc irányát, elforgathatja a téglalap alakú arckeretet, hogy megfelelőbb legyen. A kép lapjainak körülvágásához programozott módon elforgathatja a képet, hogy az arcok mindig függőlegesen jelenjenek meg.

## <a name="get-face-attributes"></a>Arcattribútumok beszereznie

Az arctéglalapok és a tereptárgyak mellett az arcfelismerési API az arc több fogalmi jellemzőjének elemzésére is alkalmas. A teljes listát a [Face attribútumok](../concepts/face-detection.md#attributes) fogalmi szakaszában található.

A face attribútumok elemzéséhez állítsa be a _returnFaceAttributes_ paramétert a [FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) értékek listájára.

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Ezután a visszaadott adatokra mutató hivatkozásokat kaphat, és az igényeinek megfelelően további műveleteket végez.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Az egyes attribútumokról az [Arcfelismerés és](../concepts/face-detection.md) az attribútumok fogalmi útmutatójában olvashat bővebben.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan használhatja az arcfelismerés különböző funkcióit. Ezután integrálja ezeket a funkciókat az alkalmazásba egy részletes oktatóanyag követésével.

- [Oktatóanyag: WpF-alkalmazás létrehozása a kép arcadatainak megjelenítéséhez](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Oktatóanyag: Android-alkalmazás készítése képeken lévő arcok észleléséhez és bekeretezéséhez](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Referenciadokumentáció (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenciadokumentáció (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)