---
title: Az Azure Data Catalog az adatforrások felderítése
description: Ebben a cikkben korábban regisztrált adategységeket az Azure Data Cataloggal, beleértve a Keresés és szűrés és az Azure Data Catalog-portál a találatok kiemelését funkciói segítségével hogyan emeli ki.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 11216640a4f4f78e0b0de850f32f0ff85bbc9ef2
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407059"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Az Azure Data Catalog az adatforrások felderítése
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog egy teljes körűen felügyelt felhőszolgáltatás, amely a regisztráció és a vállalati adatforrások rendszerként. Más szóval a Data Catalog lehetővé teszi a felderítési megértésében, valamint használhatják az adatforrásokat, és segít a szervezeteknek a meglévő adatok kihasználása. A Data Catalog adatforrás regisztrálása után a indexeli metaadatait, a szolgáltatást, így könnyen kereshet felderítéséhez szükséges adatokat.

## <a name="searching-and-filtering"></a>A Keresés és szűrés
A Data Catalog felderítési elsődlegesen két mechanizmust használ: keresés és szűrés.

A keresés nem csupán magától értetődő, de rendkívül hatékony is. Alapértelmezés szerint a keresőkifejezéseket a rendszer összeveti a katalógusban szereplő összes tulajdonsággal, még a felhasználók által beírt dekorációkkal is.

A szűrés a keresést hivatott kiegészíteni. Kiválaszthatja az adott jellemzőkkel, például szakértők, adatforrás típusa, objektumtípus és címkék. Megtekintheti a csak egyező adategységeket, és korlátozhatja a keresési eredmények megfelelő eszközökkel.

A Keresés és szűrés együttes használatával, gyorsan válthat van szüksége az adatforrások felfedezése a Data catalogban regisztrált adatforrások.

## <a name="search-syntax"></a>Keresési szintaxis
Bár az alapértelmezett szabad szöveges keresés egyszerű, intuitív, a nagyobb mértékben vezérelheti a keresési eredmények között is használhatja a Data Catalog keresési szintaxis. Keresés az adatkatalógusban támogatja az alábbi technikák segítségével:

| Módszer | Használat | Példa |
| --- | --- | --- |
| Az egyszerű keresés |Az egyszerű keresés által használt egy vagy több keresőkifejezést. Eredmények minden olyan eszközök, amelyek megfelelnek egy vagy több megadott kifejezéssel tulajdonságai. |`sales data` |
| Tulajdonság keresése |Csak olyan adatforrásokat ad vissza, a keresési kifejezés egyeztetve van-e a megadott tulajdonságot. |`name:finance` |
| Logikai operátorok |Bővítheti vagy szűkítheti a keresést logikai műveletekkel által. |`finance NOT corporate` |
| Csoportosítás zárójelekkel |A lekérdezés csoport részei zárójelekkel logikai elkülönítés érdekében, különösen a logikai operátorokkal együtt használva az. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Összehasonlító operátorok |Nem egyenlő összehasonlítások használja a szám és adat adattípusú tulajdonságok esetében. |`modifiedTime > "11/05/2014"` |

A Data Catalog search kapcsolatos további információkért lásd: a [Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx) cikk.

## <a name="hit-highlighting"></a>Találatok kiemelése
Megtekintheti a keresési eredmények között, minden megjelenített tulajdonságok, amelyek megfelelnek a megadott keresési feltételeket (például az adatok objektum nevét, leírását és címkék) emel ki, hogy könnyebben azonosítható, hogy a megadott eszköz Miért adta vissza egy adott keresési.

> [!NOTE]
> Kapcsolja ki a találatok kiemelése, használja a **kiemelése** váltson a Data Catalog-portálon.
>
>

Amikor a keresési eredmények, azt mindig nem nyilvánvaló miért adategység megtalálható, még akkor is, a találatok kiemelése engedélyezve van. A Keresés az összes tulajdonság alapértelmezés szerint történik, mert egy adategységet előfordulhat, hogy vissza egyezést miatt oszlopszintű tulajdonság alapján. És mivel több felhasználó is adategységek ellátása dekorációkkal regisztrált a saját címkéket és a leírások, nem minden metaadat előfordulhat, hogy nem megjelennek a keresési eredmények listájában.

Az alapértelmezett csempe nézetben, a keresési eredmények között megjelenik minden csempe tartalmaz egy **nézet keresési kifejezés megfelel** ikonra, így gyorsan megtekintheti a száma, egyezések és helyét, és hozzájuk ugorhat, ha azt szeretné.

 ![Találatok kiemelése és az Azure Data Catalog-portálon megfelel a keresésnek](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Összegzés
Mivel a Data Catalog adatforrás regisztrálása másolja szerkezeti és leíró metaadatokat az adatforrásból a katalógus-szolgáltatáshoz, az adatforrás könnyebben megtalálhatóvá és értelmezhetővé válnak. Egy adatforrás regisztrálása után szűrés segítségével könnyűszerrel megkeresheti, és a Data Catalog-portál a keresni.

## <a name="next-steps"></a>További lépések
* Adatforrások felfedezése részletes ismertetését lásd: [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md).
