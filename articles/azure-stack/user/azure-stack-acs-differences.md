---
title: Az Azure verem tárolási különbségek és szempontok |} Microsoft Docs
description: A verem az Azure storage és az Azure storage Azure verem központi telepítésével kapcsolatos megfontolások együtt közötti különbségek megismeréséhez.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviwer: xiaofmao
ms.openlocfilehash: 2a6cb3f1a1f8009af411ba4d97a23194f6f089ae
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604460"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>A verem az Azure storage: különbségek és szempontok

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A verem az Azure storage egy a tárolási cloud services csomag Microsoft Azure-készletben. Azure verem tárolási blob, table, várólista és az Azure-konzisztens szemantikáját funkciók biztosít.

Ez a cikk az Azure Storage szolgáltatás ismert Azure Storage-verem különbségeket foglalja össze. Emellett Azure verem telepítésekor megfontolandó szempontok sorolja fel. Globális Azure és az Azure-verem magas szintű különbségei kapcsolatos további tudnivalókért lásd: a [szempontok kulcs](azure-stack-considerations.md) témakör.

## <a name="cheat-sheet-storage-differences"></a>Lap cheat: tárolási különbségek

| Szolgáltatás | Azure (globális) | Azure Stack |
| --- | --- | --- |
|File Storage|Felhőalapú SMB-fájlmegosztások támogatott|Még nem támogatott.
|Az Azure storage szolgáltatás titkosítási az inaktív adatok|256 bites AES titkosítást|A BitLocker 128 bites AES titkosítást
|Tárfiók típusa|Általános célú és az Azure blob storage-fiókok|Általános célú csak.
|Replikációs beállítások|Helyileg redundáns tárolás, a georedundáns tárolást, az írásvédett georedundáns tárolás és a zónaredundáns tárolás|Helyileg redundáns tárolás.
|Prémium szintű Storage|Teljes mértékben támogatott.|Telepíthető, de nincs teljesítményszint vagy garantált.
|Felügyelt lemezek|Prémium és standard támogatott|Még nem támogatott.
|A blob neve|1024 karakter (2048 bájt)|880 karakterek (1,760 bájt)
|Blokkok blob maximális mérete|4.75 TB (100 MB X 50 000 blokk)|4.75 TB (100 MB x 50 000 blokk) 1802 frissítés vagy újabb verzióra. 50 000 x 4 MB (KB. 195 GB), a korábbi verziók.
|Lap blob pillanatkép másolása|Biztonsági mentés nem felügyelt VM csatolt Azure lemezekre egy támogatott virtuális gép|Még nem támogatott.
|Lap blob növekményes pillanatképet másolása|Prémium és standard Azure blobok támogatott|Még nem támogatott.
|A blob storage tárolási rétegei|Működés cool, és archiválja a tárolási rétegek.|Még nem támogatott.
A blob storage a helyreállítható törlés|Előzetes verzió|Még nem támogatott.
|Lap blob maximális mérete|8 TB|1 TB
|Oldalméret blob lap|512 bájt|4 KB
|Tábla partíciós kulcs és a sor kulcsméret|1024 karakter (2048 bájt)|400 karakterek (800 bájt)
|A BLOB pillanatkép|A maximális számú pillanatképpel egy BLOB nem korlátozott.|A maximális számú pillanatképpel egy BLOB 1000.|

A storage mérőszámainak különbségek is vannak:

* A tranzakciós adatokat a storage mérőszámainak nem tesz különbséget a belső vagy külső hálózati sávszélességet.
* A tranzakciós adatokat a storage mérőszámainak nem tartalmazza a virtuális gép érje el a csatlakoztatott lemezeket.

## <a name="api-version"></a>API-verzió

A következő verziók támogatottak az Azure Storage-verem:

Az Azure Storage API-k szolgáltatások:

1802 frissítése vagy újabb:

 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Korábbi verziók:

 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Az Azure Storage szolgáltatások felügyeleti API-kat:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>SDK-verzió

Az Azure verem storage támogatja a következő klienskódtárak segítségével:

| Ügyfélkódtár | A verem használható az Azure-verzió | Hivatkozás                                                                                                                                                                                                                                                                                                                                     | Végpont meghatározása       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | A 6.2.0 8.7.0 számára.          | Nuget-csomagot:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>GitHub-kiadás:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | App.config fájlban              |
| Java           | A 4.1.0 6.1.0 számára           | Maven csomag:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>GitHub-kiadás:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Kapcsolati karakterlánc beállítása      |
| Node.js        | A 1.1.0-ás 2.7.0 számára           | NPM hivatkozásra:<br>https://www.npmjs.com/package/azure-storage<br>(Például: Futtatás "npm telepítése azure-storage@2.7.0")<br> <br>Github-kiadás:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Szolgáltatás deklarációjában |
| C++            | A 2.4.0 3.1.0 számára           | Nuget-csomagot:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>GitHub-kiadás:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Kapcsolati karakterlánc beállítása      |
| PHP            | A 0.15.0 1.0.0 számára          | GitHub-kiadás:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Szerkesztő keresztül (lásd alább a részletekre)                                                                                                                                                                                                                  | Kapcsolati karakterlánc beállítása      |
| Python         | A 0.30.0 1.0.0 számára          | GitHub-kiadás:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Szolgáltatás deklarációjában |
| Ruby           | A 0.12.1 1.0.1-es számára          | RubyGems csomag:<br>Közös:<br>https://rubygems.org/gems/azure-storage-common/<br>BLOB: https://rubygems.org/gems/azure-storage-blob/<br>Várólista: https://rubygems.org/gems/azure-storage-queue/<br>Tábla: https://rubygems.org/gems/azure-storage-table/<br> <br>GitHub-kiadás:<br>https://github.com/Azure/azure-storage-ruby/releases | Kapcsolati karakterlánc beállítása      |

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Azure Storage-verem Fejlesztőeszközök](azure-stack-storage-dev.md)
* [A verem az Azure Storage bemutatása](azure-stack-storage-overview.md)
