---
title: Linuxos virtuális gép létrehozása a REST API-val
description: Ismerje meg, hogyan hozhat létre egy Linux virtuális gépet az Azure-ban, amely felügyelt lemezeket és SSH-hitelesítést használ az Azure REST API-val.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 1594c030839cccdd48c4b032c6ad92f746f78e26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970276"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Hozzon létre egy Linux virtuális gépet, amely SSH-hitelesítést használ a REST API-val

Az Azure-ban egy Linux virtuális gép (VM) különböző erőforrásokból, például lemezekből és hálózati felületekből áll, és olyan paramétereket határoz meg, mint a hely, a méret és az operációs rendszer lemezképe és a hitelesítési beállítások.

Linuxos virtuális gépeket az Azure Portalon, az Azure CLI 2.0-n, számos Azure SDK-n, Azure Resource Manager-sablonon és számos külső gyártótól származó eszközön, például az Ansible-en vagy a Terraformon keresztül hozhat létre. Ezek az eszközök végső soron a REST API-t használják a Linux virtuális gép létrehozásához.

Ez a cikk bemutatja, hogyan használhatja a REST API-t egy Linux VM ubuntu 18.04-LTS rendszerű felügyelt lemezekkel és SSH-hitelesítéssel.

## <a name="before-you-start"></a>Előkészületek

A kérelem létrehozása és elküldése előtt a következőkre lesz szüksége:

* Az `{subscription-id}` előfizetéshez
  * Ha több előfizetéssel rendelkezik, olvassa el a [Több előfizetés sel való együttműködés](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* A `{resourceGroupName}` ön által létrehozott idő előtt
* [Virtuális hálózati illesztő](../../virtual-network/virtual-network-network-interface.md) ugyanabban az erőforráscsoportban
* SSH kulcspár (ha még nincs ilyen, [újat hozhat létre)](mac-create-ssh-keys.md)

## <a name="request-basics"></a>Alapvető tudnivalók kérése

Virtuális gép létrehozásához vagy frissítéséhez *PUT* használja a következő PUT-műveletet:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

A mellett, `{subscription-id}` `{resourceGroupName}` hogy a és a paraméterek, `{vmName}` `api-version` akkor meg kell adnia `api-version=2017-12-01`a ( nem kötelező, de ez a cikk tesztelték)

A következő fejlécek megadása kötelező:

| Kérelem fejléce   | Leírás |
|------------------|-----------------|
| *Content-Type* (Tartalomtípus):  | Kötelező. Állítsa `application/json` értékre. |
| *Authorization* (Engedélyezés): | Kötelező. Állítsa egy érvényes `Bearer` [hozzáférési jogkivonatra](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

A REST API-kérelmekkel kapcsolatos általános tudnivalókért olvassa el [a REST API-kérelem/válasz összetevői című témakört.](/rest/api/azure/#components-of-a-rest-api-requestresponse)

## <a name="create-the-request-body"></a>A kérelemtörzs létrehozása

A következő közös definíciók segítségével hozzon létre egy kérelem törzs:

| Név                       | Kötelező | Típus                                                                                | Leírás  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True (Igaz)     | sztring                                                                              | Erőforrás helye. |
| név                       |          | sztring                                                                              | A virtuális gép neve. |
| properties.hardwareProfil |          | [HardwareProfile (Hardverprofil)](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | A virtuális gép hardverbeállításainak megadása. |
| properties.storageProfil  |          | [StorageProfile (StorageProfile)](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | A virtuálisgép-lemezek tárolási beállításait adja meg. |
| properties.osProfil       |          | [OSProfil](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | A virtuális gép operációsrendszer-beállításainak megadása. |
| properties.networkProfil  |          | [Hálózati profil](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | A virtuális gép hálózati összeköttetéseit adja meg. |

Egy példa kérelem törzs e alatt található. Győződjön meg arról, hogy `{computerName}` `{name}` megadja a virtuális gép nevét a és `networkInterfaces`a paraméterekben, `adminUsername` `path`a létrehozott hálózati adapter nevét, a felhasználónevét és `~/.ssh/id_rsa.pub`a `keyData`, valamint az SSH kulcspár *nyilvános* részét (amely például található) a alkalmazásban. A módosítani kívánt egyéb paraméterek `location` `vmSize`közé tartozik a és a.  

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

A kérelemtörzsben elérhető definíciók teljes listáját a [Virtuális gépek kérelemtörzs-definíciók létrehozása vagy frissítése](/rest/api/compute/virtualmachines/createorupdate#definitions)című témakörben található.

## <a name="sending-the-request"></a>A kérelem elküldése

Használhatja az ügyfél a preferencia küldéséhez ezt a HTTP-kérelmet. A [böngészőben](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) eszközhasználatát a Kipróbálás gombra kattintva is **használhatja.**

### <a name="responses"></a>Válaszok

A virtuális gép létrehozásához vagy frissítéséhez a műveletre két sikeres válasz érkezett:

| Név        | Típus                                                                              | Leírás |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Létrehozva | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Létrehozva     |

A virtuális gép létrehozásához létrehozott előző példakérelem-törzs ből létrehozott tömörített *201 válasz* azt mutatja, hogy egy *virtuális gép* van hozzárendelve, és a *provisioningState* *létrehozása:*

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

A REST API-válaszokról a [Válaszüzenet feldolgozása](/rest/api/azure/#process-the-response-message)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

Az Azure REST API-król vagy más felügyeleti eszközökről, például az Azure CLI-ről vagy az Azure PowerShellről az alábbi témakörben talál további információt:

- [Azure Compute szolgáltató REST API](/rest/api/compute/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Azure PowerShell-modul](/powershell/azure/overview)
