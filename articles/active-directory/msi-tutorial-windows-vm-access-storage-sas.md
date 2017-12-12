---
title: "A Windows virtuális gép MSI SAS hitelesítő adat segítségével Azure Storage eléréséhez használja"
description: "Ez az oktatóanyag bemutatja, hogyan egy Windows virtuális gép felügyelt szolgáltatás identitás (MSI) Azure Storage egy SAS-hitelesítő adat használata helyett a fiók tárelérési kulcs elérésére használhat."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: bryanla
ms.openlocfilehash: fbe46b952b72d3379ba3eb0ff8c84b9d036752e3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Az Azure Storage érhetnek el egy SAS-hitelesítő adat szolgáltatás Windows virtuális gép felügyelt identitás használatára

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag bemutatja, hogyan engedélyezése felügyelt szolgáltatás identitásának (MSI) egy Windows rendszerű virtuális gép számára, majd használja az MSI storage közös hozzáférésű Jogosultságkód (SAS) hitelesítő adatok beszerzése. Pontosabban a [szolgáltatás SAS hitelesítőadat-](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

A szolgáltatás SAS teszi lehetővé korlátozott hozzáférést biztosít egy tárfiókot, a korlátozott idejű objektumokat és egy adott szolgáltatáshoz (ebben az esetben a blob szolgáltatás), anélkül, hogy egy fiók hozzáférési kulcsot. Használhatja a szokásos módon esetén a következőnek tárolási műveletek, például amikor a Storage szolgáltatás SDK használatával SAS hitelesítő adatokat. Ebben az oktatóanyagban bemutatjuk a fel- és az Azure Storage PowerShell blob letöltéséhez. Megtudhatja, hogyan:


> [!div class="checklist"]
> * A Windows rendszerű virtuális gép MSI engedélyezése 
> * A virtuális gép hozzáférést biztosíthat a tárfiók SAS az erőforrás-kezelőben 
> * Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és a SAS lekérni az erőforrás-kezelő használatával 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Windows virtuális gép egy új erőforráscsoport létrehozása

Ebben az oktatóanyagban azt hozzon létre egy új Windows virtuális Gépet. A meglévő virtuális MSI is engedélyezheti.

1.  Kattintson a **/ hozzon létre új szolgáltatást** gomb az Azure portál bal felső sarkában található.
2.  Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3.  Adja meg a virtuális gép adatait. A **felhasználónév** és **jelszó** létrehozott itt van a hitelesítő adatok használatával jelentkezzen be a virtuális gép.
4.  Válassza ki a megfelelő **előfizetés** a virtuális gép meg a legördülő listában.
5.  Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza ki a virtuális gépet **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6.  Adja meg a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

    ![Kép helyettesítő szövege](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen lévő MSI engedélyezése

A virtuális gép MSI hozzáférési jogkivonatok beolvasása az Azure AD meg szeretne adni a kód hitelesítő adatokat igénylő nélkül teszi lehetővé. A színfalak MSI engedélyezése két dolgot eredményez: az MSI-Virtuálisgép-bővítmény a virtuális Gépet telepít, és MSI lehetővé teszi a virtuális géphez.  

1. Keresse meg az erőforráscsoport a új virtuális gép, és válassza ki a virtuális gépet, az előző lépésben létrehozott.
2. A virtuális gép "Beállítások" a bal oldali panelen kattintson **konfigurációs**.
3. Regisztrálja, és engedélyezze a MSI-t, jelölje be **Igen**, ha szeretné letiltani, válassza a nem.
4. Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.

    ![Kép helyettesítő szövege](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha ellenőrizni szeretné mely bővítmények a virtuális Gépre, kattintson a **bővítmények**. Ha MSI engedélyezve van, a **ManagedIdentityExtensionforWindows** listájában jelenik meg.

    ![Kép helyettesítő szövege](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Create a storage account 

Ha még nem rendelkezik egy, most létrehoz egy tárfiókot. Is ezt a lépést kihagyhatja, és a virtuális gép MSI hozzáférést biztosíthat a meglévő tárfiók SAS hitelesítő adatai. 

1. Kattintson a **/ hozzon létre új szolgáltatást** gomb az Azure portál bal felső sarkában található.
2. Kattintson a **tárolási**, majd **Tárfiók**, és egy új, "Create a storage-fiók" panelen jelenik meg.
3. Adjon meg egy nevet a tárfiók, amelyek később fogja használni.  
4. **Telepítési modell** és **fiók kind** meg "Erőforrás-kezelő" és "Általános célú", illetve. 
5. Győződjön meg arról a **előfizetés** és **erőforráscsoport** az előző lépésben a virtuális gép létrehozásakor megadott megfelelően.
6. Kattintson a **Create** (Létrehozás) gombra.

    ![Új tárfiók létrehozása](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>A tárfiók egy blob-tároló létrehozása

Később rendszer feltöltése és töltse le a fájlt az új tárfiókot. Mivel a szükséges blob-tároló fájlok, igazolnia kell a fájl tárolására blob tárolókat hozhat létre.

1. Lépjen vissza az újonnan létrehozott tárfiók.
2. Kattintson a **tárolók** hivatkozásra a bal oldali panelen kattintson a "Blob szolgáltatás."
3. Kattintson a **+ tároló** tetején a lap és az "új tároló" panel diák ki.
4. Nevezze el a tároló, válassza ki a hozzáférési szintet, majd kattintson a **OK**. A megadott név az oktatóanyag későbbi részében fogja használni. 

    ![A tároló létrehozása](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>A virtuális gép MSI hozzáférést egy SAS tárolók használatához 

Az Azure Storage natív módon támogatja az Azure AD-alapú hitelesítés.  Azonban egy olyan MSI Csomaghoz segítségével beolvasni a tároló SAS erőforrás-kezelő, majd a SAS használatával férjenek hozzá a tároló.  Ebben a lépésben megadta a virtuális gép MSI hozzáférést a tárfiók SAS.   

1. Lépjen vissza az újonnan létrehozott tárfiók.   
2. Kattintson a **hozzáférés-vezérlés (IAM)** hivatkozás a bal oldali panelen.  
3. Kattintson a **+ Hozzáadás** fölött a lap egy új szerepkör-hozzárendelés hozzáadása a virtuális gép számára
4. Állítsa be **szerepkör** "Tárolási fiók munkatárs", a lap jobb oldalán.  
5. A következő legördülő menüből, állítson be **való hozzáférés hozzárendelése** az erőforrás "Virtuális gép".  
6. A következő szerepel-e a megfelelő előfizetést a **előfizetés** legördülő menüből, majd állítsa be **erőforráscsoport** "Az összes erőforráscsoport" számára.  
7. Végezetül a **válasszon** meg a legördülő listában válassza ki a Windows rendszerű virtuális gép, majd kattintson az **mentése**. 

    ![Kép helyettesítő szövege](media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és hívja az Azure Resource Manager használatával 

Az oktatóanyag a hátralévő azt fog működni a korábban létrehozott virtuális gépről.

Szüksége lesz az Azure Resource Manager PowerShell-parancsmagok használatához a részében.  Ha nincs telepítve, ez a [a legújabb verzió letöltéséhez](https://docs.microsoft.com/powershell/azure/overview) folytatása előtt.

1. Az Azure-portálon lépjen a **virtuális gépek**, keresse fel a Windows rendszerű virtuális gép, majd a a **áttekintése** kattintson **Connect** tetején.
2. Adja meg a **felhasználónév** és **jelszó** számára, amely hozzá van, a Windows virtuális gép létrehozása után. 
3. Most, hogy létrehozott egy **távoli asztali kapcsolat** a virtuális géppel, nyissa meg a Powershellt a távoli munkamenet. 
4. A helyi MSI-végpont megszerezni egy hozzáférési jogkivonatot az Azure Resource Manager használatával Powershell Invoke-WebRequest, indítson egy lekérdezést.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > A "resource" paraméter értékének az Azure AD által várt pontosan egyeznie kell. Az Azure Resource Manager erőforrás-azonosító használata esetén meg kell adni a záró perjelet URI-n.
    
    Ezután bontsa ki a "Tartalom" elemet, amely tárolja a $response objektum JavaScript Object Notation (JSON) formátumú karakterláncot. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ezután bontsa ki a hozzáférési jogkivonat a választ.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>SAS hitelesítő adatokat az Azure Resource Manager tárolási hívásokat beolvasása 

Mostantól a PowerShell használatával hívja az erőforrás-kezelőt a hozzáférési jogkivonat azt lekérése az előző szakaszban tároló SAS-hitelesítő adat létrehozása. Tudunk a SAS-hitelesítő adatokat, ha a tárolási műveletek is nevezzük.

A kérelem a SAS-hitelesítő adat létrehozása a következő HTTP kérelemben szereplő paraméterek használjuk:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Ezek a paraméterek szerepelnek a FELADÁS egy vagy több szervezet SAS hitelesítési kérelem. A paraméterek egy SAS-hitelesítő adatok létrehozására vonatkozó további információkért lásd: a [lista szolgáltatás SAS REST-referencia](/rest/api/storagerp/storageaccounts/listservicesas).

Először, a paraméterek alakítható át JSON-NÁ, majd hívja a tárolási `listServiceSas` hitelesítőadat-végpont létrehozása az SA-kat:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> Az URL-címe: kis-és nagybetűket, ezért figyeljen oda arra használhatja az erőforráscsoportot, beleértve a nagy "G" a "resourceGroups." elnevezett korábbi verzióiban használt pontos nagybetűket 

Most azt is kinyerése a SAS-hitelesítő adatokat a válasz:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Ha a SAS-adatok láthatja az alábbihoz hasonló vizsgálhatja meg:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Ezután azt hozzon létre egy "teszt.txt" nevű fájlt. A SAS-hitelesítő adat segítségével hitelesítik magukat a `New-AzureStorageContent` parancsmag, a fájl feltöltése a blob-tárolóba, majd töltse le a fájlt.

```bash
echo "This is a test text file." > test.txt
```

Ügyeljen arra, hogy először telepítse az Azure Storage-parancsmagokat használó `Install-Module Azure.Storage`. Majd az imént létrehozott, blob feltöltése a `Set-AzureStorageBlobContent` PowerShell-parancsmagot:

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

Emellett letöltheti a blob imént feltöltött szolgáltatáscsomagban, használja a `Get-AzureStorageBlobContent` PowerShell-parancsmagot:

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


## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](../active-directory/msi-overview.md).
- Hogyan hajtsa végre ugyanezt az oktatóanyagot használata a tárfiók kulcsára, lásd: [Azure Storage eléréséhez használja Windows virtuális gép által felügyelt-szolgáltatási identitás](msi-tutorial-windows-vm-access-storage.md)
- Az Azure Storage-fiók SAS szolgáltatás kapcsolatos további információkért lásd:
  - [Közös hozzáférésű jogosultságkód (SAS) használatával](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [A szolgáltatásalapú SAS létrehozása](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.


