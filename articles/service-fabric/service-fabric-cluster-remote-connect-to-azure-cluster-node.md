---
title: Távoli csatlakozás az Azure Service Fabric-fürt csomópontjának |} Microsoft Docs
description: Megtudhatja, hogyan távolról csatlakozni a méretezési készlet példánya (a Service Fabric fürtcsomópont).
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 28424f9a7a0f77882ee3360c5599549303075c18
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642573"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Távoli kapcsolódás a virtuálisgép-méretezési készlet példányt vagy egy fürt csomópontja
A Service Fabric fürt minden egyes csomópont fürttípus Ön által meghatározott, az Azure-ban futó [állít be egy külön virtuálisgép-méretezési](service-fabric-cluster-nodetypes.md).  Ön távoli kapcsolódhatnak adott méretezési készlet példányok (fürtcsomópontok).  Egypéldányos virtuális gépeket, eltérően méretezési készlet példányok nem rendelkezik a saját virtuális IP-címek. Ez komoly kihívást jelenthet, amikor egy IP-címet és portot, amelynek távoli csatlakozás az egy adott példányt keres.

Egy IP-címet és portot, amelynek távoli csatlakozás az egy adott példányt megkereséséhez kövesse az alábbi lépéseket.

1. A bejövő NAT-szabályok a távoli asztal protokoll (RDP) beolvasása.

    Általában az egyes csomóponttípusok, a fürt definiálva van a saját virtuális IP-címet és egy dedikált terheléselosztó. Alapértelmezés szerint a load balancer egy csomóponttípus neve a következő formátumban: *LB-{fürtnév}-{csomóponttípus-}*, például *LB-mycluster-előtérbeli*. 
    
    A terheléselosztóhoz, Azure-portálon lapon válassza az **beállítások** > **bejövő forgalmat kezelő NAT-szabályok**: 

    ![Terheléselosztó bejövő forgalmat kezelő NAT-szabályok betöltése](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Az alábbi képernyőfelvételen látható a bejövő NAT-szabályokat előtér nevű csomóponttípus: 

    ![Terheléselosztó bejövő forgalmat kezelő NAT-szabályok betöltése](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Az egyes csomópontok az IP-cím szerepel a **cél** oszlop, a **cél** oszlop tartalmazza a méretezési készlet példány, és a **szolgáltatás** oszlop nyújt a portszámot. Távoli kapcsolat portok kiosztott növekvő sorrendben kezdődő 3389-es port minden csomóponton.

    Is megkeresheti a bejövő forgalmat kezelő NAT-szabályok a a `Microsoft.Network/loadBalancers` a Resource Manager-sablon a fürt része.
    
2. Megerősítéséhez, hogy a csomópont target porthozzárendelés a bejövő portot, kattintson a szabályt, és tekintse meg a **céloz port** érték. Az alábbi képernyőfelvételen látható a bejövő NAT-szabály a **előtér (példány 1)** csomópont az előző lépésben. Figyelje meg, hogy bár a (bejövő) portszám 3390, a cél port 3389-es, a portot a célszámítógépen az RDP-szolgáltatás porthoz van hozzárendelve.  

    ![Cél porthozzárendelés](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Alapértelmezés szerint a Windows-fürtök esetén a cél port az 3389-es, amely a célcsomóponton RDP-szolgáltatás. Linux-fürtök esetén a cél port port 22, amely a Secure Shell (SSH) szolgáltatás.

3. Távolról kapcsolódni a megadott csomópont (méretezési példány). A felhasználói nevet és jelszót, amely a fürt vagy bármely más hitelesítő adatok konfigurálása létrehozásakor beállított is használhatja. 

    Az alábbi képernyőfelvételen látható, távoli asztali kapcsolaton keresztül csatlakozni a **előtér (példány 1)** egy Windows-fürt csomópontja:
    
    ![Távoli asztali kapcsolat](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Linux-csomópont (az alábbi példában a rendszer újból felhasználja az IP-cím és port kivonatosan mutatja) SSH kapcsolatba léphet:

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


A következő lépésekkel olvassa el a következő cikkeket:
* Tekintse meg a [a "Üzembe helyezés bárhol" szolgáltatás és az Azure által kezelt fürtökkel összehasonlítása áttekintése](service-fabric-deploy-anywhere.md).
* További tudnivalók [fürt biztonsági](service-fabric-cluster-security.md).
* [Frissítse a RDP-portok tartományát értékei](./scripts/service-fabric-powershell-change-rdp-port-range.md) fürtön, virtuális gépek telepítése után
* [A rendszergazda felhasználónév és jelszó módosítása](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) fürt virtuális gépek

