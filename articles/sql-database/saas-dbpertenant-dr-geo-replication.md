---
title: SaaS-alkalmazásokhoz Azure SQL adatbázis Georeplikációt használ a vészhelyreállítás |} Microsoft Docs
description: Ismerje meg, hogyan használható az Azure SQL Database-georeplikációkhoz helyreállítani egy több-bérlős SaaS-alkalmazás nem tervezett kimaradás esetén
keywords: sql database-oktatóanyag
services: sql-database
author: AyoOlubeko
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/09/2018
ms.author: ayolubek
ms.openlocfilehash: c6f3da52643caa9aa1172db5b884c5336c409715
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Az adatbázis-georeplikációt használ több-bérlős SaaS-alkalmazás katasztrófa utáni helyreállítás

Ebben az oktatóanyagban egy több-bérlős SaaS-alkalmazáshoz, az adatbázis-/-bérlős modell használatával végrehajtott a teljes vész-helyreállítási forgatókönyv vizsgálatát. Az alkalmazás megvédeni a nem tervezett kimaradás, használhatja [ _georeplikáció_ ](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview) replikákat az alkalmazáskatalógus és a bérlői adatbázisok létrehozásához egy másik helyreállítási régióban. Nem tervezett kimaradás esetén meg gyorsan feladatátvételt ezekre a replikákra folytatja a normál üzleti tevékenységekhez. Feladatátvétel esetén az adatbázisok eredeti régióban válnak a másodlagos replikákat az adatbázisok helyreállítási régióban. Miután ezekre a replikákra ismét online elérhető azokat automatikusan történik meg, az adatbázisokat a helyreállítási régióban állapotát. A leállás nem oldódik meg, miután Ön nem vissza az adatbázisok eredeti éles régióban.

Ez az oktatóanyag ismerteti, a feladatátvétel és a feladat-visszavétel munkafolyamatok. Megtudhatja, hogyan:
> [!div classs="checklist"]

>* Sync-adatbázis és a bérlői katalógusba rugalmas konfigurációs adatait
>* A helyreállítási környezet egy másik régióban, alkalmazás, a kiszolgálók és a készletek beállítása
>* Használjon _georeplikáció_ replikálni az alkalmazáskatalógus és a bérlői adatbázisokat a helyreállítási régió
>* Az alkalmazás és az alkalmazáskatalógus és a bérlői adatbázisok helyreállítási régió feladatátvételt 
>* Később, az alkalmazás keresztül, a katalógus és a bérlői adatbázisok biztonsági az eredeti régió megoldódott a szolgáltatáskimaradás elhárítása után
>* A katalógus frissíti, az egyes bérlői adatbázison végrehajtott feladatátvételt nyomon követéséhez mindegyik bérlő adatbázis elsődleges helye
>* Győződjön meg arról, az alkalmazás és az elsődleges bérlői adatbázis mindig közösen elhelyezett ugyanabban a régióban Azure késés csökkentése érdekében  
 

Az oktatóanyag elindítása előtt győződjön meg arról, hogy befejeződött a következő előfeltételek teljesülését:
* A bérlői alkalmazásonkénti Wingtip jegyek Szolgáltatottszoftver-adatbázis telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a bérlői alkalmazásonként Wingtip jegyek Szolgáltatottszoftver-adatbázis](saas-dbpertenant-get-started-deploy.md)  
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>A georeplikáció helyreállítási mintát bemutatása

