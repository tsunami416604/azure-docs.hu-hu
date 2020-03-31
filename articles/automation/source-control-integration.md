---
title: Verziókövetés integrálása az Azure Automation szolgáltatásban
description: Ez a cikk ismerteti a forrásvezérlés integrációja a GitHub az Azure Automationben.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132930"
---
# <a name="source-control-integration-in-azure-automation"></a>Verziókövetés integrálása az Azure Automation szolgáltatásban

 Az Azure Automation forrásvezérlési integrációja támogatja az egyirányú szinkronizálást a forrásellenőrző tárházból. A forrásvezérlés lehetővé teszi, hogy a Runbookok az Automation-fiókban naprakészen tartsa a GitHub- vagy Az Azure-repos forrásvezérlő tárházban lévő parancsfájlokkal. Ez a funkció megkönnyíti a fejlesztői környezetben tesztelt kód előléptetését a termelési Automation-fiókba.
 
 A forrásvezérlésintegráció lehetővé teszi, hogy könnyedén együttműködjön a csapatával, nyomon követheti a változásokat, és visszaállíthatja a runbookok korábbi verzióit. A forrásvezérlés például lehetővé teszi a különböző ágak szinkronizálását a forrásvezérlésben a fejlesztési, tesztelési és éles automatizálási fiókokkal. 

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="source-control-types"></a>Forrásvezérlő típusok

Az Azure Automation háromféle forrásvezérlést támogat:

* GitHub
* Azure-repos (Git)
* Azure repos (TFVC)

## <a name="prerequisites"></a>Előfeltételek

* Forrásvezérlő tárház (GitHub vagy Azure Repos)
* [Futtatás másként fiók](manage-runas-account.md)
* Az [Automation-fiók legújabb Azure-moduljai,](automation-update-azure-modules.md) beleértve a `Az.Accounts` `AzureRM.Profile`modult (az Az modul megfelelője)

> [!NOTE]
> A forrásvezérlés idomai a felhasználó Automation-fiókjában futnak, és a számlázás a többi Automation-feladattal azonos ütemben lesz.

## <a name="configuring-source-control"></a>Forrásvezérlő konfigurálása

Ez a szakasz bemutatja, hogyan konfigurálhatja a forrásvezérlést az Automation-fiókhoz. Használhatja az Azure Portalon vagy a PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Forrásvezérlő konfigurálása az Azure Portalon

Ezzel az eljárással konfigurálhatja a forrásvezérlést az Azure Portal használatával.

1. Az Automation-fiókban válassza a **Forrásvezérlés** lehetőséget, és kattintson **a Hozzáadás**gombra.

    ![Forrásvezérlő kiválasztása](./media/source-control-integration/select-source-control.png)

2. Válassza a **Forrásvezérlő típus t,** majd kattintson **a Hitelesítés gombra.** 

3. Megnyílik egy böngészőablak, és kéri a bejelentkezést. A hitelesítés befejezéséhez kövesse az utasításokat.

