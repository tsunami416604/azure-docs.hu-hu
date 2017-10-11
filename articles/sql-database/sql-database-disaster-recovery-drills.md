---
title: "SQL adatbázis Vészhelyreállítások részletezésének |} Microsoft Docs"
description: "További útmutatás és ajánlott eljárások az Azure SQL Database segítségével hajtsa végre a vészhelyreállítások részletezésének védheti a kritikus kritikus hibák és a kimaradások esetén refs üzleti alkalmazások."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: b44a269c-fe2a-404f-b013-290030860bd1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 07/31/2016
ms.author: sashan
ms.openlocfilehash: 1b1d65a41a794a566287dcffe3581ac58e2a965f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="performing-disaster-recovery-drill"></a>Vész-helyreállítási részletezési végrehajtása
Javasoljuk, hogy az alkalmazás felkészültségét helyreállítási munkafolyamat érvényesség rendszeres időközönként. Az alkalmazás viselkedését, és a megvalósítását ellenőrzése az adatvesztéssel és/vagy a problémákat, hogy a feladatátvétel magában foglalja a mérnöki célszerű. A legtöbb iparági normák szerint üzleti folytonossági hitelesítő részeként követelmény egyben.

A vész-helyreállítási részletezési végrehajtása áll:

* Amelyek adatok réteg leállás
* Helyreállítása
* Alkalmazás integritási utáni helyreállítás ellenőrzése

Attól függően, hogy hogyan meg [az alkalmazás az üzletmenet folytonossága érdekében tervezett](sql-database-business-continuity.md), a munkafolyamat végrehajtása a részletezési eltérőek lehetnek. Az alábbiakban azt írják le az ajánlott eljárások végrehajtása egy vész-helyreállítási részletezéshez Azure SQL adatbázis környezetében.

## <a name="geo-restore"></a>Georedundáns helyreállítás
A vész-helyreállítási részletezési végző az esetleges adatvesztés elkerülése ajánlott másolat készítését az üzemi környezetben, és felhasználja az alkalmazás feladatátvételi munkafolyamat ellenőrizze egy tesztkörnyezetben használva részletezési végrehajtása.

#### <a name="outage-simulation"></a>Kimaradás szimulálása
A leállás szimulálása, törölheti vagy nevezze át a forrás-adatbázis. Ennek hatására a kapcsolat alkalmazáshibák.

#### <a name="recovery"></a>Helyreállítás
* Hajtsa végre a georedundáns helyreállítás az adatbázis egy másik kiszolgálóra történő leírtak [Itt](sql-database-disaster-recovery.md).
* Módosítsa az alkalmazás konfigurációját, és csatlakozzon a helyreállított adatbázis, és kövesse a [adatbázis konfigurálnia a helyreállítás után](sql-database-disaster-recovery.md) útmutató, hogy elvégezhesse a helyreállítást.

#### <a name="validation"></a>Ellenőrzés
* Fejezze be a részletezési (beleértve a kapcsolati karakterláncok, bejelentkezéseket, alapvető funkciókat tesztelési, illetve más megszokott alkalmazástelepítési signoffs eljárások érvényesítést része) alkalmazás-integritási utáni helyreállítás ellenőrzése.

## <a name="geo-replication"></a>Georeplikáció
A részletezés gyakorlat georeplikáció használatával védett adatbázis magában foglalja a tervezett feladatátvételt a másodlagos adatbázis. A tervezett feladatátvétel biztosítja, hogy az elsődleges és a másodlagos adatbázisok maradjanak szinkronban a szerepkörök bekapcsolásakor. Ellentétben a nem tervezett feladatátvétel után ez a művelet eredményez adatvesztés, a részletezés az éles környezetben hajtható végre.

#### <a name="outage-simulation"></a>Kimaradás szimulálása
A szolgáltatáskimaradás szimulálása, letilthatja a webes alkalmazás vagy a virtuális gép csatlakozott az adatbázishoz. Ennek eredményeképp a csatlakozási hibák az ügyfelek számára.

#### <a name="recovery"></a>Helyreállítás
* Győződjön meg arról, hogy az alkalmazás konfigurációját, a vész-Helyreállítási régióban mutat, a korábbi másodlagos, amely a teljes elérhető új elsődleges válik.
* Hajtsa végre [tervezett feladatátvétel](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) egy új elsődleges a másodlagos adatbázis létrehozásához.
* Kövesse a [adatbázis konfigurálnia a helyreállítás után](sql-database-disaster-recovery.md) útmutató, hogy elvégezhesse a helyreállítást.

#### <a name="validation"></a>Ellenőrzés
* Fejezze be a részletezési (beleértve a kapcsolati karakterláncok, bejelentkezéseket, alapvető funkciókat tesztelési, illetve más megszokott alkalmazástelepítési signoffs eljárások érvényesítést része) alkalmazás-integritási utáni helyreállítás ellenőrzése.

## <a name="next-steps"></a>Következő lépések
* Üzleti folytonosság forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [folytonosságának forgatókönyvek](sql-database-business-continuity.md)
* További tudnivalók az Azure SQL adatbázis automatikus biztonsági mentés című [SQL-adatbázis automatikus biztonsági mentés](sql-database-automated-backups.md)
* A helyreállítás automatikus biztonsági mentés használatával kapcsolatos további tudnivalókért lásd: [adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági másolatból](sql-database-recovery-using-backups.md)
* Gyorsabb helyreállítási beállításokkal kapcsolatos további tudnivalókért lásd: [aktív georeplikáció](sql-database-geo-replication-overview.md)  
