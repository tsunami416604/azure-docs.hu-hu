---
title: Vész-helyreállítási saas-alkalmazások georeplikációs
description: Megtudhatja, hogy miként állíthatja helyre a több-bérlős SaaS-alkalmazást kimaradás esetén az Azure SQL Database georeplikálásai használatával
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0668ccf5ceb972dd120e4e3f37be6d879a12d0a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73811701"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Vész-helyreállítás egy több-bérlős SaaS-alkalmazás adatbázis georeplikációját használó

Ebben az oktatóanyagban egy teljes vész-helyreállítási forgatókönyv egy több-bérlős SaaS-alkalmazás bérlőnkénti modell használatával megvalósított teljes vész-helyreállítási forgatókönyvet. Az alkalmazás kimaradás elleni védelme érdekében [_georeplikációval_](sql-database-geo-replication-overview.md) hozzon létre replikákat a katalógus- és bérlői adatbázisokhoz egy alternatív helyreállítási régióban. Ha kimaradás történik, gyorsan átkell adnia ezeket a replikákat a szokásos üzleti műveletek folytatásához. Feladatátvételkor az eredeti régióban lévő adatbázisok a helyreállítási régióban lévő adatbázisok másodlagos replikáivá válnak. Miután ezek a replikák újra online állapotba kerülnek, automatikusan felzárkóznak a helyreállítási régióban lévő adatbázisok állapotához. A kimaradás feloldása után az eredeti termelési régióban lévő adatbázisok at.

Ez az oktatóanyag a feladatátvételi és a feladat-visszavételi munkafolyamatokat is feltárja. A következőket fogja megtanulni:
> [!div class="checklist"]
> 
> * Adatbázis és rugalmas készlet konfigurációs adatainak szinkronizálása a bérlői katalógusban
> * Helyreállítási környezet beállítása egy alternatív régióban, amely alkalmazásból, kiszolgálókból és készletekből áll
> * A katalógus- és bérlői adatbázisok replikálása _georeplikációval_ a helyreállítási régióba
> * Feladatátvétel az alkalmazás- és katalógus- és bérlői adatbázisok felett a helyreállítási régióban 
> * Később az alkalmazás-, katalógus- és bérlői adatbázisok átadása az eredeti régiónak a kimaradás feloldása után
> * A katalógus frissítése, ahogy minden bérlői adatbázis feladatátvételt abérlői adatbázis elsődleges helyének nyomon követéséhez
> * Győződjön meg arról, hogy az alkalmazás és az elsődleges bérlői adatbázis mindig ugyanabban az Azure-régióban található a késés csökkentése érdekében  
 

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy a következő előfeltételek befejeződtek:
* A Wingtip jegyek SaaS-adatbázis bérlői alkalmazásonként telepítve van. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip Tickets SaaS-adatbázis üzembe helyezése és feltárása bérlői alkalmazásonként című témakört.](saas-dbpertenant-get-started-deploy.md)  
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Bevezetés a georeplikációs helyreállítási mintába

