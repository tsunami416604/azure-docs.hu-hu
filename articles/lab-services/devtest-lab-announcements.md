---
title: A labor-announcment közzététele az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan közlemény hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet
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
ms.openlocfilehash: eedab085098c34cdac3c49a3892f7cb0d5824f1b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259921"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Egy közlemény egy laborhoz közzététele az Azure DevTest Labs szolgáltatásban

Tesztlabor-rendszergazdaként felteheti egy meglévő tesztkörnyezetben történő legutóbbi módosítása vagy bővítése a labor létrehozása a felhasználók értesítése egy egyéni közleményt. Például érdemes ellenére tájékoztassa a felhasználókat:

- Új Virtuálisgép-méretek érhetők el
- Használhatatlan rendszerképek
- Laborszabályzatok frissítései

Miután tesznek közzé, az értesítés jelenik meg a labor létrehozása – áttekintés oldalra, és a felhasználó kiválaszthat a részleteket.

A közlemény funkció hivatott ideiglenes értesítésekhez használható.  Könnyedén letilthatja a bejelentés után már nem szükséges.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Lépéseket egy meglévő tesztlabor egy közlemény közzététele

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, válassza ki a **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából. (Előfordulhat, hogy a tesztkörnyezet már látható az irányítópult alatt **összes erőforrás**).
1. Tesztkörnyezetek listájában jelölje ki a labor, amelyekben egy közleményt használni kívánt.  
1. A laborgyakorlat **áttekintése** területen válassza **Konfigurace a zásady**.  

    ![Konfigurace a zásady gomb](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. A bal oldali alatt **beállítások**, jelölje be **labor közlemény**.

    ![Labor közlemény gomb](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Hozzon létre egy üzenetet, a felhasználók számára a laborban, állítsa **engedélyezve** való **Igen**.

1. Megadhat egy **lejárati dátum** , adja meg a dátumot és időpontot, amely után a közlemény többé nem jelenik meg a felhasználók számára. Ha nem adja meg a lejárat dátumát, a közlemény marad, amíg tiltsa le azt.

   > [!NOTE]
   > A közlemény lejárata után ez már nem jelenik meg a felhasználók számára, de továbbra is szerepel a **labor közlemény** ablaktáblán. Módosíthatja azt is, és engedélyezze újra a aktiválható újra.
   >
   >

1. Adjon meg egy **název Oznámení** és a **text Oznámení**.

   A cím legfeljebb 100 karakterből állhat, és a felhasználónak a tesztkörnyezet áttekintése lapon jelenik meg. Ha a felhasználó címe, a közlemény szöveg jelenik meg.

   A közlemény szöveg markdown fogad el. A text oznámení meg, megtekintheti az üzenetet az előzetes verzió területen a képernyő alján.

    ![Labor közlemény képernyő létrehozása az üzenetet.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Válassza ki **mentése** elkészült a közlemény közzététele.

Ha már nem szeretné ezt a hirdetményt megjelenítése a labor felhasználónak, térjen vissza a **labor közlemény** lapon, és állítsa be **engedélyezve** való **nem**. Ha megadott lejárati dátumot, a közlemény le van tiltva automatikusan, hogy a dátum és idő.

## <a name="steps-for-users-to-view-an-announcement"></a>A lépések a felhasználók számára a bejelentés megtekintése

1. Az a [az Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), válassza ki a labor.

1. Ha a labor létrehozása egy közlemény tesznek közzé a hozzá tartozó, egy tájékoztató a labor létrehozása – áttekintés lap tetején látható. Ez a tájékoztató a közlemény létrehozásakor megadott název oznámení.

    ![Labor közlemény, Áttekintés lap](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. A felhasználó kiválaszthat a teljes bejelentés megtekintése az üzenetet.

    ![További információ a labor közlemény](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Az Azure Resource Manager-sablon része egy közlemény adhatja meg az alábbi példában látható módon: 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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

Az Azure Resource Manager-sablon használatával a következő módokon telepítheti:

- [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

## <a name="next-steps"></a>További lépések
* Ha módosít, vagy egy tesztlabor házirend beállítása, érdemes egy értesítés tájékoztatja a felhasználót a közzététele. [Szabályzatok és ütemezések beállítása](devtest-lab-set-lab-policy.md) korlátozások és konvenciói nagyobb figyelmet az előfizetésében alkalmazásával testre szabott házirendekkel kapcsolatos információkat nyújt.
* Fedezze fel a [DevTest Labs Azure Resource Manager gyorsindítási sablon katalógus](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
