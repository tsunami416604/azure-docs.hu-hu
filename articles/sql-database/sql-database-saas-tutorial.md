---
title: "A Wingtip Tickets Platform (WTP) alkalmazás üzembe helyezése és megismerése (az Azure SQL Database-t használó minta SaaS-alkalmazás) | Microsoft Docs"
description: "Egy Azure SQL Database-t használó minta SaaS-alkalmazás üzembe helyezése és megismerése"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ff4dc19bb6e24ea9ceeed9721cfb3a85b4d10965
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-azure-sql-database"></a>Egy Azure SQL Database-t használó több bérlős SaaS-alkalmazás üzembe helyezése és megismerése

Ez az oktatóanyag a Wingtip Tickets Platform (WTP) SaaS-alkalmazás üzembe helyezésével és részletes ismertetésével foglalkozik. Az alkalmazás egy bérlőnként egy adatbázisos SaaS-alkalmazásmintát használ több bérlő kiszolgálására. Az alkalmazás úgy lett kialakítva, hogy bemutassa az Azure SQL Database SaaS-forgatókönyveket támogató funkcióit, illetve az SaaS kialakítási és a felügyeleti mintáit.

Öt perccel az alábbi *Deploy to Azure* (Üzembe helyezés az Azure-ban) gombra kattintás után már üzemelni és futni fog a felhőben egy SQL Database-t használó, több bérlős SaaS-alkalmazás. Az alkalmazást három mintabérlővel helyezzük üzembe, amelyek mindegyike rendelkezik egy saját, SQL rugalmas készletben üzembe helyezett adatbázissal. Az alkalmazás az Ön Azure-előfizetésében lesz üzembe helyezve, így teljes körű hozzáférése, vizsgálati és használati lehetősége lesz az alkalmazás egyes összetevőihez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * A WTP alkalmazás üzembe helyezése
> * Az alkalmazást alkotó kiszolgálók, készletek és adatbázisok
> * Bérlők társítása az adataikhoz a *katalógus* használatával
> * Új bérlők kiépítése
> * A bérlők tevékenységének megfigyelése a készlet kihasználtságának nyomon követésével
> * Mintaerőforrások törlése a kapcsolódó számlázások leállításához

A különböző SaaS-kialakításokat és felügyeleti mintákat [egy sor kapcsolódó oktatóanyag](sql-database-wtp-overview.md) ismerteti, amelyek erre az eredeti üzemelő példányra épülnek. Az oktatóanyagok tanulmányozása közben merüljön el a megadott szkriptekben, és vizsgálja meg a különböző SaaS-minták megvalósításának módjait. Haladjon végig az egyes oktatóanyagokban található szripteken, hogy jobban megértse, hogyan használható az SQL Database számos olyan funkciója, amelyek egyszerűbbé teszik az SaaS-szolgáltatások kifejlesztését.

Az oktatóanyag teljesítéséhez a következő előfeltételeknek kell teljesülnie:

* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-wtp-saas-application"></a>A Wingtip Tickets (WTP) SaaS-alkalmazás üzembe helyezése

A Wingtip Tickets Platform kevesebb mint öt perc alatt üzembe helyezhető:

1. Kattintson ide az üzembe helyezéshez:

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Adja meg az üzembe helyezéshez szükséges paraméterértékeket:

    > [!IMPORTANT]
    > A bemutató céljából bizonyos hitelesítési lépések és kiszolgálói tűzfalak beállítása szándékosan nem biztonságos. **Hozzon létre egy új erőforráscsoportot**. Ne használjon létező erőforráscsoportokat, kiszolgálókat vagy készleteket, és ne használja az alkalmazást vagy az azzal létrehozott erőforrásokat éles környezetben. Ha végzett az alkalmazással, törölje a létrehozott erőforráscsoportot a kapcsolódó számlázások leállításához.

    * **Erőforráscsoport** – Válassza ki az **Új létrehozása** elemet, és adjon meg egy **nevet** és egy **helyet**.
    * **Felhasználó** – Bizonyos erőforrásoknak olyan nevet kell adni, amelyek az összes Azure-előfizetésban csak egyszer fordulnak elő. Az egyediség biztosítása céljából adjon meg egy olyan értéket, amely megkülönbözteti az Ön által létrehozott erőforrásokat azoktól az erőforrásoktól, amelyeket a Wingtip alkalmazást üzembe helyező más felhasználok hoztak létre. Javasoljuk, hogy használjon egy rövid **Felhasználó** nevet, mondjuk a monogramját és egy számot (például *bg1*), és használja ezt az erőforráscsoport nevében (például *wingtip-bg1*). A **Felhasználó** paraméter csak betűket, számokat és kötőjeleket tartalmazhat. Az első és az utolsó karakternek betűnek vagy számnak kell lennie (emellett ajánlott csupa kisbetűket használni).

     ![sablon](./media/sql-database-saas-tutorial/template.png)

