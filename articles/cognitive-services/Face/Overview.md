---
title: Mi az a Face API?
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható az arcfelismerési szolgáltatás képeken szereplő arcok észlelésére és elemzésére.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: c45fd508c14c368c6c9057b9fdeea8df9d8a52c3
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905684"
---
# <a name="what-is-the-azure-face-api"></a>Mi az Azure Face API?

Az Azure Cognitive Services Face API észleléséhez ismeri fel, és elemezheti a képeken lévő emberi arcok használt algoritmusok biztosít. Fontos a sok különböző forgatókönyvben arra, hogy az emberi arcok adatok feldolgozásához. Példaforgatókönyvek a biztonsági, természetes felhasználói felület, képelemzés tartalom és a felügyeleti, mobilalkalmazások és robotika.

A Face API számos különböző funkciót biztosít. Minden függvény az alábbi szakaszok az ismertetett. Látogasson el lapunkra további információkat olvashat.

## <a name="face-detection"></a>Arcfelismerés

A Face API emberi arcokat észleli a képet, és a helyüket téglalap koordinátáit adja vissza. Szükség esetén arcfelismerés kibonthatja a face kapcsolódó attribútumok sorozata. Példák a fő testtartás, gender, kor, érzelem, arcfelismerési haj és szemüveg.

> [!NOTE]
> A face észlelési szolgáltatás is keresztül érhető el a [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Ha szeretne további műveletek az arcfelismerési adatokat, használjon a Face API, amely a szolgáltatás a cikkben leírtak szerint.

![Egy nőről és a egy ember, arcokat és korának, megjelenik a nemek körül téglalapokkal képe](./Images/Face.detection.jpg)

Arcfelismerés további információkért lásd: a [Arcfelismerés](concepts/face-detection.md) fogalmak cikk. További tájékoztatás a [észlelése API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) referenciadokumentációt.

## <a name="face-verification"></a>Arcellenőrzés

A Verify API hitelesítést végez két észlelt arc között vagy egy észlelt arc és egy személyobjektum között. Lényegében azt értékeli, hogy a két arc ugyanazon személyhez tartozik-e. Ez a funkció potenciálisan hasznos biztonsági forgatókönyvet. További információkért lásd: a [Arcfelismerési](concepts/face-recognition.md) fogalmak útmutató vagy a [ellenőrzése API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) referenciák dokumentációiba.

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A keresés hasonló API egy cél arc jelölt arcok használatával megkeresheti azokat, amelyek hasonlóak a cél face téglalapot kisebb számú hasonlítja össze. Két munkanapon mód, matchPerson és matchFace, támogatottak. A matchPerson mód hasonló arcokat keres adja vissza, miután használatával egy személy számára szűri a [ellenőrzése API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). A matchFace mód figyelmen kívül hagyja a ugyanazt az embert szűrőt. Hasonló jelölt arcokat keres, amelyek vagy nem tartozhat ugyanahhoz a személyhez listáját adja vissza.

Az alábbi példa bemutatja a cél oldallal:

![Mosolygó nő](./Images/FaceFindSimilar.QueryFace.jpg)

Ezek pedig a jelölt személyek arcai:

![Öt mosolygó személy. Képek a és b ugyanazt az embert megjelenítése.](./Images/FaceFindSimilar.Candidates.jpg)

Ha négy hasonló arcokat keres, a matchPerson módot adja vissza a és b, amely a cél face azonos megjelenítése. A matchFace mód értéket ad vissza, a, b, c, és a d, pontosan négy jelöltek, még akkor is, ha nem ugyanazt az embert célként, illetve alacsony hasonlóság néhány. További információkért lásd: a [Arcfelismerési](concepts/face-recognition.md) fogalmak útmutató vagy a [hasonló API található](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) referenciák dokumentációiba.

## <a name="face-grouping"></a>Arccsoportosítás

A Group API több csoportra osztja az ismeretlen arcokat a hasonlóság alapján. Mindegyik csoport az eredeti arcok halmazának különálló valódi részhalmaza. Az arcok a csoportban található összes valószínűleg ugyanazt az embert tartozik. Több másik csoport, egyetlen személy is lehet. A csoportok különbözteti meg egy másik tényező, például a kifejezés, például. További információkért lásd: a [Arcfelismerési](concepts/face-recognition.md) fogalmak útmutató vagy a [csoport API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) referenciák dokumentációiba.

## <a name="person-identification"></a>Személyazonosítás

A azonosítása API egy olyan adatbázison, akikkel észlelt arc azonosítására szolgál. Ez a funkció a fénykép felügyeleti szoftver képcímkézés automatikus hasznos lehet. Előre létrehozni az adatbázist, és idővel szerkesztheti.

Az alábbi képen egy példa egy "myfriends." nevű adatbázis Minden csoport legfeljebb 1 millió más személy objektumot is tartalmazhat. Mindegyik személyobjektumhoz legfeljebb 248 arc lehet regisztrálva.

![A különböző face lemezképek három sort az emberek három oszlopot rács](./Images/person.group.clare.jpg)

Miután létrehozott egy adatbázist, és betanított, egy új észlelt Face a csoporton azonosító is végezhet. Ha a rendszer a csoport egyik tagjaként azonosítja az arcot, visszaadja a személyobjektumot.

Személyek azonosítása kapcsolatos további információkért lásd: a [Arcfelismerési](concepts/face-recognition.md) fogalmak útmutató vagy a [azonosítása API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) referenciák dokumentációiba.

## <a name="use-containers"></a>Tárolók használata

[Arcfelismerés a tárolót használja](face-how-to-install-containers.md) észleléséhez ismeri, és az adatok szabványos Docker-tároló közelebb telepítésével arcokat azonosíthat.

## <a name="sample-apps"></a>Mintaalkalmazások

Az alábbi minta alkalmazások megjelenítése néhány módszer a Face API:

- [Microsoft Face API: Windows ügyféloldali kódtár és mintaalkalmazás](https://github.com/Microsoft/Cognitive-Face-Windows) egy WPF-alkalmazás, amely bemutatja az arcok észlelése, elemzési és azonosító számos forgatókönyv.
- [FamilyNotes UWP-alkalmazás](https://github.com/Microsoft/Windows-appsample-familynotes) egy univerzális Windows Platform (UWP) alkalmazás, hogy használja a face azonosító speech, Cortana, ink és kamera családba tartozó megjegyzés-megosztási forgatókönyvekben együtt.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Az összes Cognitive Services-erőforrások, a Face szolgáltatást használó fejlesztők ügyféladatok házirendek a Microsoft tisztában kell lennie. További információkért lásd: a [Cognitive Services-lapra](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a Microsoft Trust Center.

## <a name="next-steps"></a>További lépések

Kövesse az arcfelismerés – Ez a forgatókönyv megvalósításához a kódban a rövid útmutató:

- [Rövid útmutató: Arcfelismerés képen egy-a .NET SDK-val C# ](quickstarts/csharp.md). Más nyelveken érhető el.
