---
title: Útmutatás például SQL-adatbázis több-bérlős alkalmazás - Wingtip SaaS |} Microsoft Docs
description: Lépéseket és útmutatást biztosít telepítéséhez és futtatásához a több-bérlős mintaalkalmazás, amely az Azure SQL Database, a Wingtip jegyek SaaS példa használja.
keywords: sql database-oktatóanyag
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 6c352298c701c827cd01c0ed7f427b7ed6015e29
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646677"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Wingtip jegyek való munkához általános útmutatás mintavételhez SaaS-alkalmazásokhoz

Ez a cikk általános útmutatást nyújt a futó Wingtip jegyek minta SaaS-alkalmazásokhoz Azure SQL Database szolgáltatást használna. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Töltse le és a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok feloldása

Ha egy zip-fájl külső forrásból letöltött és kibontott végrehajtható tartalma (parancsfájlok, DLL-ek) blokkolhatja Windows. Ha a parancsfájlok kibontása zip-fájl, **tiltásának feloldása a .zip fájl kibontása előtt az alábbi lépésekkel**. Ez biztosítja, hogy a parancsfájlok futtatásának engedélyezése.

1. Keresse meg az adatbázis bérleti szabály felfedezése kívánja a Wingtip jegyek SaaS GitHub-tárház: 
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Kattintson a **Klónozás vagy letöltési**.
3. Kattintson a **zip letöltése** , és mentse a fájlt.
4. Kattintson a jobb gombbal a zip-fájl, és válassza ki **tulajdonságok**. A zip-fájl nevét a tárház nevének felel meg. (például) _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Az a **általános** lapon jelölje be **Unblock**.
6. Kattintson az **OK** gombra.
7. Bontsa ki a fájlokat.

Parancsfájlok a találhatók a *... \\Tanulási modulok* mappát.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>A Wingtip jegyek PowerShell-parancsfájlok használata

A legtöbbet hozhatja ki a minta alaposabban tanulmányozhatja a megadott parancsfájlok kell. Töréspontokat használja, és még hajtható végre, és vizsgálja meg, hogyan vannak megvalósítva a különböző Szolgáltatottszoftver-minták a parancsfájlok lépéseit. Könnyen végighaladhat a megadott parancsfájlok és a modulok legjobb megértéséhez, ajánlott használata a [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>A központi telepítés konfigurációs fájljának frissítése

Szerkessze a **UserConfig.psm1** fájl központi telepítése során beállított erőforrás csoportot és felhasználót értékét:

1. Nyissa meg a *PowerShell ISE* és betöltése... \\Tanulási modulok\\*UserConfig.psm1* 
2. Frissítés *ResourceGroupName* és *neve* a központi telepítés (sorok 10-es és 11 csak) az adott értékkel.
3. A módosítások mentése!

Ezek az értékek beállítás itt egyszerűen révén nem minden parancsfájlban a központi telepítési tartományspecifikus értékeinek frissítéséhez.

### <a name="execute-the-scripts-by-pressing-f5"></a>A parancsfájlok végrehajtása az F5 billentyű lenyomásával

Több parancsfájlok használata *$PSScriptRoot* navigáljon a mappákat, és *$PSScriptRoot* csak értékeli a parancsfájlok billentyűkombináció lenyomásával végrehajtásakor **F5**.  Kiemelése és futtató egy kijelölést (**F8**) is hibákat eredményez, így nyomja le az **F5** Ha a parancsfájlok futtatásához.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>A megvalósítás vizsgálatához lépjen végig a szkripteken

A legjobb módszer megértéséhez a parancsfájlok által számukra látni enged ezek lépésenkénti végrehajtás van. Tekintse meg a megadott **bemutató -** parancsfájlok, amelyek jelenleg egy könnyen kövesse az általános munkafolyamat. A **bemutató -** parancsfájlok szemlélteti a lépéseket minden feladatnak, így állítson be töréspontokat és elemezze szükséges mélyebb be az egyes hívások tekintse meg a különböző Szolgáltatottszoftver-minták megvalósítási részleteit.

Tippek az fel, és lépjen az PowerShell-parancsfájlokkal:

- Nyissa meg **bemutató -** a PowerShell ISE parancsfájlok.
- Hajtható végre, vagy folytassa a **F5** (használatával **F8** nem ajánlott, mert *$PSScriptRoot* nem kerül kiértékelésre beállításokat egy parancsfájl futtatásakor).
- Töréspontok elhelyezéséhez kattintson egy sorra, vagy válasszon ki egyet, és nyomja le az **F9** billentyűt.
- Függvény- vagy szkripthívás átlépéséhez használja az **F10** billentyűt.
- Függvény- vagy szkripthívásba az **F11** billentyűvel léphet.
- A jelenlegi függvény- vagy szkripthívásból a **Shift + F11** billentyűkkel léphet ki.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Adatbázisséma vizsgálatát és SQL-lekérdezések végrehajtása SSMS használatával

Használjon [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) csatlakozzanak, és keresse meg az alkalmazás-kiszolgálókat és adatbázisokat.

A központi telepítés kezdetben van a bérlők és a katalógus SQL adatbázis-kiszolgálókhoz való kapcsolódáshoz. A kiszolgálók elnevezési attól függ, hogy az adatbázis bérleti mintát (lásd lent rögzítésen). 

   - **Önálló alkalmazás:** kiszolgálók az egyes bérlők (például) *contosoconcerthall -&lt;felhasználói&gt;*  server) és *katalógus-sa -&lt;felhasználó&gt;*
   - **Adatbázis bérlőnként:** *tenants1-dpt -&lt;felhasználói&gt;*  és *katalógus-dpt -&lt;felhasználói&gt;*  kiszolgálók
   - **Több-bérlős adatbázis:** *tenants1-mt -&lt;felhasználói&gt;*  és *katalógus-mt -&lt;felhasználói&gt;*  kiszolgálók

