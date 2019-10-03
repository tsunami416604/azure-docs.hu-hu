---
title: Szolgáltatásmérő-társítás ingyenes Azure-fiókok esetében
description: Tudnivalók a szolgáltatásmérő-társításról az ingyenes fiókban elérhető szolgáltatások esetében.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 00f0db0107cee4a7f52dbf4e04a586c5e7920d61
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709401"
---
# <a name="understand-free-service-to-meter-mapping"></a>Az ingyenes szolgáltatásmérő-társítás ismertetése

Az Azure-szolgáltatások általi használat mérése fogyasztásmérőkkel történik, amelyet az Azure számlázási rendszere a felhasználók szolgáltatási díjainak kiszámításához használ. Az ingyenes szolgáltatások használatának részletesebb megismeréséhez tekintsük át a szolgáltatásmérő-társításokat a szolgáltatások esetében. Az ingyenes szolgáltatások létrehozásával kapcsolatos tudnivalókért lásd: [Ingyenes szolgáltatások létrehozása ingyenes Azure-fiókkal](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-eligible-services"></a>Szolgáltatásmérő-társítás a jogosult szolgáltatások esetében

|    Szolgáltatás   | Szolgáltatásmérő neve az Azure Portalon | Szolgáltatásmérő neve a használati fájlban/API-ban | Meter ID (Mérési azonosító) |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S Linux rendszerű virtuális gép | Üzemórák – Standard_B1 virtuális gép | Üzemórák – Ingyenes | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows rendszerű virtuális gép | Üzemórák – Standard_B1 virtuális gép (Windows) | Üzemórák – Ingyenes | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S virtuális gép – Nyilvános IP-címek  | IP-cím szolgáltatásóra – Nyilvános IP-címek | IP-cím szolgáltatásóra – Ingyenes | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Tárterület (GB) – Cosmos DB | Tárterület (GB) – Ingyenes | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 kérelemegység (óra) – Cosmos DB | 100 kérelemegység (óra) – Ingyenes | 5d638a6f-e221-41cf-ae3f-0f81d368cef6
| File Storage | Standard I/O – Fájlok (GB) – Helyileg redundáns | Standard I/O – Fájlok (GB) – Ingyenes | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standard I/O – Fájlolvasási műveleti egység (10e) | Standard I/O – Fájlolvasási műveleti egység (10e) – Ingyenes | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standard I/O – Fájlírási műveleti egység (10e) | Standard I/O – Fájlírási műveleti egység (10e) – Ingyenes | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standard I/O – Fájlprotokoll-műveleti egység (10e) | Standard I/O – Fájlprotokoll-műveleti egység (10e) – Ingyenes | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standard I/O – Fájllistázási műveleti egység (10e) | Standard I/O – Fájllistázási műveleti egység (10e) – Ingyenes | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Gyakori elérésű blokkblobok tárolása | Standard I/O – Gyakori elérésű blokkblobok olvasása (10e) | Standard I/O – Gyakori elérésű blokkblobok olvasása (10e) – Ingyenes |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Gyakori elérésű blokkblobok tárolása | Standard I/O – Gyakori elérésű blokkblob (GB) – Helyileg redundáns | Standard I/O – Gyakori elérésű blokkblob (GB) – Ingyenes | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Gyakori elérésű blokkblobok tárolása | Standard I/O – Gyakori elérésű blokkblobok írása (10e) | Standard I/O – Gyakori elérésű blokkblobok írása (10e) – Ingyenes | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Gyakori elérésű blokkblobok tárolása  | Standard I/O – Gyakori elérésű blokkblobok írása/listázása (10e) | Standard I/O – Gyakori elérésű blokkblobok írása/listázása (10e) – Ingyenes | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Felügyelt lemez<sup>1</sup>  | Standard szintű felügyelt lemez/pillanatkép (GB) – Helyileg redundáns | Standard szintű felügyelt lemez/pillanatkép (GB) – Ingyenes | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Felügyelt lemez<sup>1</sup>  | Standard szintű felügyelt lemez műveletei (10e) | Standard szintű felügyelt lemez műveletei (10e) – Ingyenes | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Felügyelt lemez<sup>1</sup>  | Premium Storage, Lapblob/P6 (egység) – Helyileg redundáns | Premium Storage, Lapblob/P6 (egység) – Ingyenes | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Standard szintű, S0 típusú adatbázis megőrzési ideje (nap) – SQL Database | Standard szintű, S0 típusú adatbázis megőrzési ideje (nap) – Ingyenes | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Közös – Sávszélesség<sup>2</sup> | Kimenő adatforgalom (GB) | Kimenő adatforgalom (GB) – Ingyenes | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

<sup>1</sup>Ha Windows rendszerű virtuális gépet hoz létre, és a felügyelt lemezt választja, akkor a felügyelt lemez fogyasztásmérőjét a virtuális gép részeként használja.

<sup>2</sup>A közös fogyasztásmérőket több szolgáltatás is használhatja. Például a Virtual machines és a Storage is a Kimenő adatforgalom (GB) fogyasztásmérőt használja.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- [Váltás magasabb szintű előfizetésre](billing-upgrade-azure-subscription.md)
