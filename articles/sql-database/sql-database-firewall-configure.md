---
title: IP-tűzfalszabályok
description: Kiszolgálói szintű IP-tűzfalszabályok konfigurálása SQL-adatbázishoz vagy SQL Data Warehouse tűzfalhoz. Az adatbázis-szintű IP-tűzfalszabályok hozzáférésének kezelése és konfigurálása egyetlen vagy készletezett adatbázishoz.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 5c1a146a12fd8881982826e0a87868a6eaf05cb1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851822"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>IP-tűzfalszabályok Azure SQL Database és Azure SQL Data Warehouse

> [!NOTE]
> Ez a cikk az Azure SQL Server-kiszolgálókra, valamint az Azure SQL-kiszolgálók Azure SQL Database és Azure SQL Data Warehouse adatbázisaira vonatkozik. Az egyszerűség kedvéért a *SQL Database* a SQL Database és a SQL Data Warehousera is vonatkozik.

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik *Azure SQL Database felügyelt példányra*. A hálózati konfigurációval kapcsolatos további információkért lásd: [az alkalmazás Összekötése Azure SQL Database felügyelt példányhoz](sql-database-managed-instance-connect-app.md).

Ha a *portra beállított mysqlserver*nevű új Azure SQL Servert hoz létre, például a SQL Database tűzfal blokkolja a kiszolgáló nyilvános végpontjának hozzáférését (amely a *mysqlserver.database.Windows.net*címen érhető el).

> [!IMPORTANT]
> A SQL Data Warehouse csak a kiszolgálói szintű IP-tűzfalszabályok használatát támogatja. Az adatbázis-szintű IP-tűzfalszabályok nem támogatottak.

## <a name="how-the-firewall-works"></a>A tűzfal működése
Az internetről és az Azure-ról érkező csatlakozási kísérletek a tűzfalon keresztül jutnak el az SQL-kiszolgáló vagy az SQL-adatbázis eléréséhez, az alábbi ábrán látható módon.

   ![Tűzfal-konfigurációs diagram][1]

### <a name="server-level-ip-firewall-rules"></a>Kiszolgálószintű IP-tűzfalszabályok

  Ezek a szabályok lehetővé teszik, hogy az ügyfelek hozzáférhessenek a teljes Azure SQL Server-kiszolgálóhoz, vagyis az összes adatbázishoz, amely ugyanabban a SQL Database-kiszolgálón található. A szabályokat a *Master* adatbázisban tárolja a rendszer. Az Azure-SQL Serverhoz legfeljebb 128 kiszolgálói szintű IP-tűzfalszabály tartozhat.
  
  A kiszolgálói szintű IP-tűzfalszabályok a Azure Portal, a PowerShell vagy a Transact-SQL utasítások használatával konfigurálhatók.
  - A portál vagy a PowerShell használatához az előfizetés tulajdonosának vagy az előfizetés közreműködőinek kell lennie.
  - A Transact-SQL használatához az SQL Database-példányhoz kell csatlakoznia, mint a kiszolgálói szintű rendszerbiztonsági tag vagy a Azure Active Directory rendszergazda. (A kiszolgálói szintű IP-tűzfalszabályok először egy Azure szintű engedélyekkel rendelkező felhasználónak kell létrehoznia.)

### <a name="database-level-ip-firewall-rules"></a>Adatbázis szintű IP-tűzfalszabályok

  Ezek a szabályok lehetővé teszik, hogy az ügyfelek hozzáférhessenek bizonyos (biztonságos) adatbázisokhoz ugyanazon a SQL Database kiszolgálón belül. Létrehozhatja az egyes adatbázisok szabályait ( *beleértve a* főadatbázist is), és azokat az egyes adatbázisokban tárolja.
  
  A fő-és a felhasználói adatbázisokhoz csak az adatbázis-szintű IP-tűzfalszabályok hozhatók létre és kezelhetők a Transact-SQL-utasítások használatával, és csak az első kiszolgálói szintű tűzfal konfigurálása után.
  
  Ha olyan IP-címtartományt ad meg az adatbázis-szintű IP-tűzfalszabály számára, amely a kiszolgálói szintű IP-tűzfalszabályok tartományán kívül esik, csak azok az ügyfelek férhetnek hozzá az adatbázishoz, amelyek IP-címmel rendelkeznek az adatbázis-szintű tartományon.
  
  Az adatbázishoz legfeljebb 128 adatbázis-szintű IP-tűzfalszabályok tartozhat. Az adatbázis-szintű IP-tűzfalszabályok konfigurálásával kapcsolatos további információkért tekintse meg a jelen cikk későbbi, [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx)című példáját.

