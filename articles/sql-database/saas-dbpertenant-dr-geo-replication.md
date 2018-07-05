---
title: Az Azure SQL Database Georeplikációt használ az SaaS-alkalmazások Vészhelyreállítása |} A Microsoft Docs
description: 'Útmutató: Azure SQL Database geo-replikákat használ a több-bérlős SaaS-alkalmazás helyreállítása meghibásodás esetére'
keywords: sql database-oktatóanyag
services: sql-database
author: AyoOlubeko
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: ayolubek
ms.openlocfilehash: f2ad92118c00f08e5dcdd4a8a12f007308b3fbd1
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2018
ms.locfileid: "34645793"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Egy több-bérlős SaaS-alkalmazás, adatbázis-replikációval geo-vészhelyreállítás

Ebben az oktatóanyagban egy több-bérlős SaaS-alkalmazás a bérlőnkénti adatbázis modellt használó megvalósítva a teljes vészhelyreállítás megismerése. Az alkalmazás védelme a kimaradás utáni érdekében használja [ _georeplikációs_ ](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) az alkalmazáskatalógus és a bérlői adatbázis-replika létrehozásához egy másik helyreállítási régióban. Egy kimaradás során, gyorsan átadja a feladatokat ezekre a replikákra normál üzleti műveletek folytatásához. Feladatátvétel esetén az adatbázisokat az eredeti régióban lesznek az adatbázisokat a helyreállítási régióban másodlagos replikáin. Ha ezek a replikák ismét online elérhető lesz, automatikusan olvasásra az adatbázisokat a helyreállítási régióban állapotát. Miután a szolgáltatáskiesés megszüntetése után a rendszer feloldott, visszaadja a feladatokat az adatbázisokat az eredeti éles régióban.

Ez az oktatóanyag bemutatja, feladatátvételének és feladat-visszavétel munkafolyamatokat. Megtudhatja, hogyan lehet:
> [!div classs="checklist"]

>* Sync-adatbázis és rugalmas tárolókészlet konfigurációs adatait a bérlő-katalógusba
>* Alkalmazás, a kiszolgálók és a készletek egy másik régióban található helyreállítási környezet beállítása
>* Használat _georeplikációs_ az alkalmazáskatalógus és a bérlői adatbázisok replikálása a helyreállítási régió
>* Az alkalmazás és az alkalmazáskatalógus és a bérlői adatbázisok helyreállítási régió feladatátvételt 
>* Később, az alkalmazás feladatátvételt, az eredeti régióba az alkalmazáskatalógus és a bérlői adatbázisok biztonsági a szolgáltatáskiesés megszüntetése után
>* A katalógus frissíteni, mivel minden bérlői adatbázison végrehajtott feladatátvételt nyomon követéséhez az egyes bérlői adatbázis elsődleges helye
>* Győződjön meg arról, az alkalmazás és az elsődleges bérlői adatbázis vannak mindig védelmicsoport-készletek ugyanabban a régióban az Azure a késés csökkentése érdekében  
 

Az oktatóanyag elindítása előtt ellenőrizze az alábbi előfeltételek végezhető el:
* A Wingtip Tickets SaaS adatbázis bérlői alkalmazásonként van üzembe helyezve. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezése és felfedezése a Wingtip Tickets SaaS adatbázis bérlői alkalmazásonként](saas-dbpertenant-get-started-deploy.md)  
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>A georeplikáció helyreállítási mintáját bemutatása

