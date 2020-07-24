---
title: Ügyfél által felügyelt kulcsok konfigurálása a Azure Batch-fiókhoz Azure Key Vault és felügyelt identitással
description: Megtudhatja, hogyan titkosíthatja a Batch-információkat a kulcsok használatával
author: pkshultz
ms.topic: how-to
ms.date: 07/17/2020
ms.author: peshultz
ms.openlocfilehash: 77c0489838685d65d7579f37d6a6cb922af509f9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062531"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Ügyfél által felügyelt kulcsok konfigurálása a Azure Batch-fiókhoz Azure Key Vault és felügyelt identitással

Alapértelmezés szerint a Azure Batch platform által felügyelt kulcsokkal titkosítja az Azure Batch szolgáltatásban tárolt összes ügyféladatokat, például a tanúsítványokat, a feladatok/feladatok metaadatait. Igény szerint használhatja a saját kulcsait, például az ügyfél által felügyelt kulcsokat a Azure Batchban tárolt adattitkosításhoz.

Az Ön által megadott kulcsokat [Azure Key Vault](../key-vault/general/basic-concepts.md)kell létrehoznia, és az ügyfél által felügyelt kulcsokkal konfigurálni kívánt batch-fiókokat engedélyezni kell az [Azure felügyelt identitásával](../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok támogatása a Azure Batch jelenleg nyilvános előzetes verzióban érhető el az USA nyugati középső régiójában, az USA keleti régiójában, az USA déli középső régiójában, az USA US Gov Virginia 2. nyugati régiójában, valamint a US Gov Arizona régióban.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Batch-fiók létrehozása rendszer által hozzárendelt felügyelt identitással

### <a name="azure-portal"></a>Azure Portal

Ha a [Azure Portal](https://portal.azure.com/)batch-fiókokat hoz létre, válassza a **speciális** lapon az Identity (identitás) elemnél **hozzárendelt rendszer** kiválasztása értéket.

![Új batch-fiók rendszerhez rendelt identitás-típussal](./media/batch-customer-managed-key/create-batch-account.png)

A fiók létrehozása után egy egyedi GUID-azonosítót talál a **tulajdonság** szakaszának **identitás egyszerű azonosítója** mezőjében. Megjelenik az **identitás típusa** `SystemAssigned` .

![Egyedi GUID az identitás elsődleges azonosítója mezőben](./media/batch-customer-managed-key/linked-batch-principal.png)
 
### <a name="azure-cli"></a>Azure CLI

Új batch-fiók létrehozásakor meg kell adni `SystemAssigned` a `--identity` paramétert.

```powershell
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

A fiók létrehozása után ellenőrizheti, hogy engedélyezve van-e a rendszerhez rendelt felügyelt identitás ezen a fiókon. Ügyeljen arra, hogy a `PrincipalId` , mivel ez az érték szükséges ahhoz, hogy a Batch-fiók hozzáférhessen a Key Vaulthoz.

```powershell
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> A Batch-fiókban létrehozott, rendszerhez rendelt felügyelt identitás csak az ügyfél által felügyelt kulcsok lekérésére szolgál a Key Vault. Ez az identitás nem érhető el a Batch-készleteken.

## <a name="configure-your-azure-key-vault-instance"></a>Az Azure Key Vault-példány konfigurálása

### <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

Ha Azure Batch ügyfél által felügyelt kulcsokkal rendelkező Azure Key Vault-példányt hoz létre, győződjön meg arról, hogy a helyreállítható **törlési** és **törlési védelem** egyaránt engedélyezve van.

![Key Vault létrehozási képernyő](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Hozzáférési szabályzat hozzáadása az Azure Key Vault-példányhoz

A Key Vault létrehozása után a Azure Portal a **hozzáférési házirendben** a **beállítás**területen adja hozzá a Batch-fiókhoz való hozzáférést a felügyelt identitás használatával. A **kulcs engedélyei**területen válassza a **beolvasás**, **kulcs** és **kicsomagolás**kulcs elemet. 

![Hozzáférési szabályzat hozzáadása](./media/batch-customer-managed-key/key-permissions.png)

A Select ( **egyszerű**) mezőben **válassza** ki a `principalId` korábban beolvasott elemet, vagy a Batch-fiók nevét.

![PrincipalId megadása](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Kulcs létrehozása Azure Key Vault

A Azure Portal lépjen a **kulcs** szakaszban található Key Vault példányra, majd válassza a **Létrehozás/importálás**lehetőséget. Válassza ki a használandó **kulcs típusát** `RSA` és a **kulcs méretét** `2048` .

![Kulcs létrehozása](./media/batch-customer-managed-key/create-key.png)

A kulcs létrehozása után kattintson az újonnan létrehozott kulcsra és a jelenlegi verzióra, másolja a **kulcs azonosítóját** a **Properties (Tulajdonságok** ) szakaszban.  Győződjön meg róla, hogy az **engedélyezett műveletek**alatt a **wrap Key** és a **dewrap Key** is be van jelölve.

## <a name="enable-customer-managed-keys-on-azure-batch-account"></a>Ügyfél által felügyelt kulcsok engedélyezése Azure Batch fiókban

### <a name="azure-portal"></a>Azure Portal

A [Azure Portal](https://portal.azure.com/)nyissa meg a Batch-fiók lapot. A **titkosítás** szakaszban engedélyezze az **ügyfél által felügyelt kulcsot**. Közvetlenül használhatja a kulcs azonosítóját, vagy kiválaszthatja a kulcstárolót, majd kattintson a **kulcstartó és-kulcs kiválasztása**elemre.

![A titkosítás alatt engedélyezze az ügyfél által felügyelt kulcsot](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

Miután létrehozta a Batch-fiókot a rendszerhez rendelt felügyelt identitással, és megadta a hozzáférést a Key Vaulthoz, frissítse a Batch-fiókot a `{Key Identifier}` paraméter alatt található URL-címmel `keyVaultProperties` . A **encryption_key_source** is állítsa be `Microsoft.KeyVault` .

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="update-the-customer-managed-key-version"></a>Az ügyfél által felügyelt kulcs verziójának frissítése

A kulcs új verziójának létrehozásakor frissítse a Batch-fiókot az új verzió használatára. Kövesse az alábbi lépéseket:

1. Nyissa meg Azure Portal a Batch-fiókját, és jelenítse meg a titkosítási beállításokat.
2. Adja meg az új kulcs verziójának URI azonosítóját. Másik lehetőségként kiválaszthatja a Key vaultot és a kulcsot is a verzió frissítéséhez.
3. Mentse a módosításokat.

A verzió frissítéséhez használhatja az Azure CLI-t is.

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```
## <a name="use-a-different-key-for-batch-encryption"></a>Más kulcs használata a Batch encryption szolgáltatáshoz

A Batch-titkosításhoz használt kulcs módosításához kövesse az alábbi lépéseket:

1. Nyissa meg a Batch-fiókját, és jelenítse meg a titkosítási beállításokat.
2. Adja meg az új kulcs URI-JÁT. Másik lehetőségként kiválaszthatja a kulcstartót, és kiválaszthat egy új kulcsot.
3. Mentse a módosításokat.

Az Azure CLI-t másik kulcs használatára is használhatja.

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```
## <a name="frequently-asked-questions"></a>Gyakori kérdések
  * **Támogatottak-e az ügyfél által felügyelt kulcsok a meglévő batch-fiókok esetében?** Nem. Az ügyfél által felügyelt kulcsok csak új batch-fiókok esetén támogatottak.
  * **Milyen műveletek érhetők el az ügyfél által felügyelt kulcs visszavonása után?** Az egyetlen engedélyezett művelet a fiók törlése, ha a Batch elveszti a hozzáférést az ügyfél által felügyelt kulcshoz.
  * **Hogyan kell visszaállítani a Batch-fiókhoz való hozzáférést, ha véletlenül törölem a Key Vault kulcsot?** Mivel a kiürítési védelem és a helyreállítható törlés engedélyezve van, visszaállíthatja a meglévő kulcsokat. További információ: [Azure Key Vault helyreállítása](../key-vault/general/soft-delete-cli.md#recovering-a-key-vault).
  * **Letiltható az ügyfél által felügyelt kulcsok?** A Batch-fiók titkosítási típusát bármikor visszaállíthatja a "Microsoft Managed Key" értékre. Ezt követően ingyenesen törölheti vagy módosíthatja a kulcsot.
  * **Hogyan lehet elforgatni a kulcsokat?** Az ügyfél által felügyelt kulcsok nem lesznek automatikusan elforgatva. A kulcs elforgatásához frissítse a fiókhoz társított kulcs azonosítóját.
  * **Miután visszaállítottam a hozzáférést, mennyi ideig tart a Batch-fiók újbóli működése?** Akár 10 percet is igénybe vehet, amíg a fiók újra elérhető lesz a hozzáférés visszaállítása után.
  * **Amíg a Batch-fiók nem érhető el, mi történik az erőforrásokkal?** Az ügyfél által felügyelt kulcsokhoz való batch-hozzáférés megszakadását futtató készletek továbbra is futnak. A csomópontok azonban elérhetetlenné válnak, és a tevékenységek nem fognak futni (és újra kell őket várólistára állítani). A hozzáférés visszaállítása után a csomópontok újra elérhetővé válnak, és a feladatok újra lesznek indítva.
  * **A titkosítási mechanizmus a Batch-készletben lévő virtuálisgép-lemezekre vonatkozik?** Nem. A Cloud Service-konfigurációs készletek esetében az operációs rendszer és az ideiglenes lemez titkosítása nem lesz alkalmazva. A virtuális gépek konfigurációs készletei esetében az operációs rendszer és a megadott adatlemezek alapértelmezés szerint a Microsoft platform által felügyelt kulccsal lesznek titkosítva. Jelenleg nem adhat meg saját kulcsot ezekhez a lemezekhez. A Microsoft platform által felügyelt kulccsal rendelkező batch-készletekhez tartozó virtuális gépek ideiglenes lemezének titkosításához engedélyeznie kell a [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) tulajdonságot a [virtuálisgép-konfigurációs](/rest/api/batchservice/pool/add#virtualmachineconfiguration) készletben. A fokozottan bizalmas környezetek esetében ajánlott engedélyezni az ideiglenes lemezek titkosítását, és elkerülni az operációs rendszer és az adatlemezek bizalmas adatok tárolására szolgáló adatokat.
  * **A rendszer által hozzárendelt felügyelt identitás a számítási csomópontokon elérhető batch-fiókban?** Nem. Ezt a felügyelt identitást jelenleg csak az ügyfél által felügyelt kulcs Azure Key Vault elérésére használják.
  
