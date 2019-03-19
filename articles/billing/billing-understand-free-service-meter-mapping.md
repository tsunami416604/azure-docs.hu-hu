---
title: Szolgáltatás az Azure ingyenes fiók mérőszám-lel |} A Microsoft Docs
description: Ismerje meg ingyenes fiókhoz tartozó szolgáltatások mérőszám-leképezés szolgáltatást.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: banders
ms.openlocfilehash: 2468f61c187d9b10ed9fe55ccf76e5d2561d0505
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57889811"
---
# <a name="understand-free-service-to-meter-mapping"></a>A mérőszám leképezés ingyenes szolgáltatás ismertetése

Minden Azure-szolgáltatások használati adatainak összevetésére a mérőszámok, amelyek az Azure számlázási rendszer használja, a felhasználók a szolgáltatások díja szerint számítjuk fel bocsát ki. Ingyenes szolgáltatások használatának segít jobban megérteni, tekintsük át a szolgáltatást, hogy ezek a szolgáltatások mérőszám-leképezés. Ingyenes szolgáltatások létrehozásával kapcsolatban lásd: [ingyenes szolgáltatások létrehozása az ingyenes Azure-fiók](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Ingyenes fiók jogosult szolgáltatások leképezése díjában szolgáltatás 

|    Szolgáltatás   | Fogyasztásmérő neve az Azure Portalon | A fájl/API/használati fogyasztásmérő neve | Meter ID (Mérési azonosító) |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S Linux rendszerű virtuális gép | -Számítási üzemóra – standard B1 VM | Üzemóra – ingyenes | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows virtuális gép | -Számítási üzemóra – standard B1 VM (Windows) | Üzemóra – ingyenes | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S virtuális gép – nyilvános IP-címek  | IP-cím-Szolgáltatásóra – nyilvános IP-címek | IP-cím-Szolgáltatásóra – ingyenes | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Tárhely (GB) – a Cosmos DB | Tárhely (GB) – ingyenes | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 kérelemegység (óra) – a Cosmos DB | 100 kérelemegység (óra) – ingyenes | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | Szabványos i/o - fájlok (GB) – helyileg redundáns | Standard I/O – fájlok (GB) – ingyenes | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standard I/O – Fájlolvasási műveleti egység (10e) | Standard I/O – Fájlolvasási műveleti egység (in 10,000s) – ingyenes | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standard I/O – Fájlírási műveleti egység (10e) | Standard I/O – fájlírási műveleti egység (in 10,000s) – ingyenes | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standard I/O – Fájlprotokoll-műveleti egység (10e) | Standard I/O – Fájlprotokoll-műveleti egység (in 10,000s) – ingyenes | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standard I/O – Fájllistázási műveleti egység (10e) | Standard I/O – Fájllistázási műveleti egység (in 10,000s) – ingyenes | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Gyakori elérésű Blokkblob-tárolás | Standard I/O – gyors elérésű Blokkblob olvasási műveletek (in 10,000s) | Standard I/O – gyors elérésű Blokkblob olvasási műveletei (in 10,000s) – ingyenes |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Gyakori elérésű Blokkblob-tárolás | Helyileg redundáns standard I/O – gyors elérésű Blokkblob (GB) – | Standard I/O – gyors elérésű Blokkblob (GB) – ingyenes | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Gyakori elérésű Blokkblob-tárolás | Standard I/O – gyors elérésű Blokkblob írási műveletei (in 10,000s) | Standard I/O – gyors elérésű Blokkblob írási műveletei (in 10,000s) – ingyenes | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Gyakori elérésű Blokkblob-tárolás  | Standard I/O – gyors elérésű Blokkblob írási/listázási műveletei (in 10,000s) | Standard I/O – gyors elérésű Blokkblob írási/listázási műveletei (in 10,000s) – ingyenes | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Managed Disk *  | Standard szintű felügyelt lemez – pillanatfelvételek (GB) – helyileg redundáns | Standard szintű felügyelt lemez – pillanatfelvételek (GB) – ingyenes | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Managed Disk *  | Standard szintű felügyelt lemezes műveletek (10 000 db) | Standard szintű felügyelt lemezes műveletek (10 000 db) – ingyenes | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Managed Disk *  | A Premium Storage – Lapblob/P6 (egység) – helyileg redundáns | A Premium Storage – Lapblob/P6 (egység) – ingyenes | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Standard S0 Database Days - SQL Database | Standard S0 szintű Adatbázisőrzés (nap) – ingyenes | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Közös – sávszélesség ** | Adatkivitel (GB) | Kimenő adatforgalom (GB) – ingyenes | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Ha Windows virtuális gép létrehozása, és válassza ki a felügyelt lemez, felügyelt lemezes mérőszám a virtuális gép részeként fog felhasználni.

\** Megosztott mérőszámok segítségével több szolgáltatást használhatók fel. Például virtuális gépek és a Storage egyaránt gridre bocsáthatja ki az adatok átvitele Out(GB) mérőszám felhasználásának.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
