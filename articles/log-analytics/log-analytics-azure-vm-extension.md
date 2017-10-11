---
title: "Az Azure virtuális gépek csatlakoztatása szolgáltatáshoz |} Microsoft Docs"
description: "Windows és Linux rendszerű virtuális gépek Azure-ban az ajánlott módszer a gyűjtött naplók, metrikák van, a napló Analytics Azure Virtuálisgép-bővítmény telepítésével. Az Azure portálon vagy a PowerShell segítségével telepítse az Azure virtuális gépek alakzatot Naplóelemzési virtuálisgép-bővítmény."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: ewinner
editor: 
ms.assetid: ca39e586-a6af-42fe-862e-80978a58d9b1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: richrund
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cdae291b546fef4d7fdb8b067c8e4f4c9708d43f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="connect-azure-virtual-machines-to-log-analytics-with-a-log-analytics-agent"></a>Az Azure virtuális gépek csatlakoztatása a Log Analyticshez ügynökkel szolgáltatáshoz

A Windows és Linux számítógépekre a naplók és a metrikák gyűjtéséhez az ajánlott módszer van a Naplóelemzési ügynök telepítésével.

A Naplóelemzési ügynök telepítése az Azure virtuális gépeken a legegyszerűbb módja a napló Analytics VM bővítményével.  A bővítmény használatával egyszerűbbé teszi a telepítési folyamat, és automatikusan konfigurálja az adatokat küldeni a Naplóelemzési munkaterület, melyet a az ügynök. Az ügynök is frissítése automatikusan történik, győződjön meg arról, hogy rendelkezik-e a legújabb funkcióit és javításokat.

Windows virtuális gépek esetében engedélyezi az *Microsoft Monitoring Agent* virtuálisgép-bővítményt.
A Linux virtuális gépekhez, engedélyezi a *OMS ügynök a Linux* virtuálisgép-bővítményt.

