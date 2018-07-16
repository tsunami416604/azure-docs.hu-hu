---
title: Windows virtuális gép MSI használata az Azure Storage eléréséhez
description: Ez az oktatóanyag végigvezeti egy Windows virtuális gépek Felügyeltszolgáltatás-identitás (MSI) Azure Storage eléréséhez használt folyamatán.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81b508661ac7195f690739fe7961691ddbedc9b0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049360"
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Azure Storage elérése hozzáférési kulcs használatával egy Windows virtuális gépek Felügyeltszolgáltatás-identitás segítségével

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ez az oktatóanyag bemutatja, hogyan lehet engedélyezni a Felügyeltszolgáltatás-identitás (MSI) egy Windows virtuális gép esetében, majd az identitásukat a tárfiók hozzáférési kulcsainak lekéréséhez. Használhatja a tárelérési kulcsok megszokott módon ha ezzel a tárolási műveletek, például amikor a Storage SDK használatával. Ebben az oktatóanyagban azt le- és feltöltése a blobok Azure Storage PowerShell-lel. Megtudhatja, hogyan lehet:


> [!div class="checklist"]
> * Windows virtuális gépen az MSI engedélyezéséhez 
> * A virtuális gép hozzáférést biztosít a tárfiók hozzáférési kulcsainak a Resource Managerben 
> * A virtuális gép identitással hozzáférési jogkivonatot kapjon, és a tárelérési kulcsok lekérése a Resource Manager használatával 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Windows virtuális gép egy új erőforráscsoport létrehozása

Ebben az oktatóanyagban létrehozunk egy új Windows virtuális Gépet. A meglévő virtuális gép MSI is engedélyezheti.

1.  Kattintson a **+/ hozzon létre új szolgáltatást** az Azure portal bal felső sarkában található gomb.
2.  Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3.  Adja meg a virtuális gép adatait. A **felhasználónév** és **jelszó** létrehozott itt van a hitelesítő adatok használatával jelentkezzen be a virtuális gépet.
4.  Válassza ki a megfelelő **előfizetés** a virtuális gép a legördülő listában.
5.  Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza ki a virtuális gép **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6.  Válassza ki a méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen az MSI engedélyezéséhez

A virtuális gép MSI lehetővé teszi hozzáférési tokenek beszerzése az Azure ad-ből anélkül, hogy hitelesítő adatok üzembe a kódot kellene. Valójában MSI engedélyezéséhez két dolgot eredményez: a virtuális Gépen az MSI-Virtuálisgép-bővítmény telepítése és a virtuális gép MSI lehetővé teszi.  

1. Keresse meg az erőforráscsoport, az új virtuális gépet, és válassza ki a virtuális gépet az előző lépésben létrehozott.
2. Kattintson a virtuális gép a bal oldalon a "beállítások" **konfigurációs**.
3. Regisztráljon, és az MSI engedélyezéséhez, válassza ki a **Igen**, ha szeretné letiltani, válassza a nem.
4. Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha ellenőrizni szeretné milyen kiterjesztésű vannak-e a virtuális gépre, kattintson a **bővítmények**. Ha az MSI engedélyezve van, a **ManagedIdentityExtensionforWindows** megjelenik a listában.

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

Ha még nem rendelkezik egy, most már létrehozhat egy storage-fiókot. Kihagyhatja ezt a lépést is, és a virtuális gép MSI hozzáférést a kulcsokhoz, egy meglévő tárfiókot. 

