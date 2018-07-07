---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep
ms.custom: include file
ms.openlocfilehash: 296e92d803bb69376f286aa60cfb4a955b08010f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "34669346"
---
## <a name="deployment-considerations"></a>Telepítési szempontok
* **Azure-előfizetés** –, több nagy számítási igényű példányok üzembe helyezése, fontolja meg a használatalapú fizetéses előfizetésre, vagy egyéb fizetési lehetőségeket. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* **Díjszabás és a rendelkezésre állási** – ezek a Virtuálisgép-méretek érhetők el csak a Standard tarifacsomagra. Ellenőrizze az [elérhető termékek régiók szerint] (https://azure.microsoft.com/regions/services/) a rendelkezésre állás érdekében az Azure-régióban. 
* **Magkvóta** – szükség lehet az Azure-előfizetésében az alapértelmezett érték a magkvóta növeléséhez. Az előfizetés is korlátozhatja az egyes virtuális gép többek között a H-sorozat családokban üzembe helyezhető magok számát. Kérje egy kvótájának növelését, [nyisson meg egy támogatási kérést online](../articles/azure-supportability/how-to-create-azure-support-request.md) díjmentesen. (Alapértelmezett korlátok az előfizetés kategória függvénye.)
  
  > [!NOTE]
  > Ha nagyméretű kapacitásigények, forduljon az Azure ügyfélszolgálatához. Azure kvóták kredit korlátai, nem a kapacitás garanciát. Függetlenül attól, a kvóta csak díjkötelesek mag használatát.
  > 
  > 
* **Virtuális hálózat** – Azure [virtuális hálózat](https://azure.microsoft.com/documentation/services/virtual-network/) a nagy számítási igényű példányok használatához nem szükséges. Azonban számos kell legalább egy felhő alapú Azure virtuális hálózat, vagy egy helyek közötti kapcsolatot, ha van szükség a helyszíni erőforrások eléréséhez. Ha szükséges, hozzon létre egy új virtuális hálózatot a példányok üzembe helyezéséhez. Nagy számítási igényű virtuális gépek hozzáadása egy affinitáscsoportban található egy virtuális hálózathoz nem támogatott.
* **Átméretezés** – speciális hardveradatokkal miatt csak méretezze át a nagy számítási igényű példányok belül az azonos virtuálisgépméret-családhoz (H-sorozat vagy a nagy számítási igényű A-sorozat). Ha például csak átméretezhető H-sorozatú VM egy H-sorozat mérete a másikra. Átméretezés eltér egy nem nagy számítási igényű, nagy számítási igényű méretre ezenkívül nem támogatott.  

## <a name="rdma-capable-instances"></a>RDMA-kompatibilis példányok
A funkció a távoli közvetlen memória-hozzáféréses (RDMA) kapcsolat egy hálózati adapter egy részét a nagy számítási igényű példányok (H16r, H16mr, a8-as és a9-es). (A kijelölt N-sorozat méretei NC24r például az "r" jelöléssel is is RDMA-kompatibilisek.) Ez az interfész a standard szintű Azure-hálózat felület érhető el, hogy más Virtuálisgép-méretek mellett van. 
  
Ez az interfész lehetővé teszi az RDMA-kompatibilis példány egy InfiniBand (IB) hálózat, a üzemeltetési FDR H16r, H16mr és RDMA-kompatibilis az N sorozatú virtuális gépek díjait, valamint A8 és a9-es virtuális gépek díjait QDR protokollt használó kommunikációra. Ezek az RDMA-képességeinek képes javítani a méretezhetőségi és az egyes Message Passing Interface (MPI) alkalmazásokhoz.

> [!NOTE]
> Az Azure-on keresztül IB IP nem támogatott. Csak IB feletti RDMA használata támogatott.
>

Az RDMA-kompatibilis HPC virtuális gépek üzembe helyezése az ugyanazon rendelkezésre állási vagy Virtuálisgép-méretezési csoportot (Ha használja az Azure Resource Manager üzemi modell) vagy ugyanazon a felhőszolgáltatáson (Ha használja a klasszikus üzemi modellhez). Ha egy Virtuálisgép-méretezési csoportot használ, győződjön meg arról, korlátozza az egyetlen elhelyezési csoportból; üzembe helyezés például egy Resource Manager-sablon beállítása az *singlePlacementGroup* tulajdonságot *igaz*. Kövesse a további követelmények HPC-RDMA-kompatibilis virtuális gépek az Azure RDMA hálózati hozzáféréshez.