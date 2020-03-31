---
title: A PowerShell használatával hozzon létre egy felhasználói delegálási SAS-t egy tárolóhoz vagy blobhoz
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre felhasználói delegálássAS-t az Azure Active Directory hitelesítő adataival a PowerShell használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5250a27e6c5fcf012207f1edb95ad46c0aabfe63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536173"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Felhasználói delegálássas létrehozása egy tárolóhoz vagy blobhoz a PowerShell segítségével

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan azure Active Directory (Azure AD) hitelesítő adatok segítségével hozzon létre egy felhasználói delegáláss SAS egy tároló vagy blob az Azure PowerShell használatával.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

Felhasználói delegálási SAS-t a PowerShellsegítségével való létrehozásához telepítse az Az.Storage modul 1.10.0-s vagy újabb verzióját. A modul legújabb verziójának telepítéséhez kövesse az alábbi lépéseket:

1. Távolítsa el az Azure PowerShell korábbi telepítéseit:

    - Távolítsa el az Azure PowerShell korábbi telepítéseit a Windowsból az **Alkalmazások & szolgáltatások** **beállításokkal**a Beállítások területen.
    - Az összes **Azure-modul** eltávolítása a ból. `%Program Files%\WindowsPowerShell\Modules`

1. Győződjön meg arról, hogy a PowerShellTelepített legújabb verziójával rendelkezik. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsot a legújabb verzió telepítéséhez:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Zárja be, majd nyissa meg újra a PowerShell-ablakot a PowerShellGet telepítése után.

1. Telepítse az Azure PowerShell legújabb verzióját:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Győződjön meg arról, hogy telepítette az Azure PowerShell 3.2.0-s vagy újabb verzióját. Futtassa a következő parancsot az Azure Storage PowerShell-modul legújabb verziójának telepítéséhez:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Zárja be, majd nyissa meg újra a PowerShell-ablakot.

Az Az.Storage modul telepített verziójának ellenőrzéséhez futtassa a következő parancsot:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Az Azure PowerShell telepítéséről az [Azure PowerShell telepítése a PowerShellget segítségével](/powershell/azure/install-az-ps)című témakörben talál további információt.

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Bejelentkezés az Azure PowerShellbe az Azure AD-vel

Hívja meg a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsot az Azure AD-fiókkal való bejelentkezéshez:

```powershell
Connect-AzAccount
```

A PowerShellben való bejelentkezésről a [Bejelentkezés az Azure PowerShellben](/powershell/azure/authenticate-azureps)című témakörben talál további információt.

## <a name="assign-permissions-with-rbac"></a>Engedélyek hozzárendelése Az RBAC-kal

Ha létre szeretne hozni egy felhasználói delegálásssas t az Azure PowerShellből, a PowerShellbe való bejelentkezéshez használt Azure AD-fiókhoz hozzá kell rendelni egy szerepkört, amely tartalmazza a **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. Ez az engedély lehetővé teszi, hogy az Azure AD-fiók kérje a *felhasználó delegálási kulcsát.* A felhasználói delegálási kulcs a felhasználói delegálássas sas aláírására szolgál. A **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet biztosító szerepkört a tárfiók, az erőforráscsoport vagy az előfizetés szintjén kell hozzárendelni. A felhasználói delegálási SAS létrehozására vonatkozó RBAC-engedélyekről a [Felhasználói delegálási SAS létrehozása](/rest/api/storageservices/create-user-delegation-sas)című **szakaszEngedélyek hozzárendelése RBAC-mal** című szakaszában talál további információt.

Ha nem rendelkezik megfelelő engedélyekkel az RBAC-szerepkörök hozzárendeléséhez egy Azure AD rendszerbiztonsági taghoz, előfordulhat, hogy meg kell kérnie a fiók tulajdonosát vagy a rendszergazdát, hogy rendelje hozzá a szükséges engedélyeket.

