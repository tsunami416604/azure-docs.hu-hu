---
title: Külső figyelő konfigurálása a rendelkezésre állási csoportokhoz
description: Ez az oktatóanyag végigvezeti az Azure-ban egy mindig rendelkezésre álláson lévő csoportfigyelő létrehozásának lépésein, amely a társított felhőszolgáltatás nyilvános virtuális IP-címe használatával külsőleg elérhető.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978180"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Külső figyelő konfigurálása az Azure SQL Server virtuális gépein rendelkezésre álló csoportokhoz
> [!div class="op_single_selector"]
> * [Belső figyelő](../classic/ps-sql-int-listener.md)
> * [Külső figyelő](../classic/ps-sql-ext-listener.md)
> 
> 

Ez a témakör bemutatja, hogyan konfigurálhatja a figyelő egy mindig rendelkezésre állási csoport, amely külsőleg elérhető az interneten. Ezt a felhőszolgáltatás **nyilvános virtuális IP-címét** a figyelővel való társításteszi lehetővé.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelő és a Klasszikus.](../../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatával ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A rendelkezésre állási csoport tartalmazhat replikákat, amelyek csak a helyszínen, csak az Azure-ban, vagy a helyszíni és az Azure hibrid konfigurációk. Az Azure-replikák ugyanabban a régióban vagy több régióban több virtuális hálózatok (virtuális hálózatok) használatával található. Az alábbi lépések feltételezik, hogy már [konfigurált egy rendelkezésre állási csoportot,](../classic/portal-sql-alwayson-availability-groups.md) de még nem konfigurált a figyelő.

## <a name="guidelines-and-limitations-for-external-listeners"></a>A külső hallgatókra vonatkozó irányelvek és korlátozások
Vegye figyelembe az alábbi irányelveket a rendelkezésre állási csoport figyelő az Azure-ban, amikor a felhőszolgáltatás nyilvános VIP-cím használatával telepíti:

* A rendelkezésre állási csoportfigyelője windows Server 2008 R2, Windows Server 2012 és Windows Server 2012 R2 rendszerben támogatott.
* Az ügyfélalkalmazásnak egy másik felhőszolgáltatáson kell lennie, mint amely a rendelkezésre állási csoport virtuális gépeit tartalmazza. Az Azure nem támogatja a közvetlen kiszolgáló-visszatérést az ügyfél és a kiszolgáló ugyanabban a felhőszolgáltatásban.
* Alapértelmezés szerint a cikkben leírt lépések bemutatják, hogyan konfigurálhat egy figyelőt a virtuális IP-cím használatára. Azonban lehetőség van több VIP-cím lefoglalására és létrehozására a felhőszolgáltatáshoz. Ez lehetővé teszi, hogy a cikkben leírt lépésekkel hozzon létre több figyelőt, amelyek mindegyike egy másik VIP-hez van társítva. Több VIP-cím létrehozásáról további információt [a Több VIP felhőalapú szolgáltatásonként](../../../load-balancer/load-balancer-multivip.md)című témakörben talál.
* Ha egy hibrid környezetfigyelőt hoz létre, a helyszíni hálózatnak az Azure virtuális hálózattal a helyek közötti VPN mellett a nyilvános internethez is rendelkeznie kell. Az Azure-alhálózatban a rendelkezésre állási csoport figyelője csak az adott felhőszolgáltatás nyilvános IP-címe érhető el.
* Nem támogatott egy külső figyelő ugyanabban a felhőszolgáltatásban, ahol is van egy belső figyelő a belső terheléselosztó (ILB) használatával.

## <a name="determine-the-accessibility-of-the-listener"></a>A figyelő hozzáférhetőségének meghatározása
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ez a cikk a **külső terheléselosztást**használó figyelő létrehozására összpontosít. Ha olyan figyelőt szeretne, amely a virtuális hálózat magánhálózata, tekintse meg a cikk azon verzióját, amely lépéseket tartalmaz a [figyelő ILB-vel történő](../classic/ps-sql-int-listener.md) beállításához

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Kiegyensúlyozott terhelésű virtuálisgép-végpontok létrehozása közvetlen kiszolgálói visszatéréssel
A külső terheléselosztás a virtuális virtuális IP-címet használja a virtuális gépeket fogadó felhőszolgáltatás nyilvános virtuális IP-címét. Így ebben az esetben nem kell létrehoznia vagy konfigurálnia a terheléselosztót.

Létre kell hoznia egy terheléselosztásos végpontot minden Azure-replikát tároló virtuális géphez. Ha több régióban rendelkezik replikákkal, az adott régió minden replikájának ugyanabban a virtuális hálózatban ugyanabban a felhőszolgáltatásban kell lennie. Több Azure-régióra kiterjedő rendelkezésre állási csoport replikák létrehozása több virtuális hálózat konfigurálását igényli. A több virtuális hálózat közötti kapcsolat konfigurálásáról a [Virtuális hálózat és a virtuális hálózat közötti kapcsolat konfigurálása című témakörben](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)talál további információt.

1. Az Azure Portalon keresse meg a replika tároló minden egyes virtuális gépre, és tekintse meg a részleteket.
2. Kattintson a **Végpontok** fülre az egyes virtuális gépek.
3. Ellenőrizze, hogy a használni kívánt figyelővégpont **neve** és **nyilvános portja** nincs-e még használatban. Az alábbi példában a név "MyEndpoint", a port pedig "1433".
4. A helyi ügyfélen töltse le és telepítse [a legújabb PowerShell-modult.](https://azure.microsoft.com/downloads/)
5. Indítsa el **az Azure PowerShellt.** Egy új PowerShell-munkamenet nyílik meg az Azure felügyeleti moduljainak betöltésével.
6. Futtassa **a Get-AzurePublishSettingsFile fájlt.** Ez a parancsmag a böngészőhöz irányítja, hogy letöltse a közzétételi beállításfájlt egy helyi könyvtárba. Előfordulhat, hogy a rendszer kéri a bejelentkezési hitelesítő adatait az Azure-előfizetéshez.
7. Futtassa az **Import-AzurePublishSettingsFile** parancsot a letöltött közzétételi beállításfájl elérési útjával:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    A közzétételi beállításfájl importálása után kezelheti az Azure-előfizetést a PowerShell-munkamenetben.
    
1. Másolja az alábbi PowerShell-parancsfájlt egy szövegszerkesztőbe, és állítsa be a változóértékeket a környezetének megfelelően (bizonyos paraméterek alapértelmezett értékei meg vannak adva). Vegye figyelembe, hogy ha a rendelkezésre állási csoport az Azure-régiókra terjed ki, a parancsfájlt egyszer kell futtatnia a felhőszolgáltatás és az adott adatközpontban található csomópontok minden egyes adatközpontjában.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Miután beállította a változókat, másolja a parancsfájlt a szövegszerkesztőből az Azure PowerShell-munkamenetbe a futtatáshoz. Ha a kérdés továbbra is >>, írja be újra az ENTER értéket, és győződjön meg arról, hogy a parancsfájl futása megkezdődik.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Szükség esetén ellenőrizze, hogy a KB2854082 új
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>A tűzfalportok megnyitása a rendelkezésre állási csoport csomópontjaiban
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelőjének létrehozása

Hozza létre a rendelkezésre állási csoport figyelő két lépésben. Először hozza létre az ügyfél-hozzáférési pont fürterőforrását, és konfigurálja a függőségeket. Másodszor konfigurálja a fürt erőforrásait a PowerShell használatával.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Az ügyfél-hozzáférési pont létrehozása és a fürtfüggőségek konfigurálása
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>A fürterőforrások konfigurálása a PowerShellben
1. A külső terheléselosztáshoz be kell szereznie a replikákat tartalmazó felhőszolgáltatás nyilvános virtuális IP-címét. Jelentkezzen be az Azure Portalra. Keresse meg a felhőalapú szolgáltatást, amely tartalmazza a rendelkezésre állási csoport virtuális gép. Nyissa meg az **Irányítópult** nézetet.
2. Vegye figyelembe a **nyilvános virtuális IP-cím (VIP) cím**alatt látható címet. Ha a megoldás virtuális hálózatokra terjed ki, ismételje meg ezt a lépést minden olyan felhőszolgáltatás esetében, amely replikát tartalmazó virtuális szolgáltatást tartalmaz.
3. Az egyik virtuális gépen másolja az alábbi PowerShell-parancsfájlt egy szövegszerkesztőbe, és állítsa be a változókat a korábban megjegyzett értékekre.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Miután beállította a változókat, nyisson meg egy emelt szintű Windows PowerShell-ablakot, majd másolja a parancsfájlt a szövegszerkesztőből, és illessze be az Azure PowerShell-munkamenetbe a futtatásához. Ha a kérdés továbbra is >>, írja be újra az ENTER értéket, és győződjön meg arról, hogy a parancsfájl futása megkezdődik.
5. Ismételje meg ezt minden virtuális gépen. Ez a parancsfájl konfigurálja az IP-cím erőforrás a felhőszolgáltatás IP-címét, és beállítja az egyéb paraméterek, például a mintavételi port. Amikor az IP-cím erőforrás online állapotba kerül, ezután válaszolhat a mintavételi porton a terheléselosztási végpontról, amelyet az oktatóanyag korábbi részében hoztak létre.

## <a name="bring-the-listener-online"></a>A hallgató online állapotba hozása
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Nyomon követési elemek
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>A rendelkezésre állási csoport figyelőjének tesztelése (ugyanazon a virtuális hálózaton belül)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>A rendelkezésre állási csoport figyelőjének tesztelése (az interneten keresztül)
Annak érdekében, hogy a figyelő a virtuális hálózaton kívülről, külső/nyilvános terheléselosztást kell használnia (ebben a témakörben ismertetett), nem pedig ILB, amely csak ugyanazon a virtuális hálózaton belül érhető el. A kapcsolati karakterláncban adja meg a felhőszolgáltatás nevét. Ha például *a mycloudservice*nevű felhőszolgáltatással rendelkező felhőszolgáltatása a következő:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Az előző példától eltérően az SQL-hitelesítést kell használni, mert a hívó nem használhatja a Windows-hitelesítést az interneten keresztül. További információ: [Mindig rendelkezésre állási csoport az Azure VM: Ügyfélkapcsolati forgatókönyvek.](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx) SQL-hitelesítés használatakor győződjön meg arról, hogy ugyanazt a bejelentkezési mindkét replikák létrehozása. A bejelentkezések rendelkezésre állási csoportokkal történő hibaelhárításáról a [Bejelentkezési csoportok leképezése vagy a tartalmazott SQL-adatbázis-felhasználó használata más replikákhoz való csatlakozáshoz és a rendelkezésre állási adatbázisokhoz való csatlakozáscímű](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)témakörben talál további információt.

Ha a Mindig bekapcsolva replikák különböző alhálózatokban találhatók, az ügyfeleknek meg kell adniuk a **MultisubnetFailover=True** értéket a kapcsolati karakterláncban. Ez azt eredményezi, hogy a különböző alhálózatok replikái párhuzamos kapcsolati kísérleteket próbálnak meg. Vegye figyelembe, hogy ez a forgatókönyv tartalmaz egy régióközi mindig a rendelkezésre állási csoport központi telepítése.

## <a name="next-steps"></a>További lépések
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

