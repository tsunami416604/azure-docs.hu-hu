---
title: Csomópontok hozzáadása vagy eltávolítása önálló Service Fabric-fürthöz
description: Megtudhatja, hogyan adhat hozzá vagy távolíthat el csomópontokat egy Azure Service Fabric-fürthöz egy Windows Server t futtató fizikai vagy virtuális gépen, amely helyszíni vagy bármely felhőben is lehet.
author: dkkapur
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: f9bee35ee8e82070b4cf601139b471562ba5e10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934211"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Csomópontok hozzáadása vagy eltávolítása a Windows Serveren futó önálló Service Fabric-fürt esetében
Miután [létrehozta az önálló Service Fabric-fürtöt Windows Server rendszerű gépeken,](service-fabric-cluster-creation-for-windows-server.md)az (üzleti) igények változhatnak, és csomópontokat kell hozzáadnia vagy eltávolítania a fürthöz. Ez a cikk részletes lépéseket tartalmaz ennek eléréséhez. Kérjük, vegye figyelembe, hogy a csomópont-funkciók hozzáadása/eltávolítása nem támogatott a helyi fejlesztési fürtökben.

## <a name="add-nodes-to-your-cluster"></a>Csomópontok hozzáadása a fürthöz

1. Készítse elő a fürthöz hozzáadni kívánt virtuális gép/gép a Terv című dokumentumban ismertetett lépéseket [követve, és készítse elő a Service Fabric-fürt központi telepítését.](service-fabric-cluster-standalone-deployment-preparation.md)

2. Azonosítsa, hogy melyik tartalék tartományés a frissítési tartomány, amelyhez hozzá adja ezt a virtuális gépet/gépet.

   Ha tanúsítványokat használ a fürt védelmére, a tanúsítványokat telepíteni kell a helyi tanúsítványtárolókban, hogy előkészítsék a csomópont csatlakozását a fürthöz. Az analóg akkor alkalmazható, ha más biztonsági formákat használ.

3. Távoli asztal (RDP) a virtuális gép/gép, amely hozzá szeretne adni a fürthöz.

