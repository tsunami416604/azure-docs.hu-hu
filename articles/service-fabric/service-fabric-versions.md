---
title: Fürtverziók támogatott az Azure Service Fabricben |} A Microsoft Docs
description: Az Azure Service Fabric fürtverziók megismerése
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/03/2019
ms.author: aljo
ms.openlocfilehash: 030e57512d10f31ca03dff2f99e57c305de49e97
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565979"
---
# <a name="supported-service-fabric-versions"></a>A Service Fabric támogatott verziói

Ellenőrizze, hogy a fürt minden esetben az Azure Service Fabric támogatott verziót futtat. Legalább 60 nap után azt jelentjük be az új verzióját a Service Fabric, az előző verzió támogatása véget ér. A közleményeket az új kiadásokkal találja a [Service Fabric-csapat blogja](https://blogs.msdn.microsoft.com/azureservicefabric/).

Tekintse meg a következő dokumentumokat a részleteket az, hogy a fürt Service Fabric támogatott verziója fut:

- [Egy Azure Service Fabric-fürt frissítése](service-fabric-cluster-upgrade.md)
- [Frissítés a Service Fabric-verzióra, amely a különálló Windows Server-fürtön](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Támogatott verziók

A következő táblázat felsorolja a Service Fabric és a támogatás záró dátumát verzióit.

| A fürt Service Fabric-futtatókörnyezet | Közvetlenül a fürt verziója frissíthető |Kompatibilis SDK-t vagy a NuGet-csomag verziója | Támogatás vége |
| --- | --- |--- | --- |
| Az összes fürtverziók 5.3.121 előtt | 5.1.158.* |Kisebb vagy egyenlő 2.3-as verzió |2017. január 20. |
| 5.3.* | 5.1.158.* |Kisebb vagy egyenlő 2.3-as verzió |2017. február 24. |
| 5.4.* | 5.1.158.* |Kisebb vagy egyenlő 2.4-es verzió |2017. május 10.       |
| 5.5.* | 5.4.164.* |Kisebb vagy egyenlő 2.5-ös verzió |Augusztus 10,2017    |
| 5.6.* | 5.4.164.* |Kisebb vagy egyenlő 2.6-os verzió |Október 13,2017   |
| 5.7.* | 5.4.164.* |Kisebb vagy egyenlő 2.7-es verzió |2017. december 15.  |
| 6.0.* | 5.6.205.* |Kisebb vagy egyenlő 2.8-as verziója |2018. március 30.     |
| 6.1.* | 5.7.221.* |Kisebb vagy egyenlő, mint a 3.0-s verzió |2018. július 15.      |
| 6.2.* | 6.0.232.* |Kisebb vagy egyenlő, mint 3.1-es verzióját |2018. október 26.   |
| 6.3.* | 6.1.480.* |Kisebb vagy egyenlő 3.2-es verziója |2019. március 31-ig.  |
| 6.4.* | 6.2.301.* |Kisebb vagy egyenlő 3.3-as verziója |2019. szeptember 15. |
| 6.5.* | 6.4.617.* |Kisebb vagy egyenlő 3.4-es verziójú |Aktuális verzió, ezért nincs befejezési dátum |

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az alábbi táblázat a támogatott operációs rendszerek támogatott a Service Fabric-verzió.

| Operációs rendszer | Legkorábbi támogatott a Service Fabric-verzió |
| --- | --- |
| Windows Server 2012 R2 | Az összes verzió |
| Windows Server 2016 | Az összes verzió |
| Windows Server 1709 | 6.0 |
| A Windows Server 1803-as verzióban | 6.4 |
| A Windows Server 1809 | 6.4.654.9590 |
| A Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>Támogatott verziók

A következő táblázat felsorolja a Service Fabric és a megfelelő verziószámok verzió neve.

| Verziónév | Windows-verzió száma | Linux-verziószám |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | NA |
| 5.3 CU1 | 5.3.204.9494 | NA |
| 5.3 CU2 | 5.3.301.9590 | NA |
| 5.3 CU3 | 5.3.311.9590 | NA |
| 5.4 CU2 | 5.4.164.9494 | NA |
| 5.5-ÖS CU1 | 5.5.216.0    | NA |
| 5.5-ÖS CU2 | 5.5.219.0    | NA |
| 5.5-ÖS CU3 | 5.5.227.0    | NA |
| 5.5-ÖS CU4 | 5.5.232.0    | NA |
| 5.6-OS RTO | 5.6.204.9494 | NA |
| 5.6-OS CU2 | 5.6.210.9494 | NA |
| 5.6-OS CU3 | 5.6.220.9494 | NA |
| 5.7 RTO | 5.7.198.9494 | NA |
| 5.7 CU4 | 5.7.221.9494 | NA |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
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
| 6.4 CU2 | 6.4.622.9590 | NA |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | NA |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU8 | 6.4.670.9590 | NA |
| 6.5-ÖS RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.5-ÖS CU1 | 6.5.641.9590 | 6.5.454.1 |