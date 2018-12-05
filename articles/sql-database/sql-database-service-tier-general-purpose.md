---
title: Általános célú csomagban – Azure SQL Database szolgáltatás |} A Microsoft Docs
description: További tudnivalók az Azure SQL Database – általános célú csomagban
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 8b708818584be6cdb84530ce008295ccee080d94
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52883131"
---
# <a name="general-purpose-tier---azure-sql-database"></a>Általános célú csomagban – Azure SQL Database

> [!NOTE]
> Általános célú csomagban nevezzük Standard dtu-vásárlási modell. A Virtuálismag-alapú vásárlási modell a DTU-alapú vásárlási modell összehasonlításáért lásd: [vásárlási modellek és az erőforrások az Azure SQL Database](sql-database-service-tiers.md).

Az Azure SQL Database igazítani a felhőalapú környezet még infrastruktúra hibák esetekben a 99,99 %-os rendelkezésre állás biztosítása érdekében az SQL Server adatbázismotor-architektúra alapján történik. Nincsenek az Azure SQL Database által használt három architekturális modellek:
- Általános rendeltetés 
- Üzletileg kritikus
- Rugalmas skálázás

Általános célú modell egy számítási és tárolási szétválasztása alapul. Ez a modell architekturális támaszkodik a magas rendelkezésre állású, és akkor fordul elő, megbízhatóságát, az Azure Premium Storage, amely transzparens módon replikálja az adatbázisfájlokat, és garantálja az adatvesztés nélkül, ha az alapul szolgáló infrastruktúra hibája.

A következő ábrán látható négy csomópont elkülönített számítási és tárolási rétegekkel standard architekturális modellben.

![Számítási és tárolási szétválasztása](media/sql-database-managed-instance/general-purpose-service-tier.png)

Az általános célú modell az alábbi két réteg:

- Állapot nélküli számítási rétegét, amelyen fut a `sqlserver.exe` feldolgozásához, és csak átmeneti és a gyorsítótárazott adatokat tartalmazza (például – tervgyorsítótárból, pufferkészletben, oszlop store készlet). Ez az állapot nélküli SQL Server-csomóponton, inicializálja a folyamatot, a csomópont állapotát vezérli és hajt végre feladatátvételt egy másik helyre, szükség esetén az Azure Service Fabric szolgáltatást.
- Állapot-nyilvántartó adatrétege-adatbázisfájlokat (.mdf/.ldf), amely az Azure Premium Storage szolgáltatásban tárolódnak. Az Azure Storage garantálja, hogy az bármilyen adatbázis-fájlban lévő bármelyik rekordra adatvesztés nélkül lesz. Az Azure Storage rendelkezik beépített rendelkezésre állása/adatredundancia, amely biztosítja, hogy a naplófájl minden rekord vagy adatfájlban lap megőrzi még akkor is, ha az SQL Server-folyamat leáll.

Alapul szolgáló infrastruktúra egy része sikertelen lesz, amikor az adatbázis-kezelő vagy az operációs rendszer frissítve van, vagy az SQL Server-folyamat kritikus probléma észlelése esetén az Azure Service Fabric az állapot nélküli SQL Server-folyamat áthelyezi egy másik az állapot nélküli számítási csomóponton. Nincs meg, ha az elsődleges csomópont feladatátvétel történik, feladatátvételi idő minimalizálása érdekében új számítási szolgáltatás váró tartalék csomópontok. Nincs hatással az Azure Storage-rétegből az adatokat, és adatokat vagy naplófájlokat csatolt újrainicializált SQL Server folyamatán. Ez a folyamat garantálja a 99,99 %-os rendelkezésre állást, de előfordulhat, hogy rendelkezik néhány teljesítményére nagy terhelést, amely miatt a váltás ideje fut-e a, és azt a tényt az új SQL Server-csomópont hideg gyorsítótáras kezdődik.

## <a name="when-to-choose-this-service-tier"></a>Ha a service-csomag kiválasztását?

Általános célú szolgáltatási szinten egy alapértelmezett szolgáltatási rétegben az általános számítási feladatok többsége tervezett, Azure SQL Database-ben. Ha egy teljes körűen felügyelt adatbázis-kezelő a tárolóeszközök késése 5-10 ms, amely megfelel a legtöbb esetben az Azure az SQL IaaS között 99,99 %-os SLA-val, általános célú csomagban lehetőség az Ön számára.

## <a name="next-steps"></a>További lépések

- Ismerje meg [üzletileg kritikus fontosságú](sql-database-service-tier-business-critical.md) és [nagy kapacitású](sql-database-service-tier-hyperscale.md) szinten.
- Ismerje meg [Service Fabric](../service-fabric/service-fabric-overview.md).
- A magas rendelkezésre állású és vész-helyreállítási További beállításokért lásd: [üzletmenet-folytonossági](sql-database-business-continuity.md).
