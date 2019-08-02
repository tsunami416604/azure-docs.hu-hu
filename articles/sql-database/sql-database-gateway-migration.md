---
title: Átjáró áttelepítésére vonatkozó értesítés a Gen2 és Gen3 közötti Azure SQL Database | Microsoft Docs
description: A cikk a Azure SQL Database átjárók IP-címeinek áttelepítésére vonatkozó értesítéseket nyújt a felhasználóknak
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 85691464684ff327c01a85bf357514f447564dd7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568119"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database forgalom áttelepítése újabb átjáróra

Az Azure-infrastruktúra fejlesztésekor a Microsoft rendszeresen frissíti a hardvert, hogy biztosítsa a lehető legjobb felhasználói élményt. Az elkövetkező hónapokban olyan átjárókat fogunk hozzáadni, amelyek újabb hardveres generációra épülnek, és egyes régiókban a régebbi hardverekre épülő leszerelési átjárók.  

Az ügyfelek értesítést kapnak e-mailben, valamint a Azure Portal az egyes régiókban elérhető átjárók változásairól. A legfrissebb információk a [Azure SQL Database átjáró IP-címei](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) táblában lesznek karbantartva.

## <a name="impact-of-this-change"></a>A változás hatása

Az átjárók leszerelésének első fordulója a következő régiókban 2019 szeptember 1-jén van ütemezve:

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

A leszerelt IP-cím leállítja a forgalom fogadását, és minden új kapcsolódási kísérletet a régió egyik átjárója felé irányítja a rendszer.

Itt nem jelenik meg a változás hatása:

- Azok az ügyfelek, akik az átirányítást használják a kapcsolataik házirendjében, semmilyen hatással nem lesznek.
- Az Azure-on belüli és a szolgáltatási címkék használatával SQL Database kapcsolatok nem lesznek hatással.
- A SQL Server JDBC illesztőprogram támogatott verzióival létesített kapcsolatok nem lesznek hatással. A támogatott JDBC-verziókért lásd: [a Microsoft JDBC-illesztőprogram letöltése SQL Serverhoz](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Mi a teendő, ha érintett

Azt javasoljuk, hogy engedélyezze a kimenő forgalmat az IP-címekre a 1433-es TCP-porton lévő összes [Azure SQL Database ÁTJÁRÓ IP](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) -címének, valamint a tűzfal eszközén a 11000-11999-es porttartomány. A porttartomány-tartományokkal kapcsolatos további információkért lásd: a [kapcsolatkérelem-házirend](sql-database-connectivity-architecture.md#connection-policy).

Az alkalmazásból a 4,0-es verziónál régebbi Microsoft JDBC illesztőprogramot használó alkalmazások kapcsolatainak érvényesítése sikertelen lehet. A Microsoft JDBC korábbi verziói a tanúsítvány Tárgy mezőjében a köznapi név (CN) függvényt használják. A megoldás célja, hogy a hostNameInCertificate tulajdonság értéke *. database.windows.net legyen. A hostNameInCertificate tulajdonság beállításával kapcsolatos további információkért lásd az SSL- [titkosítással való csatlakozást](/sql/connect/jdbc/connecting-with-ssl-encryption)ismertető témakört.

Ha a fenti megoldás nem működik, a következő URL-cím használatával SQL Database támogatási kérelmet nyújt a hoz: https://aka.ms/getazuresupport

## <a name="next-steps"></a>További lépések

- További információ az [Azure SQL kapcsolati architektúráról](sql-database-connectivity-architecture.md)