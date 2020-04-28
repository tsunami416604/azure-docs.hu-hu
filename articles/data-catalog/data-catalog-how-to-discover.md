---
title: Adatforrások felderítése Azure Data Catalog
description: Ez a cikk azt mutatja be, hogyan lehet felderíteni a regisztrált adategységeket Azure Data Catalogekkel, beleértve a keresést és a szűrést, valamint a Azure Data Catalog portál találatok kiemelési képességeinek használatát.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b12cb94832a1ea977fb13f5f2271984dc8780cee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "68736377"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Adatforrások felderítése Azure Data Catalog

## <a name="introduction"></a>Introduction (Bevezetés)

A Azure Data Catalog egy teljes körűen felügyelt felhőalapú szolgáltatás, amely a vállalati adatforrások regisztrálási és felderítési rendszereként szolgál. Más szóval a Data Catalog segít az embereknek felderíteni, értelmezni és használni az adatforrásokat. Segíti a szervezeteket abban, hogy minél több értéket kapjanak a meglévő adatokból. Ha egy adatforrás regisztrálva van Data Catalog, a metaadatokat a szolgáltatás indexeli, így könnyedén keresheti meg a szükséges adatokat.

## <a name="searching-and-filtering"></a>Keresés és szűrés

A Data Catalog felderítése két elsődleges mechanizmust használ: a keresést és a szűrést.

A keresés nem csupán magától értetődő, de rendkívül hatékony is. Alapértelmezés szerint a keresőkifejezéseket a rendszer összeveti a katalógusban szereplő összes tulajdonsággal, még a felhasználók által beírt dekorációkkal is.

A szűrés a keresést hivatott kiegészíteni. Kiválaszthat bizonyos jellemzőket, például a szakértőket, az adatforrás típusát, az Objektumtípus és a címkéket. Csak a megfelelő adategységeket lehet megtekinteni, és a keresési eredményeket a megfelelő eszközökre korlátozhatja.

A keresés és szűrés együttes használatával gyorsan megkeresheti a Data Catalog regisztrált adatforrásokat, hogy felderítse a szükséges adatforrásokat.

## <a name="search-syntax"></a>Keresési szintaxis

Bár az alapértelmezett ingyenes szöveges keresés egyszerű és intuitív, Data Catalog keresési szintaxist is használhatja a keresési eredmények nagyobb mértékű szabályozásához. Data Catalog a keresés a következő technikákat támogatja:

| Módszer | Használat | Példa |
| --- | --- | --- |
| Alapszintű keresés |Egyszerű keresés, amely egy vagy több keresési kifejezést használ. Az eredmény bármely olyan objektum, amely egy vagy több megadott kifejezéssel egyező tulajdonságnak felel meg. |`sales data` |
| Tulajdonság hatóköre |Csak azokat az adatforrásokat adja vissza, amelyekben a keresési kifejezés megfelel a megadott tulajdonságnak. |`name:finance` |
| Logikai operátorok |A keresés kiszélesítése vagy szűkítése logikai műveletek használatával. |`finance NOT corporate` |
| Csoportosítás zárójelekkel |A lekérdezés részeit zárójelek közé használva logikai elkülönítést érhet el, különösen a logikai operátorokkal együtt. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Összehasonlító operátorok |A numerikus és a dátum adattípusú tulajdonságok esetében a nem egyenlő összehasonlítások használata. |`modifiedTime > "11/05/2014"` |

Data Catalog kereséssel kapcsolatos további információkért tekintse meg a [Azure Data Catalog](/rest/api/datacatalog/#search-syntax-reference) cikket.

## <a name="hit-highlighting"></a>Találatok kiemelése

A keresési eredmények megtekintésekor a megadott keresési kifejezéseknek (például az adategység nevének, leírásának és a címkéknek) megfelelő megjelenített tulajdonságok kiemelve lesznek, hogy könnyebben azonosítható legyen, hogy egy adott keresés miért adott vissza egy adott adategységet.

> [!NOTE]
> A találatok kiemelésének kikapcsolásához használja a **kiemelés** kapcsolót a Data Catalog portálon.

A keresési eredmények megtekintésekor előfordulhat, hogy nem mindig nyilvánvaló, hogy miért szerepel egy adategység, még akkor is, ha a találatok kiemelése engedélyezve van. Mivel az összes tulajdonságot alapértelmezés szerint keresi a rendszer, egy adategység is visszaküldhető egy oszlop szintű tulajdonság egyezése miatt. Mivel több felhasználó is láthatja a regisztrált adategységeket saját címkékkel és leírásokkal, nem minden metaadat jelenik meg a keresési eredmények listájában.

Az alapértelmezett csempe nézetben a keresési eredményekben megjelenő összes csempe tartalmaz egy **keresési kifejezés egyezés** ikont, így gyorsan megtekintheti a egyezések számát és helyét, valamint a kívánt helyre ugorhat.

 ![Találatok kiemelése és keresési egyezések a Azure Data Catalog portálon](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Összefoglalás

Mivel az adatforrások Data Catalog példányokkal való regisztrálása az adatforrásból a katalógus-szolgáltatásba másolja a strukturális és leíró metaadatokat, az adatforrás könnyebben felderíthető és értelmezhető lesz. Miután regisztrált egy adatforrást, az Data Catalog-portálon található szűrés és keresés használatával derítheti fel.

## <a name="next-steps"></a>További lépések

* Az adatforrások felderítésének részletes ismertetését lásd: [Azure Data Catalog első lépései](data-catalog-get-started.md).
