---
title: Távoli csatlakozás az Azure Service Fabric-fürt csomópontjának |} Microsoft Docs
description: Megtudhatja, hogyan távolról csatlakozni a méretezési készlet példánya (a Service Fabric fürtcsomópont).
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 8c7d5446429089a0fc931175b55e81e1ad0c97a0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Távoli kapcsolódás a virtuálisgép-méretezési készlet példányt vagy egy fürt csomópontja
A Service Fabric fürt minden egyes csomópont fürttípus Ön által meghatározott, az Azure-ban futó [állít be egy külön virtuálisgép-méretezési](service-fabric-cluster-nodetypes.md).  Ön távoli kapcsolódhatnak adott méretezési készlet példányok (vagy fürtcsomópontok).  Egypéldányos virtuális gépeket, eltérően méretezési készlet példányok nem rendelkezik a saját virtuális IP-címek. Ez komoly kihívást jelenthet, amikor egy IP-címet és portot, amelynek távoli csatlakozás az egy adott példányt keres.

Egy IP-címet és portot, amelynek távoli csatlakozás az egy adott példányt megkereséséhez kövesse az alábbi lépéseket.

1. A virtuális IP-címének a csomóponttípus olvasson be a bejövő NAT-szabályok a távoli asztal protokoll (RDP).

    Először lekérni az erőforrás-definíciójának részeként meghatározott bejövő NAT szabályok értékek `Microsoft.Network/loadBalancers`.
    
    Az Azure portálon, a betöltés terheléselosztó lapon válassza ki a **beállítások** > **bejövő forgalmat kezelő NAT-szabályok**. Ez lehetővé teszi az IP-címet, és a portot, amelynek az első méretezési távolról csatlakozni állítsa be a példány. 
    
    ![Terheléselosztó][LBBlade]
    
    Az alábbi ábrán az IP-cím és port nem **104.42.106.156** és **3389-es**.
    
    ![NAT-szabályok][NATRules]

2. A port, amelyet segítségével távolról kapcsolódni a megadott méretezési készlet példányt, vagy a csomópont található.

    A méretezési példányok térkép csomópontokra. A méretezési készlet információ használatával határozhatja meg a pontos port használatára.
    
    Portok foglal le, amely megfelel a méretezési készlet példány növekvő sorrendben. A korábbi előtér csomóponttípus például az alábbi táblázat az egyes öt csomópont példányok portokat sorolja fel. A méretezési készlet példányát azonosak maradjanak érvényesek.
    
    | **Virtuálisgép-méretezési készlet példány** | **Port** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. Távoli kapcsolódás a megadott méretezési készlet példányhoz.

    A következő ábra bemutatja a távoli asztali kapcsolaton keresztül csatlakozni a FrontEnd_1 méretezési készlet példányhoz:
    
    ![Távoli asztali kapcsolat][RDP]


A következő lépésekkel olvassa el a következő cikkeket:
* Tekintse meg a [a "Üzembe helyezés bárhol" szolgáltatás és az Azure által kezelt fürtökkel összehasonlítása áttekintése](service-fabric-deploy-anywhere.md).
* További tudnivalók [fürt biztonsági](service-fabric-cluster-security.md).
* [Frissítse a RDP-portok tartományát értékei](./scripts/service-fabric-powershell-change-rdp-port-range.md) fürtön, virtuális gépek telepítése után
* [A rendszergazda felhasználónév és jelszó módosítása](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) fürt virtuális gépek

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
