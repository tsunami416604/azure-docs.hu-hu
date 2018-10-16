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
ms.openlocfilehash: c12fff63cdb7241d89e7511a3dac2ff9c1363ae6
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49346104"
---
## <a name="deployment-considerations"></a>Telepítési szempontok
* **Azure-előfizetés** –, több nagy számítási igényű példányok üzembe helyezése, fontolja meg a használatalapú fizetéses előfizetésre, vagy egyéb fizetési lehetőségeket. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* **Díjszabás és a rendelkezésre állási** – ezek a Virtuálisgép-méretek érhetők el csak a Standard tarifacsomagra. Ellenőrizze [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) a rendelkezésre állás érdekében az Azure-régióban. 
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

