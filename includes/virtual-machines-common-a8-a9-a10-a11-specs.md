---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 8de1163ab68b394b6eeaaad6412995156dbe7212
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901814"
---
## <a name="deployment-considerations"></a>Telepítési szempontok
* **Azure-előfizetés** – több számítási igényű példány üzembe helyezéséhez vegye fontolóra az utólagos elszámolású előfizetést vagy az egyéb vásárlási lehetőségeket. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* **Díjszabás és rendelkezésre állás** – ezeket a virtuálisgép-méreteket a rendszer csak a standard szintű díjszabás keretében kínálja. Tekintse meg az Azure-régiókban rendelkezésre [álló régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/) . 
* **Magok kvótája** – előfordulhat, hogy a magok kvótáját az alapértelmezett érték alapján kell megnövelni az Azure-előfizetésben. Előfordulhat, hogy az előfizetés bizonyos virtuálisgép-méretekben üzembe helyezhető magok számát is korlátozhatja, beleértve a H-sorozatot is. A kvóta növeléséhez [Nyisson meg egy online ügyfélszolgálati kérést](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) díjmentesen. (Az alapértelmezett korlátok az előfizetési kategóriától függően változhatnak.)
  
  > [!NOTE]
  > Ha nagy léptékű kapacitásra van szüksége, forduljon az Azure ügyfélszolgálatához. Az Azure-kvóták hitelkeretek, nem kapacitási garanciák. A kvótától függetlenül csak a használt magokért kell fizetnie.
  > 
  > 
* **Virtual Network (virtuális hálózat** ) – az Azure [virtuális hálózat](https://azure.microsoft.com/documentation/services/virtual-network/) nem szükséges a nagy számítási igényű példányok használatához. Számos központi telepítés esetében azonban szükség van legalább egy felhőalapú Azure-beli virtuális hálózatra, vagy egy helyek közötti kapcsolatra, ha a helyszíni erőforrásokhoz kell hozzáférnie. Ha szükséges, hozzon létre egy új virtuális hálózatot a példányok telepítéséhez. Ha nagy számítási igényű virtuális gépeket ad hozzá egy affinitási csoportban lévő virtuális hálózathoz, az nem támogatott.
* **Átméretezés** – a speciális hardverek miatt csak a számítási igényű példányokat lehet átméretezni ugyanazon a méretű családon belül (H-sorozat vagy nagy számítási igényű a-sorozat). Például csak egy h sorozatú virtuális gépet lehet átméretezni egy H-sorozatos méretről egy másikra. Emellett a nem számítási igényű méretről a nagy számítási igényű méretre való átméretezés nem támogatott.  

## <a name="rdma-capable-instances"></a>RDMA-kompatibilis példányok
A nagy számítási igényű példányok egy részhalmaza (A8, A9, H16r, H16mr, HB és HC) egy hálózati adaptert biztosít a távoli közvetlen memória-hozzáférési (RDMA) kapcsolathoz. Az r-vel jelölt kiválasztott N sorozatú méretek (például a NC24rs konfigurációk (NC24rs_v2 és NC24rs_v3) is RDMA-kompatibilisek. Ez az interfész a szabványos Azure hálózati adapteren kívül más virtuálisgép-méretek számára is elérhető. 
  
Ez az interfész lehetővé teszi, hogy a RDMA-kompatibilis példányok InfiniBand (IB) hálózaton keresztül kommunikáljanak, amely a HB, a HC, a H16r, a H16mr és a RDMA-kompatibilis N sorozatú virtuális gépekhez használható, valamint az A8-as és A9-es virtuális gépek QDR-díjain alapul. Ezek a RDMA-képességek növelhetik bizonyos Message Passing Interface-(MPI-) alkalmazások méretezhetőségét és teljesítményét. A sebességgel kapcsolatos további információkért tekintse meg az ezen a lapon található táblázatok részleteit.

> [!NOTE]
> Az Azure-ban az IB-alapú IP-cím csak az SR-IOV-kompatibilis virtuális gépeken támogatott (SR-IOV for InfiniBand, jelenleg HB és HC). Az IB protokollon keresztüli RDMA minden RDMA-kompatibilis példány esetében támogatott.
>

