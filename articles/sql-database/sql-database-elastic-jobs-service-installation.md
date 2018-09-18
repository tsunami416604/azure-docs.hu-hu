---
title: Rugalmas adatbázis-feladatok telepítése |} A Microsoft Docs
description: A rugalmas feladat funkció telepítése végig.
services: sql-database
manager: craigg
author: ddove
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: sstein
ms.openlocfilehash: e59fc7caf0fa9f02a2182f86295f1a9e59d98788
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733569"
---
# <a name="installing-elastic-database-jobs-overview"></a>Elastic Database-feladatok telepítésének áttekintése

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]



[**Elastic Database-feladatok** ](sql-database-elastic-jobs-overview.md) PowerShell-lel vagy az Azure Portalon keresztül is telepíthető. Csak akkor, ha a PowerShell-csomag telepítése a PowerShell API-val feladatok létrehozása és kezelése a hozzáférést kaphatnak. Ezenkívül a PowerShell API-k jóval több funkciót kínál, mint a portál ezen a ponton az időben.

Ha már telepített **rugalmas adatbázis-feladatok** egy meglévő a portálon keresztül **rugalmas készlet**, a Powershell legújabb előzetes verziót tartalmaz parancsprogramokat a meglévő telepítés frissítése. Erősen javasoljuk, hogy a telepítés frissítése a legújabb **rugalmas adatbázis-feladatok** összetevők a PowerShell API-kon keresztül új funkcióinak kihasználása érdekében.

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Az ingyenes próbaverzióra, lásd: [az ingyenes próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell. Telepítse a legújabb verzióját használja a [Webplatform-telepítő](http://go.microsoft.com/fwlink/p/?linkid=320376). Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).
* [NuGet parancssori segédprogrammal](https://nuget.org/nuget.exe) az Elastic Database-feladatok csomag telepítéséhez használt. További információkért lásd: http://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Töltse le és az Elastic Database-feladatok PowerShell csomag importálása
1. Indítsa el a Microsoft Azure PowerShell-parancsablakot, és lépjen abba a könyvtárba, ahová letöltötte NuGet parancssori segédprogrammal (nuget.exe).
2. Töltse le és importálja **rugalmas adatbázis-feladatok** csomagját az aktuális könyvtár a következő paranccsal:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    A **rugalmas adatbázis-feladatok** fájlok kerülnek a helyi címtárban lévő szkriptfájlokat **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** ahol *x.x.xxxx.x* tükrözi a verziószám. A PowerShell-parancsmagok (beleértve a szükséges ügyfél .dll) található a **tools\ElasticDatabaseJobs** pillanatképmappa és telepítése, frissítése és eltávolítása a PowerShell-parancsfájlokat is található a **Eszközök** pillanatképmappa.
3. Keresse meg az eszközök pillanatképmappa a Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x mappa alatt írja be a cd-eszközökkel, például:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Hajtsa végre a.\InstallElasticDatabaseJobsCmdlets.ps1 parancsfájlt az ElasticDatabaseJobs könyvtár másolnunk $home\Documents\WindowsPowerShell\Modules. Ezzel is automatikusan importálja a modult használja, például:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Az Elastic Database-feladatok PowerShell-lel összetevőinek telepítése
1. Indítsa el a Microsoft Azure PowerShell-parancsablakot, és keresse meg a \tools pillanatképmappa alatt a Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x mappa: írja be a CD-ről \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Hajtsa végre a.\InstallElasticDatabaseJobs.ps1 PowerShell-parancsfájlt, és adja meg a kért változók értékeit. Ez a szkript létrehoz az ismertetett [Elastic Database-feladatok összetevők és a díjszabás](sql-database-elastic-jobs-overview.md#components-and-pricing) konfigurálása az Azure Cloud Service, a függő összetevők megfelelően használandó együtt.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

A parancs futtatásakor ablak nyílik meg kérése egy **felhasználónév** és **jelszó**. Ez a nem Azure hitelesítő adatait, adja meg a felhasználónevet, és hogy a rendszergazdai hitelesítő adatait a jelszó szeretne létrehozni az új kiszolgáló.

A következő minta hívással megadott paraméterek a kívánt beállítások módosíthatók. A következő további információkkal szolgál az egyes paraméterek viselkedésének:

<table style="width:100%">
  <tr>
    <th>Paraméter</th>
    <th>Leírás</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Itt az Azure-erőforráscsoport neve létrehozni az újonnan létrehozott Azure-összetevőket tartalmazza. Ez a paraméter alapértelmezett értéke "__ElasticDatabaseJob". Ez az érték módosítása nem ajánlott.</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>Az Azure-helyen használható az újonnan létrehozott Azure-összetevőket tartalmaz. Ez a paraméter alapértelmezett értéke az USA középső RÉGIÓJA helyen.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Megadja a szolgáltatás feldolgozók telepítéséhez. Ez a paraméter alapértelmezett értéke 1. Feldolgozók száma is használható, a szolgáltatás horizontális és magas rendelkezésre állást biztosít. Javasoljuk, hogy használja a "2" központi telepítés, a szolgáltatás magas rendelkezésre állást igénylő.</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>A Virtuálisgép-méretet biztosít a használati Felhőszolgáltatáson belül. Ez a paraméter alapértelmezett értéke A0. A0 és A1 és A2/A3 paraméterek értékét elfogadottak, amelyek miatt a feldolgozói szerepkör egy ExtraSmall/kis és közepes vagy nagy méretű, illetve használandó. FO a feldolgozói szerepkör méretek, további információt talál [Elastic Database-feladatok összetevők és a díjszabás](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>A számítási méret biztosít a Standard edition. Ez a paraméter alapértelmezett S0. S0/S1/S2/S3/S4/S6/S9/S12 paraméter értékét elfogadottak, amelyek miatt az Azure SQL Database használata a megfelelő számítási mérete. Az SQL-adatbázis számítási méretek további információkért lásd: [Elastic Database-feladatok összetevők és a díjszabás](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Itt az újonnan létrehozott Azure SQL Database-kiszolgáló rendszergazdai felhasználóneve. Ha nincs megadva, egy PowerShell-hitelesítő adatok ablak megnyílik a hitelesítő adatok kérése.</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Az újonnan létrehozott Azure SQL Database-kiszolgálót biztosít a rendszergazdai jelszót. Ha nincs megadva, egy PowerShell-hitelesítő adatok ablak megnyílik a hitelesítő adatokat kérő.</td>
</tr>
</table>

Is egyszerűsödik a nagy számú elleni adatbázisok nagy számú párhuzamosan futó feladatot kellene rendszerek esetén javasoljuk, hogy adja meg például a Paraméterek: - ServiceWorkerCount 2 - ServiceVmSize A2 - SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>A frissítéstelepítés egy meglévő a rugalmas feladatok összetevők PowerShell-lel
**Elastic Database-feladatok** belül egy meglévő telepítéshez, a méretezés és magas rendelkezésre állású frissíthetők. Ez a folyamat lehetővé teszi, hogy a webszolgáltatás kódjához későbbi verziófrissítések esetén dobja el és hozza létre újra az adatbázis vezérlése nélkül. Ez a folyamat is használható ugyanazon verzióját módosítani a Virtuálisgép-méretet vagy a feldolgozói kiszolgálók számát.

Telepítés Virtuálisgép-méretének frissítéséhez futtassa a következő szkriptet az Ön által választott értékét a frissített paraméterekkel.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Paraméter</th>
  <th>Leírás</th>
</tr>

  <tr>
    <td>ResourceGroupName</td>
    <td>Azonosítja a használatos, ha az Elastic Database feladat összetevők is telepítve az Azure erőforráscsoport-név. Ez a paraméter alapértelmezett értéke "__ElasticDatabaseJob". Ez az érték módosítása nem ajánlott, mivel nem kell megadnia ezt a paramétert.</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>Megadja a szolgáltatás feldolgozók telepítéséhez.  Ez a paraméter alapértelmezett értéke 1.  Feldolgozók száma is használható, a szolgáltatás horizontális és magas rendelkezésre állást biztosít.  Javasoljuk, hogy használja a "2" központi telepítés, a szolgáltatás magas rendelkezésre állást igénylő.</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>A Virtuálisgép-méretet biztosít a használati Felhőszolgáltatáson belül. Ez a paraméter alapértelmezett értéke A0. A0 és A1 és A2/A3 paraméterek értékét elfogadottak, amelyek miatt a feldolgozói szerepkör egy ExtraSmall/kis és közepes vagy nagy méretű, illetve használandó. FO a feldolgozói szerepkör méretek, további információt talál [Elastic Database-feladatok összetevők és a díjszabás](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Az Elastic Database-feladatok a portál használatával összetevőinek telepítése
Miután [rugalmas készlet létrehozása](sql-database-elastic-pool-manage-portal.md), telepítheti **rugalmas adatbázis-feladatok** összetevők a rugalmas készletben található minden egyes adatbázison felügyeleti feladatok végrehajtásának engedélyezéséhez. Mikor eltérően használatával a **rugalmas adatbázis-feladatok** PowerShell API-k, a portál felületén jelenleg korlátozva, csak egy meglévő készletbe történő végrehajtás.

**Várható időtartama:** 10 perc.

1. Az irányítópult-nézet a rugalmas készlet keresztül a [az Azure portal](https://portal.azure.com/#) , kattintson a **létrehozása feladat**.
2. Ha első alkalommal hoz létre egy feladatot, akkor telepítenie kell **rugalmas adatbázis-feladatok** kattintva **előzetes verziójú szolgáltatás FELTÉTELEIT**.
3. A jelölőnégyzetére kattintva fogadja el a feltételeket.
4. Kattintson a "Telepítés szolgáltatások" nézetben **feladat hitelesítő adatai**.
   
    ![A szolgáltatások telepítése][1]
5. Adjon meg egy felhasználónevet és a egy adatbázis-rendszergazda jelszava A telepítés részeként jön létre egy új Azure SQL Database-kiszolgálót. Az új kiszolgáló belül egy új adatbázist, az adatbázis vezérlése, más néven létrejött, és a rugalmas adatbázis-feladatok metaadatot tartalmazott. A felhasználónév és a jelszó itt létrehozott céljából jelentkezik be az adatbázis vezérlése szolgálnak. Külön hitelesítő adatokat ellen a készletben lévő adatbázisok parancsfájl végrehajtására szolgál.
   
    ![Felhasználónév és jelszó létrehozása][2]
6. Kattintson az OK gombra. Az összetevők a rendszer létrehozza az új néhány perc múlva [erőforráscsoport](../azure-resource-manager/resource-group-overview.md). Az új erőforráscsoport a start-táblához, ahogy az alábbi van rögzítve. Miután létrehozott, rugalmas adatbázis-feladatok (felhőalapú szolgáltatás, az SQL Database, Service Bus és tárolás) összes jönnek létre a csoportban.
   
    ![kezdő táblán szereplő erőforráscsoport][3]
7. Ha megpróbálja létrehozni vagy kezelni egy feladatot, amikor a rugalmas adatbázis-feladatok telepíti, így **hitelesítő adatok** az alábbi üzenet jelenik meg.
   
    ![Telepítés folyamatban][4]

Ha az Eltávolítás szükség, törölje az erőforráscsoportot. Lásd: [az Elastic Database feladat összetevők eltávolítása](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>További lépések
A parancsfájl végrehajtása jön létre az egyes adatbázisokon, további információ: a csoport, győződjön meg arról, hogy a megfelelő jogosultságokkal rendelkező hitelesítő adat [SQL-adatbázis védelme](sql-database-manage-logins.md).
Lásd: [létrehozása és a egy rugalmas adatbázis-feladatok kezelése](sql-database-elastic-jobs-create-and-manage.md) a kezdéshez.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
