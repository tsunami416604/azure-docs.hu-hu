---
title: Külső figyelő konfigurálása a rendelkezésre állási csoportokhoz
description: Ez az oktatóanyag végigvezeti azon lépéseken, amelyekkel egy always on rendelkezésre állási csoport figyelője hozható létre az Azure-ban, amely külsőleg elérhető a társított felhőalapú szolgáltatás nyilvános virtuális IP-címének használatával.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: ca13d5e8369d007188a17352913519172ed8744e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978180"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Külső figyelő konfigurálása a rendelkezésre állási csoportokhoz az Azure SQL Server virtuális gépeken
> [!div class="op_single_selector"]
> * [Belső figyelő](../classic/ps-sql-int-listener.md)
> * [Külső figyelő](../classic/ps-sql-ext-listener.md)
> 
> 

Ebből a témakörből megtudhatja, hogyan konfigurálhat figyelőt olyan always on rendelkezésre állási csoport számára, amely kívülről elérhető az interneten. Ezt a Cloud Service **nyilvános virtuális IP-címének (VIP)** a figyelővel való társításával lehet elvégezni.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A rendelkezésre állási csoport olyan replikákat tartalmazhat, amelyek csak a helyszínen, csak az Azure-ban, illetve a helyszíni és az Azure-beli hibrid konfigurációk esetében is alkalmazhatók. Az Azure-replikák ugyanabban a régióban vagy több régióban is lehetnek, több virtuális hálózat (virtuális hálózatok) használatával. Az alábbi lépések feltételezik, hogy már [konfigurált egy rendelkezésre állási csoportot](../classic/portal-sql-alwayson-availability-groups.md) , de nem konfigurálta a figyelőt.

## <a name="guidelines-and-limitations-for-external-listeners"></a>A külső figyelőkre vonatkozó irányelvek és korlátozások
Vegye figyelembe a következő irányelveket a rendelkezésre állási csoport figyelője az Azure-ban a Cloud Service nyilvános VIP-címének telepítésekor:

* A rendelkezésre állási csoport figyelője a Windows Server 2008 R2, a Windows Server 2012 és a Windows Server 2012 R2 rendszeren támogatott.
* Az ügyfélalkalmazás a rendelkezésre állási csoportba tartozó virtuális gépeket tartalmazó másik felhőalapú szolgáltatásban kell, hogy legyen. Az Azure nem támogatja a közvetlen kiszolgálóknak az ügyfél és a kiszolgáló közötti visszaküldését ugyanabban a felhőalapú szolgáltatásban.
* Alapértelmezés szerint a cikkben ismertetett lépések bemutatják, hogyan konfigurálhatja az egyik figyelőt a Cloud Service virtuális IP-cím (VIP) használatára. Lehetősége van azonban több VIP-cím lefoglalására és létrehozására a felhőalapú szolgáltatás számára. Ez lehetővé teszi, hogy a cikkben ismertetett lépések segítségével több figyelőt hozzon létre, amelyek mindegyike egy másik VIP-címhez van társítva. Több virtuális IP-cím létrehozásával kapcsolatos információkért tekintse meg a [felhőalapú szolgáltatásokban több](../../../load-balancer/load-balancer-multivip.md)VIP-t.
* Ha hibrid környezethez hoz létre figyelőt, a helyszíni hálózatnak kapcsolódnia kell a nyilvános internethez a helyek közötti VPN és az Azure virtuális hálózat között. Az Azure-alhálózaton a rendelkezésre állási csoport figyelője csak a megfelelő felhőalapú szolgáltatás nyilvános IP-címével érhető el.
* Nem támogatott külső figyelőt létrehozni ugyanabban a felhőalapú szolgáltatásban, ahol a belső Load Balancer (ILB) belső figyelővel is rendelkezik.

## <a name="determine-the-accessibility-of-the-listener"></a>A figyelő hozzáférhetőségének meghatározása
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ez a cikk a **külső terheléselosztást**használó figyelő létrehozására összpontosít. Ha olyan figyelőt szeretne, amely magán a virtuális hálózatban található, tekintse meg a jelen cikk azon verzióját, amely a [figyelő ILB való](../classic/ps-sql-int-listener.md) beállításának lépéseit ismerteti.

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Elosztott terhelésű virtuálisgép-végpontok létrehozása közvetlen kiszolgálói visszatéréssel
A külső terheléselosztás a virtuális gépeket üzemeltető felhőalapú szolgáltatás virtuális IP-címét használja. Így ebben az esetben nem kell létrehoznia vagy konfigurálnia a terheléselosztó-t.