### <a name="recommendations-for-how-to-set-firewall-rules"></a>A tűzfalszabályok beállításával kapcsolatos javaslatok

Az adatbázis szintű IP-tűzfalszabályok használatát javasoljuk, ha lehetséges. Ez a gyakorlat fokozza a biztonságot, és hordozhatóvé teszi az adatbázist. A rendszergazdákra vonatkozó kiszolgálói szintű IP-tűzfalszabályok használata. Akkor is használja őket, ha sok olyan adatbázisa van, amely azonos hozzáférési követelményekkel rendelkezik, és nem szeretné külön konfigurálni az egyes adatbázisokat.

> [!NOTE]
> Az üzletmenet folytonossága és a hordozható adatbázisok közötti kapcsolatról [a vészhelyreállítás hitelesítési követelményeit](sql-database-geo-replication-security-config.md)ismertető cikkből tájékozódhat.

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Kiszolgálói szintű és adatbázis-szintű IP-tűzfalszabályok

*Egy adatbázis felhasználóinak teljesen el kell különíteni egy másik adatbázisból?*

Ha *Igen*, az adatbázis-szintű IP-tűzfalszabályok használatával engedélyezze a hozzáférést. Ez a módszer elkerüli a kiszolgálói szintű IP-tűzfalszabályok használatát, ami lehetővé teszi a tűzfalon keresztüli hozzáférését az összes adatbázishoz. Ez csökkenti a védelem mélységét.

*Az IP-címekhez tartozó felhasználóknak hozzáférésre van szükségük az összes adatbázishoz?*

Ha *Igen*, a kiszolgálói szintű IP-tűzfalszabályok használatával csökkentse az IP-tűzfalszabályok konfigurálásához szükséges időt.

*Az IP-tűzfalszabályok konfigurálását végző személy vagy csapat csak a Azure Portalon, a PowerShellen vagy a REST APIon keresztül fér hozzá?*

Ha igen, a kiszolgálói szintű IP-tűzfalszabályok használatát kell használnia. Az adatbázis szintű IP-tűzfalszabályok csak Transact-SQL használatával konfigurálhatók.  

*Az a személy vagy csapat, aki úgy konfigurálja az IP-tűzfalszabályok szabályait, hogy az adatbázis szintjén magas szintű engedélyekkel rendelkezik?*

Ha igen, használja a kiszolgálói szintű IP-tűzfalszabályok használatát. Az adatbázis-szintű IP-tűzfalszabályok Transact-SQL-en keresztüli konfigurálásához legalább a *vezérlési adatbázis* engedélyére van szükség az adatbázis szintjén.  

*Az IP-tűzfalszabályok konfigurálását vagy naplózását végző személy vagy csoport központilag kezeli az IP-tűzfalszabályok több (akár száz) adatbázisra vonatkozó szabályait?*

Ebben az esetben az ajánlott eljárásokat az Ön igényei és környezete határozza meg. A kiszolgálói szintű IP-tűzfalszabályok könnyebben konfigurálhatók, de a parancsfájlok az adatbázis szintjén is konfigurálhatók a szabályok. Ha pedig kiszolgálói szintű IP-tűzfalszabályok használatát is használja, előfordulhat, hogy az adatbázis-szintű IP-tűzfalszabályok naplózása lehetőségre kattintva ellenőrizheti, hogy a *felhasználók rendelkeznek-* e az adatbázis-szintű IP-tűzfalszabályok létrehozásához szükséges engedélyekkel.

*Használhatok a kiszolgálói szintű és az adatbázis szintű IP-tűzfalszabályok együttes használatát?*

Igen. Egyes felhasználók, például a rendszergazdák kiszolgálói szintű IP-tűzfalszabályok szükségesek. Más felhasználók, például egy adatbázis-alkalmazás felhasználóinak adatbázis-szintű IP-tűzfalszabályok szükségesek.

### <a name="connections-from-the-internet"></a>Internetkapcsolatok

Amikor egy számítógép az internetről próbál csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal először ellenőrzi a kérelem eredeti IP-címét a kapcsolódási kérelmeket tartalmazó adatbázis adatbázis-szintű IP-tűzfalszabályok szabályai alapján.

