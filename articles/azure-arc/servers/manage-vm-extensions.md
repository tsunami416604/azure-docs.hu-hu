---
title: Virtuálisgép-bővítmények kezelése az Azure arc-kompatibilis kiszolgálókkal
description: Az Azure arc-kompatibilis kiszolgálók kezelhetik azokat a virtuálisgép-bővítmények központi telepítését, amelyek a telepítés utáni konfigurálást és az automatizálási feladatokat nem Azure-beli virtuális gépekkel is rendelkeznek.
ms.date: 09/23/2020
ms.topic: conceptual
ms.openlocfilehash: 1c3d50f407f4412a14201dfe669334dbb083d323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329074"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Virtuálisgép-bővítmények kezelése az Azure arc használatára képes kiszolgálókon

A virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha egy virtuális gépen például szoftver telepítésére, vírusvédelemre vagy egy szkript futtatására van szükség, erre felhasználható egy virtuálisgép-bővítmény.

Az Azure arc-kompatibilis kiszolgálók lehetővé teszik az Azure-beli virtuálisgép-bővítmények üzembe helyezését nem Azure-beli Windows-és Linux-alapú virtuális gépeken, a helyszíni, peremhálózati és más felhőalapú környezetek felügyeletének egyszerűsítését életciklusuk alapján.

## <a name="key-benefits"></a>Főbb előnyök

Az Azure arc-kompatibilis kiszolgálók virtuálisgép-bővítmények támogatása a következő főbb előnyöket nyújtja:

* [Azure Automation állapot konfigurációjának](../../automation/automation-dsc-overview.md) használatával központilag tárolhatja a konfigurációkat, és megtarthatja a DSC virtuálisgép-bővítményen keresztül engedélyezett hibrid csatlakoztatott gépek kívánt állapotát.

* Begyűjti a naplózási adatokat az elemzéshez [Azure monitor a naplókat](../../azure-monitor/platform/data-platform-logs.md) az log Analytics ügynök virtuálisgép-bővítményének használatával. Ez hasznos lehet a különböző forrásokból származó adatok összetett elemzésének végrehajtásához.

* A [Azure monitor for VMS](../../azure-monitor/insights/vminsights-overview.md)a elemzi a Windows-és Linux-alapú virtuális gépek teljesítményét, és figyeli a folyamatokat és a függőségeket más erőforrásokra és külső folyamatokra. Ez a Log Analytics ügynök és a függőségi ügynök virtuálisgép-bővítményeinek engedélyezésével érhető el.

* Parancsfájlokat tölthet le és futtathat a hibrid csatlakoztatott gépeken az egyéni szkriptek bővítmény használatával. Ez a bővítmény az üzembe helyezési konfiguráció, a Szoftvertelepítés vagy bármely egyéb konfigurációs vagy felügyeleti feladat után hasznos.

## <a name="availability"></a>Rendelkezésre állás

