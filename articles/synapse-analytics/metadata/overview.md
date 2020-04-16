---
title: Az Azure Synapse Analytics megosztott metaadat-modellje
description: Az Azure Synapse Analytics lehetővé teszi, hogy a különböző munkaterületi számítási motorok adatbázisokat és táblákat osztanak meg a Spark-készletek (előzetes verzió), az SQL on-demand motor (előzetes verzió) és az SQL-készletek között.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 3b26d516080961a482a3ba67f314e98ece4c9f24
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424117"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Az Azure Synapse Analytics megosztott metaadatai

Az Azure Synapse Analytics lehetővé teszi, hogy a különböző munkaterületi számítási motorok adatbázisokat és táblákat osztanak meg a Spark-készletek (előzetes verzió), az SQL on-demand motor (előzetes verzió) és az SQL-készletek között.

[!INCLUDE [preview](../includes/note-preview.md)]



A megosztás támogatja az úgynevezett modern adattárház mintát, és hozzáférést biztosít a munkaterületI SQL-motorok számára a Sparkkal létrehozott adatbázisokhoz és táblákhoz. Azt is lehetővé teszi, hogy az SQL-motorok saját objektumokat hozzanak létre, amelyek nem vannak megosztva a többi motorral.

## <a name="support-the-modern-data-warehouse"></a>A modern adattárház támogatása

A megosztott metaadatmodell a következő módon támogatja a modern adattárház-mintát:

1. A Data Lake-ből származó adatok előkészítése és a Spark hatékonyan épül fel az előkészített adatok (esetleg particionált) parketta-támogatott táblákban esetleg több adatbázisban található.

2. A Spark létrehozott adatbázisok és az összes táblájuk láthatóvá válik az Azure Synapse-munkaterület Spark-készlet példányai, és a Spark-feladatok bármelyikéből használható. Ez a képesség az [engedélyek](#security-model-at-a-glance) től függ, mivel egy munkaterület összes Spark-készlete ugyanazt az alapul szolgáló katalógus metatárolót használja.

3. A Spark létrehozott adatbázisok és a parketta-támogatott táblák láthatóvá válnak a munkaterület SQL on-demand motor. [Az adatbázisok](database.md) automatikusan létrejönnek az SQL igény szerinti metaadataiban, és mind a Spark-feladat által létrehozott [külső, mind a felügyelt táblák](table.md) külső táblákként érhetők el az SQL igény szerinti metaadataiban a `dbo` megfelelő adatbázis sémájában. <!--For more details, see [ADD LINK].-->

4. Ha a munkaterületen vannak olyan SQL-készletpéldányok, amelyek metaadat-szinkronizálása engedélyezve van <!--[ADD LINK]--> vagy ha egy új SQL-készletpéldány jön létre a metaadat-szinkronizálás engedélyezve van, a Spark létrehozott adatbázisok és a parketta által támogatott táblák automatikusan leleszképezve az SQL-készlet adatbázisában leírt [Azure Synapse Analytics megosztott adatbázis.](database.md)

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Az objektumszinkronizálás aszinkron módon történik. Az objektumok némi késéssel jelennek meg az SQL környezetben. Miután megjelentek, lekérdezhetők, de nem frissíthetők, és nem módosíthatók az SQL-motorok, amelyek hozzáférhetnek.

## <a name="which-metadata-objects-are-shared"></a>Mely metaadat-objektumok vannak megosztva

A Spark lehetővé teszi adatbázisok, külső táblák, felügyelt táblák és nézetek létrehozását. Mivel a Spark-nézetek a definiáló Spark SQL-utasítás feldolgozásához Spark-motort igényelnek, és sql motor nem dolgozható fel, csak az adatbázisok és a parkettatárolási formátumot használó, tartalmazott külső és felügyelt táblák vannak megosztva a munkaterületi SQL-motorokkal. Spark-nézetek csak a Spark-készlet példányai között vannak megosztva.

## <a name="security-model-at-a-glance"></a>Biztonsági modell áttekintése

A Spark-adatbázisok és táblák, valamint az SQL-motorok szinkronizált ábrázolásai az alapul szolgáló tárolási szinten vannak biztosítva. Ha a táblát a lekérdezésbeküldő által használható motorok lekérdezik, a lekérdezés-beküldő rendszerbiztonsági tagja átkerül az alapul szolgáló fájlokba. Az engedélyek ellenőrzése a fájlrendszer szintjén van bejelölve.

További információ: [Azure Synapse Analytics shared database](database.md).

## <a name="change-maintenance"></a>Karbantartás módosítása

Ha egy metaadat-objektumot törölnek vagy módosítanak a Sparkkal, a rendszer felveszi és propagálja a módosításokat az SQL igény szerinti motorjára és az objektumokat szinkronizáló SQL-készletekre. A szinkronizálás aszinkron, és a változások rövid késleltetés után megjelennek az SQL-motorokban.

## <a name="next-steps"></a>További lépések

- [További információ az Azure Synapse Analytics megosztott metaadat-adatbázisairól](database.md)
- [További információ az Azure Synapse Analytics megosztott metaadat-táblázatairól](table.md)

