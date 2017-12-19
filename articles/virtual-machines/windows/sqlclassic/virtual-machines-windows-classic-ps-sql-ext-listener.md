---
title: "Always On rendelkezésre állási csoportok konfigurálása egy külső figyelő |} Microsoft Docs"
description: "Az oktatóanyag bemutatja, hogyan hozzon létre egy mindig a rendelkezésre állási csoport figyelőjével, amely kívülről elérhető a nyilvános virtuális IP-cím a hozzárendelt felhő szolgáltatás használatával az Azure-ban."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 8e506be42aea4fb3c48c29b771a78dcf694f4518
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Egy külső figyelőt a Always On rendelkezésre állási csoportok konfigurálása az Azure-ban
> [!div class="op_single_selector"]
> * [Belső figyelő](../classic/ps-sql-int-listener.md)
> * [Külső figyelő](../classic/ps-sql-ext-listener.md)
> 
> 

Ez a témakör bemutatja, hogyan konfigurálhatja egy figyelőt a következő mindig a rendelkezésre állási csoport kívülről elérhető az interneten. Ez tette lehetővé a felhőszolgáltatás közötti társítás **nyilvános virtuális IP-cím (VIP)** a figyelő címmel.

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A rendelkezésre állási csoport tartalmazhatnak replikákat, csak, a helyszíni Azure csak, vagy a helyszíni és az Azure span hibrid konfigurációk. Az Azure replikák a régión belül, vagy több virtuális hálózatokról (Vnetekről) segítségével különféle régiókban találhatók. Az alábbi lépések azt feltételezik, hogy már rendelkezik [konfigurált rendelkezésre állási csoport](../classic/portal-sql-alwayson-availability-groups.md) , de nincs beállítva a figyelő.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Tudnivalók és korlátozások külső figyelők
Vegye figyelembe a következő irányelveket kapcsolatos az elérhetőségi csoport figyelője az Azure-ban, való telepítéséhez használja a cloud service összeköttetés címe:

* A rendelkezésre állási csoport figyelőjének Windows Server 2008 R2, Windows Server 2012 és Windows Server 2012 R2 esetén támogatott.
* Az ügyfélalkalmazás kell lennie, amely tartalmazza a rendelkezésre állási csoport virtuális gépeket egy másik felhőalapú szolgáltatást. Azure nem támogatja a közvetlen kiszolgálói válasz ügyfél és kiszolgáló ugyanazon a felhőszolgáltatásban található.
* Alapértelmezés szerint ez a cikk lépéseit megjelenítése konfigurálása egy figyelőt, hogy a felhő virtuális IP-cím (VIP) címét használja. Azonban úgy is lefoglalni, és hozzon létre több virtuális IP-címek a felhőalapú szolgáltatáshoz. Ez lehetővé teszi, hogy ez a cikk a lépéseket követve hozzon létre minden egyes tartozó másik virtuális IP-címhez több figyelők. Több virtuális IP-cím létrehozásával kapcsolatos további információkért lásd: [több virtuális IP-címek egy felhőalapú szolgáltatás](../../../load-balancer/load-balancer-multivip.md).
* Egy figyelő hibrid környezet létrehozásakor, a helyszíni hálózat és a nyilvános internethez mellett a pont-pont VPN az Azure virtuális hálózathoz kell rendelkeznie. Az Azure-alhálózaton, amely a rendelkezésre állási csoport figyelőjének érhető csak a megfelelő felhőre szolgáltatás a nyilvános IP-cím alapján.
* Hozzon létre egy külső figyelőt az azonos felhőszolgáltatásban is esetében egy belső figyelő, a belső Load Balancer (ILB) használata nem támogatott.

