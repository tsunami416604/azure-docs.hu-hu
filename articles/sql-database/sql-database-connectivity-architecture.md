---
title: Csatlakoztatási architektúra
description: Ez a dokumentum ismerteti az Azure SQL-kapcsolatarchitektúrát az Azure-on belüli vagy az Azure-on kívüli adatbázis-kapcsolatokhoz.
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
ms.openlocfilehash: 6fdfbce6dce2428a8f2757b0755e6f982f02240f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256418"
---
# <a name="azure-sql-connectivity-architecture"></a>Az Azure SQL kapcsolati architektúrája
> [!NOTE]
> Ez a cikk az Azure SQL-kiszolgálóra, valamint az Azure SQL-kiszolgálón létrehozott SQL Database és SQL Data Warehouse adatbázisokra is vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik az **Azure SQL Database felügyelt példányára.** Tekintse meg [a felügyelt példány kapcsolatarchitektúráját.](sql-database-managed-instance-connectivity-architecture.md)

Ez a cikk ismerteti a különböző összetevők architektúráját, amelyek az Azure SQL Database vagy az SQL Data Warehouse hálózati forgalmat irányítanak. Azt is ismerteti, a különböző kapcsolati szabályzatok, és milyen hatással van az Azure-on belül csatlakozó ügyfelek és az Azure-on kívülről csatlakozó ügyfelek. 

## <a name="connectivity-architecture"></a>Kapcsolati architektúra

Az alábbi ábra az Azure SQL Database-kapcsolatarchitektúrájának magas szintű áttekintését tartalmazza.

