---
title: Az Azure Resource Manager-sablonok használata a frissítéskezelés hez | Microsoft dokumentumok
description: Az Azure Resource Manager-sablon használatával az Azure Automation Update Management megoldás.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/30/2020
ms.openlocfilehash: 81f9d242d93ffe513c0c3733ceb9d38ca9cadc1c
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617458"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Beépített frissítéskezelési megoldás az Azure Resource Manager-sablon használatával

[Az Azure Resource Manager-sablonok](../azure-resource-manager/templates/template-syntax.md) használatával engedélyezheti az Azure Automation Update Management megoldást az erőforráscsoportban. Ez a cikk egy mintasablont tartalmaz, amely automatizálja a következőket:

* Egy Azure Monitor Log Analytics munkaterület létrehozása.
* Egy Azure Automation-fiók létrehozása.
* Az Automation-fiók összekapcsolása a Log Analytics-munkaterülettel, ha még nincs összekapcsolva.
* Az Azure Automation Update Management megoldás bevezetés.

A sablon nem automatizálja egy vagy több Azure-beli vagy nem Azure-beli virtuális gép bevezetési szolgáltatását.

Ha már rendelkezik egy Log Analytics-munkaterületi és Automation-fiókkal az előfizetés egy támogatott régióban, akkor azok nincsenek összekapcsolva. A munkaterület még nem rendelkezik az Update Management megoldás telepítve. A sablon sikeres használata sikeresen létrehozza a kapcsolatot, és telepíti az Update Management megoldást. 

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="api-versions"></a>API-verziók

Az alábbi táblázat a sablonban használt erőforrások API-verzióit sorolja fel.

| Erőforrás | Erőforrás típusa | API-verzió |
|:---|:---|:---|
| Munkaterület | munkaterületek | 2017-03-15-előzetes |
| Automation-fiók | automation | 2015-10-31 | 
| Megoldás | megoldások | 2015-11-01-előzetes |

## <a name="before-using-the-template"></a>A sablon használata előtt

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez a cikk az Azure PowerShell Az modult igényli. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissítésre van szükség, olvassa el az [Azure PowerShell-modul telepítését ismertető](/powershell/azure/install-az-ps) szakaszt. Ha helyileg futtatja a PowerShellt, a [Connect-AzAccount-t](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) is futtatnia kell az Azure-ral való kapcsolat létrehozásához. Az Azure PowerShell használatával a központi telepítés a [New-AzResourceGroupDeployment szolgáltatást](/powershell/module/az.resources/new-azresourcegroupdeployment)használja.

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a cikk megköveteli, hogy az Azure CLI 2.1.0-s vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Az Azure CLI,ez a központi telepítés használja [az csoport központi telepítés létrehozása](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

A JSON-sablon úgy van beállítva, hogy a következőket kérje:

* A munkaterület neve
* A munkaterület létrehozásának helye
* Az Automation-fiók neve
* A fiók létrehozásának területe

A JSON-sablon egy alapértelmezett értéket ad meg a környezetben a szabványos konfigurációhoz valószínűleg használt egyéb paraméterekhez. A sablon tárolhatja egy Azure storage-fiók megosztott hozzáférést a szervezetben. A sablonok használatával kapcsolatos további tudnivalókért olvassa el az [Erőforrások telepítése erőforrás-kezelői sablonokkal és az Azure CLI-vel című témakört.](../azure-resource-manager/templates/deploy-cli.md)

A sablonban a következő paraméterek a Log Analytics munkaterület alapértelmezett értékével vannak beállítva:

* sku - alapértelmezés szerint a 2018 áprilisában kiadott új GB-onkénti díjszabási szint
* adatmegőrzés - alapértelmezés szerint harminc nap
* kapacitásfoglalás - alapértelmezés szerint 100 GB

>[!WARNING]
>Ha a 2018 áprilisi díjszabási modellt választó előfizetésben létrehoz vagy konfigurál egy Log Analytics-munkaterületet, az egyetlen érvényes Log Analytics-díjszabási szint a **PerGB2018.**
>

>[!NOTE]
>A sablon használata előtt tekintse át a [munkaterület](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) konfigurációs beállításait, például a hozzáférés-vezérlési módot, a tarifacsomagot, a megőrzési és kapacitásfoglalási szintet. Ha most ismerkedik az Azure Monitor naplóival, és még nem telepített munkaterületet, tekintse át a [munkaterület tervezési](../azure-monitor/platform/design-logs-deployment.md) útmutatóját a hozzáférés-vezérlésről, és ismerje meg a szervezet számára ajánlott tervezési megvalósítási stratégiákat.

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
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
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

2. A sablont az igényeinek megfelelően szerkesztheti. Fontolja meg egy [Erőforrás-kezelő paraméterfájl létrehozását](../azure-resource-manager/templates/parameter-files.md) ahelyett, hogy a paramétereket szövegközi értékként adná át.

3. Mentse a fájlt egy helyi mappába **deployUMSolutiontemplate.json néven.**

4. Készen áll a sablon üzembe helyezésére. Használhatja a PowerShell vagy az Azure CLI. Amikor a rendszer egy munkaterület- és Automation-fióknév megadását kéri, adjon meg egy globálisan egyedi nevet az összes Azure-előfizetésben.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, az alábbihoz hasonló üzenet jelenik meg, amely tartalmazza az eredményt:

    ![Példa eredmény, ha a telepítés befejeződött](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>További lépések

Most, hogy telepítette az Update Management megoldást, engedélyezheti a virtuális gépeket a felügyelethez, áttekintheti a frissítési értékeléseket, és telepítheti a frissítéseket, hogy azok megfeleljenek.

- Az [Azure Automation-fiók](automation-onboard-solutions-from-automation-account.md) egy vagy több Azure-gépek és manuálisan nem Azure-gépek.

- Egyetlen Azure-virtuális gép az Azure Portalon a virtuális gép lapjáról. Ez a forgatókönyv [Linux és](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) virtuális gépekhez érhető el.

- [Több Azure-beli virtuális gép](manage-update-multi.md) esetén az Azure Portal virtuális **gépek** lapján való kijelölésükhez. 