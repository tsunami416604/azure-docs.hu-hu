---
title: "Az Azure PowerShell-példák - a Service Fabric |} Microsoft Docs"
description: "Az Azure PowerShell-példák - a Service Fabric"
services: service-fabric
documentationcenter: service-fabric
author: rwike77
manager: timlt
editor: 
tags: 
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 17b10011af915a06e7c65f8d321a1e452956c28a
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="azure-powershell-samples"></a>Azure PowerShell-minták

A következő táblázat a PowerShell parancsfájlok mintának, hogy a Service Fabric fürtök, alkalmazások és szolgáltatások létrehozása és kezelése mutató hivatkozásokat tartalmaz.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Fürt létrehozása** ||
| [Hozzon létre egy fürtöt (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Az Azure Service Fabric-fürtöt hoz létre. |
| **Fürt csomópont és infrastruktúra kezelése** ||
| [Egy alkalmazás-tanúsítvány hozzáadása](./scripts/service-fabric-powershell-add-application-certificate.md)| Egy alkalmazás X.509 tanúsítvány felvétele a fürt összes csomópontján. |
| [A virtuális gépek fürtön RDP-porttartomány frissítése](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Módosítja az RDP porttartomány fürtben telepített virtuális gépek fürtcsomóponton.|
| [A rendszergazdai jogú felhasználó és a virtuális gépek fürt összes csomópontjára vonatkozóan jelszó frissítése](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | A rendszergazda felhasználónevét és jelszavát fürtcsomópont virtuális gépek frissíti. |
| [Port megnyitása a terheléselosztóban](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Nyissa meg az alkalmazás port az Azure load balancer egy adott portot a bejövő adatforgalom engedélyezésére. |
| [Bejövő hálózati biztonsági csoport szabály létrehozása](./scripts/service-fabric-powershell-add-nsg-rule.md) | A fürt egy adott portot a bejövő forgalom engedélyezése a bejövő hálózati biztonsági csoport szabályt létrehozni. |
| **Alkalmazások kezelése** ||
| [Alkalmazás üzembe helyezése](./scripts/service-fabric-powershell-deploy-application.md)| Alkalmazás üzembe helyezése a fürthöz.|
| [Alkalmazás frissítése](./scripts/service-fabric-powershell-upgrade-application.md)| Alkalmazás frissítése.|
| [Alkalmazások eltávolítása](./scripts/service-fabric-powershell-remove-application.md)| Az alkalmazás eltávolítása egy fürtről.|
