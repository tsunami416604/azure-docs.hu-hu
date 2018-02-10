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
ms.openlocfilehash: 5afccc4aa7b751958952d1401182f93109cff358
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-visual-studio-team-services"></a>Azure Automation forgatókönyv - automatizálási verziókövetés integrálása a Visual Studio Team Services

Ebben a forgatókönyvben egy Azure Automation-forgatókönyveket vagy a DSC-konfigurációk verziókövetési felügyeletéhez használt Visual Studio Team Services projekt rendelkezik.
Ez a cikk ismerteti, hogyan VSTS integrálása az Azure Automation-környezet, így folyamatos integrációt történik, az egyes be.

## <a name="getting-the-scenario"></a>A forgatókönyv beszerzése

Ebben a forgatókönyvben két PowerShell-forgatókönyvek, amelyek közvetlenül importálhatók áll a [forgatókönyvek](automation-runbook-gallery.md) az Azure-portálon vagy letölthető a [PowerShell-galériában](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbookok

Forgatókönyv | Leírás| 
--------|------------|
Sync-VSTS | Importálhat forgatókönyvek vagy konfigurációk VSTS verziókezelő végzett egy be. Ha manuálisan futtassa importálja, és teszi közzé a runbookok vagy a beállításokat az Automation-fiók rendszerbe.| 
Sync-VSTSGit | Importálhat forgatókönyvek vagy konfigurációk VSTS a Git verziókezelő végzett egy be. Ha manuálisan futtassa importálja, és teszi közzé a runbookok vagy a beállításokat az Automation-fiók rendszerbe.|

### <a name="variables"></a>Változók

Változó | Leírás|
-----------|------------|
VSToken | Biztonságos változóeszköz létrehozása, amely tartalmazza a VSTS személyes hozzáférési jogkivonat. Megismerheti a VSTS személyes hozzáférési jogkivonat létrehozásához a [VSTS hitelesítés lap](/vsts/accounts/use-personal-access-tokens-to-authenticate).
## <a name="installing-and-configuring-this-scenario"></a>A forgatókönyv telepítése és konfigurálása

Hozzon létre egy [személyes hozzáférési jogkivonat](/vsts/accounts/use-personal-access-tokens-to-authenticate) a VSTS, amelyekkel a runbookok vagy a beállításokat a rendszerbe az automation-fiók szinkronizálása.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Hozzon létre egy [biztonságos változó](automation-variables.md) a személyes hozzáférési jogkivonat tárolásához, hogy a runbook VSTS hitelesítést, és a runbookok vagy konfiguráció az Automation-fiók szinkronizálása az automation-fiókban. A VSToken nevére. 

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

A forgatókönyv Szinkronizáló a runbookok vagy az automation-fiók konfigurációk importálása. Használhatja a [VSTS minta runbook](https://www.powershellgallery.com/packages/Sync-VSTS/1.0/DisplayScript) vagy az [a Git minta runbook VSTS] (https://www.powershellgallery.com/packages/Sync-VSTSGit/1.0/DisplayScript) a PowerShellGallery.com attól függően, hogy a VSTS-forrás használatára vezérlő vagy a Git VSTS, és telepítse az automation-fiók.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

Most [közzététele](automation-creating-importing-runbook.md#publishing-a-runbook) ezt a runbookot, hogy létrehozhasson egy webhook. 
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Hozzon létre egy [webhook](automation-webhooks.md) a Sync-VSTS runbookhoz és adja meg a lent látható módon a paramétereket. Ellenőrizze, hogy a szolgáltatás hook a VSTS a szükség szerinti másolja át a webhook URL-címét. A VSAccessTokenVariableName esetén (VSToken) ahhoz, hogy a személyes hozzáférési jogkivonat korábban létrehozott biztonságos változó. 

VSTS (szinkronizálási-VSTS.ps1) integrálása a következő paramétereket fogadja:
### <a name="sync-vsts-parameters"></a>Szinkronizálási-VSTS-paraméterek

Paraméter | Leírás| 
--------|------------|
WebhookData | Ez a VSTS szolgáltatás hook küldött beadása információkat tartalmazza. Ez a paraméter kell hagyja üresen.| 
ResourceGroup | Azt a nevet, hogy az automation-fiók megtalálható az erőforráscsoportban.|
AutomationAccountName | A VSTS szinkronizál automation-fiók neve.|
VSFolder | Amennyiben a runbookokat és konfigurációk léteznek VSTS mappájában neve.|
VSAccount | A Visual Studio Team Services-fiók nevét.| 
VSAccessTokenVariableName | A biztonságos változó (VSToken), amely tárolja a VSTS személyes hozzáférési jogkivonat neve.| 


![](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

Ha VSTS git (szinkronizálási-VSTSGit.ps1) használ a következő paraméterek fog tartani.

Paraméter | Leírás|
--------|------------|
WebhookData | A küldött a VSTS szolgáltatás hook beadása információkat tartalmaz. Ez a paraméter kell hagyja üresen.| ResourceGroup | Ez az erőforráscsoport, amely az automation-fiók nevét.|
AutomationAccountName | A VSTS szinkronizál automation-fiók neve.|
VSAccount | A Visual Studio Team Services-fiók nevét.|
VSProject | Amennyiben a runbookokat és konfigurációk léteznek VSTS a projekt nevét.|
GitRepo | A Git-tárház neve.|
GitBranch | A fiókirodai VSTS Git-tárházban neve.|
Mappa | A VSTS Git ágában mappa neve.|
VSAccessTokenVariableName | A biztonságos változó (VSToken), amely tárolja a VSTS személyes hozzáférési jogkivonat neve.|

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Hozzon létre egy service hook VSTS jelölőnégyzet-modulok a mappába, amely elindítja a kód be a webhook. Válassza ki **webes csatlakozik** a szolgáltatás integrálása egy új előfizetés létrehozásakor. További service hurkok kapcsolatos a [VSTS szolgáltatás hurkok dokumentáció](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/get-started).
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

Most kell tudni hajtsa végre az összes ellenőrzés-modulok a runbookok és a beállításokat a VSTS rendszerbe, és ezek automatikusan be az automation-fiók synched rendelkezik.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Ha ez a forgatókönyv manuálisan nélkül futtatja VSTS által indított alatt, üresen webhookdata paraméter, és a VSTS mappából megadott egy teljes szinkronizálást végez el.

Ha kívánja, a forgatókönyv eltávolításához távolítsa el a szolgáltatás hook VSTS, a runbookot és a VSToken változó törlése.
