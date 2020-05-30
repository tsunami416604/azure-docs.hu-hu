---
title: Privát kapcsolat
description: A Private Endpoint szolgáltatás áttekintése
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.custom: sqldbrb=1
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: e1093e57757d780bf5393b6cb1bb45a706b18b11
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219882"
---
# <a name="private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Privát hivatkozás a Azure SQL Database és az Azure szinapszis Analytics szolgáltatáshoz
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

A privát hivatkozás lehetővé teszi, hogy egy **privát végponton**keresztül kapcsolódjon az Azure-beli különböző Pásti-szolgáltatásokhoz. A privát kapcsolati funkciót támogató Pásti-szolgáltatások listáját a [Private link dokumentációs oldalán találja](../../private-link/index.yml) . A privát végpont egy adott [VNet](../../virtual-network/virtual-networks-overview.md) és alhálózaton belüli magánhálózati IP-cím.

> [!IMPORTANT]
> Ez a cikk a Azure SQL Database és az Azure szinapszis Analytics (korábban SQL Data Warehouse) szolgáltatásra is vonatkozik. Az egyszerűség kedvéért az "adatbázis" kifejezés a Azure SQL Database és az Azure szinapszis Analytics mindkét adatbázisára vonatkozik. Hasonlóképpen, a "Server"re mutató hivatkozások a Azure SQL Database és az Azure szinapszis Analytics szolgáltatást futtató [logikai SQL Serverre](logical-servers.md) hivatkoznak. Ez a cikk *nem* vonatkozik az **Azure SQL felügyelt példányaira**.

## <a name="data-exfiltration-prevention"></a>Az adatkiszűrése megelőzése

A Azure SQL Databaseban lévő adatok kiszűrése, ha egy jogosult felhasználó, például egy adatbázis-rendszergazda képes az adatok kinyerésére egy rendszerből, és a szervezeten kívül más helyre vagy rendszerre helyezi át. A felhasználó például egy harmadik fél tulajdonában lévő Storage-fiókba helyezi át az adatátvitelt.

Vegyünk egy olyan forgatókönyvet, amely egy SQL Server Management Studio (SSMS) rendszert futtató felhasználó egy SQL Databasehoz csatlakozó Azure-beli virtuális gépen. Ez a SQL Database az USA nyugati régiójának adatközpontjában található. Az alábbi példa bemutatja, hogyan korlátozható a hozzáférés nyilvános végpontokkal a SQL Database hálózati hozzáférés-vezérlés használatával.

1. Tiltsa le a nyilvános végponton keresztül SQL Database összes Azure-szolgáltatás forgalmát az Azure-szolgáltatások **kikapcsolásának**engedélyezése beállítással. Győződjön meg arról, hogy a kiszolgáló és az adatbázis szintű tűzfalszabályok nem engedélyezettek az IP-címekben. További információ: [Azure SQL Database és Azure szinapszis Analytics hálózati hozzáférés-vezérlés](network-access-controls-overview.md).
1. Csak a virtuális gép magánhálózati IP-címének használatával engedélyezze a SQL Database forgalmat. További információt a [szolgáltatás-végpont](vnet-service-endpoint-rule-overview.md) és a [VNet-tűzfalszabályok](firewall-configure.md)című cikkben talál.
1. Az Azure-beli virtuális gépen a következő módon Szűkítse le a kimenő kapcsolatok hatókörét [hálózati biztonsági csoportok (NSG)](../../virtual-network/manage-network-security-group.md) és szolgáltatás-címkék használatával.
    - Olyan NSG-szabályt ad meg, amely engedélyezi a forgalmat a Service tag = SQL szolgáltatásban. WestUs – csak az USA nyugati régiójában lévő SQL Databasehoz való kapcsolódás engedélyezése
    - NSG-szabály megadása ( **magasabb prioritással**) a Service tag által megtagadott forgalom megtagadásához = SQL – az összes régióban SQL Database kapcsolatok megtagadása

A telepítés végén az Azure-beli virtuális gép csak az USA nyugati régiójában található SQL-adatbázisokhoz tud csatlakozni. Azonban a kapcsolat nem korlátozódik egyetlen SQL Databasera. A virtuális gép továbbra is csatlakozhat bármely SQL-adatbázishoz az USA nyugati régiójában, beleértve azokat az adatbázisokat is, amelyek nem részei az előfizetésnek. Noha a fenti forgatókönyvben lévő adatmennyiséget egy adott régióra csökkentették, nem kiszűrése el teljesen.

