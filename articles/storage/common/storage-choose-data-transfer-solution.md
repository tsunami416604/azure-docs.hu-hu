---
title: Válasszon egy Azure-megoldást az adatátvitelhez| Microsoft dokumentumok
description: Megtudhatja, hogyan választhat Azure-megoldást adatátvitelre az adatméretek és a környezetben rendelkezésre álló hálózati sávszélesség alapján
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: ada96d0d4b167e30cb6e271aa02d0d399a9ae7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303086"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Válasszon egy Azure-megoldást az adatátvitelhez

Ez a cikk áttekintést nyújt a gyakori Azure-adatátviteli megoldások. A cikk a környezet hálózati sávszélességétől és az átvinni kívánt adatok méretétől függően az ajánlott beállításokra is hivatkozik.

## <a name="types-of-data-movement"></a>Az adatmozgás típusai

Az adatátvitel lehet offline vagy hálózati kapcsolaton keresztül. Válassza ki a megoldást a következőktől függően:

- **Adatméret** – Az átvitelre szánt adatok mérete,
- **Átviteli gyakoriság** – egyszeri vagy időszakos adatbetöltés, és
- **Hálózat** – A környezetben történő adatátvitelhez rendelkezésre álló sávszélesség.

Az adatmozgás a következő típusú lehet:

- **Offline átvitel átruházható eszközökkel** – Fizikai, átruházható eszközöket használhat, ha egyszeri tömeges adatátvitelt szeretne végezni. A Microsoft küld önnek egy lemezt vagy egy biztonságos speciális eszközt. Azt is megteheti, hogy megvásárolja és szállítja a saját lemezeit. Adatokat másol az eszközre, majd szállítja az Azure-ba, ahol az adatok feltöltése.  Ebben az esetben a rendelkezésre álló lehetőségek: Data Box Disk, Data Box, Data Box Heavy, és import/export (használja a saját lemezeit).

- **Hálózati átvitel** – Az adatokat hálózati kapcsolaton keresztül továbbítja az Azure-ba. Ezt sokféleképpen meg lehet tenni.

    - **Grafikus felület** – Ha alkalmanként csak néhány fájlt továbbít, és nem kell automatizálnia az adatátvitelt, választhat egy grafikus felület eszközt, például az Azure Storage Explorert vagy egy webalapú feltárási eszközt az Azure Portalon.
    - **Parancsfájlalapú vagy programozott átvitel** – Használhat optimalizált szoftvereszközöket, amelyeket rest API-kat/SDK-kat közvetlenül biztosítunk vagy hívunk. A rendelkezésre álló parancsfájlok eszközei az AzCopy, az Azure PowerShell és az Azure CLI. Programozott felülethez használja a .NET, Java, Python, Node/JS, C++, Go, PHP vagy Ruby sdk-k egyikét.
    - **Helyszíni eszközök** – Olyan fizikai vagy virtuális eszközt biztosítunk Önnek, amely az adatközpontban található, és optimalizálja a hálózaton keresztüli adatátvitelt. Ezek az eszközök a gyakran használt fájlok helyi gyorsítótárát is biztosítják. A fizikai eszköz a Data Box Edge, a virtuális eszköz pedig a Data Box Gateway. Mindkettő folyamatosan fut a helyszínen, és a hálózaton keresztül csatlakozik az Azure-hoz.
    - **Felügyelt adatfolyamat** – Beállíthat egy felhőalapú folyamatot, amely rendszeresen továbbítja a fájlokat több Azure-szolgáltatás, helyszíni vagy kettő kombinációja között. Az Azure Data Factory segítségével adatfolyamatokat állíthat be és kezelhet, valamint áthelyezheti és átalakíthatja az adatokat elemzésre.

A következő vizuális bemutatja a különböző Azure adatátviteli eszközök kiválasztásának irányelveit az átvitelre rendelkezésre álló hálózati sávszélességtől, az adatátvitelre szánt adatok méretétől és az átvitel gyakoriságától függően.

![Azure adatátviteli eszközök](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Az offline átviteli eszközök felső határai - Data Box Disk, Data Box és Data Box Heavy kiterjeszthetők több eszköztípusú megrendeléssel.*

## <a name="selecting-a-data-transfer-solution"></a>Adatátviteli megoldás kiválasztása

Az adatátviteli megoldás kiválasztásához válaszoljon a következő kérdésekre:

- A rendelkezésre álló hálózati sávszélesség korlátozott vagy nem létezik, és nagy adatkészleteket szeretne átvinni?
  
    Ha igen, olvassa el a [következőt: 1. forgatókönyv: Nagy adatkészletek átvitele nincs vagy alacsony hálózati sávszélességgel.](storage-solution-large-dataset-low-network.md)
- Nagy adatkészleteket szeretne átvinni a hálózaton keresztül, és közepesen nagy hálózati sávszélességgel rendelkezik?

    Ha igen, olvassa el a [következőt: 2. forgatókönyv: Nagy, közepes vagy nagy hálózati sávszélességű adatkészletek átvitele.](storage-solution-large-dataset-moderate-high-network.md)
- Időnként csak néhány fájlt szeretne átvinni a hálózaton keresztül?

    Ha igen, olvassa [el a 3.](storage-solution-small-dataset-low-moderate-network.md)
- Rendszeres időközönként időponthoz szerzett adatátvitelt keres?

    Ha igen, használja a [4.](storage-solution-periodic-data-transfer.md)
- Folyamatos, folyamatos adatátvitelt keres?

    Ha igen, használja a [4.](storage-solution-periodic-data-transfer.md)

## <a name="data-transfer-feature-in-azure-portal"></a>Adatátviteli funkció az Azure Portalon

Az Azure Storage-fiók az Azure Portalon is, és válassza ki az **adatátviteli** funkciót. Adja meg a hálózati sávszélességet a környezetben, az átvinni kívánt adatok méretét és az adatátvitel gyakoriságát. Az Ön által megadott információknak megfelelő optimális adatátviteli megoldásokat fogja látni. 

## <a name="next-steps"></a>További lépések

- [Bevezetés az Azure Storage Explorer bemutatkozása.](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/)
- [Olvassa el az AzCopy áttekintését.](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
- [Az Azure PowerShell használata az Azure Storage szolgáltatással](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Rövid útmutató: Blobok létrehozása, letöltése és listázása az Azure CLI-vel](../blobs/storage-quickstart-blobs-cli.md)
- Ismerkedjen meg a következőkkel:

    - [Azure Data Box, Azure Data Box Disk és Azure Data Box Heavy offline átvitelhez.](https://docs.microsoft.com/azure/databox/)
    - [Az Azure Data Box Gateway és az Azure Data Box Edge az online átvitelhez.](https://docs.microsoft.com/azure/databox-online/)
- [Ismerje meg, mi az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Adatok átvitele a REST API-k használatával

    - [A .NET-ben](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [A Javában](https://docs.microsoft.com/java/api/overview/azure/storage)
