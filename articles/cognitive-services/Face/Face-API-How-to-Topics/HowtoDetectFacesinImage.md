---
title: 'Példa: Arcfelismerés a képeken – Face API'
titleSuffix: Azure Cognitive Services
description: A Face API használatával felismerheti az arcokat a képeken.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: a4c74ff70a4426abf97562bf997479a91afbf17a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124048"
---
# <a name="example-how-to-detect-faces-in-image"></a>Példa: Hogyan lehet arcokat felismerni a képen

Ez az útmutató bemutatja, hogyan lehet képeken arcokat felismerni és kiemelni hozzá az olyan tulajdonságokat, mint a nem, életkor, testtartás. A példák a Face API ügyfélkódtár használatával C# nyelven íródtak. 

## <a name="concepts"></a>Alapelvek

Ha nem ismeri az útmutatóban használt valamely alábbi fogalmat, a [szószedetben](../Glossary.md) bármikor utánanézhet a jelentésüknek: 

- Arcfelismerés
- Arcrészek
- Fejtartás
- Arc attribútumok

## <a name="preparation"></a>Előkészítés

Ebben a példában a következő funkciókat mutatjuk be: 

- Képen arcok felismerése és megjelölésük téglalap alakú kerettel
- A pupillák, orr vagy száj helyének elemzése, majd megjelölésük a képen
- A fejtartás, nem és életkor elemzése az arcra

A funkciók végrehajtásához szüksége lesz legalább egy jól kivehető arcot tartalmazó képre. 

## <a name="step-1-authorize-the-api-call"></a>1. lépés: API-hívás engedélyezése

A Face API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot vagy egy lekérdezési karakterlánc paramétereként kell átadni vagy a kérelem fejlécében lehet megadni. Az előfizetési kulcs lekérdezési karakterláncon keresztüli átadásához, példaként tekintse meg a [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) URL-kérését példaként:

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

Alternatív megoldásként az előfizetési kulcs a HTTP-kérés fejlécében is megadható: **ocp-apim-subscription-key: &lt;előfizetési kulcs&gt;** Ügyféloldali kódtár használata esetén az előfizetési kulcs átadása a FaceServiceClient osztály konstruktorán keresztül történik. Például:
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step-2-upload-an-image-to-the-service-and-execute-face-detection"></a>2. lépés: Kép feltöltése a szolgáltatásnak és arcfelismerés végrehajtása

Az arcfelismerés végeztetésének legalapvetőbb módja a kép közvetlen feltöltése. Ez „POST” kérés küldését jelenti application/octet-stream tartalomtípussal, az adatok olvasása JPEG képről történik. A kép maximális mérete 4 MB.

Az ügyféloldali kódtár használatakor az arcfelismerés feltöltéssel Stream objektum átadásával történik. Lásd az alábbi példát:

```CSharp
using (Stream s = File.OpenRead(@"D:\MyPictures\image1.jpg"))
{
    var faces = await faceServiceClient.DetectAsync(s, true, true);
 
    foreach (var face in faces)
    {
        var rect = face.FaceRectangle;
        var landmarks = face.FaceLandmarks;
    }
}
```

Vegye figyelembe, hogy a FaceServiceClient DetectAsync metódusa aszinkron. A hívó metódust is aszinkronnak kell jelölni a várakozó ciklus használatához.
Ha a rendszerkép már a weben van és van URL-címe, az arcfelismerés az URL-cím megadásával is végrehajtható. Ebben a példában a kérés törzse lesz a JSON-karakterlánc, amely tartalmazza az URL-címet.
Az ügyféloldali kódtár használatakor a URL használatával történő arcfelismerés könnyen elvégezhető a DetectAsync metódus egy másik túlterhelésével.

