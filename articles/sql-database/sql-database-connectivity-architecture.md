---
title: Kapcsolati architektúra
description: Ez a dokumentum ismerteti az Azure SQL-kapcsolati architektúráját az Azure-on belüli vagy az Azure-on kívüli adatbázis-kapcsolatokhoz.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/02/2019
ms.openlocfilehash: b3b735f7ee644bb017756f3d6378e625fa66d448
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280790"
---
# <a name="azure-sql-connectivity-architecture"></a>Azure SQL-kapcsolat architektúrája

Ez a cikk ismerteti a Azure SQL Database és SQL Data Warehouse kapcsolati architektúrát, valamint azt, hogy a különböző összetevők hogyan irányítják át a forgalmat az Azure SQL-példányra. Ezek a kapcsolati összetevők az Azure-ból és az Azure-on kívülről csatlakozó ügyfelektől a Azure SQL Database vagy SQL Data Warehouse felé irányuló hálózati forgalmat irányítják. Ez a cikk parancsfájl-mintákat is biztosít a kapcsolódás módjának módosításához, valamint az alapértelmezett kapcsolati beállítások módosításával kapcsolatos szempontokat.

## <a name="connectivity-architecture"></a>Kapcsolati architektúra

Az alábbi ábra a Azure SQL Database kapcsolati architektúrájának áttekintését nyújtja.

![architektúra – áttekintés](./media/sql-database-connectivity-architecture/connectivity-overview.png)

A következő lépések azt ismertetik, hogyan történik a kapcsolatok létrehozása egy Azure SQL Database-adatbázisba:

- Az ügyfelek csatlakoznak az átjáróhoz, amely nyilvános IP-címmel rendelkezik, és az 1433-es porton figyel.
- Az átjáró a hatályos kapcsolódási házirendtől függően átirányítja vagy proxyként továbbítja a forgalmat a megfelelő adatbázis-fürtre.
- Az adatbázis-fürt forgalmát a megfelelő Azure SQL Database-adatbázisba továbbítja a rendszer.

## <a name="connection-policy"></a>Kapcsolatok házirendje

A Azure SQL Database a következő három lehetőséget támogatja egy SQL Database-kiszolgáló kapcsolatbiztonsági házirendjének beállításához:

