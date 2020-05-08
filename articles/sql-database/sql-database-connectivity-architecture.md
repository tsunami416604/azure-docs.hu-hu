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
ms.date: 03/09/2020
ms.openlocfilehash: 112dbea4ef54c5923c586b87be9770c2e91befd2
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901429"
---
# <a name="azure-sql-connectivity-architecture"></a>Az Azure SQL kapcsolati architektúrája
> [!NOTE]
> Ez a cikk az Azure SQL Serverre vonatkozik, valamint az Azure SQL Serveren létrehozott SQL Database és SQL Data Warehouse adatbázisokra is. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik **Azure SQL Database felügyelt példányra**. Tekintse át a [felügyelt példány kapcsolati architektúráját](sql-database-managed-instance-connectivity-architecture.md).

Ez a cikk ismerteti a különböző összetevők architektúráját, amelyek közvetlen hálózati forgalmat Azure SQL Database vagy SQL Data Warehouse. Ismerteti továbbá a különböző kapcsolati szabályzatokat, valamint azt, hogy a hogyan befolyásolja az Azure-ból és az Azure-on kívülről csatlakozó ügyfelektől érkező ügyfeleket. 

## <a name="connectivity-architecture"></a>Kapcsolati architektúra

Az alábbi ábra a Azure SQL Database kapcsolati architektúrájának áttekintését nyújtja.

![architektúra – áttekintés](./media/sql-database-connectivity-architecture/connectivity-overview.png)

A következő lépések azt ismertetik, hogyan történik a kapcsolatok létrehozása egy Azure SQL Database-adatbázisba:

- Az ügyfelek csatlakoznak az átjáróhoz, amely nyilvános IP-címmel rendelkezik, és az 1433-es porton figyel.
- Az átjáró a hatályos kapcsolódási házirendtől függően átirányítja vagy proxyként továbbítja a forgalmat a megfelelő adatbázis-fürtre.
- Az adatbázis-fürt forgalmát a megfelelő Azure SQL Database-adatbázisba továbbítja a rendszer.

## <a name="connection-policy"></a>Kapcsolatok házirendje

A Azure SQL Database a következő három lehetőséget támogatja egy SQL Database-kiszolgáló kapcsolatbiztonsági házirendjének beállításához:

- **Átirányítás (ajánlott):** Az ügyfelek közvetlenül az adatbázist üzemeltető csomóponthoz csatlakoznak, ami csökkenti a késést és a jobb teljesítményt. A mód használatára szolgáló kapcsolatok esetén az ügyfeleknek a következőket kell tenniük:
   - Engedélyezi a kimenő kommunikációt az ügyféltől a régió összes Azure SQL IP-címére a 11000 11999 tartományon belüli portoknál. A szolgáltatáshoz tartozó címkék használatával könnyebben kezelheti ezt az SQL-szolgáltatást.  
   - A kimenő kommunikáció engedélyezése az ügyféltől az 1433-es porton keresztüli átjáró IP-címeinek Azure SQL Database.

- **Proxy:** Ebben a módban az összes kapcsolat a Azure SQL Database-átjárón keresztül történik, ami növeli a késést és a kisebb átviteli sebességet. A módot használó kapcsolatok esetében az ügyfeleknek engedélyeznie kell a kimenő kommunikációt az ügyféltől az 1433-es porton Azure SQL Database átjáró IP-címeinek eléréséhez.

