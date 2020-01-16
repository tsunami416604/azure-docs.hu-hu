---
title: ILB-figyelő konfigurálása a rendelkezésre állási csoportokhoz (klasszikus)
description: Ez az oktatóanyag a klasszikus üzemi modellel létrehozott erőforrásokat használja, és egy always on rendelkezésre állási csoport SQL Server VM figyelőjét hozza létre az Azure-ban, amely belső Load balancert használ.
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978175"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>ILB-figyelő konfigurálása az Azure SQL Server virtuális gépek rendelkezésre állási csoportjaihoz
> [!div class="op_single_selector"]
> * [Belső figyelő](../classic/ps-sql-int-listener.md)
> * [Külső figyelő](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Azure Resource Manager és klasszikus](../../../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. Javasoljuk, hogy az új központi telepítések a Resource Manager-modellt használják.

A Resource Manager-modellben található always on rendelkezésre állási csoport figyelőjét a következő témakörben tekintheti meg: [Load Balancer konfigurálása az Azure-beli always on rendelkezésre állási csoporthoz](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

A rendelkezésre állási csoport olyan replikákat tartalmazhat, amelyek csak a helyszíni vagy az Azure-beli, illetve a helyszíni és az Azure-beli hibrid konfigurációkra is kiterjednek. Az Azure-replikák ugyanazon a régión belül vagy több, több virtuális hálózatot használó régióban találhatók. A cikkben ismertetett eljárások feltételezik, hogy már [konfigurált egy rendelkezésre állási csoportot](../classic/portal-sql-alwayson-availability-groups.md) , de még nem konfigurálta a figyelőt.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>A belső figyelőkre vonatkozó irányelvek és korlátozások
A belső terheléselosztó (ILB) az Azure-beli rendelkezésre állási csoport figyelővel való használata az alábbi irányelvek hatálya alá esik:

* A rendelkezésre állási csoport figyelője a Windows Server 2008 R2, a Windows Server 2012 és a Windows Server 2012 R2 rendszeren támogatott.
* Minden felhőalapú szolgáltatás esetében csak egy belső rendelkezésre állási csoport figyelője támogatott, mert a figyelő a ILB van konfigurálva, és az egyes felhőalapú szolgáltatások esetében csak egy ILB van beállítva. Azonban lehetséges, hogy több külső figyelő is létrehozható. További információ: [külső figyelő konfigurálása always on rendelkezésre állási csoportokhoz az Azure-ban](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>A figyelő hozzáférhetőségének meghatározása
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ez a cikk egy ILB használó figyelő létrehozására összpontosít. Ha nyilvános vagy külső figyelőre van szüksége, tekintse meg a jelen cikk azon verzióját, amely a [külső figyelő](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)beállítását tárgyalja.

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Elosztott terhelésű virtuálisgép-végpontok létrehozása közvetlen kiszolgálói visszatéréssel
Először hozzon létre egy ILB úgy, hogy a szkript későbbi részében futtatja a parancsfájlt.

Hozzon létre egy elosztott terhelésű végpontot minden olyan virtuális géphez, amely Azure-replikát üzemeltet. Ha több régióban is vannak replikák, akkor az adott régió összes replikájának ugyanabban a felhőalapú szolgáltatásban kell lennie ugyanazon az Azure-beli virtuális hálózaton. Több Azure-régióra kiterjedő rendelkezésre állási csoport replikáinak létrehozása több virtuális hálózat konfigurálását igényli. További információ a virtuális hálózati kapcsolatok konfigurálásáról: virtuális hálózat [konfigurálása virtuális hálózati kapcsolathoz](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. A Azure Portalban nyissa meg az egyes virtuális gépeket, amelyek replikát futtatnak a részletek megtekintéséhez.

2. Kattintson az egyes virtuális gépek **végpontok** fülére.

3. Győződjön meg arról, hogy a használni kívánt figyelő-végpont **neve** és **nyilvános portja** még nincs használatban. Az ebben a szakaszban szereplő példában a név *MyEndpoint*, a port pedig *1433*.

4. A helyi ügyfélen töltse le és telepítse a legújabb [PowerShell-modult](https://azure.microsoft.com/downloads/).

5. Azure PowerShell elindítása.  
    Megnyílik egy új PowerShell-munkamenet, amelyen be van töltve az Azure felügyeleti moduljai.

6. Futtassa az `Get-AzurePublishSettingsFile` parancsot. Ez a parancsmag arra utasítja a böngészőt, hogy töltsön le egy közzétételi beállítási fájlt egy helyi könyvtárba. Előfordulhat, hogy a rendszer felszólítja az Azure-előfizetéséhez tartozó bejelentkezési hitelesítő adatok megadására.

7. Futtassa a következő `Import-AzurePublishSettingsFile` parancsot a letöltött közzétételi beállítások fájljának elérési útjával:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    A közzétételi beállítások fájljának importálása után a PowerShell-munkamenetben kezelheti az Azure-előfizetését.

8. *ILB*esetén rendeljen hozzá egy statikus IP-címet. A következő parancs futtatásával vizsgálja meg az aktuális virtuális hálózat konfigurációját:

        (Get-AzureVNetConfig).XMLConfiguration
9. Jegyezze fel a replikákat futtató virtuális gépeket tartalmazó alhálózat *alhálózatának* nevét. A rendszer ezt a nevet használja a parancsfájl $SubnetName paraméterében.

10. Jegyezze fel a *VirtualNetworkSite* nevét és a replikákat futtató virtuális gépeket tartalmazó alhálózat kezdő *AddressPrefix* . Keressen egy elérhető IP-címet úgy, hogy mindkét értéket átadja a `Test-AzureStaticVNetIP` parancsnak, és megvizsgálja a *AvailableAddresses*. Ha például a virtuális hálózat neve *MyVNet* , és rendelkezik egy alhálózati címmel, amely az *172.16.0.128*-on indul el, a következő parancs az elérhető címeket sorolja fel:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Válassza ki az egyik elérhető címet, majd használja a parancsfájl $ILBStaticIP paraméterében a következő lépésben.

12. Másolja a következő PowerShell-szkriptet egy szövegszerkesztőbe, és állítsa be a változó értékeit a környezetének megfelelően. Egyes paramétereknél az alapértelmezett beállítások lettek megadva.  

    Az affinitási csoportokat használó meglévő központi telepítések nem adhatnak hozzá ILB. A ILB követelményeivel kapcsolatos további információkért lásd: [belső Load Balancer – áttekintés](../../../load-balancer/load-balancer-internal-overview.md).

    Ha a rendelkezésre állási csoport az Azure-régiókat is felöleli, akkor a parancsfájlt egyszer kell futtatnia a Cloud Service és az adatközpontban található csomópontok minden adatközpontjában.

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

13. A változók beállítása után másolja a szkriptet a szövegszerkesztőből a PowerShell-munkamenetbe a futtatásához. Ha a kérdés továbbra is **>>** jelenik meg, nyomja le ismét az ENTER billentyűt, és győződjön meg arról, hogy a parancsfájl fut.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Szükség esetén ellenőrizze, hogy a KB2854082 telepítve van-e
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Nyissa meg a tűzfal portjait a rendelkezésre állási csoport csomópontjain
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelő létrehozása

Hozza létre a rendelkezésre állási csoport figyelőjét két lépésben. Először hozza létre az ügyfél-hozzáférési pont fürterőforrás-erőforrását, és konfigurálja a függőségeket. Másodszor konfigurálja a fürt erőforrásait a PowerShellben.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Az ügyfél-hozzáférési pont létrehozása és a fürt függőségeinek konfigurálása
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>A fürt erőforrásainak konfigurálása a PowerShellben
1. A ILB a korábban létrehozott ILB IP-címét kell használnia. Ha ezt az IP-címet a PowerShellben szeretné beszerezni, használja a következő parancsfájlt:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Az egyik virtuális gépen másolja az operációs rendszer PowerShell-parancsfájlját egy szövegszerkesztőbe, majd állítsa be a változókat a korábban feljegyzett értékekre.

    A Windows Server 2012-es vagy újabb verzióiban használja a következő parancsfájlt:

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

3. A változók beállítása után nyisson meg egy emelt szintű Windows PowerShell-ablakot, illessze be a szkriptet a szövegszerkesztőből a PowerShell-munkamenetbe a futtatásához. Ha a kérdés továbbra is **>>** jelenik meg, nyomja le ismét az ENTER billentyűt, és győződjön meg arról, hogy a parancsfájl fut.

4. Ismételje meg az előző lépéseket minden egyes virtuális géphez.  
    Ez a szkript konfigurálja az IP-cím erőforrást a Cloud Service IP-címével, és más paramétereket (például a mintavételi portot) állít be. Ha az IP-cím erőforrás online állapotba kerül, az a korábban létrehozott elosztott terhelésű végponton keresztül válaszolhat a mintavételi port lekérdezésére.

## <a name="bring-the-listener-online"></a>A figyelő online állapotba hozása
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Követő elemek
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>A rendelkezésre állási csoport figyelője (ugyanazon a virtuális hálózaton belül) tesztelése
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