4. A Forrásvezérlő összegzése lapon a mezők segítségével töltse ki az alábbiakban meghatározott forrásvezérlő tulajdonságokat. Ha végzett, kattintson a **Mentés** gombra. 

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Forrásvezérlő neve     | A forrásvezérlő rövid neve. Ez a név csak betűket és számokat tartalmazhat.        |
    |Forrásvezérlő típusa     | A forrásvezérlő mechanizmus típusa. Az elérhető lehetőségek:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Adattár     | A tárház vagy projekt neve. Az első 200 adattárak lekérésre kerülnek. Tárház kereséséhez írja be a nevet a mezőbe, és kattintson **a Keresés a GitHubon gombra.**|
    |Elágaztatás     | Ág, amelyből a forrásfájlokat le kell húzni. Az elágazás szerinti célzás nem érhető el a TFVC forrásvezérlő típusához.          |
    |Mappa elérési útja     | A szinkronizálandó runbookokat tartalmazó mappa, például **/Runbooks**. A program csak a megadott mappában lévő runbookokat szinkronizálja. A rekurzió nem támogatott.        |
    |<sup>1.</sup> automatikus szinkronizálás     | Az automatikus szinkronizálás be- és kikapcsolása, amikor véglegesítés történik a forrásvezérlő tárházban.        |
    |Runbook közzététele     | A Be beállítás, ha a runbookok automatikusan közzékerülnek a forrásvezérlőből történő szinkronizálás után, és más módon ki vannak kapcsolva.           |
    |Leírás     | A forrásvezérlővel kapcsolatos további részleteket tartalmazó szöveg.        |

    <sup>1</sup> Az automatikus szinkronizálás engedélyezéséhez az Azure-tárterülettel való forrásvezérlési integráció konfigurálásakor projektrendszergazdának kell lennie.

   ![Forrásvezérlő összegzése](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> A forrásvezérlő tárház bejelentkezése eltérhet az Azure Portalon való bejelentkezéstől. Győződjön meg arról, hogy a forrásvezérlő tárházmegfelelő fiókjával van bejelentkezve a forrásvezérlő konfigurálásakor. Kétség esetén nyisson meg egy új lapot a böngészőben, jelentkezzen ki **a dev.azure.com**, **visualstudio.com**vagy **github.com,** és próbáljon meg újra csatlakozni a forrásvezérlőhöz.

### <a name="configure-source-control-in-powershell"></a>Forrásvezérlő konfigurálása a PowerShellben

A PowerShell segítségével is konfigurálhatja a forrásvezérlést az Azure Automationben. A PowerShell-parancsmagok ehhez a művelethez, személyes hozzáférési jogkivonat (PAT) szükséges. A Forrásvezérlő kapcsolat létrehozásához használja a [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) parancsmagát. Ehhez a parancsmaghoz biztonságos karakterlánc szükséges a PAT-hez. A biztonságos karakterlánc létrehozásáról a [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)című témakörben olvashat.

Az alábbi alszakaszok bemutatják a PowerShell létrehozása a forrásvezérlő kapcsolat A GitHub, az Azure Repos (Git) és az Azure Repos (TFVC). 

#### <a name="create-source-control-connection-for-github"></a>Forrásvezérlő kapcsolat létrehozása a GitHubhoz

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Forrásvezérlő kapcsolat létrehozása az Azure Repos (Git) számára

> [!NOTE]
> Az Azure Repos (Git) egy **dev.azure.com** URL-címet használ, amely dev.azure.com fér hozzá **visualstudio.com helyett,** a korábbi formátumokban használt. A régebbi `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` URL-formátum elavult, de továbbra is támogatott. Az új formátum ajánlott.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Forrásvezérlő kapcsolat létrehozása az Azure Repos (TFVC) számára

> [!NOTE]
> Az Azure Repos (TFVC) egy **dev.azure.com** URL-címet használ, amely dev.azure.com fér hozzá **visualstudio.com helyett, a**korábbi formátumokban használt. A régebbi `https://<accountname>.visualstudio.com/<projectname>/_versionControl` URL-formátum elavult, de továbbra is támogatott. Az új formátum ajánlott.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Személyes jogkivonat (PAT) engedélyei

A forrásvezérléshez minimális engedélyek szükségesek a pat-khoz. A következő alszakaszok tartalmazzák a GitHub és az Azure Repos szükséges minimális engedélyeket.

##### <a name="minimum-pat-permissions-for-github"></a>Minimális PAT-engedélyek a GitHubhoz

Az alábbi táblázat a GitHubhoz szükséges minimális PAT-engedélyeket határozza meg. A PAT-cím GitHubon való létrehozásáról a [Személyes hozzáférési jogkivonat létrehozása a parancssorhoz című témakörben](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)talál további információt.

|Hatókör  |Leírás  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Hozzáférés véglegesítési állapota         |
|`repo_deployment`      | Hozzáférés telepítési állapotának elérése         |
|`public_repo`     | Nyilvános adattárak elérése         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Tárházhorgok írása         |
|`read:repo_hook`|Tárházhorgok olvasása|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Az Azure-repók minimális PAT-engedélyei

Az alábbi lista az Azure-repókhoz szükséges minimális PAT-engedélyeket tartalmazza. A PAT Azure-adatforgalomban való létrehozásáról a [Hozzáférés hitelesítése személyes hozzáférési jogkivonatokkal](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)című témakörben talál további információt.

| Hatókör  |  Hozzáférés típusa  |
|---------| ----------|
| `Code`      | Olvasás  |
| `Project and team` | Olvasás |
| `Identity` | Olvasás     |
| `User profile` | Olvasás     |
| `Work items` | Olvasás    |
| `Service connections` | Olvasás, lekérdezés, kezelés<sup>1</sup>    |

<sup>1</sup> `Service connections` Az engedély csak akkor szükséges, ha engedélyezte az automatikus szinkronizálást.

## <a name="synchronizing"></a>Szinkronizálása

A forrásvezérlővel való szinkronizáláshoz kövesse az alábbi lépéseket. 

1. Jelölje ki a forrást a Forrás vezérlőlap táblájából. 

2. A szinkronizálási folyamat elindításához kattintson a **Szinkronizálás indítása** gombra. 

3. A **Feladatok** szinkronizálása fülre kattintva megtekintheti az aktuális szinkronizálási feladat vagy a korábbiak állapotát. 

4. A **Forrásvezérlés** legördülő menüben válasszon ki egy forrásvezérlő mechanizmust.

    ![Szinkronizálás állapota](./media/source-control-integration/sync-status.png)

5. A feladatra kattintva megtekintheti a feladat kimenetét. A következő példa a kimenet egy forrásvezérlő szinkronizálási feladat.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzureRmEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. További naplózás a Forrásvezérlő szinkronizálási feladat **összegzése** lapján a Minden napló lehetőséget választva érhető el. Ezek a további naplóbejegyzések segíthetnek a forrásvezérlés használata során felmerülő problémák elhárításában.

## <a name="disconnecting-source-control"></a>Forrásvezérlő leválasztása

Kapcsolat bontása a forrásvezérlő tárházról:

1. Nyílt **forráskódú vezérlés** az Automation-fiók **Fiókbeállítások területén.**

2. Válassza ki az eltávolítandó forrásvezérlő mechanizmust. 

3. A Forrásvezérlő összegzése lapon kattintson a **Törlés gombra.**

## <a name="handling-encoding-issues"></a>Kódolási problémák kezelése

Ha többen szerkesztik a runbookokat a forrásvezérlő tárházban különböző szerkesztők használatával, kódolási problémák léphetnek fel. Ha többet szeretne megtudni erről a helyzetről, olvassa el [a kódolási problémák gyakori okait.](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-pat"></a>A PAT frissítése

Jelenleg nem használhatja az Azure Portalon a PAT forrásvezérlő frissítésére. Ha a PAT lejárt vagy visszavonták, a forrásvezérlőt az alábbi módokon frissítheti egy új hozzáférési jogkivonattal:

* Használja a [REST API-t](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Használja az [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) parancsmagát.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a runbook-típusokról, azok előnyeiről és korlátairól, olvassa el az [Azure Automation runbook-típusok című témakört.](automation-runbook-types.md)
