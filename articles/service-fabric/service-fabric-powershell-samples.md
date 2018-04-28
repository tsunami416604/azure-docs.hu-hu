---
title: Azure PowerShell-minták – Service Fabric | Microsoft Docs
description: Azure PowerShell-minták – Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: rwike77
manager: timlt
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 04/09/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 92dbdf2d2e3bdc48cda9ef0e0a53212cc7ea4cb7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-powershell-samples"></a>Azure PowerShell-minták

A következő táblázat a Service Fabric-fürtöket, -alkalmazásokat és -szolgáltatásokat létrehozó és kezelő PowerShell-mintaszkriptekre mutató hivatkozásokat tartalmaz.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Fürt létrehozása** ||
| [Fürt létrehozása (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Létrehoz egy Azure Service Fabric-fürtöt. |
|[Tesztfürt létrehozása (Azure)](./scripts/service-fabric-powershell-create-test-cluster.md)| Létrehoz egy három csomópontos Service Fabric-tesztfürtöt az Azure-ban.|
| **A fürt, a csomópontok és az infrastruktúra kezelése** ||
| [Alkalmazástanúsítvány hozzáadása](./scripts/service-fabric-powershell-add-application-certificate.md)| Egy X.509-alkalmazástanúsítványt rendel a fürt összes csomópontjához. |
| [Az RPD-porttartomány frissítése a fürt virtuális gépein](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Módosítja az RPD-porttartományt egy üzembe helyezett fürt csomópontbeli virtuális gépein.|
| [A fürtcsomóponti virtuális gépek rendszergazdai felhasználójának és jelszavának frissítése](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Frissíti a fürtcsomóponti virtuális gépek rendszergazdai felhasználónevét és jelszavát. |
| [Port megnyitása a terheléselosztóban](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Megnyit egy alkalmazásportot az Azure Load Balancerben, hogy egy adott porton engedélyezze a bejövő forgalmat. |
| [Bejövő hálózat biztonságicsoport-szabályának létrehozása](./scripts/service-fabric-powershell-add-nsg-rule.md) | Létrehoz egy bejövő hálózati biztonsági csoportra vonatkozó szabályt a fürtre beérkező forgalom engedélyezésére egy adott porton. |
| **Alkalmazások kezelése** ||
| [Alkalmazás üzembe helyezése](./scripts/service-fabric-powershell-deploy-application.md)| Alkalmazás üzembe helyezése egy fürtön.|
| [Alkalmazás frissítése](./scripts/service-fabric-powershell-upgrade-application.md)| Egy alkalmazás frissítése.|
| [Alkalmazás eltávolítása](./scripts/service-fabric-powershell-remove-application.md)| Alkalmazás eltávolítása egy fürtről.|
