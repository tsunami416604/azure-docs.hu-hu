---
title: Külső figyelő konfigurálása AlwaysOn rendelkezésre állási csoportok számára |} A Microsoft Docs
description: Ez az oktatóanyag végigvezeti az Azure-ban, amely a nyilvános virtuális IP-cím a kapcsolódó felhőalapú szolgáltatás segítségével külsőleg érhetőek el egy mindig rendelkezésre állási csoport figyelője létrehozásának lépésein.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 449df8e49eb63cb6e52cd4ec25dafc2bb0851347
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241770"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Always On rendelkezésre állási csoportok külső figyelő konfigurálása az Azure-ban
> [!div class="op_single_selector"]
> * [Belső figyelő](../classic/ps-sql-int-listener.md)
> * [Külső figyelő](../classic/ps-sql-ext-listener.md)
> 
> 

Ez a témakör bemutatja, hogyan konfigurálhatja egy figyelőt egy Always On rendelkezésre állási csoport kívülről elérhető az interneten. Ezt tette lehetővé a felhőszolgáltatás társításával **nyilvános virtuális IP-cím (VIP)** címet a figyelőhöz.

> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A rendelkezésre állási csoport tartalmazhat replikákat, csak, helyszíni Azure csak, vagy részben a helyszínen és az Azure hibrid konfigurációk esetében. Azure replikák ugyanabban a régióban vagy több virtuális hálózatok (Vnetek) használatával több régióban is lehetnek. Az alábbi lépések azt feltételezik, hogy már [konfigurálva a rendelkezésre állási csoport](../classic/portal-sql-alwayson-availability-groups.md) , de egy figyelő nincs konfigurálva.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Irányelvek és korlátozások külső figyelők esetében
Vegye figyelembe az alábbi irányelvek Azure rendelkezésre állási csoport figyelőjével kapcsolatos, való telepítéséhez használja a cloud service nyilvános VIP-címet:

* A rendelkezésre állási csoport figyelőjének támogatott Windows Server 2008 R2, Windows Server 2012 és Windows Server 2012 R2.
* Az ügyfélalkalmazás egy másik felhőalapú szolgáltatás, amely nem tartalmazza a rendelkezésre állási csoport virtuális gépeket kell lennie. Az Azure nem támogatja az ügyfél és a kiszolgáló közvetlen kiszolgálói válasz az ugyanazon felhőszolgáltatásban.
* Alapértelmezés szerint a cikkben ismertetett lépések szemléltetik a felhőalapú szolgáltatás virtuális IP-cím (VIP) címet használja egy figyelő konfigurálása. Azonban úgy is foglalhat le, és több VIP-címek a felhőalapú szolgáltatás létrehozása. Ez lehetővé teszi, hogy ez a cikk a lépések segítségével hozzon létre több kérésfigyelőt minden társított egy másik virtuális IP-cím. Több virtuális IP-cím létrehozásával kapcsolatos információkért lásd: [több Felhőszolgáltatásonként](../../../load-balancer/load-balancer-multivip.md).
* Egy figyelő, hibrid környezetben hoz létre, ha a helyszíni hálózat mellett az Azure virtuális hálózattal a site-to-site VPN nyilvános internetkapcsolattal kell rendelkeznie. Az Azure-alhálózaton, amely a rendelkezésre állási csoport figyelőjének csak a nyilvános IP-cím a megfelelő felhőszolgáltatás érhető el.
* Külső figyelő létrehozásához az ugyanazon a felhőszolgáltatáson, azt is, hogy a belső Load Balancer (ILB) használó belső figyelő nem támogatott.

