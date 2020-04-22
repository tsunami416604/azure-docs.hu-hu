---
title: Támogatott fürtverziók az Azure Service Fabricben
description: Ismerje meg az Azure Service Fabric fürtverzióit, beleértve a Service Fabric csapatblogjának legújabb kiadásaira mutató hivatkozást.
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.openlocfilehash: b68314a116b0d9da8baf5f61eeffeef2b30835c4
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732874"
---
# <a name="supported-service-fabric-versions"></a>A Service Fabric támogatott verziói

Győződjön meg arról, hogy a fürt mindig egy támogatott Azure Service Fabric-verziót futtat. Legalább 60 nappal azután, hogy bejelenti a Service Fabric új verziójának kiadását, az előző verzió támogatása véget ér. Az új kiadások bejelentéseit a [Service Fabric csapatblogjában](https://azure.microsoft.com/updates/?product=service-fabric)találja.

A fürt támogatott Service Fabric-verziófuttatásának fenntartásáról az alábbi dokumentumokban tájékodhat:

- [Azure Service Fabric-fürt frissítése](service-fabric-cluster-upgrade.md)
- [Az önálló Windows Server-fürtön futó Service Fabric-verzió frissítése](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Támogatott verziók

Az alábbi táblázat a Service Fabric verzióit és azok támogatási befejezési dátumait sorolja fel.

| A Service Fabric futásideje a fürtben | Közvetlenül frissíthető a fürtverziójából |Kompatibilis SDK vagy NuGet csomagverzió | A támogatás megszűnése |
| --- | --- |--- | --- |
| Az összes fürtverzió az 5.3.121 előtt | 5.1.158.* |Kisebb vagy egyenlő, mint a 2.3-as verzió |2017. január 20., az a hét, aki nem |
| 5.3.* | 5.1.158.* |Kisebb vagy egyenlő, mint a 2.3-as verzió |2017. február 24.February 24, 2017 |
| 5.4.* | 5.1.158.* |Kisebb vagy egyenlő, mint a 2.4-es verzió |2017. május 10.       |
| 5.5.* | 5.4.164.* |Kisebb vagy egyenlő, mint a 2.5-ös verzió |2017. augusztus 10.,    |
| 5.6.* | 5.4.164.* |Kisebb vagy egyenlő, mint a 2.6-os verzió |2017. október 13.,   |
| 5.7.* | 5.4.164.* |Kisebb vagy egyenlő, mint a 2.7-es verzió |2017. december 15.  |
| 6.0.* | 5.6.205.* |Kisebb vagy egyenlő, mint a 2.8-as verzió |2018. március 30.March 30, 2018     |
| 6.1.* | 5.7.221.* |Kisebb vagy egyenlő, mint a 3.0-s verzió |2018. július 15.July 15, 2018      |
| 6.2.* | 6.0.232.* |Kisebb vagy egyenlő, mint a 3.1-es verzió |2018. október 26.October 26, 2018   |
| 6.3.* | 6.1.480.* |Kisebb vagy egyenlő, mint a 3.2-es verzió |2019. március 31.March 31, 2019  |
| 6.4.* | 6.2.301.* |Kisebb vagy egyenlő, mint a 3.3-as verzió |2019. szeptember 15.September 15, 2019 |
| 6.5.* | 6.4.617.* |Kisebb vagy egyenlő, mint a 3.4-es verzió |2020. augusztus 1. |
| 7.0.466.* | 6.4.664.* |Kisebb vagy egyenlő, mint a 4.0-s verzió|2020. augusztus 1.  |
| 7.0.466.* | 6.5.* |Kisebb vagy egyenlő, mint a 4.0-s verzió|2020. augusztus 1. |
| 7.0.470.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4.0-s verzió |2020. augusztus 1.  |
| 7.1.409.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4.0-s verzió |Jelenlegi verzió, így nincs befejezési dátum |

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az alábbi táblázat a támogatott Service Fabric-verziók támogatott operációs rendszereit sorolja fel.

| Operációs rendszer | Legkorábbi támogatott Service Fabric-verzió |
| --- | --- |
| Windows Server 2012 R2 | Az összes verzió |
| Windows Server 2016 | Az összes verzió |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>Támogatott verziónevek

Az alábbi táblázat a Service Fabric verziónevét és a hozzájuk tartozó verziószámokat sorolja fel.

| Verziónév | Windows verziószáma | Linux verziószám |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | NA |
| 5.3 CU1 | 5.3.204.9494 | NA |
| 5.3 CU2 | 5.3.301.9590 | NA |
| 5.3 CU3 | 5.3.311.9590 | NA |
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
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | NA |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
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
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7.1 RTO | 7.1.409.9590 | 7.1.410.1 |
