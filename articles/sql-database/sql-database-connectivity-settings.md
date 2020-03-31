---
title: Az Azure SQL Database és az Adattárház kapcsolódási beállításai
description: Ez a dokumentum ismerteti a TLS-verzióválasztást és a Proxy vs. Átirányítási beállítást az Azure SQL-hez
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366087"
---
# <a name="azure-sql-connectivity-settings"></a>Az Azure SQL-kapcsolat beállításai
> [!NOTE]
> Ez a cikk az Azure SQL-kiszolgálóra, valamint az Azure SQL-kiszolgálón létrehozott SQL Database és SQL Data Warehouse adatbázisokra is vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik az **Azure SQL Database felügyelt példányára**

Ez a cikk bemutatja azokat a beállításokat, amelyek a kiszolgáló szintjén szabályozzák az Azure SQL Database-hez való kapcsolódást. Ezek a beállítások a kiszolgálóhoz társított **összes** SQL Database és SQL Data Warehouse adatbázisra vonatkoznak.

> [!NOTE]
> A beállítások alkalmazása után **azonnal érvénybe lépnek,** és kapcsolatvesztést okozhatnak az ügyfelek számára, ha nem felelnek meg az egyes beállítások követelményeinek.

A kapcsolódási beállítások a **tűzfalak és** a virtuális hálózatok panelről érhetők el, amint az az alábbi képernyőképen látható:

 ![Képernyőkép a kapcsolódási beállításokról][1]


## <a name="deny-public-network-access"></a>Nyilvános hálózati hozzáférés megtagadása
Az Azure Portalon, ha a **nyilvános hálózati hozzáférés megtagadása** beállítás **igen,** csak a magán-végpontokon keresztül i kapcsolatok engedélyezettek. Ha ez a beállítás **Nem,** az ügyfelek a privát vagy nyilvános végpont használatával csatlakozhatnak.

A **Nyilvános hálózati hozzáférés megtagadása** **az Igen**beállítás beállítása után a nyilvános végpontot használó ügyfelek bejelentkezési kísérletei a következő hibával sikertelenek lesznek:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Nyilvános hálózati hozzáférés módosítása a PowerShellen keresztül
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A következő parancsfájlhoz az [Azure PowerShell-modul](/powershell/azure/install-az-ps)szükséges.

A következő PowerShell-parancsfájl `Get` `Set` bemutatja a **nyilvános hálózati hozzáférés** tulajdonságot a logikai kiszolgáló szintjén:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Nyilvános hálózati hozzáférés módosítása a CLI-n keresztül
> [!IMPORTANT]
> Ebben a szakaszban minden parancsfájlhoz [az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)szükséges.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI bash rendszerhéjban
A következő CLI-parancsfájl bemutatja, hogyan módosíthatja a **nyilvános hálózati hozzáférést** egy bash rendszerhéjban:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Csatlakozási házirend
[A kapcsolati házirend](sql-database-connectivity-architecture.md#connection-policy) határozza meg, hogy az ügyfelek hogyan csatlakozzanak az Azure SQL Serverhez. 

## <a name="change-connection-policy-via-powershell"></a>Kapcsolati házirend módosítása a PowerShellen keresztül
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A következő parancsfájlhoz az [Azure PowerShell-modul](/powershell/azure/install-az-ps)szükséges.

A következő PowerShell-parancsfájl bemutatja, hogyan módosíthatja a kapcsolatszabályzatot a PowerShell használatával:

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

## <a name="change-connection-policy-via-azure-cli"></a>Csatlakozási szabályzat módosítása az Azure CLI-n keresztül
> [!IMPORTANT]
> Ebben a szakaszban minden parancsfájlhoz [az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)szükséges.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI bash rendszerhéjban
A következő CLI-parancsfájl bemutatja, hogyan módosíthatja a kapcsolatházirendet egy bash rendszerhéjban: 

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI a Windows parancssorából
A következő CLI-parancsfájl bemutatja, hogyan módosíthatja a kapcsolati szabályzatot a Windows parancssorból (az Azure CLI telepítve).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>További lépések
- Az Azure SQL Database kapcsolatműködésének áttekintését az [Azure SQL Connectivity Architecture című témakörben](sql-database-connectivity-architecture.md) találja.
- Az Azure SQL Database-kiszolgáló Azure SQL Database kapcsolati szabályzatának módosításáról a [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)című témakörben talál további információt.

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
