---
title: Nagy teljesítményű számítástechnika a H-sorozatú virtuális gépek – az Azure Virtual Machines |} A Microsoft Docs
description: Ismerje meg a funkciók és képességek H-sorozatú virtuális gépek HPC optimalizálva.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.author: amverma
ms.openlocfilehash: d6e857a87e4c7df8ffb2be1eefb7a0290da5b10a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800063"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Nagy teljesítményű feldolgozási H-sorozatú virtuális gépeken

Nagy teljesítményű feldolgozási (HPC) HB sorozatú és a hibrid kapcsolat-sorozat virtuális gépei engedélyezése a legtöbb optimalizált HPC teljesítmény olyan virtuális gépek az Azure-ban. HPC-optimalizált virtuális gépek legbonyolultabb matematikai problémák például megoldására használhatók: folyadékdinamika, az olaj- és szimulációkat vagy az időjárást modellezik.

Ez a cikk ismerteti az egyes kulcsfunkciói HB sorozatú és a hibrid kapcsolat-sorozat virtuális gépei, hogy miért ezek a virtuális gépek működik jól HPC felhasználási helyzetek, és hogyan kezdheti el.

## <a name="features-and-capabilities"></a>Funkciók és képességek

HB sorozatú és a hibrid kapcsolat-sorozat virtuális gépei biztosít a HPC a legjobb teljesítmény érdekében a message passing interface (MPI) méretezhetőséget, és költséghatékonyságot HPC számítási feladatokhoz.

### <a name="message-passing-interface"></a>Üzenet megadásának felület

HB- és hibrid kapcsolat sorozatú támogatja a szinte az összes MPI-típusok és verziók. A legtöbb általános, támogatott MPI típusok a következők: OpenMPI, MVAPICH2, Platform MPI, Intel MPI-t és az összes távoli közvetlen memória hozzáférés (RDMA) műveletek. További információkért lásd: [Message Passing Interface beállítása HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA- és InfiniBand

Az RDMA-adapter je standardní HB sorozatú és a hibrid kapcsolat-sorozat virtuális gépei. RDMA-kompatibilis példány egy InfiniBand hálózathoz, a működési adatok alapján (EDR) HB- és hibrid kapcsolat sorozatú virtuális gépek keresztül kommunikálnak. RDMA-kompatibilis példányok képes javítani a méretezhetőség és néhány MPI-alkalmazások teljesítményét.

Az InfiniBand konfiguráció HB sorozatú és a hibrid kapcsolat-sorozat virtuális gépei, nem blokkoló fat olyan egy alacsony átmérőjű teljesítménybeli tervezés a konzisztens rdma-t.

Lásd: [engedélyezése InfiniBand](enable-infiniband.md) tudhat meg többet a HB sorozat és a hibrid kapcsolat-sorozat virtuális gépei InfiniBand beállítása.

## <a name="get-started"></a>Bevezetés

Először döntse el, melyik H-sorozatú virtuális gépek használni fog. HPC részleteit optimalizált virtuális gépek, lásd: [HB sorozat áttekintése](hb-series-overview.md) és [HC sorozat áttekintése](hc-series-overview.md). Leírások, lásd: [nagy teljesítményű számítási Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Miután kiválasztott, és létrehozott egy virtuális Gépet az alkalmazáshoz, kell InfiniBand engedélyezésével konfigurálja azt. Ismerje meg, hogyan engedélyezheti a Windows és Linux rendszerű virtuális gépek is InfiniBand, lásd: [engedélyezése InfiniBand](enable-infiniband.md).

A HPC számítási feladatok kritikus összetevője MPI. HB- és hibrid kapcsolat sorozatú támogatja a szinte az összes MPI-típusok és verziók. További információkért lásd: [Message Passing Interface beállítása HPC](setup-mpi.md).

Ha kiválasztotta a Virtuálisgép-sorozatok, Infiniband és MPI, készen áll a HPC számítási feladatok kiépítésének megkezdésére.

## <a name="next-steps"></a>További lépések

- Tekintse át a [HB sorozat áttekintése](hb-series-overview.md) és [HC sorozat áttekintése](hc-series-overview.md) különbségeket és specifikációkat ismerteti.

- A magasabb szintű architekturális nézet HPC számítási feladatok, lásd: [magas teljesítményt feldolgozási (HPC) az Azure-ban](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
