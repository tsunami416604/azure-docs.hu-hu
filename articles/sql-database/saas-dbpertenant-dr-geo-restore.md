---
title: Az Azure SQL-adatbázis georedundáns biztonsági mentés használata, valamint az SaaS-alkalmazások |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure SQL-adatbázis georedundáns biztonsági mentések egy több-bérlős SaaS-alkalmazás helyreállítása meghibásodás esetére
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 04/16/2018
ms.openlocfilehash: adb3778c723f7c56b5f31381c804e39a8b782709
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056189"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Adatbázisok biztonsági mentése egy több-bérlős SaaS-alkalmazás helyreállítása geo-visszaállítás használatával

Ez az oktatóanyag bemutatja egy több-bérlős SaaS-alkalmazás az egyes bérlői modellek az adatbázis valósítja meg a teljes vész-helyreállítási helyzetekre. Használhat [geo-visszaállítás](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) automatikusan karbantartott georedundáns biztonsági másolatból az alkalmazáskatalógus és a bérlői adatbázisok helyreállítani egy másodlagos helyreállítási régióba. A szolgáltatáskiesés megszüntetése után nem oldódik meg, miután [georeplikációs](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) , azok eredeti régióba módosított adatbázisok települni.

![GEO-visszaállítás-architektúra](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

A GEO-visszaállítás a legalacsonyabb költségű vész-helyreállítási megoldást az Azure SQL Database. Azonban az georedundáns biztonsági másolatokból való visszaállítással eredményezhet az adatvesztést, akár egy óráig. Minden egyes adatbázis méretétől függően hosszabb időt is igénybe vehet. 

> [!NOTE]
> A geo-visszaállítás helyett georeplikáció használatával állíthatja helyre az alkalmazások a lehető legalacsonyabb RPO és RTO.

Ez az oktatóanyag bemutatja, visszaállítási és a hazatelepítési munkafolyamatokat. Az alábbiak végrehajtásának módját ismerheti meg:
> [!div class="checklist"]

>* Sync-adatbázis és rugalmas tárolókészlet konfigurációs adatait a bérlő-katalógusba.
>* Állítsa be egy tükrözött lemezképét környezetet, amely tartalmazza az alkalmazás, a kiszolgálók és a készletek helyreállítási régióban.   
>* A geo-visszaállítás használatával állíthatja helyre az alkalmazáskatalógus és a bérlői adatbázisok.
>* Georeplikáció használatával települni a bérlői katalógus és a módosított bérlői adatbázisok a szolgáltatáskiesés megszüntetése után.
>* A katalógus frissíteni, mivel minden adatbázis a visszaállított (vagy fogalommeghatározás) nyomon követéséhez az aktív másolata, minden bérlői adatbázis aktuális helyét.
>* Győződjön meg arról, hogy az alkalmazás és a bérlői adatbázis mindig közös találhatók ugyanabban a régióban az Azure a késés csökkentése érdekében. 
 

Ebben az oktatóanyagban a Kezdés előtt végezze el az alábbiakat:
* Az egyes bérlői alkalmazások a Wingtip Tickets SaaS-adatbázis üzembe helyezése. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezése és felfedezése a Wingtip Tickets SaaS adatbázis bérlői alkalmazásonként](saas-dbpertenant-get-started-deploy.md). 
* Az Azure PowerShell telepítése. További információkért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>A geo-visszaállítás helyreállítási mintáját bemutatása

A vészhelyreállítás (DR) sok alkalmazás esetén fontos szempont a megfelelőségi okokból, vagy az üzletmenet folytonosságának. Hosszan tartó szolgáltatáskimaradás esetén egy jól felkészültnek Vészhelyreállítási terv minimalizálhatja az üzletmenet megszakadása. A Vészhelyreállítási terv alapján a geo-visszaállítás különböző célok kell elvégezni:
 * Győződjön meg arról, hogy legyen elérhető a bérlői adatbázisok visszaállítása a lehető leggyorsabban lefoglalni az összes szükséges kapacitás a kiválasztott helyreállítási régióban.
 * Egy tükrözött lemezképét helyreállítási környezet, amely tükrözi az eredeti készlet és az adatbázis-konfigurációt hoz létre. 
 * Lehetővé teszi a visszaállítási folyamat közben továbbított megszüntetésének, ha az eredeti régió visszatér online állapotba.
 * Engedélyezze a bérlő kiépítésének gyorsan, így az új bérlő regisztrációs a lehető leghamarabb újraindíthatja.
 * Bérlők prioritási sorrendben visszaállítására lehet optimalizálni.
 * Párhuzamos lépések végrehajtásával, ahol gyakorlati minél hamarabb beolvasni a bérlők online optimalizálni.
 * Hiba, újraindítható, és idempotens rugalmasak lehetnek.
 * Csak minimális hatással van a bérlők számára saját eredeti régióba adatbázisok települni, ha a szolgáltatás újraindulása után.  

