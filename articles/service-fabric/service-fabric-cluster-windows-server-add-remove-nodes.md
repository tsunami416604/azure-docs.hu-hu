---
title: Csomópontok hozzáadása vagy eltávolítása önálló Service Fabric-fürthöz
description: Megtudhatja, hogyan adhat hozzá vagy távolíthat el csomópontokat egy Azure Service Fabric-fürthöz egy Windows Servert futtató fizikai vagy virtuális gépen, amely a helyszínen vagy bármilyen felhőben is lehetséges.
author: dkkapur
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: aa9550d1ec6201f7cbaf552fac5f71c875428e21
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458258"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Csomópontok hozzáadása vagy eltávolítása a Windows Serveren futó önálló Service Fabric fürtön
Miután [létrehozta az önálló Service Fabric fürtöt a Windows Server rendszerű gépeken](service-fabric-cluster-creation-for-windows-server.md), a (vállalati) igények változhatnak, és hozzá kell adnia vagy el kell távolítania a fürthöz tartozó csomópontokat. Ez a cikk a megvalósításának részletes lépéseit ismerteti. Vegye figyelembe, hogy a csomópontok hozzáadása/eltávolítása funkció nem támogatott a helyi fejlesztési fürtökben.

## <a name="add-nodes-to-your-cluster"></a>Csomópontok hozzáadása a fürthöz

