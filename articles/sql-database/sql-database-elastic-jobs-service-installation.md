---
title: A rugalmas adatbázis-feladatok telepítése |} Microsoft Docs
description: Végezze el a rugalmas feladat összetevő telepítése.
services: sql-database
manager: craigg
author: ddove
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 4aeb18a4cdd0fc115c0d604797cf60c867455337
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="installing-elastic-database-jobs-overview"></a>Telepítése rugalmas feladatok – áttekintés
[**Rugalmas adatbázis-feladatok** ](sql-database-elastic-jobs-overview.md) PowerShell vagy az Azure portálon keresztül is telepíthető. Akkor is hozzáférhetnek a PowerShell API használata csak akkor, ha a PowerShell telepítéséhez feladatok létrehozásához és kezeléséhez. Emellett a PowerShell API-k jóval több funkciót kínál a portál mint ezen a ponton a időben.

Ha már telepítette **rugalmas adatbázis-feladatok** a meglévő a portálon keresztül **rugalmas készlet**, Powershell legújabb előzetes parancsfájlok frissíti a meglévő példányt tartalmaz. Erősen ajánlott a legújabb verzióra frissíteni a telepítést **rugalmas adatbázis-feladatok** összetevők a PowerShell API-k segítségével új funkciók előnyeit.

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Ingyenes próbaverzió, lásd: [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell. Telepítse a legújabb verzióját használja a [Webplatform-telepítő](http://go.microsoft.com/fwlink/p/?linkid=320376). Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).
* [NuGet parancssori segédprogram](https://nuget.org/nuget.exe) a rugalmas feladatok telepítéséhez használatos. További információkért lásd: http://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Töltse le és a rugalmas feladatok PowerShell csomag importálása
1. Indítsa el a Microsoft Azure PowerShell-parancsablakot, és keresse meg a könyvtárat, amelybe letöltötte NuGet parancssori segédprogram (nuget.exe).
2. Töltse le és importálja **rugalmas adatbázis-feladatok** csomagot be az aktuális könyvtár és a következő parancsot:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    A **rugalmas adatbázis-feladatok** fájlok kerülnek a helyi címtárszolgáltatásban mappában **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** ahol *x.x.xxxx.x* tükrözi a verziószámot. A PowerShell-parancsmagok (beleértve a szükséges ügyfél .dll) található a **tools\ElasticDatabaseJobs** alkönyvtárát és telepítéséhez, frissítéséhez és eltávolításához a PowerShell-parancsfájlok is találhatók a **eszközök** alkönyvtárát.
3. Keresse meg az eszközök alkönyvtárát Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x mappában lévő írja be a cd-eszközök, például:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Hajtsa végre a ElasticDatabaseJobs directory másolja $home\Documents\WindowsPowerShell\Modules a.\InstallElasticDatabaseJobsCmdlets.ps1 parancsfájlt. Ezzel is automatikusan importálja a modult használja, például:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>A PowerShell használatával rugalmas adatbázis-feladatok összetevőinek telepítése
1. Indítsa el a Microsoft Azure PowerShell-parancsablakot, és keresse meg a Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x mappában \tools alkönyvtár: írja be a cd \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. A PowerShell-parancsfájl.\InstallElasticDatabaseJobs.ps1 hajtható végre, és adja meg a kért változók értékeit. Ezt a parancsfájlt hoz létre ismertetett összetevőkön [rugalmas feladatok összetevők és az árképzés terén](sql-database-elastic-jobs-overview.md#components-and-pricing) konfigurálása az Azure Cloud Service használandó megfelelően a függő összetevők együtt.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

A parancs futtatásakor egy ablak megnyitása megadását kéri a **felhasználónév** és **jelszó**. Ez nem az Azure hitelesítő adatait, írja be a felhasználónevet és jelszót, amely lesz a rendszergazdai hitelesítő adatokat szeretne létrehozni az új kiszolgáló.

A minta betöltéshez a megadott paraméterek módosíthatók a kívánt beállítást. A következő szakasz tartalmaz további információt a minden paraméter működését:

<table style="width:100%">
  <tr>
    <th>Paraméter</th>
    <th>Leírás</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Itt az Azure erőforráscsoport neve az újonnan létrehozott Azure összetevőket tartalmazza. Ez a paraméter alapértelmezett értéke: "__ElasticDatabaseJob". Ez az érték módosítása nem ajánlott.</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>Az újonnan létrehozott Azure-összetevők használt Azure-beli hely biztosít. Ez a paraméter alapértelmezett értéke: USA középső RÉGIÓJA helyét.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Szolgáltatás munkavállalók telepítendő számát jeleníti meg. Ez a paraméter alapértelmezett értéke 1. Feldolgozók nagyobb számú is használható, a szolgáltatás horizontális és magas rendelkezésre állás biztosításához. Javasoljuk, hogy a szolgáltatás nagy rendelkezésre állást igénylő telepítésekhez "2" használja.</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>A Felhőszolgáltatás-felhasználásról a Virtuálisgép-méretet biztosít. Ez a paraméter alapértelmezett értéke: A0. A0/A1/A2 A3 paraméterek értékeit a feldolgozói szerepkör egy: ExtraSmall/kis/közepes vagy nagy mérete, illetve használandó okozó elfogadottak. Fő feldolgozói szerepkör méretét, a további tudnivalókat lásd a [rugalmas feladatok összetevők és az árképzés terén](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>A szolgáltatási szint célkitűzés biztosít a Standard kiadását. Ez a paraméter alapértelmezett értéke: S0. S0/S1/S2/S3/S4/S6/S9/S12 paraméter értékének a megfelelő SLO használni kívánt Azure SQL adatbázis okozó elfogadottak. Az SQL-adatbázis segítségével további információkért lásd: [rugalmas feladatok összetevők és az árképzés terén](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>A rendszergazda felhasználóneve az újonnan létrehozott Azure SQL adatbázis-kiszolgáló biztosítja. Ha nincs megadva, egy PowerShell-hitelesítő adatok ablak nyílik bekéri a hitelesítő adatokat.</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>A rendszergazdai jelszó biztosít az újonnan létrehozott Azure SQL Database-kiszolgálóhoz. Ha nincs megadva, PowerShell hitelesítő adatok megnyílik egy bekéri a hitelesítő adatokat.</td>
</tr>
</table>

Számos, az adatbázisok párhuzamosan futó feladatok nagy számú rendelkező célzó rendszerekhez, javasoljuk, hogy adja meg például a Paraméterek: - ServiceWorkerCount 2 - ServiceVmSize A2 - SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Egy meglévő rugalmas feladatok összetevők telepítésének PowerShell használatával
**Rugalmas adatbázis-feladatok** belül a méretezés és magas rendelkezésre állású egy példánya lehet frissíteni. Ez a folyamat lehetővé teszi, hogy a szolgáltatás kód későbbi verziófrissítések dobja el és hozza létre a feladatvezérlő adatbázishoz nélkül. Ez a folyamat is használhatja ugyanazon verzióját a Virtuálisgép-méretet vagy a kiszolgáló feldolgozó számának módosításához.

A Virtuálisgép-méretet, a telepítés frissítéséhez futtassa a következő parancsfájlt frissíteni, hogy az Ön által választott értékek paraméterekkel.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Paraméter</th>
  <th>Leírás</th>
</tr>

  <tr>
    <td>ResourceGroupName</td>
    <td>A használható, ha a rugalmas adatbázis-feladat összetevőket kezdetben telepített Azure erőforráscsoport-név azonosítja. Ez a paraméter alapértelmezett értéke: "__ElasticDatabaseJob". Ez az érték módosítása nem ajánlott, mivel nem rendelkezik a paraméter megadásával.</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>Szolgáltatás munkavállalók telepítendő számát jeleníti meg.  Ez a paraméter alapértelmezett értéke 1.  Feldolgozók nagyobb számú is használható, a szolgáltatás horizontális és magas rendelkezésre állás biztosításához.  Javasoljuk, hogy a szolgáltatás nagy rendelkezésre állást igénylő telepítésekhez "2" használja.</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>A Felhőszolgáltatás-felhasználásról a Virtuálisgép-méretet biztosít. Ez a paraméter alapértelmezett értéke: A0. A0/A1/A2 A3 paraméterek értékeit a feldolgozói szerepkör egy: ExtraSmall/kis/közepes vagy nagy mérete, illetve használandó okozó elfogadottak. Fő feldolgozói szerepkör méretét, a további tudnivalókat lásd a [rugalmas feladatok összetevők és az árképzés terén](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>A rugalmas adatbázis-feladatok a portál használatával összetevőinek telepítése
Ha elvégezte [egy rugalmas készlet létrehozása](sql-database-elastic-pool-manage-portal.md), telepítése **rugalmas adatbázis-feladatok** összetevőt, a rugalmas készlet minden egyes adatbázison felügyeleti feladatok végrehajtását. Ha eltérően használatával a **rugalmas adatbázis-feladatok** PowerShell API-k, a portál felület korlátozódik jelenleg csak egy meglévő készlet végrehajtása.

**Oktatóanyag áttekintésének várható időtartama:** 10 perc.

1. Az irányítópult nézetben a rugalmas készlet keresztül a [Azure-portálon](https://portal.azure.com/#) , kattintson a **létrehozása feladat**.
2. Ha első alkalommal hoz létre egy feladatot, telepítenie kell **rugalmas adatbázis-feladatok** kattintva **PREVIEW feltételek**.
3. Elfogadja a feltételeket a jelölőnégyzetre kattintva.
4. A "Szolgáltatások telepítése" nézetben kattintson **feladat hitelesítő adatai**.
   
    ![A szolgáltatások telepítése][1]
5. Írjon be egy felhasználónevet és jelszót egy adatbázis-rendszergazdához. A telepítés részeként egy új Azure SQL Database kiszolgáló akkor jön létre. Ez az új kiszolgáló belül egy új adatbázist, a feladatvezérlő adatbázishoz néven létrehozott és használt a rugalmas feladatok metaadatot tartalmaz. A felhasználói nevet és az itt létrehozott jelszót használt a feladatvezérlő adatbázishoz való bejelentkezés céljából. A készletben található adatbázisok parancsfájl végrehajtásának külön hitelesítő adatokat használja.
   
    ![Felhasználónevet és jelszót hozhat létre][2]
6. Kattintson az OK gombra. Az összetevők, a rendszer létrehozza az új néhány perc múlva [erőforráscsoport](../azure-resource-manager/resource-group-overview.md). Az új erőforráscsoport rögzítve a start üzenőfalon, alább látható módon. Létrehozás után, a rugalmas adatbázis-feladatok (felhőalapú szolgáltatás, SQL-adatbázis, a Service Bus és tárolás) csoport létrejönnek.
   
    ![a start Bizottsága erőforráscsoport][3]
7. Ha megpróbálja létrehozása vagy kezelése egy feladatot, amikor a rugalmas feladatok telepíti, így **hitelesítő adatok** a következő üzenet jelenik meg.
   
    ![A folyamatban lévő telepítés][4]

Ha az Eltávolítás szükség, törölje a csoportot. Lásd: [eltávolítása a rugalmas adatbázis-feladat összetevőket](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>További lépések
A parancsfájl végrehajtása az összes adatbázisra, további információ: a csoport jön létre, győződjön meg arról, hogy a megfelelő jogosultsággal rendelkező hitelesítő adatot [SQL-adatbázisok védelme](sql-database-manage-logins.md).
Lásd: [létrehozása és egy rugalmas adatbázis-feladatok kezelése](sql-database-elastic-jobs-create-and-manage.md) a kezdéshez.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