![Recovery architektúrájáról](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
A vészhelyreállítás (DR) sok alkalmazás esetén fontos szempont a megfelelőségi okokból, vagy az üzletmenet folytonosságának. Kell egy hosszan tartó szolgáltatáskimaradás, egy jól felkészültnek Vészhelyreállítási terv minimalizálhatja üzletmenet megszakadása. Georeplikáció használatával biztosítja, mivel a legalacsonyabb RPO és RTO karbantartása egy helyreállítási régióban, amely képes végrehajtani a feladatátvételt, rövid figyelje meg, hogy az adatbázis-replikákat.

A Vészhelyreállítási terv alapján a georeplikáció három különálló részből áll:
* Beállítás-létrehozása és a helyreállítási környezetet karbantartása
* Helyreállítás – az alkalmazás és adatbázist helyezett át a helyreállítási környezet kimaradás esetén feladatátvétele
* Hazatelepítési – az alkalmazás és az adatbázisok feladatátvételét vissza az eredeti régió után az alkalmazás 

Minden részét kell alaposan megfontolni különösen akkor, ha nagy mennyiségű működési. A csomag teljes, több célok kell elvégezni:

* Beállítás
    * Hozzon létre, és biztosítja a tükör-image környezetet a helyreállítási régióban. A rugalmas készletek létrehozása és a helyreállítási környezetet önálló adatbázisokhoz replikálása fenntartja a kapacitás a helyreállítási régióban. Ebben a környezetben karbantartása új bérlői adatbázisok replikálása, kiépítésüket tartalmazza.  
* Helyreállítás
    * Ha napi költségek minimalizálása érdekében horizontálisan le-helyreállítási környezet használnak, készletek és önálló adatbázisokat kell vertikálisan fel beszerezni a teljes működési kapacitását a helyreállítási régióban
    * Új bérlő kiépítése a helyreállítási régióban minél hamarabb engedélyezése  
    * Bérlők prioritási sorrendben visszaállítása optimalizálva
    * Bérlők online lehető legnagyobb első párhuzamosan lépések végrehajtásával, ahol gyakorlati optimalizálva
    * Hiba, újraindítható, és idempotens rugalmasak lehetnek
    * Csak a folyamat közben továbbított megszüntetése, ha az eredeti régióban érhető el ismét online állapotú lesz.
* Hazatelepítési 
    * Csak minimális hatással van a bérlők számára az eredeti régió replikáihoz helyreállítási régióban adatbázisok feladatátvételét: nincs adatvesztés és minimális időtartam bérlőnként végezze a rögzítést.   

Ebben az oktatóanyagban ezek a kihívások foglalkozik az Azure SQL Database és az Azure platform szolgáltatásainak használata:

* [Az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), az összes szükséges kapacitás, a lehető leggyorsabban fenntartására. Az Azure Resource Manager-sablonok segítségével üzembe helyezése egy tükrözött lemezképét az üzemi kiszolgálók és a rugalmas készletek a helyreállítási régióban.
* [Georeplikáció](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), aszinkron módon replikált csak olvasható másodlagos példány hozható létre az összes adatbázis létrehozása. Egy kimaradás során átadja a feladatokat a replikákat a helyreállítási régióban.  Miután a szolgáltatáskiesés megszüntetése után a rendszer feloldott, visszaadja a feladatokat az adatbázisokat az eredeti régióban, az adatvesztés.
* [Aszinkron](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) feladatátvételi műveletek küldése a bérlő-prioritásuk szerinti sorrendben történik, a nagy számú adatbázis kezelésével járó feladatátvételi idő minimalizálása érdekében.
* [Szilánkleképezés helyreállítási funkciókat](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager), helyreállítási és hazatelepítési során a katalógus adatbázis-bejegyzések módosításához. Ezek a funkciók lehetővé teszik az alkalmazás csatlakozzon a bérlői adatbázisok helytől függetlenül, az alkalmazás újrakonfigurálása nélkül.
* [SQL-kiszolgáló DNS-aliasokat](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), amely lehetővé teszi az új bérlők számára, függetlenül attól, melyik régióban az alkalmazás üzemel, a zökkenőmentes kiépítését. DNS-aliasokat, hogy a katalógus szinkronizálási folyamat során, függetlenül az, hogy az aktív katalógushoz csatlakozás is használhatók.

## <a name="get-the-disaster-recovery-scripts"></a>A vészhelyreállítási szkripteket beolvasása 

> [!IMPORTANT]
> Minden a Wingtip Tickets felügyeleti parancsfájlokat, például a Vészhelyreállítási szkripteket minta minőségi és, nem éles környezetben használható. 

Ez az oktatóanyag és a Wingtip alkalmazás forráskódjának kiadásban használt parancsfájlokra érhetők el a helyreállítás a [bérlőnként GitHub-adattárat a Wingtip Tickets SaaS adatbázis](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip Tickets felügyeleti parancsfájlokat.

## <a name="tutorial-overview"></a>Az oktatóanyag áttekintése
Ebben az oktatóanyagban először használatával georeplikációs hozza létre a replikákat a Wingtip Tickets alkalmazás és a hozzá tartozó adatbázisok egy másik régióban. Ezt követően átadja a feladatokat a kimaradás utáni helyreállítása szimulálásához ebben a régióban. Amikor végzett, az alkalmazás teljes körűen működik a helyreállítási régióban.

Később egy különálló hazatelepítési lépésben átadja a feladatokat az alkalmazáskatalógus és a bérlői adatbázisokat a helyreállítási régióban, az eredeti régióba. Az alkalmazás és az adatbázisok tartózkodási teljes hazatelepítési elérhetőek. Amikor végzett, az alkalmazás teljes körűen működik az eredeti régióban.

> [!Note]
> Az alkalmazás állítható helyre, azokat a _párosított régióra_ a régió, amelyben az alkalmazás központi telepítése. További információkért lásd: [Azure párosított régióiról](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Az alkalmazás kifogástalan állapotának áttekintése

A helyreállítási folyamat megkezdése előtt tekintse át az alkalmazás normál kifogástalan állapotát.
1. A böngészőben nyissa meg a Wingtip Tickets Eseményközpontot (http://events.wingtip-dpt.&lt; felhasználó&gt;. trafficmanager.net - cserélje le &lt;felhasználói&gt; az üzemelő példány felhasználói értékkel).
    * Görgessen a lap aljára, és figyelje meg, hogy a katalógus-kiszolgáló nevét és helyét a láblécben. A hely az a régió, amelyben az alkalmazás üzembe helyezésekor.
    *Tipp: Vigye az egérmutatót megjelenítéséhez nagyítsa fel a helyet. * 
     ![Események központ megfelelő állapotba eredeti régióban](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Kattintson a Contoso Concert Hall a bérlőhöz, és nyissa meg az események lapján.
    * A láblécben figyelje meg a bérlő kiszolgáló nevét. A hely ugyanaz, mint a katalóguskiszolgálón helye lesz.

3. Az a [az Azure portal](https://portal.azure.com), nyissa meg az erőforráscsoport, amelyben az alkalmazás telepítését
    * Figyelje meg, hogy a régióban, amelyben a kiszolgálók üzembe vannak helyezve. 

## <a name="sync-tenant-configuration-into-catalog"></a>Szinkronizálási bérlői konfiguráció-katalógusba

Ebben a feladatban egy folyamat, amely a kiszolgálók, rugalmas készletek és adatbázisok konfigurációját a bérlő-katalógusba való szinkronizálásának megkezdése. A folyamat tartja naprakészen a katalógusban ezt az információt.  A folyamat az aktív katalógus működik-e az eredeti régióban és a helyreállítási régióban. A konfigurációs adatokat használja a helyreállítási környezet biztosítása érdekében a helyreállítási folyamat során az eredeti környezet összhangban, és majd később során hazatelepítési annak biztosítása érdekében az eredeti régió konzisztenssé a-ben végrehajtott változtatások a helyreállítási környezet. A katalógus segítségével is nyomon követheti, bérlői erőforrások helyreállítási állapota

> [!IMPORTANT]
> Az egyszerűség kedvéért a szinkronizálási folyamat és az egyéb hosszú futású helyreállítási és hazatelepítési folyamatok a helyi Powershell-feladatok vagy az ügyfél-felhasználói bejelentkezés alatt futó munkamenetek ezekben az oktatóanyagokban van megvalósítva. A hitelesítési tokenek ki, ha néhány óra múlva lejár, bejelentkezési és a feladat sikertelen lesz, majd. Éles forgatókönyvekben hosszú futású folyamatok, a megbízható Azure-szolgáltatások bizonyos típusú, egyszerű szolgáltatás alatt futó kell végrehajtani. Lásd: [tanúsítvánnyal egyszerű szolgáltatás létrehozása az Azure PowerShell használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. Az a _PowerShell ISE-ben_, nyissa meg a ...\Learning Modules\UserConfig.psm1 fájlt. Cserélje le `<resourcegroup>` és `<user>` sorok 10-es és 11 az alkalmazás üzembe helyezésekor használt értékkel.  Mentse a fájlt!

2. Az a *PowerShell ISE-ben*, nyissa meg a ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be:
    * **$DemoScenario = 1**, mely szinkronizálja a bérlői kiszolgálóhoz háttérben futó feladat indítása és tárolókészlet konfigurációs adatait a katalógusban

3. Nyomja meg **F5** szinkronizálási szkript futtatásához. Ekkor megnyílik egy új PowerShell-munkamenetben szinkronizálni a konfigurációt a bérlői erőforrásokhoz.
![Szinkronizálási folyamat](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Hagyja a háttérben futó PowerShell-ablakot, és folytathatja az oktatóanyag további részeinek. 

> [!Note]
> A szinkronizálási folyamat során a katalógus egy DNS-alias-n keresztül csatlakozik. Ezt az aliast visszaállítása és az aktív katalógus átirányítása hazatelepítési során is módosul. A szinkronizálási folyamat biztosítja, hogy a katalógus naprakész az adatbázis vagy készlet konfigurációs módosítások a helyreállítási régióban.  A hazatelepülés során ezeket a módosításokat az azonos erőforrásokat az eredeti régióban lépnek.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Hozzon létre másodlagos adatbázis-replikákat a helyreállítási régióban

Ebben a feladatban elindíthatja egy folyamat, amely egy ismétlődő-példányt helyez üzembe, és a egy helyreállítási régióba replikálja a katalógus és az összes bérlői adatbázison.

> [!Note]
> Ez az oktatóanyag a Wingtip Tickets mintaalkalmazás georeplikációs védelmi hozzáadja. Egy éles forgatókönyvet, az alkalmazás által használt georeplikáció, az egyes bérlők lenne üzembe egy georeplikált adatbázis kezdettől fogva. Lásd: [az Azure SQL Database használatával magas rendelkezésre állású szolgáltatások tervezése](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. Az a *PowerShell ISE-ben*, nyissa meg a ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következő értékeket:
    * **$DemoScenario = 2**, tükrözött lemezképét helyreállítási környezetet hozhat létre, és a katalógus és a bérlői adatbázisok replikálása

2. A szkriptek futtatásához nyomja le az **F5** billentyűt. Ekkor megnyílik egy új PowerShell-munkamenetet a replikák létrehozásához.
![Szinkronizálási folyamat](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Tekintse át a normál alkalmazási állapota
Ezen a ponton az alkalmazás megfelelően működik az eredeti régióban, és georeplikáció által már védett.  Csak olvasható másodlagos replikával, az összes adatbázis helyreállítási régióban található. 
1. Az Azure Portalon, tekintse meg az erőforráscsoportok és vegye figyelembe, hogy egy erőforráscsoport létre lett hozva - helyreállítási utótagot a helyreállítási régióban. 

1. Fedezze fel az erőforrásokat a helyreállítási erőforráscsoportban.  

1. Kattintson a Contoso Concert Hall adatbázison a a _tenants1-dpt -&lt;felhasználói&gt;-helyreállítási_ kiszolgáló.  Kattintson a bal oldali Georeplikáció. 

    ![Contoso Concert georeplikációs hivatkozás](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Az Azure-régiók térképen vegye figyelembe a georeplikációs hivatkozás az eredeti régióban az elsődleges és másodlagos a helyreállítási régióban között.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>A helyreállítási régióban, az alkalmazás feladatátvételt

### <a name="geo-replication-recovery-process-overview"></a>Georeplikáció a helyreállítási folyamat áttekintése

A helyreállítási szkript a következő feladatokat hajtja végre:

1. A webalkalmazás az eredeti régióban a Traffic Manager végpont letiltása. A végpont letiltása megakadályozza, hogy a felhasználók csatlakozzanak az alkalmazás érvénytelen állapotban kell az eredeti régió online állapotba a helyreállítás során.

1. Győződjön meg arról, hogy az elsődleges adatbázis használja a helyreállítási régióban kényszerített feladatátvétel, a katalógus-adatbázisban, és frissíti a _activecatalog_ alias, hogy a helyreállítási kiszolgáló mutasson.

1. Frissítések a _newtenant_ alias, mutasson a bérlői kiszolgálóhoz, a helyreállítási régióban. Ezt az aliast módosítása biztosítja, hogy minden olyan új bérlők számára az adatbázisokat a helyreállítási régióban vannak kiépítve. 

1. Az összes meglévő bérlők számára a helyreállítási katalógus offline állapotúként, hogy megakadályozza a hozzáférést a bérlői adatbázisok előtt, melyek feladatai át lettek jelöli meg.

1. Frissíti a rugalmas készletek és a replikált önálló adatbázisokat a helyreállítási régióban történő tükrözésének az eredeti régióban konfigurációjuk konfigurációját. (Ez a feladat csak akkor szükséges, ha a készletek és a helyreállítási környezetben replikált adatbázisok vannak vertikálisan leskálázni költségek csökkentése érdekében a normál működés során).

1. Lehetővé teszi, hogy a Traffic Manager-végpont a webalkalmazás a helyreállítási régióban. Ez a végpont lehetővé teszi, hogy az alkalmazás új bérlők kiépítése. Ebben a szakaszban a meglévő bérlők számára még mindig offline állapotban van.

1. Elküldi a váró kérelmek kényszerített feladatátvétel prioritási sorrendben adatbázisok.
    * Kötegek vannak rendezve, hogy az adatbázisok feladatátvételekor párhuzamosan összes készletek között.
    * Feladatátvételi kérelmek elküldése az aszinkron műveletek használatával, így gyorsan rájuk, és több kérést is feldolgozható.

   > [!Note]
   > Egy szolgáltatáskimaradás forgatókönyv az elsődleges adatbázisok eredeti régióban offline állapotban van.  Kényszerített feladatátvételt a másodlagos szünetek a kapcsolatot az elsődleges anélkül, hogy a alkalmazni maradék várólistán lévő tranzakciók. A Vészhelyreállítás részletezési forgatókönyvekben például ebben az oktatóanyagban minden frissítési tevékenység esetén a feladatátvétel időpontjában lehet némi adatvesztést. Később során hazatelepítési, amikor feladatátvételt hajt végre a helyreállítási régióban, az eredeti régióba adatbázisok egy normál feladatátvevő segítségével biztosítására ne legyen adatvesztés nélkül.

1. Figyeli az SQL database szolgáltatás meghatározni, ha adatbázisokat feladatátadása megtörtént. Miután egy bérlői adatbázison végrehajtott feladatátvételt, jegyezze fel a bérlői adatbázis helyreállítási állapota, és jelölje be a bérlő online, a katalógus frissíti.
    * Bérlői adatbázisok az alkalmazás által hozzáférhető, amint online, a katalógus van megjelölve.
    * A bérlői adatbázis rowversion értékek összege a katalógus tárolja. Ez az érték, amely lehetővé teszi a hazatelepítési folyamat határozza meg, ha az adatbázis frissítve lett a helyreállítási régióban ujjlenyomattal funkcionál.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Futtassa a szkriptet átadja a feladatokat a helyreállítási régió

Most már az imagine-beli szolgáltatáskimaradás van a régióban, amelyben az alkalmazás és a helyreállítási szkript futtatásához:

1. Az a *PowerShell ISE-ben*, nyissa meg a ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következő értékeket:
    * **$DemoScenario = 3**, helyreállítása az alkalmazás által a replikák-ba irányuló feladatátvétel olyan helyreállítási régió

2. A szkriptek futtatásához nyomja le az **F5** billentyűt.  
    * A parancsfájl egy új PowerShell-ablakban nyílik meg, és elindít egy párhuzamos futtatása PowerShell-feladatok sorozatát. Ezek a feladatok átadja a feladatokat a helyreállítási régióban bérlői adatbázison.
    * A helyreállítási régióban van a _párosított régióra_ társított Azure-régió, amelyben az alkalmazást telepítette. További információkért lásd: [Azure párosított régióiról](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. A PowerShell-ablakban a helyreállítási folyamat állapotának monitorozásához.
    ![feladatátvételi folyamatot](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Ismerje meg a helyreállítási feladatok kódja, tekintse át a PowerShell-parancsprogramok a ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-FailoverToReplica\RecoveryJobs mappában.

### <a name="review-the-application-state-during-recovery"></a>A helyreállítás során az alkalmazás állapotának áttekintése
Az alkalmazás végpont le van tiltva a Traffic Managerben, amíg az alkalmazás nem érhető el. Miután a katalógus feladatai átkerülnek a helyreállítási régióban, és a bérlők számára offline állapotban van megjelölve, akkor az alkalmazás nem válik újra online. Bár az alkalmazás nem érhető el, minden bérlő offline állapotban jelenik meg az eseményközpont mindaddig, amíg az adatbázison végrehajtott feladatátvételt. Fontos tervezze alkalmazását úgy, hogy kapcsolat nélküli bérlői adatbázisok kezelésére.

1. Azonnal után a katalógus-adatbázis helyreállítása megtörtént, frissítse a Wingtip Tickets Eseményközpont a böngészőben.
    * A láblécben, figyelje meg, hogy a katalógus kiszolgálónév most már rendelkezik egy _-helyreállítási_ utótag és a helyreállítási régióban található.
    * Figyelje meg, hogy a bérlők, amely még nem állítja vissza, offline állapotban van megjelölve, és nem választható.  

    > [!Note]
    > Csak néhány adatbázisok helyreállításához előfordulhat, hogy nem kell frissítse a böngészőt előtt helyreállítási véget ért, így előfordulhat, hogy nem látható a bérlők kapcsolat nélküli módban. 
 
    ![Eseményközpont offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

    * Ha közvetlenül egy offline bérlői események lapot megnyitni, "offline bérlői" értesítést jelenít meg. Ha például a Contoso Concert Hall offline állapotban, ha megpróbálja megnyitni http://events.wingtip-dpt.&lt; felhasználó&gt;.trafficmanager.net/contosoconcerthall ![Contoso Offline lap](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>A helyreállítási régióban új bérlő kiépítése
A meglévő bérlői adatbázisok rendelkezik a feladatátvétel előtt is telepíthet az új bérlők számára a helyreállítási régióban.  

1. Az a *PowerShell ISE-ben*, nyissa meg a ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájl, és állítsa be a következő tulajdonságot:
    * **$DemoScenario = 4**, a helyreállítási régióban új bérlő kiépítése

2. Nyomja meg **F5** futtassa a szkriptet, és az új bérlő kiépítése. 

3. A Hawthorn Hall események lapot a böngészőben megnyílik, ha lezárulnak. A lábléc megfigyelni, hogy a Hawthorn adatbázisra van kiépítve a helyreállítási régióban.
    ![Hawthorn Hall események lap](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. A böngészőben frissítse a Wingtip Tickets Eseményközpont lapot, melyen megtekintheti Hawthorn Hall tartalmazza. 
    * A többi bérlő visszaállítása várakozás nélkül üzembe helyezése Hawthorn Hall, más bérlők továbbra is lehet offline állapotban van.


## <a name="review-the-recovered-state-of-the-application"></a>Tekintse át az alkalmazást a helyreállított állapotát

A helyreállítási folyamat befejezését követően az alkalmazás és az összes bérlőre is teljesen működőképes a helyreállítási régióban. 

1. Ha a PowerShell-konzolablakot megjelenítését jelzi, hogy a rendszer visszaállítja az összes bérlőre vonatkozóan, frissítse az Eseményközpontot.  A bérlő összes jelenik meg, beleértve az új bérlő Hawthorn Hall online.

    ![az eseményközpont helyreállított és az új bérlők](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Az a [az Azure portal](https://portal.azure.com), az erőforráscsoportok listájának megnyitásához.  
    * Figyelje meg, hogy az üzembe helyezett, plusz az helyreállítási erőforráscsoport az erőforráscsoport a _-helyreállítási_ utótag.  A helyreállítási erőforráscsoportba tartozó összes a helyreállítási folyamat során létrehozott erőforrásokat, valamint a szolgáltatáskimaradás közben létrehozott új erőforrásokat tartalmaz.  

3. Nyissa meg a helyreállítási erőforráscsoportot, és figyelje meg a következő elemek:
    * A katalógus és tenants1-kiszolgálókat, a helyreállítási verziói a _-helyreállítási_ utótag.  A visszaállított katalógus és a bérlői adatbázisok ezeken a kiszolgálókon az összes az eredeti régióban használt nevekkel rendelkeznek.

    * A _tenants2-dpt -&lt;felhasználói&gt;-helyreállítási_ az SQL server.  Új bérlők kiépítése során a szolgáltatáskimaradás elhárítása után ez a kiszolgáló használható.
    *   Az App Service nevű, _események – a wingtip-dpt -&lt;recoveryregion&gt;-&lt;felhasználó & gt_; Ez a helyreállítási példány az események alkalmazás. 

    ![Az Azure helyreállítási erőforrások ](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png)    
    
4. Nyissa meg a _tenants2-dpt -&lt;felhasználói&gt;-helyreállítási_ az SQL server.  Figyelje meg az adatbázis tartalmaz _hawthornhall_ és a rugalmas készlet _Pool1_.  A _hawthornhall_ adatbázis van konfigurálva, egy rugalmas adatbázist a _Pool1_ rugalmas készlet.

5. Lépjen vissza az erőforráscsoportot, és kattintson a Contoso Concert Hall adatbázison a a _tenants1-dpt -&lt;felhasználói&gt;-helyreállítási_ kiszolgáló. Kattintson a bal oldali Georeplikáció.
    
    ![Contoso adatbázis feladatátvétel után](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Bérlői adatok módosítása 
Ebben a feladatban frissítenie a bérlői adatbázisok közül. 

1. A böngészőben a Contoso Concert Hall található események listájában, és jegyezze fel az utolsó esemény nevét.
2. Az a *PowerShell ISE-ben*, Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájl ...\Learning állítsa be a következő értéket:
    * **$DemoScenario = 5** esemény a helyreállítási régióban egy bérlő törlése
3. Nyomja meg **F5** a szkript
4. Frissítse a Contoso Concert Hall események lapot (http://events.wingtip-dpt.&lt; felhasználó&gt;.trafficmanager.net/contosoconcerthall - helyettesítse &lt;felhasználói&gt; az üzemelő példány felhasználói értékkel), és figyelje meg, hogy az utolsó esemény törölve lett.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Az alkalmazás az eredeti éles régióba települni

Ez a feladat repatriates az alkalmazás az eredeti régióba. A valós forgatókönyvekben lenne kezdeményezheti a hazatelepítési, amikor a szolgáltatáskiesés megszüntetése.

### <a name="repatriation-process-overview"></a>Hazatelepítési folyamat áttekintése

![Hazatelepítési architektúra](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

A hazatelepítési folyamat:
1. Megszakítja a szálankénti függőben lévő vagy szükségszerű adatbázis visszaállítási kérések eredményéről.
2. Frissítések a _newtenant_ alias, hogy a forrás régióban a bérlők kiszolgálóra mutasson. Ezt az aliast módosítása biztosítja, hogy az adatbázisok bérlőre most jön létre a forrás régióban.
3. Minden módosított bérlői adatok az eredeti régióba magok
4. Bérlői adatbázisok prioritási sorrendben feladatait.

Feladatátvételi hatékonyan átviszi az adatbázist az eredeti régióba. Az adatbázis átadja a feladatokat, amikor minden megnyitott kapcsolatok megszakadnak, és az adatbázis nem érhető el néhány másodpercig. Alkalmazások az újrapróbálkozási logika annak érdekében, hogy csatlakoznak újra kell írni.  Bár ez rövid disconnect gyakran nem észrevette, dönthet úgy munkaidőn kívüli adatbázisok települni. 


### <a name="run-the-repatriation-script"></a>A hazatelepítési parancsfájl futtatása
Most tegyük fel, hogy a szolgáltatáskimaradás elhárítása után fog állni, és a hazatelepítési parancsfájl futtatásához.

1. Az a *PowerShell ISE-ben*, a ...\Learning Modules\Business folytonossági és vészhelyreállítási Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt.

2. Győződjön meg arról, hogy a katalógus szinkronizálási folyamat továbbra is fut a PowerShell-példányban.  Szükség esetén indítsa újra beállításával:
    * **$DemoScenario = 1**, bérlői kiszolgálóhoz, készletek és adatbázis-konfigurációs adatainak szinkronizálása a katalógusba start
    * A szkriptek futtatásához nyomja le az **F5** billentyűt.

3.  Ezután a hazatelepítési megkezdéséhez beállítása:
    * **$DemoScenario = 6**, települni az alkalmazás az eredeti régióba
    * Nyomja meg **F5** egy új PowerShell-ablakban a helyreállítási szkript futtatásához.  Hazatelepítési több percet vesz igénybe, és a PowerShell-ablakban figyelhető.
    ![Hazatelepítési folyamat](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. A parancsfájl futása közben az események Eseményközpontba oldal frissítése (http://events.wingtip-dpt.&lt; felhasználó&gt;. trafficmanager.net)
    * Figyelje meg, hogy a bérlők számára online állapotúak és elérhetők a folyamat során.

5. Áll befejezése után frissítse az eseményközpontot, és nyissa meg az események Hawthorn Hall lapját. Figyelje meg, hogy az adatbázis az eredeti régióba fogalommeghatározás-e.
    ![Eseményközpont fogalommeghatározás](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Győződjön meg, hogy az alkalmazás- és az alkalmazás kialakítása is ugyanarra a szalagra 
Az alkalmazás célja, hogy mindig kapcsolódik egy példányával, a bérlői adatbázis ugyanabban a régióban. Ez a kialakítás csökkenti a késést az alkalmazás és az adatbázis között. Az optimalizálás feltételezi, hogy az alkalmazás-adatbázis kapcsolati chattier, mint a felhasználó alkalmazás interakció.  

Bérlői adatbázisok hazatelepítési során egy kis ideig recovery és az eredeti régiók között lehetnek elosztva. Minden adatbázis esetében az alkalmazás megkeresi a régiót, amelyben az adatbázis található egy DNS-címkeresés a bérlő kiszolgáló nevére végrehajtásával. SQL Database-ben a kiszolgálónév szerepel aliasként. Az alias-kiszolgáló neve a régió nevét tartalmazza. Ha az alkalmazás nem ugyanabban a régióban, mint az adatbázis, átirányítja a példány és az adatbázis-kiszolgáló ugyanabban a régióban.  Ugyanabban a régióban, mint az adatbázis-példányra átirányítása minimálisra csökkenti az alkalmazás- és a késés. 

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:
> [!div classs="checklist"]

>* Sync-adatbázis és rugalmas tárolókészlet konfigurációs adatait a bérlő-katalógusba
>* Alkalmazás, a kiszolgálók és a készletek egy másik régióban található helyreállítási környezet beállítása
>* Használat _georeplikációs_ az alkalmazáskatalógus és a bérlői adatbázisok replikálása a helyreállítási régió
>* Az alkalmazás és az alkalmazáskatalógus és a bérlői adatbázisok helyreállítási régió feladatátvételt 
>* Feladat-visszavételt az eredeti régió az alkalmazást, a katalógus és a bérlői adatbázisokat a szolgáltatáskiesés megszüntetése után

Az Azure SQL database az üzletmenet folytonosságának biztosítása biztosít technológiáinak többet is megtudhat a [üzleti folytonosság – áttekintés](sql-database-business-continuity.md) dokumentációját.

## <a name="additional-resources"></a>További források

* [A Wingtip SaaS-alkalmazás épülő további oktatóanyagok](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
