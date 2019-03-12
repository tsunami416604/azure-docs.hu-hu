---
title: Az Azure data az alacsony kisebb adatkészletek esetében a beállítások átvitele a közepes hálózati sávszélesség |} A Microsoft Docs
description: Ismerje meg, hogyan választható ki egy Azure-megoldás az adatátvitelhez, ha alacsony a közepes hálózati sávszélesség a környezetben, és kis adatkészletek át kíván.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 7cdaebe62c7071b26c29e599bb0614dbe7631085
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778025"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Bejövő adatátvitel kis adatkészletekhez, az alacsony, közepes hálózati sávszélesség
 
Ez a cikk áttekintést nyújt az adatok átvitele megoldások Ha alacsony a közepes hálózati sávszélesség a környezetben, és kis adatkészletek át kíván. A cikk azt is leírja, a javasolt adatok átvitel beállításai, és a megfelelő képesség kulcsfontosságú mátrix ehhez a forgatókönyvhöz.

Áttekinthetők megismerheti a rendelkezésre álló adatok átvitel beállításai, lépjen a [válasszon egy Azure-beli adat-átviteli megoldás](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Forgatókönyv leírása

Tekintse meg kisebb adatkészletek adatméretek sorrendjében GB néhány TB-osra bővül. Alacsony és közepes hálózati sávszélesség 45 Mbps (T3 kapcsolat adatközpontban), 1 GB/s jelenti.

- Ha csak néhány rekordra fájlok viszi, és nem kell automatizálhatja az adatforgalom, fontolja meg a grafikus felülettel rendelkező eszközök.
- Ha Ön rendszerfelügyelet ügyfélszolgáltatást, fontolja meg a parancssor vagy programozott/parancsfájlkezelő eszközök.

## <a name="recommended-options"></a>Ajánlott beállítások

Ebben a forgatókönyvben javasoljuk a lehetőségek közül választhat:

- **Grafikus felületi eszközök** például az Azure Storage Explorerrel és az Azure Storage az Azure Portalon. Ezek megtekintheti az adatokat, és gyorsan a fájlátvitel a néhány egyszerű módot biztosítanak.

    - **Az Azure Storage Explorer** – a többplatformos az eszköz lehetővé teszi az Azure-tárfiókok tartalmának kezeléséhez. Lehetővé teszi, hogy feltöltése, letöltése és kezelése a blobok, fájlok, üzenetsorok, táblák és az Azure Cosmos DB-entitásokat. Használhatja a Blob storage-blobok és mappák kezelése, valamint feltöltése és tölthet le blobokat a helyi fájlrendszerben és a Blob storage vagy tárfiókok között.
    - **Az Azure portal** – Azure Storage az Azure portal felfedezése fájlok webes felületet biztosít, és új fájl egyenként. Ez a beállítást akkor jó választás, ha nem szeretné, hogy telepítse minden olyan eszközt, vagy nem ad ki parancsokat gyorsan megismerheti a fájlokat, vagy egyszerűen csak töltse fel újakat néhány.

- **Parancsfájl-kezelési és programozható eszközöket** például AzCopy/PowerShell/az Azure CLI és az Azure Storage REST API-k.

    - **Az AzCopy** – Ez a parancssori eszköz segítségével könnyedén másolhatja az adatokat, és az Azure-Blobok, fájlok, és a Table storage, az optimális teljesítmény érdekében. AzCopy támogatja az egyidejűség és a párhuzamosság és másolási műveletek során megszakad folytatódhasson.
    - **Az Azure PowerShell** – magabiztosan rendszer-felügyeleti felhasználók az Azure Storage modul használata az Azure PowerShell való adatátvitel.
    - **Az Azure CLI** – az platformfüggetlen eszköz segítségével kezelheti az Azure-szolgáltatások, és töltse fel az adatokat az Azure Storage.
    - **Az Azure Storage REST API-k/SDK-k** – Ha olyan alkalmazást épít ki, az alkalmazás ellen az Azure Storage REST API-k/SDK-k fejlesztéséhez és használata az Azure ügyfélkódtárai több nyelven érhető el.


## <a name="comparison-of-key-capabilities"></a>Főbb képességek összehasonlítása

Az alábbi táblázat a legfontosabb képességei a különbségeket foglalja össze.

| Szolgáltatás                                                            | Azure Storage Explorer                    | Azure Portal  | AzCopy<br>Azure PowerShell<br>Azure CLI            | Az Azure Storage REST API-k és SDK-kkal |
|----------------------------------------------|-------------------------------------------|--------------------------------------|-------------------|---------------------------------------|
| Rendelkezésre állás                                                       | Letöltés és telepítés <br>Önálló eszközzel | Webes adatvizsgálati eszközök az Azure Portalon           | A parancssori eszköz |A .NET, Java, Python, JavaScript, C++, Go, a Ruby és a PHP programozható felületek                                                         |
| Grafikus felület                                              | Igen                                       | Igen                                                     | Nem                | Nem                                                      |
| A támogatott platformok                                              | Windows, Mac, Linux                       | Webalapú     |Windows, Mac, Linux     |Összes platform                                                         |
| Blob storage-műveletek engedélyezett<br>a blobok és mappák            | Feltöltés<br>Letöltés<br>Kezelés    | Feltöltés<br>Letöltés<br>Kezelés  |Feltöltés<br>Letöltés<br>Kezelés             | Igen, testre szabható                                                        |
| Engedélyezett Gen1 a Data Lake storage<br>a fájlok és mappák műveletek  | Feltöltés<br>Letöltés<br>Kezelés                | Nem |Feltöltés<br>Letöltés<br>Kezelés                   |      Nem                                                   |
| Engedélyezett fájl tárolási műveletek<br>a fájlok és könyvtárak        | Feltöltés<br>Letöltés<br>Kezelés       | Feltöltés<br>Letöltés<br>Kezelés   |Feltöltés<br>Letöltés<br>Kezelés |                                                         |Igen, testre szabható
| Table storage-műveletek engedélyezett<br>táblák                      |Kezelés                            | Nem            |Tábla támogatása az AzCopy 7-es verziója |Igen, testre szabható|
| Engedélyezett a Queue storage                                              | Kezelés                                    | Nem  |Nem | Igen, akkor a testre szabható|


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [adatok áthelyezése az Azure Storage Explorer](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Adatok áthelyezése az AzCopyval](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

