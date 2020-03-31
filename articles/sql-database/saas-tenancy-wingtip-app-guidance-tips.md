---
title: Több-bérlős alkalmazás példa - Wingtip SaaS
description: Lépéseket és útmutatást tartalmaz az Azure SQL Database-t használó több-bérlős mintaalkalmazás, a Wingtip Tickets SaaS példát használó minta telepítéséhez és futtatásához.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 162d1f269c65ad98afa30e8e96370bbdceca99bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132297"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Általános útmutató a Wingtip jegyek minta SaaS-alkalmazásokhoz való munkához

Ez a cikk általános útmutatást tartalmaz a Wingtip jegyek minta SaaS-alkalmazások, amelyek az Azure SQL Database futtatásához.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>A Wingtip Tickets SaaS szkriptek letöltése és feloldása

Előfordulhat, hogy a Windows blokkolja a végrehajtható tartalmakat (parancsfájlokat, dll-eket), ha a zip fájlokat külső forrásból tölti le és bontja ki. Amikor a parancsfájlokat zip fájlból nyeri ki, **az alábbi lépéseket követve bontsa fel a .zip fájl blokkolását a kibontás előtt.** Ez biztosítja, hogy a parancsfájlok futtathatók.

1. Tallózással keresse meg a Wingtip jegyek SaaS GitHub tártárát a megvizsgálni kívánt adatbázis-bérleti mintához:
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Kattintson **a Klónozás vagy letöltés gombra.**
3. Kattintson **a Zip letöltése gombra,** és mentse a fájlt.
4. Kattintson a jobb gombbal a zip fájlra, és válassza **a Tulajdonságok parancsot.** A zip fájl neve megegyezik a tárpánt nevével. (pl. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Az **Általános** lapon válassza a **Letiltás feloldása**lehetőséget.
6. Kattintson az **OK** gombra.
7. Bontsa ki a fájlokat.

A parancsfájlok a *.. Tanulási \\modulok mappába.*


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>A Wingtip-jegyek PowerShell-parancsfájlok

Ahhoz, hogy a legtöbbet hozza ki a mintából, bele kell merülnie a megadott parancsfájlokba. Használja a töréspontokat, és lépjen végig a parancsfájlok végrehajtásának során, és vizsgálja meg, hogyan valósítják meg a különböző SaaS-mintákat. A [powershell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)használatát javasoljuk, hogy a legjobb megértés érdekében egyszerűen lépkedjen végig a megadott parancsfájlokon és modulokon.

### <a name="update-the-configuration-file-for-your-deployment"></a>A központi telepítés konfigurációs fájljának frissítése

Szerkessze a **UserConfig.psm1** fájlt a telepítés során beállított erőforráscsoporttal és felhasználói értékkel:

1. Nyissa meg a *PowerShell ISE* és a terhelés ... \\Tanulási modulok\\*UserConfig.psm1*
2. Frissítse *a ResourceGroupName* és *a Name értéket* a központi telepítés adott értékeivel (csak a 10. és 11. sorban).
3. Mentse el a változásokat!

Ezek az értékek itt egyszerűen megakadályozza, hogy frissítse ezeket a központi telepítés-specifikus értékeket minden parancsfájlban.

### <a name="execute-the-scripts-by-pressing-f5"></a>A parancsfájlok végrehajtása az F5 billentyű lenyomásával

Számos parancsfájl *$PSScriptRoot* használ a mappákban való navigáláshoz, és *$PSScriptRoot* csak akkor kerül kiértékelésre, ha a parancsfájlokat az **F5**billentyű lenyomásával hajtják végre.A kijelölés kiemelése és futtatása (**F8**) hibákat okozhat, ezért parancsfájlok futtatásakor nyomja le **az F5** billentyűt.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>A megvalósítás vizsgálatához lépjen végig a szkripteken

A legjobb módja annak, hogy megértsék a szkriptek által lépve őket, hogy mit csinálnak. Nézze meg a mellékelt **Demo-szkriptek,** hogy a bemutató egy könnyen követhető magas szintű munkafolyamat. A **Demo-szkriptek** az egyes feladatok végrehajtásához szükséges lépéseket mutatják be, ezért állítsa be a töréspontokat, és mélyebben fúrjon az egyes hívásokba a különböző SaaS-minták megvalósítási részleteinek megtekintéséhez.

Tippek a PowerShell-parancsfájlok felfedezéséhez és a PowerShell-parancsfájlok használatához:

- Nyissa meg a **Demo-** parancsfájlokat a PowerShell ISE-ben.
- Az **F5** végrehajtása vagy folytatása (az **F8** használata nem *ajánlott,* mert $PSScriptRoot nem értékeli ki a parancsfájlok kiválasztásakor).
- Töréspontok elhelyezéséhez kattintson egy sorra, vagy válasszon ki egyet, és nyomja le az **F9** billentyűt.
- Függvény- vagy szkripthívás átlépéséhez használja az **F10** billentyűt.
- Függvény- vagy szkripthívásba az **F11** billentyűvel léphet.
- A jelenlegi függvény- vagy szkripthívásból a **Shift + F11** billentyűkkel léphet ki.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Adatbázisséma vizsgálatát és SQL-lekérdezések végrehajtása SSMS használatával

Az [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) segítségével csatlakozzon és böngésszen az alkalmazáskiszolgálók és adatbázisok között.

A központi telepítés kezdetben bérlők és katalógus SQL Database-kiszolgálók csatlakozni. A kiszolgálók elnevezése az adatbázis-bérleti mintátantól függ (a részleteket lásd alább).

   - **Önálló alkalmazás:** kiszolgálók minden bérlőhöz (pl. *contosoconcerthall-&lt;&gt; Felhasználói* szerver) és *katalógus-sa-&lt;Felhasználó&gt; *
   - **Adatbázis bérlőnként:** *tenants1-dpt-&lt;Felhasználó&gt; * és *katalógus-dpt-&lt;Felhasználói&gt; * kiszolgálók
   - **Több-bérlős adatbázis:** *bérlők1-mt-&lt;Felhasználói&gt; * és *katalógus-mt-&lt;Felhasználói&gt; * kiszolgálók

A sikeres demókapcsolat biztosítása érdekében minden kiszolgáló rendelkezik egy [tűzfalszabállyal,](sql-database-firewall-configure.md) amely lehetővé teszi az összes IP-szolgáltató t.


1. Nyissa meg *az SSMS-t,* és csatlakozzon a bérlőkhöz. A kiszolgáló neve a kiválasztott adatbázis-bérleti mintátantól függ (a részleteket lásd alább):
    - **Önálló alkalmazás:** az egyes bérlők kiszolgálói (pl. *contosoconcerthall-&lt;&gt;Felhasználó .database.windows.net*)
    - **Adatbázis bérlőnként:** *tenants1-dpt-&lt;Felhasználó&gt;.database.windows.net*
    - **Több-bérlős adatbázis:** *tenants1-mt-&lt;Felhasználó&gt;.database.windows.net*
2. Kattintson az > **Adatbázis-motor csatlakoztatása gombra...**: **Connect**

   ![katalóguskiszolgáló elemre](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Demo hitelesítő adatok: Login = *fejlesztő*, Jelszó = *P\@ssword1*

    Az alábbi képen bemutatja a bejelentkezési adatbázis *bérlői* minta.
    ![Kapcsolat](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Ismételje meg a 2-3.
    - **Önálló alkalmazás:** *&lt;katalógus-sa-&gt;Felhasználó .database.windows.net*
    - **Adatbázis bérlőnként:** *katalógus-dpt-&lt;Felhasználó&gt;.database.windows.net*
    - **Több-bérlős adatbázis:** *katalógus-mt-&lt;Felhasználó&gt;.database.windows.net*


Sikeres csatlakozás után látnia kell az összes kiszolgálót. Az adatbázisok listája eltérő lehet, attól függően, hogy a bérlők kiépített.

Az alábbi képen bemutatja a bejelentkezést az *adatbázis bérlői* minta.

![Object Explorer](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>További lépések
- [A Wingtip jegyek SaaS önálló alkalmazás telepítése](saas-standaloneapp-get-started-deploy.md)
- [A Wingtip jegyek SaaS-adatbázis üzembe helyezése bérlőnként](saas-dbpertenant-get-started-deploy.md)
- [A Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése](saas-multitenantdb-get-started-deploy.md)

