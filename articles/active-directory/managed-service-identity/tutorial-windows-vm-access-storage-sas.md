---
title: Egy Windows virtuális gép alapértelmezett felügyelt identitás használata egy SAS-hitelesítő adatok használatával Azure Storage eléréséhez
description: Oktatóanyag, amely bemutatja, hogyan használja a rendszer által hozzárendelt Windows virtuális gép felügyelt identitás el az Azure Storage helyett a tárfiók hozzáférési kulcsát egy SAS-hitelesítő adatok használatával.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 7120e7356816fd5308aa7189f90176143742e342
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43340714"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Oktatóanyag: Azure Storage elérése SAS-hitelesítő adatot keresztül egy Windows virtuális gép alapértelmezett felügyelt identitás használata

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag bemutatja, hogyan használható egy rendszer által hozzárendelt identitással Windows virtuális gép (VM) egy tároló közös hozzáférésű Jogosultságkód (SAS) hitelesítő adatok beszerzése. Kifejezetten [szolgáltatási SAS-hitelesítő adatok](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures) lekérésére. 

A szolgáltatás SAS lehetővé teszi a korlátozott hozzáférést a storage-fiókban, korlátozott ideig objektumok és a egy adott szolgáltatás (ebben az esetben a blob szolgáltatás), anélkül, hogy a fiók hozzáférési kulcsára. A SAS-hitelesítő adatokat a szokásos módon használhatja a tárolási műveletek során, például a Storage SDK használata esetén. Ebben az oktatóanyagban bemutatjuk, hogyan feltöltése és letöltése a blobok Azure Storage PowerShell-lel. Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * Hozzáférés engedélyezése virtuális gép számára a tárfiók a Resource Managerben lévő SAS-adataihoz 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd a SAS-adatok lekérése a Resource Managerből annak használatával 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Jelentkezzen be az Azure Portalon](https://portal.azure.com)

- [Windows virtuális gép létrehozása](/azure/virtual-machines/windows/quick-create-portal)

- [A virtuális gépen, rendszer által hozzárendelt identitás engedélyezése](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)


## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

Ha még nem rendelkezik tárfiókkal, most létrehoz egyet. Is ezt a lépést kihagyhatja, és hozzáférést biztosítani a virtuális gép felügyelt identitás alapértelmezett meglévő tárfiók SAS-hitelesítő adatot. 

1. Kattintson az Azure Portal bal felső sarkában található **+/Új szolgáltatás létrehozása** gombra.
2. Kattintson a **Tárolás**, majd a **Tárfiók** elemre, amit követően megjelenik egy új „Tárfiók létrehozása” panel.
3. Nevezze el a tárfiókot, amelyet később fog használni.  
4. Az **Üzemi modell** mezőben a „Resource Manager”, a **Fióktípus** mezőben az „Általános célú” értéket kell megadni. 
5. Ellenőrizze, hogy az **Előfizetés** és az **Erőforráscsoport** mező értéke egyezik-e az előző lépésben a virtuális gép létrehozása során megadottakkal.
6. Kattintson a **Create** (Létrehozás) gombra.

    ![Új tárfiók létrehozása](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Blobtároló létrehozása a tárfiókban

Később feltöltünk egy fájlt az új tárfiókba, majd letöltjük abból. Mivel a fájlok tárolásához blobtároló szükséges, létre kell hoznunk egyet, amelyben tárolhatjuk a fájlt.

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. Kattintson a **Tárolók** hivatkozásra bal oldalon található „Blob szolgáltatás” területen.
3. Kattintson a **+ Tároló** gombra a lap tetején, amit követően becsúszik az „Új tároló” panel.
4. Nevezze el a tárolót, válasszon ki egy hozzáférési szintet, majd kattintson az **OK** gombra. A megadott névre az oktatóanyag későbbi részében lesz majd szükség. 

    ![Storage-tároló létrehozása](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>A virtuális gép felügyelt identitás alapértelmezett hozzáférést egy SAS tárolók használatához 

Az Azure Storage nem támogatja natív módon az Azure AD-hitelesítést.  Azonban a tároló SAS lekérése a Resource Manager egy felügyelt identitás használatával, majd az SAS tároló eléréséhez.  Ebben a lépésben a virtuális gép felügyelt identitás alapértelmezett hozzáférést adhat a tárfiók SAS.   

1. Lépjen vissza az újonnan létrehozott tárfiókra.   
2. Kattintson a **Hozzáférés-vezérlés (IAM)** hivatkozásra a bal oldali panelen.  
3. A lap tetején a **+ Hozzáadás** gombra kattintva adjon hozzá egy új szerepkör-hozzárendelést a VM-hez.
4. A lap jobb oldalán a **Szerepkör** értékeként adja meg a „Tárfiók-közreműködő” értéket.  
5. A következő legördülő menüben a **Hozzáférés hozzárendelése** beállítás számára válassza ki a „Virtuális gép” értéket.  
6. Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az **Előfizetés** legördülő menüben, majd állítsa az **Erőforráscsoport** értékét a „Minden erőforráscsoport” értékre.  
7. Végül a **Kiválasztás** mezőben válassza ki a Windows rendszerű virtuális gépet a legördülő menüben, majd kattintson a **Mentés** gombra. 

    ![Helyettesítő képszöveg](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Hozzáférési jogkivonat lekérése a VM identitásával, majd az Azure Resource Manager meghívása a használatával 

Az oktatóanyag további részében a korábban létrehozott virtuális gépről dolgozunk.

Ebben a részben az Azure Resource Manager PowerShell-parancsmagokat kell használnia.  Ha nincs telepítve, a folytatás előtt [töltse le a legújabb verziót](https://docs.microsoft.com/powershell/azure/overview).

1. Az Azure Portalon lépjen a **Virtuális gépek** lapra, keresse meg a Windows rendszerű virtuális gépet, majd kattintson az **Áttekintés** lap tetején található **Csatlakozás** gombra.
2. A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a Windows VM létrehozásakor adott meg. 
3. Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a PowerShellt a távoli munkamenetben. 
4. Powershell az Invoke-WebRequest használja, indítson egy Azure-erőforrások végpontot a hozzáférési jogkivonat beszerzése az Azure Resource Manager a helyi felügyelt identitást.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > A „resource” paraméter értékének pontosan egyeznie kell az Azure AD által várt értékkel. Az Azure Resource Manager erőforrás-azonosítójának használatakor a záró perjelet is szerepeltetni kell az URI-ban.
    
    Ezután nyerje ki a „Content” elemet, amelyet egy JavaScript Object Notation (JSON) formátumú sztringként tárol a $response objektum. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ezután nyerje ki a hozzáférési jogkivonatot a válaszból.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>SAS-hitelesítő adatok lekérése az Azure Resource Managerből tárolóhívások indításához 

Most már a PowerShell használatával hívja meg az előző szakaszban azt lekérése hozzáférési token használatával-tároló SAS-hitelesítő adat létrehozása a Resource Manager. Ha már rendelkezünk a SAS-hitelesítő adatot, a tárolási műveletek is nevezzük.

Ehhez a kéréshez az alábbi HTTP-kérésparamétereket használja majd a SAS-hitelesítő adatok létrehozásához:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Ezek a paraméterek a SAS-hitelesítő adatokra vonatkozó kérés POST-törzsében találhatók. A SAS-hitelesítő adatok létrehozására vonatkozó paraméterekkel kapcsolatos további információkat [a szolgáltatási SAS REST referenciában](/rest/api/storagerp/storageaccounts/listservicesas) találja.

Először a paraméter átalakítása JSON, majd hívja a storage `listServiceSas` hitelesítőadat-végpontot, hogy a SAS létrehozása:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> Az URL megkülönbözteti a kis- és nagybetűket, ezért ellenőrizze, hogy pontosan ugyanúgy írja be a kis- és nagybetűket, mint az erőforráscsoport elnevezésekor, beleértve a „resourceGroups” kifejezés nagy „G” betűjét is. 

Most már tudjuk kibonthatja a SAS-hitelesítő adatot a válaszból:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Ha, vizsgálja meg az SAS-adatok látni fogja a következőhöz hasonlóan:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Ezután létrehozunk egy „test.txt” nevű fájlt. -Hitelesítésre használható a SAS-hitelesítő adatot a `New-AzureStorageContent` parancsmagot, a fájl feltöltése a blob-tárolóba, majd töltse le a fájlt.

```bash
echo "This is a test text file." > test.txt
```

Először az Azure Storage-parancsmagokat telepítse az `Install-Module Azure.Storage` használatával. Ezután töltse fel a most létrehozott blobot a `Set-AzureStorageBlobContent` PowerShell-parancsmaggal:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Válasz:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Le is töltheti a most feltöltött blobot a `Get-AzureStorageBlobContent` PowerShell-parancsmaggal:

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Válasz:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan Windows virtuális gép alapértelmezett felügyelt identitás használata az Azure Storage-ban egy SAS-hitelesítő adatot eléréséhez.  További információ az Azure Storage SAS-hitelesítéséről:

> [!div class="nextstepaction"]
>[Közös hozzáférésű jogosultságkódok (SAS) használata](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)