- **Alapértelmezett:** Ez a kapcsolódási házirend a létrehozás után minden kiszolgálón érvényben van, kivéve, ha explicit módon módosítja a kapcsolódási házirendet a `Proxy` vagy `Redirect`a rendszerre. Az alapértelmezett házirend`Redirect` az Azure-ból (például egy Azure-beli virtuális gépről) és `Proxy`a kívülről származó összes ügyfélkapcsolatra (például a helyi munkaállomásról létesített kapcsolatokra) vonatkozó összes ügyfélkapcsolat.

 A kapcsolati szabályzatot a legalacsonyabb késés `Proxy` és a legmagasabb átviteli sebesség érdekében javasoljuk `Redirect` . Azonban meg kell felelnie a fentiekben ismertetett hálózati forgalom engedélyezésének további követelményeinek. Ha az ügyfél egy Azure-beli virtuális gép, ezt a hálózati biztonsági csoportok (NSG) és a [szolgáltatás-címkék](../virtual-network/security-overview.md#service-tags)használatával végezheti el. Ha az ügyfél helyszíni munkaállomásról csatlakozik, akkor előfordulhat, hogy a hálózati rendszergazdával kell dolgoznia a vállalati tűzfalon keresztüli hálózati forgalom engedélyezéséhez.

## <a name="connectivity-from-within-azure"></a>Kapcsolat az Azure-on belül

Ha az Azure-on belülről csatlakozik, a kapcsolatokhoz alapértelmezés `Redirect` szerint csatlakozási házirend tartozik. Az `Redirect` azt jelenti, hogy a TCP-munkamenet az Azure SQL Database-adatbázishoz való létrehozása után a rendszer átirányítja az ügyfél-munkamenetet a megfelelő adatbázis-fürtre, amely a célként megadott virtuális IP-címet a fürt Azure SQL Database átjárójának egy másik példányára módosítja. Ezt követően az összes további csomag közvetlenül a fürtre áramlik, és megkerüli a Azure SQL Database-átjárót. A következő ábra szemlélteti ezt a forgalmat.

![architektúra – áttekintés](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Kapcsolódás az Azure-on kívülről

Ha az Azure-on kívülről csatlakozik, a kapcsolatok alapértelmezés `Proxy` szerint csatlakozási házirendtel rendelkeznek. A szabályzat `Proxy` azt jelenti, hogy a TCP-munkamenet a Azure SQL Database átjárón és az összes további, az átjárón keresztüli adatcsomagon keresztül jön. A következő ábra szemlélteti ezt a forgalmat.

![architektúra – áttekintés](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Emellett a 1434-es és a 14000-14999-as TCP-porton is megnyitható a [DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


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
| Kelet-Kína 2         | 40.73.82.1         |
| Észak-Kína          | 139.219.15.17      |
| Észak-Kína 2        | 40.73.50.0         |
| Kelet-Ázsia            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| USA keleti régiója              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| USA 2. keleti régiója            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Közép-Franciaország       | 40.79.137.0, 40.79.129.1 |
| Közép-Németország      | 51.4.144.100       |
| Kelet-Észak-Németország   | 51.5.144.179       |
| Közép-India        | 104.211.96.159     |
| Dél-India          | 104.211.224.146    |
| Nyugat-India           | 104.211.160.80     |
| Kelet-Japán           | 13.78.61.196, 40.79.184.8, 191.237.240.43, 40.79.192.5 | 
| Nyugat-Japán           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Dél-Korea középső régiója        | 52.231.32.42       |
| Dél-Korea déli régiója          | 52.231.200.86      |
| USA északi középső régiója     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Észak-Európa         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Kelet-Norvégia          | 51.120.96.0        |
| Norvégia nyugati régiója          | 51.120.216.0       |
| Dél-Afrika északi régiója   | 102.133.152.0      |
| Dél-Afrika nyugati régiója    | 102.133.24.0       |
| USA déli középső régiója     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Délkelet-Ázsia      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| UAE középső régiója          | 20.37.72.64        |
| Észak-Egyesült Arab            | 65.52.248.0        |
| Az Egyesült Királyság déli régiója             | 51.140.184.11      |
| Az Egyesült Királyság nyugati régiója              | 51.141.8.11        |
| USA nyugati középső régiója      | 13.78.145.25       |
| Nyugat-Európa          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| USA nyugati régiója              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| USA nyugati régiója, 2.            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>További lépések

- Az Azure SQL Database-kiszolgálók Azure SQL Database-kapcsolódási szabályzatának módosításáról további információt a következő témakörben talál: [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- A ADO.NET 4,5-as vagy újabb verziót használó ügyfelek Azure SQL Database-kapcsolatainak működéséről további információért lásd: [a 1433-nál nagyobb portok a ADO.NET 4,5](sql-database-develop-direct-route-ports-adonet-v12.md)-ban.
- Az alkalmazások fejlesztésének általános áttekintését lásd: [SQL Database alkalmazásfejlesztés áttekintése](sql-database-develop-overview.md).
