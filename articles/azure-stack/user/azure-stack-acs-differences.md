---
title: Az Azure stack tárolási különbségek és szempontok |} A Microsoft Docs
description: Az Azure stack storage és az Azure storage, Azure Stack üzembe helyezési szempontok együtt közötti különbségek megértéséhez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2018
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.openlocfilehash: 6c0c42763ec3d124850555500c3a322073af2479
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139578"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Az Azure stack-tároló: különbségek és szempontok

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure stack tárolási a storage cloud services a Microsoft Azure Stackhez. Az Azure stack storage biztosít a blob, table, queue és fiók felügyeleti funkciók az Azure-konzisztens szemantikáját.

Ez a cikk az Azure Storage szolgáltatások ismert Azure Stack Storage különbségeket foglalja össze. Szintén megfontolandó szempontok az Azure Stack telepítésekor sorolja fel. Globális Azure és az Azure Stack közötti magas szintű különbségek kapcsolatos további információkért tekintse meg a [szempontok kulcs](azure-stack-considerations.md) témakör.

## <a name="cheat-sheet-storage-differences"></a>Hasznos tanácsok: Storage különbségek

| Szolgáltatás | Azure (globális) | Azure Stack |
| --- | --- | --- |
|File Storage|Felhőalapú SMB-fájlmegosztások támogatott|Még nem támogatott
|Az Azure storage service encryption az inaktív adatok|256 bites AES-titkosítás|A BitLocker 128 bites AES-titkosítás
|Tárfiók típusa|Általános célú és az Azure blob storage-fiókok|Általános célú csak.
|Replikációs beállítások|Helyileg redundáns tárolás, georedundáns tárolás, georedundáns írásvédett tárolás és zónaredundáns tárolás|Helyileg redundáns tárolás.
|Prémium szintű Storage|Teljes mértékben támogatott.|Bővítheti, de nincs teljesítményszint vagy garantálja.
|Felügyelt lemezek|Prémium és standard szintű támogatott|Még nem támogatott.
|A blob neve|1024 karakter hosszúságú (2048 bájt)|880 karakter (1,760 bájt)
|Block blob maximális mérete|4,75 TB (100 MB X 50 000 blokk)|Az 1802-es frissítés vagy újabb verzió 4,75 TB (100 MB x 50 000 blokk) 50 000 x 4 MB (KB. 195 GB), a korábbi verziók.
|Blob pillanatkép-másolás lap|Biztonsági mentés nem felügyelt virtuális Géphez csatolt Azure lemezeken futó virtuális gépek támogatott|Még nem támogatott.
|Blob növekményes pillanatkép-másolás lap|Prémium és standard oldala az Azure-blobok támogatott|Még nem támogatott.
|A blob Storage tárolási rétegek|Gyakori és ritka elérésű és archív tárolási szintek.|Még nem támogatott.
A blob Storage a helyreállítható törlés|Előzetes verzió|Még nem támogatott.
|Blob maximális mérete|8 TB|1 TB
|Lapblob oldal méretét|512 bájt|4 KB
|Tábla partíciós kulcs és a sor kulcsméret|1024 karakter hosszúságú (2048 bájt)|400 karakter (800 bájt)
|BLOB-pillanatkép|Egy blob pillanatképeinek maximális számát. nem korlátozódik.|Egy blob pillanatképeinek maximális száma 1000.|

A storage-mérőszámok különbségek is vannak:

* A storage-mérőszámok tranzakciós adatok nem tesznek különbséget a belső vagy külső hálózati sávszélességet.
* A tranzakciós adatokat a storage-mérőszámok nem tartalmazza a virtuális gép hozzá a csatlakoztatott lemezeket.

## <a name="api-version"></a>API-verzió

Azure Stack Storage a következő verziók támogatottak:

Az Azure Storage szolgáltatási API-kkal:

az 1802-frissítés vagy újabb:

 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Korábbi verziók:

 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Az Azure Storage felügyeleti API-k szolgáltatásaihoz:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>SDK-verziókra

Az Azure Stack storage a következő ügyfélkódtárak támogatja:

| Ügyfélkódtár | Az Azure Stack támogatott verziója | Hivatkozás                                                                                                                                                                                                                                                                                                                                     | Végpont-specifikáció       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | A 6.2.0 8.7.0 való.          | Nuget-csomagot:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>GitHub-verzió:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | App.config fájlban              |
| Java           | A 4.1.0-s 6.1.0           | Maven-csomag:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>GitHub-verzió:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Kapcsolati karakterlánc beállítása      |
| Node.js        | Az 1.1.0-s 2.7.0           | Az NPM-hivatkozás:<br>https://www.npmjs.com/package/azure-storage<br>(Például: futtatása "npm telepítése azure-storage@2.7.0")<br> <br>Github-verzió:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Szolgáltatás deklarációjában |
| C++            | A 2.4.0 3.1.0           | Nuget-csomagot:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>GitHub-verzió:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Kapcsolati karakterlánc beállítása      |
| PHP            | Az az 1.0.0-s 0.15.0          | GitHub-verzió:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Composer keresztül (lásd lejjebb)                                                                                                                                                                                                                  | Kapcsolati karakterlánc beállítása      |
| Python         | Az az 1.0.0-s 0.30.0          | GitHub-verzió:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Szolgáltatás deklarációjában |
| Ruby           | A 0.12.1 1.0.1          | RubyGems csomag:<br>Közös:<br>https://rubygems.org/gems/azure-storage-common/<br>BLOB: https://rubygems.org/gems/azure-storage-blob/<br>Várólista: https://rubygems.org/gems/azure-storage-queue/<br>Tábla: https://rubygems.org/gems/azure-storage-table/<br> <br>GitHub-verzió:<br>https://github.com/Azure/azure-storage-ruby/releases | Kapcsolati karakterlánc beállítása      |

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Azure Stack tárolásfejlesztési eszközök](azure-stack-storage-dev.md)
* [Az Azure Stack Storage bemutatása](azure-stack-storage-overview.md)
