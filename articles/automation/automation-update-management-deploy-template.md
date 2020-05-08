---
title: Azure Resource Manager-sablonok használata a bevezetéshez Update Management | Microsoft Docs
description: A Azure Automation Update Management megoldás bevezetéséhez használhat Azure Resource Manager sablont.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: dd8706c1e95e6b1e4ca4a38d4a336f6186464696
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872201"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Update Management megoldás előkészítése Azure Resource Manager sablon használatával

[Azure Resource Manager-sablonok](../azure-resource-manager/templates/template-syntax.md) használatával engedélyezheti az Azure Automation Update Management megoldást az erőforráscsoporthoz. Ez a cikk egy minta sablont tartalmaz, amely automatizálja a következőket:

* Azure Monitor Log Analytics munkaterület létrehozása.
* Azure Automation-fiók létrehozása.
* Az Automation-fiók összekapcsolása a Log Analytics munkaterülettel, ha még nincs csatolva.
* A Azure Automation Update Management megoldás bevezetése.

A sablon nem automatizálja egy vagy több Azure-beli vagy nem Azure-beli virtuális gép bevezetését.

Ha már rendelkezik egy Log Analytics munkaterülettel és Automation-fiókkal, amely az előfizetés támogatott régiójában van telepítve, akkor nincsenek összekapcsolva. A munkaterületen még nincs telepítve a Update Management megoldás. A sablon használatával sikeresen létrehozta a hivatkozást, és üzembe helyezi a Update Management megoldást. 

>[!NOTE]
>A Linux Update Management részeként bekészített **nxautomation** -felhasználó csak az aláírt runbookok hajtja végre.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="api-versions"></a>API-verziók

A következő táblázat a sablonban használt erőforrások API-verzióit sorolja fel.

| Erőforrás | Erőforrás típusa | API-verzió |
|:---|:---|:---|
| Munkaterület | munkaterületek | 2017-03-15 – előzetes verzió |
| Automation-fiók | automation | 2015-10-31 | 
| Megoldás | megoldások | 2015-11-01 – előzetes verzió |

## <a name="before-using-the-template"></a>A sablon használata előtt

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez a Azure PowerShell az modul szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissítésre van szükség, olvassa el az [Azure PowerShell-modul telepítését ismertető](/powershell/azure/install-az-ps) szakaszt. Ha helyileg futtatja a PowerShellt, akkor a [AzAccount-](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) t is futtatnia kell a kapcsolat létrehozásához az Azure-ban. A Azure PowerShell használatával a telepítés a [New-AzResourceGroupDeployment-](/powershell/module/az.resources/new-azresourcegroupdeployment)t használja.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.1.0 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Az Azure CLI-vel ez a telepítés az [az Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)paranccsal működik. 

A JSON-sablon úgy van konfigurálva, hogy a következőre Kérdezzen:

* A munkaterület neve
* Az a régió, amelyben létre kívánja hozni a munkaterületet
* Az Automation-fiók neve
* Az a régió, amelyben létre kívánja hozni a fiókot

A JSON-sablon olyan alapértelmezett értéket határoz meg a többi paraméter számára, amelyet valószínűleg a környezetben lévő normál konfigurációhoz fog használni. A sablont egy Azure Storage-fiókban is tárolhatja a szervezet megosztott hozzáféréséhez. További információ a sablonok használatáról: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../azure-resource-manager/templates/deploy-cli.md).

A sablonban a következő paraméterek a Log Analytics munkaterület alapértelmezett értékével vannak beállítva:

* SKU – alapértelmezett érték az új GB-os díjszabási szinten, amely az áprilisi 2018 díjszabási modellben jelent meg
* adatmegőrzés – az alapértelmezett érték 30 nap
* kapacitás foglalása – alapértelmezett érték: 100 GB

>[!WARNING]
>Ha Log Analytics munkaterületet hoz létre vagy konfigurál egy olyan előfizetésben, amely az új, április 2018 díjszabási modellbe van lefoglalva, az egyetlen érvényes Log Analytics díjszabási csomag **PerGB2018**.
>

A JSON-sablon olyan alapértelmezett értéket határoz meg a többi paraméter számára, amely valószínűleg a környezetben megszokott konfigurációként lesz használva. A sablont egy Azure Storage-fiókban is tárolhatja a szervezet megosztott hozzáféréséhez. További információ a sablonok használatáról: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../azure-resource-manager/templates/deploy-cli.md).

Fontos, hogy az új Automation-fiókhoz kapcsolódó Log Analytics munkaterületek létrehozásakor, konfigurálásakor és használatakor ne a hibák elkerülése érdekében a következő konfigurációs részleteket Ismerje meg: Azure Automation és Azure Monitor.

* [További részletekért](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) tekintse át a munkaterület-konfigurációs beállításokat, például a hozzáférés-vezérlési módot, a díjszabási szintet, a megőrzést és a kapacitás foglalási szintjét.

* Mivel csak bizonyos régiók támogatottak egy Log Analytics munkaterület és egy Automation-fiók összekapcsolásához az előfizetésben, tekintse át a [munkaterület-hozzárendeléseket](how-to/region-mappings.md) a támogatott régiók beágyazott vagy egy Parameters fájlban való megadásához.

* Ha még nem Azure Monitor naplókat, és már nem telepített munkaterületet, tekintse át a [munkaterület kialakításával](../azure-monitor/platform/design-logs-deployment.md) kapcsolatos útmutatót a hozzáférés-vezérlés megismeréséhez, és ismerkedjen meg a szervezete számára ajánlott kialakítási stratégiákkal.

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
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
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
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
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
                    "location": "[resourceGroup().location]",
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
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
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

2. Szerkessze a sablont, hogy megfeleljen a követelményeinek. Hozzon létre egy [Resource Manager-paramétereket tartalmazó fájlt](../azure-resource-manager/templates/parameter-files.md) ahelyett, hogy a paramétereket beágyazott értékként adja át.

3. Mentse ezt a fájlt egy helyi mappába a **deployUMSolutiontemplate. JSON**néven.

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

    ![Példa az üzembe helyezés befejezésekor bekövetkezett eredményre](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>További lépések

Most, hogy telepítette a Update Management megoldást, engedélyezheti a virtuális gépek felügyeletét, ellenőrizheti a frissítések értékeléseit, és telepítheti a frissítéseket, hogy azok megfeleljenek.

- Azure Automation- [fiókjából](automation-onboard-solutions-from-automation-account.md) egy vagy több Azure-gép számára, és manuálisan a nem Azure-beli gépekhez.

- Egyetlen Azure-beli virtuális géphez a Azure Portal virtuális gép lapján. Ez a forgatókönyv [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) és [Windows rendszerű](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) virtuális gépek esetében érhető el.

- [Több Azure](manage-update-multi.md) -beli virtuális gép esetén válassza ki őket a Azure Portal **Virtual Machines** lapján. 