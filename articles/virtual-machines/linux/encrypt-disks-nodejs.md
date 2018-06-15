---
title: A Linux virtuális gép és az Azure CLI 1.0 lemezeket titkosítása |} Microsoft Docs
description: A Linux virtuális gépet az Azure CLI 1.0 és a Resource Manager üzembe helyezési modellel lemezzel titkosítása
services: virtual-machines-linux
documentationcenter: ''
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
ms.date: 03/06/2017
ms.author: iainfou
ms.openlocfilehash: d1704ba37b1d2acc595cb0c354b22bfcf1c57036
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30911682"
---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>A Linux virtuális gépet az Azure CLI 1.0 lemezeket titkosítása
A bővített virtuális gép (VM) biztonsági és megfelelőségi az Azure-ban virtuális lemezek aktívan titkosíthatók. Lemezek titkosítása egy Azure Key Vault a titkosított titkosítási kulcsok használatával. Szabályozhatja a titkosítási kulcsokat, és a használatukat naplózhatók. Ez a cikk részletesen titkosítása a Linux virtuális gépet az Azure CLI 1.0 és a Resource Manager üzembe helyezési modellel virtuális lemezzel.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#quick-commands) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

## <a name="quick-commands"></a>Gyors parancsok
Ha szeretné gyorsan a feladatnak a, a következő szakasz részleteit a következő parancsokat a virtuális Gépen lévő virtuális lemezek titkosításához. Részletes információkat és a környezetben az egyes lépések a dokumentum többi részén található [itt indítása](#overview-of-disk-encryption).

Van szüksége a [Azure CLI legújabb 1.0](../../xplat-cli-install.md) telepítve, és bejelentkezett a Resource Manager módra használatával az alábbiak szerint:

```azurecli
azure config mode arm
```

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők `myResourceGroup`, `myKeyVault`, és `myVM`.

Először engedélyezése az Azure Key Vault-szolgáltató az Azure-előfizetéshez belül, és hozzon létre egy erőforráscsoportot. Az alábbi példa létrehoz egy erőforráscsoport neve `myResourceGroup` a a `WestUS` helye:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Hozzon létre egy Azure-tárolóban. Az alábbi példakód létrehozza a kulcstároló nevű `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Titkosítási kulcs létrehozására a kulcstároló, majd engedélyezze a lemez titkosításához. Az alábbi példakód létrehozza nevű kulcs `myKey`:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Hozzon létre egy Azure Active Directory használatával kezeli a hitelesítési és titkosítási kulcsok a Key Vault cseréjét végpontot. A `--home-page` és `--identifier-uris` tényleges irányítható címnek nem kell. A legmagasabb szintű biztonság jelszavak helyett ügyfélkulcs kell használni. Az Azure parancssori felület jelenleg nem hozható létre ügyfélkulcs. Titkos ügyfélkulccsal csak az Azure portálon hozható létre. Az alábbi példa létrehoz egy Azure Active Directory végpontján nevű `myAADApp` és egy jelszót használja `myPassword`. Adja meg a saját jelszavát a következőképpen:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Megjegyzés: a `applicationId` az előző parancs kimenetében megjelennek. Az alkalmazás azonosítója szerepel a következő lépéseket:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Adatlemez hozzáadása egy meglévő virtuális gépre. A következő példa adatlemezt ad egy nevű virtuális gép `myVM`:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Tekintse át a Key Vault és a létrehozott kulcsot. A Key Vault azonosító URI és kulcs van szüksége az utolsó lépésben URL-CÍMÉT. A következő példa a részletek ellenőrzi, hogy a Key vault nevű `myKeyVault` és nevű kulcs `myKey`:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Titkosítása következő, a lemezek megadása egész saját paraméterek nevei:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Az Azure parancssori felület nem biztosít részletes hibák a titkosítási folyamat során. További hibaelhárítási információért tekintse át `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Az előző parancs sok változók rendelkezik, és amelyekhez nem tarozik sok jelzi, miért a sikertelen, a teljes parancs példa lehet az alábbiak szerint:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Végül tekintse át a titkosítási állapot újra megerősítéséhez, hogy a virtuális lemezek most lett titkosítva. A következő példa egy nevű virtuális gép állapotát ellenőrzi `myVM` a a `myResourceGroup` erőforráscsoport:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Lemeztitkosítás áttekintése
A Linux virtuális gépek virtuális lemezzel titkosított rest az [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Nincs nem kell fizetni az Azure-ban virtuális lemezek titkosítására. Titkosítási kulcsok Azure Key Vault szoftver-védelemmel vannak tárolva, vagy importálhat vagy a tanúsított FIPS 140-2 2. szint szabványok hardveres biztonsági modulokkal (HSM) a kulcsok létrehozásához. A titkosítási kulcsokat a felügyeletet, és naplózhatja a használatukat. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. Egy Azure Active Directory végpontján lehetővé teszi a biztonságos kiadása a titkosítási kulcsokat, a virtuális gépek vannak kapcsolva, és ki.

A virtuális gépek titkosításához a folyamat a következőképpen történik:

1. Hozzon létre egy Azure Key Vault egy titkosítási kulcsot.
2. Állítsa be a titkosítási kulccsal, hogy a lemezek titkosítására használható.
3. Az Azure Key Vault beolvasni a titkosítási kulcsot, hozzon létre egy végpontot, az Azure Active Directoryval a megfelelő engedélyekkel.
4. Adja ki a parancsot, a virtuális lemezek, Azure Active Directory végpontján és egyéb használandó megfelelő titkosítási kulcs titkosításához.
5. Az Azure Active Directory végpontján kér az Azure Key Vault a szükséges titkosítási kulcs.
6. A virtuális lemezek vannak titkosítva, a megadott titkosítási kulcs használatával.

## <a name="supporting-services-and-encryption-process"></a>Szolgáltatások és a titkosítási folyamat támogatása
Adatok titkosítása a következő további összetevők támaszkodik:

* **Az Azure Key Vault** – a lemez titkosítási/visszafejtési folyamathoz használt titkosítási kulcsok és titkos biztosításához használt.
  * Ha van ilyen, használhat egy meglévő Azure Key Vault. Nem jelölt ki a kulcstároló, a lemezek titkosító rendelkeznek.
  * Külön felügyeleti határokat és a kulcs látható, a dedikált kulcstároló is létrehozhat.
* **Az Azure Active Directory** -kezeli a szükséges titkosítási kulcsokat és a kért műveletek hitelesítési biztonságos cseréjét.
  * Az alkalmazás elhelyezésére szolgáló használhat meglévő Azure Active Directory-példány általában.
  * Az alkalmazás több végpont le tudja kérni és a megfelelő titkosítási kulcsok beszerzése kiadott Kulcstárolónak, valamint a virtuális gép számára. Nem fejleszt, amely az Azure Active Directory tényleges kérelmet.

## <a name="requirements-and-limitations"></a>Követelmények és korlátozások
Támogatott esetek és lemez titkosítására vonatkozó követelményekkel kapcsolatos:

* A következő Linux-kiszolgálóra SKU - Ubuntu, CentOS, SUSE és SUSE Linux Enterprise Server (SLES) és Red Hat Enterprise Linux.
* Az azonos Azure-régió, és az előfizetés összes erőforrások (például a Key Vault, a tárfiók és a virtuális gép) kell lennie.
* Standard A, a D, a DS-ben, a G és a GS adatsorozat virtuális gépeket.

Lemeztitkosítás jelenleg nem támogatott a következő esetekben:

* Az alapszintű csomag virtuális gépeket.
* A virtuális gépek létrehozása a klasszikus telepítési modell használatával.
* Az operációs rendszer lemeztitkosítás Linux virtuális gépeken letiltása.
* A titkosítási kulcsok egy már titkosított Linux virtuális gép frissítése.

## <a name="create-the-azure-key-vault-and-keys"></a>Az Azure Key Vault és a kulcsok létrehozása
Ez az útmutató többi végrehajtásához, rendelkeznie kell a [Azure CLI legújabb 1.0](../../xplat-cli-install.md) telepítve, és bejelentkezett a Resource Manager módra használatával az alábbiak szerint:

```azurecli
azure config mode arm
```

Egész a parancspéldákban cserélje le minden példa paraméter a saját nevét, helyét és kulcsértékek. Az alábbi példák a szabályt használ `myResourceGroup`, `myKeyVault`, `myAADApp`stb.

Az első lépés, ha az Azure Key Vault a kriptográfiai kulcsok tárolásához. Az Azure Key Vault kulcsok, a titkos kulcsok és a jelszót, amely engedélyezi, hogy biztonságosan végrehajtja az alkalmazások és szolgáltatások a tárolhatja. A virtuális lemez titkosításához segítségével Key Vault titkosítására vagy visszafejtésére a virtuális lemezek használt kriptográfiai kulcs tárolása.

Engedélyezze az Azure Key Vault-szolgáltató az Azure-előfizetése, majd hozzon létre egy erőforráscsoportot. Az alábbi példa létrehoz egy erőforráscsoportot `myResourceGroup` a a `WestUS` helye:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Az Azure Key Vault a kriptográfiai kulcsokat és tartozó számítási erőforrásokat, például a tároló és a virtuális gépért tartalmazó ugyanabban a régióban kell lennie. Az alábbi példa létrehoz egy Azure Key Vault nevű `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

A szoftver vagy a biztonsági modell (HSM) védelmi kriptográfiai kulcsokat is tárolhatja. A Key Vault prémium HSM használata szükséges. Nincs a prémium szabványos Key Vault szoftveresen védett tároló helyett kulcstároló létrehozásához további költségek. A prémium kulcstároló létrehozásához az előző lépésben hozzáadása `--sku Premium` a parancshoz. Az alábbi példában szoftveresen védett azt a szabványos kulcstároló létrehozása óta.

Mindkét védelmi modellek esetében az Azure platformon kell hozzáférést kérni a titkosítási kulcsokat a virtuális lemezek visszafejtése a virtuális gép indításakor. Hozzon létre egy titkosítási kulcsot a Key Vault belül, majd a virtuális lemez titkosítás használatának engedélyezése. Az alábbi példakód létrehozza nevű kulcs `myKey` és majd lehetővé teszi az adatok titkosítása:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Az Azure Active Directory-alkalmazás létrehozása
Amikor a virtuális lemezek vannak titkosított vagy visszafejtett, a végpont használatával kezeli a hitelesítési és titkosítási kulcsok a Key Vault cseréjét. Ehhez a végponthoz, egy Azure Active Directory-alkalmazás, lehetővé teszi, hogy az Azure platformon, kérje a megfelelő titkosítási kulcsokat a virtuális gép nevében. Azure Active Directory alapértelmezett példányán érhető előfizetését, bár számos szervezet Azure Active Directory-könyvtárak dedikált.

Nem a teljes Azure Active Directory-alkalmazás hozza létre a `--home-page` és `--identifier-uris` az alábbi példában szereplő paraméterek nem kell tényleges irányítható cím lehet. Az alábbi példa is itt adhatja meg, az Azure-portálon belül előállítása kulcsokat helyett jelszóalapú titkos kulcs. Most kulcs létrehozásakor nem hajtható végre az Azure parancssori felületen.

Az Azure Active Directory-alkalmazás létrehozása. Az alábbi példakód létrehozza egy alkalmazás nevű `myAADApp` és egy jelszót használja `myPassword`. Adja meg a saját jelszavát a következőképpen:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Jegyezze fel a `applicationId` , amely küld vissza a kimenet a fenti paranccsal. Az alkalmazás azonosító olyan egyes fennmaradó lépéseit. Ezután hozzon létre egy egyszerű szolgáltatásnév (SPN), így az alkalmazás nem érhető el a környezetben. Sikeresen vagy titkosítására vagy visszafejtésére virtuális lemezek, a Key Vault tárolt titkosítási kulcs engedélyei lehetővé teszik az Azure Active Directory-alkalmazás az a kulcsok beolvasása a értékre kell állítani.

Az egyszerű szolgáltatásnév létrehozásához, és állítsa be a megfelelő engedélyeket az alábbiak szerint:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Adjon hozzá egy virtuális lemezt, és tekintse át a titkosítás állapotát
Ténylegesen titkosíthatja az egyes virtuális lemezek, lehetővé teszi, hogy a lemez hozzáadása egy meglévő virtuális gépre. Egy 5Gb adatlemez hozzáadása egy meglévő virtuális gépre az alábbiak szerint:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

A virtuális lemezek jelenleg nincs titkosítva. Tekintse át a virtuális gép aktuális titkosítási állapotát az alábbiak szerint:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Virtuális lemezek titkosítása
A virtuális lemezek most titkosításához, kapcsolása együtt minden az előző összetevők működését:

1. Adja meg az Azure Active Directory-alkalmazás és a jelszót.
2. Adja meg a Key Vault a titkosított lemezek metaadatait tárolja.
3. Adja meg a titkosítási kulcsok használandó tényleges titkosításához és visszafejtéséhez.
4. Adja meg, hogy az operációsrendszer-lemezképet, az adatlemezek vagy az összes titkosításához.

Lehetővé teszi, hogy tekintse át a részletes adatait az Azure Key Vault és a létrehozott kulccsal, a tároló Kulcsazonosító, URI, és majd kulcs az utolsó lépésben URL-címe:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

A virtuális lemezek használatával kimenete titkosítása a `azure keyvault show` és `azure keyvault key show` parancsok az alábbiak szerint:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

A fenti paranccsal sok változók rendelkezik, az alábbi példa referenciaként a teljes parancs:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Az Azure parancssori felület nem biztosít részletes hibák a titkosítási folyamat során. További hibaelhárítási információért tekintse át `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` titkosít a virtuális gépen.

Végezetül lehetővé teszi, hogy tekintse át a titkosítás állapotát, hogy a virtuális lemezek most már titkosítva megerősítésként:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>További adatok lemezek hozzáadása
Az adatlemezek titkosított, ha később további virtuális lemezek hozzáadása a virtuális Gépet, és is titkosítani. Amikor futtatja a `azure vm enable-disk-encryption` paranccsal, növeli a feladatütemezési verzióját használja a `--sequence-version` paraméter. A feladatütemezési verzió paraméter lehetővé teszi az azonos virtuális gépen végzett műveletek végrehajtását.

Például lehetővé teszi, hogy a második virtuális lemez hozzáadása a virtuális Gépet az alábbiak szerint:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Futtassa újra a parancsot a virtuális lemezek titkosításához a idő hozzáadása a `--sequence-version` paraméter, és az értéket az első alkalommal történő futtatásakor az alábbiak szerint növekvő:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>További lépések
* Az Azure Key Vault, beleértve a titkosítási kulcsok és a tárolók kezelésével kapcsolatos további információért lásd: [kezelése Key Vault parancssori felület használatával](../../key-vault/key-vault-manage-with-cli2.md).
* Lemeztitkosítás, például egy titkosított egyéni virtuális Gépre az Azure-ba, a feltöltendő előkészítése kapcsolatos további információk: [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
