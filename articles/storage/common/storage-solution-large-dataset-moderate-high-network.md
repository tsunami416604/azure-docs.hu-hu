---
title: Azure adatátviteli lehetőségek nagy adatkészletekhez, közepes és nagy hálózati sávszélességhez| Microsoft dokumentumok
description: Megtudhatja, hogyan választhat azure-megoldást adatátvitelre, ha közepes vagy nagy hálózati sávszélességgel rendelkezik a környezetében, és nagy adatkészletek átvitelét tervezi.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: f7177a95bdd585ff2822c9ac8c94a85d12f9259b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900366"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Nagy méretű adathalmazok átvitele közepes vagy nagy hálózati sávszélességen
 
Ez a cikk áttekintést nyújt az adatátviteli megoldásokról, ha közepes vagy nagy hálózati sávszélességgel rendelkezik a környezetben, és nagy adatkészletek átvitelét tervezi. A cikk ismerteti az ajánlott adatátviteli lehetőségeket és a megfelelő kulcsfontosságú képességmátrixot ebben a forgatókönyvben.

Az összes rendelkezésre álló adatátviteli lehetőség áttekintéséhez válassza [az Azure-adattovábbítási megoldás kiválasztása című témakört.](storage-choose-data-transfer-solution.md)

## <a name="scenario-description"></a>Forgatókönyv leírása

A nagy adatkészletek a TB-k és a PC-k sorrendjében megadott adatméretekre vonatkoznak. A közepes és nagy hálózati sávszélesség 100 Mb/s és 10 Gb/s közötti átviteli sebességet igényel.

## <a name="recommended-options"></a>Ajánlott beállítások

Az ebben a forgatókönyvben ajánlott beállítások attól függnek, hogy mérsékelt hálózati vagy nagy hálózati sávszélességgel rendelkezik-e.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Mérsékelt hálózati sávszélesség (100 Mbps - 1 Gb/s)

Mérsékelt hálózati sávszélesség esetén ki kell vetítenie a hálózaton keresztüli adatátvitel idejét.

Az alábbi táblázat segítségével megbecsülheti az időt, és ennek alapján választhat offline átvitel vagy hálózati átvitel között. A táblázat a hálózati adatátvitel tervezett idejét mutatja a különböző rendelkezésre álló hálózati sávszélességek esetében (90%-os kihasználtságot feltételezve).  

