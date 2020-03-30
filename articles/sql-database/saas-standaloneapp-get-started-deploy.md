---
title: Egybérlős SaaS-oktatóanyag
description: Üzembe helyezhet és felderülhet egy önálló egybérlős SaaS-alkalmazás, amely az Azure SQL Database-t használja.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822132"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Az Azure SQL Database-t használó önálló egybérlős alkalmazások üzembe helyezése és feltárása

Ebben az oktatóanyagban üzembe helyezheti és felfedezheti a Wingtip Tickets SaaS mintaalkalmazás segítségével kifejlesztett önálló alkalmazás, vagy az alkalmazás-bérlőnként, minta.  Az alkalmazás célja, hogy bemutassa az Azure SQL Database olyan funkcióit, amelyek leegyszerűsítik a több-bérlős SaaS-forgatókönyvek engedélyezését.

Az önálló alkalmazás vagy a bérlőnkénti alkalmazásminta minden bérlőhöz telepít egy alkalmazáspéldányt.  Minden alkalmazás egy adott bérlőhöz van konfigurálva, és egy külön Azure-erőforráscsoportban van telepítve. Az alkalmazás több példánya i. több-bérlős megoldás biztosítása érdekében vannak kiépítve. Ez a minta a legalkalmasabb a kisebb számú, a bérlők, ahol a bérlők elkülönítése a legfontosabb prioritás. Az Azure olyan partnerprogramokkal rendelkezik, amelyek lehetővé teszik, hogy az erőforrások at a bérlő előfizetésébe telepítsék, és a bérlő nevében egy szolgáltató által kezeljék. 

Ebben az oktatóanyagban három önálló alkalmazást telepít három bérlőre az Azure-előfizetésbe.  Teljes hozzáféréssel rendelkezik az egyes alkalmazás-összetevők felfedezéséhez és az okkal való munkához.