## <a name="determine-the-accessibility-of-the-listener"></a>A figyelő a kisegítő lehetőségek meghatározása
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ez a cikk foglalkozik, amely használja egy figyelő létrehozásához **külső terheléselosztás**. Ha egy figyelő, amely a virtuális hálózat a saját, ez a cikk, amely való beállításának lépéseit ismerteti verziójának megtekintéséhez egy [figyelő az ilb-vel](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Elosztott terhelésű virtuális gépek végpontjaihoz visszatérési közvetlen kiszolgálói létrehozása
Külső terheléselosztás használ a virtuális a felhőalapú szolgáltatás, amely futtatja a virtuális gépek nyilvános virtuális IP-címét. Így nem kell létrehozni, vagy ebben az esetben a load balancer konfigurálása.

Létre kell hoznia egy elosztott terhelésű végpontot az egyes virtuális Gépekhez az Azure-replika üzemeltetéséhez. Ha replikákat több régióban, minden egyes replikának a régió ugyanazon a felhőszolgáltatáson ugyanazon virtuális hálózatban kell lennie. Több Azure-régióra kiterjedő replikák rendelkezésre állási csoport létrehozása szükséges, több virtuális hálózatok konfigurálása. Átívelő virtuális hálózatok közötti kapcsolat konfigurálásával kapcsolatos további információkért lásd: [virtuális hálózatok közötti kapcsolat konfigurálása virtuális hálózat](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Az Azure Portalon keresse meg az egyes replikát üzemeltető virtuális és a részletek megtekintéséhez.
2. Kattintson a **végpontok** lapon az egyes virtuális gépek.
3. Ellenőrizze, hogy a **neve** és **nyilvános Port** a figyelő használandó végpont már nem használja. Az alábbi példában a neve "MyEndpoint" és a port pedig a "1433".
4. A helyi ügyfél letöltése és telepítése [a legújabb PowerShell-modul](https://azure.microsoft.com/downloads/).
5. Indítsa el a **az Azure PowerShell**. Egy új PowerShell-munkamenetet nyit az Azure felügyeleti modulok terhelését.
6. Run **Get-AzurePublishSettingsFile**. Ez a parancsmag egy böngészőt, és a közzétételi beállítások fájljának letöltése egy helyi könyvtárba irányítja. Kérheti a bejelentkezési hitelesítő adatokat az Azure-előfizetés.
7. Futtassa a **Import-AzurePublishSettingsFile** parancsot a letöltött közzétételi beállítások fájljának elérési útja:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    A közzétételi beállítások fájljának importálása után kezelheti az Azure-előfizetés a PowerShell-munkamenetben.
    
1. Másolja az alábbi PowerShell-parancsprogram egy szövegszerkesztőbe, és állítsa be a változó értéke a (alapértelmezett érték adtak meg bizonyos paraméterek) környezetnek megfelelően. Vegye figyelembe, hogy ha a rendelkezésre állási csoport Azure-régióra is kiterjed, futtatnia kell a parancsfájl egyszer a valamennyi adatközpont a felhőszolgáltatás és a csomópontok található azt az adatközpontot.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Miután beállította a változókat, másolja a szkriptet a szövegszerkesztőben a futtatáshoz az Azure PowerShell-munkamenetbe. Ha a rendszer még mindig >>, írja be újra, hogy a parancsfájl futtatásának megkezdése. Adjon meg.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Győződjön meg arról, hogy KB2854082 telepítve van-e, ha szükséges
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Nyissa meg a tűzfal portjait a rendelkezésre állási csoport csomópontjain
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelőjének létrehozása

A rendelkezésre állási csoport figyelőjének létrehozása két lépésben. Először hozzon létre az ügyfél-hozzáférési pont fürterőforrás, és a függőségek konfigurálása. Másodszor konfigurálja a fürt erőforrásai a PowerShell használatával.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Az ügyfél hozzáférési pontjának létrehozása és a függőségeit konfigurálása
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurálja a fürt erőforrásai a PowerShellben
1. Külső terheléselosztás, be kell szereznie a felhőalapú szolgáltatás, amely tartalmazza a replikák nyilvános virtuális IP-cím. Jelentkezzen be az Azure Portalon. Keresse meg a felhőalapú szolgáltatás, amely tartalmazza a rendelkezésre állási csoport virtuális Gépet. Nyissa meg a **irányítópult** megtekintése.
2. Vegye figyelembe a cím alatt látható **nyilvános virtuális IP-cím (VIP)**. Ha megoldását átnyúlik a virtuális hálózatok, ismételje meg ezt a lépést minden felhőszolgáltatás, amely tartalmazza a virtuális gép, amelyen replika.
3. A virtuális gépek valamelyikére másolja az alábbi PowerShell-parancsprogram egy szövegszerkesztőbe, és állítsa be a változókat a korábban feljegyzett értékekre.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Egyszer, állított be a változókat, nyisson meg egy rendszergazda jogú Windows PowerShell-ablakban, majd másolja a szkriptet a szöveges szerkesztő és illessze be az Azure PowerShell-munkamenetet a futtatáshoz. Ha a rendszer még mindig >>, írja be újra, hogy a parancsfájl futtatásának megkezdése. Adjon meg.
5. Ismételje meg ezt az egyes virtuális Gépeken. Ez a szkript konfigurálja az IP-cím erőforrás a felhőszolgáltatás IP-címmel és egyéb paramétereinek beállítása hasonló a mintavételi portot. Az IP-cím erőforrás online állapotba kerül, ha, majd válaszolhat a lekérdezés a mintavételi portot az ebben az oktatóanyagban korábban létrehozott elosztott terhelésű végpont.

## <a name="bring-the-listener-online"></a>A figyelő online állapotba.
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Ellenőrzési elemek
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>A rendelkezésre állási csoport figyelőjét (belül az azonos virtuális hálózaton) tesztelése
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>A rendelkezésre állási csoport figyelőjének tesztelése (az interneten keresztül)
Ahhoz, hogy hozzáférhessen a figyelő a virtuális hálózaton kívülről érkező, kell használnia terheléselosztás külső/nyilvános (ebben a témakörben ismertetett) ILB, ahelyett, hogy ez az egyetlen érhető el. az azonos virtuális hálózaton belül. A kapcsolati karakterláncban adja meg a felhőszolgáltatás neve. Ha például egy felhőalapú szolgáltatás nevű kellett *mycloudservice*, az sqlcmd utasítás a következő lenne:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Ellentétben az előző példában SQL-hitelesítést kell használni, mert a hívó nem tudja kezelni a windows-hitelesítés az interneten keresztül. További információkért lásd: [Always On rendelkezésre állási csoportot az Azure virtuális Gépen: ügyfél-kapcsolódási forgatókönyvek](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). SQL-hitelesítés használata esetén győződjön meg arról, hogy mindkét replikákon ugyanazokat a bejelentkezési adatokat létrehozni. Rendelkezésre állási csoportok használatának hibaelhárítással kapcsolatos további információkért lásd: [bejelentkezések leképezése, vagy használja tartalmazott SQL adatbázis-felhasználó más replikák csatlakozhat, és a rendelkezésre állási adatbázisok leképezése](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Ha az AlwaysOn-replikákat külön alhálózatokon vannak, az ügyfelek meg kell adnia **MultisubnetFailover = True** a kapcsolati karakterláncban. Az eredmény párhuzamos kapcsolódási kísérletek a replikákat a különböző alhálózatokon. Vegye figyelembe, hogy ez az eset tartalmazza-e a központi telepítés régiók közötti Always On rendelkezésre állási csoportot.

## <a name="next-steps"></a>További lépések
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

