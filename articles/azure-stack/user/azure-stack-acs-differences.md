---
title: "Verem az Azure Storage: Különbségek és szempontok"
description: "Azure verem központi telepítésével kapcsolatos megfontolások együtt Azure verem Storage és az Azure Storage közötti különbségek megismeréséhez."
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 4d6fb44fd6fd2261059ca45093d8b49345adfa74
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Verem az Azure Storage: Különbségek és szempontok

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A verem az Azure Storage tárolási cloud services csomag Microsoft Azure verem egy. Az Azure verem Storage blob, table, várólista és az Azure-konzisztens szemantikáját funkciók biztosít.

Ez a cikk az Azure Storage Azure Storage-verem ismert különbségek foglalja össze. Emellett összegzi más szempontokat tartalmaz, amelyek Azure verem telepítésekor tartsa szem előtt. Azure verem és az Azure közötti magas szintű különbségek kapcsolatos további tudnivalókért lásd: a [szempontok kulcs](azure-stack-considerations.md) témakör.

## <a name="cheat-sheet-storage-differences"></a>Lap cheat: tárolási különbségek

| Szolgáltatás | Azure (globális) | Azure Stack |
| --- | --- | --- |
|File Storage|Felhőalapú SMB-fájlmegosztások támogatott|Még nem támogatott.
|Adatok inaktív adatok titkosítása|256 bites AES titkosítást|Még nem támogatott.
|Tárfiók típusa|Általános célú és az Azure Blob storage-fiókok|Általános célú csak
|Replikációs beállítások|Helyileg redundáns tárolás, a georedundáns tárolást, az írásvédett georedundáns tárolás és a zónaredundáns tárolás|Helyileg redundáns tárolás
|Prémium szintű Storage|Teljes mértékben támogatott.|Telepíthető, de nincs teljesítményszint vagy garancia
|Felügyelt lemezek|Prémium és standard támogatott|Még nem támogatott.
|A BLOB neve|1024 karakter (2048 bájt)|880 karakterek (1,760 bájt)
|Blokkok blob maximális mérete|4.75 TB (100 MB X 50 000 blokk)|50 000 x 4 MB (KB. 195 GB)
|Lap blob pillanatkép másolása|Biztonsági mentés nem felügyelt VM csatolt Azure lemezekre egy támogatott virtuális gép|Még nem támogatott.
|Lap blob növekményes pillanatképet másolása|Prémium és standard Azure blobok támogatott|Még nem támogatott.
|Lap blob maximális mérete|8 TB|1 TB
|Oldalméret blob lap|512 bájt|4 KB-OS
|Tábla partíciós kulcs és a sor kulcsméret|1024 karakter (2048 bájt)|400 karakterek (800 bájt)

### <a name="metrics"></a>Mérőszámok
Van még néhány különbség a storage mérőszámainak:
* A tranzakciós adatokat a storage mérőszámainak nem tesz különbséget a belső vagy külső hálózati sávszélességet.
* A tranzakciós adatokat a storage mérőszámainak nem tartalmazza a virtuális gép érje el a csatlakoztatott lemezeket.

## <a name="api-version"></a>API-verzió
A következő verziók támogatottak az Azure Storage-verem:

* Az Azure Storage-adatszolgáltatások: [2015-04-05 REST API-verzió](https://docs.microsoft.com/rest/api/storageservices/Version-2015-04-05?redirectedfrom=MSDN)
* Az Azure Storage kezelési szolgáltatás: [2015-05-01. dátumú előnézeti, 2015-06-15 és 2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN) 

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés az Azure Storage-verem Fejlesztőeszközök](azure-stack-storage-dev.md)
* [A verem az Azure Storage bemutatása](azure-stack-storage-overview.md)

