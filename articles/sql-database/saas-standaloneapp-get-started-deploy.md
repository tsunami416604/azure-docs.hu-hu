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
ms.date: 11/14/2017
ms.author: sstein
ms.openlocfilehash: 2ca290dfcb23215ffd727500e76076ae8b14fa6f
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Központi telepítése, és vizsgálja meg az Azure SQL Database egy önálló single-bérlő alkalmazás

Ebben az oktatóanyagban telepíti, és megismerkedhet a Wingtip jegyek SaaS önálló alkalmazás. Az alkalmazás szolgál, lehetővé teszi Szolgáltatottszoftver-forgatókönyvek egyszerűsítő szolgáltatásairól az Azure SQL Database megjelenítve.

Az önálló alkalmazásminta telepíti egy Azure erőforráscsoport egy egyetlen-bérlő alkalmazást és egy bérlői adatbázis tartalmazó az egyes bérlők számára.  Az alkalmazás több példánya is kell létrehozni, hogy egy több-bérlős megoldást kínál.

Ebben az oktatóanyagban rendszer telepítése közben erőforráscsoportok több bérlő az Azure-előfizetése, ebben a mintában lehetővé teszi, hogy az erőforráscsoportok az Azure-előfizetés a bérlő helyezhető üzembe.  Azure rendelkezik partner programok, amelyek lehetővé teszik a bérlő előfizetésének rendszergazdaként a bérlő nevében a szolgáltató által kezelt ezeket az erőforráscsoportokat.

A központi telepítés az alábbi szakaszban vannak az Azure gombok, az alkalmazás egy adott bérlő testreszabott eltérő példányát telepíti, amelyek három telepítéséhez. Minden egyes gomb megnyomásakor megfelelő telepítik az alkalmazást teljesen 5 percen belül.  Az alkalmazások az Azure-előfizetéshez vannak telepítve.  Fedezze fel, és az egyéni alkalmazás-összetevők együttműködve teljes hozzáféréssel rendelkezik.

