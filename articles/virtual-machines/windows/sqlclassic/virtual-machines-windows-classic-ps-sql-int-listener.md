---
title: Always On rendelkezésre állási csoportokra vonatkozó ILB figyelő konfigurálása az Azure-ban |} A Microsoft Docs
description: Ebben az oktatóanyagban a klasszikus üzemi modellel létrehozott erőforrásokat használja, és a egy Always On rendelkezésre állási csoport figyelőjének hozza létre az Azure-ban, amely egy belső terheléselosztót használ.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 0466265ad5a24e8ea6dc5079e2b4006d74e7dde0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452532"
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Always On rendelkezésre állási csoportokra vonatkozó ILB figyelő konfigurálása az Azure-ban
> [!div class="op_single_selector"]
> * [Belső figyelő](../classic/ps-sql-int-listener.md)
> * [Külső figyelő](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus üzemi modell használatát ismerteti. Azt javasoljuk, hogy az új telepítések esetén a Resource Manager modellt használja.

Egy Always On rendelkezésre állási csoporthoz figyelő beállítása a Resource Manager-modellben: [egy Always On rendelkezésre állási csoport terheléselosztó konfigurálása az Azure-ban](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

A rendelkezésre állási csoport replikáit, amelyek csak helyszíni vagy csak az Azure, vagy a helyszíni és az Azure, amely span hibrid konfigurációk esetében is tartalmazhat. Azure replikák ugyanabban a régióban vagy több virtuális hálózatokat használó több régióban is lehetnek. Ebben a cikkben szereplő eljárások azt feltételezik, hogy már [konfigurálva a rendelkezésre állási csoport](../classic/portal-sql-alwayson-availability-groups.md) , de még nem konfigurálta a egy figyelőt.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Irányelvek és korlátozások a belső figyelők
Belső terheléselosztó (ILB) rendelkező Azure-ban, egy rendelkezésre állási csoport figyelőjének használata a következőkre vonatkozik:

* A rendelkezésre állási csoport figyelőjének támogatott Windows Server 2008 R2, Windows Server 2012 és Windows Server 2012 R2.
* Csak egy belső rendelkezésre állási csoport figyelőjét, mert a figyelő az ILB van konfigurálva, és minden felhőszolgáltatás esetében csak egy ILB minden felhőszolgáltatás esetében támogatott. Azonban akkor hozhat létre több külső figyelői lehet. További információkért lásd: [külső figyelő az Always On rendelkezésre állási csoportok konfigurálása az Azure-ban](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>A figyelő a kisegítő lehetőségek meghatározása
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ez a cikk foglalkozik, amely egy ILB használja egy figyelő létrehozásához. Ha egy nyilvános vagy külső figyelő, ez a cikk ismerteti a beállítás mentése verziójának megtekintéséhez egy [külső figyelő](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Elosztott terhelésű virtuális gépek végpontjaihoz visszatérési közvetlen kiszolgálói létrehozása
Először hozzon létre egy ILB a szkript futtatásával később ebben a szakaszban.

Az egyes virtuális Gépekhez az Azure-replikát tartalmazó elosztott terhelésű végpont létrehozásához. Ha replikákat több régióban, minden egyes replikának a régió ugyanazon a felhőszolgáltatáson ugyanazon az Azure virtuális hálózatban kell lennie. Rendelkezésre állási csoport replikái több Azure-régióra kiterjedő létrehozása több virtuális hálózat konfigurálása szükséges. Közötti virtuális hálózati kapcsolat konfigurálásával kapcsolatos további információkért lásd: [virtuális hálózati kapcsolat konfigurálása virtuális hálózat](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Az Azure Portalon nyissa meg minden virtuális gép, amelyen a replika a részletek megtekintéséhez.

2. Kattintson a **végpontok** lapon az egyes virtuális Gépekhez.

3. Ellenőrizze, hogy a **neve** és **nyilvános Port** a figyelő végpontot, amely a használni kívánt még nem használja. Ebben a szakaszban a példában a neve a következő *MyEndpoint*, és a portot *1433-as*.

4. A helyi ügyfélen, töltse le és telepítse a legújabb [PowerShell-modul](https://azure.microsoft.com/downloads/).

5. Indítsa el az Azure Powershellt.  
    Egy új PowerShell-munkamenetben nyílik meg, az Azure felügyeleti modulok terhelését.

6. Futtassa az `Get-AzurePublishSettingsFile` parancsot. Ez a parancsmag egy böngészőt, és a közzétételi beállítások fájljának letöltése egy helyi könyvtárba irányítja. Akkor kérheti a bejelentkezési hitelesítő adatokat az Azure-előfizetésében.

7. Futtassa a következő `Import-AzurePublishSettingsFile` parancsot a letöltött közzétételi beállítások fájljának elérési útja:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    A közzétételi beállítások fájljának importálása után kezelheti az Azure-előfizetés a PowerShell-munkamenetben.

8. A *ILB*, statikus IP-címet rendelni. Vizsgálja meg az aktuális virtuális hálózati konfiguráció a következő parancs futtatásával:

        (Get-AzureVNetConfig).XMLConfiguration
9. Megjegyzés: a *alhálózati* nevét az alhálózatot, amelyet a virtuális gépeket tartalmaz, amelyeken a replikákat. Ez a név szolgál a szkriptben $SubnetName paraméterben.

10. Megjegyzés: a *VirtualNetworkSite* nevét és a kezdő *AddressPrefix* az, hogy a replika virtuális gépeket tartalmazó alhálózathoz. Mindkét értéket átadásával elérhető IP-cím keresése a `Test-AzureStaticVNetIP` parancsot, és a vizsgálata a *AvailableAddresses*. Például, ha a virtuális hálózat neve *MyVNet* egy alhálózati címtartományt, amely kezdődik, és *172.16.0.128*, a következő parancsot kellene listában elérhető címek:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Válassza ki valamelyik rendelkezésre álló címet, és használja azt a parancsfájlt a következő lépésben $ILBStaticIP paraméterében.

12. Másolja a következő PowerShell-parancsfájl egy szövegszerkesztőbe, és állítsa be a változó értéke a környezetnek megfelelően. Alapértelmezett érték az egyes paramétereket adott meg.  

    Meglévő affinitáscsoportok használó központi telepítések ILB nem vehető fel. ILB-követelményeivel kapcsolatos további információkért lásd: [belső load balancer áttekintése](../../../load-balancer/load-balancer-internal-overview.md).

    Is ha a rendelkezésre állási csoport Azure-régióra is kiterjed, futtatnia kell a parancsfájl egyszer a valamennyi adatközpont a felhőszolgáltatás és a csomópontok található azt az adatközpontot.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Miután beállította a változókat, másolja a szkriptet a szöveges szerkesztő a PowerShell-munkamenetet a futtatáshoz. Ha a rendszer még mindig **>>**, nyomja le az ENTER billentyűt a újra, hogy a parancsfájl futtatásának megkezdése.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Győződjön meg arról, hogy KB2854082 telepítve van-e, ha szükséges
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Nyissa meg a tűzfal portjait a rendelkezésre állási csoport csomópontjain
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelőjének létrehozása

A rendelkezésre állási csoport figyelőjének létrehozása két lépésben. Először hozzon létre az ügyfél-hozzáférési pont fürterőforrás, és a függőségek konfigurálása. Második konfigurálja a fürt erőforrásai a PowerShellben.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Az ügyfél hozzáférési pontjának létrehozása és a függőségeit konfigurálása
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurálja a fürt erőforrásai a PowerShellben
1. ILB a korábban létrehozott ILB IP-címét kell használnia. Ez a PowerShell IP-cím beszerzéséhez használja a következő szkriptet:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. A virtuális gépek egyik másolja a PowerShell-parancsfájl az operációs rendszerének egy szövegszerkesztőbe, és ezután állítsa be a változókat a korábban feljegyzett értékekre.

    A Windows Server 2012 vagy újabb verzió használja a következő szkriptet:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    A Windows Server 2008 R2 használja a következő szkriptet:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Miután beállította a változókat, nyisson meg egy rendszergazda jogú Windows PowerShell-ablakban, illessze be a szövegszerkesztőben a szkriptet a PowerShell-munkamenetet a futtatáshoz. Ha a rendszer még mindig **>>**, nyomja le az Enter újra, győződjön meg arról, hogy a parancsfájl futtatásának megkezdése.

4. Ismételje meg a fenti lépéseket minden egyes virtuális Géphez.  
    Ez a szkript konfigurálja az IP-cím erőforrás a felhőszolgáltatás IP-címmel, és beállítja a más paramétereket, például a mintavételi portot. Az IP-cím erőforrás online állapotba kerül, ha azt a korábban létrehozott elosztott terhelésű végpont válaszolni tud a lekérdezés a a mintavételi portokon.

## <a name="bring-the-listener-online"></a>A figyelő online állapotba.
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Ellenőrzési elemek
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>A rendelkezésre állási csoport figyelőjét (az azonos virtuális hálózaton belül) tesztelése
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>További lépések
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
