---
title: Ismerkedés az Azure Stack tárolásfejlesztési eszközök |} A Microsoft Docs
description: Útmutató az Azure Stack tárolásfejlesztési eszközök használatának első lépései
services: azure-stack
author: mattbriggs
ms.author: mabrigg
ms.date: 12/03/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.openlocfilehash: 56814cc44874fe0c169e5fb51dc6cbff5a225231
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727853"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Ismerkedés az Azure Stack tárolásfejlesztési eszközök

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A Microsoft Azure Stack biztosít egy storage-szolgáltatás, amely tartalmazza a blob, table és queue storage.

Ez a cikk használja útmutatóként tárolásfejlesztési eszközök az Azure Stack használatának első lépéseit. További információt és a mintakódot a megfelelő Azure storage-oktatóanyagok találja.

> [!NOTE]
> Azure Stack storage és az Azure storage, beleértve az egyes platformokra vonatkozó konkrét követelmények közötti különbségek is ismertek. Például vannak bizonyos klienskódtárak és adott végpont utótagja követelmények az Azure Stack. További információkért lásd: [Azure Stack-tároló: Különbségek és szempontok](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Azure-ügyfélkönyvtárak

A tároló ügyfélkódtárai vegye figyelembe a REST API-val kompatibilis verzióra. Meg kell adnia az Azure Stack-végpont a kódban.

### <a name="1811-update-or-newer-versions"></a>1811 frissítés vagy újabb verzió

| Ügyfélkódtár | Az Azure Stack támogatott verziója | Hivatkozás | Végpont-specifikáció |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Nuget-csomagot:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>GitHub-verzió:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | App.config fájlban |
| Java | 6.1.0 | Maven-csomag:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>GitHub-verzió:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Kapcsolati karakterlánc beállítása |
| Node.js | 2.7.0 | Az NPM-hivatkozás:<br>https://www.npmjs.com/package/azure-storage<br>(Futtatás: `npm install azure-storage@2.7.0`)<br> <br>GitHub-verzió:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Szolgáltatás deklarációjában |
| C++ | 3.1.0 | Nuget-csomagot:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>GitHub-verzió:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Kapcsolati karakterlánc beállítása |
| PHP | 1.0.0 | GitHub-verzió:<br>Közös: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Várólista:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Tábla: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Composer-n keresztül telepítse (további információért [lásd a lentebbi részletes](#install-php-client-via-composer---current).) | Kapcsolati karakterlánc beállítása |
| Python | 1.0.0 | GitHub-verzió:<br>Közös:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>BLOB:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Várólista:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Szolgáltatás deklarációjában |
| Ruby | 1.0.1 | RubyGems csomag:<br>Közös:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Várólista: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Tábla: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>GitHub-verzió:<br>Közös: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Várólista: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tábla: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Kapcsolati karakterlánc beállítása |

#### <a name="install-php-client-via-composer---current"></a>Composer - aktuális keresztül PHP-ügyfél telepítése

Composer keresztül történő: (például a blob igénybe).

1. Hozzon létre egy fájlt **composer.json** az alábbi kódot a projekt gyökérkönyvtárában található:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Töltse le [composer.phar](http://getcomposer.org/composer.phar) a projekt gyökérkönyvtárába.
3. Futtatás: `php composer.phar install`.

### <a name="previous-versions-1802-to-1809-update"></a>Korábbi verziók (a 1809 1802-es frissítés)

|Ügyfélkódtár|Az Azure Stack támogatott verziója|Hivatkozás|Végpont-specifikáció|
|---------|---------|---------|---------|
|.NET     |6.2.0|Nuget-csomagot:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>GitHub-verzió:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|App.config fájlban|
|Java|4.1.0|Maven-csomag:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>GitHub-verzió:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Kapcsolati karakterlánc beállítása|
|Node.js     |1.1.0|Az NPM-hivatkozás:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(futtatása: `npm install azure-storage@1.1.0)`<br><br>GitHub-verzió:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Szolgáltatás deklarációjában||C++|2.4.0|Nuget-csomagot:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub-verzió:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Kapcsolati karakterlánc beállítása|
|C++|2.4.0|Nuget-csomagot:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub-verzió:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Kapcsolati karakterlánc beállítása|
|PHP|0.15.0|GitHub-verzió:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Composer keresztül (lásd lejjebb)|Kapcsolati karakterlánc beállítása|
|Python     |0.30.0|A PIP csomag:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Futtatása: `pip install -v azure-storage==0.30.0)`<br><br>GitHub-verzió:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Szolgáltatás deklarációjában|
|Ruby|0.12.1<br>Előzetes verzió|RubyGems csomag:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>GitHub-verzió:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Kapcsolati karakterlánc beállítása|

#### <a name="install-php-client-via-composer---previous"></a>Composer - korábbi keresztül PHP-ügyfél telepítése

Composer keresztül történő: (végezze el a blob példaként).

1. Hozzon létre egy fájlt **composer.json** az alábbi kódot a projekt gyökérkönyvtárában található:

  ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```

2. Töltse le [composer.phar](http://getcomposer.org/composer.phar) a projekt gyökérkönyvtárába.
3. Futtatás: `php composer.phar install`.

## <a name="endpoint-declaration"></a>Végpont deklarációját

Egy Azure Stack-végpont tartalmaz két részből áll: nevét és a egy régióban az Azure Stack tartományhoz.
Az Azure Stack fejlesztői készletének az alapértelmezett végpont az **local.azurestack.external**.
Ha nem biztos a végpont kapcsolatban, forduljon a felhő rendszergazdájához.

## <a name="examples"></a>Példák

### <a name="net"></a>.NET

Azure Stack az app.config fájlban van megadva a végpont utótagja:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Azure Stack a végpont utótagja van megadva a kapcsolati karakterlánc beállítása:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Azure Stack a végpont utótagja a nyilatkozat példányban van megadva:

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Azure Stack a végpont utótagja van megadva a kapcsolati karakterlánc beállítása:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Azure Stack a végpont utótagja van megadva a kapcsolati karakterlánc beállítása:

```php
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Azure Stack a végpont utótagja a nyilatkozat példányban van megadva:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Azure Stack a végpont utótagja van megadva a kapcsolati karakterlánc beállítása:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

Az Azure Blob storage alábbi oktatóanyagok az Azure Stackhez vonatkoznak. Vegye figyelembe az adott végponti utótag követelmény az előző ismertetett Azure Stackhez készült [példák](#examples) szakaszban.

* [Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [How to use Blob storage from Java (A Blob Storage használata Javával)](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [How to use Blob storage from Node.js (A Blob Storage használata Node.js-sel)](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [A c++ segítségével Blob storage használata](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [How to use Blob storage from PHP (A Blob Storage használata PHP-val)](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Azure Blob storage a Python használata](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [How to use Blob storage from Ruby (A Blob Storage használata Rubyval)](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue Storage

Az Azure Queue storage alábbi oktatóanyagok az Azure Stackhez vonatkoznak. Vegye figyelembe az adott végponti utótag követelmény az előző ismertetett Azure Stackhez készült [példák](#examples) szakaszban.

* [Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [How to use Queue Storage from Java (A Queue Storage használata Javával)](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [How to use Queue storage from Node.js (A Queue Storage használata Node.js-sel)](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [A c++ segítségével a Queue storage használata](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [How to use Queue storage from PHP (A Queue Storage használata PHP-val)](../../storage/queues/storage-php-how-to-use-queues.md)
* [How to use Queue storage from Python (A Queue Storage használata Pythonnal)](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [How to use Queue storage from Ruby (A Queue Storage használata Rubyval)](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)

## <a name="table-storage"></a>Table Storage

Az Azure Table storage alábbi oktatóanyagok az Azure Stackhez vonatkoznak. Vegye figyelembe az adott végponti utótag követelmény az előző ismertetett Azure Stackhez készült [példák](#examples) szakaszban.

* [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [How to use Table storage from Java (A Table Storage használata Javával)](../../cosmos-db/table-storage-how-to-use-java.md)
* [Az Azure Table Storage használata Node.js-sel](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [A c++ segítségével a Table storage használata](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [How to use Table storage from PHP (A Table Storage használata PHP-val)](../../cosmos-db/table-storage-how-to-use-php.md)
* [A Table storage használata a Pythonban](../../cosmos-db/table-storage-how-to-use-python.md)
* [How to use Table storage from Ruby (A Table Storage használata Rubyval)](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>További lépések

* [A Microsoft Azure storage bemutatása](../../storage/common/storage-introduction.md)
