---
title: A Azure Databricks útválasztási táblázatok és tűzfalak frissítése új MySQL IP-címekkel
description: Megtudhatja, hogyan frissítheti Azure Databricks útválasztási táblázatokat és tűzfalakat új MySQL IP-címekkel.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 5bd4822c21e6d9dbe8f3534dd0171a6f785bd5ac
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325914"
---
# <a name="update-your-azure-databricks-route-tables-and-firewalls-with-new-mysql-ips"></a>A Azure Databricks útválasztási táblázatok és tűzfalak frissítése új MySQL IP-címekkel

A rendszer a Azure Databricks metaadattár használt Azure Database for MySQL összes IP-címét megváltoztatja. Frissítse a Azure Databricks útválasztási táblázatokat vagy tűzfalakat új MySQL IP-címekkel, 2020. június 30-ig, hogy elkerülje a fennakadást.

A saját virtuális hálózatában telepített Azure Databricks munkaterület [útválasztási táblázattal](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/udr#--metastore-artifact-blob-storage-log-blob-storage-and-event-hub-endpoint-ip-addresses) rendelkezik. Előfordulhat, hogy az útválasztási táblázat közvetlenül tartalmaz egy Azure Databricks metaadattár IP-címet vagy egy olyan tűzfal vagy proxy készülék útvonalát, amely engedélyezheti a címet.

## <a name="recommended-actions"></a>Ajánlott műveletek

A szolgáltatás megszakadásának elkerülése érdekében tekintse át és alkalmazza ezeket a műveleteket a 2020. június 30. előtt.

* Ellenőrizze, hogy az útválasztási táblázatot befolyásolja-e az Azure MySQL IP-cím frissítésének változása.

* A következő szakaszban található táblázat segítségével megkeresheti az új IP-címeket. Minden egyes régió esetében több IP-cím is használható az Azure Database for MySQL-átjárók elsődleges és másodlagos IP-címeiként. Az elsődleges IP-cím az átjáró aktuális IP-címe, a második IP-cím pedig a feladatátvételi IP-címek az elsődleges hiba esetén. A szolgáltatás állapotának biztosítása érdekében engedélyeznie kell a kimenő adatforgalmat az összes IP-címhez. Ha [külső metaadattár](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore)használ, győződjön meg arról, hogy az Azure MySQL-értesítések esetében érvényes IP-cím van átirányítva vagy engedélyezési listán.

* Frissítse az útválasztási táblázatot, a tűzfalat vagy a proxy berendezést az új IP-címekkel.

## <a name="updated-mysql-ip-addresses"></a>Frissített MySQL IP-címek

A következő táblázat tartalmazza az összes olyan IP-címet, amelyeknek engedélyezési címnek kell lennie. A **félkövér** IP-címek az új IP-címek. 

| Régió neve          | Átjáró IP-címei                                                                                       |
| -------------------- | ---------------------------------------------------------------------------------------------------------- |
| Ausztrália középső régiója    | 20.36.105.0                                                                                                |
| Ausztrália 2. középső régiója  | 20.36.113.0                                                                                                |
| Kelet-Ausztrália       | 13.75.149.87<br><br>40.79.161.1                                                                            |
| Délkelet-Ausztrália | 191.239.192.109<br><br>13.73.109.251                                                                       |
| Dél-Brazília         | 104.41.11.5 <br><br> 191.233.201.8 <br><br> **191.233.200.16**                                             |
| Közép-Kanada       | 40.85.224.249                                                                                              |
| Kelet-Kanada          | 40.86.226.166                                                                                              |
| USA középső régiója           | 23.99.160.139<br><br>13.67.215.62<br><br>**52.182.136.37**<br><br>**52.182.136.38**                        |
| Kelet-Kína           | 139.219.130.35                                                                                             |
| Kelet-Kína 2         | 40.73.82.1                                                                                                 |
| Észak-Kína          | 139.219.15.17                                                                                              |
| Észak-Kína 2        | 40.73.50.0                                                                                                 |
| Kelet-Ázsia            | 191.234.2.139<br><br>52.175.33.150<br><br>**13.75.33.20**<br><br>**13.75.33.21**                           |
| USA keleti régiója              | 40.121.158.30<br>191.238.6.43                                                                              |
| USA 2. keleti régiója            | 40.79.84.180<br><br>191.239.224.107<br><br>52.177.185.181<br><br>**40.70.144.38**<br><br>**52.167.105.38** |
| Közép-Franciaország       | 40.79.137.0<br><br>40.79.129.1                                                                             |
| Közép-Németország      | 51.4.144.100                                                                                               |
| Kelet-Észak-Németország   | 51.5.144.179                                                                                               |
| Közép-India        | 104.211.96.159                                                                                             |
| Dél-India          | 104.211.224.146                                                                                            |
| Nyugat-India           | 104.211.160.80                                                                                             |
| Kelet-Japán           | 13.78.61.196<br><br>191.237.240.43                                                                         |
| Nyugat-Japán           | 104.214.148.156<br><br>191.238.68.11<br><br>**40.74.96.6**<br><br>**40.74.96.7**                           |
| Dél-Korea középső régiója        | 52.231.32.42                                                                                               |
| Dél-Korea déli régiója          | 52.231.200.86                                                                                              |
| USA északi középső régiója     | 23.96.178.199<br><br>23.98.55.75<br><br>**52.162.104.35**<br><br>**52.162.104.36**                         |
| Észak-Európa         | 40.113.93.91<br><br>191.235.193.75<br><br>**52.138.224.6**<br><br>**52.138.224.7**                         |
| Dél-Afrika északi régiója   | 102.133.152.0                                                                                              |
| Dél-Afrika nyugati régiója    | 102.133.24.0                                                                                               |
| USA déli középső régiója     | 13.66.62.124<br><br>23.98.162.75<br><br>**104.214.16.39**<br><br>**20.45.120.0**                           |
| Délkelet-Ázsia      | 104.43.15.0<br><br>23.100.117.95<br><br>**40.78.233.2**<br><br>**23.98.80.12**                             |
| UAE középső régiója          | 20.37.72.64                                                                                                |
| Észak-Egyesült Arab            | 65.52.248.0                                                                                                |
| Az Egyesült Királyság déli régiója             | 51.140.184.11                                                                                              |
| Az Egyesült Királyság nyugati régiója              | 51.141.8.11                                                                                                |
| USA nyugati középső régiója      | 13.78.145.25                                                                                               |
| Nyugat-Európa          | 40.68.37.158<br><br>191.237.232.75<br><br>**13.69.105.208**                                                |
| USA nyugati régiója              | 104.42.238.205<br><br>23.99.34.75                                                                          |
| USA nyugati régiója, 2.            | 13.66.226.202                                                                                              |

## <a name="next-steps"></a>Következő lépések

* [Azure Databricks üzembe helyezése az Azure Virtual Networkben (VNet Injection)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)
* [Külső Apache Hive metaadattár](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore)
