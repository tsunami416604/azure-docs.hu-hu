---
title: A virtuális gépek leválasztott lemezek miatti telepítésének – problémamegoldás | Microsoft dokumentumok
description: A virtuális gépek leválasztott lemezek miatti telepítésének – problémamegoldás
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486819"
---
# <a name="troubleshoot-virtual-machine-deployment-due-to-detached-disks"></a>A virtuális gépek leválasztott lemezek miatti telepítésének – problémamegoldás

## <a name="symptom"></a>Hibajelenség

Amikor olyan virtuális gépet próbál frissíteni, amelynek korábbi adatlemez-leválasztása sikertelen volt, előfordulhat, hogy ez a hibakód találkozik.

```
Code=\"AttachDiskWhileBeingDetached\" 
Message=\"Cannot attach data disk '{disk ID}' to virtual machine '{vmName}' because the disk is currently being detached or the last detach  operation failed. Please wait until the disk is completely detached, and then try again or delete/detach the disk explicitly again\” 
```

## <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha megpróbál újra csatlakoztatni egy adatlemezt, amelynek utolsó leválasztási művelete sikertelen volt. A legjobb módja annak, hogy ki ebben az állapotban, hogy leválassza a hibás lemezt.

## <a name="solution-1-powershell"></a>1. megoldás: Powershell

### <a name="step-1-get-the-virtual-machine-and-disk-details"></a>1. lépés: A virtuális gép és a lemez részleteinek beszerezni

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

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>2. lépés: Állítsa a hibás lemezek jelzőjét "true" értékre.

A meghibásodott lemez tömbindexének bekéselése, és a hibás lemez **BeDetached** jelzője (amelynél **attachDiskWhileBeingDetached** hiba történt) "true" értékre állítható. Ez a beállítás azt jelenti, hogy le kell választani a lemezt a virtuális gépről. A hibás lemeznév megtalálható a **errorMessage**.

> ! Megjegyzés: A hívások lekéréséhez és lebonyolításához megadott API-verziónak 2019-03-01-es vagy nagyobb verziónak kell lennie.

```azurepowershell-interactive
PS D:> $vm.StorageProfile.DataDisks[0].ToBeDetached = $true 
```

Másik lehetőségként leis választhatja ezt a lemezt az alábbi paranccsal, ami hasznos lehet az API-verziókat használó felhasználók számára 2019.

```azurepowershell-interactive
PS D:> Remove-AzureRmVMDataDisk -VM $vm -Name "<disk ID>" 
```

### <a name="step-3-update-the-virtual-machine"></a>3. lépés: A virtuális gép frissítése

```azurepowershell-interactive
PS D:> Update-AzureRmVM -ResourceGroupName "Example Resource Group" -VM $vm 
```

## <a name="solution-2-rest"></a>2. megoldás: REST

### <a name="step-1-get-the-virtual-machine-payload"></a>1. lépés: A virtuális gép hasznos terhelésének betöltése.

```azurepowershell-interactive
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?$expand=instanceView&api-version=2019-03-01
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>2. lépés: Állítsa a hibás lemezek jelzőjét "true" értékre.

Állítsa be a **BeDetached** jelzőt a hibás lemez true értékre az 1. Megjegyzés: A hívások fogadásához és kezdeményezéséhez megadott `2019-03-01` API-verziónak vagy annál nagyobbnak kell lennie.

**Mintakérelem törzse**

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

Emellett eltávolíthatja a hibás adatlemezt a fenti hasznos adatból is, ami hasznos lehet az API-verziókat használó felhasználók számára 2019.

### <a name="step-3-update-the-virtual-machine"></a>3. lépés: A virtuális gép frissítése

A 2.

```azurepowershell-interactive
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2019-03-01
```

**Mintaválasz:**

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

Ha problémái vannak a virtuális géphez való csatlakozással, olvassa [el az RDP-kapcsolatok hibaelhárítása azure-beli virtuális géphez című témakört.](troubleshoot-rdp-connection.md)

A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákról a [Windows virtuális gépeken az alkalmazáscsatlakozási problémák elhárítása című](troubleshoot-app-connection.md)témakörben nyújt fel problémát.
