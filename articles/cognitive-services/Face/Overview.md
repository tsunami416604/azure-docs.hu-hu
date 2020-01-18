---
title: Mi az a Face szolgáltatás?
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services Face szolgáltatás olyan algoritmusokat biztosít, amelyek a képek emberi arcok észlelésére, felismerésére és elemzésére szolgálnak.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: c4e2253229f4461bcb6a2a9fe3e561f3cde29760
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170255"
---
# <a name="what-is-the-azure-face-service"></a>Mi az Azure Face Service?

Az Azure Cognitive Services Face szolgáltatás olyan algoritmusokat biztosít, amelyek a képek emberi arcok észlelésére, felismerésére és elemzésére szolgálnak. Az emberi arc-információk feldolgozásának lehetősége számos különböző szoftveres helyzetben fontos. Ilyenek például a biztonság, a természetes felhasználói felület, a képek tartalmának elemzése és kezelése, a Mobile apps és a robotika.

A Face szolgáltatás számos különböző funkciót biztosít. Az egyes függvényeket a következő részben ismertetjük. További információért olvassa el a következőt:.

## <a name="face-detection"></a>Arcfelismerés

A Face szolgáltatás észleli az emberi arcokat egy képen, és visszaadja a helyük téglalapjának koordinátáit. Ha szeretné, a Arcfelismerés több arc-specifikus attribútumot is Kinyer. Ilyenek például a következők: fej, nemek, kor, érzelem, arcszőrzet és szemüveg.

> [!NOTE]
> Az Arcfelismerés funkció a [Computer Vision APIon](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)keresztül is elérhető. Ha további műveleteket szeretne végezni az adatokkal kapcsolatban, használja a Face szolgáltatást, amely a jelen cikkben tárgyalt szolgáltatás.

![Egy nő és egy férfi képe, amely az arcukon és a nemek közötti téglalapokat ábrázolja](./Images/Face.detection.jpg)

Az Arcfelismerés szolgáltatással kapcsolatos további információkért tekintse meg a [Face észlelési](concepts/face-detection.md) fogalmakat ismertető cikket. Lásd még az API-referenciák [észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) című dokumentumot.

## <a name="face-verification"></a>Arcellenőrzés

Az ellenőrzés az API-val két észlelt arc vagy egy személy objektum közötti hitelesítést végez. Lényegében azt értékeli, hogy a két arc ugyanazon személyhez tartozik-e. Ez a funkció biztonsági helyzetekben hasznos lehet. További információ: [Arcfelismerés](concepts/face-recognition.md) fogalmakat ismertető útmutató vagy az API-dokumentáció [ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) .

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A keresés a hasonló API-val hasonlítja össze a cél arcát a jelölt arcokkal, hogy megkeresse a cél felületéhez hasonló arcok kisebb készletét. A matchPerson és a matchFace két működési mód támogatott. A matchPerson mód hasonló arcokat ad vissza, miután az [API ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)után ugyanezt a személyt szűri. A matchFace mód figyelmen kívül hagyja az egyszemélyes szűrőt. A hasonló jelölt arcok listáját adja vissza, amelyek esetleg vagy esetleg nem ugyanahhoz a személyhez tartoznak.

Az alábbi példa a cél arcot mutatja:

![Mosolygó nő](./Images/FaceFindSimilar.QueryFace.jpg)

És ezek a képek a jelölt arcok:

![Öt mosolygó személy. Az a és b képek ugyanazt a személyt jelenítik meg.](./Images/FaceFindSimilar.Candidates.jpg)

Négy hasonló arc megtalálásához a matchPerson mód az a és b értéket adja vissza, amely ugyanazt a személyt jeleníti meg, mint a cél arca. A matchFace mód az a, b, c és d&mdash;pontosan négy pályázót ad vissza, még akkor is, ha nem ugyanaz a személy, mint a cél, vagy alacsony hasonlóságot mutat. További információkért tekintse meg az [Arcfelismerés](concepts/face-recognition.md) fogalmakat ismertető útmutatót vagy a [hasonló API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) -referenciát.

## <a name="face-grouping"></a>Arcok csoportosítása

A Group API több csoportra osztja az ismeretlen arcokat a hasonlóság alapján. Mindegyik csoport az eredeti arcok halmazának különálló valódi részhalmaza. Egy csoport összes arca valószínűleg ugyanahhoz a személyhez tartozik. Egyetlen személynek több különböző csoportja is lehet. A csoportokat egy másik tényező különbözteti meg, például a kifejezést. További információ: [Arcfelismerés](concepts/face-recognition.md) fogalmakat ismertető útmutató vagy a [csoport API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) -dokumentációja.

## <a name="person-identification"></a>Személyazonosítás

Az azonosított API azonosítja az észlelt arcot egy személy adatbázisán. Ez a funkció akkor lehet hasznos, ha az automatikus képcímkézést a Photo Management szoftverben lehet használni. Az adatbázist előre kell létrehoznia, és bármikor szerkesztheti.

Az alábbi képen egy `"myfriends"`nevű adatbázisra mutatunk be példát. Mindegyik csoport legfeljebb 1 000 000 különböző személy objektumot tartalmazhat. Mindegyik személyobjektumhoz legfeljebb 248 arc lehet regisztrálva.

![Három oszlopból álló rács különböző személyek számára, melyek mindegyike három sornyi képpel rendelkezik](./Images/person.group.clare.jpg)

Az adatbázis létrehozása és betanítása után az azonosítást a csoporton belül egy új észlelt arc használatával végezheti el. Ha a rendszer a csoport egyik tagjaként azonosítja az arcot, visszaadja a személyobjektumot.

A személy azonosításával kapcsolatos további információkért tekintse meg az [Arcfelismerés](concepts/face-recognition.md) fogalmakat ismertető útmutatót vagy az API-referenciák [azonosítására](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) szolgáló dokumentációt.

## <a name="use-containers"></a>Tárolók használata

[A Face tárolóval](face-how-to-install-containers.md) az arcok észlelésére, felismerésére és azonosítására van egy szabványosított Docker-tároló telepítése az adataihoz közelebb.

## <a name="sample-apps"></a>Mintaalkalmazások

Az alábbi példák a Face szolgáltatás használatának néhány módját mutatják be:

- [Face API: a Windows ügyféloldali könyvtára és mintája](https://github.com/Microsoft/Cognitive-Face-Windows) egy WPF-alkalmazás, amely számos, az Arcfelismerés, az elemzés és az azonosítás forgatókönyvét mutatja be.
- A [FAMILYNOTES UWP alkalmazás](https://github.com/Microsoft/Windows-appsample-familynotes) egy olyan univerzális Windows-platform (UWP) alkalmazás, amely a beszédfelismerést, a Cortana, a tinta és a fényképezőgépet használja a család megjegyzésekkel való megosztására szolgáló forgatókönyvben.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes Cognitive Services erőforrás esetében, a Face szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. További információkért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>Következő lépések

Kövessen egy rövid útmutatót egy Arcfelismerés forgatókönyv megvalósításához a kódban:

- Gyors útmutató [: az arcok észlelése egy rendszerképben a .net SDK C#és a használatával ](quickstarts/csharp.md). Más nyelvek is elérhetők.
