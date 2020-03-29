---
title: Válasszon egy Azure-megoldást az időszakos adatátvitelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan választhat azure-megoldást adatátvitelre, amikor rendszeres időközönként adatokat továbbít.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: fb49802adf6242f445b700d06622d7e6aa336b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67357039"
---
# <a name="solutions-for-periodic-data-transfer"></a>Időszakos adatátviteli megoldások
 
Ez a cikk áttekintést nyújt az adatátviteli megoldásokról, amikor rendszeres időközönként adatokat továbbít. A hálózaton keresztüli rendszeres adatátvitel rendszeres időközönként ismétlődőként vagy folyamatos adatáthelyezésként kategorizálható. A cikk ismerteti az ajánlott adatátviteli lehetőségeket és a megfelelő kulcsfontosságú képességmátrixot ebben a forgatókönyvben.

Az összes rendelkezésre álló adatátviteli lehetőség áttekintéséhez válassza [az Azure-adattovábbítási megoldás kiválasztása című témakört.](storage-choose-data-transfer-solution.md)

## <a name="recommended-options"></a>Ajánlott beállítások

Az időszakos adatátvitel ajánlott lehetőségei két kategóriába sorolhatók attól függően, hogy az átvitel ismétlődő vagy folyamatos.

- **Parancsfájlalapú/programozott eszközök** – Rendszeres időközönként történő adatátvitelhez használja a parancsfájlalapú és programozott eszközöket, például az AzCopy és az Azure Storage REST API-kat. Ezek az eszközök informatikai szakembereket és fejlesztőket céloznak meg.

    - **AzCopy** – Ezzel a parancssori eszközzel egyszerűen másolhat adatokat az Azure Blobs, files és table storage optimális teljesítményével. AzCopy támogatja az egyidejűséget és a párhuzamosságot, valamint a másolási műveletek folytatását megszakítás esetén.
    - **Azure Storage REST API-k/SDK-k** – Egy alkalmazás létrehozásakor fejlesztheti az alkalmazást az Azure Storage REST API-k, és használja az Azure SDK-k kínált több nyelven. A REST API-k is kihasználhatják az Azure Storage Data Movement Library kifejezetten a nagy teljesítményű adatok másolása az Azure-ba és az Azure-ból.

- **Folyamatos adatbetöltési eszközök** – A folyamatos, folyamatos adatbetöltés, kiválaszthatja az egyik Data Box online átviteli eszköz vagy az Azure Data Factory. Ezeket az eszközöket informatikai szakemberek állítják be, és átláthatómódon automatizálhatják az adatátvitelt.

    - **Azure Data Factory** – Data Factory kell használni egy átviteli művelet horizontális felskálázása, és ha szükség van a vezénylési és nagyvállalati szintű figyelési képességek. Az Azure Data Factory segítségével hozzon létre egy felhőalapú folyamatot, amely rendszeresen továbbítja a fájlokat több Azure-szolgáltatás, a helyszíni, vagy a kettő kombinációja között. Az Azure Data Factory lehetővé teszi az adatközpontú munkafolyamatok vezénylését, amelyek különböző adattárakból adatokat adnak be, és automatizálják az adatok mozgását és az adatok átalakítását.
    - **Azure Data Box család az online átvitelhez** – a Data Box Edge és a Data Box Gateway olyan online hálózati eszközök, amelyek adatokat helyezhetnek át az Azure-ba és az Azure-ból. A Data Box Edge mesterséges intelligenciával (AI) képes Edge-számítást használ az adatok feltöltés előtti előzetes feldolgozásához. A Data Box Gateway az eszköz virtuális verziója, amely azonos adatátviteli képességekkel rendelkezik.


## <a name="comparison-of-key-capabilities"></a>A legfontosabb képességek összehasonlítása

Az alábbi táblázat összefoglalja a kulcsfontosságú képességek közötti különbségeket.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Parancsfájlalapú/automatizált hálózati adatátvitel

| Képesség                  | AzCopy                                 | Azure Storage REST API-k       |
|-----------------------------|----------------------------------------|-------------------------------|
| Alaktényező                 | Parancssori eszköz a Microsofttól       | Az ügyfelek a Storage ellen fejlődnek <br> REST API-k Azure-ügyfélkódtárak használatával |
| Kezdeti egyszeri beállítás     | Minimális                                | Mérsékelt, változó fejlesztési erőfeszítés    |
| Adatformátum                 | Azure Blobok, Azure-fájlok, Azure-táblázatok | Azure Blobok, Azure-fájlok, Azure-táblázatok   |
| Teljesítmény                 | Már optimalizált                      | Optimalizálás fejlesztés előtt                  |
| Díjszabás                     | Ingyenes, adatforgalom díjak vonatkoznak      | Ingyenes, adatforgalom díjak vonatkoznak        |

### <a name="continuous-data-ingestion-over-network"></a>Folyamatos adatbetöltés hálózaton keresztül

| Szolgáltatás                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Alaktényező                                   | Virtuális eszköz             | Fizikai eszköz          | Szolgáltatás az Azure Portalon, ügynök a helyszínen                                                            |
| Hardver                                      | Az Ön hipervizor            | A Microsoft által szállított    | NA                                                            |
| Kezdeti beállítási munka                          | Alacsony (<30 mins.)            | Mérsékelt (~pár óra) | Nagy (~nap)                                                 |
| Adatformátum                                   | Azure Blobok, Azure-fájlok   | Azure Blobok, Azure-fájlok | [Több mint 70 adatösszekötőt támogat adattárakhoz és formátumokhoz](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Adatok előzetes feldolgozása                           | Nem                         | Igen, az Edge-en keresztül    | Igen                                                           |
| Helyi gyorsítótár<br>(helyszíni adatok tárolása)    | Igen                        | Igen                      | Nem                                                            |
| Átvitel más felhőkből                    | Nem                         | Nem                       | Igen                                                           |
| Díjszabás                                       | [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Díjszabás](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>További lépések

- [Adatok átvitele az AzCopy programmal.](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [További információ a Storage REST API-kkal történő adatátvitelről.](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet)
- Ismerje meg, hogyan:
    - [Adatátvitel a Data Box Gateway segítségével.](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares)
    - [Az Azure-ba való küldés előtt alakítsa át az adatokat a Data Box Edge segítségével.](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute)
- [Ismerje meg, hogyan vihet át adatokat az Azure Data Factory segítségével.](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal)
