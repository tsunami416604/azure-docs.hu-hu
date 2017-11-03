---
title: "Működés közbeni alkalmazás frissítés - Azure SQL Database |} Microsoft Docs"
description: "Útmutató: Azure SQL Database georeplikációja használatát a felhőalapú alkalmazások online frissítések támogatásához."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 58f42859-1e37-463c-a3d8-a3ca2e867148
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 07/16/2016
ms.author: sashan
ms.openlocfilehash: deb91d55e5b796f7b1b53a99866156fe492e0a24
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Az SQL-adatbázis aktív georeplikáció használatával a felhőalapú alkalmazásokhoz működés közbeni frissítések kezelése
> [!NOTE]
> [Aktív georeplikáció](sql-database-geo-replication-overview.md) érhető el az összes olyan adatbázis az összes rétege.
> 

Ismerje meg, hogyan használható [georeplikáció](sql-database-geo-replication-overview.md) az SQL-adatbázis ahhoz, hogy a felhő alkalmazás működés közbeni frissítések. Mivel a frissítés egy zavaró művelet, azt az üzleti folytonossági tervezési és kialakítási részének kell lennie. Ebben a cikkben azt koordinálása a frissítési folyamat két különböző módszer tekintse meg, és az előnyöket és kompromisszumot alakítson ki az egyes lehetőségek ismertetik. Ez a cikk céljából használjuk egy egyszerű alkalmazást, amely a webhely, az adatok rétegként egy adatbázishoz kapcsolódó áll. A cél, hogy a 2-es nélkül a végfelhasználói élmény jelentős hatással az alkalmazás 1 verziójának frissítése. 

A frissítési beállításokban kiértékelése során a következő tényezőket kell figyelembe vennie:

* Érintő forgalomkiesés alkalmazás frissítéskor. Mennyi ideig a kérelem függvény korlátozható vagy csökkentett teljesítményű.
* A visszaállítás sikertelen frissítése esetén lehetősége.
* A biztonsági rés az alkalmazás, ha egy független végzetes hiba akkor fordul elő, a frissítés során.
* Amerikai dollár teljes költsége.  Ez magában foglalja a további redundancia és a frissítési folyamat által használt ideiglenes összetevőket többletköltségei. 

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>A vész-helyreállítási mentéseket használó alkalmazások frissítése
Ha az alkalmazás automatikus mentését alapul, és vész-helyreállítási georedundáns helyreállítás használ, azt általában a rendszer egyetlen Azure-régiót. Ebben az esetben a frissítés magában foglalja a biztonsági mentési központi telepítés összes alkalmazás-összetevő részt vesz a frissítés. A végfelhasználói megszűnésének minimalizálása érdekében a feladatátvételi profillal Azure Traffic Manager (WATM) fogja használni.  A következő ábra szemlélteti a működési környezetben a verziófrissítés előtt. A végpont <i>contoso-1.azurewebsites.net</i> frissíteni alkalmazás éles helyét jelöli. Ahhoz, hogy a visszaállítás lehetősége a frissítést, meg kell szakasz tárhely létrehozása az alkalmazás teljesen szinkronizált másolatát. A következő lépések szükségesek a frissítés az alkalmazás előkészítése:

1. A verziófrissítés szakasz tárhely létrehozása. Az ehhez a másodlagos adatbázist (1) létrehozása és központi telepítése egy azonos webhely azonos Azure-régióban. A másodlagos megjelenítéséhez, ha az index-összehangolási folyamat befejeződött-e figyelni.
2. Hozzon létre egy feladatátvevő profilt az WATM a <i>contoso-1.azurewebsites.net</i> online végpontként és <i>contoso-2.azurewebsites.net</i> offline állapotúként. 

> [!NOTE]
> Vegye figyelembe a előkészítő lépések nem befolyásolja az alkalmazás az éles ponton, és teljes hozzáférési módban működéséhez.
>  

