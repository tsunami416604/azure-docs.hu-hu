---
title: Átjáró áttelepítési értesítés az Azure SQL Database adatbázist a Gen2 Gen3 |} A Microsoft Docs
description: A cikk figyelje meg a felhasználók számára az Azure SQL Database átjárók IP-címek az áttelepítéssel kapcsolatban
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5894579c62c524394c7fea044b96885f7c8e8204
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538379"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Az Azure SQL Database forgalom áttelepítési újabb átjárók

Javítja az Azure-infrastruktúra, a Microsoft rendszeres időközönként frissíti azt adja meg a lehetséges legjobb felhasználói élmény biztosítása érdekében hardveres. Az elkövetkező hónapokban hogy újabb hardvergenerációk épülő átjárók hozzáadásának tervezése, és egyes régiókban régebbi hardver épülő átjárók leszerelése.  

Ügyfél értesítést kap e-mailen keresztül, és jóval bármilyen módosítása az egyes régiókban elérhető átjárók az Azure Portalon. A legfrissebb információk lesznek kezelhetők a [Azure SQL Database-átjáró IP-címek](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) tábla.

## <a name="impact-of-this-change"></a>E módosítás hatásának

Az átjárókiszolgáló leszerelésének első körben az alábbi régiókban 2019. szeptember 1. van ütemezve:

- USA nyugati régiója
- Nyugat-Európa
- East US
- USA középső régiója
- Délkelet-Ázsia
- USA déli középső régiója
- Észak-Európa
- USA északi középső régiója
- Nyugat-Japán
- Kelet-Japán
- USA 2. keleti régiója
- Kelet-Ázsia

A leszerelt IP-cím le fog állni, forgalmat fogad, és minden új kapcsolódási kísérletek a rendszer átirányítja az egyik az átjárók a régióban.

Ha e módosítás hatásának nem jelenik meg:

- Az ügyfelek átirányítás használata, mivel a kapcsolódási szabályzat nem jelenik meg semmilyen hatással.
- SQL Database-kapcsolatokat a belül az Azure és a szolgáltatás címkék használatával nem lesz hatással.
- Nincs hatással az SQL Server JDBC-illesztőprogram támogatott verzióinak használatával létrehozott kapcsolatok jelenik meg. Tekintse meg a támogatott JDBC-verziók [töltse le a Microsoft JDBC-illesztőprogram SQL Serverhez](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Mi a teendő, ha Ön érintett tegye

Azt javasoljuk, hogy a kimenő forgalom IP-címek az összes ahhoz, hogy a [Azure SQL Database-átjáró IP-címek](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) a régióban, a TCP 1433-as porton, és tartomány 11000-11999 tűzfal eszközét a port. Porttartományok további információkért lásd: [kapcsolódási szabályzat](sql-database-connectivity-architecture.md#connection-policy).

Az alkalmazások a Microsoft JDBC-illesztőprogram használatával 4.0-s verziója alatt létrehozott kapcsolatok tanúsítvány érvényesítése sikertelen lehet. Microsoft JDBC alacsonyabb verzióját használja az egyszerű név (CN) a tanúsítvány Tárgy mezőjében. A megoldás az, hogy győződjön meg arról, hogy a hostNameInCertificate tulajdonság értéke *. database.windows.net. A hostNameInCertificate tulajdonság beállításával kapcsolatos további információkért lásd: [csatlakoztatása SSL-titkosítást](/sql/connect/jdbc/connecting-with-ssl-encryption).

Ha a fenti megoldás sem működik, fájl egy támogatási kérést az SQL Database, a következő URL-cím használatával: https://aka.ms/getazuresupport

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Azure SQL-kapcsolati architektúra](sql-database-connectivity-architecture.md)