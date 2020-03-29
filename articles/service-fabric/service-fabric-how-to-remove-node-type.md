---
title: Csomóponttípus eltávolítása az Azure Service Fabricben | Microsoft dokumentumok
description: Ismerje meg, hogyan távolíthatja el a csomóponttípusát az Azure-ban futó Service Fabric-fürtből.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969405"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Service Fabric-csomóponttípus eltávolítása
Ez a cikk ismerteti, hogyan skálázhatja az Azure Service Fabric-fürt egy meglévő csomóponttípus eltávolítása a fürtből. A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatások üzembe helyezése és kezelése történik. Egy gép vagy virtuális gép, amely egy fürt része, nevezzük csomópont. A virtuálisgép-méretezési csoportok egy Azure-számítási erőforrás, amely et a virtuális gépek gyűjteményének készletként történő üzembe helyezéséhez és kezeléséhez használja. Az Azure-fürtben definiált minden csomóponttípus [külön méretezési csoportként van beállítva.](service-fabric-cluster-nodetypes.md) Ezután minden csomóponttípus külön-külön kezelhető. A Service Fabric-fürt létrehozása után vízszintesen skálázhatja a fürtöt egy csomóponttípus (virtuálisgép-méretezési csoport) és az összes csomópont eltávolításával.  A fürt bármikor méretezhető, még akkor is, ha a számítási feladatok futnak a fürtön.  A fürt méretezése, az alkalmazások automatikusan skálázható is.

> [!WARNING]
> Ezzel a módszerrel távolítsa el a csomópont típusát egy éles fürtből nem ajánlott gyakran használni. Ez egy veszélyes parancs, mivel törli a virtuális gép méretezési készlet erőforrás mögött a csomópont típusát. 

## <a name="durability-characteristics"></a>Tartóssági jellemzők
Az Remove-AzServiceFabricNodeType használata esetén a biztonság elsőbbséget élvez a sebességhez. A csomóponttípusnak ezüst vagy arany [tartóssági szintnek](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)kell lennie, mivel:
- A Bronze nem ad garanciát az állapotadatok megtakarításával kapcsolatban.
- Az ezüst és az arany tartóssága csapdába ejti a mérleg minden módosítását.
- A Gold emellett az Azure-frissítések et is szabályozhatja a méretezési csoport alatt.

A Service Fabric "vezényli" az alapul szolgáló módosításokat és frissítéseket, hogy az adatok ne vesszenek el. Ha azonban eltávolít egy Bronz tartósságú csomóponttípust, elveszítheti az állapotadatokat. Ha egy elsődleges csomóponttípust távolít el, és az alkalmazás állapot nélküli, a Bronz elfogadható. Amikor állapotalapú számítási feladatokat futtat éles környezetben, a minimális konfiguráció ezüst nek kell lennie. Hasonlóképpen, éles forgatókönyvek esetében az elsődleges csomópont típusának mindig ezüst nek vagy aranynak kell lennie.

### <a name="more-about-bronze-durability"></a>További információk a Bronz tartósságról

Bronz csomóponttípus eltávolításakor a csomóponttípus összes csomópontja azonnal leáll. A Service Fabric nem tudja csapdába egyik bronz csomópont méretezési készlet frissítéseit sem, így az összes virtuális gép azonnal leáll. Ha volt valami állapotú azokon a csomópontokon, az adatok elvesznek. Most, még akkor is, ha állapot nélküli, a Service Fabric összes csomópontja részt vesz a gyűrűben, így egy teljes szomszédság elveszhet, ami destabilizálhatja magát a fürtöt.

## <a name="remove-a-node-type"></a>Csomóponttípus eltávolítása

1. Kérjük, a folyamat megkezdése előtt gondoskodjon erről az előfeltételről.

    - A fürt kifogástalan állapotú.
    - A csomópont típusának eltávolítása után is elegendő kapacitás lesz, pl. a szükséges replikaszámot elhelyező csomópontok száma.

2. Helyezze át az összes olyan szolgáltatást, amely elhelyezési korlátozásokkal rendelkezik a csomóponttípus használatához a csomóponttípuson kívülre.

    - Az Alkalmazás /Szolgáltatásjegyzék módosítása, hogy már ne hivatkozzon a csomóponttípusra.
    - Telepítse a módosítást.

    Ezután ellenőrizze, hogy:
    - A fent módosított összes szolgáltatás már nem fut a csomóponttípushoz tartozó csomóponton.
    - Minden szolgáltatás egészséges.

