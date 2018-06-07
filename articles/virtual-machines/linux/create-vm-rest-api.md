---
title: Linux virtuális gép létrehozása Azure REST API-val |} Microsoft Docs
description: 'Útmutató: a Linux virtuális gép létrehozása az Azure REST API-val felügyelt lemezek és az SSH-hitelesítést használó Azure-ban.'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: e3f41bea26e9a5ff45b31ae9d9a2e5955317ad7a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825327"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Hozzon létre egy Linux virtuális gép SSH-hitelesítést használó REST API-val

A virtuális gép (VM) az Azure-ban a különböző paraméterek, például hely, a hardver mérete, a operációsrendszer-lemezkép és a bejelentkezési hitelesítő adatok határozzák meg. Ez a cikk bemutatja, hogyan hozzon létre egy Linux virtuális gép SSH-hitelesítést használó a REST API használatával.

Hozzon létre vagy frissíthető egy virtuális gép, használja a következő *PUT* műveletet:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>Kérelem létrehozása

Létrehozásához a *PUT* kérelem, az `{subscription-id}` paraméter megadása kötelező. Ha több előfizetéssel rendelkezik, tekintse meg [működik-e több előfizetés](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Megadhatja egy `{resourceGroupName}` és `{vmName}` erőforrások, valamint a `api-version` paraméter. Ebben a cikkben az `api-version=2017-12-01`.

A következő fejléc szükség:

| Fejléc   | Leírás |
|------------------|-----------------|
| *Content-Type:*  | Kötelező. Beállítása `application/json`. |
| *Engedélyezési:* | Kötelező. Egy érvényes értékre `Bearer` [hozzáférési jogkivonat](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

A kérés létrehozásával kapcsolatos további információkért lásd: [a REST API-kérelem/válasz összetevők](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>A kérelem törzsében létrehozása

A következő közös definíciók alapján állítja össze a kérés törzsében:

| Name (Név)                       | Szükséges | Típus                                                                                | Leírás  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True (Igaz)     | karakterlánc                                                                              | Erőforrás helye. |
| név                       |          | karakterlánc                                                                              | A virtuális gép nevét. |
| properties.hardwareProfile |          | [A HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Adja meg a hardverbeállításokat, a virtuális géphez. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Megadja a virtuálisgép-lemezeket a tároló beállításait. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Meghatározza a virtuális gép operációs rendszer beállításait. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Adja meg a virtuális gép hálózati adaptereihez. |

A definíciók a kérelem törzsében szereplő teljes listáját lásd: [virtuális gépek létrehozása vagy frissítése a kérelem törzsében defintions](/rest/api/compute/virtualmachines/createorupdate#definitions).

### <a name="example-request-body"></a>Példa kérelemtörzset

A következő példa kérés törzsében felügyelt prémium lemezeket használó Ubuntu 18.04-es lts verzió lemezkép határozza meg. SSH nyilvános kulcs hitelesítés van használatban, és a virtuális gép használ-e egy meglévő virtuális hálózati kártya (NIC), hogy rendelkezik [korábban létrehozott](../../virtual-network/virtual-network-network-interface.md). Adja meg az SSH nyilvános kulcs a *osProfile.linuxConfiguration.ssh.publicKeys.keyData* mező. Ha szükséges, akkor [hozható létre egy SSH-kulcspár](mac-create-ssh-keys.md).

```json
{
  "location": "eastus",
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
      "computerName": "myVM",
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
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  },
  "name": "myVM"
}
```

## <a name="responses"></a>Válaszok

A művelet sikerült létrehozni vagy frissíteni a virtuális gépek két sikeres válaszok van:

| Name (Név)        | Típus                                                                              | Leírás |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201-es létrehozása | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Létrehozva     |

További információ a REST API-válaszok: [feldolgozni a válaszüzenetet](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Példaválasz

A tömörített *201 Created* az előző példa kérés törzsében hozza létre a virtuális gépek kapott válasz látható egy *vmId* van hozzárendelve, és a *provisioningState* *Létrehozása*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>További lépések

További információ az Azure REST API-k vagy más felügyeleti eszközökhöz, például az Azure CLI 2.0 vagy az Azure PowerShell-lel tekintse át a következőket:

- [Az Azure Compute szolgáltató REST API-n](/rest/api/compute/)
- [Ismerkedés az Azure REST API](/rest/api/azure/)
- [Azure CLI 2.0](/cli/azure/)
- [Az Azure PowerShell modul](/powershell/azure/overview)
