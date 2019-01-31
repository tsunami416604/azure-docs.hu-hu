---
title: Egybérlős SaaS-oktatóanyag – Azure SQL Database |} A Microsoft Docs
description: Üzembe helyezése, és a egy önálló egybérlős SaaS-alkalmazásban, amely az Azure SQL Database megismerése.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: 7941938b089034565307b0104034589ee44c24bf
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470399"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Üzembe helyezése és megismerése az Azure SQL-adatbázist használó önálló egybérlős alkalmazás

Ebben az oktatóanyagban üzembe helyezése, és a Wingtip Tickets SaaS-mintaalkalmazás egy önálló alkalmazás, vagy az alkalmazás bérlőnkénti, mintát fejlesztett megismerése.  Az alkalmazás az Azure SQL Database szolgáltatások, amelyek megkönnyítik a több-bérlős SaaS-forgatókönyveket lehetővé bemutatására lett tervezve.

Az önálló alkalmazás vagy alkalmazás bérlőnkénti minta üzembe helyez egy alkalmazáspéldány az egyes bérlők számára.  Minden alkalmazás egy adott bérlő konfigurálva, és a egy külön Azure-erőforráscsoport üzembe helyezve. Több-bérlős megoldást kínál az alkalmazás több példánya érvénybe lépnek. Ez a minta akkor kisebb számokat, a bérlők, ahol a bérlők elkülönítését prioritást jelent a legmegfelelőbb. Azure-partner programok, amelyek lehetővé teszik az erőforrások egy bérlő-előfizetésben helyezi üzembe helyezni és felügyelt egy szolgáltató által a bérlő nevében megtalálható. 

Ebben az oktatóanyagban három különálló alkalmazás három bérlők számára telepíti az Azure-előfizetésben helyezi.  Fedezze fel és az egyes alkalmazás-összetevők teljes hozzáféréssel rendelkezik.

