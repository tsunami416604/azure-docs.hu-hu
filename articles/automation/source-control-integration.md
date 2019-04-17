---
title: Verziókövetés integrálása az Azure Automationben
description: Ez a cikk ismerteti a forráskezelés integrálása a GitHub az Azure Automationben.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 81602f1a30fb753d7a8fcfccace581cd8c7b2f0c
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607092"
---
# <a name="source-control-integration-in-azure-automation"></a>Verziókövetés integrálása az Azure Automation szolgáltatásban

Verziókövetés lehetővé teszi, hogy a forgatókönyvek az Automation fiókhoz a parancsfájlok a GitHub vagy az Azure-Adattárakkal verziókövetési tárházat a naprakészek legyenek. Verziókövetés együttműködhet a csapatával, követi a változásokat, és állítsa vissza a runbookok korábbi verzióira teszi lehetővé. Például verziókövetés lehetővé teszi, hogy a fejlesztési, tesztelési vagy éles környezetben az Automation-fiókok a verziókövetési rendszerben különböző ágak szinkronizálása. Ez megkönnyíti az előléptetni a kódot, amely a fejlesztési környezetben, az Automation-fiók éles üzemben futó tesztelve lett. Verziókövetés integrálása az Automation szolgáltatással támogatja az egyetlen irányú szinkronizálását a verziókövetési tárházzal.

Az Azure Automation három típusú verziókövetés támogatja:

* GitHub
* Az Azure-kódtárak (Git)
* Az Azure-kódtárak (TFVC)

## <a name="pre-requisites"></a>Előfeltételek

* Verziókövetési adattár (GitHub vagy az Azure-kódtárak)
* A [futtató fiókja](manage-runas-account.md)
* Ellenőrizze, hogy a [legújabb Azure-modulok](automation-update-azure-modules.md) az Automation-fiókban

> [!NOTE]
> Verziókövetési szinkronizálási feladatok a felhasználók Automation-fiók alatt fusson, és ugyanez a díjszabás szerint más automatizálási feladatok számlázzuk.

## <a name="configure-source-control---azure-portal"></a>Verziókövetés - Azure portal konfigurálása

Válassza ki az Automation-fiókban lévő **verziókövetés** kattintson **+ Hozzáadás**

![Válassza ki a verziókövetés](./media/source-control-integration/select-source-control.png)

Válasszon **verziókövetési típus**, kattintson a **hitelesítés**. Egy böngészőablakban megnyitja, és megkéri, hogy jelentkezzen be, kövesse az utasításokat a hitelesítés befejezéséhez.

Az a **forrás vezérlő összefoglalás** lapon adja meg az adatokat, és kattintson **mentése**. Az alábbi táblázat a mezők leírását.

|Tulajdonság  |Leírás  |
|---------|---------|
|Forrás vezérlő neve     | A verziókövetési rövid nevét. *Ez a név csak betűket és számokat kell tartalmaznia.*        |
|Verziókövetési típusa     | A forrás-vezérlő típusa. Az elérhető lehetőségek:</br> GitHub</br>Az Azure-kódtárak (Git)</br> Az Azure-kódtárak (TFVC)        |
|Adattár     | A tárház vagy projekt nevét. Az első 200 tárházak adja vissza. Keresse meg a tárházat, írja be a nevét a mezőbe, majd kattintson **keresése a Githubon**.|
|Ág     | Az ág forrásfájlok lekérni. Ág célzó nem érhető el a TFVC verziókövetési típusa.          |
|Mappa elérési útja     | A runbookok szinkronizálása tartalmazó mappát. Példa: /Runbooks </br>*A megadott mappában csak runbookok vannak szinkronizálva. A rekurzió nem támogatott.*        |
|Auto Sync     | Engedélyezi vagy letiltja az automatikus szinkronizálás, amikor a véglegesítés történik a verziókövetési tárházat a         |
|Runbook közzététele     | Ha beállítása **a**, miután a forráskezelőből azok lesz automatikusan közzétenni a runbookok szinkronizálva lesznek.         |
|Leírás     | További információkat egy szövegmező        |

