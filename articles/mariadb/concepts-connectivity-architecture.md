---
title: Kapcsolódási architektúra – Azure-adatbázis a MariaDB-hez
description: A MariaDB-kiszolgáló Azure-adatbázisának kapcsolati architektúráját ismerteti.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 38b6f797541ef07fa3fb5e1dc71029a4cbcf5d22
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546296"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Kapcsolódási architektúra a MariaDB Azure Database szolgáltatásában
Ez a cikk ismerteti az Azure Database for MariaDB kapcsolatarchitektúra, valamint hogyan a forgalom az Azure Database for MariaDB-példány az ügyfelek az Azure-on belül és kívül is.

## <a name="connectivity-architecture"></a>Kapcsolati architektúra

A MariaDB-hez való Azure Database-hez való csatlakozás egy átjárón keresztül jön létre, amely felelős a bejövő kapcsolatok útválasztásáért a kiszolgáló fürtjeinkben lévő fizikai helyére. Az alábbi ábra a forgalom áramlását mutatja be.

![A kapcsolódási architektúra áttekintése](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Ahogy az ügyfél csatlakozik az adatbázishoz, kapnak egy kapcsolati karakterláncot, amely csatlakozik az átjáróhoz. Ez az átjáró nyilvános IP-címmel rendelkezik, amely a 3306-os portot figyeli. Az adatbázis-fürtön belül a forgalom továbbítása a megfelelő Azure-adatbázis MariaDB. Ezért annak érdekében, hogy csatlakozzon a kiszolgálóhoz, például a vállalati hálózatok, meg kell nyitni az ügyféloldali tűzfal, hogy a kimenő forgalom képes elérni az átjárók. Az alábbiakban régiónként megtalálja az átjáróink által használt IP-címek teljes listáját.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Azure-adatbázis a MariaDB átjáró IP-címeihez

Az alábbi táblázat az Azure Database for MariaDB átjáró elsődleges és másodlagos IP-it sorolja fel az összes adatrégióban. Az elsődleges IP-cím az átjáró aktuális IP-címe, a második IP-cím pedig feladatátvételi IP-cím az elsődleges hiba esetén. Mint említettük, az ügyfelek lehetővé kell tenniük a kimenő mind az IP-címeket. A második IP-cím nem hallgatja le a szolgáltatásokat, amíg az Azure Database nem aktiválja a MariaDB kapcsolatok fogadására.

| **Régiónév** | **Átjáró IP-címei** |
|:----------------|:-------------|
| Ausztrália középső régiója| 20.36.105.0     |
| Ausztrália Central2     | 20.36.113.0   |
| Kelet-Ausztrália | 13.75.149.87, 40.79.161.1     |
| Délkelet-Ausztrália |191.239.192.109, 13.73.109.251   |
| Dél-Brazília | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Közép-Kanada |40.85.224.249  |
| Kelet-Kanada | 40.86.226.166    |
| USA középső régiója | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     |
| Kelet-Kína | 139.219.130.35    |
| Kína Keleti 2 | 40.73.82.1  |
| Észak-Kína | 139.219.15.17    |
| Kína Észak 2 | 40.73.50.0     |
| Kelet-Ázsia | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| USA keleti régiója | 40.121.158.30, 191.238.6.43  |
| USA 2. keleti régiója |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  |
| Közép-Franciaország | 40.79.137.0, 40.79.129.1  |
| Közép-Németország | 51.4.144.100     |
| Németország Északkelet | 51.5.144.179  |
| Közép-India | 104.211.96.159     |
| Dél-India | 104.211.224.146  |
| Nyugat-India | 104.211.160.80    |
| Kelet-Japán | 13.78.61.196, 191.237.240.43  |
| Nyugat-Japán | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    |
| Dél-Korea középső régiója | 52.231.32.42   |
| Dél-Korea déli régiója | 52.231.200.86    |
| USA északi középső régiója | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Észak-Európa | 40.113.93.91, 191.235.193.75, 52.138.224.6, 52.138.224.7    |
| Dél-Afrika Észak-Afrika  | 102.133.152.0    |
| Dél-Afrika Nyugati | 102.133.24.0   |
| USA déli középső régiója |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| Délkelet-Ázsia | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Egyesült Arab Emírségek központi | 20.37.72.64  |
| Egyesült Arab Emírségek északi | 65.52.248.0    |
| Az Egyesült Királyság déli régiója | 51.140.184.11   |
| Az Egyesült Királyság nyugati régiója | 51.141.8.11  |
| USA nyugati középső régiója | 13.78.145.25     |
| Nyugat-Európa | 40.68.37.158, 191.237.232.75, 13.69.105.208  |
| USA nyugati régiója | 104.42.238.205, 23.99.34.75  |
| USA nyugati régiója, 2. | 13.66.226.202  |
||||

## <a name="next-steps"></a>További lépések

* [Azure Database létrehozása és kezelése a MariaDB tűzfalszabályaihoz az Azure Portal használatával](./howto-manage-firewall-portal.md)
* [Azure Database létrehozása és kezelése a MariaDB tűzfalszabályaihoz az Azure CLI használatával](./howto-manage-firewall-cli.md)
