---
title: "Az Azure Data Catalog adatforrások felfedezése |} Microsoft Docs"
description: "Ez a cikk emel ki, hogyan találhat meg regisztrált adategységeket az Azure Data Catalog, beleértve a Keresés és szűrés és az Azure Data Catalog-portál a találati kijelölő képességeivel."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 9ff67dcb5ecb00440f73f979fd8d2b79a570c674
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Az Azure Data Catalog adatforrások felfedezése
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog egy teljes körűen felügyelt felhőszolgáltatás, amely a regisztráció és a vállalati adatforrások felderítését a rendszer funkcionál. Más szóval a Data Catalog segít személyek felderítése, megismeréséhez és használatához adatforrások, és segít a szervezeteknek több érték lekérése a meglévő adatokat. A Data Catalog egy adatforrás regisztrálása után a metaadatait indexelik a szolgáltatást, így könnyen kereshet felderítéséhez szükséges adatok.

## <a name="searching-and-filtering"></a>A Keresés és szűrés
A Data Catalog felderítési két elsődleges mechanizmus használja: a Keresés és szűrés.

A keresés nem csupán magától értetődő, de rendkívül hatékony is. Alapértelmezés szerint a keresőkifejezéseket a rendszer összeveti a katalógusban szereplő összes tulajdonsággal, még a felhasználók által beírt dekorációkkal is.

A szűrés a keresést hivatott kiegészíteni. Kiválaszthatja, hogy adott jellemzőiket, például a szakértők, adatforrástípust, objektumtípus és címkék. Csak megfelelő adategységeket megtekintheti, és korlátozhatja a keresési eredményeket annak megfelelő eszközökkel.

A Keresés és szűrés együttes használatával a Data Catalog kell adatforrások felfedezése regisztrált adatforrások gyorsan lépjen.

## <a name="search-syntax"></a>Keresési szintaxis
Bár az alapértelmezett szabad szöveges keresés egyszerű és intuitív, is használható a Data Catalog keresési szintaxisának nagyobb mértékben vezérelheti a keresési eredmények között. Data Catalog keresési a következő módszereket támogatja:

| Módszer | Használat | Példa |
| --- | --- | --- |
| Az egyszerű keresés |Az egyszerű keresés által használt egy vagy több keresési feltételeket. A eredményei bármely eszközök, amelyek megfelelnek egy vagy több meghatározott egyik tulajdonságnak sem. |`sales data` |
| Tulajdonság keresése |Csak az adatforrások, ahol a keresési kifejezés egyeztetve van-e a megadott tulajdonság visszaadása. |`name:finance` |
| Logikai operátorok |Bővíthetők, vagy a megadásával szűkíthető, a Keresés logikai műveletekkel. |`finance NOT corporate` |
| Csoportosítás zárójelekkel |A lekérdezés csoport részei zárójelekkel segítségével logikai elkülönítés érdekében, különösen a logikai operátorokkal együtt használva. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Összehasonlító operátorok |Nem egyenlő összehasonlítások is használni azokhoz a tulajdonságokhoz, szám és adat adattípusúak. |`modifiedTime > "11/05/2014"` |

A Data Catalog keresési kapcsolatos további információkért tekintse meg a [Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx) cikk.

## <a name="hit-highlighting"></a>Találatok kiemelése
Amikor megtekinti a keresési eredmények között, minden megjelenített tulajdonságok (például az adatok eszköz neve, leírása és címkék) a megadott keresési feltételeknek megfelelő vannak kiemelve abba, hogy könnyebben azonosíthatja, ezért a megadott eszköz által visszaadott egy adott keresési.

> [!NOTE]
> A találatok kiemelése kikapcsolásához használja a **kiemelése** kapcsoló számára a Data Catalog-portált.
>
>

Amikor a keresési eredmények között, nem mindig lehet nyilvánvaló ezért egy adategységet megtalálható, még akkor is kiemeléssel találati engedélyezve van. Az összes tulajdonság figyelembe veszi a keresésnél alapértelmezés szerint, mert egy adategységet esetleg visszaadott egyezés miatt az oszlopszintű tulajdonság. És több felhasználó megjegyzéseket fűzhet regisztrált adategységeket saját címkéket és leírásokat, mert nem minden metaadat előfordulhat jelenik meg a keresési eredmények listájában.

Az alapértelmezett mozaik elrendezés nézet, minden egyes csempe jelenik meg a keresési eredmények tartalmaz egy **nézet keresési kifejezés megegyezik** ikonra, így gyorsan megtekintheti a találatok és a helyét, valamint ugrani rájuk, ha azt szeretné.

 ![Találati kiemelve, és megkeresi a megadott Azure Data Catalog-portálon](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Összefoglalás
Mivel a Data Catalog egy adatforrás regisztrálása másolja szerkezeti és leíró metaadatok az adatforrásból a katalógus szolgáltatáshoz, az adatforrás könnyebben megtalálhatóvá és értelmezhetővé válik. Egy adatforrás regisztrálása után tudja azt deríteni szűrés használatával, és a keresni a Data Catalog-portált.

## <a name="next-steps"></a>Következő lépések
* További adatforrások felfedezése kapcsolatos részletes információkért lásd: [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md).