![Forrás vezérlő összegzése](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Győződjön meg arról, a megfelelő fiókkal van bejelentkezve, verziókövetési konfigurálásakor. Ha egy bizonytalan, nyisson meg egy új lapot a böngészőben, és visualstudio.com vagy a github.com jelentkezzen ki, és próbálkozzon újra a csatlakozó verziókövetés.

## <a name="configure-source-control---powershell"></a>Verziókövetés - PowerShell konfigurálása

PowerShell használatával Verziókövetés beállítása az Azure Automationben. Verziókövetés beállítása a PowerShell-parancsmagokkal, a személyes hozzáférési jogkivonat (PAT) van szükség. Használja a [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) a verziókövetési kapcsolat létrehozásához. A parancsmaghoz szükséges, a személyes hozzáférési tokent, megtudhatja, hogyan hozzon létre egy biztonságos karakterláncot, a biztonságos karakterláncot [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Az Azure-kódtárak (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Az Azure-kódtárak (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Személyes hozzáférési jogkivonat engedélyek

Verziókövetés személyes hozzáférési jogkivonatok egyes minimális engedélyekkel kell rendelkeznie. Az alábbi táblázatok tartalmazzák a GitHub-és Azure-Adattárakkal szükséges minimális engedélyeket.

#### <a name="github"></a>GitHub

A GitHub egy személyes hozzáférési jogkivonat létrehozásával kapcsolatos további információkért látogasson el a [létrehozása a személyes hozzáférési tokent a parancssor](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Hatókör  |Leírás  |
|---------|---------|
|**adattár**     |         |
|Adattár: állapota     | Hozzáférés lefoglaltsági állapota         |
|repo_deployment      | Hozzáférési telepítési állapota         |
|public_repo     | Hozzáférés a nyilvános adattár         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Tárház hurkokat írása         |
|read:repo_hook|Olvassa el a tárházat hurkokat|

#### <a name="azure-repos"></a>Azure-beli adattárak

A személyes hozzáférési jogkivonat létrehozása az Azure-Adattárakkal kapcsolatos további információkért látogasson el [hitelesíti a hozzáférést a személyes hozzáférési jogkivonatok](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Hatókör  |
|---------|
|A kód (olvasás)     |
|Projekt és a team (olvasás)|
|Identitás (olvasás)      |
|Felhasználói profil (olvasás)     |
|A munkaelemek (olvasás)    |
|(Olvasása, lekérdezéséhez és kezeléséhez) Szolgáltatáskapcsolatokat<sup>1</sup>    |

<sup>1</sup> a Szolgáltatáskapcsolatokat engedély csak akkor szükséges, ha engedélyezte a autosync.

## <a name="syncing"></a>Szinkronizálás…

Válassza ki a forrás a tábla a a **verziókövetés** lapot. Kattintson a **szinkronizálás indítása** a szinkronizálási folyamat elindításához.

A jelenlegi szinkronizálási feladat vagy a korábbiakat kattintva állapotát is megtekintheti a **feladatok szinkronizálása** fülre. Az a **verziókövetés** legördülő menüben válassza ki a verziókövetési.

![Szinkronizálás állapota](./media/source-control-integration/sync-status.png)

Egy olyan feladatra kattintva lehetővé teszi a feladat kimenetének megtekintéséhez. Az alábbi példa egy forrás vezérlő szinkronizálási feladat kimenete.

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

További naplózás kiválasztásával érhető **az összes napló** a a **Source Control Sync feladat összegzése** lap. Ezek további naplóbejegyzések segíthet a verziókövetés használata során esetlegesen felmerülő problémák hibaelhárításához.

## <a name="disconnecting-source-control"></a>Verziókezelő leválasztása

Nyissa meg bontja a verziókövetési adattár, **verziókövetés** alatt **Fiókbeállítások** az Automation-fiókban.

Válassza ki az eltávolítani kívánt verziókövetés. Az a **forrás vezérlő összefoglalás** kattintson **törlése**.

## <a name="encoding"></a>Encoding

Ha több személynek a különböző szerkesztők módosítani a verziókövetési tárházat a runbookok, nincs arra, hogy közben felmerülő problémák kódolás. Ez a helyzet a runbookban helytelen karaktereket vezethet. Ezzel kapcsolatos további információkért lásd: [gyakori okai kódolási problémák](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>További lépések

További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
