---
title: Az Azure-beli Linuxos virtuális gép lemezeinek titkosításához |} A Microsoft Docs
description: A fokozott biztonság az Azure CLI 2.0 használatával Linux virtuális Gépet a virtuális lemezek titkosítása
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
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 75ec087536d6f833a9a2106b1fdf4ed1fd73ef8e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932411"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Az Azure-beli Linuxos virtuális gép titkosítása
Bővített virtuális gép (VM) biztonsági és megfelelőségi virtuális lemezeket és a virtuális gépre titkosíthatók. Virtuális gépek vannak titkosítva, amely egy Azure Key vaultban biztosított titkosítási kulcsok használatával. Szabályozhatja a kriptográfiai kulcsokat és naplózhatja azok használatát. Ez a cikk részletesen bemutatja az Azure CLI 2.0 használatával Linux virtuális gép virtuális lemezeinek titkosításához. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület, ez a cikk megköveteli, hogy futnak-e az Azure CLI 2.0.30-as verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Lemeztitkosítás – áttekintés
Rest használatával titkosított virtuális lemezhez a Linux rendszerű virtuális gépek [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Nem jár költséggel az Azure-beli virtuális lemezek titkosításához. Titkosítási kulcsok Azure Key Vault szoftver védelemmel vannak tárolva, vagy Ön importálhat vagy hozhat létre a kulcsokat hardveres biztonsági modulokban (HSM) certified FIPS 140-2 szabványnak megfelelő 2. szint. Ezek a titkosítási kulcsok feletti ellenőrzés megtartása és naplózhatja azok használatát. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. Egy Azure Active Directory egyszerű szolgáltatás lehetővé teszi a biztonságos kiállító a kriptográfiai kulcsokat, a virtuális gépek vannak kapcsolva, és ki.

Virtuális gépek titkosításához a folyamat a következőképpen történik:

1. Hozzon létre egy titkosítási kulcsot az Azure Key vaultban.
2. A titkosítási kulcs is használható titkosításához lemezek konfigurálása.
3. A titkosítási kulcs olvasni az Azure Key Vault, egy Azure Active Directory egyszerű szolgáltatás létrehozása a megfelelő engedélyekkel.
4. Adja ki a parancsot a virtuális lemezeket, és adja meg az Azure Active Directory szolgáltatás egyszerű és a megfelelő titkosítási kulcs használható titkosításához.
5. Az Azure Active Directory egyszerű szolgáltatás a szükséges titkosítási kulcs Azure Key vault kérelmek.
6. A virtuális lemezek vannak titkosítva, a megadott titkosítási kulcs használatával.

## <a name="encryption-process"></a>Titkosítási folyamat
Lemeztitkosítás támaszkodik a következő további összetevők:

* **Az Azure Key Vault** – az a lemez titkosítási/visszafejtési folyamathoz használt kriptográfiai kulcsok és titkos védelme érdekében.
  * Ha ilyen használhatja egy meglévő Azure Key Vaultban. Nem kell rendelnie egy Key Vaultot a lemezek titkosítása.
  * Külön adminisztratív határokat és a kulcs láthatóságát, létrehozhat egy dedikált Key Vaultot.
* **Az Azure Active Directory** -szükséges titkosítási kulcsokat és a hitelesítés kért műveletek biztonságos cserélt kezeli.
  * Használhat meglévő Azure Active Directory-példány elhelyezésére szolgáló az alkalmazás általában.
  * Az egyszerű szolgáltatás használatával igényelhet és állítanak ki a megfelelő titkosítási kulcsok biztonságos módot biztosít. Nem fejleszt egy tényleges alkalmazás, amely integrálható az Azure Active Directoryban.

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
az group create --name myResourceGroup --location eastus
```

Az Azure Key Vault a kriptográfiai kulcsokat és a kapcsolódó számítási erőforrásokat, például a storage és a virtuális gépre tartalmazó ugyanabban a régióban kell lennie. Hozzon létre egy Azure Key Vault- [az keyvault létrehozása](/cli/azure/keyvault#az-keyvault-create) , és engedélyezze a Key Vault lemeztitkosítás való használatra. Adjon meg egy egyedi Key Vault számára *keyvault_name* módon:

```azurecli-interactive
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Titkosítási kulcsok használatával a szoftver vagy hardver biztonsági modell (HSM) védelmet is tárolhatja. Egy prémium szintű Key Vault HSM használata szükséges. Nincs hozhat létre egy standard szintű tároló Key Vaultot, szoftveres védelemmel ellátott kulcsokkal helyett a Key Vault premium további költségekkel járnak. Hozzon létre egy Key Vault premium, az előző lépésben adja hozzá `--sku Premium` a parancshoz. Az alábbi példában szoftveres védelemmel ellátott kulcsokkal egy standard Key Vault létrehozása óta.

Mindkét védelme esetében az Azure platform kell hozzáférést kérni a titkosítási kulcsok a virtuális gép indításakor a virtuális lemezek visszafejtéséhez. Hozzon létre egy titkosítási kulcsot a Key vaultban a [az keyvault key létrehozása](/cli/azure/keyvault/key#az-keyvault-key-create). A következő példában létrehozunk egy kulcsot *myKey*:

```azurecli-interactive
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-an-azure-active-directory-service-principal"></a>Azure Active Directory-szolgáltatásnév létrehozása
Virtuális lemezek titkosítása vagy visszafejtése, meg kell adnia egy fiókot, amely kezeli a hitelesítési és titkosítási kulcsok a Key Vaultból cseréje. Ezt a fiókot, egy Azure Active Directory-szolgáltatásnevet, lehetővé teszi, hogy az Azure platform kérése a megfelelő titkosítási kulcsok a virtuális gép nevében. Egy alapértelmezett Azure Active Directory-példányt az előfizetésében, érhető el, bár számos szervezet rendelkezik dedikált Azure Active Directory-címtár.

Az Azure Active Directory-szolgáltatásnév létrehozása [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Az alábbi példa az egyszerű szolgáltatás és a jelszót használni a későbbi parancsokban olvassa be az értékeket:

```azurecli-interactive
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

A jelszó csak akkor jelenik meg, ha Ön az egyszerű szolgáltatás létrehozása. Ha szükséges, megtekintheti, és jegyezze fel a jelszót (`echo $sp_password`). A szolgáltatásnevek az listázhatja [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) és a egy egyszerű, az adott szolgáltatás kapcsolatos további információk megtekintéséhez [az ad sp show](/cli/azure/ad/sp#az-ad-sp-show).

Sikeresen titkosítására vagy visszafejtésére virtuális lemezek, engedélyezi az Azure Active Directory szolgáltatás egyszerű, olvassa el a kulcsokat a Key vaultban tárolt titkosítási kulcs engedélyeket kell beállítani. A Key Vault-engedélyek beállítása [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). A következő példában a szolgáltatásnév-Azonosítót az ezt megelőző parancsban megadott:

```azurecli-interactive
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Egy virtuális gépet a [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) és 5 GB-os adatlemezt. Csak bizonyos Piactéri lemezképek támogatja a lemeztitkosítást. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* használatával egy *Ubuntu 16.04 LTS* lemezkép:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

Ssh-KAPCSOLATOT a virtuális gépet a *publicIpAddress* az előző parancs kimenetében található. Hozzon létre egy partíciót és a fájlrendszer, majd az adatlemezt csatlakoztatni. További információkért lásd: [csatlakozhat egy Linux rendszerű virtuális gép csatlakoztatása az új lemez](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Zárja be az SSH-munkamenet.


## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása
A virtuális lemezek titkosítását, hogy egyesítik az előző összetevők:

1. Adja meg az Azure Active Directory egyszerű szolgáltatás és a jelszót.
2. Adja meg a Key Vault, a titkosított lemezek metaadatok tárolására.
3. Adja meg a titkosítási kulcsok használata a tényleges titkosításra és visszafejtésre.
4. Adja meg, hogy szeretné-e az operációsrendszer-lemez, az adatlemezeket, vagy az összes titkosítása.

A virtuális gép titkosítása [az vm encryption engedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable). Az alábbi példában a *$sp_id* és *$sp_password* az előző változók [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancsot:

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

A titkosítási folyamat végrehajtásához némi időt vesz igénybe. A folyamat állapotának monitorozásához [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

A kimenet az alábbihoz csonkolva hasonlít:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress"
]
```

Várjon, amíg az operációs rendszer állapota lemez jelentések **VMRestartPending**, majd indítsa újra a virtuális gép [az virtuális gép újraindítása](/cli/azure/vm#az-vm-restart):

```azurecli-interactive
az vm restart --resource-group myResourceGroup --name myVM
```

A titkosítási folyamat akkor fejeződik be, a rendszerindítási folyamat során, ezért Várjon néhány percet, mielőtt újra a titkosítás állapotának ellenőrzésével [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Az állapota most jelentse az operációsrendszer-lemez és a, adatlemez **titkosított**.


## <a name="add-additional-data-disks"></a>További adatlemezek hozzáadása
Az adatlemezek titkosított, miután később további virtuális lemezek hozzáadása a virtuális gép, és a titkosításhoz is őket. Ha például lehetővé teszi egy második virtuális lemez hozzáadása a virtuális gépre a következő:

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --disk myDataDisk \
    --new \
    --size-gb 5
```

Futtassa újra a parancsot a virtuális lemezek titkosítása a következőképpen:

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```


## <a name="next-steps"></a>További lépések
* Az Azure Key Vault, beleértve a kriptográfiai kulcsokat és a tárolók törlése kezelésével kapcsolatos további információért lásd: [kezelése a Key Vault parancssori felület használatával](../../key-vault/key-vault-manage-with-cli2.md).
* További információ a lemeztitkosítást, például egy titkosított egyéni VM feltöltése az Azure-ba való felkészülés: [az Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
