---
title: Azure renderelési referenciaarchitektúrák – Azure Batch
description: Az Azure Batch és más Azure-szolgáltatások használatával a helyszíni renderelőfarm felhőbe való felszakítással történő bővítésére szolgáló architektúrák
services: batch
ms.service: batch
author: davefellows
manager: evansma
ms.author: labrenne
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: 20442a6618ca9357bb3be95879b68bffca45a40d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022953"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referenciaarchitektúrák az Azure rendereléséhez

Ez a cikk magas szintű architektúradiagramokat jelenít meg a helyszíni renderelőfarm azure-ba való kiterjesztéséhez vagy "burst" kiterjesztéséhez. A példák az Azure számítási, hálózati és tárolási szolgáltatásainak különböző beállításait mutatják be.

## <a name="hybrid-with-nfs-or-cfs"></a>Hibrid NFS-sel vagy CFS-sel

Az alábbi ábrán egy hibrid forgatókönyv látható, amely a következő Azure-szolgáltatásokat tartalmazza:

* **Számítási** – Azure Batch-készlet vagy virtuálisgép-méretezési készlet.

* **Hálózat** – Helyszíni: Azure ExpressRoute vagy VPN. Azure: Azure-virtuális hálózat.

* **Tárolás** – Bemeneti és kimeneti fájlok: NFS vagy CFS Azure virtuális gépek használatával, az Azure File Sync vagy RSync használatával szinkronizálva a helyszíni tárterülettel. Másik lehetőségként: Avere vFXT bemeneti vagy kimeneti fájlokat a helyszíni NAS-eszközök NFS használatával.

  ![Felhőbe deszkolás - Hibrid NFS-sel vagy CFS-sel](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hibrid és Blobfuse

Az alábbi ábrán egy hibrid forgatókönyv látható, amely a következő Azure-szolgáltatásokat tartalmazza:

* **Számítási** – Azure Batch-készlet vagy virtuálisgép-méretezési készlet.

* **Hálózat** – Helyszíni: Azure ExpressRoute vagy VPN. Azure: Azure-virtuális hálózat.

* **Tárolás** – Bemeneti és kimeneti fájlok: Blob storage, erőforrások azure Blobfuse-on keresztül történő számítási feladatokhoz csatlakoztatva.

  ![Felhőbe deszkodik - Hibrid a Blobfuse-szal](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hibrid számítás és tárolás

Az alábbi ábra egy teljesen összekapcsolt hibrid forgatókönyvet mutat be mind a számítási, mind a tárolási, és a következő Azure-szolgáltatásokat tartalmazza:

* **Számítási** – Azure Batch-készlet vagy virtuálisgép-méretezési készlet.

* **Hálózat** – Helyszíni: Azure ExpressRoute vagy VPN. Azure: Azure-virtuális hálózat.

* **Tárolás** - Telephelyek közötti: Avere vFXT. A helyszíni fájlok opcionális archiválása az Azure Data Box segítségével a Blob storage vagy a helyszíni Avere FXT NAS-gyorsításhoz.

  ![Felhőbe deszkolás – Hibrid számítás és tárolás](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>További lépések

* További információ a [Renderkezelők](batch-rendering-render-managers.md) Azure Batch használatával kapcsolatos további tudnivalókról.

* További információ az [Azure-beli megjelenítés idáig vonatkozó](batch-rendering-service.md)lehetőségekről.