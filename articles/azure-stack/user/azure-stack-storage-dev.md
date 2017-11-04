---
title: "Ismerkedés az Azure Storage-verem Fejlesztőeszközök"
description: "Ismerkedés az Azure Storage-verem fejlesztői eszközök segítségével útmutató"
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 9/25/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.openlocfilehash: 5b2898c64c0f1b5d804e63fa4e4e1218fa7a672c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Ismerkedés az Azure Storage-verem Fejlesztőeszközök

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*


A Microsoft Azure verem tárolószolgáltatásokra, köztük az Azure Blob, Table és Queue storage biztosít.

Ez a cikk útmutatást gyors indítása az Azure Storage-verem fejlesztői eszközök segítségével. További részletes információk és mintakódját, valamint a megfelelő Azure Storage oktatóanyagok találja.

Azure Storage és az Azure verem tároló, beleértve az egyes konkrét követelmények az egyes platformokon különbségei ismertek. Például nincsenek adott ügyféloldali kódtáraknál és az adott végponti utótag követelmények Azure verem. További információkért lásd: [Azure verem Storage: szempontok és a különbségeket](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Azure ügyfélkódtárai
A támogatott REST API-t az Azure Storage-verem rendszer 2015-04-05. Az Azure Storage REST API legújabb verziójának teljes paritás nem tartalmaz. Így a storage ügyfélkódtáraival kell ismernie, amely kompatibilis a REST API-t 2015-04-05-ös verziója.


|Ügyfélkódtár|A verem használható az Azure-verzió|Hivatkozás|Végpont meghatározása|
|---------|---------|---------|---------|
|.NET     |6.2.0|Nuget-csomagot:<br>[https://www.nuget.org/Packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>GitHub-kiadás:<br>[https://github.com/Azure/Azure-Storage-NET/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|App.config fájlban|
|Java|4.1.0|Maven csomag:<br>[http://mvnrepository.com/artifact/com.microsoft.Azure/Azure-Storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>GitHub-kiadás:<br> [https://github.com/Azure/Azure-Storage-Java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Kapcsolati karakterlánc beállítása|
|Node.js     |1.1.0|NPM hivatkozásra:<br>[https://www.npmjs.com/Package/Azure-Storage](https://www.npmjs.com/package/azure-storage)<br>(futtatása:`npm install azure-storage@1.1.0)`<br><br>Github-kiadás:<br>[https://github.com/Azure/Azure-Storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Szolgáltatás deklarációjában||C++|2.4.0|Nuget-csomagot:<br>[https://www.nuget.org/Packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub-kiadás:<br>[https://github.com/Azure/Azure-Storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Kapcsolati karakterlánc beállítása|
|C++|2.4.0|Nuget-csomagot:<br>[https://www.nuget.org/Packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub-kiadás:<br>[https://github.com/Azure/Azure-Storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Kapcsolati karakterlánc beállítása|
|PHP|0.15.0|GitHub-kiadás:<br>[https://github.com/Azure/Azure-Storage-php/releases/tag/V0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Szerkesztő keresztül (lásd alább a részletekre)|Kapcsolati karakterlánc beállítása|
|Python     |0.30.0|A PIP csomag:<br> [https://pypi.Python.org/pypi/Azure-Storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Futtatása:`pip install -v azure-storage==0.30.0)`<br><br>GitHub-kiadás:<br> [https://github.com/Azure/Azure-Storage-Python/releases/tag/V0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Szolgáltatás deklarációjában|
|Ruby|0.12.1<br>Előzetes verzió|RubyGems csomag:<br> [https://rubygems.org/GEMS/Azure-Storage/versions/0.12.1.Preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>GitHub-kiadás:<br> [https://github.com/Azure/Azure-Storage-Ruby/releases/tag/V0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Kapcsolati karakterlánc beállítása|

> [!NOTE]
> PHP-részletek<br><br>
>Keresztül történő szerkesztő:
>1. Hozzon létre egy fájlt `composer.json` a következő kóddal projekt gyökérkönyvtárában található:<br>
>
>   ```
>   {
>       "require":{
>           "Microsoft/azure-storage":"0.15.0"
>        }
>    }
>   ```
>
>2. Töltse le [composer.phar](http://getcomposer.org/composer.phar) be a projekt legfelső szintű.
>3. Futtatás: `php composer.phar install`.
>


## <a name="endpoint-declaration"></a>Végpont deklarációját
A végpont Azure verem tartalmazza a két részből áll: egy régiót és az Azure-verem tartomány nevét.
A csomagban Azure verem, az alapértelmezett végpont esetében **local.azurestack.external**.
Ha nem biztos a végpont kapcsolatban, forduljon a felhő rendszergazdájához.

## <a name="examples"></a>Példák


### <a name="net"></a>.NET

Azure-vermet az app.config fájlban van megadva a végpont-utótag:

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```
### <a name="java"></a>Java

Azure-vermet a végpont-utótag van megadva a kapcsolati karakterlánc beállítása:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Azure verem deklaráció példány van megadva a végpont utótag:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```
### <a name="c"></a>C++

Azure-vermet a végpont-utótag van megadva a kapcsolati karakterlánc beállítása:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Azure-vermet a végpont-utótag van megadva a kapcsolati karakterlánc beállítása:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Azure verem deklaráció példány van megadva a végpont utótag:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```
### <a name="ruby"></a>Ruby

Azure-vermet a végpont-utótag van megadva a kapcsolati karakterlánc beállítása:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

Az Azure Blob storage anyagra Azure verem vonatkoznak. Vegye figyelembe az adott végponti utótag követelmény az előző leírt Azure verem [példák](#examples) szakasz.

* [Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [How to use Blob storage from Java (A Blob Storage használata Javával)](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Hogyan a Blob storage-ának Node.js használata]... /.. / storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Blob storage-ának C++ használata](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [How to use Blob storage from PHP (A Blob Storage használata PHP-val)](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Azure Blob storage-ának Python használata](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [How to use Blob storage from Ruby (A Blob Storage használata Rubyval)](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue Storage

Az Azure Queue storage anyagra Azure verem vonatkoznak. Vegye figyelembe az adott végponti utótag követelmény az előző leírt Azure verem [példák](#examples) szakasz.

* [Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [How to use Queue Storage from Java (A Queue Storage használata Javával)](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [How to use Queue storage from Node.js (A Queue Storage használata Node.js-sel)](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [A C++ a Queue storage használata](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [How to use Queue storage from PHP (A Queue Storage használata PHP-val)](../../storage/queues/storage-php-how-to-use-queues.md)
* [How to use Queue storage from Python (A Queue Storage használata Pythonnal)](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [How to use Queue storage from Ruby (A Queue Storage használata Rubyval)](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)


## <a name="table-storage"></a>Table Storage

A következő Azure Table storage oktatóprogramok találhatók Azure verem alkalmazható. Vegye figyelembe az adott végponti utótag követelmény az előző leírt Azure verem [példák](#examples) szakasz.

* [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [How to use Table storage from Java (A Table Storage használata Javával)](../../cosmos-db/table-storage-how-to-use-java.md)
* [Node.js-ből a Azure Table storage használata](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [A C++ a Table storage használata](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [How to use Table storage from PHP (A Table Storage használata PHP-val)](../../cosmos-db/table-storage-how-to-use-php.md)
* [A Table storage használata a Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [How to use Table storage from Ruby (A Table Storage használata Rubyval)](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Következő lépések

* [A Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md)