- **Átirányítás (ajánlott):** Az ügyfelek közvetlen kapcsolatot létesít az adatbázist futtató csomóponttal. A kapcsolat engedélyezéséhez az ügyfeleknek engedélyezniük kell a kimenő tűzfalszabályok használatát a régió összes Azure-beli IP-címére a (z) 11000-11999-es portokhoz tartozó NSG használatával, nem csak a 1433 [-es](../virtual-network/security-overview.md#service-tags) porton található Azure SQL Database átjáró IP-címeit. Mivel a csomagok közvetlenül az adatbázishoz mennek, a késés és az átviteli sebesség jobb teljesítményt biztosít.
- **Proxy:** Ebben a módban az összes kapcsolat proxyn keresztül az Azure SQL Database-átjárón keresztül történik. A kapcsolat engedélyezéséhez az ügyfélnek olyan kimenő tűzfalszabályok szükségesek, amelyek csak a Azure SQL Database átjáró IP-címeit engedélyezik (általában két IP-cím régiónként). Ha ezt a módot választja, a számítási feladatok természetétől függően nagyobb késést és alacsonyabb átviteli sebességet eredményezhet. Javasoljuk, hogy a `Redirect` kapcsolati házirendet a legalacsonyabb késés és a legmagasabb átviteli sebesség érdekében a `Proxy` kapcsolati szabályzaton keresztül.
- **Alapértelmezett:** Ez a kapcsolódási házirend a létrehozás után minden kiszolgálón érvényben van, kivéve, ha explicit módon módosítja a kapcsolódási szabályzatot `Proxy` vagy `Redirect`. A hatályos szabályzat attól függ, hogy a kapcsolatok az Azure-ból (`Redirect`) vagy az Azure-on kívülről (`Proxy`) származnak-e.

## <a name="connectivity-from-within-azure"></a>Kapcsolat az Azure-on belül

Ha az Azure-ból csatlakozik, a kapcsolatok alapértelmezés szerint `Redirect` kapcsolati házirenddel rendelkeznek. `Redirect` házirend azt jelenti, hogy a TCP-munkamenetnek az Azure SQL Database-adatbázishoz való létrehozása után az ügyfél-munkamenetet a rendszer átirányítja a megfelelő adatbázis-fürtre, és a célként megadott virtuális IP-címet a Azure SQL Database átjárótól a fürt. Ezt követően az összes további csomag közvetlenül a fürtre áramlik, és megkerüli a Azure SQL Database-átjárót. A következő ábra szemlélteti ezt a forgalmat.

![architektúra – áttekintés](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Kapcsolódás az Azure-on kívülről

Ha az Azure-on kívülről csatlakozik, a kapcsolatok alapértelmezés szerint `Proxy` kapcsolati házirenddel rendelkeznek. A `Proxy` szabályzata azt jelenti, hogy a TCP-munkamenet a Azure SQL Database átjárón és az összes további, az átjárón keresztüli adatforgalomban van létrehozva. A következő ábra szemlélteti ezt a forgalmat.

![architektúra – áttekintés](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Emellett a 14000-14999-as porton keresztül is engedélyezheti a [csatlakozást a DAC](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac) használatával


## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database-átjárók IP-címei

Az alábbi táblázat az átjárók régió szerinti IP-címeit sorolja fel. Azure SQL Databasehoz való kapcsolódáshoz engedélyeznie kell a hálózati forgalmat, hogy & a régió **összes** átjáróján.

A forgalom áttelepítésének részletei az egyes régiókban lévő új átjárók számára a következő cikkben olvashatók: [Azure SQL Database forgalom áttelepítése újabb átjáróra](sql-database-gateway-migration.md)


| Régiónév          | Átjáró IP-címei |
| --- | --- |
| Ausztrália középső régiója    | 20.36.105.0 |
| Ausztráliai Central2   | 20.36.113.0 |
| Kelet-Ausztrália       | 13.75.149.87, 40.79.161.1 |
| Délkelet-Ausztrália | 191.239.192.109, 13.73.109.251 |
| Dél-Brazília         | 104.41.11.5, 191.233.200.14 |
| Közép-Kanada       | 40.85.224.249      |
| Kelet-Kanada          | 40.86.226.166      |
| USA középső régiója           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Kelet-Kína           | 139.219.130.35     |
| Kína 2. keleti régiója         | 40.73.82.1         |
| Észak-Kína          | 139.219.15.17      |
| Kína 2. északi régiója        | 40.73.50.0         |
| Kelet-Ázsia            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| USA keleti régiója              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| USA 2. keleti régiója            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Közép-Franciaország       | 40.79.137.0, 40.79.129.1 |
| Közép-Németország      | 51.4.144.100       |
| Kelet-Észak-Németország   | 51.5.144.179       |
| Közép-India        | 104.211.96.159     |
| Dél-India          | 104.211.224.146    |
| Nyugat-India           | 104.211.160.80     |
| Kelet-Japán           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Nyugat-Japán           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Korea középső régiója        | 52.231.32.42       |
| Korea déli régiója          | 52.231.200.86      |
| USA északi középső régiója     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Észak-Európa         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Dél-Afrika északi régiója   | 102.133.152.0      |
| Dél-Afrika nyugati régiója    | 102.133.24.0       |
| USA déli középső régiója     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Délkelet-Ázsia      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Egyesült Arab Emírségek középső régiója          | 20.37.72.64        |
| Egyesült Arab Emírségek északi régiója            | 65.52.248.0        |
| Az Egyesült Királyság déli régiója             | 51.140.184.11      |
| Az Egyesült Királyság nyugati régiója              | 51.141.8.11        |
| USA nyugati középső régiója      | 13.78.145.25       |
| Nyugat-Európa          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| USA nyugati régiója              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| USA nyugati régiója, 2.            | 13.66.226.202      |
|                      |                    |

## <a name="change-azure-sql-database-connection-policy"></a>Azure SQL Databasei kapcsolatok házirendjének módosítása

Azure SQL Database-kiszolgáló Azure SQL Database-kapcsolódási házirendjének módosításához használja a [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) parancsot.

- Ha a kapcsolódási házirend értéke `Proxy`, az összes hálózati csomag a Azure SQL Database átjárón keresztül áramlik. Ebben a beállításban engedélyezni kell a kimenő értéket csak az Azure SQL Database átjáró IP-címére. `Proxy` beállításának használata több késéssel rendelkezik, mint a `Redirect`beállítása.
- Ha a kapcsolódási szabályzata `Redirect`, az összes hálózati csomag közvetlenül az adatbázis-fürthöz áramlik. Ennél a beállításnál engedélyeznie kell a kimenő és a több IP-cím használatát.

## <a name="script-to-change-connection-settings-via-powershell"></a>A kapcsolódási beállítások PowerShell használatával történő módosítására szolgáló parancsfájl

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A következő parancsfájlhoz a [Azure PowerShell modul](/powershell/azure/install-az-ps)szükséges.

A következő PowerShell-szkript bemutatja, hogyan módosíthatja a kapcsolódási szabályzatot.

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

## <a name="script-to-change-connection-settings-via-azure-cli"></a>A kapcsolódási beállítások Azure CLI-n keresztüli módosítására szolgáló parancsfájl

> [!IMPORTANT]
> Ehhez a parancsfájlhoz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)szükséges.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI bash-rendszerhéjban

> [!IMPORTANT]
> Ehhez a parancsfájlhoz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)szükséges.

A következő CLI-szkript bemutatja, hogyan módosíthatja a kapcsolódási szabályzatot egy bash-rendszerhéjban.

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

> [!IMPORTANT]
> Ehhez a parancsfájlhoz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)szükséges.

A következő CLI-szkript bemutatja, hogyan lehet módosítani a Windows-parancssorból a kapcsolódási szabályzatot (az Azure CLI telepítve van).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Következő lépések

- Az Azure SQL Database-kiszolgálók Azure SQL Database-kapcsolódási szabályzatának módosításáról további információt a következő témakörben talál: [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- A ADO.NET 4,5-as vagy újabb verziót használó ügyfelek Azure SQL Database-kapcsolatainak működéséről további információért lásd: [a 1433-nál nagyobb portok a ADO.NET 4,5](sql-database-develop-direct-route-ports-adonet-v12.md)-ban.
- Az alkalmazások fejlesztésének általános áttekintését lásd: [SQL Database alkalmazásfejlesztés áttekintése](sql-database-develop-overview.md).