További információ [Azure virtuálisgép-bővítmények](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és a [Linux-ügynök](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ha az ügynök-alapú gyűjtemény naplóadatokat használja, konfigurálnia kell [Naplóelemzési adatforrások](log-analytics-data-sources.md) adhatja meg a naplók és metrikákat, szeretne gyűjteni.

> [!IMPORTANT]
> Ha konfigurálását a Log Analyticshez való index naplóadatok [Azure diagnostics](log-analytics-azure-storage.md), és konfigurálja az ügynököt, hogy az azonos gyűjtését, majd a naplók összegyűjtését kétszer. Mindkét adatforrások van szó. Ha az ügynök telepítve van, csak az ügynök a napló adatgyűjtés - napló adatokat gyűjteni az Azure diagnostics Naplóelemzési ne állítson be.
>
>

Háromféleképpen könnyen Naplóelemzési virtuálisgép-bővítmény engedélyezése:

* Az Azure portál használatával
* Azure PowerShell használatával
* Az Azure Resource Manager-sablon használatával

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Az Azure-portálon a Virtuálisgép-bővítmény engedélyezése
Telepítse az ügynököt a Naplóelemzési, és csatlakozzon az Azure virtuális géphez, amely segítségével az fut a [Azure-portálon](https://portal.azure.com).

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>A Log Analytics-ügynök telepítése, és csatlakoztassa a virtuális gépet a Naplóelemzési munkaterület
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Válassza ki **Tallózás** a portálon, és lépjen a bal oldalon **Naplóelemzés (OMS)** , és jelölje ki.
3. A Naplóelemzési munkaterület, jelölje ki azt, amelyik az Azure virtuális Géphez használni kívánt.  
   ![OMS-munkaterület](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4. A **analytics management naplózása**, jelölje be **virtuális gépek**.  
   ![Virtuális gépek](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5. A közül **virtuális gépek**, válassza ki a virtuális gépre, amelyen szeretné az ügynök telepítéséhez. A **OMS kapcsolat állapota** a virtuális gép azt mutatja, hogy a **nincs csatlakoztatva**.  
   ![Virtuális gép nincs csatlakoztatva](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6. Válassza ki a virtuális gép részleteit, **Connect**. Az ügynök automatikusan telepítve és beállítva a Naplóelemzési munkaterület. Ez a folyamat néhány percet vesz igénybe, amely idő alatt az OMS-kapcsolat állapota *csatlakozás...*  
   ![Csatlakozás a virtuális gép](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7. Csatlakoztassa az ügynököt, és telepítése után a **OMS kapcsolat** állapota frissülni fognak megjelenítése **a munkaterület**.  
   ![Csatlakoztatva](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)

## <a name="enable-the-vm-extension-using-powershell"></a>A PowerShell használatával Virtuálisgép-bővítmény engedélyezése
A virtuális gép PowerShell használatával történő konfigurálásakor meg kell adnia a **workspaceId** és **workspaceKey**. A json-konfigurációt a tulajdonságnevek megkülönböztetik **kis-és nagybetűket**.

Az azonosító található, és a kulcs a **beállítások** oldalán az OMS-portálon, vagy a PowerShell használatával az előző példában látható módon.

![Munkaterületének Azonosítóját és az elsődleges kulcs](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

Nincsenek Azure klasszikus virtuális gépek és a Resource Manager virtuális gépek különböző parancsokat. Az alábbiakban például a klasszikus és Resource Manager virtuális gépeket.

Klasszikus virtuális gépeket használja a következő PowerShell-példa:

```PowerShell
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Erőforrás-kezelő Linux virtuális gépek a következő parancssori felület használatával
```azurecli
az vm extension set --resource-group myRGMonitor --vm-name myMonitorVM --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --version 1.3 --protected-settings ‘{"workspaceKey": "<workspace-key>"}’ --settings ‘{"workspaceId": "<workspace-id>"}’ 
```

A virtuális gépek erőforrás-kezelő használja a következő PowerShell-példa:

```PowerShell
Login-AzureRMAccount
Select-AzureRMSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```


## <a name="deploy-the-vm-extension-using-a-template"></a>A sablon használatával Virtuálisgép-bővítmény telepítése
Azure Resource Manager használatával létrehozhat egy sablont (JSON formátumban), amely meghatározza a központi telepítés és az alkalmazás konfigurációját. Ez a sablon Resource Manager sablonként is ismert és deklaratív lehetőséget biztosít a telepítés meghatározásához. Egy sablon használatával ismételten telepítheti az alkalmazást, az alkalmazás életciklusa és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz alatt.

A Naplóelemzési ügynök beleértve a Resource Manager-sablon részeként, biztosítható, hogy előre konfigurálva, hogy a Naplóelemzési munkaterület-e minden virtuális géphez.

Erőforrás-kezelő sablonokkal kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).

Az alábbiakban látható egy példa a Resource Manager-sablont, amely a Microsoft Monitoring Agent-kiterjesztés telepítése a Windows rendszerű virtuális gép telepítéséhez használt. Ez a sablon a következő tipikus virtuálisgép-sablont, az alábbi kiegészítésekkel:

* workspaceId és workspaceName paraméterek
* Erőforrás-kiterjesztésben megadottaknak Microsoft.EnterpriseCloud.Monitoring
* A workspaceId és workspaceSharedKey kereséséhez kimenetek

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMS workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

A sablon a következő PowerShell-parancs segítségével telepítheti:

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-the-log-analytics-vm-extension"></a>Hibaelhárítás a napló Analytics Virtuálisgép-bővítmény
Általában kap egy üzenetet, ha a probléma, az Azure portálon vagy az Azure powershell.

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Keresse meg a virtuális Gépet, és nyissa meg a virtuális gép részletei.
3. Kattintson a **bővítmények** ellenőrzése, ha az OMS-bővítmény engedélyezve van-e.

   ![Virtuálisgép-bővítmények nézetet](./media/log-analytics-azure-vm-extension/oms-vmview-extensions.png)

4. Kattintson a *MicrosoftMonitoringAgent*(Windows) vagy *OmsAgentForLinux*(Linux) bővítményt, és a nézet részletek. 

   ![Virtuálisgép-bővítmény részletei](./media/log-analytics-azure-vm-extension/oms-vmview-extensiondetails.png)

### <a name="troubleshooting-windows-virtual-machines"></a>Hibaelhárítás Windows virtuális gépek
Ha a *Microsoft Monitoring Agent* nem telepíti a Virtuálisgép-ügynök bővítmény vagy reporting, végezheti el a probléma elhárítása érdekében az alábbi lépéseket.

1. Az Azure Virtuálisgép-ügynök telepítésének ellenőrzése és működik a megfelelő segítségével lépéseit [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Emellett áttekintheti a Virtuálisgép-ügynök naplófájlját`C:\WindowsAzure\logs\WaAppAgent.log`
   * Ha a napló nem létezik, a Virtuálisgép-ügynök nincs telepítve.
     * [Az Azure Virtuálisgép-ügynök telepítése a klasszikus virtuális gépeken](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
2. Ellenőrizze, hogy a Microsoft Monitoring Agent bővítmény szívverés feladat fut-e az alábbi lépéseket követve:
   * Jelentkezzen be a virtuális gép
   * Nyissa meg a Feladatütemezőt, és keresse a `update_azureoperationalinsight_agent_heartbeat` feladat
   * Erősítse meg a feladat engedélyezve van, és egy percenként fut.
   * Ellenőrizze, hogy a szívverés logfile szerepel`C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Tekintse át a Microsoft Monitoring Agent VM bővítmény naplófájlokban`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Győződjön meg arról, a virtuális gép PowerShell-parancsfájlok is futtathatók.
5. Győződjön meg arról, nem módosította a C:\Windows\temp engedélyeinek
6. Írja be a következő parancsot egy emelt szintű PowerShell-ablakban a virtuális gépen a Microsoft Monitoring Agent állapotának megtekintése`  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Tekintse át a Microsoft Monitoring Agent telepítési naplófájlok`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

További információkért lásd: [hibaelhárítása a Windows](../virtual-machines/windows/extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="troubleshooting-linux-virtual-machines"></a>Linux virtuális gépek hibaelhárítása
Ha a *Linux OMS-ügynököt* nem telepíti a Virtuálisgép-ügynök bővítmény vagy reporting, végezheti el a probléma elhárítása érdekében az alábbi lépéseket.

1. Ha a bővítmény állapotát *ismeretlen* ellenőrizze, hogy ha az Azure Virtuálisgép-ügynök telepítve van és megfelelően működik-e a virtuális gép ügynök naplófájlját megtekintésével`/var/log/waagent.log`
   * Ha a napló nem létezik, a Virtuálisgép-ügynök nincs telepítve.
   * [Az Azure Virtuálisgép-ügynök telepítése Linux virtuális gépeken](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
2. A többi nem kifogástalan állapot, tekintse át az OMS-ügynököt, a Linux Virtuálisgép-bővítmény naplózza a fájlokat `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` és`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Ha a bővítmény állapota kifogástalan, de nem feltöltött adatok tekintse át az OMS-ügynököt a Linux-naplófájl`/var/opt/microsoft/omsagent/log/omsagent.log`

## <a name="next-steps"></a>Következő lépések
* Konfigurálása [Naplóelemzési adatforrások](log-analytics-data-sources.md) adhatja meg a naplók és a metrikák gyűjtéséhez.
* Az adatok gyűjtését a virtuális gépek [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).
* [A Azure diagnosztikai adatok gyűjtéséhez](log-analytics-azure-storage.md) más Azure-ban futó erőforrások.

A számítógépek számára, amelyek nem az Azure-ban a Naplóelemzési ügynök a következő cikkekben ismertetett módszerekkel telepíthető:

* [Windows rendszerű számítógépek csatlakoztatása szolgáltatáshoz](log-analytics-windows-agents.md)
* [Linux rendszerű számítógépek csatlakoztatása szolgáltatáshoz](log-analytics-linux-agents.md)
