---
title: Az Azure adatátvitel nagy hálózati sávszélességet, közepes, nagy adatkészletek lehetőségei |} A Microsoft Docs
description: Ismerje meg, hogyan válassza ki egy Azure-megoldás az adatátvitelhez, ha magas, közepes hálózati sávszélesség a környezetben, és azt tervezi, hogy nagy mennyiségű adat átvitele.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: bc5668d826395fb71ee70907f095303a43f1ec7f
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214317"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>A közepes és nagy méretű adatkészletek irányuló adatátvitel nagy hálózati sávszélesség
 
Ez a cikk áttekintést nyújt az adatok átvitele megoldások magas, közepes hálózati sávszélesség a környezetében, és azt tervezi, hogy nagy mennyiségű adat átvitele során. A cikk azt is leírja, a javasolt adatok átvitel beállításai, és a megfelelő képesség kulcsfontosságú mátrix ehhez a forgatókönyvhöz.

Áttekinthetők megismerheti a rendelkezésre álló adatok átvitel beállításai, lépjen a [válasszon egy Azure-beli adat-átviteli megoldás](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Forgatókönyv leírása

Nagy adatkészletek adatméretek sorrendjében TB-osra bővül PBS hivatkoznak. Közepes, nagy hálózati sávszélességet 100 MB/s, 10 GB/s vonatkozik.

## <a name="recommended-options"></a>Ajánlott beállítások

Ebben a forgatókönyvben ajánlott beállítások attól függenek, hogy van-e közepes hálózati sávszélesség vagy a nagy hálózati sávszélességet.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Közepes hálózati sávszélesség (100 MB/s - 1 GB/s)

Közepes hálózati sávszélesség, az adatátvitel időpontját projektre a hálózaton keresztül kell.

A következő táblázat segítségével megbecsülheti az idő és a egy offline átviteli között, vagy a hálózati átvitel során, amely alapján válassza. A táblázat a különböző rendelkezésre álló hálózati sávszélesség (90 %-os kihasználtságot feltételezve) a hálózati adatátvitel, az előre jelzett idő.  

![Hálózati átruházás vagy offline állapotú](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- A hálózati átvitel előre jelzett túl lassú, ha egy fizikai eszközt kell használnia. Az ajánlott beállítások ebben az esetben: az Azure Data Box-család vagy az Azure Import/Export használatával saját lemezek offline átviteli eszközöket.

    - **Az Azure Data Box-család kapcsolat nélküli átvitelek** – eszközök a Data Box a Microsoft által biztosított eszközök segítségével áthelyezni a nagy mennyiségű adat Azure-ba, ha az idő, a hálózat rendelkezésre állásának és plusz költségek korlátot jelent. Eszközök, például a Robocopy használatával a helyszíni adatok másolása. Az átvitelre szánt adatok méretétől függően a Data Box-lemezek, a Data Box vagy a Data Box nehéz közül választhat.
    - **Az Azure Import/Export** – használja az Azure Import/Export szolgáltatás biztonságosan nagy mennyiségű adatot importálhat az Azure Blob storage és az Azure Files a saját lemezmeghajtók szállításával. Ez a szolgáltatás adatainak átvitelét az Azure Blob storage-ból a merevlemez-meghajtók, és küldje el a helyszíni hely a használatával is lehet.

- Ha a hálózati átvitel előre jelzett költségről ésszerűnek, majd bármilyen részleteit a következő eszközök [nagy hálózati sávszélességet](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Nagy hálózati sávszélesség (1 GB/s - 100 GB/s)

Ha a rendelkezésre álló sávszélesség nagy, használja a következő eszközök egyikét.

- **Az AzCopy** – Ez a parancssori eszköz segítségével könnyedén másolhatja az adatokat, és az Azure-Blobok, fájlok, és a Table storage, az optimális teljesítmény érdekében. AzCopy támogatja az egyidejűség és a párhuzamosság és másolási műveletek során megszakad folytatódhasson.
- **Az Azure Storage REST API-k/SDK-k** – Ha olyan alkalmazást épít ki, az alkalmazást az Azure Storage REST API-k fejlesztéséhez és használata az Azure SDK-k több nyelven érhető el.
- **Az Azure Data Box-család online átvitelek** – Data Box Edge és a Data Box-átjáró olyan online hálózati eszköz, amely adatokat helyezhet át, és az Azure-ból. Használja a Data Box Edge fizikai eszközt, ha van egy egyidejű van szüksége a folyamatos Adatbetöltési és az adatok előzetes előfeldolgozásához feltölteni. Data Box átjáró egy virtuális verziója – az eszközt az adatok átvitel ugyanazokat a lehetőségeket. Minden esetben az adatok átvitele felügyeli az eszközt.
- **Az Azure Data Factory** – Data Factory kell használni a méretezési csoport egy adatátviteli műveletet, és ha a vezénylési és enterprise osztályú figyelőfunkciókat van szükség. A Data Factory segítségével rendszeresen vihetők át fájlok között a több Azure-szolgáltatások, a helyszíni vagy a kettő kombinációját. a Data Factory hozhat létre és ütemezhet, adatvezérelt munkafolyamatokat (folyamatokat is nevezik), amelyek különálló adattárakból származó adatokat, és automatizálható az adatok áthelyezését és átalakítását.

## <a name="comparison-of-key-capabilities"></a>Főbb képességek összehasonlítása

A következő táblázat összefoglalja az ajánlott beállítások legfontosabb képességei a különbségeket.

### <a name="moderate-network-bandwidth"></a>Közepes hálózati sávszélesség

Offline adatok átvitellel, ha az alábbi táblázat segítségével legfontosabb képességei a különbségeket.

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


Online adatátvitel használja, ha a táblázat használható a következő szakaszban a magas sávszélességet.

### <a name="high-network-bandwidth"></a>Nagy hálózati sávszélesség

|                                     |    Eszközök AzCopy, <br>Az Azure PowerShell <br>Azure CLI             |    Az Azure Storage REST API-k, SDK-k                   |    Adatátjáró mezőben vagy a Data Box Edge (előzetes verzió)           |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Adattípus                  |    Azure-Blobok, az Azure Files, Azure-táblák    |    Azure-Blobok, az Azure Files, Azure-táblák    |    Azure-Blobok, az Azure Files                           |   70-nél több adatösszekötők támogatja az adattárak és formátumok    |
|    Helyigény                |    Parancssori eszközök                        |    Programozható felületet                    |    A Microsoft több virtuális tartalmaz <br>vagy a fizikai eszköz     |    Az Azure Portalon                                            |
|    Kezdeti egyszeri beállítás     |    Egyszerű               |    Mérsékelt                       |    Egyszerű (< 30 perc) közepes (1 – 2 óra)            |    Széles körű                                                          |
|    Üzem előtti adatfeldolgozás              |    Nem                                        |    Nem                                        |    Igen (az Edge számítás)                               |    Igen                                                                |
|    Vigye át más felhőkben       |    Nem                                        |    Nem                                        |    Nem                                                    |    Igen                                                                |
|    Felhasználó típusa                        |    IT Pro vagy a dev                                       |    Dev                                       |    Informatikus                                                |    Informatikus                                                             |
|    Díjszabás                          |    Ingyenes, kimenő adatforgalom díját alkalmazása         |    Ingyenes, kimenő adatforgalom díját alkalmazása         |    [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Díjszabás](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>További lépések

- [Útmutató: adatok áthelyezése az importálási/exportálási](/azure/storage/common/storage-import-export-data-to-blobs).
- Megismerheti, hogyan

    - [Data Box-lemezek adatátvitel](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Data Box adatátvitel](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Adatok áthelyezése az Azcopyval](/azure/storage/common/storage-use-azcopy-v10).
- Megismerheti, hogyan:
    - [Adatok áthelyezése az adatátjáró Box](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [A Data Box Edge adatok átalakítása az Azure-bA küldése előtt](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Útmutató: adatok áthelyezése az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Adatátvitel a REST API-k használatával

    - [A .NET-ben](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [Java-környezetben](https://docs.microsoft.com/java/api/overview/azure/storage/client)