1. **Az alkalmazás üzembe helyezése**.

    * Kattintson, ha elfogadja a feltételeket és a kikötéseket.
    * Válassza a **Rögzítés az irányítópulton** lehetőséget.
    * Kattintson a **Purchase** (Vásárlás) gombra.

1. Az üzembe helyezés állapotának figyeléséhez kattintson az **Értesítések** ikonra (a harangra a keresőmező mellett jobbra). A WTP alkalmazás üzembe helyezése körülbelül négy percet vesz igénybe.

   ![üzembe helyezés sikeres](media/sql-database-saas-tutorial/succeeded.png)

## <a name="explore-the-application"></a>Ismerkedés az alkalmazással

Az alkalmazás különböző helyszíneket mutat be, például koncerttermeket, jazzklubokat és sportklubokat, amelyek különböző eseményeknek adnak otthont. A helyszínek ügyfélként (vagyis bérlőként) regisztrálhatnak a Wingtip Tickets Platform (WTP) alkalmazásba, amely egy egyszerű módot kínál különböző rendezvények listázására és jegyek értékesítésére. Minden helyszín kap egy testreszabott webappot, amelyen keresztül a többi bérlőtől függetlenül és elkülönítve listázhatják és kezelhetik a rendezvényeket, illetve értékesíthetik a jegyeket. A háttérben minden bérlő számára üzembe lesz helyezve egy SQL Database-adatbázis egy rugalmas SQL-készletben.

A központi **eseményközpont** biztosítja az üzemelő példányra vonatkozó konkrét bérlői URL-címek listáját. A bérlői URL-címek mindegyike tartalmazza a konkrét *Felhasználó* értéket, és a következő formátumot használja: http://events.wtp.&lt;FELHASZNÁLÓ&gt;.trafficmanager.net/*fabrikamjazzclub*. 

1. Nyissa meg az _eseményközpontot_: http://events.wtp.&lt;FELHASZNÁLÓ&gt;.trafficmanager.net (a megfelelő helyre a saját felhasználónevét írja be):

    ![eseményközpont](media/sql-database-saas-tutorial/events-hub.png)

1. Kattintson a **Fabrikam Jazz Club** elemre az *eseményközpontban*.

   ![Események](./media/sql-database-saas-tutorial/fabrikam.png)

1. Kattintson a **Tickets** (Jegyek) lehetőségre, és fedezze fel, hogyan lehet jegyet vásárolni egy eseményre.

A WTP alkalmazás az [*Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) segítségével szabályozza a bejövő forgalom elosztását. Az eseményoldalak bérlőnként eltérőek, ezért az URL-címekben szerepeltetni kell a bérlők nevét. Az Események alkalmazás kielemzi a bérlőnevet az URL-címből, és a használatával létrehoz egy kulcsot egy katalógus [*szilánkleképezés-kezelés*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management) segítségével történő eléréséhez. A katalógus leképezi a kulcsot a bérlői adatbázis helyére. Az **eseményközpont** a katalógus kibővített metaadataival lekéri az egyes adatbázisokhoz társított bérlőnevet.

Éles környezetben általában egy CNAME DNS-rekordot fog létrehozni, amely [*a vállalati internettartományt*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) a Traffic Manager-profilra irányítja.

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazás szkriptjeinek lekérése

