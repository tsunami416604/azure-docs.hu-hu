---
title: Az Azure-beli Linuxos virtuális gép lemezeinek titkosításához |} A Microsoft Docs
description: A fokozott biztonság az Azure CLI használatával egy Linux rendszerű virtuális gépen a virtuális lemezek titkosítása
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 15bd3cf2ab6ea5285662610c2c0a850bb180e2f8
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658768"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Az Azure-beli Linuxos virtuális gép titkosítása

Bővített virtuális gép (VM) biztonsági és megfelelőségi virtuális lemezeket és a virtuális gépre titkosíthatók. Virtuális gépek vannak titkosítva, amely egy Azure Key vaultban biztosított titkosítási kulcsok használatával. Szabályozhatja a kriptográfiai kulcsokat és naplózhatja azok használatát. Ez a cikk részletesen bemutatja az Azure parancssori felületével Linux virtuális gép virtuális lemezeinek titkosításához. 

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/bash](https://shell.azure.com/bash) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha helyi telepítése és használata a parancssori felület, ez a cikk megköveteli, hogy futnak-e az Azure CLI 2.0.30-as verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Lemeztitkosítás – áttekintés
Rest használatával titkosított virtuális lemezhez a Linux rendszerű virtuális gépek [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Nem jár költséggel az Azure-beli virtuális lemezek titkosításához. Titkosítási kulcsok Azure Key Vault szoftver védelemmel vannak tárolva, vagy Ön importálhat vagy hozhat létre a kulcsokat hardveres biztonsági modulokban (HSM) certified FIPS 140-2 szabványnak megfelelő 2. szint. Ezek a titkosítási kulcsok feletti ellenőrzés megtartása és naplózhatja azok használatát. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. 

Virtuális gépek titkosításához a folyamat a következőképpen történik:

1. Hozzon létre egy titkosítási kulcsot az Azure Key vaultban.
1. A titkosítási kulcs is használható titkosításához lemezek konfigurálása.
1. A virtuális lemezek lemeztitkosítás engedélyezve.
1. A szükséges titkosítási kulcsokat az Azure Key vault kérnek.
1. A virtuális lemezek vannak titkosítva, a megadott titkosítási kulcs használatával.


## <a name="requirements-and-limitations"></a>Követelmények és korlátozások
Támogatott esetek és lemeztitkosításhoz követelmények:

* A következő Linux-kiszolgálón SKU - Ubuntu, a CentOS, a SUSE és a SUSE Linux Enterprise Server (SLES) és a Red Hat Enterprise Linux.
* Az azonos Azure-régióban és az előfizetés összes erőforrás (például a Key Vault, a Storage-fiók és a virtuális gép) kell lennie.
* Standard A, D, DS, G, GS, sorozatot, stb.
* Frissítése folyamatban van a titkosítási kulcsok már a titkosított Linux rendszerű virtuális gépen.

Lemeztitkosítás jelenleg nem támogatott a következő esetekben:

* Alapszintű csomag virtuális gépeket.
* A virtuális gépek a klasszikus üzemi modellel létrehozott.
* Linux rendszerű virtuális gépek operációs rendszer lemeztitkosítással letiltása.
* Egyéni Linux-rendszerképek használata.

További információ a támogatott forgatókönyvekkel és korlátozásokkal, lásd: [az Azure Disk Encryption IaaS virtuális gépekhez](../../security/azure-security-disk-encryption.md)


## <a name="create-an-azure-key-vault-and-keys"></a>Az Azure Key Vaultban, és a kulcsok létrehozása
A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa a paraméter nevek a következők *myResourceGroup*, *myKey*, és *myVM*.

Az első lépés, ha az Azure Key Vaultban tárolni a titkosítási kulcsokat. Az Azure Key Vaultban tárolhatók, kulcsok, titkos kódok és jelszavak, amelyek lehetővé teszik, hogy biztonságosan végrehajtja azokat az alkalmazásokat és szolgáltatásokat. A virtuális lemez titkosításhoz a Key Vault titkosításakor vagy visszafejtésekor a virtuális lemezek használt kriptográfiai kulcs tárolására használhatja.

Engedélyezze az Azure Key Vault-szolgáltató belül az Azure-előfizetésbe [az provider register](/cli/azure/provider#az-provider-register) , és hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az-group-create). Az alábbi példa létrehoz egy erőforráscsoport-nevet *myResourceGroup* a a `eastus` helye:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

Az Azure Key Vault a kriptográfiai kulcsokat és a kapcsolódó számítási erőforrásokat, például a storage és a virtuális gépre tartalmazó ugyanabban a régióban kell lennie. Hozzon létre egy Azure Key Vault- [az keyvault létrehozása](/cli/azure/keyvault#az-keyvault-create) , és engedélyezze a Key Vault lemeztitkosítás való használatra. Adjon meg egy egyedi Key Vault számára *keyvault_name* módon:

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Titkosítási kulcsok használatával a szoftver vagy hardver biztonsági modell (HSM) védelmet is tárolhatja. Egy prémium szintű Key Vault HSM használata szükséges. Nincs hozhat létre egy standard szintű tároló Key Vaultot, szoftveres védelemmel ellátott kulcsokkal helyett a Key Vault premium további költségekkel járnak. Hozzon létre egy Key Vault premium, az előző lépésben adja hozzá `--sku Premium` a parancshoz. Az alábbi példában szoftveres védelemmel ellátott kulcsokkal egy standard Key Vault létrehozása óta.

Mindkét védelme esetében az Azure platform kell hozzáférést kérni a titkosítási kulcsok a virtuális gép indításakor a virtuális lemezek visszafejtéséhez. Hozzon létre egy titkosítási kulcsot a Key vaultban a [az keyvault key létrehozása](/cli/azure/keyvault/key#az-keyvault-key-create). A következő példában létrehozunk egy kulcsot *myKey*:

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Egy virtuális gépet a [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) és 5 GB-os adatlemezt. Csak bizonyos Piactéri lemezképek támogatja a lemeztitkosítást. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* használatával egy *Ubuntu 16.04 LTS* lemezkép:

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

Ssh-KAPCSOLATOT a virtuális gépet a *publicIpAddress* az előző parancs kimenetében található. Hozzon létre egy partíciót és a fájlrendszer, majd az adatlemezt csatlakoztatni. További információkért lásd: [csatlakozhat egy Linux rendszerű virtuális gép csatlakoztatása az új lemez](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Zárja be az SSH-munkamenet.


## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása


A virtuális gép titkosítása [az vm encryption engedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable):

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

A titkosítási folyamat végrehajtásához némi időt vesz igénybe. A folyamat állapotának monitorozásához [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

Amikor végzett, a kimenet a következő példához hasonló fog kinézni:

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>További adatlemezek hozzáadása
Az adatlemezek titkosított, miután további virtuális lemezek hozzáadása a virtuális Géphez, és titkosítani. 

Után az adatlemezt a virtuális gép van adva, futtassa újra a parancsot a virtuális lemezek titkosítása a következőképpen:

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>További lépések
* Az Azure Key Vault, beleértve a kriptográfiai kulcsokat és a tárolók törlése kezelésével kapcsolatos további információért lásd: [kezelése a Key Vault parancssori felület használatával](../../key-vault/key-vault-manage-with-cli2.md).
* További információ a lemeztitkosítást, például egy titkosított egyéni VM feltöltése az Azure-ba való felkészülés: [az Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