## <a name="determine-the-accessibility-of-the-listener"></a>A figyelő a kisegítő lehetőségek meghatározása
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ez a cikk foglalkozik használó figyelő létrehozásával **külső terheléselosztás**. Ha egy figyelő, amely a virtuális hálózat a saját, lásd: Ez a cikk beállításának lépéseit verziója egy [ILB rendelkező figyelő](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Hozzon létre virtuális gép elosztott terhelésű végpontok közvetlen kiszolgálói visszatérési
Külső terheléselosztás használ a virtuális a nyilvános virtuális IP-cím a felhőalapú szolgáltatás, amely a virtuális gépet futtat. Ezért nem kívánja létrehozása és konfigurálása ebben az esetben a terheléselosztót.

Az egyes virtuális gépek Azure replikájának üzemeltető, létre kell hoznia egy elosztott terhelésű végpont. Ha több régióba replikákat, az adott régió minden egyes replikának a azonos felhőalapú szolgáltatás ugyanazt a virtuális hálózatot kell lennie. Több Azure-régiók kiterjedő replikák rendelkezésre állási csoport létrehozása több Vnetek beállítását igényli. Virtuális hálózat közötti kapcsolat konfigurálásával kapcsolatos további információkért lásd: [virtuális hálózaton konfigurálja a VNet-kapcsolatot](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Az Azure portálon keresse meg az egyes replikát üzemeltető virtuális, és a részletek megtekintéséhez.
2. Kattintson a **végpontok** lapon minden egyes virtuális gépek.
3. Ellenőrizze, hogy a **neve** és **nyilvános Port** a figyelő használni kívánt végpont már nem használja. Az alábbi példában a neve "MyEndpoint", a port pedig a "1433".
4. A helyi ügyfélen, töltse le és telepítse [a legújabb PowerShell-modul](https://azure.microsoft.com/downloads/).
5. Indítsa el **Azure PowerShell**. Új PowerShell-munkamenetben, ahol az Azure felügyeleti modulok terhelését.
6. Futtatás **Get-AzurePublishSettingsFile**. Ez a parancsmag egy böngészőt, és töltse le a közzétételi beállítások fájlja egy helyi könyvtárba irányítja. Kérheti a bejelentkezési hitelesítő adatok Azure-előfizetése.
7. Futtassa a **Import-AzurePublishSettingsFile** elérési útját a letöltött közzétételi beállítások fájlja parancsot:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    A közzétételi beállítások fájlja importálása után kezelheti az Azure-előfizetéshez a PowerShell-munkamenetben.
    
1. Másolja az alábbi PowerShell-parancsprogramot egy szövegszerkesztőbe, és állítsa be a változó értéke (alapértelmezett vannak-e megadva az egyes paraméterek) a környezetnek megfelelően. Vegye figyelembe, hogy a rendelkezésre állási csoport Azure-régiók is, ha futtatnia kell a parancsfájl egyszer minden adatközpontban és a felhőszolgáltatás és a csomópontok, hogy az adatközpontban található.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Már megadta a változókat, másolja a parancsfájl futtatásához az Azure PowerShell-munkamenetbe szövegszerkesztőben. Ha a parancssor még mindig >>, írja be újra, és győződjön meg arról, hogy a parancsfájl futásának indításakor. Adjon meg.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Győződjön meg arról, hogy KB2854082 telepítve van-e, ha szükséges
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Nyissa meg a tűzfal portjait a rendelkezésre állási csoport csomópontok
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelőjének létrehozása

A rendelkezésre állási csoport figyelőjének létrehozása két lépésben. Először az ügyfél hozzáférési pont fürterőforrás létrehozása, és konfigurálja a függőségek. Második konfigurálja a fürt erőforrásait a PowerShell használatával.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Az ügyfél hozzáférési pontjának létrehozása és konfigurálása a fürt függőségek
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurálja a fürt erőforrásait a PowerShellben
1. A külső terheléselosztást, be kell szereznie a replikát tartalmazó felhőalapú szolgáltatás nyilvános virtuális IP-cím. Jelentkezzen be az Azure portálon. Nyissa meg a felhőalapú szolgáltatás, amely tartalmazza a rendelkezésre állási csoport virtuális gép. Nyissa meg a **irányítópult** nézet.
2. Jegyezze fel a cím mezőben látható **nyilvános virtuális IP-cím (VIP)**. A megoldás Vnetek is, ha minden felhőalapú szolgáltatás, amely egy replikát tartalmazó ismételje meg ezt a lépést.
3. Válassza ki a virtuális gépek egyik másolja az alábbi PowerShell-parancsprogramot egy szövegszerkesztőbe, és állítsa be a változók a értékekre, amelyet korábban feljegyzett.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Egyszer, a változók van beállítva, nyisson meg egy rendszergazda jogú Windows PowerShell-ablakban, majd másolja a parancsfájlt a szövegszerkesztőben és illessze be az Azure PowerShell-munkamenetet futtatni. Ha a parancssor még mindig >>, írja be újra, és győződjön meg arról, hogy a parancsfájl futásának indításakor. Adjon meg.
5. Ismételje meg ezt az összes virtuális Géphez. Ez a parancsfájl az IP-cím erőforrás a felhőalapú szolgáltatás IP-címmel konfigurálja, és azokat más paramétereket állítja be, mint a mintavételi portot. Az IP-cím erőforrás online állapotba kerül, ha azt is majd válaszol a lekérdezés a mintavételi portot ebben az oktatóanyagban korábban létrehozott elosztott terhelésű végpont.

## <a name="bring-the-listener-online"></a>A figyelő hálózatra kapcsolása
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Követő műveletet elemek
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>A rendelkezésre állási csoport figyelőjét (belül ugyanazt a virtuális hálózatot) tesztelése
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Tesztelje a rendelkezésre állási csoport figyelőjét (interneten keresztül)
A figyelő a virtuális hálózaton kívülről való eléréséhez kell használnia terheléselosztás külső/nyilvános (a jelen témakörben ismertetett) ILB, ahelyett, hogy ez az egyetlen elérhető belül ugyanazt a virtuális hálózatot. A kapcsolati karakterláncban adja meg a felhőszolgáltatás neve. Például, ha egy felhőalapú szolgáltatás nevű *mycloudservice*, az sqlcmd utasítás a következőképpen nézne ki:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Az előző példában eltérően SQL-hitelesítést kell használni, mert a hívó nem windows-hitelesítést használ az interneten keresztül. További információkért lásd: [Always On rendelkezésre állási csoportnak Azure virtuális gépen: ügyfél-csatlakozási forgatókönyvek](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Amikor az SQL-hitelesítést használ, győződjön meg arról, hogy hozzon létre ugyanazon bejelentkezési mindkét replikákon. Rendelkezésre állási csoportokkal bejelentkezések hibaelhárítással kapcsolatos további információkért lásd: [bejelentkezések leképezéséhez vagy használatához található SQL-adatbázis felhasználó más replikák csatlakozni és hozzárendelését az egyes rendelkezésre állási adatbázisok](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Ha az Always On replikák külön alhálózatokon vannak, ügyfeleknek meg kell adniuk **MultisubnetFailover = True** a kapcsolati karakterláncban. Az eredmény párhuzamos próbálnak meg kapcsolódni a különböző alhálózatokon replikához. Vegye figyelembe, hogy ez az eset tartalmazza-e a központi telepítés kereszt-régió Always On rendelkezésre állási csoportnak.

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

