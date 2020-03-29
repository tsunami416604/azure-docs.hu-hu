---
title: Távoli csatlakozás egy Azure Service Fabric-fürtcsomóponthoz
description: Ismerje meg, hogyan csatlakozhat távolról egy méretezési csoport példányához (egy Service Fabric-fürtcsomóponthoz).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: c7ca4f0d5dce1b19837a44d5c9749f3e1293c6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458315"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Távoli csatlakozás virtuálisgép-méretezési csoporthoz vagy fürtcsomóponthoz
Az Azure-ban futó Service Fabric-fürtökben minden megadott fürtcsomópont-típus [külön skálát állít be.](service-fabric-cluster-nodetypes.md)  Távoli csatlakozás adott méretezési csoport példányai (fürtcsomópontok).  Az egypéldányos virtuális gépektől eltérően a méretezési csoport példányai nem rendelkeznek saját virtuális IP-címekkel. Ez kihívást jelenthet, ha olyan IP-címet és portot keres, amellyel távolról csatlakozhat egy adott példányhoz.

Ha meg szeretne keresni egy ip-címet és portot, amellyel távolról csatlakozhat egy adott példányhoz, hajtsa végre a következő lépéseket.

1. A Távoli asztali protokoll (RDP) bejövő NAT-szabályainak beolvasása.

    A fürtben definiált csomóponttípusok általában saját virtuális IP-címmel és dedikált terheléselosztóval rendelkeznek. Alapértelmezés szerint egy csomóponttípus terheléselosztója a következő formátumú: *LB-{cluster-name}-{node-type}*; például *LB-mycluster-FrontEnd*. 
    
    Az Azure Portal terheléselosztójának lapján válassza a **Bejövő NAT-beállítások** > **szabályait:** 

    ![Bejövő terheléselosztó NAT-szabályok](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    A következő képernyőképen egy FrontEnd nevű csomóponttípus bejövő NAT-szabályai láthatók: 

    ![Bejövő terheléselosztó NAT-szabályok](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Minden csomópontesetében az IP-cím megjelenik a **DESTINATION** oszlopban, a **TARGET** oszlop a méretezési csoport példányát adja meg, a **SERVICE** oszlop pedig a portszámot. Távoli kapcsolat esetén a portok a 3389-es porttól kezdődően növekvő sorrendben vannak lefoglalva az egyes csomópontokhoz.

    A bejövő NAT-szabályokat a `Microsoft.Network/loadBalancers` fürt Erőforrás-kezelő sablonjának szakaszában is megtalálhatja.
    
2. Ha meg szeretné erősíteni, hogy a bejövő port egy csomópont portleképezéséhez érkezik, kattintson a szabályra, és tekintse meg a **célport** értékét. A következő képernyőképen látható a bejövő NAT-szabály a **FrontEnd (Instance 1)** csomópont az előző lépésben. Figyelje meg, hogy bár a (bejövő) port száma 3390, a célport van leképezve a 3389-es port, a port az RDP szolgáltatás a cél.  

    ![Célport leképezése](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    A Windows-fürtök esetében alapértelmezés szerint a célport a 3389-es port, amely a célcsomópont RDP szolgáltatásához rendeli hozzá az RDP szolgáltatást. Linux-fürtök esetén a célport a 22-es port, amely leképezi a Secure Shell (SSH) szolgáltatás.

3. Távolról csatlakozhat az adott csomóponthoz (méretezési csoport példánya). A fürt létrehozásakor megadott felhasználónevet és jelszót vagy bármely más konfigurált hitelesítő adatot használhatja. 

    Az alábbi képernyőképen a Távoli asztali kapcsolat segítségével csatlakozhat a **Windows-fürt FrontEnd (1. példánya)** csomóponthoz:
    
    ![Távoli asztali kapcsolat](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Linux-csomópontokon csatlakozhat az SSH-hoz (a következő példa ugyanazt az IP-címet és portot használja fel a rövidség érdekében):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


A következő lépéseket az alábbi cikkekben olvashatja:
* Tekintse meg [a "Bárhol üzembe helyezés" szolgáltatás áttekintését és az Azure által felügyelt fürtökkel való összehasonlítást.](service-fabric-deploy-anywhere.md)
* További információ a [fürtbiztonságról](service-fabric-cluster-security.md).
* [Az RDP-porttartomány értékeinek frissítése](./scripts/service-fabric-powershell-change-rdp-port-range.md) a fürt virtuális gépein a telepítés után
* [Fürtvirtuális gépek rendszergazdai felhasználónevének és jelszavának módosítása](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

