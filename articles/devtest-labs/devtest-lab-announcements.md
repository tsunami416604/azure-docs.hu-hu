---
title: Hirdetmény küldése laborba Azure DevTest Labsban | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá hirdetményt laborhoz Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 6381c42114e53068188eb8e81034bd6d2a2665bf
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896129"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Hirdetmény küldése a laborba Azure DevTest Labs

Labor-rendszergazdaként egy meglévő laborban közzétehet egy egyéni bejelentést, amely értesíti a felhasználókat a tesztkörnyezet legutóbbi változásairól vagy kiegészítéseiről. Előfordulhat például, hogy tájékoztatni szeretné a felhasználókat a következőkről:

- Új virtuálisgép-méretek érhetők el
- Jelenleg használhatatlan lemezképek
- A labor-házirendek frissítései

A közzététel után a közlemény megjelenik a labor áttekintés lapján, és a felhasználó további részleteket is kiválaszthat.

A bejelentési funkció ideiglenes értesítésekhez használható.  Ha már nincs rá szükség, egyszerűen letilthatja a bejelentést.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>A hirdetmény egy meglévő laborban való közzétételének lépései

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, válassza a **minden szolgáltatás**lehetőséget, majd válassza a **DevTest Labs** elemet a listából. (Előfordulhat, hogy a labor már megjelenik az irányítópulton az **összes erőforrás**alatt).
1. A Labs listából válassza ki azt a labort, amelyben közzé kívánja venni a bejelentést.
1. A labor **Áttekintés** területén válassza a **konfiguráció és szabályzatok**lehetőséget.

    ![Konfigurálás és házirendek gomb](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. A **Beállítások**területen kattintson a **labor bejelentése**lehetőségre.

    ![Labor bejelentése gomb](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Ha a laborban lévő felhasználók számára szeretne üzenetet létrehozni, állítsa az **Igen**értékre **a beállítást.**

1. Megadhat egy **lejárati dátumot** , amely azt a dátumot és időpontot adja meg, amely után a bejelentés már nem jelenik meg a felhasználók számára. Ha nem ad meg lejárati dátumot, a bejelentés addig marad, amíg le nem tiltja.

   > [!NOTE]
   > A bejelentés lejárta után már nem jelenik meg a felhasználók számára, de továbbra is létezik a **labor bejelentési** ablaktábláján. Szerkesztheti, majd újból engedélyezheti, hogy ismét aktívvá váljon.
   >
   >

1. Adja meg a **hirdetmény címét** és a **hirdetmény szövegét**.

   A cím legfeljebb 100 karakterből állhat, és megjelenik a felhasználó számára a labor Áttekintés oldalán. Ha a felhasználó kiválasztja a címet, megjelenik a hirdetmény szövege.

   A bejelentési szöveg elfogadja a Markdown. A bejelentési szöveg megadásakor a képernyő alján található előnézet területen tekintheti meg az üzenetet.

    ![Az üzenet létrehozásához a labor bejelentési képernyője.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Válassza a **Mentés** lehetőséget, ha a bejelentés készen áll a közzétételre.

Ha már nem szeretné megjeleníteni ezt a hirdetményt a labor felhasználói számára, térjen vissza a **labor bejelentése** oldalra, és állítsa a **nem**értékre **a beállítást.** Ha lejárati dátumot adott meg, a hirdetmény automatikusan le lesz tiltva ezen a napon és időpontban.

## <a name="steps-for-users-to-view-an-announcement"></a>A bejelentések megtekintésének lépései a felhasználók számára

1. A [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)válassza ki a labort.

1. Ha a laborban van közzétett hirdetmény, a tesztkörnyezet Áttekintés oldalának tetején megjelenik egy tájékoztató hirdetmény. Ez az értesítés a hirdetmény létrehozásakor megadott bejelentési cím.

    ![Laboratóriumi közlemény az Áttekintés oldalon](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. A felhasználó kiválaszthatja az üzenetet a teljes bejelentés megtekintéséhez.

    ![További információ a labor bejelentéséről](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Az alábbi példában látható módon megadhat egy bejelentést Azure Resource Manager sablon részeként:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

A következő módszerek egyikével telepítheti Azure Resource Manager-sablont:

- [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [REST API](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>További lépések
* Ha módosítja vagy beállítja a tesztkörnyezet szabályzatát, érdemes lehet közzétenni egy közleményt, amely tájékoztatja a felhasználókat. A [szabályzatok és az ütemtervek beállítása](devtest-lab-set-lab-policy.md) információt nyújt az előfizetésre vonatkozó korlátozások és konvenciók egyéni szabályzatok használatával történő alkalmazásáról.
* Ismerkedjen meg a [DevTest Labs Azure Resource Manager a rövid útmutató sablonjának galériájában](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
