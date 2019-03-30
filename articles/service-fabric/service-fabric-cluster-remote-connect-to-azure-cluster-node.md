---
title: Távoli csatlakozás az Azure Service Fabric-fürtcsomópont |} A Microsoft Docs
description: Útmutató a távoli példányhoz való csatlakozáshoz egy méretezési csoportot (a Service Fabric-fürtcsomópont).
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 4cc2d6355a0147c33048f1c2c27a3648b9223db4
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663774"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Távoli kapcsolódás a virtuálisgép-méretezési készlet példányt vagy egy fürt csomópontja
Service fabric-fürt az Azure-ban, minden Ön által meghatározott fürtcsomóponttípus futó [állít be egy külön virtuálisgép-méretezési](service-fabric-cluster-nodetypes.md).  Akkor is távoli csatlakozás adott méretezési csoport példányaihoz (fürtcsomópontok).  Egypéldányos virtuális gépek, ellentétben a méretezési csoport példányaihoz a saját virtuális IP-címek nem rendelkezik. Ez kihívást jelenthet, ha a keresett egy IP-címet és portot, amelyen a távoli példányhoz való csatlakozáshoz egy adott használhatja.

A következő lépéseket egy IP-címet és portot, amelyen a távoli példányhoz való csatlakozáshoz egy adott használhatja megkereséséhez.

1. A bejövő NAT-szabályokat a távoli asztal protokoll (RDP) beolvasása.

    Általában mindegyik a fürt megadott csomóponttípus rendelkezik a saját virtuális IP-cím és a egy dedikált load balancert. Alapértelmezés szerint a terheléselosztó egy csomóponttípus neve a következő formátumban: *LB-{fürt neve} – {csomóponttípus}*; például *LB-mycluster-FrontEnd*. 
    
    A terheléselosztó az Azure portal oldalán válassza **beállítások** > **bejövő NAT-szabályok**: 

    ![Load balancer bejövő NAT-szabályok](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Az alábbi képernyőfelvételen láthatók a bejövő NAT-szabályok egy FrontEnd nevű csomóponttípus: 

    ![Load balancer bejövő NAT-szabályok](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Minden egyes csomópont esetében az IP-cím megjelenik a **cél** oszlop, a **cél** oszlop tartalmazza a scale set-példányban, és a **szolgáltatás** oszlop biztosít a port számát. Távoli kapcsolat minden csomópontjához növekvő sorrendben kiadásától kezdve a 3389-es portok vannak lefoglalva.

    A bejövő NAT-szabályokat is megkeresheti a `Microsoft.Network/loadBalancers` szakasz a Resource Manager-sablon a fürt számára.
    
2. Ellenőrizze a bejövő portot, céloldali port leképezése egy csomópontot, kattintson a szabály és tekintse meg a **port cél** értéket. Az alábbi képernyőfelvételen láthatók a bejövő NAT-szabályt a **előtérbeli (példány % 1)** csomópont az előző lépésben. Figyelje meg, hogy bár a (bejövő) portszám 3390, a célportot 3389-es, a port, az RDP-szolgáltatás a cél van leképezve.  

    ![Cél port hozzárendelése](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Alapértelmezés szerint a Windows-fürtök a cél port az 3389-es, porton képez le az RDP-szolgáltatás célcsomóponton. A Linux-fürtök esetén a cél port a 22-es, porton a Secure Shell (SSH) szolgáltatást képez le.

3. Az adott csomópont távolról csatlakozni (méretezési csoport példánya). Használhatja a felhasználónév és a fürt vagy bármely más hitelesítő adatokat már konfigurálta a létrehozásakor megadott jelszót. 

    Az alábbi képernyőfelvételen a távoli asztali kapcsolat segítségével szeretne csatlakozni a **előtérbeli (példány % 1)** egy Windows-fürt csomópontja:
    
    ![A távoli asztali kapcsolat](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    A Linux-csomópontokat csatlakoztathatja az ssh-val (a következő példa újrahasználja az azonos IP-cím és port kivonatosan):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Következő lépések olvassa el a következő cikkeket:
* Tekintse meg a [áttekintése a "Üzembe helyezés bárhol" funkció és az Azure által felügyelt fürtöket való összehasonlítást](service-fabric-deploy-anywhere.md).
* Ismerje meg [biztonsági fürt](service-fabric-cluster-security.md).
* [Az RPD-porttartomány értékeinek frissítése](./scripts/service-fabric-powershell-change-rdp-port-range.md) fürtön, virtuális gépek üzembe helyezés után
* [Módosítsa a rendszergazdai felhasználónevét és jelszavát](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) fürt virtuális gépeihez

