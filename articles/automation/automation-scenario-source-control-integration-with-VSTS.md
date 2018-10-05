---
title: Azure Automation integrálása az Azure DevOps-szolgáltatásokkal verziókövetés
description: A forgatókönyv végigvezeti egy Azure Automation-fiók és az Azure DevOps-szolgáltatásokkal verziókövetés-integráció beállítása.
services: automation
author: eamonoreilly
ms.author: eamono
keywords: az Azure powershell, az Azure DevOps-szolgáltatásokkal, verziókezelő, automatizálás
ms.service: automation
ms.component: process-automation
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: b06e315cc12856976dce87791b423d10f002c6df
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801437"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-azure-devops"></a>Az Azure Automation-forgatókönyv – Automation verziókövetés integrálása az Azure DevOps

> [!NOTE]
> Nincs új felülettel jelentkezik a verziókövetés. Új funkciókkal kapcsolatos további tudnivalókért lásd: [Source-Control (előzetes verzió)](source-control-integration.md).

Ilyen esetben van egy Azure Automation-runbook vagy DSC-konfigurációk verziókövetési kezeléséhez használt Azure DevOps-projekt.

Ez a cikk ismerteti az Azure DevOps integrálása az Azure Automation-környezetet, hogy a folyamatos integráció esetében minden egyes bejelentkezéskor történik.

## <a name="getting-the-scenario"></a>A forgatókönyv beszerzése

Ebben a forgatókönyvben két PowerShell-forgatókönyvek, amelyet közvetlenül importálhat áll a [forgatókönyv-katalógusában](automation-runbook-gallery.md) az Azure Portalon, vagy letölthető a [PowerShell-galériából](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbookok

Forgatókönyv | Leírás|
--------|------------|
Sync-VSTS | Importálja runbookok és a konfigurációk a forráskezelőből az Azure DevOps amikor egy bejelentkezés történik. Ha manuálisan futtatja, importálja, és közzéteszi a runbookok vagy a konfigurációkat az Automation-fiókba.| 
Sync-VSTSGit | Importálhat runbookok és a konfigurációk az Azure DevOps Git verziókövetési amikor egy bejelentkezés történik. Ha manuálisan futtatja, importálja, és közzéteszi a runbookok vagy a konfigurációkat az Automation-fiókba.|

### <a name="variables"></a>Változók

Változó | Leírás|
-----------|------------|
VSToken | Biztonságos változóeszköz hoz létre, amely tartalmazza az Azure DevOps személyes hozzáférési tokent. Megismerheti az Azure DevOps személyes hozzáférési jogkivonat létrehozása a [Azure DevOps-hitelesítés lap](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

## <a name="installing-and-configuring-this-scenario"></a>A forgatókönyv telepítése és konfigurálása

Hozzon létre egy [személyes hozzáférési tokent](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) az Azure DevOps, amellyel szinkronizálni a runbookok és a konfigurációk az automation-fiókba.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Hozzon létre egy [biztonságos változó](automation-variables.md) az automation-fiókban a személyes hozzáférési jogkivonat tárolására, hogy a runbook hitelesítheti az Azure DevOps és szinkronizálása a runbookok és a konfigurációk az Automation-fiókba. Nevezze el az e VSToken.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Importálja a forgatókönyvet, mely szinkronizálja a runbookok és a konfigurációk az automation-fiókba. Használhatja a [Azure DevOps-minta runbook](https://www.powershellgallery.com/packages/Sync-VSTS) vagy a [Azure DevOps és a Git minta runbook](https://www.powershellgallery.com/packages/Sync-VSTSGit) származó a [PowerShell-galériából](https://www.powershellgallery.com) attól függően, hogy az Azure DevOps-adatforrás használata vezérlő vagy a Git Azure DevOps és az automation-fiók üzembe helyezése.

![PowerShell-galéria](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

Mostantól [közzététele](automation-creating-importing-runbook.md#publishing-a-runbook) ezt a runbookot, így hozhat létre egy webhook.

![A runbook pulish](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Hozzon létre egy [webhook](automation-webhooks.md) a szinkronizálás – VSTS runbookhoz és adja meg a paramétereket az alább látható módon. Ellenőrizze, hogy szükség szerinti kiosztását az Azure devops-szolgáltatás hook a webhook URL-címet másolja. A VSAccessTokenVariableName pedig (VSToken) neve, amely tárolja a személyes hozzáférési tokent korábban létrehozott biztonságos változó.

Integrálás az Azure DevOps (szinkronizálási-VSTS.ps1) a következő paramétereket fogadja:

### <a name="sync-vsts-parameters"></a>Szinkronizálás – VSTS-paraméterek

Paraméter | Leírás|
--------|------------|
WebhookData | Ez az Azure DevOps szolgáltatás hook küldött beadni információkat tartalmazza. Ezt a paramétert kell hagyja üresen.| 
ResourceGroup | Azt a nevet, az erőforráscsoport, amely az automation-fiókot.|
AutomationAccountName | Az Azure DevOps szinkronizál automation-fiók neve.|
VSFolder | Az Azure DevOps, ahol a runbookokban és konfigurációkban létezik a mappa neve.|
VSAccount | Az Azure DevOps-szervezet nevét.|
VSAccessTokenVariableName | A biztonságos változó (VSToken), amely tartalmazza az Azure DevOps személyes hozzáférési jogkivonat neve.|

![Webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

Az Azure DevOps a GITTEL (szinkronizálási-VSTSGit.ps1) használatakor a következő paraméterek vesz igénybe.

Paraméter | Leírás|
--------|------------|
WebhookData | A beadni adatokat küld az Azure DevOps szolgáltatás hook tartalmaz. Ezt a paramétert kell hagyja üresen.|
ResourceGroup | Ez az erőforráscsoport, amely az automation-fiók nevére.|
AutomationAccountName | Az Azure DevOps szinkronizál automation-fiók neve.|
VSAccount | Az Azure DevOps-szervezet nevét.|
VSProject | Az Azure DevOps, ahol a runbookokban és konfigurációkban létezik a projekt neve.|
A GitRepo | A Git-adattár neve.|
GitBranch | Az ág az Azure DevOps Git-adattár neve.|
Mappa | Az Azure DevOps Git-ágak a mappa neve.|
VSAccessTokenVariableName | A biztonságos változó (VSToken), amely tartalmazza az Azure DevOps személyes hozzáférési jogkivonat neve.|

![GIT-Webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Hozzon létre egy service hook az Azure DevOps-ellenőrzéshez a mappába, amely ezt a webhookot a kódbeadás. Válassza ki **Web Hooks** a szolgáltatás integrálása egy új előfizetés létrehozásakor. További információ szolgáltatás hurkokat a [Azure fejlesztési és üzemeltetési szolgáltatás hurkokat dokumentáció](https://www.visualstudio.com/docs/marketplace/integrate/service-hooks/get-started).
![Szolgáltatás Hook](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

Most el szeretnék megtenni az összes-ellenőrzéshez a runbookokat és konfigurációkat az Azure DevOps, és rendelkezik ezek automatikusan szinkronizálva az automation-fiókba.

![Szinkronizálási runbook-kimenet](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Ha futtatja a runbook manuálisan anélkül, hogy folyamatban van az Azure DevOps váltott, a webhookdata paraméter üresen hagyhatja, és hajtja végre a teljes szinkronizálás az Azure DevOps mappából a megadott.

Ha szeretné eltávolítani a forgatókönyvet, távolítsa el a szolgáltatás hook Azure DevOps, a runbook és VSToken változó törlése.