![architektúra – áttekintés](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Az alábbi lépések azt ismertetik, hogyan jön létre egy kapcsolat egy Azure SQL-adatbázissal:

- Az ügyfelek az átjáróhoz csatlakoznak, amely nyilvános IP-címmel rendelkezik, és az 1433-as porton figyel.
- Az átjáró a hatékony kapcsolati házirendtől függően átirányítja vagy proxykat irányít át a megfelelő adatbázis-fürtre.
- Az adatbázis-fürt forgalom továbbítása a megfelelő Azure SQL-adatbázisba.

## <a name="connection-policy"></a>Csatlakozási házirend

Az Azure SQL Database a következő három lehetőséget támogatja az SQL Database-kiszolgáló kapcsolatházirend-beállításához:

- **Átirányítás (ajánlott):** Az ügyfelek közvetlenül az adatbázist üzemeltető csomóponthoz létesítenek kapcsolatot, ami csökkentett késést és jobb átviteli teljesítményhez vezet. A hálózati kapcsolatok használatához az ügyfeleknek a következőket kell tenniük:
   - Az ügyfél kimenő kommunikációjának engedélyezése az összes Azure IP-címhez a régióban az 11000 11999-es tartományú portokon. Használja a Service Tags for SQL, hogy ez könnyebben kezelhető.  
   - Engedélyezze a kimenő kommunikációt az ügyféltől az Azure SQL Database átjáró IP-címeiközött az 1433-as porton.

- **Proxy:** Ebben a módban az összes kapcsolat az Azure SQL Database-átjárókon keresztül lesz proxied, ami nagyobb késést és csökkentett átviteli. Ahhoz, hogy a kapcsolatok ezt a módot használhassa, az ügyfeleknek engedélyezniük kell az ügyfél től az Azure SQL Database átjáró IP-címeibe irányuló kimenő kommunikációt az 1433-as porton.

- **Alapértelmezett:** Ez a kapcsolati házirend a létrehozás után minden kiszolgálón érvényben van, kivéve, ha kifejezetten módosítja a kapcsolati házirendet vagy `Proxy` a számára. `Redirect` Az alapértelmezett`Redirect` szabályzat az Azure-on belülről (például egy Azure virtuális `Proxy`gépről) származó összes ügyfélkapcsolatra és a külső ügyfélkapcsolatokra (például a helyi munkaállomásról származó kapcsolatokra) vonatkozóan.

 A `Redirect` legalacsonyabb késés `Proxy` és a legmagasabb átviteli képesség esetén a kapcsolatházirend et javasoljuk a kapcsolatházirenden keresztül. A fent vázolt hálózati forgalom engedélyezéséhez azonban meg kell felelnie a további követelményeknek. Ha az ügyfél egy Azure virtuális gép, ezt a hálózati biztonsági csoportok (NSG) [szolgáltatáscímkékkel elvégezheti.](../virtual-network/security-overview.md#service-tags) Ha az ügyfél helyszíni munkaállomásról csatlakozik, akkor előfordulhat, hogy a hálózati rendszergazdával kell együttműködnie a vállalati tűzfalon keresztüli hálózati forgalom engedélyezéséhez.

## <a name="connectivity-from-within-azure"></a>Kapcsolat az Azure-on belülről

Ha az Azure-on belülről csatlakozik, `Redirect` a kapcsolatok alapértelmezés szerint rendelkeznek egy kapcsolati szabályzattal. A szabályzat `Redirect` azt jelenti, hogy miután a TCP-munkamenet jön létre az Azure SQL-adatbázis, az ügyfél-munkamenet ezután átirányítja a megfelelő adatbázis-fürtre a változás a cél virtuális IP-cím az Azure SQL Database átjáró, hogy a fürt. Ezt követően az összes további csomagok közvetlenül a fürtbe, megkerülve az Azure SQL Database átjáró. Az alábbi ábra ezt a forgalmat mutatja be.

![architektúra – áttekintés](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Kapcsolat az Azure-on kívülről

Ha az Azure-on kívülről csatlakozik, a `Proxy` kapcsolatok alapértelmezés szerint rendelkeznek egy kapcsolati szabályzattal. A szabályzat `Proxy` azt jelenti, hogy a TCP-munkamenet az Azure SQL Database átjárón keresztül jön létre, és az összes további csomagok az átjárón keresztül. Az alábbi ábra ezt a forgalmat mutatja be.

![architektúra – áttekintés](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Ezenfelül nyissa meg az 14000-14999-es portokat, hogy a [DAC-hoz kapcsolódjon](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database-átjárók IP-címei

Az alábbi táblázat az átjárók IP-címeit sorolja fel régiónként. Az Azure SQL-adatbázishoz való csatlakozáshoz engedélyeznie kell a hálózati forgalmat a régió **összes** átjárójából &.

A forgalom új átjárókba való átáttelepítésének részletei az egyes régiókban a következő cikkben találhatók: [Az Azure SQL Database-forgalom átáttelepítése újabb átjárókra](sql-database-gateway-migration.md)


| Régiónév          | Átjáró IP-címei |
| --- | --- |
| Ausztrália középső régiója    | 20.36.105.0 |
| Ausztrália Central2   | 20.36.113.0 |
| Kelet-Ausztrália       | 13.75.149.87, 40.79.161.1 |
| Délkelet-Ausztrália | 191.239.192.109, 13.73.109.251 |
| Dél-Brazília         | 104.41.11.5, 191.233.200.14 |
| Közép-Kanada       | 40.85.224.249      |
| Kelet-Kanada          | 40.86.226.166      |
| USA középső régiója           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Kelet-Kína           | 139.219.130.35     |
| Kína Keleti 2         | 40.73.82.1         |
| Észak-Kína          | 139.219.15.17      |
| Kína Észak 2        | 40.73.50.0         |
| Kelet-Ázsia            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| USA keleti régiója              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| USA 2. keleti régiója            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Közép-Franciaország       | 40.79.137.0, 40.79.129.1 |
| Közép-Németország      | 51.4.144.100       |
| Németország Északkelet   | 51.5.144.179       |
| Közép-India        | 104.211.96.159     |
| Dél-India          | 104.211.224.146    |
| Nyugat-India           | 104.211.160.80     |
| Kelet-Japán           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Nyugat-Japán           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Dél-Korea középső régiója        | 52.231.32.42       |
| Dél-Korea déli régiója          | 52.231.200.86      |
| USA északi középső régiója     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Észak-Európa         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Norvégia Kelet          | 51.120.96.0        |
| Norvégia Nyugati          | 51.120.216.0       |
| Dél-Afrika Észak-Afrika   | 102.133.152.0      |
| Dél-Afrika Nyugati    | 102.133.24.0       |
| USA déli középső régiója     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Délkelet-Ázsia      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Egyesült Arab Emírségek központi          | 20.37.72.64        |
| Egyesült Arab Emírségek északi            | 65.52.248.0        |
| Az Egyesült Királyság déli régiója             | 51.140.184.11      |
| Az Egyesült Királyság nyugati régiója              | 51.141.8.11        |
| USA nyugati középső régiója      | 13.78.145.25       |
| Nyugat-Európa          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| USA nyugati régiója              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| USA nyugati régiója, 2.            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>További lépések

- Az Azure SQL Database-kiszolgáló Azure SQL Database kapcsolati szabályzatának módosításáról a [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)című témakörben talál további információt.
- Az Azure SQL Database-kapcsolat viselkedéséről ADO.NET a 4.5-ös vagy újabb verziót használó ügyfelek számára a [Portok 1433-nál ADO.NET 4.5-höz](sql-database-develop-direct-route-ports-adonet-v12.md)című témakörben talál.
- Az általános alkalmazásfejlesztési áttekintésről az [SQL Database Application Development Overview című témakörben olvashat.](sql-database-develop-overview.md)
