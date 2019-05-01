---
title: Arcok felismerése fogalmak
titleSuffix: Azure Cognitive Services
description: Ismerje meg az arcfelismerés kapcsolatos alapvető fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 8c0bf001c2bdbedaa041dbd10b5c7edb08eec4c6
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "64415999"
---
# <a name="face-recognition-concepts"></a>Arcok felismerése fogalmak

Ez a cikk ismerteti a a különböző arcok felismerése műveletek (ellenőrzési, hasonló, csoportosítás, azonosító keresése) és az alapul szolgáló adatok struktúrák. Széles körben felismerés leírja annak meghatározásához, hogy azok hasonló két különböző arcok összehasonlítása a munkáját, vagy ugyanahhoz a személyhez tartozik.

## <a name="recognition-related-data-structures"></a>Elismerés kapcsolatos datové struktury

A beszédfelismerést műveletek elsősorban a következő adatstruktúrák használja. Ezek az objektumok a felhőben vannak tárolva, és azok azonosító karakterláncot hivatkozhat. Azonosító karakterláncot, azaz mindig egy előfizetésen belül egyedi név mezőt is megkettőzhetők, mivel.

|Name (Név)|Leírás|
|:--|:--|
|**DetectedFace**| Ez az egyetlen face reprezentációja az lekéri a [arcfelismerés](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) műveletet. Az azonosítója a létrehozást követően 24 órával lejár.|
|**PersistedFace**| Amikor **DetectedFace** egy csoportba kerülnek objektumok (például **FaceList** vagy **személy**), lesznek **PersistedFace** objektumok, amelyek segítségével lehet [lekért](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) bármelyik időt, és ne járjanak le.|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| Ez a Szortimentbe felvett listája **PersistedFace** objektumokat. A **FaceList** rendelkezik egy egyedi Azonosítót, egy karakterláncát, és szükség esetén a felhasználói adatok karakterlánc.|
|**[Személy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| Ez a lista a **PersistedFace** ugyanazt az embert tartozó objektumok. Rendelkezik egy egyedi Azonosítót, egy karakterláncát, és szükség esetén a felhasználói adatok karakterlánc.|
|**[Is lehet PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| Ez a Szortimentbe felvett listája **személy** objektumokat. Rendelkezik egy egyedi Azonosítót, egy karakterláncát, és szükség esetén a felhasználói adatok karakterlánc. A **is lehet PersonGroup** kell [betanított](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) ahhoz, hogy a felismerés műveletek kell használni.|

## <a name="recognition-operations"></a>Elismerés műveletek

Ez a szakasz részletesen a fenti adatstruktúrákat négy felismerés műveletek használatát. Tekintse meg a [áttekintése](../Overview.md) egyes felismerés műveletek széles körű leírását.

### <a name="verification"></a>Ellenőrzés

A [ellenőrizze](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) művelet vesz igénybe, a face ID (**DetectedFace** vagy **PersistedFace**) és vagy egy másik face ID vagy egy **személy** objektum és a azt határozza meg, hogy ugyanazt az embert tartoznak. Ha a felhasználó a egy **személy**, akkor lehetősége van átadni a egy **is lehet PersonGroup** , amelyhez az adott **személy** tartozik teljesítményének javítása érdekében.

### <a name="find-similar"></a>Hasonló keresése

A [található hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) művelet vesz igénybe, a face ID (**DetectedFace** vagy **PersistedFace**) és egy **FaceList** vagy más face tömbje Azonosítók. Az egy **FaceList**, akkor adja vissza egy kisebb **FaceList** téglalapot hasonló az adott személyt. A face azonosítóinak tömbje, hasonlóképpen tömböt ad vissza egy kisebb.

### <a name="grouping"></a>Csoportosítás

A [csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) művelet egy diagnosztikakonfigurációs tömböt foglal Szortimentbe felvett face azonosítók (**DetectedFace** vagy **PersistedFace**), és visszaadja a számos kisebb tömbök csoportosítva ugyanazokat az azonosítókat. Minden "csoport" tömb oldallal, amely megjelenik a hasonló azonosítók tartalmazza, egy egyetlen "messyGroup" tömb face azonosítókat tartalmazza a hasonlóságot nem találhatók.

### <a name="identification"></a>Azonosító

A [azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) művelet egy vagy több face azonosítók vesz igénybe (**DetectedFace** vagy **PersistedFace**) és a egy **is lehet PersonGroup** és a egy listát ad vissza a **személy** objektumok, amelyek minden tartozhat. Visszaadott **személy** objektumok burkolja **jelölt** objektumok, amelyek az előrejelzés megbízhatósági értékkel rendelkeznie.

## <a name="input-data"></a>Bemeneti adatok

Győződjön meg arról, hogy a bemeneti képekhez adnak a legpontosabb felismerési eredményeket használja a következő tippeket:

* A bemeneti kép támogatott formátumok a következők: JPEG, PNG, (az első képkocka) GIF és BMP.
* Kép fájlmérete kell nem lehet nagyobb, mint 4 MB.
* Létrehozásakor **személy** objektumok, fényképeket, a szolgáltatás számos különböző szögek és megvilágítási kell használnia.
* Néhány arcok előfordulhat, hogy nem ismerhető fel kihívások miatt:
  * Képek a szélsőséges világítás (például súlyos háttérvilágítás).
  * Akadályok blokkolja az egyik vagy mindkét szemet.
  * Stílus és videofelismerést, arc haj haj különbségek.
  * Face megjelenés kora miatt bekövetkezett változásokat.
  * Szélsőséges arckifejezések.

## <a name="next-steps"></a>További lépések

Most, hogy Ön ismeri a arcok felismerése fogalmakat, ismerje meg, hogyan írhat egy egyszerű szkript, amely azonosítja az arcok elleni egy betanított **is lehet PersonGroup**.

* [Arcok, képek azonosítása](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)