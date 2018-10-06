---
title: Tábla, naplózás és TDS-átirányítást, és az Azure SQL Database IP-végpontok |} A Microsoft Docs
description: Ismerje meg naplózás, a TDS-átirányítást és az IP-végpont módosításokat az Azure SQL Database táblanaplózás implementálásakor.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 41509c1f35d42a1e8558299b4244ad515c36ec17
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816055"
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>SQL Database - alacsonyabb szintű ügyfelek támogatása és a Táblanaplózás IP-végpontjainak módosítása

> [!IMPORTANT]
> Ez a dokumentum csak érvényes Táblanaplózás, amely **elavultak**.<br>
> Használja az új [Blobnaplózás](sql-database-auditing.md) módszer, amely **nem** szükséges korábbi verziójú ügyfél kapcsolati karakterlánc módosítani. További információ a Blobnaplózás található [első lépései az SQL database naplózási szolgáltatásával](sql-database-auditing.md).

[Adatbázis-naplózás](sql-database-auditing.md) TDS-átirányítást támogató SQL-ügyfelek szolgáltatással automatikusan együttműködve. Vegye figyelembe, hogy az átirányítási nem vonatkozik, amikor Blobnaplózás mód használatával.

## <a id="subheading-1"></a>Alacsonyabb szintű ügyfelek támogatása
Bármely ügyfél, amely megvalósítja a TDS 7.4 is támogatnia kell a átirányítást. A kivételek közé tartozik a JDBC 4.0, amelyben az átirányító szolgáltatás nem támogatja teljes mértékben, és mely átirányítása a node.js-hez készült Tedious nem lett megvalósítva.

"Régebbi ügyfelek" azaz melyik támogatási TDS verzió 7.3-as és az alacsonyabb – a kiszolgáló teljes Tartománynevét a kapcsolat karakterláncot kell módosítani:

A kapcsolati karakterláncban eredeti kiszolgálójának teljes Tartományneve: <*kiszolgálónév*>. database.windows.net

A kapcsolati karakterláncban a módosított kiszolgálói FQDN: <*kiszolgálónév*> .database. **biztonságos**. windows.net

"Régebbi típusú ügyfelek" részleges listáját tartalmazza:

* .NET 4.0-s vagy régebbi verzió,
* ODBC 10.0-s vagy régebbi verzió.
* JDBC (JDBC támogatja a TDS 7.4, míg a TDS-átirányítási funkció nem teljes mértékben támogatott)
* Fárasztó (a node.js-ben)

**Megjegyzés:** a fenti kiszolgáló FQDN módosítása egy SQL Server szint naplózási házirend alkalmazása nélkül szükséges konfiguráció. lépés: az egyes adatbázisokban (ideiglenes megoldás) számára is hasznos lehet.

## <a id="subheading-2"></a>IP-végpontjainak módosítása a naplózás engedélyezése
Vegye figyelembe, hogy engedélyezte a Táblanaplózás, az IP-végpontból az adatbázis is megváltoznak. Ha szigorú tűzfal beállításait, frissítse ezeket a tűzfal beállításainak megfelelően.

Az új adatbázis IP végpont függ az adatbázis-terület:

| Az adatbázis-terület | Lehetséges IP-végpontok |
| --- | --- |
| Észak-Kína |139.217.29.176, 139.217.28.254 |
| Kelet-Kína |42.159.245.65, 42.159.246.245 |
| Kelet-Ausztrália |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Délkelet-Ausztrália |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Dél-Brazília |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| USA középső régiója |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| USA középső régiója – EUAP |52.180.178.16, 52.180.176.190 |
| Kelet-Ázsia |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| USA 2. keleti régiója |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| USA keleti régiója |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| USA keleti régiója – EUAP |52.225.190.86, 52.225.191.187 |
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
