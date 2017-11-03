---
title: "Naplózás, TDS átirányítás miatt a táblázat és az Azure SQL Database IP-végpontok |} Microsoft Docs"
description: "További tudnivalók naplózását, TDS-átirányítási és IP-végpont módosításokat az Azure SQL Database naplózási táblázat végrehajtása során."
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: 
ms.assetid: 4ef19ed1-e798-43a2-ad99-0e563f93ab53
ms.service: sql-database
ms.custom: security
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: giladm
ms.openlocfilehash: 42c89f09eee4394fec7d2f33f51ddc5875587530
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>SQL adatbázis - a régebbi típusú ügyfeleknek támogatja, és IP-végponton megváltoztatja a táblázat naplózás

> [!IMPORTANT]
> Ez a dokumentum csak érvényes tábla naplózás, amely **elavult**.<br>
> Használja az új [Blobnaplózási funkció](sql-database-auditing.md) metódus, amely **nem** szükséges korábbi verziójú ügyfél kapcsolati karakterlánc módosítani. További információ a Blobnaplózási funkció található [Ismerkedés az SQL-adatbázis naplózásának](sql-database-auditing.md).

[Adatbázis naplózási](sql-database-auditing.md) TDS átirányítást támogató SQL-ügyfelek automatikusan működik. Vegye figyelembe, hogy az átirányítási nem vonatkozik a Blobnaplózási funkció módszer használata esetén.

## <a id="subheading-1"></a>Alacsonyabb szintű ügyfelek támogatása
Bármely olyan ügyfél, amely TDS 7.4 kell is támogatja az átirányítást. A kivételek közé tartozik a JDBC 4.0-s verzióját, amelyben az átirányítás nem teljes mértékben támogatja, és a Node.JS mely funkcióhoz Tedious nem lett megvalósítva.

Az "Alsószintű ügyfelek" azaz mely támogatási TDS verzió 7.3 és az alacsonyabb – a kiszolgáló teljes Tartománynevét a kapcsolat-karakterlánc kell módosítani:

A kapcsolódási karakterláncban eredeti kiszolgálójának teljes Tartományneve: <*kiszolgálónév*>. database.windows.net

A kapcsolati karakterláncban a módosított kiszolgálójának teljes Tartományneve: <*kiszolgálónév*> .database. **biztonságos**. windows.net

"A régebbi típusú ügyfeleknek" részleges listáját tartalmazza:

* A .NET 4.0-s vagy régebbi verzió,
* ODBC 10.0-s vagy régebbi verzió.
* JDBC (JDBC támogatja a TDS 7.4, a TDS-átirányítási funkció még nem teljes mértékben támogatott)
* (A Node.JS) fárasztó

**Megjegyzés:** lehet, hogy a fenti kiszolgáló FQDN módosítását is hasznos egy SQL Server szint naplózási házirend alkalmazása nélkül konfiguráció szükséges lépést az egyes adatbázisok (ideiglenes megoldás).

## <a id="subheading-2"></a>IP-végponton vált, ha engedélyezve van a naplózás
Vegye figyelembe, hogy ha a tábla a naplózás engedélyezése az IP-végpontot az adatbázis változik. Ha szigorú tűzfal beállításait, frissítse ezeket a tűzfal beállításainak megfelelően.

Az új adatbázis IP-végpontot az adatbázis-terület függ:

| Adatbázis-terület | Lehetséges IP-végpontok |
| --- | --- |
| Észak-Kína |139.217.29.176, 139.217.28.254 |
| Kelet-Kína |42.159.245.65, 42.159.246.245 |
| Kelet-Ausztrália |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Délkelet-Ausztrália |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Dél-Brazília |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| USA középső régiója |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| USA középső RÉGIÓJA EUAP |52.180.178.16, 52.180.176.190 |
| Kelet-Ázsia |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| USA 2. keleti régiója |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| USA keleti régiója |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| USA keleti régiója EUAP |52.225.190.86, 52.225.191.187 |
| Közép-India |104.211.98.219, 104.211.103.71 |
| Dél-India |104.211.227.102, 104.211.225.157 |
| Nyugat-India |104.211.161.152, 104.211.162.21 |
| Kelet-Japán |104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Nyugat-Japán |104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| USA északi középső régiója |191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Észak-Európa |104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| USA déli középső régiója |191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Délkelet-Ázsia |104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Nyugat-Európa |104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| USA nyugati régiója |191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| USA nyugati régiója, 2. |13.66.224.156, 13.66.227.8 |
| USA nyugati középső régiója |52.161.29.186, 52.161.27.213 |
| Közép-Kanada |13.88.248.106, 13.88.248.110 |
| Kelet-Kanada |40.86.227.82, 40.86.225.194 |
| Egyesült Királyság északi régiója |13.87.101.18, 13.87.100.232 |
| Egyesült Királyság 2. déli régiója |13.87.32.202, 13.87.32.226 |
