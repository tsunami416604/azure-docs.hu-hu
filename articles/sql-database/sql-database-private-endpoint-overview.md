---
title: Privát kapcsolat
description: A privát végpont szolgáltatás áttekintése
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: ab9c5c5c1134d2e09a790a788a3b7e55f807dd9b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78945368"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse"></a>Privát hivatkozás az Azure SQL Adatbázishoz és adattárházhoz

A Private Link lehetővé teszi, hogy egy **privát végponton**keresztül csatlakozzon különböző PaaS-szolgáltatásokhoz az Azure-ban. A Privát hivatkozás funkciót támogató PaaS-szolgáltatások listáját a [Privát hivatkozás dokumentációja](../private-link/index.yml) lapon találja. A privát végpont egy privát IP-cím egy adott [virtuális hálózaton](../virtual-network/virtual-networks-overview.md) és alhálózaton belül. 

> [!IMPORTANT]
> Ez a cikk az Azure SQL-kiszolgálóra, valamint az Azure SQL-kiszolgálón létrehozott SQL Database és SQL Data Warehouse adatbázisokra is vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database. Ez a cikk *nem* vonatkozik az Azure SQL Database **felügyelt példányüzembe** helyezésére.

## <a name="data-exfiltration-prevention"></a>Adatszivárgás megelőzése

Az Azure SQL Database-ben az adatok kiszivárgása az, amikor egy jogosult felhasználó, például egy adatbázis-rendszergazda képes adatokat kinyerni az egyik rendszerből, és áthelyezni egy másik helyet vagy rendszert a szervezeten kívül. Például a felhasználó áthelyezi az adatokat egy harmadik fél tulajdonában lévő tárfiókba.

Fontolja meg egy olyan forgatókönyv, amikor egy felhasználó sql server management studio (SSMS) fut egy Azure virtuális gép egy SQL-adatbázishoz csatlakozó. Ez az SQL-adatbázis az USA nyugati részén található adatközpontban található. Az alábbi példa bemutatja, hogyan korlátozhatja a hozzáférést a nyilvános végpontok az SQL Database hálózati hozzáférés-vezérlés használatával.

1. Tiltsa le az SQL Database összes Azure-szolgáltatásforgalmát a nyilvános végponton keresztül az Azure Services engedélyezése **ki állásra**beállításával. Győződjön meg arról, hogy a kiszolgálói és adatbázisszintű tűzfalszabályok nem engedélyezettek IP-címek. További információ: [Azure SQL Database and Data Warehouse network access controls.](sql-database-networkaccess-overview.md)
1. Csak a virtuális gép privát IP-címét használó sql-adatbázis forgalmat engedélyezze. További információt a [Szolgáltatásvégpont](sql-database-vnet-service-endpoint-rule-overview.md) és a [virtuális hálózat tűzfalszabályairól](sql-database-firewall-configure.md)szóló cikkektartalmaznak.
1. Az Azure virtuális gép, szűkítse le a kimenő kapcsolat hatókörét a [hálózati biztonsági csoportok (NSG-k)](../virtual-network/manage-network-security-group.md) és a szolgáltatáscímkék az alábbiak szerint
    - Adjon meg egy NSG-szabályt a Service Tag = SQL forgalom engedélyezéséhez. WestUs - csak az SQL Database-hez való csatlakozás engedélyezése az USA nyugati részén
    - NSG-szabály megadása **(magasabb prioritással)** a Service Tag = SQL forgalom megtagadásához - az SQL-adatbázishoz való kapcsolatok megtagadása minden régióban

A telepítés végén az Azure virtuális gép csak az USA nyugati régiójában lévő SQL-adatbázisokhoz csatlakozhat. A kapcsolat azonban nem korlátozódik egyetlen SQL-adatbázisra. A virtuális gép továbbra is csatlakozhat az USA nyugati régiójában lévő bármely SQL-adatbázishoz, beleértve azokat az adatbázisokat is, amelyek nem részei az előfizetésnek. Bár a fenti forgatókönyvben az adatok kiszivárgásának hatókörét egy adott régióra csökkentettük, azt nem szüntettük meg teljesen.

A Private Link segítségével az ügyfelek mostantól beállíthatnak hálózati hozzáférés-vezérléseket, például az NSG-ket, hogy korlátozzák a privát végponthoz való hozzáférést. Az egyes Azure PaaS-erőforrások ezután adott privát végpontokhoz vannak rendelve. Egy rosszindulatú bennfentes csak a leképezett PaaS-erőforráshoz (például egy SQL-adatbázishoz) férhet hozzá, más erőforráshoz nem. 