A VM-bővítmény funkció csak a [támogatott régiók](overview.md#supported-regions)listájában érhető el. Győződjön meg arról, hogy a számítógépe ezen régiók egyikében található.

## <a name="extensions"></a>Bővítmények

Ebben a kiadásban a következő virtuálisgép-bővítményeket támogatjuk a Windows és Linux rendszerű gépeken.

|Kiterjesztés |Operációs rendszer |Publisher |További információ |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Egyéni Windows-szkriptek bővítménye](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Windows PowerShell DSC-bővítmény](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics-ügynök |Windows |Microsoft. EnterpriseCloud. monitoring |[Log Analytics virtuálisgép-bővítmény a Windowshoz](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft függőségi ügynök | Windows |Microsoft.Compute | [Függőségi ügynök virtuálisgép-bővítménye Windows rendszerhez](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. Extension |[Linux Custom script Extension 2. verzió](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft. OSTCExtensions |[PowerShell DSC-bővítmény Linux rendszerhez](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics-ügynök |Linux |Microsoft. EnterpriseCloud. monitoring |[A Linux rendszerhez készült virtuálisgép-bővítmény Log Analytics](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft függőségi ügynök | Linux |Microsoft.Compute | [Függőségi ügynök linuxos virtuálisgép-bővítménye](../../virtual-machines/extensions/agent-dependency-linux.md) |

A virtuálisgép-bővítmények Azure Resource Manager-sablonokkal, a Azure Portal, Azure PowerShell illetve az arc-kompatibilis kiszolgálók által felügyelt hibrid kiszolgálókon is futtathatók.

Ha többet szeretne megtudni az Azure-beli csatlakoztatott gépi ügynök csomagról és a bővítmény ügynök összetevőjéről, tekintse meg az [ügynök áttekintése](agent-overview.md#agent-component-details)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az előfizetés alábbi Azure-erőforrás-szolgáltatói függ:

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

Ha még nincsenek regisztrálva, kövesse az [Azure Resource Providers regisztrálása](agent-overview.md#register-azure-resource-providers)című témakör lépéseit.

A Linux rendszerhez készült Log Analytics Agent virtuálisgép-bővítményhez a Python 2. x verziója szükséges a célszámítógépen.

### <a name="connected-machine-agent"></a>Csatlakoztatott számítógép ügynöke

Ellenőrizze, hogy a gép megfelel-e a Windows és Linux operációs rendszer [támogatott verzióival](agent-overview.md#supported-operating-systems) az Azure Connected Machine Agent ügynökhöz.

A Windows és Linux rendszeren a szolgáltatással támogatott csatlakoztatott gépi ügynök minimális verziója a 1,0-es kiadás.

Ha a gépet a szükséges ügynök verziójára szeretné frissíteni, tekintse meg az [ügynök frissítése](manage-agent.md#upgrading-agent)című témakört.

## <a name="enable-extensions-from-the-portal"></a>Bővítmények engedélyezése a portálról

A virtuálisgép-bővítmények alkalmazhatók a kiszolgáló által felügyelt gépekre a Azure Portalon keresztül.

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com).

2. A portálon keresse meg a **kiszolgálók – Azure arc** elemet, és válassza ki a hibrid gépet a listából.

3. Válassza a **bővítmények**, majd a **Hozzáadás**lehetőséget. Válassza ki a kívánt bővítményt az elérhető bővítmények listájából, és kövesse a varázsló utasításait. Ebben a példában a Log Analytics virtuálisgép-bővítményt fogjuk telepíteni.

    ![Virtuálisgép-bővítmény kiválasztása a kiválasztott géphez](./media/manage-vm-extensions/add-vm-extensions.png)

    Az alábbi példa a Log Analytics virtuálisgép-bővítmény telepítését mutatja be a Azure Portal:

    ![Log Analytics VM-bővítmény telepítése](./media/manage-vm-extensions/mma-extension-config.png)

    A telepítés befejezéséhez meg kell adnia a munkaterület AZONOSÍTÓját és az elsődleges kulcsot. Ha nem ismeri ezt az információt, tekintse meg a [munkaterület-azonosító és-kulcs beszerzése](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)című témakört.

4. A szükséges információk megerősítése után válassza a **Létrehozás**lehetőséget. Megjelenik a központi telepítés összegzése, és megtekintheti a központi telepítés állapotát.

>[!NOTE]
>Habár több bővítmény kötegelt feldolgozását és feldolgozását is elvégezheti, a rendszer sorosan telepíti őket. Az első bővítmény telepítésének befejezése után a rendszer a következő bővítmény telepítését kísérli meg.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A virtuálisgép-bővítmények hozzáadhatók egy Azure Resource Manager sablonhoz, és a sablon üzembe helyezésével hajthatók végre. Az arc-kompatibilis kiszolgálók által támogatott virtuálisgép-bővítményekkel a Azure PowerShell használatával telepítheti a támogatott virtuálisgép-bővítményt Linux vagy Windows rendszerű gépekre. Az alábbi példákban egy sablonfájl és egy, a sablonhoz adni kívánt minta értékekkel rendelkező Parameters fájl szerepel.

>[!NOTE]
>Habár több bővítmény kötegelt feldolgozását és feldolgozását is elvégezheti, a rendszer sorosan telepíti őket. Az első bővítmény telepítésének befejezése után a rendszer a következő bővítmény telepítését kísérli meg.

### <a name="deploy-the-log-analytics-vm-extension"></a>A Log Analytics virtuálisgép-bővítmény üzembe helyezése

A Log Analytics-ügynök egyszerű üzembe helyezéséhez a következő minta kerül az ügynök telepítésére Windows vagy Linux rendszeren.

#### <a name="template-file-for-linux"></a>A Linux sablon fájlja

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Sablonfájl a Windowshoz

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Paraméter fájlja

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Mentse a sablont és a paraméter fájljait a lemezre, és szerkessze a paramétert a központi telepítés megfelelő értékeivel. Ezután telepítheti a bővítményt az erőforráscsoport összes csatlakoztatott számítógépén a következő paranccsal. A parancs a *TemplateFile* paramétert használja a sablon és a *TemplateParameterFile* paraméter megadásához, amely paraméterek és paraméterek értékét tartalmazó fájlt ad meg.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>Az egyéni szkriptek bővítményének üzembe helyezése

Az egyéni szkriptek bővítményének használatához a következő minta a Windows és Linux rendszeren való futtatásra szolgál. Ha nem ismeri az egyéni szkriptek bővítményét, tekintse meg a [Windowshoz készült egyéni szkriptek bővítményét](../../virtual-machines/extensions/custom-script-windows.md) vagy a [Linuxhoz készült egyéni szkriptek bővítményét](../../virtual-machines/extensions/custom-script-linux.md). A bővítmény hibrid gépekkel való használata során több különböző jellemzővel is tisztában kell lennie:

* Az Azure VM Custom script bővítménnyel rendelkező támogatott operációs rendszerek listája nem alkalmazható az Azure arc-kompatibilis kiszolgálókra. Az arc-kompatibilis kiszolgálókhoz támogatott OSs listája [itt](agent-overview.md#supported-operating-systems)található.

* Az Azure Virtual Machine Scale Sets vagy klasszikus virtuális gépekre vonatkozó konfigurációs adatok nem alkalmazhatók.

* Ha a gépeken kívülről kell letöltenie egy parancsfájlt, és csak proxykiszolgálón keresztül tud kommunikálni, akkor [konfigurálnia kell a csatlakoztatott számítógép ügynököt](manage-agent.md#update-or-remove-proxy-settings) a proxykiszolgáló környezeti változójának beállításához.

Az egyéni szkriptek bővítményének konfigurációja meghatározza a parancsfájl helyét és a futtatandó parancsot. Ez a konfiguráció egy Azure Resource Manager sablonban van megadva, amely a Linux és a Windows hibrid gépek esetében is elérhető.

#### <a name="template-file-for-linux"></a>A Linux sablon fájlja

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>Sablonfájl a Windowshoz

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>A PowerShell DSC-bővítmény üzembe helyezése

A PowerShell DSC bővítmény használatához a következő minta a Windows és Linux rendszeren való futtatásra szolgál. Ha nem ismeri a PowerShell DSC-bővítményt, tekintse meg a [DSC-kezelő bővítményének áttekintése](../../virtual-machines/extensions/dsc-overview.md)című témakört. A bővítmény hibrid gépekkel való használata során több különböző jellemzővel is tisztában kell lennie:

* Az Azure VM PowerShell DSC bővítménnyel rendelkező támogatott operációs rendszerek listája nem alkalmazható az Azure arc-kompatibilis kiszolgálókra. Az arc-kompatibilis kiszolgálókhoz támogatott OSs listája [itt](agent-overview.md#supported-operating-systems)található.

* Ha a gépeken kívülről kell letöltenie egy parancsfájlt, és csak proxykiszolgálón keresztül tud kommunikálni, akkor [konfigurálnia kell a csatlakoztatott számítógép ügynököt](manage-agent.md#update-or-remove-proxy-settings) a proxykiszolgáló környezeti változójának beállításához.

#### <a name="template-file-for-linux"></a>A Linux sablon fájlja

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Sablonfájl a Windowshoz

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>Függőségi ügynök

A Azure Monitor függőségi ügynök bővítményének használatához a következő minta a Windows és Linux rendszeren való futtatásra szolgál. Ha nem ismeri a függőségi ügynököt, tekintse meg [a Azure monitor ügynökök áttekintése](../../azure-monitor/platform/agents-overview.md#dependency-agent)című témakört.

#### <a name="template-file-for-linux"></a>A Linux sablon fájlja

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>Sablonfájl a Windowshoz

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="uninstall-extension"></a>Bővítmény eltávolítása

Egy vagy több bővítmény egy ív által engedélyezett kiszolgálóról való eltávolítása csak a Azure Portal végezhető el. A bővítmény eltávolításához hajtsa végre az alábbi lépéseket.

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com).

2. A portálon keresse meg a **kiszolgálók – Azure arc** elemet, és válassza ki a hibrid gépet a listából.

3. Válassza a **bővítmények**lehetőséget, majd válasszon ki egy bővítményt a telepített bővítmények listájából.

4. Válassza az **Eltávolítás** lehetőséget, és amikor a rendszer kéri az ellenőrzésre, válassza az **Igen** lehetőséget a folytatáshoz.

## <a name="next-steps"></a>Következő lépések

* A hibaelhárítási információk a virtuálisgép- [bővítmények hibaelhárítási útmutatójában](troubleshoot-vm-extensions.md)találhatók.

* Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/insights/vminsights-enable-policy.md)és sok más további műveletet.

* További információ a [log Analytics-ügynökről](../../azure-monitor/platform/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics ügynökre akkor van szükség, ha az operációs rendszer és a számítási feladatok figyelésére szolgáló adatokat szeretné összegyűjteni, az Automation-runbookok vagy-szolgáltatásokkal, például a Update Managementekkel, vagy más Azure-szolgáltatások, például a [Azure Security Center](../../security-center/security-center-intro.md)használatával