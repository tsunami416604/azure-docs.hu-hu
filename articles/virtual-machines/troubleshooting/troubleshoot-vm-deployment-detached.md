---
title: A virtuális gépek központi telepítésének hibája leválasztott lemezek miatt | Microsoft Docs
description: A virtuális gépek központi telepítésének hibája leválasztott lemezek miatt
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2019
ms.author: vaaga
ms.openlocfilehash: e049a2b914cbf9c4f0ca0f3a1dd0281d58f881b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75486819"
---
# <a name="troubleshoot-virtual-machine-deployment-due-to-detached-disks"></a>A virtuális gépek központi telepítésének hibája leválasztott lemezek miatt

## <a name="symptom"></a>Hibajelenség

Ha olyan virtuális gépet próbál frissíteni, amelynek az előző adatlemeze leválasztása sikertelen volt, előfordulhat, hogy ez a hibakód jelenik meg.

```
Code=\"AttachDiskWhileBeingDetached\" 
Message=\"Cannot attach data disk '{disk ID}' to virtual machine '{vmName}' because the disk is currently being detached or the last detach  operation failed. Please wait until the disk is completely detached, and then try again or delete/detach the disk explicitly again\” 
```

## <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha olyan adatlemezt próbál újra csatolni, amelynek utolsó leválasztási művelete sikertelen volt. Ennek az állapotnak a legjobb módja a hibás lemez leválasztása.

## <a name="solution-1-powershell"></a>1. megoldás: PowerShell

### <a name="step-1-get-the-virtual-machine-and-disk-details"></a>1. lépés: a virtuális gép és a lemez adatainak beolvasása

```azurepowershell-interactive
PS D:> $vm = Get-AzureRmVM -ResourceGroupName "Example Resource Group" -Name "ERGVM999999" 

PS D:> $vm.StorageProfile.DataDisks 
lun          : 0
name         : f94901ef-75ee-4477-9ad6-1c74da50e7ef
createOption : Attach
caching      : ReadOnly
managedDisk  : @{storageAccountType=Premium_LRS; id=/subscriptions/<subscription ID>/resourceGroups/<Resource Group name>}
diskSizeGB   : 8
toBeDetached : False 
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>2. lépés: állítsa be a hibás lemezek jelzőjét "true" értékre.

Szerezze be a hibás lemez tömb indexét, és állítsa be a hibás lemez **toBeDetached** jelzőjét (amely **AttachDiskWhileBeingDetached** hiba történt) az "igaz" értékre. Ez a beállítás azt jelenti, hogy leválasztja a lemezt a virtuális gépről. A hibás lemez neve megtalálható a **errorMessage**.

> ! Megjegyzés: a Get és Put hívásokhoz megadott API-verziónak 2019-03-01 vagy annál nagyobbnak kell lennie.

```azurepowershell-interactive
PS D:> $vm.StorageProfile.DataDisks[0].ToBeDetached = $true 
```

Másik lehetőségként leválaszthatja ezt a lemezt az alábbi parancs használatával is, amely az API-verziókat használó felhasználók számára hasznos lehet a 2019. március 01. előtt.

```azurepowershell-interactive
PS D:> Remove-AzureRmVMDataDisk -VM $vm -Name "<disk ID>" 
```

### <a name="step-3-update-the-virtual-machine"></a>3. lépés: a virtuális gép frissítése

```azurepowershell-interactive
PS D:> Update-AzureRmVM -ResourceGroupName "Example Resource Group" -VM $vm 
```

## <a name="solution-2-rest"></a>2. megoldás: REST

### <a name="step-1-get-the-virtual-machine-payload"></a>1. lépés: szerezze be a virtuális gép hasznos adatait.

```azurepowershell-interactive
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?$expand=instanceView&api-version=2019-03-01
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>2. lépés: állítsa be a hibás lemezek jelzőjét "true" értékre.

Az 1. lépésben visszaadott hasznos adatok esetében állítsa a **toBeDetached** jelzőt a hibás lemezre. Megjegyzés: a Get és Put hívásokhoz megadott API-verziónak vagy annál `2019-03-01` nagyobbnak kell lennie.

**Mintául szolgáló kérelem törzse**

```azurepowershell-interactive
{
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      },
      "dataDisks": [
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "lun": 0,
          "toBeDetached": true
        },
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
          "lun": 1,
          "toBeDetached": false
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Másik lehetőségként eltávolíthatja a hibás adatlemezt a fenti hasznos adatokból, ami hasznos lehet az API-verziókat használó felhasználók számára a 2019. március 01. előtt.

### <a name="step-3-update-the-virtual-machine"></a>3. lépés: a virtuális gép frissítése

Használja a kérelem törzsének a 2. lépésben megadott hasznos adatát, és frissítse a virtuális gépet a következőképpen:

```azurepowershell-interactive
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2019-03-01
```

**Példa a válaszra:**

```azurepowershell-interactive
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "type": "Microsoft.Compute/virtualMachines",
  "properties": {
    "osProfile": {
      "adminUsername": "{your-username}",
      "secrets": [],
      "computerName": "myVM",
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/nsgExistingNic",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "osType": "Windows",
        "caching": "ReadWrite",
        "createOption": "FromImage",
        "name": "myVMosdisk",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        }
      },
      "dataDisks": [
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 0,
          "diskSizeGB": 1023,
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "toBeDetached": true
        },
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 1,
          "diskSizeGB": 1023,
          "toBeDetached": false
        }
      ]
    },
    "vmId": "3906fef9-a1e5-4b83-a8a8-540858b41df0",
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "provisioningState": "Updating"
  },
  "name": "myVM",
  "location": "westus"
}
```

## <a name="next-steps"></a>Következő lépések

Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, tekintse meg [az RDP-kapcsolatok hibaelhárítása Azure-beli virtuális géppel](troubleshoot-rdp-connection.md)című témakört.

A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákért lásd: az [alkalmazások kapcsolódási problémáinak elhárítása Windows rendszerű virtuális gépen](troubleshoot-app-connection.md).
