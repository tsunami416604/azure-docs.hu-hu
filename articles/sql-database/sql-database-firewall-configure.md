---
title: IP tűzfalszabályai
description: Kiszolgálószintű IP-tűzfalszabályok konfigurálása SQL-adatbázishoz vagy SQL Data Warehouse tűzfalhoz. Hozzáférés kezelése és adatbázisszintű IP-tűzfalszabályok konfigurálása egyetlen vagy készletezésű adatbázishoz.
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
ms.date: 03/18/2019
ms.openlocfilehash: af88fdf3378a6290c773c658ea6dd3469d7c92cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531277"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Az Azure SQL Database és az Azure SQL Data Warehouse IP tűzfalszabályai

> [!NOTE]
> Ez a cikk az Azure SQL-kiszolgálókra, valamint az Azure SQL Database és az Azure SQL Data Warehouse adatbázisokra egy Azure SQL-kiszolgálón egyaránt vonatkozik. Az egyszerűség kedvéért az *SQL Database* az SQL Database és az SQL Data Warehouse rendszerre is hivatkozik.

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik az *Azure SQL Database felügyelt példányára.* A hálózati konfigurációról az [Alkalmazás csatlakoztatása az Azure SQL Database Felügyelt példányhoz című témakörben](sql-database-managed-instance-connect-app.md)talál.

Amikor például létrehoz egy új Azure SQL-kiszolgálót *mysqlserver*néven, az SQL Database tűzfala blokkolja a kiszolgáló nyilvános végpontjához való összes hozzáférést (amely *mysqlserver.database.windows.net*érhető el).

> [!IMPORTANT]
> Az SQL Data Warehouse csak a kiszolgálószintű IP-tűzfalszabályokat támogatja. Nem támogatja az adatbázisszintű IP-tűzfal szabályokat.

## <a name="how-the-firewall-works"></a>A tűzfal működése
Az internetről és az Azure-ból történő csatlakozási kísérleteknek át kell haladniuk a tűzfalon, mielőtt elérnék az SQL-kiszolgálót vagy az SQL-adatbázist, ahogy az alábbi ábra is mutatja.

   ![Tűzfal konfigurációs diagramja][1]

### <a name="server-level-ip-firewall-rules"></a>Kiszolgálószintű IP-tűzfalszabályok

  Ezek a szabályok lehetővé teszik az ügyfelek számára, hogy hozzáférjenek a teljes Azure SQL-kiszolgálóhoz, azaz az ugyanazon SQL Database-kiszolgálón belüli összes adatbázishoz. A szabályok a *fő* adatbázisban tárolódnak. Egy Azure SQL Server hez legfeljebb 128 kiszolgálószintű IP-tűzfalszabály vonatkozhat. Ha az **Azure Services és az erőforrások hozzáférésének engedélyezése a kiszolgáló** beállítás engedélyezve van, ez számít az Azure SQL Server egyetlen tűzfalszabály.
  
  Az Azure Portal, a PowerShell vagy a Transact-SQL utasítások használatával konfigurálhatja a kiszolgálószintű IP-tűzfalszabályokat.
  - A portál vagy a PowerShell használatához az előfizetés tulajdonosának vagy egy előfizetés közreműködőjének kell lennie.
  - A Transact-SQL használatához csatlakoznia kell az SQL Database-példányhoz a kiszolgálószintű egyszerű bejelentkezésként vagy az Azure Active Directory rendszergazdájaként. (A kiszolgálószintű IP-tűzfalszabályt először egy Azure-szintű engedélyekkel rendelkező felhasználónak kell létrehoznia.)

