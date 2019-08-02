---
title: Azure SQL Database – általános célú és üzleti szempontból kritikus fontosságú | Microsoft Docs
description: A cikk a virtuális mag-alapú vásárlási modell általános célú és üzleti szempontból kritikus szolgáltatási rétegeit tárgyalja.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 02/23/2019
ms.openlocfilehash: decb4428321d5083d6ba7af134e223eb2fa5a912
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566703"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database szolgáltatási szintek

A Azure SQL Database SQL Server adatbázismotor-architektúrán alapul, amely a felhőalapú környezet számára lett kialakítva, hogy 99,99%-os rendelkezésre állást biztosítson, még akkor is, ha van infrastrukturális hiba. A Azure SQL Databaseban három szolgáltatási szintet használunk, amelyek mindegyike más építészeti modellel rendelkezik. A szolgáltatási szintek a következők:

- [Általános célú](sql-database-service-tier-general-purpose.md), amely a legtöbb általános számítási feladathoz lett tervezve.
- [Üzleti szempontból kritikus fontosságú](sql-database-service-tier-business-critical.md), amely kis késleltetésű számítási feladatokhoz készült, egyetlen olvasható replikával.
- A [nagy kapacitású](sql-database-service-tier-hyperscale.md)nagy méretű adatbázisokhoz készült (akár 100 TB-ig) több olvasható replikával.

Ez a cikk a virtuális mag-alapú vásárlási modell általános célú és üzleti szempontból kritikus szolgáltatási szintjeinek tárolási és biztonsági mentési szempontjait ismerteti.

> [!NOTE]
> További információ a nagy kapacitású szolgáltatási szintjéről a virtuális mag-alapú vásárlási modellben: [nagy kapacitású szolgáltatási réteg](sql-database-service-tier-hyperscale.md). A virtuális mag-alapú vásárlási modellnek a DTU-alapú vásárlási modellel való összehasonlítását lásd: [Azure SQL Database vásárlási modellek és erőforrások](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Adatkezelési és naplózási tár

Az alábbi tényezők befolyásolják az adatokhoz és a naplófájlokhoz használt tárterület méretét:

- A lefoglalt tárterületet az adatfájlok (MDF) és a naplófájlok (LDF) használják.
- Minden egyes adatbázis számítási mérete támogatja a maximális adatbázis-méretet, amely az alapértelmezett maximális méret 32 GB.
- A szükséges Egyadatbázisos méret (az MDF-fájl mérete) beállításakor a rendszer automatikusan hozzáadja a további tárterületet az LDF-fájlok támogatásához.
- A SQL Database felügyelt példányok tárolási méretét a 32 GB-os többszörösében kell megadni.
- Az egyes adatbázisok mérete 10 GB és a támogatott maximális érték között választható ki.
  - A standard vagy általános célú szolgáltatási rétegekben történő tároláshoz a 10 GB-os növekmények méretének növelése vagy csökkentése.
  - A prémium vagy üzleti szempontból kritikus szolgáltatási rétegekben a méret növeléséhez vagy csökkentéséhez növelje vagy csökkentse a 250 GB-os növekményeket.
- Az általános célú szolgáltatási `tempdb` szinten egy csatolt SSD-t használ, és ez a tárolási költség a virtuális mag ár részét képezi.
- Az üzleti szempontból kritikus szolgáltatási `tempdb` szinten megosztja a csatlakoztatott SSD-t az MDF-és az ldf-fájlokkal, és a `tempdb` tárolási költséget a virtuális mag ára tartalmazza.

> [!IMPORTANT]
> Az MDF-és az LDF-fájlokhoz lefoglalt teljes tárterületért kell fizetnie.

Az MDF-és az LDF-fájlok aktuális teljes méretének figyeléséhez használja a [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Az egyes MDF-és LDF-fájlok aktuális méretének figyeléséhez használja a [sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Biztonsági másolatok és tárolás

Az adatbázis-biztonsági másolatok tárolását a rendszer lefoglalta a SQL Database időponthoz kötött visszaállításának (PITR) és [hosszú távú adatmegőrzési (ltr)](sql-database-long-term-retention.md) képességeinek támogatásához. Ezt a tárolót külön kell lefoglalni az egyes adatbázisokhoz, és a számlázást két külön adatbázis-díj alapján számoljuk el.

- **PITR**: Az egyes adatbázisok biztonsági másolatait a rendszer automatikusan átmásolja az [olvasási hozzáférésű geo-redundáns (ra-GRS) tárolóba](../storage/common/storage-designing-ha-apps-with-ragrs.md) . A tárterület mérete dinamikusan növekszik, ahogy új biztonsági másolatok jönnek létre. A tárterületet hetente teljes biztonsági mentések, napi különbözeti biztonsági másolatok és tranzakciónapló-biztonsági másolatok használják, amelyek 5 percenként másolódnak át. A tárterület-felhasználás az adatbázis változásának és a biztonsági mentések megőrzési idejének a függvénye. Minden adatbázishoz külön megőrzési időtartamot állíthat be 7 és 35 nap között. Az adatbázis méretének 100 százalékkal (1x) megegyező minimális tárterülete külön díj nélkül elérhető. A legtöbb adatbázis esetében ez az érték elegendő a biztonsági másolatok 7 napos tárolására.
- **LTR**: A SQL Database akár 10 évig is beállíthatja a teljes biztonsági másolatok hosszú távú megőrzésének lehetőségét. Ha LTR szabályzatot állít be, akkor ezeket a biztonsági másolatokat az RA-GRS tárolóban automatikusan tárolja a rendszer, de szabályozhatja, hogy a biztonsági másolatok milyen gyakran legyenek átmásolva. A különböző megfelelőségi követelmények kielégítése érdekében a heti, havi és/vagy éves biztonsági mentések esetében eltérő megőrzési időt választhat. A kiválasztott konfiguráció határozza meg, hogy mekkora tárterületet használ a rendszer a LTR biztonsági mentések esetén. A LTR-tároló költségének megbecsléséhez használhatja a LTR árképzési számológépet. További információ: [SQL Database hosszú távú adatmegőrzés](sql-database-long-term-retention.md).

## <a name="next-steps"></a>További lépések

- Az általános célú és az üzleti szempontból kritikus szolgáltatási rétegekben elérhető egyedi számítási méretekről és tárolási méretekről az [önálló adatbázisok SQL Database virtuális mag-alapú erőforrás-korlátozásait](sql-database-vcore-resource-limits-single-databases.md)ismertető részben olvashat bővebben.
- Az általános célú és az üzleti szempontból kritikus szolgáltatási rétegekben található rugalmas készletekhez rendelkezésre álló számítási méretekről és tárolási méretekről a [rugalmas készletek SQL Database virtuális mag-alapú erőforrás-korlátozásait](sql-database-vcore-resource-limits-elastic-pools.md)ismertető cikk nyújt tájékoztatást.
