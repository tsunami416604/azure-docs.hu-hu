---
title: A PowerShell használata fájlok & ACL-ekkel Azure Data Lake Storage Gen2 (előzetes verzió)
description: PowerShell-parancsmagok használatával kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 360845d75c27fab0151c3a64846ff788f9f56b95
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931242"
---
# <a name="use-powershell-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>A PowerShell használata fájlok & ACL-ekkel Azure Data Lake Storage Gen2 (előzetes verzió)

Ez a cikk bemutatja, hogyan lehet a PowerShell használatával könyvtárakat, fájlokat és engedélyeket létrehozni és kezelni olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS). 

> [!IMPORTANT]
> A cikkben Kiemelt PowerShell-modul jelenleg nyilvános előzetes verzióban érhető el.

[Gen1 a Gen2-megfeleltetéshez](#gen1-gen2-map) | [visszajelzés küldése](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.
> * A .NET-keretrendszer 4.7.2 vagy újabb. Lásd: [.NET-keretrendszer letöltése](https://dotnet.microsoft.com/download/dotnet-framework).
> * PowerShell-verzió `5.1` vagy újabb.

## <a name="install-powershell-modules"></a>PowerShell-modulok telepítése

1. A következő parancs használatával ellenőrizze, hogy a telepített PowerShell-verzió `5.1` vagy magasabb-e. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    A PowerShell verziójának frissítéséhez lásd: a [meglévő Windows PowerShell frissítése](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Telepítse a legújabb **PowershellGet** -modult. Ezután lépjen be és nyissa meg újra a PowerShell-konzolt.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Telepítse **az az. Storage** Preview modult.

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    A PowerShell-modulok telepítésével kapcsolatos további információkért lásd: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

1. Nyisson meg egy Windows PowerShell-parancssori ablakot.

2. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

3. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetését azon Storage-fiók előfizetésére, amelyet a címtárban szeretne létrehozni és kezelni.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

4. Szerezze be a Storage-fiókot.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   ```

   * Cserélje le a `<resource-group-name>` helyőrző értékét az erőforráscsoport nevére.

   * Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

5. A Storage-fiók környezetének beolvasása.

   ```powershell
   $ctx = $storageAccount.Context
   ```

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer tárolóként működik a fájlok számára. Létrehozhat egyet a `New-AzDatalakeGen2FileSystem` parancsmag használatával. 

Ez a példa egy `my-file-system`nevű fájlrendszert hoz létre.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy könyvtárat a `New-AzDataLakeGen2Item` parancsmag használatával. 

Ez a példa egy `my-directory` nevű könyvtárat helyez el egy fájlrendszerhez.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Ez a példa hozzáadja ugyanazt a könyvtárat, de beállítja az engedélyeket, a umask, a tulajdonság értékeit és a metaadatok értékét is. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Könyvtár tulajdonságainak megjelenítése

Ez a példa egy könyvtárat olvas be a `Get-AzDataLakeGen2Item` parancsmag használatával, majd kinyomtatja a tulajdonságokat a konzolra.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a `Move-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa átnevez egy könyvtárat a név `my-directory` a név `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

Ez a példa egy `my-directory` nevű könyvtárat helyez át egy `my-subdirectory`nevű `my-directory-2` alkönyvtárába. Ez a példa egy umask is alkalmaz az alkönyvtárra.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Könyvtár törlése

Törölje a könyvtárat a `Remove-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa töröl egy `my-directory`nevű könyvtárat. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

A fájlt a `-Force` paraméterrel távolíthatja el.

## <a name="download-from-a-directory"></a>Letöltés egy címtárból

Töltse le a fájlt egy könyvtárból a `Get-AzDataLakeGen2ItemContent` parancsmag használatával.

Ez a példa egy `my-directory`nevű könyvtárból tölt le egy `upload.txt` nevű fájlt. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Egy könyvtár tartalmának listázása a `Get-AzDataLakeGen2ChildItem` parancsmag használatával.

Ez a példa egy `my-directory`nevű könyvtár tartalmát sorolja fel. 

A fájlrendszer tartalmának listázásához hagyja ki a `-Path` paramétert a parancsból.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

Ez a példa egy `my-directory` nevű könyvtár tartalmát sorolja fel, és a listában található ACL-eket tartalmazza. A `-Recurse` paramétert is használja az alkönyvtárak tartalmának listázásához.

A fájlrendszer tartalmának listázásához hagyja ki a `-Path` paramétert a parancsból.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
```

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Töltse fel a fájlt egy könyvtárba a `New-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa egy `upload.txt` nevű fájlt tölt fel egy `my-directory`nevű könyvtárba. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Ez a példa feltölti ugyanazt a fájlt, de ezt követően beállítja a célfájl engedélyeit, umask, tulajdonságának értékeit és metaadat-értékeit. Ez a példa ezeket az értékeket is kiírja a konzolra.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Fájl tulajdonságainak megjelenítése

Ez a példa egy fájlt kér le a `Get-AzDataLakeGen2Item` parancsmag használatával, majd kinyomtatja a tulajdonságokat a konzolra.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>Fájl törlése

Töröljön egy fájlt a `Remove-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa töröl egy `upload.txt`nevű fájlt. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

A fájlt a `-Force` paraméterrel távolíthatja el.

## <a name="manage-access-permissions"></a>Hozzáférési engedélyek kezelése

Lekérheti, beállíthatja és frissítheti a címtárak és fájlok hozzáférési engedélyeit.

### <a name="get-directory-and-file-permissions"></a>Könyvtár-és fájlengedélyek beolvasása

Egy könyvtár vagy fájl hozzáférés-vezérlési listájának lekérése az `Get-AzDataLakeGen2Item`parancsmag használatával.

Ez a példa egy **könyvtár**ACL-listáját kéri le, majd kiírja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Ez a példa egy **fájl** ACL-listáját kéri le, majd kiírja az ACL-t a konzolra.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Az alábbi képen egy könyvtár ACL-listájának beolvasása után a kimenet látható.

![ACL kimenetének beolvasása](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Ebben a példában a tulajdonos felhasználó olvasási, írási és végrehajtási engedélyekkel rendelkezik. A tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listával kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Könyvtár-és fájlengedélyek megadása

A `New-AzDataLakeGen2ItemAclObject` parancsmaggal hozzon létre egy ACL-t a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók számára. Ezután a `Update-AzDataLakeGen2Item` parancsmag használatával véglegesítse az ACL-t.

Ez a példa a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók **könyvtárának** ACL-listáját állítja be, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
Ez a példa egy fájl ACL- **fájlját** állítja be a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók számára, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Az alábbi képen egy fájl ACL-listájának beállítása után a kimenet látható.

![ACL kimenetének beolvasása](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Ebben a példában a tulajdonos felhasználó és a tulajdonos csoport csak olvasási és írási engedéllyel rendelkezik. Minden más felhasználó írási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listával kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Könyvtár-és fájlengedélyek frissítése

A `Get-AzDataLakeGen2Item` parancsmaggal kérheti le egy könyvtár vagy fájl hozzáférés-vezérlési listáját. Ezután a `New-AzDataLakeGen2ItemAclObject` parancsmag használatával hozzon létre egy új ACL-bejegyzést. Az új ACL alkalmazásához használja az `Update-AzDataLakeGen2Item` parancsmagot.

Ez a példa egy felhasználó írási és végrehajtási engedélyét adja meg egy címtárban.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```
Ez a példa egy felhasználó írási és végrehajtási engedélyét adja meg egy fájlra vonatkozóan.

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $acl
```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Engedélyek beállítása fájlrendszer összes eleméhez

A `Get-AzDataLakeGen2Item` és a `-Recurse` paramétert a `Update-AzDataLakeGen2Item` parancsmaggal együtt használva rekurzív módon állíthatja be a fájlrendszer összes könyvtárának és fájljának ACL-listáját. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen1 a Gen2-megfeleltetéshez

A következő táblázat azt mutatja be, hogy a parancsmagok hogyan használhatók a Data Lake Storage Gen1 leképezéshez a Data Lake Storage Gen2 parancsmagokhoz.

|Data Lake Storage Gen1 parancsmag| Data Lake Storage Gen2 parancsmag| Megjegyzések |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Alapértelmezés szerint a Get-AzDataLakeGen2ChildItem parancsmag csak az első szintű alárendelt elemeket sorolja fel. A-recurse paraméter rekurzív módon sorolja fel a alárendelt elemeket. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|A Get-AzDataLakeGen2Item parancsmag kimeneti elemei a következő tulajdonságokkal rendelkeznek: ACL, tulajdonos, csoport, engedély.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|A Get-AzDataLakeGen2FileContent parancsmag a fájl tartalmát a helyi fájlba tölti le.|
|Áthelyezés – AzDataLakeStoreItem|Áthelyezés – AzDataLakeGen2Item||
|Új – AzDataLakeStoreItem|Új – AzDataLakeGen2Item|Ez a parancsmag egy helyi fájlból tölti fel az új fájl tartalmát.|
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Frissítés – AzDataLakeGen2Item|Az Update-AzDataLakeGen2Item parancsmag csak egyetlen elem frissítését frissíti, és nem rekurzív módon. Ha rekurzív módon szeretné frissíteni a frissítést, listaelemeket listázhat a Get-AzDataLakeStoreChildItem parancsmag használatával, majd a folyamatot az Update-AzDataLakeGen2Item parancsmaggal.|
|Teszt – AzDataLakeStoreItem|Get-AzDataLakeGen2Item|A Get-AzDataLakeGen2Item parancsmag hibát jelez, ha az adott objektum nem létezik.|



## <a name="see-also"></a>Lásd még:

* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Azure PowerShell használata az Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-ban.
* [Storage PowerShell-parancsmagok](/powershell/module/az.storage).

