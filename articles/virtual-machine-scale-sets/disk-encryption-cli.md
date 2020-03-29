---
title: Az Azure CLI-vel az Azure SCALE-készletek lemezeinek titkosítása
description: Megtudhatja, hogy az Azure PowerShell használatával hogyan titkosíthatja a virtuálisgép-példányokat és a csatlakoztatott lemezeket egy Windows virtuálisgép-méretezési csoportban
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 557d5c023acbc7987d58c9e78bfe11e25f314879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279076"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Az operációs rendszer és a csatlakoztatott adatlemezek titkosítása egy virtuálisgép-méretezési csoportban az Azure CLI-vel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használhatja az Azure CLI egy virtuális gép méretezési csoport létrehozása és titkosítása. Az Azure Disk titkosítás virtuálisgép-méretezési csoportra történő alkalmazásáról az [Azure Lemezesítés a virtuálisgép-méretezési készletekhez című témakörben talál](disk-encryption-overview.md)további információt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez az oktatóanyag megköveteli, hogy az Azure CLI 2.0.31-es vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Mielőtt létrehozhatna egy méretezési csoportot, létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss) paranccsal. A következő példa egy *myScaleSet* nevű méretezési csoportot hoz létre, amely automatikusan frissül a módosítások alkalmazásakor, valamint SSH-kulcsokat hoz létre, amennyiben azok még nem léteznének a *~/.ssh/id_rsa* helyen. Minden virtuálisgép-példányhoz egy 32 Gbita adatlemez csatlakozik, és az Azure [Custom Script Extension](../virtual-machines/linux/extensions-customscript.md) az a [vmss bővítménykészlettel](/cli/azure/vmss/extension)előkészíti az adatlemezeket:

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Lemeztitkosításhoz engedélyezett Azure-kulcstartó létrehozása

Az Azure Key Vault olyan kulcsokat, titkos kulcsokat vagy jelszavakat tárolhat, amelyek lehetővé teszik azok biztonságos megvalósítását az alkalmazásokban és a szolgáltatásokban. A kriptográfiai kulcsok at az Azure Key Vault szoftvervédelemmel tárolja, vagy importálhatja vagy létrehozhatja a kulcsokat a HARDVERbiztonsági modulok (HSM) FIPS 140-2 szintű 2 szabványoknak megfelelően hitelesített hardverbiztonsági modulokban.Cryptographic keys are stored in Azure Key Vault using software-protection, or you can import or generate your keys in Hardware Security Modules (HSM) certified to FIPS 140-2 level 2 standards. Ezek a kriptográfiai kulcsok a virtuális géphez csatlakoztatott virtuális lemezek titkosítására és visszafejtésére szolgálnak. Ön megtartja a titkosítási kulcsok feletti ellenőrzést, és naplózhatja azok használatát.

Határozza meg saját egyedi *keyvault_name.* Ezután hozzon létre egy KeyVault [az keyvault létrehozása](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) ugyanabban az előfizetésben és régióban, mint a méretezési csoport, és állítsa be a *--enabled-for-disk-encryption hozzáférési* szabályzat.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Meglévő kulcstartó használata

Ez a lépés csak akkor szükséges, ha rendelkezik egy meglévő Key Vault, amely lemeztitkosítással használni kívánt. Hagyja ki ezt a lépést, ha az előző szakaszban létrehozott egy key vaultot.

Határozza meg saját egyedi *keyvault_name.* Ezután frissítette a [KeyVault-ot az keyvault frissítésével,](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) és beállította a *--enabled-for-disk-encryption hozzáférési* házirendet.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Titkosítás engedélyezése

A virtuálisgép-példányok méretezési csoportban való titkosításához először szerezzen be néhány információt a Key Vault erőforrás-azonosítóról az [keyvault show-val.](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show) Ezeket a változókat használják, majd indítsa el a titkosítási folyamat [ot vmss titkosítás engedélyezése:](/cli/azure/vmss/encryption#az-vmss-encryption-enable)

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

A titkosítási folyamat elindítása egy-két percet is igénybe vehet.

Mivel a méretezési csoport frissítési házirend a méretezési csoport egy korábbi lépésben létrehozott *automatikus,* a virtuálisgép-példányok automatikusan elindítja a titkosítási folyamatot. Olyan méretezési csoportokon, ahol a frissítési szabályzat manuális, indítsa el a titkosítási szabályzatot a virtuálisgép-példányok az [vmss update-példányok.](/cli/azure/vmss#az-vmss-update-instances)

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Titkosítás engedélyezése a KEK használatával a kulcs burkolórendszeréhez

A virtuálisgép-méretezési csoport titkosításakor kulcstitkosítási kulcsot is használhat a nagyobb biztonság érdekében.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> A kulcstitkosítási kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Titkosítási folyamat ellenőrzése

A lemeztitkosítás állapotának ellenőrzéséhez használja az [az vmss titkosítási show-t:](/cli/azure/vmss/encryption#az-vmss-encryption-show)

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Ha a vm-példányok titkosítva vannak, az állapotkód a *EncryptionState/encrypted*jelentést jelenti, ahogy az a következő példa kimeneti képen látható:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Titkosítás letiltása

Ha a továbbiakban nem szeretne titkosított virtuálisgép-példánylemezeket használni, az [az vmss titkosítás letiltásával](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) letilthatja a titkosítást az alábbiak szerint:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>További lépések

- Ebben a cikkben az Azure CLI egy virtuálisgép-méretezési csoport titkosításához. [Azure PowerShell-](disk-encryption-powershell.md) vagy [Azure Resource Manager-sablonokat](disk-encryption-azure-resource-manager.md)is használhat.
- Ha azt szeretné, hogy az Azure Disk Encryption egy másik bővítmény kiépítése után alkalmazva legyen, [használhatja a bővítmény-szekvenálást.](virtual-machine-scale-sets-extension-sequencing.md) 
- A Linux méretezési csoport adatlemez-titkosításának végpontok közötti kötegfájlja [itt](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)található. Ebben a példában létrehoz egy erőforráscsoportot, a Linux méretezési csoport, csatlakoztatja az 5 GB-os adatlemezt, és titkosítja a virtuális gép méretezési készlet.
