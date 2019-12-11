---
title: Verziókövetés integrációja Azure Automation
description: Ez a cikk ismerteti a verziókövetés integrációját a GitHubon Azure Automationban.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/10/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4e5e0135e4ab56d319c78b7daeab9dd2e9d101f9
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996510"
---
# <a name="source-control-integration-in-azure-automation"></a>Verziókövetés integrálása az Azure Automation szolgáltatásban

A verziókövetés lehetővé teszi, hogy a runbookok az Automation-fiókban naprakész legyen a GitHub vagy az Azure Repos forrás-ellenőrzési tárházában lévő parancsfájlok használatával. A verziókövetés segítségével könnyedén együttműködhet a csapatával, követheti a változásokat, és visszatérhet a runbookok korábbi verzióihoz. A verziókövetés lehetővé teszi például, hogy a verziókövetés különböző ágait szinkronizálja fejlesztési, tesztelési vagy üzemi automatizálási fiókjaiba. Így egyszerűen előléptetheti a fejlesztési környezetben tesztelt kódokat az éles Automation-fiókjába. A verziókövetés és az Automation integrációja támogatja a forrás-ellenőrzési adattárból való egyirányú szinkronizálást.

A Azure Automation háromféle verziókövetés használatát támogatja:

* GitHub
* Azure-repók (git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Előfeltételek

* Egy forrásoldali vezérlő adattár (GitHub vagy Azure Repos)
* [Futtató fiók](manage-runas-account.md)
* Győződjön meg arról, hogy rendelkezik az Automation-fiók [legújabb Azure-moduljaival](automation-update-azure-modules.md) , beleértve a **AzureRM. Profile** modult is. 

> [!NOTE]
> A verziókövetés szinkronizálási feladatai a felhasználók Automation-fiók alatt futnak, és a többi Automation-feladattal megegyező sebességgel lesznek számlázva.

## <a name="configure-source-control---azure-portal"></a>A verziókövetés konfigurálása – Azure Portal

Az Automation-fiókon belül válassza a **verziókövetés** lehetőséget, majd kattintson a **+ Hozzáadás** gombra.

![Forrás vezérlőelem kiválasztása](./media/source-control-integration/select-source-control.png)

Válassza a **forrás vezérlőelem típusa**lehetőséget, majd kattintson a **hitelesítés**elemre. Megnyílik egy böngészőablak, amely felszólítja, hogy jelentkezzen be, és kövesse az utasításokat a hitelesítés befejezéséhez.

A **verziókövetés összegzése** lapon adja meg az adatokat, és kattintson a **Mentés**gombra. Az alábbi táblázat az elérhető mezők leírását tartalmazza.

|Tulajdonság  |Leírás  |
|---------|---------|
|Forrás vezérlőelem neve     | A verziókövetés rövid neve. *A név csak betűket és számokat tartalmazhat.*        |
|Forrás vezérlőelem típusa     | A forrás-ellenőrzési forrás típusa. Az elérhető lehetőségek:</br> GitHub</br>Azure-repók (git)</br> Azure Repos (TFVC)        |
|Adattár     | A tárház vagy a projekt neve. A rendszer az első 200 adattárat adja vissza. Egy adattár kereséséhez írja be a nevet a mezőbe, majd kattintson a **Keresés a githubon**lehetőségre.|
|Ág     | A forrásfájlok lekérésére szolgáló ág. A TFVC nem érhető el a fiókra vonatkozó célzás.          |
|Mappa elérési útja     | A szinkronizálni kívánt runbookok tartalmazó mappa. Példa:/Runbooks </br>*A rendszer csak a megadott mappában lévő runbookok szinkronizálja. A rekurzió nem támogatott.*        |
|<sup>1</sup> . automatikus szinkronizálás     | Be-és kikapcsolja az automatikus szinkronizálást, ha véglegesítenek a verziókövetés adattárában         |
|Runbook közzététele     | Ha **a**be értékre van állítva, a runbookok szinkronizálása után a rendszer automatikusan közzéteszi azokat.         |
|Leírás     | Egy szöveges mező, amely további részleteket biztosít        |

<sup>1</sup> ha engedélyezni szeretné az automatikus szinkronizálást a verziókövetés Azure Repos-integrációjának konfigurálásakor, a projekt rendszergazdájának kell lennie.

![Verziókövetés összegzése](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Előfordulhat, hogy a forrás-ellenőrzési adattárhoz tartozó bejelentkezési azonosító eltér a Azure Portalhoz tartozó bejelentkezési adatoktól. Győződjön meg arról, hogy a verziókövetés konfigurálásakor a forrás vezérlő adattárának megfelelő fiókkal van bejelentkezve. Ha kétségei vannak, nyisson meg egy új fület a böngészőben, és jelentkezzen ki a visualstudio.com vagy a github.com, és próbálkozzon újra a verziókövetés összekapcsolásával.

## <a name="configure-source-control---powershell"></a>Verziókövetés konfigurálása – PowerShell

A PowerShell használatával is konfigurálhatja a verziókövetés Azure Automationban való konfigurálását. A verziókövetés PowerShell-parancsmagokkal történő konfigurálásához egy személyes hozzáférési jogkivonat (PAT) szükséges. A [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) használatával hozza létre a verziókövetés-kapcsolatokat. A parancsmaghoz a személyes hozzáférési jogkivonat biztonságos karakterlánca szükséges, amelyből megtudhatja, hogyan hozhat létre biztonságos karakterláncot: [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure-repók (git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Személyes hozzáférési jogkivonat engedélyei

A verziókövetés bizonyos minimális engedélyeket igényel a személyes hozzáférési jogkivonatokhoz. Az alábbi táblázatok tartalmazzák a GitHub és az Azure Repos minimálisan szükséges engedélyeit.

#### <a name="github"></a>GitHub

A személyes hozzáférési tokenek GitHubon való létrehozásával kapcsolatos további információkért látogasson el [egy személyes hozzáférési jogkivonat létrehozásához a parancssorban](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Hatókör  |Leírás  |
|---------|---------|
|**adattár**     |         |
|repó: állapot     | Hozzáférési véglegesítés állapota         |
|repo_deployment      | Hozzáférés központi telepítési állapota         |
|public_repo     | Nyilvános adattárak elérése         |
|**rendszergazda: repo_hook**     |         |
|írás: repo_hook     | Adattár-hookok írása         |
|olvasás: repo_hook|Adattár-hookok olvasása|

#### <a name="azure-repos"></a>Azure-beli adattárak

Ha további információt szeretne arról, hogyan hozhat létre személyes hozzáférési tokent az Azure Reposban, látogasson el a [személyes hozzáférési jogkivonatokkal történő hitelesítésre](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Hatókör  |
|---------|
|Kód (olvasás)     |
|Projekt és csapat (olvasás)|
|Identitás (olvasás)      |
|Felhasználói profil (olvasás)     |
|Munkaelemek (olvasás)    |
|Szolgáltatási kapcsolatok (olvasási, lekérdezési és kezelési)<sup>1</sup>    |

<sup>1</sup> a szolgáltatási kapcsolatok engedély csak akkor szükséges, ha engedélyezve van az AutoSync.

## <a name="syncing"></a>Szinkronizál

Válassza ki a forrást a **forrás vezérlőelem** oldalon található táblázatból. A szinkronizálási folyamat elindításához kattintson a **szinkronizálás indítása** elemre.

A **szinkronizálási feladatok** lapra kattintva megtekintheti az aktuális szinkronizálási feladat vagy az előzőek állapotát. A **verziókövetés** legördülő menüben válassza ki a forrás vezérlőelemet.

![Szinkronizálás állapota](./media/source-control-integration/sync-status.png)

A feladatokra kattintva megtekintheti a feladatok kimenetét. A következő példa egy verziókövetés-szinkronizálási feladatokból származó kimenet.

```output
========================================================================================================

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



========================================================================================================
```

A további naplózást a **verziókövetés szinkronizálása feladatok összegzése** lapon található **összes napló** lehetőség kiválasztásával érheti el. Ezek a további naplóbejegyzések segíthetnek a verziókövetés használata során felmerülő problémák megoldásában.

## <a name="disconnecting-source-control"></a>Verziókövetés leválasztása

A forrás-felügyeleti adattárból való leválasztáshoz **nyissa meg a verziókövetés elemet** az **Automation-fiók Fiókbeállítások területén** .

Válassza ki az eltávolítani kívánt forrás vezérlőelemet. A **verziókövetés összegzése** lapon kattintson a **Törlés**elemre.

## <a name="encoding"></a>Encoding

Ha több ember szerkeszti a runbookok a különböző szerkesztőkkel, akkor lehetséges, hogy kódolási problémákba ütköznek. Ez a helyzet nem megfelelő karaktereket eredményezhet a runbook. További információ: a [kódolási problémák gyakori okai](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-access-token"></a>A hozzáférési jogkivonat frissítése

Jelenleg a portálon nem lehet frissíteni a hozzáférési tokent a forrás vezérlőelemben. Ha a személyes hozzáférési token lejárt vagy visszavont, a verziókövetés a következő módokon frissíthető egy új hozzáférési jogkivonattal:

* A [REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update)-n keresztül.
* Az [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) parancsmag használatával.

## <a name="next-steps"></a>Következő lépések

További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
