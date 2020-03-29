---
title: Hálózati hozzáférés-vezérlés
description: Az Azure SQL Database és data warehouse hálózati hozzáférés-vezérléseinek áttekintése a hozzáférés kezeléséhez és egy vagy készletbe adott adatbázis konfigurálásához.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 822fab5c00501d415c3c184587141e869523e417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945383"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Az Azure SQL Database és a Data Warehouse hálózati hozzáférés-vezérlései

> [!NOTE]
> Ez a cikk az Azure SQL-kiszolgálóra, valamint az Azure SQL-kiszolgálón létrehozott SQL Database és SQL Data Warehouse adatbázisokra is vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik az **Azure SQL Database felügyelt példányára.** A hálózati konfigurációról további információt a [Felügyelt példányhoz való csatlakozás](sql-database-managed-instance-connect-app.md) című témakörben talál.

Amikor új Azure SQL Servert hoz létre az [Azure Portalról,](sql-database-single-database-get-started.md)az eredmény egy nyilvános végpont a *yourservername.database.windows.net*formátumban.

A következő hálózati hozzáférés-vezérlők segítségével szelektíven engedélyezheti a hozzáférést az SQl-adatbázishoz a nyilvános végponton keresztül:
- Az Azure Services engedélyezése: Ha be van állítva, az Azure-határon belüli egyéb erőforrások, például egy Azure virtuális gép hozzáférhetnek az SQL Database-hez

- IP-tűzfalszabályok: Ezzel a funkcióval explicit módon engedélyezheti a kapcsolatokat egy adott IP-címről, például helyszíni gépekről

Az SQL-adatbázishoz virtuális [hálózatokról](../virtual-network/virtual-networks-overview.md) is engedélyezhet privát hozzáférést a következő eszközökön keresztül:
- Virtuális hálózati tűzfalszabályok: Ezzel a funkcióval engedélyezheti egy adott virtuális hálózatból érkező forgalmat az Azure-határon belül

- Privát hivatkozás: Ezzel a funkcióval privát végpontot hozhat létre az Azure SQL Server számára egy adott virtuális hálózaton belül



Az alábbi videóban a hozzáférés-vezérlés és ezek tennivalóinak magas szintű magyarázatát talál:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>Azure-szolgáltatások engedélyezése 
Az [Azure Portalúj](sql-database-single-database-get-started.md)Azure SQL Server létrehozása során ez a beállítás nincs bejelölve.



Ezt a beállítást az Azure SQL Server létrehozása után is módosíthatja a tűzfal ablaktábláján keresztül.
  
 ![Képernyőkép a kiszolgáló tűzfalának kezeléséről][2]

Ha **az ON-ra** van állítva, az Azure SQL Server lehetővé teszi az Azure-határon belüli összes erőforrásból származó kommunikációt, amely lehet, hogy nem része az előfizetésnek.

Sok esetben az **ON** beállítás megengedőbb, mint amit a legtöbb ügyfél szeretne. Előfordulhat, hogy ezt a beállítást **KI-ra** szeretnék állítani, és szigorúbb IP-tűzfalszabályokkal vagy virtuális hálózati tűzfalszabályokkal szeretnék helyettesíteni. Ezzel hatással van a következő funkciók, amelyek futnak a virtuális gépek az Azure-ban, hogy nem része a virtuális hálózat, és így csatlakozik az Sql Database egy Azure-BELI IP-cím.

### <a name="import-export-service"></a>Exportálási szolgáltatás importálása
Az Import Export Szolgáltatás nem **működik: Az Azure-szolgáltatások hozzáférhetnek** a kiszolgáló ki-ki beállításához. Azonban megoldhatja a problémát [manuálisan futtatva az sqlpackage.exe-t egy Azure-beli virtuális gépről, vagy az exportálást](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) közvetlenül a kódban a DACFx API használatával.

### <a name="data-sync"></a>Adatszinkronizálás
Az Adatok szinkronizálása szolgáltatás használatához az **Azure-szolgáltatások elérhetőkiszolgáló-beállítással** a kiszolgáló off- beállításával létre kell hoznia az egyes tűzfalszabály-bejegyzéseket, hogy [ip-címeket adjon hozzá](sql-database-server-level-firewall-rule.md) a **Hub-adatbázist** üzemeltető régió SQL **szolgáltatáscímkéjéből.**
Adja hozzá ezeket a kiszolgálószintű tűzfalszabályokat a **hub-** és **tagadatbázisokat** egyaránt tároló logikai kiszolgálókhoz (amelyek különböző régiókban lehetnek)

