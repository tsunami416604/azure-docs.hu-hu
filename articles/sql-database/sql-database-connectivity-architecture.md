---
title: Az Azure SQL Database és az SQL Data warehouse-bA az Azure forgalom átirányítása |} A Microsoft Docs
description: Ez a dokumentum ismerteti az Azure SQL Database és az SQL Data Warehouse kapcsolati architektúra az Azure-ban vagy az Azure-on kívül.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 801e3b20908c3e92693e5e800428773bf5c90539
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521465"
---
# <a name="azure-sql-connectivity-architecture"></a>Az Azure SQL-kapcsolati architektúra

Ez a cikk ismerteti az Azure SQL Database és az SQL Data Warehouse kapcsolati architektúra, valamint, hogy hogyan a különböző összetevők működéséhez forgalom az Azure SQL-példányra. E kapcsolat összetevők közvetlen hálózati forgalmat az Azure SQL Database vagy az SQL Data warehouse-bA az Azure-ban a csatlakozó ügyfelek és az Azure-on kívül csatlakozó ügyféltől függvény. Ez a cikk a szkriptminták módosításához, hogyan történik a kapcsolat, és az alapértelmezett kapcsolat beállításainak módosításával kapcsolatos szempontokat is biztosít.

> [!IMPORTANT]
> **[Közelgő változás] Azure SQL-kiszolgáló, végpont kapcsolatok szolgáltatás egy `Default` kapcsolat viselkedés vált `Redirect`.**
> Ügyfelek javasolja, hogy hozzon létre új kiszolgálók és a kapcsolat típusát a már meglévőket explicit módon állítva átirányítási (előnyösebb) vagy a Proxy kapcsolódási architektúráját függően.
>
> Ebből a változásból meglévő környezetekben használhatatlanná tévő szolgáltatási végponton keresztüli kapcsolat megakadályozása érdekében használjuk telemetriai hajtsa végre a következő:
>
> - -Kiszolgálók, hogy a rendszer azt észleli, hogy a módosítás előtt a szolgáltatásvégpontokon keresztül elért, hogy váltson a kapcsolat típusát `Proxy`.
> - További kiszolgálók, hogy váltson a kapcsolat típusa fognak váltani `Redirect`.
>
> Szolgáltatási végpont felhasználók továbbra is hatással lehetnek a következő esetekben:
>
> - Alkalmazás ritkán csatlakozik egy meglévő kiszolgálót, így a telemetria adott alkalmazásokra vonatkozó információk nem rögzítése
> - Automatizált üzembehelyezési logic létrehoz egy SQL Database-kiszolgálóhoz, feltéve, hogy az alapértelmezett viselkedést, a szolgáltatás végpontja kapcsolatokhoz `Proxy`
>
> Végpont szolgáltatáskapcsolatokat nem sikerült létrehozni az Azure SQL Serverhez, és meg is feltételezi, hogy ez a változás által érintett, győződjön meg arról, hogy kapcsolattípus explicit módon értéke `Redirect`. Ha ez a helyzet, hogy nyissa meg a virtuális gép tűzfal-szabályok és a hálózati biztonsági csoportok (NSG) régióban minden olyan Azure IP-címekhez tartozó Sql [szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) portok 11000-12000. Ha ez nem megfelelő megoldás, váltson kiszolgáló explicit módon a `Proxy`.
> [!NOTE]
> Ez a témakör az önálló adatbázisok és rugalmas készletek és az SQL Data Warehouse-adatbázist üzemeltető Azure SQL Database-kiszolgálókra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

## <a name="connectivity-architecture"></a>Kapcsolati architektúra

Az alábbi ábra az Azure SQL Database kapcsolati architektúra magas szintű áttekintést nyújt.

