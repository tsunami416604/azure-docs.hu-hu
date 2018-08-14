---
title: Renderelés Azure - referenciaarchitektúrák
description: Architektúrák Azure Batch- és más Azure-szolgáltatások használatával kiterjesztheti helyszíni renderelési farm által tartalékkapacitás képzése a felhőben
services: batch
author: davefellows
manager: jeconnoc
ms.author: danlep
ms.date: 08/13/2018
ms.topic: conceptual
ms.openlocfilehash: 0fe101ee6eb88094034b90c4d39f06ba509c9512
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2018
ms.locfileid: "40099894"
---
# <a name="reference-architectures-for-azure-rendering"></a>Az Azure renderelési referenciaarchitektúrák

Ez a cikk bemutatja a magas szintű architektúra-diagramok forgatókönyvek bővítése, vagy "burst", egy helyszíni renderelési farm az Azure-bA. A példák bemutatják az Azure számítási, hálózati és tárolási szolgáltatások különböző lehetőségei.

## <a name="hybrid-with-nfs-or-cfs"></a>Az NFS vagy CFS hibrid

Az alábbi ábrán látható, amely tartalmazza a következő Azure-szolgáltatások hibrid forgatókönyvek:

* **COMPUTE** – Azure Batch-készlet vagy virtuálisgép-méretezési csoportot.

* **Hálózati** – helyszíni: Azure ExpressRoute vagy VPN. Azure: Azure virtuális hálózat.

* **Tárolási** – a bemeneti és kimeneti fájlok: NFS, vagy Azure virtuális gépekkel, CFS szinkronizálva a helyszíni tárolók az Azure File Sync vagy RSync keresztül.

  ![Felhőbeli tartalékkapacitás – hibrid NFS vagy CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Hibrid Blobfuse

Az alábbi ábrán látható, amely tartalmazza a következő Azure-szolgáltatások hibrid forgatókönyvek:

* **COMPUTE** – Azure Batch-készlet vagy virtuálisgép-méretezési csoportot.

* **Hálózati** – helyszíni: Azure ExpressRoute vagy VPN. Azure: Azure virtuális hálózat.

* **Tárolási** – a bemeneti és kimeneti fájlok: Blob storage, a számítási erőforrások elérése az Azure Blobfuse csatlakoztatva van.

  ![Felhőbeli tartalékkapacitás – hibrid Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hibrid számítási és tárolási

Az alábbi ábrán egy teljes mértékben csatlakoztatott hibrid forgatókönyv látható a számítási és tárolási, és az alábbi Azure-szolgáltatásokat tartalmazza:

* **COMPUTE** – Azure Batch-készlet vagy virtuálisgép-méretezési csoportot.

* **Hálózati** – helyszíni: Azure ExpressRoute vagy VPN. Azure: Azure virtuális hálózat.

* **Tárolási** -létesítmények közötti: Avere vFXT. Nem kötelező archiválása a helyi fájlok az Azure Data Box-n keresztül a Blob storage.

  ![Felhőbeli tartalékkapacitás – hibrid számítási és tárolási](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>További lépések

* További információ [jelennek meg a kezelők](batch-rendering-render-managers.md) az Azure Batch segítségével.

* További információk az [megjelenítése az Azure-ban](batch-rendering-service.md).