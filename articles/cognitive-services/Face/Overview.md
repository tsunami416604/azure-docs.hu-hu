---
title: Mi az Azure Face Service?
titleSuffix: Azure Cognitive Services
description: Az Azure Face Service olyan AI-algoritmusokat biztosít, amelyeket a képeken található emberi arcok észlelésére, felismerésére és elemzésére használhat.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 9/17/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: arc-felismerés, Arcfelismerés szoftver, arc-elemzés, szembenézés egyeztetése, Arcfelismerés alkalmazás, Szembenézés a képek alapján, Arcfelismerés keresés
ms.openlocfilehash: 0a7e242add9fdaa9e169a4003e8ad8f39b1fb111
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91262484"
---
# <a name="what-is-the-azure-face-service"></a>Mi az Azure Face Service?

> [!WARNING]
> 2020. június 11-én a Microsoft bejelentette, hogy nem értékesít arcfelismerő technológiát az Egyesült Államok rendőrségének egészen addig, amíg az emberi jogokra alapozott erős szabályozást nem vezetnek be. Ilyen esetben előfordulhat, hogy az ügyfelek nem használhatják az Azure-szolgáltatások (például a Face vagy a Video Indexer) arc-felismerési funkcióit és funkcióit, ha az ügyfél vagy a által a Egyesült Államok egy rendőrségi részlege által vagy a szolgáltatásban való használatát engedélyezte.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Face szolgáltatás olyan AI-algoritmusokat biztosít, amelyek a képeken az emberi arcok észlelését, felismerését és elemzését végzik. Az Arcfelismerés szoftver számos különböző helyzetben fontos, például a biztonság, a természetes felhasználói felület, a képtartalom elemzése és kezelése, a Mobile apps és a robotika.

A Face szolgáltatás számos különböző arc-elemzési funkciót biztosít, amelyek mindegyike az alábbi részben látható.

## <a name="face-detection"></a>Arcészlelés

A Face szolgáltatás észleli az emberi arcokat egy képen, és visszaadja a helyük téglalapjának koordinátáit. Ha szeretné, a Arcfelismerés számos arc-alapú attribútumot, például a Head póz, a nemek, a kor, az érzelem, az arcszőrzet és a szemüvegeket is kinyerheti.

> [!NOTE]
> Az Arcfelismerés funkció a [Computer Vision szolgáltatáson](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)keresztül is elérhető. Ha azonban az adatokkal kapcsolatos további műveleteket kíván végrehajtani, ezt a szolgáltatást kell használnia.

![Egy nő és egy férfi képe, amely az arcukon és a nemek közötti téglalapokat ábrázolja](./Images/Face.detection.jpg)

Az Arcfelismerés szolgáltatással kapcsolatos további információkért tekintse meg a [Face észlelési](concepts/face-detection.md) fogalmakat ismertető cikket. Lásd még az API-referenciák [észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) című dokumentumot.

## <a name="face-verification"></a>Arcellenőrzés

Az ellenőrzés az API-val két észlelt arc vagy egy személy objektum közötti hitelesítést végez. Lényegében azt értékeli, hogy a két arc ugyanazon személyhez tartozik-e. Ez a funkció biztonsági helyzetekben hasznos lehet. További információ: [arc-felismerési](concepts/face-recognition.md) fogalmakat ismertető útmutató vagy az API-dokumentáció [ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) .

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A hasonló API megkeresése a cél és a jelölt arcok közötti egyezést is magában foglalhatja, és megkeresheti a cél felületéhez hasonló arcok kisebb készletét. Ez akkor lehet hasznos, ha a képet egy arc alapján keresik. 

A **matchPerson** és a **matchFace**két működési mód támogatott. A **matchPerson** mód a hasonló arcokat adja vissza, miután ugyanezen személyre szűrést végez az [API ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)paranccsal. A **matchFace** mód figyelmen kívül hagyja az egyszemélyes szűrőt. A hasonló jelölt arcok listáját adja vissza, amelyek esetleg nem ugyanahhoz a személyhez tartoznak.

