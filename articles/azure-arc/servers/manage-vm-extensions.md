---
title: Virtuálisgép-bővítmények kezelése az Azure arc-kiszolgálókkal
description: Az Azure arc for Servers (előzetes verzió) képes felügyelni a virtuálisgép-bővítmények telepítését, amelyek a telepítés utáni konfigurációs és automatizálási feladatokat biztosítják a nem Azure-beli virtuális gépekhez.
ms.date: 06/17/2020
ms.topic: conceptual
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.openlocfilehash: e04b2c63f782a4fcbb64273c08511ecb0bbc2bc3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84977716"
---
# <a name="virtual-machine-extension-management-with-azure-arc-for-servers-preview"></a>Virtuálisgép-bővítmények kezelése az Azure arc for Servers szolgáltatással (előzetes verzió)

A virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha egy virtuális gépen például szoftver telepítésére, vírusvédelemre vagy egy szkript futtatására van szükség, erre felhasználható egy virtuálisgép-bővítmény.

Az Azure arc for Servers (előzetes verzió) szolgáltatással Azure virtuálisgép-bővítményeket telepíthet a nem Azure-beli Windows-és Linux-alapú virtuális gépekre, így egyszerűbbé válik a helyszíni, peremhálózati és más felhőalapú környezetek kezelése a saját életciklusa során.

## <a name="key-benefits"></a>Főbb előnyök

Az Azure arc for Servers (előzetes verzió) virtuálisgép-bővítmények támogatása a következő főbb előnyöket nyújtja:

* [Azure Automation állapot konfigurációjának](../../automation/automation-dsc-overview.md) használatával központilag tárolhatja a konfigurációkat, és megtarthatja a DSC virtuálisgép-bővítményen keresztül engedélyezett hibrid csatlakoztatott gépek kívánt állapotát.

* Begyűjti a naplózási adatokat az elemzéshez [Azure monitor a naplókat](../../azure-monitor/platform/data-platform-logs.md) az log Analytics ügynök virtuálisgép-bővítményének használatával. Ez hasznos lehet a különböző forrásokból származó adatok összetett elemzésének végrehajtásához.

* A [Azure monitor for VMS](../../azure-monitor/insights/vminsights-overview.md)a elemzi a Windows-és Linux-alapú virtuális gépek teljesítményét, és figyeli a folyamatokat és a függőségeket más erőforrásokra és külső folyamatokra. Ez a Log Analytics ügynök és a függőségi ügynök virtuálisgép-bővítményeinek engedélyezésével érhető el.

* Parancsfájlokat tölthet le és futtathat a hibrid csatlakoztatott gépeken az egyéni szkriptek bővítmény használatával. Ez a bővítmény az üzembe helyezési konfiguráció, a Szoftvertelepítés vagy bármely egyéb konfigurációs vagy felügyeleti feladat után hasznos.

## <a name="availability"></a>Rendelkezésre állás