1. Készítse elő a fürthöz hozzáadni kívánt virtuális gépet/gépet a [Service Fabric-fürt üzembe helyezésének megtervezése és előkészítése](service-fabric-cluster-creation-for-windows-server.md) című témakörben ismertetett lépéseket követve.
2. Azonosítsa, hogy melyik tartalék tartományhoz és frissítési tartományhoz fogja hozzáadni ezt a virtuális gépet/gépet
3. Távoli asztal (RDP) a fürthöz hozzáadni kívánt virtuális gépre vagy gépre
4. Másolja vagy [töltse le a Windows Server rendszerhez készült Service Fabric önálló csomagját](https://go.microsoft.com/fwlink/?LinkId=730690) a virtuális gépre/gépre, és bontsa ki a csomagot
5. Futtassa a PowerShellt emelt szintű jogosultságokkal, és navigáljon a kibontott csomag helyére.
6. Futtassa a *hozzáadási. ps1* parancsfájlt a hozzáadandó új csomópontot leíró paraméterekkel. Az alábbi példa egy VM5 nevű új csomópontot hoz létre, amelynek típusa NodeType0 és IP-182.17.34.52, a UD1 és az FD:/DC1/R0. A *ExistingClusterConnectionEndPoint* a meglévő fürtben már létező csomóponthoz tartozó kapcsolatok végpontja, amely a fürt *bármely* csomópontjának IP-címe lehet.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Miután a parancsfájl futása befejeződött, megtekintheti, hogy az új csomópont hozzá lett-e adva a [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) parancsmag futtatásával.

7. A fürt különböző csomópontjai közötti konzisztencia biztosításához a konfiguráció frissítését kell elindítania. Futtassa a [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) parancsot a legújabb konfigurációs fájl beszerzéséhez, és adja hozzá az újonnan hozzáadott csomópontot a "csomópontok" szakaszhoz. Azt is javasoljuk, hogy mindig legyen elérhető a legújabb fürtkonfiguráció abban az esetben, ha újra kell telepítenie egy fürtöt ugyanazzal a konfigurációval.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. A frissítés megkezdéséhez futtassa a [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) parancsot.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Service Fabric Exploreron figyelheti a frissítés előrehaladását. Azt is megteheti, [hogy a Get-ServiceFabricClusterUpgrade-](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) t futtatja

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Csomópontok hozzáadása a Windows biztonsággal konfigurált fürtökhöz a gMSA használatával
A csoportosan felügyelt szolgáltatásfiók (gMSA) használatával konfigurált fürtökhöz (https://technet.microsoft.com/library/hh831782.aspx) egy új csomópontot adhat hozzá egy konfiguráció frissítésével:
1. Futtassa a [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) parancsot bármelyik meglévő csomóponton a legújabb konfigurációs fájl beszerzéséhez, és adja meg a "csomópontok" szakaszban hozzáadni kívánt új csomópont részleteit. Győződjön meg arról, hogy az új csomópont ugyanahhoz a csoportosan felügyelt fiókhoz tartozik. Ennek a fióknak az összes gépen rendszergazdának kell lennie.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. A frissítés megkezdéséhez futtassa a [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) parancsot.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Service Fabric Exploreron figyelheti a frissítés előrehaladását. Azt is megteheti, [hogy a Get-ServiceFabricClusterUpgrade-](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) t futtatja

### <a name="add-node-types-to-your-cluster"></a>Csomópont-típusok hozzáadása a fürthöz
Új csomópont-típus hozzáadásához módosítsa úgy a konfigurációt, hogy az tartalmazza az új csomópont típusát a "tulajdonságok" területen található "NodeTypes" szakaszban, majd kezdjen el egy konfigurációs frissítést a [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)használatával. Ha a frissítés befejeződött, új csomópontokat adhat hozzá a fürthöz ehhez a csomópont-típushoz.

## <a name="remove-nodes-from-your-cluster"></a>Csomópontok eltávolítása a fürtből
A csomópontokat a következő módon távolíthatja el egy fürtből a fürtből a konfiguráció frissítésével:

1. Futtassa a [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) parancsot a legújabb konfigurációs fájl beszerzéséhez, és *távolítsa el* a csomópontot a "csomópontok" szakaszból.
Adja hozzá a "NodesToBeRemoved" paramétert a "Setup" szakaszhoz a "FabricSettings" szakaszon belül. Az "érték" az eltávolítandó csomópontok csomópont-neveinek vesszővel tagolt listája.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. A frissítés megkezdéséhez futtassa a [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) parancsot.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Service Fabric Exploreron figyelheti a frissítés előrehaladását. Azt is megteheti, [hogy a Get-ServiceFabricClusterUpgrade-](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) t futtatja

> [!NOTE]
> A csomópontok eltávolítása több frissítést is kezdeményezhet. Egyes csomópontok `IsSeedNode=”true”` címkével vannak megjelölve, és a fürt jegyzékfájljának lekérdezésével azonosíthatók a `Get-ServiceFabricClusterManifest`használatával. Az ilyen csomópontok eltávolítása több időt is igénybe vehet, mivel a vetőmag-csomópontokat ilyen helyzetekben kell áthelyezni. A fürtnek legalább 3 elsődleges csomópont típusú csomópontot kell fenntartania.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Csomópont-típusok eltávolítása a fürtből
A csomópontok típusának eltávolítása előtt ellenőrizze, hogy van-e olyan csomópont, amely hivatkozik a csomópont típusára. Távolítsa el ezeket a csomópontokat, mielőtt eltávolítja a megfelelő csomópont-típust. Az összes kapcsolódó csomópont eltávolítása után eltávolíthatja a NodeType a fürt konfigurációjától, és megkezdheti a konfiguráció frissítését a [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)használatával.


### <a name="replace-primary-nodes-of-your-cluster"></a>A fürt elsődleges csomópontjainak cseréje
Az elsődleges csomópontok cseréjét egy másik csomópont után kell végrehajtani, a kötegek eltávolítása és hozzáadása helyett.


## <a name="next-steps"></a>Következő lépések
* [Önálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md)
* [Önálló fürt biztonságossá tétele a Windowsban X509-tanúsítványok használatával](service-fabric-windows-cluster-x509-security.md)
* [Önálló Service Fabric-fürt létrehozása Windows rendszerű Azure-beli virtuális gépekkel](service-fabric-cluster-creation-with-windows-azure-vms.md)

