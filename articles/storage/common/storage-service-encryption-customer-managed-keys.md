---
title: Az Azure Storage szolgáltatás titkosítási kulcsok ügyfél által felügyelt Azure Key Vault használatával |} Microsoft Docs
description: Az Azure Blob Storage szolgáltatás oldalán titkosítani, ha az adatok tárolása az Azure Storage szolgáltatás titkosítási szolgáltatás segítségével, és visszafejteni az ügyfél által felügyelt kulcsokkal adatok beolvasása közben.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/07/2018
ms.author: lakasa
ms.openlocfilehash: 04688f943ac9eba27ca193aa2054c69b6a94547d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Storage szolgáltatás titkosítási kulcsok ügyfél által felügyelt Azure Key Vault használatával

Microsoft Azure a elkötelezett az Ön és az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalások megvédeni. Egyik módja az, hogy az Azure Storage védi az adatokat az keresztül Storage szolgáltatás titkosítási (SSE), amely titkosítja a történő írásakor, tárolási és visszafejti az adatok beolvasása közben azt. A titkosítási és visszafejtési automatikus, átlátható, és használja 256 bites [AES titkosítási](https://wikipedia.org/wiki/Advanced_Encryption_Standard), a legerősebb blokk egyik Rejtjelek érhető el.

A Microsoft által felügyelt titkosítási kulcsok használatával SSE, vagy használhatja a saját titkosítási kulccsal. A cikkből megtudhatja, hogyan használhatja a saját titkosítási kulccsal. További információ a Microsoft által felügyelt kulcsokkal, vagy SSE általában: [Storage szolgáltatás titkosítási az inaktív adatok](storage-service-encryption.md).

A Blob és a fájl tárolásához SSE integrálva van az Azure Key Vaulthoz, hogy a titkosítási kulcsok kezeléséhez használhatja a kulcstároló. Létrehozhat saját titkosítási kulccsal, és tárolja őket a kulcstároló, vagy az Azure Key Vault API-k segítségével készítése a titkosítási kulcsokat. Az Azure Key Vault kezelése és szabályozhatja a kulcsok és a kulcs használatának is naplózása.

Miért létrehozása a saját kulcsok? Egyéni kulcsok rugalmasabban, így létrehozása, elforgatása, tiltsa le, és adja meg a hozzáférés-vezérlést. Egyéni kulcsok lehetővé teszik az adatok védelme érdekében használt titkosítási kulcsok naplózása.

## <a name="get-started-with-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsok első lépései

Ügyfél által felügyelt kulcsok használata SSE, hozhat létre egy új kulcstartó és kulcsa, vagy használhat egy meglévő kulcstároló és a kulcs. A tárfiók és a key vault ugyanabban a régióban kell lennie, de különböző előfizetésekhez el. 

### <a name="step-1-create-a-storage-account"></a>1. lépés: Tárfiók létrehozása

Először hozzon létre egy tárfiókot, ha Ön nem rendelkezik ilyennel. További információkért lásd: [hozzon létre egy új tárfiókot](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>2. lépés: SSE engedélyezése a Blob és a fájl tárolásához

Ahhoz, hogy az ügyfél által felügyelt kulcsokkal SSE, két fő szolgáltatásainak, véglegesen törli és nem törölhetők, is engedélyezni kell. Ezek a beállítások győződjön meg arról, a kulcsok nem lehet akár véletlenül vagy szándékosan törölt. A kulcsok maximális megőrzési időszak 90 nap, a felhasználók rosszindulatú szereplője vagy nevű támadások elleni védelme van beállítva.

Ha programozottan az SSE az ügyfél által felügyelt kulcsok engedélyezni szeretné, használhatja a [Azure Storage erőforrás szolgáltató REST API felülete](https://docs.microsoft.com/rest/api/storagerp), a [Storage erőforrás szolgáltató ügyféloldali kódtára a .NET](https://docs.microsoft.com/dotnet/api), [ Az Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), vagy a [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Ügyfél által felügyelt kulcsok használata SSE, a tárolási fiók identitását kell rendelnie a tárfiók. Az identitás állította futtassa a következő PowerShell-parancsot:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

Véglegesen törli, és tegye kiürítése is engedélyezheti a következő PowerShell-parancsok végrehajtásával:

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>3. lépés: A titkosítás engedélyezése az ügyfél által felügyelt kulcsok

Alapértelmezés szerint az SSE Microsoft által felügyelt kulcsokat használ. Az ügyfél által felügyelt használható billentyűparancsok a tárolási fiók SSE engedélyezheti a [Azure-portálon](https://portal.azure.com/). Az a **beállítások** a storage-fiók paneljén kattintson **titkosítási**. Válassza ki a **a saját kulcs használata** beállítás, az alábbi ábrán látható módon.

![A titkosítási beállítással portál ábrázoló képernyőfelvétel](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>4. lépés: Válassza ki a kulcs

Megadhatja a kulcs URI, vagy a kulcs kijelölésével kulcstároló.

#### <a name="specify-a-key-as-a-uri"></a>Adja meg a kulcsot egy URI-t

Adja meg a kulcsot az URI, kövesse az alábbi lépéseket:

1. Válassza ki a **Enter billentyű URI** lehetőséget.  
2. Az a **kulcs URI** mezőben adja meg az URI azonosító.

    ![Portál képernyőfelvétel: a titkosítási kulcs uri-beállítás megadása](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)


#### <a name="specify-a-key-from-a-key-vault"></a>Adja meg a kulcstároló kulcsot 

Adja meg a kulcstároló-kulcsot, kövesse az alábbi lépéseket:

1. Válassza ki a **válassza ki a Key Vault** lehetőséget.  
2. Válassza ki a használni kívánt kulcsot tartalmazó kulcstároló.
3. A key vault lehetőségek közül választhat a kulcsot.

    ![Portál ábrázoló képernyőfelvétel titkosítás használata a saját kulcs lehetőséggel](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Ha a tárfiók nincs hozzáférése a kulcstároló, futtathatja az Azure PowerShell-paranccsal hozzáférést a következő ábrán látható.

![A hozzáférés megtagadva a kulcstartót portál képernyőfelvétel](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Navigáljon az Azure Key Vault az Azure portálon, és a tárfiók való hozzáférés biztosítása szerint is engedélyezheti a hozzáférést az Azure-portálon.


A fenti kulcs társíthatja egy meglévő tárfiókot használ, a következő PowerShell-parancsok használatával:
```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -EnableEncryptionService "Blob" -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```


### <a name="step-5-copy-data-to-storage-account"></a>5. lépés: Adatok másolása storage-fiók

Adatok átviteléhez az új tárolási figyelembe, hogy titkosítva van. További információ: [gyakran ismételt kérdések a Storage szolgáltatás titkosítási](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>6. lépés: A titkosított adatok állapotának lekérdezése

A titkosított adatok állapotának lekérdezése.

## <a name="faq-for-sse-with-customer-managed-keys"></a>Az ügyfél által felügyelt kulccsal rendelkező SSE – gyakori kérdések

**K: használom a prémium szintű storage; Használhatok ügyfél által felügyelt kulcsok SSE?**

A: a Microsoft által felügyelt és az ügyfél által felügyelt kulccsal rendelkező SSE Igen, Standard szintű tárolást és a prémium szintű storage esetén támogatott.

**K: hozható létre új tárfiókok SSE az ügyfél által felügyelt kulcsokkal engedélyezve van az Azure PowerShell és az Azure parancssori felület használatával?**

V: Igen.

**K: hogyan sokkal költségeket a Azure Storage a SSE az ügyfél által felügyelt kulcsok használata esetén?**

V: nincs az Azure Key Vault használatával költsége. További részletekért látogasson el a [Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/). Nincs SSE, amelyen engedélyezve van az összes tárfiók további költség nélkül.

**K: visszavonja a titkosítási kulcsokat a hozzáférést?**

A: visszavonhatja a hozzáférést Igen, tetszőleges időpontban. Többféleképpen is lehet visszavonni a hozzáférést a kulcsokat. Tekintse meg [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) és [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) további részleteket. Visszavonni a hozzáférési gyakorlatilag letiltja való hozzáférést a tárfiókban lévő összes BLOB, a fiók titkosítási kulcs Azure Storage nem érhetők el.

**K: hozható létre a tárfiók és a kulcs másik régióban?**

V: nem, a tárfiók és az Azure Key Vault és a kulcs kell ugyanabban a régióban kell.

**K: engedélyezhető az ügyfél által felügyelt kulcsok SSE a storage-fiók létrehozása közben?**

V: nem. A tárfiók létrehozásakor az SSE csak a Microsoft által felügyelt kulcsok érhetők el. Ügyfél által felügyelt kulcsok használatához frissítenie kell a tárfiók tulajdonságai. Programozott frissítése a tárfiók REST- vagy a storage ügyfélkódtáraival egyikét használja, vagy frissíteni a tárfiók tulajdonságai-fiók létrehozása után az Azure portál használatával.

**K: titkosítási kulcsok ügyfél által felügyelt használatakor az SSE letiltása?**

A: nem titkosítás nem tiltható le. Az összes services – Blob, fájl, Table és Queue storage alapértelmezés szerint engedélyezve van a titkosítás. Igény szerint válthat az ügyfél által felügyelt kulcsok használata a Microsoft által felügyelt kulcsokkal, és ez fordítva is igaz.

**K: SSE alapértelmezés szerint engedélyezve van, egy új tárfiók létrehozásakor?**

V: SSE összes storage-fiókok és az összes services – Blob, fájl, Table és Queue storage alapértelmezés szerint engedélyezve van.

**K: nem engedélyezhető a felhasználó által felügyelt kulcsok használata a storage-fiókom SSE.**

V: van, az Azure Resource Manager-tárfiókot? Klasszikus tárfiókokba ügyfél által felügyelt kulcsok használata nem támogatott. Ügyfél által felügyelt kulccsal rendelkező SSE csak erőforrás-kezelő tárfiókok engedélyezhető.

**K: Mi az az enyhe törölje, majd tegye kiürítése? Engedélyezi ezt a beállítást, az ügyfél által felügyelt kulcsok SSE használandó kell?**

V: világos törlése, és tegye nem kiürítése engedélyezni kell az ügyfél által felügyelt kulcsok SSE használandó. Ezek a beállítások győződjön meg arról, hogy a kulcs nem véletlenül vagy szándékosan törölt. A kulcsok maximális megőrzési időszak 90 nap, a felhasználók rosszindulatú szereplője és nevű támadások elleni védelme van beállítva. Ez a beállítás nem lehet letiltani.

**K: az SSE csak meghatározott régióiba engedélyezett felhasználó által felügyelt kulcsokkal?**

V: SSE ügyfél által felügyelt kulccsal rendelkező Blob és a fájl tárolási minden területen érhető el.

**K: hogyan do I kapcsolatfelvételre Ha I problémák merülnek fel, vagy visszajelzést szeretne biztosítani?**

V: kapcsolattartási [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) a Storage szolgáltatás titkosítási okozó problémákat.

## <a name="next-steps"></a>További lépések

-   További információk az átfogó készletét biztonsági funkciókat nyújtanak, amelyek segítségével a fejlesztők olyan biztonságos alkalmazásokat hozhat létre, lásd: a [tárolási biztonsági útmutatója](storage-security-guide.md).

-   Áttekintés az Azure Key Vault kapcsolatos információkért lásd: [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?

-   Ismerkedés az Azure Key Vault, lásd: [Ismerkedés az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
