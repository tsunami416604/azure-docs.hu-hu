---
title: "Útmutatás például SQL-adatbázis több-bérlős alkalmazás - Wingtip SaaS |} Microsoft Docs"
description: "Lépéseket és útmutatást biztosít telepítéséhez és futtatásához a több-bérlős mintaalkalmazás, amely az Azure SQL Database, a Wingtip SaaS példa használja."
keywords: "sql database-oktatóanyag"
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: genemi
ms.openlocfilehash: 4c90d70bb3b043ef81a224f0f69107eaa6eb0547
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="guidance-and-tips-for-azure-sql-database-multi-tenant-saas-app-example"></a>Útmutatás és tippek az Azure SQL Database több-bérlős SaaS alkalmazás – példa


## <a name="download-and-unblock-the-wingtip-tickets-saas-database-per-tenant-scripts"></a>Töltse le és a bérlői parancsfájlok Wingtip jegyek SaaS adatbázishoz feloldása

Ha egy zip-fájl külső forrásból letöltött és kibontott végrehajtható tartalma (parancsfájlok, DLL-ek) blokkolhatja Windows. Ha a parancsfájlok kibontása zip-fájl, ***tiltásának feloldása a .zip fájl kibontása előtt az alábbi lépésekkel***. Ez biztosítja, hogy a parancsfájlok futtatásának engedélyezése.

1. Keresse meg a [a Wingtip jegyek SaaS adatbázishoz bérlői GitHub-tárház](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
2. Kattintson a **Klónozás vagy letöltési**.
3. Kattintson a **töltse le a ZIP-** , és mentse a fájlt.
4. Kattintson a jobb gombbal a **WingtipTicketsSaaS-DbPerTenant-master.zip** fájlt, és válassza ki **tulajdonságok**.
5. Az a **általános** lapon jelölje be **Unblock**.
6. Kattintson az **OK** gombra.
7. Bontsa ki a fájlokat.

Parancsfájlok a találhatók a *... \\Tanulási modulok* mappát.


## <a name="working-with-the-wingtip-tickets-saas-database-per-tenant-powershell-scripts"></a>A Wingtip jegyek SaaS adatbázishoz bérlői PowerShell-parancsfájlok használata

A legtöbbet hozhatja ki a minta alaposabban tanulmányozhatja a megadott parancsfájlok kell. Töréspontokat használja, és a parancsfájlok vizsgálata folyamatban van a különböző Szolgáltatottszoftver-minták megvalósított hogyan részleteit lépéseit. Könnyen végighaladhat a megadott parancsfájlok és a modulok legjobb megértéséhez, ajánlott használata a [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>A központi telepítés konfigurációs fájljának frissítése

Szerkessze a **UserConfig.psm1** fájl központi telepítése során beállított erőforrás csoportot és felhasználót értékét:

1. Nyissa meg a *PowerShell ISE* és betöltése... \\Tanulási modulok\\*UserConfig.psm1* 
2. Frissítés *ResourceGroupName* és *neve* a központi telepítés (sorok 10-es és 11 csak) az adott értékkel.
3. A módosítások mentése!

Ezek az értékek beállítás itt egyszerűen révén nem minden parancsfájlban a központi telepítési tartományspecifikus értékeinek frissítéséhez.

### <a name="execute-scripts-by-pressing-f5"></a>A szkripteket az F5 lenyomásával hajtsa végre

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

A központi telepítés először van csatlakozni - két SQL adatbázis-kiszolgálók a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló, és a *katalógus-dpt -&lt;felhasználói&gt;* kiszolgáló. Ahhoz, hogy a sikeres bemutató kapcsolatot, mindkét kiszolgáló rendelkezik egy [tűzfalszabály](sql-database-firewall-configure.md) így minden IP-címen keresztül.


1. Nyissa meg *SSMS* , és kapcsolódjon a *tenants1-dpt -&lt;felhasználói&gt;. database.windows.net* kiszolgáló.
2. Kattintson a **Kapcsolódás** > **Adatbázismotor...** :

   ![katalóguskiszolgáló elemre](media/saas-dbpertenant-wingtip-app-guidance-tips/connect.png)

3. Bemutató hitelesítő adatai: bejelentkezés = *fejlesztő*, jelszó =*P@ssword1*

   ![kapcsolat](media/saas-dbpertenant-wingtip-app-guidance-tips/tenants1-connect.png)

4. Ismételje meg a 2-3, és kapcsolódjon a *katalógus-dpt -&lt;felhasználói&gt;. database.windows.net* kiszolgáló.


Sikeres kapcsolódás után meg kell jelennie mindkét kiszolgálónak. Az adatbázisok listája, a bérlők ellátta függően eltérőek lehetnek.

![Object Explorer](media/saas-dbpertenant-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Következő lépések

[Bérlői alkalmazásonként Wingtip jegyek SaaS adatbázis központi telepítése](saas-dbpertenant-get-started-deploy.md)