### <a name="database-level-ip-firewall-rules"></a>Adatbázis-szintű IP-tűzfalszabályok

  Ezek a szabályok lehetővé teszik az ügyfelek számára, hogy hozzáférjenek bizonyos (biztonságos) adatbázisokhoz ugyanazon az SQL Database-kiszolgálón belül. Az egyes adatbázisokhoz (beleértve a *fő* adatbázist is beleértve) hozza létre a szabályokat, és azok az egyes adatbázisokban tárolódnak.
  
  A fő- és felhasználói adatbázisokhoz csak a Transact-SQL utasítások használatával hozhat létre és kezelhet adatbázisszintű IP-tűzfalszabályokat, és csak az első kiszolgálószintű tűzfal konfigurálása után.
  
  Ha olyan IP-címtartományt ad meg az adatbázisszintű IP-tűzfalszabályban, amely kívül esik a kiszolgálószintű IP-tűzfal szabály tartományán, csak az adatbázis-szintű tartományban IP-címmel rendelkező ügyfelek férhetnek hozzá az adatbázishoz.
  
  Egy adatbázishoz legfeljebb 128 adatbázisszintű IP-tűzfalszabály vonatkozhat. Az adatbázisszintű IP-tűzfalszabályok konfigurálásáról a cikk későbbi részében található példában olvashat bővebben, és az [sp_set_database_firewall_rule (Azure SQL Database) című](https://msdn.microsoft.com/library/dn270010.aspx)témakörben olvashat bővebben.

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Javaslatok a tűzfalszabályok beállításához

Azt javasoljuk, hogy amikor csak lehetséges, használjon adatbázisszintű IP-tűzfalszabályokat. Ez a gyakorlat növeli a biztonságot, és hordozhatóbbá teszi az adatbázist. A rendszergazdák kiszolgálószintű IP-tűzfal-szabályokat használhat. Akkor is használja őket, ha sok olyan adatbázissal rendelkezik, amelyek azonos hozzáférési követelményekkel rendelkeznek, és nem szeretné az egyes adatbázisokat külön-külön konfigurálni.

> [!NOTE]
> Az üzletmenet folytonossága és a hordozható adatbázisok közötti kapcsolatról [a vészhelyreállítás hitelesítési követelményeit](sql-database-geo-replication-security-config.md)ismertető cikkből tájékozódhat.

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Kiszolgálószintű és adatbázisszintű IP-tűzfalszabályok

*Az egyik adatbázis felhasználóit teljesen el kell különíteni egy másik adatbázistól?*

Ha *igen,* használja az adatbázis-szintű IP-tűzfal szabályokat a hozzáférés engedélyezéséhez. Ezzel a módszerrel elkerüli a kiszolgálószintű IP-tűzfalszabályok használatát, amelyek lehetővé teszik a tűzfalon keresztüli hozzáférést az összes adatbázishoz. Ez csökkentené a védekezésed mélységét.

*Az IP-címek felhasználóinak minden adatbázishoz szükségük van a hozzáférésre?*

Ha *igen,* kiszolgálószintű IP-tűzfalszabályokhasználatával csökkentse az IP-tűzfalszabályok beállításának számát.

*Az IP-tűzfalszabályokat konfiguráló személy vagy csoport csak az Azure Portalon, a PowerShellen vagy a REST API-n keresztül rendelkezik hozzáféréssel?*

Ha igen, kiszolgálószintű IP-tűzfalszabályokat kell használnia. Az adatbázis-szintű IP-tűzfalszabályok csak a Transact-SQL-en keresztül konfigurálhatók.  

*Az IP tűzfalszabályokat konfiguráló személy vagy csoport nem rendelkezik magas szintű engedéllyel az adatbázis szintjén?*

Ha igen, használjon kiszolgálószintű IP-tűzfalszabályokat. Az adatbázisszintű IP-tűzfalszabályok Transact-SQL-en keresztültörténő konfigurálásához legalább *a CONTROL DATABASE* engedélyre van szükség az adatbázis szintjén.  

*Az a személy vagy csoport, aki konfigurálja vagy naplózza az IP tűzfal szabályok központilag kezeli az IP tűzfal szabályok több (talán több száz) adatbázisok?*

Ebben a forgatókönyvben az ajánlott eljárásokat az igényei és a környezet határozza meg. A kiszolgálószintű IP-tűzfalszabályok at könnyebb lehet konfigurálni, de a parancsfájlok konfigurálhatják a szabályokat az adatbázis szintjén. És még akkor is, ha kiszolgálószintű IP-tűzfal szabályokat használ, előfordulhat, hogy ellenőriznie kell az adatbázisszintű IP-tűzfal szabályokat, hogy lássa, hogy az adatbázisra *vonatkozó CONTROL* engedéllyel rendelkező felhasználók adatbázisszintű IP-tűzfalszabályokat hoznak-e létre.

*Használhatok kiszolgálószintű és adatbázisszintű IP-tűzfalszabályokat?*

Igen. Egyes felhasználóknak, például a rendszergazdáknak kiszolgálószintű IP-tűzfalszabályokra lehet szükségük. Más felhasználóknak, például egy adatbázis-alkalmazás felhasználóinak, adatbázisszintű IP-tűzfalszabályokra lehet szükségük.

### <a name="connections-from-the-internet"></a>Kapcsolatok az internetről

Amikor egy számítógép az internetről próbál csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal először a kérelem eredeti IP-címét ellenőrzi a kapcsolat által kért adatbázis-szintű IP-tűzfal szabályaival.

- Ha a cím az adatbázis-szintű IP-tűzfal szabályokban megadott tartományon belül van, a kapcsolat a szabályt tartalmazó SQL-adatbázishoz kerül.
- Ha a cím nincs az adatbázis-szintű IP-tűzfal szabályok egy tartományán belül, a tűzfal ellenőrzi a kiszolgálószintű IP-tűzfal szabályait. Ha a cím a kiszolgálószintű IP-tűzfal szabályokban szereplő tartományon belül van, a kapcsolat megadható. A kiszolgálószintű IP-tűzfal szabályai az Azure SQL-kiszolgálóösszes SQL-adatbázisára vonatkoznak.  
- Ha a cím nincs olyan tartományon belül, amely az adatbázis- vagy kiszolgálószintű IP-tűzfal szabályok bármelyikében található, a csatlakozási kérelem sikertelen lesz.

> [!NOTE]
> Ha a helyi számítógépről szeretné elérni az SQL Database szolgáltatást, győződjön meg arról, hogy a hálózat és a helyi számítógép tűzfala engedélyezi a kimenő kommunikációt az 1433-as TCP-porton.

### <a name="connections-from-inside-azure"></a>Kapcsolatok az Azure-on belülről

Ahhoz, hogy az Azure-ban tárolt alkalmazások csatlakozhassanak az SQL-kiszolgálóhoz, engedélyezni kell az Azure-kapcsolatokat. Amikor egy Azure-alkalmazás megpróbál csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezettek-e. A *0.0.0.0-nak* megfelelő kezdő és záró IP-címekkel kapcsolatos tűzfalbeállítás azt jelzi, hogy az Azure-kapcsolatok engedélyezettek. Ez közvetlenül az Azure Portal panelről kapcsolható be a tűzfalszabályok beállításával, valamint az **Azure Services és az erőforrások hozzáférésének engedélyezése a kiszolgálóra** a **tűzfalak és** a virtuális hálózatok **beállításaiban** való eléréséhez. Ha a kapcsolat nem engedélyezett, a kérelem nem éri el az SQL Database-kiszolgálót.

> [!IMPORTANT]
> Ez a beállítás úgy konfigurálja a tűzfalat, hogy engedélyezze az Azure-ból származó összes kapcsolatot, beleértve a más ügyfelek előfizetéseiből származó kapcsolatokat is. Ha ezt a lehetőséget választja, győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyek csak a jogosult felhasználókra korlátozzák a hozzáférést.

## <a name="create-and-manage-ip-firewall-rules"></a>IP-tűzfalszabályok létrehozása és kezelése

Az első kiszolgálószintű tűzfalbeállítást az [Azure Portalon](https://portal.azure.com/) vagy programozott módon hozza létre az [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.sql) [az Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)vagy egy Azure [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)használatával. Ezekkel a módszerekkel vagy a Transact-SQL-rel további kiszolgálószintű IP-tűzfalszabályokat hozhat létre és kezelhet.

> [!IMPORTANT]
> Az adatbázis-szintű IP-tűzfalszabályok csak a Transact-SQL használatával hozhatók létre és kezelhetők.

A teljesítmény javítása érdekében a kiszolgálószintű IP-tűzfalszabályok ideiglenesen az adatbázis szintjén kerülnek gyorsítótárba. A gyorsítótár frissítésével kapcsolatban lásd: [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Az [SQL Database Auditing](sql-database-auditing.md) segítségével naplózhatja a kiszolgáló- és adatbázis-szintű tűzfalmódosításokat.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Kiszolgálószintű IP-tűzfalszabályok kezelése az Azure Portalon

Kiszolgálószintű IP-tűzfalszabály beállítása az Azure Portalon, nyissa meg az Azure SQL-adatbázis vagy az SQL Database-kiszolgáló áttekintő lapját.

> [!TIP]
> Az oktatóanyagról az [Adatbázis létrehozása az Azure Portalhasználatával című témakörben](sql-database-single-database-get-started.md)található.

#### <a name="from-the-database-overview-page"></a>Az adatbázis áttekintése lapról

1. Ha kiszolgálószintű IP-tűzfalszabályt szeretne beállítani az adatbázis áttekintő lapjáról, válassza a **Kiszolgáló tűzfalának beállítása** lehetőséget az eszköztáron, ahogy az alábbi képen látható. 

    ![Kiszolgáló IP tűzfalának szabálya](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    Megnyílik az SQL-adatbáziskiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

2. Az **eszköztáron** válassza az Ügyfél IP hozzáadása lehetőséget a használt számítógép IP-címének hozzáadásához, majd kattintson a **Mentés gombra.** Az aktuális IP-címhez kiszolgálószintű IP-tűzfalszabály jön létre.

    ![Kiszolgálószintű IP-tűzfalszabály beállítása](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>A kiszolgáló áttekintése lapról

Megnyílik a kiszolgáló áttekintő lapja. A teljesen minősített kiszolgálónevét (például *mynewserver20170403.database.windows.net)* jeleníti meg, és további konfigurációs lehetőségeket biztosít.

1. Ha ebből a lapból kiszolgálószintű szabályt szeretne beállítani, válassza a bal oldali **Beállítások** menü **Tűzfal** parancsát.

2. Az **eszköztáron** válassza az Ügyfél IP hozzáadása lehetőséget a használt számítógép IP-címének hozzáadásához, majd kattintson a **Mentés gombra.** Az aktuális IP-címhez kiszolgálószintű IP-tűzfalszabály jön létre.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>IP-tűzfalszabályok kezelése a Transact-SQL segítségével

| Katalógusnézet vagy tárolt eljárás | Szint | Leírás |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Kiszolgáló |Az aktuális kiszolgálószintű IP-tűzfal szabályok megjelenítése |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Kiszolgáló |Kiszolgálószintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Kiszolgáló |Eltávolítja a kiszolgálószintű IP-tűzfal szabályait |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Adatbázis |Az aktuális adatbázisszintű IP-tűzfalszabályok megjelenítése |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Adatbázis |Adatbázisszintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Adatbázisok |Adatbázisszintű IP-tűzfalszabályok eltávolítása |

A következő példa áttekinti a meglévő szabályokat, engedélyezi az IP-címek tartományát a *kiszolgálócontoso,* és töröl egy IP-tűzfal szabályt:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Ezután adjon hozzá egy kiszolgálószintű IP-tűzfalszabályt.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Kiszolgálószintű IP-tűzfalszabály törléséhez hajtsa végre a *sp_delete_firewall_rule* tárolt eljárást. A következő példa törli a *ContosoFirewallRule*szabályt:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Kiszolgálószintű IP-tűzfalszabályok kezelése a PowerShell használatával 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden fejlesztés most már az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az és az AzureRm-modulok parancsainak argumentumai lényegében azonosak.

| Parancsmag | Szint | Leírás |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Kiszolgáló |Az aktuális kiszolgálószintű tűzfalszabályokat adja vissza |
| [Új-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Kiszolgáló |Új kiszolgálószintű tűzfalszabály létrehozása |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Kiszolgáló |Meglévő kiszolgálószintű tűzfalszabály tulajdonságainak frissítése |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok eltávolítása |

A következő példa a PowerShell segítségével állít be egy kiszolgálószintű IP-tűzfalszabályt:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> A $servername adja meg a kiszolgáló nevét, és nem a teljesen minősített DNS-nevet pl. adja meg **a mysqldbserver-t** **mysqldbserver.database.windows.net**

> [!TIP]
> A Rövid útmutató környezetében a PowerShell példáiról a [DB – PowerShell létrehozása](sql-database-powershell-samples.md) és egyetlen adatbázis létrehozása című témakörben [talál, és konfigurálhat egy SQL Database-kiszolgálószintű IP-tűzfalszabályt a PowerShell használatával.](scripts/sql-database-create-and-configure-database-powershell.md)

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Kiszolgálószintű IP-tűzfalszabályok kezelése a CLI segítségével

| Parancsmag | Szint | Leírás |
| --- | --- | --- |
|[az sql server tűzfal-szabály létrehozása](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Kiszolgáló|Kiszolgáló IP-tűzfalszabályának létrehozása|
|[az sql server tűzfalszabálylistája](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Kiszolgáló|A kiszolgálón lévő IP-tűzfal szabályainak listája|
|[az sql server tűzfal-szabály megjelenítése](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Kiszolgáló|Ip-tűzfalszabály részleteinek megjelenítése|
|[az sql server tűzfalszabály frissítése](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Kiszolgáló|IP-tűzfalszabály frissítése|
|[az sql server tűzfal-szabály törlése](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Kiszolgáló|IP-tűzfalszabály törlése|

A következő példa a CLI segítségével állít be egy kiszolgálószintű IP-tűzfalszabályt:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> A $servername adja meg a kiszolgáló nevét, és nem a teljesen minősített DNS-nevet pl. adja meg **a mysqldbserver-t** **mysqldbserver.database.windows.net**

> [!TIP]
> A CLI-példát a rövid útmutató környezetében lásd: [DB - Azure CLI létrehozása](sql-database-cli-samples.md) és [hozzon létre egy adatbázist, és konfiguráljon egy SQL Database IP tűzfalszabályt az Azure CLI használatával.](scripts/sql-database-create-and-configure-database-cli.md)

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Kiszolgálószintű IP-tűzfalszabályok kezelése REST API-val

| API | Szint | Leírás |
| --- | --- | --- |
| [Tűzfalszabályok listázása](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Kiszolgáló |Az aktuális kiszolgálószintű IP-tűzfal szabályok megjelenítése |
| [Tűzfalszabályok létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Kiszolgáló |Kiszolgálószintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [Tűzfalszabályok törlése](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Kiszolgáló |Eltávolítja a kiszolgálószintű IP-tűzfal szabályait |
| [Tűzfalszabályok beszereznie](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Kiszolgáló | Kiszolgálószintű IP-tűzfalszabályok bekerülése |

## <a name="troubleshoot-the-database-firewall"></a>Az adatbázis-tűzfal – problémamegoldás

Vegye figyelembe a következő pontokat, ha az SQL Database szolgáltatáshoz való hozzáférés nem a várt módon viselkedik.

- **Helyi tűzfal konfigurációja:**

  Ahhoz, hogy a számítógép hozzáférhessen az SQL Database-hez, előfordulhat, hogy tűzfalkivételt kell létrehoznia a számítógépen az 1433-as TCP-porthoz. Kapcsolatok létesítéséhez az Azure felhőhatárain belül, előfordulhat, hogy meg kell nyitnia a további portok. További információt az [1433-as portok 1433 utáni szakaszainak](sql-database-develop-direct-route-ports-adonet-v12.md)"SQL Database: Outside vs inside" című részében talál a ADO.NET 4.5 és az SQL Database számára.

- **Hálózati cím fordítása:**

  A hálózati címfordítás (NAT) miatt a számítógép által az SQL-adatbázishoz való csatlakozáshoz használt IP-cím eltérhet a számítógép IP-konfigurációs beállításaiban megadott IP-címtől. A számítógép által az Azure-hoz való csatlakozáshoz használt IP-cím megtekintése:
    1. Jelentkezzen be az Portalra.
    1. Nyissa meg az adatbázist tartalmazó kiszolgáló **Konfigurálás** lapját.
    1. Az **aktuális ügyfél IP-címe** az **Engedélyezett IP-címek** szakaszban jelenik meg. Válassza a **Hozzáadás** az **Engedélyezett IP-címekhez** lehetőséget, ha engedélyezni szeretné a számítógép számára a kiszolgáló elérését.

- **Az engedélyezési lista módosításai még nem léptek érvénybe:**

  Az SQL Database tűzfalkonfigurációjának módosításai akár öt perces késést is okozhatnak.

- **A bejelentkezés nem engedélyezett, vagy helytelen jelszót használtak:**

  Ha egy bejelentkezési jogosultság nem rendelkezik az SQL Database kiszolgálón, vagy a jelszó helytelen, a rendszer megtagadja a kapcsolatot a kiszolgálóval. A tűzfalbeállítás létrehozása csak lehetőséget ad az ügyfeleknek *arra,* hogy megpróbáljanak csatlakozni a kiszolgálóhoz. Az ügyfélnek továbbra is meg kell adnia a szükséges biztonsági hitelesítő adatokat. A bejelentkezések előkészítéséről az SQL Database és az [SQL Data Warehouse adatbázis-hozzáférésének szabályozása és megadása című témakörben talál](sql-database-manage-logins.md)további információt.

- **Dinamikus IP-cím:**

  Ha dinamikus IP-címzést használó internetkapcsolattal rendelkezik, és nem tud átjutni a tűzfalon, próbálkozzon az alábbi megoldási javaslatokkal:
  
  - Kérdezze meg az internetszolgáltatótól az SQL Database-kiszolgálóhoz hozzáférő ügyfélszámítógépekhez rendelt IP-címtartományt. Adja hozzá az IP-címtartományt IP-tűzfalszabályként.
  - Statikus IP-címzést kaphat az ügyfélszámítógépekhez. Adja hozzá az IP-címeket IP-tűzfal-szabályként.

## <a name="next-steps"></a>További lépések

- Győződjön meg arról, hogy a vállalati hálózati környezet lehetővé teszi a bejövő kommunikációt a számítási IP-címtartományok (beleértve az SQL-tartományok) által használt Azure adatközpontok. Előfordulhat, hogy ezeket az IP-címeket hozzá kell adnia az engedélyezési listához. Lásd: [Microsoft Azure adatközpont IP-tartományai.](https://www.microsoft.com/download/details.aspx?id=41653)  
- A kiszolgálószintű IP-tűzfalszabály létrehozásáról az [Azure SQL-adatbázis létrehozása című](sql-database-single-database-get-started.md)témakörben található.
- Ha segítségre van szüksége az Azure SQL-adatbázishoz nyílt forráskódú vagy külső alkalmazásokból való csatlakozáshoz, olvassa el [az Ügyfél rövid útmutatókódmintáit az SQL Database-hez című témakörben.](https://msdn.microsoft.com/library/azure/ee336282.aspx)
- A megnyitandó további portokról a [ADO.NET 4.5-ös és az SQL Database 1433-nál nagyobb portok "SQL](sql-database-develop-direct-route-ports-adonet-v12.md) Database: Outside vs inside" című részében talál további információt.
- Az Azure SQL Database biztonságának áttekintését az Adatbázis védelme című témakörben [találja.](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
