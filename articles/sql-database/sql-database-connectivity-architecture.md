---
title: "Az Azure SQL adatbázis-kapcsolat architektúra |} Microsoft Docs"
description: "Ez a dokumentum ismerteti a Azure SQLDB kapcsolat architektúrát Azure-ban vagy az Azure-on kívüli."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: monicar
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 01/16/2018
ms.author: carlrab
ms.openlocfilehash: 8cf926bab45705802cfaca95973ec1071d8d175a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="azure-sql-database-connectivity-architecture"></a>Az Azure SQL Database kapcsolat architektúrája 

Ez a cikk ismerteti az Azure SQL Database kapcsolat architektúra, és elmagyarázza, hogyan a különböző összetevők működnek-e át tudja irányítani a forgalmat az Azure SQL Database-példány. Ezek az Azure SQL Database kapcsolat összetevői függvény át tudja irányítani a hálózati forgalmat és az Azure-adatbázis, a csatlakozás az Azure ügyfelekkel és az Azure-on kívüli csatlakozó ügyfelek. Ez a cikk a parancsfájl minták módosítani a kapcsolat módját, és a kapcsolat alapértelmezett beállításainak módosítása kapcsolódó szempontok is biztosít. 

## <a name="connectivity-architecture"></a>Kapcsolati architektúra

Az alábbi ábra magas szintű áttekintést nyújt az Azure SQL Database kapcsolat architektúrája.

![architektúra áttekintése](./media/sql-database-connectivity-architecture/architecture-overview.png)


A következő lépések bemutatják, hogyan kapcsolatot létesít egy Azure SQL Database az Azure SQL Database szoftver betöltési-terheléselosztó Állapotfigyelője és az Azure SQL Database-átjárón keresztül.

- Azure-ban vagy az Azure-on kívüli ügyfelek csatlakozni a SLB, amelyek egy nyilvános IP-címmel rendelkezik, és az 1433-as porton figyel.
- A SLB irányítja a forgalmat az Azure SQL Database-átjáróra.
- Az átjáró a megfelelő proxy köztes átirányítja a forgalmat.
- A proxy köztes átirányítja a forgalmat a megfelelő Azure SQL-adatbázis.

> [!IMPORTANT]
> Ezeket az összetevőket tartalmaz elosztott szolgáltatásmegtagadásos (DDoS-) szolgáltatás védelmi beépített a hálózat és az app réteget.
>

## <a name="connectivity-from-within-azure"></a>Kapcsolat az Azure-ban

Ha csatlakozik Azure-ban, a kapcsolatokat. a kapcsolat házirenddel rendelkezhetnek a **átirányítási** alapértelmezés szerint. A házirend a **átirányítási** azt jelenti, hogy az Azure SQL adatbázishoz, az TCP-munkamenet az ügyfélmunkamenethez létrejöttét követően kapcsolatok majd átirányítja a proxy köztes való áttérés a cél virtuális IP-címhez, amely az Azure SQL Database-átjáró, a proxy köztes. Ezt követően az összes későbbi csomagok folyamat közvetlenül a proxy köztes, az Azure SQL Database átjáró kihagyásával keresztül. A következő ábra szemlélteti a forgalom áramlását.

