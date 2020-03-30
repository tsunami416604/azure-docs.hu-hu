---
title: Adatok lejárata az Azure Cosmos DB-ben az élő idővel
description: A TTL segítségével a Microsoft Azure Cosmos DB lehetővé teszi, hogy a dokumentumok egy idő után automatikusan törlődjenek a rendszerből.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: 5407c38f33d167ff5114cd55878e3470e7248d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188720"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Élettartam (TTL) az Azure Cosmos DB-ben 

Az Azure Cosmos DB az **Élő idő** vagy a TTL használatával lehetővé teszi az elemek automatikus törlését egy tárolóból egy bizonyos idő elteltével. Alapértelmezés szerint beállíthatja, hogy az idő a tároló szintjén éljen, és felülbírálja az értéket cikkenként. Miután beállította a TTL egy tárolóban vagy egy elem szintjén, az Azure Cosmos DB automatikusan eltávolítja ezeket az elemeket az időszak után, mivel az utolsó módosítás óta. Az élő értékhez szükséges idő másodpercek alatt van konfigurálva. A TTL konfigurálásakor a rendszer automatikusan törli a lejárt elemeket a TTL érték alapján, anélkül, hogy az ügyfélalkalmazás által kifejezetten kiadott törlési műveletre lenne szükség.

A lejárt elemek törlése olyan háttérfeladat, amely a megmaradt [kérelemegységeket](request-units.md), azaz a felhasználói kérelmek által fel nem számított kérelemegységeket használja fel. Még a TTL lejárta után is, ha a tároló túl van terhelve kérésekkel, és ha nincs elég RU rendelkezésre álló, az adatok törlése késik. Az adatok törlődnek, ha elegendő rt-áll rendelkezésre a törlési művelet végrehajtásához. Bár az adatok törlése késik, az adatokat nem adja vissza semmilyen lekérdezések (bármely API) után a TTL lejárta után.

## <a name="time-to-live-for-containers-and-items"></a>Ideje élni a konténerek és elemek

Az élő érték másodpercben van beállítva, és az elem utolsó módosításának időpontjától kezdve különbözetként lesz értelmezve. Beállíthatja, hogy az élet egy tárolóban vagy egy elem a tárolón belül:

1. **Az élethez való idő egy tárolóban** (a használatával beállítva): `DefaultTimeToLive`

   - Ha hiányoznak (vagy null értékre vannak állítva), az elemek nem járnak le automatikusan.

   - Ha a jelen és az érték értéke "-1", akkor végtelen, és az elemek alapértelmezés szerint nem járnak le.

   - Ha a jelen és az érték *bizonyos számú "n"* értékre van állítva – az elemek az utolsó módosítási időpont után *másodpercekkel "n"* lejárnak.

2. **Életidő egy elemen** (a következő vel beállítva): `ttl`

   - Ez a tulajdonság `DefaultTimeToLive` csak akkor alkalmazható, ha jelen van, és nincs null értékre állítva a szülőtárolóesetében.

   - Ha jelen van, `DefaultTimeToLive` felülírja a szülőtároló értékét.

## <a name="time-to-live-configurations"></a>Az élő konfigurációkhoz való idő

* Ha a TTL beállítása *"n"* egy tárolón, akkor a tárolóban lévő elemek *n* másodperc után lejárnak.  Ha ugyanabban a tárolóban vannak olyan elemek, amelyeknek saját idejük van az életre, állítsa a -1 értékre (ami azt jelzi, hogy nem járnak le), vagy ha egyes elemek felülbírálták az élethez szükséges időt egy másik számmal, ezek az elemek a saját konfigurált TTL-értékük alapján járnak le. 

* Ha a TTL nincs beállítva egy tárolón, akkor a tárolóban lévő elemen való életidő nek nincs hatása. 

* Ha egy tárolóTTL beállítása -1, a tárolóban lévő olyan elem, amelynek élettartama n, n másodperc után lejár, és a fennmaradó elemek nem járnak le.

## <a name="examples"></a>Példák

Ez a szakasz néhány példát mutat be, amelyekkülönböző ideig vannak a tárolóhoz és az elemekhez rendelt élő értékek:

### <a name="example-1"></a>1. példa

A tárolón lévő TTL értéke null (DefaultTimeToLive = null)

|TTL a cikken| Eredmény|
|---|---|
|ttl = null|    A TTL le van tiltva. Az elem soha nem jár le (alapértelmezett).|
|ttl = -1   |A TTL le van tiltva. Az elem soha nem jár le.|
|ttl = 2000 |A TTL le van tiltva. Az elem soha nem jár le.|


### <a name="example-2"></a>2. példa

A tárolón lévő TTL beállítása -1 (DefaultTimeToLive = -1)

|TTL a cikken| Eredmény|
|---|---|
|ttl = null |A TTL engedélyezve van. Az elem soha nem jár le (alapértelmezett).|
|ttl = -1   |A TTL engedélyezve van. Az elem soha nem jár le.|
|ttl = 2000 |A TTL engedélyezve van. Az elem 2000 másodperc után lejár.|


### <a name="example-3"></a>3. példa

A tárolón lévő TTL beállítása 1000 (DefaultTimeToLive = 1000)

|TTL a cikken| Eredmény|
|---|---|
|ttl = null|    A TTL engedélyezve van. Az elem 1000 másodperc után lejár (alapértelmezett).|
|ttl = -1   |A TTL engedélyezve van. Az elem soha nem jár le.|
|ttl = 2000 |A TTL engedélyezve van. Az elem 2000 másodperc után lejár.|

## <a name="next-steps"></a>További lépések

További információ a Time to Live beállításáról az alábbi cikkekben:

* [A Time to Live beállítása](how-to-time-to-live.md)
