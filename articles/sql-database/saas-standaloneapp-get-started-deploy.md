---
title: Egyetlen bérlős SaaS-oktatóanyag
description: Egy önálló, egybérlős SaaS-alkalmazás üzembe helyezése és megismerése, amely Azure SQL Databaset használ.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 11/07/2018
ms.openlocfilehash: e3afc8aa58551b995070ffaca978c8e7c8454da3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822132"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>A Azure SQL Databaset használó önálló egybérlős alkalmazás üzembe helyezése és megismerése

Ebben az oktatóanyagban üzembe helyezi és felderíti a Wingtip tickets SaaS-minta alkalmazást, amelyet az önálló alkalmazás, vagy az alkalmazás-bérlői minta használatával fejlesztett ki.  Az alkalmazás a több-bérlős SaaS-forgatókönyvek egyszerűsítését megkönnyítő Azure SQL Database funkcióinak bemutatására szolgál.

Az önálló alkalmazás vagy az alkalmazás/bérlő minta az egyes bérlők számára telepít egy alkalmazás-példányt.  Az egyes alkalmazások egy adott bérlőhöz vannak konfigurálva, és egy külön Azure-erőforráscsoporthoz vannak telepítve. Több-bérlős megoldás biztosításához az alkalmazás több példánya van kiépítve. Ez a minta olyan bérlők számára ideális, amelyek a bérlők elkülönítésének elsődleges prioritása. Az Azure olyan partneri programokkal rendelkezik, amelyek lehetővé teszik, hogy az erőforrások a bérlő előfizetésében legyenek üzembe helyezhetők, és a bérlők nevében egy szolgáltató felügyeli. 

Ebben az oktatóanyagban három különálló alkalmazást fog telepíteni három bérlőre az Azure-előfizetésében.  Teljes hozzáféréssel rendelkezik az egyes alkalmazás-összetevők megismeréséhez és használatához.

Az alkalmazás forráskódja és a felügyeleti szkriptek a [WingtipTicketsSaaS-StandaloneApp GitHub-](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) tárházban érhetők el. Az alkalmazás a Visual Studio 2015 használatával lett létrehozva, és a frissítés nélkül nem sikerült megnyitni és lefordítani a Visual Studio 2019-ben.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Wingtip tickets SaaS önálló alkalmazás üzembe helyezése.
> * Az alkalmazás forráskódjának és felügyeleti parancsfájljainak beszerzése.
> * Az alkalmazást alkotó kiszolgálók és adatbázisok ismertetése.

A rendszer további oktatóanyagokat is felszabadít. Lehetővé teszik, hogy az alkalmazás mintája alapján számos felügyeleti forgatókönyvet ismerjen meg.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>A Wingtip tickets SaaS önálló alkalmazás üzembe helyezése

Telepítse az alkalmazást a három megadott bérlőhöz:

