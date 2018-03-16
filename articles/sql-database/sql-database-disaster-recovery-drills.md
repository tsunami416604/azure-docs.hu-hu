---
title: "SQL adatbázis Vészhelyreállítások részletezésének |} Microsoft Docs"
description: "További útmutatás és ajánlott eljárások az Azure SQL Database a vészhelyreállítások részletezésének végrehajtásához."
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 10/20/2016
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 10a3891c51d20a26b946847d47bb712d97fa3c51
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="performing-disaster-recovery-drill"></a>Vész-helyreállítási részletezési végrehajtása
Javasoljuk, hogy az alkalmazás felkészültségét helyreállítási munkafolyamat érvényesség rendszeres időközönként. Az alkalmazás viselkedését, és a megvalósítását ellenőrzése az adatvesztéssel és/vagy a problémákat, hogy a feladatátvétel magában foglalja a mérnöki célszerű. A legtöbb iparági normák szerint üzleti folytonossági hitelesítő részeként követelmény egyben.

A vész-helyreállítási részletezési végrehajtása áll:

* Amelyek adatok réteg leállás
* Helyreállítása
* Alkalmazás integritási utáni helyreállítás ellenőrzése

Attól függően, hogy hogyan meg [az alkalmazás az üzletmenet folytonossága érdekében tervezett](sql-database-business-continuity.md), a munkafolyamat végrehajtása a részletezési eltérőek lehetnek. Ez a cikk ismerteti a vész-helyreállítási részletezéshez Azure SQL adatbázis környezetében végző az ajánlott eljárásokat.

## <a name="geo-restore"></a>Georedundáns helyreállítás
A vész-helyreállítási részletezési végző az esetleges adatvesztés elkerülése hajtsa végre a részletezési másolat készítését az üzemi környezetben, és felhasználja az alkalmazás feladatátvételi munkafolyamat ellenőrizze egy tesztkörnyezetben használva.

#### <a name="outage-simulation"></a>Kimaradás szimulálása
A leállás szimulálása, átnevezheti a forráshely adatbázisára. Ennek hatására a kapcsolat alkalmazáshibák.

#### <a name="recovery"></a>Helyreállítás
* Hajtsa végre a georedundáns helyreállítás az adatbázis egy másik kiszolgálóra történő leírtak [Itt](sql-database-disaster-recovery.md).
* Módosítsa az alkalmazás konfigurációját, és csatlakozzon a helyreállított adatbázis, és kövesse a [adatbázis konfigurálnia a helyreállítás után](sql-database-disaster-recovery.md) útmutató, hogy elvégezhesse a helyreállítást.

#### <a name="validation"></a>Ellenőrzés
* Fejezze be a részletezési (beleértve a kapcsolati karakterláncok, bejelentkezések, alapvető funkciókat tesztelési vagy más megszokott alkalmazástelepítési signoffs eljárások érvényesítést része) alkalmazás-integritási utáni helyreállítás ellenőrzése.

## <a name="failover-groups"></a>Feladatátvételi csoportok
A részletezés a gyakorlatban feladatátvételi csoportok használatával védett adatbázis, magában foglalja a tervezett feladatátvételt a másodlagos kiszolgáló. A tervezett feladatátvétel biztosítja, hogy az elsődleges és a másodlagos adatbázisok a feladatátvételi csoport maradjanak szinkronban a szerepkörök bekapcsolásakor. Ellentétben a nem tervezett feladatátvétel után ez a művelet eredményez adatvesztés, a részletezés az éles környezetben hajtható végre.

#### <a name="outage-simulation"></a>Kimaradás szimulálása
A szolgáltatáskimaradás szimulálása, letilthatja a webes alkalmazás vagy a virtuális gép csatlakozott az adatbázishoz. Ennek eredményeképp a csatlakozási hibák az ügyfelek számára.

#### <a name="recovery"></a>Helyreállítás
* Győződjön meg arról, hogy az alkalmazás konfigurációját, a vész-Helyreállítási régió pontokon, a korábbi másodlagos, amely a teljes elérhető új elsődleges válik.
* Kezdeményezzen [tervezett feladatátvétel](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) a feladatátvételi csoport a másodlagos kiszolgálóról.
* Kövesse a [adatbázis konfigurálnia a helyreállítás után](sql-database-disaster-recovery.md) útmutató, hogy elvégezhesse a helyreállítást.

#### <a name="validation"></a>Ellenőrzés
Fejezze be a részletezési ellenőrzése az alkalmazás integritási feladás egy vagy több helyreállítási (beleértve a kapcsolatot, alapvető funkciókat tesztelési vagy más érvényesítést a részletezési signoffs szükséges).

## <a name="next-steps"></a>További lépések
* Üzleti folytonosság forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [folytonosságának forgatókönyvek](sql-database-business-continuity.md).
* További tudnivalók az Azure SQL adatbázis automatikus biztonsági mentés című [SQL-adatbázis automatikus biztonsági mentés](sql-database-automated-backups.md)
* A helyreállítás automatikus biztonsági mentés használatával kapcsolatos további tudnivalókért lásd: [adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági másolatból](sql-database-recovery-using-backups.md).
* Gyorsabb helyreállítási beállításokkal kapcsolatos további tudnivalókért lásd: [aktív georeplikáció és feladatátvételi csoportok](sql-database-geo-replication-overview.md).  
