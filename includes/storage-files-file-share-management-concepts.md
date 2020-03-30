---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597818"
---
Az Azure-fájlmegosztások *a tárfiókokban*vannak telepítve, amelyek legfelső szintű objektumok, amelyek megosztott tárolókészletet képviselnek. Ez a tárolókészlet több fájlmegosztás, valamint más tárolási erőforrások, például blobtárolók, várólisták vagy táblák üzembe helyezésére használható. A tárfiókba üzembe helyezett összes tárfiókban üzembe helyezett összes tárerőforrás az adott tárfiókra vonatkozó korlátokkal rendelkezik. A tárfiók jelenlegi korlátait az [Azure Files méretezhetőségi és teljesítménycélok című témakörben láthatja.](../articles/storage/files/storage-files-scale-targets.md)

Az Azure Files-telepítésekhez két fő tárfióktípust fog használni: 
- **Általános célú 2-es verziójú (GPv2) tárfiókok:** A GPv2-tárfiókok lehetővé teszik az Azure-fájlmegosztások szabványos/merevlemezalapú (HDD-alapú) hardverre történő üzembe helyezését. Az Azure-fájlmegosztások tárolása mellett a GPv2-tárfiókok más tárolási erőforrásokat is tárolhatnak, például blobtárolókat, várólistákat vagy táblákat. 
- **FileStorage storage-fiókok:** FileStorage storage-fiókok lehetővé teszik az Azure-fájlmegosztások üzembe helyezését prémium/ssd-alapú (SSD-alapú) hardveren. A FileStorage-fiókok csak Azure-fájlmegosztások tárolására használhatók; más tárolási erőforrások (blobtárolók, várólisták, táblák stb.) nem telepíthetők egy FileStorage-fiókban.

Számos más tárfiók-típusok előfordulhat, hogy az Azure Portalon, a PowerShell ben vagy a CLI- ben találkoznak. Két tárfiók-típus, a BlockBlobStorage és a BlobStorage tárfiókok nem tartalmazhatnak Azure-fájlmegosztásokat. A másik két tárfiók-típusok láthatják az általános célú 1-es verzió (GPv1) és a klasszikus tárfiókok, amelyek mindegyike tartalmazhat Azure-fájlmegosztások. Bár a GPv1 és a klasszikus tárfiókok azure-fájlmegosztásokat tartalmazhatnak, az Azure Files legtöbb új szolgáltatása csak GPv2- és FileStorage-tárfiókokban érhető el. Ezért azt javasoljuk, hogy csak GPv2- és FileStorage-tárfiókokat használjon az új központi telepítésekhez, és frissítse a GPv1- és klasszikus tárfiókokat, ha azok már léteznek a környezetben.  