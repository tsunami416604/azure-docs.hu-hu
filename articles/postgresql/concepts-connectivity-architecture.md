---
title: Kapcsolati architektúra a Azure Database for PostgreSQLban
description: A Azure Database for PostgreSQL-kiszolgáló kapcsolati architektúráját ismerteti.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 5288c3025e523e767082f4ab7cfbb08805a74eaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500093"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Kapcsolati architektúra a Azure Database for PostgreSQLban
Ez a cikk ismerteti a Azure Database for PostgreSQL kapcsolati architektúrát, valamint azt, hogy a forgalom hogyan legyen átirányítva a Azure Database for PostgreSQL adatbázis-példányára az Azure-on belüli és kívüli ügyfelektől.

## <a name="connectivity-architecture"></a>Kapcsolati architektúra
A Azure Database for PostgreSQL kapcsolata egy olyan átjárón keresztül jön létre, amely felelős a bejövő kapcsolatok útválasztásához a fürtben lévő kiszolgáló fizikai helyén. A következő ábra a forgalmat mutatja be.

![A kapcsolati architektúra áttekintése](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Ahogy az ügyfél csatlakozik az adatbázishoz, egy kapcsolati karakterláncot kapnak, amely az átjáróhoz csatlakozik. Az átjáró egy nyilvános IP-címmel rendelkezik, amely a 5432-es portot figyeli. Az adatbázis-fürt forgalmát a megfelelő Azure Database for PostgreSQL továbbítja a rendszer. Ezért ahhoz, hogy csatlakozni tudjon a kiszolgálóhoz, például a vállalati hálózatokból, meg kell nyitnia az ügyféloldali tűzfalat, hogy a kimenő forgalom elérje az átjárókat. Az alábbiakban megtalálhatja az átjárók által régiónként használt IP-címek teljes listáját.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Átjáró IP-címeinek Azure Database for PostgreSQL
A következő táblázat felsorolja az Azure Database for PostgreSQL átjáró elsődleges és másodlagos IP-címeit az összes adatterülethez. Az elsődleges IP-cím az átjáró aktuális IP-címe, a második IP-cím pedig egy feladatátvételi IP-cím az elsődleges hiba esetén. Ahogy említettük, az ügyfeleknek engedélyezniük kell a kimenő forgalmat az IP-címekre is. A második IP-cím nem figyeli a szolgáltatásokat, amíg Azure Database for PostgreSQL nem aktiválja a kapcsolatokat.

| **Régió neve** | **Elsődleges IP-cím** | **Másodlagos IP-cím** |
|:----------------|:-------------|:------------------------|
| Kelet-Ausztrália | 13.75.149.87 | 40.79.161.1 |
| Délkelet-Ausztrália | 191.239.192.109 | 13.73.109.251 |
| Dél-Brazília | 104.41.11.5 | |
| Közép-Kanada | 40.85.224.249 | |
| Kelet-Kanada | 40.86.226.166 | |
| USA középső régiója | 23.99.160.139 | 13.67.215.62 |
| Kelet-Kína 1 | 139.219.130.35 | |
| Kína 2. keleti régiója | 40.73.82.1 | |
| Észak-Kína 1 | 139.219.15.17 | |
| Kína 2. északi régiója | 40.73.50.0 | |
| Kelet-Ázsia | 191.234.2.139 | 52.175.33.150 |
| USA keleti régiója 1 | 191.238.6.43 | 40.121.158.30 |
| USA 2. keleti régiója | 191.239.224.107 | 40.79.84.180 * |
| Közép-Franciaország | 40.79.137.0 | 40.79.129.1 |
| Közép-Németország | 51.4.144.100 | |
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
| Dél-Afrika északi régiója | 102.133.152.0 | |
| Dél-Afrika nyugati régiója | 102.133.24.0 | |
| Egyesült Arab Emírségek északi régiója | 65.52.248.0 | |
| Egyesült Királyság déli régiója | 51.140.184.11 | |
| Az Egyesült Királyság nyugati régiója | 51.141.8.11| |
| Nyugat-Európa | 191.237.232.75 | 40.68.37.158 |
| USA nyugati régiója 1 | 23.99.34.75 | 104.42.238.205 |
| USA 2. nyugati régiója | 13.66.226.202 | |
||||

> [!NOTE]
> Az *USA 2. keleti* régiójában a `52.167.104.0`harmadlagos IP-címe is szerepel.

## <a name="next-steps"></a>További lépések

* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./howto-manage-firewall-using-portal.md)
* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése az Azure CLI-vel](./howto-manage-firewall-using-cli.md)
