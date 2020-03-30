---
title: Adatforrások felderítése az Azure Data Catalogban
description: Ez a cikk bemutatja, hogyan fedezheti fel a regisztrált adatelemeket az Azure Data Catalog segítségével, beleértve a keresést és a szűrést, valamint az Azure Data Catalog portál találatkiemelési képességeinek használatát.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b12cb94832a1ea977fb13f5f2271984dc8780cee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736377"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Adatforrások felderítése az Azure Data Catalogban

## <a name="introduction"></a>Bevezetés

Az Azure Data Catalog egy teljes körűen felügyelt felhőszolgáltatás, amely a vállalati adatforrások regisztrációs és felderítési rendszereként szolgál. Más szóval a Data Catalog segítségével a személyek felderíthetik, megérthetik és használhatják az adatforrásokat. Segít a szervezeteknek abban, hogy a meglévő adataikból több értéket kapjanak. Miután egy adatforrás regisztrálva van a Data Catalog szolgáltatásban, a szolgáltatás indexeli a metaadatait, így könnyen kereshet a szükséges adatok felderítéséhez.

## <a name="searching-and-filtering"></a>Keresés és szűrés

A Data Catalog felderítése két elsődleges mechanizmust használ: a keresést és a szűrést.

A keresés nem csupán magától értetődő, de rendkívül hatékony is. Alapértelmezés szerint a keresőkifejezéseket a rendszer összeveti a katalógusban szereplő összes tulajdonsággal, még a felhasználók által beírt dekorációkkal is.

A szűrés a keresést hivatott kiegészíteni. Kiválaszthatja az okat olyan jellemzőket, mint a szakértők, az adatforrás típusa, az objektumtípusa és a címkék. Csak az egyező adatelemeket tekintheti meg, és a keresési eredményeket az egyező eszközökre korlátozhatja.

A keresés és a szűrés kombinációjával gyorsan navigálhat a Data Catalog ban regisztrált adatforrások között a szükséges adatforrások felderítéséhez.

## <a name="search-syntax"></a>Keresés szintaxisa

Bár az alapértelmezett szabad szöveges keresés egyszerű és intuitív, a data catalog keresési szintaxisát is használhatja a keresési eredmények jobb szabályozásához. A Data Catalog-keresés a következő technikákat támogatja:

| Módszer | Használat | Példa |
| --- | --- | --- |
| Egyszerű keresés |Egyszerű keresés, amely egy vagy több keresési kifejezést használ. Az eredmények olyan eszközök, amelyek bármely tulajdonságot egy vagy több megadott kifejezéssel egy vagy több tulajdonságnak egyeznek meg. |`sales data` |
| Tulajdonság hatóköre |Csak azokat az adatforrásokat adja vissza, amelyekhez a keresési kifejezés a megadott tulajdonsághoz igazodik. |`name:finance` |
| Logikai operátorok |Logikai műveletek használatával szélesítheti vagy szűkítheti a keresést. |`finance NOT corporate` |
| Csoportosítás zárójelgel |Zárójelek használatával csoportosítsa a lekérdezés egyes részeit a logikai elkülönítés elérése érdekében, különösen a logikai operátorokkal együtt. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Összehasonlító operátorok |A numerikus és dátumadat-típusokkal rendelkező tulajdonságok egyenlőségén kívül más összehasonlításokat is használhat. |`modifiedTime > "11/05/2014"` |

A data catalog-keresésről az [Azure Data Catalog](/rest/api/datacatalog/#search-syntax-reference) cikkében olvashat bővebben.

## <a name="hit-highlighting"></a>Találatok kiemelése

A keresési eredmények megtekintésekor a megadott keresési kifejezéseknek megfelelő megjelenített tulajdonságok (például az adateszköz neve, leírása és címkék) ki lesznek emelve, így könnyebben azonosítható, hogy egy adott adateszközt miért adott vissza egy adott keresés.

> [!NOTE]
> A találatkiemelésének kikapcsolásához használja a **Kiemelés** kapcsolót az Adatkatalógus portálon.

A keresési eredmények megtekintésekor előfordulhat, hogy nem mindig egyértelmű, hogy miért szerepel az adateszköz, még akkor is, ha engedélyezve van a leütéskiemelés. Mivel a rendszer alapértelmezés szerint az összes tulajdonságot keresi, előfordulhat, hogy egy oszlopszintű tulajdonság egyezése miatt adategységet ad vissza. És mivel több felhasználó is jegyzetekkel láthatja el a regisztrált adateszközöket a saját címkéivel és leírásaival, nem minden metaadat jelenik meg a keresési eredmények listájában.

Az alapértelmezett csempenézetben a keresési eredmények között megjelenő minden csempe tartalmaz egy **Keresési kifejezés-egyezések megtekintése** ikont, így gyorsan megtekintheti az egyezések számát és helyét, és szükség esetén rájuk ugorhat.

 ![Találatkiemelés és keresési egyezések az Azure Data Catalog portálon](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Összefoglalás

Mivel az adatforrás adatkatalógussal történő regisztrálása szerkezeti és leíró metaadatokat másol az adatforrásból a katalógusszolgáltatásba, az adatforrás könnyebben felderíthető és érthetőbbé válik. Miután regisztrált egy adatforrást, a Data Catalog portálon található szűrés és keresés használatával fedezheti fel.

## <a name="next-steps"></a>További lépések

* Az adatforrások felderítésével kapcsolatos részletes részleteket az Azure Data Catalog – Első lépések az [Azure Data Catalog használatával kapcsolatban](data-catalog-get-started.md)ismerteti.