![architektúra áttekintése](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Azure-on kívüli kapcsolat

Ha a külső Azure kapcsolódik, a kapcsolatokat. a kapcsolat házirenddel rendelkezhetnek a **Proxy** alapértelmezés szerint. A házirend a **Proxy** azt jelenti, hogy a TCP munkamenet az Azure SQL Database-átjárón keresztül, és minden későbbi csomagok flow az átjárón keresztül. A következő ábra szemlélteti a forgalom áramlását.

![architektúra áttekintése](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Az Azure SQL Database átjáró IP-címek

Az csatlakozni egy Azure SQL Database adatbázist a helyszíni erőforrások, akkor engedélyeznie kell az Azure SQL Database átjáró az Azure-régió, a kimenő hálózati forgalmát. A kapcsolatok csak lépjen az átjárón keresztül Proxy módban, ez az alapértelmezett a helyszíni erőforrások kapcsolódáskor kapcsolódáskor.

A következő táblázat az elsődleges és másodlagos IP-címek az Azure SQL Database-átjáró minden adatok területen. Egyes régiókhoz a rendszer két IP-cím. Ezeken a területeken az elsődleges IP-cím az aktuális IP-cím az átjáró, a második IP-cím a feladatátvételi IP-címet. A feladatátvételi címe a címet, amelyre azt a kiszolgálót, hogy a szolgáltatás magas rendelkezésre állás megőrzése előfordulhat, hogy áthelyezése. Ezek a régiókban javasoljuk, hogy lehetővé tegye az IP-címek kimenő. A második IP-cím a Microsoft tulajdonában van, és nem figyel a szolgáltatások által az Azure SQL Database-kapcsolatok fogadására aktiválásáig.

| Régió neve | Elsődleges IP-cím | Másodlagos IP-cím |
| --- | --- |--- |
| Kelet-Ausztrália | 191.238.66.109 | 13.75.149.87 |
| Délkelet-Ausztrália | 191.239.192.109 | 13.73.109.251 |
| Dél-Brazília | 104.41.11.5 | |
| Közép-Kanada | 40.85.224.249 | |
| Kelet-Kanada | 40.86.226.166 | |
| USA középső régiója | 23.99.160.139 | 13.67.215.62 |
| Kelet-Ázsia | 191.234.2.139 | 52.175.33.150 |
| 1 USA keleti régiója | 191.238.6.43 | 40.121.158.30 |
| USA 2. keleti régiója | 191.239.224.107 | 40.79.84.180 * |
| Közép-India | 104.211.96.159  | |
| Dél-India | 104.211.224.146  | |
| Nyugat-India | 104.211.160.80 | |
| Kelet-Japán | 191.237.240.43 | 13.78.61.196 |
| Nyugat-Japán | 191.238.68.11 | 104.214.148.156 |
| Korea középső régiója | 52.231.32.42 | |
| Korea déli régiója | 52.231.200.86 |  |
| USA északi középső régiója | 23.98.55.75 | 23.96.178.199 |
| Észak-Európa | 191.235.193.75 | 40.113.93.91 |
| USA déli középső régiója | 23.98.162.75 | 13.66.62.124 |
| Délkelet-Ázsia | 23.100.117.95 | 104.43.15.0 |
| Egyesült Királyság északi régiója | 13.87.97.210 | |
| Egyesült Királyság déli régiója 1 | 51.140.184.11 | |
| Egyesült Királyság 2. déli régiója | 13.87.34.7 | |
| Az Egyesült Királyság nyugati régiója | 51.141.8.11  | |
| USA nyugati középső régiója | 13.78.145.25 | |
| Nyugat-Európa | 191.237.232.75 | 40.68.37.158 |
| 1 USA nyugati régiója | 23.99.34.75 | 104.42.238.205 |
| USA nyugati régiója, 2. | 13.66.226.202  | |
||||

\***Megjegyzés:** *USA keleti régiója 2* rendelkezik is egy harmadlagos IP-címe `2.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Módosítsa a kapcsolatkezelési házirendet az Azure SQL Database

Az Azure SQL Database kapcsolatkezelési házirendet az Azure SQL Database-kiszolgáló módosításához használja a [REST API](https://msdn.microsoft.com/library/azure/mt604439.aspx).

- Ha a kapcsolat-házirend **Proxy**, az összes hálózati csomagok folyamata az Azure SQL Database-átjárón keresztül. Ehhez a beállításhoz szeretné engedélyezni a kimenő csak az Azure SQL Database átjáró IP-címhez. Beállítás használatával **Proxy** rendelkezik további késést biztosít beállítását **átirányítási**.
- Ha a kapcsolat szabályzatot állítja **átirányítási**, az összes hálózati csomagok folyamat közvetlenül a köztes proxy felé. Ehhez a beállításhoz több IP-cím a kimenő forgalom engedélyezése kell.

## <a name="script-to-change-connection-settings-via-powershell"></a>Parancsfájl PowerShell biztonságoskapcsolat-beállításainak módosítása

> [!IMPORTANT]
> Ez a parancsfájl igényel a [Azure PowerShell modul](/powershell/azure/install-azurerm-ps).
>

A következő PowerShell-parancsfájl bemutatja, hogyan módosítsa a kapcsolatkezelési házirendet.

```powershell
Add-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli-20"></a>Parancsfájl keresztül Azure CLI 2.0 biztonságoskapcsolat-beállításainak módosítása

> [!IMPORTANT]
> Ez a parancsfájl igényel a [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
>

A következő parancssori parancsfájl bemutatja, hogyan módosítsa a kapcsolatkezelési házirendet.

<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy 
az resource show --ids $id

# Update connection policy 
az resource update --ids $id --set properties.connectionType=Proxy

</pre>

## <a name="next-steps"></a>További lépések

- Az Azure SQL Database kapcsolatkezelési házirendet az Azure SQL Database-kiszolgáló módosításának módjával kapcsolatos információkért lásd: [létrehozási vagy frissítési kiszolgáló kapcsolatkezelési házirendet a REST API használatával](https://msdn.microsoft.com/library/azure/mt604439.aspx).
- Információ az Azure SQL Database kapcsolat viselkedésről ADO.NET 4.5 vagy újabb verzióját használó ügyfelek számára, lásd: [kívüli ADO.NET 4.5 1433-as portokon](sql-database-develop-direct-route-ports-adonet-v12.md).
- Általános alkalmazás fejlesztési, témakör [SQL adatbázis alkalmazás fejlesztői áttekintés](sql-database-develop-overview.md).
