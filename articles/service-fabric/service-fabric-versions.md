---
title: További tudnivalók az Azure Service Fabric-fürt verziók |} A Microsoft Docs
description: Az Azure Service Fabric-fürt támogatott verziók
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
ms.openlocfilehash: d99000e1682b662f4bceb28096395243c894282f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681607"
---
# <a name="supported-service-fabric-versions"></a>A Service Fabric támogatott verziói

Győződjön meg arról, hogy a fürt minden esetben támogatott a Service Fabric verziót futtat. És hogy jelentjük, hogy a Service Fabric egy új verziójának, az előző verzió után 60 napon, hogy legalább támogatásuk van megjelölve. Az új kiadásokkal már bejelentettünk [a Service Fabric blogján](https://blogs.msdn.microsoft.com/azureservicefabric/).

Tekintse meg a következő dokumentumokban talál útmutatást, hogy a fürt Service Fabric támogatott verziót futtat.

- [Az Azure-fürtön a Service Fabric-verzió frissítése](service-fabric-cluster-upgrade.md)
- [A Service Fabric verziója egy önálló windows server-fürt frissítése](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Támogatott verziók

A következő táblázat felsorolja a Service Fabric-verziók által támogatott és a támogatás záró dátumát.

| **A fürt Service Fabric-futtatókörnyezet** | **Közvetlenül a fürt verziója frissíthető** |**Kompatibilis SDK / NuGet-csomag verziója** | **Támogatásának vége** |
| --- | --- |--- | --- |
| Fürt összes 5.3.121 korábbi verziók | 5.1.158* |Kisebb vagy egyenlő 2.3-as verzió |2017. január 20. |
| 5.3.* | 5.1.158.* |Kisebb vagy egyenlő 2.3-as verzió |2017. február 24. |
| 5.4.* | 5.1.158.* |Kisebb vagy egyenlő 2.4-es verzió |Előfordulhat, hogy 10,2017       |
| 5.5.* | 5.4.164.* |Kisebb vagy egyenlő 2.5-ös verzió |Augusztus 10,2017    |
| 5.6.* | 5.4.164.* |Kisebb vagy egyenlő 2.6-os verzió |Október 13,2017   |
| 5.7.* | 5.4.164.* |Kisebb vagy egyenlő 2.7-es verzió |December 15,2017  |
| 6.0.* | 5.6.205.* |Kisebb vagy egyenlő 2.8-as verziója |30,2018. március     |
| 6.1.* | 5.7.221.* |Kisebb vagy egyenlő, mint a 3.0-s verzió |Július 15,2018      |
| 6.2.* | 6.0.232.* |Kisebb vagy egyenlő, mint 3.1-es verzióját |Október 26,2018   |
| 6.3.* | 6.1.480.* |Kisebb vagy egyenlő 3.2-es verziója |31,2019. március  |
| 6.4.* | 6.2.301.* |Kisebb vagy egyenlő 3.3-as verziója |Aktuális verzióját, és ezért nincs befejezési dátum |

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

 Az alábbi táblázat a támogatott operációs rendszerek támogatott a Service Fabric-verzió.

| **Operációs rendszer** | **Legkorábbi támogatott Service Fabric verziója** |
| --- | --- |
| Windows Server 2012 R2 | Az összes verzió |
| Windows Server 2016 | Az összes verzió |
| Windows Server 1709 | 6.0 |
| A Windows Server 1803-as verzióban | 6.4 |
| A Windows Server 1809 | 6.4.654.9590 |
| A Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