1. Kattintson a **+/ hozzon létre új szolgáltatást** az Azure portal bal felső sarkában található gomb.
2. Kattintson a **tárolási**, majd **Tárfiók**, és a egy új "Tárfiók létrehozása" panelen jelenik meg.
3. Adjon meg egy nevet a tárfiók, amely később fogja használni.  
4. **Üzemi modell** és **fióktípus** értékre kell állítani "Erőforrás-kezelő" és "Általános célú", illetve. 
5. Győződjön meg, hogy a **előfizetés** és **erőforráscsoport** egyeznek azokkal az előző lépésben a virtuális gép létrehozásakor megadott.
6. Kattintson a **Create** (Létrehozás) gombra.

    ![Új tárfiók létrehozása](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Hozzon létre egy blobtárolót a tárfiókban

Hogy később feltöltése és fájl letöltése az új tárfiókot. Mivel a fájlok van szüksége a blob storage-ba, hozzon létre egy blobtárolót a fájl tárolására kell.

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. Kattintson a **tárolók** hivatkozásra a bal oldalon, a "Blob szolgáltatás."
3. Kattintson a **+ tároló** tetején az oldalon, és a egy "új tároló" panel becsúszik.
4. Adja meg a tároló nevét, válasszon egy hozzáférési szintet, majd kattintson a **OK**. A megadott név lesz az oktatóanyag későbbi részében. 

    ![Storage-tároló létrehozása](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Hozzáférést biztosít a virtuális gép MSI használata a tárfiók hozzáférési kulcsainak 

Az Azure Storage nem támogatja natív módon az Azure AD-hitelesítés.  Azonban az MSI használata az tárfiók hozzáférési kulcsainak lekérése a Resource Manager, majd egy kulcsot az tároló eléréséhez.  Ebben a lépésben hozzáférést biztosít a virtuális gép MSI a kulcsok a tárfiókhoz.   

1. Lépjen vissza az újonnan létrehozott tárfiókra.  
2. Kattintson a **hozzáférés-vezérlés (IAM)** hivatkozásra a bal oldali panelen.  
3. Kattintson a **+ Hozzáadás** az oldalra egy új szerepkör-hozzárendelés hozzáadása a virtuális gép felett
4. Állítsa be **szerepkör** a "Storage fiók kulcs operátora – szolgáltatási szerepkör", az oldal jobb oldalán. 
5. Állítsa be a következő legördülő **rendelhet hozzáféréseket** az erőforrás "Virtuális gép".  
6. Következő lépésként győződjön meg arról, a megfelelő előfizetés szerepel-e **előfizetés** legördülő menüből, majd állítsa be **erőforráscsoport** "Minden erőforráscsoportok".  
7. Végül **kiválasztása** a legördülő listában válassza ki a Windows virtuális gép, majd kattintson a **mentése**. 

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>A virtuális gép identitással hozzáférési jogkivonatot kapjon, és hívja az Azure Resource Manager használatával 

Az oktatóanyag további részében hogy működik a korábban létrehozott virtuális gépről. 

Ebben a részében az Azure Resource Manager PowerShell-parancsmagokat használni kell.  Ha még nincs telepítve, [a legújabb verzió letöltéséhez](https://docs.microsoft.com/powershell/azure/overview) a folytatás előtt.

1. Az Azure Portalon lépjen **virtuális gépek**, nyissa meg a Windows virtuális gépre, majd a a **áttekintése** lapon kattintson **Connect** tetején. 
2. Adja meg a **felhasználónév** és **jelszó** számára, amelyhez hozzáadta a Windows virtuális gép létrehozásakor. 
3. Most, hogy létrehozott egy **távoli asztali kapcsolat** a virtuális géphez, nyissa meg a Powershellt a távoli munkamenet.
4. A helyi MSI-végpontot a hozzáférési jogkivonat beszerzése az Azure Resource Manager Powershell Invoke-WebRequest használja, végezze el egy kérelmet.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Az "erőforrás" paraméter értékét az Azure AD által várt pontos egyezésűnek kell lennie. Az Azure Resource Manager erőforrás-azonosító használata esetén meg kell adnia az URI a záró perjellel.
    
    Ezután bontsa ki a "Tartalom" elemet, amely egy JavaScript Object Notation (JSON) formátumú karakterláncot a $response objektumban van tárolva. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ezután bontsa ki a hozzáférési jogkivonatot a válaszból.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Tárfiók hozzáférési kulcsainak lekérése az Azure Resource Manager-storage-hívásokhoz  

Most már a PowerShell használatával hívja meg az előző szakaszban azt lekérése hozzáférési token használatával a Resource Manager a tárelérési kulcs lekéréséhez. Amint rendelkezésünkre áll, a tárelérési kulcs, a storage feltöltési/letöltési műveletek is nevezzük.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> Az URL-címe: kis-és nagybetűket, ezért győződjön meg arról, az erőforráscsoport, többek között a "resourceGroups." nagy "G" nevű korábbi verzióiban használt azonos írásmódját használja 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Ezután létrehozunk egy "teszt.txt" nevű fájlt. A tárelérési kulcs használatával a hitelesítéshez a `New-AzureStorageContent` parancsmagot, a fájl feltöltése a blob-tárolóba, majd töltse le a fájlt.

```bash
echo "This is a test text file." > test.txt
```

Ügyeljen arra, hogy első lépésként telepítse az Azure Storage-parancsmagok használatával `Install-Module Azure.Storage`. Majd a most létrehozott, a blob feltöltése a `Set-AzureStorageBlobContent` PowerShell-parancsmagot:

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

Az imént feltöltött blobot, használatával is letöltheti a `Get-AzureStorageBlobContent` PowerShell-parancsmagot:

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


## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).
- Megtudhatja, hogyan teheti ezt a tároló SAS-hitelesítő adatok használatával ugyanezt az oktatóanyagot, tekintse meg a [Azure Storage elérése SAS-hitelesítő adatot keresztül egy Windows virtuális gépek Felügyeltszolgáltatás-identitás segítségével](msi-tutorial-windows-vm-access-storage-sas.md)
- Az Azure Storage-fiók SAS funkció kapcsolatos további információkért lásd:
  - [Közös hozzáférésű jogosultságkódok (SAS) használata](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [A szolgáltatásalapú SAS létrehozása](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása


