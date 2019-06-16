---
title: Arcfelismerés a kép – Face API
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az különböző, a face észlelési szolgáltatás által visszaadott adatokat.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124546"
---
# <a name="get-face-detection-data"></a>Face észlelési adatok lekérése

Ez az útmutató bemutatja, hogyan használja az arcfelismerés nemét, korra és testtartás-attribútumok kibontani a megadott lemezkép. Ebben az útmutatóban foglalt kódrészletek nyelven írták C# az Azure Cognitive Services Face API ügyféloldali kódtár használatával. Ugyanazokat a funkciókat keresztül érhető el a [REST API-val](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Ez az útmutató bemutatja, hogyan való:

- Kérje le a helyek és az arcok méretei képet.
- A helyek, a különböző arcrészek, például a tanulók, orrot és szájához beszéd, a képet kaphat.
- Kitalálni a nemek, kor, érzelem és egy felismert arc egyéb attribútumai.

## <a name="setup"></a>Beállítás

Ez az útmutató feltételezi, hogy Ön már felépített egy [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) nevű objektum `faceClient`, Arcfelismerési előfizetési kulcs és a végpont URL-címet. Itt használhatja a face észlelés funkció hívása, vagy [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), ebben az útmutatóban használt vagy [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Ez a szolgáltatás beállítása az utasításokért lásd: a [hibakeresés gyors útmutató: arcokat C# ](../quickstarts/csharp-detect-sdk.md).

Ez az útmutató elsősorban a hibakeresés hívás tulajdonságairól, például milyen argumentumokat adhat át, és mit tehet a visszaadott adatok. Azt javasoljuk, hogy lekérdezése a szükséges funkciót. Minden művelet befejezéséhez további időt vesz igénybe.

## <a name="get-basic-face-data"></a>Alapszintű face adatok lekérése

Arcok keresése és a egy képet a helyüket, a metódust hívja a _returnFaceId_ paraméter beállítása **igaz**. Ez az alapértelmezett beállítás.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

A visszaadott lekérdezheti [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) objektumokat a saját egyedi azonosítóját és a egy téglalapot, amely lehetővé teszi a face képpontos koordinátáit.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

A hely és a face méretei elemezni információkért lásd: [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Adott téglalapban általában a szemet, szemöldök, orrot és szájához beszéd tartalmazza. A fő füleken és chin tetején nem feltétlenül tartalmazzák. Használja a négyszög meghatározása vágja körül, egy teljes head, vagy szerezze be a képernyőkép-készítés közben álló talán egy fénykép-típusa képre vonatkozóan, bontsa ki a négyszög minden irányban.

## <a name="get-face-landmarks"></a>Arcrészek beolvasása

[Nyomtatandó oldallal arcrész](../concepts/face-detection.md#face-landmarks) könnyen megtalálható pontok a egy oldallal, például a tanulók vagy az én csúcsa gyűjteménye. Face tereptárgyak adatokat kíván, állítsa be a _returnFaceLandmarks_ paramétert **igaz**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

A következő kód bemutatja, hogyan lehet, hogy kérheti le a orrot és a tanulók a helyek:

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

Face arcrész adatok pontos kiszámításához a face irányának is használhatja. Elforgatási szögét a face megadhatja például, mint a szemet közepére a szájához beszéd, a központ egy vektoros. A következő kódot a vektoros számítja ki:

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

Ha tudja, hogy a face irányát, több megfelelően igazítjuk azt a téglalap alakú face keret elfordításával. Arcok a képen levágni, programozott módon elfordításával a képet úgy, hogy az mindig jelzése idővonalon függőleges.

## <a name="get-face-attributes"></a>Face-attribútumainak lekérése

Arcjelző négyszögek és tereptárgyak felismerése, mellett az arcfelismerés API fogalmi attribútumai a face is elemezhet. A teljes listát lásd: a [attribútumok arc](../concepts/face-detection.md#attributes) fogalmi szakaszban.

Arctulajdonságok elemzéséhez, állítsa be a _returnFaceAttributes_ paraméter listájára [FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) értékeket.

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

Ezután a visszaadott adatok referenciák beszerzése és szükség szerint további műveleteket.

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

Attribútumait kapcsolatos további információkért tekintse meg a [Arcfelismerés és attribútumok](../concepts/face-detection.md) fogalmi útmutató.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan használhatja a különféle funkciók biztosításához az arcfelismerés. Ezután integrálja ezeket a funkciókat az alkalmazás egy részletes oktatóanyag utasításait követve.

- [Oktatóanyag: Face adatok megjelenítéséhez a képet a WPF-alkalmazás létrehozása](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Oktatóanyag: Keret arcok a képet, és Android-alkalmazás létrehozása](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Kapcsolódó témakörök

- [(REST) dokumentációja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [(.NET SDK-t) dokumentációja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)