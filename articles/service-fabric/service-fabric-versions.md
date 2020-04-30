---
title: Támogatott fürtözött verziók az Azure Service Fabric
description: Ismerje meg az Azure Service Fabric-beli fürtök verzióit, beleértve a Service Fabric csapat blogján elérhető legújabb kiadásokra mutató hivatkozást is.
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.openlocfilehash: b68314a116b0d9da8baf5f61eeffeef2b30835c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732874"
---
# <a name="supported-service-fabric-versions"></a>Támogatott Service Fabric verziók

Győződjön meg arról, hogy a fürt mindig támogatott Azure Service Fabric verziót futtat. Legalább 60 nappal azután, hogy bejelentjük a Service Fabric új verziójának kiadását, az előző verzió támogatása megszűnik. Az új kiadásokról a [Service Fabric csapat blogján](https://azure.microsoft.com/updates/?product=service-fabric)talál hirdetményeket.

A következő dokumentumokban tájékozódhat arról, hogyan tarthatja meg a fürt egy támogatott Service Fabric verzióját:

- [Azure Service Fabric-fürt frissítése](service-fabric-cluster-upgrade.md)
- [Az önálló Windows Server-fürtön futó Service Fabric verziójának frissítése](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Támogatott verziók

A következő táblázat felsorolja a Service Fabric verzióit és azok támogatásának befejezési dátumát.

| Service Fabric futtatókörnyezet a fürtben | Közvetlenül a fürt verziójáról is frissíthető |Kompatibilis SDK-vagy NuGet-csomag verziója | Támogatás vége |
| --- | --- |--- | --- |
| A 5.3.121 előtti összes fürt verziója | 5.1.158.* |Kisebb vagy egyenlő, mint a 2,3-es verzió |Január 20, 2017 |
| 5,3. * | 5.1.158.* |Kisebb vagy egyenlő, mint a 2,3-es verzió |Február 24., 2017 |
| 5,4. * | 5.1.158.* |Kisebb vagy egyenlő, mint a 2,4-es verzió |2017. május 10.       |
| 5,5. * | 5.4.164.* |Kisebb vagy egyenlő, mint a 2,5-es verzió |2017. augusztus 10.    |
| 5,6. * | 5.4.164.* |Kisebb vagy egyenlő, mint a 2,6-es verzió |2017. október 13.   |
| 5,7. * | 5.4.164.* |Kisebb vagy egyenlő, mint a 2,7-es verzió |2017. december 15.  |
| 6,0. * | 5.6.205.* |Kisebb vagy egyenlő, mint a 2,8-es verzió |Március 30., 2018     |
| 6,1. * | 5.7.221.* |Kisebb vagy egyenlő, mint a 3,0-es verzió |Július 15., 2018      |
| 6,2. * | 6.0.232.* |Kisebb vagy egyenlő, mint a 3,1-es verzió |Október 26., 2018   |
| 6,3. * | 6.1.480.* |Kisebb vagy egyenlő, mint a 3,2-es verzió |Március 31., 2019  |
| 6,4. * | 6.2.301.* |Kisebb vagy egyenlő, mint a 3,3-es verzió |Szeptember 15., 2019 |
| 6,5. * | 6.4.617.* |Kisebb vagy egyenlő, mint a 3,4-es verzió |Augusztus 1-től 2020 |
| 7.0.466.* | 6.4.664.* |Kisebb vagy egyenlő, mint a 4,0-es verzió|Augusztus 1-től 2020  |
| 7.0.466.* | 6,5. * |Kisebb vagy egyenlő, mint a 4,0-es verzió|Augusztus 1-től 2020 |
| 7.0.470.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,0-es verzió |Augusztus 1-től 2020  |
| 7.1.409.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,0-es verzió |Aktuális verzió, így nincs befejező dátum |

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A következő táblázat felsorolja a támogatott Service Fabric-verziókhoz tartozó támogatott operációs rendszereket.

| Operációs rendszer | A legkorábbi támogatott Service Fabric verziója |
| --- | --- |
| Windows Server 2012 R2 | Az összes verzió |
| Windows Server 2016 | Az összes verzió |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16,04 | 6.0 |

## <a name="supported-version-names"></a>Támogatott verziók nevei

A következő táblázat a Service Fabric verziószámait és a hozzájuk tartozó verziószámokat sorolja fel.

| Verzió neve | Windows verziószáma | Linux verziószáma |
| --- | --- | --- |
| 5,3 RTO | 5.3.121.9494 | NA |
| 5,3 CU1 | 5.3.204.9494 | NA |
| 5,3 CU2 | 5.3.301.9590 | NA |
| 5,3 CU3 | 5.3.311.9590 | NA |
| 5,4 CU2 | 5.4.164.9494 | NA |
| 5,5 CU1 | 5.5.216.0    | NA |
| 5,5 CU2 | 5.5.219.0    | NA |
| 5,5 CU3 | 5.5.227.0    | NA |
| 5,5 CU4 | 5.5.232.0    | NA |
| 5,6 RTO | 5.6.204.9494 | NA |
| 5,6 CU2 | 5.6.210.9494 | NA |
| 5,6 CU3 | 5.6.220.9494 | NA |
| 5,7 RTO | 5.7.198.9494 | NA |
| 5,7 CU4 | 5.7.221.9494 | NA |
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | NA |
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | NA |
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | NA |
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | NA |
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7,0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7,1 RTO | 7.1.409.9590 | 7.1.410.1 |
