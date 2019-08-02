---
title: Távoli kapcsolódás Azure Service Fabric fürtcsomópont-csomóponthoz | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat távolról egy méretezési csoport példányaihoz (Service Fabric fürtcsomópont).
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: atsenthi
ms.openlocfilehash: 12508fd5297691f06bce46e056527672083c3a91
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599931"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Távoli kapcsolódás virtuálisgép-méretezési csoport példányaihoz vagy fürtcsomóponton
Az Azure-ban futó Service Fabric-fürtökben minden egyes definiált csomópont-típus [a virtuális gép külön méretezését állítja](service-fabric-cluster-nodetypes.md)be.  A távoli kapcsolódást egy adott méretezési csoport példányaihoz (fürtcsomópontok) lehet csatlakoztatni.  Az Egypéldányos virtuális gépektől eltérően a méretezési csoport példányai nem rendelkeznek saját virtuális IP-címmel. Ez kihívást jelenthet, ha olyan IP-címet és portot keres, amelyet egy adott példányhoz való távoli kapcsolódáshoz használhat.

Ha olyan IP-címet és portot szeretne megkeresni, amelyet egy adott példányhoz való távoli kapcsolódáshoz használhat, hajtsa végre az alábbi lépéseket.

1. A RDP protokoll (RDP) bejövő NAT-szabályainak beolvasása.

    A fürtben definiált csomópont-típusok általában saját virtuális IP-címmel és dedikált Load balancerrel rendelkeznek. Alapértelmezés szerint a rendszer a csomópontok típusának terheléselosztó nevét a következő formátumban adja meg: *LB-{fürt neve} – {Node-Type}* ; például: *LB-mycluster-FrontEnd*. 
    
    A terheléselosztó lapján a Azure Portalban válassza a **Beállítások** > **bejövő NAT-szabályok**elemet: 

    ![Terheléselosztó bejövő NAT-szabályai](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Az alábbi képernyőfelvételen egy FrontEnd nevű csomópont-típus bejövő NAT-szabályai láthatók: 

    ![Terheléselosztó bejövő NAT-szabályai](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Az egyes csomópontok esetében az IP-cím a **cél** oszlopban jelenik meg, a **cél** oszlop pedig megadja a méretezési csoport példányát, és a **szolgáltatás** oszlopa megadja a portszámot. A távoli kapcsolatok esetében a portok a 3389-es porttól kezdődően növekvő sorrendben vannak lefoglalva az egyes csomópontokhoz.

    A bejövő NAT-szabályokat a fürt Resource Manager- `Microsoft.Network/loadBalancers` sablonjának szakaszában találja.
    
2. Ha szeretné megerősíteni, hogy a bejövő port egy csomóponthoz tartozó porthoz legyen hozzárendelve, kattintson a szabályra, és tekintse meg a **célport értékét.** Az alábbi képernyőfelvételen az előző lépésben a **FrontEnd (1. példány)** csomópont bejövő NAT-szabálya látható. Figyelje meg, hogy bár a (bejövő) portszáma 3390, a célként megadott port a 3389-es portra van leképezve, a célhelyen lévő RDP szolgáltatás portja.  

    ![Célport megfeleltetése](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Alapértelmezés szerint a Windows-fürtök esetében a célként megadott port a 3389-es port, amely a cél csomóponton lévő RDP szolgáltatásra van leképezve. Linux-fürtök esetén a célport a 22-es port, amely a Secure Shell (SSH) szolgáltatáshoz van hozzárendelve.

3. Távolról kapcsolódhat az adott csomóponthoz (méretezési csoport példánya). Használhatja a fürt vagy más konfigurált hitelesítő adatok létrehozásakor beállított felhasználónevet és jelszót. 

    Az alábbi képernyőfelvételen a Távoli asztali kapcsolat használatával csatlakozhat a Windows-fürt előtér- **(1. példány)** csomópontjára:
    
    ![Távoli asztali kapcsolat](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Linux-csomópontokon csatlakozhat SSH-val (az alábbi példa ugyanazt az IP-címet és portot használja a rövidítésekhez):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


A következő lépésekhez olvassa el a következő cikkeket:
* Tekintse meg a ["bárhonnan üzembe helyezhető" funkció áttekintését, valamint az Azure által felügyelt fürtökkel való összehasonlítást](service-fabric-deploy-anywhere.md).
* További információ a [fürt biztonságáról](service-fabric-cluster-security.md).
* [Az RDP-porttartomány értékének frissítése](./scripts/service-fabric-powershell-change-rdp-port-range.md) a fürtön futó virtuális gépeken az üzembe helyezést követően
* A fürt virtuális gépei [rendszergazdai felhasználónevének és jelszavának módosítása](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