## <a name="on-premises-connectivity-over-private-peering"></a>Helyszíni kapcsolat a privát társviszony-létesítésen

Amikor az ügyfelek helyszíni gépekről csatlakoznak a nyilvános végponthoz, ip-címüket hozzá kell adni az IP-szintű tűzfalhoz egy [kiszolgálószintű tűzfalszabály](sql-database-server-level-firewall-rule.md)használatával. Bár ez a modell jól működik, amely lehetővé teszi az egyes gépek hez való hozzáférést a fejlesztési vagy tesztelési számítási feladatokhoz, éles környezetben nehéz kezelni.

A Private Link használatával az ügyfelek engedélyezhetik a magánvégponthoz való hozzáférést az [ExpressRoute,](../expressroute/expressroute-introduction.md)a privát társviszony-létesítés vagy a VPN-bújtatás használatával. Az ügyfelek ezután letilthatják az összes hozzáférést a nyilvános végponton keresztül, és nem használhatják az IP-alapú tűzfalat az IP-címek engedélyezéséhez.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Privát kapcsolat beállítása az Azure SQL Database-hez 

### <a name="creation-process"></a>Létrehozási folyamat
Privát végpontok hozhatók létre a portál, a PowerShell vagy az Azure CLI használatával:
- [Portál](../private-link/create-private-endpoint-portal.md)
- [Powershell](../private-link/create-private-endpoint-powershell.md)
- [parancssori felület](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Jóváhagyási folyamat
Miután a hálózati rendszergazda létrehozza a magánvégpontot (PE), az SQL-rendszergazda kezelheti a privát végpontkapcsolat (PEC) és az SQL-adatbázis.

1. Keresse meg az SQL-kiszolgáló erőforrást az Azure Portalon az alábbi képernyőképen látható lépések nek köszönhetően

    - (1) Válassza ki a privát végpontkapcsolatokat a bal oldali ablaktáblában
    - (2) Az összes privát végpontkapcsolat (PEC) listáját jeleníti meg
    - (3) Megfelelő Private Endpoint ![(PE) létrehozott Screenshot az összes PEC][3]

1. Válasszon ki egy egyedi PEC-et a listából annak kiválasztásával.
![Képernyőkép a PEC-en][6]

1. Az SQL-rendszergazda jóváhagyhatja vagy elutasíthatja a PEC-et, és szükség esetén rövid szöveges választ adhat hozzá.
![Képernyőkép a PEC jóváhagyásáról][4]

1. A jóváhagyást vagy elutasítást követően a lista a válaszszöveggel együtt a megfelelő állapotot fogja tükrözni.
![Képernyőkép az összes PEC-ről a jóváhagyás után][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Privát hivatkozás az Azure SQL-adatbázishoz 

Az ügyfelek csatlakozhatnak a privát végponthoz ugyanarról a virtuális hálózatról, társviszony-létesített virtuális hálózat ugyanabban a régióban, vagy a virtuális hálózat és a virtuális hálózat közötti kapcsolat on régiók között. Emellett az ügyfelek a helyszíni kapcsolatfelvétel expressroute, privát társviszony-létesítés vagy VPN-bújtatás használatával csatlakozhatnak. Az alábbiakban egy egyszerűsített diagram mutatja be a gyakori használati eseteket.

 ![Kapcsolódási lehetőségek diagramja][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Az SQL-adatbázissal való kapcsolat tesztelése egy Azure virtuális gépről ugyanabban a virtuális hálózatban (VNet)

Ebben a forgatókönyvben tegyük fel, hogy létrehozott egy Windows Server 2016 rendszerű Azure Virtuális gépet (VM). 

1. [Indítsa el a Távoli asztal (RDP) munkamenetét, és csatlakozzon a virtuális géphez.](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) 
1. Ezután néhány alapvető kapcsolatellenőrzést végezhet annak érdekében, hogy a virtuális gép a következő eszközökkel csatlakozzon az SQL Database-hez a privát végponton keresztül:
    1. Telnet
    1. Psping között
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>A kapcsolat ellenőrzése a Telnet használatával

[A Telnet-ügyfél](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) egy Windows-szolgáltatás, amely a kapcsolat tesztelésére használható. A Windows operációs rendszer verziójától függően előfordulhat, hogy ezt a szolgáltatást kifejezetten engedélyeznie kell. 

A Telnet telepítése után nyisson meg egy parancssori ablakot. Futtassa a Telnet parancsot, és adja meg az SQL-adatbázis IP-címét és magánhálózati végpontját.

```
>telnet 10.1.1.5 1433
```

Amikor a Telnet sikeresen csatlakozik, egy üres képernyő jelenik meg a parancsablakban, mint az alábbi képen:

 ![Telnet diagram][2]

### <a name="check-connectivity-using-psping"></a>A kapcsolat ellenőrzése a Psping használatával

[A psping](/sysinternals/downloads/psping) az alábbiak szerint használható annak ellenőrzésére, hogy a Privát végpontkapcsolat (PEC) figyeli-e az 1433-as porton lévő kapcsolatokat.

Futtassa a psping-et az alábbiak szerint az SQL Database-kiszolgáló és az 1433-as port teljes tartományn-tartományának megadásával:

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

A kimenet azt mutatja, hogy psping ping el tudta pingelni a PEC-hez társított privát IP-címet.

### <a name="check-connectivity-using-nmap"></a>A kapcsolat ellenőrzése az Nmap használatával

Az Nmap (Network Mapper) egy ingyenes és nyílt forráskódú eszköz, amelyet a hálózat felderítésére és a biztonsági naplózásra használnak. További információért és a https://nmap.orgletöltési linkért látogasson el a alkalmazásba. Ezzel az eszközzel biztosíthatja, hogy a privát végpont figyelje az 1433-as porton lévő kapcsolatokat.

Futtassa az Nmap-ot az alábbiak szerint a magánhálózati végpontot tartalmazó alhálózat címtartományának megadásával.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Az eredmény azt mutatja, hogy egy IP-cím van fel; amely megfelel a privát végpont IP-címének.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>A kapcsolat ellenőrzése az SQL Server Management Studio (SSMS) használatával
> [!NOTE]
> Használja a kiszolgáló **teljes minősített tartománynevét (FQDN)** az ügyfelek kapcsolati karakterláncaiban. A közvetlenül az IP-címre tett bejelentkezési kísérletek sikertelenek lesznek. Ez a viselkedés szándékosan, mivel a privát végpont útvonalak forgalmat az SQL Gateway a régióban, és a teljes tartományn a bejelentkezések sikeres elérése érdekében meg kell adni.

Az itt leírt lépéseket követve az SSMS segítségével [csatlakozzon az SQL-adatbázishoz](sql-database-connect-query-ssms.md). Miután az SSMS használatával csatlakozott az SQL-adatbázishoz, ellenőrizze, hogy az Azure virtuális gép privát IP-címéről csatlakozik-e a következő lekérdezés futtatásával:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>Korlátozások 
A privát végponthoz való kapcsolatok csak a **proxyt** támogatják [kapcsolatházirendként](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Csatlakozás egy Azure virtuális gépről társviszonyban lévő virtuális hálózatban (VNet) 

Konfigurálja [a virtuális hálózat társviszony-létesítési](../virtual-network/tutorial-connect-virtual-networks-powershell.md) kapcsolatot az SQL-adatbázishoz egy Azure virtuális gép egy társviszony-létesített virtuális hálózat.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Csatlakozás egy Azure-beli virtuális gépről virtuális hálózat és virtuális hálózat környezetben

Konfigurálja [a Virtuálishálózat és a Virtuális hálózat VPN-átjárókapcsolatát,](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) hogy kapcsolatot létesítsen egy SQL-adatbázissal egy másik régióban vagy előfizetésben lévő Azure virtuális gépről.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Csatlakozás helyszíni környezetből VPN-en keresztül

A helyszíni környezetés az SQL-adatbázis közötti kapcsolat létrehozásához válasszon és valósítson meg egy lehetőséget:
- [Pont-hely kapcsolat](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Helyek közötti VPN-kapcsolat](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute-kapcsolat](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>Csatlakozás Azure SQL Data Warehouse-ról az Azure Storage-ba a Polybase használatával

A PolyBase gyakran használható adatok azure Storage-fiókokból az Azure SQL Data Warehouse-ba történő betöltésére. Ha az Azure Storage-fiók, amely az adatok betöltése korlátozza a hozzáférést csak egy sor virtuális hálózati alhálózatok magánvégpontokon, szolgáltatásvégpontokon vagy IP-alapú tűzfalakon keresztül, a PolyBase és a fiók közötti kapcsolat megszakad. Ha engedélyezni szeretné a PolyBase importálási és exportálási forgatókönyveit az Azure SQL Data Warehouse virtuális hálózathoz való csatlakoztatásával, kövesse az [itt](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)megadott lépéseket. 



## <a name="next-steps"></a>További lépések

- Az Azure SQL Database biztonságának áttekintését az Adatbázis védelme című témakörben [találja.](sql-database-security-overview.md)
- Az Azure SQL Database-kapcsolat áttekintését az [Azure SQL Connectivity Architecture című témakörben találja.](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
