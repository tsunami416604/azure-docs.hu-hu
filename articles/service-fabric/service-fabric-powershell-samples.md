---
title: Azure PowerShell-minták – Service Fabric
description: Ismerje meg az Azure Service Fabric-fürtök, -alkalmazások és -szolgáltatások létrehozását és felügyeletét a Powershell használatával.
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: f9ba1b4833b3aff13284a68c23f398ea47ef2ae2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75645650"
---
# <a name="azure-service-fabric-powershell-samples"></a>Az Azure Service Fabric PowerShell-minták

A következő táblázat a Service Fabric-fürtöket, -alkalmazásokat és -szolgáltatásokat létrehozó és kezelő PowerShell-mintaszkriptekre mutató hivatkozásokat tartalmaz.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Fürt létrehozása** ||
| [Fürt létrehozása (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Létrehoz egy Azure Service Fabric-fürtöt. |
| **A fürt, a csomópontok és az infrastruktúra kezelése** ||
| [Alkalmazástanúsítvány hozzáadása](./scripts/service-fabric-powershell-add-application-certificate.md)| X509-tanúsítványt hoz létre a Key Vault számára, és a fürtben lévő virtuálisgép-méretezési csoportba helyezi azt. |
| [Az RPD-porttartomány frissítése a fürt virtuális gépein](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Módosítja az RPD-porttartományt egy üzembe helyezett fürt csomópontbeli virtuális gépein.|
| [A fürtcsomóponti virtuális gépek rendszergazdai felhasználójának és jelszavának frissítése](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Frissíti a fürtcsomóponti virtuális gépek rendszergazdai felhasználónevét és jelszavát. |
| [Port megnyitása a terheléselosztóban](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Megnyit egy alkalmazásportot az Azure Load Balancerben, hogy egy adott porton engedélyezze a bejövő forgalmat. |
| [Bejövő hálózat biztonságicsoport-szabályának létrehozása](./scripts/service-fabric-powershell-add-nsg-rule.md) | Létrehoz egy bejövő hálózati biztonsági csoportra vonatkozó szabályt a fürtre beérkező forgalom engedélyezésére egy adott porton. |
| **Alkalmazások kezelése** ||
| [Alkalmazás üzembe helyezése](./scripts/service-fabric-powershell-deploy-application.md)| Alkalmazás üzembe helyezése egy fürtön.|
| [Alkalmazás frissítése](./scripts/service-fabric-powershell-upgrade-application.md)| Egy alkalmazás frissítése.|
| [Alkalmazás eltávolítása](./scripts/service-fabric-powershell-remove-application.md)| Alkalmazás eltávolítása egy fürtről.|
