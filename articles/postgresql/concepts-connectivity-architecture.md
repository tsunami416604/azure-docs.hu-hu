---
title: Az Azure Database for postgresql-hez kapcsolati architektúra
description: A témakör ismerteti a kapcsolati architektúra az Azure Database for PostgreSQL-kiszolgálóhoz.
author: kummanish
ms.author: manishku
ms.service: PostgreSQL
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 73c23c471cb12ca3a3a7df4380779b464b8d86d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735736"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Az Azure Database for postgresql-hez kapcsolati architektúra
Ez a cikk ismerteti az Azure Database for PostgreSQL csatlakozási architektúra, valamint, hogy hogyan a átirányítja a forgalmat az Azure Database for PostgreSQL-adatbázispéldány belül és kívül az Azure-ügyfelekről.

## <a name="connectivity-architecture"></a>Kapcsolati architektúra
Az Azure Database for postgresql-hez kapcsolat jön létre, amely a kiszolgáló a fürtben használt fizikai helynek útválasztási bejövő kapcsolatokat az átjárón keresztül. A következő ábra szemlélteti a forgalom áramlását.

![A kapcsolati architektúra áttekintése](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Ügyfél kapcsolódni az adatbázishoz, kapnak egy kapcsolati karakterláncot, amely az átjáró csatlakozik. Ez az átjáró van egy nyilvános IP-címet, amely az 5432-es porton figyel. Az adatbázisban clusterz forgalmat továbbítja a megfelelő Azure-adatbázis PostgreSQL-hez. Annak érdekében, hogy csatlakozni a kiszolgálóhoz, mint például a vállalati hálózatokra, ezért szükséges képes elérni az átjáró kimenő forgalom engedélyezésére az ügyfél oldalán tűzfalát megnyitnia. Az átjárók régiónként által használt IP-címek teljes listája az alábbiakban találja.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Azure Database for PostgreSQL-átjáró IP-címek
Az alábbi táblázat az elsődleges és másodlagos IP-címek, az Azure Database for PostgreSQL átjáró adatok minden régió esetében az. Az elsődleges IP-cím az az aktuális IP-cím az átjáró, a második IP-cím pedig az elsődleges meghiúsulásának feladatátvételi IP-címet. Ahogy említettük, ügyfelek engedélyeznie kell a kimenő mindkét IP-címet. A második IP-cím nem figyel a függő szolgáltatások mindaddig, amíg aktívvá válik, amelyet, Azure Database for PostgreSQL-kapcsolatok fogadására.

| **Régió neve** | **Elsődleges IP-cím** | **Másodlagos IP-cím** |
|:----------------|:-------------|:------------------------|
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
| Nyugat-Európa | 191.237.232.75 | 40.68.37.158 |
| USA nyugati RÉGIÓJA 1 | 23.99.34.75 | 104.42.238.205 |
| USA nyugati régiója, 2. | 13.66.226.202 | |
||||

> [!NOTE]
> *USA keleti RÉGIÓJA 2* is a harmadlagos IP-címmel rendelkezik `52.167.104.0`.

## <a name="next-steps"></a>További lépések

* [Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok az Azure portál használatával](./howto-manage-firewall-using-portal.md)
* [Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok Azure parancssori felület használatával](./howto-manage-firewall-using-cli.md)
