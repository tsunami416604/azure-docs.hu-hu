---
title: Több-bérlős Szolgáltatottszoftver-oktatóanyag – az Azure SQL Database |} Microsoft Docs
description: Központi telepítése, és vizsgálja meg önálló single-bérlő SaaS-alkalmazás, az Azure SQL Database használja.
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 86a5bc31639cbbcdac1468f3bc2e35a547068882
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Központi telepítése, és vizsgálja meg az Azure SQL Database egy önálló single-bérlő alkalmazás

Ebben az oktatóanyagban telepíti, és megismerkedhet a Wingtip jegyek Szolgáltatottszoftver-minta egy önálló alkalmazás, vagy alkalmazás-/-bérlő mintát segítségével létrehozott alkalmazást.  Célja, hogy az alkalmazás, az Azure SQL Database, több-bérlős Szolgáltatottszoftver-forgatókönyvek engedélyezése egyszerűsítő szolgáltatásairól megjelenítve.

Az önálló alkalmazás vagy alkalmazás / bérlői minta telepíti egy alkalmazáspéldányt az egyes bérlők számára.  Minden alkalmazás egy adott bérlő konfigurálva, és egy külön Azure erőforráscsoport üzembe helyezve. Az alkalmazás több példánya több-bérlős megoldást nyújt törlődnek. Ez a minta olyan kisebb számú, ahol a bérlők elszigetelésére az a legnagyobb prioritással bérlők. Azure partner programok telepíthető a bérlő előfizetés az erőforrások és a felügyelt szolgáltató által a bérlő nevében rendelkezik. 

Ebben az oktatóanyagban be az Azure-előfizetéssel telepíti a három különálló alkalmazás három bérlők számára.  Fedezze fel, és az egyéni alkalmazás-összetevők együttműködve teljes hozzáféréssel rendelkezik.

