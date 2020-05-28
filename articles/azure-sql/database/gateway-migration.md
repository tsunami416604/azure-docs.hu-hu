---
title: Átjáró forgalmi áttelepítési nyilatkozata
description: A cikk a Azure SQL Database átjárók IP-címeinek áttelepítésére vonatkozó értesítéseket nyújt a felhasználóknak
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1 
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: f5e45a4625b1cf9422f7ef7e10e9080a7878172d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043393"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database forgalom áttelepítése újabb átjáróra
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az Azure-infrastruktúra fejlesztésekor a Microsoft rendszeresen frissíti a hardvert, hogy biztosítsa a lehető legjobb felhasználói élményt. Az elkövetkező hónapokban az újabb hardveres generációk számára készült átjárók hozzáadását tervezzük, áttelepítjük a forgalmat, és végül leszerelik a régebbi hardverekre épülő átjárókat egyes régiókban.  

Az ügyfelek értesítést kapnak e-mailben, valamint a Azure Portal az egyes régiókban elérhető átjárók változásairól. A legfrissebb információk a [Azure SQL Database átjáró IP-címei](connectivity-architecture.md#gateway-ip-addresses) táblában lesznek karbantartva.

## <a name="impact-of-this-change"></a>A változás hatása

Az újabb átjárók felé irányuló első adatforgalom a következő régiókban **2019 október 14** -én van ütemezve:

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

A forgalom áttelepítése megváltoztatja azt a nyilvános IP-címet, amelyet a DNS feloldja a SQL Database.
A szolgáltatásra hatással lesz:

- A helyszíni tűzfal adott átjárójának IP-címe nem kódolt
- Bármely olyan alhálózat, amely a Microsoft. SQL szolgáltatást használja szolgáltatási végpontként, de nem tud kommunikálni az átjáró IP-címeivel

A következőket nem érinti a rendszer:

- Átirányítás a kapcsolatok házirendjének megfelelően
- Kapcsolatok SQL Database az Azure-ban és a szolgáltatási címkék használatával
- A SQL Server JDBC illesztőprogram támogatott verzióival létesített kapcsolatok nem lesznek hatással. A támogatott JDBC-verziókért lásd: [a Microsoft JDBC-illesztőprogram letöltése SQL Serverhoz](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Mi a teendő, ha érintett

Azt javasoljuk, hogy engedélyezze a kimenő forgalmat az IP-címekre a 1433-es TCP-porton és a 11000-11999-es porton a régióban lévő összes [ÁTJÁRÓ IP-címe](connectivity-architecture.md#gateway-ip-addresses) esetében. Ez a javaslat a helyszíni és a szolgáltatás-végpontokon keresztül csatlakozó ügyfelekre is érvényes. A porttartomány-tartományokkal kapcsolatos további információkért lásd: a [kapcsolatkérelem-házirend](connectivity-architecture.md#connection-policy).

Az alkalmazásból a 4,0-es verziónál régebbi Microsoft JDBC illesztőprogramot használó alkalmazások kapcsolatainak érvényesítése sikertelen lehet. A Microsoft JDBC korábbi verziói a tanúsítvány Tárgy mezőjében a köznapi név (CN) függvényt használják. A megoldás célja, hogy a hostNameInCertificate tulajdonság értéke *. database.windows.net legyen. A hostNameInCertificate tulajdonság beállításával kapcsolatos további információkért lásd: [Csatlakozás a titkosítással](/sql/connect/jdbc/connecting-with-ssl-encryption).

Ha a fenti megoldás nem működik, a következő URL-cím használatával kérjen SQL Database vagy SQL felügyelt példányra vonatkozó támogatási kérelmet:https://aka.ms/getazuresupport

## <a name="next-steps"></a>További lépések

- További információ az [Azure SQL kapcsolati architektúráról](connectivity-architecture.md)
