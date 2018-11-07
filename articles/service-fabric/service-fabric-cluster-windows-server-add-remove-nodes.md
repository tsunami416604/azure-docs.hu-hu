---
title: Hozzáadása vagy eltávolítása, csomópontok önálló Service Fabric-fürtön |} A Microsoft Docs
description: Megtudhatja, hogyan hozzáadása vagy eltávolítása, csomópontok az Azure Service Fabric-fürt fizikai vagy virtuális gép fut, amely lehet a helyszíni Windows Server vagy a felhőben.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 69680331bdad0faa36cb3df6117baf8b358da132
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251019"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Hozzáadása vagy eltávolítása, csomópontok, a Windows Server rendszert futtató önálló Service Fabric-fürt
Miután [az önálló Service Fabric-fürt létrehozása a Windows Server-gépek](service-fabric-cluster-creation-for-windows-server.md), a (vállalati) igényeinek megfelelően módosíthatja, és adjon hozzá vagy távolíthat el csomópontokat a fürthöz kell. Ez a cikk részletesen bemutatja a lépéseket ennek eléréséhez. Vegye figyelembe, hogy hozzáadása/eltávolítása csomópont funkció nem támogatott a helyi fejlesztési fürtök.

## <a name="add-nodes-to-your-cluster"></a>Csomópontok hozzáadása a fürthöz

1. Készítse elő a virtuális gép/gép ismertetett lépéseket követve a fürthöz hozzáadni kívánt [tervezze meg és készítse elő a Service Fabric-fürtök üzembe helyezése](service-fabric-cluster-creation-for-windows-server.md)
2. Melyik tartalék tartomány és frissítési tartományt szeretné hozzáadni a virtuális gép/gép azonosítása
3. A fürthöz hozzáadni kívánt virtuális gép/géphez távoli asztali (RDP)
4. Másolás vagy [töltse le a különálló csomag a Service Fabric Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) a virtuális géphez, és tömörítse ki a csomagot
5. Powershell futtatásához emelt szintű jogosultságokkal, majd keresse meg a kicsomagolt csomag helye
6. Futtassa a *AddNode.ps1* parancsfájlt az új csomópont hozzáadása leíró paraméterekkel. Az alábbi példa hozzáad egy új csomópont VM5 nevű, típus, NodeType0 és IP-cím 182.17.34.52, UD1 és fd: / dc1/r0. A *ExistingClusterConnectionEndPoint* egy kapcsolati végpont egy csomópont már meglévő fürtben, amely az IP-címe lehet *bármely* a fürt csomópontjaihoz.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Miután a parancsfájl a lejáratot követően újrainduljon, ha hozzá van adva az új csomópont futtatásával ellenőrizheti a [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) parancsmagot.

7. A konzisztencia érdekében a fürt csomópontjai között, akkor a konfiguráció frissítése kell kezdeményezni. Futtatás [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) a legfrissebb konfigurációs fájlt, és adja hozzá az újonnan hozzáadott csomópontot "Csomópont" szakaszban. Azt is javasoljuk, hogy mindig a legújabb fürtkonfiguráció érhető el, abban az esetben módosítania helyezéskor egy fürt ugyanazt a konfigurációt.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Futtatás [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) a frissítés megkezdéséhez.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    A Service Fabric Explorert a frissítés állapotának figyelése Másik megoldásként futtathatja [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Csoportosan felügyelt szolgáltatásfiókot használó Windows-Security szolgáltatással konfigurált fürtöknél csomópontok hozzáadása
Csoport által felügyelt szolgáltatás Account(gMSA) szolgáltatással konfigurált fürtöknél (https://technet.microsoft.com/library/hh831782.aspx), egy új csomópont-konfiguráció frissítése vehetők:
1. Futtatás [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) bármely, a meglévő csomópontok, a legfrissebb konfigurációs fájlt, és adja hozzá az új csomópontot szeretne hozzáadni a "Csomópont" szakaszban részleteit. Ellenőrizze, hogy az új csomópont ugyanazzal a csoportosan felügyelt fiókkal része. Ez a fiók minden gépen rendszergazdának kell lennie.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Futtatás [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) a frissítés megkezdéséhez.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    A Service Fabric Explorert a frissítés állapotának figyelése Másik megoldásként futtathatja [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Csomóponttípusok hozzáadni a fürthöz
Annak érdekében, hogy adjon hozzá egy új csomópont típusa, módosíthatja a konfigurációt, például az új csomópont típusa az "NodeType" a "Tulajdonságok" szakaszt, és a konfigurálás megkezdése használó frissítse [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). A frissítés befejezése után is hozzáadhat új csomópontokat a fürthöz az ennek a csomóponttípusnak.

## <a name="remove-nodes-from-your-cluster"></a>Csomópont eltávolítása a fürtből
Egy csomópont eltávolíthatók a fürtök a konfiguráció frissítése a következő módon:

1. Futtatás [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) beolvasni a legfrissebb konfigurációs fájlt, és *eltávolítása* a csomópont "Csomópont" szakaszban.
Adja hozzá a "NodesToBeRemoved" paraméter "FabricSettings" szakasz "Beállítása" szakaszát. A "value" egy vesszővel elválasztott listáját a csomópontok, el kell távolítani a csomópont nevének kell lennie.

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
2. Futtatás [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) a frissítés megkezdéséhez.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    A Service Fabric Explorert a frissítés állapotának figyelése Másik megoldásként futtathatja [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> Csomópont eltávolítása több frissítés kezdeményezhet. Egyes csomópontok lesznek megjelölve `IsSeedNode=”true”` címkézés és a fürt lekérdezésével azonosíthatók használatával manifest `Get-ServiceFabricClusterManifest`. Ilyen csomópont eltávolítása, mint a többi tovább tarthat, mivel az ilyen esetekben áthelyezhető a magcsomópontok tartalmaz. A fürt legalább 3 elsődleges csomópont típusa csomópont kell fenntartani.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Csomóponttípus eltávolítása a fürtből
Mielőtt eltávolítaná a csomópont típusa, ellenőrizze ellenőrizze, hogy a csomópont típusa hivatkozó bármely csomópont. Ezek a csomópontok eltávolítása a megfelelő csomóponttípus eltávolítása előtt. Miután az összes megfelelő csomópont el lesznek távolítva, a NodeType csomóponttípus eltávolítása a fürt konfigurációját, és a konfigurálás megkezdése használó frissítse [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Cserélje le a fürt elsődleges csomópontok
Váltja fel az elsődleges csomópont lehet elvégezni egy csomópont eltávolítása, majd kötegekben helyett egymás után.


## <a name="next-steps"></a>További lépések
* [Különálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md)
* [Különálló fürt védelme a Windows X509 használ a tanúsítványok](service-fabric-windows-cluster-x509-security.md)
* [Önálló Service Fabric-fürt létrehozása Windows rendszert futtató Azure virtuális gépekkel](service-fabric-cluster-creation-with-windows-azure-vms.md)

