---
title: Az Azure data transfer nagy mennyiségű adat kis vagy semmilyen hálózati sávszélesség lehetőségei |} A Microsoft Docs
description: Útmutató egy Azure-megoldás az adatátvitelhez válassza, ha a környezetben, sem hálózati sávszélesség korlátozott és nagy méretű adatkészleteket át kíván.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/10/2018
ms.author: alkohli
ms.openlocfilehash: 54a51f5f124857419727ed7ca574f717e17b125a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263803"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Adatok áthelyezése az alacsony vagy nincs hálózati sávszélesség a nagyméretű adathalmazok
 
Ez a cikk áttekintést nyújt az adatok átvitele megoldásokat a környezetben, sem hálózati sávszélesség korlátozott és nagy méretű adatkészleteket át kíván. A cikk azt is leírja, a javasolt adatok átvitel beállításai, és a megfelelő képesség kulcsfontosságú mátrix ehhez a forgatókönyvhöz.

Áttekinthetők megismerheti a rendelkezésre álló adatok átvitel beállításai, lépjen a [válasszon egy Azure-beli adat-átviteli megoldás](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>A kapcsolat nélküli átviteli vagy a hálózati átvitel

Nagy adatkészletek jelenti azt, hogy néhány TB-osra bővül az adatok néhány PBS. Nincs hálózati sávszélesség a korlátozott számú, a hálózat lassú, vagy nem megbízható hálózathatáron. Lásd még:

- Az internetszolgáltatók (ISP) a hálózati adatátvitel költségeit révén korlátozottak.
- Biztonsági vagy a szervezeti szabályzatok nem kimenő kapcsolatok engedélyezése bizalmas adatok esetén.

A fenti esetekben összes fizikai eszköz használata egy egyszeri tömeges adatátvitel végrehajtásához. Válassza ki a Data Box-lemezek, Data Box, Data Box nehéz eszközök, amelyek a Microsoft vagy a saját lemezek használatával importálási/exportálási szolgáltatja.

Győződjön meg arról, hogy egy fizikai eszköz-e a megfelelő megoldás, használja az alábbi táblázat. Azt mutatja, hogy a különböző rendelkezésre álló sávszélesség (90 %-os kihasználtságot feltételezve) a hálózati adatátvitel, az előre jelzett idő. Ha hálózati átvitelt a előre jelzett költségről túl lassú, fizikai eszközt kell használnia.  

![Hálózati átruházás vagy offline állapotú](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Ajánlott beállítások

Ebben a forgatókönyvben az elérhető lehetőségek az Azure Data Box offline átvitel vagy az Azure Import/Export eszköz.

- **Az Azure Data Box-család kapcsolat nélküli átvitelek** – eszközök a Data Box a Microsoft által biztosított eszközök segítségével áthelyezni a nagy mennyiségű adat Azure-ba, ha az idő, a hálózat rendelkezésre állásának és plusz költségek korlátot jelent. Eszközök, például a Robocopy használatával a helyszíni adatok másolása. Az átvitelre szánt adatok méretétől függően a Data Box-lemezek, a Data Box vagy a Data Box nehéz közül választhat.
- **Az Azure Import/Export** – használja az Azure Import/Export szolgáltatás biztonságosan nagy mennyiségű adatot importálhat az Azure Blob storage és az Azure Files a saját lemezmeghajtók szállításával. Ez a szolgáltatás adatainak átvitelét az Azure Blob storage-ból a merevlemez-meghajtók, és küldje el a helyszíni hely a használatával is lehet.

## <a name="comparison-of-key-capabilities"></a>Főbb képességek összehasonlítása

Az alábbi táblázat a legfontosabb képességei a különbségeket foglalja össze.

|                                     |    Data Box-lemezek (előzetes verzió)    |    Data Box                                      |    Data Box nehéz (előzetes verzió)              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Adatok mérete                        |    Visszamenőleges tárolása akár 35 TB-osra bővül                 |    Legfeljebb 80 TB-osra bővül eszközönként                       |    Akár 800 TB eszközönként               |    Változó                            |
|    Adattípus                        |    Azure-Blobok                  |    Azure-Blobok<br>Azure Files                    |    Azure-Blobok<br>Azure Files            |    Azure-Blobok<br>Azure Files          |
|    Helyigény                      |    Megrendelésenként 5 SSD             |    1 x 50-lbs. asztal-méretű eszköz megrendelésenként    |    1 X 500 KB-lbs. megrendelésenként nagy eszköz    |    Megrendelésenként legfeljebb 10 HDD és SSD-k        |
|    Kezdeti telepítési idővel               |    Alacsony <br>(15 perc)            |    Alacsony – mérsékelt <br> (< 30 perc)               |    Mérsékelt<br>(1 – 2 óra)               |    A bonyolult mérsékelt<br>(változó) |
|    Odesílání dat do Azure               |    Igen                          |    Igen                                           |    Igen                                   |    Igen                                 |
|    Adatok exportálása az Azure-ból           |    Nem                           |    Nem                                            |    Nem                                    |    Igen                                 |
|    Titkosítás                       |    128 bites AES                  |    256 bites AES                                   |    256 bites AES                           |    128 bites AES                         |
|    Hardver                         |     Microsoft által biztosított          |    Microsoft által biztosított                            |    Microsoft által biztosított                    |    A megadott ügyfél                   |
|    Hálózati illesztő                |    USB 3.1/SATA                 |    RJ 45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III.                    |
|    Partnerintegráció              |    Néhány                         |    [Magas](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Magas](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Néhány                                |
|    Szállítás                         |    A Microsoft által felügyelt            |    A Microsoft által felügyelt                             |    A Microsoft által felügyelt                     |    Felügyelt ügyfél                    |
| Akkor használja, ha az adatok áthelyezése         |Egy kereskedelmi határán belül|Egy kereskedelmi határán belül|Egy kereskedelmi határán belül|Például az USA, Európa földrajzi határokon keresztül történő|
|    Díjszabás                          |    [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/disk/)                    |   [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Díjszabás](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>További lépések

- Megismerheti, hogyan

    - [Data Box-lemezek adatátvitel](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Data Box adatátvitel](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Adatok áthelyezése az importálási/exportálási](/azure/storage/common/storage-import-export-data-to-blobs).