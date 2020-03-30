---
title: Announcment post a lab az Azure DevTest Labs | Microsoft dokumentumok
description: Megtudhatja, hogy miként adhat hozzá közleményt egy laborhoz az Azure DevTest Labsben
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
ms.openlocfilehash: 84120b07de3a03a049493eb973b6dc46f8668387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976451"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Közlemény feladása az Azure DevTest Labs egyik laborjában

Laborrendszergazdaként egyéni értesítést tehet közzé egy meglévő tesztkörnyezetben, hogy értesítse a felhasználókat a labor legutóbbi változásairól vagy kiegészítéseiről. Előfordulhat például, hogy a következőkről szeretné tájékoztatni a felhasználókat:

- Új virtuálisgép-méretek, amelyek elérhetők
- Jelenleg használhatatlan képek
- A tesztkörnyezet-házirendek frissítései

A közzétételt követően a közlemény megjelenik a labor áttekintő oldalán, és a felhasználó további részletekért kiválaszthatja azt.

A bejelentési funkció ideiglenes értesítésekhez használható.  Könnyedén letilthatja a bejelentést, miután már nincs rá szükség.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Értesítés közzétételének lépései meglévő laborban

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
1. Ha szükséges, válassza a **Minden szolgáltatás**lehetőséget, majd válassza a **DevTest Labs** elemet a listából. (Előfordulhat, hogy a tesztkörnyezet már látható az irányítópulton a **Minden erőforrás csoportban).**
1. A laborok listájából válassza ki azt a tesztkörnyezetet, amelyben hirdetményt szeretne közzétenni.
1. A tesztkörnyezet **áttekintése** területen válassza a **Konfiguráció és házirendek**lehetőséget.

    ![Konfiguráció és házirendek gomb](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. A **beállítások**csoportbal válassza a **Laborközlemény**lehetőséget.

    ![A Labor bejelentése gomb](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Ha üzenetet szeretne létrehozni a tesztkörnyezetfelhasználói számára, állítsa **az Engedélyezve értéket** **Igen**értékre.

1. **A lejárati dátum** megadásával megadhatja azt a dátumot és időpontot, amely után a bejelentés már nem jelenik meg a felhasználók számára. Ha nem ad meg lejárati dátumot, a közlemény addig marad, amíg le nem tiltja.

   > [!NOTE]
   > A bejelentés lejárta után már nem jelenik meg a felhasználók számára, de továbbra is megtalálható a **Lab értesítési** ablaktáblájában. Módosíthatja, majd újra engedélyezheti, hogy újra aktív legyen.
   >
   >

1. Adja meg **a Hirdetmény címét** és a **Hirdetmény szövegét**.

   A cím legfeljebb 100 karakter, és megjelenik a felhasználó a labor áttekintése oldalon jelenik meg. Ha a felhasználó kiválasztja a címet, megjelenik a hirdetmény szövege.

   A közlemény szövege elfogadja a jelölést. A bejelentés szövegének beírásakor az üzenetet a képernyő alján található Előnézet területen tekintheti meg.

    ![Labor közlemény képernyőn létrehozni az üzenetet.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Ha a bejelentés készen áll a közzétételre, válaszd a **Mentés** gombot.

Ha már nem szeretné megjeleníteni ezt a közleményt a tesztkörnyezet felhasználóinak, térjen vissza a **Lab közlemény** oldalára, és állítsa **az Engedélyezve értéket** **Nem**értékre. Ha lejárati dátumot adott meg, a hirdetmény automatikusan le lesz tiltva az adott napon és időpontban.

## <a name="steps-for-users-to-view-an-announcement"></a>A közlemény megtekintésének lépései a felhasználók számára

1. Az [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040)válasszon ki egy tesztkörnyezetet.

1. Ha a laborban közzétett egy közleményt, a tesztkörnyezet áttekintése oldal tetején egy tájékoztató jelenik meg. Ez az értesítés a hirdetmény nek a hirdetmény létrehozásakor megadott címe.

    ![Labor közlemény az áttekintés oldalon](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. A felhasználó kiválaszthatja az üzenetet a teljes hirdetmény megtekintéséhez.

    ![További információ a labor bejelentéséhez](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Az Azure Resource Manager-sablon részeként megadhat egy hirdetményt az alábbi példában látható módon:

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

Az Azure Resource Manager-sablont az alábbi módszerek egyikével telepítheti:

- [Azure-portál](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [REST API](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>További lépések
* Ha módosítja vagy beállítja a tesztkörnyezet házirendjét, érdemes lehet közleményt közzétennie a felhasználók tájékoztatására. [A Házirendek és ütemezések beállítása](devtest-lab-set-lab-policy.md) a korlátozások és konvenciók alkalmazásával kapcsolatos információkat tartalmaz az előfizetésben a testreszabott házirendek használatával.
* Fedezze fel a [DevTest Labs Azure Resource Manager QuickStart sablongyűjteményét.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