![Helyreállítási architektúra](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Vész-helyreállítási (DR) fontos szempont számos alkalmazás, akár megfelelőségi okokból, akár az üzletmenet folytonossága. Ha hosszabb szolgáltatáskimaradás, egy jól előkészített VÉSZ-terv minimálisra csökkentheti az üzleti zavarokat. A georeplikáció használata biztosítja a legalacsonyabb RPO és RTO azáltal, hogy az adatbázis-replikák egy helyreállítási régióban, amely lehet feladatátvételrövid időn belül.

A földrajzi replikáción alapuló VÉSZ-terv három különálló részből áll:
* A helyreállítási környezet létrehozása és karbantartása
* Helyreállítás – az alkalmazás és az adatbázisok feladatátvétele a helyreállítási környezetbe, ha kimaradás történik,
* Hazaszállítás – az alkalmazás és az adatbázisok feladatátvétele az eredeti régióba az alkalmazás feloldása után 

Minden alkatrészt alaposan meg kell fontolni, különösen, ha nagy méretekben működik. Összességében a tervnek több célt kell elérnie:

* Telepítés
    * Tükörkép-környezet létrehozása és karbantartása a helyreállítási régióban. A rugalmas készletek létrehozása és a helyreállítási környezetben lévő adatbázisok replikálása kapacitást foglal le a helyreállítási régióban. A környezet karbantartása magában foglalja az új bérlői adatbázisok replikálását, ahogy azok ki vannak építve.  
* Helyreállítás
    * Ha a napi költségek minimalizálására lekicsinyített helyreállítási környezetet alkalmaznak, a készleteket és az adatbázisokat úgy kell bővíteni, hogy teljes működési kapacitást szerezzenek a helyreállítási régióban
    * Új bérlői kiépítés engedélyezése a helyreállítási régióban a lehető leghamarabb  
    * Optimalizálni kell a bérlők prioritási sorrendben történő visszaállítására
    * Optimalizálni kell arra, hogy a bérlők a lehető leggyorsabban online állapotba kerüljenek, párhuzamosan, ahol praktikus lépéseket tesznek
    * Legyen rugalmas a hiba, újraindítható, és idempotent
    * Lehetővé kell tenni, hogy törölje a folyamatot a repülés közben, ha az eredeti régió jön vissza on-line.
* Hazaszállítás 
    * A helyreállítási régióból az eredeti régió replikáiba származó adatbázisok feladatátvétele minimális hatással van a bérlőkre: nincs adatvesztés és a bérlőnkénti minimális időszak off-line.   

Ebben az oktatóanyagban ezeket a kihívásokat az Azure SQL Database és az Azure platform szolgáltatásai kezelik:

* [Az Azure Resource Manager-sablonokat,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)hogy a lehető leggyorsabban lefoglalja az összes szükséges kapacitást. Az Azure Resource Manager-sablonok az éles kiszolgálók és a helyreállítási régió rugalmas készleteinek tükörképének kiépítésére szolgálnak.
* [Georeplikáció](sql-database-geo-replication-overview.md), hogy aszinkron módon replikált csak olvasható másodlagos az összes adatbázishoz. Egy kimaradás során, a helyreállítási régióreplikák feladatátvételt.  A kimaradás feloldása után, ha adatvesztés nélkül visszaad az eredeti régió adatbázisainak.
* [Aszinkron](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) feladatátvételi műveletek bérlői prioritási sorrendben küldött, nagy számú adatbázis feladatátvételi idejének minimalizálása érdekében.
* [Shard felügyeleti helyreállítási funkciók ,](sql-database-elastic-database-recovery-manager.md)adatbázis-bejegyzések módosítása a katalógusban a helyreállítás és a hazaszállítás során. Ezek a funkciók lehetővé teszik, hogy az alkalmazás helytől függetlenül csatlakozzon a bérlői adatbázisokhoz az alkalmazás újrakonfigurálása nélkül.
* [SQL-kiszolgáló DNS-aliasok](dns-alias-overview.md), az új bérlők zökkenőmentes kiépítésének lehetővé tétele érdekében, függetlenül attól, hogy az alkalmazás melyik régióban működik. A DNS-aliasok arra is használhatók, hogy a katalógus szinkronizálási folyamata a helytől függetlenül csatlakozzon az aktív katalógushoz.

## <a name="get-the-disaster-recovery-scripts"></a>A vész-helyreállítási parancsfájlok beszerzése 

> [!IMPORTANT]
> Mint minden Wingtip jegyek felügyeleti parancsfájlok, a DR-parancsfájlok minta minősége, és nem használható éles környezetben. 

Az oktatóanyagban használt helyreállítási parancsfájlok és a Wingtip alkalmazás forráskódja a [Wingtip Tickets SaaS-adatbázisban érhetők el bérlői GitHub-tárházonként.](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) Tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip tickets felügyeleti parancsfájlok letöltéséhez és blokkolásának feloldásához.

## <a name="tutorial-overview"></a>Az oktatóanyag áttekintése
Ebben az oktatóanyagban először a georeplikáció segítségével hozza létre a Wingtip Tickets alkalmazás és adatbázisainak replikáit egy másik régióban. Ezután feladatátvételt végez a régióban, hogy szimulálja a kimaradásból való helyreállítást. Ha elkészült, az alkalmazás teljes mértékben működőképes a helyreállítási régióban.

Később egy külön hazatelepítési lépésben feladatátvételt a katalógus és a bérlői adatbázisok a helyreállítási régióban az eredeti régióba. Az alkalmazás és az adatbázisok a hazatelepítés során is elérhetőek maradnak. Ha elkészült, az alkalmazás teljesen működőképes az eredeti régióban.

> [!Note]
> Az alkalmazás helyreáll a _régió párosított régiójában,_ amelyben az alkalmazás telepítve van. További információ: [Azure párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Az alkalmazás állapotának áttekintése

A helyreállítási folyamat megkezdése előtt tekintse át az alkalmazás normál kifogástalan állapotát.
1. A webböngészőben nyissa meg a Wingtip&gt;Tickets Events &lt;Hub&gt; (;userhttp://events.wingtip-dpt.&lt.trafficmanager.net – cserélje le a felhasználót a központi telepítés felhasználói értékére).
    * Görgessen a lap aljára, és figyelje meg a katalóguskiszolgáló nevét és helyét az élőlábban. A hely az a régió, ahol az alkalmazást telepítette.
    *TIPP: Vigye az egeret a hely fölé a kijelző nagyításához.* 
    Események hub kifogástalan állapotban az eredeti ![régióban](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Kattintson a Contoso Concert Hall bérlő, és nyissa meg az esemény oldalon.
    * Az élőlábban figyelje meg a bérlői kiszolgáló nevét. A hely megegyezik a katalóguskiszolgáló helyével.

3. Az [Azure Portalon](https://portal.azure.com)nyissa meg azt az erőforráscsoportot, amelyben az alkalmazás telepítve van
    * Figyelje meg, hogy a kiszolgálók melyik régióban vannak telepítve. 

## <a name="sync-tenant-configuration-into-catalog"></a>Bérlői konfiguráció szinkronizálása a katalógusban

Ebben a feladatban olyan folyamatot indít el, amely szinkronizálja a kiszolgálók, rugalmas készletek és adatbázisok konfigurációját a bérlői katalógusba. A folyamat naprakészen tartja ezeket az információkat a katalógusban.  A folyamat az aktív katalógussal működik, akár az eredeti régióban, akár a helyreállítási régióban. A konfigurációs adatokat a helyreállítási folyamat részeként használjuk fel annak biztosítására, hogy a helyreállítási környezet összhangban legyen az eredeti környezettel, majd később a hazaszállítás során, hogy az eredeti régió összhangban legyen a helyreállítási környezetben végrehajtott módosításokkal. helyreállítási környezetben. A katalógus a bérlői erőforrások helyreállítási állapotának nyomon követésére is használható.

> [!IMPORTANT]
> Az egyszerűség kedvéért a szinkronizálási folyamat és más hosszú ideig futó helyreállítási és hazatelepítési folyamatok ezekben az oktatóanyagokban helyi PowerShell-feladatokként vagy az ügyfélfelhasználói bejelentkezés alatt futó munkamenetekként valósulnak meg. A bejelentkezéskor kiadott hitelesítési tokenek néhány óra elteltével lejárnak, és a feladatok sikertelenek lesznek. Éles környezetben a hosszú ideig futó folyamatokat kell végrehajtani, mint a megbízható Azure-szolgáltatások valamilyen, egyszerű szolgáltatás alatt futó. Lásd: [Az Azure PowerShell használata egyszerű szolgáltatás létrehozása tanúsítvánnyal.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)

1. A _PowerShell ISE-ben_nyissa meg a ...\Learning Modules\UserConfig.psm1 fájlt. Cserélje `<resourcegroup>` `<user>` le és a 10- es és 11-es sorokat az alkalmazás telepítésekor használt értékkel.  Mentse a fájlt!

2. A *PowerShell ISE-ben*nyissa meg a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be:
    * **$DemoScenario = 1**, A bérlői kiszolgálót szinkronizáló háttérfeladat indítása és a készlet konfigurációs adatai a katalógusban

3. A szinkronizálási parancsfájl futtatásához nyomja le az **F5** billentyűt. Egy új PowerShell-munkamenet nyílik meg a bérlői erőforrások konfigurációjának szinkronizálásához.
![Szinkronizálási folyamat](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Hagyja a PowerShell ablakfut a háttérben, és folytassa a többi az oktatóanyag. 

> [!Note]
> A szinkronizálási folyamat DNS-aliason keresztül kapcsolódik a katalógushoz. Ez az alias a visszaállítás és a hazaszállítás során módosul, hogy az aktív katalógusra mutasson. A szinkronizálási folyamat a katalógust naprakészen tartja a helyreállítási régióban végrehajtott adatbázis- vagy készletkonfiguráció-módosításokkal.  A hazaszállítás során ezek a módosítások az eredeti régió egyenértékű erőforrásaira vonatkoznak.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Másodlagos adatbázis-replikák létrehozása a helyreállítási régióban

Ebben a feladatban elindít egy folyamatot, amely telepíti az ismétlődő alkalmazáspéldányt, és replikálja a katalógust és az összes bérlői adatbázist egy helyreállítási régióba.

> [!Note]
> Ez az oktatóanyag georeplikációs védelmet ad a Wingtip Tickets mintaalkalmazáshoz. A georeplikációt használó alkalmazások éles környezetében minden bérlő takarásban lesz egy georeplikált adatbázissal. Lásd: [Magas rendelkezésre állású szolgáltatások tervezése az Azure SQL Database használatával](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. A *PowerShell ISE-ben*nyissa meg a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következő értékeket:
    * **$DemoScenario = 2**, Tükörkép-helyreállítási környezet létrehozása és katalógus- és bérlői adatbázisok replikálása

2. A szkript futtatásához nyomja le az **F5** billentyűt. Egy új PowerShell-munkamenet nyílik meg a replikák létrehozásához.
![Szinkronizálási folyamat](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>A normál alkalmazásállapot áttekintése

Ezen a ponton az alkalmazás normálisan fut az eredeti régióban, és most georeplikáció védi.  Csak olvasható másodlagos replikák, léteznek a helyreállítási régióban az összes adatbázishoz. 

1. Az Azure Portalon tekintse meg az erőforráscsoportokat, és vegye figyelembe, hogy egy erőforráscsoport jött létre a -recovery utótag a helyreállítási régióban. 

2. Fedezze fel az erőforrásokat a helyreállítási erőforráscsoportban.  

3. Kattintson a Contoso Concert Hall adatbázisa a _tenants1-dpt-&lt;user&gt;-recovery_ kiszolgáló.  Kattintson a georeplikáció a bal oldalon. 

    ![Contoso Concert georeplikációs kapcsolata](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Az Azure-régiók leképezése, vegye figyelembe a georeplikációs kapcsolat az elsődleges az eredeti régióban, és a másodlagos a helyreállítási régióban.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Feladatátvétel az alkalmazás felett a helyreállítási régióba

### <a name="geo-replication-recovery-process-overview"></a>Georeplikációs helyreállítási folyamat – áttekintés

A helyreállítási parancsfájl a következő feladatokat hajtja végre:

1. Letiltja a Traffic Manager végpontot az eredeti régióban lévő webalkalmazáshoz. A végpont letiltása megakadályozza, hogy a felhasználók érvénytelen állapotban csatlakozzanak az alkalmazáshoz, ha az eredeti régió a helyreállítás során online állapotba kerül.

1. A helyreállítási régióban lévő katalógus-adatbázis feladatátvételt használ az elsődleges _activecatalog_ adatbázissá, és frissíti az activecatalog-aliast, hogy a helyreállítási katalógus-kiszolgálóra mutasson.

1. Frissíti az _újbérlői_ aliast, hogy a helyreállítási régióban lévő bérlői kiszolgálóra mutasson. Az alias módosítása biztosítja, hogy az új bérlők adatbázisai ki vannak építve a helyreállítási régióban. 

1. A helyreállítási katalógusban lévő összes meglévő bérlőt offline állapotúként jelöli meg, hogy megakadályozza a bérlői adatbázisokhoz való hozzáférést, mielőtt feladatátvételt rendelne el.

1. Frissíti az összes rugalmas készletek és replikált egyetlen adatbázisok konfigurációját a helyreállítási régióban, hogy tükrözze a konfigurációt az eredeti régióban. (Ez a feladat csak akkor szükséges, ha a helyreállítási környezetben lévő készletek vagy replikált adatbázisok a költségek csökkentése érdekében a normál műveletek során lesznek méretezve).

1. Engedélyezi a Traffic Manager-végpontot a webalkalmazáshoz a helyreállítási régióban. A végpont engedélyezése lehetővé teszi, hogy az alkalmazás új bérlők kiépítése. Ebben a szakaszban a meglévő bérlők továbbra is offline állapotban vannak.

1. Elküldi a kérelmek kötegeit az adatbázisok prioritási sorrendben való kényszerítésére.
    * A kötegek úgy vannak rendezve, hogy az adatbázisok az összes készletben párhuzamosan legyenek átvételek.
    * A feladatátvételi kérelmek aszinkron műveletekkel kerülnek elküldésre, így azok gyorsan beküldhetők, és egyszerre több kérelem is feldolgozható.

   > [!Note]
   > Kimaradás esetén az elsődleges adatbázisok az eredeti régióban offline állapotban vannak.  A másodlagos feladatátvétel kényszerítése megszakítja a kapcsolatot az elsődlegesvel anélkül, hogy megpróbálna alkalmazni a fennmaradó várólistás tranzakciókat. Egy VÉSZ-fúró forgatókönyv, mint ez az oktatóanyag, ha van olyan frissítési tevékenység a feladatátvétel időpontjában előfordulhat, hogy némi adatvesztés. Később a repatriálás során, amikor a helyreállítási régióban lévő adatbázisok at az eredeti régióban, a rendszer egy normál feladatátvételt használ annak biztosítására, hogy nincs adatvesztés.

1. Figyeli az SQL adatbázis-szolgáltatás annak megállapítására, hogy az adatbázisok feladatátvétele. Miután egy bérlői adatbázis feladatátvételt, frissíti a katalógust a bérlői adatbázis helyreállítási állapotának rögzítéséhez, és a bérlő online ként való megjelölése.
    * A bérlői adatbázisok at az alkalmazás is elérheti, amint online meg vannak jelölve a katalógusban.
    * A bérlői adatbázis ban lévő rowversion értékek összege a katalógusban tárolódik. Ez az érték ujjlenyomatként működik, amely lehetővé teszi a hazatelepítési folyamat számára annak megállapítását, hogy az adatbázis frissítve lett-e a helyreállítási régióban.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Futtassa a parancsfájlt a helyreállítási régióba való feladatátvételhez

Most képzeljük el, hogy van egy kimaradás a régióban, ahol az alkalmazás telepítve van, és futtassa a helyreállítási parancsfájlt:

1. A *PowerShell ISE-ben*nyissa meg a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következő értékeket:
    * **$DemoScenario = 3**, Az alkalmazás helyreállítása helyreállítási régióba a replikák rakoncátlika i.

2. A szkript futtatásához nyomja le az **F5** billentyűt.  
    * A parancsfájl megnyílik egy új PowerShell-ablakban, majd elindítja a párhuzamosan futó PowerShell-feladatok sorozatát. Ezek a feladatok feladatátvételi bérlői adatbázisok a helyreállítási régióba.
    * A helyreállítási régió az Azure-régióhoz társított _párosított régió,_ amelyben az alkalmazást telepítette. További információ: [Azure párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Figyelje a helyreállítási folyamat állapotát a PowerShell ablakban.
    ![feladatátvételi folyamat](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> A helyreállítási feladatok kódjának megismeréséhez tekintse át a PowerShell-parancsfájlokat a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs mappában.

### <a name="review-the-application-state-during-recovery"></a>Az alkalmazás állapotának áttekintése a helyreállítás során

Bár az alkalmazás végpontja le van tiltva a Traffic Managerben, az alkalmazás nem érhető el. Miután a katalógus feladatátvételt a helyreállítási régióban, és az összes bérlő megjelölt offline, az alkalmazás ismét online állapotba kerül. Bár az alkalmazás elérhető, minden bérlő offline állapotban jelenik meg az eseményközpontban, amíg az adatbázis feladatátvételig. Fontos, hogy az alkalmazás kapcsolat nélküli bérlői adatbázisok kezelésére tervezze meg.

1. A katalógus-adatbázis helyreállása után azonnal frissítse a Wingtip Tickets Events Hub-ot a webböngészőben.
   * Az élőlábban figyelje meg, hogy a katalóguskiszolgáló neve most már rendelkezik egy _-recovery_ utótaggal, és a helyreállítási régióban található.
   * Figyelje meg, hogy a bérlők, amelyek még nem állnak vissza, offline ként vannak megjelölve, és nem választhatók ki.  

     > [!Note]
     > Csak néhány adatbázis helyreállítása, előfordulhat, hogy nem tudja frissíteni a böngészőt, mielőtt a helyreállítás befejeződött, így előfordulhat, hogy nem látja a bérlők, amíg offline állapotban vannak. 
 
     ![Az Események központ offline módban](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Ha közvetlenül megnyit egy offline bérlő események lapját, az egy "bérlő offline" értesítést jelenít meg. Ha például a Contoso Koncertterem offline http://events.wingtip-dpt.&ltállapotban van, próbálja megnyitni a .user&gt;.trafficmanager.net/contosoconcerthall ![Contoso Offline lapot](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Új bérlő kiépítése a helyreállítási régióban
Még mielőtt az összes meglévő bérlői adatbázisok feladatátvétel, új bérlők kiépítése a helyreállítási régióban.  

1. A *PowerShell ISE-ben*nyissa meg a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következő tulajdonságot:
    * **$DemoScenario = 4**, Új bérlő kiépítése a helyreállítási régióban

2. Nyomja le **az F5** billentyűt a parancsfájl futtatásához és az új bérlő üzembe ágyazásához. 

3. A Hawthorn Hall események oldal megnyílik a böngészőben, amikor befejeződik. Vegye figyelembe a láblécből, hogy a Hawthorn Hall adatbázis ki van építve a helyreállítási régióban.
    ![Hawthorn Hall események oldal](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. A böngészőben frissítse a Wingtip Jegyek események hub lap megtekintéséhez Hawthorn Hall tartalmazza. 
    * Ha a Hawthorn Hall kiépítése megvárás a többi bérlő visszaállítása, más bérlők továbbra is offline állapotban lehet.


## <a name="review-the-recovered-state-of-the-application"></a>Az alkalmazás helyreállított állapotának áttekintése

Amikor a helyreállítási folyamat befejeződik, az alkalmazás és az összes bérlő teljes mértékben működőképes a helyreállítási régióban. 

1. Miután a PowerShell konzolablakban a kijelző azt jelzi, hogy az összes bérlő helyreállt, frissítse az Eseményközpontot.  A bérlők mind megjelennek az interneten, beleértve az új bérlőt, Hawthorn Hallt is.

    ![helyreállított és új bérlők az eseményközpontban](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Az [Azure Portalon](https://portal.azure.com)nyissa meg az erőforráscsoportok listáját.  
    * Figyelje meg a telepített erőforráscsoportot, valamint a helyreállítási erőforráscsoportot a _-recovery_ utótaggal.  A helyreállítási erőforráscsoport tartalmazza a helyreállítási folyamat során létrehozott összes erőforrást, valamint a kimaradás során létrehozott új erőforrásokat.  

3. Nyissa meg a helyreállítási erőforráscsoportot, és figyelje meg a következő elemeket:
   * A katalógus és a bérlők1 kiszolgálók helyreállítási verziói, _-recovery_ utótaggal.  A visszaállított katalógus és a bérlői adatbázisok ezeken a kiszolgálókon mind az eredeti régióban használt neveket.

   * A _&lt;tenants2-dpt-&gt;user -recovery_ SQL-kiszolgáló.  Ez a kiszolgáló új bérlők kiépítésére szolgál a szolgáltatáskimaradás során.
   * Az App Service neve, _events-wingtip-dpt-&lt;recoveryregion&gt;-&lt;user&gt_;, amely az Események alkalmazás helyreállítási példánya. 

     ![Azure helyreállítási erőforrások](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Nyissa meg a _tenants2-dpt-&lt;user&gt;-recovery_ SQL-kiszolgálót.  Figyelje meg, hogy tartalmazza az adatbázis _hawthornhall_ és a rugalmas készlet, _Pool1_.  A _hawthornhall-adatbázis_ rugalmas adatbázisként van konfigurálva az _1-es_ készlet rugalmas készletében.

5. Lépjen vissza az erőforráscsoporthoz, és kattintson a Contoso Concert Hall adatbázisára a _tenants1-dpt-&lt;user&gt;-recovery_ kiszolgálón. Kattintson a georeplikáció a bal oldalon.
    
    ![Contoso adatbázis feladatátvétel után](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Bérlői adatok módosítása 
Ebben a feladatban frissíti az egyik bérlői adatbázist. 

1. A böngészőben keresse meg a Contoso Koncertterem eseménylistáját, és jegyezze fel a vezetékesemény nevét.
2. A *PowerShell ISE*alkalmazásban a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlban állítsa be a következő értéket:
    * **$DemoScenario = 5** Esemény törlése egy bérlőből a helyreállítási régióban
3. Nyomja **le az F5 billentyűt** a parancsfájl végrehajtásához
4. Frissítse a Contoso Concerthttp://events.wingtip-dpt.&ltHall&gt;események lap &lt;(&gt; ;user .trafficmanager.net/contosoconcerthall - helyettesítő felhasználó a központi telepítés felhasználói érték), és figyelje meg, hogy az utolsó esemény törölve lett.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Az alkalmazás az eredeti termelési régióba történő hazaszállítása

Ez a feladat repatriates az alkalmazás az eredeti régióba. Egy valós esetben akkor kezdeményezheti a hazaszállítást, amikor a kimaradás megoldódik.

### <a name="repatriation-process-overview"></a>A hazatelepítési folyamat áttekintése

![Hazatelepítési architektúra](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

A hazatelepítési folyamat:
1. Törli a függőben lévő vagy a repülés közbeni adatbázis-visszaállítási kérelmeket.
2. Frissíti az _újbérlői_ aliast, hogy a bérlői kiszolgáló az eredeti régióban mutasson. Az alias módosítása biztosítja, hogy az új bérlők adatbázisai mostantól a származási régióban lesznek kiépítve.
3. Minden megváltozott bérlői adatot az eredeti régióba magolni
4. A bérlői adatbázisok prioritási sorrendben való átadása.

A feladatátvétel hatékonyan áthelyezi az adatbázist az eredeti régióba. Amikor az adatbázis átadja a feladatait, a megnyitott kapcsolatok megszakadnak, és az adatbázis néhány másodpercig nem érhető el. Az alkalmazásokat újrapróbálkozási logikával kell írni, hogy megbizonyosodjon arról, hogy újra csatlakoznak.  Bár ez a rövid kapcsolat bontása gyakran nem veszik észre, dönthet úgy, hogy az adatbázisokat munkaidőn kívül relíti. 


### <a name="run-the-repatriation-script"></a>A hazatelepítési parancsfájl futtatása
Most képzeljük el, hogy a leállás megoldódott, és futtassuk a hazatelepítési parancsfájlt.

1. A *PowerShell ISE*, a ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlban.

2. Ellenőrizze, hogy a katalógusszinkronizálási folyamat továbbra is fut-e a PowerShell-példányban.  Szükség esetén indítsa újra a következő beállítással:
    * **$DemoScenario = 1**, A bérlői kiszolgáló, a készlet és az adatbázis konfigurációs adatainak szinkronizálásának megkezdése a katalógusban
    * A szkript futtatásához nyomja le az **F5** billentyűt.

3.  Ezután indítsa el a hazatelepítési folyamatot, állítsa be:
    * **$DemoScenario = 6**, Az alkalmazás hazatelepítése az eredeti régióba
    * Nyomja le **az F5** billentyűt a helyreállítási parancsfájl futtatásához egy új PowerShell-ablakban.  A hazatelepítés több percet vesz igénybe, és a PowerShell ablakban figyelhető.
    ![Hazatelepítési folyamat](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. A parancsfájl futása közben frissítse azhttp://events.wingtip-dpt.&ltEseményközpont lapot ( ;user&gt;.trafficmanager.net)
    * Figyelje meg, hogy az összes bérlő online és elérhető a folyamat során.

5. A hazaszállítás befejezése után frissítse az Események központot, és nyissa meg a Hawthorn Hall eseményoldalát. Figyelje meg, hogy az adatbázis tanusította az eredeti régióba.
    ![Események központ hazatelepített](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Az alkalmazás tervezése annak biztosítására, hogy az alkalmazás és az adatbázis közös helyen található 
Az alkalmazás úgy van kialakítva, hogy mindig egy példányból csatlakozzon ugyanabban a régióban, mint a bérlői adatbázis. Ez a kialakítás csökkenti az alkalmazás és az adatbázis közötti késést. Ez az optimalizálás feltételezi, hogy az alkalmazás-adatbázis interakció chattier, mint a felhasználó és az alkalmazás közötti interakció.  

A bérlői adatbázisok a helyreállítás és az eredeti régiók között elosztva lehetnek egy ideig a hazaszállítás során. Az alkalmazás minden egyes adatbázis esetében megkeresi azt a régiót, amelyben az adatbázis található, a bérlői kiszolgáló nevének DNS-keresése alapján. Az SQL Database rendszerben a kiszolgáló neve alias. Az aliasos kiszolgálónév tartalmazza a régió nevét. Ha az alkalmazás nem ugyanabban a régióban, mint az adatbázis, átirányítja a példány ugyanabban a régióban, mint az adatbázis-kiszolgáló.  Átirányítás a példány ugyanabban a régióban, mint az adatbázis minimáliskéssze az alkalmazás és az adatbázis közötti késés. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> 
> * Adatbázis és rugalmas készlet konfigurációs adatainak szinkronizálása a bérlői katalógusban
> * Helyreállítási környezet beállítása egy alternatív régióban, amely alkalmazásból, kiszolgálókból és készletekből áll
> * A katalógus- és bérlői adatbázisok replikálása _georeplikációval_ a helyreállítási régióba
> * Feladatátvétel az alkalmazás- és katalógus- és bérlői adatbázisok felett a helyreállítási régióban 
> * Az alkalmazás-, katalógus- és bérlői adatbázisok visszavétele az eredeti régióba a kimaradás feloldása után

Az Azure SQL-adatbázis által az üzletmenet-folytonosság ot lehetővé tevő technológiákról az [Üzletmenet-folytonosság áttekintése](sql-database-business-continuity.md) dokumentációban olvashat bővebben.

## <a name="additional-resources"></a>További források

* [További oktatóanyagok, amelyek a Wingtip SaaS alkalmazásra épülnek](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
