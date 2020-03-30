---
title: 'SaaS-alkalmazások: Georedundáns biztonsági mentések a vészhelyreállításhoz'
description: Ismerje meg, hogyan használható az Azure SQL Database georedundáns biztonsági mentések egy több-bérlős SaaS-alkalmazás helyreállításához kimaradás esetén
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/14/2019
ms.openlocfilehash: 270fc157fa14efa19ed30d35b614fb769804b72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826460"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Több-bérlős SaaS-alkalmazás helyreállítása adatbázis-biztonsági mentések segítségével

Ez az oktatóanyag egy teljes vész-helyreállítási forgatókönyvet tár fel egy több-bérlős SaaS-alkalmazás bérlői modellenként az adatbázissal megvalósítva. A [geo-visszaállítás](sql-database-recovery-using-backups.md) segítségével helyreállíthatja a katalógus és a bérlői adatbázisok automatikusan karbantartott georedundáns biztonsági mentések egy másik helyreállítási régióba. A szolgáltatáskimaradás feloldása után [a georeplikáció](sql-database-geo-replication-overview.md) használatával a módosított adatbázisokat az eredeti régióba kell repatriálni.

![Geo-visszaállítás-architektúra](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geo-visszaállítás a legalacsonyabb költségű vész-helyreállítási megoldás az Azure SQL Database. A georedundáns biztonsági mentések visszaállítása azonban akár egy órás adatvesztést is eredményezhet. Az egyes adatbázisok méretétől függően jelentős időt vehet igénybe. 

> [!NOTE]
> Helyreállítani az alkalmazásokat a lehető legalacsonyabb RPO és RTO segítségével georeplikáció helyett geo-visszaállítás.

Ez az oktatóanyag a visszaállítási és a hazatelepítési munkafolyamatokat is feltárja. Az alábbiak végrehajtásának módját ismerheti meg:
> [!div class="checklist"]
> 
> * Szinkronizálja az adatbázist és a rugalmas készlet konfigurációs adatait a bérlői katalógusba.
> * Állítson be egy tükrözött lemezkép-környezetet egy olyan helyreállítási régióban, amely alkalmazásokat, kiszolgálókat és készleteket tartalmaz.   
> * Katalógus- és bérlői adatbázisok helyreállítása geo-visszaállítás használatával.
> * A georeplikáció segítségével hazatelepíti a bérlői katalógust, és a szolgáltatáskimaradás feloldása után módosított bérlői adatbázisokat.
> * Frissítse a katalógust az egyes adatbázisok visszaállításának (vagy hazaszállításakor) az egyes bérlők adatbázisa aktív példányának aktuális helyének nyomon követéséhez.
> * Győződjön meg arról, hogy az alkalmazás és a bérlői adatbázis mindig ugyanabban az Azure-régióban található a késés csökkentése érdekében. 
 

Az oktatóanyag megkezdése előtt hajtsa végre az alábbi előfeltételeket:
* Telepítse a Wingtip jegyek SaaS-adatbázis bérlői alkalmazásonként. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip Tickets SaaS-adatbázis bérlőnkénti telepítése és feltárása című témakört.](saas-dbpertenant-get-started-deploy.md) 
* Az Azure PowerShell telepítése. További információt az [Azure PowerShell – első lépések.](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Bevezetés a geo-visszaállítási helyreállítási mintába

Vész-helyreállítási (DR) fontos szempont számos alkalmazás, akár megfelelőségi okokból, akár az üzletmenet folytonossága. Ha van egy hosszabb szolgáltatáskimaradás, egy jól előkészített DR-terv minimálisra csökkentheti az üzleti zavarokat. A földrajzi visszaállításon alapuló VÉSZ-tervnek több célt kell elérnie:
 * A kiválasztott helyreállítási régióban a lehető leggyorsabban lefoglalja az összes szükséges kapacitást, hogy a bérlői adatbázisok visszaállítása elérhető legyen.
 * Hozzon létre egy tükörkép-helyreállítási környezetet, amely tükrözi az eredeti készlet- és adatbázis-konfigurációt. 
 * Ha az eredeti régió újra online állapotba kerül, engedélyezze a visszaállítási folyamat törlését repülés közben.
 * Engedélyezze a bérlői kiépítést gyorsan, hogy az új bérlői bevezetés a lehető leghamarabb újraindulhasson.
 * Optimalizálni kell a bérlők prioritási sorrendben való visszaállításához.
 * Optimalizálni kell, hogy a bérlők a lehető leghamarabb online állapotba kerüljenek, ha praktikus antotéri lépéseket tesznek párhuzamosan.
 * Legyen rugalmas a hiba, újraindítható, és idempotent.
 * Adatbázisok hazatelepítése az eredeti régióba minimális hatással a bérlők, ha a kimaradás megoldódott.  

> [!NOTE]
> Az alkalmazás helyreáll a régió párosított régiójában, amelyben az alkalmazás telepítve van. További információ: [Azure párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Ez az oktatóanyag az Azure SQL Database és az Azure platform szolgáltatásait használja az alábbi kihívások kezelésére:

* [Az Azure Resource Manager-sablonokat,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)hogy a lehető leggyorsabban lefoglalja az összes szükséges kapacitást. Az Azure Resource Manager-sablonok segítségével egy tükörképet az eredeti kiszolgálók és rugalmas készletek a helyreállítási régióban. Egy külön kiszolgáló és készlet is jönnek létre az új bérlők kiépítéséhez.
* [Rugalmas adatbázis-ügyfélkódtár](sql-database-elastic-database-client-library.md) (EDCL), bérlői adatbázis-katalógus létrehozásához és karbantartásához. A bővített katalógus rendszeresen frissített készlet- és adatbázis-konfigurációs adatokat tartalmaz.
* [Shard felügyeleti helyreállítási funkciók](sql-database-elastic-database-recovery-manager.md) az EDCL, adatbázis helybejegyzéseinek karbantartása a katalógusban a helyreállítás és a hazaszállítás során.  
* [Geo-visszaállítás](sql-database-disaster-recovery.md), a katalógus és a bérlői adatbázisok automatikusan karbantartott georedundáns biztonsági mentések helyreállítása. 
* A rendszer aszinkron [visszaállítási műveleteket , amelyekbérlői](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)prioritási sorrendben kerülnek elküldésre, a rendszer minden egyes készlethez várólistára helyezi, és kötegekben dolgozza fel, így a készlet nincs túlterhelve. Ezek a műveletek szükség esetén a végrehajtás előtt vagy alatt visszavonhatók.   
* [Georeplikáció](sql-database-geo-replication-overview.md), adatbázisok hazaszállítása az eredeti régióban a szolgáltatáskimaradás után. Nincs adatvesztés, és minimális hatással van a bérlőre, ha georeplikációt használ.
* [SQL-kiszolgáló DNS-aliasai](dns-alias-overview.md), hogy a katalógus szinkronizálási folyamat a helyétől függetlenül csatlakozzon az aktív katalógushoz.  

## <a name="get-the-disaster-recovery-scripts"></a>A vész-helyreállítási parancsfájlok beszerzése

Az oktatóanyagban használt DR-parancsfájlok a [Wingtip Tickets SaaS-adatbázisban érhetők el bérlői GitHub-tárházonként.](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) Tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip tickets felügyeleti parancsfájlok letöltéséhez és blokkolásának feloldásához.

> [!IMPORTANT]
> Mint minden Wingtip jegyek felügyeleti parancsfájlok, a DR-parancsfájlok minta minősége, és nem használható éles környezetben.

## <a name="review-the-healthy-state-of-the-application"></a>Az alkalmazás állapotának áttekintése
A helyreállítási folyamat megkezdése előtt tekintse át az alkalmazás normál kifogástalan állapotát.

1. A webböngészőben nyissa meg a Wingtip&gt;Jegyek események &lt;hub&gt; (http://events.wingtip-dpt.&lt;user .trafficmanager.net, cserélje le a felhasználót a központi telepítés felhasználói értékére).
    
   Görgessen a lap aljára, és figyelje meg a katalóguskiszolgáló nevét és helyét az élőlábban. A hely az a régió, ahol az alkalmazást telepítette.    

   > [!TIP]
   > Vigye az egeret az egérmutatót a hely fölé a kijelző nagyításához.

   ![Események hub kifogástalan állapotban az eredeti régióban](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Válassza ki a Contoso Concert Hall bérlő, és nyissa meg az esemény lap.

   A láblécben figyelje meg a bérlő kiszolgálónevét. A hely megegyezik a katalóguskiszolgáló helyével.

   ![Contoso Concert Hall eredeti régió](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. Az [Azure Portalon](https://portal.azure.com)tekintse át, és nyissa meg az erőforráscsoportot, amelyben az alkalmazást telepítette.

   Figyelje meg az erőforrásokat és azt a régiót, amelyben az alkalmazásszolgáltatás-összetevők és az SQL Database-kiszolgálók telepítve vannak.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>A bérlői konfiguráció szinkronizálása a katalógusban

Ebben a feladatban elindíthatja a kiszolgálók, rugalmas készletek és adatbázisok konfigurációjának szinkronizálására szolgáló folyamatot a bérlői katalógusba. Ezt az információt később a helyreállítási régióban lévő tükörkép-környezet konfigurálására használják.

> [!IMPORTANT]
> Az egyszerűség kedvéért a szinkronizálási folyamat és más hosszú ideig futó helyreállítási és hazatelepítési folyamatok ezekben a mintákban helyi PowerShell-feladatokként vagy az ügyfélfelhasználói bejelentkezés alatt futó munkamenetekként vannak megvalósítva. A bejelentkezéskor kiadott hitelesítési tokenek több óra elteltével lejárnak, és a feladatok sikertelenek lesznek. Éles környezetben a hosszú ideig futó folyamatokat kell végrehajtani, mint a megbízható Azure-szolgáltatások valamilyen, egyszerű szolgáltatás alatt futó. Lásd: [Az Azure PowerShell használata egyszerű szolgáltatás létrehozása tanúsítvánnyal.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) 

1. A PowerShell ISE-ben nyissa meg a ...\Learning Modules\UserConfig.psm1 fájlt. Cserélje `<resourcegroup>` `<user>` le és a 10- es és 11-es sorokat az alkalmazás telepítésekor használt értékkel. Mentse a fájlt.

2. A PowerShell ISE-ben nyissa meg a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlt.

    Ebben az oktatóanyagban futtatja az ebben a PowerShell-parancsfájlban szereplő egyes forgatókönyveket, ezért tartsa nyitva ezt a fájlt.

3. Állítsa be a következőket:

    $DemoScenario = 1: Indítsa el a háttérfeladat, amely szinkronizálja a bérlői kiszolgáló és a készlet konfigurációs adatait a katalógusba.

4. A szinkronizálási parancsfájl futtatásához válassza az F5 lehetőséget. 

    Ezt az információt később annak biztosítására használjuk, hogy a helyreállítás tükörképet képezakiszolgálókról, készletekről és adatbázisokról a helyreállítási régióban.  

    ![Szinkronizálási folyamat](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Hagyja futni a PowerShell-ablakot a háttérben, és folytassa az oktatóanyag többi részével.

> [!NOTE]
> A szinkronizálási folyamat DNS-aliason keresztül kapcsolódik a katalógushoz. Az alias a visszaállítás és a hazaszállítás során módosul, hogy az aktív katalógusra mutasson. A szinkronizálási folyamat naprakészen tartja a katalógust a helyreállítási régióban végrehajtott adatbázis- vagy készletkonfiguráció-módosításokkal. A hazaszállítás során ezek a módosítások az eredeti régió egyenértékű erőforrásaira vonatkoznak.

## <a name="geo-restore-recovery-process-overview"></a>Geo-visszaállítási helyreállítási folyamat áttekintése

A geo-visszaállítási helyreállítási folyamat telepíti az alkalmazást, és visszaállítja az adatbázisokat a biztonsági mentések a helyreállítási régióba.

A helyreállítási folyamat a következőket teszi:

1. Letiltja az Azure Traffic Manager végpont a webalkalmazás az eredeti régióban. A végpont letiltása megakadályozza, hogy a felhasználók érvénytelen állapotban csatlakozzanak az alkalmazáshoz, ha az eredeti régió a helyreállítás során online állapotba kerül.

2. A helyreállítási katalógus kiszolgálója a helyreállítási régióban, geo-visszaállítja a katalógus adatbázist, és frissíti az activecatalog aliast, hogy a visszaállított katalóguskiszolgálóra mutasson. A katalógusalias módosítása biztosítja, hogy a katalógus szinkronizálási folyamata mindig szinkronizálva legyen az aktív katalógussal.

3. A helyreállítási katalógusban lévő összes meglévő bérlőt offline állapotban jelöli meg, hogy a visszaállítás előtt megakadályozza a bérlői adatbázisokhoz való hozzáférést.

4. Az alkalmazás egy példányát a helyreállítási régióban, és konfigurálja, hogy használja a visszaállított katalógust az adott régióban. A késés minimális, a mintaalkalmazás célja, hogy mindig csatlakozzon egy bérlői adatbázis ugyanabban a régióban.

5. Egy kiszolgálót és egy rugalmas készletet biztosít, amelyben új bérlők vannak kiépítve. Ezek az erőforrások létrehozása biztosítja, hogy az új bérlők kiépítése nem zavarja a meglévő bérlők helyreállítását.

6. Frissíti az új bérlői aliast, hogy a helyreállítási régióúj bérlői adatbázisainak kiszolgálójára mutasson. Az alias módosítása biztosítja, hogy az új bérlők adatbázisai ki vannak építve a helyreállítási régióban.
        
7. Kiszolgálók és rugalmas készletek a helyreállítási régióban a bérlői adatbázisok visszaállításához. Ezek a kiszolgálók és készletek az eredeti régió konfigurációjának tükörképei. A készletkiépítési készletek előre fenntartja az összes adatbázis visszaállításához szükséges kapacitást.

    Egy régióban egy kimaradás jelentős nyomást gyakorolhat a párosított régióban rendelkezésre álló erőforrásokra. Ha a DR geo-visszaállítási szolgáltatását használja, akkor az erőforrások gyors lefoglalása ajánlott. Fontolja meg a georeplikációt, ha fontos, hogy egy alkalmazás egy adott régióban helyreáll. 

8. Engedélyezi a Traffic Manager-végpontot a webalkalmazáshoz a helyreállítási régióban. A végpont engedélyezése lehetővé teszi, hogy az alkalmazás új bérlők kiépítése. Ebben a szakaszban a meglévő bérlők továbbra is offline állapotban vannak.

9. Elküldi az adatbázisok prioritási sorrendben való visszaállítására irányuló kérelmek kötegeit. 

    * A kötegek úgy vannak rendezve, hogy az adatbázisok az összes készletben párhuzamosan visszaállhassanak.  

    * A visszaállítási kérelmek aszinkron módon kerülnek elküldésre, így azok gyorsan elküldve vannak, és az egyes készletekben végrehajtásra várnak.

    * Mivel a visszaállítási kérelmek feldolgozása párhuzamosan történik az összes készlet között, jobb, ha fontos bérlőket oszt szét számos készlet között. 

10. Az SQL Database szolgáltatás figyelésével határozza meg, hogy mikor állnak vissza az adatbázisok. A bérlői adatbázis visszaállítása után online van megjelölve a katalógusban, és a bérlői adatbázis rowversion összege rögzítésre kerül. 

    * A bérlői adatbázisok at az alkalmazás is elérheti, amint online meg vannak jelölve a katalógusban.

    * A bérlői adatbázis ban lévő rowversion értékek összege a katalógusban tárolódik. Ez az összeg ujjlenyomatként működik, amely lehetővé teszi a hazatelepítési folyamat számára annak megállapítását, hogy az adatbázis frissítve lett-e a helyreállítási régióban.       

## <a name="run-the-recovery-script"></a>A helyreállítási parancsfájl futtatása

> [!IMPORTANT]
> Ez az oktatóanyag visszaállítja az adatbázisokat a georedundáns biztonsági mentésből. Bár ezek a biztonsági mentések általában 10 percen belül elérhetők, akár egy órát is igénybe vehet. A parancsfájl szünetel, amíg elérhetővé nem válik.

Képzelje el, hogy kimaradás van abban a régióban, ahol az alkalmazás telepítve van, és futtassa a helyreállítási parancsfájlt:

1. A PowerShell ISE-ben a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlban állítsa be a következő értéket:

    $DemoScenario = 2: Helyreállítsa az alkalmazást egy helyreállítási régióba a georedundáns biztonsági mentések visszaállításával.

2. A parancsfájl futtatásához válassza az F5 lehetőséget.  

    * A parancsfájl megnyílik egy új PowerShell-ablakban, majd elindítja a párhuzamosan futó PowerShell-feladatok készletét. Ezek a feladatok visszaállítják a kiszolgálókat, készleteket és adatbázisokat a helyreállítási régióba.

    * A helyreállítási régió az Azure-régióhoz társított párosított régió, amelyben az alkalmazást telepítette. További információ: [Azure párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Figyelje a helyreállítási folyamat állapotát a PowerShell ablakban.

    ![Helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> A helyreállítási feladatok kódjának megismeréséhez tekintse át a PowerShell-parancsfájlokat a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs mappában.

## <a name="review-the-application-state-during-recovery"></a>Az alkalmazás állapotának áttekintése a helyreállítás során
Bár az alkalmazás végpontja le van tiltva a Traffic Managerben, az alkalmazás nem érhető el. A katalógus vissza áll, és az összes bérlő offline van megjelölve. Az alkalmazás végpontja a helyreállítási régióban ezután engedélyezve van, és az alkalmazás újra online állapotba kerül. Bár az alkalmazás elérhető, a bérlők offline állapotban jelennek meg az eseményközpontban, amíg az adatbázisok vissza nem állnak. Fontos, hogy az alkalmazás kapcsolat nélküli bérlői adatbázisok kezelésére tervezze meg.

* Miután a katalógus-adatbázis helyreállt, de mielőtt a bérlők újra online állapotba kerül, frissítse a Wingtip Jegyek események hub a webböngészőben.

  * Az élőlábban figyelje meg, hogy a katalóguskiszolgáló neve most már rendelkezik egy -recovery utótaggal, és a helyreállítási régióban található.

  * Figyelje meg, hogy a bérlők, amelyek még nem állnak vissza, offline ként vannak megjelölve, és nem választhatók ki.   
 
    ![Helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

  * Ha közvetlenül a bérlő i. Ha például a Contoso Koncertterem offline http://events.wingtip-dpt.&ltállapotban van, próbálja megnyitni a .user&gt;.trafficmanager.net/contosoconcerthall.

    ![Helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Új bérlő kiépítése a helyreállítási régióban
A bérlői adatbázisok visszaállítása előtt is üzembe helyezhet új bérlőket a helyreállítási régióban. A helyreállítási régióban kiépített új bérlői adatbázisok at később a helyreállított adatbázisok hazaszállítják.   

1. A PowerShell ISE-ben a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlban állítsa be a következő tulajdonságot:

    $DemoScenario = 3: Új bérlő kiépítése a helyreállítási régióban.

2. A parancsfájl futtatásához válassza az F5 lehetőséget.

3. A Hawthorn Hall események lap megnyílik a böngészőben, amikor a kiépítés befejeződik. 

    Figyelje meg, hogy a Hawthorn Hall adatbázis a helyreállítási régióban található.

    ![Hawthorn Hall kiépített a helyreállítási régióban](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. A böngészőben frissítse a Wingtip Jegyek események hub oldalt, hogy Hawthorn Hall is. 

    Ha a Hawthorn Hall kiépítése megvárása nélkül a többi bérlő visszaállítása, más bérlők továbbra is offline állapotban lehet.

## <a name="review-the-recovered-state-of-the-application"></a>Az alkalmazás helyreállított állapotának áttekintése

Amikor a helyreállítási folyamat befejeződik, az alkalmazás és az összes bérlő teljes mértékben működőképes a helyreállítási régióban. 

1. Miután a PowerShell konzolablakban a kijelző azt jelzi, hogy az összes bérlő helyreáll, frissítse az eseményközpontot. 

    A bérlők mind megjelennek az interneten, beleértve az új bérlőt, Hawthorn Hallt is.

    ![Helyreállított és új bérlők az eseményközpontban](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Kattintson a Contoso Concert Hall és nyissa meg a rendezvények oldalon. 

    Az élőlábban figyelje meg, hogy az adatbázis a helyreállítási régióban található helyreállítási kiszolgálón található.

    ![Contoso a helyreállítási régióban](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Az [Azure Portalon](https://portal.azure.com)nyissa meg az erőforráscsoportok listáját.  

    Figyelje meg a telepített erőforráscsoportot, valamint a helyreállítási erőforráscsoportot a -recovery utótaggal. A helyreállítási erőforráscsoport tartalmazza a helyreállítási folyamat során létrehozott összes erőforrást, valamint a kimaradás során létrehozott új erőforrásokat. 

4. Nyissa meg a helyreállítási erőforráscsoportot, és figyelje meg a következő elemeket:

   * A katalógus és a bérlők1 kiszolgálók helyreállítási verziói a -recovery utótaggal. A visszaállított katalógus és a bérlői adatbázisok ezeken a kiszolgálókon mind az eredeti régióban használt neveket.

   * A&lt;tenants2-dpt-&gt;user -recovery SQL-kiszolgáló. Ez a kiszolgáló új bérlők kiépítésére szolgál a szolgáltatáskimaradás során.

   * Az alkalmazás szolgáltatás nevű&lt;események-wingtip-dpt- recoveryregion&gt;-&lt;felhasználó&gt;, amely az események alkalmazás helyreállítási példányát.

     ![Contoso-erőforrások a helyreállítási régióban](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Nyissa meg a tenants2-dpt-&lt;user&gt;-recovery SQL-kiszolgálót. Figyelje meg, hogy tartalmazza az adatbázis hawthornhall és a rugalmas készlet készlet1. A hawthornhall-adatbázis rugalmas adatbázisként van konfigurálva a Készlet1 rugalmas készletben.

## <a name="change-the-tenant-data"></a>A bérlői adatok módosítása 
Ebben a feladatban frissíti az egyik visszaállított bérlői adatbázist. A hazatelepítési folyamat az eredeti régióra módosított visszaállított adatbázisokat másolja. 

1. A böngészőben keresse meg a Contoso Koncertterem eseménylistáját, görgessen végig az eseményeken, és figyelje meg az utolsó eseményt, a Seriously Strauss-t.

2. A PowerShell ISE-ben a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlban állítsa be a következő értéket:

    $DemoScenario = 4: Egy esemény törlése egy bérlőből a helyreállítási régióban.

3. A parancsfájl végrehajtásához válassza az F5 lehetőséget.

4. Frissítse a Contoso Concerthttp://events.wingtip-dpt.&ltHall&gt;események lap ( ;user .trafficmanager.net/contosoconcerthall), és figyelje meg, hogy az esemény Komolyan Strauss hiányzik.

Ezen a ponton az oktatóanyag, akkor vissza az alkalmazást, amely most fut a helyreállítási régióban. Egy új bérlőt létesített a helyreállítási régióban, és módosította az egyik visszaállított bérlő adatait.  

> [!NOTE]
> A mintában szereplő egyéb oktatóanyagok nem úgy vannak kialakítva, hogy az alkalmazással a helyreállítási állapotban fussanak. Ha azt szeretnénk, hogy vizsgálja meg más oktató, győződjön meg róla, hogy hazaaz alkalmazás első.

## <a name="repatriation-process-overview"></a>A hazatelepítési folyamat áttekintése

A hazatelepítési folyamat visszaállítja az alkalmazást és annak adatbázisait az eredeti régióba egy kimaradás feloldása után.

![Geo-visszaállítás hazaszállítása](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

A folyamat:

1. Leállít minden folyamatban lévő visszaállítási tevékenységet, és törli a függőben lévő vagy a fedélzeti adatbázis-visszaállítási kérelmeket.

2. Újraaktiválja az eredeti régióban bérlői adatbázisok, amelyek nem változtak meg a kimaradás óta. Ezek az adatbázisok közé tartoznak azok, amelyek még nem helyreálltak, és azok, amelyeket később nem sikerült megváltoztatni. Az újraaktivált adatbázisok pontosan olyanok, mint a bérlők által utoljára elért.

3. Az új bérlő kiszolgálójának és rugalmas készletének tükörképét az eredeti régióban ismerteti. A művelet befejezése után az új bérlői alias frissül, hogy erre a kiszolgálóra mutasson. Az alias frissítése új bérlői bevezetést eredményez az eredeti régióban a helyreállítási régió helyett.

3. A georeplikáció segítségével a katalógust a helyreállítási régióból az eredeti régióba helyezheti át.

4. Frissíti a készlet konfigurációját az eredeti régióban, így konzisztens a helyreállítási régióban a szolgáltatáskimaradás során végrehajtott módosításokkal.

5. Létrehozza a szükséges kiszolgálókat és készleteket a kimaradás során létrehozott új adatbázisok üzemeltetéséhez.

6. Georeplikáció val hazatelepíteni a visszaállított bérlői adatbázisok, amelyek a visszaállítás után frissített, és az összes új bérlői adatbázisok kiépítése során a szolgáltatáskimaradás. 

7. Törli a helyreállítási régióban a visszaállítási folyamat során létrehozott erőforrásokat.

A repatriatálandó bérlői adatbázisok számának korlátozása érdekében az 1–3.  

4. lépés csak akkor történik meg, ha a katalógus a helyreállítási régióban módosult a kimaradás során. A katalógus frissül, ha új bérlők jönnek létre, vagy ha bármely adatbázis vagy készlet konfigurációja megváltozik a helyreállítási régióban.

Fontos, hogy a 7. E cél elérése érdekében a folyamat georeplikációt használ.

Az egyes adatbázisok georeplikálása előtt a megfelelő adatbázis törlődik az eredeti régióban. A helyreállítási régióban lévő adatbázis ezután georeplikált, másodlagos replika létrehozása az eredeti régióban. A replikáció befejezése után a bérlő offline módban van megjelölve a katalógusban, amely megszakítja a kapcsolatot az adatbázissal a helyreállítási régióban. Az adatbázis ezután feladatátvételt kap, így a függőben lévő tranzakciók feldolgozható a másodlagos, így nem adat vesznek el. 

Feladatátvételkor az adatbázis-szerepkörök sztornírozva vannak. A másodlagos az eredeti régióban lesz az elsődleges olvasási és olvasási adatbázis, és az adatbázis a helyreállítási régióban lesz egy írásvédett másodlagos. A bérlői bejegyzés a katalógusban frissül, hogy az eredeti régióban lévő adatbázisra hivatkozzon, és a bérlő online van megjelölve. Ezen a ponton az adatbázis hazatelepítése befejeződött. 

Az alkalmazásokat újrapróbálkozási logikával kell írni annak érdekében, hogy a kapcsolatok megszakadásakor automatikusan újracsatlakozzanak. Amikor a katalógus takar a reconnection közvetítésére, csatlakoznak a hazatelepített adatbázishoz az eredeti régióban. Bár a rövid kapcsolat bontása gyakran nem veszik észre, előfordulhat, hogy úgy dönt, hogy hazatelepíti adatbázisok munkaidőn kívül.

Az adatbázis hazaszállítása után a másodlagos adatbázis a helyreállítási régióban törölhető. Az adatbázis az eredeti régióban, majd ismét támaszkodik a geo-visszaállítás a VÉSZ-védelem.

a 8.

## <a name="run-the-repatriation-script"></a>A hazatelepítési parancsfájl futtatása
Képzeljük el, hogy a leállás megoldódott, és futtassa a hazatelepítési parancsfájlt.

Ha követte az oktatóanyagot, a szkript azonnal újraaktiválja a Fabrikam Jazz Clubot és a Dogwood Dojo-t az eredeti régióban, mert változatlanok. Ezután hazatelepíti az új bérlőt, a Hawthorn Hallt és a Contoso Koncerttermet, mert módosították. A parancsfájl is repatriates a katalógus, amely frissült, amikor Hawthorn Hall kiépítésre került.
  
1. A PowerShell ISE,-ban a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlban ellenőrizze, hogy a katalógusszinkronizálási folyamat továbbra is fut-e a PowerShell-példányban. Szükség esetén indítsa újra a következő beállítással:

    $DemoScenario = 1: Indítsa el a bérlői kiszolgáló, a készlet és az adatbázis konfigurációs adatainak szinkronizálását a katalógusban.

    A parancsfájl futtatásához válassza az F5 lehetőséget.

2.  Ezután indítsa el a hazatelepítési folyamatot, állítsa be:

    $DemoScenario = 5: Az alkalmazás hazatelepítése az eredeti régióba.

    A helyreállítási parancsfájl futtatásához egy új PowerShell-ablakban válassza az F5 lehetőséget. A hazatelepítés néhány percet vesz igénybe, és a PowerShell ablakban figyelhető.

3. A parancsfájl futása közben frissítse azhttp://events.wingtip-dpt.&lteseményközpont lapját ( ;user&gt;.trafficmanager.net).

    Figyelje meg, hogy az összes bérlő online és elérhető a folyamat során.

4. Válassza ki a Fabrikam Jazz Club megnyitásához. Ha nem módosította ezt a bérlőt, az élőláb értesítést, hogy a kiszolgáló már vissza lett állítva az eredeti kiszolgálóra.

5. Nyissa meg vagy frissítse a Contoso Concert Hall események lapját. Figyelje meg az élőlábtól, hogy az adatbázis kezdetben még mindig a -recovery kiszolgálón van. 

6. Frissítse a Contoso Concert Hall események lap, amikor a hazatelepítési folyamat befejeződik, és figyelje meg, hogy az adatbázis most az eredeti régióban.

7. Frissítse újra az eseményközpontot, és nyissa meg a Hawthorn Hall-t. Figyelje meg, hogy az adatbázis is található az eredeti régióban. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Helyreállítási régió erőforrásainak karbantartása a hazatelepítés után
A hazaszállítás befejezése után biztonságosan törölheti az erőforrásokat a helyreállítási régióban. 

> [!IMPORTANT]
> Törölje ezeket az erőforrásokat azonnal, hogy leállítsa az összes számlázást.

A visszaállítási folyamat létrehozza az összes helyreállítási erőforrást egy helyreállítási erőforráscsoportban. A törlési folyamat törli ezt az erőforráscsoportot, és eltávolítja az erőforrásokra mutató összes hivatkozást a katalógusból. 

1. A PowerShell ISE,-ban állítsa be a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlt:
    
    $DemoScenario = 6: Törölje az elavult erőforrásokat a helyreállítási régióból.

2. A parancsfájl futtatásához válassza az F5 lehetőséget.

A parancsfájlok megtisztítása után az alkalmazás ott tér vissza, ahol elindult. Ezen a ponton újra futtathatja a parancsfájlt, vagy kipróbálhatja a többi oktatóanyagot.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Az alkalmazás tervezése annak biztosítása érdekében, hogy az alkalmazás és az adatbázis közös 
Az alkalmazás célja, hogy mindig csatlakozni egy példány ugyanabban a régióban, mint a bérlő adatbázisa. Ez a kialakítás csökkenti az alkalmazás és az adatbázis közötti késést. Ez az optimalizálás feltételezi, hogy az alkalmazás-adatbázis interakció chattier, mint a felhasználó és az alkalmazás közötti interakció.  

A bérlői adatbázisok a helyreállítás és az eredeti régiók között lehetnek elosztva a hazaszállítás során. Az alkalmazás minden egyes adatbázis esetében megkeresi azt a régiót, amelyben az adatbázis található, a bérlői kiszolgáló nevének DNS-keresése alapján. Az SQL Database rendszerben a kiszolgáló neve alias. Az aliasos kiszolgálónév tartalmazza a régió nevét. Ha az alkalmazás nem ugyanabban a régióban, mint az adatbázis, átirányítja a példány ugyanabban a régióban, mint az adatbázis-kiszolgáló. Átirányítása a példány ugyanabban a régióban, mint az adatbázis minimáliskéssze az alkalmazás és az adatbázis közötti késés.  

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> 
> * A bérlői katalógus segítségével rendszeresen frissített konfigurációs adatokat, amely lehetővé teszi a tükörkép helyreállítási környezet ben létrehozott egy másik régióban.
> * Az Azure SQL-adatbázisok helyreállítása a helyreállítási régióban a geo-visszaállítás használatával.
> * Frissítse a bérlői katalógust, hogy tükrözze a visszaállított bérlői adatbázis helyeket. 
> * Dns-alias használatával engedélyezheti, hogy egy alkalmazás újrakonfigurálás nélkül csatlakozzon a bérlői katalógushoz.
> * A georeplikáció segítségével a helyreállított adatbázisokat a kimaradás feloldása után az eredeti régióba telepítsék.

Próbálja meg a [vész-helyreállítási egy több-bérlős SaaS-alkalmazás segítségével adatbázis georeplikációs](saas-dbpertenant-dr-geo-replication.md) oktatóanyag, hogy megtanulják, hogyan használhatja a georeplikáció, hogy jelentősen csökkentse a szükséges időt egy nagyméretű több-bérlős alkalmazás helyreállításához.

## <a name="additional-resources"></a>További források

[További oktatóanyagok, amelyek a Wingtip SaaS alkalmazásra épülnek](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
