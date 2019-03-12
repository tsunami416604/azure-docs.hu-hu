---
title: Válasszon egy Azure-megoldás rendszeres adatátvitel |} A Microsoft Docs
description: Ismerje meg, hogyan választható ki egy Azure-megoldás az adatátvitelhez átvitelekor adatok rendszeres időközönként.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: ba6d5e20f0d6a004fd85d7e9dc57584547d52ba3
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772857"
---
# <a name="solutions-for-periodic-data-transfer"></a>Rendszeres adatforgalom-megoldások
 
Ez a cikk áttekintést nyújt az adatok átvitele megoldások átvitelekor adatok rendszeres időközönként. Rendszeres adat-átvitel a hálózaton keresztül, rendszeres időközönként vagy folyamatos adatátvitel ismétlődő csoportosíthatók. A cikk azt is leírja, a javasolt adatok átvitel beállításai, és a megfelelő képesség kulcsfontosságú mátrix ehhez a forgatókönyvhöz.

Áttekinthetők megismerheti a rendelkezésre álló adatok átvitel beállításai, lépjen a [válasszon egy Azure-beli adat-átviteli megoldás](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Ajánlott beállítások

Az ajánlott beállítások rendszeres adatátvitel attól függően, hogy az átvitel ismétlődő vagy folytonos két kategóriába sorolhatók.

- **Parancsfájlalapú és programozható eszközöket** –, amelyek a parancsfájlalapú és szoftveres használható eszközök például az AzCopy és az Azure Storage REST API-k rendszeres időközönként történik adatátvitel. Ezek az eszközök informatikai szakemberek és fejlesztők felé célozzák meg.

    - **Az AzCopy** – Ez a parancssori eszköz segítségével könnyedén másolhatja az adatokat, és az Azure-Blobok, fájlok, és a Table storage, az optimális teljesítmény érdekében. AzCopy támogatja az egyidejűség és a párhuzamosság és másolási műveletek során megszakad folytatódhasson.
    - **Az Azure Storage REST API-k/SDK-k** – Ha olyan alkalmazást épít ki, az alkalmazást az Azure Storage REST API-k fejlesztéséhez és használata az Azure SDK-k több nyelven érhető el. A REST API-kat is használhatják az Azure Storage adatátviteli könyvtár a nagy teljesítményű másolása az adatok és az Azure-ból kifejezetten mobilalkalmazásokhoz tervezett.

- **Folyamatos adatfeldolgozási eszközök** – folyamatos, folyamatban lévő Adatbetöltési, kiválaszthatja a Data Box-eszköz online átvitel vagy az Azure Data Factory. Ezek az eszközök informatikai szakemberek által vannak beállítva, és transzparens módon automatizálható az adatforgalom.

    - **Az Azure Data Factory** – Data Factory kell használni a méretezési csoport egy adatátviteli műveletet, és ha a vezénylési és enterprise osztályú figyelőfunkciókat van szükség. Azure Data Factory használatával állítsa be a felhő folyamat, amely rendszeresen küld a fájlok között számos Azure-szolgáltatások, a helyszíni vagy a kettő kombinációját. Az Azure Data Factory lehetővé teszi, hogy vezényelni az adatvezérelt munkafolyamatok, amelyek különálló adattárakból származó adatokat, és automatizálható az adatok áthelyezését és átalakítását.
    - **Az Azure Data Box-család online átvitelek** -Data Box Edge és a Data Box-átjáró olyan online hálózati eszköz, amely adatokat helyezhet át, és az Azure-ból. Data Box Edge használ a mesterséges intelligencia (AI) – Edge számítási előre a feltöltés előtt adatok feldolgozására engedélyezett. Data Box átjáró egy virtuális verziója – az eszközt az adatok átvitel ugyanazokat a lehetőségeket.


## <a name="comparison-of-key-capabilities"></a>Főbb képességek összehasonlítása

Az alábbi táblázat a legfontosabb képességei a különbségeket foglalja össze.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Parancsfájlalapú/Programmatic hálózati adatforgalom

| Képesség                  | AzCopy                                 | Az Azure Storage REST API-k       |
|-----------------------------|----------------------------------------|-------------------------------|
| Helyigény                 | A Microsoft parancssori eszköz       | Ügyfelek tárterületen fejlesztése <br> REST API-k használatával az Azure-ügyfélkönyvtárak |
| Kezdeti egyszeri beállítás     | Minimális                                | Ennél kisebb, változó fejlesztési munkamennyiség    |
| Adatformátum                 | Azure-Blobok, az Azure Files, Azure-táblák | Azure-Blobok, az Azure Files, Azure-táblák   |
| Teljesítmény                 | Már optimalizálva                      | Kidolgozása optimalizálása                  |
| Díjszabás                     | Ingyenes, kimenő adatforgalom díját alkalmazása      | Ingyenes, kimenő adatforgalom díját alkalmazása        |

### <a name="continuous-data-ingestion-over-network"></a>Folyamatos adatbetöltés hálózaton keresztül

| Szolgáltatás                                       | Data Box Gateway (előzetes verzió) | Data Box Edge (előzetes verzió)  | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Helyigény                                   | Virtuális eszköz             | Fizikai eszköz          | Az Azure Portalon, az ügynök a helyi szolgáltatás                                                            |
| Hardver                                      | A hipervizor            | A Microsoft által biztosított    | NA                                                            |
| Az első telepítés munkamennyiség                          | Alacsony (< 30 perc.)            | Közepes (~ összekapcsolhatja óra) | Nagy (~ nap)                                                 |
| Adatformátum                                   | Azure Blobs, Azure Files   | Azure Blobs, Azure Files | [70-nél több adatösszekötők támogatja az adattárak és formátumok](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Üzem előtti adatfeldolgozás                           | Nem                         | Igen, Edge számítási    | Igen                                                           |
| Helyi gyorsítótár<br>(a helyszíni adatok tárolásához)    | Igen                        | Igen                      | Nem                                                            |
| Vigye át más felhőkben                    | Nem                         | Nem                       | Igen                                                           |
| Díjszabás                                       | [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Díjszabás](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>További lépések

- [Adatok áthelyezése az Azcopyval](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [További információ a data transfer Storage REST API-kkal](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
- Megismerheti, hogyan:
    - [Adatok áthelyezése az adatátjáró Box](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [A Data Box Edge adatok átalakítása az Azure-bA küldése előtt](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Útmutató: adatok áthelyezése az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).
