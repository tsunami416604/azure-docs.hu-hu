---
title: A JavaScript használata fájlok & ACL-ekkel Azure Data Lake Storage Gen2
description: Az Azure Storage Data Lake ügyféloldali függvénytárának használatával kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: c774d3f56eaf666a31ff73f433a3b4a5a363ce2f
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142488"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>A JavaScript használata a címtárak, a fájlok és a hozzáférés-vezérlési listák kezeléséhez Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan lehet a JavaScript használatával könyvtárakat, fájlokat és engedélyeket létrehozni és kezelni olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS). 

[Csomag (node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Példák](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.
> * Ha ezt a csomagot egy Node.js alkalmazásban használja, Node.js 8.0.0 vagy újabb verzióra van szüksége.

## <a name="set-up-your-project"></a>A projekt beállítása

Telepítse Data Lake ügyféloldali kódtárat a JavaScripthez egy terminál ablak megnyitásával, majd írja be a következő parancsot.

```javascript
npm install @azure/storage-file-datalake
```

Importálja a `storage-file-datalake` csomagot úgy, hogy a fájl elejére helyezi ezt az utasítást. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz 

A cikkben szereplő kódrészletek használatához létre kell hoznia egy **DataLakeServiceClient** -példányt, amely a Storage-fiókot jelképezi. 

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra. 

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre a fiók kulcsa alapján.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Ez az engedélyezési módszer csak Node.js alkalmazásokhoz működik. Ha a kódot egy böngészőben szeretné futtatni, akkor a Azure Active Directory (AD) használatával engedélyezheti. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Kapcsolat Azure Active Directory (AD) használatával

Az [Azure Identity Client Library for js](https://www.npmjs.com/package/@azure/identity) használatával hitelesítheti alkalmazását az Azure ad-vel.

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  Az értékek [lekéréséhez lásd: token beszerzése az Azure ad-ből az ügyfélalkalmazások kéréseinek engedélyezéséhez](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> További példákért tekintse meg az [Azure Identity Client Library for js](https://www.npmjs.com/package/@azure/identity) dokumentációját.

## <a name="create-a-container"></a>Tároló létrehozása

A tároló fájlrendszerként működik a fájlok számára. Hozzon létre egyet egy **FileSystemClient** -példány beszerzésével, majd hívja meg a **FileSystemClient. Create** metódust.

Ez a példa egy nevű tárolót hoz létre `my-file-system` . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-referenciát egy **DirectoryClient** -példány beszerzésével, majd hívja meg a **DirectoryClient. Create** metódust.

Ez a példa egy nevű könyvtárat helyez `my-directory` el egy tárolóhoz. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a **DirectoryClient. Rename** metódus meghívásával. Adja meg a kívánt könyvtár elérési útját (a paramétert). 

Ez a példa átnevez egy alkönyvtárat a névre `my-directory-renamed` .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Ez a példa egy nevű könyvtárat helyez át egy nevű `my-directory-renamed` könyvtár alkönyvtárába `my-directory-2` . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Könyvtár törlése

A **DirectoryClient. Delete** metódus meghívásával törölhet egy könyvtárat.

Ez a példa törli a nevű könyvtárat `my-directory` .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Címtár-ACL kezelése

Ez a példa lekéri, majd beállítja a nevű könyvtár ACL-listáját `my-directory` . Ez a példa az olvasási, írási és végrehajtási engedélyeket adja meg, így a tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik, és minden más olvasási hozzáférést biztosít.

> [!NOTE]
> Ha az alkalmazás a Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ismertető témakört.

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Először olvassa el a fájlt. Ez a példa a Node.js `fs` modult használja. Ezután hozzon létre egy fájlt a **FileClient** -példány létrehozásával, majd hívja meg a **FileClient. Create** metódust. Töltsön fel egy fájlt a **FileClient. Append** metódus meghívásával. Ügyeljen arra, hogy a feltöltést a **FileClient. flush** metódus meghívásával végezze el.

Ez a példa egy szövegfájlt tölt fel egy nevű könyvtárba `my-directory` . "

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>Fájl hozzáférés-vezérlési listájának kezelése

Ez a példa lekéri, majd beállítja a nevű fájl ACL-listáját `upload-file.txt` . Ez a példa az olvasási, írási és végrehajtási engedélyeket adja meg, így a tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik, és minden más olvasási hozzáférést biztosít.

> [!NOTE]
> Ha az alkalmazás a Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ismertető témakört.

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>Letöltés egy címtárból

Először hozzon létre egy **FileSystemClient** -példányt, amely a letölteni kívánt fájlt jelöli. A fájl olvasásához használja a **FileSystemClient. Read** metódust. Ezután írja be a fájlt. Ebben a példában a Node.js `fs` modult használja. 

> [!NOTE]
> A fájlok letöltésének módja csak Node.js alkalmazásokhoz használható. Ha azt tervezi, hogy futtatja a kódot egy böngészőben, tekintse meg az [Azure Storage-fájl data Lake az ügyféloldali kódtár JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) -fájlhoz című témakört, amelyből megtudhatja, hogyan teheti ezt meg egy böngészőben. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Ez a példa a nevű könyvtárban található minden könyvtár és fájl nevét kiírja `my-directory` .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Lásd még

* [Csomag (node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Példák](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)