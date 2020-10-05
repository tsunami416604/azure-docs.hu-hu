---
title: Azure Data Lake Storage Gen2 PowerShell a fájlokhoz & ACL-ek
description: PowerShell-parancsmagok használatával kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62a6bb807f01fd19a92c3dc4edf797171dd5ebc9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713408"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>A PowerShell használatával kezelheti a címtárakat, a fájlokat és a hozzáférés-vezérlési listákat Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan lehet a PowerShell használatával könyvtárakat, fájlokat és engedélyeket létrehozni és kezelni olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS). 

[Hivatkozás](https://docs.microsoft.com/powershell/module/Az.Storage/?view=azps-4.5.0)  |  [Gen1 a Gen2-megfeleltetéshez](#gen1-gen2-map)  |  [Visszajelzés küldése](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.
> * A .NET-keretrendszer 4.7.2 vagy újabb. Lásd: [.NET-keretrendszer letöltése](https://dotnet.microsoft.com/download/dotnet-framework).
> * PowerShell `5.1` -verzió vagy újabb.

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

1. A következő parancs használatával ellenőrizze, hogy a telepített PowerShell `5.1` -verzió vagy magasabb-e.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   A PowerShell verziójának frissítéséhez lásd: a [meglévő Windows PowerShell frissítése](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Telepítse **az az. Storage** modult.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   A PowerShell-modulok telepítésével kapcsolatos további információkért lásd: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

Nyisson meg egy Windows PowerShell-parancssorablakot, majd jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetését azon Storage-fiók előfizetésére, amelyet a címtárban szeretne létrehozni és kezelni. Ebben a példában a helyőrző értékét cserélje le az `<subscription-id>` előfizetés azonosítójára.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Ezután válassza ki, hogyan szeretné megszerezni a parancsokat a Storage-fiókhoz való engedélyezéshez. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>1. lehetőség: Engedélyezés beszerzése Azure Active Directory (AD) használatával

Ezzel a módszerrel a rendszer biztosítja, hogy a felhasználói fiókja rendelkezik a megfelelő Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) hozzárendelésekkel és ACL-engedélyekkel. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>2. lehetőség: engedély beszerzése a Storage-fiók kulcsa alapján

Ezzel a módszerrel a rendszeren nem ellenőrizhető az Azure-RBAC vagy az ACL-engedélyek.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-container"></a>Tároló létrehozása

A tároló fájlrendszerként működik a fájlok számára. A parancsmag használatával létrehozhat egyet `New-AzStorageContainer` . 

Ez a példa egy nevű tárolót hoz létre `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-hivatkozást a `New-AzDataLakeGen2Item` parancsmag használatával. 

Ez a példa egy nevű könyvtárat helyez `my-directory` el egy tárolóhoz.

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

Ez a példa egy könyvtárat kap a `Get-AzDataLakeGen2Item` parancsmag használatával, majd kinyomtatja a tulajdonságokat a konzolra.

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
> [!NOTE]
> A tároló gyökérkönyvtárának beszerzéséhez hagyja ki a `-Path` paramétert.

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a `Move-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa átnevez egy könyvtárat a név alapján `my-directory` `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Használja a `-Force` paramétert, ha kérés nélkül szeretné felülírni.

Ez a példa egy nevű könyvtárat helyez át `my-directory` egy nevű alkönyvtárba `my-directory-2` `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Könyvtár törlése

Törölje a könyvtárat a parancsmag használatával `Remove-AzDataLakeGen2Item` .

Ez a példa törli a nevű könyvtárat `my-directory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

A (z `-Force` ) paraméterrel a fájl parancssor nélkül is eltávolítható.

## <a name="download-from-a-directory"></a>Letöltés egy címtárból

Töltse le a fájlt egy könyvtárból a `Get-AzDataLakeGen2ItemContent` parancsmag használatával.

Ez a példa egy nevű könyvtárból tölt le egy nevű fájlt `upload.txt` `my-directory` . 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Egy könyvtár tartalmának listázása a parancsmag használatával `Get-AzDataLakeGen2ChildItem` . A nem kötelező paraméterrel `-OutputUserPrincipalName` beolvashatja a felhasználók nevét (az objektum azonosítója helyett).

Ez a példa egy nevű könyvtár tartalmát sorolja fel `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

A következő példa a `ACL` `Permissions` `Group` `Owner` címtár egyes elemeinek,, és tulajdonságait sorolja fel. A `-FetchProperty` tulajdonság értékének beolvasásához a paraméter szükséges `ACL` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> A tároló gyökérkönyvtárának tartalmának listázásához hagyja ki a `-Path` paramétert.

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Töltse fel a fájlt egy könyvtárba a parancsmag használatával `New-AzDataLakeGen2Item` .

Ez a példa egy nevű fájlt tölt fel egy nevű `upload.txt` könyvtárba `my-directory` . 

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
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Ha fel szeretne tölteni egy fájlt a tároló gyökérkönyvtárában, hagyja ki a `-Path` paramétert.

## <a name="show-file-properties"></a>Fájl tulajdonságainak megjelenítése

Ez a példa egy fájlt kap a `Get-AzDataLakeGen2Item` parancsmag használatával, majd kinyomtatja a tulajdonságokat a konzolra.

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

Ez a példa törli a nevű fájlt `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

A (z `-Force` ) paraméterrel a fájl parancssor nélkül is eltávolítható.

## <a name="manage-access-permissions"></a>Hozzáférési engedélyek kezelése

Lekérheti, beállíthatja és frissítheti a címtárak és fájlok hozzáférési engedélyeit. Ezeket az engedélyeket a hozzáférés-vezérlési listák (ACL-ek) rögzítik.

> [!NOTE]
> Ha Azure Active Directory (Azure AD) használatával engedélyezi a parancsokat, akkor győződjön meg arról, hogy a rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a  [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ismertető témakört.

### <a name="get-an-acl"></a>ACL beszerzése

Egy könyvtár vagy fájl hozzáférés-vezérlési listájának lekérése a `Get-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa egy **tároló** gyökérkönyvtárának ACL-listáját jeleníti meg, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

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

![ACL-kimenet beolvasása a címtárhoz](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Ebben a példában a tulajdonos felhasználó olvasási, írási és végrehajtási engedélyekkel rendelkezik. A tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listával kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>ACL beállítása

A `set-AzDataLakeGen2ItemAclObject` parancsmag használatával hozzon létre egy ACL-t a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók számára. Ezután a parancsmag használatával `Update-AzDataLakeGen2Item` véglegesítse az ACL-t.

Ez a példa egy **tároló** gyökérkönyvtárában lévő ACL-t állítja be a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók számára, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Ez a példa a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók **könyvtárának** ACL-listáját állítja be, majd kinyomtatja az ACL-t a konzolra.

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
Ez a példa egy fájl ACL- **fájlját** állítja be a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók számára, majd kinyomtatja az ACL-t a konzolra.

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

Az alábbi képen egy fájl ACL-listájának beállítása után a kimenet látható.

![A fájl ACL-kimenetének beolvasása](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Ebben a példában a tulajdonos felhasználó és a tulajdonos csoport csak olvasási és írási engedéllyel rendelkezik. Minden más felhasználó írási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listával kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](data-lake-storage-access-control.md).

### <a name="add-or-update-an-acl-entry"></a>ACL-bejegyzés hozzáadása vagy frissítése

Először kérje le az ACL-t. Ezután használja a `set-AzDataLakeGen2ItemAclObject` parancsmagot egy ACL-bejegyzés hozzáadásához vagy frissítéséhez. Az `Update-AzDataLakeGen2Item` ACL-t a parancsmag használatával véglegesítheti.

Ez a példa egy felhasználó **címtárában** lévő ACL-t hozza létre vagy frissíti.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

### <a name="remove-an-acl-entry"></a>ACL-bejegyzés eltávolítása

Ez a példa egy meglévő ACL-ből származó bejegyzést távolít el.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="set-an-acl-recursively-preview"></a>ACL rekurzív beállítása (előzetes verzió)

Az ACL-eket a szülő könyvtár meglévő alárendelt elemein is hozzáadhatja, frissítheti és eltávolíthatja anélkül, hogy ezeket a módosításokat egyenként el kellene végeznie az egyes alárendelt elemek esetében. További információ: [rekurzív hozzáférés-vezérlési listák (ACL-ek) beállítása Azure Data Lake Storage Gen2hoz](recursive-access-control-lists.md).

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Gen1 a Gen2-megfeleltetéshez

A következő táblázat azt mutatja be, hogy a parancsmagok hogyan használhatók a Data Lake Storage Gen1 leképezéshez a Data Lake Storage Gen2 parancsmagokhoz.

|Data Lake Storage Gen1 parancsmag| Data Lake Storage Gen2 parancsmag| Jegyzetek |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Alapértelmezés szerint a Get-AzDataLakeGen2ChildItem parancsmag csak az első szintű alárendelt elemeket sorolja fel. A-recurse paraméter rekurzív módon sorolja fel a alárendelt elemeket. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|A Get-AzDataLakeGen2Item parancsmag kimeneti elemei a következő tulajdonságokkal rendelkeznek: ACL, tulajdonos, csoport, engedély.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|A Get-AzDataLakeGen2FileContent parancsmag a fájl tartalmát a helyi fájlba tölti le.|
|Áthelyezés – AzDataLakeStoreItem|Áthelyezés – AzDataLakeGen2Item||
|Új – AzDataLakeStoreItem|Új – AzDataLakeGen2Item|Ez a parancsmag egy helyi fájlból tölti fel az új fájl tartalmát.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Frissítés – AzDataLakeGen2Item|Az Update-AzDataLakeGen2Item parancsmag csak egyetlen elem frissítését frissíti, és nem rekurzív módon. Ha rekurzív módon szeretné frissíteni a frissítést, listaelemeket listázhat a Get-AzDataLakeStoreChildItem parancsmag használatával, majd a folyamatot az Update-AzDataLakeGen2Item parancsmaggal.|
|Teszt – AzDataLakeStoreItem|Get-AzDataLakeGen2Item|A Get-AzDataLakeGen2Item parancsmag hibát jelez, ha az adott objektum nem létezik.|

## <a name="see-also"></a>Lásd még

* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage)
