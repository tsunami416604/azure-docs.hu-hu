---
title: "Több-bérlős Szolgáltatottszoftver-oktatóanyag – az Azure SQL Database |} Microsoft Docs"
description: "Központi telepítése, és vizsgálja meg a Wingtip jegyek SaaS-több-bérlős alkalmazás, azt mutatja be, az adatbázis a bérlők és az egyéb Azure SQL Database használata Szolgáltatottszoftver-minták száma."
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: sstein
ms.openlocfilehash: 9b1ae219eb1278b818e3e1d4237d04fe54c980ec
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Központi telepítése, és vizsgálja meg az adatbázis típusú bérlői mintában használja az Azure SQL Database több-bérlős SaaS-alkalmazás

Ebben az oktatóanyagban telepíti, és megismerkedhet a Wingtip jegyek SaaS adatbázis bérlői alkalmazásonként. Az alkalmazás egy adatbázis-/-bérlő mintát használ, több bérlő adatainak tárolásához. Az alkalmazás szolgál, lehetővé teszi Szolgáltatottszoftver-forgatókönyvek egyszerűsítő szolgáltatásairól az Azure SQL Database megjelenítve.

Öt perccel az alábbi *Deploy to Azure* (Üzembe helyezés az Azure-ban) gombra kattintás után már üzemelni és futni fog a felhőben egy SQL Database-t használó, több bérlős SaaS-alkalmazás. A három minta bérlőkkel, mindegyiket a saját adatbázis, a rugalmas SQL-készlet összes üzembe helyezett telepítik az alkalmazást. Az alkalmazás telepítve van az Azure-előfizetéshez, felkínálva vizsgálatát, és az egyéni alkalmazás-összetevők együttműködve a teljes hozzáférést. Az alkalmazás forrás kód és a felügyeleti parancsfájlok a WingtipTicketsSaaS-DbPerTenant GitHub-tárház érhetők el.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * A Wingtip SaaS-alkalmazás központi telepítése
> * Honnan szerezhetők be az alkalmazás forráskódjához, és a parancsfájlok
> * Az alkalmazást alkotó kiszolgálók, készletek és adatbázisok
> * Hogyan bérlők vannak leképezve az adataikat a *katalógus*
> * Egy új bérlőt kiépítése
> * Bérlői tevékenységeket az alkalmazásban figyelése

A különböző SaaS-kialakításokat és felügyeleti mintákat [egy sor kapcsolódó oktatóanyag](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) ismerteti, amelyek erre az eredeti üzemelő példányra épülnek. Az oktatóanyagok tanulmányozása közben merüljön el a megadott szkriptekben, és vizsgálja meg a különböző SaaS-minták megvalósításának módjait. Haladjon végig az egyes oktatóanyagokban található szripteken, hogy jobban megértse, hogyan használható az SQL Database számos olyan funkciója, amelyek egyszerűbbé teszik az SaaS-szolgáltatások kifejlesztését.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-saas-application"></a>A Wingtip jegyek SaaS-alkalmazás központi telepítése

Az alkalmazás központi telepítése:

1. Kattintson a **az Azure telepítéséhez** gombra kattintva megnyílik a Wingtip jegyek SaaS adatbázishoz bérlő központi telepítési sablont az Azure portálon. A sablon csak két paraméter; Új erőforráscsoport nevét, és a felhasználónevet, amely megkülönbözteti a központi telepítés a Wingtip jegyek SaaS adatbázis bérlői alkalmazásonkénti más központi telepítésektől. A következő lépés az értékek beállításával részletes adatokat biztosít.

   Győződjön meg arról, a pontos értékek, amelyekkel, mert meg kell adnia azokat egy konfigurációs fájlba később.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Adja meg az üzembe helyezéshez szükséges paraméterértékeket:

    > [!IMPORTANT]
    > A bemutató céljából bizonyos hitelesítési lépések és kiszolgálói tűzfalak beállítása szándékosan nem biztonságos. **Hozzon létre egy új erőforráscsoportot**. Ne használjon meglévő erőforráscsoport-sablonok, kiszolgálóknak vagy készletek. Ne használja ezt az alkalmazást, parancsprogramok vagy bármely telepített erőforrások termelési környezetben. Ha végzett az alkalmazással, törölje a létrehozott erőforráscsoportot a kapcsolódó számlázások leállításához.

    * **Erőforráscsoport** – Itt adhatja meg **hozzon létre új** , és adjon meg egy **neve** ahhoz az erőforráscsoporthoz. 
    * **Hely** – Itt adhatja meg a **hely** a legördülő listából.
    * **Felhasználói** -bizonyos globálisan egyedi neveit kell. Ahhoz, hogy egyediségi, minden egyes telepítéshez az alkalmazás értéket adjon meg egy hoz létre, a Wingtip alkalmazás bármilyen más központi telepítésének által létrehozott erőforrásokból erőforrások megkülönböztetésére. Egy rövid használandó javasoljuk **felhasználói** nevét, például a saját monogramjával, plusz egy szám (például *af1*), és akkor használja azt az erőforráscsoport neve (például *wingtip-af1*). A **felhasználói** paraméter csak tartalmazhat betűket, számokat és kötőjeleket tartalmazhat (szóközök nélkül). Az első és az utolsó karakternek betűnek vagy számnak kell lennie (emellett ajánlott csupa kisbetűket használni).