Privát hivatkozással az ügyfelek mostantól olyan hálózati hozzáférés-vezérlőket állíthatnak be, mint például a NSG a privát végponthoz való hozzáférés korlátozására. Az egyes Azure-beli Pásti-erőforrások ezután adott privát végpontokra vannak leképezve. A rosszindulatú bennfentesek csak a leképezett (például egy SQL Database) erőforráshoz férnek hozzá, és nincs más erőforrás. 

## <a name="on-premises-connectivity-over-private-peering"></a>Helyszíni kapcsolat privát társon keresztül

Amikor az ügyfelek a helyszíni gépekről csatlakoznak a nyilvános végponthoz, az IP-címüket [kiszolgálói szintű tűzfalszabály](firewall-create-server-level-portal-quickstart.md)használatával kell hozzáadni az IP-alapú tűzfalhoz. Habár ez a modell jól működik, hogy lehetővé tegye az egyes gépekhez való hozzáférést a fejlesztési vagy tesztelési feladatokhoz, nehéz felügyelni éles környezetben.

A privát kapcsolat lehetővé teszi, hogy az ügyfelek az [ExpressRoute](../../expressroute/expressroute-introduction.md), a privát vagy a VPN-alagúton keresztül engedélyezzék a helyszíni hozzáférést a privát végponthoz. Az ügyfelek ezután letilthatják az összes hozzáférést a nyilvános végponton keresztül, és nem használhatják az IP-alapú tűzfalat az IP-címek engedélyezésére.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Privát hivatkozás beállítása Azure SQL Databasehoz 