A következő példa a **Storage Blob Data Contributor** szerepkört rendeli hozzá, amely tartalmazza a **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. A szerepkör hatóköre a tárfiók szintjén.

Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

A **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet tartalmazó beépített szerepkörökről további információt az [Azure-erőforrások beépített szerepkörei](../../role-based-access-control/built-in-roles.md)című témakörben talál.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>A SAS biztonságossá tétele Azure AD-hitelesítő adatokkal

Amikor létrehoz egy felhasználói delegálássas SAS-t az Azure PowerShell használatával, implicit módon létrejön a SAS aláírásához használt felhasználói delegálási kulcs. A SAS-hez megadott kezdési és lejárati idő a felhasználó delegálási kulcsának kezdési és lejárati idejeként is használatos. 

Mivel a felhasználói delegálási kulcs érvényességi ideje a kezdő dátumtól számított 7 nap, meg kell adnia a SAS lejárati idejét, amely a kezdési időponttól számított 7 napon belül van. A SAS érvénytelen a felhasználói delegálási kulcs lejárta után, így a 7 napnál hosszabb lejárati idővel rendelkező SAS továbbra is csak 7 napig lesz érvényes.

Ha létre szeretne hozni egy felhasználói delegáláss SAS-t egy tárolóhoz vagy `-UseConnectedAccount` blobhoz az Azure PowerShell használatával, először hozzon létre egy új Azure Storage-környezetobjektumot, amely megadja a paramétert. A `-UseConnectedAccount` paraméter azt adja meg, hogy a parancs létrehozza a környezetobjektumot az Azure AD-fiók, amellyel bejelentkezett.

Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Felhasználói delegálássas létrehozása tárolóhoz

Ha egy tárolóhoz egy felhasználói delegálási SAS-jogkivonatot szeretne visszaadni, hívja meg a [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) parancsot, amely a korábban létrehozott Azure Storage-környezetobjektumban halad át.

A következő példa egy felhasználói delegálási SAS-jogkivonatot ad vissza egy tárolóhoz. Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

A visszaadott felhasználói delegálási SAS-token a következőhöz hasonló lesz:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Felhasználói delegálássAS létrehozása blobhoz

Ha egy blob hoz létre egy felhasználói delegálási SAS-jogkivonatot, hívja meg a [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) parancsot, amely a korábban létrehozott Azure Storage-környezetobjektumban halad át.

A következő szintaxis egy blob felhasználói delegálási SAS-t ad vissza. A példa adja `-FullUri` meg a paramétert, amely visszaadja a blob URI a SAS-jogkivonat hozzáfűzve. Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

A visszaadott felhasználói delegálási SAS URI a következőhöz hasonló lesz:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> A felhasználói delegálási SAS nem támogatja az engedélyek definiálását tárolt hozzáférési házirenddel.

## <a name="revoke-a-user-delegation-sas"></a>Felhasználói delegálási SAS visszavonása

Ha vissza szeretne vonni egy felhasználói delegálási SAS-t az Azure PowerShellből, hívja meg a **Revoke-AzStorageAccountUserDelegationKeys** parancsot. Ez a parancs visszavonja a megadott tárfiókhoz társított összes felhasználói delegálási kulcsot. A kulcsokhoz társított megosztott hozzáférési aláírások érvénytelenné válnak.

Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Mind a felhasználói delegálási kulcs, mind az RBAC szerepkör-hozzárendelések gyorsítótárazása az Azure Storage gyorsítótárazza, így előfordulhat, hogy a visszavonási folyamat kezdeményezése és egy meglévő felhasználói delegálási SAS érvénytelenné válik között.

## <a name="next-steps"></a>További lépések

- [Felhasználói delegálássas (REST API) létrehozása](/rest/api/storageservices/create-user-delegation-sas)
- [Felhasználói delegálási kulcs művelet beszereznie](/rest/api/storageservices/get-user-delegation-key)
