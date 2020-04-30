---
title: Az Azure szinapszis Analytics megosztott metaadat-modellje
description: Az Azure szinapszis Analytics lehetővé teszi, hogy a különböző munkaterület-számítási motorok adatbázisokat és táblákat osszanak meg a Spark-készletek (előzetes verzió), az SQL igény szerinti motorja (előzetes verzió) és az SQL-készletek között.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 3b26d516080961a482a3ba67f314e98ece4c9f24
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424117"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Azure szinapszis Analytics megosztott metaadatok

Az Azure szinapszis Analytics lehetővé teszi, hogy a különböző munkaterület-számítási motorok adatbázisokat és táblákat osszanak meg a Spark-készletek (előzetes verzió), az SQL igény szerinti motorja (előzetes verzió) és az SQL-készletek között.

[!INCLUDE [preview](../includes/note-preview.md)]



A megosztás támogatja az úgynevezett modern adattárház-mintát, és lehetővé teszi a munkaterület SQL-motorjai számára a Spark használatával létrehozott adatbázisok és táblák elérését. Azt is lehetővé teszi, hogy az SQL-motorok olyan saját objektumokat hozzanak létre, amelyek nem a többi motorral vannak megosztva.

## <a name="support-the-modern-data-warehouse"></a>A modern adattárház támogatása

A megosztott metaadat-modell a következő módon támogatja a modern adattárház-mintát:

1. A rendszer az adatközpontból származó adatok előkészítését és szerkezetbe állítását hatékonyan végzi el, ha az előkészített (esetlegesen particionált) parketta-alapú táblákat a valószínűleg több adatbázisban tárolja.

2. A Spark által létrehozott adatbázisok és minden táblázat láthatóvá válik az Azure szinapszis-munkaterület Spark Pool-példányaiban, és bármely Spark-feladatból felhasználható. Ez a képesség az [engedélyek](#security-model-at-a-glance) hatálya alá tartozik, mivel a munkaterület összes Spark-készlete ugyanazt a mögöttes katalógus-metaadatokat használja.

3. A Spark által létrehozott adatbázisok és a parketta által támogatott táblázatok láthatóvá válnak a munkaterület SQL igény szerinti motorjában. Az [adatbázisok](database.md) automatikusan létrejönnek az igény szerinti SQL-metaadatokban, és a Spark-feladatok által létrehozott [külső és felügyelt táblák](table.md) is elérhetők külső táblákként a megfelelő adatbázis `dbo` sémájában található SQL igény szerinti metaadatokban. <!--For more details, see [ADD LINK].-->

4. Ha vannak olyan SQL Pool-példányok a munkaterületen, amelyeken engedélyezve van a metaadatok szinkronizálása <!--[ADD LINK]--> vagy ha új SQL Pool-példányt hoz létre a metaadatok szinkronizálásával, a Spark által létrehozott adatbázisok és a hozzájuk tartozó parketta-alapú táblák automatikusan le lesznek képezve az SQL-készlet adatbázisába az [Azure szinapszis Analytics megosztott adatbázisa](database.md)című témakörben leírtak szerint.

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Az objektum szinkronizálása aszinkron módon történik. Az objektumok csak néhány másodperc elteltével fognak megjelenni az SQL-környezetben. Amint megjelentek, lekérdezheti, de nem frissítheti és nem változtathatja meg azokat az SQL-motorokat, amelyek hozzáférnek hozzájuk.

## <a name="which-metadata-objects-are-shared"></a>Mely metaadat-objektumok vannak megosztva

A Spark segítségével adatbázisok, külső táblák, felügyelt táblák és nézetek hozhatók létre. Mivel a Spark-nézetek megkövetelik, hogy a Spark-motor feldolgozza a definiált Spark SQL-utasítást, és nem dolgozható fel egy SQL-motor, csak az adatbázisok és a bennük tárolt külső és felügyelt táblák vannak megosztva a munkaterület SQL-motorokkal. A Spark-nézetek csak a Spark Pool-példányok között vannak megosztva.

## <a name="security-model-at-a-glance"></a>Biztonsági modell áttekintése

A Spark-adatbázisok és-táblák, valamint az SQL-motorokban lévő szinkronizált ábrázolások a mögöttes tárolási szinten vannak biztosítva. Ha a táblát bármely olyan motor kérdezi le, amelyet a lekérdezés küldője jogosult használni, a lekérdezés elküldő rendszerbiztonsági tagja átkerül az alapul szolgáló fájlokra. A rendszer a fájlrendszer szintjén ellenőrzi az engedélyeket.

További információ: az [Azure szinapszis Analytics megosztott adatbázisa](database.md).

## <a name="change-maintenance"></a>Karbantartás módosítása

Ha egy metaadat-objektumot törölnek vagy módosítanak a Sparktal, a módosításokat a rendszer átveszi és propagálja az SQL igény szerinti motorján, valamint az objektumokat szinkronizáló SQL-készleteken. A szinkronizálás aszinkron módon történik, és a módosítások rövid késleltetés után az SQL-motorokban is megjelennek.

## <a name="next-steps"></a>További lépések

- [További információ az Azure szinapszis Analytics megosztott metaadat-adatbázisairól](database.md)
- [További információ az Azure szinapszis Analytics megosztott metaadatait tartalmazó tábláiról](table.md)

