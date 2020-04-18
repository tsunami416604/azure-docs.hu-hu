---
title: Automation-fiók létrehozása Azure Resource Manager-sablonokkal | Microsoft dokumentumok
description: Azure Resource Manager-sablon használatával azure automation-fiók létrehozásához.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.openlocfilehash: efe51fbada8ac70b24c16a5c7c1e0e91879e5e9f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618684"
---
# <a name="create-automation-account-using-azure-resource-manager-template"></a>Automation-fiók létrehozása az Azure Resource Manager-sablonnal

[Az Azure Resource Manager-sablonok](../azure-resource-manager/templates/template-syntax.md) használatával azure automation-fiókot hozhat létre az erőforráscsoportban. Ez a cikk egy mintasablont tartalmaz, amely automatizálja a következőket:

* Egy Azure Monitor Log Analytics munkaterület létrehozása.
* Egy Azure Automation-fiók létrehozása.
* Az Automation-fiók a Log Analytics-munkaterülethez kapcsolódik.

A sablon nem automatizálja egy vagy több Azure-beli vagy nem Azure-beli virtuális gép vagy megoldás bevezetési folyamatát. 

>[!NOTE]
>Az Automation Run As fiók létrehozása nem támogatott Az Azure Resource Manager-sablon használata esetén. Ha manuálisan szeretne futtatni futtatási fiókot a portálról vagy a PowerShell használatával, olvassa [el a Futtatás másként fiók kezelése című témakört.](manage-runas-account.md)

## <a name="api-versions"></a>API-verziók

Az alábbi táblázat a példában használt erőforrások API-verzióját sorolja fel.

| Erőforrás | Erőforrás típusa | API-verzió |
|:---|:---|:---|
| Munkaterület | munkaterületek | 2017-03-15-előzetes |
| Automation-fiók | automation | 2015-10-31 | 

## <a name="before-using-the-template"></a>A sablon használata előtt

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez a cikk az Azure PowerShell Az modult igényli. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissítésre van szükség, olvassa el az [Azure PowerShell-modul telepítését ismertető](/powershell/azure/install-az-ps) szakaszt. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. Az Azure PowerShell használatával a központi telepítés a [New-AzResourceGroupDeployment szolgáltatást](/powershell/module/az.resources/new-azresourcegroupdeployment)használja.

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a cikk megköveteli, hogy az Azure CLI 2.1.0-s vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Az Azure CLI,ez a központi telepítés használja [az csoport központi telepítés létrehozása](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

A JSON-sablon úgy van beállítva, hogy a következőket kérje:

* A munkaterület neve
* Az a régió, amelyben a munkaterületet létre lehet hozni
* Az Automation-fiók neve
* Az a régió, amelyben a fiókot létre szeretné hozni

A JSON-sablon egy alapértelmezett értéket ad meg a többi paraméterhez, amelyek valószínűleg szabványos konfigurációként lennének használva a környezetben. A sablon tárolhatja egy Azure storage-fiók megosztott hozzáférést a szervezetben. A sablonok használatával kapcsolatos további tudnivalókért olvassa el az [Erőforrások telepítése erőforrás-kezelői sablonokkal és az Azure CLI-vel című témakört.](../azure-resource-manager/templates/deploy-cli.md)

A sablonban a következő paraméterek a Log Analytics munkaterület alapértelmezett értékével vannak beállítva:

* sku - alapértelmezés szerint a 2018 áprilisában kiadott új GB-onkénti díjszabási szint
* adatmegőrzés - alapértelmezés szerint harminc nap
* kapacitásfoglalás - alapértelmezés szerint 100 GB

>[!WARNING]
>Ha egy Log Analytics-munkaterületet hoz létre vagy konfigurál egy olyan előfizetésben, amely az új 2018 áprilisi díjszabási modellt választotta, az egyetlen érvényes Log Analytics-díjszabási szint a **PerGB2018.**
>

>[!NOTE]
>A sablon használata előtt tekintse át a [további részleteket](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) a munkaterület konfigurációs beállításainak teljes megértéséhez, például a hozzáférés-vezérlési mód, a tarifacsomag, a megőrzési és a kapacitásfoglalási szint. Ha most ismerkedik az Azure Monitor naplóival, és még nem telepített munkaterületet, tekintse át a [munkaterület tervezési](../azure-monitor/platform/design-logs-deployment.md) útmutatóját a hozzáférés-vezérlésről, valamint a szervezet számára ajánlott tervezési megvalósítási stratégiák megértéséről.

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
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
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
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
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
     ]
    }
    ```

2. A sablont az igényeinek megfelelően szerkesztheti. Fontolja meg egy [Erőforrás-kezelő paraméterfájl létrehozását](../azure-resource-manager/templates/parameter-files.md) ahelyett, hogy a paramétereket szövegközi értékként adná át.

3. Mentse ezt a fájlt deployAzAutomationAccttemplate.json fájlként egy helyi mappába.

4. Készen áll a sablon üzembe helyezésére. Használhatja a PowerShell vagy az Azure CLI. Amikor a rendszer egy munkaterület- és Automation-fióknév megadását kéri, adjon meg egy globálisan egyedi nevet az összes Azure-előfizetésben.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, az alábbihoz hasonló üzenet jelenik meg, amely tartalmazza az eredményt:

    ![Példa eredmény, ha a telepítés befejeződött](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik egy Automation-fiókkal, létrehozhat runbookokat, és automatizálhatja a manuális folyamatokat.
