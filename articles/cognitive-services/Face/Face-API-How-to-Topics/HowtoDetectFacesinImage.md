---
title: Lapok észleli a képek Arcfelismerési API-val |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Kognitív szolgáltatások a Arcfelismerési API segítségével azonosíthatók a lapok képek.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 57cd0915450428399fd680638aa4fae2cdbe17c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347103"
---
# <a name="how-to-detect-faces-in-image"></a>A kép néz történő Adatmásolást

Ez az útmutató fog bemutatják, hogyan lehet lemezkép, nemét, kora vagy kibontott jelentő arcfelismerési attribútumokkal rendelkező lapok észleléséhez. A minták a Arcfelismerési API-ügyfél szalagtárat használó C# nyelven íródtak. 

## <a name="concepts"></a> Alapfogalmak

Ha nem ismeri az összes, az útmutató a következő fogalmakat, tekintse meg a definíciók a [szószedet](../Glossary.md) bármikor: 

- Arcfelismerés
- Arcfelismerési jellegzetes hely
- HEAD jelent.
- Arcfelismerési attribútumok

## <a name="preparation"></a> Előkészítése

A példában bemutatjuk a következő szolgáltatásokat: 

- Lemezkép lapok észlelésére, és jelölheti meg őket téglalap alakú keretezési használatával
- A helyek diák, a orr vagy szájához elemzésével és majd jelölheti meg őket a kép
- A head jelentő, nem és a tapasztalt korát elemzése

Ezek a funkciók végrehajtásához, szüksége lesz legalább egy tiszta arcfelismerési rendelkező kép előkészítése. 

## <a name="step1"></a> 1. lépés: Engedélyezze az API-hívás

Minden hívás a Arcfelismerési API-val egy előfizetés kulcs szükséges. Ez a kulcs vagy a lekérdezési karakterlánc paraméterként keresztül történő továbbítása során, vagy a kérelem fejlécében megadott kell. Az Előfizetés kulcs lekérdezési karakterlánc keresztül továbbítani, tekintse meg a kérelem URL-címe a [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) példa:

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

Alternatív megoldásként az Előfizetés kulcs is megadható a kérelem HTTP-fejlécben: **ocp-apim-előfizetés-kulcs: &lt;előfizetés kulcs&gt;**  egy ügyféloldali kódtár használata esetén az Előfizetés kulcs átadott keresztül FaceServiceClient osztály konstruktor. Példa:
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step2"></a> 2. lépés: Lemezkép feltöltése a szolgáltatáshoz, és arcfelismerési észlelés végrehajtása

A legalapvetőbb úgy arcfelismerési észlelés végrehajtására, hogy közvetlenül egy kép feltöltésével. Ez az application/octet-stream tartalomtípus "POST" kérelmet küld a JPEG-képek adatsorból beolvasott adatok történik. A kép maximális mérete 4 MB.

Az ügyféloldali kódtár használatával, arc észlelési feltöltése segítségével végezhető el az adatfolyam-objektummá sikeres. Lásd az alábbi példát:
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