Az alkalmazás forráskódja és felügyeleti parancsfájljai a [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub tárházban érhetők el. Az alkalmazás a Visual Studio 2015 használatával készült, és frissítés nélkül nem nyitja meg és fordítja le sikeresen a Visual Studio 2019-ben.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Wingtip jegyek SaaS önálló alkalmazás üzembe helyezése.
> * Hol szerezhető be az alkalmazás forráskódja és a felügyeleti parancsfájlok?
> * Az alkalmazást kiszolgálókés adatbázisok.

További oktatóanyagok fog megjelenni. Lehetővé teszik, hogy az alkalmazásminta alapján számos felügyeleti forgatókönyvet tárjon fel.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>A Wingtip jegyek SaaS önálló alkalmazás telepítése

Telepítse az alkalmazást a három megadott bérlőhöz:

1. Kattintson az egyes kék **üzembe helyezés az Azure-ba** gombra a központi telepítési sablon megnyitásához az [Azure Portalon.](https://portal.azure.com) Minden sablonhoz két paraméterérték szükséges; egy új erőforráscsoport nevét és egy felhasználónevet, amely megkülönbözteti ezt a központi telepítést az alkalmazás többi központi telepítésétől. A következő lépés az értékek beállításának részleteit tartalmazza.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Contoso koncertterem**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Somfa Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Fabrikam Jazz Klub**

2. Adja meg az egyes telepítésekhez szükséges paraméterértékeket.

    > [!IMPORTANT]
    > Egyes hitelesítési és kiszolgálói tűzfalak szándékosan nem biztonságosak demonstrációs célokra. **Hozzon létre egy új erőforráscsoportot** minden alkalmazás központi telepítéséhez.  Ne használjon meglévő erőforráscsoportot. Ne használja ezt az alkalmazást, vagy az általa létrehozott erőforrásokat éles környezetben. Törölje az összes erőforráscsoportot, ha befejezte az alkalmazásokat a kapcsolódó számlázás leállításához.

    Az erőforrásnevekben csak kisbetűket, számokat és kötőjeleket használjon.
    * Az **Erőforráscsoport csoportban**válassza az Új létrehozása lehetőséget, majd adja meg az erőforráscsoport kisnevét. **wingtip-sa-\<\>-\<venueName\> user** az ajánlott minta.  A \<venueName\>esetében cserélje le a helyszín nevét szóközök nélkül. \>Felhasználó esetén \<cserélje le a felhasználói értéket alulról.  Ezzel a mintával az erőforráscsoport nevei *lehetnek wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Válasszon **egy Helyet** a legördülő listából.

    * **Felhasználó** - Javasoljuk, hogy egy rövid felhasználói érték, mint például a monogramplusz egy számjegy: például *af1*.


3. **Az alkalmazás üzembe helyezése**.

    * Kattintson ide, hogy elfogadja a feltételeket.
    * Kattintson a **Purchase** (Vásárlás) gombra.

4. Mindhárom központi telepítés állapotának figyeléséhez kattintson az **Értesítések** (a keresőmező jobb oldalán található csengő ikonra) elemre. Az alkalmazások telepítése körülbelül öt percet vesz igénybe.


## <a name="run-the-applications"></a>Az alkalmazások futtatása

Az alkalmazás bemutatja az eseményeket fogadó helyszíneket.  A helyszínek az alkalmazás bérlői. Minden helyszín kap egy személyre szabott weboldalt, hogy felsorolják az eseményeket és eladják a jegyeket. A helyszínek között koncerttermek, jazz klubok és sportklubok is szerepelnek. A mintában a helyszín típusa határozza meg a helyszín honlapján látható háttérképet.   Az önálló alkalmazásmodellben minden helyszín külön alkalmazáspéldányt, saját önálló SQL-adatbázissal rendelkezik.

1. Nyissa meg az események lapot mindhárom bérlő számára külön böngészőlapon:

   - http://events.contosoconcerthall.&lt;felhasználó&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;felhasználó&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;felhasználó&gt;.trafficmanager.net

     (Minden URL-címben cserélje le &lt;a felhasználót&gt; a központi telepítés felhasználói értékére.)

   ![Események](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

A bejövő kérelmek elosztásának szabályozásához az alkalmazás az [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md)t használja. Minden bérlő-specifikus alkalmazáspéldány tartalmazza a bérlő nevét az URL-címben a tartománynév részeként. Az összes bérlői URL-cím tartalmazza a megadott **felhasználói** értéket. Az URL-ek a következő formátumot követik:
- http://events.&lt;helyszínnév&gt;. &lt;felhasználó&gt;.trafficmanager.net

Minden bérlő adatbázis **helye** szerepel az alkalmazás beállításait a megfelelő üzembe helyezett alkalmazás.

Éles környezetben általában létrehoz egy CNAME DNS-rekordot, amely [*a vállalat internetes tartományát*](../traffic-manager/traffic-manager-point-internet-domain.md) a forgalomkezelő profil URL-címére irányíthatja.


## <a name="explore-the-servers-and-tenant-databases"></a>A kiszolgálók és a bérlői adatbázisok felfedezése

Nézzünk meg néhány, a telepített erőforrások:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az erőforráscsoportok listáját.
2. Meg kell jelennie a három bérlői erőforráscsoportnak.
3. Nyissa meg a **wingtip-sa-fabrikam-&lt;felhasználói&gt; ** erőforrás csoportot, amely a Fabrikam Jazz Club telepítéséhez szükséges erőforrásokat tartalmazza.  A **fabrikamjazzclub&lt;&gt; felhasználói** szerver tartalmazza a **fabrikamjazzclub** adatbázist.

Minden bérlői adatbázis egy 50 DTU *önálló* adatbázis.

## <a name="additional-resources"></a>További források

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](elastic-jobs-overview.md)
-->

- A több-bérlős SaaS-alkalmazásokról a [több-bérlős SaaS-alkalmazások tervezési mintái című](saas-tenancy-app-design-patterns.md)témakörben olvashat.

 
## <a name="delete-resource-groups-to-stop-billing"></a>Erőforráscsoportok törlése a számlázás leállításához ##

Miután befejezte a minta használatát, törölje az összes létrehozott erőforráscsoportot a társított számlázás leállításához.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> * A Wingtip jegyek SaaS önálló alkalmazás üzembe helyezése.
> * Az alkalmazást kiszolgálókés adatbázisok.
> * Mintaforrások törlése a kapcsolódó számlázás leállításához.

Ezután próbálja meg a [kiépítési és katalógus](saas-standaloneapp-provision-and-catalog.md) oktatóanyag, amelyben megvizsgálja a bérlők katalógusának használatát, amely lehetővé teszi a több bérlőközötti forgatókönyvek, például a séma-kezelés és a bérlői elemzés használatát.
 