Az alábbi példa a cél arcot mutatja:

![Mosolygó nő](./Images/FaceFindSimilar.QueryFace.jpg)

És ezek a képek a jelölt arcok:

![Öt mosolygó személy. Az a és b képek ugyanazt a személyt jelenítik meg.](./Images/FaceFindSimilar.Candidates.jpg)

Négy hasonló arc megtalálásához a **matchPerson** mód az a és b értéket adja vissza, amely ugyanazt a személyt jeleníti meg, mint a cél arca. A **matchFace** mód az a, b, c és d &mdash; pontosan négy pályázót ad vissza, még akkor is, ha nem ugyanaz a személy, mint a cél, vagy alacsony hasonlóságot mutat. További információ: [arc-felismerési](concepts/face-recognition.md) fogalmakat ismertető útmutató vagy a [hasonló API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) -k keresése dokumentáció.

## <a name="face-grouping"></a>Arccsoportosítás

A Group API több csoportra osztja az ismeretlen arcokat a hasonlóság alapján. Mindegyik csoport az eredeti arcok halmazának különálló valódi részhalmaza. Egy csoport összes arca valószínűleg ugyanahhoz a személyhez tartozik. Egyetlen személynek több különböző csoportja is lehet. A csoportokat egy másik tényező különbözteti meg, például a kifejezést. További információ: [arc-felismerési](concepts/face-recognition.md) fogalmakat ismertető útmutató vagy a [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) -referenciák dokumentációja.

## <a name="person-identification"></a>Személyazonosítás

Az azonosított API használatával azonosíthatók az észlelt arcok az emberek adatbázisán (Arcfelismerés keresés). Ez a funkció akkor lehet hasznos, ha az automatikus képcímkézést a Photo Management szoftverben lehet használni. Az adatbázist előre kell létrehoznia, és bármikor szerkesztheti.

Az alábbi képen a nevű adatbázisra mutat példát `"myfriends"` . Mindegyik csoport legfeljebb 1 000 000 különböző személy objektumot tartalmazhat. Mindegyik személyobjektumhoz legfeljebb 248 arc lehet regisztrálva.

![Három oszlopból álló rács különböző személyek számára, melyek mindegyike három sornyi képpel rendelkezik](./Images/person.group.clare.jpg)

Az adatbázis létrehozása és betanítása után az azonosítást a csoporton belül egy új észlelt arc használatával végezheti el. Ha a rendszer a csoport egyik tagjaként azonosítja az arcot, visszaadja a személyobjektumot.

A személy azonosításával kapcsolatos további információkért tekintse meg az [arc-felismerési](concepts/face-recognition.md) fogalmakat ismertető útmutatót vagy az API-referenciák [azonosítására](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) szolgáló dokumentációt.

## <a name="containers"></a>Containers

[A Face tárolóval](face-how-to-install-containers.md) az arcok észlelésére, felismerésére és azonosítására van egy szabványosított Docker-tároló telepítése az adataihoz közelebb.

## <a name="sample-apps"></a>Mintaalkalmazások

Az alábbi példák a Face szolgáltatás használatának néhány módját mutatják be:

- [Face API: a Windows ügyféloldali könyvtára és mintája](https://github.com/Microsoft/Cognitive-Face-Windows) egy WPF-alkalmazás, amely számos, az Arcfelismerés, az elemzés és az azonosítás forgatókönyvét mutatja be.
- A [FAMILYNOTES UWP alkalmazás](https://github.com/Microsoft/Windows-appsample-familynotes) egy olyan univerzális Windows-platform (UWP) alkalmazás, amely a beszédfelismerést, a Cortana, a tinta és a fényképezőgépet használja a család megjegyzésekkel való megosztására szolgáló forgatókönyvben.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes Cognitive Services erőforrás esetében, a Face szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. További információkért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>További lépések

Kövessen egy rövid útmutatót egy Arcfelismerés alkalmazás alapvető összetevőinek a választott nyelven való kódolásához.

- [Ügyféloldali kódtár](quickstarts/client-libraries.md)gyors üzembe helyezése