- Ha a cím az adatbázis szintű IP-tűzfalszabályok megadott tartományon belül van, akkor a rendszer a szabályt tartalmazó SQL-adatbázishoz adja meg a kapcsolódást.
- Ha a cím nem az adatbázis szintű IP-tűzfalszabályok tartományán belül van, a tűzfal ellenőrzi a kiszolgálói szintű IP-tűzfalszabályok szabályait. Ha a cím olyan tartományon belül van, amely a kiszolgálói szintű IP-tűzfalszabályok esetében szerepel, akkor a rendszer megadta a kapcsolatokat. A kiszolgálói szintű IP-tűzfalszabályok az Azure SQL Server összes SQL-adatbázisára érvényesek.  
- Ha a cím nem olyan tartományon belül van, amely az adatbázis vagy a kiszolgáló szintű IP-tűzfalszabályok egyikében sem szerepel, a kapcsolati kérelem sikertelen lesz.

> [!NOTE]
> A helyi számítógép SQL Databaseának eléréséhez győződjön meg arról, hogy a tűzfal a hálózaton és a helyi számítógépen engedélyezi a kimenő kommunikációt a 1433-es TCP-porton.

### <a name="connections-from-inside-azure"></a>Kapcsolatok az Azure-on belül

Ha engedélyezni szeretné az Azure-ban üzemeltetett alkalmazások számára az SQL Serverhez való csatlakozást, engedélyezni kell az Azure-kapcsolatokat. Ha egy Azure-alkalmazás megpróbál csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezettek-e. Egy olyan tűzfal-beállítás, amely a *0.0.0.0* értékkel megegyező IP-címekkel rendelkezik, azt jelzi, hogy az Azure-kapcsolatok engedélyezettek. Ha a kapcsolat nem engedélyezett, a kérelem nem éri el a SQL Database-kiszolgálót.

> [!IMPORTANT]
> Ez a beállítás úgy konfigurálja a tűzfalat, hogy engedélyezze az Azure összes kapcsolatát, beleértve a más ügyfelek előfizetései által létesített kapcsolatokat is. Ha ezt a beállítást választja, győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei csak a jogosult felhasználókra korlátozzák a hozzáférést.

## <a name="create-and-manage-ip-firewall-rules"></a>IP-tűzfalszabályok létrehozása és kezelése

