---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ee32886ddb74bdbbe0f240310629c8ef26230a68
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
## <a name="deployment-considerations"></a>Telepítési szempontok
* **Azure-előfizetés** – a használatalapú előfizetés vagy egyéb beszerzési lehetőségek csak néhány számítási igényű példányok telepítése érdekében. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* **Tarifa- és rendelkezésre állás** -ezek a Virtuálisgép-méretek érhető el csak a Standard tarifacsomagot. Ellenőrizze a [régió elérhető termékek] (https://azure.microsoft.com/regions/services/) a Azure-régiók rendelkezésre állás érdekében. 
* **Magok kvóta** – szükség lehet az Azure-előfizetéshez az alapértelmezett érték a magok kvótájának növeléséhez. Az előfizetés is lehet, hogy korlátozza az egyes virtuális gép mérete családok, többek között a H-sorozat telepítheti magok száma. A kvóta növelését [nyissa meg az online támogatás ügyfélkérés](../articles/azure-supportability/how-to-create-azure-support-request.md) díjmentesen. (Alapértelmezett korlátokat eltérhetnek attól függően, hogy az előfizetés kategória.)
  
  > [!NOTE]
  > Ha nagyméretű kapacitási igényeihez van, forduljon az Azure támogatási szolgálatához. Azure-kvótákról jóváírás korlátozza, nem a kapacitás garanciát. Függetlenül attól, a kvóta van csak szó, a magok használatát.
  > 
  > 
* **Virtuális hálózati** – az Azure [virtuális hálózati](https://azure.microsoft.com/documentation/services/virtual-network/) a számítási igényű példányok használatához nem szükséges. Azonban a számos olyan telepítése esetén kell legalább egy felhőalapú Azure virtuális hálózat vagy a hely-hely kapcsolatot, ha a helyszíni erőforrások eléréséhez szükséges. Szükség esetén hozzon létre egy új virtuális hálózat a példányok telepítése. Az affinitáscsoportokban lévő virtuális hálózathoz számítási igényű virtuális gépek hozzáadása nem támogatott.
* **Átméretezése** – miatt a speciális hardverre átméretezése csak az ugyanazon mérete család (H-méretek és számítási igényű A-sorozatú) számítási igényű példányokat. Például csak átméretezheti H-sorozat VM egy H sorozatnak megfelelő méretűre egy másikra. A számítási igényű méretre eltér egy nem számítási intenzív átméretezése ezenkívül nem támogatott.  

## <a name="rdma-capable-instances"></a>Az RDMA-kompatibilis példányok
A számítási igényű példányok (H16r, H16mr, A8 és A9) egy részét a beállítást, a távoli közvetlen memória-hozzáférés (RDMA) hálózati kapcsolatot a hálózati adaptert. (Kijelölt N-sorozat "r" NC24r például kijelölt értékek is RDMA-kompatibilis.) Ez az interfész mellett más Virtuálisgép-méretek elérhető szabványos Azure hálózati kapcsolat van. 
  
Ez az interfész lehetővé teszi az RDMA-kompatibilis példányok egy InfiniBand (IB) hálózaton, működő FDR díjszabás H16r H16mr és RDMA-kompatibilis N sorozatú virtuális gépekhez, és QDR díjszabás A8 és A9 virtuális gépekhez való kommunikációhoz. E RDMA-képességeinek képes javítani, a méretezhetőség és a bizonyos Message Passing Interface (MPI) alkalmazások teljesítményét.

> [!NOTE]
> Az Azure IP / IB nem támogatott. Csak IB feletti RDMA esetén támogatott.
>

Telepítse az RDMA-kompatibilis HPC az azonos rendelkezésre állási készlet vagy Virtuálisgép-méretezési készlet (Ha használja az Azure Resource Manager telepítési modell) vagy felhő szolgáltatásának ugyanazt a virtuális gépek (Ha használja a klasszikus üzembe helyezési modellel). Hajtsa végre a HPC-RDMA-kompatibilisek-e virtuális gépek az Azure RDMA hálózati elérésére vonatkozó további követelményeket.