A VM-bővítmény funkció csak a [támogatott régiók](overview.md#supported-regions)listájában érhető el. Győződjön meg arról, hogy a számítógépe ezen régiók egyikében található.

## <a name="extensions"></a>Bővítmények

Ebben az előzetes verzióban a következő virtuálisgép-bővítményeket támogatjuk a Windows és Linux rendszerű gépeken.

|Mellék |Operációs rendszer |Publisher |További információ |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Egyéni Windows-szkriptek bővítménye](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Windows PowerShell DSC-bővítmény](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics-ügynök |Windows |Microsoft. EnterpriseCloud. monitoring |[Log Analytics virtuálisgép-bővítmény a Windowshoz](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft függőségi ügynök | Windows |Microsoft.Compute | [Függőségi ügynök virtuálisgép-bővítménye Windows rendszerhez](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. Extension |[Linux Custom script Extension 2. verzió](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft. OSTCExtensions |[PowerShell DSC-bővítmény Linux rendszerhez](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics-ügynök |Linux |Microsoft. EnterpriseCloud. monitoring |[A Linux rendszerhez készült virtuálisgép-bővítmény Log Analytics](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft függőségi ügynök | Linux |Microsoft.Compute | [Függőségi ügynök linuxos virtuálisgép-bővítménye](../../virtual-machines/extensions/agent-dependency-linux.md) |

A virtuálisgép-bővítmények Azure Resource Manager-sablonokkal, a Azure Portal, Azure PowerShell illetve az arc for Servers (előzetes verzió) által felügyelt hibrid kiszolgálókon is futtathatók.

Ha többet szeretne megtudni az Azure-beli csatlakoztatott gépi ügynök csomagról és a bővítmény ügynök összetevőjéről, tekintse meg az [ügynök áttekintése](agent-overview.md#agent-component-details)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az előfizetés alábbi Azure-erőforrás-szolgáltatói függ:

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

Ha még nincsenek regisztrálva, kövesse az [Azure Resource Providers regisztrálása](agent-overview.md#register-azure-resource-providers)című témakör lépéseit.

A Linux rendszerhez készült Log Analytics Agent virtuálisgép-bővítményhez a Python 2. x verziója szükséges a célszámítógépen.

### <a name="connected-machine-agent"></a>Csatlakoztatott számítógép ügynöke

Ellenőrizze, hogy a gép megfelel-e a Windows és Linux operációs rendszer [támogatott verzióival](agent-overview.md#supported-operating-systems) az Azure Connected Machine Agent ügynökhöz.

A szolgáltatás által támogatott csatlakoztatott számítógép-ügynök minimális verziója:

* Windows-0.7. x
* Linux – 0,8. x

Ha a gépet a szükséges ügynök verziójára szeretné frissíteni, tekintse meg az [ügynök frissítése](manage-agent.md#upgrading-agent)című témakört.

## <a name="enable-extensions-from-the-portal"></a>Bővítmények engedélyezése a portálról

A virtuálisgép-bővítmények a Azure Portal használatával alkalmazhatók a kiszolgáló (előzetes verzió) által felügyelt gépekre.

1. A böngészőben nyissa meg a [Azure Portal](https://aka.ms/arcserver-preview).

2. A portálon keresse meg a **Machines-Azure arc** elemet, és válassza ki a hibrid gépet a listából.

3. Válassza a **bővítmények**, majd a **Hozzáadás**lehetőséget. Válassza ki a kívánt bővítményt az elérhető bővítmények listájából, és kövesse a varázsló utasításait. Ebben a példában a Log Analytics virtuálisgép-bővítményt fogjuk telepíteni. 

    ![Virtuálisgép-bővítmény kiválasztása a kiválasztott géphez](./media/manage-vm-extensions/add-vm-extensions.png)

    Az alábbi példa a Log Analytics virtuálisgép-bővítmény telepítését mutatja be a Azure Portal:

    ![Log Analytics VM-bővítmény telepítése](./media/manage-vm-extensions/mma-extension-config.png)

    A telepítés befejezéséhez meg kell adnia a munkaterület AZONOSÍTÓját és az elsődleges kulcsot. Ha nem ismeri ezt az információt, tekintse meg a [munkaterület-azonosító és-kulcs beszerzése](../../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)című témakört.

4. A szükséges információk megerősítése után válassza a **Létrehozás**lehetőséget. Megjelenik a központi telepítés összegzése, és megtekintheti a központi telepítés állapotát.

>[!NOTE]
>Habár több bővítmény kötegelt feldolgozását és feldolgozását is elvégezheti, a rendszer sorosan telepíti őket. Az első bővítmény telepítésének befejezése után a rendszer a következő bővítmény telepítését kísérli meg.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A virtuálisgép-bővítmények hozzáadhatók egy Azure Resource Manager sablonhoz, és a sablon üzembe helyezésével hajthatók végre. Az arc for Servers (előzetes verzió) által támogatott virtuálisgép-bővítmények segítségével a Azure PowerShell használatával telepítheti a támogatott virtuálisgép-bővítményt Linux vagy Windows rendszerű gépekre. Az alábbi példákban egy sablonfájl és egy, a sablonhoz adni kívánt minta értékekkel rendelkező Parameters fájl szerepel.

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

* Az Azure VM Custom script bővítménnyel rendelkező támogatott operációs rendszerek listája nem alkalmazható az Azure arc for Servers szolgáltatásra. [Itt](agent-overview.md#supported-operating-systems)találhatja meg a kiszolgálók számára támogatott OSS-t a kiszolgálókhoz.

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

* Az Azure VM PowerShell DSC bővítménnyel rendelkező támogatott operációs rendszerek listája nem alkalmazható az Azure arc for Servers szolgáltatásra. [Itt](agent-overview.md#supported-operating-systems)találhatja meg a kiszolgálók számára támogatott OSS-t a kiszolgálókhoz.

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

## <a name="troubleshooting"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok a Azure Portalból kérhetők le.

Az alábbi hibaelhárítási lépések minden virtuálisgép-bővítményre érvényesek.

1. A vendég ügynök naplójának vizsgálatához tekintse meg a tevékenységet, ha a bővítmény a Windows rendszerhez lett kiépítve `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` , és a Linux alatt `/var/lib/GuestConfig/ext_mgr_logs` .

2. A Windows rendszerre vonatkozó további részletekért olvassa el a bővítmények naplóit az adott bővítménynél `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` . A bővítmény kimenetét a rendszer a Linux rendszeren telepített minden bővítmény fájljában naplózza `/var/log/GuestConfig/extension_logs` .

3. Tekintse meg a bővítmények specifikus dokumentációjának hibaelhárítási szakaszt a hibakódok, ismert problémák stb. esetében. Az egyes bővítményekkel kapcsolatos további hibaelhárítási információk a bővítmény áttekintésében, a **Hibaelhárítás és támogatás** szakaszban találhatók. Ez magában foglalja a naplóba írt hibakódok leírását. A bővítmények cikkei a jelen cikk korábbi részében található [bővítmények táblázatban](#extensions) vannak csatolva.

4. Tekintse meg a rendszernaplókat. Keresse meg azokat a műveleteket, amelyek esetleg zavart okozhattak a bővítménnyel, például egy olyan alkalmazás hosszú futású telepítését, amely kizárólagos csomagkezelő-hozzáférést igényelt.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)és sok más további műveletet.

- További információ a [log Analytics-ügynökről](../../azure-monitor/platform/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics-ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és munkaterheléseket, felügyelheti azt automatizálási runbookok vagy olyan megoldások használatával, mint például a Update Management, vagy más Azure-szolgáltatásokat is használhat, mint például a [Azure Security Center](../../security-center/security-center-intro.md).