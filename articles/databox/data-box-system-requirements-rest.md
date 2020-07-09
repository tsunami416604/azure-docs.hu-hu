---
title: Microsoft Azure Data Box blob Storage-követelmények | Microsoft Docs
description: Ismerje meg az API-k, SDK-k és a Azure Data Box blob Storage-hoz készült ügyféloldali kódtárak támogatott verzióit
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "61436494"
---
# <a name="azure-data-box-blob-storage-requirements"></a>BLOB Storage-követelmények Azure Data Box

Ez a cikk felsorolja az Azure API-k, az Azure ügyféloldali kódtárak és a Data Box blob Storage által támogatott eszközök verzióját. A blob Storage Data Box Azure-konzisztens szemantikai funkciókat biztosít a Blobok kezeléséhez. Ez a cikk az Azure Storage-szolgáltatásokkal kapcsolatos ismert Azure Data Box blob Storage-különbségeket is összegzi.

Javasoljuk, hogy figyelmesen tekintse át az információkat, mielőtt csatlakozik a Data Box blob Storage-hoz, majd szükség szerint küldje vissza.


## <a name="storage-differences"></a>Tárolási különbségek

|     Szolgáltatás                                             |     Azure Storage                                     |     Data Box Blob Storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Felhőalapú SMB-fájlmegosztás támogatott              |    Nem támogatott      |
|    Szolgáltatás titkosítása inaktív adatok esetén                  |    256 bites AES-titkosítás                             |    256 bites AES-titkosítás |
|    Tárfiók típusa                                 |    Általános célú és Azure Blob Storage-fiókok    |    Csak általános célú v1|
|    A blob neve                                            |    1 024 karakter (2 048 bájt)                     |    880 karakter (1 760 bájt)|
|    BLOB maximális méretének letiltása                              |    4,75 TB (100 MB X 50 000 blokk)                   |    4,75 TB (100 MB x 50 000 blokk) Azure Data Box v 1,8-től.|
|    Oldal blobjának maximális mérete                               |    8 TB                                               |    1 TB                   |
|    Oldal blob-oldalának mérete                                  |    512 bájt                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Támogatott API-verziók

Az Azure Storage Service API-k következő verziói támogatottak Data Box blob Storage-ban:

Azure Data Box 1,8-tól

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017. 4. 17.](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Támogatott Azure-ügyféloldali kódtárak

Data Box blob Storage esetében bizonyos ügyféloldali kódtárak és meghatározott végponti utótagok szükségesek. A Data Box blob Storage-végpontok nem rendelkeznek teljes paritással az Azure Blob Storage REST API legújabb verziójával, lásd a [támogatott verziókat Azure Data Box 1,8](#supported-api-versions)-es verzióra. A Storage ügyféloldali kódtárai esetében tisztában kell lennie azzal a verzióval, amely kompatibilis a REST APIával.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1,8-tól

| Ügyfélkódtár     |Data Box blob Storage támogatott verziója     | Hivatkozás   |     Végpont specifikációja      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Nuget-csomag:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub-kiadás:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config fájl                 |
|    Java                |    7.0.0                                           |    Maven-csomag:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub-kiadás:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    A kapcsolatok karakterláncának beállítása         |
|    Node.js             |    2.8.3                                           |    NPM-hivatkozás: https://www.npmjs.com/package/azure-storage (Futtatás: `npm install azure-storage@2.7.0` )   <br>GitHub-kiadás:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Szolgáltatási példány deklarációja    |
|    C++                 |    5.2.0                                           |    Nuget-csomag:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub-kiadás:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    A kapcsolatok karakterláncának beállítása         |
|    PHP                 |    1.2.0                                           |    GitHub-kiadás:<br>Közöshttps://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>BLOBhttps://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Telepítés a Zeneszerzőn keresztül (további tudnivalókért tekintse meg az alábbi részleteket.)                                                                                                             |    A kapcsolatok karakterláncának beállítása         |
|    Python              |    1.1.0                                           |    GitHub-kiadás:<br>Közöshttps://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>BLOBhttps://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Szolgáltatási példány deklarációja    |
|    Ruby                |    1.0.1                                           |    RubyGems-csomag:<br>Közöshttps://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>BLOBhttps://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub-kiadás:<br>Közöshttps://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>BLOBhttps://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    A kapcsolatok karakterláncának beállítása         |



### <a name="install-php-client-via-composer---current"></a>PHP-ügyfél telepítése a Zeneszerzőn keresztül – aktuális

A következőn keresztül történő telepítéshez: (blob készítése példaként).
1. Hozzon létre egy composer.jsnevű fájlt a projekt gyökérkönyvtárában a következő kóddal:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Töltse le `composer.phar` a projekt gyökerét.

3. Futtatás: php zeneszerző. farmakovigilancia-telepítés.

### <a name="endpoint-declaration"></a>Végpont deklarációja

Az Azure Data Box blob Storage-végpont két részből áll: a régió és a Data Box tartomány neve. A Data Box blob Storage SDK-ban az alapértelmezett végpont a `\<serial no. of the device>.microsoftdatabox.com` .  A blob Service-végponttal kapcsolatos további információkért nyissa meg a [kapcsolódás Data Box blob Storage](data-box-deploy-copy-data-via-rest.md)-on keresztül című témakört.
 
## <a name="examples"></a>Példák

### <a name="net"></a>.NET

Data Box blob Storage esetében a végpont utótagja meg van adva a `app.config` fájlban:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Data Box blob Storage esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Data Box blob Storage esetében a végpont utótagja meg van adva a deklarációs példányban:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Data Box blob Storage esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Data Box blob Storage esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Data Box blob Storage esetében a végpont utótagja meg van adva a deklarációs példányban:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Data Box blob Storage esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>További lépések

* [A Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