Az első kiszolgálói szintű tűzfalbeállítások létrehozásához használja a [Azure Portal](https://portal.azure.com/) vagy programozott módon az [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), az [azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)vagy egy Azure [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)használatával. Ezen metódusok vagy Transact-SQL használatával további kiszolgálói szintű IP-tűzfalszabályok hozhatók létre és kezelhetők.

> [!IMPORTANT]
> Az adatbázis-szintű IP-tűzfalszabályok csak a Transact-SQL használatával hozhatók létre és kezelhetők.

A teljesítmény javítása érdekében a kiszolgálói szintű IP-tűzfalszabályok átmenetileg gyorsítótárazva lesznek az adatbázis szintjén. A gyorsítótár frissítésével kapcsolatban lásd: [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> A kiszolgáló-és adatbázis-szintű tűzfal változásainak naplózásához [SQL Database naplózást](sql-database-auditing.md) használhat.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>A kiszolgálói szintű IP-tűzfalszabályok kezelése a Azure Portal használatával

A kiszolgálói szintű IP-tűzfalszabály beállításához a Azure Portal nyissa meg az Azure SQL Database vagy a SQL Database-kiszolgáló áttekintés lapját.

> [!TIP]
> Oktatóanyagért lásd: [adatbázis létrehozása a Azure Portal használatával](sql-database-single-database-get-started.md).

#### <a name="from-the-database-overview-page"></a>Az adatbázis – áttekintés oldalon

1. Kiszolgálóoldali IP-tűzfalszabály beállításához az adatbázis áttekintése lapon válassza a **kiszolgáló tűzfal beállítása** az eszköztáron lehetőséget, majd az alábbi képen látható módon. 

    ![Kiszolgáló IP-tűzfalának szabálya](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    Megnyílik az SQL-adatbáziskiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

2. Válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron, és adja hozzá a használt számítógép IP-címét, majd kattintson a **Mentés**gombra. Az aktuális IP-címhez kiszolgálói szintű IP-tűzfalszabály jön létre.

    ![Kiszolgálói szintű IP-tűzfalszabály beállítása](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>A kiszolgáló áttekintése lapon

Megnyílik a kiszolgáló Áttekintés lapja. Megjeleníti a teljes kiszolgálónevet (például *mynewserver20170403.database.Windows.net*), és további konfigurálási lehetőségeket biztosít.

1. Ha ezen a lapon szeretné beállítani a kiszolgálói szintű szabályt, válassza a bal oldalon található **Beállítások** menüből a **tűzfal** lehetőséget.

2. Válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron, és adja hozzá a használt számítógép IP-címét, majd kattintson a **Mentés**gombra. Az aktuális IP-címhez kiszolgálói szintű IP-tűzfalszabály jön létre.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Az IP-tűzfalszabályok kezelése a Transact-SQL használatával

| Katalógus nézet vagy tárolt eljárás | Szint | Leírás |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Kiszolgáló |Megjeleníti az aktuális kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Kiszolgáló |Kiszolgálói szintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Kiszolgáló |Eltávolítja a kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Adatbázis |Az adatbázis-szintű IP-tűzfalszabályok aktuális szabályait jeleníti meg |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Adatbázis |Az adatbázis-szintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Adatbázis-kezelés |Adatbázis-szintű IP-tűzfalszabályok eltávolítása |

Az alábbi példa a meglévő szabályokat tekinti át, lehetővé teszi a kiszolgáló *contoso*IP-címeinek tartományát, és töröl egy IP-tűzfalszabály-szabályt:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Ezután adjon hozzá egy kiszolgálói szintű IP-tűzfalszabály-szabályt.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

A kiszolgálói szintű IP-tűzfalszabály törléséhez hajtsa végre az *sp_delete_firewall_rule* tárolt eljárást. A következő példa törli a szabály *ContosoFirewallRule*:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>A kiszolgáló szintű IP-tűzfalszabályok kezelése a PowerShell használatával 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a fejlesztés most már az az. SQL modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

| Parancsmag | Szint | Leírás |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Kiszolgáló |Az aktuális kiszolgálószintű tűzfalszabályokat adja vissza |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Kiszolgáló |Új kiszolgálószintű tűzfalszabály létrehozása |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Kiszolgáló |Meglévő kiszolgálószintű tűzfalszabály tulajdonságainak frissítése |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok eltávolítása |

A következő példa a PowerShell használatával állítja be a kiszolgálói szintű IP-tűzfalszabály beállításait:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> A $servername adja meg a kiszolgáló nevét, ne a teljes DNS-nevet, például adja meg a **mysqldbserver** -t a **mysqldbserver.database.Windows.net** helyett.

> [!TIP]
> Egy rövid útmutató kontextusában a PowerShell-példákat lásd: [create db-PowerShell](sql-database-powershell-samples.md) és [egyetlen adatbázis létrehozása és egy SQL Database kiszolgálói szintű IP-tűzfalszabály konfigurálása a PowerShell használatával](scripts/sql-database-create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Kiszolgálói szintű IP-tűzfalszabályok kezelése a CLI használatával

| Parancsmag | Szint | Leírás |
| --- | --- | --- |
|[az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Kiszolgáló|Kiszolgálói IP-tűzfalszabály létrehozása|
|[az SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Kiszolgáló|A kiszolgálón található IP-tűzfalszabályok listája|
|[az SQL Server Firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Kiszolgáló|Egy IP-tűzfalszabály részleteit jeleníti meg|
|[az SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Kiszolgáló|IP-tűzfalszabály frissítése|
|[az SQL Server Firewall-Rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Kiszolgáló|IP-tűzfalszabály törlése|

A következő példa a parancssori felületet használja a kiszolgálói szintű IP-tűzfalszabály beállításához:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> A $servername adja meg a kiszolgáló nevét, ne a teljes DNS-nevet, például adja meg a **mysqldbserver** -t a **mysqldbserver.database.Windows.net** helyett.

> [!TIP]
> CLI-példa egy rövid útmutató kontextusában: az [adatbázis létrehozása – Azure CLI](sql-database-cli-samples.md) és [egyetlen adatbázis létrehozása és egy SQL Database IP-tűzfalszabály konfigurálása az Azure CLI használatával](scripts/sql-database-create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>A kiszolgálói szintű IP-tűzfalszabályok kezelésére szolgáló REST API használata

| API | Szint | Leírás |
| --- | --- | --- |
| [Tűzfalszabályok listázása](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Kiszolgáló |Megjeleníti az aktuális kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [Tűzfalszabályok létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Kiszolgáló |Kiszolgálói szintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [Tűzfalszabályok törlése](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Kiszolgáló |Eltávolítja a kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [Tűzfalszabályok beolvasása](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Kiszolgáló | Kiszolgálói szintű IP-tűzfalszabályok beolvasása |

## <a name="troubleshoot-the-database-firewall"></a>Az adatbázis tűzfala – problémamegoldás

Vegye figyelembe a következő szempontokat, amikor a SQL Database szolgáltatáshoz való hozzáférés nem a várt módon működik.

- **Helyi tűzfal konfigurációja:**

  Ahhoz, hogy a számítógép hozzáférhessen SQL Databasehoz, előfordulhat, hogy létre kell hoznia egy tűzfal-kivételt a számítógépen a 1433-es TCP-porthoz. Előfordulhat, hogy az Azure-felhő határain belül szeretne kapcsolatokat létesíteni. további portok megnyitására van lehetőség. További információ: a [1433-es és a SQL Database-es porton túli portok](sql-database-develop-direct-route-ports-adonet-v12.md)"SQL Database: külső és belső" szakasza, 4,5 ADO.net.

- **Hálózati címfordítás:**

  Hálózati címfordítás (NAT) miatt a számítógépe által az SQL Databasehoz való kapcsolódáshoz használt IP-cím a számítógép IP-konfigurációs beállításainak IP-címétől eltérő lehet. A számítógép által az Azure-hoz való kapcsolódáshoz használt IP-cím megtekintése:
    1. Jelentkezzen be az Portalra.
    1. Lépjen a **configure (Konfigurálás** ) lapra az adatbázist futtató kiszolgálón.
    1. Az **aktuális ügyfél IP-címe** az **engedélyezett IP-címek** szakaszban jelenik meg. Válassza a **Hozzáadás** az **engedélyezett IP-címekhez** lehetőséget, hogy a számítógép hozzáférjen a kiszolgálóhoz.

- **Az engedélyezési lista módosításai még nem kerültek érvénybe:**

  A SQL Database tűzfal konfigurációjának változásainak érvénybe léptetéséhez akár öt perc is eltelhet.

- **A bejelentkezés nincs engedélyezve, vagy helytelen jelszót használt:**

  Ha a bejelentkezési azonosító nem rendelkezik engedéllyel a SQL Database-kiszolgálón, vagy helytelen a jelszó, a rendszer megtagadja a kapcsolódást a kiszolgálóval. A tűzfalszabályok létrehozása csak *lehetőséget* biztosít az ügyfeleknek, hogy megpróbáljon csatlakozni a kiszolgálóhoz. Az ügyfélnek továbbra is meg kell adnia a szükséges biztonsági hitelesítő adatokat. A bejelentkezések előkészítésével kapcsolatos további információkért lásd: [az adatbázis-hozzáférés szabályozása és megadása SQL Database és SQL Data Warehousehoz](sql-database-manage-logins.md).

- **Dinamikus IP-cím:**

  Ha olyan internetkapcsolattal rendelkezik, amely dinamikus IP-címzést használ, és problémát tapasztal a tűzfalon keresztül, próbálkozzon az alábbi megoldások egyikével:
  
  - Kérje meg az internetszolgáltatót az SQL Database-kiszolgálót elérő ügyfélszámítógépekhez rendelt IP-címtartomány megadására. Adja hozzá az IP-címtartományt IP-tűzfalszabályként.
  - Az ügyfélszámítógépek helyett statikus IP-címzést kell lekérnie. Adja hozzá az IP-címeket IP-tűzfalszabályokként.

## <a name="next-steps"></a>Következő lépések

- Ellenőrizze, hogy a vállalati hálózati környezet lehetővé teszi-e a bejövő kommunikációt az Azure-adatközpontok által használt számítási IP-címtartományok (beleértve az SQL-tartományokat is). Előfordulhat, hogy ezeket az IP-címeket fel kell vennie az engedélyezési listára. Lásd: [Microsoft Azure adatközpont IP-tartományai](https://www.microsoft.com/download/details.aspx?id=41653).  
- A kiszolgálói szintű IP-tűzfalszabály létrehozásával kapcsolatos rövid útmutató: [Azure SQL Database létrehozása](sql-database-single-database-get-started.md).
- A nyílt forráskódú vagy külső alkalmazásokból származó Azure SQL Database-adatbázisokhoz való csatlakozással kapcsolatos segítségért tekintse meg a következő témakört: az ügyfél-útmutató [kódok mintái SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- További információ a megnyitható további portokról: a [ADO.NET 4,5-es és a 1433 SQL Database-es porton túli portok](sql-database-develop-direct-route-ports-adonet-v12.md) "SQL Database: külső vs belső" szakasza.
- A Azure SQL Database biztonság áttekintését lásd: [az adatbázis biztonságossá tétele](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