Vegye figyelembe, hogy a DetectAsync FaceServiceClient módszer aszinkron. A hívó metódus ahhoz, hogy a await záradék is, aszinkron módon kell megjelölni.
Ha a kép már szerepel a webes és egy URL-címet, arc észlelési URL-CÍMÉT is megadásával hajtható végre. Ebben a példában a kérés törzsében lesz, az URL-címet tartalmazó JSON karakterlánc.
Az ügyféloldali kódtár használatával, arc észlelési egy URL-cím segítségével is hajthatnak végre könnyen DetectAsync metódus másik túlterhelését.
```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

A FaceRectangle észlelt lapok lett visszaadva tulajdonság alapvetően a képpontban megadott nyílméretét felületen helyét. Általában a téglalap tartalmazza a szem, szemöldök, az én, és a – head füleken és a chin tetején nem érhetők el. Ha egy teljes head vagy a közepes hibaüzenetet álló (egy fénykép azonosító típusa image) levágja, érdemes lehet bontsa ki a a téglalap alakú arcfelismerési keret mert lehet, hogy a terület a felületen egyes alkalmazások túl kicsi. Egy ARC pontosabban található használata oldallal jellegzetes hely (keresse meg a tapasztalt szolgáltatásokat vagy irányt mechanizmusok szembesülhetnek) leírása a következő szakasz fog bizonyulhat hasznosnak.

## <a name="step3"></a> 3. lépés: Ismertetése és használata oldallal jellegzetes hely

Arcfelismerési jellegzetes hely egy ARC; részletes pontok sorozata általában pontok arcfelismerési összetevők, mint például a diák, canthus vagy orrot. Arcfelismerési jellegzetes hely elemzése során tapasztalt választható attribútumok. Is vagy fázis "igaz", egy logikai értéket, amely a returnFaceLandmarks lekérdezésparaméter meghívásakor a [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), vagy a FaceServiceClient osztály DetectAsync módszert ahhoz a returnFaceLandmarks nem kötelező paraméter használható Ahhoz, hogy a tapasztalt jellegzetes hely szerepeljen a észlelésének eredménye.

Alapértelmezés szerint nincsenek 27 előre definiált jellegzetes pontok. A következő ábra bemutatja hogyan összes 27 pontok társítva:

![HowToDetectFace](../Images/landmarks.1.jpg)

A visszaadott pontok képpont, csakúgy, mint a tapasztalt téglalap alakú keret egységekben. Ezáltal könnyítve meg a lemezkép iránti érdeklődését adott pontjainak megjelölni. A következő kód bemutatja, hogy a orr és a diákok helyeinek beolvasása:
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

Mellett a kép arcfelismerési szolgáltatások jelölés, arc jellegzetes hely használhatók a tapasztalt irányának pontosan kiszámításához. Például azt határozhatja meg a tapasztalt irányát a szem közepére a szájához közepétől egy vektor. Az alábbi kódot a témakör részletesen ismerteti:

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

És ismerje meg, amely a tapasztalt irányát, forgatható a téglalap alakú arcfelismerési keret való megfelelés érdekében ez a tapasztalt. Törölje a jelet, hogy további részletek és segédprogram használata oldallal jellegzetes hely nyújthat.

## <a name="step4"></a> 4. lépés: Egyéb arcfelismerési attribútumok használata

Arcfelismerési jellegzetes hely, arc – mellett észlelése API is elemezheti a egy felületen számos más attribútum. Ezek az attribútumok a következők:

- Kor
- Nem
- Mosolynál intenzitásának
- Arcfelismerést haj
- A 3D központi jelentő

Ezek az attribútumok vannak előre jelezni statisztikai algoritmusokkal, és előfordulhat, hogy mindig nem lehet pontos 100 %. Azonban azok továbbra is hasznos, ha szeretné besorolni a lapok ezek az attribútumok szerint. A minden attribútumhoz kapcsolatos további információkért tekintse meg a [szószedet](../Glossary.md).

Az alábbiakban az arcfelismerési attribútumok kibontása során tapasztalt egy egyszerű példája:
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
## <a name="summary"></a> Összefoglalás

Ez az útmutató megtanulta, funkciói [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API, hogyan azonosítására képes lapokat a helyi feltöltött lemezkép vagy a kép URL-címeket, a weben; hogyan azonosítására képes lapok téglalap alakú arcfelismerési keretek; vissza, és hogyan azt is elemezheti arcfelismerési jellegzetes hely, 3D központi kockázatot és más arcfelismerési attribútumok is.

A részletek API kapcsolatos további információkért tekintse meg az API referencia-útmutató a [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related"></a> Kapcsolódó témakörök

[A kép néz azonosítása](HowtoIdentifyFacesinImage.md)
