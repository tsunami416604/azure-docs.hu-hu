---
title: "Azure Automation integrálása Visual Stuido Team Services verziókezelő |} Microsoft Docs"
description: "A forgatókönyv bemutatja, hogyan egy Azure Automation-fiók és a Visual Stuido Team Services a verziókövetési rendszerrel való integráció beállításával."
services: automation
documentationcenter: 
author: eamono
manager: 
editor: 
keywords: "az Azure powershell, a VSTS, verziókezelő, automatizálás"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.openlocfilehash: 01f9c01c9e04e02dbb548b68cf99684ba6ddd57e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-visual-studio-team-services"></a>Azure Automation forgatókönyv - automatizálási verziókövetés integrálása a Visual Studio Team Services

Ebben a forgatókönyvben egy Azure Automation-forgatókönyveket vagy a DSC-konfigurációk verziókövetési felügyeletéhez használt Visual Studio Team Services projekt rendelkezik.
Ez a cikk ismerteti, hogyan VSTS integrálása az Azure Automation-környezet, így folyamatos integrációt történik, az egyes be.

## <a name="getting-the-scenario"></a>A forgatókönyv beszerzése

Ebben a forgatókönyvben két PowerShell-forgatókönyvek, amelyek közvetlenül importálhatók áll a [forgatókönyvek](automation-runbook-gallery.md) az Azure-portálon vagy letölthető a [PowerShell-galériában](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbookok

Forgatókönyv | Leírás| 
--------|------------|
Szinkronizálási-VSTS | Importálhat forgatókönyvek vagy konfigurációk VSTS verziókezelő végzett egy be. Ha manuálisan futtassa azt importálja, és minden runbookok vagy az Automation-fiók konfigurációk közzététele.| 
Szinkronizálási-VSTSGit | Importálhat forgatókönyvek vagy konfigurációk VSTS a Git verziókezelő végzett egy be. Ha manuálisan futtassa azt importálja, és minden runbookok vagy az Automation-fiók konfigurációk közzététele.|

### <a name="variables"></a>Változók

Változó | Leírás|
-----------|------------|
VSToken | Biztonságos változóeszköz hoz létre, amely tartalmazza a VSTS személyes hozzáférési jogkivonat. Megismerheti a VSTS személyes hozzáférési jogkivonat létrehozásához a [VSTS hitelesítés lap](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview). 
## <a name="installing-and-configuring-this-scenario"></a>A forgatókönyv telepítése és konfigurálása

Hozzon létre egy [személyes hozzáférési jogkivonat](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview) a VSTS, amelyek segítségével a runbookok vagy a beállításokat a rendszerbe az automation-fiók szinkronizálása.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Hozzon létre egy [biztonságos változó](automation-variables.md) a személyes hozzáférési jogkivonat tárolásához, hogy a runbook VSTS hitelesítést, és a runbookok vagy konfiguráció az Automation-fiók szinkronizálása az automation-fiókban. A VSToken nevére. 

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

A forgatókönyv, amely szinkronizálva lesz a runbookok vagy az automation-fiók konfigurációk importálása. Használhatja a [VSTS minta runbook](https://www.powershellgallery.com/packages/Sync-VSTS/1.0/DisplayScript) vagy az [a Git minta runbook VSTS] (https://www.powershellgallery.com/packages/Sync-VSTSGit/1.0/DisplayScript) a PowerShellGallery.com attól függően, hogy a VSTS-forrás használatára vezérlő vagy a Git VSTS, és telepítse az automation-fiók.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

Most [közzététele](automation-creating-importing-runbook.md#publishing-a-runbook) ezt a runbookot, hogy létrehozhasson egy webhook. 
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Hozzon létre egy [webhook](automation-webhooks.md) a Sync-VSTS runbookhoz és adja meg a lent látható módon a paramétereket. Győződjön meg arról, szüksége lesz, a szolgáltatás hook a VSTS másolja át a webhook URL-címét. A VSAccessTokenVariableName esetén (VSToken) ahhoz, hogy a személyes hozzáférési jogkivonat korábban létrehozott biztonságos változó. 

VSTS (szinkronizálási-VSTS.ps1) integrálása lépnek a következő paraméterekkel.
### <a name="sync-vsts-parameters"></a>Szinkronizálási-VSTS-paraméterek

Paraméter | Leírás| 
--------|------------|
WebhookData | A küldött a VSTS szolgáltatás hook beadása információkat tartalmaz. Ez a paraméter kell hagyja üresen.| 
ResourceGroup | Azt a nevet, hogy az automation-fiók megtalálható az erőforráscsoportban.|
AutomationAccountName | Szinkronizálja a VSTS automation-fiók neve.|
VSFolder | Amennyiben a runbookokat és konfigurációk léteznek VSTS mappájában neve.|
VSAccount | A Visual Studio Team Services-fiók nevét.| 
VSAccessTokenVariableName | A biztonságos változó (VSToken), amely tárolja a VSTS személyes hozzáférési jogkivonat neve.| 


![](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

Ha VSTS git (szinkronizálási-VSTSGit.ps1) használ a következő paraméterek fog tartani.

Paraméter | Leírás|
--------|------------|
WebhookData | A küldött a VSTS szolgáltatás hook beadása információkat tartalmaz. Ez a paraméter kell hagyja üresen.| ResourceGroup | Ez az erőforráscsoport, amely az automation-fiók nevét.|
AutomationAccountName | Szinkronizálja a VSTS automation-fiók neve.|
VSAccount | A Visual Studio Team Services-fiók nevét.|
VSProject | Amennyiben a runbookokat és konfigurációk léteznek VSTS a projekt nevét.|
GitRepo | A Git-tárház neve.|
GitBranch | A fiókirodai VSTS Git-tárházban neve.|
Mappa | A VSTS Git ágában mappa neve.|
VSAccessTokenVariableName | A biztonságos változó (VSToken), amely tárolja a VSTS személyes hozzáférési jogkivonat neve.|

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Hozzon létre egy service hook VSTS jelölőnégyzet-modulok a mappába, amely elindítja a kód be a webhook. A szolgáltatás integrálása egy új előfizetés létrehozásakor válassza a webes csatlakozik. További service hurkok kapcsolatos a [VSTS szolgáltatás hurkok dokumentáció](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/get-started).
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

Meg kell tudni hajtsa végre az összes ellenőrzés-modulok a runbookok és a beállításokat a VSTS rendszerbe, és ezek automatikusan be vannak szinkronizálási d azokat az automation-fiók.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Ha futtatja a runbook manuálisan nélkül VSTS által indított alatt, üresen webhookdata paraméter, és a teljes szinkronizálás fog tenni a megadott VSTS mappából.

Ha kívánja, a forgatókönyv eltávolításához távolítsa el a szolgáltatás hook VSTS, a runbookot és a VSToken változó törlése.