Ahhoz, hogy a sikeres bemutató kapcsolatot, az összes kiszolgáló rendelkezik egy [tűzfalszabály](sql-database-firewall-configure.md) így minden IP-címen keresztül.


1. Nyissa meg *SSMS* és a bérlők kapcsolódni. A kiszolgáló nevét az adatbázis bérleti mintát (lásd lent rögzítésen) kijelölt függ:
    - **Önálló alkalmazás:** kiszolgálók az egyes bérlők számára (például) *contosoconcerthall -&lt;felhasználói&gt;. database.windows.net*) 
    - **Adatbázis bérlőnként:** *tenants1-dpt -&lt;felhasználói&gt;. database.windows.net*
    - **Több-bérlős adatbázis:** *tenants1-mt -&lt;felhasználói&gt;. database.windows.net* 
2. Kattintson a **Kapcsolódás** > **Adatbázismotor...** :

   ![katalóguskiszolgáló elemre](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Bemutató hitelesítő adatai: bejelentkezés = *fejlesztő*, jelszó =*P@ssword1*

    Az alábbi képen azt mutatja be, a bejelentkezési adatait a következő a *bérlőnként adatbázis* mintát. 
    ![Kapcsolat](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Ismételje meg a 2-3 és az alkalmazáskatalógus-kiszolgálóhoz (lásd lent adott kiszolgálók nevei a kijelölt adatbázis bérleti mintája alapján)
    - **Önálló alkalmazás:** *katalógus-sa -&lt;felhasználói&gt;. database.windows.net*
    - **Adatbázis bérlőnként:** *katalógus-dpt -&lt;felhasználói&gt;. database.windows.net*
    - **Több-bérlős adatbázis:** *katalógus-mt -&lt;felhasználói&gt;. database.windows.net*


Sikeres csatlakozást követően megtekintheti az összes kiszolgálót. Az adatbázisok listája, a bérlők ellátta függően eltérőek lehetnek.

Az alábbi képen azt mutatja be, a bejelentkezés a *bérlőnként adatbázis* mintát.

![Object Explorer](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>További lépések
- [A Wingtip jegyek SaaS önálló alkalmazás központi telepítése](saas-standaloneapp-get-started-deploy.md)
- [Bérlői alkalmazásonként Wingtip jegyek SaaS adatbázis központi telepítése](saas-dbpertenant-get-started-deploy.md)
- [A Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás központi telepítése](saas-multitenantdb-get-started-deploy.md)