![Helyreállítási architektúrája](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Vészhelyreállítás (DR) számos alkalmazás fontos szempont a megfelelőségi okokból vagy az üzletmenet folytonossága. Kell egy hosszan tartó szolgáltatáskimaradás, egy jól felkészültnek vész-Helyreállítási terv minimálisra üzleti problémákat. Georeplikáció használatának a legalacsonyabb RPO és RTO fölött: adatbázis-replikák egy helyreállítási régióban található, amely rövid, figyelje meg a feladatátvételre.

A vész-Helyreállítási terv alapján georeplikáció három különálló részből áll:
* Telepítés- és a helyreállítási környezet
* Helyreállítási - feladatátvétel a az alkalmazás és a helyreállítási környezetre, nem tervezett kimaradás esetén adatbázisok
* Hazatelepítési - feladatátvétel a az alkalmazás és az adatbázisok biztonsági az eredeti régió után az alkalmazás meg oldva 

Bármely részét úgy kell tekinteni, különösen akkor, ha a skála nyújtsák. A teljes a terv kell elérheti, ha több célok:

* Beállítás
    * Állítson be és kezelheti a tükör-lemezkép környezetet a helyreállítási régióban. A rugalmas készletek létrehozása és a helyreállítási környezetben egyetlen önálló adatbázis replikálása fenntartja a kapacitás, a helyreállítási régióban. Ebben a környezetben karbantartása új bérlő adatbázisok replikálása, mert kiépítésüket tartalmazza.  
* Helyreállítás
    * Ha egy kiterjesztett-le nyilas helyreállítási környezet napi költségek minimalizálása érdekében alkalmaznak, készletek és az önálló adatbázisok kell akár méretezhető teljes működési kapacitását a helyreállítási régióban beszerzése
    * Új bérlő helyreállítási régióban minél hamarabb kiépítés engedélyezése  
    * Lesz optimalizálva a bérlők prioritási sorrendben visszaállítása
    * Bérlők online lehető legnagyobb első párhuzamosan lépések végrehajtásával, ha gyakorlati optimalizálni
    * Hiba, újraindítható, és az idempotent rugalmasak lehetnek
    * Lehet megszakítja a folyamatot, közepes útban, ha az eredeti régió ismét online állapotú lesz.
* Hazatelepítési 
    * Adatbázisok helyreállítási régióban replikákra eredeti régióban bérlők minimális hatással van a feladatátvétel: nincs adatvesztés, és bérlőnként rögzítést minimális időtartam.   

Ebben az oktatóanyagban ezekkel a kihívásokkal foglalkozik az Azure SQL Database és az Azure platform olyan szolgáltatását használja:

* [Az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), minden szükséges kapacitást minél gyorsabban lefoglalni. Az Azure Resource Manager-sablonok segítségével az üzemi kiszolgálók és a rugalmas készletek helyreállítási régióban szerinti kiépíteni.
* [A georeplikáció](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), aszinkron módon replikált csak olvasható másodlagos adatbázis az összes olyan adatbázis létrehozásához. Nem tervezett kimaradás során rendszer átadja a replikák helyreállítási régióban.  A leállás nem oldódik meg, miután Ön nem vissza az eredeti régióban adatvesztés nélküli adatbázisok.
* [Aszinkron](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) feladatátvételi műveletek küldött bérlői-prioritási sorrendben szerepel, az adatbázisok nagy mennyiségű feladatátvételi idő minimalizálása érdekében.
* [A shard helyreállítási funkciók](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager), helyreállítási és hazatelepítési során a katalógus adatbáziselemek módosítása. Ezek a szolgáltatások engedélyezése az alkalmazás adatbázisaihoz való kapcsolódásra bérlői függetlenül helye az alkalmazás újrakonfigurálása nélkül.
* [SQL server DNS-alias](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), az új bérlők számára, függetlenül attól, milyen az alkalmazás üzemel zökkenőmentes kiépítés engedélyezése. DNS-aliasok is használhatók engedélyezése a katalógus szinkronizálási folyamat függetlenül az, hogy az aktív katalógusban való kapcsolódáshoz.

## <a name="get-the-disaster-recovery-scripts"></a>A vész-helyreállítási parancsfájlok beolvasása 

> [!IMPORTANT]
> Minden a Wingtip jegyek felügyeleti parancsfájlok, például a vész-Helyreállítási parancsfájlok minta minőségét és a rendszer nem éles környezetben használja. 

Ez az oktatóanyag és Wingtip alkalmazás forráskódjához használt parancsfájlok érhetők el a helyreállítást a [Wingtip jegyek SaaS adatbázis GitHub-tárházban bérlőnként](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek felügyeleti parancsfájlok lépéseit.

## <a name="tutorial-overview"></a>Az oktatóanyag áttekintése
Ebben az oktatóanyagban először georeplikáció létrehozására használhatja a Wingtip jegyek alkalmazás és az adatbázisok replikákat egy másik régióban. Ezt követően rendszer átadja ebben a régióban, végezze el a nem tervezett kimaradás szimulálásához. Amikor végzett, az alkalmazás teljesen működőképes a helyreállítási régióban.

Később egy külön hazatelepítési lépésben rendszer átadja az alkalmazáskatalógus és a bérlői adatbázisok eredeti régió helyreállítási régióban. Az alkalmazás és az adatbázisok maradnak hazatelepítési rendelkezésre. Amikor végzett, az alkalmazás teljesen működőképes az eredeti régióban.

> [!Note]
> Az alkalmazás a állítja helyre a rendszer a _párosított régió_ a régió, ahol az alkalmazás központi telepítése. További információkért lásd: [Azure párosítva régiók](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Az alkalmazás kifogástalan állapotának áttekintése

A helyreállítási folyamat megkezdése előtt tekintse át az alkalmazás normál kifogástalan állapotát.
1. A böngészőben nyissa meg a Wingtip jegyek események Hub (http://events.wingtip-dpt.&lt; felhasználói&gt;. trafficmanager.net - csere &lt;felhasználói&gt; a központi telepítés felhasználói értékű).
    * Görgessen a lap alján, és figyelje meg, a katalógus kiszolgáló nevét és helyét, a láblécben. A hely a régiót, amelyben az alkalmazás telepítve.
    *Tipp: Az egér megjelenítésének nagyítása a helyre mutasson. * 
     ![Események hub állapota kifogástalan eredeti régióban](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. A Contoso energiaoptimalizálást egyszerre Hall bérlő parancsára, és nyissa meg az esemény lapját.
    * A lábrészben megjelenő figyelje meg, hogy a bérlő kiszolgáló nevét. A hely ugyanaz, mint a katalógus kiszolgálójától helye lesz.

3. Az a [Azure-portálon](https://portal.azure.com), nyissa meg az erőforráscsoportot, amelyben az alkalmazás telepítve van
    * Figyelje meg, hogy a régió, ahol a kiszolgáló lett telepítve. 

## <a name="sync-tenant-configuration-into-catalog"></a>Szinkronizálási bérlő konfigurációjához-katalógusba

Ebben a feladatban egy folyamat, amely a kiszolgálók, a rugalmas készletek és az adatbázisok konfigurációját a bérlő katalógusba való szinkronizálásának megkezdése. A folyamat tartja ezt az információt a katalógus naprakész.  A folyamat működik a aktív katalógust és az eredeti régióban található, vagy a helyreállítási régióban. A konfigurációs adatok, a helyreállítási környezet biztosításához a helyreállítási folyamat során az eredeti környezettel következetes, és majd később biztosításához hazatelepítési során az eredeti régió konzisztenssé a végrehajtott módosításokat a a helyreállítási környezet. A katalógus segítségével is nyomon követheti, bérlői erőforrások helyreállítási állapota

> [!IMPORTANT]
> Az egyszerűség kedvéért a szinkronizálási folyamat és a más helyreállítási és hazatelepítési hosszú ideig futó folyamatok szereplő helyi Powershell feladatok vagy az ügyfél felhasználói bejelentkezés alatt futó munkamenetek is érvényes. A hitelesítési tokenek ki, ha először jelentkezik több óra elteltével lejár, és a feladat majd sikertelen lesz. Egy éles telepítési forgatókönyvhöz, a hosszú futású folyamatokat valamilyen, egyszerű szolgáltatás alatt futó megbízható Azure szolgáltatásként kell végrehajtani. Lásd: [a Azure PowerShell szolgáltatás használatával hozzon létre egy egyszerű tanúsítvány](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. Az a _PowerShell ISE_, nyissa meg a ...\Learning Modules\UserConfig.psm1 fájlt. Cserélje le `<resourcegroup>` és `<user>` 10-es és 11 az értéket, az alkalmazás üzembe helyezésekor a sorban.  Mentse a fájlt!

2. Az a *PowerShell ISE*, nyissa meg a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be:
    * **$DemoScenario = 1**bérlői server Szinkronizáló háttér feladat indítása és tárolókészlet konfigurációs adatait a katalógusba

3. Nyomja le az **F5** a szinkronizálási parancsfájl futtatásához. Egy új PowerShell-munkamenetet a bérlői erőforrásokhoz konfigurációjának szinkronizálása van megnyitva.
![Szinkronizálási folyamat](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Hagyja meg a PowerShell ablakban fut a háttérben, és a többi részének az oktatóanyag folytatásához. 

> [!Note]
> A szinkronizálási folyamat a katalógus DNS-alias keresztül kapcsolódik. Ez az alias visszaállítása és az aktív katalógus mutasson hazatelepítési során is módosul. A szinkronizálási folyamat tartja a katalógus naprakész az adatbázis vagy a tárolókészlet konfigurációs módosítások helyreállítási régióban.  A hazatelepülés során ezeket a módosításokat az eredeti régióban egyenrangú erőforrásokkal is vonatkozik.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>A helyreállítási régióban másodlagos adatbázis-replikák létrehozása

Ebben a feladatban indítsa el a folyamat, amely egy duplikált alkalmazás ezen példányát telepíti, és a katalógus, illetve az összes bérlői adatbázis replikálja egy helyreállítási területet.

> [!Note]
> Ez az oktatóanyag georeplikáció védelem hozzáadása a Wingtip jegyek mintaalkalmazást. A georeplikáció használó alkalmazások éles forgatókönyvben mindegyik bérlő volna el van látva kezdettől fogva georeplikált adatbázis. Lásd: [Azure SQL Database használata magas rendelkezésre állású szolgáltatások tervezése](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. Az a *PowerShell ISE*, nyissa meg a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következő értékeket:
    * **$DemoScenario = 2**, hozzon létre tükrözött lemezképét helyreállítási környezetet, és a katalógus és a bérlői adatbázisok replikálása

2. A szkriptek futtatásához nyomja le az **F5** billentyűt. Új PowerShell-munkamenetben hozza létre a replikákat van megnyitva.
![Szinkronizálási folyamat](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Tekintse át a normál alkalmazási állapota
Ezen a ponton az alkalmazás eredeti régióban megfelelően fut, és most védik a georeplikáció.  Csak olvasható másodlagos replika, az összes adatbázis helyreállítási régióban található. 
1. Az Azure portálon, tekintse meg az erőforrás-csoportok és vegye figyelembe, hogy egy erőforráscsoport jött létre - helyreállítási utótag helyreállítási régióban. 

1. Fedezze fel az erőforrások helyreállítási erőforráscsoportban.  

1. Kattintson a Contoso energiaoptimalizálást egyszerre Hall adatbázison a a _tenants1-dpt -&lt;felhasználói&gt;-helyreállítási_ kiszolgáló.  A bal oldalon kattintson a Georeplikáció. 

    ![Contoso energiaoptimalizálást egyszerre georeplikáció hivatkozás](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Az Azure-régiók térkép jegyezze fel a georeplikáció hivatkozás az eredeti régióban az elsődleges és a másodlagos helyreállítási régióban között.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Feladatok átadása a helyreállítási régióba alkalmazás

### <a name="geo-replication-recovery-process-overview"></a>A georeplikáció a helyreállítási folyamat áttekintése

A helyreállítási parancsfájlt a következő feladatokat hajtja végre:

1. Letiltja a Traffic Manager-végpont a webalkalmazás az eredeti régióban. A végpont letiltása megakadályozza, hogy a felhasználók csatlakozni az alkalmazás állapota érvénytelen az eredeti régió érkezik online helyreállítás során.

1. Használja a katalógus adatbázis kényszerített feladatátvétel helyreállítási régióban abba, hogy az elsődleges adatbázissal, és frissíti a _activecatalog_ alias, hogy a helyreállítási kiszolgáló mutasson.

1. Frissítések a _newtenant_ alias, hogy a helyreállítási régióban a bérlő kiszolgálóra mutasson. Ez az alias módosítása biztosítja, hogy az új tenantokat adatbázisok helyreállítási régióban törlődnek. 

1. Jelöli meg az összes meglévő bérlők számára a helyreállítási katalógus offline állapotúként kívánja tagadni a hozzáférést bérlői adatbázisok ahhoz, azok a feladatátvételt.

1. Frissíti az összes rugalmas készletek és a helyreállítási régióban mappába történő tükrözésének eredeti régióban konfigurációjuk replikált önálló adatbázisok konfigurációját. (Ez a feladat csak akkor szükséges, ha a készletek és a helyreállítási környezetben replikált adatbázisok méretezése költségek csökkentése érdekében a normál működés során).

1. Lehetővé teszi, hogy a webalkalmazás a helyreállítási régióban Traffic Manager-végpontot. Ez a végpont lehetővé teszi, hogy az alkalmazás telepítéséhez az új bérlők számára. Ebben a szakaszban a meglévő bérlőkre is offline állapotú.

1. Elküldi a kötegek kérelmek kényszerített feladatátvétel adatbázisok prioritási sorrendben.
    * Kötegek vannak rendezve, hogy az adatbázisok feladatátvétel történt párhuzamosan készletekben között.
    * Feladatátvételi kérelmek elküldi az aszinkron műveletek használ, így gyorsan rájuk, és több kérelmek feldolgozási egyidejűleg.

   > [!Note]
   > Egy kimaradás esetén az elsődleges adatbázis eredeti régióban offline módban.  Kényszerített feladatátvételt a másodlagos megszakítására vonatkozó a kapcsolatot az elsődleges anélkül, hogy alkalmazza a maradék várakozó tranzakciók. Vész-Helyreállítási részletezési esetén például ez az oktatóanyag minden frissítés tevékenység esetén a feladatátvétel időpontjában lehet adatvesztést. Később során hazatelepítési, ha nem sikerül a helyreállítás régióban vissza az eredeti terület, az adatbázis szokásos feladatátvevő szolgál gondoskodjon arról, hogy adatvesztés nélküli.

1. Az SQL-adatbázis szolgáltatást annak meghatározásához, amikor adatbázisok lett átvette figyeli. Miután egy bérlő adatbázison végrehajtott feladatátvételt, jegyezze fel a bérlő adatbázis helyreállítási állapotát, és jelölje be a bérlő az online katalógus frissíti.
    * Bérlői adatbázisok hozzáférhet az alkalmazáshoz, amint online, a katalógus van megjelölve.
    * A tenant adatbázisban rowversion értékek összegét a katalógus tárolja. Ez az érték, amely lehetővé teszi a hazatelepítési folyamat határozza meg, ha az adatbázis frissítése a helyreállítási régióban ujjlenyomattal funkcionál.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Futtassa a parancsfájlt, hogy áthelyezze a feladatokat a helyreállítási régióban

Most tegyük fel, a régióban, amelyben az alkalmazás üzemel, és a helyreállítási parancsprogrammal kimaradás van:

1. Az a *PowerShell ISE*, nyissa meg a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következő értékeket:
    * **$DemoScenario = 3**, az alkalmazás által a replikák feladatátvételét olyan helyreállítási régió helyreállítása

2. A szkriptek futtatásához nyomja le az **F5** billentyűt.  
    * A parancsfájl egy új PowerShell-ablakban nyílik meg, és elindítja a párhuzamosan futó PowerShell feladatok sorozata. Ezeket a feladatokat a helyreállítási régióban bérlői adatbázisok feladatátvételt.
    * A helyreállítási régió a _párosított régió_ társított Azure-régiót, amelyben az alkalmazás telepítése. További információkért lásd: [Azure párosítva régiók](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions). 

3. A PowerShell-ablakban a helyreállítási folyamat állapotának figyelése.
    ![Feladatátvételi folyamat](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> A helyreállítási feladatok kódja megismeréséhez tekintse át a PowerShell-parancsfájlok a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-FailoverToReplica\RecoveryJobs mappában.

### <a name="review-the-application-state-during-recovery"></a>A helyreállítás során állapotának áttekintése
Az alkalmazás végpont le van tiltva a Traffic Managerben, amíg az alkalmazás nem érhető el. Miután a katalógus átadja a helyreállítási régió, és a bérlők megjelölve offline, az alkalmazás nem válik újra elérhetővé. Bár az alkalmazás nem érhető el, mindegyik bérlő jelenik meg az események központban offline mindaddig, amíg az adatbázison végrehajtott feladatátvételt. Fontos alkalmazás offline bérlői adatbázisok kezelésének megtervezése.

1. Azonnal a katalógus-adatbázis a helyreállítás után frissítse a Wingtip jegyek események Hub a böngészőben.
    * A lábrészben megjelenő figyelje meg, hogy rendelkezik-e már a kiszolgáló katalógusnév egy _-helyreállítási_ utótag és a helyreállítási régióban található.
    * Figyelje meg, hogy a bérlők, amelyek nem lettek visszaállítva, offline állapotban van megjelölve, és nem választható.  

    > [!Note]
    > Csak néhány adatbázisok helyreállításához akkor nem lehet lehet csatlakozni a böngésző előtt helyreállítási befejeződött, így előfordulhat, hogy nem látja a bérlők kapcsolat nélküli módban. 
 
    ![Kapcsolat nélküli események hub](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

    * Ha közvetlenül az offline bérlő események lapot megnyitni, egy "offline bérlői" értesítést jelenít meg. Például a Contoso energiaoptimalizálást egyszerre Hall offline állapotban, ha megpróbálja megnyitni http://events.wingtip-dpt.&lt; felhasználói&gt;.trafficmanager.net/contosoconcerthall ![Contoso Offline lap](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Egy új bérlőt a helyreállítási régióban kiépítése
Még a bérlő összes létező adatbázis felett sikertelen, mielőtt új bérlők a régióban helyreállítási oszthat meg.  

1. Az a *PowerShell ISE*, nyissa meg a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következő tulajdonságot:
    * **$DemoScenario = 4**, egy új bérlőt a helyreállítási régióban kiépítése

2. Nyomja le az **F5** futtassa a parancsfájlt, és az új tenanthoz kiépítéséhez. 

3. A böngészőben megnyílik a Hawthorn Hall események lap, amikor elkészült. A lábléc megjegyzései, a Hawthorn Hall adatbázis helyreállítási régióban van regisztrálva.
    ![Hawthorn Hall események lap](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. A böngészőben frissítse a Wingtip jegyek események Hub lapot, melyen megtekintheti, Hawthorn Hall tartalmazza. 
    * Kiépített Hawthorn Hall visszaállítása a többi bérlő várakozás nélkül, ha más bérlők előfordulhat, hogy még mindig offline állapotban van.


## <a name="review-the-recovered-state-of-the-application"></a>Tekintse át a helyreállított az alkalmazás állapota

A helyreállítási folyamat befejezése után az alkalmazás és minden bérlők teljesen működőképes helyreállítási területen. 

1. A PowerShell-konzolablakot megjelenítését azt jelzi, hogy a bérlők helyreállítás, miután az események Hub frissítése  A bérlők összes jelenik meg, beleértve az új tenanthoz Hawthorn Hall online.

    ![az események központban helyreállított és az új bérlők](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Az a [Azure-portálon](https://portal.azure.com), az erőforráscsoport-sablonok listájának megnyitásához.  
    * Figyelje meg a központilag telepített, a helyreállítási erőforráscsoport, valamint az erőforráscsoportot a _-helyreállítási_ utótag.  A helyreállítási erőforráscsoport összes a helyreállítási folyamat során létrejött erőforrásokat, valamint a szolgáltatáskimaradás elhárítása során létrehozott új erőforrásokat tartalmazza.  

3. Nyissa meg a helyreállítási erőforráscsoportot, és figyelje meg a következő elemek:
    * A katalógus és tenants1 kiszolgálók helyreállítási verziói a _-helyreállítási_ utótag.  A visszaállított katalógus és a bérlői adatbázisok ezeken a kiszolgálókon minden eredeti régióban használt nevekkel rendelkeznek.

    * A _tenants2-dpt -&lt;felhasználói&gt;-helyreállítási_ SQL server.  Ezt a kiszolgálót az új bérlők lépnek a szolgáltatáskimaradás elhárítása során használja.
    *   Az App Service nevű, _események-wingtip-dpt -&lt;recoveryregion&gt;-&lt;felhasználó & gt_; amely az események alkalmazás helyreállítási példányát. 

    ![Az Azure recovery erőforrások ](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png)    
    
4. Nyissa meg a _tenants2-dpt -&lt;felhasználói&gt;-helyreállítási_ SQL server.  Figyelje meg az adatbázis tartalmaz _hawthornhall_ és a rugalmas készlet _Pool1_.  A _hawthornhall_ egy olyan rugalmas adatbázis az adatbázis lesz beállítva _Pool1_ rugalmas készlet.

5. Lépjen vissza az erőforráscsoportot, majd kattintson a Contoso energiaoptimalizálást egyszerre Hall adatbázison a a _tenants1-dpt -&lt;felhasználói&gt;-helyreállítási_ kiszolgáló. A bal oldalon kattintson a Georeplikáció.
    
    ![Contoso adatbázis feladatátvétel után](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Bérlői adatok módosítása 
Ebben a feladatban frissíti egy bérlő adatbázis. 

1. A böngészőben az eseménylista keresése a Contoso energiaoptimalizálást egyszerre Hall, és jegyezze fel az utolsó esemény nevét.
2. Az a *PowerShell ISE*, Modules\Business folytonossága és vészhelyreállítás Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájl ...\Learning állítson be a következő értéket:
    * **$DemoScenario = 5** helyreállítási régióban bérlőtől esemény törlése
3. Nyomja le az **F5** a parancsfájl végrehajtása
4. Frissítse a Contoso energiaoptimalizálást egyszerre Hall események lapot (http://events.wingtip-dpt.&lt; felhasználói&gt;.trafficmanager.net/contosoconcerthall - helyettesítse &lt;felhasználói&gt; a központi telepítés felhasználói értékkel), és figyelje meg, hogy a legutóbbi esemény törölve lett.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Az alkalmazás eredeti éles régiója települni

Ez a feladat az alkalmazás eredeti régiója repatriates. Valós forgatókönyv esetében hazatelepítési volna kezdeményezni, a kimaradást okozó megoldásakor.

### <a name="repatriation-process-overview"></a>Hazatelepítési folyamat áttekintése

![Hazatelepítési architektúrája](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

A hazatelepítési folyamat:
1. Megszakítja a függőben lévő vagy üzenetsoroktól adatbázis visszaállítási kéréseit.
2. Frissítések a _newtenant_ alias, hogy a forrás régióban a bérlők kiszolgálóra mutasson. Ez az alias módosítása biztosítja, hogy az adatbázisok új tenantokat most megkapják a származási régióban.
3. Magok a módosított tenant adatokat az eredeti régió
4. Feladatátadás a bérlő adatbázisok prioritási sorrendben.

Feladatátvételi hatékonyan helyezi át az adatbázist az eredeti régió. Ha az adatbázis átadja, bármely nyitott kapcsolatok megszakadnak, és az adatbázis nem érhető el, néhány másodpercig. Alkalmazások az újrapróbálkozási logika ahhoz, azok csatlakozzon újra kell írni.  Bár ez rövid disconnect gyakran nem első fellépése, választhatja munkaidőn kívülre adatbázisok települni. 


### <a name="run-the-repatriation-script"></a>A hazatelepítési parancsprogrammal
Most tegyük képzelhető el, a kimaradást okozó megoldása és a hazatelepítési parancsprogrammal.

1. Az a *PowerShell ISE*, a ...\Learning Modules\Business folytonossága és vészhelyreállítás Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájl.

2. Győződjön meg arról, hogy a katalógus szinkronizálási folyamat továbbra is fut a PowerShell-példányban.  Ha szükséges, indítsa újra úgy, hogy:
    * **$DemoScenario = 1**, bérlői server, a készlet és az adatbázis konfigurációs adatainak szinkronizálása a katalógusba való indítása
    * A szkriptek futtatásához nyomja le az **F5** billentyűt.

3.  Ezután a hazatelepítési megkezdéséhez beállítása:
    * **$DemoScenario = 6**, az alkalmazás települni az eredeti régióba
    * Nyomja le az **F5** egy új PowerShell-ablakban a helyreállítási parancsfájl futtatásához.  Hazatelepítési néhány percet vesz igénybe, és figyelhetők a PowerShell-ablakban.
    ![Hazatelepítési folyamat](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. A parancsfájl futása közben, az események Hub lap frissítése (http://events.wingtip-dpt.&lt; felhasználói&gt;. trafficmanager.net)
    * Figyelje meg, hogy a bérlők online állapotúak és elérhetők a folyamat során.

5. Áll befejezése után, az események központ frissítése, és nyissa meg a Hawthorn Hall események lapját. Figyelje meg, hogy az adatbázis eredeti régió fogalommeghatározás-e.
    ![Események hub fogalommeghatározás](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Az alkalmazás az alkalmazás- és biztosításához tervezése közös elhelyezésű 
Az alkalmazás célja, hogy mindig kapcsolódik egy példányával, mivel a bérlői adatbázis ugyanabban a régióban. Ez a kialakítás csökkenti a késést, az alkalmazás és az adatbázis között. Az optimalizálás feltételezi a app adatbázis-kapcsolati chattier, mint a felhasználó alkalmazáson belüli kommunikáció.  

Bérlői adatbázisok hazatelepítési során egy kis ideig eljutni helyreállítási és az eredeti régiók között. Az egyes adatbázisok az alkalmazás a régió, ahol az adatbázis található egy DNS-címkeresés a kiszolgáló nevére bérlői végrehajtásával keres. Az SQL-adatbázis a kiszolgáló neve, egy aliast. Az alias-kiszolgáló neve a régió nevét tartalmazza. Ha az alkalmazás nem az adatbázis ugyanabban a régióban, átirányítja a példány az adatbázis-kiszolgáló ugyanabban a régióban.  Az adatbázis ugyanabban a régióban példány átirányítása minimálisra csökkenti az alkalmazás- és közötti késés. 

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:
> [!div classs="checklist"]

>* Sync-adatbázis és a bérlői katalógusba rugalmas konfigurációs adatait
>* A helyreállítási környezet egy másik régióban, alkalmazás, a kiszolgálók és a készletek beállítása
>* Használjon _georeplikáció_ replikálni az alkalmazáskatalógus és a bérlői adatbázisokat a helyreállítási régió
>* Az alkalmazás és az alkalmazáskatalógus és a bérlői adatbázisok helyreállítási régió feladatátvételt 
>* A feladat-visszavételt az alkalmazás, a katalógus és a bérlői adatbázisokat az eredeti régió megoldódott a szolgáltatáskimaradás elhárítása után

Azure SQL database is tartalmaz ahhoz, hogy az üzletmenet folytonossága technológiákkal kapcsolatos többet is megtudhat a [üzleti folytonosság – áttekintés](sql-database-business-continuity.md) dokumentációját.

## <a name="additional-resources"></a>További források

* [További oktatóprogramot kínál, amelyek a Wingtip SaaS-alkalmazás épül](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
