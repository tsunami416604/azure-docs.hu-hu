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
ms.openlocfilehash: 0f77b46be0207b0ce96e6dc2562fb5298afbe36b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928182"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Hozzon létre egy Linux virtuális gép SSH-hitelesítést használó REST API-val

Egy virtuális gépet (VM) az Azure-ban különböző paraméterek, például hely, hardver mérete, operációs rendszer lemezképét és bejelentkezési hitelesítő adatokat határozza meg. Ez a cikk bemutatja, hogyan hozhat létre Linux rendszerű virtuális gép SSH-hitelesítést használó a REST API használatával.

Hozzon létre vagy frissíthető egy virtuális gép, használja a következő *PUT* műveletet:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>Kérés létrehozása

Hozhat létre a *PUT* kérelem, a `{subscription-id}` paraméter megadása kötelező. Ha több előfizetéssel rendelkezik, tekintse meg [több előfizetés használata](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Megadhat egy `{resourceGroupName}` és `{vmName}` az erőforrások és a `api-version` paraméter. Ez a cikk `api-version=2017-12-01`.

A következő fejléceket szükség:

| Kérelem fejléce   | Leírás |
|------------------|-----------------|
| *A Content-Type:*  | Kötelező. Állítsa be `application/json`. |
| *Hitelesítés:* | Kötelező. Egy érvényes értékre `Bearer` [hozzáférési jogkivonat](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

A kérés létrehozásával kapcsolatos további információkért lásd: [egy REST API-kérés/válasz összetevői](/rest/api/azure/#components-of-a-rest-api-requestresponse).

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

A rendelkezésre álló definíciók a kérelem törzsében szereplő teljes listáját lásd: [virtuális gépek létrehozása vagy frissítése a kérelem törzsében defintions](/rest/api/compute/virtualmachines/createorupdate#definitions).

### <a name="example-request-body"></a>Példa kérelem törzse

A következő példa kérelem törzse meghatározza egy Ubuntu 18.04-LTS-rendszerképhez, amely prémium szintű felügyelt lemezeket használ. SSH nyilvános kulcsos hitelesítés szolgál, és a virtuális gép használ egy meglévő virtuális hálózati kártya (NIC), amely rendelkezik [korábban létrehozott](../../virtual-network/virtual-network-network-interface.md). Adja meg a nyilvános SSH-kulcsot a a *osProfile.linuxConfiguration.ssh.publicKeys.keyData* mező. Ha szükséges, [SSH-kulcspár létrehozása](mac-create-ssh-keys.md).

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

Nincsenek a művelethez létrehozni vagy frissíteni a virtuális gép két sikeres válaszok:

| Name (Név)        | Típus                                                                              | Leírás |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Created | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Létrehozva     |

További információ a REST API-válaszok: [feldolgozni a válaszüzenet](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Példaválasz

Egy tömörített *201 Created* az előző példában kérelem törzse, amely létrehoz egy virtuális Gépet válasza egy *vmId* hozzá lett rendelve, és a *provisioningState* *Létrehozása*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>További lépések

További információ az Azure REST API-kat vagy más felügyeleti eszközök, például az Azure CLI 2.0 vagy az Azure PowerShell-lel tekintse meg a következőket:

- [Az Azure Compute-szolgáltató REST API-val](/rest/api/compute/)
- [Azure REST API használatának első lépései](/rest/api/azure/)
- [Azure CLI 2.0](/cli/azure/)
- [Azure PowerShell-modul](/powershell/azure/overview)