### <a name="creation-process"></a>Létrehozási folyamat
A privát végpontokat a portál, a PowerShell vagy az Azure CLI használatával lehet létrehozni:
- [Portál](../../private-link/create-private-endpoint-portal.md)
- [PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Parancssori felület](../../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Jóváhagyási folyamat
Miután a hálózati rendszergazda létrehozta a magánhálózati végpontot (PE), az SQL-rendszergazda felügyelheti a magánhálózati végponti kapcsolatokat (PEC) SQL Database.

1. Az alábbi képernyőképen megjelenő lépések szerint navigáljon az SQL Server-erőforráshoz a Azure Portal.

    - (1) válassza ki a privát végponti kapcsolatokat a bal oldali ablaktáblán
    - (2) az összes privát végponti kapcsolat (Pécs) listáját jeleníti meg
    - (3) a megfelelő privát végpont (PE) ![ az összes Pécsről készített képernyőképet][3]

1. Válasszon ki egy egyéni PEC-t a listából.
![A PEC kiválasztott képernyője][6]

1. Az SQL-rendszergazda dönthet úgy, hogy jóváhagyja vagy elutasítja a PEC-t, és opcionálisan egy rövid szöveges választ is hozzáadhat.
![A PEC jóváhagyásának képernyőképe][4]

1. Jóváhagyás vagy elutasítás után a lista a megfelelő állapotot jeleníti meg a válasz szövegével együtt.
![A jóváhagyás utáni összes Pécs képernyőképe][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Privát hivatkozás használata Azure SQL Database esetén 

Az ügyfelek csatlakozhatnak a privát végponthoz ugyanarról a VNet, egymással azonos régióban lévő VNet, illetve a régiók közötti VNet-VNet kapcsolaton keresztül. Emellett az ügyfelek a helyszíni ExpressRoute, a privát vagy a VPN-alagúton keresztül is csatlakozhatnak. Alább látható egy egyszerűsített diagram, amely a gyakori használati eseteket mutatja.

 ![Csatlakozási lehetőségek diagramja][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>SQL Database Azure-beli virtuális gépről való kapcsolat tesztelése ugyanazon a Virtual Network (VNet)

Ebben az esetben tegyük fel, hogy létrehozott egy Windows Server 2016 rendszerű Azure-beli virtuális gépet (VM). 

1. [Indítsa el a távoli asztal (RDP) munkamenetet, és kapcsolódjon a virtuális géphez](../../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Ezt követően elvégezheti az alapszintű kapcsolat ellenőrzéseit, hogy a virtuális gép a következő eszközök használatával csatlakozzon a SQL Databasehoz a privát végponton keresztül:
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Kapcsolódás ellenőrzése a Telnet használatával

A [Telnet-ügyfél](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) egy Windows-szolgáltatás, amely a kapcsolat tesztelésére használható. A Windows operációs rendszer verziójától függően előfordulhat, hogy explicit módon engedélyeznie kell ezt a szolgáltatást. 

A Telnet telepítése után nyisson meg egy parancssorablakot. Futtassa a Telnet parancsot, és adja meg a SQL Database IP-címét és magánhálózati végpontját.

```
>telnet 10.1.1.5 1433
```

Ha a Telnet sikeresen csatlakozik, egy üres képernyő jelenik meg a parancsablakban, például az alábbi képen:

 ![Telnet-diagram][2]

### <a name="check-connectivity-using-psping"></a>Kapcsolat ellenőrzése a Psping használatával

A [Psping](/sysinternals/downloads/psping) a következőképpen ellenőrizhető, hogy a magánhálózati végponti kapcsolat (PEC) figyeli-e a kapcsolatokat az 1433-as porton.

Futtassa a psping az alábbiak szerint a logikai SQL Server és a 1433-es port teljes tartománynevének megadásával:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

A kimenet azt mutatja, hogy a Psping pingelheti a PEC-hez társított magánhálózati IP-címet.

### <a name="check-connectivity-using-nmap"></a>Kapcsolat ellenőrzése az nmap használatával

Az nmap (Network Mapper) egy ingyenes és nyílt forráskódú eszköz, amely a hálózati felderítéshez és a biztonsági naplózáshoz használatos. További információért és a letöltési hivatkozásért keresse fel a következőt: https://nmap.org . Ezzel az eszközzel ellenőrizheti, hogy a magánhálózati végpont a 1433-es porton keresztül figyeli-e a kapcsolatokat.

A következőképpen futtathatja az nmap-t úgy, hogy megadja a magánhálózati végpontot futtató alhálózat címtartomány-tartományát.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Az eredmény azt mutatja, hogy egy IP-cím működik; a magánhálózati végpont IP-címének felel meg.

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Kapcsolat ellenőrzése SQL Server Management Studio használatával (SSMS)
> [!NOTE]
> A kiszolgáló **teljes tartománynevét (FQDN)** használja az ügyfelekhez tartozó kapcsolatok karakterláncában. A közvetlenül az IP-címen végrehajtott bejelentkezési kísérletek sikertelenek lesznek. Ez a viselkedés a tervezés szerint történik, mivel a magánhálózati végpont továbbítja a forgalmat a régióbeli SQL-átjáró felé, és a sikeres bejelentkezéshez meg kell adni a teljes tartománynevet.

A [SQL Databasehoz való kapcsolódáshoz](connect-query-ssms.md)kövesse az alábbi lépéseket a SSMS használatához. Miután a SSMS használatával kapcsolódott a SQL Databasehoz, ellenőrizze, hogy az Azure-beli virtuális gép magánhálózati IP-címéről csatlakozik a következő lekérdezés futtatásával:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>Korlátozások 
A magánhálózati végponttal létesített kapcsolatok csak a **proxyt** támogatják a [kapcsolati házirendként](connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Csatlakozás egy Azure-beli virtuális gépről a Virtual Network (VNet) 

Konfigurálja a [VNet](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) -társítást úgy, hogy az Azure-beli virtuális gép SQL Database kapcsolatot hozzon létre egy egyenrangú VNet.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Csatlakozás egy Azure-beli virtuális gépről a VNet-VNet-környezetben

A [VNet-to-VNET VPN Gateway-kapcsolat](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) konfigurálása egy másik régióban vagy előfizetésben lévő Azure-beli virtuális gépről létesített SQL Database kapcsolat létesítéséhez.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Csatlakozás helyszíni környezetből VPN-kapcsolaton keresztül

A helyszíni környezet és a SQL Database közötti kapcsolat létesítéséhez válassza ki és implementálja a következő lehetőségek egyikét:
- [Pont – hely kapcsolat](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Helyek közötti VPN-kapcsolat](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute áramkör](../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase"></a>Csatlakozás az Azure-beli szinapszis Analytics-ből az Azure Storage-ba a Base használatával

A rendszer általában az Azure-beli Storage-fiókokból származó adatok Azure szinapszis Analyticsbe való betöltésére használatos. Ha az Azure Storage-fiók, amelyből az adatok betöltése csak a VNet-alhálózatok számára történik, csak privát végpontokon, szolgáltatási végpontokon vagy IP-alapú tűzfalakon keresztül fér hozzá Az [itt](vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)ismertetett lépéseket követve engedélyezheti a többek között a VNet védett Azure Storage-hoz kapcsolódó, az Azure-beli importálási és exportálási forgatókönyveket. 

## <a name="next-steps"></a>További lépések

- A Azure SQL Database biztonság áttekintését lásd: [az adatbázis biztonságossá tétele](security-overview.md)
- Az Azure SQL Database kapcsolatok áttekintését lásd: [Azure SQL connectivity Architecture](connectivity-architecture.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/pe-connect-overview.png
[2]: media/quickstart-create-single-database/telnet-result.png
[3]: media/quickstart-create-single-database/pec-list-before.png
[4]: media/quickstart-create-single-database/pec-approve.png
[5]: media/quickstart-create-single-database/pec-list-after.png
[6]: media/quickstart-create-single-database/pec-select.png
