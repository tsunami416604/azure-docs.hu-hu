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
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588771"
---
# <a name="get-face-detection-data"></a>Face észlelési adatok lekérése

Ez az útmutató mutatják be, hogyan használja az arcfelismerés nemét, korra és testtartás-attribútumok kibontani a megadott lemezkép. Ebben az útmutatóban foglalt kódrészletek nyelven írták C# használatával, a Face API ügyféloldali kódtár, de ugyanezeket a funkciókat is elérhetővé válnak a [REST API-t](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Ez az útmutató bemutatja, hogyan való:

- Kérje le a helyek és az arcok méretei képet.
- Helyeinek különböző arcrészek (tanulók, orrot, szájához beszéd és így tovább) a képet kaphat.
- Kitalálni a nemek, kor, és érzelmeket és a egy felismert arc egyéb attribútumai.

## <a name="setup"></a>Beállítás

Ez az útmutató feltételezi, hogy Ön rendelkezik már meglévő, egy **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** nevű objektum `faceClient`, Arcfelismerési előfizetési kulcs és a végpont URL-címet. Itt használhatja a face észlelés funkció hívása, vagy **[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** (ebben az útmutatóban használt) vagy **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)**. Tekintse meg a [arcokat észleli a rövid útmutatóban a C# ](../quickstarts/csharp-detect-sdk.md) beállítására vonatkozó útmutatást.

Ez az útmutató a hibakeresés hívás tulajdonságairól összpontosítanak&mdash;milyen argumentumokat adhat át, és mit tehet a visszaadott adatokkal. Azt javasoljuk, hogy csak a kérdezi le van szüksége, szolgáltatások, az egyes műveletek végrehajtásához további időt igényel.

## <a name="get-basic-face-data"></a>Alapszintű face adatok lekérése

Arcok keresése és a egy képet a helyüket, a metódust hívja a _returnFaceId_ paraméter beállítása **igaz** (alapértelmezett).

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

A visszaadott **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** objektumok egyedi azonosítók és a egy téglalapot, amely lehetővé teszi a face képpontos koordinátáit lehet lekérdezni.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Lásd: **[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)** elemezni a hely és a face méretei olvashat. Általában a téglalap a szemet, szemöldök, orrot és szájához beszéd; tartalmaz-e a fő füleken és chin tetején nem feltétlenül szerepelnek. Ha szeretne használni a négyszög meghatározása egy teljes átjáró- vagy közepes képernyőkép-készítés álló (egy fénykép azonosító típusa image) levágni, érdemes bontsa ki a téglalap által egy bizonyos árrése az összes csatlakozás minden irányában.

## <a name="get-face-landmarks"></a>Arcrészek beolvasása

Arcrészek egy könnyen megtalálható halmaza, például a tanulók a óraszámlapján vagy orrot csúcsa. Beállításával face tereptárgyak adatokat kaphat a _returnFaceLandmarks_ paramétert **igaz**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Alapértelmezés szerint 27 előre meghatározott arcpont van. A következő ábrán látható összes 27 pont:

![Face ábra. Ez az összes 27 arcrész címkével](../Images/landmarks.1.jpg)

A visszaadott pontok képpont, csakúgy, mint a face téglalap keret egysége. A következő kód bemutatja, hogyan lehet, hogy kérheti le a orrot és a tanulók a helyek:

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

Face arcrész adatokat is a face irányának pontos kiszámításához használható. Ha például a szemet közepén a szájához beszéd, a központ egy vektor, elforgatási szögét a face is meghatározzuk. Az alábbi kódot a vektoros számítja ki:

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

A face irányának ismerete, forgatható a téglalap alakú face keret több megfelelően igazítjuk azt. Ha azt szeretné, az arcokat a kép levágása, programozott módon elfordításával a képet úgy, hogy az mindig jelzése idővonalon függőleges.

## <a name="get-face-attributes"></a>Face-attribútumainak lekérése

Arcjelző négyszögek és tereptárgyak felismerése, mellett az arcfelismerés API fogalmi attribútumai a face is elemezhet. Ezek a következők:

- Kor
- Nem
- Mosoly intenzitása
- Arcszőrzet
- Szemüveg
- 3D fő testtartás
- Érzelemfelismerés

> [!IMPORTANT]
> Ezek az attribútumok vannak előre meghatározott statisztikai algoritmusokkal, és előfordulhat, hogy nem pontos. Körültekintően járjon el, ha az attribútum adatokon alapuló döntéseket.
>

Arctulajdonságok elemzéséhez, állítsa be a _returnFaceAttributes_ paraméter listájára **[FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** értékeket.

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

Ezt követően a visszaadott adatokhoz való hivatkozás, és végezze el a további az igényeinek megfelelően.

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

Az attribútumok mindegyike kapcsolatos további információkért tekintse meg a [szószedet](../Glossary.md).

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan használhatja a különféle funkciók biztosításához az arcfelismerés. Ezután tekintse meg a [szószedet](../Glossary.md) a részletesebb tekintse meg a már lekért arcfelismerési adatokat.

## <a name="related-topics"></a>Kapcsolódó témakörök

- [(REST) dokumentációja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [(.NET SDK-t) dokumentációja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
