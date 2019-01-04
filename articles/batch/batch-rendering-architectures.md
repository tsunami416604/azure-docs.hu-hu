---
title: Az Azure Renderelés referenciaarchitektúrák – Azure Batch
description: Architektúrák Azure Batch- és más Azure-szolgáltatások használatával kiterjesztheti helyszíni renderelési farm által tartalékkapacitás képzése a felhőben
services: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 08/13/2018
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: d5102ba94e2b7808a457df00a87b35ef7022c454
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543495"
---
# <a name="reference-architectures-for-azure-rendering"></a>Az Azure renderelési referenciaarchitektúrák

Ez a cikk bemutatja a magas szintű architektúra-diagramok forgatókönyvek bővítése, vagy "burst", egy helyszíni renderelési farm az Azure-bA. A példák bemutatják az Azure számítási, hálózati és tárolási szolgáltatások különböző lehetőségei.

## <a name="hybrid-with-nfs-or-cfs"></a>Az NFS vagy CFS hibrid

Az alábbi ábrán látható, amely tartalmazza a következő Azure-szolgáltatások hibrid forgatókönyvek:

* **COMPUTE** – Azure Batch-készlet vagy virtuálisgép-méretezési csoportot.

* **Hálózati** – helyszíni: Az Azure ExpressRoute vagy VPN. Azure: Az Azure virtuális hálózat.

* **Tárolási** – a bemeneti és kimeneti fájlok: NFS vagy Azure virtuális gépekkel, CFS szinkronizálva az Azure File Sync vagy RSync keresztül a helyszíni tárolók.

  ![Felhőbeli tartalékkapacitás – hibrid NFS vagy CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Hibrid Blobfuse

Az alábbi ábrán látható, amely tartalmazza a következő Azure-szolgáltatások hibrid forgatókönyvek:

* **COMPUTE** – Azure Batch-készlet vagy virtuálisgép-méretezési csoportot.

* **Hálózati** – helyszíni: Az Azure ExpressRoute vagy VPN. Azure: Az Azure virtuális hálózat.

* **Tárolási** – a bemeneti és kimeneti fájlok: A BLOB storage, a számítási erőforrások elérése az Azure Blobfuse csatlakoztatva van.

  ![Felhőbeli tartalékkapacitás – hibrid Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hibrid számítási és tárolási

Az alábbi ábrán egy teljes mértékben csatlakoztatott hibrid forgatókönyv látható a számítási és tárolási, és az alábbi Azure-szolgáltatásokat tartalmazza:

* **COMPUTE** – Azure Batch-készlet vagy virtuálisgép-méretezési csoportot.

* **Hálózati** – helyszíni: Az Azure ExpressRoute vagy VPN. Azure: Az Azure virtuális hálózat.

* **Tárolási** -létesítmények közötti: Avere vFXT. Nem kötelező archiválása a helyi fájlok az Azure Data Box-n keresztül a Blob storage.

  ![Felhőbeli tartalékkapacitás – hibrid számítási és tárolási](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>További lépések

* További információ [jelennek meg a kezelők](batch-rendering-render-managers.md) az Azure Batch segítségével.

* További információk az [megjelenítése az Azure-ban](batch-rendering-service.md).