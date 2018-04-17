---
title: SaaS-alkalmazásokhoz az Azure SQL Database georedundáns biztonsági másolatokból vészhelyreállítás |} Microsoft Docs
description: Ismerje meg, hogyan használható az Azure SQL Database georedundáns biztonsági mentések helyreállítani egy több-bérlős SaaS-alkalmazás nem tervezett kimaradás esetén
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/09/2018
ms.author: ayolubek
ms.openlocfilehash: 928ff9b2519f6e92b091d9fd6b4ea0a7bca94d2b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="recover-a-multi-tenant-saas-application-using-geo-restore-from-database-backups"></a>Helyreállítani egy több-bérlős SaaS-alkalmazás használatával georedundáns helyreállítás az adatbázis biztonsági mentése

Ebben az oktatóanyagban egy több-bérlős SaaS-alkalmazáshoz, az adatbázis-/-bérlős modell használatával végrehajtott a teljes vész-helyreállítási forgatókönyv vizsgálatát. Használhat [ _georedundáns helyreállítás_ ](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-recovery-using-backups) is helyreállíthatják az alkalmazáskatalógus és a bérlői adatbázisokat automatikusan karbantartott georedundáns biztonsági másolatból egy másik helyreállítási régióba. A leállás nem oldódik meg, miután az [ _georeplikáció_ ](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview) a módosított adatbázisokat az eredeti régió települni.

![földrajzi-visszaállítási-architektúra](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Georedundáns helyreállítás az SQL-adatbázis a legkisebb költségű vész-helyreállítási megoldást.  Azonban visszaállítás, georedundáns biztonsági másolatból, akár egy óráig adatvesztést eredményezhet, és attól függően, hogy az egyes adatbázisok méretétől, jelentős ideig eltarthat. **A legkisebb lehetséges RPO és RTO alkalmazások helyreállításához használni georeplikáció helyett georedundáns helyreállítás**.

Ez az oktatóanyag ismerteti, visszaállítás és a hazatelepítési munkafolyamatok. Az alábbiak végrehajtásának módját ismerheti meg:
> [!div class="checklist"]

>* Sync-adatbázis és a bérlői katalógusba rugalmas konfigurációs adatait
>* A tükrözött lemezképét környezet "helyreállítási" régióban, alkalmazás, a kiszolgálók és a készletek beállítása    
>* Katalógus és a bérlői adatbázisok használatával _georedundáns helyreállítás_
>* A bérlői katalógus és a módosított tenant adatbázis települni _georeplikáció_ megoldódott a szolgáltatáskimaradás elhárítása után
>* A katalógus frissíti, az egyes adatbázisok visszaállítása (vagy fogalommeghatározás) nyomon követésére az aktív példány mindegyik bérlő adatbázis jelenlegi helye
>* Győződjön meg arról, az alkalmazás és a bérlői adatbázis közös mindig találhatók a késés csökkentése érdekében az azonos Azure-régió  
 

Az oktatóanyag elindítása előtt győződjön meg arról, hogy befejeződött a következő előfeltételek teljesülését:
* A bérlői alkalmazásonkénti Wingtip jegyek Szolgáltatottszoftver-adatbázis telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a bérlői alkalmazásonként Wingtip jegyek Szolgáltatottszoftver-adatbázis](saas-dbpertenant-get-started-deploy.md)  
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>A georedundáns helyreállítás helyreállítási mintát bemutatása

Vészhelyreállítás (DR) számos alkalmazás fontos szempont a megfelelőségi okokból vagy az üzletmenet folytonossága. Kell egy hosszan tartó szolgáltatáskimaradás, egy jól felkészültnek vész-Helyreállítási terv minimálisra üzleti problémákat. A vész-Helyreállítási terv georedundáns helyreállítás alapján kell elérheti, ha több célok:
 * Tartalékkapacitás valamennyi szükséges a kiválasztott helyreállítási régióban lehető leggyorsabban szeretné bérlői adatbázisok visszaállítása érdekében.
 * Egy tükrözött lemezképét helyreállítási környezetre, amely tükrözi az eredeti készlet és adatbázis-konfiguráció létrehozása 
 * A visszaállítási folyamat közepes útban megszüntetése, ha az eredeti régió ismét online állapotú lesz előre kell lennie.
 * Engedélyezze a bérlő kiépítésének gyorsan, a lehető leghamarabb indíthatja újra új bérlő bevezetése  
 * Lesz optimalizálva a bérlők prioritási sorrendben visszaállítása
 * Bérlők online minél hamarabb első párhuzamosan lépések végrehajtásával, ha gyakorlati optimalizálni
 * Hiba, újraindítható, és az idempotent rugalmasak lehetnek
 * Települni adatbázisokat az eredeti régió gyakorolt minimális hatás mellett a bérlők számára, amikor a szolgáltatáskimaradás elhárítása.  

