---
title: Az Azure Cosmos DB élettartama lejár
description: A TTL esetében a Microsoft Azure Cosmos DB lehetővé teszi a dokumentumok automatikus kiürítését a rendszerből egy adott idő elteltével.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: c3e1c4f56c641bf5bfa189836a4bcdf99672a3c1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597486"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Élettartam (TTL) Azure Cosmos DB 

Az **élettartam vagy a** TTL esetében a Azure Cosmos db lehetővé teszi, hogy egy adott időszak után automatikusan törölje a tárolóból az elemeket. Alapértelmezés szerint beállíthatja az időt a tároló szintjén, és felülbírálhatja az értéket cikkenként. Miután beállította az ÉLETTARTAMot egy tárolóban vagy egy elem szintjén, Azure Cosmos DB automatikusan eltávolítja ezeket az elemeket az időszak után az utolsó módosítás időpontja óta. Az élettartam értéke másodpercben van konfigurálva. Az élettartam beállításakor a rendszer automatikusan törli a lejárt elemeket az élettartam értéke alapján, anélkül, hogy az ügyfélalkalmazás által explicit módon kiállított törlési műveletre lenne szükség.

A lejárt elemek törlése olyan háttérbeli feladat, amely felhasználja a [kérelmeket, és](request-units.md)ez olyan kérelmeket foglal magába, amelyeket a felhasználói kérések nem használnak fel. A lejárat késleltethető, ha a tároló nagy terhelés alatt van, és a karbantartási feladatok nem maradnak a kérések egységében.

## <a name="time-to-live-for-containers-and-items"></a>A tárolók és elemek élettartama

Az élettartam értéke másodpercben van megadva, és a rendszer az elem utolsó módosításának időpontjában változásként értelmezi. Beállíthatja, hogy az élettartam egy tárolón vagy egy elemen belül legyen:

1. A **tárolón való működés ideje** (a használatával `DefaultTimeToLive`állítható be):

   - Ha hiányzik (vagy NULL értékre van állítva), az elemek nem lesznek automatikusan lejáratva.

   - Ha van, és az értéke "-1", akkor az a végtelen értékkel egyenlő, és az elemek alapértelmezés szerint nem járnak le.

   - Ha van, és az érték egy *"n"* számra van beállítva, az elemek az utolsó módosítás időpontja után az *"n"* másodpercet fogják lejárni.

2. **Az elem élettartama** (a használatával `ttl`állítható be):

   - Ez a tulajdonság csak akkor alkalmazható `DefaultTimeToLive` , ha létezik, és a szülő tárolóban nem null értékre van állítva.

   - Ha van ilyen, felülbírálja `DefaultTimeToLive` a szülő tároló értékét.

## <a name="time-to-live-configurations"></a>Élettartam-konfigurációk

* Ha a TTL értéke *"n"* értékre van állítva egy tárolón, akkor az adott tárolóban lévő elemek *n* másodperc után lejárnak.  Ha ugyanabban a tárolóban vannak olyan elemek, amelyeken a saját élettartama van, állítsa az-1 értéket (jelezve, hogy nem jár le), vagy ha egyes elemek felülbírálják az élő beállítás eltérő számmal való megadásának idejét, akkor ezek az elemek a saját beállított TTL-értékük alapján lejárnak. 

* Ha az élettartam nincs beállítva egy tárolón, akkor az ebben a tárolóban lévő elemhez tartozó időtartam nem lép érvénybe. 

* Ha a tárolón lévő TTL-1 értékre van állítva, akkor a tároló egyik eleme, amelynek az élettartama n értékre van állítva, a (z) n másodperc után lejár, és a fennmaradó elemek nem fognak lejárni. 

Az ÉLETTARTAMon alapuló elemek törlése ingyenes. A TTL lejárata miatt a rendszer nem számít fel további költségeket (azaz nincs további RUs felhasználva).

## <a name="examples"></a>Példák

Ez a szakasz néhány példát mutat be a tárolóhoz és elemekhez rendelt élő értékek különböző időpontjával:

### <a name="example-1"></a>1\. példa

A tároló ÉLETTARTAMa NULL értékre van állítva (DefaultTimeToLive = null)

|TTL az elemnél| Eredmény|
|---|---|
|TTL = null|    Az élettartam le van tiltva. Az tétel soha nem jár le (alapértelmezett).|
|TTL =-1   |Az élettartam le van tiltva. Az adott tétel soha nem jár le.|
|TTL = 2000 |Az élettartam le van tiltva. Az adott tétel soha nem jár le.|


### <a name="example-2"></a>2\. példa

A tárolón belüli TTL értéke-1 (DefaultTimeToLive =-1)

|TTL az elemnél| Eredmény|
|---|---|
|TTL = null |Az élettartam engedélyezve van. Az tétel soha nem jár le (alapértelmezett).|
|TTL =-1   |Az élettartam engedélyezve van. Az adott tétel soha nem jár le.|
|TTL = 2000 |Az élettartam engedélyezve van. Az tétel 2000 másodperc után lejár.|


### <a name="example-3"></a>3\. példa

A tárolón lévő TTL értéke 1000 (DefaultTimeToLive = 1000)

|TTL az elemnél| Eredmény|
|---|---|
|TTL = null|    Az élettartam engedélyezve van. Az tétel 1000 másodperc (alapértelmezett) után lejár.|
|TTL =-1   |Az élettartam engedélyezve van. Az adott tétel soha nem jár le.|
|TTL = 2000 |Az élettartam engedélyezve van. Az tétel 2000 másodperc után lejár.|

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan konfigurálhatja az élettartamot a következő cikkekben:

* [Az élettartam konfigurálása](how-to-time-to-live.md)
