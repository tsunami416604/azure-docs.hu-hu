---
title: "Az Azure Linux virtuális gép lemezeinek titkosítása |} Microsoft Docs"
description: "A fokozott biztonság, az Azure CLI 2.0 használatával Linux virtuális gép virtuális lemezein titkosítása"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.openlocfilehash: 2489d4bfda5d9a08b35e8d80b6cc9d00bf69117b
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-encrypt-virtual-disks-on-a-linux-vm"></a>Virtuális lemezek, a Linux virtuális gép titkosítása
A bővített virtuális gép (VM) biztonsági és megfelelőségi a virtuális lemezek, a virtuális gépért titkosíthatók. Virtuális gépek titkosítása egy Azure Key Vault a titkosított titkosítási kulcsok használatával. Szabályozhatja a titkosítási kulcsokat, és a használatukat naplózhatók. Ez a cikk részletesen titkosítása a Linux virtuális gépet az Azure CLI 2.0 virtuális lemezzel. Az [Azure CLI 1.0-s](encrypt-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) verziójával is elvégezheti ezeket a lépéseket.

## <a name="quick-commands"></a>Gyors parancsok
Ha szeretné gyorsan a feladatnak a, a következő szakasz részleteit a következő parancsokat a virtuális Gépen lévő virtuális lemezek titkosításához. Részletes információkat és a környezetben az egyes lépések a dokumentum többi részén található [itt indítása](#overview-of-disk-encryption).

A legújabb kell [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/#login). A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők *myResourceGroup*, *SajátKulcs*, és *myVM*.

Először, engedélyezése az Azure Key Vault-szolgáltató az Azure-előfizetése belül [az szolgáltató regisztrálása](/cli/azure/provider#register) és hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#create). Az alábbi példa létrehoz egy erőforráscsoport neve *myResourceGroup* a a *eastus* helye:

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy Azure Key Vault a [az keyvault létrehozása](/cli/azure/keyvault#create) , és engedélyezze a Key Vault lemeztitkosítás való használatra. Adjon meg egy egyedi kulcstároló nevet *keyvault_name* az alábbiak szerint:

```azurecli
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

A kulcstároló, a titkosítási kulcs létrehozására [az keyvault kulcs létrehozása](/cli/azure/keyvault/key#create). Az alábbi példakód létrehozza nevű kulcs *SajátKulcs*:

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```

Az Azure Active Directoryval az egyszerű szolgáltatás létrehozása [az ad sp létrehozása-az-rbac](/cli/azure/ad/sp#create-for-rbac). A szolgáltatás egyszerű hitelesítési és titkosítási kulcsok a Key Vault exchange kezeli. A következő példa a résztvevő-azonosító és a jelszót később parancsok használható olvassa be az értékeket:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

A jelszó csak kimeneti, az egyszerű szolgáltatás létrehozásakor. Szükség esetén megtekintheti, és jegyezze fel a jelszót (`echo $sp_password`). A szolgáltatás elsődleges listázhatja [az sp hirdetéslista](/cli/azure/ad/sp#list) és egyszerű, az adott szolgáltatás további információkat tekinthet [az ad sp megjelenítése](/cli/azure/ad/sp#show).

A kulcstároló, az engedélyeket [az keyvault-szabály beállítása](/cli/azure/keyvault#set-policy). A következő példában a résztvevő-azonosító az előző parancs van megadva:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
    --key-permissions wrapKey \
    --secret-permissions set
```

A virtuális gép létrehozása [az virtuális gép létrehozása](/cli/azure/vm#create) és 5 Gb adatlemezt csatolni. Csak bizonyos piactéren elérhető rendszerkép lemeztitkosítás támogatja. Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* használatával egy *CentOS 7.2n* lemezképet:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH-kapcsolatot a virtuális gép használata a *publicIpAddress* látható a fenti parancs kimenetét. Hozzon létre egy partíciót és filesystem, majd csatlakoztassa az adatok lemezt. További információkért lásd: [csatlakozás egy Linux virtuális Gépet az új lemez csatlakoztatásához](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Zárja be az SSH-munkamenetet.

Az a virtuális gép titkosítása [az vm-titkosítás engedélyezése](/cli/azure/vm/encryption#enable). Az alábbi példában a *$sp_id* és *$sp_password* változókat az előző `ad sp create-for-rbac` parancs:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

A titkosítási folyamat befejezéséhez sok időt vesz igénybe. A folyamat állapotának figyelése [az vm titkosítási megjelenítése](/cli/azure/vm/encryption#show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Az állapot látható **EncryptionInProgress**. Várjon, amíg az operációs rendszer állapota lemez jelentések **VMRestartPending**, majd indítsa újra a virtuális Gépet a [az virtuális gép újraindítása](/cli/azure/vm#restart):

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

A titkosítási folyamat akkor fejeződik be, a rendszerindítási folyamat során, így Várjon néhány percet, majd újra titkosítási állapotának ellenőrzésekor [az vm titkosítási megjelenítése](/cli/azure/vm/encryption#show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Az állapot most jelentse az operációsrendszer-lemez és az adatok lemezre **titkosított**.


## <a name="overview-of-disk-encryption"></a>Lemeztitkosítás áttekintése
A Linux virtuális gépek virtuális lemezzel titkosított rest az [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Nincs nem kell fizetni az Azure-ban virtuális lemezek titkosítására. Titkosítási kulcsok Azure Key Vault szoftver-védelemmel vannak tárolva, vagy importálhat vagy a tanúsított FIPS 140-2 2. szint szabványok hardveres biztonsági modulokkal (HSM) a kulcsok létrehozásához. A titkosítási kulcsokat a felügyeletet, és naplózhatja a használatukat. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. Egy egyszerű Azure Active Directory szolgáltatás lehetővé teszi a biztonságos kiadása a titkosítási kulcsokat, a virtuális gépek vannak kapcsolva, és ki.

A virtuális gépek titkosításához a folyamat a következőképpen történik:

1. Hozzon létre egy Azure Key Vault egy titkosítási kulcsot.
2. Állítsa be a titkosítási kulccsal, hogy a lemezek titkosítására használható.
3. Az Azure Key Vault beolvasni a titkosítási kulcsot, hozzon létre egy Azure Active Directory szolgáltatás egyszerű a megfelelő engedélyekkel.
4. Adja ki a parancsot, a virtuális lemezek, adja meg az Azure Active Directory szolgáltatás egyszerű és megfelelő titkosítási használandó kulcs titkosításához.
5. Az Azure Active Directory szolgáltatás egyszerű kér az Azure Key Vault a szükséges titkosítási kulcs.
6. A virtuális lemezek vannak titkosítva, a megadott titkosítási kulcs használatával.

## <a name="encryption-process"></a>Titkosítási folyamat
Adatok titkosítása a következő további összetevők támaszkodik:

* **Az Azure Key Vault** – a lemez titkosítási/visszafejtési folyamathoz használt titkosítási kulcsok és titkos biztosításához használt.
  * Ha van ilyen, használhat egy meglévő Azure Key Vault. Nem jelölt ki a kulcstároló, a lemezek titkosító rendelkeznek.
  * Külön felügyeleti határokat és a kulcs látható, a dedikált kulcstároló is létrehozhat.
* **Az Azure Active Directory** -kezeli a szükséges titkosítási kulcsokat és a kért műveletek hitelesítési biztonságos cseréjét.
  * Az alkalmazás elhelyezésére szolgáló használhat meglévő Azure Active Directory-példány általában.
  * Az egyszerű szolgáltatás lehetővé teszi a biztonságos le tudja kérni, és adja ki a megfelelő titkosítási kulcsokat. Nem fejleszt, amely az Azure Active Directory tényleges kérelmet.

## <a name="requirements-and-limitations"></a>Követelmények és korlátozások
Támogatott esetek és lemez titkosítására vonatkozó követelményekkel kapcsolatos:

* A következő Linux-kiszolgálóra SKU - Ubuntu, CentOS, SUSE és SUSE Linux Enterprise Server (SLES) és Red Hat Enterprise Linux.
* Az azonos Azure-régió, és az előfizetés összes erőforrások (például a Key Vault, a tárfiók és a virtuális gép) kell lennie.
* Standard A, a D, a DS-ben, a G, a GS, adatsorozat VMs, stb.
* A titkosítási kulcsok egy már titkosított Linux virtuális gép frissítése.

Lemeztitkosítás jelenleg nem támogatott a következő esetekben:

* Az alapszintű csomag virtuális gépeket.
* A virtuális gépek létrehozása a klasszikus telepítési modell használatával.
* Az operációs rendszer lemeztitkosítás Linux virtuális gépeken letiltása.
* Egyéni Linux-lemezképek használatát.

További információ a támogatott forgatókönyvekkel és korlátozásokkal: [Azure Disk Encryption IaaS virtuális gépekhez](../../security/azure-security-disk-encryption.md)


## <a name="create-azure-key-vault-and-keys"></a>Az Azure Key Vault és kulcsok létrehozása
A legújabb kell [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/#login). A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők *myResourceGroup*, *SajátKulcs*, és *myVM*.

Az első lépés, ha az Azure Key Vault a kriptográfiai kulcsok tárolásához. Az Azure Key Vault kulcsok, a titkos kulcsok és a jelszót, amely engedélyezi, hogy biztonságosan végrehajtja az alkalmazások és szolgáltatások a tárolhatja. A virtuális lemez titkosításához segítségével Key Vault titkosítására vagy visszafejtésére a virtuális lemezek használt kriptográfiai kulcs tárolása.

Engedélyezze az Azure Key Vault-szolgáltató az Azure-előfizetése belül [az szolgáltató regisztrálása](/cli/azure/provider#register) és hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#create). Az alábbi példa létrehoz egy erőforráscsoport neve *myResourceGroup* a a `eastus` helye:

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Az Azure Key Vault a kriptográfiai kulcsokat és tartozó számítási erőforrásokat, például a tároló és a virtuális gépért tartalmazó ugyanabban a régióban kell lennie. Hozzon létre egy Azure Key Vault a [az keyvault létrehozása](/cli/azure/keyvault#create) , és engedélyezze a Key Vault lemeztitkosítás való használatra. Adjon meg egy egyedi kulcstároló nevet *keyvault_name* az alábbiak szerint:

```azurecli
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

A szoftver vagy a biztonsági modell (HSM) védelmi kriptográfiai kulcsokat is tárolhatja. A Key Vault prémium HSM használata szükséges. Nincs a prémium szabványos Key Vault szoftveresen védett tároló helyett kulcstároló létrehozásához további költségek. A prémium kulcstároló létrehozásához az előző lépésben hozzáadása `--sku Premium` a parancshoz. Az alábbi példa szoftveres védelemmel ellátott kulcsokat használ, a standard kulcstároló létrehozása óta.

Mindkét védelmi modellek esetében az Azure platformon kell hozzáférést kérni a titkosítási kulcsokat a virtuális lemezek visszafejtése a virtuális gép indításakor. A kulcstároló, a titkosítási kulcs létrehozására [az keyvault kulcs létrehozása](/cli/azure/keyvault/key#create). Az alábbi példakód létrehozza nevű kulcs *SajátKulcs*:

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-the-azure-active-directory-service-principal"></a>Az Azure Active Directory szolgáltatás egyszerű létrehozása
Ha a virtuális lemezek vannak titkosított vagy visszafejtett, meg kell adnia egy fiókot a hitelesítési és titkosítási kulcsok a Key Vault cseréjét. Ezt a fiókot, egy Azure Active Directory szolgáltatás egyszerű, lehetővé teszi, hogy az Azure platformon, kérje a megfelelő titkosítási kulcsokat a virtuális gép nevében. Azure Active Directory alapértelmezett példányán érhető előfizetését, bár számos szervezet Azure Active Directory-könyvtárak dedikált.

Az Azure Active Directoryval az egyszerű szolgáltatás létrehozása [az ad sp létrehozása-az-rbac](/cli/azure/ad/sp#create-for-rbac). Az alábbi példa beolvassa az értékek a szolgáltatás egyszerű azonosító és jelszó használható újabb parancsokat:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

A jelszó csak akkor jelenik meg, ha a szolgáltatás egyszerű létrehozása. Szükség esetén megtekintheti, és jegyezze fel a jelszót (`echo $sp_password`). A szolgáltatás elsődleges listázhatja [az sp hirdetéslista](/cli/azure/ad/sp#list) és egyszerű, az adott szolgáltatás további információkat tekinthet [az ad sp megjelenítése](/cli/azure/ad/sp#show).

Sikeresen vagy titkosítására vagy visszafejtésére virtuális lemezek, a Key Vault tárolt titkosítási kulcs engedélyei lehetővé teszik az Azure Active Directory szolgáltatás egyszerű az a kulcsok beolvasása a értékre kell állítani. A kulcstároló, az engedélyeket [az keyvault-szabály beállítása](/cli/azure/keyvault#set-policy). A következő példában a résztvevő-azonosító az előző parancs van megadva:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-virtual-machine"></a>Virtuális gép létrehozása
Hozzon létre egy virtuális Gépet titkosítást a [az virtuális gép létrehozása](/cli/azure/vm#create) és 5 Gb adatlemezt csatolni. Csak bizonyos piactéren elérhető rendszerkép lemeztitkosítás támogatja. Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* használatával egy *CentOS 7.2n* lemezképet:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH-kapcsolatot a virtuális gép használata a *publicIpAddress* látható a fenti parancs kimenetét. Hozzon létre egy partíciót és filesystem, majd csatlakoztassa az adatok lemezt. További információkért lásd: [csatlakozás egy Linux virtuális Gépet az új lemez csatlakoztatásához](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Zárja be az SSH-munkamenetet.


## <a name="encrypt-virtual-machine"></a>Virtuális gép titkosítása
A virtuális lemez titkosításához, kapcsolása együtt minden az előző összetevők működését:

1. Adja meg az Azure Active Directory szolgáltatás egyszerű és a jelszót.
2. Adja meg a Key Vault a titkosított lemezek metaadatait tárolja.
3. Adja meg a titkosítási kulcsok használandó tényleges titkosításához és visszafejtéséhez.
4. Adja meg, hogy az operációsrendszer-lemezképet, az adatlemezek vagy az összes titkosításához.

Az a virtuális gép titkosítása [az vm-titkosítás engedélyezése](/cli/azure/vm/encryption#enable). Az alábbi példában a *$sp_id* és *$sp_password* változókat az előző [az ad sp létrehozása-az-rbac](/cli/azure/ad/sp#create-for-rbac) parancs:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

A titkosítási folyamat befejezéséhez sok időt vesz igénybe. A folyamat állapotának figyelése [az vm titkosítási megjelenítése](/cli/azure/vm/encryption#show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

A kimenete a következő csonkolt példához hasonló:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress",
]
```

Várjon, amíg az operációs rendszer állapota lemez jelentések **VMRestartPending**, majd indítsa újra a virtuális Gépet a [az virtuális gép újraindítása](/cli/azure/vm#restart):

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

A titkosítási folyamat akkor fejeződik be, a rendszerindítási folyamat során, így Várjon néhány percet, majd újra titkosítási állapotának ellenőrzésekor **az vm titkosítási megjelenítése**:

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Az állapot most jelentse az operációsrendszer-lemez és az adatok lemezre **titkosított**.


## <a name="add-additional-data-disks"></a>További adatok lemezek hozzáadása
Az adatlemezek titkosított, ha később további virtuális lemezek hozzáadása a virtuális Gépet, és is titkosítani. Például lehetővé teszi, hogy a második virtuális lemez hozzáadása a virtuális Gépet az alábbiak szerint:

```azurecli
az vm disk attach-new --resource-group myResourceGroup --vm-name myVM --size-in-gb 5
```

Futtassa újra a parancsot a virtuális lemezek titkosítani az alábbiak szerint:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```


## <a name="next-steps"></a>Következő lépések
* Az Azure Key Vault, beleértve a titkosítási kulcsok és a tárolók kezelésével kapcsolatos további információért lásd: [kezelése Key Vault parancssori felület használatával](../../key-vault/key-vault-manage-with-cli2.md).
* Lemeztitkosítás, például egy titkosított egyéni virtuális Gépre az Azure-ba, a feltöltendő előkészítése kapcsolatos további információk: [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
