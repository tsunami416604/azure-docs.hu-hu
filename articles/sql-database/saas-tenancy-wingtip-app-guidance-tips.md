---
title: Útmutató az SQL Database több-bérlős alkalmazás példa – a Wingtip SaaS |} A Microsoft Docs
description: Útmutató és lépéseket biztosít telepítéséhez és futtatásához a több-bérlős mintaalkalmazás, amely az Azure SQL Database, a Wingtip Tickets SaaS-példa.
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
ms.date: 12/18/2018
ms.openlocfilehash: af0e642a1f8ec34e654295ae7dcf2960c8664d91
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565564"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>A Wingtip Tickets használatának általános útmutatást minta SaaS-alkalmazások

Ez a cikk a Wingtip Tickets minta SaaS-alkalmazásokhoz Azure SQL Database használata futó általános útmutatást tartalmaz. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Töltse le és a Wingtip Tickets SaaS-parancsfájlok feloldása

Végrehajtható tartalom (szkriptek, DLL-ek) Windows előfordulhat, hogy blokkolja, amikor a zip-fájlokat egy külső forrásból letöltődnek és ki kell olvasni. Ha a parancsfájlok kibontása zip-fájlból **tiltásának feloldása a .zip fájl beolvasása előtt az alábbi lépésekkel**. Ez biztosítja, hogy a parancsfájlok futtatását engedélyezi.