Az alkalmazás forrás kód és a felügyeleti parancsfájlok érhetők el a [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub-tárház.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ügyfélszoftverek központi telepítése a Wingtip jegyek SaaS önálló alkalmazás.
> * Honnan szerezhetők be az alkalmazás forráskódjához, és a parancsfájlok.
> * A kiszolgálók és adatbázisok, amelyek az alkalmazás alkotják.

További oktatóanyagok kiadjuk. Ezek lehetővé teszi felfedezése, mely egy tartományt az alkalmazás mintája alapján-kezelési forgatókönyveket.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>A Wingtip jegyek SaaS önálló alkalmazás központi telepítése

Az alkalmazás központi telepítése a három megadott bérlők esetén:

1. Kattintson az egyes kék **az Azure telepítéséhez** gombra kattintva nyissa meg a központi telepítési sablont a [Azure-portálon](https://portal.azure.com). Minden sablon csak két paraméter; Új erőforráscsoport nevét, és a felhasználónevet, amely megkülönbözteti a központi telepítés az alkalmazás más központi telepítésektől. A következő lépés az értékek beállításával részletes adatokat biztosít.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso energiaoptimalizálást egyszerre Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Somfát Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Adja meg a szükséges paraméterértékeket az egyes központi telepítések.

    > [!IMPORTANT]
    > Bizonyos hitelesítési és a kiszolgáló tűzfal rendszer szándékosan nem biztonságos, bemutatási céllal. **Hozzon létre egy új erőforráscsoportot** egyes alkalmazások telepítése.  Ne használjon egy meglévő erőforráscsoportot. Ne használja ezt az alkalmazást, vagy minden olyan erőforrásnál létrehozza, üzemi. Ha elkészült, az alkalmazások leállításához kapcsolódó számlázási, törölje az erőforráscsoportokat.

    Célszerű használni az erőforrás neve csak kisbetűket, számokat és kötőjeleket tartalmazhat.
    * A **erőforráscsoport**, hozzon létre új kijelölése, és adja meg az erőforráscsoport kis nevét. **a Wingtip-sa -\<venueName\>-\<felhasználói\>**  a javasolt minta.  A \<venueName\>, szóközök nélkül helyszínére nevét. A \<felhasználói\>, helyettesítse be a felhasználói értékét az alábbi lehetőségek közül.  Az ebben a mintában az erőforráscsoportok nevei lehet *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Válassza ki a **hely** a legördülő listából.

    * A **felhasználói** -azt javasoljuk, hogy egy rövid felhasználói értéket, például saját monogramjával, valamint egy számjegy: például *af1*.


3. **Az alkalmazás üzembe helyezése**.

    * Kattintson a gombra kattintva elfogadja a feltételeket és kikötéseket.
    * Kattintson a **Purchase** (Vásárlás) gombra.

4. Az összes három központi telepítés állapotának figyelése kattintva **értesítések** (a harang ikonra a keresőmezőbe jobbra). Az alkalmazások központi telepítéséhez a körülbelül öt percet vesz igénybe.


## <a name="run-the-applications"></a>Az alkalmazások futtatása

Az alkalmazás események üzemeltető helyszínek bővíthető.  A helyszínek a bérlők számára az alkalmazás. Egyes helyszínekkel lekérdezi a listában azok az események és eladásra a jegyektől személyre szabott webhely. Helyszínére típusai koncerttermek jazz treff és sportegyesületek. A minta típusa helyszínére meghatározza, hogy a háttér fénykép jelenik meg a webhelyet a helyszínére.   Az önálló app modellben egyes helyszínekkel rendelkezik egy különálló alkalmazás példány saját önálló SQL-adatbázis.

1. Nyissa meg az események lapról az egyes külön-külön böngészőlapokon a három bérlők:

    - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

    (Egyes URL-Címekhez, cserélje le &lt;felhasználói&gt; a központi telepítés felhasználói értékkel.)

   ![Események](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

A bejövő kérelem, az alkalmazás által használt eloszlás [ *Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Bérlői-specifikus alkalmazás feltünteti a tartománynév részeként a bérlő nevét az URL-címet tartalmazza. A bérlő URL-címek közé tartozik az adott **felhasználói** érték. Az URL-címeket a következő formátumot követi:
- http://events.&lt;venuename&gt;.&lt;user&gt;.trafficmanager.net

Mindegyik bérlő adatbázis **hely** a megfelelő telepített alkalmazás app beállításait tartalmazza.

Éles környezetben általában hoz létre egy CNAME DNS-rekord [ *vállalati internetes tartomány pont* ](../traffic-manager/traffic-manager-point-internet-domain.md) a traffic manager-profil URL-címét.


## <a name="explore-the-servers-and-tenant-databases"></a>Megismerkedhet a kiszolgálók és adatbázisok bérlői

Vizsgáljuk meg a telepített erőforrások:

1. Az a [Azure-portálon](http://portal.azure.com), keresse meg az erőforráscsoportok listáját.
2. A három bérlői erőforráscsoportok kell megjelennie.
3. Nyissa meg a **wingtip-sa-fabrikam -&lt;felhasználói&gt;**  erőforráscsoport, amelyek a Fabrikam Jazz Club telepítési erőforrásokat tartalmazza.  A **fabrikamjazzclub -&lt;felhasználói&gt;**  kiszolgáló tartalmaz az **fabrikamjazzclub** adatbázis.

Minden egyes bérlő adatbázisa 50 DTU *önálló* adatbázis.

## <a name="additional-resources"></a>További források

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Több-bérlős SaaS-alkalmazásokhoz, lásd: [kialakítási minták a több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Leállítja a számlázási erőforráscsoportok törlése ##

Ha befejezte a mintát használja, állítsa le a társított számlázási létrehozott összes erőforráscsoport törlése

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> * Ügyfélszoftverek központi telepítése a Wingtip jegyek SaaS önálló alkalmazás.
> * A kiszolgálók és adatbázisok, amelyek az alkalmazás alkotják.
> * Hogyan mintaerőforrásokat leállításához kapcsolódó számlázási törlése.

Ezt követően próbálja meg a [biztosítása és a katalógus](saas-standaloneapp-provision-and-catalog.md) oktatóanyag, amely lehetővé teszi a kereszt-bérlői forgatókönyvet séma felügyeletet és a bérlői például számos katalógusát a bérlők használatának néhány.
 

