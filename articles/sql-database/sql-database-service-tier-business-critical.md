---
title: Üzleti kritikus szintet – Azure SQL Database szolgáltatás |} A Microsoft Docs
description: További tudnivalók az Azure SQL Database üzletileg kritikus
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 2f18b51a07bb58f13673427f0acadb6b80d7a1c8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005318"
---
# <a name="business-critical-tier---azure-sql-database"></a>Üzleti kritikus szintet – Azure SQL Database

> [!NOTE]
> Kritikus fontosságú üzleti szint DTU-vásárlási modell a prémium szintű neve. A Virtuálismag-alapú vásárlási modell a DTU-alapú vásárlási modell összehasonlításáért lásd: [vásárlási modellek és az erőforrások az Azure SQL Database](sql-database-purchase-models.md).

Az Azure SQL Database az SQL Server adatbázismotor architektúra, amely 99,99 %-os rendelkezésre állását, még akkor is, az infrastruktúra-hibák esetekben biztosítása érdekében a felhőalapú környezet módosul alapul. Nincsenek az Azure SQL Database által használt három architekturális modellek:
- Általános célú/Standard 
- Üzleti kritikus/prémium
- Rugalmas skálázás

Prémium és üzletileg kritikus szolgáltatási réteg modell egy olyan fürtjét, adatbázis-motor folyamatainak alapul. Ez a modell architekturális a tény, hogy nem mindig elérhető database engine csomópont és minimális hatása van a számítási feladatokra karbantartásának idejére is támaszkodik.

Az Azure frissíti, és javítások alapjául szolgáló operációs rendszert, illesztőprogramokat és az SQL Server adatbázismotor transzparens módon az a minimális-ideje a végfelhasználók számára. 

Az Azure SQL Database prémium és üzletileg kritikus szolgáltatási szinten a prémium szintű rendelkezésre állás engedélyezve van, és arra tervezték, intenzív számítási feladatokhoz, amelyek működését zavarják a teljesítmény romlása a folyamatban lévő karbantartási tevékenységek miatt.

A prémium szintű modellben az Azure SQL database egyesíti a számítási és a egy csomóponton. Négy csomópontból álló fürtben, az SQL Serverhez hasonló technológia segítségével telepítve (SQL Server adatbázismotor folyamat) számítási és tárolási (helyileg csatlakoztatott SSD) replikációs úgy érhető el magas rendelkezésre állású architektúra ebben a modellben [mindig bekapcsolva Rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Database engine fürtben](media/sql-database-managed-instance/business-critical-service-tier.png)

Mind az SQL database, a motor folyamata és az alapul szolgáló mdf/ldf-fájlok kerülnek, ugyanazon a csomóponton a helyileg csatlakoztatott SSD-tárolás biztosítja a számítási feladathoz alacsony késést biztosít. Magas rendelkezésre állású SQL Server hasonló technológia használatával lett megvalósítva [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Minden adatbázis, a fürt egy elsődleges adatbázis, amely elérhető ügyfél számítási feladata, és a egy három másodlagos folyamat adatok másolatát tartalmazó adatbázis-csomópont. Az elsődleges csomópont folyamatosan leküldi a módosítások a másodlagos csomópontot annak érdekében, hogy az adatok elérhető legyen a másodlagos replikákon. Ha bármilyen okból leáll az elsődleges csomópont. Feladatátvétel az SQL Server adatbázismotor kezeli – egy másodlagos másodpéldány az elsődleges csomópont válik, és annak biztosítására, elég a fürtben található csomópontok jön létre egy új másodlagos replikára. A számítási feladatok a rendszer automatikusan átirányítja az új elsődleges csomópontra.

Emellett a kritikus fontosságú üzleti fürt olyan beépített [olvasási kibővített](sql-database-read-scale-out.md) képesség, amely ingyenes, biztosít beépített csak olvasható csomópont csak olvasási lekérdezések futtatása (például a jelentések) teljesítményét nem befolyásolja használható díja az elsődleges munkaterhelés.

## <a name="when-to-choose-this-service-tier"></a>Ha a service-csomag kiválasztását?

Üzleti kritikus szolgáltatási szinten a közel valós idejű igénylő alkalmazásokhoz készült válaszokat az alapul szolgáló SSD-tárolás (átlagosan 1 – 2 ms), gyors helyreállítás, ha az alapul szolgáló infrastruktúra meghibásodik vagy kell arra a célra jelentések, elemzések, és csak olvasható az ingyenes díj olvasható másodlagos replika az elsődleges adatbázis a lekérdezéseket.

## <a name="next-steps"></a>További lépések

- Ismerje meg [általános célú](sql-database-service-tier-general-purpose.md) és [nagy kapacitású](sql-database-service-tier-hyperscale.md) szinten.
- Ismerje meg [Service Fabric](../service-fabric/service-fabric-overview.md).
- A magas rendelkezésre állású és vész-helyreállítási További beállításokért lásd: [üzletmenet-folytonossági](sql-database-business-continuity.md).
