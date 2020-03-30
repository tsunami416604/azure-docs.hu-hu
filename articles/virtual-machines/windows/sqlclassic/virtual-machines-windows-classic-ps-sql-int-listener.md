---
title: ILB-figyelő konfigurálása rendelkezésre állási csoportokhoz (klasszikus)
description: Ez az oktatóanyag a klasszikus üzembe helyezési modellel létrehozott erőforrásokat használja, és létrehoz egy mindig rendelkezésre állási csoportfigyelőt egy SQL Server virtuális géphez az Azure-ban, amely belső terheléselosztót használ.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77j
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f26c5a6c6fc2774d19beaa021015357a1991f0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978175"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>ILB-figyelő konfigurálása az Azure SQL Server virtuális gépein rendelkezésre állási csoportokhoz
> [!div class="op_single_selector"]
> * [Belső figyelő](../classic/ps-sql-int-listener.md)
> * [Külső figyelő](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Azure Resource Manager és a klasszikus.](../../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. Azt javasoljuk, hogy a legtöbb új központi telepítések használja az Erőforrás-kezelő modell.

Ha az Erőforrás-kezelő modellben egy Mindig csak állási csoport figyelőjét szeretné konfigurálni, olvassa el a [Terheléselosztó konfigurálása az Azure-ban egy mindig elérhető helyen lévő csoporthoz című témakört.](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)

A rendelkezésre állási csoport tartalmazhat replikákat, amelyek csak a helyszínen, vagy csak az Azure-ban, vagy amelyek mind a helyszíni és az Azure hibrid konfigurációk. Az Azure-replikák ugyanabban a régióban vagy több régióban, amelyek több virtuális hálózatokat használó található. Ebben a cikkben az eljárások feltételezik, hogy már [konfigurált egy rendelkezésre állási csoportot,](../classic/portal-sql-alwayson-availability-groups.md) de még nem konfigurált a figyelő.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>A belső hallgatókra vonatkozó irányelvek és korlátozások
A belső terheléselosztó (ILB) és az Azure-beli rendelkezésre állási csoportfigyelő használata a következő irányelvek nek megfelelő:

* A rendelkezésre állási csoportfigyelője windows Server 2008 R2, Windows Server 2012 és Windows Server 2012 R2 rendszerben támogatott.
* Csak egy belső rendelkezésre állási csoport figyelője támogatja az egyes felhőszolgáltatás, mert a figyelő az ILB-re van konfigurálva, és minden felhőszolgáltatáshoz csak egy ILB van konfigurálva. Azonban lehetőség van több külső figyelők létrehozására. További információ: [Külső figyelő konfigurálása az Azure-ban a Mindig rendelkezésre állási csoportokhoz](../classic/ps-sql-ext-listener.md)című témakörben talál.

## <a name="determine-the-accessibility-of-the-listener"></a>A figyelő hozzáférhetőségének meghatározása
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ez a cikk egy ILB-t használó figyelő létrehozására összpontosít. Ha nyilvános vagy külső figyelőre van szüksége, tekintse meg a cikk nek azt a verzióját, amely egy külső figyelő beállítását [tárgyalja.](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Kiegyensúlyozott terhelésű virtuálisgép-végpontok létrehozása közvetlen kiszolgálói visszatéréssel
Először hozzon létre egy ILB futtatásával a parancsfájl későbbi szakaszában ebben a szakaszban.

Hozzon létre egy terhelés-kiegyensúlyozott végpont minden virtuális gép, amely egy Azure-replika. Ha több régióban replikák, az adott régió minden replika kell ugyanabban a felhőszolgáltatásugyanabban az Azure virtuális hálózatban. Több Azure-régióra kiterjedő rendelkezésre állási csoport replikák létrehozása több virtuális hálózat konfigurálását igényli. A virtuális hálózatok közötti kapcsolat konfigurálásáról a [Virtuális hálózat konfigurálása a virtuális hálózati kapcsolatra](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)című témakörben talál további információt.

1. Az Azure Portalon tekintse meg az egyes virtuális gépek, amely egy replika a részletek megtekintéséhez.

2. Kattintson a **Végpontok** fülre az egyes virtuális gépek.

3. Ellenőrizze, hogy a használni kívánt figyelővégpont **neve** és **nyilvános portja** nincs-e még használatban. Ebben a szakaszban a példában a név *MyEndpoint*, a port pedig *1433*.

4. A helyi ügyfélen töltse le és telepítse a legújabb [PowerShell-modult.](https://azure.microsoft.com/downloads/)

5. Indítsa el az Azure PowerShellt.  
    Megnyílik egy új PowerShell-munkamenet, amelybe az Azure felügyeleti moduljai töltődnek be.

6. Futtassa az `Get-AzurePublishSettingsFile` parancsot. Ez a parancsmag a böngészőhöz irányítja, hogy letöltse a közzétételi beállításfájlt egy helyi könyvtárba. Előfordulhat, hogy a rendszer kéri a bejelentkezési hitelesítő adatait az Azure-előfizetéshez.

7. Futtassa `Import-AzurePublishSettingsFile` a következő parancsot a letöltött közzétételi beállításfájl elérési útjával:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    A közzétételi beállításfájl importálása után kezelheti az Azure-előfizetést a PowerShell-munkamenetben.

8. Az *ILB-hez*rendeljen statikus IP-címet. Vizsgálja meg az aktuális virtuális hálózati konfigurációt a következő parancs futtatásával:

        (Get-AzureVNetConfig).XMLConfiguration
9. Jegyezze fel a replikákat tartalmazó virtuális gépeket tartalmazó *alhálózat* nevét. Ez a név a parancsfájl $SubnetName paraméterében használatos.

10. Vegye figyelembe a *VirtualNetworkSite* nevét és a replikákat tartalmazó alhálózat kezdő *AddressPrefix-jét.* Keresse meg a rendelkezésre álló IP-címet úgy, hogy mindkét értéket átadja a `Test-AzureStaticVNetIP` parancsnak, és megvizsgálja a *AvailableAddresses értéket.* Ha például a virtuális hálózat neve *MyVNet,* és alhálózati címtartománya *172.16.0.128-nál*kezdődik, a következő parancs az elérhető címeket sorolja fel:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Válassza ki az egyik elérhető címet, és használja azt a $ILBStaticIP paraméter a parancsfájl a következő lépésben.

12. Másolja a következő PowerShell-parancsfájlt egy szövegszerkesztőbe, és állítsa be a változóértékeket a környezetének megfelelően. Bizonyos paraméterek alapértelmezett beállításai tadtak meg.  

    Az affinitáscsoportokat használó meglévő központi telepítések nem adhatnak hozzá ILB-t. Az ILB-követelményekről a [Belső terheléselosztó áttekintéscímű témakörben olvashat bővebben.](../../../load-balancer/load-balancer-internal-overview.md)

    Emellett ha a rendelkezésre állási csoport az Azure-régiókra terjed ki, a parancsfájlt egyszer kell futtatnia a felhőszolgáltatás és az adott adatközpontban található csomópontok minden egyes adatközpontjában.

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

13. Miután beállította a változókat, másolja a parancsfájlt a szövegszerkesztőből a PowerShell-munkamenetbe a futtatásához. Ha a kérdés **>>** továbbra is megjelenik, nyomja le ismét az Enter billentyűt, hogy a parancsfájl elinduljon.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Szükség esetén ellenőrizze, hogy a KB2854082 új
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>A tűzfalportok megnyitása a rendelkezésre állási csoport csomópontjaiban
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelőjének létrehozása

Hozza létre a rendelkezésre állási csoport figyelő két lépésben. Először hozza létre az ügyfél-hozzáférési pont fürterőforrását, és konfigurálja a függőségeket. Másodszor konfigurálja a fürt erőforrásait a PowerShellben.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Az ügyfél-hozzáférési pont létrehozása és a fürtfüggőségek konfigurálása
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>A fürterőforrások konfigurálása a PowerShellben
1. Az ILB-hez a korábban létrehozott ILB IP-címét kell használnia. Az IP-cím PowerShellben való beszerzéséhez használja a következő parancsfájlt:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Az egyik virtuális gépen másolja az operációs rendszer PowerShell-parancsfájlját egy szövegszerkesztőbe, majd állítsa be a változókat a korábban megjegyzett értékekre.

    Windows Server 2012 vagy újabb rendszer esetén használja a következő parancsfájlt:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Windows Server 2008 R2 esetén használja a következő parancsfájlt:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Miután beállította a változókat, nyisson meg egy emelt szintű Windows PowerShell-ablakot, illessze be a parancsfájlt a szövegszerkesztőből a PowerShell-munkamenetbe a futtatásához. Ha a kérdés **>>** továbbra is megjelenik, nyomja le ismét az Enter billentyűt, és győződjön meg arról, hogy a parancsfájl elindul.

4. Ismételje meg az előző lépéseket az egyes virtuális gépek.  
    Ez a parancsfájl konfigurálja az IP-cím erőforrás a felhőszolgáltatás IP-címét, és beállítja az egyéb paraméterek, például a mintavételi port. Amikor az IP-cím erőforrás online állapotba kerül, válaszolhat a mintavételi porton a korábban létrehozott terhelésű végpontlekérdezésre.

## <a name="bring-the-listener-online"></a>A hallgató online állapotba hozása
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Nyomon követési elemek
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>A rendelkezésre állási csoport figyelőjének tesztelése (ugyanazon a virtuális hálózaton belül)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>További lépések
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
