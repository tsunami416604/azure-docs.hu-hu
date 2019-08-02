---
title: Az Azure Database biztonsági ellenőrzőlistája | Microsoft Docs
description: Ez a cikk az Azure Database biztonságára vonatkozó ellenőrzőlista-készletet tartalmaz.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 3f51f23f47cfbd81c71234bc2ce5a0adeeadd3f9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727368"
---
# <a name="azure-database-security-checklist"></a>Az Azure Database biztonsági ellenőrzőlistája

A biztonság növelése érdekében az Azure Database számos beépített biztonsági vezérlőt tartalmaz, amelyek segítségével korlátozható és szabályozható a hozzáférés.

Ezek a következők:

-   Tűzfal, amely lehetővé teszi, hogy az IP-cím alapján korlátozza a kapcsolatra [vonatkozó tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) létrehozását,
-   A Azure Portal elérhető kiszolgáló szintű tűzfal
-   SSMS elérhető adatbázis-szintű tűzfalszabályok
-   Biztonságos kapcsolódás az adatbázishoz biztonságos kapcsolati karakterláncok használatával
-   Hozzáférés-kezelés használata
-   Adattitkosítás
-   Az SQL Database naplózási funkciója
-   SQL Database-fenyegetések észlelése

## <a name="introduction"></a>Bevezetés
A felhő-számítástechnika olyan új biztonsági paradigmokat igényel, amelyek számos alkalmazás-felhasználó, adatbázis-rendszergazda és programozó számára ismeretlenek. Ennek eredményeképpen néhány szervezet vonakodik az adatkezeléshez szükséges felhőalapú infrastruktúra megvalósításában, az észlelt biztonsági kockázatok miatt. A probléma nagy része azonban a Microsoft Azureba beépített biztonsági funkciók jobb megismerésével és Microsoft Azure SQL Databaseával is enyhíthető.

## <a name="checklist"></a>Ellenőrzőlista
Javasoljuk, hogy az ellenőrzőlista áttekintése előtt olvassa el az [Azure-adatbázis biztonsági eljárásairól](https://docs.microsoft.com/azure/security/azure-database-security-best-practices) szóló cikket. Az ajánlott eljárások megismerése után a lehető legtöbbet hozhatja ki ebből az ellenőrzőlistából. Ezt a feladatlistát követve meggyőződhet arról, hogy az Azure Database biztonságával kapcsolatos fontos problémák merültek fel.


|Ellenőrzőlista kategóriája| Leírás|
| ------------ | -------- |
|**Az adatvédelem**||
| <br> Titkosítás a mozgásban/átvitelben| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol)az adattitkosításhoz, amikor az adatátvitelt a hálózatokra helyezi.</li><li>Az adatbázishoz biztonságos kommunikáció szükséges az ügyfelektől a (z) [TDS (táblázatos adatfolyam)](https://msdn.microsoft.com/library/dd357628.aspx) protokollon keresztül a TLS protokoll (Transport Layer Security) alapján.</li></ul> |
|<br>Titkosítás inaktív állapotban| <ul><li>[Transzparens adattitkosítás](https://go.microsoft.com/fwlink/?LinkId=526242), ha az inaktív adattárolást fizikailag bármilyen digitális formában tárolják.</li></ul>|
|**Hozzáférés vezérlése**||  
|<br> Adatbázis-hozzáférés | <ul><li>[Hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) (Azure Active Directory hitelesítés) Az AD-hitelesítés Azure Active Directory által felügyelt identitásokat használ.</li><li>Az [engedélyezési](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) jogosultsággal rendelkező felhasználóknak a legkevésbé szükséges jogosultságokkal kell rendelkezniük.</li></ul> |
|<br>Alkalmazás-hozzáférés| <ul><li>[Sor szintű biztonság](https://msdn.microsoft.com/library/dn765131) (A biztonsági szabályzattal egyidejűleg korlátozza a sor szintű hozzáférést a felhasználó identitása, szerepköre vagy végrehajtási környezete alapján).</li><li>[Dinamikus Adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (Az engedélyezési & házirend használatával korlátozza a bizalmas adatokkal való érintkezést a nem privilegizált felhasználóknak való maszkolással)</li></ul>|
|**Proaktív figyelés**||  
| <br>& Észlelésének nyomon követése| <ul><li>A [naplózás](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) nyomon követi az adatbázis eseményeit, és az [Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account)-fiókban lévő naplóba/tevékenység naplóba írja azokat.</li><li>Az Azure-adatbázis állapotának nyomon követése [Azure monitor tevékenységi naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)használatával.</li><li>[](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) A veszélyforrások észlelése rendellenes adatbázis-tevékenységeket észlel, ami az adatbázis potenciális biztonsági fenyegetéseket jelez. </li></ul> |
|<br>Azure Security Center| <ul><li>[Adatfigyelés](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) A Azure Security Center központi biztonsági monitorozási megoldásként használható az SQL és más Azure-szolgáltatások számára.</li></ul>|       

## <a name="conclusion"></a>Összegzés
Az Azure Database egy robusztus adatbázis-platform, amely a számos szervezeti és szabályozási megfelelőségi követelménynek megfelelő biztonsági funkciók széles skáláját tartalmazza. Az adatvédelmet az adataihoz való fizikai hozzáférés szabályozásával, valamint a fájl-, oszlop-vagy sorcsoport szintjén a transzparens adattitkosítás, a sejtek szintjének titkosításával vagy a sorok szintjének biztonságával könnyedén védetté teheti. A Always Encrypted a titkosított adatok elleni műveleteket is lehetővé teszi, leegyszerűsítve az alkalmazások frissítéseinek folyamatát. A SQL Database tevékenység naplózási naplóihoz való hozzáférés pedig a szükséges információkat nyújtja, így megtudhatja, hogyan és mikor férhet hozzá az adatokhoz.

## <a name="next-steps"></a>További lépések
Mindössze néhány lépés végrehajtásával fokozhatja az adatbázis védelmét a rosszindulatú felhasználókkal és a jogosulatlan hozzáféréssel szemben. Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

- [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) beállítása a kiszolgálóhoz és az adatbázishoz.
- Az adatainak védelme [titkosítással](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- [SQL Database naplózás](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)engedélyezése.

