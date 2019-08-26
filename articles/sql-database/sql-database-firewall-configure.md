---
title: A Azure SQL Database és az adatraktár IP-tűzfalának szabályai | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy SQL-adatbázist vagy SQL Data Warehouse tűzfalat kiszolgálói szintű IP-tűzfalszabályok használatával a hozzáférés kezeléséhez, valamint egy vagy készletezett adatbázis konfigurálásához az adatbázis szintű IP-tűzfalszabályok segítségével.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 39d2dae28bde8ff35408733a1af886c302ec79bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568188"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>IP-tűzfalszabályok Azure SQL Database és SQL Data Warehouse

A Microsoft Azure [SQL Database](sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) biztosítanak egy, az Azure-hoz és más internetalapú alkalmazásokhoz használható, a kapcsolatot biztosító adatbázis-szolgáltatást. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.

> [!NOTE]
> Ez a cikk az Azure SQL Serverre vonatkozik, valamint az Azure SQL Serveren létrehozott SQL Database és SQL Data Warehouse adatbázisokra is. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.
> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik **Azure SQL Database felügyelt példányra**. Ha további információkra van szüksége a hálózati konfigurációval kapcsolatban, tekintse meg a következő cikket a [felügyelt példányhoz való csatlakozásról](sql-database-managed-instance-connect-app.md) .

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Virtuális hálózati szabályok az IP-szabályok alternatívájaként

