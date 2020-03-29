---
title: Arcfelismerési fogalmak
titleSuffix: Azure Cognitive Services
description: Ez a cikk ismerteti az Ellenőrzés, A hasonlóság keresése, a Csoport és az Arcfelismerő műveletek és az alapul szolgáló adatstruktúrák fogalmait.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743076"
---
# <a name="face-recognition-concepts"></a>Arcfelismerési fogalmak

Ez a cikk ismerteti az Ellenőrzés, A hasonlóság keresése, a Csoport és az Arcfelismerő műveletek és az alapul szolgáló adatstruktúrák fogalmait. A felismerés nagyjából két különböző arc összehasonlítását írja le annak megállapítására, hogy hasonlítanak-e ugyanahhoz a személyhez.

## <a name="recognition-related-data-structures"></a>Elismeréssel kapcsolatos adatstruktúrák

A felismerési műveletek elsősorban a következő adatstruktúrákat használják. Ezek az objektumok a felhőben tárolódnak, és az azonosító karakterláncaik kal hivatkozhatnak. Az azonosítókarakterláncok mindig egyediek az előfizetésen belül. A névmezők duplikálhatók.

|Név|Leírás|
|:--|:--|
|DetectedFace (DetectedFace)| Ezt az egyarc-ábrázolást az [arcfelismerési](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) művelet olvassa be. Az azonosítója a létrehozása után 24 órával lejár.|
|Megőrzött face| Ha detectedFace objektumok at adunk egy csoporthoz, például a FaceList vagy a Person, válnak PersistedFace objektumokká válnak. Ezek bármikor [visszakereshetők,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) és nem járnak le.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) vagy [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Ez az adatstruktúra a PersistedFace objektumok válogatott listája. A FaceList rendelkezik egy egyedi azonosítóval, egy névkarakterláncgal és opcionálisan egy felhasználói adatkarakterláncgal.|
|[Személy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Ez az adatstruktúra az ugyanahhoz a személyhez tartozó PersistedFace objektumok listája. Egyedi azonosítóval, névkarakterlánccal és adott esetben felhasználói adatkarakterlánccal rendelkezik.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) vagy [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Ez az adatstruktúra a Személy objektumok válogatott listája. Egyedi azonosítóval, névkarakterlánccal és adott esetben felhasználói adatkarakterlánccal rendelkezik. A persongroup kell [képezni,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) mielőtt használható a felismerési műveletek.|

## <a name="recognition-operations"></a>Elismerési műveletek

Ez a szakasz részletezi, hogy a négy felismerési művelet hogyan használja a korábban leírt adatstruktúrákat. Az egyes felismerési műveletek átfogó leírását az Áttekintés című [témakörben találja.](../Overview.md)

### <a name="verify"></a>Ellenőrzés

Az [Ellenőrzés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) művelet arcazonosítót vesz fel az DetectedFace vagy a PersistedFace vagy egy másik arcazonosító vagy egy személy objektumból, és meghatározza, hogy ugyanahhoz a személyhez tartoznak-e. Ha átmegy egy Személy objektumon, a teljesítmény javítása érdekében adott át egy olyan személycsoportban, amelyhez az adott személy tartozik.

### <a name="find-similar"></a>Hasonló keresése

A [Hasonló keresése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) művelet arcazonosítót vesz igénybe az DetectedFace vagy a PersistedFace és egy FaceList vagy más arcazonosítók tömbjében. A FaceList segítségével az adott laphoz hasonló arcok kisebb FaceList-listáját adja vissza. Az arcazonosítók tömbjével hasonlóképpen egy kisebb tömböt ad vissza.

### <a name="group"></a>Csoport

A [csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) művelet az DetectedFace vagy a PersistedFace rendszerkülönböző arcazonosítóinak tömbjét veszi fel, és ugyanazokat az azonosítókat adja vissza, amelyek több kisebb tömbbe vannak csoportosítva. Minden "csoport" tömb hasonló arcazonosítókat tartalmaz. Egyetlen "messyGroup" tömb olyan arcazonosítókat tartalmaz, amelyekhez nem találtak hasonlóságokat.

### <a name="identify"></a>Azonosítani

Az [Azonosítás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) művelet egy vagy több arcazonosítót vesz igénybe az DetectedFace vagy a PersistedFace és a PersistedFace és a PersonGroup rendszerből, és olyan személyobjektumok listáját adja vissza, amelyekhez az egyes arcok tartozhatnak. A visszaadott személy objektumok jelölt objektumokként vannak csomagolva, amelyek előrejelzési megbízhatósági értékkel rendelkeznek.

## <a name="input-data"></a>Bemeneti adatok

Az alábbi tippek segítségével biztosíthatja, hogy a bemeneti képek a legpontosabb felismerési eredményeket nyújtsák:

* A támogatott bemeneti képformátumok a Következők: JPEG, PNG, GIF (az első képkocka), BMP.
* A képfájl mérete nem lehet nagyobb 4 MB-nál.
* Személy objektumok létrehozásakor használjon különböző szögeket és megvilágítást tartalmazó fényképeket.
* Előfordulhat, hogy egyes arcokat nem ismer fel a rendszer technikai kihívások miatt, például:
  * Képek extrém világítás, például súlyos háttérvilágítás.
  * Akadályok, amelyek elzárják az egyik vagy mindkét szemet.
  * Különbségek a haj típusában vagy az arcszőrzetben.
  * Változások az arc megjelenésében az életkor miatt.
  * Extrém arckifejezések.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri az arcfelismerési fogalmakat, ismerje meg, hogyan írhat olyan parancsfájlt, amely azonosítja az arcokat egy képzett személycsoport ellen.

* [Arcok azonosítása a képeken](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)