A következő PowerShell-parancsfájl használatával hozza létre az SQL szolgáltatáscímkének megfelelő IP-címeket az USA nyugati régiójához
```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> A Get-AzNetworkServiceTag az Sql Service Tag globális tartományát adja vissza, annak ellenére, hogy megadta a Hely paramétert. Ügyeljen arra, hogy a szinkronizálási csoport által használt Hub-adatbázist tartalmazó régióra szűrje.

Vegye figyelembe, hogy a PowerShell-parancsfájl kimenete osztály nélküli TARTOMÁNYOK közötti útválasztás (CIDR) jelölésben van, és ezt a Get-IPrangeStartEnd.ps1 használatával a Kezdő és záró IP-cím formátumára kell konvertálni a [Get-IPrangeStartEnd.ps1 használatával,](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) mint ez
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Az alábbi további lépések végrehajtásával az összes IP-címet CIDR-ből Kezdő és Záró IP-címformátumba konvertálhatja.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
Most már hozzáadhatja ezeket külön tűzfalszabályokként, majd **beállíthatja, hogy az Azure-szolgáltatások a kiszolgálót a kiszolgálót KI-ra tegyék.**


## <a name="ip-firewall-rules"></a>IP tűzfalszabályai
Az IP-alapú tűzfal az Azure SQL Server egyik szolgáltatása, amely megakadályozza az adatbázis-kiszolgálóhoz való összes hozzáférést, amíg explicit módon hozzá nem adja az ügyfélgépek [IP-címét.](sql-database-server-level-firewall-rule.md)


## <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai

Az IP-szabályok mellett az Azure SQL Server tűzfal lehetővé teszi *a virtuális hálózati szabályok meghatározását.*  
További információ: [Virtual Network service endpoints and rules for Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md) or watch this video:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Az Azure Networking terminológia  
A virtuális hálózati tűzfalszabályok felderítésekénél vegye figyelembe az alábbi Azure-hálózati feltételeket

**Virtuális hálózat:** Az Azure-előfizetéséhez virtuális hálózatokat társíthat 

**Alhálózat:** A virtuális hálózat **alhálózatokat**tartalmaz. Minden Azure virtuális gépek (Virtuális gépek), amely rendelkezik az alhálózatokhoz van rendelve. Egy alhálózat több virtuális gépet vagy más számítási csomópontot tartalmazhat. A virtuális hálózaton kívüli számítási csomópontok csak akkor férhetnek hozzá a virtuális hálózathoz, ha a biztonságot úgy állítja be, hogy engedélyezze a hozzáférést.

**Virtuális hálózat szolgáltatás végpontja:** A [virtuális hálózati szolgáltatás végpontja](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek tulajdonságértékei egy vagy több hivatalos Azure-szolgáltatástípus-neveket tartalmaznak. Ebben a cikkben a **Microsoft.Sql**típusneve érdekel, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik.

**Virtuális hálózati szabály:** Az SQL Database-kiszolgáló virtuális hálózati szabálya az SQL Database-kiszolgáló hozzáférés-vezérlési listájában (ACL) szereplő alhálózat. Az SQL-adatbázis acl-jében való megadáshoz az alhálózatnak tartalmaznia kell a **Microsoft.Sql** típusnevet. A virtuális hálózati szabály arra utasítja az SQL Database-kiszolgálót, hogy fogadja el az alhálózaton található összes csomópont kommunikációját.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP és virtuális hálózat tűzfalának szabályai

Az Azure SQL Server tűzfal lehetővé teszi, hogy ip-címtartományokat adjon meg, amelyekből a kommunikáció tanavanaz SQL-adatbázisba. Ez a megközelítés az Azure magánhálózaton kívüli stabil IP-címek esetén is megfelel. Az Azure magánhálózatán belüli virtuális gépek (VM-ek) azonban *dinamikus* IP-címekkel vannak konfigurálva. A dinamikus IP-címek a virtuális gép újraindításakor változhatnak, és érvényteleníthetik az IP-alapú tűzfalszabályt. Ostobaság lenne dinamikus IP-címet megadni egy tűzfalszabályban, éles környezetben.

Ezt a korlátozást megkerülheti a virtuális gép *statikus* IP-címének beszerzésével. További információt a [Privát IP-címek konfigurálása egy virtuális géphez az Azure Portal használatával című témakörben talál.](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md) Azonban a statikus IP-megközelítés válhat nehéz kezelni, és költséges, ha történik a skála. 

A virtuális hálózati szabályok egyszerűbb alternatívát jelentenek a virtuális gépeket tartalmazó adott alhálózatból való hozzáférés létrehozásához és kezeléséhez.

> [!NOTE]
> Alhálózaton még nem lehet SQL Database. Ha az Azure SQL Database-kiszolgáló a virtuális hálózat egy alhálózatának csomópontja volt, a virtuális hálózaton belül minden csomópont kommunikálhat az SQL-adatbázissal. Ebben az esetben a virtuális gépek virtuális hálózati szabályok vagy IP-szabályok nélkül kommunikálhatnak az SQL Database-lel.

## <a name="private-link"></a>Privát kapcsolat 
A Private Link lehetővé teszi, hogy **privát végponton**keresztül csatlakozzon az Azure SQL Server szolgáltatáshoz. A privát végpont egy privát IP-cím egy adott [virtuális hálózaton](../virtual-network/virtual-networks-overview.md) és alhálózaton belül.

## <a name="next-steps"></a>További lépések

- A kiszolgálószintű IP-tűzfalszabály létrehozásának rövid útmutatóját az [Azure SQL-adatbázis létrehozása](sql-database-single-database-get-started.md)című témakörben ismerteti.

- A kiszolgálószintű virtuális hálózat tűzfalszabályának létrehozásáról a [Virtuális hálózat szolgáltatás végpontjai és az Azure SQL Database szabályai](sql-database-vnet-service-endpoint-rule-overview.md)című témakörben található.

- Ha segítségre van szüksége az Azure SQL-adatbázishoz nyílt forráskódú vagy külső alkalmazásokból történő csatlakozással kapcsolatban, olvassa el [az Ügyfél rövid útmutatókódmintáit az SQL Database-hez című témakörben.](https://msdn.microsoft.com/library/azure/ee336282.aspx)

- A megnyitandó további portokról az SQL Database: Outside vs inside [1433 for ADO.NET 4.5 és AZ SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md) című **témakörben** talál további információt.

- Az Azure SQL Database Connectivity áttekintését az [Azure SQL Connectivity architektúrában](sql-database-connectivity-architecture.md) találja.

- Az Azure SQL Database biztonságának áttekintését az Adatbázis védelme című témakörben [találja.](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

