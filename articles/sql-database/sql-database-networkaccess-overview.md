---
title: A Azure SQL Database és az adatraktár hálózati hozzáférés-vezérlése
description: Az Azure SQL Database és az adatraktár hálózati hozzáférés-vezérlésének áttekintése a hozzáférés kezeléséhez és egy vagy készletezett adatbázis konfigurálásához.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: deee668c3597047a5d5f0fd153626916c3fd8517
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687713"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>A Azure SQL Database és az adatraktár hálózati hozzáférés-vezérlése

> [!NOTE]
> Ez a cikk az Azure SQL Serverre vonatkozik, valamint az Azure SQL Serveren létrehozott SQL Database és SQL Data Warehouse adatbázisokra is. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik **Azure SQL Database felügyelt példányra**. a hálózati konfigurációval kapcsolatos további információkért lásd: [Csatlakozás felügyelt példányhoz](sql-database-managed-instance-connect-app.md) .

Amikor új Azure-SQL Server hoz létre a [Azure Portalból](sql-database-single-database-get-started.md), az eredmény egy nyilvános végpont a *yourservername.database.Windows.net*formátumban. A terv szerint a nyilvános végponthoz való hozzáférés megtagadva. A következő hálózati hozzáférés-vezérlések használatával szelektíven engedélyezheti az SQl Database-hez való hozzáférést a nyilvános végponton keresztül.
- Azure-szolgáltatások engedélyezése: – Ha be van kapcsolva, az Azure határán belüli egyéb erőforrások, például egy Azure-beli virtuális gép, hozzáférhetnek SQL Database

- IP-tűzfalszabályok: – ezzel a funkcióval explicit módon engedélyezheti a kapcsolódást egy adott IP-címről, például a helyszíni gépekről.

- Virtual Network tűzfalszabályok: – ezzel a szolgáltatással engedélyezheti az Azure határán belüli adott Virtual Network forgalmát

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure-szolgáltatások engedélyezése 
Új Azure-SQL Server [Azure Portalból](sql-database-single-database-get-started.md)való létrehozásakor a beállítás nincs bejelölve.

 ![Képernyőfelvétel az új kiszolgáló létrehozásáról][1]

Ezt a beállítást a tűzfal panelen is módosíthatja az Azure-SQL Server létrehozása után az alábbiak szerint.
  
 ![Képernyőkép a kiszolgáló tűzfalának kezeléséről][2]

Ha az Azure SQL Server- **ra** van beállítva, az Azure határán belüli összes erőforrásról folytatott kommunikációt tesz lehetővé, amely esetleg nem része az előfizetésnek.

Sok esetben a **on** beállítás nagyobb mértékben megengedhető, mint amit a legtöbb ügyfél szeretne. Előfordulhat, hogy ezt a beállítást ki szeretné **kapcsolni** , és lecseréli az IP-tűzfalszabályok szigorúbb szabályaira vagy Virtual Network tűzfalszabályok megadására. Ennek hatására a következő funkciók érvényesek:

### <a name="import-export-service"></a>Exportálási szolgáltatás importálása

Azure SQL Database importálási exportálási szolgáltatás az Azure-beli virtuális gépeken fut. Ezek a virtuális gépek nem a VNet találhatók, ezért Azure-beli IP-címet kapnak az adatbázishoz való csatlakozáskor. Ha eltávolítja az **Azure-szolgáltatások elérésének engedélyezése kiszolgálót,** ezek a virtuális gépek nem fognak tudni hozzáférni az adatbázisokhoz.
A probléma megkerüléséhez futtassa a BACPAC importálását, vagy exportálja közvetlenül a kódban a DACFx API használatával.

### <a name="sql-database-query-editor"></a>SQL Database lekérdezés-szerkesztő

A Azure SQL Database lekérdezés-szerkesztő üzembe helyezése az Azure-beli virtuális gépeken történik. Ezek a virtuális gépek nem a VNet találhatók. Ezért a virtuális gépek Azure-beli IP-címet kapnak az adatbázishoz való csatlakozáskor. Ha eltávolítja az **Azure-szolgáltatások elérésének engedélyezése kiszolgálót**, ezek a virtuális gépek nem fognak tudni hozzáférni az adatbázisokhoz.

### <a name="table-auditing"></a>Tábla naplózása

Jelenleg két módon engedélyezheti a naplózást a SQL Databaseon. A tábla naplózása meghiúsul, miután engedélyezte a szolgáltatási végpontokat az Azure-SQL Server. Az itt található megoldás a Blobok naplózására való áttérés.

### <a name="impact-on-data-sync"></a>Az adatszinkronizálás hatása

Azure SQL Database rendelkezik az adatszinkronizálási funkcióval, amely az Azure IP-címek használatával csatlakozik az adatbázisokhoz. A szolgáltatási végpontok használatakor ki kell kapcsolni az **Azure-szolgáltatások hozzáférésének engedélyezése** a SQL Database kiszolgálóhoz való hozzáférést, és meg kell szüntetnie az adatszinkronizálási funkciót.

## <a name="ip-firewall-rules"></a>IP-tűzfalszabályok
Az IP-alapú tűzfal az Azure SQL Server egyik funkciója, amely megakadályozza az adatbázis-kiszolgáló elérését, amíg explicit módon nem [adja hozzá az ügyfélszámítógépek IP-címeit](sql-database-server-level-firewall-rule.md) .


