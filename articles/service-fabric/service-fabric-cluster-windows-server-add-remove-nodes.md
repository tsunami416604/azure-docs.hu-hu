---
title: "Különálló Service Fabric-fürt a csomópontok hozzáadásához és eltávolításához |} Microsoft Docs"
description: "Útmutató egy fizikai gép vagy a helyszíni lehet a Windows Server rendszerű virtuális gép vagy a felhőben az Azure Service Fabric-fürt a csomópontok hozzáadásához és eltávolításához."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/02/2017
ms.author: dekapur
ms.openlocfilehash: 9c6035e97de38ff63ef074109afd9f3c7484f828
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>A Windows Server rendszert futtató önálló Service Fabric-fürt a csomópontok hozzáadásához és eltávolításához
Miután [a különálló Service Fabric-fürt létrehozása a Windows Server gépeken](service-fabric-cluster-creation-for-windows-server.md), az üzleti igényeknek megfelelően változhat, és szükség lehet az a fürt a csomópontok hozzáadásához és eltávolításához. Ez a cikk részletes lépéseit ennek érdekében. Vegye figyelembe, hogy hozzáadása csomópont funkció nem támogatott a helyi fejlesztési fürtök.

## <a name="add-nodes-to-your-cluster"></a>Csomópontok hozzáadása a fürthöz
1. Az említett lépések a fürthöz hozzáadni kívánt virtuális gép/gép előkészítése a [készítse elő a fürtöt tartalmazó környezetben az Előfeltételek teljesítése érdekében a gépek](service-fabric-cluster-creation-for-windows-server.md) szakasz
2. Melyik tartalék tartomány és a frissítési tartomány hozzáadása a virtuális gép/gép kívánja azonosítása
3. A távoli asztal (RDP) a fürthöz hozzáadni kívánt virtuális gép vagy gépek be
4. Másolás vagy [töltse le az önálló csomag a Service Fabric Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) a virtuális géphez, és bontsa ki a csomagot
5. Powershell futtassa emelt szintű jogosultságokkal, és keresse meg a helyet a tömörítetlen csomag
6. Futtassa a *AddNode.ps1* parancsfájl hozzáadása az új csomópont leíró paraméterekkel. Az alábbi példában egy VM5 nevű új csomópont hozzáadása, típussal, NodeType0 és IP-cím 182.17.34.52, UD1 és fd: / dc1/r0. A *ExistingClusterConnectionEndPoint* van a kapcsolat végpontja már a meglévő fürt csomópontja, amely az IP-címe lehet *bármely* a fürt csomópontja.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Miután a parancsfájl befejezése után történik, ha az új csomópont bővült futtatásával ellenőrizheti a [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) parancsmag.

7. Konzisztencia biztosításához a fürt csomópontjai között, a felhasználónak kezdeményeznie kell a konfiguráció frissítése. Futtatás [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) a legfrissebb konfigurációs fájlt, és adja hozzá az újonnan hozzáadott csomópontot "Csomópont" szakasz. Emellett javasoljuk, hogy mindig a legújabb elérhető abban az esetben újra kell redploy ugyanazt a konfigurációt a fürt fürt konfigurációval rendelkezik.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Futtatás [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) a frissítésének megkezdésére.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Az előrehaladást a frissítés a Service Fabric Explorerben talál. Alternatív megoldásként futtathatja [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Csomópontok hozzáadása a konfigurált Windows biztonsági csoportosan felügyelt szolgáltatásfiókot használó fürtök
A fürtök konfigurált csoport által felügyelt szolgáltatás Account(gMSA) (https://technet.microsoft.com/library/hh831782.aspx) egy új csomópont felveheti egy konfigurációs frissítéssel:
1. Futtatás [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) a legfrissebb konfigurációs fájlt, és adja hozzá az új csomópont hozzá szeretne adni az "Csomópont" szakaszban adatait a meglévő csomópontok egyikén. Ellenőrizze, hogy az új csomópont ugyanazt a csoportosan felügyelt fiókot részét képezi. Ezt a fiókot minden számítógépen rendszergazdának kell lennie.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Futtatás [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) a frissítésének megkezdésére.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Az előrehaladást a frissítés a Service Fabric Explorerben talál. Alternatív megoldásként futtathatja [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Csomóponttípusok hozzáadása a fürthöz
Ahhoz, hogy az új csomópont-típus hozzáadása, módosítása a konfigurációját, és tartalmazza az új csomópont típus az "a NodeType tulajdonságok értéke" a "Tulajdonságok" szakaszt, és egy konfigurációs megkezdéséhez használatával frissítse [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). A frissítés után is hozzáadhat új csomópontok a fürthöz, ez a csomóponttípus.

## <a name="remove-nodes-from-your-cluster"></a>Csomópontok eltávolítása a fürtből
A csomópont távolíthatók el a fürt egy konfigurációs frissítése a következő módon:

1. Futtatás [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) beolvasni a legfrissebb konfigurációs fájl és *eltávolítása* a csomópont "Csomópont" szakaszában.
A "NodesToBeRemoved" paraméter hozzáadása "FabricSettings" szakasz "Beállítása" szakaszát. A "érték" csomópont nevének a csomópontokra, amelyeket el kell távolítani a vesszővel tagolt listáját kell lennie.

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
2. Futtatás [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) a frissítésének megkezdésére.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Az előrehaladást a frissítés a Service Fabric Explorerben talál. Alternatív megoldásként futtathatja [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> Csomópontok eltávolítása több frissítés kezdeményezhet. Egyes csomópontok lesznek megjelölve `IsSeedNode=”true”` címkét, és a fürt lekérdezésével azonosítható manifest használatával `Get-ServiceFabricClusterManifest`. Ilyen csomópontok eltávolítása is tovább tarthat a többinél mivel helyezi át az ilyen helyzetekben a kezdőérték csomópontok kell. A fürt legalább 3 elsődleges típusú csomópontok kell fenntartani.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Csomóponttípusok eltávolítása a fürtből
Mielőtt eltávolítaná a csomóponttípus, kérjük, ellenőrizze hivatkozzon a csomóponttípus csomópontok esetén. A megfelelő típusú csomópont eltávolítása előtt távolítsa el ezeket a csomópontokat. Minden megfelelő csomópontokat törlődik, miután a NodeType távolítsa el a fürtkonfiguráció, és egy konfigurációs megkezdéséhez használatával frissítse [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Cserélje le a fürt elsődleges csomópontok
A cseréje az elsődleges csomópont helyett eltávolítása és a kötegek egymás után kell elvégezni egy csomópont.


## <a name="next-steps"></a>Következő lépések
* [Önálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md)
* [Biztonságos Windows X509 használata önálló fürtben, tanúsítványok](service-fabric-windows-cluster-x509-security.md)
* [Hozzon létre egy különálló Service Fabric-fürt Windowst futtató Azure virtuális gépeken](service-fabric-cluster-creation-with-windows-azure-vms.md)

