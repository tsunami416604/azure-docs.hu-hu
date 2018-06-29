---
title: Azure ingyenes fiók mérő leképezést szolgáltatás |} Microsoft Docs
description: Ismerje meg, az ingyenes fiók szolgáltatásait mérő leképezést szolgáltatást.
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
ms.author: amberb
ms.openlocfilehash: 3019fe5f8b44eaa32d4ab309f296d6b4574df1d3
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061370"
---
# <a name="understand-free-service-to-meter-mapping"></a>Mérési leképezési szabad szolgáltatás ismertetése

Minden Azure-szolgáltatások megfelelően kibocsát használat elleni mérőszámok, amelyek az Azure számlázási rendszer használja, hogy fel a felhasználók számára. Jobb megértése érdekében az ingyenes szolgáltatásokhoz használatát, vizsgáljuk meg a szolgáltatás és a mérési egymáshoz rendelését, a szolgáltatások. Az ingyenes szolgáltatásokhoz létrehozásával kapcsolatban lásd: [hozzon létre az ingyenes szolgáltatásokhoz ingyenes Azure-fiókot](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Leképezési ingyenes fiók jogosult szolgáltatások mérő szolgáltatás 

|    Szolgáltatás   | Azure-portál mérő neve | A fájl/API/használati mérő neve | Meter ID (Mérési azonosító) |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S Linux virtuális gép | Számítási óra - Standard_B1 méretű VM | Számítási óra - szabad | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows virtuális gép | Számítási óra - Standard_B1 virtuális gép (Windows) | Számítási óra - szabad | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S VM - nyilvános IP-címek  | IP-címszolgáltatási óra - nyilvános IP-címek | IP-címszolgáltatási óra - szabad | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Tárhely (GB) – Cosmos DB | Tárhely (GB) – ingyenes | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 kérelem egységek (óra) – Cosmos DB | 100 kérelem egységek (óra) – ingyenes | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | Helyi redundancia standard IO – fájlok (GB) – | Standard I/O - fájlok (GB) – ingyenes | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standard I/O – Fájlolvasási műveleti egység (10e) | Standard IO – fájl olvasási műveleti egység (10 000 db) – ingyenes | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standard I/O – Fájlírási műveleti egység (10e) | Standard I/O – fájl írási műveleti egység (10 000 db) – ingyenes | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standard I/O – Fájlprotokoll-műveleti egység (10e) | Standard I/O – Fájlprotokoll-műveleti egység (10 000 db) – ingyenes | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standard I/O – Fájllistázási műveleti egység (10e) | Standard I/O – Fájllistázási műveleti egység (10 000 db) – ingyenes | e8ae79ad-c2ab-4D82-b226-dd3c33dfd40c
| Gyors elérésű Blob-tároló | Standard I/O – gyors elérésű Blokkblob olvasási műveletek (10 000 db) | Standard I/O – gyors elérésű Blokkblob olvasási műveletek (10 000 db) – ingyenes |fd7cfa1e-026e-4BE1-871b-1c2386e8902e
| Gyors elérésű Blob-tároló | Helyi redundancia standard I/O – gyors elérésű Blokkblob (GB) – | Standard I/O – gyors elérésű Blokkblob (GB) – ingyenes | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Gyors elérésű Blob-tároló | Standard I/O – gyors elérésű Blokkblob írási műveletek (10 000 db) | Standard I/O – gyors elérésű Blokkblob írási műveletek (10 000 db) – ingyenes | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Gyors elérésű Blob-tároló  | Standard I/O – gyors elérésű Blokkblob írási/listázási műveletei (10 000 db) | Standard I/O – gyors elérésű Blokkblob írási/listázási műveletei (10 000 db) – ingyenes | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Lemez felügyelt *  | Standard szintű felügyelt lemez – pillanatfelvételek (GB) – helyi redundancia | Standard szintű felügyelt lemez – pillanatfelvételek (GB) – ingyenes | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Lemez felügyelt *  | Standard szintű felügyelt lemezes műveletek (10 000 db) | Standard szintű felügyelt lemezes műveletek (10 000 db) – ingyenes | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Lemez felügyelt *  | Prémium szintű Storage oldal Blob/P6 (egység) – helyi redundancia | Prémium szintű Storage oldal Blob/P6 (egység) – ingyenes | 2b98c168-27CA-4cc1-B509-e887dec87657
| SQL Database | Standard szintű, S0 adatbázis-elszámolási napok - SQL-adatbázis | Standard szintű, S0 Adatbázisőrzés (nap) – ingyenes | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Megosztott - sávszélesség ** | Adatkivitel (GB) | Kimenő adatforgalom (GB) – ingyenes | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Ha egy Windows virtuális gép létrehozása, és válassza ki a felügyelt lemezes, fog használni, a virtuális gép részeként felügyelt lemezes mérni.

\** Megosztott mérőszámok segítségével több szolgáltatás képes használni. Például mind a virtuális gép, és a tárterület kibocsátás használati adatok átvitele Out(GB) mérő ellen.





## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
