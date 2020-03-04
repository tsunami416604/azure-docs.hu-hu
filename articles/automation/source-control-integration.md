---
title: Verziókövetés integrálása az Azure Automation szolgáltatásban
description: Ez a cikk ismerteti a verziókövetés integrációját a GitHubon Azure Automationban.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: eef67ca8111983adb4d9994894ba215240daee6f
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253734"
---
# <a name="source-control-integration-in-azure-automation"></a>Verziókövetés integrálása az Azure Automation szolgáltatásban

 A verziókövetés integrációja Azure Automation támogatja az egyirányú szinkronizálást a verziókövetés adattárból. A verziókövetés lehetővé teszi, hogy a runbookok az Automation-fiókban naprakészek maradjanak a GitHub vagy az Azure Repos Source Control adattárában található parancsfájlok használatával. Ez a szolgáltatás megkönnyíti a fejlesztési környezetben tesztelt kód előállítását az üzemi Automation-fiókba.
 
 A verziókövetés integrációjának használatával könnyedén dolgozhat a csapatával, követheti a változásokat, és visszatérhet a runbookok korábbi verzióihoz. A verziókövetés lehetővé teszi például a verziókövetés különböző ágainak szinkronizálását a fejlesztési, tesztelési és üzemi Automation-fiókokkal. 

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Verziókövetés típusai

A Azure Automation háromféle verziókövetés használatát támogatja:

* GitHub
* Azure-repók (git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Előfeltételek

* Egy forrásoldali vezérlő adattár (GitHub vagy Azure Repos)
* [Futtató fiók](manage-runas-account.md)
* A [legújabb Azure-modulok](automation-update-azure-modules.md) az Automation-fiókban, beleértve az az **. accounts** modult (az az Module AzureRM. profil)

> [!NOTE]
> A verziókövetés szinkronizációs feladatai a felhasználó Automation-fiókjában futnak, és a többi Automation-feladattal megegyező sebességgel lesznek számlázva.

## <a name="configuring-source-control"></a>A verziókövetés konfigurálása

Ez a szakasz azt ismerteti, hogyan konfigurálható a verziókövetés az Automation-fiókhoz. Használhatja a Azure Portal vagy a PowerShellt is.

### <a name="configure-source-control----azure-portal"></a>A verziókövetés konfigurálása – Azure Portal

Ezzel az eljárással konfigurálhatja a verziókövetés használatát a Azure Portal használatával.

1. Az Automation-fiókon belül válassza a **verziókövetés** lehetőséget, majd kattintson a **+ Hozzáadás**gombra.

    ![Forrás vezérlőelem kiválasztása](./media/source-control-integration/select-source-control.png)

2. Válassza a **forrás vezérlőelem típusát**, majd kattintson a **hitelesítés**elemre. 

3. Megnyílik egy böngészőablak, amely felszólítja a bejelentkezésre. A hitelesítés befejezéséhez kövesse az utasításokat.

4. A **verziókövetés összegzése** lapon használja a mezőket az alább megadott verziókövetés tulajdonságainak kitöltéséhez. Ha elkészült, kattintson a **Mentés** gombra. 

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Forrás vezérlőelem neve     | A verziókövetés rövid neve. A név csak betűket és számokat tartalmazhat.        |
    |Forrás vezérlőelem típusa     | A forrás-ellenőrzési mechanizmus típusa. Az elérhető lehetőségek:</br> GitHub</br>Azure-repók (git)</br> Azure Repos (TFVC)        |
    |Tárház     | A tárház vagy a projekt neve. A rendszer beolvassa az első 200 adattárat. Egy adattár kereséséhez írja be a nevet a mezőbe, majd kattintson a **Keresés a githubon**lehetőségre.|
    |Ág     | A forrásfájlokat lekérő ág. A TFVC nem érhető el a fiókra vonatkozó célzás.          |
    |Mappa elérési útja     | A szinkronizálni kívánt runbookok tartalmazó mappa, például/Runbooks. A rendszer csak a megadott mappában lévő runbookok szinkronizálja. A rekurzió nem támogatott.        |
    |<sup>1</sup> . automatikus szinkronizálás     | Az automatikus szinkronizálás bekapcsolásának vagy kikapcsolásának beállítása a verziókövetés adattárában.        |
    |Runbook közzététele     | Annak beállítása, hogy a runbookok automatikusan közzé legyenek-e téve a forrás-vezérlőelemről való szinkronizálás után, vagy más módon.           |
    |Leírás     | A verziókövetés további részleteit megadó szöveg        |

    <sup>1</sup> ha engedélyezni szeretné az automatikus szinkronizálást a verziókövetés Azure Repos-integrációjának konfigurálásakor, a projekt rendszergazdájának kell lennie.

   ![Verziókövetés összegzése](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Előfordulhat, hogy a forrásként szolgáló adattárházhoz tartozó bejelentkezési azonosító eltér a Azure Portalhoz tartozó bejelentkezési adatoktól. Győződjön meg arról, hogy a verziókövetés konfigurálásakor a forrás vezérlő adattárának megfelelő fiókkal van bejelentkezve. Ha kétségei vannak, nyisson meg egy új fület a böngészőben, jelentkezzen ki a visualstudio.com vagy a github.com-ből, és próbálkozzon újra a forrás-vezérlőelemmel való csatlakozással.

### <a name="configure-source-control----powershell"></a>Verziókövetés konfigurálása – PowerShell

A PowerShell használatával is konfigurálhatja a verziókövetés Azure Automationban való konfigurálását. Ehhez a művelethez a PowerShell-parancsmagok használatához személyes hozzáférési jogkivonat (PAT) szükséges. A [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) parancsmaggal hozza létre a verziókövetés-kapcsolatokat. Ehhez a parancsmaghoz biztonságos karakterlánc szükséges a PAT számára. A biztonságos sztringek létrehozásáról további információt a következő témakörben talál: [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Az alábbi alszakaszok a GitHub, az Azure Repos (git) és az Azure Repos (TFVC) forrás-ellenőrzési kapcsolatainak létrehozását szemléltetik.

#### <a name="create-source-control-connection-for-github"></a>Verziókövetés-kapcsolatok létrehozása a GitHubhoz

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Adatforrás-vezérlési kapcsolatok létrehozása az Azure Repos (git) szolgáltatáshoz

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Adatforrás-vezérlési kapcsolatok létrehozása az Azure Repos (TFVC) számára

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Személyes hozzáférési jogkivonat (PAT) engedélyei

A verziókövetés bizonyos minimális engedélyeket igényel a PATs számára. A következő alszakaszok tartalmazzák a GitHub és az Azure Repos minimálisan szükséges engedélyeit.

##### <a name="minimum-pat-permissions-for-github"></a>A GitHub minimálisan szükséges engedélyei

A következő táblázat a GitHubhoz szükséges minimális PAT-engedélyeket határozza meg. A PAT a GitHubon való létrehozásával kapcsolatos további információkért lásd: [személyes hozzáférési jogkivonat létrehozása a parancssorhoz](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Hatókör  |Leírás  |
|---------|---------|
|**adattár**     |         |
|repó: állapot     | Hozzáférési véglegesítés állapota         |
|repo_deployment      | Hozzáférés központi telepítési állapota         |
|public_repo     | Nyilvános adattárak elérése         |
|**rendszergazda: repo_hook**     |         |
|write:repo_hook     | Adattár-hookok írása         |
|read:repo_hook|Adattár-hookok olvasása|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Az Azure-adattárakhoz minimálisan szükséges PAT-engedélyek

Az alábbi lista meghatározza az Azure Repos minimálisan szükséges PAT-engedélyeit. További információ a PAT Azure Repos-beli létrehozásáról: [hozzáférés hitelesítése személyes hozzáférési jogkivonatokkal](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Hatókör  |  Hozzáférés típusa  |
|---------| ----------|
| Kód      | Olvasás  |
| Projekt és csapat | Olvasás |
| Identitás | Olvasás     |
| Felhasználói profil | Olvasás     |
| Munkaelemek | Olvasás    |
| Szolgáltatási kapcsolatok | Olvasás, lekérdezés, kezelés<sup>1</sup>    |

<sup>1</sup> a szolgáltatási kapcsolatok engedély csak akkor szükséges, ha engedélyezve van az AutoSync.

## <a name="synchronizing"></a>Szinkronizálása

A következő lépések végrehajtásával szinkronizálhat a verziókövetés használatával. 

1. Válassza ki a forrást a **forrás vezérlőelem** oldalon található táblázatból. 

2. A szinkronizálási folyamat elindításához kattintson a **szinkronizálás indítása** elemre. 

3. A **szinkronizálási feladatok** lapra kattintva megtekintheti az aktuális szinkronizálási feladat vagy az előzőek állapotát. 

4. **A verziókövetés legördülő menüben** válassza ki a forrás vezérlő mechanizmust.

    ![Szinkronizálás állapota](./media/source-control-integration/sync-status.png)

5. A feladatokra kattintva megtekintheti a feladatok kimenetét. A következő példa egy verziókövetés-szinkronizálási feladatokból származó kimenet.

    ```output
    ============================================================================

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

     =========================================================================

    ```

6. A további naplózást a **verziókövetés szinkronizálása feladatok összegzése** lapon található **összes napló** lehetőség kiválasztásával érheti el. Ezek a további naplóbejegyzések segíthetnek a verziókövetés használata során felmerülő problémák megoldásában.

## <a name="disconnecting-source-control"></a>Verziókövetés leválasztása

A forrás-felügyeleti adattárból való leválasztáshoz:

1. Az **Automation-fiók Fiókbeállítások területén** nyissa meg a **verziókövetés** csomópontot.

2. Válassza ki az eltávolítandó verziókövetés-mechanizmust. 

3. A **verziókövetés összegzése** lapon kattintson a **Törlés**elemre.

## <a name="handling-encoding-issues"></a>Kódolási problémák kezeléséhez

Ha több ember szerkeszti a runbookok a különböző szerkesztők használatával, a kódolási problémák léphetnek fel. Ha többet szeretne megtudni erről a helyzetről, tekintse meg a [kódolási problémák gyakori okait](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues) .

## <a name="updating-the-pat"></a>A PAT frissítése

Jelenleg nincs mód arra, hogy a Azure Portal használatával frissítse a PATt a forrás vezérlőelemben. Miután a PAT lejárt vagy vissza lett vonva, az alábbi módszerek egyikével frissítheti a verziókövetés új hozzáférési jogkivonattal:

* Használja a [REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Használja az [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) parancsmagot.

## <a name="next-steps"></a>Következő lépések

További információ a runbook típusairól és azok előnyeiről és korlátairól: [Azure Automation runbook-típusok](automation-runbook-types.md).