1. Keresse meg a Wingtip Tickets SaaS GitHub-adattárat a az adatbázis bérlős minta kívánt felfedezése: 
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Kattintson a **Klónozás vagy letöltés**.
3. Kattintson a **zip letöltése** és mentse a fájlt.
4. Kattintson a jobb gombbal a zip-fájlt, és válassza ki **tulajdonságok**. A zip-fájl neve a tárház nevének felel meg. (ex. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Az a **általános** lapon jelölje be **feloldása**.
6. Kattintson az **OK** gombra.
7. Bontsa ki a fájlokat.

Parancsfájlok a találhatók a *... \\Tanulási modulok* mappát.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>A Wingtip Tickets PowerShell-parancsfájlok használata

A legtöbbet hozhassa ki a minta a megadott szkriptek mélyedjen kell. Töréspontok használja, és elvégezhető a parancsfájlok, hajtsa végre, és vizsgálja meg, hogyan valósíthatók meg a különböző SaaS-minták. Egyszerűen elvégezhető a megadott szkriptek és modulok ajánlott megértéséhez, javasoljuk a [PowerShell ISE-ben](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>A központi telepítés konfigurációs fájljának frissítése

Szerkessze a **UserConfig.psm1** az erőforrás-csoport és felhasználó értéke üzembe helyezése során beállított fájlt:

1. Nyissa meg a *PowerShell ISE-ben* és betöltése... \\Tanulási modulok\\*UserConfig.psm1* 
2. Frissítés *ResourceGroupName* és *neve* jellemző egyedi értékekkel (a 10-es és 11 csak vonalakat) az üzembe helyezéshez.
3. A módosítások mentéséhez.

Az értékek beállításával itt egyszerűen révén nem, frissítse az összes parancsfájl-specifikus értékeket.

### <a name="execute-the-scripts-by-pressing-f5"></a>Hajtsa végre a szkripteket az F5 billentyű lenyomásával

Számos szkript használata *$PSScriptRoot* nyissa meg a mappákban és *$PSScriptRoot* csak akkor történik meg parancsfájlok lenyomásával végrehajtásakor **F5**.  Kiemelés, és a kijelöltek futtatása (**F8**) is hibákat eredményezhet, ezért nyomja le az **F5** amikor futtatja a parancsfájlokat.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>A megvalósítás vizsgálatához lépjen végig a szkripteken

A legjobb módja, tudni, hogy a parancsfájlok, vétel őket, hogy mit tesznek azt. Tekintse meg a benne foglalt **Demo -** parancsfájlok, amelyek könnyen hajtsa végre a munkafolyamathoz. A **Demo -** parancsfájlok megjelenítése bemutatják a feladat elvégzéséhez, hogy állítson be töréspontokat, és részletes lépéseit mélyebben az egyes hívásokat a különböző SaaS-minták megvalósítási részleteit megtekintheti.

Tippek a böngészést és a PowerShell-parancsfájlok:

- Nyissa meg **Demo -** szkripteket a PowerShell ISE-ben.
- Hajtsa végre vagy folytassa **F5** (használatával **F8** használata nem javasolt, mert *$PSScriptRoot* nem kerül kiértékelésre, szkript kijelöléseinek futtatásakor).
- Töréspontok elhelyezéséhez kattintson egy sorra, vagy válasszon ki egyet, és nyomja le az **F9** billentyűt.
- Függvény- vagy szkripthívás átlépéséhez használja az **F10** billentyűt.
- Függvény- vagy szkripthívásba az **F11** billentyűvel léphet.
- A jelenlegi függvény- vagy szkripthívásból a **Shift + F11** billentyűkkel léphet ki.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Adatbázisséma vizsgálatát és SQL-lekérdezések végrehajtása SSMS használatával

Használat [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) csatlakozhat, és keresse meg az alkalmazás-kiszolgálókra és adatbázisokra.

Az üzembe helyezés kezdetben a bérlők és a katalógus SQL Database-kiszolgálókra való csatlakozáshoz rendelkezik. A kiszolgálók elnevezési attól függ, hogy az adatbázis bérlős minta (lásd alább a részletekért). 

   - **Önálló alkalmazás:** minden egyes bérlőhöz (például kiszolgálók *contosoconcerthall -&lt;felhasználói&gt;*  kiszolgáló) és *katalógus-sa -&lt;felhasználó&gt;*
   - **Bérlőnkénti adatbázis:** *tenants1-dpt -&lt;felhasználói&gt;*  és *katalógus-dpt -&lt;felhasználói&gt;*  kiszolgálók
   - **Több-bérlős adatbázis:** *tenants1-mt -&lt;felhasználói&gt;*  és *katalógus-mt -&lt;felhasználói&gt;*  kiszolgálók

Ahhoz, hogy egy sikeres bemutató kapcsolat, minden kiszolgáló rendelkezik egy [tűzfalszabály](sql-database-firewall-configure.md) lehetővé teszi az összes IP-címek használatával.


1. Nyissa meg *SSMS* és a bérlők kapcsolódni. A kiszolgáló nevét a database bérlős minta (lásd lent bírálattal) kiválasztott függ:
    - **Önálló alkalmazás:** kiszolgálók (például az egyes bérlők *contosoconcerthall-&lt;User&gt;.database.windows.net*) 
    - **Bérlőnkénti adatbázis:** *tenants1-dpt -&lt;felhasználói&gt;. database.windows.net*
    - **Több-bérlős adatbázis:** *tenants1-mt -&lt;felhasználói&gt;. database.windows.net* 
2. Kattintson a **Kapcsolódás** > **Adatbázismotor...** :

   ![katalóguskiszolgáló elemre](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Bemutató hitelesítő adatai a következők: Bejelentkezés = *fejlesztői*, jelszó = *P@ssword1*

    Az az alábbi képen láthatja a bejelentkezési adatait a következő a *bérlőnkénti adatbázis* mintát. 
    ![kapcsolat](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Ismételje meg a 2-3, és csatlakozzon a katalóguskiszolgálón (lásd lent megadott kiszolgálók nevei a kiválasztott adatbázis-bérlős minta alapján)
    - **Önálló alkalmazás:** *katalógus-sa -&lt;felhasználói&gt;. database.windows.net*
    - **Bérlőnkénti adatbázis:** *katalógus-dpt -&lt;felhasználói&gt;. database.windows.net*
    - **Több-bérlős adatbázis:** *katalógus-mt -&lt;felhasználói&gt;. database.windows.net*


Sikeres csatlakozás után megtekintheti az összes kiszolgálót. Az adatbázisok listája a bérlők kiépítése függően eltérő lehet.

Az alábbi képen azt ismerteti, a bejelentkezés a *bérlőnkénti adatbázis* mintát.

![Object Explorer](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>További lépések
- [A Wingtip Tickets SaaS önálló alkalmazás üzembe helyezése](saas-standaloneapp-get-started-deploy.md)
- [A Wingtip Tickets SaaS adatbázis bérlői alkalmazásonként üzembe helyezése](saas-dbpertenant-get-started-deploy.md)
- [A Wingtip Tickets SaaS több-bérlős adatbázis üzembe helyezése](saas-multitenantdb-get-started-deploy.md)

