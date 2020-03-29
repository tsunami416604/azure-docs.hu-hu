---
title: A Microsoft Azure Data Box Blob tárolási követelményei| Microsoft dokumentumok
description: Ismerje meg az Azure Data Box Blob storage API-k, SDK-k és ügyfélkódtárak támogatott verzióit
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436494"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Az Azure Data Box Blob tárolási követelményei

Ez a cikk az Azure API-k, az Azure-ügyfélkódtárak és a Data Box Blob storage által támogatott eszközök verzióit sorolja fel. A Data Box Blob storage az Azure-konzisztens szemantikával biztosítja a blobkezelési funkciókat. Ez a cikk is összefoglalja az ismert Azure Data Box Blob tárolási különbségek az Azure Storage-szolgáltatások.

Azt javasoljuk, hogy figyelmesen tekintse át az információkat, mielőtt csatlakozna a Data Box Blob storage-hoz, majd szükség szerint hivatkozzon rá.


## <a name="storage-differences"></a>Tárolási különbségek

|     Szolgáltatás                                             |     Azure Storage                                     |     Data Box Blob Storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    A felhőalapú SMB-fájlmegosztások támogatottak              |    Nem támogatott      |
|    Szolgáltatástitkosítás az inaktív adatokhoz                  |    256 bites AES titkosítás                             |    256 bites AES titkosítás |
|    Tárfiók típusa                                 |    Általános célú és Azure blobstorage-fiókok    |    Csak általános célú v1|
|    A blob neve                                            |    1024 karakter (2048 bájt)                     |    880 karakter (1760 bájt)|
|    Blob maximális méretének blokkolása                              |    4,75 TB (100 MB X 50 000 blokk)                   |    4,75 TB (100 MB x 50 000 blokk) az Azure Data Box 1.8-tól kezdődően.|
|    Lapblob maximális mérete                               |    8 TB                                               |    1 TB                   |
|    Lapblob-oldal méret                                  |    512 bájt                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Támogatott API-verziók

Az Azure Storage szolgáltatás API-k következő verzióit támogatja a Data Box Blob storage:

Azure Data Box 1.8-tól

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017. 4. 17.](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Támogatott Azure-ügyfélkönyvtárak

A Data Box Blob storage, vannak adott ügyfélkódtárak és a megadott végpont-utótag követelmények. A Data Box Blob storage-végpontok nem rendelkeznek teljes paritást az Azure Blob Storage REST API legújabb verziójával, tekintse meg az [Azure Data Box 1.8 támogatott verzióit.](#supported-api-versions) A tárolóügyfél-kódtárak esetében tisztában kell lennie a REST API-val kompatibilis verzióval.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8-tól

| Ügyfélkódtár     |Data Box Blob storage támogatott verzió     | Hivatkozás   |     Végpont specifikációja      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Nuget csomag:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub-kiadás:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config fájl                 |
|    Java                |    7.0.0                                           |    Maven csomag:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub-kiadás:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Kapcsolati karakterlánc beállítása         |
|    Node.js             |    2.8.3                                           |    NPM hivatkozás: https://www.npmjs.com/package/azure-storage (Futtatás: `npm install azure-storage@2.7.0`)   <br>GitHub-kiadás:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Szolgáltatáspéldány-deklaráció    |
|    C++                 |    5.2.0                                           |    Nuget csomag:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub-kiadás:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Kapcsolati karakterlánc beállítása         |
|    PHP                 |    1.2.0                                           |    GitHub-kiadás:<br>Közös:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Telepítse keresztül Zeneszerző (További információért, Lásd a részleteket alább.)                                                                                                             |    Kapcsolati karakterlánc beállítása         |
|    Python              |    1.1.0                                           |    GitHub-kiadás:<br>Közös:https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob:https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Szolgáltatáspéldány-deklaráció    |
|    Ruby                |    1.0.1                                           |    RubyGems csomag:<br>Közös:https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob:https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub-kiadás:<br>Közös:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Kapcsolati karakterlánc beállítása         |



### <a name="install-php-client-via-composer---current"></a>PHP kliens telepítése composer-en keresztül - aktuális

A következő tördelőn keresztültörténő telepítéshez: (vegyük például a blobot).
1. Hozzon létre egy composer.json nevű fájlt a projekt gyökerében a következő kóddal:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Töltse `composer.phar` le a projekt gyökér.

3. Futtatás: php composer.phar install.

### <a name="endpoint-declaration"></a>Végponti deklaráció

Az Azure Data Box Blob storage-végpont két részből áll: egy régió nevét és a Data Box tartomány. A Data Box Blob storage SDK-ban `\<serial no. of the device>.microsoftdatabox.com`az alapértelmezett végpont a.  A blobszolgáltatás végpontjának további információiért nyissa meg a [Csatlakozás a Data Box Blob storage-on keresztül című területet.](data-box-deploy-copy-data-via-rest.md)
 
## <a name="examples"></a>Példák

### <a name="net"></a>.NET

A Data Box Blob storage esetében a végpontutótag `app.config` a fájlban van megadva:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

A Data Box Blob storage esetében a végpontutótag a kapcsolati karakterlánc beállításában van megadva:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

A Data Box Blob storage esetében a végpontutótag a deklarációs példányban van megadva:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

A Data Box Blob storage esetében a végpontutótag a kapcsolati karakterlánc beállításában van megadva:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

A Data Box Blob storage esetében a végpontutótag a kapcsolati karakterlánc beállításában van megadva:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

A Data Box Blob storage esetében a végpontutótag a deklarációs példányban van megadva:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

A Data Box Blob storage esetében a végpontutótag a kapcsolati karakterlánc beállításában van megadva:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>További lépések

* [Az Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
