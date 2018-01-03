---
title: "Egy ILB figyelőt az Always On rendelkezésre állási csoportok konfigurálása az Azure-ban |} Microsoft Docs"
description: "Ez az oktatóanyag a klasszikus üzembe helyezési modellel létrehozott erőforrást használ, és a belső terheléselosztót használó Azure-ban létrehoz egy Always On rendelkezésre állási csoport figyelőjének."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: fea70b389b1f1d6af963e3f14fdc48e8d857dd53
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Egy ILB figyelőt az Always On rendelkezésre állási csoportok konfigurálása az Azure-ban
> [!div class="op_single_selector"]
> * [Belső figyelő](../classic/ps-sql-int-listener.md)
> * [Külső figyelő](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzembe helyezési modellel használatát. Azt javasoljuk, hogy az új telepítések esetén a Resource Manager modellt használja.

A Resource Manager modellt egy figyelőt a következő Always On rendelkezésre állási csoport konfigurálásához lásd: [egy terheléselosztót egy Always On rendelkezésre állási csoport konfigurálása az Azure-](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

A rendelkezésre állási csoport replikák, amelyek csak a helyszíni vagy Azure csak, vagy a helyszíni és az Azure hibrid konfigurációk átnyúló tartalmazhat. Az Azure replikák a régión belül, vagy több virtuális hálózat használó különféle régiókban találhatók. Ebben a cikkben szereplő eljárások azt feltételezik, hogy már rendelkezik [konfigurált rendelkezésre állási csoport](../classic/portal-sql-alwayson-availability-groups.md) , de még nincs konfigurálva egy figyelő.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Tudnivalók és korlátozások belső figyelők
A használata egy belső terheléselosztón (ILB) egy rendelkezésre állási csoport figyelőjével, az Azure rendszerben a következő irányelveket:

* A rendelkezésre állási csoport figyelőjének Windows Server 2008 R2, Windows Server 2012 és Windows Server 2012 R2 esetén támogatott.
* Csak egy belső rendelkezésre állási csoport figyelőjének minden felhőalapú szolgáltatás, mert a figyelő a Példánynak van konfigurálva, és csak egy ILB minden felhőszolgáltatás esetén támogatott. Azonban azt is létrehozhat több külső figyelők. További információkért lásd: [egy külső figyelőt a Always On rendelkezésre állási csoportok konfigurálása az Azure-](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>A figyelő a kisegítő lehetőségek meghatározása
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ez a cikk foglalkozik, amely egy ILB használja egy figyelő létrehozásával. Ha egy nyilvános vagy külső figyelőt, lásd: Ez a cikk ismerteti, amelyek a beállítás mentése verziója egy [külső figyelő](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Hozzon létre virtuális gép elosztott terhelésű végpontok közvetlen kiszolgálói visszatérési
Először hozzon létre egy ILB a parancsfájl futtatásával később ebben a szakaszban.

Az egyes virtuális gépek az Azure-replikát tartalmazó elosztott terhelésű végpont létrehozásához. Ha több régióba replikákat, az adott régió minden egyes replikának a azonos felhőszolgáltatásban azonos Azure virtuális hálózatban kell lennie. Rendelkezésre állási csoport replikái, amelyek több Azure-régiók több létrehozása több virtuális hálózat beállítását igényli. Több virtuális hálózati kapcsolat konfigurálásával kapcsolatos további információkért lásd: [virtuális hálózat konfigurálása a virtuális hálózati kapcsolat](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Nyissa meg az Azure portálon, minden egyes virtuális géphez, amelyen a replika adatait tekintheti meg.

2. Kattintson a **végpontok** lapon az egyes virtuális gépek.

3. Ellenőrizze, hogy a **neve** és **nyilvános Port** a figyelő végponttal, amely a használni kívánt nem már használatban van. A jelen szakaszban ismertetett példa, a neve: *MyEndpoint*, és a portot *1433*.

4. A helyi ügyfél, töltse le és telepítse a legújabb [PowerShell modul](https://azure.microsoft.com/downloads/).

5. Indítsa el az Azure PowerShell.  
    Egy új PowerShell-munkamenetet nyit meg, az Azure felügyeleti modulok terhelését.

6. Futtassa az `Get-AzurePublishSettingsFile` parancsot. Ez a parancsmag egy böngészőt, és töltse le a közzétételi beállítások fájlja egy helyi könyvtárba irányítja. Rendszer felajánlhatja a bejelentkezési hitelesítő adatok Azure-előfizetése.

7. Futtassa a következő `Import-AzurePublishSettingsFile` elérési útját a letöltött közzétételi beállítások fájlja parancsot:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    A közzétételi beállítások fájlja az importálása után kezelheti az Azure-előfizetéshez a PowerShell-munkamenetben.

8. A *ILB*, statikus IP-cím. Vizsgálja meg az aktuális virtuális hálózati konfigurációt a következő parancs futtatásával:

        (Get-AzureVNetConfig).XMLConfiguration
9. Megjegyzés: a *alhálózati* nevet az alhálózatot, amely tartalmazza a virtuális gépeket üzemeltető a replikákat. Ez a név a parancsfájl a $SubnetName paraméter szerepel.

10. Megjegyzés: a *VirtualNetworkSite* nevét és a kezdő *címelőtagja* az alhálózat, amely tartalmazza az, hogy a replika virtuális gépeket. Elérhető IP-cím úgy, hogy két keresse meg a `Test-AzureStaticVNetIP` parancsot, és az megvizsgálta a *AvailableAddresses*. Például, ha a virtuális hálózat neve *MyVNet* , és a alhálózati címtartományt kezdődő *172.16.0.128*, a következő parancsot kellene listában elérhető címek:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Válassza ki a rendelkezésre álló címek egyikét, és használja azt a parancsfájlt a következő lépésben $ILBStaticIP paraméterében.

12. Másolja a következő PowerShell-parancsfájl egy szövegszerkesztőben, és állítsa be a változó értéke a környezetnek megfelelően. Egyes paraméterek alapértelmezett adtak ki.  

    Meglévő affinitáscsoportok használó központi telepítések nem adható hozzá egy Példánynak. ILB követelményeivel kapcsolatos további információkért lásd: [belső load balancer áttekintése](../../../load-balancer/load-balancer-internal-overview.md).

    Is ha a rendelkezésre állási csoport is az Azure-régiók, futtatnia kell a parancsfájl egyszer minden adatközpontban és a felhőalapú szolgáltatás, és azt az adatközpontot lévő csomópontok.

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

13. Miután beállította a változókat, másolja a parancsfájlt a szövegszerkesztőben az futtatni a PowerShell-munkamenethez. Ha a parancssor még mindig  **>>** , nyomja le az Enter újra győződjön meg arról, hogy a parancsfájl futásának indításakor.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Győződjön meg arról, hogy KB2854082 telepítve van-e, ha szükséges
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Nyissa meg a tűzfal portjait a rendelkezésre állási csoport csomópontok
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelőjének létrehozása

A rendelkezésre állási csoport figyelőjének létrehozása két lépésben. Először az ügyfél hozzáférési pont fürterőforrás létrehozása, és konfigurálja a függőségek. Ezután adja meg a fürt erőforrásait PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Az ügyfél hozzáférési pontjának létrehozása és konfigurálása a fürt függőségek
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurálja a fürt erőforrásait a PowerShellben
1. A Példánynak a korábban létrehozott ILB IP-címét kell használnia. Ez a PowerShell IP-cím beszerzéséhez használja a következő parancsfájlt:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Válassza ki a virtuális gépek egyik másolja a PowerShell-parancsfájl az operációs rendszer egy szövegszerkesztőben, és akkor a változó értéke az értékeket, amelyet korábban feljegyzett.

    A Windows Server 2012 vagy újabb használja a következő parancsfájlt:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = “<X.X.X.X>” # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    A Windows Server 2008 R2 a következő parancsprogram használata:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = “<X.X.X.X>” # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Miután beállította a változókat, nyisson meg egy rendszergazda jogú Windows PowerShell-ablakban, a parancsfájl a szövegszerkesztőben illessze be a PowerShell-munkamenetet futtatni. Ha a parancssor még mindig  **>>** , nyomja le az Enter újra annak ellenőrzése, hogy a parancsfájl futásának indításakor.

4. Ismételje meg az előző lépést az egyes virtuális gépek.  
    Ez a parancsfájl az IP-cím erőforrás a felhőalapú szolgáltatás IP-címmel konfigurálja, és paramétereinek más, mint a mintavételi portot. Az IP-cím erőforrás online állapotba kerül, ha azt is válaszol a mintavételi portot a lekérdezés a korábban létrehozott elosztott terhelésű végpont.

## <a name="bring-the-listener-online"></a>A figyelő hálózatra kapcsolása
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Követő műveletet elemek
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>A rendelkezésre állási csoport figyelőjét (belül az azonos virtuális hálózatban) tesztelése
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>További lépések
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
