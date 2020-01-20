---
title: Nagy teljesítményű számítástechnika a H-sorozatú virtuális gépeken – Azure Virtual Machines
description: Ismerje meg a HPC-re optimalizált H-sorozatú virtuális gépek funkcióit és képességeit.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271023"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Nagy teljesítményű számítástechnika a H-sorozatú virtuális gépeken

A HB és a HC sorozatú virtuális gépek nagy teljesítményű számítástechnikai (HPC) technológiái lehetővé teszik az Azure-beli virtuális gépek legnagyobb mértékben optimalizált HPC-teljesítményét. A HPC-optimalizált virtuális gépek a legnehezebb matematikai problémák megoldására szolgálnak, például: a Fluid Dynamics, az olaj-és a gáz-szimulációk, valamint az időjárási modellezés.

Ez a cikk a HB és a HC sorozatú virtuális gépek főbb funkcióit ismerteti, ezért a virtuális gépek a HPC-forgatókönyvekben jól teljesítenek, és hogyan kezdheti el a lépéseket.

## <a name="features-and-capabilities"></a>Funkciók és képességek

A HB és a HC sorozatú virtuális gépek úgy vannak kialakítva, hogy biztosítsák a legjobb HPC-teljesítményt, a Message Passing Interface (MPI) skálázhatóságot és a költséghatékonyságot a HPC számítási feladataihoz.

### <a name="message-passing-interface"></a>Üzenet átadása illesztőfelület

A HB-sorozat és a HC-sorozat csaknem minden MPI-típust és-verziót támogat. A leggyakoribb, támogatott MPI-típusok a következők: OpenMPI, MVAPICH2, platform MPI, Intel MPI és minden távoli közvetlen memória-hozzáférési (RDMA) művelet. További információk: [Message Passing Interface beállítása HPC-hez](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA és InfiniBand

A RDMA felület a HB és a HC sorozatú virtuális gépek esetében a standard. A RDMA-kompatibilis példányok egy InfiniBand-hálózaton keresztül kommunikálnak, a HB-sorozat és a HC sorozatú virtuális gépek esetében pedig fokozott adatforgalmi díjszabással (EDR) működnek. A RDMA-kompatibilis példányok képesek növelni bizonyos MPI-alkalmazások méretezhetőségét és teljesítményét.

A HB-sorozatot és a HC-sorozatú virtuális gépeket támogató InfiniBand-konfiguráció nem blokkolja a FAT Trees-t, amely alacsony átmérőjű kialakítást biztosít a konzisztens RDMA teljesítmény érdekében.

A InfiniBand a HB-sorozatú vagy a HC-sorozatú virtuális gépeken való beállításával kapcsolatos további tudnivalókért tekintse meg a [InfiniBand engedélyezése](enable-infiniband.md) című témakört.

## <a name="get-started"></a>Az első lépések

Először döntse el, melyik H-sorozatú virtuális gépet fogja használni. A HPC-optimalizált virtuális gépekről további részleteket a [HB-sorozat áttekintése](hb-series-overview.md) és a [HC-sorozat áttekintése](hc-series-overview.md)című témakörben talál. A specifikációkat lásd: [nagy teljesítményű számítási](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)virtuálisgép-méretek.

Miután kiválasztotta és létrehozott egy virtuális gépet az alkalmazáshoz, konfigurálnia kell a InfiniBand engedélyezésével. A InfiniBand Windows és Linux rendszerű virtuális gépeken való engedélyezéséről a [InfiniBand engedélyezése](enable-infiniband.md)című témakörben olvashat bővebben.

A HPC-munkaterhelések kritikus összetevője az MPI. A HB-sorozat és a HC-sorozat csaknem minden MPI-típust és-verziót támogat. További információk: [Message Passing Interface beállítása HPC-hez](setup-mpi.md).

Miután kiválasztotta a virtuális gép sorozatát, állítsa be a InfiniBand és az MPI-t, készen áll a HPC-munkaterhelések kiépítése.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [HB-sorozat áttekintését](hb-series-overview.md) és a [HC-sorozat](hc-series-overview.md) áttekintését, és ismerkedjen meg a legfontosabb különbségekkel és specifikációkkal.

- A HPC számítási feladatainak futtatásához szükséges építészeti áttekintést lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)-ban.