![Hálózati átvitel vagy kapcsolat nélküli átvitel](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Ha a hálózati átvitel az előrejelzések szerint túl lassú, használjon fizikai eszközt. Az ajánlott lehetőségek ebben az esetben az Azure Data Box családból vagy az Azure importálási/exportálási eszközökből a saját lemezek használatával.

    - **Azure Data Box család offline átvitelhez** – A Microsoft által biztosított Data Box-eszközökről származó eszközökkel nagy mennyiségű adatot helyezhet át az Azure-ba, ha az idő, a hálózat rendelkezésre állása vagy a költségek korlátozottak. A helyszíni adatok másolása olyan eszközökkel, mint például a Robocopy. Az átvitelre szánt adatmérettől függően a Data Box Disk, a Data Box vagy a Data Box Heavy is választhat.
    - **Azure importálási/exportálási** – Azure import/export szolgáltatás használata saját lemezmeghajtók szállításával nagy mennyiségű adat biztonságos importálásához az Azure Blob storage és az Azure Files számára. Ez a szolgáltatás is használható adatok átvitelére az Azure Blob storage lemezmeghajtókra, és a szállítás a helyszíni helyekre.

- Ha a hálózati átvitel az előrejelzések szerint ésszerű, akkor használhatja az alábbi eszközök bármelyikét, amelyek nagy hálózati sávszélességben vannak [részletezve.](#high-network-bandwidth)


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Nagy hálózati sávszélesség (1 Gbps - 100 Gbps)

Ha a rendelkezésre álló hálózati sávszélesség magas, használja az alábbi eszközök egyikét.

- **AzCopy** – Ezzel a parancssori eszközzel egyszerűen másolhat adatokat az Azure Blobs, files és table storage optimális teljesítményével. AzCopy támogatja az egyidejűséget és a párhuzamosságot, valamint a másolási műveletek folytatását megszakítás esetén.
- **Azure Storage REST API-k/SDK-k** – Egy alkalmazás létrehozásakor fejlesztheti az alkalmazást az Azure Storage REST API-k, és használja az Azure SDK-k kínált több nyelven.
- **Azure Data Box család az online átvitelhez** – a Data Box Edge és a Data Box Gateway olyan online hálózati eszközök, amelyek adatokat helyezhetnek át az Azure-ba és az Azure-ból. Használja a Data Box Edge fizikai eszközt, ha egyidejűleg szükség van az adatok feltöltés előtti folyamatos feldolgozására és előfeldolgozására. A Data Box Gateway az eszköz virtuális verziója, amely azonos adatátviteli képességekkel rendelkezik. Az adatátvitelt minden esetben az eszköz kezeli.
- **Azure Data Factory** – Data Factory kell használni egy átviteli művelet horizontális felskálázása, és ha szükség van a vezénylési és nagyvállalati szintű figyelési képességek. A Data Factory használatával rendszeresen átvihet fájlokat több Azure-szolgáltatás, helyszíni vagy a kettő kombinációja között. a Data Factory segítségével olyan adatalapú munkafolyamatokat (úgynevezett folyamatokat) hozhat létre és ütemezhet, amelyek különböző adattárakból adatokat adnak be, és automatizálják az adatáthelyezést és az adatátalakítást.

## <a name="comparison-of-key-capabilities"></a>A legfontosabb képességek összehasonlítása

Az alábbi táblázatok összefoglalják az ajánlott beállítások kulcsfontosságú képességei közötti különbségeket.

### <a name="moderate-network-bandwidth"></a>Mérsékelt hálózati sávszélesség

Kapcsolat nélküli adatátvitel esetén az alábbi táblázat segítségével ismerje meg a kulcsfontosságú képességek közötti különbségeket.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Import/Export                       |
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


Ha online adatátvitelt használ, használja a következő szakaszban található táblázatot a nagy hálózati sávszélességhez.

### <a name="high-network-bandwidth"></a>Nagy hálózati sávszélesség

|                                     |    Eszközök AzCopy, <br>Azure PowerShell, <br>Azure CLI             |    Azure Storage REST API-k, SDK-k                   |    Data Box Átjáró vagy Data Box Edge          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Adattípus                  |    Azure Blobok, Azure-fájlok, Azure-táblázatok    |    Azure Blobok, Azure-fájlok, Azure-táblázatok    |    Azure Blobok, Azure-fájlok                           |   Több mint 70 adatösszekötőt támogat adattárakhoz és formátumokhoz    |
|    Alaktényező                |    Parancssori eszközök                        |    Automatizált felület                    |    A Microsoft virtuális <br>vagy fizikai eszköz     |    Szolgáltatás az Azure Portalon                                            |
|    Kezdeti egyszeri beállítás     |    Könnyű               |    Közepes                       |    Könnyű (<30 perc) és közepes (1-2 óra)            |    Kiterjedt                                                          |
|    Adatok előzetes feldolgozása              |    Nem                                        |    Nem                                        |    Igen (Edge-számítással)                               |    Igen                                                                |
|    Átvitel más felhőkből       |    Nem                                        |    Nem                                        |    Nem                                                    |    Igen                                                                |
|    Felhasználó típusa                        |    IT Pro vagy dev                                       |    Dev                                       |    IT-szakértő                                                |    IT-szakértő                                                             |
|    Díjszabás                          |    Ingyenes, adatforgalom díjak vonatkoznak         |    Ingyenes, adatforgalom díjak vonatkoznak         |    [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Díjszabás](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>További lépések

- [További információ az adatok importálása/exportálása című programról.](/azure/storage/common/storage-import-export-data-to-blobs)
- Ismerje meg, hogyan kell

    - [Adatátvitel a Data Box Disk segítségével.](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal)
    - [Adatok átvitele a Data Box segítségével.](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)
- [Adatok átvitele az AzCopy programmal.](/azure/storage/common/storage-use-azcopy-v10)
- Ismerje meg, hogyan:
    - [Adatátvitel a Data Box Gateway segítségével.](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares)
    - [Az Azure-ba való küldés előtt alakítsa át az adatokat a Data Box Edge segítségével.](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute)
- [Ismerje meg, hogyan vihet át adatokat az Azure Data Factory segítségével.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)
- Adatok átvitele a REST API-k használatával

    - [A .NET-ben](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [A Javában](https://docs.microsoft.com/java/api/overview/azure/storage)
