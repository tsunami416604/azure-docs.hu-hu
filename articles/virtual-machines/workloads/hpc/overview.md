---
title: Nagy teljesítményű számítástechnika H sorozatú virtuális gépeken – Azure virtuális gépek
description: Ismerje meg a HPC-re optimalizált H sorozatú virtuális gépek funkcióit és képességeit.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76271023"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Nagy teljesítményű számítástechnika H sorozatú virtuális gépeken

A HB sorozatú és HC-sorozatú virtuális gépek nagy teljesítményű számítástechnikai (HPC) szolgáltatása az Azure-beli virtuális gépek legoptimálisabb HPC-teljesítményét teszi lehetővé. A HPC-optimalizált virtuális gépek a legnehezebb matematikai problémák megoldására szolgálnak, mint például: folyadékdinamika, olaj- és gázszimulációk és időjárásmodellezés.

Ez a cikk a HB-sorozatú és hc-sorozatú virtuális gépek néhány fő bb jellemzőjét ismerteti, ezért ezek a virtuális gépek jól teljesítenek a HPC-forgatókönyvekben, és hogyan kezdheti el a dolgokat.

## <a name="features-and-capabilities"></a>Funkciók és képességek

A HB-sorozatú és HC-sorozatú virtuális gépek et úgy tervezték, hogy a legjobb HPC-teljesítményt, mpi-es hangskálázhatóságot és költséghatékonyságot biztosítsanak a HPC-munkaterhelésekhez.

### <a name="message-passing-interface"></a>Üzenetátadási felület

A HB-sorozatú és HC-sorozatú sorozatok szinte minden MPI-típust és verziót támogatnak. A leggyakoribb, támogatott MPI-típusok a következők: OpenMPI, MVAPICH2, Platform MPI, Intel MPI és minden távoli közvetlen memória-hozzáférési (RDMA) ige. További információ: [Üzenetátadási felület beállítása a HPC-hez](setup-mpi.md)című témakörben.

### <a name="rdma-and-infiniband"></a>RDMA és InfiniBand

Az RDMA interfész szabványos a HB-sorozatú és HC-sorozatú virtuális gépeken. Az RDMA-képes példányok InfiniBand hálózaton keresztül kommunikálnak, és a HB- és HC-sorozatú virtuális gépek esetében megnövelt adatátviteli sebességgel (EDR) működnek. Az RDMA-kompatibilis példányok növelhetik egyes MPI-alkalmazások méretezhetőségét és teljesítményét.

A HB sorozatú és HC-sorozatú virtuális gépeket támogató InfiniBand konfiguráció nem blokkoló zsírfák, amelyek alacsony átmérőjűek a konzisztens RDMA-teljesítmény érdekében.

Az [InfiniBand engedélyezése](enable-infiniband.md) című témakörben olvashat bővebben az InfiniBand HB- vagy HC-sorozatú virtuális gépeken való beállításáról.

## <a name="get-started"></a>Bevezetés

Először döntse el, hogy melyik H-sorozatú virtuális gép lesz használva. A HPC-optimalizált virtuális gépekről a [HB-sorozat áttekintése](hb-series-overview.md) és [a HC-sorozat áttekintése című témakörben olvashat részletesen.](hc-series-overview.md) A specifikációkról a [Nagy teljesítményű számítási virtuálisgépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)című témakörben látható.

Miután kiválasztotta és létrehozta az alkalmazás virtuális gépét, konfigurálnia kell azt az InfiniBand engedélyezésével. Az InfiniBand windowsos és linuxos virtuális gépeken való engedélyezéséről az [InfiniBand engedélyezése (Engedélyezése) témakörben](enable-infiniband.md)olvashat.

A HPC-munkaterhelések kritikus összetevője az MPI. A HB-sorozatú és HC-sorozatú sorozatok szinte minden MPI-típust és verziót támogatnak. További információ: [Üzenetátadási felület beállítása a HPC-hez](setup-mpi.md)című témakörben.

Miután kiválasztotta a virtuális gép sorozatát, beállította az Infiniband-et és az MPI-t, készen áll a HPC-számítási feladatok létrehozásának megkezdésére.

## <a name="next-steps"></a>További lépések

- Tekintse át a [HB-sorozat áttekintését](hb-series-overview.md) és a [HC-sorozat áttekintését,](hc-series-overview.md) hogy megismerje a legfontosabb különbségeket és specifikációkat.

- A HPC-számítási feladatok futtatásának magasabb szintű architekturális nézetéről a [Nagy teljesítményű számítástechnikát (HPC) az Azure-ban című témakörben tekintheti](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)meg.
