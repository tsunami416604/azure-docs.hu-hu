---
title: A Azure SQL Database és az adatraktár csatlakozási beállításai
description: Ez a dokumentum ismerteti a TLS-verzió választása és a proxy és az átirányítás az Azure SQL-re vonatkozó beállítását
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79366087"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL-kapcsolati beállítások
> [!NOTE]
> Ez a cikk az Azure SQL Serverre vonatkozik, valamint az Azure SQL Serveren létrehozott SQL Database és SQL Data Warehouse adatbázisokra is. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik **Azure SQL Database felügyelt példányra**

Ez a cikk azokat a beállításokat ismerteti, amelyek a kiszolgáló szintjén vezérlik Azure SQL Database kapcsolatát. Ezek a beállítások a kiszolgálóhoz társított **összes** SQL Databasera és SQL Data Warehouse-adatbázisra érvényesek.

> [!NOTE]
> A beállítások alkalmazása után **azonnal érvénybe** lépnek, és az ügyfelek számára a kapcsolatok elvesztését eredményezhetik, ha nem felelnek meg az egyes beállítások követelményeinek.

A kapcsolódási beállítások a **tűzfalak és a virtuális hálózatok** panelen érhetők el, ahogy az alábbi képernyőképen is látható:

 ![A kapcsolati beállítások képernyőképe][1]


## <a name="deny-public-network-access"></a>Nyilvános hálózati hozzáférés megtagadása
A Azure Portal, ha a **nyilvános hálózati hozzáférés megtagadása** beállítás az **Igen**értékre van állítva, csak a magánhálózati végpontokon keresztüli kapcsolatok engedélyezettek. Ha ez a beállítás **nem**értékre van állítva, az ügyfelek a privát vagy a nyilvános végponton keresztül csatlakozhatnak.

Miután beállította a **nyilvános hálózati hozzáférés megtagadását** **, a**nyilvános végponttal rendelkező ügyfelektől érkező bejelentkezési kísérletek sikertelenek lesznek a következő hibával:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Nyilvános hálózati hozzáférés módosítása a PowerShell használatával
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A következő parancsfájlhoz a [Azure PowerShell modul](/powershell/azure/install-az-ps)szükséges.

A következő PowerShell `Get` -szkript bemutatja, hogyan `Set` és a **nyilvános hálózati hozzáférés** tulajdonságot a logikai kiszolgáló szintjén:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Nyilvános hálózati hozzáférés módosítása a CLI-n keresztül
> [!IMPORTANT]
> Az ebben a szakaszban szereplő összes parancsfájlhoz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)szükséges.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI bash-rendszerhéjban
A következő CLI-szkript bemutatja, hogyan változtathatók meg a **nyilvános hálózati hozzáférés** egy bash-rendszerhéjban:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Kapcsolatok házirendje
A [kapcsolati házirend](sql-database-connectivity-architecture.md#connection-policy) határozza meg, hogyan kapcsolódnak az ügyfelek az Azure SQL Serverhoz. 

## <a name="change-connection-policy-via-powershell"></a>A kapcsolatbiztonsági szabályzat módosítása a PowerShell használatával
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A következő parancsfájlhoz a [Azure PowerShell modul](/powershell/azure/install-az-ps)szükséges.

A következő PowerShell-szkript bemutatja, hogyan módosíthatja a kapcsolódási szabályzatot a PowerShell használatával:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>A kapcsolatkérelem-házirend módosítása az Azure CLI-n keresztül
> [!IMPORTANT]
> Az ebben a szakaszban szereplő összes parancsfájlhoz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)szükséges.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI bash-rendszerhéjban
A következő CLI-szkript bemutatja, hogyan módosíthatja a kapcsolódási szabályzatot egy bash-rendszerhéjban: 

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI Windows-parancssorból
A következő CLI-szkript bemutatja, hogyan lehet módosítani a Windows-parancssorból a kapcsolódási szabályzatot (az Azure CLI telepítve van).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>További lépések
- A Azure SQL Database kapcsolatok működésének áttekintését az [Azure SQL-kapcsolati architektúrában](sql-database-connectivity-architecture.md) tekintheti meg
- Az Azure SQL Database-kiszolgálók Azure SQL Database-kapcsolódási szabályzatának módosításáról további információt a következő témakörben talál: [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