> [!NOTE]
> Az alkalmazás hasznosítják a régió, amelyben az alkalmazás központi telepítése a párosított régióba. További információkért lásd: [Azure párosított régióiról](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Ebben az oktatóanyagban az Azure SQL Database és az Azure platform szolgáltatásait használja ezek a kihívások megoldása érdekében:

* [Az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), az összes szükséges kapacitás, a lehető leggyorsabban fenntartására. Az Azure Resource Manager-sablonok segítségével üzembe helyezése egy tükrözött lemezképét az eredeti kiszolgálók és a rugalmas készletek a helyreállítási régióban. Egy önálló kiszolgáló és a készlet is hoz létre az új bérlők kiépítésének folyamatát.
* [Elastic Database-Ügyfélkódtár](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL), a bérlői adatbázis katalógus létrehozásához és kezeléséhez. A kiterjesztett catalog rendszeres időközönként frissülnek készlet és az adatbázis-konfigurációs adatokat tartalmaz.
* [Szilánkleképezés helyreállítási funkciókat](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) , az EDCL, helyreállítási és hazatelepítési során adatbázis helye bejegyzéseket a katalógus fenntartásához.  
* [A GEO-visszaállítás](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), automatikusan karbantartott georedundáns biztonsági másolatból az alkalmazáskatalógus és a bérlői adatbázisok helyreállítani. 
* [Aszinkron visszaállítási műveletek](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), bérlő-prioritási sorrendben küldött, a rendszer minden egyes készlethez helyezi várólistára és feldolgozott kötegekben, így a készlet nem túlterhelt. Ezek a műveletek előtt, vagy ha szükséges, a végrehajtás során szakadhatnak meg.   
* [Georeplikáció](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), hogy az eredeti régióba adatbázisok települni a szolgáltatáskiesés megszüntetése után. Nem érkezik nincs adatvesztés és a lehető legkisebb hatással legyen a bérlő Ha georeplikációt használ.
* [SQL-kiszolgáló DNS-aliasokat](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), hogy a katalógus szinkronizálási folyamat során, függetlenül az, hogy az aktív katalógusban való kapcsolódáshoz.  

## <a name="get-the-disaster-recovery-scripts"></a>A vészhelyreállítási szkripteket beolvasása

A Vészhelyreállítási szkripteket a jelen oktatóanyagban használt érhetők el a [bérlőnként GitHub-adattárat a Wingtip Tickets SaaS adatbázis](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip Tickets felügyeleti parancsfájlokat.

> [!IMPORTANT]
> Minden a Wingtip Tickets felügyeleti parancsfájlokat, például a Vészhelyreállítási szkripteket minta minőségi és, nem éles környezetben használható.

## <a name="review-the-healthy-state-of-the-application"></a>Az alkalmazás kifogástalan állapotának áttekintése
A helyreállítási folyamat megkezdése előtt tekintse át az alkalmazás normál kifogástalan állapotát.

1. A böngészőben nyissa meg a Wingtip Tickets eseményközpontot (http://events.wingtip-dpt.&lt; felhasználó&gt;. trafficmanager.net, cserélje le &lt;felhasználói&gt; az üzemelő példány felhasználói értékkel).
    
   Görgessen a lap aljára, és figyelje meg, hogy a katalógus-kiszolgáló nevét és helyét a láblécben. A hely az a régió, amelyben az alkalmazás üzembe helyezésekor.    

   > [!TIP]
   > Vigye az egérmutatót megjelenítéséhez nagyítsa fel a helyet.

   ![Események központ megfelelő állapotba eredeti régióban](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Válassza ki a Contoso Concert Hall bérlőt, és nyissa meg az események lapján.

   A láblécben figyelje meg a bérlő kiszolgáló nevét. A hely a ugyanaz, mint a katalóguskiszolgálón helye.

   ![Contoso Concert Hall eredeti régió](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. Az a [az Azure portal](https://portal.azure.com), tekintse át, és nyissa meg az erőforráscsoport, amelyben az alkalmazás üzembe helyezésekor.

   Figyelje meg, hogy az erőforrások és a régiót, amelyben az app service-összetevők és az SQL Database-kiszolgálók üzembe vannak helyezve.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>A bérlő konfigurációjához a katalógusba való szinkronizálása

Ebben a feladatban egy szinkronizálni a konfigurációt a bérlő-katalógusba a kiszolgálók, rugalmas készletek és adatbázisok folyamat elindítása. Ez az információ segítségével később egy tükrözött lemezképét környezet konfigurálása a helyreállítási régióban.

> [!IMPORTANT]
> Az egyszerűség kedvéért a szinkronizálási folyamat és egyéb hosszú futású helyreállítási és hazatelepítési folyamatokat az ezeket a mintákat a helyi PowerShell-feladatok vagy az ügyfél-felhasználói bejelentkezés alatt futó munkamenetek van megvalósítva. A hitelesítési tokenek bejelentkezéskor kiadott több óra után lejárnak, és a feladat sikertelen lesz, majd. Éles forgatókönyvekben hosszú futású folyamatok, a megbízható Azure-szolgáltatások bizonyos típusú, egyszerű szolgáltatás alatt futó kell végrehajtani. Lásd: [tanúsítvánnyal egyszerű szolgáltatás létrehozása az Azure PowerShell használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. A PowerShell ISE-ben nyissa meg a ...\Learning Modules\UserConfig.psm1 fájlt. Cserélje le `<resourcegroup>` és `<user>` sorok 10-es és 11 az alkalmazás üzembe helyezésekor használt értékkel. Mentse a fájlt.

2. A PowerShell ISE-ben nyissa meg a ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlt.

    Ebben az oktatóanyagban a PowerShell parancsfájl futtatása az egyes a forgatókönyvek esetében tehát ne zárja be ezt a fájlt.

3. Állítsa be a következőt:

    $DemoScenario = 1: start egy háttérfeladat, mely szinkronizálja a bérlői kiszolgálóhoz és a tárolókészlet konfigurációs adatait a katalógusba.

4. A sync-szkript futtatásához válassza ki az F5 billentyűt. 

    Ez az információ segítségével később győződjön meg arról, hogy recovery létrehoz egy tükrözött lemezképét a kiszolgálókat, készleteket és adatbázisokat a helyreállítási régióban.  

    ![Szinkronizálási folyamat](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Hagyja meg a PowerShell ablakban fut a háttérben, és ez az oktatóanyag további részeinek folytatásához.

> [!NOTE]
> A szinkronizálási folyamat során a katalógus egy DNS-alias-n keresztül csatlakozik. Az alias visszaállítása és az aktív katalógus átirányítása hazatelepítési során is módosul. A szinkronizálási folyamat naprakészen tarthatja a katalógus-adatbázis vagy készlet konfigurációs módosítások a helyreállítási régióban. A hazatelepülés során ezeket a módosításokat az azonos erőforrásokat az eredeti régióban lépnek.

## <a name="geo-restore-recovery-process-overview"></a>A GEO-visszaállítás helyreállítási folyamat áttekintése

A geo-visszaállítás helyreállítási folyamat üzembe helyezi az alkalmazást, és visszaállítja a adatbázisok a biztonsági mentések a helyreállítási régióba.

A helyreállítási folyamat a következőket teszi:

1. A webalkalmazás az eredeti régióban az Azure Traffic Manager végpont letiltása. A végpont letiltása megakadályozza, hogy a felhasználók csatlakozzanak az alkalmazás érvénytelen állapotban kell az eredeti régió online állapotba a helyreállítás során.

2. Egy helyreállítási építi ki a katalógus-adatbázis a kiszolgáló a helyreállítási régióban, geo-visszaállítás katalógus, és frissíti a activecatalog alias, hogy a visszaállított kiszolgáló mutasson. Az alkalmazáskatalógus alias módosítása biztosítja, hogy a katalógus szinkronizálási folyamat mindig szinkronizálja az aktív katalógushoz.

3. Az összes meglévő bérlők számára a helyreállítási katalógus offline állapotúként, hogy megakadályozza a hozzáférést a bérlői adatbázisok visszaállítását végzi előtt jelöli meg.

4. A helyreállítási régióban az alkalmazás egy példányát építi ki, és konfigurálja, hogy a visszaállított katalógus használata az adott régióban. Legkisebb késés megtartásához a mintaalkalmazás célja mindig kapcsolódik egy bérlői adatbázis ugyanabban a régióban.

5. Látja el a kiszolgáló és a rugalmas készletben, amelyben az új bérlők kiépítése történik. Ezek az erőforrások létrehozását biztosítja, hogy új bérlők kiépítése nem zavarja meg a helyreállítás a meglévő bérlők számára.

6. Frissíti az új bérlő alias az kiszolgálóra, az új bérlői adatbázisokat a helyreállítási régióban. Ezt az aliast módosítása biztosítja, hogy minden olyan új bérlők adatbázisok helyreállítási régióban vannak kiépítve.
        
7. Látja a kiszolgálók és a rugalmas készletek a bérlői adatbázisok visszaállítását a helyreállítási régióban. Ezek a kiszolgálók és -készletek egy tükrözött lemezképét, a konfiguráció az eredeti régióban. Az összes adatbázis visszaállításához szükséges kapacitás kiépítése készletek meghozni fenntart.

    Egy régióban leállás előfordulhat, hogy helyezze a párosított régióban elérhető erőforrások jelentős nyomás. Ha a Vészhelyreállításhoz támaszkodnak geo-visszaállítás, majd gyorsan az erőforrások lefoglalását ajánlott. Fontolja meg georeplikációs Ha rendkívül fontos, hogy egy alkalmazás helyreállított-e egy adott régióban. 

8. Lehetővé teszi, hogy a Traffic Manager-végpont a webalkalmazás a helyreállítási régióban. Ez a végpont lehetővé teszi, hogy az alkalmazás új bérlők kiépítése. Ebben a szakaszban a meglévő bérlők számára még mindig offline állapotban van.

9. Adatbázisok prioritási sorrendben visszaállítására irányuló kérelmekre váró küldi el. 

    * Kötegek vannak rendezve, hogy az adatbázisok párhuzamos visszaállnak az összes készletek között.  

    * Visszaállítás kérelmek aszinkron módon nyújtják, így gyorsan elküldve, és a készlet minden egyes végrehajtási várólistára.

    * Visszaállítási kérések feldolgozása párhuzamosan történik az összes készletek között, mert éppen számos készletek között oszthatja el a fontos bérlők számára. 

10. Figyeli az SQL Database szolgáltatás meghatározni, hogy amikor adatbázis visszaállítását végzi. A bérlői adatbázis visszaállítása után a katalógus online van megjelölve, és a bérlői adatbázis rowversion összeg rögzíti. 

    * Bérlői adatbázisok az alkalmazás által hozzáférhető, amint online, a katalógus van megjelölve.

    * A bérlői adatbázis rowversion értékek összege a katalógus tárolja. Ez a sum, amely lehetővé teszi a hazatelepítési folyamat határozza meg, ha az adatbázis frissítve lett-e a helyreállítási régióban ujjlenyomattal funkcionál.       

## <a name="run-the-recovery-script"></a>A helyreállítási szkript futtatása

> [!IMPORTANT]
> Ebben az oktatóanyagban visszaállítja az adatbázis georedundáns biztonsági másolatokból. Bár a másolatokat jellemzően elérhető 10 percen belül, is igénybe vehet egy órát. A parancsfájl felfüggeszti, amíg el nem érhető el.

Képzelje el, szolgáltatáskimaradás van a régióban, amelyben az alkalmazás telepítve, és futtassa a helyreállítási szkriptet:

1. A PowerShell ISE-ben a ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlt, állítsa be a következő értéket:

    $DemoScenario = 2: az alkalmazás helyreállítani egy helyreállítási régió szerint georedundáns biztonsági másolatokból való visszaállítással.

2. A szkript futtatásához válassza ki az F5.  

    * A parancsfájl egy új PowerShell-ablakban nyílik meg, és elindít egy PowerShell-feladatok párhuzamosan futó készletét. Ezek a feladatok kiszolgálók, készletek és adatbázisok visszaállítása a helyreállítási régióban.

    * A helyreállítási régióban az Azure-régió, amelyben az alkalmazás üzembe helyezett társított párosított régió. További információkért lásd: [Azure párosított régióiról](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. A PowerShell-ablakban a helyreállítási folyamat állapotának monitorozásához.

    ![A helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Ismerje meg a helyreállítási feladatok kódja, tekintse át a PowerShell-parancsprogramok a ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-RestoreFromBackup\RecoveryJobs mappában.

## <a name="review-the-application-state-during-recovery"></a>A helyreállítás során az alkalmazás állapotának áttekintése
Az alkalmazás végpont le van tiltva a Traffic Managerben, amíg az alkalmazás nem érhető el. A katalógus visszaállítása, és a bérlők számára vannak megjelölve az offline állapotban van. Az alkalmazás végpontjának a helyreállítási régióban majd engedélyezve van, és az alkalmazás újra online állapotba kerül. Bár az alkalmazás elérhető-e, bérlők jelennek meg offline állapotban az eseményközpontot adatbázisaikat visszaállításáig. Fontos tervezze alkalmazását úgy, hogy kapcsolat nélküli bérlői adatbázisok kezelésére.

* Után a katalógus-adatbázis helyreállítása megtörtént, de mielőtt online állapotba a bérlők számára, frissítse a Wingtip Tickets események központ a böngészőben.

    * A láblécben, figyelje meg, hogy rendelkezik-e már a katalógus kiszolgálónév - helyreállítási utótaggal, és a helyreállítási régióban található.

    * Figyelje meg, hogy a bérlők számára, amely még nem állítja vissza offline állapotban van megjelölve, és amelyek nem választható.   
 
    ![A helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Ha megnyit egy bérlő események lapot közvetlenül a bérlő kapcsolat nélküli módban, az oldal egy bérlő offline értesítés jeleníti meg. Ha például a Contoso Concert Hall offline állapotban, ha megpróbálja megnyitni http://events.wingtip-dpt.&lt; felhasználó&gt;.trafficmanager.net/contosoconcerthall.

    ![A helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>A helyreállítási régióban új bérlő kiépítése
Bérlői adatbázisok visszaállítását végzi, előtt is telepíthet az új bérlők számára a helyreállítási régióban. A helyreállítási régióban üzembe helyezett új bérlői adatbázisokat vannak fogalommeghatározás a helyreállított adatbázisokkal később.   

1. A PowerShell ISE-ben a ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlt, állítsa be a következő tulajdonság:

    $DemoScenario = 3: a helyreállítási régióban új bérlő kiépítése.

2. A szkript futtatásához válassza ki az F5.

3. Az Hawthorn Hall események lapot a böngészőben megnyílik, amikor a kiépítés befejeztét. 

    Figyelje meg, hogy a helyreállítási régióban található-e a Hawthorn adatbázisra.

    ![A helyreállítási régióban üzembe helyezett hawthorn Hall](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. A böngészőben frissítse a Wingtip Tickets események kezdőoldala megtekintéséhez Hawthorn Hall tartalmazza. 

    Hawthorn Hall visszaállítása a többi bérlő várakozás nélkül üzembe helyezése, más bérlők továbbra is lehet offline állapotban van.

## <a name="review-the-recovered-state-of-the-application"></a>Tekintse át az alkalmazást a helyreállított állapotát

A helyreállítási folyamat befejeződése után, az alkalmazás és az összes bérlőre is teljesen működőképes a helyreállítási régióban. 

1. Miután a PowerShell-konzolablakot megjelenítését jelzi, hogy a rendszer visszaállítja az összes bérlőre vonatkozóan, frissítse az eseményközpontot. 

    A bérlő összes online-hoz, beleértve az új bérlő Hawthorn Hall jelennek meg.

    ![az eseményközpont helyreállított és az új bérlők](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Kattintson a Contoso Concert Hall, és az események lap megnyitásához. 

    A láblécben figyelje meg, hogy az adatbázist a helyreállítási kiszolgálón a helyreállítási régióban található.

    ![A helyreállítási régióban contoso](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Az a [az Azure portal](https://portal.azure.com), az erőforráscsoportok listájának megnyitásához.  

    Figyelje meg, hogy az erőforráscsoport üzembe helyezett, valamint a helyreállítási erőforráscsoport, a - helyreállítási utótaggal. A helyreállítási erőforráscsoportba tartozó összes a helyreállítási folyamat során létrehozott erőforrásokat, valamint a szolgáltatáskimaradás közben létrehozott új erőforrásokat tartalmaz. 

4. Nyissa meg a helyreállítási erőforráscsoportot, és figyelje meg a következő elemek:

    * A katalógus és tenants1-kiszolgálókat, a helyreállítási verziói az - helyreállítási utótaggal. A visszaállított katalógus és a bérlői adatbázisok ezeken a kiszolgálókon az összes az eredeti régióban használt nevekkel rendelkeznek.

    * A tenants2-dpt -&lt;felhasználói&gt;– SQL server helyreállítása. Új bérlők kiépítése során a szolgáltatáskimaradás elhárítása után ez a kiszolgáló használható.

    * Az app service nevű események – a wingtip-dpt -&lt;recoveryregion&gt;-&lt;felhasználói&gt;, azaz a helyreállítási példány az események alkalmazás.

    ![A helyreállítási régióban contoso-erőforrások](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Nyissa meg a tenants2-dpt -&lt;felhasználói&gt;– SQL server helyreállítása. Figyelje meg, hogy az adatbázis hawthornhall és a rugalmas készlet Pool1 tartalmazza. A hawthornhall adatbázis egy rugalmas adatbázist a Pool1 rugalmas készletben van konfigurálva.

## <a name="change-the-tenant-data"></a>A bérlő adatainak módosítása 
Ebben a feladatban frissítenie a visszaállított bérlői adatbázisok közül. A hazatelepítési folyamat másolatokat úgy módosítottuk, hogy az eredeti régió-adatbázisok visszaállítása. 

1. A böngészőben események listájában keresése a Contoso Concert Hall, görgessen végig az eseményeket, és figyelje meg, hogy az utolsó esemény, komolyan Strauss.

2. A PowerShell ISE-ben a ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlt, állítsa be a következő értéket:

    $DemoScenario = 4: a helyreállítási régióban egy bérlő esemény törlése.

3. Hajtsa végre a parancsfájlt, válassza ki az F5.

4. Frissítse a Contoso Concert Hall események lapot (http://events.wingtip-dpt.&lt; felhasználó&gt;.trafficmanager.net/contosoconcerthall), és figyelje meg, hogy az esemény komolyan Strauss hiányzik.

Ezen a ponton az oktatóanyagban helyreállította az alkalmazásról, így most már fut a helyreállítási régióban. Kiépített egy új bérlőt a helyreállítási régióban, és a módosított adatokat a visszaállított bérlők valamelyikének.  

> [!NOTE]
> Egyéb oktatóanyagok a minta nem tervezték, hogy helyreállítási állapotban az alkalmazás futtatásához. Ha azt szeretné, hogy böngészhet az oktatóanyagok között, mindenképpen először települni a kérelmet.

## <a name="repatriation-process-overview"></a>Hazatelepítési folyamat áttekintése

A hazatelepítési folyamat visszaállítja az alkalmazás és a hozzá tartozó adatbázisok annak eredeti régióba után egy újraindulása után.

![A GEO-visszaállítás hazatelepítési](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

A folyamat:

1. Minden folyamatban lévő visszaállítás tevékenység leáll, és megszakítja a szálankénti függőben lévő vagy szükségszerű adatbázis visszaállítási kérések eredményéről.

2. Újraaktiválja az az eredeti régió bérlői adatbázisban, amely a szolgáltatáskiesés megszüntetése után óta nem módosult. Ezek az adatbázisok a következők: még nincs helyreállítva, valamint azok helyre, de ezt követően nem változott. Az újraaktivált adatbázisok megegyeznek a legutóbbi által elért bérlőik számára.

3. Egy tükrözött lemezképét az eredeti régióban az új bérlő kiszolgáló és a rugalmas készlet kiépítése. Ez a művelet befejezése után az új bérlő alias frissül, hogy a kiszolgáló mutasson. Az alias frissítése hatására az új bérlő regisztrációs történjen a helyreállítási régióban helyett az eredeti régióban.

3. Georeplikáció használatával helyezze át a katalógust az eredeti régióba helyreállítási régióban.

4. Frissítések készletkonfigurációt az eredeti régióban, hogy a helyreállítási régióban leállás során végrehajtott változtatásokat konzisztens legyen.

5. A szükséges kiszolgálókat és az adatbáziskészletek üzemeltetésére leállás során létrehozott új adatbázisokat hoz létre.

6. Georeplikációs adatbázisok frissítve lett-e visszaállítás utáni települni a visszaállított bérlő és a szolgáltatáskimaradás elhárítása után kiépített összes új bérlői adatbázisokat használ. 

7. A helyreállítási régióban a visszaállítási folyamat során létrehozott erőforrások törlése.

A bérlői adatbázisokat kell fogalommeghatározás igénylő számának korlátozásához, 1 – 3. lépéseket az azonnal kell elvégezni.  

4. lépés csak akkor történik meg, ha a katalógust a helyreállítási régióban leállás során nem módosított. A katalógus frissül, ha új bérlők számára, vagy ha bármilyen adatbázis vagy készlet konfigurációjának módosításakor a helyreállítási régióban.

Fontos, hogy a 7. lépés minimális hatására a bérlők és nem történik adatvesztés. E cél eléréséhez, a folyamat a georeplikációt használ.

Minden adatbázisa georeplikált, mielőtt a rendszer törli a megfelelő adatbázist az eredeti régióban. A helyreállítási régióban adatbázisa majd georeplikált, az eredeti régióban másodlagos replika létrehozásához. Replikáció befejezése után a bérlő offline állapotban van, a katalógus, amely minden, az adatbázishoz létesített kapcsolatokhoz a helyreállítási régióban van megjelölve. Az adatbázis majd feladatátvétele, függőben lévő tranzakciók a másodlagos feldolgozandó adatok így nem okoz elvész. 

Feladatátvétel esetén az adatbázis-szerepkörök fordított irányú. A másodlagos az eredeti régióban lesz az elsődleges írási-olvasási adatbázis, az adatbázist a helyreállítási régióban pedig egy csak olvasható másodlagos. Az adatbázis az eredeti régióban hivatkozni a bérlő-bejegyzést a katalógus frissül, és a bérlő online van megjelölve. Ezen a ponton hazatelepítési az adatbázis számára befejeződött. 

Alkalmazások kell írni az újrapróbálkozási logika, győződjön meg arról, hogy azok újracsatlakozás automatikusan amikor a kapcsolat nem működik. Az újracsatlakozás közvetítse a katalógust használnak, csatlakoznak a repatriated adatbázis az eredeti régióban. Bár a rövid bontása gyakran nem észrevette, választhatja a munkaidőn kívüli adatbázisok települni.

Miután egy adatbázis fogalommeghatározás van, a másodlagos adatbázist a helyreállítási régióban lehet törölni. Az adatbázis az eredeti régióban majd támaszkodik újra geo-visszaállítás a DR-védelem.

8. lépésben a helyreállítási régióban, így a helyreállítási és -készletek, erőforrások törlődnek.

## <a name="run-the-repatriation-script"></a>A hazatelepítési parancsfájl futtatása
Tegyük fel, a szolgáltatáskimaradás elhárítása után fog állni, és a hazatelepítési parancsfájl futtatásához.

Ha követte az oktatóanyag, a a parancsfájl azonnali újraaktiválja a Fabrikam Jazz Club és az eredeti régióban somfát Dojo annak mivel korábban már változatlan. Ezután repatriates Hawthorn Hall, és a Contoso Concert Hall az új bérlőhöz, mert módosítva lett. A parancsfájl is repatriates a katalógus, amely Hawthorn Hall lett üzembe helyezve frissítve lett.
  
1. A PowerShell ISE-ben, a ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl, a győződjön meg arról, hogy a katalógus szinkronizálási folyamat továbbra is fut a PowerShell-példányban. Szükség esetén indítsa újra beállításával:

    $DemoScenario = 1: start a bérlői kiszolgálóhoz, készletek és adatbázis-konfigurációs adatainak szinkronizálása a katalógusba.

    A szkript futtatásához válassza ki az F5.

2.  Ezután a hazatelepítési megkezdéséhez beállítása:

    $DemoScenario = 5: települni az alkalmazás az eredeti régióba.

    A helyreállítási szkript futtatásához egy új PowerShell-ablakban válassza ki az F5. Hazatelepítési több percet vesz igénybe, és a PowerShell-ablakban figyelhető.

3. A parancsfájl futása közben az események eseményközpontba oldal frissítése (http://events.wingtip-dpt.&lt; felhasználó&gt;. trafficmanager.net).

    Figyelje meg, hogy a bérlők számára online állapotúak és elérhetők a folyamat során.

4. Válassza ki a Fabrikam Jazz Club való megnyitásához. Ha nem módosítja ezt a bérlőt, figyelje meg, hogy a lábléc az, hogy a kiszolgáló már vissza lett vonva, az eredeti kiszolgálón.

5. Nyissa meg, vagy frissítse a Contoso Concert Hall események lapot. Figyelje meg, az élőláb, hogy kezdetben, az adatbázis továbbra is – a helyreállítási kiszolgálón. 

6. Frissítse a Contoso Concert Hall események lapot a hazatelepítési folyamat befejeződése után, és figyelje meg, hogy az adatbázis jelenleg az eredeti régióban.

7. Frissítse újra az eseményközpontot, és nyissa meg a Hawthorn Hall. Figyelje meg, hogy az adatbázis az eredeti régióban is található. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Helyreállítási régió erőforrások törlése után hazatelepítési
Hazatelepítési befejezése után, biztonságos-e törölni az erőforrásokat a helyreállítási régióban. 

> [!IMPORTANT]
> Törli ezeket az erőforrásokat, azonnal őket az összes számlázási művelet leállításához.

A visszaállítási folyamat összes helyreállítási erőforrás létrehoz egy helyreállítási erőforráscsoportban. A kitakarítási folyamatot magát az erőforráscsoportot, és eltávolít minden hivatkozás, amelyben az erőforrások a katalógusból. 

1. A PowerShell ISE-ben a szkriptben ...\Learning Modules\Business üzletmenet-folytonossági és vészhelyreállítási Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, állítsa be:
    
    $DemoScenario = 6: elavult erőforrások törlését a helyreállítási régióban.

2. A szkript futtatásához válassza ki az F5.

A szkriptjeinek törlésével, miután a az alkalmazás vissza ahol használatába. Ezen a ponton futtassa a parancsprogramot, vagy próbálja ki más oktatóanyagokat.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Az alkalmazás annak érdekében, hogy az alkalmazás és az adatbázis közös elhelyezésű tervezése 
Az alkalmazás mindig kapcsolódik egy példányával, és a bérlői adatbázis ugyanabban a régióban a célja. Ez a kialakítás csökkenti a késést az alkalmazás és az adatbázis között. Az optimalizálás feltételezi, hogy az alkalmazás-adatbázis kapcsolati chattier, mint a felhasználó alkalmazás interakció.  

Bérlői adatbázisok hazatelepítési során egy kis ideig recovery és az eredeti régiók között lehetnek elosztva. Minden adatbázis esetében az alkalmazás megkeresi a régiót, amelyben az adatbázis található egy DNS-címkeresés a bérlő kiszolgáló nevére végrehajtásával. SQL Database-ben a kiszolgálónév szerepel aliasként. Az alias-kiszolgáló neve a régió nevét tartalmazza. Ha az alkalmazás nem ugyanabban a régióban, mint az adatbázis, átirányítja a példány és az adatbázis-kiszolgáló ugyanabban a régióban. Átirányítás, a példány ugyanabban a régióban, mint az adatbázis minimalizálja a késleltetést, az alkalmazás és az adatbázis között.  

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]

>* A bérlői katalógus használata, amely a rendszeres időközönként frissülnek konfigurációs adatokat, amely lehetővé teszi egy tükrözött lemezképét helyreállítási környezet hozható létre egy másik régióban tárolja.
>* A geo-visszaállítás használatával állíthatja helyre az Azure SQL Database-adatbázisok a helyreállítási régióba.
>* A bérlői katalógus, hogy a visszaállított bérlői adatbázis helyek frissítése. 
>* DNS-alias használatával engedélyezheti egy alkalmazás számára a bérlői katalógus egész újrakonfigurálása nélkül csatlakozhat.
>* Georeplikáció használatával helyreállított adatbázis az eredeti régióba települni a szolgáltatáskiesés megszüntetése után.

Próbálja ki a [egy több-bérlős SaaS-alkalmazás, adatbázis-replikációval geo-vészhelyreállítás](saas-dbpertenant-dr-geo-replication.md) oktatóanyagból megtudhatja, hogyan georeplikáció használatával jelentősen csökkentheti a nagyméretű több-bérlős alkalmazások helyreállításához szükséges időt.

## <a name="additional-resources"></a>További források

[A Wingtip SaaS-alkalmazás épülő további oktatóanyagok](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
