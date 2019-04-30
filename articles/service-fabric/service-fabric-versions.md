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
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: 75e95737eecb9407a80103d1cad00d4987fe7091
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716081"
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
| 6.4.* | 6.2.301.* |Kisebb vagy egyenlő 3.3-as verziója |Aktuális verzió, ezért nincs befejezési dátum |

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