```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

Az észlelt arcokkal visszaadott FaceRectangle tulajdonság lényegében pontokat jelent az arcon pixelben. Általában ez a téglalap tartalmazza a szemet, szemöldök, az orrot, a szájat – a homlok, a fül és az áll nincs benne. Teljes fejkép vagy mellkaskép körbevágásakor (fényképes igazolvány típusú kép) a téglalap alakú arckeret nagyítására lehet szükség, mert az arc területe egyes alkalmazásokhoz túl kicsi lehet. Hasznosnak bizonyul majd az arc pontosabb azonosításához az arcrészek használata (arcrészek megkeresése és az arc irányát azonosító mechanizmus), amit a következő rész ír majd le.

## <a name="step-3-understanding-and-using-face-landmarks"></a>3. lépés: Arcrészek megismerés és használata

Az arcrész egy sor jellegzetes pont az arcon; jellemzően olyan arcrészek pontjai, mint a pupilla, a szemzug vagy az orr. Az arcrészek olyan nem kötelező attribútumok, amelyek elemezhetők az arcfelismerés során. Vagy „true” logikai értéket ad a returnFaceLandmarks lekérdezési paraméternek a [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) hívásakor, vagy a FaceServiceClient osztály DetectAsync metódusában a nem kötelező returnFaceLandmarks paramétert használja ahhoz, hogy az arcrészek is szerepeljenek az észlelési eredményben.

Alapértelmezés szerint 27 előre meghatározott arcpont van. A következő ábra mutatja a 27 pont meghatározását:

![HowToDetectFace](../Images/landmarks.1.jpg)

A visszaadott pontok képpontban vannak, csakúgy mint az arcot keretező téglalap. Ezért könnyebb a képen egyes érdekes pontokat megjelölni. A következő kód az orr és a pupillák helyének lekérését mutatja be:

```CSharp
var faces = await faceServiceClient.DetectAsync(imageUrl, returnFaceLandmarks:true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
 
    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;
 
    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;
 
    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
``` 

Az arcrészek az arc jellemzőinek a képen történő megjelölésén kívül az arc irányának meghatározására is használhatók. Például az arc irányát definiálhatjuk úgy, mint a száj közepétől a szemek közepéig mutató vektort. Az alábbi kódot ezt részletesen ismerteti:

```CSharp
var landmarks = face.FaceLandmarks;
 
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

Miután megismertük az arc irányát, a téglalap alakú keretet elforgathatjuk úgy, hogy az arccal egy síkba kerüljön. Egyértelmű, hogy arcrészek használata nagyobb részletességet és hasznot hoz.

## <a name="step-4-using-other-face-attributes"></a>4. lépés: Egyéb arctulajdonságok használata

Az arcrészek mellett a Face – API sok más jellemzőt is képes elemezni az arcon. Ezek az attribútumok a következők:

- Kor
- Nem
- Mosoly intenzitása
- Arcszőrzet
- 3D fejtartás

Ezeket az attribútumokat statisztikai algoritmusok használatával próbálja megadni és nem mindig 100%-osan pontosak. Ennek ellenére hasznosak, ha az arcokat ezek szerint az attribútumok szerint szeretné osztályozni. Az attribútumokkal kapcsolatos további információkat keresse a [szószedetben](../Glossary.md).

Az alábbi egyszerű példa arc attribútumokat gyűjt ki az arcfelismerés közben:

```CSharp
var requiredFaceAttributes = new FaceAttributeType[] {
                FaceAttributeType.Age,
                FaceAttributeType.Gender,
                FaceAttributeType.Smile,
                FaceAttributeType.FacialHair,
                FaceAttributeType.HeadPose,
                FaceAttributeType.Glasses
            };
var faces = await faceServiceClient.DetectAsync(imageUrl,
    returnFaceLandmarks: true,
    returnFaceAttributes: requiredFaceAttributes);

foreach (var face in faces)
{
    var id = face.FaceId;
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
}
``` 

## <a name="summary"></a>Összegzés

Ebben az útmutatóban megismerte a [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API funkcióit, hogyan képes észlelni arcokat a helyi feltöltött képeken vagy URL-címmel megadott képeken a weben; hogyan képes észlelni arcokat téglalap alakú arckeretekkel; és hogy képes elemezni arcrészeket, 3D fejtartásokat és egyéb arc jellemzőket is.

API-részletekkel kapcsolatos további információkért tekintse meg az API [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) referencia útmutatót.

## <a name="related-topics"></a>Kapcsolódó témakörök

[Hogyan lehet arcokat azonosítani a képen](HowtoIdentifyFacesinImage.md)
