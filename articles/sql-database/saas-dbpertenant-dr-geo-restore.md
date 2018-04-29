---
title: Használja az Azure SQL Database georedundáns biztonsági mentések vész-helyreállítási az SaaS-alkalmazásokhoz |} Microsoft Docs
description: Bemutatják, hogyan használhatja az Azure SQL Database georedundáns biztonsági mentések helyreállítani egy több-bérlős SaaS-alkalmazás nem tervezett kimaradás esetén
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/16/2018
ms.author: ayolubek
ms.openlocfilehash: 8fd25e13f6796b8be99ad3efd425bcde7bca3905
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Georedundáns helyreállítás használja egy több-bérlős SaaS-alkalmazáshoz helyreállíthatja az adatbázis biztonsági mentése

Ez az oktatóanyag egy több-bérlős SaaS-alkalmazáshoz, az egyes bérlői modellek adatbázis megvalósítva a teljes vész-helyreállítási forgatókönyv ismerteti. Használhat [georedundáns helyreállítás](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) is helyreállíthatják az alkalmazáskatalógus és a bérlői adatbázisokat automatikusan karbantartott georedundáns biztonsági másolatból egy másik helyreállítási régióba. A leállás nem oldódik meg, miután az [georeplikáció](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) a módosított adatbázisokat az eredeti régió települni.