> [!Note]
> Az alkalmazás a állítja helyre a rendszer a _párosított régió_ a régió, ahol az alkalmazás központi telepítése. További információkért lásd: [Azure párosítva régiók](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).   



Ebben az oktatóanyagban ezekkel a kihívásokkal foglalkozik az Azure SQL Database és az Azure platform olyan szolgáltatását használja:

* [Az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), minden szükséges kapacitást minél gyorsabban lefoglalni. Azure Resource Manager-sablonok segítségével az eredeti kiszolgálók és a helyreállítási régióban rugalmas készletek szerinti kiépítéséhez. Egy önálló kiszolgáló és a készlet is létre új bérlők számára történő üzembe helyezéséhez. 
* [A rugalmas adatbázis ügyféloldali kódtár](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-database-client-library) hozhatja létre és kezelheti a bérlő adatbázis katalógus (EDCL).  A katalógus rendszeresen frissíteni címkészletet és az adatbázis-konfigurációs adatokat terjeszteni.
* [A shard helyreállítási funkciók](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) az adatbázis helyek listáját a katalógus fenntartásához helyreállítási és hazatelepítési során EDCL.  
* [Georedundáns helyreállítás](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), az alkalmazáskatalógus és a bérlői adatbázisok helyreállításához automatikusan karbantartott georedundáns biztonsági másolatból. 
* [Aszinkron visszaállítási műveletek](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) küldött bérlői-prioritási sorrendben szerepel, amelyeket a rendszer mindegyik készlet várakozó és feldolgozta kötegekben, így az alkalmazáskészlet nem túl van terhelve. Ezek a műveletek előtt vagy a során szükség lehet visszavonni.    
* [A georeplikáció](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), az adatbázisok eredeti régió települni a szolgáltatáskimaradás elhárítása után. A georeplikáció biztosítja, nem az adatvesztéssel és a bérlői minimális hatással van.
* [SQL server DNS-alias](https://docs.microsoft.com/azure/sql-database/dns-alias-overview) engedélyezi a katalógus szinkronizálási folyamat függetlenül az, hogy az aktív katalógusban való kapcsolódáshoz.  

## <a name="get-the-disaster-recovery--scripts"></a>A vész-helyreállítási parancsfájlok beolvasása 

A jelen oktatóanyagban használt vész-Helyreállítási parancsfájlok érhetők el a [Wingtip jegyek SaaS adatbázis GitHub-tárházban bérlőnként](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/tree/feature-DR-georestore). Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek felügyeleti parancsfájlok lépéseit.
> [!IMPORTANT]
> Minden a Wingtip jegyek felügyeleti parancsfájlok, például a vész-Helyreállítási parancsfájlok minta minőségét és a rendszer nem éles környezetben használja.   

## <a name="review-the-healthy-state-of-the-application"></a>Az alkalmazás kifogástalan állapotának áttekintése
A helyreállítási folyamat megkezdése előtt tekintse át az alkalmazás normál kifogástalan állapotát.
1. A böngészőben nyissa meg a Wingtip jegyek események Hub (http://events.wingtip-dpt.&lt; felhasználói&gt;. trafficmanager.net - csere &lt;felhasználói&gt; a központi telepítés felhasználói értékű).
    * Görgessen a lap alján, és figyelje meg, a katalógus kiszolgáló nevét és helyét, a láblécben.  A hely a régiót, amelyben az alkalmazás telepítve.    
        Tipp: az egér megjelenítésének nagyítása a helyre mutasson.

    ![Események hub állapota kifogástalan eredeti régióban](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

1. A Contoso energiaoptimalizálást egyszerre Hall bérlő parancsára, és nyissa meg az esemény lapját.
    * A lábrészben megjelenő figyelje meg, hogy a bérlők kiszolgáló nevét. A hely ugyanaz, mint a katalógus kiszolgálójától helye lesz.

    ![Contoso energiaoptimalizálást egyszerre Hall eredeti régió](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)    
1. Az a [Azure-portálon](https://portal.azure.com), tekintse át, és nyissa meg az erőforráscsoportot, amelyben az alkalmazás telepítése.
    * Figyelje meg, az erőforrások és a régiót, amelyben az app service-összetevők és az SQL Database-kiszolgálók vannak telepítve.

## <a name="sync-tenant-configuration-into-catalog"></a>Szinkronizálási bérlő konfigurációjához-katalógusba

Ebben a feladatban olyan folyamatot, amely a kiszolgálók, a rugalmas készletek és az adatbázisok konfigurációját a bérlő katalógusba való szinkronizálás elindításához.  Ez az információ később egy tükrözött lemezképét környezet konfigurálása a helyreállítási régióban szolgál.

> [!IMPORTANT]
> Az egyszerűség kedvéért a szinkronizálási folyamat és a más helyreállítási és hazatelepítési hosszú ideig futó folyamatok vannak megvalósítva ezeket a mintákat a helyi Powershell feladatok vagy az ügyfél felhasználói bejelentkezés alatt futó munkameneteket. A hitelesítési tokenek ki, ha több óra elteltével lejár, a bejelentkezési és a feladat majd sikertelen lesz. Egy éles telepítési forgatókönyvhöz, a hosszú futású folyamatokat valamilyen, egyszerű szolgáltatás alatt futó megbízható Azure szolgáltatásként kell végrehajtani. Lásd: [a Azure PowerShell szolgáltatás használatával hozzon létre egy egyszerű tanúsítvány](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. Az a _PowerShell ISE_, nyissa meg a ...\Learning Modules\UserConfig.psm1 fájlt. Cserélje le `<resourcegroup>` és `<user>` 10-es és 11 az értéket, az alkalmazás üzembe helyezésekor a sorban.  Mentse a fájlt!

2. Az a *PowerShell ISE*, nyissa meg a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl.
    *  Ez az oktatóanyag során minden egyes futtasson, milyen alkalmazási helyzetek a PowerShell parancsfájl, úgyhogy tartsa a fájl megnyitása.

3. Állítsa be a következőt:
    * **$DemoScenario = 1**bérlői server Szinkronizáló háttér feladat indítása és tárolókészlet konfigurációs adatait a katalógusba

3. Nyomja le az **F5** a szinkronizálási parancsfájl futtatásához. 
    *  Ez az információ később annak biztosítására szolgál, hogy helyreállítási lemezképet hoz létre tükrözött a kiszolgálók, a készletek és az adatbázisok helyreállítási régióban.  
![Szinkronizálási folyamat](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Hagyja meg a PowerShell ablakban fut a háttérben, és a többi Ez az oktatóanyag folytatásához.

> [!Note]
> A szinkronizálási folyamat a katalógus DNS-alias keresztül kapcsolódik. Az alias visszaállítása és az aktív katalógus mutasson hazatelepítési során is módosul. A szinkronizálási folyamat tartja a katalógus naprakész az adatbázis vagy a tárolókészlet konfigurációs módosítások helyreállítási régióban.  A hazatelepülés során ezeket a módosításokat az eredeti régióban egyenrangú erőforrásokkal is vonatkozik.

## <a name="geo-restore-recovery-process-overview"></a>Georedundáns helyreállítás helyreállítási folyamat áttekintése

A georedundáns helyreállítás helyreállítási folyamat központilag telepíti az alkalmazást, és visszaállítja az adatbázisok biztonsági másolatból a helyreállítási régióba.

A helyreállítási folyamat a következőket teszi:

1. Letiltja a Traffic Manager-végpont a webalkalmazás az eredeti régióban. A végpont letiltása megakadályozza, hogy a felhasználók csatlakozni az alkalmazás állapota érvénytelen az eredeti régió érkezik online helyreállítás során.

1. A helyreállítási régióban, földrajzi-visszaállítások egy helyreállítási katalógus kiszolgálójától a katalógus-adatbázis kiépítését, és frissíti a _activecatalog_ alias, hogy a visszaállított kiszolgáló mutasson.  
    * A katalógus alias módosítása biztosítja a katalógus szinkronizálási folyamat mindig szinkronizálja a aktív katalógust.

1. Az összes meglévő bérlők számára a helyreállítási katalógus offline állapotúként kívánja tagadni a hozzáférést bérlői adatbázisokhoz, mielőtt vissza jelöli.

1. Az alkalmazás a helyreállítási régióban példányának kiépítését, és konfigurálja a visszaállított katalógus használatát az adott régióban.
    * Minimális késés megtartásához mintaalkalmazás célja, hogy mindig kapcsolódik egy bérlő adatbázis ugyanabban a régióban.

1. A kiszolgáló és a rugalmas készletben, amelyben az új bérlők kiépítendő látja el. Ezeket az erőforrásokat létrehozása biztosítja, hogy új bérlők kiépítése nem zavarja a visszaállítást meglévő bérlők számára.

1. Frissíti az új tenanthoz alias a kiszolgálót az új bérlő adatbázisokat a helyreállítási régióban. Ez az alias módosítása biztosítja, hogy az új tenantokat adatbázisok helyreállítási régióban törlődnek.
        
1. Kiszolgálók és a bérlői adatbázisok helyreállításához a helyreállítási régióban rugalmas készletek kiépítését. A kiszolgálók és a készletek lesznek a konfiguráció az eredeti régióban.  Az összes adatbázis visszaállításához szükséges kapacitás kezdeti készletek kiépítés foglalja le.
    * Nem tervezett kimaradás régióban jelentős terhelés helyezhet párosított régióban elérhető erőforrások.  Ha használ, georedundáns helyreállítás Dr, majd gyorsan az erőforrások lefoglalása ajánlott. Érdemes georeplikáció, ha nagyon fontos, hogy egy alkalmazás helyre kell állítani az adott régióban található. 

1. Lehetővé teszi, hogy a webalkalmazás a helyreállítási régióban Traffic Manager-végpontot. Ez a végpont lehetővé teszi, hogy az alkalmazás telepítéséhez az új bérlők számára. Ebben a szakaszban a meglévő bérlőkre is offline állapotú.

1. Adatbázisok prioritási sorrendben visszaállítására irányuló kérelmekre kötegekben küldi el. 
    * Kötegek vannak rendezve, hogy az adatbázisok visszaállítása párhuzamosan készletekben között.  
    * Visszaállítás kéri, hogy gyorsan be, és mindegyik készlet végrehajtás várakozó aszinkron módon elküldi.
    * Visszaállítási kérelmek feldolgozása párhuzamosan történik készletekben keresztül, mert már fontos bérlők szét sok készletek. 

1. Annak meghatározásához, amikor adatbázisok visszaállítása az SQL adatbázis-szolgáltatás figyeli. Egy bérlő adatbázis helyreállítása után a katalógus online van megjelölve, és a bérlői adatbázis rowversion összege rögzítése. 
    * Bérlői adatbázisok hozzáférhet az alkalmazáshoz, amint online, a katalógus van megjelölve.
    * A tenant adatbázisban rowversion értékek összegét a katalógus tárolja. Ezt az összeget, amely lehetővé teszi a hazatelepítési folyamat határozza meg, ha az adatbázis frissítése a helyreállítási régióban ujjlenyomattal funkcionál.      

## <a name="run-the-recovery-script"></a>A helyreállítási parancsprogrammal

> [!IMPORTANT]
> Ez az oktatóanyag adatbázisok visszaállítása georedundáns biztonsági másolatból. Annak ellenére, hogy ezek a biztonsági másolatok általában 10 percen belül, azt órát is igénybe vehet egy előtt. A parancsfájl felfüggeszti, amíg ilyenek.  A kávé beolvasandó idő!

Most tegyük fel, a régióban, amelyben az alkalmazás üzemel, és a helyreállítási parancsprogrammal kimaradás van:

1. Az a *PowerShell ISE*, Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl ...\Learning állítson be a következő értékeket:
    * **$DemoScenario = 2**, az alkalmazás helyreállítását olyan helyreállítási régió georedundáns biztonsági másolatból állítja vissza

1. A szkriptek futtatásához nyomja le az **F5** billentyűt.  
    * A parancsfájl egy új PowerShell-ablakban nyílik meg, és elindítja a párhuzamosan futó feladatok PowerShell készlete.  Ezeket a feladatokat a helyreállítási régióban kiszolgálók, a készletek és az adatbázisok visszaállítása. 
    * A helyreállítási régió a _párosított régió_ társított Azure-régiót, amelyben az alkalmazás telepítése. További információkért lásd: [Azure párosítva régiók](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions). 

1. A PowerShell-ablakban a helyreállítási folyamat állapotának figyelése.

    ![A helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

>A helyreállítási feladatok kódja megismeréséhez tekintse át a PowerShell-parancsfájlok a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\RecoveryJobs mappában.

## <a name="review-the-application-state-during-recovery"></a>A helyreállítás során állapotának áttekintése
Az alkalmazás végpont le van tiltva a Traffic Managerben, amíg az alkalmazás nem érhető el. A katalógus visszaállítása, és a bérlők offline állapotban van megjelölve.  Az alkalmazás végpontjának helyreállítási régióban majd engedélyezve van, és az alkalmazást újra online állapotba kerül. Bár az alkalmazás nem érhető el, bérlők offline állapotúnak fogja látni az események központban hozzájuk tartozó adatbázisok visszaállításáig. Fontos alkalmazás offline bérlői adatbázisok kezelésének megtervezése.

1. A katalógus-adatbázis helyreállított de előtt a bérlők ismét online állapotban, frissítse a Wingtip jegyek események Hub a böngészőben.
    * A lábrészben megjelenő figyelje meg, hogy rendelkezik-e már a kiszolgáló katalógusnév egy _-helyreállítási_ utótag és a helyreállítási régióban található.
    * Figyelje meg, hogy a bérlők számára, amelyek nem lettek visszaállítva offline állapotban van megjelölve, és nincsenek választható.   
 
    ![A helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Ha megnyit egy bérlő események lapot közvetlenül a bérlő kapcsolat nélküli módban, a lap egy "offline bérlői" értesítést jelenít meg. Például a Contoso energiaoptimalizálást egyszerre Hall offline állapotban, ha megpróbálja megnyitni http://events.wingtip-dpt.&lt; felhasználói&gt;.trafficmanager.net/contosoconcerthall ![helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Egy új bérlőt a helyreállítási régióban kiépítése
Még a bérlő adatbázisok visszaállítása, mielőtt új bérlők a régióban helyreállítási oszthat meg. Új bérlő adatbázisok helyreállítási régióban üzembe lesz kell fogalommeghatározás a helyreállított adatbázisokkal később.   

1. Az a *PowerShell ISE*, Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl ...\Learning állítson be a következő tulajdonság:
    * **$DemoScenario = 3**, egy új bérlőt a helyreállítási régióban kiépítése

1. A szkriptek futtatásához nyomja le az **F5** billentyűt. 

1. A böngészőben megnyílik a Hawthorn Hall események lap, kiépítés befejezéséről. 
    * Figyelje meg, hogy a helyreállítási régióban található, a Hawthorn Hall adatbázis.
    ![A helyreállítási régióban üzembe hawthorn Hall](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

1. A böngészőben frissítse a Wingtip jegyek események Hub lapot, melyen megtekintheti, Hawthorn Hall tartalmazza. 
    * Kiépített Hawthorn Hall visszaállítása a többi bérlő várakozás nélkül, ha más bérlők előfordulhat, hogy még mindig offline állapotban van.

## <a name="review-the-recovered-state-of-the-application"></a>Tekintse át a helyreállított az alkalmazás állapota

A helyreállítási folyamat befejezése után az alkalmazás és minden bérlők teljesen működőképes helyreállítási területen. 

1. A PowerShell-konzolablakot megjelenítését azt jelzi, hogy a bérlők helyreállítás, miután az események Hub frissítése  A bérlők összes jelenik meg, beleértve az új tenanthoz Hawthorn Hall online.

    ![az események központban helyreállított és az új bérlők](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

1. Kattintson a Contoso energiaoptimalizálást egyszerre Hall, és nyissa meg az események lapról.
    * A lábrészben megjelenő figyelje meg, az adatbázist a helyreállítási régióban található, a helyreállítási kiszolgálón található.

    ![A helyreállítási régióban contoso](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

1. Az a [Azure-portálon](https://portal.azure.com), az erőforráscsoport-sablonok listájának megnyitásához.  
    * Figyelje meg a központilag telepített, a helyreállítási erőforráscsoport, valamint az erőforráscsoportot a _-helyreállítási_ utótag.  A helyreállítási erőforráscsoport összes a helyreállítási folyamat során létrejött erőforrásokat, valamint a szolgáltatáskimaradás elhárítása során létrehozott új erőforrásokat tartalmazza.  

1. Nyissa meg a helyreállítási erőforráscsoportot, és figyelje meg a következő elemek:
    * A katalógus és tenants1 kiszolgálók helyreállítási verziói a _-helyreállítási_ utótag.  A visszaállított katalógus és a bérlői adatbázisok ezeken a kiszolgálókon minden eredeti régióban használt nevekkel rendelkeznek.

    * A _tenants2-dpt -&lt;felhasználói&gt;-helyreállítási_ SQL server.  Ezt a kiszolgálót az új bérlők lépnek a szolgáltatáskimaradás elhárítása során használja.
    *   Az App Service nevű, _események-wingtip-dpt -&lt;recoveryregion&gt;-&lt;felhasználó & gt_; amely az események alkalmazás helyreállítási példányát. 

    ![A helyreállítási régióban contoso](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png)   
    
1. Nyissa meg a _tenants2-dpt -&lt;felhasználói&gt;-helyreállítási_ SQL server.  Figyelje meg az adatbázis tartalmaz _hawthornhall_ és a rugalmas készlet _Pool1_.  A _hawthornhall_ egy olyan rugalmas adatbázis az adatbázis lesz beállítva _Pool1_ rugalmas készlet.

## <a name="change-tenant-data"></a>Bérlői adatok módosítása 
Ebben a feladatban frissíti egy visszaállított bérlői adatbázis. A hazatelepítési folyamat másolatot készít a visszaállított adatbázis eredeti régió megváltozott. 

1. A böngészőben, a Keresés az események listájában a Contoso energiaoptimalizálást egyszerre Hall, görgessen végig az eseményeket, és jegyezze fel az utolsó esemény _súlyosan Strauss_.

1. Az a *PowerShell ISE*, Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl ...\Learning állítson be a következő értéket:
    * **$DemoScenario = 4**, a helyreállítási régióban bérlőtől esemény törlése
1. Nyomja le az **F5** a parancsfájl végrehajtása.
1. Frissítse a Contoso energiaoptimalizálást egyszerre Hall események lapot (http://events.wingtip-dpt.&lt; felhasználói&gt;.trafficmanager.net/contosoconcerthall), és figyelje meg, hogy az esemény súlyosan Strauss hiányzik.

Ezen a ponton az oktatóanyagban helyreállította az alkalmazásról, így most már fut a helyreállítási régióban.  Egy új bérlőt a helyreállítási régióban kiosztása és módosította az egyik a visszaállított bérlők adatokat.  

> [!Note]
> Egyéb oktatóanyagok minta nem tervezték, és a helyreállítási állapotban az alkalmazás futtatásához. Ha szeretné használni a többi oktatóanyag, ügyeljen arra, hogy az alkalmazás első települni.

## <a name="repatriation-process-overview"></a>Hazatelepítési folyamat áttekintése

A hazatelepítési folyamat visszaállítja az alkalmazás és az adatbázisok eredeti régiója, egy a szolgáltatáskimaradás elhárítása után.

![Georedundáns helyreállítás hazatelepítési](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 


A folyamat:

1. Minden folyamatban lévő visszaállítási tevékenység leáll, és megszakítja a függőben lévő vagy üzenetsoroktól adatbázis visszaállítási kéréseit.

1. Újraaktiválja a eredeti régió bérlői adatbázisokban, amely a szolgáltatáskimaradás óta nem módosult.  Ez magában foglalja, amely rendelkezik még nincs helyreállítva, és adatbázisait helyre lett állítva, de később nem módosultak. Az újraaktivált adatbázisok megegyeznek a legutóbbi által elért bérlőiknek.

1. Az új bérlők kiszolgáló és a rugalmas készlet az eredeti régió szerinti látja el. Ez a művelet befejezése után az új tenanthoz alias frissítése erre a kiszolgálóra mutasson. Az alias frissítése hatására új bérlő bevezetési fordulnak elő az eredeti régió helyett a helyreállítási régió.

1. Az eredeti régió-georeplikációt használ, áthelyezi a katalógus helyreállítási régióban.

1. Frissíti az eredeti régió tárolókészlet konfigurációját, hogy konzisztens helyreállítási régióban a szolgáltatáskimaradás elhárítása során végrehajtott módosításokat.

1. A szükséges kiszolgálók és üzemeltetéséhez a szolgáltatáskimaradás elhárítása során létrehozott új adatbázisokat vagy tárolókészleteit hoz létre.

1. Georeplikációt használ, repatriates visszaállítása frissített adatbázisok visszaállítás utáni bérlői, és minden új bérlő adatbázisok kiépítése során a szolgáltatáskimaradás. 

1. A visszaállítás során a helyreállítási régióban létrehozott erőforrások a szükségtelenné vált.

Korlátozni kell fogalommeghatározás igénylő bérlői adatbázisok száma, 1 – 3. lépést végzett azonnal.  

4. lépés csak akkor történik, ha a helyreállítási régióban katalógus módosítva lett a szolgáltatáskimaradás elhárítása során. A katalógus frissül, ha az új bérlők jöttek létre, vagy ha bármely adatbázis vagy a tárolókészlet konfigurációs megváltozik, a helyreállítási régióban.

Fontos, hogy 7. lépés minimális megszakítás hatására a bérlők és az adatok nem vesztek el. A folyamat használja e cél eléréséhez _georeplikáció_.

Mielőtt az egyes adatbázisok georeplikált, a megfelelő adatbázist az eredeti régióban törlődik. A helyreállítási régióban adatbázisa majd georeplikált, egy másodlagos másodpéldány létrehozása az eredeti régióban. Replikáció befejezése után a bérlői offline a katalógus, amely a kapcsolatokat, az adatbázis helyreállítási régióban van megjelölve. Az adatbázis majd végrehajtott feladatátvételt, minden függőben lévő adatok nem vesztek el, a másodlagos feldolgozandó tranzakciók okozza. Feladatátvétel esetén az adatbázis-szerepkörök fordított irányú.  A másodlagos eredeti régióban lesz az elsődleges adatbázis írható-olvasható, az adatbázis helyreállítási régióban pedig egy írásvédett másodlagos. A bérlői bejegyzést a katalógusban való hivatkozáshoz az adatbázist az eredeti régióban frissül, és a bérlői online van megjelölve.  Ezen a ponton az adatbázis hazatelepítési befejeződött. 

Újrapróbálkozási logika ahhoz, azok újra automatikusan amikor a kapcsolat nem működik az alkalmazások kell írni.  A katalógus használata a kapcsolat replikaszervező újracsatlakozáskor, csatlakoznak a repatriated adatbázis eredeti régióban. Bár a rövid bontása gyakran nem első fellépése, választhatja munkaidőn kívülre adatbázisok települni. 

Egy adatbázis fogalommeghatározás van, a másodlagos adatbázist a helyreállítási régióban törölhetők. Az adatbázis az eredeti régióban majd támaszkodik a vész-Helyreállítási védelem újra georedundáns helyreállítás.

8. lépésben a helyreállítási régióban, így a helyreállítási és -készletek, erőforrások törlése.

## <a name="run-the-repatriation-script"></a>A hazatelepítési parancsprogrammal
Most tegyük képzelhető el, a kimaradást okozó megoldása és a hazatelepítési parancsprogrammal.

Ha már elvégezte az oktatóanyag, a a parancsfájl azonnali újraaktiválja Fabrikam Jazz Club és az eredeti régióban somfát Dojo annak mert változatlan. Majd repatriates az új tenanthoz Hawthorn Hall, és a Contoso energiaoptimalizálást egyszerre Hall, mert lett módosítva. A parancsfájl is repatriates a katalógus, amely frissült, amikor Hawthorn Hall lett kiépítve.
  
1. Az a *PowerShell ISE*, a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl.

1. Győződjön meg arról, hogy a katalógus szinkronizálási folyamat továbbra is fut a PowerShell-példányban.  Ha szükséges, indítsa újra úgy, hogy:
    * **$DemoScenario = 1**, bérlői server, a készlet és az adatbázis konfigurációs adatainak szinkronizálása a katalógusba való indítása
    * A szkriptek futtatásához nyomja le az **F5** billentyűt.
1.  Ezután a hazatelepítési megkezdéséhez beállítása:
    * **$DemoScenario = 5**, az alkalmazás települni az eredeti régióba
    * Nyomja le az **F5** egy új PowerShell-ablakban a helyreállítási parancsfájl futtatásához.  Hazatelepítési néhány percet vesz igénybe, és figyelhetők a PowerShell-ablakban.
1. A parancsfájl futása közben, az események Hub lap frissítése (http://events.wingtip-dpt.&lt; felhasználói&gt;. trafficmanager.net)
    * Figyelje meg, hogy a bérlők online állapotúak és elérhetők a folyamat során.
1. Kattintson a Fabrikam Jazz Club való megnyitásához. Ha nem módosította ezt a bérlőt, figyelje meg, a láblécben a, hogy a kiszolgáló már tért vissza az eredeti kiszolgálón.
1. Nyissa meg, vagy frissítse a Contoso energiaoptimalizálást egyszerre Hall események lapot, és láthatja a lábléc az, hogy az adatbázis továbbra is a _-helyreállítási_ server kezdetben.  
1. A Contoso energiaoptimalizálást egyszerre Hall események lap frissítése a hazatelepítési folyamat befejezése után, és figyelje meg, hogy az adatbázis jelenleg az eredeti régióban.
1. Frissítse újra az események hub, és nyissa meg a Hawthorn Hall, és figyelje meg, hogy az adatbázisában is az eredeti régióban található. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Miután hazatelepítési helyreállítási régió erőforrások törlése
Hazatelepítési végrehajtása után már biztonságosan törli az erőforrást a helyreállítási régióban. 

> [!IMPORTANT]
> Törölje ezeket az erőforrásokat azonnal minden számlázási számukra leállítására.

A visszaállítási folyamat összes helyreállítási erőforrást egy helyreállítási erőforráscsoportot hoz létre. A kitakarítási folyamatot fog törölje a csoportot, és távolítson el minden hivatkozást az erőforrásokat a katalógusból. 

1. Az a *PowerShell ISE*, a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl, beállítása:
    * **$DemoScenario = 6**, a helyreállítási régió elavult erőforrások törlése

1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

Takarítás a parancsfájlok, az alkalmazás párt vissza ahol elindította.  Futtassa újra a parancsfájlt, vagy próbálja ki más oktatóanyagokat ezen a ponton.

## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Az alkalmazás az alkalmazás- és biztosításához tervezése közös elhelyezésű 
Az alkalmazás célja, hogy mindig kapcsolódik egy példányával, mivel a bérlői adatbázis ugyanabban a régióban. Ez a kialakítás csökkenti a késést, az alkalmazás és az adatbázis között. Az optimalizálás feltételezi a app adatbázis-kapcsolati chattier, mint a felhasználó alkalmazáson belüli kommunikáció.  

Bérlői adatbázisok hazatelepítési során egy kis ideig eljutni helyreállítási és az eredeti régiók között.  Az egyes adatbázisok az alkalmazás a régió, ahol az adatbázis található egy DNS-címkeresés a kiszolgáló nevére bérlői végrehajtásával keres. Az SQL-adatbázis a kiszolgáló neve, egy aliast. Az alias-kiszolgáló neve a régió nevét tartalmazza. Ha az alkalmazás nem az adatbázis ugyanabban a régióban, átirányítja a példány az adatbázis-kiszolgáló ugyanabban a régióban.  Az adatbázis ugyanabban a régióban példány átirányítása minimálisra csökkenti az alkalmazás- és közötti késés.  

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:
> [!div class="checklist"]

>* A bérlői katalógusának használatával rendszeresen frissíteni konfigurációs adatokat, így egy tükrözött lemezképét helyreállítási környezet egy másik régióban hozhatók létre
>* Azure SQL-adatbázisok helyreállítását a helyreállítási régióba georedundáns helyreállítás használata
>* A bérlői katalógus megfelelően visszaállított bérlői adatbázis helyek frissítése  
>* A DNS-alias használatával lehetővé teszik az alkalmazások a bérlő katalógus teljes újrakonfigurálása nélkül való kapcsolódáshoz
>* A georeplikáció használatával helyreállított adatbázisokat az eredeti régió települni után kimaradás oldva

Most próbáljon meg a [egy adatbázis-georeplikációt használ több-bérlős SaaS-alkalmazáshoz vész-helyreállítási](saas-dbpertenant-dr-geo-replication.md) hogyan jelentős mértékben a georeplikáció használatával állíthatja helyre a nagy méretű több-bérlős alkalmazás szükséges idő csökkentéséhez.

## <a name="additional-resources"></a>További források

* [További oktatóprogramot kínál, amelyek a Wingtip SaaS-alkalmazás épül](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
