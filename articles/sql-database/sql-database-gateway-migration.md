---
title: Átjáróforgalom áttelepítési értesítése
description: A cikk értesítést küld a felhasználóknak az Azure SQL Database Gateways IP-címeinek áttelepítéséről
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 9e3c33bb7493f07d9fbf19710f21d0114e7abec8
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757055"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Az Azure SQL Database-forgalom áttelepítése újabb átjárókra

Az Azure-infrastruktúra fejlődése során a Microsoft rendszeres időközönként frissíti a hardvereket, hogy a lehető legjobb felhasználói élményt nyújtsuk. Az elkövetkező hónapokban azt tervezzük, hogy újabb hardvergenerációkra épülő átjárókat adunk hozzá, áttelepítjük a forgalmat, és végül egyes régiókban régebbi hardverre épülő átjárókat telepítünk le.  

Az ügyfelek e-mailben és az Azure Portalon jóval az egyes régiókban elérhető átjárók bármilyen változása előtt értesítést kapnak. A legfrissebb információkat az [Azure SQL Database átjáró IP-címek](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) táblájában tartja karban.

## <a name="impact-of-this-change"></a>A változás hatása

**2019. október 14-én** a következő régiókban tervezik a forgalom újabb átjárókra való átállásának első fordulóját:
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

A forgalom áttelepítése megváltoztatja a DNS által az SQL-adatbázishoz feloldott nyilvános IP-címet.
Hatással lesz önre, ha:
- A helyszíni tűzfal bármely átjárójának IP-címe szigorú kódolású
- A Microsoft.SQL szolgáltatást szolgáltatási végpontként használó alhálózatok, de nem tudnak kommunikálni az átjáró IP-címeivel

Nem lesz hatással, ha:
- Átirányítás kapcsolati házirendként
- Kapcsolatok az SQL-adatbázissal az Azure-on belülről és a szolgáltatáscímkék használatával
- A JDBC Driver for SQL Server támogatott verzióival létesített kapcsolatok nem fognak hatást gyakorolni. A támogatott JDBC-verziókról a [Microsoft JDBC illesztőprogram letöltése sql serverhez](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)című témakörben található.

## <a name="what-to-do-you-do-if-youre-affected"></a>Mi a teendő, ha ön érintett?

Azt javasoljuk, hogy engedélyezze a kimenő forgalmat az IP-címekre az [összes Azure SQL Database átjáró IP-címek](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) a régióban a TCP-port 1433, és a port tartomány 11000-11999. Ez a javaslat a helyszíni és a szolgáltatásvégpontokon keresztül csatlakozó ügyfelekre vonatkozik. A porttartományokról a [Kapcsolatházirend](sql-database-connectivity-architecture.md#connection-policy)című témakörben talál további információt.

A 4.0-s verzió alatti Microsoft JDBC-illesztőprogramot használó alkalmazásokból származó kapcsolatok sikertelenek lehetnek a tanúsítványok érvényesítésében. A Microsoft JDBC kisebb verziói a tanúsítvány Tárgy mezőjében a köznapi névre (CN) támaszkodnak. A megoldás annak biztosítása, hogy a hostNameInCertificate tulajdonság beállítása *.database.windows.net. A hostNameInCertificate tulajdonság beállításáról a [Csatlakozás titkosítással](/sql/connect/jdbc/connecting-with-ssl-encryption)című témakörben talál további információt.

Ha a fenti megoldás nem működik, nyújtson be támogatási kérelmet az SQL Database-hez a következő URL-cím használatával:https://aka.ms/getazuresupport

## <a name="next-steps"></a>További lépések

- További információ az [Azure SQL Connectivity Architecture rendszerről](sql-database-connectivity-architecture.md)
