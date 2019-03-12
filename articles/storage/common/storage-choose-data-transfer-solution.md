---
title: Válasszon egy Azure-megoldás az adatátvitelhez |} A Microsoft Docs
description: Ismerje meg, az adatátvitelhez adatméretek és a környezetben a rendelkezésre álló hálózati sávszélesség alapján egy Azure-megoldás kiválasztása
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/10/2018
ms.author: alkohli
ms.openlocfilehash: 4e2a182493b1e9de3d2ba9d586a9560e42fe0ecb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773435"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Válasszon egy Azure-megoldás adatátvitel

Ez a cikk áttekintést néhány gyakori Azure adatátviteli megoldásokat nyújt. A cikk is hivatkozik a hálózati sávszélesség a környezetben, és szeretne átvinni az adatok méretétől függően a ajánlott beállítások.

## <a name="types-of-data-movement"></a>Adatáthelyezés típusa

Az adatátviteli kapcsolat nélküli üzemmódban vagy a hálózati kapcsolaton keresztül is lehet. Válassza ki a megoldást, attól függően, a:

- **Adatok mérete** -átvitel, szánt adatok mérete
- **Gyakoriság Transfer** -egyszeri vagy ismétlődő adatbetöltés, és
- **Hálózati** – sávszélesség áll rendelkezésre, az adatok átvitele a környezetben.

Az adatok áthelyezése a következő típusú lehet:

- **Shippable eszközök használatával offline átviteli** – fizikai shippable eszközök használata, amikor a kapcsolat nélküli egyszeri tömeges adatátviteli szeretné. A Microsoft küld Önnek egy lemezt, vagy specializált biztonságos eszköz. Azt is megteheti vásárol, és a saját lemezek szállításra. Adatok másolása az eszközön, és ezután küldje az Azure-ba, az adatok feltöltése.  Ebben az esetben az elérhető lehetőségek a következők Data Box-lemezek, Data Box, Data Box nehéz és Import/Export (a saját lemezek használata).

- **A hálózati átvitel** -, az adatok az Azure-bA hálózaton keresztül helyezze át a hálózati kapcsolatot. Ez számos módon teheti meg.

    - **Grafikus felület** – Ha időnként néhány fájlok átvitele, és nem kell az adatátvitel automatizálása, választhatja például az Azure Storage Explorer egy grafikus felület eszköz vagy egy webalapú feltárás eszközt az Azure Portalon.
    - **Parancsfájlalapú vagy programozott átviteli** – optimalizált szoftvereszközök, hogy a Microsoft adja meg, vagy a REST API-k/SDK-k meghívása közvetlenül is használhatja. A rendelkezésre álló parancsfájlok futtatására alkalmas eszközök AzCopy, az Azure PowerShell és az Azure CLI-vel is. Programozható felületet használja az SDK-k közül a .NET, Java, Python, csomópont/js Platformokra, C++, Go, PHP vagy Ruby.
    - **A helyszíni eszközök** – azt adja meg, hogy egy fizikai vagy virtuális eszköz, amely az adatközpontban található, és az adatátviteli optimalizálja a hálózaton keresztül. Ezeket az eszközöket is biztosít a gyakran használt fájlok a helyi gyorsítótárat. A fizikai eszköz a Data Box Edge és a virtuális eszköz a Box átjáró. Mind a helyszíni eredetű forgalomhoz véglegesen futnak, és kapcsolódhat az Azure a hálózaton keresztül.
    - **Felügyelt adatfolyamat** – felhőalapú folyamat rendszeresen vihetők át fájlok között a több Azure-szolgáltatások, a helyszíni vagy a kettő kombinációját állíthatja. Azure Data Factory használatával beállítása és kezelése a folyamatok, és az adatok áthelyezéséhez és átalakításához elemzés céljából.

Az alábbi Vizualizáció az irányelveket, válassza ki a különböző Azure adatátvitel attól függően, hogy az átvitel, rendelkezésre álló sávszélesség adatméret átvitel és az átvitel gyakorisága számára készült eszközök mutatja be.

![Az Azure adatátviteli eszközök](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**A kapcsolat nélküli adatátviteli eszközök – Data Box-lemezek, a Data Box és a Data Box nehéz felső határértéke úgy, hogy egy eszköz típusú több rendelések is kiterjeszthető.*

## <a name="selecting-a-data-transfer-solution"></a>Egy átviteli megoldás kiválasztása

Segítségével kiválaszthatja egy adatátviteli megoldás a következő kérdések megválaszolásával:

- A rendelkezésre álló hálózati sávszélesség korlátozott vagy nem létezik, és nagy mennyiségű adat átvitele szeretne?
  
    Ha igen, tekintse meg: [1. forgatókönyv: Nélkül nagy mennyiségű adat átvitele vagy alacsony a sávszélesség](storage-solution-large-dataset-low-network.md).
- Nagy adatkészletek átvitel a hálózaton keresztül szeretné és egy közepes, nagy hálózati sávszélességre van?

    Ha igen, tekintse meg: [2. forgatókönyv: Közepes és nagy mennyiségű adat átvitele nagy hálózati sávszélességet](storage-solution-large-dataset-moderate-high-network.md).
- Biztosan alkalmanként átvitel a hálózaton keresztül néhány fájlokat?

    Ha igen, tekintse meg [3. forgatókönyv: Közepes hálózati sávszélesség korlátozott rendelkező kis adatkészletek átvitele](storage-solution-small-dataset-low-moderate-network.md).
- Információra van szüksége – időponthoz adatátvitel rendszeres időközönként?

    Ha igen, használja a parancsfájlalapú/szoftveres lehetőségek ismertetett [4. forgatókönyv: Rendszeres adatforgalom](storage-solution-periodic-data-transfer.md).
- Információra van szüksége a folyamatban lévő, folyamatos adatátvitel?

    Ha igen, használja a lehetőségek a [4. forgatókönyv: Rendszeres adatforgalom](storage-solution-periodic-data-transfer.md).

## <a name="next-steps"></a>További lépések

- [Ismerkedjen meg az Azure Storage Explorer](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Az AzCopy – áttekintés](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Az Azure PowerShell és az Azure Storage használata](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Azure CLI és az Azure Storage használata](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- További információ:

    - [Az Azure Data Box, az Azure Data Box-lemezek és az Azure Data Box (nagy erőforrásigényű) kapcsolat nélküli átvitelek](https://docs.microsoft.com/azure/databox/).
    - [Az Azure Data Box-átjáró és az Azure Data Box Edge online átvitelek](https://docs.microsoft.com/azure/databox-online/).
- [Ismerje meg, mi az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Adatátvitel a REST API-k használatával

    - [A .NET-ben](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [Java-környezetben](https://docs.microsoft.com/java/api/overview/azure/storage/client)