3. A csomóponttípus nem elsődlegesként való megjelölésének törlése (Ugrás nem elsődleges csomóponttípusok esetén)

    - Keresse meg a központi telepítéshez használt Azure Resource Manager-sablont.
    - Keresse meg a csomópont típusához kapcsolódó szakaszt a Service Fabric szakaszban.
    - A változás elsődleges tulajdonsága hamis. ** Ne távolítsa el a feladat csomóponttípusához kapcsolódó szakaszt.
    - Telepítse a módosított Azure Resource Manager-sablont. ** A fürt konfigurációjától függően ez a lépés eltarthat egy ideig.
    
    Ezután ellenőrizze, hogy:
    - A Service Fabric szakasz a portálon azt jelzi, hogy a fürt készen áll.
    - A fürt kifogástalan állapotú.
    - A csomóponttípushoz tartozó csomópontok egyike sincs Seed Node-ként megjelölve.

4. Tiltsa le a csomóponttípus adatait.

    Csatlakozzon a fürthöz a PowerShell használatával, majd futtassa a következő lépést.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - A bronz tartósság, várja meg, amíg az összes csomópont, hogy a fogyatékkal élő állapot
    - Az ezüst és arany tartósság, néhány csomópont megy a fogyatékkal élő és a többi lesz letiltása állapotban. Ellenőrizze a csomópontok részletes lapját letiltási állapotban, ha mindannyian beragadtak az infrastruktúra-szolgáltatás partícióik kvórumának biztosításához, akkor biztonságos anno folytatni.

5. A csomóponttípus adatainak leállítása.

    Csatlakozzon a fürthöz a PowerShell használatával, majd futtassa a következő lépést.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Várjon, amíg a csomóponttípus összes csomópontja Lefelé lesz jelölve.
    
6. Távolítsa el a csomóponttípus adatait.

    Csatlakozzon a fürthöz a PowerShell használatával, majd futtassa a következő lépést.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Várjon, amíg az összes csomópont törlődik a fürtből. A csomópontok nem jelennek meg az SFX-ben.

7. Távolítsa el a csomóponttípust a Service Fabric szakaszból.

    - Keresse meg a központi telepítéshez használt Azure Resource Manager-sablont.
    - Keresse meg a csomópont típusához kapcsolódó szakaszt a Service Fabric szakaszban.
    - Távolítsa el a csomóponttípusnak megfelelő szakaszt.
    - Csak silver és nagyobb tartóssági fürtök esetében frissítse a fürterőforrást a sablonban, és `applicationDeltaHealthPolicies` konfigurálja `properties` az állapotházirendeket úgy, hogy figyelmen kívül hagyja a fabric:/System alkalmazás állapotát az alábbi fürterőforrás alatt. Az alábbi házirend figyelmen kívül hagyja a meglévő hibákat, de nem engedélyezi az új állapothibákat. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - Telepítse a módosított Azure Resource Manager-sablont. ** Ez a lépés eltart egy ideig, általában legfeljebb két órát. Ez a frissítés módosítja a beállításokat az InfrastructureService szolgáltatásra, ezért csomópont-újraindításra van szükség. Ebben az `forceRestart` esetben figyelmen kívül hagyja. 
    A `upgradeReplicaSetCheckTimeout` paraméter azt a maximális időt adja meg, ameddig a Service Fabric megvárja, hogy egy partíció biztonságos állapotban legyen, ha még nem biztonságos állapotban van. Miután a biztonsági ellenőrzések átegy csomópont összes partícióját, service fabric folytatja a frissítést, hogy a csomóponton.
    A paraméter `upgradeTimeout` értéke 6 órára csökkenthető, de a maximális biztonság érdekében 12 órát kell használni.

    Ezután ellenőrizze, hogy:
    - Service Fabric erőforrás a portálon mutatja készen áll.

8. A csomóponttípushoz kapcsolódó összes hivatkozás eltávolítása.

    - Keresse meg a központi telepítéshez használt Azure Resource Manager-sablont.
    - Távolítsa el a virtuálisgép-méretezési készletet és a csomóponttípushoz kapcsolódó egyéb erőforrásokat a sablonból.
    - Telepítse a módosításokat.

    Ezután:
    - Várja meg, amíg befejeződik a telepítés.

## <a name="next-steps"></a>További lépések
- További információ a fürtök [tartóssági jellemzőiről.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)
- További információ a [csomóponttípusokról és a virtuálisgép-méretezési készletekről.](service-fabric-cluster-nodetypes.md)
- További információ a [Service Fabric fürtméretezéséről.](service-fabric-cluster-scaling.md)
