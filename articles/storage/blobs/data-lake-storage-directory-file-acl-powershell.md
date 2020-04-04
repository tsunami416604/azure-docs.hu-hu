---
title: Azure Data Lake Storage Gen2 PowerShell fájlokhoz & ACL-khez (előzetes verzió)
description: A PowerShell-parancsmagokkal kezelheti a könyvtárakat, valamint a fájl- és címtárhozzáférés-vezérlési listákat (ACL) olyan tárfiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 58548c5cb1aa6aba6dda09d5d420b36bb8154726
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656400"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Könyvtárak, fájlok és AC-k kezelése a PowerShell használatával az Azure Data Lake Storage Gen2 szolgáltatásban (előzetes verzió)

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet könyvtárakat, fájlokat és engedélyeket a hierarchikus névtérrel (HNS) rendelkező tárfiókokban a PowerShell használatával. 

> [!IMPORTANT]
> A cikkben szereplő PowerShell-modul jelenleg nyilvános előzetes verzióban érhető el.

[Gen1 a Gen2 feltérképezése](#gen1-gen2-map) | [Visszajelzés küldése](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan tárfiók, amelynek hierarchikus névtere (HNS) engedélyezve van. Az [alábbi](data-lake-storage-quickstart-create-account.md) utasításokat követve hozzon létre egyet.
> * A . Lásd: [.NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)letöltése .
> * PowerShell-verzió `5.1` vagy újabb verzió.

## <a name="install-powershell-modules"></a>PowerShell-modulok telepítése

1. Ellenőrizze, hogy a telepített PowerShell-verzió a `5.1` következő paranccsal vagy annál magasabb.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   A PowerShell verziójának frissítéséhez olvassa el [a meglévő Windows PowerShell frissítése című témakört.](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Telepítse a legújabb **PowershellGet** modult. Ezután zárja be és nyissa meg újra a PowerShell-konzolt.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force 
   ```

3. Telepítse **az Az.Storage** előzetes modulját.

   ```powershell
   Install-Module az.storage -RequiredVersion 1.13.3-preview -Repository PSGallery -AllowClobber -AllowPrerelease -Force 
   ```

   A PowerShell-modulok telepítéséről az [Azure PowerShell-modul telepítése című](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) témakörben talál további információt.

## <a name="connect-to-the-account"></a>Csatlakozás a fiókhoz

Nyisson meg egy Windows PowerShell-parancsablakot, majd `Connect-AzAccount` jelentkezzen be az Azure-előfizetésbe a paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

Ha az identitás egynél több előfizetéshez van társítva, állítsa be az aktív előfizetést a könyvtárak létrehozásához és kezeléséhez kívánt tárfiók előfizetésére. Ebben a példában `<subscription-id>` cserélje le a helyőrző értékét az előfizetés azonosítójára.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Ezután válassza ki, hogyan szeretné, hogy a parancsok engedélyt szerezzenek a tárfiókhoz. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>1. lehetőség: Engedélyezés beszerzése az Azure Active Directory (AD) használatával

Ezzel a megközelítéssel a rendszer biztosítja, hogy a felhasználói fiók rendelkezik a megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) hozzárendelésekkel és ACL-engedélyekkel. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>2. lehetőség: Engedélyezés beszerzése a tárfiók kulcsával

Ezzel a módszerrel a rendszer nem ellenőrzi az RBAC- vagy ACL-engedélyeket.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer a fájlok tárolójaként működik. Létrehozhat egyet a `New-AzDatalakeGen2FileSystem` parancsmag használatával. 

Ez a példa létrehoz `my-file-system`egy fájlrendszert.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Könyvtárhivatkozás létrehozása a `New-AzDataLakeGen2Item` parancsmag használatával. 

Ez a példa `my-directory` egy fájlrendszerhez nevezett könyvtárat ad hozzá.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Ez a példa ugyanazt a könyvtárat adja hozzá, de beállítja az engedélyeket, umask, tulajdonságértékek és metaadat-értékeket is. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Könyvtártulajdonságok megjelenítése

Ez a példa lead egy könyvtárat a `Get-AzDataLakeGen2Item` parancsmag használatával, majd kinyomtatja a tulajdonságértékeket a konzolra.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

## <a name="rename-or-move-a-directory"></a>Könyvtár átnevezése vagy áthelyezése

Könyvtár átnevezése vagy áthelyezése a `Move-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa átnevez egy `my-directory` könyvtárat `my-new-directory`a névről a névre.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Használja `-Force` a paramétert, ha kérdés nélkül szeretné felülírni.

Ez a példa `my-directory` áthelyez egy megnevezett `my-subdirectory`könyvtárat az elnevezett alkönyvtárba. `my-directory-2` Ez a példa egy umask-ot is alkalmaz az alkönyvtárra.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Könyvtár törlése

Könyvtár törlése a `Remove-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa törli `my-directory`a . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

A paraméter `-Force` segítségével kérdés nélkül távolíthatja el a fájlt.

## <a name="download-from-a-directory"></a>Letöltés könyvtárból

Töltse le a fájlt egy `Get-AzDataLakeGen2ItemContent` könyvtárból a parancsmag használatával.

Ez a példa a `upload.txt` nevű könyvtárból letölti a nevű `my-directory`fájlt. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

A könyvtár tartalmának listázása a `Get-AzDataLakeGen2ChildItem` parancsmag használatával. A választható paraméter `-OutputUserPrincipalName` rel lekaphatja a felhasználók nevét (az objektumazonosító helyett).

Ez a példa a program `my-directory`nak nevezett könyvtár tartalmát sorolja fel.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

A következő példa `ACL` `Permissions`a `Group`könyvtárban lévő elemek , , , és `Owner` tulajdonságait sorolja fel. A `-FetchProperty` paraméter szükséges a tulajdonság `ACL` értékeinek lekérnie. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

A fájlrendszer tartalmának listázásához `-Path` hagyja ki a paramétert a parancsból.

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Töltsön fel egy fájlt `New-AzDataLakeGen2Item` egy könyvtárba a parancsmag használatával.

Ez a példa feltölt `upload.txt` egy nevű `my-directory`fájlt a nevesített könyvtárba. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Ez a példa feltölti ugyanazt a fájlt, de ezután beállítja a célfájl engedélyeit, umask, tulajdonságértékeit és metaadat-értékeit. Ez a példa ezeket az értékeket is kinyomtatja a konzolra.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

## <a name="show-file-properties"></a>Fájltulajdonságok megjelenítése

Ez a példa lead egy fájlt a `Get-AzDataLakeGen2Item` parancsmag használatával, majd kinyomtatja a tulajdonságértékeket a konzolra.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Fájl törlése

Fájl törlése a `Remove-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa a `upload.txt`nevű fájlt törli. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

A paraméter `-Force` segítségével kérdés nélkül távolíthatja el a fájlt.

## <a name="manage-access-permissions"></a>Hozzáférési engedélyek kezelése

A fájlrendszerek, könyvtárak és fájlok hozzáférési engedélyeit beszerezheti, beállíthatja és frissítheti.

> [!NOTE]
> Ha az Azure Active Directory (Azure AD) parancsok engedélyezéséhez használja, győződjön meg arról, hogy a rendszerbiztonsági tag hozzá van rendelve a [Storage Blob Data Owner szerepkörhöz.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Ha többet szeretne tudni az ACL-engedélyek alkalmazásáról és módosításuk hatásairól, olvassa el [a Hozzáférés-vezérlés az Azure Data Lake Storage Gen2 alkalmazásban című témakört.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

### <a name="get-permissions"></a>Engedélyek beszereznie

Egy könyvtár vagy fájl ACL-jének beszereznie a `Get-AzDataLakeGen2Item`parancsmag használatával.


Ez a példa leveszi egy **fájlrendszer** ACL-ét, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Ez a példa leveszi egy **könyvtár**acl-t , majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Ez a példa leveszi egy **fájl** ACL-ét, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Az alábbi képen látható a kimenet egy könyvtár ACL-jének beszerzése után.

![ACL kimenet beszereznie](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Ebben a példában a tulajdonában lévő felhasználó olvasási, írási és végrehajtási engedélyekkel rendelkezik. A tulajdonában lévő csoport csak olvasási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listákról további információt az [Azure Data Lake Storage Gen2 hozzáférés-vezérlése című témakörben](data-lake-storage-access-control.md)talál.

### <a name="set-or-update-permissions"></a>Engedélyek beállítása vagy frissítése

A `set-AzDataLakeGen2ItemAclObject` parancsmag segítségével hozzon létre egy ACL a tulajdonában lévő felhasználó, a tulajdonában lévő csoport, vagy más felhasználók számára. Ezután használja `Update-AzDataLakeGen2Item` a parancsmamot az ACL véglegesítéséhez.

Ez a példa beállítja az ACL-t egy **fájlrendszeren** a saját felhasználó, a tulajdonában lévő csoport vagy más felhasználók számára, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Ez a példa beállítja az ACL-t a tulajdonában lévő felhasználó, a tulajdonában lévő csoport vagy más felhasználók **könyvtárában,** majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
Ez a példa beállítja az ACL-t egy **fájlon** a tulajdonában lévő felhasználó, a tulajdonában lévő csoport vagy más felhasználók számára, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Az alábbi képen a kimenet látható a fájl ACL-jének beállítása után.

![ACL kimenet beszereznie](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Ebben a példában a tulajdonában lévő felhasználó és a tulajdonában lévő csoport csak olvasási és írási engedéllyel rendelkezik. Minden más felhasználó rendelkezik írási és végrehajtási engedéllyel. A hozzáférés-vezérlési listákról további információt az [Azure Data Lake Storage Gen2 hozzáférés-vezérlése című témakörben](data-lake-storage-access-control.md)talál.


### <a name="set-permissions-on-all-items-in-a-file-system"></a>Engedélyek beállítása a fájlrendszer összes elemére vonatkozóan

A `Get-AzDataLakeGen2Item` és a `-Recurse` paraméter és `Update-AzDataLakeGen2Item` a parancsmag segítségével rekurzívmódon állíthatja be a fájlrendszerben lévő összes könyvtár és fájl ACL-ét. 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen1 –Gen2 leképezés

Az alábbi táblázat bemutatja, hogyan a Data Lake Storage Gen1 parancsmagok leképezése a Data Lake Storage Gen2 parancsmagok.

|Data Lake Storage Gen1 parancsmag| Data Lake Storage Gen2 parancsmag| Megjegyzések |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2Childitem|Alapértelmezés szerint a Get-AzDataLakeGen2ChildItem parancsmag csak az első szintű gyermekelemeket sorolja fel. A -Recurse paraméter rekurzívmódon sorolja fel az alárendelt elemeket. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2tétel|A Get-AzDataLakeGen2Item parancsmag kimeneti elemei a következő tulajdonságokkal rendelkeznek: Acl, Owner, Group, Permission.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|A Get-AzDataLakeGen2FileContent parancsmag fájltartalmat tölt le a helyi fájlba.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2tétel||
|Új-AzDataLakeStoreItem|Új-AzDataLakeGen2tétel|Ez a parancsmag helyi fájlból tölti fel az új fájltartalmat.|
|Eltávolítás-AzDataLakeStoreItem|Remove-AzDataLakeGen2item||
|Set-AzDataLakeStoreItemowner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2tétel|Az Update-AzDataLakeGen2Item parancsmag csak egyetlen elemet frissít, rekurzívmódon nem. Ha rekurzívan szeretné frissíteni az elemeket a Get-AzDataLakeChildItem parancsmag használatával, majd az Update-AzDataLakeGen2Item parancsmagba.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2tétel|A Get-AzDataLakeGen2Item parancsmag hibát jelez, ha az elem nem létezik.|



## <a name="see-also"></a>Lásd még

* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Az Azure PowerShell használata az Azure Storage szolgáltatással.](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Tároló PowerShell-parancsmagok](/powershell/module/az.storage).