4. Másolja vagy [töltse le a Windows Server Service Fabric önálló csomagját](https://go.microsoft.com/fwlink/?LinkId=730690) a virtuális gépre/gépre, és csomagolja ki a csomagot.

5. Futtassa a PowerShellt emelt szintű jogosultságokkal, és lépjen a kibontott csomag helyére.

6. Futtassa az *AddNode.ps1* parancsfájlt a hozzáadni hozandó új csomópontot leíró paraméterekkel. A következő példa egy VM5 nevű új csomópontot ad hozzá, nodeType0 típussal és 182.17.34.52 IP-címmel az UD1 és fd:/dc1/r0 azonosítóval. `ExistingClusterConnectionEndPoint`a meglévő fürtben már meglévő csomópont kapcsolatvégpontja, amely a fürt *bármely* csomópontjának IP-címe lehet. 

   Nem biztonságos (prototípus):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Biztonságos (tanúsítványalapú):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Amikor a parancsfájl futása befejeződik, ellenőrizheti, hogy az új csomópont hozzá lett-e adva a [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) parancsmag futtatásával.

7. A fürt különböző csomópontjai közötti konzisztencia biztosítása érdekében kezdeményeznie kell egy konfigurációs frissítést. Futtassa [a Get-ServiceFabricClusterConfiguration szolgáltatást](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) a legújabb konfigurációs fájl leéséhez, és adja hozzá az újonnan hozzáadott csomópontot a "Csomópontok" szakaszhoz. Azt is javasoljuk, hogy mindig a legújabb fürtkonfiguráció áll rendelkezésre abban az esetben, ha újra kell telepítenie egy fürt, amely ugyanazt a konfigurációt.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. A frissítés megkezdéséhez futtassa a [Start-ServiceFabricConfigurationUpgrade szolgáltatást.](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   Figyelheti a frissítés előrehaladását a Service Fabric Explorer. Másik lehetőségként futtathatja a [Get-ServiceFabricClusterUpgrade szolgáltatást.](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Csomópontok hozzáadása a Windows biztonsággal konfigurált fürtökhöz a gMSA használatával
A csoportfelügyelt szolgáltatásfiókkal(gMSA) konfigurált fürtök(https://technet.microsoft.com/library/hh831782.aspx)konfigurációs frissítéssel új csomópont adható hozzá:
1. Futtassa a [Get-ServiceFabricClusterConfiguration-t](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) bármelyik meglévő csomóponton a legújabb konfigurációs fájl leéséhez, és adja meg a "Csomópontok" szakaszban hozzáadni kívánt új csomópont részleteit. Győződjön meg arról, hogy az új csomópont ugyanannak a csoportkezelt fióknak a része. Ennek a fióknak minden gépen rendszergazdának kell lennie.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. A frissítés megkezdéséhez futtassa a [Start-ServiceFabricConfigurationUpgrade szolgáltatást.](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Figyelheti a frissítés előrehaladását a Service Fabric Explorer. Másik lehetőségként futtathatja a [Get-ServiceFabricClusterUpgrade szolgáltatást](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Csomóponttípusok hozzáadása a fürthöz
Új csomóponttípus hozzáadásához módosítsa a konfigurációt úgy, hogy az az új csomóponttípus szerepeljen a "NodeTypes" szakaszban a "Tulajdonságok" alatt, és kezdje meg a konfigurációs frissítést [a Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)segítségével. A frissítés befejezése után új csomópontokat adhat hozzá a fürthöz ezzel a csomóponttípussal.

## <a name="remove-nodes-from-your-cluster"></a>Csomópontok eltávolítása a fürtből
A csomópont a következő módon távolítható el a fürtből egy konfigurációs frissítés sel:

1. Futtassa [a Get-ServiceFabricClusterConfiguration parancsot](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) a legújabb konfigurációs fájl leéséhez, és *távolítsa el* a csomópontot a "Csomópontok" szakaszból.
Adja hozzá a "NodesToBeRemoved" paramétert a "Setup" szakaszhoz a "FabricSettings" szakaszban. Az "értéknek" az eltávolítandó csomópontok csomópontneveinek vesszővel elválasztott listájának kell lennie.

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
2. A frissítés megkezdéséhez futtassa a [Start-ServiceFabricConfigurationUpgrade szolgáltatást.](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Figyelheti a frissítés előrehaladását a Service Fabric Explorer. Másik lehetőségként futtathatja a [Get-ServiceFabricClusterUpgrade szolgáltatást.](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> A csomópontok eltávolítása több frissítést is kezdeményezhet. Egyes csomópontok `IsSeedNode=”true”` címkével vannak megjelölve, és a fürtjegyzék `Get-ServiceFabricClusterManifest`lekérdezésével azonosíthatók. Az ilyen csomópontok eltávolítása hosszabb időt vehet igénybe, mint mások, mivel a magcsomópontokat ilyen esetekben mozgatni kell. A fürtnek legalább 3 elsődleges csomóponttípus-csomópontot kell fenntartania.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Csomóponttípusok eltávolítása a fürtből
A csomóponttípus eltávolítása előtt ellenőrizze, hogy vannak-e olyan csomópontok, amelyek a csomópont típusára hivatkoznak. Távolítsa el ezeket a csomópontokat a megfelelő csomóponttípus eltávolítása előtt. Miután az összes megfelelő csomópontot eltávolította, eltávolíthatja a NodeType típust a fürtkonfigurációból, és megkezdheti a konfigurációfrissítést a [Start-ServiceFabricConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)használatával.


### <a name="replace-primary-nodes-of-your-cluster"></a>A fürt elsődleges csomópontjainak cseréje
Az elsődleges csomópontok cseréjét egyik csomópontot a másik után kell elvégezni, ahelyett, hogy eltávolítaná, majd kötegekben hozzáadná őket.


## <a name="next-steps"></a>További lépések
* [Az önálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md)
* [Önálló fürt biztonságossá tétele Windows rendszeren X509-tanúsítványokkal](service-fabric-windows-cluster-x509-security.md)
* [Önálló Service Fabric-fürt létrehozása Windows tfuttatóAzure-beli virtuális gépekkel](service-fabric-cluster-creation-with-windows-azure-vms.md)

