---
title: Update Management engedélyezése Azure Resource Manager sablon használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható Azure Resource Manager-sablon a Update Management engedélyezéséhez.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/10/2020
ms.openlocfilehash: c4b29db8bbcb741116fcd425b4489973228066e6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021595"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Az Update Management engedélyezése Azure Resource Manager-sablonnal

Az erőforráscsoport Azure Automation Update Management funkciójának engedélyezéséhez használhat egy [Azure Resource Manager sablont](../../azure-resource-manager/templates/template-syntax.md) . Ez a cikk egy minta sablont tartalmaz, amely automatizálja a következőket:

* Azure Monitor Log Analytics munkaterület létrehozása.
* Azure Automation-fiók létrehozása.
* Az Automation-fiók összekapcsolása a Log Analytics munkaterülettel, ha még nincs csatolva.
* Update Management engedélyezése.

A sablon nem automatizálja az Update Management egy vagy több Azure-beli vagy nem Azure-beli virtuális gépen.

Ha már rendelkezik egy Log Analytics munkaterülettel és Automation-fiókkal, amely az előfizetés támogatott régiójában van telepítve, akkor nincsenek összekapcsolva. A sablon használatával sikeresen létrehozta a hivatkozást, és üzembe helyezi Update Management.

## <a name="api-versions"></a>API-verziók

A következő táblázat a sablonban használt erőforrások API-verzióit sorolja fel.

| Erőforrás | Erőforrás típusa | API-verzió |
|:---|:---|:---|
| Munkaterület | munkaterületek | 2020-03-01 – előzetes verzió |
| Automation-fiók | automatizálás | 2018-06-30 |
| Megoldás | megoldások | 2015-11-01 – előzetes verzió |

## <a name="before-using-the-template"></a>A sablon használata előtt

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez a Azure PowerShell az modul szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissítésre van szükség, olvassa el az [Azure PowerShell-modul telepítését ismertető](/powershell/azure/install-az-ps) szakaszt. Ha helyileg futtatja a PowerShellt, akkor a [AzAccount-](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) t is futtatnia kell a kapcsolat létrehozásához az Azure-ban. A Azure PowerShell használatával a telepítés a [New-AzResourceGroupDeployment-](/powershell/module/az.resources/new-azresourcegroupdeployment)t használja.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.1.0 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli?view=azure-cli-latest). Az Azure CLI-vel ez a telepítés az [az Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)paranccsal működik. 

A JSON-sablon úgy van konfigurálva, hogy a következőre Kérdezzen:

* A munkaterület neve.
* Az a régió, amelyben létre kívánja hozni a munkaterületet.
* Az erőforrás vagy a munkaterület engedélyeinek engedélyezéséhez.
* Az Automation-fiók neve.
* Az a régió, amelyben létre kívánja hozni a fiókot.

A JSON-sablon olyan alapértelmezett értéket határoz meg a többi paraméter számára, amelyet valószínűleg a környezetben lévő normál konfigurációhoz fog használni. A sablont egy Azure Storage-fiókban is tárolhatja a szervezet megosztott hozzáféréséhez. További információ a sablonok használatáról: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md).

A sablonban a következő paraméterek a Log Analytics munkaterület alapértelmezett értékével vannak beállítva:

* SKU – alapértelmezett érték az új GB-os díjszabási szinten, amely az áprilisi 2018 díjszabási modellben jelent meg
* adatmegőrzés – az alapértelmezett érték 30 nap

>[!WARNING]
>Ha Log Analytics munkaterületet hoz létre vagy konfigurál egy olyan előfizetésben, amely az új, április 2018 díjszabási modellbe van lefoglalva, az egyetlen érvényes Log Analytics díjszabási csomag **PerGB2018**.
>

A JSON-sablon olyan alapértelmezett értéket határoz meg a többi paraméter számára, amely valószínűleg a környezetben megszokott konfigurációként lesz használva. A sablont egy Azure Storage-fiókban is tárolhatja a szervezet megosztott hozzáféréséhez. További információ a sablonok használatáról: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md).

Fontos, hogy az új Automation-fiókhoz kapcsolódó Log Analytics munkaterületek létrehozásakor, konfigurálásakor és használatakor ne a hibák elkerülése érdekében a következő konfigurációs részleteket Ismerje meg: Azure Automation és Azure Monitor.

* [További részletekért](../../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) tekintse át a munkaterület-konfigurációs beállításokat, például a hozzáférés-vezérlési módot, a díjszabási szintet, a megőrzést és a kapacitás foglalási szintjét.

* Mivel csak bizonyos régiók támogatottak egy Log Analytics munkaterület és egy Automation-fiók összekapcsolásához az előfizetésben, tekintse át a [munkaterület-hozzárendeléseket](../how-to/region-mappings.md) a támogatott régiók beágyazott vagy egy Parameters fájlban való megadásához.

* Ha még nem Azure Monitor naplókat, és már nem telepített munkaterületet, tekintse át a [munkaterület kialakításával](../../azure-monitor/platform/design-logs-deployment.md) kapcsolatos útmutatót a hozzáférés-vezérlés megismeréséhez, és ismerkedjen meg a szervezete számára ajánlott kialakítási stratégiákkal.

## <a name="deploy-template"></a>Sablon üzembe helyezése

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
              }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]"
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('location')]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2018-06-30",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            }
        },
        {
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. Szerkessze a sablont, hogy megfeleljen a követelményeinek. Hozzon létre egy [Resource Manager-paramétereket tartalmazó fájlt](../../azure-resource-manager/templates/parameter-files.md) ahelyett, hogy a paramétereket beágyazott értékként adja át.

3. Mentse ezt a fájlt egy helyi mappába **deployUMSolutiontemplate.jsként**.

4. Készen áll a sablon üzembe helyezésére. Használhatja a PowerShellt vagy az Azure CLI-t is. Ha a rendszer a munkaterület és az Automation-fiók nevének megadását kéri, adjon meg egy olyan nevet, amely globálisan egyedi az összes Azure-előfizetésen belül.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, a következőhöz hasonló üzenet jelenik meg, amely tartalmazza az eredményt:

    ![Példa az üzembe helyezés befejezésekor bekövetkezett eredményre](media/update-mgmt-enable-template/template-output.png)

## <a name="next-steps"></a>Következő lépések

* A Update Management virtuális gépekhez való használatához lásd: [a virtuális gépek frissítéseinek és javításának kezelése](update-mgmt-manage-updates-for-vm.md).

* Ha már nem szeretné Update Management használni, és el szeretné távolítani, tekintse meg az [Update Management funkció eltávolítása](update-mgmt-remove-feature.md)című témakör utasításait.

* A virtuális gépek Update Managementból való törléséről lásd: [virtuális gépek eltávolítása Update Managementról](update-mgmt-remove-vms.md).
