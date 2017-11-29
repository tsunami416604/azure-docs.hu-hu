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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ae132dbb650e08c3a25a9366563e70c6d56e089d
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="azure-powershell-samples"></a>Azure PowerShell-minták

A következő táblázat a PowerShell parancsfájlok mintának, hogy a Service Fabric fürtök, alkalmazások és szolgáltatások létrehozása és kezelése mutató hivatkozásokat tartalmaz.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Fürt létrehozása** ||
| [Hozzon létre egy fürtöt (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Az Azure Service Fabric-fürtöt hoz létre. |
| **Kezelheti a fürt és a csomópontok** ||
| [Egy alkalmazás-tanúsítvány hozzáadása](./scripts/service-fabric-powershell-add-application-certificate.md)| Egy alkalmazás X.509 tanúsítvány felvétele a fürt összes csomópontján. |
|[Az RDP-porttartomány fürtcsomóponton virtuális gépek módosítása](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Módosítja az RDP porttartomány fürtben telepített virtuális gépek fürtcsomóponton.|
| [A rendszergazdai jogú felhasználó és a virtuális gépek fürt összes csomópontjára vonatkozóan jelszó frissítése](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | A rendszergazda felhasználónevét és jelszavát fürtcsomópont virtuális gépek frissíti. |
| **Alkalmazások kezelése** ||
| [Alkalmazás üzembe helyezése](./scripts/service-fabric-powershell-deploy-application.md)| Alkalmazás üzembe helyezése a fürthöz.|
| [Alkalmazás frissítése](./scripts/service-fabric-powershell-upgrade-application.md)| Alkalmazás frissítése |
| [Alkalmazások eltávolítása](./scripts/service-fabric-powershell-remove-application.md)| Az alkalmazás eltávolítása egy fürtről.|
| [Port megnyitása a terheléselosztóban](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Nyissa meg az Azure load balancer egy alkalmazás port. |
