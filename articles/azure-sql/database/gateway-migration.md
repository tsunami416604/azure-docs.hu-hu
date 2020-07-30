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
ms.openlocfilehash: 22bfab5b9f00a392054fa1aef6a93195180fd968
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373487"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database forgalom áttelepítése újabb átjáróra
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az Azure-infrastruktúra fejlesztésekor a Microsoft rendszeresen frissíti a hardvert, hogy biztosítsa a lehető legjobb felhasználói élményt. Az elkövetkező hónapokban az újabb hardveres generációk számára készült átjárók hozzáadását tervezzük, áttelepítjük a forgalmat, és végül leszerelik a régebbi hardverekre épülő átjárókat egyes régiókban.  

Az ügyfelek értesítést kapnak e-mailben, valamint a Azure Portal az egyes régiókban elérhető átjárók változásairól. A legfrissebb információk a [Azure SQL Database átjáró IP-címei](connectivity-architecture.md#gateway-ip-addresses) táblában lesznek karbantartva.

## <a name="status-updates"></a>Állapot-frissítések

# <a name="in-progress"></a>[Folyamatban](#tab/in-progress-ip)
### <a name="september-2020"></a>2020. szeptember

Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva:

- Észak-Európa: 13.74.104.113 
- Nyugat-RÉGIÓJA: 40.78.248.10 
- Nyugat-Európa: 52.236.184.163 
- USA déli középső régiója: 20.45.121.1, 20.49.88.1 

A meglévő SQL-átjárók a következő régiókban kezdik el a forgalom fogadását:
- Kelet-Japán: 40.79.184.8, 40.79.192.5

Ezek az SQL-átjárók a 2020. szeptember 1-jén kezdik el az ügyfelek forgalmának elfogadását. 

### <a name="august-2020"></a>Augusztus 2020

Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva:

- Kelet-Ausztrália: 13.70.112.9
- Közép-Kanada: 52.246.152.0, 20.38.144.1 
- USA 2. nyugati régiója: 40.78.240.8

Ezek az SQL-átjárók az ügyfelek forgalmának elfogadását az 2020. augusztus 10-én megkezdik. 

# <a name="completed"></a>[Befejeződött](#tab/completed-ip)

A következő átjárók áttelepítése befejeződött: 

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
- A [zóna redundáns konfigurációjának](high-availability-sla.md#zone-redundant-configuration) használata az adatbázishoz

A következőket nem érinti a rendszer:

- Átirányítás a kapcsolatok házirendjének megfelelően
- Kapcsolatok SQL Database az Azure-ban és a szolgáltatási címkék használatával
- A SQL Server JDBC illesztőprogram támogatott verzióival létesített kapcsolatok nem lesznek hatással. A támogatott JDBC-verziókért lásd: [a Microsoft JDBC-illesztőprogram letöltése SQL Serverhoz](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Mi a teendő, ha érintett

Azt javasoljuk, hogy engedélyezze a kimenő forgalmat az IP-címekre a 1433-es TCP-porton és a 11000-11999-es porton a régióban lévő összes [ÁTJÁRÓ IP-címe](connectivity-architecture.md#gateway-ip-addresses) esetében. Ez a javaslat a helyszíni és a szolgáltatás-végpontokon keresztül csatlakozó ügyfelekre is érvényes. A porttartomány-tartományokkal kapcsolatos további információkért lásd: a [kapcsolatkérelem-házirend](connectivity-architecture.md#connection-policy).

Az alkalmazásból a 4,0-es verziónál régebbi Microsoft JDBC illesztőprogramot használó alkalmazások kapcsolatainak érvényesítése sikertelen lehet. A Microsoft JDBC korábbi verziói a tanúsítvány Tárgy mezőjében a köznapi név (CN) függvényt használják. A megoldás célja, hogy a hostNameInCertificate tulajdonság értéke *. database.windows.net legyen. A hostNameInCertificate tulajdonság beállításával kapcsolatos további információkért lásd: [Csatlakozás a titkosítással](/sql/connect/jdbc/connecting-with-ssl-encryption).

Ha a fenti megoldás nem működik, a következő URL-cím használatával kérjen SQL Database vagy SQL felügyelt példányra vonatkozó támogatási kérelmet:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure SQL kapcsolati architektúráról](connectivity-architecture.md)
