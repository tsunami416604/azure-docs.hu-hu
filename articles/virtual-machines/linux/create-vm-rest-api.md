---
title: Linux rendszerű virtuális gép létrehozása az Azure REST API-val |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Linux rendszerű virtuális gép az Azure Managed Disks, valamint SSH-hitelesítést használó Azure REST API-val.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 3eeaee9bc6320231f10aa85227e2f43756181806
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433480"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Hozzon létre egy Linux virtuális gép SSH-hitelesítést használó REST API-val

Egy Linux rendszerű virtuális gép (VM) az Azure-ban különböző erőforrásokhoz, például a lemezek áll, és a hálózati adapterek, és határozza meg a paramétereket, például a helyét, méretét és az operációs rendszer lemezkép és a hitelesítési beállításokat.

Létrehozhat Linux virtuális gép az Azure Portalon, az Azure CLI 2.0, számos Azure SDK-k az Azure Resource Manager-sablonok és például az Ansible vagy Terraform számos külső eszközök. Ezek az eszközök végső soron a REST API használata a Linux rendszerű virtuális gép létrehozásához.

Ez a cikk bemutatja, hogyan a felügyelt lemezek és SSH-hitelesítést Ubuntu 18.04-LTS rendszert futtató Linux rendszerű virtuális gép létrehozása a REST API használatával.

## <a name="before-you-start"></a>Előkészületek

Hozzon létre, és küldje el a kérelmet, mielőtt lesz szüksége:

* A `{subscription-id}` az előfizetéshez
  * Ha több előfizetéssel rendelkezik, tekintse meg [több előfizetés használata](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions)
* A `{resourceGroupName}` előre létrehozott
* A [virtuális hálózati adapter](../../virtual-network/virtual-network-network-interface.md) ugyanabban az erőforráscsoportban
* Ssh-kulcs (is [hozzon létre egy új](mac-create-ssh-keys.md) Ha nem rendelkezik ilyennel)

## <a name="request-basics"></a>Kérelem alapjai

Hozzon létre vagy frissíthető egy virtuális gép, használja a következő *PUT* műveletet:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Mellett a `{subscription-id}` és `{resourceGroupName}` paraméterek kell megadnia a `{vmName}` (`api-version` nem kötelező, azonban ez a cikk teszteltük `api-version=2017-12-01`)

A következő fejléceket szükség:

| Kérelem fejléce   | Leírás |
|------------------|-----------------|
| *A Content-Type:*  | Kötelező. Állítsa be `application/json`. |
| *Hitelesítés:* | Kötelező. Egy érvényes értékre `Bearer` [hozzáférési jogkivonat](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

REST API-kérelmek használatával kapcsolatos általános információkért lásd: [egy REST API-kérés/válasz összetevői](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

A következő gyakori definíciókat hozhat létre a kéréstörzs használhatók:

| Name (Név)                       | Szükséges | Típus                                                                                | Leírás  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True (Igaz)     | sztring                                                                              | Erőforrás helye. |
| név                       |          | sztring                                                                              | A virtuális gép nevét. |
| properties.hardwareProfile |          | [A HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Itt adhatja meg a hardverbeállításokat, a virtuális gép. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Itt adhatja meg a virtuális gépek lemezeinek tárolási beállításait. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Meghatározza a virtuális gép operációs rendszer beállításait. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Adja meg a hálózati adaptereket a virtuális gép. |

Egy példa kérelem törzse nem éri el. Győződjön meg arról, megadhatja a virtuális gép nevét, a `{computerName}` és `{name}` paraméterek, a csoportban létrehozott hálózati adapter neve `networkInterfaces`, a felhasználónevére `adminUsername` és `path`, és a *nyilvános*az SSH-kulcspár része (található, például `~/.ssh/id_rsa.pub`) a `keyData`. Más paraméterek módosítása érdemes `location` és `vmSize`.  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

A rendelkezésre álló definíciók a kérelem törzsében szereplő teljes listáját lásd: [virtuális gépek létrehozása vagy frissítése a kérelem törzsében defintions](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>A kérés küldése

Az ügyfél, a beállításokat a HTTP-kérelem küldéséhez használhatja. Is használhatja egy [böngészőben eszköz](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) kattintva a **kipróbálás** gombra.

### <a name="responses"></a>Válaszok

Nincsenek a művelethez létrehozni vagy frissíteni a virtuális gép két sikeres válaszok:

| Name (Név)        | Típus                                                                              | Leírás |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Created | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Létrehozva     |

Egy tömörített *201 Created* az előző példában kérelem törzse, amely létrehoz egy virtuális Gépet válasza egy *vmId* hozzá lett rendelve, és a *provisioningState* *Létrehozása*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

További információ a REST API-válaszok: [feldolgozni a válaszüzenet](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>További lépések

További információ az Azure REST API-kat vagy más felügyeleti eszközök, például az Azure CLI-vel vagy az Azure PowerShell-lel tekintse meg a következőket:

- [Az Azure Compute-szolgáltató REST API-val](/rest/api/compute/)
- [Azure REST API használatának első lépései](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Azure PowerShell-modul](/powershell/azure/overview)
