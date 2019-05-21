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
ms.openlocfilehash: fa38c492530cb8938e49bc15e13fdd39ed5b6f1c
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890881"
---
# <a name="face-recognition-concepts"></a>Arcok felismerése fogalmak

Ez a cikk ismerteti a győződjön meg arról, hasonló keresése, csoport és azonosítása arcok felismerése műveletek és az alapul szolgáló adatok struktúrák. Széles körben felismerés ismerteti összehasonlítja a két különböző arcokat határozza meg, ha azok hasonló, vagy ugyanazt az embert tartozik a munkát.

## <a name="recognition-related-data-structures"></a>Elismerés kapcsolatos datové struktury

A beszédfelismerést műveletek elsősorban a következő adatstruktúrák használja. Ezek az objektumok a felhőben vannak tárolva, és azok azonosító karakterláncot hivatkozhat. Azonosító karakterláncot mindig egyediek-e egy előfizetésen belül. Név mezőt is kettőzhető meg.

|Name (Név)|Leírás|
|:--|:--|
|DetectedFace| Ez egyetlen face ábrázolása az lekéri a [arcfelismerés](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) műveletet. Az azonosítója a létrehozást követően 24 órával lejár.|
|PersistedFace| Amikor DetectedFace objektumok kerülnek egy csoportba, például FaceList vagy személy, PersistedFace objektumok válnak. Ezek lehetnek [lekért](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) bármelyik időt, és nem jár le.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) vagy [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Ezt az adatstruktúrát egy Szortimentbe felvett PersistedFace objektumok listájához. Egy FaceList rendelkezik egy egyedi Azonosítót, egy karakterláncát, és szükség esetén a felhasználói adatok karakterlánc.|
|[Személy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Az adatszerkezetet az ugyanazt az embert tartozó PersistedFace objektumok listája. Rendelkezik egy egyedi Azonosítót, egy karakterláncát, és szükség esetén a felhasználói adatok karakterlánc.|
|[Is lehet PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) vagy [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Ezt az adatstruktúrát egy személy objektumot Szortimentbe felvett listájához. Rendelkezik egy egyedi Azonosítót, egy karakterláncát, és szükség esetén a felhasználói adatok karakterlánc. Egy is lehet PersonGroup kell [betanított](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) ahhoz, hogy a felismerés műveletek kell használni.|

## <a name="recognition-operations"></a>Elismerés műveletek

Ez a szakasz részletesen korábban leírt adatstruktúrák négy felismerés műveletek használatát. Minden egyes felismerés művelet széles körű ismertetését lásd: [áttekintése](../Overview.md).

### <a name="verify"></a>Megerősítés

A [ellenőrizze](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) művelet vesz igénybe a face ID DetectedFace vagy PersistedFace és a egy másik face ID vagy egy személy objektumot, és meghatározza, hogy ugyanazt az embert tartoznak. Ha egy személy objektumot adja át, akkor lehetősége van átadni egy is lehet PersonGroup, amelyhez a személy tartozik, a teljesítmény javítása a.

### <a name="find-similar"></a>Hasonló keresése

A [hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) művelet vesz igénybe a face ID DetectedFace vagy PersistedFace és a egy FaceList vagy más face azonosítóinak tömbje. Egy FaceList az egy kisebb FaceList hasonló az adott személyt téglalapot ad vissza. A face azonosítóinak tömbje, hasonlóképpen tömböt ad vissza egy kisebb.

### <a name="group"></a>Csoport

A [csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) művelet egy diagnosztikakonfigurációs tömböt foglal Szortimentbe felvett face azonosítók DetectedFace vagy PersistedFace, és adja vissza, ugyanazokat az azonosítókat több kisebb tömbök csoportosítva. Minden egyes "csoport" tömb face hasonlóan néz azonosítókat tartalmaz. Egy egyetlen "messyGroup" tömb face azonosítókat tartalmazza a hasonlóságot nem találhatók.

### <a name="identify"></a>Azonosítás

A [azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) művelet eltarthat egy vagy több face azonosítók DetectedFace vagy PersistedFace és a egy is lehet PersonGroup, és előfordulhat, hogy tartozó minden személy objektumok listáját adja vissza. Visszaadott objektumok burkolja jelölt objektumként, amely az előrejelzés megbízhatósági értéke személy.

## <a name="input-data"></a>Bemeneti adatok

Győződjön meg arról, hogy a bemeneti képekhez adnak a legpontosabb felismerési eredményeket használja a következő tippeket:

* A bemeneti kép támogatott formátumok a következők: JPEG, PNG, (az első képkocka) GIF és BMP.
* Kép fájlmérete kell nem lehet nagyobb, mint 4 MB.
* Személy objektumok létrehozásakor használja a fényképeket, különböző szögek és megvilágítási funkciót.
* Néhány arcok előfordulhat, hogy nem ismeri fel miatt kihívások, például:
  * Háttérvilágítás szélsőséges világító, például súlyos tartalmazó lemezképeket.
  * Egy vagy is szemet letiltó akadályokat.
  * Írja be vagy videofelismerést, arc haj haj különbségek.
  * Arcfelismerési megjelenés kora miatt bekövetkezett változásokat.
  * Szélsőséges arckifejezések.

## <a name="next-steps"></a>További lépések

Most, hogy Ön ismeri arcok felismerése fogalmait, megtudhatja, hogyan írható olyan szkript, amely azonosítja az arcok egy betanított is lehet PersonGroup ellen.

* [Arcazonosítás képeken](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)