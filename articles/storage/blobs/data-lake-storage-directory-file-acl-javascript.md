---
title: JavaScript használata fájlok & ACL-ekkel Azure Data Lake Storage Gen2 (előzetes verzió)
description: Az Azure Storage Data Lake ügyféloldali függvénytárának használatával kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.service: storage
ms.date: 12/18/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 8fd63adc76422b7fd9978e626208aa90593f8604
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154867"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>A JavaScript használata könyvtárak, fájlok és ACL-ek kezeléséhez Azure Data Lake Storage Gen2 (előzetes verzió)

Ez a cikk bemutatja, hogyan lehet a JavaScript használatával könyvtárakat, fájlokat és engedélyeket létrehozni és kezelni olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS). 

> [!IMPORTANT]
> A cikkben szereplő JavaScript-kódtár jelenleg nyilvános előzetes verzióban érhető el.

[Csomag (node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.
> * Ha ezt a csomagot egy Node. js-alkalmazásban használja, akkor a Node. js 8.0.0 vagy újabb verziójára lesz szüksége.

## <a name="set-up-your-project"></a>A projekt beállítása

Telepítse Data Lake ügyféloldali kódtárat a JavaScripthez egy terminál ablak megnyitásával, majd írja be a következő parancsot.

```javascript
npm install @azure/storage-file-datalake
```

Importálja a `storage-file-datalake` csomagot úgy, hogy az utasítást a kódlap elejére helyezi. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz 

A cikkben szereplő kódrészletek használatához létre kell hoznia egy **DataLakeServiceClient** -példányt, amely a Storage-fiókot jelképezi. A legegyszerűbb módja, ha egy fiókot használ. 

Ez a példa a **DataLakeServiceClient** egy példányát hozza létre a fiók kulcsa alapján.

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
> Ez az engedélyezési módszer csak Node. js-alkalmazásokhoz használható. Ha a kódot egy böngészőben szeretné futtatni, akkor a Azure Active Directory (AD) használatával engedélyezheti. Ennek módjáról az [Azure Storage-fájl data Lake az ügyféloldali kódtár a JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) -fájlhoz című témakörben talál útmutatást. 

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer tárolóként működik a fájlok számára. Hozzon létre egyet egy **FileSystemClient** -példány beszerzésével, majd hívja meg a **FileSystemClient. Create** metódust.

Ez a példa egy `my-file-system`nevű fájlrendszert hoz létre. 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-referenciát egy **DirectoryClient** -példány beszerzésével, majd hívja meg a **DirectoryClient. Create** metódust.

Ez a példa egy `my-directory` nevű könyvtárat helyez el egy fájlrendszerhez. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a **DirectoryClient. Rename** metódus meghívásával. Adja meg a kívánt könyvtár elérési útját (a paramétert). 

Ez a példa egy alkönyvtárat nevez át a `my-directory-renamed`névre.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Ez a példa egy `my-directory-renamed` nevű könyvtárat helyez át egy `my-directory-2`nevű könyvtár alkönyvtárába. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Könyvtár törlése

A **DirectoryClient. Delete** metódus meghívásával törölhet egy könyvtárat.

Ez a példa töröl egy `my-directory`nevű könyvtárat.   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Címtár-ACL kezelése

Ez a példa lekéri és beállítja a `my-directory`nevű könyvtár ACL-listáját. Ez a példa az olvasási, írási és végrehajtási engedélyeket adja meg, így a tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik, és minden más olvasási hozzáférést biztosít.

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

Először olvassa el a fájlt. Ez a példa a Node. js `fs` modult használja. Ezután hozzon létre egy fájlt a **FileClient** -példány létrehozásával, majd hívja meg a **FileClient. Create** metódust. Töltsön fel egy fájlt a **FileClient. Append** metódus meghívásával. Ügyeljen arra, hogy a feltöltést a **FileClient. flush** metódus meghívásával végezze el.

Ez a példa egy szövegfájlt tölt fel egy `my-directory`nevű könyvtárba.

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

Ez a példa lekérdezi és beállítja a `upload-file.txt`nevű fájl hozzáférés-vezérlési listáját. Ez a példa az olvasási, írási és végrehajtási engedélyeket adja meg, így a tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik, és minden más olvasási hozzáférést biztosít.

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

Először hozzon létre egy **FileSystemClient** -példányt, amely a letölteni kívánt fájlt jelöli. A fájl olvasásához használja a **FileSystemClient. Read** metódust. Ezután írja be a fájlt. Ebben a példában a Node. js `fs` modult használja. 

> [!NOTE]
> A fájlok letöltésének módja csak Node. js-alkalmazásokhoz használható. Ha azt tervezi, hogy futtatja a kódot egy böngészőben, tekintse meg az [Azure Storage-fájl data Lake az ügyféloldali kódtár JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) -fájlhoz című témakört, amelyből megtudhatja, hogyan teheti ezt meg egy böngészőben. 

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

Ez a példa kinyomtatja az egyes könyvtárak és fájlok nevét, amelyek egy `my-directory`nevű könyvtárban találhatók.

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
* [Minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)