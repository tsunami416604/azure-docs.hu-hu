---
title: Azure SQL Database és SQL Data Warehouse kapcsolati architektúra | Microsoft Docs
description: Ez a dokumentum ismerteti az Azure SQL-kapcsolati architektúráját az Azure-on belüli vagy az Azure-on kívüli adatbázis-kapcsolatokhoz.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/02/2019
ms.openlocfilehash: 0e9bdb22baed74ef948f369f9259784900486860
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569087"
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

- **Redirect (recommended):** Az ügyfelek közvetlen kapcsolatot létesít az adatbázist futtató csomóponttal. A kapcsolat engedélyezéséhez az ügyfeleknek engedélyezniük kell a kimenő tűzfalszabályok használatát a régió összes Azure-beli IP-címére a hálózati biztonsági csoportok (NSG) és a (z) 11000-11999-es portokon keresztül, nem csak a 1433 [-es](../virtual-network/security-overview.md#service-tags)porton található átjáró IP-címeinek Azure SQL Database. Mivel a csomagok közvetlenül az adatbázishoz mennek, a késés és az átviteli sebesség jobb teljesítményt biztosít.
- **Proxy** Ebben a módban az összes kapcsolat proxyn keresztül az Azure SQL Database-átjárón keresztül történik. A kapcsolat engedélyezéséhez az ügyfélnek olyan kimenő tűzfalszabályok szükségesek, amelyek csak a Azure SQL Database átjáró IP-címeit engedélyezik (általában két IP-cím régiónként). Ha ezt a módot választja, a számítási feladatok természetétől függően nagyobb késést és alacsonyabb átviteli sebességet eredményezhet. A kapcsolati szabályzatot a legalacsonyabb késés `Proxy` és a legmagasabb átviteli sebesség érdekében javasoljuk `Redirect` .
- **Alapértelmezett** Ez a kapcsolódási házirend a létrehozás után minden kiszolgálón érvényben van, kivéve, ha explicit módon módosítja a kapcsolódási `Redirect`házirendet `Proxy` a vagy a rendszerre. A hatályos szabályzat attól függ, hogy a kapcsolatok az Azure`Redirect`-ból vagy az`Proxy`Azure-on kívülről () származnak-e.

## <a name="connectivity-from-within-azure"></a>Kapcsolat az Azure-on belül

Ha az Azure-on belülről csatlakozik, a kapcsolatokhoz alapértelmezés `Redirect` szerint csatlakozási házirend tartozik. `Redirect` Az azt jelenti, hogy a TCP-munkamenet az Azure SQL Database-adatbázishoz való létrehozása után a rendszer átirányítja az ügyfél-munkamenetet a megfelelő adatbázis-fürtre, és a célként megadott virtuális IP-címet a Azure SQL Database átjárótól a fürt. Ezt követően az összes további csomag közvetlenül a fürtre áramlik, és megkerüli a Azure SQL Database-átjárót. A következő ábra szemlélteti ezt a forgalmat.

![architektúra – áttekintés](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Kapcsolódás az Azure-on kívülről

Ha az Azure-on kívülről csatlakozik, a kapcsolatok alapértelmezés `Proxy` szerint csatlakozási házirendtel rendelkeznek. A szabályzat `Proxy` azt jelenti, hogy a TCP-munkamenet a Azure SQL Database átjárón és az összes további, az átjárón keresztüli adatcsomagon keresztül jön. A következő ábra szemlélteti ezt a forgalmat.

![architektúra – áttekintés](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database-átjárók IP-címei

Az alábbi táblázat az átjárók régió szerinti IP-címeit sorolja fel. Azure SQL Databasehoz való kapcsolódáshoz engedélyeznie kell a hálózati forgalmat, hogy & a régió **összes** átjáróján.

A továbbiakban további átjárókat fogunk hozzáadni az egyes régiókban, és kivonják az átjárókat az alábbi táblázat leszerelt átjárójának IP-címe oszlopában. További részletek az alábbi cikkben megadott leszerelési folyamatról: [Azure SQL Database forgalom áttelepítése újabb átjáróra](sql-database-gateway-migration.md)


| Régiónév          | Átjáró IP-címe | Leszerelt átjáró </br> IP-cím| A leszereléssel kapcsolatos megjegyzések | 
| --- | --- | --- | --- |
| Ausztrália középső régiója    | 20.36.105.0 | | |
| Ausztráliai Central2   | 20.36.113.0 | | |
| Kelet-Ausztrália       | 13.75.149.87, 40.79.161.1 | | |
| Délkelet-Ausztrália | 191.239.192.109, 13.73.109.251 | | |
| Dél-Brazília         | 104.41.11.5        |                 | |
| Közép-Kanada       | 40.85.224.249      |                 | |
| Kelet-Kanada          | 40.86.226.166      |                 | |
| USA középső régiója           | 13.67.215.62, 52.182.137.15 | 23.99.160.139 | 2019. szeptember 1. után nincsenek kapcsolatok |
| Kelet-Kína           | 139.219.130.35     |                 | |
| Kelet-Kína 2         | 40.73.82.1         |                 | |
| Észak-Kína          | 139.219.15.17      |                 | |
| Észak-Kína 2        | 40.73.50.0         |                 | |
| Kelet-Ázsia            | 191.234.2.139, 52.175.33.150 |       | |
| East US              | 40.121.158.30, 40.79.153.12 | 191.238.6.43 | 2019. szeptember 1. után nincsenek kapcsolatok |
| USA 2. keleti régiója            | 40.79.84.180, 52.177.185.181, 52.167.104.0 | 191.239.224.107    | 2019. szeptember 1. után nincsenek kapcsolatok |
| Közép-Franciaország       | 40.79.137.0, 40.79.129.1 |           | |
| Közép-Németország      | 51.4.144.100       |                 | |
| Kelet-Észak-Németország   | 51.5.144.179       |                 | |
| Közép-India        | 104.211.96.159     |                 | |
| Dél-India          | 104.211.224.146    |                 | |
| Nyugat-India           | 104.211.160.80     |                 | |
| Kelet-Japán           | 13.78.61.196, 40.79.184.8, 13.78.106.224 | 191.237.240.43 | 2019. szeptember 1. után nincsenek kapcsolatok |
| Nyugat-Japán           | 104.214.148.156, 40.74.100.192 | 191.238.68.11 | 2019. szeptember 1. után nincsenek kapcsolatok |
| Korea középső régiója        | 52.231.32.42       |                 | |
| Korea déli régiója          | 52.231.200.86      |                 | |
| USA északi középső régiója     | 23.96.178.199      | 23.98.55.75     | 2019. szeptember 1. után nincsenek kapcsolatok |
| Észak-Európa         | 40.113.93.91       | 191.235.193.75  | 2019. szeptember 1. után nincsenek kapcsolatok |
| Dél-Afrika északi régiója   | 102.133.152.0      |                 | |
| Dél-Afrika nyugati régiója    | 102.133.24.0       |                 | |
| USA déli középső régiója     | 13.66.62.124       | 23.98.162.75    | 2019. szeptember 1. után nincsenek kapcsolatok |
| Délkelet-Ázsia      | 104.43.15.0        | 23.100.117.95   | 2019. szeptember 1. után nincsenek kapcsolatok |
| Egyesült Arab Emírségek középső régiója          | 20.37.72.64        |                 | |
| Egyesült Arab Emírségek északi régiója            | 65.52.248.0        |                 | |
| Az Egyesült Királyság déli régiója             | 51.140.184.11      |                 | |
| Az Egyesült Királyság nyugati régiója              | 51.141.8.11        |                 | |
| USA nyugati középső régiója      | 13.78.145.25       |                 | |
| Nyugat-Európa          | 40.68.37.158       | 191.237.232.75  | 2019. szeptember 1. után nincsenek kapcsolatok |
| USA nyugati régiója              | 104.42.238.205     | 23.99.34.75     | 2019. szeptember 1. után nincsenek kapcsolatok |
| USA nyugati régiója, 2.            | 13.66.226.202      |                 | |
|                      |                    |                 | |

## <a name="change-azure-sql-database-connection-policy"></a>Azure SQL Databasei kapcsolatok házirendjének módosítása

Azure SQL Database-kiszolgáló Azure SQL Database-kapcsolódási házirendjének módosításához használja a [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) parancsot.

- Ha a kapcsolódási házirend `Proxy`értéke, az összes hálózati csomag a Azure SQL Database átjárón keresztül áramlik. Ebben a beállításban engedélyezni kell a kimenő értéket csak az Azure SQL Database átjáró IP-címére. A beállításának `Proxy` használata több késéssel rendelkezik, `Redirect`mint a beállításai.
- Ha a kapcsolódási házirend beállítása `Redirect`, az összes hálózati csomag közvetlenül az adatbázis-fürtre áramlik. Ennél a beállításnál engedélyeznie kell a kimenő és a több IP-cím használatát.

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

## <a name="next-steps"></a>További lépések

- Az Azure SQL Database-kiszolgálók Azure SQL Database-kapcsolódási szabályzatának módosításáról további információt a következő témakörben talál: [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- A ADO.NET 4,5-as vagy újabb verziót használó ügyfelek Azure SQL Database-kapcsolatainak működéséről további információért lásd: [a 1433-nál nagyobb portok a ADO.NET 4,5](sql-database-develop-direct-route-ports-adonet-v12.md)-ban.
- Az alkalmazások fejlesztésének általános áttekintését lásd: [SQL Database alkalmazásfejlesztés áttekintése](sql-database-develop-overview.md).