A Wingtip Tickets szkriptjei és alkalmazás-forráskódja a [WingtipSaas](https://github.com/Microsoft/WingtipSaaS) GitHub-adattárban érhető el. A szkriptfájlok a [Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules) (Tanulási modulok) mappában találhatók. Töltse le a **Learning Modules** (Tanulási modulok) mappát a helyi számítógépére a mappaszerkezet megtartásával.

## <a name="provision-a-new-tenant"></a>Új bérlő kiépítése

Az első üzembe helyezés létrehoz három mintabérlőt, azonban az oktatóanyag jobb hasznosíthatósága érdekében további bérlőket is létre fogunk hozni. Ebben a lépésben gyorsan létrehoz majd egy új bérlőt. Később érdemes lesz majd behatóbban megismernie az új bérlők kiépítésének folyamatát a [kiépítéssel és katalogizálással foglalkozó oktatóanyagban](sql-database-saas-tutorial-provision-and-catalog.md), amelyből megtudhatja, milyen egyszerűen implementálhatók regisztrációs összetevők az alkalmazásokba, valamint építhetők ki a bérlők automatikusan, az ügyfelek regisztrációjával párhuzamosan.

### <a name="initialize-the-user-config-file-for-your-deployment"></a>Az üzemelő példány felhasználói konfigurációs fájljának inicializálása

Mivel a kezdeti WTP-alkalmazástelepítés után ezt a szkriptet fogja elsőként futtatni, frissítenie kell a felhasználókonfigurációs fájlt. Frissítse a változókat az üzemelő példányra jellemző egyedi értékekkel.

   1. Nyissa meg a \\Learning Modules\\*UserConfig.psm1* fájlt a *PowerShell ISE*-ben.
   1. Módosítsa a _$userConfig.ResourceGroupName_ értékét az alkalmazás üzembe helyezésekor beállított _erőforráscsoportra_.
   1. Módosítsa a _$userConfig.Name_ értékét az alkalmazás üzembe helyezésekor beállított _felhasználónévre_.

### <a name="provision-the-new-tenant"></a>Az új bérlő kiépítése

1. Nyissa meg a \\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* fájlt a *PowerShell ISE*-ben.
1. Nyomja le az **F5** billentyűt a szkript futtatásához (egyelőre ne módosítsa a szkript alapértelmezett értékeit).

Az új bérlő mostantól regisztrálva van a katalógusban. A rendszer létrehozza az adatbázisát, és hozzáadja egy rugalmas SQL-készlethez. A sikeres kiépítés után az új bérlő jegyárusító webhelye megjelenik a böngészőben:

![Új bérlő](./media/sql-database-saas-tutorial/red-maple-racing.png)

Frissítse az eseményközpontot, és ellenőrizze, hogy az új bérlő szerepel-e a listában.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy már több bérlői adatbázisunk is van, állítsuk munkába őket! A mellékelt PowerShell-szkript egy, az összes bérlői adatbázisra kiható terhelési tesztet biztosít. A terhelés alapértelmezés szerint 60 percig fut. A Wingtip Tickets egy SaaS-alkalmazás, és a SaaS-alkalmazásokra ható valós terhelések általában szórványosak és kiszámíthatatlanok. Ennek szimulálására a terhelésgenerátor az összes bérlő között elosztott, véletlenszerű terhelést hoz létre. Több perc szükséges, amíg a minta kivehetővé válik, ezért futtassa a terhelésgenerátort 5–10 percig, mielőtt a következő szakaszokban megkísérelné a terheléseket figyelni.

1. Nyissa meg a \\Learning Modules\\Utilities\\*Demo-LoadGenerator.psm1* fájlt a **PowerShell ISE**-ben.
1. Nyomja le az **F5** billentyűt a szkript futtatásához és a terhelésgenerátor indításához (egyelőre nem módosítsa az alapértelmezett paraméterértékeket).

> [!IMPORTANT]
> A terhelésgenerátor feladatok sorozataként fut a helyi PowerShell-munkamenetben, ezért hagyja megnyitva azt. Ha bezárja a PowerShellt vagy a lapot, amelyen a terhelésgenerátort elindította, vagy ha felfüggeszti a gép működését, a feladatok futtatása leáll.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>A kiszolgálók, készletek és bérlői adatbázisok megismerése

Miután megismerkedett az alkalmazással, kiépített egy új bérlőt, és elkezdte terhelni a bérlők sorát, vizsgáljuk meg egyik-másik üzembe helyezett erőforrást.

1. Az [Azure Portalon](http://portal.azure.com) nyissa meg a **catalog-&lt;FELHASZNÁLÓ&gt;** kiszolgálót. A katalóguskiszolgáló két adatbázist tartalmaz. Ezek a **tenantcatalog** és a **basetenantdb** (egy üres *mester*adatbázis, amelynek megtöbbszörözésével hozza létre a rendszer az új bérlőket).

   ![adatbázisok](./media/sql-database-saas-tutorial/databases.png)

1. Nyissa meg a **tenants1-&lt;FELHASZNÁLÓ&gt;** kiszolgálót, amely a bérlői adatbázisokat tartalmazza. Vegye figyelembe, hogy mindegyik bérlői adatbázis egy _Normál rugalmas_ adatbázis egy 50 eDTU méretű standard készletben. Azt is megfigyelheti, hogy létezik egy _Red Maple Racing_ nevű adatbázis, amely a korábban kiépített bérlő.

   ![kiszolgáló](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>A készlet figyelése

Ha a terhelésgenerátor már fut néhány perce, elegendő adat áll rendelkezésre, hogy elkezdhessük megvizsgálni a készletekbe és adatbázisokba épített figyelőfunkciókat.

1. Lépjen a *tenants1-&lt;FELHASZNÁLÓ&gt;* kiszolgálóra, és kattintson a **Pool1** elemre a készlet erőforrás-használatának megjelenítéséhez:

   ![készlet figyelése](./media/sql-database-saas-tutorial/monitor-pool.png)

A két diagram tökéletesen illusztrálja, milyen jól alkalmazkodnak a rugalmas készletek és az SQL Database az SaaS-alkalmazások munkaterheléseihez. Négy, egyenként akár 40 eDTU magas terheléstüskékkel rendelkező adatbázist együtt könnyedén kiszolgál egy 50 eDTU méretű készlet. Ha egyenként, külön adatbázisként lennének kiépítve, mindegyikhez egy S2 (50 DTU) csomag lenne szükséges a tüskék kiszolgálásához, a 4 különálló S2-adatbázis költsége a készlet költségének majd háromszorosára rúgna. És a készletben még rengeteg hely maradt további adatbázisok számára. Valós forgatókönyvekben az ügyfelek 200 eDTU méretű készletekben egyszerre akár 500 adatbázist is futtatnak. További információkért lásd a [teljesítményfigyeléssel foglalkozó oktatóanyagot](sql-database-saas-tutorial-performance-monitoring.md).


## <a name="deleting-the-resources-created-with-this-tutorial"></a>Az oktatóanyagban létrehozott erőforrások törlése

Miután végzett a WTF alkalmazás vizsgálatával és használatával, lépjen az alkalmazás erőforráscsoportjához a portálon, és törölje azt az üzemelő példányhoz kapcsolódó összes számlázási művelet leállításához. Ha a kapcsolódó oktatóanyagok bármelyikét szintén alkalmazta, az azokban létrehozott erőforrások is törölve lesznek az alkalmazással együtt.


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]

> * A WTP alkalmazás üzembe helyezése
> * Az alkalmazást alkotó kiszolgálók, készletek és adatbázisok
> * Bérlők társítása az adataikhoz a *katalógus* használatával
> * Új bérlők kiépítése
> * A bérlők tevékenységének megfigyelése a készlet kihasználtságának nyomon követésével
> * Mintaerőforrások törlése a kapcsolódó számlázások leállításához

Most folytassa a [kiépítéssel és katalogizálással foglalkozó oktatóanyaggal](sql-database-saas-tutorial-provision-and-catalog.md).



## <a name="additional-resources"></a>További források

* [További oktatóanyagok, amelyek az eredetileg üzembe helyezett Wingtip Tickets Platform (WTP) alkalmazásra épülnek](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* A rugalmas készletekkel kapcsolatos tudnivalókért lásd [*a rugalmas Azure SQL-készleteket*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) ismertető cikket.
* A rugalmas feladatokkal kapcsolatos tudnivalókért lásd a [*horizontálisan felskálázott felhőalapú adatbázisok kezelését*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview) ismertető cikket.
* A több-bérlős SaaS-alkalmazásokkal kapcsolatos tudnivalókért lásd a [*több-bérlős SaaS-alkalmazások tervezési mintáit*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)

