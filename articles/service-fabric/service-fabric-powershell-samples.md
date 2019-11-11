---
title: Azure PowerShell-minták – Service Fabric | Microsoft Docs
description: Azure PowerShell-minták – Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: cae88e142c3bca15e837db7f084eef68434a78ca
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903271"
---
# <a name="azure-powershell-samples"></a>Azure PowerShell-minták

A következő táblázat a Service Fabric-fürtöket, -alkalmazásokat és -szolgáltatásokat létrehozó és kezelő PowerShell-mintaszkriptekre mutató hivatkozásokat tartalmaz.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Fürt létrehozása** ||
| [Fürt létrehozása (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Létrehoz egy Azure Service Fabric-fürtöt. |
| **A fürt, a csomópontok és az infrastruktúra kezelése** ||
| [Alkalmazástanúsítvány hozzáadása](./scripts/service-fabric-powershell-add-application-certificate.md)| Létrehoz egy X509-tanúsítványt Key Vault és üzembe helyezi egy virtuálisgép-méretezési csoportba a fürtben. |
| [Az RPD-porttartomány frissítése a fürt virtuális gépein](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Módosítja az RPD-porttartományt egy üzembe helyezett fürt csomópontbeli virtuális gépein.|
| [A fürtcsomóponti virtuális gépek rendszergazdai felhasználójának és jelszavának frissítése](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Frissíti a fürtcsomóponti virtuális gépek rendszergazdai felhasználónevét és jelszavát. |
| [Port megnyitása a terheléselosztóban](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Megnyit egy alkalmazásportot az Azure Load Balancerben, hogy egy adott porton engedélyezze a bejövő forgalmat. |
| [Bejövő hálózat biztonságicsoport-szabályának létrehozása](./scripts/service-fabric-powershell-add-nsg-rule.md) | Létrehoz egy bejövő hálózati biztonsági csoportra vonatkozó szabályt a fürtre beérkező forgalom engedélyezésére egy adott porton. |
| **Alkalmazások kezelése** ||
| [Alkalmazás üzembe helyezése](./scripts/service-fabric-powershell-deploy-application.md)| Alkalmazás üzembe helyezése egy fürtön.|
| [Alkalmazás frissítése](./scripts/service-fabric-powershell-upgrade-application.md)| Egy alkalmazás frissítése.|
| [Alkalmazás eltávolítása](./scripts/service-fabric-powershell-remove-application.md)| Alkalmazás eltávolítása egy fürtről.|