1. Kattintson a kék **üzembe helyezés az Azure** -ban gombra, hogy megnyissa a központi telepítési sablont a [Azure Portal](https://portal.azure.com). Minden sablonhoz két paraméter érték szükséges; egy új erőforráscsoport neve, valamint egy olyan Felhasználónév, amely megkülönbözteti a központi telepítést az alkalmazás más központi telepítései között. A következő lépés az értékek beállításának részleteit tartalmazza.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **contoso Concert Hall**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **somfa Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Adja meg az egyes központi telepítések kötelező paramétereinek értékét.

    > [!IMPORTANT]
    > Bizonyos hitelesítési és kiszolgálói tűzfalak szándékosan nem biztonságosak a demonstrációs célokra. **Hozzon létre egy új erőforráscsoportot** az egyes alkalmazások telepítéséhez.  Ne használjon meglévő erőforráscsoportot. Ne használja az alkalmazást vagy az általa létrehozott erőforrásokat éles környezetben. Törölje az összes erőforráscsoportot, ha elkészült az alkalmazásokkal a kapcsolódó számlázás leállításához.

    A legjobb, ha csak kisbetűket, számokat és kötőjeleket használ az erőforrásnevek.
    * Az **erőforráscsoport**területen válassza az új létrehozása lehetőséget, majd adja meg az erőforráscsoport kisbetűs nevét. **Wingtip-SA-\<venueName\>-\<felhasználói\>** a javasolt minta.  \<venueName\>esetében cserélje le a helyszín nevét szóköz nélkül. \<felhasználói\>a lenti felhasználói értéket cserélje le.  Ezzel a mintával az erőforráscsoportok nevei lehetnek *Wingtip-SA-contosoconcerthall-AF1*, *Wingtip-SA-dogwooddojo-AF1*, *Wingtip-SA-fabrikamjazzclub-AF1*.
    * Válasszon ki egy **helyet** a legördülő listából.

    * **Felhasználó** számára – egy rövid felhasználói értéket ajánlunk, például a monogramját és egy számjegyét: például *AF1*.


3. **Az alkalmazás üzembe helyezése**.

    * Kattintson ide a feltételek és kikötések elfogadásához.
    * Kattintson a **Purchase** (Vásárlás) gombra.

4. Mindhárom központi telepítés állapotának figyeléséhez kattintson az **értesítések** elemre (a keresőmező jobb oldalán található harang ikonra). Az alkalmazások üzembe helyezése körülbelül öt percet vesz igénybe.


## <a name="run-the-applications"></a>Az alkalmazások futtatása

Az alkalmazás az eseményeket üzemeltető helyszíneket mutatja be.  A helyszínek az alkalmazás bérlői. Minden helyszín egy személyre szabott webhelyet kap az események listázásához és jegyek eladásához. A helyszín típusok közé tartoznak a koncert termek, a jazz klubok és a sport klubok. A mintában a helyszín típusa határozza meg a helyszín webhelyén látható háttér-fényképet.   Az önálló alkalmazás modelljében minden egyes helyszín külön alkalmazás-példánnyal rendelkezik saját önálló SQL-adatbázissal.

1. Nyissa meg az Events (események) lapot a három bérlő mindegyikéhez külön böngésző lapjain:

   - http://events.contosoconcerthall.&lt; felhasználó&gt;. trafficmanager.net
   - http://events.dogwooddojo.&lt; felhasználó&gt;. trafficmanager.net
   - http://events.fabrikamjazzclub.&lt; felhasználó&gt;. trafficmanager.net

     (Minden URL-címben cserélje le &lt;felhasználói&gt; a központi telepítés felhasználói értékére.)

   ![Események](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

A bejövő kérelmek eloszlásának szabályozásához az alkalmazás az [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md)-t használja. Minden bérlőre jellemző alkalmazás-példány tartalmazza a bérlő nevét a tartománynév részeként az URL-címben. Az összes bérlői URL-cím tartalmazza az adott **felhasználói** értéket. Az URL-címek a következő formátumot követik:
- http://events.&lt; venuename&gt;.&lt;felhasználói&gt;. trafficmanager.net

Az egyes bérlők adatbázis- **helye** a megfelelő telepített alkalmazás Alkalmazásbeállítások részét képezi.

Éles környezetben általában egy CNAME DNS-rekordot hoz létre, amely a [*vállalati internetes tartományt*](../traffic-manager/traffic-manager-point-internet-domain.md) a Traffic Manager-profil URL-címére irányítja.


## <a name="explore-the-servers-and-tenant-databases"></a>A kiszolgálók és a bérlői adatbázisok megismerése

Nézzük meg az üzembe helyezett erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg az erőforráscsoportok listáját.
2. Ekkor látnia kell a három bérlői erőforráscsoportot.
3. Nyissa meg a **Wingtip-SA-Fabrikam-&lt;felhasználói&gt;** erőforráscsoportot, amely a fabrikam Jazz Club üzembe helyezésének erőforrásait tartalmazza.  A **fabrikamjazzclub-&lt;felhasználói&gt;** kiszolgáló tartalmazza a **fabrikamjazzclub** -adatbázist.

Minden bérlői adatbázis 50 DTU *önálló* adatbázis.

## <a name="additional-resources"></a>További források

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](elastic-jobs-overview.md)
-->

- A több-bérlős SaaS-alkalmazásokkal kapcsolatos további információkért lásd: [tervezési minták a több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Erőforráscsoportok törlése a számlázás leállításához ##

Ha befejezte a minta használatát, törölje az összes létrehozott erőforráscsoportot a társított számlázás leállításához.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> * A Wingtip tickets SaaS önálló alkalmazás üzembe helyezése.
> * Az alkalmazást alkotó kiszolgálók és adatbázisok ismertetése.
> * Mintavételi erőforrások törlése a kapcsolódó számlázás leállításához.

Ezután próbálja ki a kiépítéssel [és a katalógussal](saas-standaloneapp-provision-and-catalog.md) foglalkozó oktatóanyagot, amelyben megtekintheti a bérlők katalógusának használatát, amely több bérlős forgatókönyv, például a séma-felügyeleti és a bérlői elemzések széles körét teszi lehetővé.
 