![földrajzi-visszaállítási-architektúra](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Georedundáns helyreállítás a legkisebb költségű vész-helyreállítási megoldást az Azure SQL Database. Azonban visszaállítás, georedundáns biztonsági másolatból adatok elvesztését eredményezheti legfeljebb egy óra. Az egyes adatbázisok méretétől függően hosszabb időt is igénybe vehet. 

> [!NOTE]
> Georedundáns helyreállítás helyett georeplikáció használatával állíthatja helyre az alkalmazások és a lehető legalacsonyabb RPO RTO.

Ez az oktatóanyag ismerteti, visszaállítás és a hazatelepítési munkafolyamatok. Az alábbiak végrehajtásának módját ismerheti meg:
> [!div class="checklist"]

>* Sync-adatbázis és a bérlői katalógusba rugalmas konfigurációs adatait.
>* Egy helyreállítási régióban található, amely tartalmazza az alkalmazás, a kiszolgálók és a készletek tükrözött lemezképét környezet beállítása.   
>* Georedundáns helyreállítás használatával állíthatja helyre az alkalmazáskatalógus és a bérlői adatbázisok.
>* A georeplikáció használatával települni a bérlő katalógus és a módosított tenant adatbázisok megoldódott a szolgáltatáskimaradás elhárítása után.
>* A katalógus frissíti, az egyes adatbázisok visszaállítása (vagy fogalommeghatározás) nyomon követéséhez mindegyik bérlő adatbázis aktív példány aktuális helyzete.
>* Győződjön meg arról, hogy az alkalmazás és a bérlői adatbázis mindig közös található ugyanabban a régióban Azure késés csökkentése érdekében. 
 

Ez az oktatóanyag megkezdése előtt hajtsa végre a következő előfeltételek teljesülését:
* A bérlői alkalmazásonkénti Wingtip jegyek Szolgáltatottszoftver-adatbázis központi telepítése. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS adatbázis bérlői alkalmazásonként](saas-dbpertenant-get-started-deploy.md). 
* Az Azure PowerShell telepítése. További információkért lásd: [Ismerkedés az Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>A georedundáns helyreállítás helyreállítási mintát bemutatása

Vészhelyreállítás (DR) számos alkalmazás fontos szempont a megfelelőségi okokból vagy az üzletmenet folytonossága. Hosszan tartó szolgáltatáskimaradás esetén egy jól felkészültnek vész-Helyreállítási terv minimálisra üzleti problémákat. A vész-Helyreállítási terv georedundáns helyreállítás alapján kell elérheti, ha több célok:
 * Győződjön meg arról, hogy az elérhető bérlő adatbázisok visszaállítása a lehető leggyorsabban lefoglalni valamennyi szükséges kapacitás a kiválasztott helyreállítási régióban.
 * Egy tükrözött lemezképét helyreállítási környezetre, amely tükrözi az eredeti készlet és adatbázis-konfiguráció létrehozására. 
 * Lehetővé teszi a visszaállítási folyamat közepes útban gyermektevékenysége, ha az eredeti régió ismét online elérhető lesz.
 * Engedélyezze a bérlő kiépítésének gyorsan, új bérlő bevezetése a lehető leghamarabb újraindíthatja.
 * Állítsa vissza a bérlők prioritási sorrendben lehet optimalizálni.
 * Online tenant minél hamarabb beolvasásának párhuzamosan lépések végrehajtásával, ha gyakorlati lehet optimalizálni.
 * Hiba, újraindítható, és az idempotent rugalmasak lehetnek.
 * Települni adatbázisokat az eredeti régió gyakorolt minimális hatás mellett a bérlők számára, amikor a szolgáltatáskimaradás elhárítása.  

> [!NOTE]
> Az alkalmazás a régió, ahol az alkalmazás központi telepítése a párosított régióba helyre lett állítva. További információkért lásd: [Azure párosítva régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Ez az oktatóanyag az Azure SQL Database és az Azure platform funkciókat használ, ezek a kihívások megoldására:

* [Az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), minden szükséges kapacitást minél gyorsabban lefoglalni. Azure Resource Manager-sablonok segítségével az eredeti kiszolgálók és a helyreállítási régióban rugalmas készletek szerinti kiépítéséhez. Egy önálló kiszolgáló és a készlet is létre új bérlők számára történő üzembe helyezéséhez.
* [A rugalmas adatbázis ügyféloldali kódtár](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL), egy bérlő adatbázis katalógus karbantartását. A kiterjesztett katalógus rendszeresen frissíteni címkészletet és az adatbázis konfigurációs adatokat tartalmaz.
* [A shard helyreállítási funkciók](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) , a EDCL, amelyeknek köszönhetően a hely adatbáziselemek a katalógus helyreállítási és hazatelepítési során.  
* [Georedundáns helyreállítás](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), az alkalmazáskatalógus és a bérlői adatbázisok helyreállításához automatikusan karbantartott georedundáns biztonsági másolatból. 
* [Aszinkron visszaállítási műveletek](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), bérlő-prioritási sorrendben küldött, a rendszer mindegyik készlet várakozó és feldolgozta kötegekben, így az alkalmazáskészlet nem túlterhelt. Ezek a műveletek előtt vagy a során szükség lehet visszavonni.   
* [A georeplikáció](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), az adatbázisok eredeti régió települni a szolgáltatáskimaradás elhárítása után. Nincs nincs az adatvesztéssel és a tenant gyakorolt minimális hatás georeplikáció használatakor.
* [SQL server DNS-alias](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), hogy a katalógus szinkronizálási folyamat függetlenül az, hogy az aktív katalógusban való kapcsolódáshoz.  

## <a name="get-the-disaster-recovery-scripts"></a>A vész-helyreállítási parancsfájlok beolvasása

A jelen oktatóanyagban használt vész-Helyreállítási parancsfájlok érhetők el a [Wingtip jegyek SaaS adatbázis GitHub-tárházban bérlőnként](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek felügyeleti parancsfájlok lépéseit.

> [!IMPORTANT]
> Minden a Wingtip jegyek felügyeleti parancsfájlok, például a vész-Helyreállítási parancsfájlok minta minőségét és a rendszer nem éles környezetben használja.

## <a name="review-the-healthy-state-of-the-application"></a>Az alkalmazás kifogástalan állapotának áttekintése
A helyreállítási folyamat megkezdése előtt tekintse át az alkalmazás normál kifogástalan állapotát.

1. A böngészőben nyissa meg a Wingtip jegyek események hub (http://events.wingtip-dpt.&lt; felhasználói&gt;. trafficmanager.net, cserélje le &lt;felhasználói&gt; a központi telepítés felhasználói értékű).
    
   Görgessen a lap alján, és figyelje meg, a katalógus kiszolgáló nevét és helyét, a láblécben. A hely a régiót, amelyben az alkalmazás telepítve.    

   > [!TIP]
   > Mutasson az egérrel megjelenítésének nagyítása helyét.

   ![Események hub állapota kifogástalan eredeti régióban](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Válassza ki a Contoso energiaoptimalizálást egyszerre Hall bérlői, és nyissa meg az esemény lapját.

   A lábrészben megjelenő figyelje meg, hogy a bérlő kiszolgáló nevét. A hely megegyezik a katalógus kiszolgáló helyét.

   ![Contoso energiaoptimalizálást egyszerre Hall eredeti régió](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. Az a [Azure-portálon](https://portal.azure.com), tekintse át, és nyissa meg az erőforráscsoportot, amelyben az alkalmazás telepítése.

   Figyelje meg, az erőforrások és a régiót, amelyben az app service-összetevők és az SQL Database-kiszolgálók vannak telepítve.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>A bérlő konfigurációjához a katalógusba való szinkronizálása

Ebben a feladatban olyan folyamatot, amely a kiszolgálók, a rugalmas készletek és az adatbázisok konfigurációját a bérlő katalógusba való szinkronizálás elindításához. Ez az információ később egy tükrözött lemezképét környezet konfigurálása a helyreállítási régióban szolgál.

> [!IMPORTANT]
> Az egyszerűség kedvéért a szinkronizálási folyamat és egyéb hosszú futású helyreállítási és hazatelepítési folyamatok valósíthatók meg e minták helyi PowerShell feladatok vagy az ügyfél felhasználói bejelentkezés alatt futó munkameneteket. A hitelesítési tokenek ki, ha a bejelentkezést több óra elteltével lejár, és a művelete sikertelen lesz, majd. Egy éles telepítési forgatókönyvhöz, a hosszú futású folyamatokat valamilyen, egyszerű szolgáltatás alatt futó megbízható Azure szolgáltatásként kell végrehajtani. Lásd: [a Azure PowerShell szolgáltatás használatával hozzon létre egy egyszerű tanúsítvány](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. A PowerShell ISE nyissa meg a ...\Learning Modules\UserConfig.psm1 fájlt. Cserélje le `<resourcegroup>` és `<user>` 10-es és 11 az értéket, az alkalmazás üzembe helyezésekor a sorban. Mentse a fájlt.

2. A PowerShell ISE nyissa meg a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl.

    Ebben az oktatóanyagban a PowerShell parancsfájl futtatása minden egyes forgatókönyv, úgyhogy tartsa a fájl megnyitása.

3. Állítsa be a következőt:

    $DemoScenario = 1: Indítsa el a háttérben futó feladat Szinkronizáló bérlői kiszolgáló és a konfigurációs adatait a katalógusba.

4. A szinkronizálás parancsfájl futtatásához, válassza ki az F5. 

    Ez az információ később annak biztosítására szolgál, hogy helyreállítási lemezképet hoz létre tükrözött a kiszolgálók, a készletek és az adatbázisok helyreállítási régióban.  

    ![Szinkronizálási folyamat](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Hagyja meg a PowerShell ablakban fut a háttérben, és a többi Ez az oktatóanyag folytatásához.

> [!NOTE]
> A szinkronizálási folyamat a katalógus DNS-alias keresztül kapcsolódik. Az alias visszaállítása és az aktív katalógus mutasson hazatelepítési során is módosul. A szinkronizálási folyamat a katalógus naprakészen tartható az adatbázis vagy a tárolókészlet konfigurációs módosítások helyreállítási régióban. A hazatelepülés során ezeket a módosításokat az eredeti régióban egyenrangú erőforrásokkal is vonatkozik.

## <a name="geo-restore-recovery-process-overview"></a>Georedundáns helyreállítás helyreállítási folyamat áttekintése

A georedundáns helyreállítás helyreállítási folyamat központilag telepíti az alkalmazást, és visszaállítja az adatbázisok biztonsági másolatból a helyreállítási régióba.

A helyreállítási folyamat a következőket teszi:

1. Letiltja a webalkalmazás az eredeti régióban Azure Traffic Manager-végpontot. A végpont letiltása megakadályozza, hogy a felhasználók csatlakozni az alkalmazás állapota érvénytelen az eredeti régió érkezik online helyreállítás során.

2. Kiosztja a helyreállítási katalógus-kiszolgáló a helyreállítási régióban, földrajzi-visszaállítások a katalógus-adatbázis, és frissíti a activecatalog alias, hogy a visszaállított kiszolgáló mutasson. A katalógus alias módosítása biztosítja, hogy a katalógus szinkronizálási folyamat mindig szinkronizálja a aktív katalógus.

3. Az összes meglévő bérlők számára a helyreállítási katalógus offline állapotúként kívánja tagadni a hozzáférést bérlői adatbázisokhoz, mielőtt vissza jelöli.

4. Az alkalmazás a helyreállítási régióban példányának kiépítését, és konfigurálja a visszaállított katalógus használatát az adott régióban. Minimális késés megtartásához mintaalkalmazás célja, hogy mindig ugyanabban a régióban bérlői adatbázishoz csatlakozni.

5. Látja el a kiszolgáló és a rugalmas készletben, amelyben az új bérlők törlődnek. Ezeket az erőforrásokat létrehozása biztosítja, hogy új bérlők kiépítése nem zavarja a visszaállítást meglévő bérlők számára.

6. Frissíti az új bérlő alias a kiszolgálót az új bérlő adatbázisokat a helyreállítási régióban. Ez az alias módosítása biztosítja, hogy az új tenantokat adatbázisok helyreállítási régióban törlődnek.
        
7. Kiszolgálók és a bérlői adatbázisok helyreállításához a helyreállítási régióban rugalmas készletek kiépítését. A kiszolgálók és a készletek lesznek a konfiguráció az eredeti régióban. Az összes adatbázis visszaállításához szükséges kapacitás készletek előre kiépítés foglalja le.

    Nem tervezett kimaradás régióban lehet, hogy helyezze jelentős terhelés párosított régióban elérhető erőforrások. Ha használ, georedundáns helyreállítás Dr, majd gyorsan az erőforrások lefoglalása ajánlott. Fontolja meg a georeplikáció Ha nagyon fontos, hogy az alkalmazás állítja helyre a rendszer egy adott területre. 

8. Lehetővé teszi, hogy a webalkalmazás a helyreállítási régióban Traffic Manager-végpontot. Ez a végpont lehetővé teszi, hogy az alkalmazás telepítéséhez az új bérlők számára. Ebben a szakaszban a meglévő bérlőkre is offline állapotú.

9. Adatbázisok prioritási sorrendben visszaállítására irányuló kérelmekre kötegekben küldi el. 

    * Kötegek vannak rendezve, hogy az adatbázisok visszaállítása párhuzamosan készletekben között.  

    * Visszaállítás kéri, hogy gyorsan be, és mindegyik készlet végrehajtás várakozó aszinkron módon elküldi.

    * Visszaállítási kérelmek feldolgozása párhuzamosan történik készletekben keresztül, mert már fontos bérlők szét sok készletek. 

10. Annak meghatározásához, amikor adatbázisok visszaállítása az SQL Database szolgáltatás figyeli. Egy bérlő adatbázis helyreállítása után a katalógus online van megjelölve, és a bérlői adatbázis rowversion összege van. 

    * Bérlői adatbázisok hozzáférhet az alkalmazáshoz, amint online, a katalógus van megjelölve.

    * A tenant adatbázisban rowversion értékek összegét a katalógus tárolja. Ezt az összeget úgy működik, mint egy ujjlenyomat, amely lehetővé teszi a hazatelepítési folyamat határozza meg, ha az adatbázis helyreállítási régióban frissült.       

## <a name="run-the-recovery-script"></a>A helyreállítási parancsprogrammal

> [!IMPORTANT]
> Ez az oktatóanyag adatbázisok visszaállítása georedundáns biztonsági másolatból. Annak ellenére, hogy ezek a biztonsági másolatok általában 10 percen belül, azt órát is igénybe vehet egy. A parancsfájl felfüggesztése, amíg azok elérhetők.

Tegyük fel, a régióban, amelyben az alkalmazás üzemel, és a helyreállítási parancsprogrammal kimaradás van:

1. A PowerShell ISE a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl, állítsa be a következő értéket:

    $DemoScenario = 2: az alkalmazás helyreállítását olyan helyreállítási régió georedundáns biztonsági másolatból állítja vissza.

2. Futtassa a parancsfájlt, válassza ki az F5 billentyűt.  

    * A parancsfájl egy új PowerShell-ablakban nyílik meg, és elindítja a párhuzamosan futó feladatok PowerShell készlete. Ezeket a feladatokat a helyreállítási régióban kiszolgálók, a készletek és az adatbázisok visszaállítása.

    * A helyreállítási terület értéke a párosított régió társított Azure-régiót, amelyben az alkalmazás telepítése. További információkért lásd: [Azure párosítva régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. A PowerShell-ablakban a helyreállítási folyamat állapotának figyelése.

    ![A helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> A helyreállítási feladatok kódja megismeréséhez tekintse át a PowerShell-parancsfájlok a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\RecoveryJobs mappában.

## <a name="review-the-application-state-during-recovery"></a>A helyreállítás során állapotának áttekintése
Az alkalmazás végpont le van tiltva a Traffic Managerben, amíg az alkalmazás nem érhető el. A katalógus helyreáll, és a bérlők offline megjelölve. Az alkalmazás végpontjának helyreállítási régióban majd engedélyezve van, és az alkalmazást újra online állapotba kerül. Bár az alkalmazás nem érhető el, bérlők offline állapotúnak fogja látni az események központban hozzájuk tartozó adatbázisok visszaállításáig. Fontos alkalmazás offline bérlői adatbázisok kezelésének megtervezése.

* A katalógus-adatbázis helyreállított de előtt ismét online állapotban a bérlők, frissítse a Wingtip jegyek események hub a böngészőben.

    * A lábrészben megjelenő figyelje meg, hogy rendelkezik-e már a katalógus kiszolgálónév - helyreállítási utótag és a helyreállítási régióban található.

    * Figyelje meg, hogy a bérlők számára, amelyek nem lettek visszaállítva offline állapotban van megjelölve, és nincsenek választható.   
 
    ![A helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Ha megnyit egy bérlő események lapot közvetlenül a bérlő kapcsolat nélküli módban, a lap bérlői offline értesítést jelenít meg. Például a Contoso energiaoptimalizálást egyszerre Hall offline állapotban, ha megpróbálja megnyitni http://events.wingtip-dpt.&lt; felhasználói&gt;.trafficmanager.net/contosoconcerthall.

    ![A helyreállítási folyamat](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Egy új bérlőt a helyreállítási régióban kiépítése
Még a bérlő adatbázisok visszaállítása, mielőtt új bérlők a régióban helyreállítási oszthat meg. A helyreállítási régióban üzembe új bérlő adatbázisok vannak fogalommeghatározás a helyreállított adatbázisokkal később.   

1. A PowerShell ISE a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl, állítsa be a következő tulajdonságot:

    $DemoScenario = 3: egy új bérlőt a helyreállítási régióban kiépítéséhez.

2. Futtassa a parancsfájlt, válassza ki az F5 billentyűt.

3. A böngészőben megnyílik az Hawthorn Hall események lap, ha befejeződik. 

    Figyelje meg, hogy a helyreállítási régióban található, a Hawthorn Hall adatbázis.

    ![A helyreállítási régióban üzembe hawthorn Hall](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. A böngészőben frissítse a Wingtip jegyek események hub lapot, melyen megtekintheti, Hawthorn Hall tartalmazza. 

    Kiépített Hawthorn Hall visszaállítása a többi bérlő várakozás nélkül, ha más bérlők előfordulhat, hogy még mindig offline állapotban van.

## <a name="review-the-recovered-state-of-the-application"></a>Tekintse át a helyreállított az alkalmazás állapota

A helyreállítási folyamat befejezése után az alkalmazás és minden bérlők teljesen működőképes helyreállítási területen. 

1. Után a PowerShell-konzolablakot megjelenítését azt jelzi, hogy a bérlők helyreállítás, frissítse az események központhoz. 

    A bérlők összes online, beleértve az új tenanthoz Hawthorn Hall jelennek meg.

    ![az események központban helyreállított és az új bérlők](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Kattintson a Contoso energiaoptimalizálást egyszerre Hall, és nyissa meg az események lapról. 

    A lábrészben megjelenő figyelje meg, hogy az adatbázist a helyreállítási régióban található, a helyreállítási kiszolgálón található.

    ![A helyreállítási régióban contoso](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Az a [Azure-portálon](https://portal.azure.com), az erőforráscsoport-sablonok listájának megnyitásához.  

    Figyelje meg, melyik erőforráscsoport üzembe helyezett, valamint a helyreállítási erőforráscsoport, a - helyreállítási utótaggal rendelkező. A helyreállítási erőforráscsoport összes a helyreállítási folyamat során létrejött erőforrásokat, valamint a szolgáltatáskimaradás elhárítása során létrehozott új erőforrásokat tartalmazza. 

4. Nyissa meg a helyreállítási erőforráscsoportot, és figyelje meg a következő elemek:

    * A katalógus és tenants1 kiszolgálók helyreállítási verziói - helyreállítási utótaggal rendelkező. A visszaállított katalógus és a bérlői adatbázisok ezeken a kiszolgálókon minden eredeti régióban használt nevekkel rendelkeznek.

    * A tenants2-dpt -&lt;felhasználói&gt;– SQL server helyreállítása. Ezt a kiszolgálót az új bérlők lépnek a szolgáltatáskimaradás elhárítása során használja.

    * Az app service nevű esemény-wingtip-dpt -&lt;recoveryregion&gt;-&lt;felhasználói&gt;, vagyis az események alkalmazás helyreállítási példányát.

    ![A helyreállítási régióban contoso erőforrások](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Nyissa meg a tenants2-dpt -&lt;felhasználói&gt;– SQL server helyreállítása. Figyelje meg, hogy az adatbázis hawthornhall és a rugalmas készlet Pool1 benne. A hawthornhall adatbázis konfigurálják, mint egy olyan rugalmas adatbázis Pool1 a rugalmas készletben.

## <a name="change-the-tenant-data"></a>A bérlői adatok módosítása 
Ebben a feladatban frissíti egy visszaállított bérlői adatbázis. A hazatelepítési folyamat másolatok visszaállítása adatbázisok eredeti régió megváltozott. 

1. A böngészőben az eseménylista keresése a Contoso energiaoptimalizálást egyszerre Hall, görgessen végig az eseményeket, és figyelje meg, az utolsó esemény, súlyosan Strauss.

2. A PowerShell ISE a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl, állítsa be a következő értéket:

    $DemoScenario = 4: a helyreállítási régióban bérlőtől esemény törlése.

3. Végrehajtani a parancsprogramot, válassza ki az F5 billentyűt.

4. Frissítse a Contoso energiaoptimalizálást egyszerre Hall események lapot (http://events.wingtip-dpt.&lt; felhasználói&gt;.trafficmanager.net/contosoconcerthall), és figyelje meg, hogy az esemény súlyosan Strauss hiányzik.

Ezen a ponton az oktatóanyagban helyreállította az alkalmazásról, így most már fut a helyreállítási régióban. Egy új bérlőt a helyreállítási régióban kiosztása és módosította az egyik a visszaállított bérlők adatokat.  

> [!NOTE]
> Egyéb oktatóanyagok minta nem tervezték, és a helyreállítási állapotban az alkalmazás futtatásához. Ha szeretné használni a többi oktatóanyag, ügyeljen arra, hogy az alkalmazás első települni.

## <a name="repatriation-process-overview"></a>Hazatelepítési folyamat áttekintése

A hazatelepítési folyamat visszaállítja az alkalmazás és az adatbázisok eredeti régiója, egy a szolgáltatáskimaradás elhárítása után.

![Georedundáns helyreállítás hazatelepítési](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

A folyamat:

1. Minden folyamatban lévő visszaállítási tevékenység leáll, és megszakítja a függőben lévő vagy üzenetsoroktól adatbázis visszaállítási kéréseit.

2. Újraaktiválja a eredeti régió bérlői adatbázisokban, amely a szolgáltatáskimaradás óta nem módosult. Ezek az adatbázisok a következők: még nincs helyreállítva, valamint azok helyre, de ezt követően nem módosul. Az újraaktivált adatbázisok megegyeznek a legutóbbi által elért bérlőiknek.

3. Az új tenanthoz kiszolgáló és a rugalmas készlet az eredeti régió szerinti látja el. Ez a művelet befejezése után az új bérlő alias frissítése erre a kiszolgálóra mutasson. Az alias frissítése hatására új bérlő bevezetési fordulnak elő az eredeti régió helyett a helyreállítási régió.

3. A georeplikáció használatával helyezze át a katalógus az eredeti régió helyreállítási régióban.

4. Frissíti az eredeti régió tárolókészlet konfigurációját, hogy konzisztens helyreállítási régióban a szolgáltatáskimaradás elhárítása során végzett módosításokat.

5. A szükséges kiszolgálókat és a készletek üzemeltetésére a szolgáltatáskimaradás elhárítása során létrehozott új adatbázisokat hoz létre.

6. Georeplikálási frissített adatbázisok visszaállítás utáni települni a visszaállított bérlői és minden új bérlő adatbázisok kiépítése során a szolgáltatáskimaradás használja. 

7. A visszaállítás során a helyreállítási régióban létrehozott erőforrások a szükségtelenné vált.

Igénylő kell fogalommeghatározás bérlői adatbázisok száma korlátozásához ismertetett 1 – 3 azonnal történik.  

4. lépés csak akkor történik, ha a helyreállítási régióban katalógus módosítva lett a szolgáltatáskimaradás elhárítása során. A katalógus frissül, ha az új bérlők jöttek létre, vagy ha bármely adatbázis vagy a tárolókészlet konfigurációs megváltozik, a helyreállítási régióban.

Fontos, hogy 7. lépés minimális megszakítás hatására a bérlők és az adatok nem vesztek el. E cél eléréséhez a folyamat használja a georeplikáció.

Mielőtt az egyes adatbázisok georeplikált, a megfelelő adatbázist az eredeti régióban törlődik. A helyreállítási régióban adatbázisa majd georeplikált, egy másodlagos másodpéldány létrehozása az eredeti régióban. Replikáció befejezése után a bérlői offline a katalógus, amely a kapcsolatokat, az adatbázis helyreállítási régióban van megjelölve. Az adatbázis majd feladatátvételt, minden függőben lévő tranzakciók a másodlagos feldolgozni, így nincsenek adatok, amely elvész. 

Feladatátvétel esetén az adatbázis-szerepkörök fordított irányú. A másodlagos eredeti régióban lesz az elsődleges adatbázis írható-olvasható, az adatbázis helyreállítási régióban pedig egy írásvédett másodlagos. A bérlői bejegyzést a katalógusban való hivatkozáshoz az adatbázist az eredeti régióban frissül, és a bérlői online van megjelölve. Ezen a ponton az adatbázis hazatelepítési befejeződött. 

Újrapróbálkozási logikát győződjön meg arról, hogy újracsatlakozáskor automatikusan amikor a kapcsolat nem működik az alkalmazások kell írni. Az újracsatlakozás replikaszervező való használata a katalógusban, csatlakoznak a repatriated adatbázis eredeti régióban. Bár a rövid bontása gyakran nem első fellépése, melyet kiválaszthat munkaidőn kívülre adatbázisok települni.

Miután egy adatbázis fogalommeghatározás van, a másodlagos adatbázist a helyreállítási régióban törölhetők. Az adatbázis az eredeti régióban majd támaszkodik újra a vész-Helyreállítási védelem georedundáns helyreállítás.

8. lépésben a helyreállítási régióban, így a helyreállítási és -készletek, erőforrások törlése.

## <a name="run-the-repatriation-script"></a>A hazatelepítési parancsprogrammal
Most tegyük fel, a szolgáltatáskimaradás megoldása és a hazatelepítési parancsprogrammal.

Ha követte az oktatóanyag, a a parancsfájl azonnali újraaktiválja Fabrikam Jazz Club és az eredeti régióban somfát Dojo annak mert változatlan. Majd repatriates az új tenanthoz Hawthorn Hall, és a Contoso energiaoptimalizálást egyszerre Hall, mert lett módosítva. A parancsfájl is repatriates a katalógus, amely frissült, amikor Hawthorn Hall lett kiépítve.
  
1. A PowerShell ISE a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájl, a ellenőrizze, hogy a katalógus szinkronizálási folyamat továbbra is fut-e a PowerShell-példányban. Ha szükséges, indítsa újra úgy, hogy:

    $DemoScenario = 1: Indítsa el a bérlői server, a készlet és az adatbázis konfigurációs adatainak szinkronizálása a katalógusba.

    Futtassa a parancsfájlt, válassza ki az F5 billentyűt.

2.  Ezután a hazatelepítési megkezdéséhez beállítása:

    $DemoScenario = 5: az alkalmazás az eredeti régióba települni.

    A parancsfájl futtatásához a helyreállítás egy új PowerShell-ablakban válassza ki az F5. Hazatelepítési több percet vesz igénybe, és figyelhetők a PowerShell-ablakban.

3. A parancsfájl futása közben, az események hub lap frissítése (http://events.wingtip-dpt.&lt; felhasználói&gt;. trafficmanager.net).

    Figyelje meg, hogy a bérlők online állapotúak és elérhetők a folyamat során.

4. Válassza ki a Fabrikam Jazz Club való megnyitásához. Ha nem módosítja ezt a bérlőt, figyelje meg, a láblécben az, hogy a kiszolgáló már tért vissza az eredeti kiszolgálón.

5. Nyissa meg, vagy frissítse a Contoso energiaoptimalizálást egyszerre Hall események lapot. Láthatja a lábléc az, hogy, kezdetben az adatbázis - a helyreállítási kiszolgálón marad. 

6. A Contoso energiaoptimalizálást egyszerre Hall események lapot a hazatelepítési folyamat befejezése után, és figyelje meg, hogy az adatbázis jelenleg az eredeti régióban frissítése.

7. Frissítse újra az események központhoz, majd nyissa meg a Hawthorn Hall. Figyelje meg, hogy az adatbázis az eredeti régióban is található. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Miután hazatelepítési helyreállítási régió erőforrások törlése
Hazatelepítési befejezése után is csökkentett törli az erőforrást a helyreállítási régióban. 

> [!IMPORTANT]
> Törölje ezeket az erőforrásokat azonnal minden számlázási számukra leállítására.

A visszaállítási folyamat összes helyreállítási erőforrást egy helyreállítási erőforráscsoportot hoz létre. A kitakarítási folyamatot az erőforráscsoport törli, és eltávolítja a katalógusból az erőforrásokhoz való összes hivatkozást. 

1. A PowerShell ISE a parancsfájlban ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, állítsa be:
    
    $DemoScenario = 6: a helyreállítási régióban törli az elavult erőforrást.

2. Futtassa a parancsfájlt, válassza ki az F5 billentyűt.

Takarítás a parancsfájlok, az alkalmazás párt vissza ahol elindította. Ezen a ponton újra futtassa a parancsfájlt, vagy próbálja ki más oktatóanyagokat.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Az alkalmazást, annak érdekében, hogy az alkalmazás és az adatbázis közös elhelyezésű tervezése 
Az alkalmazás célja, hogy mindig csatlakoznak ugyanabban a régióban, mivel a bérlői adatbázis-példányról. Ez a kialakítás csökkenti a késést, az alkalmazás és az adatbázis között. Az optimalizálás feltételezi a app adatbázis-kapcsolati chattier, mint a felhasználó alkalmazáson belüli kommunikáció.  

Bérlői adatbázisok hazatelepítési során egy kis ideig helyreállítási és az eredeti régiók közötti lehet, hogy terjednek. Az egyes adatbázisok az alkalmazás a régió, ahol az adatbázis található egy DNS-címkeresés a kiszolgáló nevére bérlői végrehajtásával keres. Az SQL-adatbázis a kiszolgáló neve, egy aliast. Az alias-kiszolgáló neve a régió nevét tartalmazza. Ha az alkalmazás nem az adatbázis ugyanabban a régióban, átirányítja a példány az adatbázis-kiszolgáló ugyanabban a régióban. Az adatbázis ugyanabban a régióban példány átirányítása minimálisra csökkenti az alkalmazás és az adatbázis közötti késleltetés.  

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]

>* A bérlő-katalógus használatáról az ahhoz, hogy rendszeresen frissíteni konfigurációs adatok, amely lehetővé teszi egy tükrözött lemezképét helyreállítási környezet egy másik régióban hozhatók létre.
>* Azure SQL-adatbázisok helyreállítására a helyreállítási régióba georedundáns helyreállítás használatával.
>* A bérlői katalógus megfelelően visszaállított bérlői adatbázis helyek frissítése. 
>* A DNS-alias használatával lehetővé teszik az alkalmazások a bérlő katalógus teljes újrakonfigurálása nélkül való kapcsolódáshoz.
>* A georeplikáció használatával helyreállított adatbázisokat az eredeti régió települni, miután kimaradás fel lett oldva.

Próbálja meg a [egy több-bérlős SaaS-alkalmazáshoz, adatbázis-georeplikációt használ vész-helyreállítási](saas-dbpertenant-dr-geo-replication.md) megtudhatja, hogyan georeplikáció használatával jelentősen csökkenthető a egy nagy méretű több-bérlős alkalmazás helyreállításához szükséges idő az oktatóanyagot.

## <a name="additional-resources"></a>További források

[További oktatóprogramot kínál, amelyek a Wingtip SaaS-alkalmazás épül](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