![SQL adatbázis Ugrás-replikációs konfiguráció. Felhőalapú vészhelyreállítás.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Ha megadta az előkészítő lépések a tényleges frissítés készen áll az alkalmazást. A következő ábra szemlélteti a frissítési folyamat lépéseit. 

1. Állítsa be az elsődleges adatbázis csak olvasható módra (3) az éles ponton. Ez garantálja, hogy az alkalmazás (V1) éles példánya marad írásvédett megelőzve a adateltérésekkel között a V1 és V2 adatbázis-példány frissítése során.  
2. Válassza le a másodlagos adatbázist, a tervezett lezárást módban (4). Az elsődleges adatbázis teljesen szinkronizált független másolatot hoz létre. Ez az adatbázis lesz frissítve.
3. Kapcsolja be az elsődleges adatbázis írható-olvasható módba, és a frissítési parancsprogrammal szakasz tárolóhelye (5).     

![SQL-adatbázis georeplikáció konfigurációs. Felhőalapú vészhelyreállítás.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Ha a frissítés sikeresen befejeződött most már készen áll a végfelhasználók váltson át az előkészített másolatát az alkalmazást. Ez lesz az alkalmazás az éles webalkalmazásra.  Ez néhány további lépést meg az alábbi ábrán szemléltetett foglal magában.

1. Váltás az online végpont a WATM profil <i>contoso-2.azurewebsites.net</i>, amely mutat, a V2 verziójú a webhely (6). Az éles webalkalmazásra, a V2 alkalmazással most válik, és a végfelhasználói forgalom van átirányítva.  
2. Ha már nincs szüksége a V1-es alkalmazás-összetevők, nyugodtan távolítsa el őket (7).   

![SQL-adatbázis georeplikáció konfigurációs. Felhőalapú vészhelyreállítás.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Ha a frissítési folyamat sikertelen, például a frissítési parancsfájl hiba miatt a szakasz tárolóhely tekintendő sérült. Szeretné visszaállítani a frissítés előtti állapotot az alkalmazás az alkalmazás teljes körű hozzáférés az éles ponton egyszerűen vissza. Lépéseit a következő diagramon láthatók.    

1. Az adatbázis-példány beállítása a írható-olvasható módba (8). A művelettel visszaállítja a teljes V1 funkcionálisan a az éles webalkalmazásra.
2. Hajtsa végre a legfelső szintű okát, és távolítsa el a sérült biztonságú összetevők szakasz tárolóhelye (9). 

Ezen a ponton az alkalmazás teljesen működőképes, és a frissítési lépéseket is megismételhető.

> [!NOTE]
> A visszaállítás nem módosítások szükségesek az WATM profil már mutat, <i>contoso-1.azurewebsites.net</i> aktív végpontjának.
> 
> 

![SQL-adatbázis georeplikáció konfigurációs. Felhőalapú vészhelyreállítás.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

A kulcs **előny** ezt a beállítást az, hogy egy alkalmazás egyszerű ismertetett lépések segítségével egy régió frissítheti. A frissítési dollár költsége viszonylag alacsony. A fő **kompromisszumot** , hogy a frissítés során végzetes hiba esetén a helyreállítás a frissítés előtti állapotába magában foglalja az alkalmazás egy másik régióban található, és az adatbázis visszaállítása biztonsági másolatból georedundáns helyreállítás használatával újbóli telepítését. Ez a folyamat jelentős állásidőt eredményez.   

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>A database georeplikációja a vész-helyreállítási használó alkalmazások frissítése
Ha az alkalmazás kihasználja az üzletmenet folytonossága érdekében georeplikáció, a rendszer telepíti az elsődleges régióban egy aktív központi telepítés és a készenléti biztonsági mentés régióban legalább két különböző régiókban. A korábban említett tényezőket, mellett a frissítési folyamat garantálnia kell, hogy:

* Az alkalmazás védve marad az végzetes hibák mindig a frissítési folyamat során
* Az alkalmazás a georedundáns összetevő frissítésének párhuzamosan aktív összetevői

Ezen célok eléréséhez a feladatátvételi profilt használ egy aktív és három biztonsági mentés végpontok Azure Traffic Manager (WATM) fogja használni.  A következő ábra szemlélteti a működési környezetben a verziófrissítés előtt. A webhelyek <i>contoso-1.azurewebsites.net</i> és <i>contoso-dr.azurewebsites.net</i> teljes földrajzi redundanciával határoz meg az alkalmazás egy éles tárolóhelyre. Ahhoz, hogy a visszaállítás lehetősége a frissítést, meg kell szakasz tárhely létrehozása az alkalmazás teljesen szinkronizált másolatát. Győződjön meg arról, hogy az alkalmazás gyorsan helyreállíthatja, ha a frissítési folyamat során végzetes hiba bekövetkezése van szüksége, mert a szakasz tárolóhely kell lennie georedundáns is. A következő lépések szükségesek a frissítés az alkalmazás előkészítése:

1. A verziófrissítés szakasz tárhely létrehozása. A nincs, hozzon létre egy másodlagos adatbázist (1), és a webhely azonos Azure-régióban azonos példányának telepítését. A másodlagos megjelenítéséhez, ha az index-összehangolási folyamat befejeződött-e figyelni.
2. Hozzon létre georedundáns másodlagos adatbázist szakasz tárolóhelye földrajzi replikálni a másodlagos adatbázis a biztonsági mentési régióban (Ez a lehetőség "kapcsolt georeplikáció"). Figyelje meg, ha az index-összehangolási folyamat befejeződött (3) másodlagos biztonsági mentés.
3. A biztonsági mentési régióban a webhely készenléti másolatának létrehozása hozzákapcsolja azt a georedundáns másodlagos (4).  
4. A további végpontok hozzáadása <i>contoso-2.azurewebsites.net</i> és <i>contoso-3.azurewebsites.net</i> offline végpontként (5) a WATM feladatátvételi profilt. 

> [!NOTE]
> Vegye figyelembe a előkészítő lépések nem befolyásolja az alkalmazás az éles ponton, és teljes hozzáférési módban működéséhez.
> 
> 

![SQL-adatbázis georeplikáció konfigurációs. Felhőalapú vészhelyreállítás.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

Előkészítő lépések elvégzése után a szakasz tárolóhely készen áll a frissítésre. A következő ábra szemlélteti a frissítési lépéseket.

1. Állítsa be az elsődleges adatbázis az éles ponton csak olvasható módra (6). Ez garantálja, hogy az alkalmazás (V1) éles példánya marad írásvédett megelőzve a adateltérésekkel között a V1 és V2 adatbázis-példány frissítése során.  
2. Válassza le a másodlagos adatbázis ugyanabban a régióban a tervezett lezárást módban (7). Az elsődleges adatbázis, amelyhez elsődleges automatikusan válik leállása után teljesen szinkronizált független másolatot hoz létre. Ez az adatbázis lesz frissítve.
3. Kapcsolja be az elsődleges adatbázis írható-olvasható módba szakasz tárolóhelye, és futtassa a frissítési parancsfájlt (8).    

![SQL-adatbázis georeplikáció konfigurációs. Felhőalapú vészhelyreállítás.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Ha a frissítés sikeresen befejeződött, most már készen áll a végfelhasználók váltson át a V2 alkalmazásverziónként. A következő ábra szemlélteti a lépéseit.

1. Az aktív végpontot a WATM profil kapcsoló <i>contoso-2.azurewebsites.net</i>, mely most mutat, a V2 verziójú, a webhely (9). Most válik, a V2 alkalmazással éles tárhely és végfelhasználói forgalom van átirányítva. 
2. Ha már nincs szüksége a V1-es alkalmazás így biztonságosan eltávolíthatja azt (10-es és 11).  

![SQL-adatbázis georeplikáció konfigurációs. Felhőalapú vészhelyreállítás.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Ha a frissítési folyamat sikertelen, például a frissítési parancsfájl hiba miatt a szakasz tárolóhely tekintendő sérült. A frissítés előtti állapotba, akkor egyszerűen vissza az alkalmazás az éles ponton teljes hozzáféréssel rendelkező alkalmazás visszavonásához. Lépéseit a következő diagramon láthatók.    

1. Állítsa be az elsődleges adatbázis másolatát az éles ponton írható-olvasható módba (12). A művelettel visszaállítja a teljes V1 funkcionálisan a az éles webalkalmazásra.
2. Hajtsa végre a legfelső szintű okát, és távolítsa el a sérült biztonságú összetevők (13 és 14) szakaszban tárolóhelye. 

Ezen a ponton az alkalmazás teljesen működőképes, és a frissítési lépéseket is megismételhető.

> [!NOTE]
> A visszaállítás nem módosítások szükségesek az WATM profil már mutat, <i>contoso-1.azurewebsites.net</i> aktív végpontjának.
> 
> 

![SQL-adatbázis georeplikáció konfigurációs. Felhőalapú vészhelyreállítás.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

A kulcs **előny** ezt a beállítást az, hogy frissítheti az alkalmazás és a párhuzamos a georedundáns Másolás a frissítés során az üzletmenet folytonossága veszélyeztetése nélkül. A fő **kompromisszumot** igényel minden egyes alkalmazás-összetevő dupla redundanciát, így ezért költséget az magasabb dollár áll. A bonyolultabb munkafolyamat is magában foglalja. 

## <a name="summary"></a>Összefoglalás
A cikkben ismertetett a két frissítési módszer nem egyezik a összetettsége pedig a költségeket, dollár, de mindkét a kis méretre az időpontot, amikor a végfelhasználó csak olvasási műveletek. A frissítési parancsfájl időtartama közvetlenül határozza meg, az időpont. Nem függ a az adatbázis mérete, a kiválasztott szolgáltatási rétegben, a webhely konfigurációs és egyéb tényezőkkel, amelyek könnyen nem tudja beállítani. Ennek az oka, hogy az előkészítő lépések vannak különválik a frissítési lépéseket, és a termelési alkalmazások befolyásolása nélkül végezheti. A frissítési parancsfájl hatékonyságának a kulcsfontosságú tényező, amely meghatározza, hogy a végfelhasználói élmény frissítéskor. Így a legjobb módja, fejlesztheti azt, a próbálkozások összpontosító és a frissítési parancsfájl felhasználását.  

## <a name="next-steps"></a>Következő lépések
* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
* További tudnivalók az Azure SQL adatbázis automatikus biztonsági mentés című [SQL-adatbázis biztonsági mentések automatikus](sql-database-automated-backups.md).
* A helyreállítás automatikus biztonsági mentés használatával kapcsolatos további tudnivalókért lásd: [adatbázis visszaállítása biztonsági másolatból automatizált](sql-database-recovery-using-backups.md).
* Gyorsabb helyreállítási beállításokkal kapcsolatos további tudnivalókért lásd: [aktív georeplikáció](sql-database-geo-replication-overview.md).


