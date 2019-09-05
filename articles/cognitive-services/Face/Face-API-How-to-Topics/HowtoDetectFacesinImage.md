---
title: Arcok észlelése egy képen – Face API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a Arcfelismerés funkció által visszaadott különféle információkat.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 07089def3290d6d64919ad2551de7584646cc681
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306636"
---
# <a name="get-face-detection-data"></a>Arcfelismerés-adatlekérdezés

Ez az útmutató bemutatja, hogyan használható a Arcfelismerés olyan attribútumok kinyeréséhez, mint a nemek, az életkor vagy az adott rendszerképből származó adatok. Az útmutatóban szereplő kódrészletek az Azure Cognitive Services Face API C# ügyféloldali kódtár használatával íródnak. Ugyanez a funkció a [Rest APIon](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)keresztül érhető el.

Ez az útmutató a következőket mutatja be:

- Az arcok helyeinek és méreteinek beolvasása egy képben.
- Különböző arc-tereptárgyak (például tanulók, orr és száj) helyeinek beszerzése egy képben.
- Kitalálhatja az észlelt arc nemet, korát, érzelemét és egyéb attribútumait.

## <a name="setup"></a>Beállítás

Ez az útmutató feltételezi, hogy már létrehozta a nevű `faceClient` [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) -objektumot egy Face előfizetési kulccsal és egy végpont URL-címmel. Innen a Arcfelismerés funkciót az útmutatóban vagy a [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)használt [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)meghívásával is használhatja. A szolgáltatás beállításával kapcsolatos utasításokért kövesse az egyik rövid útmutatót.

Ez az útmutató az észlelési hívás sajátosságait mutatja be, például azt, hogy milyen argumentumokat adhat át, és hogy mit tehet a visszaadott adatmennyiséggel. Javasoljuk, hogy csak a szükséges szolgáltatásokat kérdezze le. Az egyes műveletek végrehajtása további időt vesz igénybe.

## <a name="get-basic-face-data"></a>Alapszintű Face-adatok beolvasása

Az arcok megkereséséhez és a helyüknek a rendszerképben való lekéréséhez hívja meg a metódust az _returnFaceId_ paraméter **true**értékre állításával. Ez az alapértelmezett beállítás.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Lekérdezheti a visszaadott [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) objektumokat egyedi azonosítójuk és egy olyan négyszög között, amely az arc képpontjának koordinátáit adja meg.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

További információ az arc helyének és méreteinek elemzéséről: [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Ez a négyszög általában a szemet, a szemöldökét, az orrát és a száját tartalmazza. A fej, a fülek és az álla teteje nem feltétlenül szerepel. Ha a Face téglalapot szeretné használni egy teljes fej kivágásához vagy egy közép-shot portré létrehozásához, például egy fénykép-azonosító típusú képhez, kibonthatja a téglalapot az egyes irányokban.

## <a name="get-face-landmarks"></a>Face-tereptárgyak beolvasása

Az [arc tereptárgyak](../concepts/face-detection.md#face-landmarks) könnyen megtalált pontok, például a tanulók és az orr hegye. A tereptárgyak beszerzéséhez állítsa a _returnFaceLandmarks_ paramétert **true (igaz**) értékre.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

A következő kód bemutatja, hogyan kérheti le az orr és a tanulók helyét:

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

A Face tereptárgyak adataival az arc irányának pontos kiszámításához is használható. Például megadhatja az arc elforgatását vektorként a száj közepétől a szem közepéig. A következő kód kiszámítja ezt a vektort:

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

Ha ismeri az arc irányát, elforgathatja a téglalap alakú keretet úgy, hogy az megfelelően illeszkedjen. A képeken lévő arcok kivágásához programozott módon forgathatja el a képet úgy, hogy az arcok mindig egyenesen jelenjenek meg.

## <a name="get-face-attributes"></a>Arc attribútumainak beolvasása

A Face észlelési API-k mellett az arc téglalapok és a tereptárgyak is elemezhetők az arc számos fogalmi attribútuma. A teljes listát a [Face attributes](../concepts/face-detection.md#attributes) fogalmi szakasza tartalmazza.

A Face attribútumok elemzéséhez állítsa a _returnFaceAttributes_ paramétert a [FaceAttributeType enumerálási](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) értékek listájára.

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

Ezután szerezzen be a visszaadott adatokra mutató hivatkozásokat, és tegye meg az igényeinek megfelelő további műveleteket.

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

Ha többet szeretne megtudni az egyes attribútumokról, tekintse meg a [arcfelismerés és attribútumok](../concepts/face-detection.md) fogalmi útmutatót.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan használhatja a Arcfelismerés különböző funkcióit. Ezután integrálja ezeket a funkciókat az alkalmazásba egy részletes oktatóanyag követésével.

- [Oktatóanyag: Egy WPF-alkalmazás létrehozása egy Rendszerképbeli Arcfelismerés megjelenítéséhez](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Oktatóanyag: Android-alkalmazás létrehozása az arcok észleléséhez és a képek keretének megjelenítéséhez](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Dokumentáció (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Hivatkozási dokumentáció (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)