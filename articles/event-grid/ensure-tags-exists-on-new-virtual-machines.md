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
ms.openlocfilehash: 8b698659ed91782b80dbefbfea02aa036c09210d
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Azure Automation szolgáltatásbeli integrálható a esemény rács és a Microsoft csapatai

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy esemény rács minta forgatókönyv importálása.
> * Hozzon létre egy nem kötelező Microsoft Teams webhook.
> * A runbook a webhook létrehozása.
> * Esemény rács előfizetéssel, hozzon létre.
> * Hozzon létre egy virtuális Gépet, amely elindítja a runbookot.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy [Azure Automation-fiók](../automation/automation-offering-get-started.md) a runbookot, az Azure Event rács előfizetésből kiváltó tárolásához szükséges.

## <a name="import-an-event-grid-sample-runbook"></a>Egy esemény rács minta forgatókönyv importálása
1. Válassza ki **Automation-fiók**, és válassza ki a **Runbookok** lap.

2. Válassza ki a **Tallózás gyűjtemény** gombra.

    ![A felhasználói Felületről Runbook listája](media/ensure-tags-exists-on-new-virtual-machines/event-grid-runbook-list.png)

3. Keresse meg **esemény rács**, és a runbook importálása az Automation-fiók.

    ![Gyűjteményelem forgatókönyv importálása](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Válassza ki **szerkesztése** tekintse meg a runbook forrást, és válassza ki a **közzététel** gombra.

    ![A felhasználói Felületről runbook közzététele](media/ensure-tags-exists-on-new-virtual-machines/publish-runbook.png)

## <a name="create-an-optional-microsoft-teams-webhook"></a>Egy nem kötelező Microsoft Teams webhook létrehozása
1. A Microsoft Teams, válassza ki **további beállítások** a Tovább gombra a csatorna nevét, és adja **összekötők**.

    ![Microsoft Teams kapcsolatok](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Görgessen végig az összekötők listáját **bejövő Webhook**, és válassza ki **Hozzáadás**.

    ![Microsoft Teams webhook kapcsolat](media/ensure-tags-exists-on-new-virtual-machines/select-teams-webhook.png)

3. Adja meg **AzureAutomationIntegration** nevét, és válassza ki a **létrehozása**.

    ![Microsoft Teams webhook](media/ensure-tags-exists-on-new-virtual-machines/configure-teams-webhook.png)

4. A webhook másolása a vágólapra, és mentse. A webhook URL-cím segítségével adatokat küld a Microsoft Teams.

5. Válassza ki **végzett** a webhook mentése.

## <a name="create-a-webhook-for-the-runbook"></a>A runbook a webhook létrehozása
1. Nyissa meg a figyelés-VMWrite runbookot.

2. Válassza ki **Webhookok**, és válassza ki a **hozzáadása Webhook** gombra.

    ![Webhook létrehozása](media/ensure-tags-exists-on-new-virtual-machines/add-webhook.png)

3. Adja meg **WatchVMEventGrid** nevét. Másolja az URL-címet a vágólapra, és mentse.

    ![Konfigurálja webhook neve](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-name.png)

4. Válassza ki **paraméterek és futtatási beállítások**, és írja be a Microsoft Teams webhook URL-CÍMÉT. Hagyja **WEBHOOKDATA** üres.

    ![Webhook paramétereinek beállítása](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Válassza ki **OK** a runbook Automation-webhook létrehozása.


## <a name="create-an-event-grid-subscription"></a>Egy esemény rács előfizetés létrehozása
1. Az a **Automation-fiók** – Áttekintés lapon jelölje be **esemény rács**.

    ![Rács listában](media/ensure-tags-exists-on-new-virtual-machines/event-grid-list.png)

2. Válassza ki a **Eseményelőfizetés** gombra.

3. Az előfizetés konfigurálása a következő információkat:

    *   Adja meg **AzureAutomation** nevét. 
    *   A **témakörtípus**, jelölje be **Azure-előfizetések**.
    *   Törölje a jelet a **minden eseménytípushoz előfizetni** jelölőnégyzetet.
    *   A **eseménytípusok**, jelölje be **erőforrás írása sikeres**.
    *   A **teljes végponti URL-cím**, írja be a webhook URL-CÍMÉT a figyelési-VMWrite runbookhoz.
    *   A **előtag szűrő**, adja meg azt az előfizetést, és ha az új virtuális gépek megkeresni kívánt erőforrás-csoport létrehozása. Az alábbi példához hasonló /subscriptions/124aa551-849d-46e4-a6dc-0bc4895422aB/resourcegroups/ContosoResourceGroup/providers/Microsoft.Compute/virtualMachines

    ![Rács listában](media/ensure-tags-exists-on-new-virtual-machines/configure-event-grid-subscription.png)

4. Válassza ki **létrehozása** menteni az esemény rács előfizetést.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Hozzon létre egy virtuális Gépet, amely elindítja a runbookot
1. Hozzon létre egy új virtuális gép abban az esetben, ha rács előfizetés előtag szűrő megadott erőforráscsoportban.

2. A figyelési-VMWrite runbook kell meghívni, és a virtuális gép hozzá új címke.

    ![Virtuális gép címke](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Egy új üzenetet küld a Microsoft Teams csatornára.

    ![Microsoft Teams értesítés](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban beállította esemény rács és automatizálás közötti integrációt. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egy esemény rács minta forgatókönyv importálása.
> * Hozzon létre egy nem kötelező Microsoft Teams webhook.
> * A runbook a webhook létrehozása.
> * Esemény rács előfizetéssel, hozzon létre.
> * Hozzon létre egy virtuális Gépet, amely elindítja a runbookot.

> [!div class="nextstepaction"]
> [Hozzon létre és útvonal-esemény rácshoz egyéni események](../event-grid/custom-event-quickstart.md)
