---
title: Automation-fiók létrehozása Azure Resource Manager sablon használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure Automation-fiókot Azure Resource Manager sablon használatával.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/09/2020
ms.openlocfilehash: 6b26db522db246add48941da9af4784ed2942a0a
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84661029"
---
# <a name="create-an-automation-account-using-an-azure-resource-manager-template"></a>Automation-fiók létrehozása Azure Resource Manager sablon használatával

[Azure Resource Manager-sablonok](../azure-resource-manager/templates/template-syntax.md) használatával létrehozhat egy Azure Automation fiókot az erőforráscsoporthoz. Ez a cikk egy minta sablont tartalmaz, amely a következőket tartalmazza:

* Automatizálja Azure Monitor Log Analytics munkaterület létrehozását.
* Automatizálja Azure Automation fiók létrehozását.
* Az Automation-fiók csatolása a Log Analytics munkaterülethez.

A sablon nem automatizálja az Azure-beli vagy nem Azure-beli virtuális gépek engedélyezését. 

>[!NOTE]
>Azure Resource Manager-sablon használata esetén nem támogatott az Automation futtató fiók létrehozása. Ha manuálisan szeretne létrehozni egy futtató fiókot a portálon vagy a PowerShell-lel, tekintse meg a [futtató fiókok kezelése](manage-runas-account.md)című témakört.

A lépések elvégzése után be kell állítania az Automation-fiókhoz tartozó [diagnosztikai beállításokat](automation-manage-send-joblogs-log-analytics.md) , hogy a runbook-feladatok állapotát és a feladatok adatfolyamait a csatolt log Analytics munkaterületre küldje. 

## <a name="api-versions"></a>API-verziók

A következő táblázat felsorolja az ebben a példában használt erőforrások API-verzióját.

| Erőforrás | Erőforrás típusa | API-verzió |
|:---|:---|:---|
| Munkaterület | munkaterületek | 2020-03-01 – előzetes verzió |
| Automation-fiók | automation | 2018-06-30 | 

## <a name="before-you-use-the-template"></a>A sablon használata előtt

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez a Azure PowerShell az modul szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissítésre van szükség, olvassa el az [Azure PowerShell-modul telepítését ismertető](/powershell/azure/install-az-ps) szakaszt. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást. A PowerShell használatával a központi telepítés a [New-AzResourceGroupDeployment-](/powershell/module/az.resources/new-azresourcegroupdeployment)t használja.

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a cikkhez a 2.1.0 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket. Az Azure CLI-vel ez a telepítés az [az Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)paranccsal működik. 

A JSON-sablon úgy van konfigurálva, hogy a következőre Kérdezzen:

* A munkaterület neve.
* Az a régió, amelyben létre kívánja hozni a munkaterületet.
* Az erőforrás vagy a munkaterület engedélyeinek engedélyezéséhez.
* Az Automation-fiók neve.
* Az a régió, amelybe az Automation-fiókot létre kell hozni.

A sablonban a következő paraméterek a Log Analytics munkaterület alapértelmezett értékével vannak beállítva:

* az *SKU* alapértelmezett értéke az április 2018 díjszabási modellben megjelent GB-os díjszabási szinten.
* a *dataRetention* alapértelmezett értéke 30 nap.

>[!WARNING]
>Ha egy Log Analytics munkaterületet szeretne létrehozni vagy konfigurálni egy olyan előfizetésben, amely az áprilisi 2018 díjszabási modellre van kiválasztva, akkor az egyetlen érvényes Log Analytics díjszabási csomag *PerGB2018*.
>

A JSON-sablon olyan alapértelmezett értéket határoz meg a többi paraméter számára, amely valószínűleg a környezetben megszokott konfigurációként lesz használva. A sablont egy Azure Storage-fiókban is tárolhatja a szervezet megosztott hozzáféréséhez. További információ a sablonok használatáról: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../azure-resource-manager/templates/deploy-cli.md).

Ha még nem Azure Automation és Azure Monitor, fontos, hogy megértse a következő konfigurációs adatokat. Az új Automation-fiókhoz kapcsolódó Log Analytics munkaterületek létrehozásakor, konfigurálásakor és használatakor elkerülheti a hibák elhárítását.

* [További részletekért](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) tekintse át a munkaterület-konfigurációs beállításokat, például a hozzáférés-vezérlési módot, a díjszabási szintet, a megőrzést és a kapacitás foglalási szintjét.

* Tekintse át a [munkaterület-leképezéseket](how-to/region-mappings.md) a támogatott régiók beágyazott vagy egy paraméterérték megadásához. Egy Log Analytics munkaterület és egy Automation-fiók összekapcsolása csak bizonyos régiókban támogatott az előfizetésben.

* Ha még nem Azure Monitor naplókat, és már nem telepített munkaterületet, tekintse át a [munkaterület kialakítására vonatkozó útmutatást](../azure-monitor/platform/design-logs-deployment.md). Segít megismerni a hozzáférés-vezérlést, és megismerheti a szervezete számára ajánlott kialakítási stratégiákat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days to retain data."
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
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]",
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
            "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
     ]
    }
    ```

2. Szerkessze a sablont, hogy megfeleljen a követelményeinek. Érdemes lehet [Resource Manager-paramétert](../azure-resource-manager/templates/parameter-files.md) létrehozni, ahelyett, hogy a paramétereket beágyazott értékként adja át.

3. Mentse ezt a fájlt deployAzAutomationAccttemplate.jsként egy helyi mappába.

4. Most már készen áll a sablon üzembe helyezésére. Használhatja a PowerShellt vagy az Azure CLI-t is. Ha a rendszer a munkaterület és az Automation-fiók nevének megadását kéri, adjon meg egy olyan nevet, amely globálisan egyedi az összes Azure-előfizetésen belül.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    Az üzembe helyezés eltarthat néhány percig. Ha igen, a következőhöz hasonló üzenet jelenik meg, amely tartalmazza az eredményt.

    ![Példa az üzembe helyezés befejezésekor bekövetkezett eredményre](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Következő lépések

A runbook-feladatok állapotának és a feladatok adatfolyamoknak a csatolt Log Analytics munkaterületre való továbbításához tekintse át a [Azure Automation-feladatok továbbítása Azure monitor naplókba](automation-manage-send-joblogs-log-analytics.md). Ezzel konfigurálja az Automation-fiók diagnosztikai beállításait Azure PowerShell parancsok használatával, hogy elvégezze a naplók a munkaterületre való küldésének integrálását elemzés céljából. 