Az alkalmazás forrás-kód és a felügyeleti parancsprogramokat érhetők el a [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub-adattárban. Az alkalmazás a Visual Studio 2015-ben lett létrehozva, és nem sikerült nyissa meg és fordítsa le a Visual Studio 2017 frissítése nélkül.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Wingtip Tickets SaaS önálló alkalmazás telepítésének módjáról.
> * Az alkalmazás forráskódjának és felügyeleti parancsfájlokat helyét.
> * A kiszolgálók és adatbázisok az alkalmazás alkotó.

További oktatóanyagok elérhető lesz. Ezek lehetővé teszi, hogy ismerje meg, ezen alkalmazás mintán alapuló felügyeleti forgatókönyvek széles.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>A Wingtip Tickets SaaS önálló alkalmazás üzembe helyezése

Az alkalmazás üzembe helyezése a három megadott bérlők esetén:

1. Kattintson az egyes kék **üzembe helyezés az Azure** gombra kattintva nyissa meg a központi telepítési sablont a [az Azure portal](https://portal.azure.com). Mindegyik sablon szükséges paraméter két értéket; egy új erőforráscsoport nevét, és a egy felhasználónevet, amely megkülönbözteti ezt az alkalmazás más központi telepítését. A következő lépésben részletesen állítja ezeket az értékeket.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso Concert Hall**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Somfát Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Adja meg a szükséges paraméterértékeket minden egyes üzemelő példányhoz.

    > [!IMPORTANT]
    > Bizonyos hitelesítési és kiszolgálói tűzfalak szándékosan nem biztonságos, bemutatási céllal. **Hozzon létre egy új erőforráscsoportot** minden egyes alkalmazás üzembe helyezéshez.  Ne használjon egy meglévő erőforráscsoportot. Ne használja az alkalmazást vagy az azzal létrehozott erőforrásokat éles környezetben. Törölje az erőforráscsoportok, amikor befejezte az alkalmazásokat a kapcsolódó számlázások leállításához.

    A legcélszerűbb használata az erőforrás neve csak kisbetűket, számokat és kötőjeleket tartalmazhat.
    * A **erőforráscsoport**, hozzon létre új és a egy kis az erőforráscsoport nevét adja meg. **a Wingtip-sa -\<venueName\>-\<felhasználói\>**  a javasolt minta.  A \<venueName\>, helyettesítse be a helyszín neve nem tartalmazhat szóközt. A \<felhasználói\>, illessze be az alábbi a felhasználó értéket.  Ezzel a mintával az erőforráscsoportok nevei lehet *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Válassza ki a **hely** a legördülő listából.

    * A **felhasználói** – javasoljuk, hogy egy felhasználó rövid értéket a monogramját és egy számjegyet: például *af1*.


3. **Az alkalmazás üzembe helyezése**.

    * Ide kattintva elfogadja a feltételeket és kikötéseket.
    * Kattintson a **Purchase** (Vásárlás) gombra.

4. Kattintson a három központi telepítések állapotának figyelése **értesítések** (a harang ikonra a jobb oldalon, a keresőmező). Az alkalmazások üzembe helyezése körülbelül öt percet vesz igénybe.


## <a name="run-the-applications"></a>Az alkalmazások futtatása

Az alkalmazás események üzemeltető helyszínek bemutatja.  A helyszínek a bérlők számára az alkalmazás. Minden helyszín kap egy személyre szabott webhely listában azok az események és jegyek értékesítésére. Helyszíntípusok például koncerttermeket, különböző helyszíneket és sportklubokat által az olimpián. A mintában a helyszín típusa határozza meg, a háttérben futó fénykép a helyszín webhely jelenik meg.   Az önálló app modellben minden egyes helyszín egy külön alkalmazáspéldány saját önálló SQL-adatbázissal rendelkezik.

1. A három külön böngészőlapokon bérlők mindegyike esetében nyissa meg az események lapról:

    - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

    (Egyes URL-címben cserélje le a &lt;felhasználói&gt; az üzemelő példány felhasználói értékkel.)

   ![Események](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Bejövő kérelmek, az alkalmazás által használt elosztását [ *Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Minden bérlő-specifikus alkalmazás-példány URL-CÍMÉT a bérlő nevét a tartomány nevének részeként tartalmazza. Az összes bérlői URL-címeket tartalmazza a konkrét **felhasználói** értéket. Az URL-címeket hajtsa végre a következő formátumban:
- http://events.&lt;venuename&gt;.&lt;user&gt;.trafficmanager.net

Minden bérlői adatbázis **hely** az a megfelelő telepített alkalmazások beállításait tartalmazza.

Éles környezetben általában hoz létre egy CNAME DNS-rekord [ *egy vállalati internetes tartomány* ](../traffic-manager/traffic-manager-point-internet-domain.md) URL-címét a traffic manager-profilt.


## <a name="explore-the-servers-and-tenant-databases"></a>Ismerje meg a kiszolgálók és a bérlői adatbázisok

Nézzük, egyes üzembe helyezett erőforrások:

1. Az a [az Azure portal](http://portal.azure.com), tallózással keresse meg a azon erőforráscsoportok listája.
2. A három bérlői erőforrás-csoportok kell megjelennie.
3. Nyissa meg a **wingtip-sa-fabrikam -&lt;felhasználói&gt;**  a Fabrikam Jazz Club központi telepítés az erőforrásokat tartalmazó erőforráscsoportot.  A **fabrikamjazzclub -&lt;felhasználói&gt;**  kiszolgáló tartalmazza a **fabrikamjazzclub** adatbázis.

Minden bérlői adatbázis egy 50 DTU *önálló* adatbázis.

## <a name="additional-resources"></a>További források

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](sql-database-elastic-jobs-overview.md)
-->

- Több-bérlős SaaS-alkalmazások kapcsolatos további információkért lásd: [tervezési minták több-bérlős SaaS-alkalmazások](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Erőforráscsoportok leállítani a számlázási törlése ##

Miután végzett, a minta használatával, és törölje az összes, a kapcsolódó számlázások leállításához létrehozott erőforráscsoport.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> * A Wingtip Tickets SaaS önálló alkalmazás telepítésének módjáról.
> * A kiszolgálók és adatbázisok az alkalmazás alkotó.
> * Hogyan törlése a kapcsolódó számlázások leállításához.

Ezt követően próbálja meg a [kiépítéssel és Katalogizálással](saas-standaloneapp-provision-and-catalog.md) oktatóanyag, amelyben felderíti a bérlők egy katalógus, amely lehetővé teszi a több-bérlős forgatókönyvek, például a séma felügyeletet és a bérlői analitikai számos használatát.
 

