---
title: Vész-helyreállítási gyakorlatok
description: Útmutatást és gyakorlati tanácsokat az Azure SQL Database használatával a vész-helyreállítási gyakorlatok végrehajtásához.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 3ca00a03976ae38b7956616b8287220a7bc5998c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825853"
---
# <a name="performing-disaster-recovery-drill"></a>Vész-helyreállítási gyakorlat végrehajtása

Javasoljuk, hogy az alkalmazás helyreállítási munkafolyamatra való felkészültségének hitelesítése rendszeres időközönként történik. Az alkalmazás viselkedésének és az adatvesztés következményeinek és/vagy a feladatátvételt igénylő megszakításnak a ellenőrzése jó mérnöki gyakorlat. Ez is egy követelmény a legtöbb ipari szabványok részeként üzletmenet-folytonosság tanúsítás.

A vész-helyreállítási gyakorlat végrehajtása a következőkből áll:

* Az adatréteg kimaradásának szimulálása
* Visszaszerzés
* Az alkalmazás integritásának ellenőrzése a helyreállítás után

Attól függően, hogy hogyan [tervezte meg az alkalmazást az üzletmenet folytonosságára,](sql-database-business-continuity.md)a fúró végrehajtására szolgáló munkafolyamat eltérő lehet. Ez a cikk ismerteti a vész-helyreállítási gyakorlat az Azure SQL Database környezetében végzett gyakorlati tanácsok.

## <a name="geo-restore"></a>Georedundáns helyreállítás

A vész-helyreállítási gyakorlat végrehajtásakor a potenciális adatvesztés megelőzése érdekében hajtsa végre a fúrót egy tesztkörnyezet használatával az éles környezet másolatának létrehozásával és az alkalmazás feladatátvételi munkafolyamatának ellenőrzésére.

### <a name="outage-simulation"></a>Leállás szimuláció

A kimaradás szimulálása érdekében átnevezheti a forrásadatbázist. Ez a névváltozás alkalmazáskapcsolati hibákat okoz.

### <a name="recovery"></a>Helyreállítás

* Az adatbázis geo-visszaállításának végrehajtása az [itt](sql-database-disaster-recovery.md)leírtak szerint egy másik kiszolgálóra .
* Módosítsa az alkalmazás konfigurációját úgy, hogy csatlakozzon a helyreállított adatbázishoz, és kövesse az [Adatbázis konfigurálása a helyreállítási](sql-database-disaster-recovery.md) útmutató után című útmutatót a helyreállítás befejezéséhez.

### <a name="validation"></a>Ellenőrzés

Fejezze be a fúrót az alkalmazás integritásának a helyreállítás utáni ellenőrzésével (beleértve a kapcsolati karakterláncokat, bejelentkezéseket, alapvető funkciók tesztelését vagy a szabványos alkalmazás-kijelentkezési eljárások egyéb érvényesítési részét).

## <a name="failover-groups"></a>Feladatátvételi csoportok

A feladatátvételi csoportokkal védett adatbázis esetében a fúrógyakorlat a másodlagos kiszolgálótervezett feladatátvételt foglal magában. A tervezett feladatátvétel biztosítja, hogy a feladatátvételi csoport elsődleges és másodlagos adatbázisai szinkronban maradjanak a szerepkörök váltásakor. A nem tervezett feladatátvételsel ellentétben ez a művelet nem eredményez adatvesztést, így a fúró az éles környezetben is elvégezhető.

### <a name="outage-simulation"></a>Leállás szimuláció

A kimaradás szimulálása érdekében letilthatja az adatbázishoz csatlakoztatott webalkalmazást vagy virtuális gépet. Ez a kimaradás szimuláció eredménye a webes ügyfelek kapcsolódási hibák.

### <a name="recovery"></a>Helyreállítás

* Győződjön meg arról, hogy a VÉSZ-régióban az alkalmazás konfigurációja a korábbi másodlagos, amely a teljesen elérhető új elsődleges lesz.
* Indítsa el a feladatátvételi csoport [tervezett feladatátvételét](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) a másodlagos kiszolgálóról.
* A helyreállítás befejezéséhez kövesse az [Adatbázis konfigurálása a helyreállítási](sql-database-disaster-recovery.md) útmutató után című útmutatót.

### <a name="validation"></a>Ellenőrzés

Fejezze be a fúrót az alkalmazás sértetlenségének a helyreállítás utáni ellenőrzésével (beleértve a kapcsolatot, az alapvető funkciók tesztelését vagy a fúrókijelentkezásokhoz szükséges egyéb ellenőrzéseket).

## <a name="next-steps"></a>További lépések

* Az üzletmenet-folytonossági forgatókönyvekről a [Folytonossági forgatókönyvek (Folytonossági forgatókönyvek) (Folytonossági forgatókönyvek) (Folytonossági forgatókönyvek) (Folytonossági forgatókönyvek) (Folytonossági forgatókönyvek) (Folytonossági forgatókönyv](sql-database-business-continuity.md)
* Az Azure SQL Database automatikus biztonsági mentéseiről az SQL Database automatikus biztonsági mentései című [témakörben olvashat.](sql-database-automated-backups.md)
* Az automatikus biztonsági mentések helyreállítási használatáról az [adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági mentések ből](sql-database-recovery-using-backups.md)című témakörben olvashat.
* A gyorsabb helyreállítási lehetőségekről az [Aktív georeplikáció](sql-database-active-geo-replication.md) és [az automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)című témakörben olvashat.
