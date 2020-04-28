---
title: Azure-alapú megoldás kiválasztása adatátvitelhez | Microsoft Docs
description: Megtudhatja, hogyan választhat ki Azure-megoldást az adatátvitelhez az adatméretek és a környezetben elérhető hálózati sávszélesség alapján
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: df261dbf8af8ffdb4ccb10626594626150245748
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176396"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Azure-alapú megoldás kiválasztása adatátvitelhez

Ez a cikk áttekintést nyújt a közös Azure adatátviteli megoldásokról. A cikk a környezetében található hálózati sávszélességtől és az átvinni kívánt adatok méretétől függően a javasolt beállításokra is hivatkozik.

## <a name="types-of-data-movement"></a>Adatáthelyezési típusok

Az adatátvitel lehet offline vagy a hálózati kapcsolaton keresztül. Válasszon a megoldástól függően:

- **Adatok mérete** – az átvitelre szánt adatok mérete
- **Átvitel gyakorisága** – egyszeri vagy rendszeres adatfeldolgozás, valamint
- **Hálózat** – a környezetében az adatátvitelhez rendelkezésre álló sávszélesség.

Az adatáthelyezés a következő típusúak lehetnek:

- **Offline átvitel shippable-eszközök használatával** – használjon fizikai shippable eszközöket, ha offline egyszeri tömeges adatátvitelt szeretne végezni. A Microsoft egy lemezt vagy biztonságos speciális eszközt küld Önnek. Másik lehetőségként megvásárolhatja és elküldheti saját lemezeit. Másolja az eszközöket az eszközre, majd küldje el az Azure-ba, ahol az adatfeltöltve van.  Ebben az esetben a rendelkezésre álló beállítások a következők: Data Box Disk, Data Box, Data Box Heavy és Importálás/Exportálás (saját lemezek használata).

- **Hálózati átvitel** – az adatok átvitele az Azure-ba hálózati kapcsolaton keresztül történik. Ezt többféleképpen is megteheti.

    - **Grafikus felület** – ha alkalmanként csak néhány fájlt továbbít, és nem kell automatizálnia az adatátvitelt, kiválaszthat egy grafikus kezelőfelületi eszközt, például Azure Storage Explorer vagy webalapú feltárási eszközt a Azure Portal.
    - **Parancsfájlos vagy programozott átvitel** – használhat optimalizált szoftvereket, amelyeket a REST API-k/SDK-k közvetlen használatával biztosítanak vagy meghívhatnak. A rendelkezésre álló parancsfájl-előkészítő eszközök a következők: AzCopy, Azure PowerShell és Azure CLI. Programozási felületen a .NET, a Java, a Python, a Node/JS, a C++, a go, a PHP vagy a Ruby egyik SDK-t használhatja.
    - Helyszíni **eszközök** – biztosítunk Önnek egy fizikai vagy virtuális eszközt, amely az adatközpontban található, és optimalizálja a hálózaton keresztüli adatátvitelt. Ezek az eszközök a gyakran használt fájlok helyi gyorsítótárát is tartalmazzák. A fizikai eszköz a Data Box Edge, a virtuális eszköz pedig a Data Box Gateway. Mindkettő véglegesen fut a helyszínen, és csatlakozik az Azure-hoz a hálózaton keresztül.
    - **Felügyelt adatfolyamat** – beállíthatja, hogy egy felhőalapú folyamat rendszeresen továbbítson fájlokat több Azure-szolgáltatás, a helyszíni vagy kettő kombinációja között. Az adatfolyamatok beállításához és kezeléséhez, valamint az elemzéshez az adatátvitelhez és az átalakításhoz használja a Azure Data Factory.

A következő vizualizáció bemutatja az irányelveket, amelyekkel kiválaszthatja a különböző Azure-adatátviteli eszközöket az átvitelhez rendelkezésre álló hálózati sávszélességtől, az átvitelre szánt adatok méretétől és az átvitel gyakoriságának megfelelően.

![Az Azure adatátviteli eszközei](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Az offline adatátviteli eszközök felső határértékei – Data Box Disk, Data Box és Data Box Heavy bővíthetők, ha több rendelést helyeznek el az eszköz típusától.*

## <a name="selecting-a-data-transfer-solution"></a>Adatátviteli megoldás kiválasztása

Válaszoljon a következő kérdésekre az adatátviteli megoldás kiválasztásához:

- A rendelkezésre álló hálózati sávszélesség korlátozott vagy nem létezik, és nagy adatkészleteket szeretne átvinni?
  
    Ha igen, lásd: [1. forgatókönyv: nagyméretű adatkészletek átvitele nem vagy alacsony hálózati sávszélességgel](storage-solution-large-dataset-low-network.md).
- Nagy adatkészleteket szeretne átvinni a hálózaton keresztül, és közepes és magas hálózati sávszélességgel rendelkezik?

    Ha igen, lásd: [2. forgatókönyv: nagyméretű adatkészletek átvitele közepes és magas hálózati sávszélességgel](storage-solution-large-dataset-moderate-high-network.md).
- Időnként csak néhány fájlt szeretne átvinni a hálózaton keresztül?

    Ha igen, tekintse [meg a 3. forgatókönyv: kisméretű adathalmazok átvitele korlátozott hálózati sávszélességgel](storage-solution-small-dataset-low-moderate-network.md).
- Rendszeres időközönként keresi az időponthoz való adatátvitelt?

    Ha igen, használja a 4. forgatókönyvben ismertetett parancsfájl-/programozási beállításokat [: rendszeres adatátvitelek](storage-solution-periodic-data-transfer.md).
- Folyamatos adatátvitelt keres?

    Ha igen, használja a [4. forgatókönyv: rendszeres adatátvitelek](storage-solution-periodic-data-transfer.md)lehetőségeit.

## <a name="data-transfer-feature-in-azure-portal"></a>Adatátviteli szolgáltatás a Azure Portal

Az Azure Storage-fiókját Azure Portalban is megtekintheti, és kiválaszthatja az **adatátviteli** funkciót. Adja meg a hálózati sávszélességet a környezetében, az átvinni kívánt adatok méretét és az adatátvitel gyakoriságát. A megadott adatoknak megfelelő optimális adatátviteli megoldások jelennek meg. 

## <a name="next-steps"></a>További lépések

- [Ismerkedjen meg Azure Storage Explorerával](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Olvassa el a AzCopy áttekintését](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Rövid útmutató: Blobok feltöltése, letöltése és listázása a PowerShell-lel](../blobs/storage-quickstart-blobs-powershell.md)
- [Gyors útmutató: Blobok létrehozása, letöltése és listázása az Azure CLI-vel](../blobs/storage-quickstart-blobs-cli.md)
- Ismerkedjen meg a következőkkel:

    - [Az offline átvitelek Azure Data Box, Azure Data Box Disk és Azure Data Box Heavy](https://docs.microsoft.com/azure/databox/).
    - [Online átvitelek Azure Data Box Gateway és Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/).
- [Ismerje meg, mi az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Adatok átvitele a REST API-k használatával

    - [A .NET-ben](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [A Javában](https://docs.microsoft.com/java/api/overview/azure/storage)