1. **Az alkalmazás üzembe helyezése**.

    * Kattintson a gombra kattintva elfogadja a feltételeket és kikötéseket.
    * Kattintson a **Purchase** (Vásárlás) gombra.

1. Az üzembe helyezés állapotának figyeléséhez kattintson az **Értesítések** ikonra (a harangra a keresőmező mellett jobbra). A Wingtip jegyek SaaS-alkalmazás telepítése, körülbelül öt percet vesz igénybe.

   ![üzembe helyezés sikeres](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Töltse le és a Wingtip jegyek felügyeleti parancsfájlok feloldása

Amíg az alkalmazás telepítése van, töltse le a forrás kódot és kezelésre szolgáló parancsfájlok.

> [!IMPORTANT]
> Ha egy zip-fájl külső forrásból letöltött és kibontott végrehajtható tartalma (parancsfájlok, DLL-ek) blokkolhatja Windows. Ha a parancsfájlok kibontása zip-fájlban, az alábbi lépésekkel tiltásának feloldása a .zip fájl kibontása előtt. Ez biztosítja, hogy a parancsfájlok futtatásának engedélyezése.

1. Keresse meg a [WingtipTicketsSaaS-DbPerTenant GitHub-tárház](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
1. Kattintson a **Klónozás vagy letöltési**.
1. Kattintson a **töltse le a ZIP-** , és mentse a fájlt.
1. Kattintson a jobb gombbal a **WingtipTicketsSaaS-DbPerTenant-master.zip** fájlt, és válassza ki **tulajdonságok**.
1. Az a **általános** lapon jelölje be **Unblock**, és kattintson a **alkalmaz**.
1. Kattintson az **OK** gombra.
1. Bontsa ki a fájlokat.

Parancsfájlok a találhatók a *... \\WingtipTicketsSaaS főkiszolgálós DbPerTenant\\tanulási modulok* mappát.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>A központi telepítés a felhasználó-konfigurációs fájl frissítése

Bármely-parancsfájlok futtatásakor, előtt frissítse a *erőforráscsoport* és *felhasználói* értékei **UserConfig.psm1**. Ezeket a változókat beállítva a központi telepítése során használt értékekre.

1. Az a *PowerShell ISE*, nyissa meg... \\Tanulási modulok\\*UserConfig.psm1* 
1. Frissítés *ResourceGroupName* és *neve* a központi telepítés (sorok 10-es és 11 csak) az adott értékkel.
1. A módosítások mentése!

Majdnem minden parancsfájl hivatkozott ezeket az értékeket.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás helyszínek, például koncerttermek jazz treff vagy események üzemeltető sportegyesületek céloz meg. Helyszínek felhasználók (vagy bérlők) Wingtip jegyek események listában, és értékesítés jegyek egyszerűen regisztrálni. Egyes helyszínekkel lekérdezi egy személyre szabott webhely kezelése és a listában azok az események és a jegyet, független és elkülönítése a bérlőktől értékesítés. A színfalak mindegyik bérlő lekérdezi a rugalmas SQL-készlet helyezett SQL-adatbázis.

Egy központi **események Hub** lapra mutató hivatkozásokat biztosít a környezetben a bérlők számára.

1. Nyissa meg a _események Hub_ a böngészőben: http://events.wingtip-dpt.&lt;felhasználói&gt;. trafficmanager.net (helyettesítse &lt;felhasználói&gt; a központi telepítés felhasználói érték):

    ![eseményközpont](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Kattintson a **Fabrikam Jazz Club** elemre az *eseményközpontban*.

   ![Események](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)


A bejövő kérelem, az alkalmazás által használt eloszlás [ *Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Az eseményoldalak bérlőnként eltérőek, ezért az URL-címekben szerepeltetni kell a bérlők nevét. A bérlő URL-címek közé tartozik az adott *felhasználói* értékét, és ezt a formátumot követi: http://events.wingtipp-dpt.&lt;felhasználói&gt;.trafficmanager.net/*fabrikamjazzclub*. Az események alkalmazás elemzi a bérlő nevét az URL-címről, és hozzon létre egy kulcsot a katalógus készletével megvalósított eléréséhez használja [ *shard térkép felügyeleti*](sql-database-elastic-scale-shard-map-management.md). A katalógus van leképezve a kulcsot a bérlő adatbázis helye. A **események Hub** kiterjesztett metaadatokat használ a katalógus társított minden egyes adatbázis adataival kínálnak az URL-címek listájának a bérlő nevének beolvasására.

Éles környezetben általában egy CNAME DNS-rekordot fog létrehozni, amely [*a vállalati internettartományt*](../traffic-manager/traffic-manager-point-internet-domain.md) a Traffic Manager-profilra irányítja.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy az alkalmazás telepítését, most használatba vétel! A *bemutató-LoadGenerator* PowerShell-parancsfájl elindítja a munkaterhelés futtatott összes bérlői adatbázis. Sok Szolgáltatottszoftver-alkalmazásoknál valós terhelése jellemzően szórványos és előre nem látható. Szimulálása a terhelésének típusát, a kódgenerátor összes bérlőre, az egyes bérlők, véletlenszerű időközönként előforduló véletlenszerű felszakadásáig az elosztott terhelésű hoz létre. Emiatt a terhelés minta bontakozik több percet vesz igénybe ezért érdemes lehetővé teszik a generátor legalább három vagy négy percig figyelése a betöltés előtt futtassa.

1. Az a *PowerShell ISE*, nyissa meg a... \\Tanulási modulok\\segédprogramok\\*bemutató-LoadGenerator.ps1* parancsfájl.
1. Nyomja le az **F5** billentyűt a szkript futtatásához és a terhelésgenerátor indításához (egyelőre nem módosítsa az alapértelmezett paraméterértékeket).

> [!IMPORTANT]
> További parancsfájlok futtatásához nyisson meg egy új PowerShell ISE-ablakot. A terhelésgenerátor feladatok sorozataként fut a helyi PowerShell-munkamenetben. A *bemutató-LoadGenerator.ps1* parancsfájl másolattól a tényleges betöltést generátor parancsfájlt, az előtérben feladat és a feladatok a háttérben terhelés generációs sorozatát futtatja. Egy terhelés-készítő feladat meghívták az egyes adatbázisok a katalógusban regisztrált. A feladatok a helyi PowerShell-munkamenetben futnak, így a PowerShell-munkamenet lezárása leállítja az összes feladat. Ha felfüggeszti a gép, terhelés szüneteltetve van, és folytatódik, amint azt felébreszteni a gép.

Amennyiben a betöltés generátor terhelés generációs feladatok hív meg az egyes bérlők számára, az előtérben feladat állapotban marad feladat meghívása, ahol további háttérfeladatok elindítja a bármely új bérlők ezt követően törlődnek. Használhatja *Ctrl-C* vagy nyomja meg a *leállítása* gombra kattint, hogy az előtér-feladat leállítása, de a meglévő feladatok a háttérben folytatódik létrehozása minden egyes adatbázis terhelését. Ha szeretné figyelni és a feladatok a háttérben kontrolljához használja a *Get-Job*, *Receive-Job* és *feladat leállításával*. Az előtér-feladat futása közben további parancsfájlok végrehajtása az ugyanazon PowerShell-munkamenetben nem használható. További parancsfájlok futtatásához nyisson meg egy új PowerShell ISE-ablakot.

Ha szeretné újraindítani a terhelés generátor munkamenet, például más paraméterekkel, leállíthatja az előtérben futó feladat és futtassa újra a *bemutató-LoadGenerator.ps1* parancsfájl. Ismételt futtatásával *bemutató-LoadGenerator.ps1* első leállítja a futó feladatok, és újraindítja a a generátor, amely az aktuális paraméterek használó feladatok új készletét másolattól.

A lépést a betöltés generátor fut a feladat meghívása állapotban hagyja.


## <a name="provision-a-new-tenant"></a>Új bérlő kiépítése

A kezdeti telepítés három minta bérlők hoz létre, de hozzon létre egy másik bérlői megtekintéséhez, hogy ez hatással van a telepített alkalmazás. A Wingtip jegyek Szolgáltatottszoftver-kiépítés bérlők munkafolyamat részletes a [biztosítása és a katalógus oktatóanyag](saas-dbpertenant-provision-and-catalog.md). Ebben a lépésben gyorsan létrehozhat egy új bérlőt.

1. Az a *PowerShell ISE*, nyissa meg... \\Modules\Provision és a katalógus\\*bemutató-ProvisionAndCatalog.ps1* .
1. Nyomja le az **F5** a parancsfájl futtatásához (hagyja meg az alapértelmezett értékeket most).

   > [!NOTE]
   > Sok Wingtip Szolgáltatottszoftver-parancsfájlok használata *$PSScriptRoot* mappák függvényeinek meghívása más parancsfájlokban történő megnyitása. Ez a változó csak akkor értékeli ki, ha a teljes parancsfájl végrehajtása billentyűkombináció lenyomásával **F5**.  Kiemelése és futtató egy kijelölést (**F8**) is hibákat eredményez, így nyomja le az **F5** Ha a parancsfájlok futtatásához.

Az új bérlő adatbázis a rugalmas SQL-készletet létrehozni, inicializálása és a katalógus regisztrálni. Sikeres kiépítése, az új tenanthoz után *események* hely jelenik meg a böngészőben:

![Új bérlő](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Frissítse a *események Hub* és az új tenanthoz most megjelenik a listában.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>A kiszolgálók, készletek és bérlői adatbázisok megismerése

Most, hogy egy futtatott bérlők gyűjteménye már elkezdte, vizsgáljuk meg a telepített erőforrások:

1. Az a [Azure-portálon](http://portal.azure.com), keresse meg az SQL Server-kiszolgálók listáját, és nyissa meg a **katalógus-dpt -&lt;felhasználói&gt;**  kiszolgáló. A kiszolgáló tartalmazza a két adatbázis, a **tenantcatalog** és a **basetenantdb** (sablon adatbázis létrehozására az új bérlők másolt).

   ![adatbázisok](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Lépjen vissza az SQL Server-kiszolgálók listáját, és nyissa meg a **tenants1-dpt -&lt;felhasználói&gt;**  kiszolgáló, amely a bérlői adatbázisok. Minden egyes bérlő adatbázis egy _Standard rugalmas_ adatbázis egy standard 50 eDTU-készlet. Is láthatja a van egy _piros Maple Racing_ adatbázis, a korábban kiépített bérlői adatbázis.

   ![kiszolgáló](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>A készlet figyelése

Ha a terhelésgenerátor már fut néhány perce, elegendő adat áll rendelkezésre, hogy elkezdhessük megvizsgálni a készletekbe és adatbázisokba épített figyelőfunkciókat.

1. Keresse meg a kiszolgáló **tenants1-dpt -&lt;felhasználói&gt;**, és kattintson a **Pool1** a gyűjtő (a betöltési generátor lefutott egy óráig a következő diagramokban) erőforrás-használat megtekintéséhez:

   ![készlet figyelése](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

A felső diagram ábrázolja a készlet edtu-k használatát, amíg alsó ábrázolja a felső 5 adatbázisok eDTU utiization a készletben.  A két diagram tökéletesen illusztrálja, milyen jól alkalmazkodnak a rugalmas készletek és az SQL Database az SaaS-alkalmazások munkaterheléseihez. Négy, egyenként akár 40 eDTU magas terheléstüskékkel rendelkező adatbázist együtt könnyedén kiszolgál egy 50 eDTU méretű készlet. Ha önálló adatbázisok, telepített, akkor minden kell lenniük egy S2 ugyanúgy (50 DTU) az felszakadásáig támogatásához. 4 önálló S2 adatbázisok költségeinek a készlet ár majdnem 3-szor, és a tárolókészlet még számos további adatbázisok belső magasságnak rengeteg. Valós helyzetekben SQL-adatbázis ügyfelek jelenleg futnak legfeljebb 500 adatbázisok 200 eDTU-készletek. További információkért lásd a [teljesítményfigyeléssel foglalkozó oktatóanyagot](saas-dbpertenant-performance-monitoring.md).


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]

> * A Wingtip jegyek SaaS-alkalmazás központi telepítése
> * Az alkalmazást alkotó kiszolgálók, készletek és adatbázisok
> * Bérlők társítása az adataikhoz a *katalógus* használatával
> * Új bérlők kiépítése
> * A bérlők tevékenységének megfigyelése a készlet kihasználtságának nyomon követésével
> * Mintaerőforrások törlése a kapcsolódó számlázások leállításához

Most a [biztosítása és a katalógus oktatóanyag](saas-dbpertenant-provision-and-catalog.md).



## <a name="additional-resources"></a>További források

* További [oktatóprogramot kínál, amelyek a Wingtip jegyek SaaS adatbázis bérlői alkalmazásonként létrehozása](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* A rugalmas készletekkel kapcsolatos tudnivalókért lásd [*a rugalmas Azure SQL-készleteket*](sql-database-elastic-pool.md) ismertető cikket.
* A rugalmas feladatokkal kapcsolatos tudnivalókért lásd a [*horizontálisan felskálázott felhőalapú adatbázisok kezelését*](sql-database-elastic-jobs-overview.md) ismertető cikket.
* A több-bérlős SaaS-alkalmazásokkal kapcsolatos tudnivalókért lásd a [*több-bérlős SaaS-alkalmazások tervezési mintáit*](saas-tenancy-app-design-patterns.md)