## <a name="virtual-network-firewall-rules"></a>Tűzfalszabályok Virtual Network

Az IP-szabályok mellett az Azure SQL Server tűzfala lehetővé teszi a *virtuális hálózati szabályok*megadását.  
További információ: [Virtual Network szolgáltatási végpontok és a Azure SQL Database szabályai](sql-database-vnet-service-endpoint-rule-overview.md) , vagy tekintse meg ezt a videót:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Azure hálózati terminológia  
Vegye figyelembe a következő Azure-hálózatkezelési feltételeket az Virtual Network tűzfalszabályok megismerése során

**Virtuális hálózat:** Az Azure-előfizetéshez társított virtuális hálózatokkal is rendelkezhet 

**Alhálózat:** Egy virtuális hálózat **alhálózatokat**tartalmaz. Az alhálózatokhoz társított bármely Azure-beli virtuális gép (VM). Egy alhálózat több virtuális gépet vagy más számítási csomópontot is tartalmazhat. A virtuális hálózatán kívüli számítási csomópontok nem férnek hozzá a virtuális hálózathoz, kivéve, ha úgy konfigurálja a biztonságot, hogy engedélyezze a hozzáférést.

**Virtual Network szolgáltatási végpont:** A [Virtual Network szolgáltatási végpont] [VM-Virtual-Network-Service-endpoints – Overview-649d] olyan alhálózat, amelynek a tulajdonságai egy vagy több formális Azure szolgáltatástípus-nevet tartalmaznak. Ebben a cikkben a **Microsoft. SQL**típus neve érdekli, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik.

**Virtuális hálózati szabály:** A SQL Database-kiszolgáló virtuális hálózati szabálya egy alhálózat, amely a SQL Database-kiszolgáló hozzáférés-vezérlési listájában (ACL) szerepel. Ahhoz, hogy a SQL Database hozzáférés-vezérlési listájában legyen, az alhálózatnak tartalmaznia kell a **Microsoft. SQL** típus nevét. Egy virtuális hálózati szabály közli a SQL Database-kiszolgálóval, hogy fogadja a kommunikációt az alhálózaton lévő összes csomópontról.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP-cím és Virtual Network tűzfalszabályok

Az Azure SQL Server tűzfal lehetővé teszi olyan IP-címtartományok megadását, amelyekről a rendszer a kommunikációt SQL Databaseba fogadja. Ez a megközelítés az Azure-magánhálózaton kívüli stabil IP-címekre is kiváló. Az Azure-beli magánhálózaton lévő virtuális gépek (VM-EK) azonban *dinamikus* IP-címekkel vannak konfigurálva. A dinamikus IP-címek a virtuális gép újraindításakor változhatnak, és az IP-alapú tűzfalszabály érvénytelenné válik. Az éles környezetben nem lehet dinamikus IP-címet megadni egy tűzfalszabály számára.

Ezt a korlátozást a virtuális gép *statikus* IP-címének beszerzésével lehet megkerülni. Részletekért lásd: [magánhálózati IP-címek konfigurálása virtuális géphez a Azure Portal használatával] [VM-configure-Private-IP-Addresss-for-a-Virtual-Machine-using-the-Azure-Portal-321w]. A statikus IP-cím azonban nehezen kezelhető, és költséges, ha nagy léptékben történik. 

A virtuális hálózati szabályok egyszerűbben használhatók a virtuális gépeket tartalmazó adott alhálózat elérésének létrehozásához és kezeléséhez.

> [!NOTE]
> Még nem rendelkezhet SQL Database alhálózaton. Ha a Azure SQL Database-kiszolgáló a virtuális hálózat egyik alhálózatának csomópontja volt, a virtuális hálózaton belüli összes csomópont kommunikálhat a SQL Databaseval. Ebben az esetben a virtuális gépek kommunikálhatnak a SQL Database anélkül, hogy virtuális hálózati szabályokat vagy IP-szabályokat kellene megadnia.

## <a name="next-steps"></a>További lépések

- A kiszolgálói szintű IP-Tűzfalszabályok létrehozásával kapcsolatos rövid útmutató: [Azure SQL Database létrehozása](sql-database-single-database-get-started.md).

- A kiszolgálói szintű vnet létrehozásával kapcsolatos rövid útmutatóért tekintse meg [Virtual Network szolgáltatási végpontokat és a Azure SQL Database szabályait](sql-database-vnet-service-endpoint-rule-overview.md).

- A nyílt forráskódú vagy külső alkalmazásokból származó Azure SQL Database-adatbázisokhoz való csatlakozással kapcsolatos segítségért tekintse meg a következő témakört: az ügyfél-útmutató [kódok mintái SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- További információ a megnyitható további portokról **: SQL Database** a [ADO.net 4,5-as és a 1433 SQL Database-es porton túli portok](sql-database-develop-direct-route-ports-adonet-v12.md) területén

- Az Azure SQL Database kapcsolatok áttekintését lásd: [Azure SQL connectivity Architecture](sql-database-connectivity-architecture.md)

- A Azure SQL Database biztonság áttekintését lásd: [az adatbázis biztonságossá tétele](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