Minden Azure-replikát üzemeltető virtuális géphez létre kell hoznia egy elosztott terhelésű végpontot. Ha több régióban is vannak replikák, akkor az adott régió minden replikájának ugyanabban a felhőalapú szolgáltatásban kell lennie ugyanabban a VNet. A több Azure-régióra kiterjedő rendelkezésre állási csoport replikáinak létrehozásához több virtuális hálózatok-t kell konfigurálni. A VNet-kapcsolatok konfigurálásával kapcsolatos további információkért lásd: [VNet konfigurálása VNet-kapcsolathoz](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. A Azure Portal navigáljon a replikát üzemeltető virtuális gépekhez, és tekintse meg a részleteket.
2. Kattintson az egyes virtuális gépek **végpontok** fülére.
3. Győződjön meg arról, hogy a használni kívánt figyelő végpontjának **neve** és **nyilvános portja** még nincs használatban. Az alábbi példában a név "MyEndpoint", a port pedig "1433".
4. A helyi ügyfélen töltse le és telepítse [a legújabb PowerShell-modult](https://azure.microsoft.com/downloads/).
5. **Azure PowerShell**elindítása. A rendszer új PowerShell-munkamenetet nyit meg az Azure felügyeleti modulok betöltésével.
6. Futtassa a **Get-AzurePublishSettingsFile**parancsot. Ez a parancsmag arra utasítja a böngészőt, hogy töltsön le egy közzétételi beállítási fájlt egy helyi könyvtárba. Előfordulhat, hogy a rendszer felszólítja az Azure-előfizetéséhez tartozó bejelentkezési hitelesítő adatokra.
7. Futtassa az **import-AzurePublishSettingsFile** parancsot a letöltött közzétételi beállítások fájljának elérési útjával:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    A közzétételi beállítások fájljának importálása után a PowerShell-munkamenetben kezelheti az Azure-előfizetését.
    
1. Másolja az alábbi PowerShell-szkriptet egy szövegszerkesztőbe, és állítsa be a változó értékeit a környezetének megfelelően (az alapértelmezett beállítások bizonyos paraméterekhez lettek megadva). Vegye figyelembe, hogy ha a rendelkezésre állási csoport felöleli az Azure-régiókat, akkor egyszer kell futtatnia a parancsfájlt a felhőalapú szolgáltatás és az adatközpontban található csomópontok minden adatközpontjában.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. A változók beállítása után másolja a szkriptet a szövegszerkesztőből a Azure PowerShell-munkamenetbe a futtatásához. Ha a kérdés továbbra is > > jelenik meg, írja be újra az ENTER billentyűt a szkript futtatásának megkezdéséhez.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Szükség esetén ellenőrizze, hogy a KB2854082 telepítve van-e
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Nyissa meg a tűzfal portjait a rendelkezésre állási csoport csomópontjain
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelő létrehozása

Hozza létre a rendelkezésre állási csoport figyelőjét két lépésben. Először hozza létre az ügyfél-hozzáférési pont fürterőforrás-erőforrását, és konfigurálja a függőségeket. Másodszor konfigurálja a fürt erőforrásait a PowerShell-lel.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Az ügyfél-hozzáférési pont létrehozása és a fürt függőségeinek konfigurálása
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>A fürt erőforrásainak konfigurálása a PowerShellben
1. A külső terheléselosztáshoz be kell szereznie a replikákat tartalmazó felhőalapú szolgáltatás nyilvános virtuális IP-címét. Jelentkezzen be az Azure Portalra. Navigáljon ahhoz a felhőalapú szolgáltatáshoz, amely a rendelkezésre állási csoport virtuális gépet tartalmazza. Nyissa meg az **irányítópult** nézetet.
2. Jegyezze fel a **nyilvános virtuális IP-cím (VIP)** alatt látható címet. Ha a megoldás virtuális hálózatok-ra terjed ki, ismételje meg ezt a lépést minden olyan felhőalapú szolgáltatás esetében, amely egy replikát futtató virtuális gépet tartalmaz.
3. Az egyik virtuális gépen másolja az alábbi PowerShell-szkriptet egy szövegszerkesztőbe, és állítsa be a változókat a korábban feljegyzett értékekre.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Miután beállította a változókat, nyisson meg egy emelt szintű Windows PowerShell-ablakot, majd másolja a szkriptet a szövegszerkesztőből, és illessze be a Azure PowerShell-munkamenetbe a futtatásához. Ha a kérdés továbbra is > > jelenik meg, írja be újra az ENTER billentyűt a szkript futtatásának megkezdéséhez.
5. Ismételje meg ezt minden egyes virtuális gépen. Ez a parancsfájl konfigurálja az IP-cím erőforrást a Cloud Service IP-címével, és más paramétereket állít be, például a mintavételi portot. Ha az IP-cím-erőforrás online állapotba kerül, az ebben az oktatóanyagban korábban létrehozott terheléselosztási végponton keresztül válaszolhat a mintavételi port lekérdezésére.

## <a name="bring-the-listener-online"></a>A figyelő online állapotba hozása
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Követő elemek
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>A rendelkezésre állási csoport figyelője (ugyanazon a VNet belül) tesztelése
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>A rendelkezésre állási csoport figyelője (az interneten keresztül) tesztelése
Ha a figyelőt a virtuális hálózaton kívülről szeretné elérni, a ILB helyett külső/nyilvános terheléselosztást kell használnia (lásd a témakört), amely csak ugyanazon a VNet belül érhető el. A kapcsolatok karakterláncában adja meg a felhőalapú szolgáltatás nevét. Ha például a *mycloudservice*nevű Cloud Service-t használta, a Sqlcmd utasítás a következő lesz:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Az előző példától eltérően az SQL-hitelesítést kell használni, mivel a hívó nem használhatja a Windows-hitelesítést az interneten keresztül. További információ [: always on rendelkezésre állási csoport az Azure-beli virtuális gépen: ügyfél-csatlakozási forgatókönyvek](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). SQL-hitelesítés használatakor győződjön meg arról, hogy ugyanazt a bejelentkezést hozza létre mindkét replikán. A rendelkezésre állási csoportokkal való bejelentkezések hibaelhárításával kapcsolatos további információkért lásd: [bejelentkezések leképezése vagy SQL Database-felhasználó használata más replikához való kapcsolódáshoz, valamint a rendelkezésre állási adatbázisok leképezése](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Ha az Always On replikák különböző alhálózatokban találhatók, az ügyfeleknek a **MultisubnetFailover = True** értéket kell megadniuk a kapcsolatok karakterláncában. Ennek eredményeképpen a párhuzamos kapcsolódás megkísérli a különböző alhálózatokban lévő replikákat. Vegye figyelembe, hogy ez a forgatókönyv több régióra kiterjedő always on rendelkezésre állási csoport központi telepítését is magában foglalja.

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