Az IP-szabályok mellett a tűzfal a *virtuális hálózati szabályokat*is kezeli. A virtuális hálózati szabályok Virtual Network szolgáltatási végpontokon alapulnak. Bizonyos esetekben a virtuális hálózati szabályok előnyben részesítettek az IP-szabályoknál. További információ: [Virtual Network szolgáltatási végpontok és a Azure SQL Database szabályai](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Áttekintés

Kezdetben a SQL Database tűzfal blokkolja az Azure SQL Serverhez való hozzáférést. Egy adatbázis-kiszolgáló eléréséhez meg kell adnia egy vagy több, az Azure SQL Serverhez való hozzáférést engedélyező kiszolgálói szintű IP-tűzfalszabály-szabályt. Az IP-tűzfalszabályok segítségével megadhatja, hogy mely IP-címtartományok engedélyezettek az internetről, és hogy az Azure-alkalmazások tudnak-e csatlakozni az Azure SQL Serverhez.

Ha az Azure SQL Serveren csak egyetlen adatbázishoz szeretne hozzáférést biztosítani, akkor az adott adatbázishoz egy adatbázisszintű szabályt kell létrehoznia. Adja meg azt az IP-címtartományt az adatbázis IP-tűzfalszabály számára, amely túllépi a kiszolgálói szintű IP-tűzfalszabályok megadott IP-címtartományt, és győződjön meg arról, hogy az ügyfél IP-címe az adatbázis szintű szabályban megadott tartományba esik.

> [!IMPORTANT]
> SQL Data Warehouse csak a kiszolgálói szintű IP-tűzfalszabályok használatát támogatja, és nem támogatja az adatbázis-szintű IP-tűzfalszabályok használatát.

Az internetről és az Azure-ból érkező kapcsolódási kísérleteknek először át kell jutniuk a tűzfalon, mielőtt elérnék az Azure SQL Servert vagy az SQL Database-t, ahogyan az a következő ábrán látható:

   ![A tűzfal-konfigurációt bemutató ábra.][1]

- **Kiszolgálói szintű IP-tűzfalszabályok:**

  Ezek a szabályok lehetővé teszik, hogy az ügyfelek hozzáférhessenek a teljes Azure SQL Server-kiszolgálóhoz, vagyis az összes adatbázishoz, amely ugyanabban a SQL Database-kiszolgálón található. Ezek a szabályok a **fő** adatbázisban vannak tárolva. A kiszolgálói szintű IP-tűzfalszabályok a portálon vagy a Transact-SQL-utasítások használatával konfigurálhatók. Ha kiszolgálói szintű IP-tűzfalszabályok létrehozására van szükség a Azure Portal vagy a PowerShell használatával, az előfizetés tulajdonosának vagy az előfizetés közreműködőinek kell lennie. A kiszolgálói szintű IP-tűzfalszabályok Transact-SQL használatával történő létrehozásához az SQL Database-példányhoz kell csatlakoznia, mint a kiszolgálói szintű rendszerbiztonsági tag vagy a Azure Active Directory rendszergazdája (ami azt jelenti, hogy a kiszolgálói szintű IP-tűzfalszabályok először létre kell hozni Az Azure-szintű engedélyekkel rendelkező felhasználó).

- **Adatbázis-szintű IP-tűzfalszabályok:**

  Ezek a szabályok lehetővé teszik, hogy az ügyfelek hozzáférhessenek bizonyos (biztonságos) adatbázisokhoz ugyanazon a SQL Database kiszolgálón belül. Ezeket a szabályokat minden adatbázishoz létrehozhatja ( **beleértve a** főadatbázist is), és ezeket külön adatbázisokban tárolja a rendszer. A fő-és felhasználói adatbázisokra vonatkozó adatbázis-szintű IP-tűzfalszabályok csak Transact-SQL-utasítások használatával hozhatók létre és kezelhetők, és csak az első kiszolgálói szintű tűzfal konfigurálása után. Ha olyan IP-címtartományt ad meg az adatbázis-szintű IP-tűzfalszabály számára, amely kívül esik a kiszolgálói szintű IP-tűzfalszabály mezőben megadott tartományon, akkor csak azok az ügyfelek férhetnek hozzá az adatbázishoz, amelyek IP-címmel rendelkeznek az adatbázis szintű tartományon. Az adatbázishoz legfeljebb 128 adatbázis-szintű IP-tűzfalszabályok tartozhat. Az adatbázis-szintű IP-tűzfalszabályok konfigurálásával kapcsolatos további információkért tekintse meg a jelen cikk későbbi, a [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx)című példáját.

### <a name="recommendation"></a>Ajánlás

A Microsoft az adatbázis szintű IP-tűzfalszabályok használatát javasolja, ha lehetséges a biztonság növelése és az adatbázis hordozhatóvé tétele. Kiszolgálói szintű IP-tűzfalszabályok használata a rendszergazdák számára, és ha sok olyan adatbázissal rendelkezik, amelyek azonos hozzáférési követelményekkel rendelkeznek, és nem szeretne időt beállítani az egyes adatbázisok konfigurálására.

> [!IMPORTANT]
> A Windows Azure SQL Database legfeljebb 128 IP-tűzfalszabályok használatát támogatja.
> [!NOTE]
> Az üzletmenet folytonossága és a hordozható adatbázisok közötti kapcsolatról [a vészhelyreállítás hitelesítési követelményeit](sql-database-geo-replication-security-config.md)ismertető cikkből tájékozódhat.

### <a name="connecting-from-the-internet"></a>Csatlakozás az internetről

Amikor egy számítógép az internetről kísérli meg az adatbázis-kiszolgálóhoz való csatlakozást, a tűzfal először ellenőrzi a kérelem eredeti IP-címét az adatbázis-szintű IP-tűzfalszabályok esetében, a kapcsolatot kérő adatbázis esetében:

- Ha a kérés IP-címe az adatbázis szintű IP-tűzfalszabályok megadott tartományán belül van, akkor a rendszer a szabályt tartalmazó SQL Database megkapja a kapcsolódást.
- Ha a kérés IP-címe nem az adatbázis-szintű IP-tűzfalszabály által megadott tartományok egyikén belül van, akkor a rendszer ellenőrzi a kiszolgálói szintű IP-tűzfalszabályok beadásának szabályait. Ha a kérés IP-címe a kiszolgálói szintű IP-tűzfalszabályok megadott tartományán belül van, akkor a rendszer engedélyezi a hozzáférést. A kiszolgálói szintű IP-tűzfalszabályok az Azure SQL Server összes SQL-adatbázisára érvényesek.  
- Ha a kérés IP-címe nem az adatbázis vagy a kiszolgáló szintű IP-tűzfalszabályok egyikében megadott tartományon belül van, a kapcsolati kérelem sikertelen lesz.

> [!NOTE]
> Ha a helyi számítógépről szeretné elérni az Azure SQL Database-t, akkor ellenőrizze, hogy a hálózaton és a helyi számítógépen lévő tűzfal engedélyezi-e a kimenő kommunikációt az 1433-as TCP-porton keresztül.

### <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból

Annak engedélyezéséhez, hogy az Azure-alkalmazások csatlakozhassanak az Azure SQL Serverhez, engedélyezni kell az Azure-kapcsolatokat. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. A 0.0.0.0 értékkel megegyező kezdő és záró címekkel rendelkező tűzfalbeállítások esetén az Azure-kapcsolatok engedélyezettek. Ha a csatlakozási kísérlet nem engedélyezett, a kérés nem éri el az Azure SQL-adatbáziskiszolgálót.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.

## <a name="creating-and-managing-ip-firewall-rules"></a>IP-tűzfalszabályok létrehozása és kezelése

Az első kiszolgálói szintű tűzfalbeállítások a [Azure Portal](https://portal.azure.com/) használatával vagy programozott módon hozható létre a [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), az [Azure CLI](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)használatával. A következő kiszolgálói szintű IP-tűzfalszabályok létrehozhatók és kezelhetők ezekkel a módszerekkel és a Transact-SQL használatával.

> [!IMPORTANT]
> Az adatbázis-szintű IP-tűzfalszabályok csak a Transact-SQL használatával hozhatók létre és kezelhetők.

A teljesítmény javítása érdekében a kiszolgálói szintű IP-tűzfalszabályok átmenetileg gyorsítótárazva lesznek az adatbázis szintjén. A gyorsítótár frissítésével kapcsolatban lásd: [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> A kiszolgáló-és adatbázis-szintű tűzfal változásainak naplózásához [SQL Database naplózást](sql-database-auditing.md) használhat.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Kiszolgálói szintű IP-tűzfalszabályok kezelése a Azure Portal használatával

Ha a Azure Portal kiszolgálói szintű IP-tűzfalszabály beállítására van szüksége, az Azure SQL Database-adatbázis áttekintés lapjára vagy a SQL Database-kiszolgáló áttekintés lapjára léphet.

> [!TIP]
> Oktatóanyagért lásd: [adatbázis létrehozása a Azure Portal használatával](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>Az adatbázis áttekintése oldalon

1. Kiszolgálóoldali IP-tűzfalszabály beállításához az adatbázis áttekintése lapon kattintson a **kiszolgáló tűzfal beállítása** elemre az eszköztáron az alábbi ábrán látható módon: Megnyílik az SQL-adatbáziskiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

      ![kiszolgáló IP-tűzfalának szabálya](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Kattintson az **ügyfél IP-** címének hozzáadása elemre az eszköztáron, és adja hozzá a jelenleg használt számítógép IP-címét, majd kattintson a **Mentés**gombra. Az aktuális IP-címhez kiszolgálói szintű IP-tűzfalszabály jön létre.

      ![kiszolgálói szintű IP-tűzfalszabály beállítása](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>A kiszolgáló áttekintése lapon

Megnyílik a kiszolgáló Áttekintés lapja, amely megjeleníti a teljes kiszolgálónevet (például **mynewserver20170403.database.Windows.net**), és további konfigurálási lehetőségeket biztosít.

1. Ha kiszolgálói szintű szabályt szeretne beállítani a kiszolgáló áttekintése lapon, kattintson a bal oldali menüben a **tűzfal** elemre a beállítások területen.

2. Kattintson az **ügyfél IP-** címének hozzáadása elemre az eszköztáron, és adja hozzá a jelenleg használt számítógép IP-címét, majd kattintson a **Mentés**gombra. Az aktuális IP-címhez kiszolgálói szintű IP-tűzfalszabály jön létre.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>IP-tűzfalszabályok kezelése a Transact-SQL használatával

| Katalógusnézet vagy tárolt eljárás | Szint | Leírás |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Kiszolgáló |Megjeleníti az aktuális kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Kiszolgáló |Kiszolgálói szintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Kiszolgáló |Eltávolítja a kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Adatbázis |Az adatbázis-szintű IP-tűzfalszabályok aktuális szabályait jeleníti meg |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Adatbázis |Az adatbázis-szintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Adatbázisok |Adatbázis-szintű IP-tűzfalszabályok eltávolítása |

Az alábbi példákban áttekintheti a meglévő szabályokat, engedélyezheti a kiszolgáló contoso IP-címeinek tartományát, és törölheti az IP-tűzfalszabály szabályait:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Ezután adjon hozzá egy kiszolgálói szintű IP-tűzfalszabály-szabályt.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

A kiszolgálói szintű IP-tűzfalszabály törléséhez hajtsa végre a sp_delete_firewall_rule tárolt eljárást. A következő példa törli a ContosoFirewallRule nevű szabályt:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Kiszolgálói szintű IP-tűzfalszabályok kezelése Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

| A parancsmag | Szint | Leírás |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Kiszolgáló |Az aktuális kiszolgálószintű tűzfalszabályokat adja vissza |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Kiszolgáló |Új kiszolgálószintű tűzfalszabály létrehozása |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Kiszolgáló |Meglévő kiszolgálószintű tűzfalszabály tulajdonságainak frissítése |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok eltávolítása |

Az alábbi példa egy kiszolgálói szintű IP-tűzfalszabály beállítását állítja be a PowerShell használatával:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> A gyors üzembe helyezési környezetekben a PowerShell-példákat lásd: [adatbázis létrehozása](sql-database-powershell-samples.md) és önálló [adatbázis létrehozása és SQL Database kiszolgáló szintű IP-tűzfalszabály konfigurálása a PowerShell használatával](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Kiszolgálói szintű IP-tűzfalszabályok kezelése az Azure CLI használatával

| A parancsmag | Szint | Leírás |
| --- | --- | --- |
|[az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Kiszolgáló|Kiszolgálói IP-tűzfalszabály létrehozása|
|[az SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Kiszolgáló|A kiszolgálón található IP-tűzfalszabályok listája|
|[az SQL Server Firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Kiszolgáló|Egy IP-tűzfalszabály részleteit jeleníti meg|
|[az SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Kiszolgáló|IP-tűzfalszabály frissítése|
|[az SQL Server Firewall-Rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Kiszolgáló|IP-tűzfalszabály törlése|

Az alábbi példa egy kiszolgálói szintű IP-tűzfalszabály beállítását állítja be az Azure CLI használatával:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Azure CLI-példa a gyors üzembe helyezéssel kapcsolatban: adatbázis [létrehozása – Azure CLI](sql-database-cli-samples.md) és [egyetlen adatbázis létrehozása és SQL Database IP-tűzfalszabály konfigurálása az Azure CLI használatával](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Kiszolgálói szintű IP-tűzfalszabályok kezelése REST API használatával

| API | Szint | Leírás |
| --- | --- | --- |
| [List Firewall Rules](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Kiszolgáló |Megjeleníti az aktuális kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [Tűzfalszabály létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Kiszolgáló |Kiszolgálói szintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [Tűzfalszabály törlése](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Kiszolgáló |Eltávolítja a kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [Tűzfalszabályok beolvasása](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Kiszolgáló | Kiszolgálói szintű IP-tűzfalszabályok beolvasása |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Kiszolgálói szintű és adatbázis-szintű IP-tűzfalszabályok

K. Egy adatbázis felhasználóinak teljesen el kell különíteni egy másik adatbázisból?
Ha igen, adja meg a hozzáférést az adatbázis szintű IP-tűzfalszabályok használatával. Ezzel elkerülhető a kiszolgálói szintű IP-tűzfalszabályok használata, amely lehetővé teszi a tűzfalon keresztüli hozzáférését az összes adatbázishoz, így csökkentve a védelem mélységét.

K. Az IP-címen lévő felhasználóknak hozzá kell férniük az összes adatbázishoz?
A kiszolgálói szintű IP-tűzfalszabályok használatával csökkentheti az IP-tűzfalszabályok konfigurálásához szükséges időt.

K. Az IP-tűzfalszabályok konfigurálását végző személy vagy csapat csak a Azure Portalon, a PowerShellen vagy a REST APIon keresztül fér hozzá?
A kiszolgálói szintű IP-tűzfalszabályok használatára van szükség. Az adatbázis-szintű IP-tűzfalszabályok csak Transact-SQL használatával konfigurálhatók.  

K. Az a személy vagy csapat, akinek az IP-tűzfalszabályok nem rendelkeznek magas szintű engedélyekkel az adatbázis szintjén?
Kiszolgálói szintű IP-tűzfalszabályok használata. Az adatbázis-szintű IP-tűzfalszabályok a Transact-SQL használatával történő konfigurálásához `CONTROL DATABASE` legalább az adatbázis szintjén kell engedélyt adni.  

K. Az a személy vagy csapat konfigurálja vagy naplózza az IP-tűzfalszabályok szabályait, központilag felügyeli az IP-tűzfalszabályok számát az adatbázisok sok (esetleg 100s) esetében?
Ez a választás az igényeitől és a környezettől függ. A kiszolgálói szintű IP-tűzfalszabályok könnyebben konfigurálhatók, de a parancsfájlok az adatbázis szintjén is konfigurálhatók a szabályok. Ha pedig kiszolgálói szintű IP-tűzfalszabályok használatát is alkalmazza, előfordulhat, hogy az adatbázis-szintű IP-tűzfalszabályok naplózása lehetőségre van szükség, hogy ellenőrizze `CONTROL` , hogy az adatbázishoz tartozó felhasználók rendelkeznek-e adatbázis-szintű IP-Tűzfalszabályok létrehozásával.

K. Használhatok a kiszolgálói szintű és az adatbázis szintű IP-tűzfalszabályok kombinációját is?
Igen. Bizonyos felhasználóknak, például a rendszergazdáknak szüksége lehet a kiszolgálói szintű IP-tűzfalszabályok megírására. Más felhasználók, például egy adatbázis-alkalmazás felhasználóinak adatbázis-szintű IP-tűzfalszabályok szükségesek.

## <a name="troubleshooting-the-database-firewall"></a>Az adatbázistűzfal hibaelhárítása

A következő szempontokat vegye figyelembe, ha a Microsoft Azure SQL Database szolgáltatáshoz való hozzáférése nem a várt módon működik:

- **Helyi tűzfal konfigurációja:**

  Ahhoz, hogy a számítógép hozzáférhessen Azure SQL Databasehoz, előfordulhat, hogy létre kell hoznia egy tűzfal-kivételt a számítógépen a 1433-es TCP-porthoz. Ha az Azure-felhő határain belül létesít kapcsolatokat, előfordulhat, hogy további portokat is meg kell nyitnia. További információkért lásd a **SQL Database: A [ADO.net 4,5-es és a SQL Database-es portokon túli portok](sql-database-develop-direct-route-ports-adonet-v12.md)szakaszánkívüla1433**

- **Hálózati címfordítás (NAT):**

  A NAT miatt a számítógépe által az Azure SQL Databasehoz való kapcsolódáshoz használt IP-cím eltérő lehet a számítógép IP-konfigurációs beállításai között megjelenő IP-címről. A számítógép által az Azure-hoz való csatlakozáshoz használt IP-cím megtekintéséhez jelentkezzen be a portálra, és lépjen az adatbázist futtató kiszolgáló **Konfigurálás** lapjára. Az **Engedélyezett IP-címek** szakasz alatt jelenik meg az **Ügyfél aktuális IP-címe**. Kattintson a **Hozzáadás** lehetőségre az **Engedélyezett IP-címek** között, ha szeretné engedélyezni a számítógép számára a kiszolgáló elérését.

- **Az engedélyezési lista módosításai még nem léptek érvénybe:**

  A Azure SQL Database tűzfal konfigurációjának változásainak érvénybe léptetéséhez akár öt perc is eltelhet.

- **A bejelentkezési azonosító nincs engedélyezve, vagy helytelen jelszót használt:**

  Ha a bejelentkezési azonosító nem rendelkezik engedéllyel a Azure SQL Database-kiszolgálón, vagy helytelen a használt jelszó, a rendszer megtagadja a kapcsolódást a Azure SQL Database-kiszolgálóhoz. Egy tűzfalbeállítás létrehozása lehetővé teszi az ügyfelek számára a kiszolgálóhoz való csatlakozás megkísérlését, azonban minden egyes ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat. A bejelentkezések előkészítésével kapcsolatos további információkért lásd: [adatbázisok, bejelentkezések és felhasználók kezelése Azure SQL Databaseban](sql-database-manage-logins.md).

- **Dinamikus IP-cím:**

  Ha a dinamikus IP-címzéssel rendelkező internetkapcsolattal rendelkezik, és a tűzfalon keresztül nem sikerül bejutnia, akkor az alábbi megoldások valamelyikét kipróbálhatja:
  
  - Kérje meg az internetszolgáltatót (ISP) a Azure SQL Database-kiszolgálót elérő ügyfélszámítógépekhez rendelt IP-címtartomány számára, majd adja hozzá az IP-címtartományt IP-tűzfalszabályként.
  - Szerezze be a statikus IP-címzést az ügyfélszámítógépek helyett, majd adja hozzá az IP-címeket IP-tűzfalszabályokként.

## <a name="next-steps"></a>További lépések

- Erősítse meg, hogy a vállalati hálózati környezet lehetővé teszi az Microsoft Azure adatközpontok által használt számítási IP-címtartományok (beleértve az SQL-tartományokat is) bejövő kommunikációját. Előfordulhat, hogy az alábbi IP-címeket kell megadnia: [Microsoft Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653)  
- A kiszolgálói szintű IP-Tűzfalszabályok létrehozásával kapcsolatos rövid útmutató: [Azure SQL Database-adatbázis létrehozása](sql-database-single-database-get-started.md).
- Ha nyílt forráskódú vagy külső alkalmazásokból szeretne kapcsolódni az Azure SQL Database-hez, lásd az [SQL Database gyors üzembe helyezési ügyfélkódmintáival](https://msdn.microsoft.com/library/azure/ee336282.aspx) foglalkozó cikket.
- További információ a megnyitható **további portokról: SQL Database: A [ADO.net 4,5-es és az SQL Database-es portokon túli portok](sql-database-develop-direct-route-ports-adonet-v12.md) szakaszánkívüla1433**
- A Azure SQL Database biztonság áttekintését lásd: [az adatbázis biztonságossá tétele](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
