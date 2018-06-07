---
title: Ismerkedés az Azure-verem tárolási Fejlesztőeszközök |} Microsoft Docs
description: Útmutató a verem Azure tárolás fejlesztői eszközök használatával
services: azure-stack
author: mabriggs
ms.author: mabrigg
ms.date: 05/21/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.openlocfilehash: 0ceda393412f8217a893a347ec5f3a9ac03efa3d
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604477"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Ismerkedés az Azure-verem tárolási Fejlesztőeszközök

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A Microsoft Azure verem tárolási szolgáltatások olyan készlete, amely tartalmazza a blob, table és a queue storage biztosít.

Ez a cikk használja útmutatóként verem Azure tárolás fejlesztői eszközök használatának megkezdése. További részletes információk és mintakódját, valamint a megfelelő az Azure storage oktatóanyagok találja.

> [!NOTE]  
> Azure verem tárolási és az Azure-tárolót, beleértve a különleges követelményeket az egyes platformokon különbségei ismertek. Például nincsenek adott ügyféloldali kódtáraknál és az adott végponti utótag követelmények Azure verem. További információkért lásd: [verem Azure storage: szempontok és a különbségeket](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Azure ügyfélkódtárai

A verem Azure storage REST API verziók a következők: 2017-04-17, 2016-05-31, 2015-12-11, 2015-07-08, 2015-04-05 1802 frissítés vagy újabb verziók és 2015-04-05 korábbi verzióihoz. Az Azure-verem végpontok nem rendelkezik teljes paritásos az Azure storage REST API-t a legújabb verziójával. A storage ügyfélkódtáraival kell figyelembe vennie a REST API-val kompatibilis verzióra.

### <a name="1802-update-or-newer-versions"></a>1802 frissítés vagy újabb verzió

| Ügyfélkódtár | A verem használható az Azure-verzió | Hivatkozás | Végpont meghatározása |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Nuget-csomagot:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>GitHub-kiadás:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | App.config fájlban |
| Java | 6.1.0 | Maven csomag:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>GitHub-kiadás:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Kapcsolati karakterlánc beállítása |
| Node.js | 2.7.0 | NPM hivatkozásra:<br>https://www.npmjs.com/package/azure-storage<br>(Futtatás: `npm install azure-storage@2.7.0`)<br> <br>Github-kiadás:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Szolgáltatás deklarációjában |
| C++ | 3.1.0 | Nuget-csomagot:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>GitHub-kiadás:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Kapcsolati karakterlánc beállítása |
| PHP | 1.0.0 | GitHub-kiadás:<br>Közös: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Várólista:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Tábla: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Szerkesztő keresztül (további, [a részleteit lásd alább](#install-php-client-via-composer---current).) | Kapcsolati karakterlánc beállítása |
| Python | 1.0.0 | GitHub-kiadás:<br>Közös:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>BLOB:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Várólista:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Szolgáltatás deklarációjában |
| Ruby | 1.0.1 | RubyGems csomag:<br>Közös:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Várólista: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Tábla: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>GitHub-kiadás:<br>Közös: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Várólista: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tábla: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Kapcsolati karakterlánc beállítása |

#### <a name="install-php-client-via-composer---current"></a>Keresztül szerkesztő - aktuális PHP-ügyfél telepítése

Szerkesztő keresztül történő: (hajtsa végre a megfelelő blob példaként).

1. Hozzon létre egy fájlt **composer.json** a következő kóddal projekt gyökérkönyvtárában található:

  ```php
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```

2. Töltse le [composer.phar](http://getcomposer.org/composer.phar) a projekt legfelső szintű.
3. Futtatás: `php composer.phar install`.

### <a name="previous-versions"></a>Korábbi verziók

|Ügyfélkódtár|A verem használható az Azure-verzió|Hivatkozás|Végpont meghatározása|
|---------|---------|---------|---------|
|.NET     |6.2.0|Nuget-csomagot:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>GitHub-kiadás:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|App.config fájlban|
|Java|4.1.0|Maven csomag:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>GitHub-kiadás:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Kapcsolati karakterlánc beállítása|
|Node.js     |1.1.0|NPM hivatkozásra:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(futtatása: `npm install azure-storage@1.1.0)`<br><br>Github-kiadás:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Szolgáltatás deklarációjában||C++|2.4.0|Nuget-csomagot:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub-kiadás:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Kapcsolati karakterlánc beállítása|
|C++|2.4.0|Nuget-csomagot:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub-kiadás:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Kapcsolati karakterlánc beállítása|
|PHP|0.15.0|GitHub-kiadás:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Szerkesztő keresztül (lásd alább a részletekre)|Kapcsolati karakterlánc beállítása|
|Python     |0.30.0|A PIP csomag:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Futtatása: `pip install -v azure-storage==0.30.0)`<br><br>GitHub-kiadás:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Szolgáltatás deklarációjában|
|Ruby|0.12.1<br>Előzetes verzió|RubyGems csomag:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>GitHub-kiadás:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Kapcsolati karakterlánc beállítása|

#### <a name="install-php-client-via-composer---previous"></a>Telepítse a PHP client keresztül szerkesztő – előző

Keresztül történő szerkesztő:

1. Hozzon létre egy fájlt **composer.json** a következő kóddal projekt gyökérkönyvtárában található:

  ```php
    {
          "require":{
          "Microsoft/azure-storage":"0.15.0"
          }
    }
  ```

2. Töltse le [composer.phar](http://getcomposer.org/composer.phar) be a projekt legfelső szintű.
3. Futtatás: `php composer.phar install`.

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
* [How to use Blob storage from Node.js (A Blob Storage használata Node.js-sel)](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
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
* [Az Azure Table Storage használata Node.js-sel](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [A C++ a Table storage használata](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [How to use Table storage from PHP (A Table Storage használata PHP-val)](../../cosmos-db/table-storage-how-to-use-php.md)
* [A Table storage használata a Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [How to use Table storage from Ruby (A Table Storage használata Rubyval)](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>További lépések

* [A Microsoft Azure storage bemutatása](../../storage/common/storage-introduction.md)