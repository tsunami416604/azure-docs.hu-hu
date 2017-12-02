---
title: "Több-bérlős Szolgáltatottszoftver-oktatóanyag – az Azure SQL Database |} Microsoft Docs"
description: "Központi telepítése, és vizsgálja meg önálló single-bérlő SaaS-alkalmazás, az Azure SQL Database használja."
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
ms.date: 11/30/2017
ms.author: genemi
ms.openlocfilehash: d38cd108821bce05824732bbdbdd322ae8563bde
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Központi telepítése, és vizsgálja meg az Azure SQL Database egy önálló single-bérlő alkalmazás

Ebben az oktatóanyagban telepíti, és megismerkedhet a Wingtip jegyek SaaS önálló alkalmazás. Az alkalmazás szolgál, lehetővé teszi Szolgáltatottszoftver-forgatókönyvek egyszerűsítő szolgáltatásairól az Azure SQL Database megjelenítve.

Az önálló alkalmazásminta telepíti egy Azure erőforráscsoport egy egyetlen-bérlő alkalmazást és egy bérlői adatbázis tartalmazó az egyes bérlők számára.  Az alkalmazás több példánya is kell létrehozni, hogy egy több-bérlős megoldást kínál.

Ebben az oktatóanyagban telepít erőforráscsoportok több bérlő az Azure-előfizetése.  Ebben a mintában lehetővé teszi, hogy az erőforráscsoportok az Azure-előfizetés a bérlő helyezhető üzembe. Azure rendelkezik partner programok, amelyek lehetővé teszik a bérlő nevében a szolgáltató által kezelt ezeket az erőforráscsoportokat. A szolgáltató egy rendszergazda a bérlői előfizetéshez.

A telepítés későbbi szakaszában vannak három kék **az Azure telepítéséhez** gombokat. Egyes gombokon telepíti az alkalmazás egy másik példánya. Egy adott bérlő testreszabott minden példánya. Minden egyes a gomb megnyomott állapota esetén, a megfelelő alkalmazás teljes rendszer öt percen belül.  Az alkalmazások az Azure-előfizetéshez vannak telepítve.  Fedezze fel, és az egyéni alkalmazás-összetevők együttműködve teljes hozzáféréssel rendelkezik.

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
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Contoso energiaoptimalizálást egyszerre Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Somfát Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Fabrikam Jazz Club**

2. Adja meg a szükséges paraméterértékeket az egyes központi telepítések.

    > [!IMPORTANT]
    > Bizonyos hitelesítési és a kiszolgáló tűzfal rendszer szándékosan nem biztonságos, bemutatási céllal. **Hozzon létre egy új erőforráscsoportot** egyes alkalmazások telepítése.  Ne használjon egy meglévő erőforráscsoportot. Ne használja ezt az alkalmazást, vagy minden olyan erőforrásnál létrehozza, üzemi. Ha elkészült, az alkalmazások leállításához kapcsolódó számlázási, törölje az erőforráscsoportokat.

    Célszerű használni az erőforrás neve csak kisbetűket, számokat és kötőjeleket tartalmazhat.
    * A **erőforráscsoport** – Itt adhatja meg **hozzon létre új**, és adja meg egy kisbetűt **neve** ahhoz az erőforráscsoporthoz.
        * Azt javasoljuk, hogy a kötőjel, majd a monogramját számjegy követ hozzáfűzése: például *wingtip-sa-af1*.
        * Válassza ki a **hely** a legördülő listából.

    * A **felhasználói** -azt javasoljuk, hogy rövid felhasználói értéket, például a saját monogramjával, valamint egy számjegy választ: például *af1*.


3. **Az alkalmazás üzembe helyezése**.

    * Kattintson a gombra kattintva elfogadja a feltételeket és kikötéseket.
    * Kattintson a **Purchase** (Vásárlás) gombra.

4. Három központi telepítések központi telepítési állapot figyelése kattintva **értesítések** (a harang ikonra a keresőmezőbe jobbra). Az alkalmazás telepítéséhez öt percet vesz igénybe.


## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás események üzemeltető helyszínek bővíthető. Helyszínére típusai koncerttermek jazz treff és sportegyesületek. Helyszínek az ügyfelek a Wingtip jegyek alkalmazás. A Wingtip jegyek helyszínek nyilvántartott *bérlők*. A bérlő folyamatban lehetőséget ad egy helyszínére egyszerűen lista események és jegyek értékesít az ügyfelek. Egyes helyszínekkel lekérdezi egy személyre szabott webhely, az események elemet, és adjon a jegyektől el. Mindegyik bérlő elkülönítése a bérlőktől, és nem függ össze őket. A színfalak mindegyik bérlő saját önálló SQL-adatbázis egy különálló alkalmazás példány lekérdezi.

1. Nyissa meg az események lapról az egyes külön-külön böngészőlapokon a három bérlők:

    - http://events.contosoconcerthall. &lt;Felhasználói&gt;. trafficmanager.net
    - http://events.dogwooddojo. &lt;Felhasználói&gt;. trafficmanager.net
    - http://events.fabrikamjazzclub. &lt;Felhasználói&gt;. trafficmanager.net

    (Egyes URL-Címekhez, cserélje le &lt;felhasználói&gt; a központi telepítés felhasználói értékkel.)

   ![Események](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

A bejövő kérelem, az alkalmazás által használt eloszlás [ *Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Bérlői-specifikus alkalmazás feltünteti a tartománynév részeként a bérlő nevét az URL-címet tartalmazza. A bérlő URL-címek közé tartozik az adott **felhasználói** érték. Az URL-címeket a következő formátumot követi:
- http://events. &lt;venuename&gt;.&lt; FELHASZNÁLÓI&gt;. trafficmanager.net

Mindegyik bérlő adatbázis **hely** a megfelelő telepített alkalmazás app beállításait tartalmazza.

Éles környezetben általában hoz létre egy CNAME DNS-rekord [ *vállalati internetes tartomány pont* ](../traffic-manager/traffic-manager-point-internet-domain.md) a traffic manager-profil URL-címét.


## <a name="explore-the-servers-and-tenant-databases"></a>Megismerkedhet a kiszolgálók és adatbázisok bérlői

Vizsgáljuk meg a telepített erőforrások:

1. Az a [Azure-portálon](http://portal.azure.com), keresse meg az erőforráscsoportok listáját.
2. Tekintse meg a **wingtip-sa-katalógus -&lt;felhasználói&gt;**  erőforráscsoportot.
    - Ez az erőforráscsoport, a a **katalógus-sa -&lt;felhasználói&gt;**  kiszolgáló van telepítve. A kiszolgáló tartalmaz az **tenantcatalog** adatbázis.
    - Emellett meg kell jelennie a három bérlői erőforráscsoportok.
3. Nyissa meg a **wingtip-sa-fabrikam -&lt;felhasználói&gt;**  erőforráscsoport, amelyek a Fabrikam Jazz Club telepítési erőforrásokat tartalmazza.  A **fabrikamjazzclub -&lt;felhasználói&gt;**  kiszolgáló tartalmaz az **fabrikamjazzclub** adatbázis.

Minden egyes bérlő adatbázisa 50 DTU *önálló* adatbázis.

## <a name="additional-resources"></a>További források

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Több-bérlős SaaS-alkalmazásokhoz, lásd: [kialakítási minták a több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> * Ügyfélszoftverek központi telepítése a Wingtip jegyek SaaS önálló alkalmazás.
> * A kiszolgálók és adatbázisok, amelyek az alkalmazás alkotják.
> * Hogyan mintaerőforrásokat leállításához kapcsolódó számlázási törlése.

