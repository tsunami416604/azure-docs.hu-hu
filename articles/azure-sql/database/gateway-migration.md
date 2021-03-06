---
title: Átjáró forgalmi áttelepítési nyilatkozata
description: A cikk a Azure SQL Database átjáró IP-címeinek áttelepítésére vonatkozó értesítéseket nyújt a felhasználóknak
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 7fadbecc2c00a739afb2f94dd1d049805915cfa5
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427107"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database forgalom áttelepítése újabb átjáróra
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az Azure-infrastruktúra fejlesztésekor a Microsoft rendszeresen frissíti a hardvert, hogy biztosítsa a lehető legjobb felhasználói élményt. Az elkövetkező hónapokban az újabb hardveres generációk számára készült átjárók hozzáadását tervezzük, áttelepítjük a forgalmat, és végül leszerelik a régebbi hardverekre épülő átjárókat egyes régiókban.  

Az ügyfelek értesítést kapnak e-mailben, valamint a Azure Portal az egyes régiókban elérhető átjárók változásairól. A legfrissebb információk a [Azure SQL Database átjáró IP-címei](connectivity-architecture.md#gateway-ip-addresses) táblában lesznek karbantartva.

## <a name="status-updates"></a>Állapot-frissítések

# <a name="in-progress"></a>[Folyamatban](#tab/in-progress-ip)
### <a name="october-2020"></a>2020. október

Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva:

- Középnyugat-Németország: 51.116.240.0, 51.116.248.0

Ezek az SQL-átjárók a 2020 október 12-én megkezdik az ügyfél forgalmának elfogadását. 

### <a name="september-2020"></a>2020. szeptember
Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva. Ezek az SQL-átjárók a **2020. szeptember 15**-én megkezdik az ügyfelek forgalmának fogadását:

- Délkelet-Ausztrália: 13.77.48.10
- Kelet-Kanada: 40.86.226.166, 52.242.30.154
- Egyesült Királyság déli régiója: 51.140.184.11, 51.105.64.0

A meglévő SQL Gateway-átjárók a következő régiókban kezdik el a forgalom fogadását. Ezek az SQL-átjárók a **2020. szeptember 15** -én megkezdik az ügyfelek forgalmának fogadását:

- Délkelet-Ausztrália: 191.239.192.109 és 13.73.109.251
- USA középső régiója: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 és 104.208.21.1
- Kelet-Ázsia: 191.234.2.139, 52.175.33.150 és 13.75.32.4
- USA keleti régiója: 40.121.158.30, 40.79.153.12, 191.238.6.43 és 40.78.225.32
- USA 2. keleti régiója: 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 és 104.208.150.3
- Közép-Franciaország: 40.79.137.0 és 40.79.129.1
- Nyugat-Japán: 104.214.148.156, 40.74.100.192, 191.238.68.11 és 40.74.97.10
- USA északi középső régiója: 23.96.178.199, 23.98.55.75 és 52.162.104.33
- Délkelet-Ázsia: 104.43.15.0, 23.100.117.95 és 40.78.232.3
- USA nyugati régiója: 104.42.238.205, 23.99.34.75 és 13.86.216.196

Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva. Ezek az SQL-átjárók a **2020 szeptember 10**-én megkezdik az ügyfél forgalmának elfogadását:

- USA nyugati középső régiója: 13.78.248.43 
- Dél-Afrika északi régiója: 102.133.120.2  

Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva. Ezek az SQL-átjárók a **2020 szeptember 1-jén**kezdik el az ügyfelek forgalmának elfogadását:

- Észak-Európa: 13.74.104.113 
- Nyugat-RÉGIÓJA: 40.78.248.10 
- Nyugat-Európa: 52.236.184.163 
- USA déli középső régiója: 20.45.121.1, 20.49.88.1 

A meglévő SQL Gateway-átjárók a következő régiókban kezdik el a forgalom fogadását. Ezek az SQL-átjárók a **2020 szeptember 1-jén** kezdik el az ügyfelek forgalmának elfogadását:
- Kelet-Japán: 40.79.184.8, 40.79.192.5

# <a name="completed"></a>[Befejeződött](#tab/completed-ip)

A következő átjárók áttelepítése befejeződött: 

### <a name="august-2020"></a>2020. augusztus

Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva:

- Kelet-Ausztrália: 13.70.112.9
- Közép-Kanada: 52.246.152.0, 20.38.144.1 
- USA 2. nyugati régiója: 40.78.240.8

Ezek az SQL-átjárók az ügyfelek forgalmának elfogadását az 2020. augusztus 10-én megkezdik. 

### <a name="october-2019"></a>2019. október
- Dél-Brazília
- USA nyugati régiója
- Nyugat-Európa
- USA keleti régiója
- USA középső régiója
- Délkelet-Ázsia
- USA déli középső régiója
- Észak-Európa
- USA északi középső régiója
- Nyugat-Japán
- Kelet-Japán
- USA 2. keleti régiója
- Kelet-Ázsia

---

## <a name="impact-of-this-change"></a>A változás hatása

A forgalom áttelepítése megváltoztathatja azt a nyilvános IP-címet, amelyet a DNS feloldja az adatbázishoz Azure SQL Databaseban.
A következőket lehet befolyásolni:

- A helyszíni tűzfal adott átjárójának IP-címe nem kódolt
- Minden olyan alhálózattal rendelkezik, amely a Microsoft. SQL szolgáltatást használja szolgáltatási végpontként, de nem tud kommunikálni az átjáró IP-címeivel
- Az [általános célú zóna redundáns konfigurációjának](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) használata
- A [zóna redundáns konfigurációjának használata a prémium szintű & üzleti szempontból kritikus szinteken](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

A következőket nem érinti a rendszer:
 
- Átirányítás a kapcsolatok házirendjének megfelelően
- Kapcsolatok SQL Database az Azure-ban és a szolgáltatási címkék használatával
- A SQL Server JDBC illesztőprogram támogatott verzióival létesített kapcsolatok nem lesznek hatással. A támogatott JDBC-verziókért lásd: [a Microsoft JDBC-illesztőprogram letöltése SQL Serverhoz](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Mi a teendő, ha érintett

Azt javasoljuk, hogy engedélyezze a kimenő forgalmat az IP-címekre a 1433-es TCP-porton és a 11000-11999-es porton a régióban lévő összes [ÁTJÁRÓ IP-címe](connectivity-architecture.md#gateway-ip-addresses) esetében. Ez a javaslat a helyszíni és a szolgáltatás-végpontokon keresztül csatlakozó ügyfelekre is érvényes. A porttartomány-tartományokkal kapcsolatos további információkért lásd: a [kapcsolatkérelem-házirend](connectivity-architecture.md#connection-policy).

Az alkalmazásból a 4,0-es verziónál régebbi Microsoft JDBC illesztőprogramot használó alkalmazások kapcsolatainak érvényesítése sikertelen lehet. A Microsoft JDBC korábbi verziói a tanúsítvány Tárgy mezőjében a köznapi név (CN) függvényt használják. A megoldás célja, hogy a hostNameInCertificate tulajdonság értéke *. database.windows.net legyen. A hostNameInCertificate tulajdonság beállításával kapcsolatos további információkért lásd: [Csatlakozás a titkosítással](/sql/connect/jdbc/connecting-with-ssl-encryption).

Ha a fenti megoldás nem működik, a következő URL-cím használatával kérjen SQL Database vagy SQL felügyelt példányra vonatkozó támogatási kérelmet: https://aka.ms/getazuresupport

## <a name="next-steps"></a>További lépések

- További információ az [Azure SQL kapcsolati architektúráról](connectivity-architecture.md)
