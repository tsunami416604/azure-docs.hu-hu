---
title: Kapcsolati architektúra – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A Azure Database for PostgreSQL-egyetlen kiszolgáló kapcsolati architektúráját ismerteti.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: f03b5e93e4ed5155f6a713c152fbcf34dc873674
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88065879"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Kapcsolati architektúra a Azure Database for PostgreSQLban
Ez a cikk ismerteti a Azure Database for PostgreSQL kapcsolati architektúrát, valamint azt, hogy a forgalom hogyan legyen átirányítva a Azure Database for PostgreSQL adatbázis-példányára az Azure-on belüli és kívüli ügyfelektől.

## <a name="connectivity-architecture"></a>Kapcsolati architektúra
A Azure Database for PostgreSQL kapcsolata egy olyan átjárón keresztül jön létre, amely felelős a bejövő kapcsolatok útválasztásához a fürtben lévő kiszolgáló fizikai helyén. A következő ábra a forgalmat mutatja be.

![A kapcsolati architektúra áttekintése](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Ahogy az ügyfél csatlakozik az adatbázishoz, egy kapcsolati karakterláncot kapnak, amely az átjáróhoz csatlakozik. Az átjáró egy nyilvános IP-címmel rendelkezik, amely a 5432-es portot figyeli. Az adatbázis-fürt forgalmát a megfelelő Azure Database for PostgreSQL továbbítja a rendszer. Ezért ahhoz, hogy csatlakozni tudjon a kiszolgálóhoz, például a vállalati hálózatokból, meg kell nyitnia az ügyféloldali tűzfalat, hogy a kimenő forgalom elérje az átjárókat. Az alábbiakban megtalálhatja az átjárók által régiónként használt IP-címek teljes listáját.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Átjáró IP-címeinek Azure Database for PostgreSQL
A következő táblázat felsorolja az Azure Database for PostgreSQL átjáró elsődleges és másodlagos IP-címeit az összes adatterülethez. Az elsődleges IP-cím az átjáró aktuális IP-címe, a második IP-cím pedig egy feladatátvételi IP-cím az elsődleges hiba esetén. Ahogy említettük, az ügyfeleknek engedélyezniük kell a kimenő forgalmat az IP-címekre is. A második IP-cím nem figyeli a szolgáltatásokat, amíg Azure Database for PostgreSQL nem aktiválja a kapcsolatokat.

| **Régiónév** | **Átjáró IP-címei** |
|:----------------|:-------------|
| Ausztrália középső régiója| 20.36.105.0     |
| Ausztráliai Central2     | 20.36.113.0   |
| Kelet-Ausztrália | 13.75.149.87, 40.79.161.1     |
| Délkelet-Ausztrália |191.239.192.109, 13.73.109.251   |
| Dél-Brazília | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Közép-Kanada |40.85.224.249  |
| Kelet-Kanada | 40.86.226.166    |
| USA középső régiója | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     |
| Kelet-Kína | 139.219.130.35    |
| Kelet-Kína 2 | 40.73.82.1  |
| Észak-Kína | 139.219.15.17    |
| Észak-Kína 2 | 40.73.50.0     |
| Kelet-Ázsia | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| USA keleti régiója | 40.121.158.30, 191.238.6.43, 40.71.8.203, 40.71.83.113   |
| USA 2. keleti régiója |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  |
| Közép-Franciaország | 40.79.137.0, 40.79.129.1  |
| Dél-Franciaország | 40.79.177.0     |
| Közép-Németország | 51.4.144.100     |
| Kelet-Észak-Németország | 51.5.144.179  |
| Közép-India | 104.211.96.159     |
| Dél-India | 104.211.224.146  |
| Nyugat-India | 104.211.160.80    |
| Kelet-Japán | 13.78.61.196, 191.237.240.43  |
| Nyugat-Japán | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    |
| Dél-Korea középső régiója | 52.231.32.42   |
| Dél-Korea déli régiója | 52.231.200.86    |
| USA északi középső régiója | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Észak-Európa | 40.113.93.91, 191.235.193.75, 52.138.224.6, 52.138.224.7    |
| Dél-Afrika északi régiója  | 102.133.152.0    |
| Dél-Afrika nyugati régiója | 102.133.24.0   |
| USA déli középső régiója |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| Délkelet-Ázsia | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| UAE középső régiója | 20.37.72.64  |
| Észak-Egyesült Arab | 65.52.248.0    |
| Az Egyesült Királyság déli régiója | 51.140.184.11   |
| Az Egyesült Királyság nyugati régiója | 51.141.8.11  |
| USA nyugati középső régiója | 13.78.145.25     |
| Nyugat-Európa | 40.68.37.158, 191.237.232.75, 13.69.105.208, 104.40.169.187  |
| USA nyugati régiója | 104.42.238.205, 23.99.34.75, 13.86.216.212, 13.86.217.212 |
| USA 2. nyugati régiója | 13.66.226.202  |
||||

## <a name="next-steps"></a>További lépések

* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./howto-manage-firewall-using-portal.md)
* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése az Azure CLI-vel](./howto-manage-firewall-using-cli.md)
