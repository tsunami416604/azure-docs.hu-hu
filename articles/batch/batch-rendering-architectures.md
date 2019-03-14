---
title: Az Azure Renderelés referenciaarchitektúrák – Azure Batch
description: Architektúrák Azure Batch- és más Azure-szolgáltatások használatával kiterjesztheti helyszíni renderelési farm által tartalékkapacitás képzése a felhőben
services: batch
ms.service: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: ae4680c948ce8e1efd32207dc37821d61182f2d8
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791205"
---
# <a name="reference-architectures-for-azure-rendering"></a>Az Azure renderelési referenciaarchitektúrák

Ez a cikk bemutatja a magas szintű architektúra-diagramok forgatókönyvek bővítése, vagy "burst", egy helyszíni renderelési farm az Azure-bA. A példák bemutatják az Azure számítási, hálózati és tárolási szolgáltatások különböző lehetőségei.

## <a name="hybrid-with-nfs-or-cfs"></a>Az NFS vagy CFS hibrid

Az alábbi ábrán látható, amely tartalmazza a következő Azure-szolgáltatások hibrid forgatókönyvek:

* **COMPUTE** – Azure Batch-készlet vagy virtuálisgép-méretezési csoportot.

* **Hálózati** – helyszíni: Az Azure ExpressRoute vagy VPN. Azure: Az Azure virtuális hálózat.

* **Tárolási** – a bemeneti és kimeneti fájlok: NFS vagy Azure virtuális gépekkel, CFS szinkronizálva az Azure File Sync vagy RSync keresztül a helyszíni tárolók. Azt is megteheti: Avere vFXT bemeneti vagy kimeneti fájlok az NFS használata a helyszíni NAS-eszközökön.

  ![Felhőbeli tartalékkapacitás – hibrid NFS vagy CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

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

* **Tárolási** -létesítmények közötti: Avere vFXT. Választható archiválása a helyszíni Azure Data Box-n keresztül a Blob storage-fájlokat, vagy a helyszíni Avere FXT NAS-gyorsítás esetében.

  ![Felhőbeli tartalékkapacitás – hibrid számítási és tárolási](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>További lépések

* További információ [jelennek meg a kezelők](batch-rendering-render-managers.md) az Azure Batch segítségével.

* További információk az [megjelenítése az Azure-ban](batch-rendering-service.md).