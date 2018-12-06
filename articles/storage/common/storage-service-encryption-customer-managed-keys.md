---
title: Az Azure Storage Service Encryption ügyfél által kezelt kulcsok használata az Azure Key Vault |} A Microsoft Docs
description: Titkosítani az Azure Blob storage és az Azure Files szolgáltatás oldalán, az adatok tárolásakor a az Azure Storage Service Encryption szolgáltatással, és visszafejtésre ügyfél által kezelt kulcsok használata az adatok lekérésekor.
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 10/11/2018
ms.author: lakasa
ms.component: common
ms.openlocfilehash: 5ef9c15d4edf62ef63b16765f16971a9be5ca58b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970705"
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Felhasználó által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption

A Microsoft Azure számára fontos, hogy segítséget nyújt a szervezeti biztonsági és megfelelőségi követelmények kielégítése érdekében az adatok biztonságos megőrzésében. Egyik módja, hogy az Azure storage-platform védi az adatokat a keresztül Storage Service Encryption (SSE), amely titkosítja az adatokat, amikor a tárfiókba történő írása, és visszafejti az adatokat, lekérésekor. A titkosítási és visszafejtési automatikus, átlátható, és használja 256 bites [AES-titkosítás](https://wikipedia.org/wiki/Advanced_Encryption_Standard), egyik legerősebb Rejtjelek érhető el.

SSE technológiával is használhatja a Microsoft által felügyelt titkosítási kulcsokat, vagy használhatja a saját titkosítási kulcsokat. Ez a cikk ismerteti, hogyan használhatja a saját titkosítási kulcsokat. További információ a Microsoft által kezelt kulcsok használata, vagy kapcsolatban SSE általános: [inaktív adatok a Storage Service Encryption](storage-service-encryption.md).

Az SSE az Azure Blob storage és az Azure Files integrálva van az Azure Key Vault, hogy a key vault segítségével kezelje a titkosítási kulcsokat. Létrehozhat saját titkosítási kulcsok és a key vaultban tárolja őket, vagy használhatja az Azure Key Vault API-k készítése a titkosítási kulcsokat. Az Azure Key Vault kezelheti és vezérelheti a kulcsokat és a kulcshasználat is naplózása.

> [!Note]  
> Nem áll rendelkezésre a felhasználó által kezelt kulcsok használata a Storage Service Encryption [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). [Az Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md) iparági szabványt használó [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) a Windows és [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) egy titkosítási megoldást linuxon KeyVault integrálva.

Miért érdemes létrehozni a saját kulcsok? Egyéni kulcsok nagyobb rugalmasságot nyújtanak, így létrehozása, elforgatása, tiltsa le, és adja meg a hozzáférés-vezérlést. Egyéni kulcsok lehetővé teszi, hogy a titkosítási kulcsokat az adatok védelmére szolgáló naplózása.

## <a name="get-started-with-customer-managed-keys"></a>Ismerkedés a felhasználó által kezelt kulcsok

Felhasználó által kezelt kulcsok használata SSE, létrehozhat egy új kulcstartóba, és a kulcs, vagy használhatja egy meglévő kulcstároló és egy kulcsot. A storage-fiók és a key vault ugyanabban a régióban kell lennie, de különböző előfizetésekhez is lehetnek.

### <a name="step-1-create-a-storage-account"></a>1. lépés: Tárfiók létrehozása

Először hozzon létre egy storage-fiókot, ha még nincs ilyen. További információ: [Tárfiók létrehozása](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>2. lépés: A Blobok és fájlok tárolási engedélyezéséről

Ahhoz, hogy az ügyfél által felügyelt kulcsokkal SSE, két Kulcsvédelmi szolgáltatás: a helyreállítható törlés és nem kiürítése, is engedélyezni kell az Azure Key Vaultban. Ezek a beállítások biztosítása érdekében a kulcsok nem lehet véletlenül vagy szándékosan törölve. A kulcsok a megőrzési időszak 90 nap, a felhasználók rosszindulatú vagy zsarolóprogram-támadások elleni védelem van beállítva.

Ha programozott módon engedélyezzük a felhasználó által kezelt kulcsokkal SSE szeretné, használhatja a [Azure Storage erőforrás-szolgáltató REST API](https://docs.microsoft.com/rest/api/storagerp), a [Storage Resource Provider ügyféloldali kódtára a .NET](https://docs.microsoft.com/dotnet/api), [ Az Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), vagy a [az Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Felhasználó által kezelt kulcsok használata SSE, hozzá kell rendelnie egy storage-fiók identitás a storage-fiókba. Az identitást a következő PowerShell vagy az Azure CLI-parancs végrehajtásával állíthatja be:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

```azurecli-interactive
az storage account \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --assign-identity
```

Az alábbi PowerShell vagy az Azure CLI-parancsok végrehajtásával engedélyezheti a helyreállítható törlés és hajtsa végre végleges törlése:

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

```azurecli-interactive
az resource update \
    --id $(az keyvault show --name <vault_name> -o tsv | awk '{print $1}') \
    --set properties.enableSoftDelete=true

az resource update \
    --id $(az keyvault show --name <vault_name> -o tsv | awk '{print $1}') \
    --set properties.enablePurgeProtection=true
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>3. lépés: Engedélyezze a titkosítást a felhasználó által kezelt kulcsok

Alapértelmezés szerint az SSE használja a Microsoft által felügyelt kulcsokkal. Az SSE engedélyezheti az ügyfél által felügyelt kulcsokat a tárfiók tárfiókkulcsait a [az Azure portal](https://portal.azure.com/). Az a **beállítások** a storage-fiókok panelen kattintson a **titkosítási**. Válassza ki a **a saját kulcs használata** beállítás, az alábbi ábrán látható módon.

![Portál képernyőfelvétel: a titkosítási beállítás](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>4. lépés: Válassza ki a kulcsot

Megadhatja a kulcs URI-t, vagy válassza ki a kulcsot egy kulcstartót.

#### <a name="specify-a-key-as-a-uri"></a>Adja meg a kulcs URI-t

Adja meg a kulcsot egy URI-ból, kövesse az alábbi lépéseket:

1. Válassza ki a **Enter kulcshoz tartozó URI** lehetőséget.
2. Az a **kulcs URI-JÁT** mezőben adja meg az URI-t.

   ![Portál képernyőképe a titkosítási kulcs uri-beállítást adja meg](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

#### <a name="specify-a-key-from-a-key-vault"></a>Adja meg a key vault-kulcs

Az a key vault-kulcs megadásához kövesse az alábbi lépéseket:

1. Válassza ki a **válassza ki a Key Vaultból** lehetőséget.
2. Válassza ki a használni kívánt kulcsot tartalmazó kulcstartó.
3. Válassza ki a kulcsot a kulcstartóban található.

   ![Portál ábrázoló képernyőfelvétel titkosítás használata a saját kulcs lehetőséget](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Ha a tárfiók nincs hozzáférése a key vaulthoz, futtathatja az Azure PowerShell-paranccsal hozzáférést a következő képen látható.

![Portál képernyőképe a hozzáférés megtagadva a key vault számára](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Az Azure Key Vault az Azure Portalon és a tárfiókhoz való hozzáférést is biztosíthat hozzáférést az Azure Portalon keresztül.

A fenti kulcsot is társíthat egy meglévő tárfiókot, a következő PowerShell-parancsokkal:

```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```

### <a name="step-5-copy-data-to-storage-account"></a>5. lépés: Adatok másolása a storage-fiók

Adatok átviteléhez az új storage-fiókba, hogy titkosítva van. További információ: [gyakran ismételt kérdések a Storage Service Encryption](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>6. lépés: A titkosított adatok állapotának lekérdezése

A titkosított adatok állapotának lekérdezése.

## <a name="faq-for-sse-with-customer-managed-keys"></a>Az SSE ügyfél által felügyelt kulcsokkal kapcsolatos gyakori kérdések

**A Premium storage; használok használható a felhasználó által kezelt kulcsokkal SSE technológiával?**  
Igen, a Microsoft által felügyelt és az ügyfél által felügyelt kulcsokkal SSE Standard storage és a Premium storage is támogatott.

**Létrehozhatók új storage-fiókok SSE technológiával engedélyezve van az Azure PowerShell-lel és az Azure CLI használatával az ügyfél által felügyelt kulcsokkal?**  
Igen.

**Több IP-címek fenntartási az Azure Storage szolgáltatás felhasználó által kezelt kulcsok használata SSE technológiával?**  
Nincs az Azure Key Vault használata ingyenes. További részletekért látogasson el [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/). Nincs SSE, amely engedélyezve van az összes storage-fiókok számára további költség nélkül.

**Az Azure Managed Disks elérhető-e a Storage Service Encryption szolgáltatással?**  
A Storage Service Encryption az Azure Managed Disks elérhető a Microsoft által felügyelt kulcsokkal, de nem az ügyfél által kezelt kulcsok. Ügyfél által felügyelt kulcsokkal SSE támogató Managed Disks, helyett javasoljuk [az Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), iparági szabványt használó [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) a Windows és [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)biztosít Linux rendszeren titkosítási KeyVault integrálva.

**Miben különbözik a Storage Service Encryption az Azure Disk Encryption?**  
Az Azure Disk Encryption biztosít operációsrendszer-alapú megoldások, például a BitLocker és a DM-Crypt és az Azure KeyVault közötti integrációt. A Storage Service Encryption natív módon a rétegben az Azure storage platform, a virtuális gép alábbi titkosítását biztosítja.

**Visszavonhatja a hozzáférést a titkosítási kulcsokat?**
Igen, visszavonhatja a hozzáférést bármikor. Többféleképpen is lehet vonni a hozzáférést a kulcsokhoz. Tekintse meg [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) és [Azure Key Vault parancssori felület](https://docs.microsoft.com/cli/azure/keyvault) további részletekért. Hozzáférés visszavonása hatékonyan letilthatja a hozzáférést a storage-fiókban lévő összes BLOB, mivel a titkosítási kulcsát nem érhető el az Azure Storage által.

**Létrehozhatok egy tárfiók és a kulcs eltérő régióban?**  
Nem, a storage-fiók és az Azure Key Vault és a kulcsot kell ugyanabban a régióban.

**Lehet engedélyezni a felhasználó által kezelt kulcsokkal a SSE a storage-fiók létrehozása során?**  
Nem. A storage-fiók létrehozásakor a SSE csak a Microsoft által felügyelt kulcsokkal érhetők el. Használja az ügyfél által felügyelt kulcsokat, frissítenie kell a storage-fiók tulajdonságait. REST vagy valamelyik, a tároló ügyfélkódtárai használatával programozott módon frissítheti a storage-fiókjába, vagy az Azure portal használatával a fiók létrehozását követően a tárfiók tulajdonságainak frissítése.

**Letilthatja a során a felhasználó által kezelt kulcsok használata az SSE titkosítást?**  
Nem, titkosítás nem tiltható le. Az Azure Blob storage, Azure Files, az Azure Queue és az Azure Table storage alapértelmezés szerint engedélyezve van a titkosítás. Igény szerint válthat a felhasználó által kezelt kulcsok használata a Microsoft által kezelt kulcsok használata, és ez fordítva is igaz.

**Az SSE engedélyezve van, ha létrehozok egy új tárfiókot?**  
Az SSE engedélyezve van az összes storage-fiókok és az Azure Blob storage, Azure Files, az Azure Queue storage és Azure Table storage.

**Felhasználó által kezelt kulcsok használata a storage-fiókom SSE nem engedélyezhető.**  
Egy Azure Resource Manager-tárfiókot is ez? Az ügyfél által felügyelt kulcsokat a klasszikus tárfiókok nem támogatottak. Ügyfél által felügyelt kulcsokkal SSE csak a Resource Manager-tárfiókokra is engedélyezhető.

**Mi a helyreállítható törlés és hajtsa végre végleges törlés? Van szükségem ahhoz, hogy ezzel a beállítással SSE használata az ügyfél által felügyelt kulcsokat?**  
Helyreállítható törlés, és tegye nem végleges törlés engedélyezni kell a felhasználó által kezelt kulcsok használata a SSE. Ezek a beállítások győződjön meg arról, hogy a kulcs nem véletlenül vagy szándékosan törölve. A kulcsok a megőrzési időszak 90 nap, a felhasználók rosszindulatú és zsarolóprogram-támadások elleni védelem van beállítva. Ez a beállítás nem lehet letiltani.

**Az SSE ügyfél által felügyelt kulcsokkal csak akkor engedélyezett, az adott régióban?**  
Ügyfél által felügyelt kulcsokkal SSE minden régióban az Azure Blob storage és az Azure Fileshoz érhető el.

**Hogyan vehetem fel a kapcsolatot valaki Ha szeretnék problémája van, vagy visszajelzést szeretne biztosítani?**  
Kapcsolattartó [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) Storage Service Encryption szolgáltatással kapcsolatos problémákkal.

## <a name="next-steps"></a>További lépések

- További információ a biztonsági széles körű, amelyekkel a fejlesztők biztonságos alkalmazásokat hozhat létre, tekintse meg a [Storage biztonsági útmutatóját](storage-security-guide.md).
- Áttekintés az Azure Key Vault kapcsolatos információkért lásd: [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
- Első lépések az Azure Key Vaultban, lásd: [Ismerkedés az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
