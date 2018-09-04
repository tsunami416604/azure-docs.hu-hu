---
title: Az Azure Storage elérése Windows VM-beli felügyeltszolgáltatás-identitással
description: Az oktatóanyag azt ismerteti, hogyan lehet hozzáférni az Azure Storage-hoz egy Windows VM-beli felügyeltszolgáltatás-identitással.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 12e17977d010b460681f72e62fb72e07ad713a3a
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887524"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Oktatóanyag: Windows VM-beli felügyeltszolgáltatás-identitás használata az Azure Storage eléréséhez hozzáférési kulccsal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan kérheti le a tárfiókok hozzáférési kulcsait egy windowsos virtuális gép (VM) a rendszer által hozzárendelt identitásának használatával. A tárelérési kulcsokat a szokásos módon használhatja a tárolási műveletek során, például a Storage SDK használata esetén. Ebben az oktatóanyagban blobokat töltünk fel és le az Azure Storage PowerShell használatával. Az alábbiakat fogja elsajátítani:


> [!div class="checklist"]
> * Tárfiók létrehozása
> * Hozzáférés biztosítása a VM számára a tárfiók Resource Managerben található hozzáférési kulcsaihoz 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd a tárelérési kulcsok lekérése a Resource Managerből a használatával 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Bejelentkezés az Azure Portalra](https://portal.azure.com)

- [Windows rendszerű virtuális gép létrehozása](/azure/virtual-machines/windows/quick-create-portal)

- [Rendszer által hozzárendelt identitás engedélyezése a virtuális gépen](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

Ha még nem rendelkezik tárfiókkal, most létrehoz egyet. Ki is hagyhatja ezt a lépést, és a VM-beli felügyeltszolgáltatás-identitás számára biztosíthat hozzáférést egy meglévő tárfiók kulcsaihoz. 

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

## <a name="grant-your-vms-managed-service-identity-access-to-use-storage-account-access-keys"></a>Hozzáférés biztosítása a VM felügyeltszolgáltatás-identitása számára a tárfiók hozzáférési kulcsainak használatához 

Az Azure Storage nem támogatja natív módon az Azure AD-hitelesítést.  A felügyeltszolgáltatás-identitás használatával azonban lekérheti a tárfiók hozzáférési kulcsait a Resource Managerből, majd a kulcsokkal elérheti a tárolót.  Ebben a lépésben hozzáférést biztosít a VM felügyeltszolgáltatás-identitása számára a tárfiók kulcsaihoz.   

1. Lépjen vissza az újonnan létrehozott tárfiókra.  
2. Kattintson a **Hozzáférés-vezérlés (IAM)** hivatkozásra a bal oldali panelen.  
3. A lap tetején a **+ Hozzáadás** gombra kattintva adjon hozzá egy új szerepkör-hozzárendelést a VM-hez.
4. A lap jobb oldalán a **Szerepkör** értékeként adja meg a „Storage Account Key Operator Service Role” (Tárfiókkulcs-kezelési szolgáltatás szerepkör) értéket. 
5. A következő legördülő menüben a **Hozzáférés hozzárendelése** beállítás számára válassza ki a „Virtuális gép” értéket.  
6. Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az **Előfizetés** legördülő menüben, majd állítsa az **Erőforráscsoport** értékét a „Minden erőforráscsoport” értékre.  
7. Végül a **Kiválasztás** mezőben válassza ki a Windows rendszerű virtuális gépet a legördülő menüben, majd kattintson a **Mentés** gombra. 

    ![Helyettesítő képszöveg](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Hozzáférési jogkivonat lekérése a VM identitásával, majd az Azure Resource Manager meghívása a használatával 

Az oktatóanyag további részében a korábban létrehozott virtuális gépről dolgozunk. 

Ebben a részben az Azure Resource Manager PowerShell-parancsmagokat kell használnia.  Ha nincs telepítve, a folytatás előtt [töltse le a legújabb verziót](https://docs.microsoft.com/powershell/azure/overview).

1. Az Azure Portalon lépjen a **Virtuális gépek** lapra, keresse meg a Windows rendszerű virtuális gépet, majd kattintson az **Áttekintés** lap tetején található **Csatlakozás** gombra. 
2. A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a Windows VM létrehozásakor adott meg. 
3. Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a PowerShellt a távoli munkamenetben.
4. A Powershell Invoke-WebRequest parancsával küldjön kérést a helyi felügyeltszolgáltatás-identitási végpontra, hogy lekérjen egy hozzáférési jogkivonatot az Azure Resource Managerhez.

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
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>A tárfiók hozzáférési kulcsainak a lekérése az Azure Resource Managerből tárolóhívások indításához  

Most a PowerShell használatával hívjuk meg a Resource Managert az előző szakaszban lekért hozzáférési jogkivonattal a tárelérési kulcs lekéréséhez. Amint megkaptuk a tárelérési kulcsot, meghívhatjuk a tárfeltöltési/-letöltési műveleteket.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> Az URL megkülönbözteti a kis- és nagybetűket, ezért ellenőrizze, hogy pontosan ugyanúgy írja be a kis- és nagybetűket, mint az erőforráscsoport elnevezésekor, beleértve a „resourceGroups” kifejezés nagy „G” betűjét is. 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Ezután létrehozunk egy „test.txt” nevű fájlt. Utána a tárelérési kulccsal végezzük a hitelesítést a `New-AzureStorageContent` parancsmagban, feltöltjük a fájlt a blobtárolóba, majd letöltjük a fájlt.

```bash
echo "This is a test text file." > test.txt
```

Először az Azure Storage-parancsmagokat telepítse az `Install-Module Azure.Storage` használatával. Ezután töltse fel a most létrehozott blobot a `Set-AzureStorageBlobContent` PowerShell-parancsmaggal:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Válasz:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
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
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>További lépések

Az oktatóanyagban elsajátította egy felügyeltszolgáltatás-identitás létrehozását, amellyel elérheti az Azure Storage-ot hozzáférési kulcs használatával.  További információ az Azure Storage tárelérési kulcsairól:

> [!div class="nextstepaction"]
>[A tárelérési kulcsok kezelése](/azure/storage/common/storage-create-storage-account#manage-your-storage-access-keys)

