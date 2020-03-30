---
title: JavaScript használata fájlokhoz & AC-khez az Azure Data Lake Storage Gen2 szolgáltatásban
description: Használja az Azure Storage Data Lake javascript-ügyfélkönyvtárát a könyvtárak és a fájl- és címtárhozzáférés-vezérlési listák (ACL) kezeléséhez a hierarchikus névtérrel (HNS) engedélyezve lévő tárfiókokban.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061541"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Könyvtárak, fájlok és AC-k kezelése a JavaScript használatával az Azure Data Lake Storage Gen2 szolgáltatásban

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet könyvtárakat, fájlokat és engedélyeket a hierarchikus névtérrel (HNS) rendelkező tárfiókokban a JavaScript használatával. 

[Csomag (node package manager)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [visszajelzést adnak](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan tárfiók, amelynek hierarchikus névtere (HNS) engedélyezve van. Az [alábbi](data-lake-storage-quickstart-create-account.md) utasításokat követve hozzon létre egyet.
> * Ha ezt a csomagot node.js alkalmazásban használja, szüksége lesz a Node.js 8.0.0 vagy újabb verzióra.

## <a name="set-up-your-project"></a>A projekt beállítása

Telepítse a Data Lake javascript-es ügyfélkönyvtárát egy terminálablak megnyitásával, majd írja be a következő parancsot.

```javascript
npm install @azure/storage-file-datalake
```

Importálja `storage-file-datalake` a csomagot úgy, hogy ezt a nyilatkozatot a kódfájl tetejére helyezi. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Csatlakozás a fiókhoz 

A jelenések ebben a cikkben, létre kell hoznia egy **DataLakeServiceClient** példányt, amely a tárfiókot jelöli. 

### <a name="connect-by-using-an-account-key"></a>Csatlakozás fiókkulcs használatával

Ez a legegyszerűbb módja annak, hogy csatlakozzon egy fiókhoz. 

Ez a példa egy **DataLakeServiceClient-példányt** hoz létre egy fiókkulcs használatával.

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
> Ez az engedélyezési módszer csak a Node.js alkalmazások esetében működik. Ha azt tervezi, hogy futtassa a kódot a böngészőben, engedélyezheti az Azure Active Directory (AD) használatával. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Csatlakozás az Azure Active Directory (AD) használatával

Használhatja az [Azure identity ügyfélkódtár JS](https://www.npmjs.com/package/@azure/identity) az alkalmazás hitelesítéséhez az Azure AD.You can use the Azure identity client library for JS to authenticate your application with Azure AD.

Ez a példa egy **DataLakeServiceClient-példányt** hoz létre egy ügyfélazonosító, egy ügyféltitok és egy bérlői azonosító használatával.  Ezeknek az értékeknek a [beszerzése, olvassa el a jogkivonat beszerzése az Azure AD-től az ügyfélalkalmazásból érkező kérelmek engedélyezéséhez](../common/storage-auth-aad-app.md)című témakört.

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> További példák: az [Azure identity client library for JS](https://www.npmjs.com/package/@azure/identity) dokumentáció.

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer a fájlok tárolójaként működik. Létrehozhat egyet egy **FileSystemClient** példány beszerzésével, majd a **FileSystemClient.Create** metódus hívásával.

Ez a példa létrehoz `my-file-system`egy fájlrendszert. 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

**DirectoryClient-példány** beszerzésével hozzon létre könyvtárhivatkozást, majd hívja meg a **DirectoryClient.create** metódust.

Ez a példa `my-directory` egy fájlrendszerhez nevezett könyvtárat ad hozzá. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Könyvtár átnevezése vagy áthelyezése

Könyvtár átnevezése vagy áthelyezése a **DirectoryClient.rename** metódus meghívásával. Adja át a kívánt könyvtár elérési útját egy paraméternek. 

Ez a példa átnevezi az `my-directory-renamed`alkönyvtárat a nevére.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Ez a példa `my-directory-renamed` áthelyezi a névvel ellátott `my-directory-2`könyvtárat egy könyvtár alkönyvtárába. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Könyvtár törlése

Könyvtár törlése a **DirectoryClient.delete** metódus hívásával.

Ez a példa törli `my-directory`a .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Címtár ACL kezelése

Ez a példa bekerül, majd beállítja a névvel ellátott könyvtár ACL-ét. `my-directory` Ebben a példában a tulajdonában lévő felhasználó olvasási, írási és végrehajtási engedélyeket ad, a tulajdonában lévő csoport csak olvasási és végrehajtási engedélyeket ad, és az összes többi olvasási hozzáférést biztosít.

> [!NOTE]
> Ha az alkalmazás engedélyezi a hozzáférést az Azure Active Directory (Azure AD) használatával, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá van rendelve a [Storage Blob Data Owner szerepkörhöz.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Ha többet szeretne tudni az ACL-engedélyek alkalmazásáról és módosításuk hatásairól, olvassa el [a Hozzáférés-vezérlés az Azure Data Lake Storage Gen2 alkalmazásban című témakört.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

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

Először olvassa el az aktát. Ez a példa a Node.js modult `fs` használja. Ezután hozzon létre egy fájlhivatkozást a célkönyvtárban egy **FileClient** példány létrehozásával, majd hívja meg a **FileClient.create** metódust. Töltsön fel egy fájlt a **FileClient.append** metódus hívásával. Győződjön meg arról, hogy a feltöltést a **FileClient.flush** metódus hívásával fejezi be.

Ez a példa egy .' `my-directory`nevű könyvtárba tölt fel egy szövegfájlt.

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

## <a name="manage-a-file-acl"></a>Fájl acl kezelése

Ez a példa bekerül, majd beállítja a fájl acl nevű `upload-file.txt`. Ebben a példában a tulajdonában lévő felhasználó olvasási, írási és végrehajtási engedélyeket ad, a tulajdonában lévő csoport csak olvasási és végrehajtási engedélyeket ad, és az összes többi olvasási hozzáférést biztosít.

> [!NOTE]
> Ha az alkalmazás engedélyezi a hozzáférést az Azure Active Directory (Azure AD) használatával, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá van rendelve a [Storage Blob Data Owner szerepkörhöz.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Ha többet szeretne tudni az ACL-engedélyek alkalmazásáról és módosításuk hatásairól, olvassa el [a Hozzáférés-vezérlés az Azure Data Lake Storage Gen2 alkalmazásban című témakört.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

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

## <a name="download-from-a-directory"></a>Letöltés könyvtárból

Először hozzon létre egy **FileSystemClient** példányt, amely a letölteni kívánt fájlt jelöli. A fájl olvasásához használja a **FileSystemClient.read** metódust. Ezután írja meg a fájlt. Ez a példa a Node.js modult `fs` használja ehhez. 

> [!NOTE]
> Ez a fájlletöltési módszer csak node.js alkalmazások esetén működik. Ha azt tervezi, hogy futtassa a kódot a böngészőben, tekintse meg az [Azure Storage File Data Lake ügyfélkönyvtár JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) readme fájlt egy példa, hogyan kell ezt a böngészőben. 

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

Ebben a példában kinyomtatja a könyvtárban található könyvtárak `my-directory`és fájlok nevét.

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

* [Package (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)