![az architektúra áttekintése](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Az alábbi lépések bemutatják, hogyan létrejön a kapcsolat az Azure SQL Database-adatbázishoz:

- Az ügyfelek csatlakozni az átjáróhoz, nyilvános IP-címmel rendelkezik, és a 1433-as portot figyeli.
- Az átjáró, attól függően, a hatékony kapcsolódási szabályzat, az átirányítások vagy a proxyk a forgalom a megfelelő adatbázis-fürthöz.
- Az adatbázisban fürt forgalmat továbbítja a megfelelő Azure SQL Database-adatbázishoz.

## <a name="connection-policy"></a>Kapcsolat-házirend

Az Azure SQL Database támogatja a kapcsolat egy SQL Database-kiszolgáló házirend-beállítás a következő három módon:

- **Redirect (recommended):** Az ügyfelek közvetlenül az adatbázist futtató csomópontot kapcsolatokat hozhat létre. Ahhoz, hogy a kapcsolat, az ügyfelek engedélyeznie kell a kimenő tűzfalszabályokat, hogy az összes Azure IP-címek a régióban a hálózati biztonsági csoportok (NSG) a használatával [szolgáltatáscímkéket](../virtual-network/security-overview.md#service-tags)) portok 11000-12000, nem csak az Azure SQL Database gateway-IP címek 1433-as portot. Mivel a csomagok adatbázissal, teljesítmény és a késés teljesítményűek.
- **Proxy:** Ebben a módban az összes kapcsolat az Azure SQL Database-átjárókon keresztül webszolgáltatásokhoz használják proxyként. Ahhoz, hogy a kapcsolat, az ügyfél kimenő tűzfalszabályokat, amelyek lehetővé teszik az Azure SQL Database-átjáró IP-címek (általában két IP-címek régiónként) kell rendelkeznie. Ez a mód kiválasztása nagyobb késést és alacsonyabb átviteli sebességet a számítási feladatok természetétől függően eredményezhet. Kifejezetten ajánljuk a `Redirect` keresztül a kapcsolódási szabályzat a `Proxy` kapcsolódási szabályzat a legkisebb késés és a legnagyobb átviteli sebességet.
- **alapértelmezett érték:** Ez a kapcsolat-házirend érvényben minden kiszolgálón létrehozása után hacsak nem változtatja meg a kapcsolódási szabályzat vagy `Proxy` vagy `Redirect`. A tényleges házirend attól függ, hogy Azure-ban származik kapcsolatok (`Redirect`) vagy Azure-on kívül (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Csatlakozás Azure-ban

Azure-on belül csatlakozik a kapcsolatok van-e, a kapcsolódási szabályzat `Redirect` alapértelmezés szerint. Egy szabályzatot a `Redirect` azt jelenti, hogy az Azure SQL Database-adatbázishoz a TCP-munkamenet létrehozása után az ügyfél munkamenet ezután átirányítja a megfelelő adatbázis-fürt és a cél virtuális IP-címhez, az Azure SQL Database-átjáró, valamint a módosul a a fürt. Ezt követően a fürt, az Azure SQL Database átjárója megkerülésével közvetlenül a flow további csomagokat. A következő ábra szemlélteti a forgalom áramlását.

![az architektúra áttekintése](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Csatlakozás Azure-on kívül

Ha Azure-on kívülről csatlakozik, a kapcsolatok rendelkezik-e a kapcsolódási szabályzat, `Proxy` alapértelmezés szerint. Egy szabályzatot a `Proxy` azt jelenti, hogy a TCP-munkamenet létrehozását az Azure SQL Database-átjárón keresztül, és további csomagokat flow az átjárón keresztül. A következő ábra szemlélteti a forgalom áramlását.

![az architektúra áttekintése](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Az Azure SQL Database átjárója IP-címek

Csatlakozhat egy Azure SQL database a helyszíni erőforrásairól, szüksége, hogy a kimenő hálózati forgalom az Azure SQL Database-átjáróhoz, az az Azure-régióban. A kapcsolatokat csak nyissa meg az átjárón keresztül a csatlakozáskor `Proxy` üzemmódot, amely az alapértelmezett érték, amikor csatlakozik a helyszíni erőforrások.

Az alábbi táblázat az elsődleges és másodlagos IP-címek, adatok minden régió esetében az Azure SQL Database-átjáró. Az egyes régiókban vannak két IP-címet. Ezekben a régiókban az elsődleges IP-cím az átjáró aktuális IP-címe pedig a második IP-cím feladatátvételi IP-címet. A feladatátvétel a címet, amelyre nem lehet áthelyezni, hogy a szolgáltatás magas rendelkezésre állását a kiszolgáló címe. Ezekben a régiókban javasoljuk, hogy engedélyezi a kimenő mindkét IP-címet. A második IP-cím a Microsoft tulajdonában van, és nem figyel a függő szolgáltatások mindaddig, amíg aktívvá válik, az Azure SQL Database-kapcsolatok fogadására által.

| Régiónév | Elsődleges IP-cím | Másodlagos IP-cím |
| --- | --- |--- |
| Kelet-Ausztrália | 13.75.149.87 | 40.79.161.1 |
| Délkelet-Ausztrália | 191.239.192.109 | 13.73.109.251 |
| Dél-Brazília | 104.41.11.5 | |
| Közép-Kanada | 40.85.224.249 | |
| Kelet-Kanada | 40.86.226.166 | |
| USA középső régiója | 23.99.160.139 | 13.67.215.62 |
| Kelet-Kína 1 | 139.219.130.35 | |
| Kelet-Kína 2 | 40.73.82.1 | |
| Észak-Kína 1 | 139.219.15.17 | |
| Észak-Kína 2 | 40.73.50.0 | |
| Kelet-Ázsia | 191.234.2.139 | 52.175.33.150 |
| 1 USA keleti régiója | 191.238.6.43 | 40.121.158.30 |
| USA 2. keleti régiója | 191.239.224.107 | 40.79.84.180 * |
| Közép-Franciaország | 40.79.137.0 | 40.79.129.1 |
| Közép-Németország | 51.4.144.100 | |
| Németország, Északkelet | 51.5.144.179 | |
| Közép-India | 104.211.96.159 | |
| Dél-India | 104.211.224.146 | |
| Nyugat-India | 104.211.160.80 | |
| Kelet-Japán | 191.237.240.43 | 13.78.61.196 |
| Nyugat-Japán | 191.238.68.11 | 104.214.148.156 |
| Korea középső régiója | 52.231.32.42 | |
| Korea déli régiója | 52.231.200.86 |  |
| USA északi középső régiója | 23.98.55.75 | 23.96.178.199 |
| Észak-Európa | 191.235.193.75 | 40.113.93.91 |
| USA déli középső régiója | 23.98.162.75 | 13.66.62.124 |
| Délkelet-Ázsia | 23.100.117.95 | 104.43.15.0 |
| Az Egyesült Királyság déli régiója | 51.140.184.11 | |
| Az Egyesült Királyság nyugati régiója | 51.141.8.11| |
| USA nyugati középső régiója | 13.78.145.25 | |
| Nyugat-Európa | 191.237.232.75 | 40.68.37.158 |
| USA nyugati RÉGIÓJA 1 | 23.99.34.75 | 104.42.238.205 |
| USA nyugati régiója, 2. | 13.66.226.202 | |
||||

\* **MEGJEGYZÉS:** *USA keleti RÉGIÓJA 2* is a harmadlagos IP-címmel rendelkezik `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Azure SQL Database-kapcsolat házirend módosítása

Az Azure SQL Database egy Azure SQL Database-kiszolgáló kapcsolódási szabályzat módosításához használja a [kap-házirend](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) parancsot.

- Ha a kapcsolat-házirend `Proxy`, az összes hálózati csomagok folyamatot az Azure SQL Database-átjárón keresztül. A beállítás csak az Azure SQL Database IP átjáró kimenő engedélyeznie kell. Beállítás használatával `Proxy` további beállítás késést rendelkezik `Redirect`.
- Ha a kapcsolódási szabályzat beállítása `Redirect`, az összes hálózati csomagok folyamat közvetlenül az adatbázis-fürt. Ehhez a beállításhoz több IP-cím kimenő engedélyeznie kell.

## <a name="script-to-change-connection-settings-via-powershell"></a>Módosítja a kapcsolati beállításokat PowerShell-szkript

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

> [!IMPORTANT]
> A szkriptnek szüksége van a [Azure PowerShell-modul](/powershell/azure/install-az-ps).

A következő PowerShell-parancsfájl bemutatja, hogyan módosíthatja a kapcsolódási szabályzat.

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

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Módosítja a kapcsolati beállításokat az Azure CLI-szkript

> [!IMPORTANT]
> A szkriptnek szüksége van a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli).

A következő CLI-példaszkript bemutatja, hogyan módosíthatja a kapcsolódási szabályzat.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>További lépések

- Egy Azure SQL Database-kiszolgálóhoz az Azure SQL Database kapcsolódási szabályzat módosításának módjával kapcsolatos információkért lásd: [kap-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Az ADO.NET 4.5 vagy újabb verzióját használó ügyfelek az Azure SQL Database-kapcsolat működéssel kapcsolatos információkért lásd: [az ADO.NET 4.5 szoftverrel 1433-Ason túli](sql-database-develop-direct-route-ports-adonet-v12.md).
- Általános fejlesztési áttekintő információkért lásd: [SQL Database-alapú alkalmazásfejlesztés áttekintése](sql-database-develop-overview.md).
