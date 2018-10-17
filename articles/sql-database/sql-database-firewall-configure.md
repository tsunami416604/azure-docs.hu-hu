---
title: Az Azure SQL Database és az adatraktár tűzfalszabályok |} A Microsoft Docs
description: Ismerje meg, hogy egy SQL database és az SQL Data Warehouse-tűzfal konfigurálása a kiszolgálószintű tűzfalszabályok kezelésére, és SQL-adatbázishoz adatbázisszintű tűzfalszabályok konfigurálása.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 4f6c98533a2ab1289ca5f1da25c44fe1a77a983c
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353665"
---
# <a name="azure-sql-database-and-sql-data-warehouse-firewall-rules"></a>Az Azure SQL Database és az SQL Data Warehouse tűzfalszabályok

A Microsoft Azure [SQL Database](sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) adja meg az Azure és egyéb internetalapú alkalmazások relációsadatbázis-szolgáltatás. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.

> [!NOTE]
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>A virtuális hálózati szabályok alternatív IP-szabályok

IP-szabályokat, valamint a tűzfal is kezeli *virtuális hálózati szabályok*. A virtuális hálózati szabályok a virtuális hálózati Szolgáltatásvégpontok alapulnak. A virtuális hálózati szabályok bizonyos esetekben IP-szabályok használata előnyösebb lehet. További tudnivalókért lásd: [virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Áttekintés

Kezdetben a tűzfal az Azure SQL Server összes Transact-SQL-hozzáférését blokkolja. Az Azure SQL server használatának megkezdéséhez, egy vagy több kiszolgálószintű tűzfalszabályt, amely engedélyezi az Azure SQL Serverhez való hozzáférést kell adnia. A tűzfalszabályok segítségével adja meg, hogy az internet felől mely IP-címtartományok engedélyezettek, valamint hogy az Azure-alkalmazások megpróbálhatnak-e csatlakozni az Azure SQL Serverhez.

Ha az Azure SQL Serveren csak egyetlen adatbázishoz szeretne hozzáférést biztosítani, akkor az adott adatbázishoz egy adatbázisszintű szabályt kell létrehoznia. Adjon meg egy olyan IP-címtartományt az adatbázis tűzfalszabályához, amely a kiszolgálószintű tűzfalszabályban megadott IP-címtartományon kívül van, és ügyeljen arra, hogy az ügyfél IP-címe az adatbázisszintű szabályban megadott tartományon belülre essen.

> [!IMPORTANT]
> SQL Data Warehouse csak támogatja a kiszolgálószintű tűzfalszabályok és adatbázisszintű tűzfalszabályok nem támogatja.

Az internetről és az Azure-ból érkező kapcsolódási kísérleteknek először át kell jutniuk a tűzfalon, mielőtt elérnék az Azure SQL Servert vagy az SQL Database-t, ahogyan az a következő ábrán látható:

   ![A tűzfal-konfigurációt bemutató ábra.][1]

- **Kiszolgálószintű tűzfalszabályok:**

  Ezek a szabályok hozzáférést biztosítanak ügyfelek számára a teljes Azure SQL Serverhez, azaz lévő összes adatbázis az egyazon logikai kiszolgálón. Ezek a szabályok a **fő** adatbázisban vannak tárolva. A kiszolgálószintű tűzfalszabályok a portálon vagy Transact-SQL utasításokkal konfigurálhatók. Az Azure Portal vagy a PowerShell használatával csak az előfizetés tulajdonosa vagy az előfizetés közreműködői hozhatnak létre kiszolgálószintű tűzfalszabályokat. Ha kiszolgálószintű tűzfalszabályt kíván létrehozni a Transact-SQL használatával, akkor kiszolgálószintű fő bejelentkezőként vagy az Azure Active Directory rendszergazdájaként kell csatlakoznia az SQL Database-példányhoz (ez egyben azt is jelenti, hogy először kiszolgálószintű tűzfalszabályt kell létrehoznia egy Azure-szintű engedélyekkel rendelkező felhasználóval).

- **Adatbázisszintű tűzfalszabályok:**

  Ezek a szabályok hozzáférést biztosítanak ügyfelek számára az egyazon logikai kiszolgálón található bizonyos (biztonságos) adatbázisokhoz. Ezek a szabályok az egyes adatbázisok hozhat létre (beleértve a **fő** adatbázis) és az egyes adatbázisokban tárolódnak. Adatbázisszintű tűzfalszabályok a master és a felhasználói adatbázisok csak is létrehozhatók és kezelhetők a Transact-SQL-utasítások használatával, és csak az első kiszolgálószintű tűzfalszabály konfigurálása után. Ha egy olyan IP-címtartományt ad meg az adatbázisszintű tűzfalszabályban, amely kívül esik a kiszolgálószintű tűzfalszabályban megadott tartományon, akkor csak az adatbázisszinten megadott tartományba eső IP-címekkel rendelkező ügyfelek érhetik el az adatbázist. Egy adatbázishoz legfeljebb 128 adatbázisszintű tűzfalszabály adható meg. Adatbázisszintű tűzfalszabályok konfigurálásáról további információkért tekintse meg később a jelen példában című cikket, és tekintse meg [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Ajánlás

A Microsoft azt javasolja, amikor csak lehetséges, a biztonságot, és az adatbázis hordozhatóságának adatbázisszintű tűzfal-szabályok használatával. Kiszolgálószintű tűzfalszabályokat használhat a rendszergazdákra vonatkozóan, vagy ha több, azonos hozzáférési követelményt támasztó adatbázissal rendelkezik, és nem szeretne időt tölteni az egyes adatbázisok egyenként való konfigurálásával.

> [!Important]
> Windows Azure SQL Database támogatja a legfeljebb 128 tűzfalszabály.
> [!Note]
> Az üzletmenet folytonossága és a hordozható adatbázisok közötti kapcsolatról [a vészhelyreállítás hitelesítési követelményeit](sql-database-geo-replication-security-config.md)ismertető cikkből tájékozódhat.

### <a name="connecting-from-the-internet"></a>Csatlakozás az internetről

Amikor egy számítógép megpróbál csatlakozni az adatbázis-kiszolgálóhoz az internetről, a tűzfal először az adatbázisszintű tűzfalszabályoknak megfelelően ellenőrzi a kérés eredeti IP-címét, a kapcsolati kérésben szereplő adatbázisra vonatkozóan:

- Ha a kérés IP-címe a adatbázisszintű tűzfalszabályokban megadott tartományok egyikében található, a tűzfal engedélyezi a csatlakozást a szabályt tartalmazó SQL-adatbázishoz.
- Ha a kérés IP-címe nem található meg az adatbázisszintű tűzfalszabályokban megadott tartományok egyikében sem, a tűzfal ellenőrzi a kiszolgálószintű tűzfalszabályokat. Ha a kérés IP-címe a kiszolgálószintű tűzfalszabályokban megadott tartományok egyikében található, a tűzfal engedélyezi a csatlakozást. A kiszolgálószintű tűzfalszabályok az összes SQL-adatbázisra érvényesek az Azure SQL Serveren.  
- Ha a kérés IP-címe az adatbázisszintű vagy kiszolgálószintű tűzfalszabályok megadott tartományok nem esik, a csatlakozási kísérlet meghiúsul.

> [!NOTE]
> Ha a helyi számítógépről szeretné elérni az Azure SQL Database-t, akkor ellenőrizze, hogy a hálózaton és a helyi számítógépen lévő tűzfal engedélyezi-e a kimenő kommunikációt az 1433-as TCP-porton keresztül.

### <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból

Annak engedélyezéséhez, hogy az Azure-alkalmazások csatlakozhassanak az Azure SQL Serverhez, engedélyezni kell az Azure-kapcsolatokat. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Ha a csatlakozási kísérlet nem engedélyezett, a kérés nem éri el az Azure SQL Database-kiszolgálót.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.

## <a name="creating-and-managing-firewall-rules"></a>Tűzfal-szabályok létrehozását és kezelését

Az első kiszolgálószintű tűzfalbeállítás hozható létre a [az Azure portal](https://portal.azure.com/) vagy programozott módon [Azure PowerShell-lel](https://docs.microsoft.com/powershell/module/azurerm.sql), [Azure CLI-vel](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create), vagy a [ REST API-val](https://docs.microsoft.com/rest/api/sql/firewallrules/firewallrules_createorupdate). A további kiszolgálószintű tűzfalszabályok ugyanezekkel a módszerekkel, vagy a Transact-SQL-lel hozhatók létre és kezelhetők.

> [!IMPORTANT]
> Adatbázisszintű tűzfalszabályok csak hozhatók létre, és Transact-SQL használatával kezelhetők.

A teljesítmény javítása érdekében a kiszolgálószintű tűzfalszabályokat átmenetileg adatbázisszinten is gyorsítótárazza a rendszer. A gyorsítótár frissítésével kapcsolatban lásd: [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Használhat [SQL Database naplózási szolgáltatásával](sql-database-auditing.md) kiszolgálószintű és adatbázisszintű tűzfalszabályokkal módosítások naplózását.

## <a name="manage-firewall-rules-using-the-azure-portal"></a>Tűzfalszabályok kezelése az Azure portal használatával

A kiszolgálószintű tűzfalszabályok beállítása az Azure Portalon, vagy megnyithatja az Áttekintés oldal az Azure SQL database vagy az Áttekintés oldal az Azure Database logikai kiszolgáló.

> [!TIP]
> Foglalkozó oktatóanyagért lásd: [létrehozása az Azure portal használatával adatbázis](sql-database-get-started-portal.md).

### <a name="from-database-overview-page"></a>Az adatbázis áttekintő oldala

1. Egy kiszolgálószintű tűzfalszabályt az adatbázis áttekintő oldala, kattintson a **kiszolgálótűzfal beállítása** az eszköztáron az alábbi képen látható módon: A **tűzfalbeállítások** az SQL Database-kiszolgálóhoz tartozó lapon Megnyílik a.

      ![kiszolgálói tűzfalszabály](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Kattintson a **ügyfél IP-cím hozzáadása** gombra az eszköztárban, a számítógép IP-cím hozzáadásához jelenleg használ, és kattintson a **mentése**. A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez.

      ![kiszolgálótűzfal-szabály beállítása](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>A kiszolgáló Áttekintés lapján

Megnyílik a kiszolgáló áttekintő oldala, amelyen látható a teljes kiszolgálónév (például **mynewserver20170403.database.windows.net**) és a további lehetőségeket biztosít.

1. Kattintson a kiszolgáló Áttekintés lapján adja meg a kiszolgálói szintű szabályt, **tűzfal** a bal oldali menüben, a beállítások:

2. Kattintson a **ügyfél IP-cím hozzáadása** gombra az eszköztárban, a számítógép IP-cím hozzáadásához jelenleg használ, és kattintson a **mentése**. A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez.

## <a name="manage-firewall-rules-using-transact-sql"></a>Tűzfalszabályok kezelése a Transact-SQL használatával

| Katalógusnézet vagy tárolt eljárás | Szint | Leírás |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Kiszolgáló |Az aktuális kiszolgálószintű tűzfalszabályok megjelenítése |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok eltávolítása |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Adatbázis |Az aktuális adatbázisszintű tűzfalszabályok megjelenítése |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Adatbázis |Adatbázisszintű tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Adatbázisok |Adatbázisszintű tűzfalszabályok eltávolítása |

Tekintse át a meglévő szabályokat, egy IP-címtartományt a Contoso kiszolgálón engedélyezése és törlése egy tűzfalszabályt az alábbi példák:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Ezután adjon hozzá egy tűzfalszabályt.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Kiszolgálószintű tűzfalszabály törléséhez futtassa az sp_delete_firewall_rule tárolt eljárást. A következő példa a ContosoFirewallRule nevű szabályt törli:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-firewall-rules-using-azure-powershell"></a>Tűzfalszabályok kezelése az Azure PowerShell használatával

| Parancsmag | Szint | Leírás |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |Kiszolgáló |Az aktuális kiszolgálószintű tűzfalszabályokat adja vissza |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |Kiszolgáló |Új kiszolgálószintű tűzfalszabály létrehozása |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |Kiszolgáló |Meglévő kiszolgálószintű tűzfalszabály tulajdonságainak frissítése |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok eltávolítása |

Az alábbi mintakód egy kiszolgálószintű tűzfalszabályt, PowerShell-lel:

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> PowerShell példák a környezetében, a rövid útmutató: [DB létrehozása – PowerShell](sql-database-powershell-samples.md) és [egy önálló adatbázis létrehozása és a egy PowerShell-lel tűzfalszabály konfigurálása](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-firewall-rules-using-azure-cli"></a>Tűzfalszabályok kezelése az Azure CLI használatával

| Parancsmag | Szint | Leírás |
| --- | --- | --- |
|[az sql server firewall-rule létrehozása](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Kiszolgáló|Létrehoz egy kiszolgálói tűzfalszabályt|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Kiszolgáló|A tűzfalszabályokat a kiszolgáló listákat|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Kiszolgáló|A részletek a tűzfalszabályok megjelenítése|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Kiszolgáló|A tűzfalszabályok frissítése|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Kiszolgáló|Tűzfalszabály törlése|

Az alábbi példa egy kiszolgálószintű tűzfalszabályt az Azure CLI használatával állítja be:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Azure CLI-vel a környezetében, a gyors üzembe helyezési, találhat példát [DB létrehozása – Azure CLI-vel](sql-database-cli-samples.md) és [egy önálló adatbázis létrehozása, és konfiguráljon egy tűzfalszabályt az Azure CLI használatával](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-firewall-rules-using-rest-api"></a>Tűzfalszabályok kezelése REST API használatával

| API | Szint | Leírás |
| --- | --- | --- |
| [List Firewall Rules](https://docs.microsoft.com/rest/api/sql/firewallrules/firewallrules_listbyserver) |Kiszolgáló |Az aktuális kiszolgálószintű tűzfalszabályok megjelenítése |
| [Tűzfalszabály létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/firewallrules/firewallrules_createorupdate) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok létrehozása vagy frissítése |
| [Tűzfalszabály törlése](https://docs.microsoft.com/rest/api/sql/firewallrules/firewallrules_delete) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok eltávolítása |
| [Tűzfal-szabályok beolvasása](https://docs.microsoft.com/rest/api/sql/firewallrules/firewallrules_get) | Kiszolgáló | Kiszolgálószintű tűzfalszabályok beolvasása |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabályt és egy adatbázisszintű tűzfalszabályt

K. Egy adatbázis felhasználói lehet egy másik adatbázisból elszigetelt?
Ha igen, hozzáférés és adatbázisszintű tűzfalszabályok használatát. Ezzel elkerülhető a kiszolgálószintű tűzfal-szabályok, amelyek engedélyezze a hozzáférést a tűzfalon az összes adatbázis, a védelem mélysége csökkentése használata.

K. Szükség van az IP-címet a felhasználók összes adatbázishoz hozzáférést?
Kiszolgálószintű tűzfalszabályok használatával csökkenthető az, hogy hányszor konfigurálnia kell a tűzfalszabályokat.

K. Az olyan személyt vagy csapatot a tűzfal-szabályok konfigurálása van hozzáférése az Azure portal, PowerShell vagy a REST API használatával?
Kiszolgálószintű tűzfalszabályokat kell használnia. Adatbázisszintű tűzfalszabályok a Transact-SQL használatával csak konfigurálható.  

K. Az olyan személyt vagy csapatot konfigurálja a tilos a magas szintű engedéllyel rendelkező adatbázisszintű tűzfalszabályok?
Kiszolgálószintű tűzfalszabályok használja. Adatbázisszintű tűzfalszabályok a Transact-SQL használatával konfigurálása szükséges `CONTROL DATABASE` engedéllyel az adatbázis szintjén.  

K. A személy vagy csoport konfigurálásához, vagy a tűzfalszabályok naplózását központilag sokak számára a tűzfalszabályok kezelésére van (például 100 db) adatbázisok?
Ez a beállítás attól függ, az igények és a környezet. Lehet, hogy a kiszolgálószintű tűzfalszabályok beállítása egyszerűbb, de a parancsfájl-kezelési konfigurálható szabályok az adatbázis szintjén. És még akkor is, ha a kiszolgálószintű tűzfalszabályokat használ, szükség lehet az adatbázis-tűzfalszabályokat, tekintse meg, ha a naplózandó rendelkező felhasználók `CONTROL` engedéllyel hozott létre adatbázisszintű tűzfalszabályokat.

K. Használható mindkét kiszolgáló- és adatbázisszintű tűzfalszabályok vegyesen?
Igen. Néhány felhasználó, például a rendszergazdákat előfordulhat, hogy a kiszolgálószintű tűzfalszabályokat kell. Más felhasználókkal, például egy adatbázis-alkalmazást, előfordulhat, hogy kell adatbázisszintű tűzfalszabályokat.

## <a name="troubleshooting-the-database-firewall"></a>Az adatbázistűzfal hibaelhárítása

A következő szempontokat vegye figyelembe, ha a Microsoft Azure SQL Database szolgáltatáshoz való hozzáférése nem a várt módon működik:

- **Helyi tűzfal konfigurációja:**

  A számítógép férjenek hozzá az Azure SQL Database, szükség lehet hozzon létre egy tűzfalkivételt a számítógépén az 1433-as TCP-porton. Ha az Azure-felhő határain belül létesít kapcsolatokat, előfordulhat, hogy további portokat is meg kell nyitnia. További információkért lásd: a **SQL Database: portjaival** szakaszában [az ADO.NET 4.5 és az SQL Database 1433-Ason túli](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Hálózati címfordítás (NAT):**

  NAT miatt a számítógépe által az Azure SQL adatbázishoz való csatlakozáshoz használt IP-cím nem feltétlenül azonos az IP-cím látható a számítógép IP-konfigurációs beállításokat. A számítógép által az Azure-hoz való csatlakozáshoz használt IP-cím megtekintéséhez jelentkezzen be a portálra, és lépjen az adatbázist futtató kiszolgáló **Konfigurálás** lapjára. Az **Engedélyezett IP-címek** szakasz alatt jelenik meg az **Ügyfél aktuális IP-címe**. Kattintson a **Hozzáadás** lehetőségre az **Engedélyezett IP-címek** között, ha szeretné engedélyezni a számítógép számára a kiszolgáló elérését.

- **Az engedélyezési lista módosításai nem lépnek érvénybe még:**

  Akár öt perces késleltetés a Azure SQL Database-tűzfal konfigurálásának életbelépéséhez módosítások lehet.

- **A bejelentkezés nem engedélyezett, vagy helytelen jelszó lett megadva:**

  Ha egy bejelentkezés nem rendelkezik engedélyekkel az Azure SQL Database-kiszolgálón, vagy a jelszó helytelen, a rendszer megtagadja az Azure SQL Database-kiszolgálóhoz. Egy tűzfalbeállítás létrehozása lehetővé teszi az ügyfelek számára a kiszolgálóhoz való csatlakozás megkísérlését, azonban minden egyes ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat. A bejelentkezések előkészítésével kapcsolatos további információkért lásd: [kezelése adatbázisok, bejelentkezések és felhasználók az Azure SQL Database](sql-database-manage-logins.md).

- **Dinamikus IP-cím:**

  Ha internetkapcsolat és a dinamikus IP-címkezelés és problémákat okoz a tűzfalon, próbálkozzon az alábbi megoldások valamelyikét:
  
  - Kérje el az internetszolgáltatójától az Azure SQL Database-kiszolgáló eléréséhez használt ügyfélszámítógépeihez társított IP-címtartományt, majd adja meg ezt az IP-címtartományt egy tűzfalszabályként.
  - Állítson be statikus IP-címeket az ügyfélszámítógépei számára, majd adja meg az IP-címeket tűzfalszabályokként.

## <a name="next-steps"></a>További lépések

- Egy adatbázis és a egy kiszolgálószintű tűzfalszabály létrehozása a gyors üzembe helyezési, lásd: [hozzon létre egy Azure SQL database](sql-database-get-started-portal.md).
- Ha nyílt forráskódú vagy külső alkalmazásokból szeretne kapcsolódni az Azure SQL Database-hez, lásd az [SQL Database gyors üzembe helyezési ügyfélkódmintáival](https://msdn.microsoft.com/library/azure/ee336282.aspx) foglalkozó cikket.
- A további portok megnyitásához szükséges információkért lásd: a **SQL Database: portjaival** szakaszában [az ADO.NET 4.5 és az SQL Database 1433-Ason túli](sql-database-develop-direct-route-ports-adonet-v12.md)
- Azure SQL Database biztonsági áttekintését lásd: [az adatbázis biztonságossá tétele](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
