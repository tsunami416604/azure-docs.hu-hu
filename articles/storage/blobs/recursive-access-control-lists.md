---
title: ACL-ek rekurzív beállítása a Azure Data Lake Storage Gen2hoz | Microsoft Docs
description: A hozzáférés-vezérlési lista rekurzív módon adható hozzá, frissíthető és eltávolítható a szülő könyvtár meglévő alárendelt elemeihez.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 10/06/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: f9f0983bdb5e8763d13eeab8ea21bef7fb9ef47f
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803330"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Hozzáférés-vezérlési listák (ACL-ek) rekurzív beállítása Azure Data Lake Storage Gen2

Az ACL öröklése már elérhető az új alárendelt elemekhez, amelyeket a rendszer a szülő címtárban hozott létre. Mostantól hozzáadhatja, frissítheti és eltávolíthatja a hozzáférés-vezérlési listákat a szülő könyvtár meglévő alárendelt elemeihez anélkül, hogy ezeket a módosításokat egyenként el kellene végeznie az egyes alárendelt elemek esetében.

> [!NOTE]
> A hozzáférési listák rekurzív beállításának lehetősége nyilvános előzetes verzióban érhető el, és minden régióban elérhető.  

[Könyvtárak](#libraries)  |  [Példák](#code-samples)  |  [Ajánlott eljárások](#best-practice-guidelines)  |  [Visszajelzés küldése](#provide-feedback)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.

- A rekurzív ACL-folyamat végrehajtásának megfelelő engedélyei. A megfelelő engedély a következők egyikét tartalmazza: 

  - Egy kiépített Azure Active Directory (AD) [rendszerbiztonsági tag](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) , amely a [tároló blob-adattulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepkört rendelte hozzá a cél tároló, a szülő erőforráscsoport vagy az előfizetés hatókörében.   

  - Annak a tárolónak vagy könyvtárnak a tulajdonosa, amelyre a rekurzív ACL-folyamat alkalmazását tervezi. Ez a cél tárolóban vagy a címtárban található összes alárendelt elemet magában foglalja. 

- Annak megértése, hogy a rendszer hogyan alkalmazza a ACL-eket a címtárakra és a fájlokra. Lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Ennek a cikknek a **projekt beállítása** című szakaszában találja a PowerShell, a .net SDK és a Python SDK telepítési útmutatójának megtekintését.

## <a name="set-up-your-project"></a>A projekt beállítása

Telepítse a szükséges kódtárakat.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Győződjön meg arról, hogy a .NET-keretrendszer telepítve van. Lásd: [.NET-keretrendszer letöltése](https://dotnet.microsoft.com/download/dotnet-framework).
 
2. A következő parancs használatával ellenőrizze, hogy a telepített PowerShell `5.1` -verzió vagy magasabb-e.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   A PowerShell verziójának frissítéséhez lásd: a [meglévő Windows PowerShell frissítése](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell)
    
3. Telepítse a PowershellGet modul legújabb verzióját.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. Kattintson a Bezárás gombra, majd nyissa meg újra a PowerShell-konzolt.

5. Telepítse **az az. Storage** Preview modult.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   A PowerShell-modulok telepítésével kapcsolatos további információkért lásd: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="net"></a>[.NET](#tab/dotnet)

1. Nyisson meg egy parancssori ablakot (például: Windows PowerShell).

2. A projekt könyvtárából telepítse az Azure. Storage. files. DataLake előzetes verzióját a `dotnet add package` parancs használatával.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Vegye fel ezeket a using utasításokkal a programkód elejére.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="python"></a>[Python](#tab/python)

1. Töltse le a [Azure Data Lake Storage a Pythonhoz készült ügyféloldali kódtárat](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0).

2. Telepítse a letöltött könyvtárat a [pip](https://pypi.org/project/pip/)használatával.

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

Adja hozzá ezeket az importálási utasításokat a fájl elejéhez.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>Csatlakozás a fiókhoz

A Azure Active Directory (AD) vagy egy fiók kulcsa segítségével csatlakozhat. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

A következő táblázat az egyes támogatott szerepköröket és azok ACL-beállítási képességét mutatja be.

|Szerepkör|ACL-beállítási képesség|
|--|--|
|[Storage-blobadatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|A fiókban lévő összes könyvtár és fájl.|
|[Storage-blobadatok közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Csak a rendszerbiztonsági tag tulajdonában lévő könyvtárak és fájlok.|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>2. lehetőség: engedély beszerzése a Storage-fiók kulcsa alapján

Ezzel a módszerrel a rendszeren nem ellenőrizhető az Azure-RBAC vagy az ACL-engedélyek.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="net"></a>[.NET](#tab/dotnet)

A cikkben szereplő kódrészletek használatához létre kell hoznia egy [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -példányt, amely a Storage-fiókot jelképezi.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Kapcsolat Azure Active Directory (AD) használatával

A [.net-hez készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) használatával hitelesítheti alkalmazását az Azure ad-vel.

A csomag telepítése után adja hozzá a using utasítást a programkód elejéhez.

```csharp
using Azure.Identity;
```

Szerezze be az ügyfél-azonosítót, az ügyfél titkos kulcsát és a bérlő AZONOSÍTÓját. Ehhez tekintse meg [a jogkivonat beszerzése az Azure ad-ből az ügyfélalkalmazástól érkező kérések engedélyezéséhez](../common/storage-auth-aad-app.md)című témakört. A folyamat részeként hozzá kell rendelnie a következő [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC)](../../role-based-access-control/overview.md) szerepkörök egyikét a rendszerbiztonsági tag számára. 

|Szerepkör|ACL-beállítási képesség|
|--|--|
|[Storage-blobadatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|A fiókban lévő összes könyvtár és fájl.|
|[Storage-blobadatok közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Csak a rendszerbiztonsági tag tulajdonában lévő könyvtárak és fájlok.|

Ez a példa egy [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a módszer a fiókhoz való kapcsolódás legegyszerűbb módja. 

Ez a példa egy [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) -példányt hoz létre a fiók kulcsa alapján.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> További példákért tekintse meg a [.net-hez készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) dokumentációját.

### <a name="python"></a>[Python](#tab/python)

A cikkben szereplő kódrészletek használatához létre kell hoznia egy **DataLakeServiceClient** -példányt, amely a Storage-fiókot jelképezi. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Kapcsolat Azure Active Directory (AD) használatával

A [Pythonhoz készült Azure Identity ügyféloldali kódtár](https://pypi.org/project/azure-identity/) használatával hitelesítheti az alkalmazást az Azure ad-vel.

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  Az értékek [lekéréséhez lásd: token beszerzése az Azure ad-ből az ügyfélalkalmazások kéréseinek engedélyezéséhez](../common/storage-auth-aad-app.md). A folyamat részeként hozzá kell rendelnie a következő [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC)](../../role-based-access-control/overview.md) szerepkörök egyikét a rendszerbiztonsági tag számára. 

|Szerepkör|ACL-beállítási képesség|
|--|--|
|[Storage-blobadatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|A fiókban lévő összes könyvtár és fájl.|
|[Storage-blobadatok közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Csak a rendszerbiztonsági tag tulajdonában lévő könyvtárak és fájlok.|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> További példákért tekintse meg az [Azure Identity ügyféloldali kódtárat a Python](https://pypi.org/project/azure-identity/) dokumentációjában.

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra. 

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre a fiók kulcsa alapján.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Cserélje le a `storage_account_name` helyőrző értékét a Storage-fiók nevére.

- Cserélje le a `storage_account_key` helyőrző értékét a Storage-fiók hozzáférési kulcsára.

---

## <a name="set-an-acl-recursively"></a>ACL beállítása rekurzív módon

Az ACL *beállításakor* a teljes ACL-t **lecseréli** , beleértve az összes bejegyzését. Ha módosítani szeretné egy rendszerbiztonsági tag hozzáférési szintjét, vagy egy új rendszerbiztonsági tag hozzáadása az ACL-hez anélkül, hogy ez hatással lenne a többi meglévő bejegyzésre, *frissítenie* kell az ACL-t. Ha az ACL-t nem a helyett szeretné frissíteni, tekintse meg a jelen cikk [ACL rekurzív frissítése](#update-an-acl-recursively) című szakaszát.   

Ez a szakasz példákat tartalmaz az ACL beállítására 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az ACL-t rekurzív módon állítsa be a `Set-AzDataLakeGen2AclRecursive` parancsmag használatával.

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ezek a bejegyzések az olvasási, írási és végrehajtási engedélyeket adják meg a tulajdonos számára, és csak olvasási és végrehajtási engedélyeket biztosítanak a tulajdonos csoportnak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad meg az "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" AZONOSÍTÓJÚ objektumhoz olvasási és végrehajtási engedélyekkel.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

Az ACL-t rekurzív módon állíthatja be a **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** metódus meghívásával. Adja át ezt a metódust a [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [listájához](/dotnet/api/system.collections.generic.list-1) . Mindegyik [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) egy ACL-bejegyzést definiál.

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ezek a bejegyzések az olvasási, írási és végrehajtási engedélyeket adják meg a tulajdonos számára, és csak olvasási és végrehajtási engedélyeket biztosítanak a tulajdonos csoportnak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad a (z) "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "objektumhoz olvasási és végrehajtási engedélyekkel.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

Az ACL-t rekurzív módon állíthatja be a **DataLakeDirectoryClient.set_access_control_recursive** metódus meghívásával.

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ezek a bejegyzések az olvasási, írási és végrehajtási engedélyeket adják meg a tulajdonos számára, és csak olvasási és végrehajtási engedélyeket biztosítanak a tulajdonos csoportnak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad a (z) "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "objektumhoz olvasási és végrehajtási engedélyekkel.

```python
def set_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>ACL rekurzív frissítése

*Ha módosít egy ACL* -t, akkor az ACL helyett módosítania kell az ACL-t. Hozzáadhat például egy új rendszerbiztonsági tag-t az ACL-hez anélkül, hogy ez hatással lenne az ACL-ben felsorolt többi rendszerbiztonsági tagra is.  Ha a frissítés helyett az ACL-t szeretné cserélni, tekintse meg a jelen cikk [ACL rekurzív beállítása](#set-an-acl-recursively) című szakaszát. 

Az ACL frissítéséhez hozzon létre egy új ACL-objektumot a frissíteni kívánt ACL-bejegyzéssel, majd használja ezt az objektumot az ACL frissítése műveletben. Ne szerezze be a meglévő ACL-t, csak adja meg a frissítendő ACL-bejegyzéseket.

Ez a szakasz példákat tartalmaz az ACL frissítésére.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Frissítsen egy ACL-t rekurzív módon az  **Update-AzDataLakeGen2AclRecursive** parancsmag használatával. 

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

Az ACL-t rekurzív módon frissítheti a **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** metódus meghívásával. 

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít. 

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Az ACL-t rekurzív módon frissítheti a **DataLakeDirectoryClient.update_access_control_recursive** metódus meghívásával. 

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít. 

```python
def update_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>ACL-bejegyzések rekurzív eltávolítása

Egy vagy több ACL-bejegyzést rekurzív módon eltávolíthat. Az ACL-bejegyzések eltávolításához hozzon létre egy új ACL-objektumot az ACL-bejegyzés eltávolításához, majd használja ezt az objektumot az ACL eltávolítása műveletben. Ne szerezze be a meglévő ACL-t, csak adja meg az eltávolítandó ACL-bejegyzéseket. 

Ez a szakasz példákat tartalmaz az ACL eltávolítására.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Távolítsa el az ACL-bejegyzéseket a **Remove-AzDataLakeGen2AclRecursive** parancsmag használatával. 

Ez a példa egy ACL-bejegyzést távolít el a tároló gyökérkönyvtárában.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

### <a name="net"></a>[.NET](#tab/dotnet)

Távolítsa el az ACL-bejegyzéseket a **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** metódus meghívásával. 

Ez a példa egy ACL-bejegyzést távolít el a nevű könyvtár ACL-listájából `my-parent-directory` . 

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Távolítsa el az ACL-bejegyzéseket a **DataLakeDirectoryClient.remove_access_control_recursive** metódus meghívásával. 

Ez a példa egy ACL-bejegyzést távolít el a nevű könyvtár ACL-listájából `my-parent-directory` . 

```python
def remove_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>Helyreállítás hibákból

A futásidejű vagy az engedélyekkel kapcsolatos hibák merülhetnek fel. Futásidejű hibák esetén indítsa újra a folyamatot az elejétől. Engedélyekkel kapcsolatos hibák akkor fordulhatnak elő, ha a rendszerbiztonsági tag nem rendelkezik megfelelő engedélyekkel egy olyan könyvtár vagy fájl hozzáférés-vezérlési listájának módosításához, amely a címtár-hierarchiában van módosítva. Oldja meg az engedélyekkel kapcsolatos problémát, majd a folytatási token használatával folytassa a folyamatot a meghibásodási pontról, vagy indítsa újra a folyamatot az elejétől. A folytatási tokent nem kell használnia, ha az elejéről szeretne újraindulni. Az ACL-bejegyzéseket a negatív hatás nélkül is újra alkalmazhatja.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Eredmény visszaküldése a változónak. A pipe nem tudta bejegyzéseket egy formázott táblába.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

A táblázat kimenete alapján kijavíthatja az engedélyek hibáit, majd folytathatja a végrehajtást a folytatási token használatával.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

## <a name="net"></a>[.NET](#tab/dotnet)

Ez a példa egy folytatási tokent ad vissza egy hiba esetén. Az alkalmazás a hiba megoldását követően újra meghívhatja ezt a metódust, és a folytatási tokent adja át. Ha ezt a példát a rendszer első alkalommal hívja meg, akkor az alkalmazás a ``null`` folytatási jogkivonat paraméter értékét átadja. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, continuationToken: continuationToken, null);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="python"></a>[Python](#tab/python)

Ez a példa egy folytatási tokent ad vissza egy hiba esetén. Az alkalmazás a hiba megoldását követően újra meghívhatja ezt a metódust, és a folytatási tokent adja át. Ha ezt a példát a rendszer első alkalommal hívja meg, akkor az alkalmazás a ``None`` folytatási jogkivonat paraméter értékét átadja. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>További források

Ez a szakasz a kódtárak és a kód mintáinak hivatkozásait tartalmazza.

#### <a name="libraries"></a>Kódtárak

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>Kódminták

- PowerShell: [readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)-  |  [minta](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- NET: [readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [minta](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [minta](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D)

## <a name="best-practice-guidelines"></a>Ajánlott eljárási irányelvek

Ez a szakasz az ACL-ek rekurzív beállítására vonatkozó ajánlott eljárásokat ismerteti. 

#### <a name="handling-runtime-errors"></a>Futásidejű hibák elhárítása

Futásidejű hiba számos okból történhet (például leállás vagy ügyfél-csatlakozási probléma). Ha futásidejű hibát tapasztal, indítsa újra a rekurzív ACL-folyamatot. Az ACL-ek újra alkalmazhatók az elemekre anélkül, hogy negatív hatással lenne. 

#### <a name="handling-permission-errors-403"></a>Engedélyek kezelésére vonatkozó hibák (403)

Ha egy rekurzív ACL-folyamat futtatásakor hozzáférés-vezérlési kivételt tapasztal, előfordulhat, hogy az AD [rendszerbiztonsági tag](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) nem rendelkezik megfelelő engedéllyel ahhoz, hogy ACL-t alkalmazzon egy vagy több alárendelt elemhez a címtár-hierarchiában. Ha egy engedélyezési hiba történik, a folyamat leáll, és a folytatási jogkivonat van megadva. Javítsa ki az engedélyekkel kapcsolatos problémát, majd a folytatási token használatával dolgozza fel a fennmaradó adatkészletet. A már sikeresen feldolgozott címtárakat és fájlokat nem kell újra feldolgozni. Azt is megteheti, hogy újraindítja a rekurzív ACL-folyamatot. Az ACL-ek újra alkalmazhatók az elemekre anélkül, hogy negatív hatással lenne. 

#### <a name="credentials"></a>Hitelesítő adatok 

Azt javasoljuk, hogy hozzon létre egy Azure AD rendszerbiztonsági tag, amely hozzá lett rendelve a [Storage blob-adat tulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepköréhez a cél Storage-fiók vagy tároló hatókörében. 

#### <a name="performance"></a>Teljesítmény 

A késés csökkentése érdekében javasoljuk, hogy futtassa a rekurzív ACL-folyamatot egy olyan Azure-beli virtuális gépen (VM), amely ugyanabban a régióban található, mint a Storage-fiók. 

#### <a name="acl-limits"></a>ACL-korlátok

A címtárra vagy fájlra érvényes ACL-ek maximális száma 32 hozzáférési ACL-ek és 32 alapértelmezett ACL-ek. További információ: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>Visszajelzési vagy jelentési problémák megadása

Megadhatja a visszajelzését, vagy jelenthet problémát a következő címen:  [recursiveACLfeedback@microsoft.com](mailto:recursiveACLfeedback@microsoft.com) .

## <a name="see-also"></a>Lásd még

- [Hozzáférés-vezérlés a 2. generációs Azure Data Lake Storage-ben](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [Ismert problémák](data-lake-storage-known-issues.md)


