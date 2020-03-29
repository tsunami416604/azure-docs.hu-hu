---
title: Az Azure adatátviteli lehetőségei nagy, alacsony vagy hálózati sávszélesség nélküli adatkészletek esetén| Microsoft dokumentumok
description: Ismerje meg, hogyan választhat egy Azure-megoldást adatátvitelre, ha a környezetében nincs hálózati sávszélesség, és nagy adatkészletek átvitelét tervezi.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 4c4ac9489b9613b2eeaf26a3df9f4cbc664a1026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60730690"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Nagy méretű adathalmazok átvitele alacsony hálózati sávszélességen vagy sávszélesség nélkül
 
Ez a cikk áttekintést nyújt az adatátviteli megoldásokról, ha a környezetben nincs hálózati sávszélesség, és nagy adatkészletek átvitelét tervezi. A cikk ismerteti az ajánlott adatátviteli lehetőségeket és a megfelelő kulcsfontosságú képességmátrixot ebben a forgatókönyvben.

Az összes rendelkezésre álló adatátviteli lehetőség áttekintéséhez válassza [az Azure-adattovábbítási megoldás kiválasztása című témakört.](storage-choose-data-transfer-solution.md)

## <a name="offline-transfer-or-network-transfer"></a>Kapcsolat nélküli átvitel vagy hálózati átvitel

A nagy adatkészletek azt jelentik, hogy kevés TB-vel rendelkezik néhány PC-nyi adathoz. A hálózati sávszélesség nem korlátozott, a hálózat lassú vagy megbízhatatlan. Lásd még:

- Az internetszolgáltatóktól érkező hálózati átvitel költségei korlátozzák Önt.
- A biztonsági vagy szervezeti házirendek nem engedélyezik a kimenő kapcsolatokat a bizalmas adatok kezelése során.

A fenti esetekben használjon fizikai eszközt egyszeri tömeges adatátvitelhez. Válasszon a Data Box Disk, Data Box, Data Box Heavy eszközök közül, amelyeket a Microsoft biztosít, vagy az Importálás/exportálás a saját lemezein keresztül.

Annak ellenőrzéséhez, hogy egy fizikai eszköz a megfelelő beállítás-e, használja az alábbi táblázatot. Ez azt mutatja, a tervezett idő a hálózati adatátvitel, a különböző rendelkezésre álló sávszélesség (feltételezve, hogy 90%-os kihasználtság). Ha a hálózati átvitel az előrejelzések szerint túl lassú, használjon fizikai eszközt.  

![Hálózati átvitel vagy kapcsolat nélküli átvitel](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Ajánlott beállítások

Ebben a forgatókönyvben elérhető lehetőségek az Azure Data Box offline átviteléhez vagy az Azure importálási/exportálási eszközei.

- **Azure Data Box család offline átvitelhez** – A Microsoft által biztosított Data Box-eszközökről származó eszközökkel nagy mennyiségű adatot helyezhet át az Azure-ba, ha az idő, a hálózat rendelkezésre állása vagy a költségek korlátozottak. A helyszíni adatok másolása olyan eszközökkel, mint például a Robocopy. Az átvitelre szánt adatmérettől függően a Data Box Disk, a Data Box vagy a Data Box Heavy is választhat.
- **Azure importálási/exportálási** – Azure import/export szolgáltatás használata saját lemezmeghajtók szállításával nagy mennyiségű adat biztonságos importálásához az Azure Blob storage és az Azure Files számára. Ez a szolgáltatás is használható adatok átvitelére az Azure Blob storage lemezmeghajtókra, és a szállítás a helyszíni helyekre.

## <a name="comparison-of-key-capabilities"></a>A legfontosabb képességek összehasonlítása

Az alábbi táblázat összefoglalja a kulcsfontosságú képességek közötti különbségeket.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Adatméret                        |    Akár 35 TB                 |    Akár 80 TB eszközönként                       |    Akár 800 TB eszközönként               |    Változó                            |
|    Adattípus                        |    Azure Blobs                  |    Azure Blobs<br>Azure Files                    |    Azure Blobs<br>Azure Files            |    Azure Blobs<br>Azure Files          |
|    Alaktényező                      |    5 SSD rendelésenként             |    1 X 50 font. asztali méretű eszköz rendelésenként    |    1 X ~500 font. nagy eszköz rendelésenként    |    Rendelésenként akár 10 HDD/SSD        |
|    Kezdeti beállítási idő               |    Alacsony <br>(15. hétpont)            |    Alacsonytól közepesig <br> (<30.               |    Közepes<br>(1-2 óra)               |    Közepesen nehézvagy nehéz<br>(változó) |
|    Adatok küldése az Azure-ba               |    Igen                          |    Igen                                           |    Igen                                   |    Igen                                 |
|    Adatexportálás az Azure-ból           |    Nem                           |    Nem                                            |    Nem                                    |    Igen                                 |
|    Titkosítás                       |    AES 128 bites                  |    AES 256 bites                                   |    AES 256 bites                           |    AES 128 bites                         |
|    Hardver                         |     A Microsoft által biztosított          |    A Microsoft által biztosított                            |    A Microsoft által biztosított                    |    A megadott vevő                   |
|    Hálózati illesztő                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Partnerintegráció              |    Néhány                         |    [Magas](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Magas](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Néhány                                |
|    Shipping                         |    Microsoft által kezelt            |    Microsoft által kezelt                             |    Microsoft által kezelt                     |    Vevő által kezelt                    |
| Adatok áthelyezésekkor való használata         |Kereskedelmi határon belül|Kereskedelmi határon belül|Kereskedelmi határon belül|Földrajzi határokon átnyúlóan, pl. az USA és az EU között|
|    Díjszabás                          |    [Díjszabás](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Díjszabás](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan kell

    - [Adatátvitel a Data Box Disk segítségével.](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal)
    - [Adatok átvitele a Data Box segítségével.](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)
    - [Adatok átvitele importálással/exportálással.](/azure/storage/common/storage-import-export-data-to-blobs)
