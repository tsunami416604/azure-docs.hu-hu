---
title: Az SQL Database vészhelyreállítási próbák |} A Microsoft Docs
description: Ismerje meg, útmutatást és ajánlott eljárásait vészhelyreállítási próba végrehajtása az Azure SQL Database használatával.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 5d754ae558d485036a9a55f573a3f40162ed9f84
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55561127"
---
# <a name="performing-disaster-recovery-drill"></a>Vészhelyreállítási próba végrehajtása

Javasoljuk, hogy az alkalmazás készenléti állapota helyreállítási munkafolyamat érvényesítése rendszeres időközönként történik. Az alkalmazások működését és a következmények ellenőrzése az adatvesztést és/vagy a megszakítások időtartamát, hogy a feladatátvétel magában foglalja a mérnöki célszerű. Emellett akkor is követelmény a legtöbb iparági normák szerint üzleti folytonossági hitelesítő részeként.

Vészhelyreállítási próba végrehajtása áll:

* Szimuláló üzemkimaradások esetére szint
* Helyreállítás alatt
* Alkalmazás-integritás utáni helyreállítás ellenőrzése

Attól függően, hogy miként, [megtervezni az alkalmazás az üzletmenet folytonosságának](sql-database-business-continuity.md), a munkafolyamat végrehajtása a részletezés eltérőek lehetnek. Ez a cikk egy vészhelyreállítási próbát az Azure SQL Database környezetében végző ajánlott eljárásokat ismertetjük.

## <a name="geo-restore"></a>Georedundáns helyreállítás

Végző vészhelyreállítási próbát az esetleges adatvesztés megelőzése hajtsa végre a részletezés egy tesztkörnyezetben használatával hoz létre egy példányát az éles környezetben, és ellenőrizze az alkalmazást feladatátvételi munkafolyamat használatával.

### <a name="outage-simulation"></a>Szolgáltatáskimaradás szimuláció

A szolgáltatáskiesés megszüntetése után a szimulálásához átnevezheti a forrásadatbázisban. Ezt a névmódosítást alkalmazások csatlakozási hibái miatt.

### <a name="recovery"></a>Helyreállítás

* Hajtsa végre a geo-visszaállítás, az adatbázis egy másik kiszolgálóra leírtak szerint [Itt](sql-database-disaster-recovery.md).
* Módosítsa az alkalmazás konfigurációját, a helyreállított adatbázis csatlakozhat, és kövesse a [konfigurálása az adatbázis helyreállítása után](sql-database-disaster-recovery.md) útmutató a helyreállítás végrehajtásához.

### <a name="validation"></a>Ellenőrzés

Fejezze be a részletezés (beleértve a kapcsolati karakterláncok, bejelentkezések, alapvető funkciói tesztelési vagy egyéb standard application signoffs eljárások ellenőrzések része) alkalmazás-integritás utáni helyreállítás ellenőrzése.

## <a name="failover-groups"></a>Feladatátvételi csoportok

Feladatátvételi csoportok használatával védett adatbázis a részletezés gyakorlat magában foglalja a tervezett feladatátvételt a másodlagos kiszolgálóra. A tervezett feladatátvétel biztosítja, hogy az elsődleges és a másodlagos adatbázisok a feladatátvételi csoportban maradnak szinkronban a szerepkörök bekapcsolásakor. A nem tervezett feladatátvétel eltérően ez a művelet eredményez az adatvesztést, érdekében a részletezés az üzemi környezetben is elvégezhető.

### <a name="outage-simulation"></a>Szolgáltatáskimaradás szimuláció

A szolgáltatáskiesés megszüntetése után a szimulálásához, letilthatja, a webes alkalmazás vagy a virtuális gép csatlakozik az adatbázishoz. Szolgáltatáskimaradás eredményez a csatlakozási hibák az ügyfelek számára.

### <a name="recovery"></a>Helyreállítás

* Győződjön meg arról, hogy az alkalmazás konfigurációját a DR régióban pontokon a korábbi másodlagos, a teljes mértékben elérhető új elsődleges válik.
* Kezdeményezzen [tervezett feladatátvételt](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) a feladatátvételi csoport a másodlagos kiszolgálóról.
* Kövesse a [konfigurálása az adatbázis helyreállítása után](sql-database-disaster-recovery.md) útmutató a helyreállítás végrehajtásához.

### <a name="validation"></a>Ellenőrzés

Fejezze be a részletezés ellenőrzése az alkalmazás integritás utáni helyreállítási (beleértve a kapcsolatok, alapvető funkciói tesztelési vagy egyéb ellenőrzések, a részletezés signoffs szükséges).

## <a name="next-steps"></a>További lépések

* Folyamatos üzletmenet – forgatókönyvek kapcsolatos további információkért lásd: [folyamatos üzletmenet – forgatókönyvek](sql-database-business-continuity.md).
* További információ az Azure SQL Database automatikus biztonsági mentések, lásd: [SQL-adatbázis automatikus biztonsági másolatai](sql-database-automated-backups.md)
* Az automatikus biztonsági másolatokból helyreállítási kapcsolatos további információkért lásd: [adatbázis visszaállítása a szolgáltatás által létrehozott biztonsági másolatokból](sql-database-recovery-using-backups.md).
* Gyorsabb helyreállítási beállítások kapcsolatos további információkért lásd: [aktív georeplikáció](sql-database-active-geo-replication.md) és [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md).
