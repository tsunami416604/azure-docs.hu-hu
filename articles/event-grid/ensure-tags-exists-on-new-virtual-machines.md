---
title: "Azure Automation szolgáltatásbeli integrálása esemény rács |} Microsoft Docs"
description: "Megtudhatja, hogyan automatikusan címke hozzáadása egy új virtuális gép létrehozásakor, és értesítést küldeni a Microsoft Teams."
keywords: "automatizálás, runbook, csoportok, esemény rács, virtuális gépek, virtuális gép"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: eamono
ms.openlocfilehash: 6798f98755ad1d70d316b074643700f7b3e25ee7
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="integrating-azure-automation-with-event-grid-and-microsoft-teams"></a>Azure Automation szolgáltatásbeli integrálása esemény rács és a Microsoft csapatai

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Esemény rács minta forgatókönyv importálása.
> * Hozzon létre egy nem kötelező csapatok webhook.
> * A runbook a webhook létrehozása.
> * Esemény rács előfizetéssel, hozzon létre.
> * Hozzon létre virtuális Gépet, amely elindítja a runbookot.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre szükség.
+ [Automation-fiók](../automation/automation-offering-get-started.md) az esemény rács előfizetésből elindított runbook tárolásához.

## <a name="import-event-grid-sample-runbook"></a>Esemény rács minta forgatókönyv importálása
1.  Nyissa meg az Automation-fiókot, majd kattintson a Runbookok oldalon.
2.  Kattintson a "Tallózás gyűjtemény" gombra.
![A felhasználói Felületről Runbook listája](media/ensure-tags-exists-on-new-virtual-machines/event-grid-runbook-list.png)
3.  Keresse meg az "Esemény rács", és a runbook importálása az Automation-fiók.
![Gyűjteményelem forgatókönyv importálása](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)
4.  Kattintson az "Edit" tekintse meg a Runbook forrást, és kattintson a "Közzététele" gombra.
![A felhasználói Felületről runbook közzététele](media/ensure-tags-exists-on-new-virtual-machines/publish-runbook.png)

## <a name="create-an-optional-teams-webhook"></a>Egy nem kötelező csapatok webhook létrehozása
1.  A Microsoft Teams a csatorna neve mellett kattintson a beállítások (...), majd válassza az összekötők.
![Csoportok kapcsolatok](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)
2.  Görgessen végig a bejövő Webhook összekötők, és kattintson a Hozzáadás gombra.
![Csoportok webhook kapcsolat](media/ensure-tags-exists-on-new-virtual-machines/select-teams-webhook.png)
3.  Adja meg a AzureAutomationIntegration nevét, és kattintson a Létrehozás gombra.
![Csoportok webhook](media/ensure-tags-exists-on-new-virtual-machines/configure-teams-webhook.png)
4.  A webhook másolja a vágólapra, és mentse. Információ küldése a Microsoft Teams a webhook URL-cím használható.
5.  Válassza ki a webhook mentése történik.

## <a name="create-a-webhook-for-the-runbook"></a>A runbook a webhook létrehozása
1.  Nyissa meg a figyelés-VMWrite runbook.
2.  Kattintson a Webhookokkal, és a webhook Hozzáadás gomb ![webhook létrehozása](media/ensure-tags-exists-on-new-virtual-machines/add-webhook.png)
2.  Adja meg a "WatchVMEventGrid" nevet, és másolja az URL-címet a vágólapra, majd mentse.
![Konfigurálja webhook neve](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-name.png)
3.  Válassza ki a paramétereket, és adja meg a Microsoft Teams webhook URL-CÍMÉT, és hagyja üresen a WEBHOOKDATA.
![Webhook paramétereinek beállítása](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)
4.  Válassza ki a runbook Automation-webhook létrehozásához az OK gombra.

## <a name="create-an-event-grid-subscription"></a>Egy esemény rács előfizetés létrehozása
1.  Kattintson az esemény rács oldalon Automation-fiók áttekintésében.
![Rács listában](media/ensure-tags-exists-on-new-virtual-machines/event-grid-list.png)
2.  Kattintson az új esemény előfizetés gombra.
3.  Az előfizetés konfigurálása a következő információkat:
    *   Adja meg AzureAutomation nevét. 
    *   Tématípus válassza ki az Azure-előfizetések.
    *   Törölje a jelet az "előfizetés összes eseménytípust"
    *   Az eseménytípusok válassza ki az erőforrás írása sikeres.
    *   Előfizető végpont adja meg a figyelés-VMWrite runbook a Webhook URL-CÍMÉT.
    *   Szűrő előtag meg előfizetés és a létrehozott új virtuális gépek megkeresni kívánt erőforráscsoportot. Az alábbi példához hasonló /subscriptions/124aa551-849d-46e4-a6dc-0bc4895422aB/resourcegroups/ContosoResourceGroup/providers/Microsoft.Compute/virtualMachines ![esemény rács listája](media/ensure-tags-exists-on-new-virtual-machines/configure-event-grid-subscription.png)
6.  Kattintson a "Létrehozás" gombra a rács Eseményelőfizetés mentéséhez.

## <a name="create-vm-that-triggers-runbook"></a>Hozzon létre virtuális Gépet, amely elindítja a runbookot
1.  Hozzon létre egy új virtuális gép abban az esetben, ha rács előfizetés előtag szűrő megadott erőforráscsoportban.
2.  A figyelési-VMWrite runbook kell meghívni, és a virtuális gép hozzá új címke.
![VMTag](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)
3.  Egy új üzenet a csapatok csatorna.

![Csoportok értesítési](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban beállította esemény rács és automatizálás közötti integrációt. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Esemény rács minta forgatókönyv importálása.
> * Hozzon létre egy nem kötelező csapatok webhook.
> * A runbook a webhook létrehozása.
> * Esemény rács előfizetéssel, hozzon létre.
> * Hozzon létre virtuális Gépet, amely elindítja a runbookot.

> [!div class="nextstepaction"]
> [Hozzon létre és útvonal-esemény rácshoz egyéni események](../event-grid/custom-event-quickstart.md)