Az alkalmazás forrás kód és a felügyeleti parancsfájlok érhetők el a [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub-tárház.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * A Wingtip jegyek SaaS önálló alkalmazás központi telepítése
> * Honnan szerezhetők be az alkalmazás forráskódjához, és a parancsfájlok
> * A kiszolgálók és adatbázisok, az alkalmazás alkotó

További oktatóanyagok kiadjuk során, amely lehetővé teszi a felügyeleti lehetőségeket az alkalmazás mintája alapján számos vizsgálatát.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>A Wingtip jegyek SaaS önálló alkalmazás központi telepítése

Az alkalmazás központi telepítése a három megadott bérlők esetén:

1. Kattintson a felsorolt **az Azure telepítéséhez** gombra kattintva nyissa meg a központi telepítési sablont az Azure portálon. Minden sablon csak két paraméter; Új erőforráscsoport nevét, és a felhasználónevet, amely megkülönbözteti a központi telepítés az alkalmazás más központi telepítésektől. A következő lépés az értékek beállításával részletes adatokat biztosít.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>     &nbsp;&nbsp;**Contoso energiaoptimalizálást egyszerre Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; &nbsp; **Somfát Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>    &nbsp;&nbsp;**Fabrikam Jazz Club**

2. Adja meg a szükséges paraméterértékeket az egyes központi telepítések.

    > [!IMPORTANT]
    > A bemutató céljából bizonyos hitelesítési lépések és kiszolgálói tűzfalak beállítása szándékosan nem biztonságos. **Hozzon létre egy új erőforráscsoportot** egyes alkalmazások telepítése.  Ne használjon egy meglévő erőforráscsoportot. Ne használja ezt az alkalmazást, vagy minden olyan erőforrásnál létrehozza, üzemi. Ha elkészült, az alkalmazások leállításához kapcsolódó számlázási, törölje az erőforráscsoportokat.

    Célszerű használni az erőforrás neve csak kisbetűket, számokat és kötőjeleket tartalmazhat.
    * A **erőforráscsoport** – válassza ki az új létrehozása, és adja meg egy nevet az erőforráscsoport (kis-és nagybetűket).
        * Azt javasoljuk, hogy az erőforráscsoport neve szereplő összes betű kisbetűs lehet.
        * Azt javasoljuk, hogy a kötőjel, majd a monogramját számjegy követ hozzáfűzése: például _wingtip-sa-af1_.
        * Jelöljön ki egy helyet a legördülő listából.

    * A **felhasználói** -javasoljuk, hogy ezt a módot rövid felhasználói érték, például a saját monogramjával, valamint egy számjegy: például _af1_.


1. **Az alkalmazás üzembe helyezése**.

    * Kattintson a gombra kattintva elfogadja a feltételeket és kikötéseket.
    * Kattintson a **Purchase** (Vásárlás) gombra.

1. Három központi telepítések központi telepítési állapot figyelése kattintva **értesítések** (a harang ikonra a keresőmezőbe a jobb). Az alkalmazás telepítéséhez, körülbelül öt percet vesz igénybe.


## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás helyszínek, például koncerttermek jazz treff vagy események üzemeltető sportegyesületek bővíthető. Helyszínek felhasználók (vagy bérlők) Wingtip jegyek események listában, és értékesítés jegyek egyszerűen regisztrálni. Egyes helyszínekkel lekérdezi egy személyre szabott webhely kezelése és a listában azok az események és a jegyet, független és elkülönítése a bérlőktől értékesítés. A színfalak az egyes bérlők lekérdezi egy külön alkalmazáspéldány és az önálló SQL-adatbázis.

1. Nyissa meg az események lapról az egyes külön-külön böngészőlapokon a három bérlők:

    http://events.contosoconcerthall. &lt;Felhasználói&gt;. trafficmanager.net <br>
    http://events.dogwooddojo. &lt;Felhasználói&gt;. trafficmanager.net<br>
    http://events.fabrikamjazzclub. &lt;Felhasználói&gt;. trafficmanager.net

    (csere &lt;felhasználói&gt; a központi telepítés felhasználói értékű).

   ![Események](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)


A bejövő kérelem, az alkalmazás által használt eloszlás [ *Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview). Minden egyes bérlő-specifikus alkalmazás URL-CÍMÉT a tartománynév részeként a bérlő nevét tartalmazza. A bérlő URL-címek közé tartozik az adott *felhasználói* értékét, és ezt a formátumot követi: http://events.&lt; venuename&gt;.&lt; FELHASZNÁLÓI&gt;. trafficmanager.net. Mindegyik bérlő adatbázis helye az alkalmazás beállításaiban a megfelelő telepített alkalmazás szerepel.

Éles környezetben, akkor általában kell létrehoznia a DNS CNAME-rekord [ *vállalati internetes tartomány pont* ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) a traffic manager-profil URL-címét.


## <a name="explore-the-servers-and-tenant-databases"></a>Megismerkedhet a kiszolgálók és adatbázisok bérlői

Vizsgáljuk meg a telepített erőforrások:

1. Az a [Azure-portálon](http://portal.azure.com), keresse meg az erőforráscsoportok listáját.  Kell megjelennie a **wingtip-sa-katalógus -&lt;felhasználói&gt;**  erőforrás csoportot, amely **katalógus-sa -&lt;felhasználói&gt;**  kiszolgáló üzembe helyezéséhez a **tenantcatalog** adatbázis. Emellett meg kell jelennie a három bérlői erőforráscsoportok.

1. Nyissa meg a **wingtip-sa-fabrikam -&lt;felhasználói&gt;**  erőforráscsoport, amelyek a Fabrikam Jazz Club telepítési erőforrásokat tartalmazza.  A **fabrikamjazzclub -&lt;felhasználói&gt;**  kiszolgáló tartalmaz az **fabrikamjazzclub** adatbázis.


Minden egyes bérlő adatbázisa 50 DTU _önálló_ adatbázis.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]

> * A Wingtip jegyek SaaS önálló alkalmazás központi telepítése
> * A kiszolgálók és adatbázisok, az alkalmazás alkotó
> * Mintaerőforrások törlése a kapcsolódó számlázások leállításához


## <a name="additional-resources"></a>További források

<!--* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview) -->
* A több-bérlős SaaS-alkalmazásokkal kapcsolatos tudnivalókért lásd a [*több-bérlős SaaS-alkalmazások tervezési mintáit*](saas-tenancy-app-design-patterns.md)
