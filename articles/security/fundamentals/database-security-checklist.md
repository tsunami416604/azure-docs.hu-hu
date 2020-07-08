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
ms.openlocfilehash: fc79d7204ba360696b8d9411cd56efd09d1678dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021840"
---
# <a name="azure-database-security-checklist"></a>Az Azure Database biztonsági ellenőrzőlistája

A biztonság növelése érdekében az Azure Database számos beépített biztonsági vezérlőt tartalmaz, amelyek segítségével korlátozható és szabályozható a hozzáférés.

Ezek a következők:

-    Tűzfal, amely lehetővé teszi, hogy az IP-cím alapján korlátozza a kapcsolatra [vonatkozó tűzfalszabályok](../../azure-sql/database/firewall-configure.md) létrehozását,
-    A Azure Portal elérhető kiszolgáló szintű tűzfal
-    SSMS elérhető adatbázis-szintű tűzfalszabályok
-    Biztonságos kapcsolódás az adatbázishoz biztonságos kapcsolati karakterláncok használatával
-    Hozzáférés-kezelés használata
-    Adattitkosítás
-    Az SQL Database naplózási funkciója
-    SQL Database fenyegetések észlelése

## <a name="introduction"></a>Introduction (Bevezetés)
A felhő-számítástechnika olyan új biztonsági paradigmokat igényel, amelyek számos alkalmazás-felhasználó, adatbázis-rendszergazda és programozó számára ismeretlenek. Ennek eredményeképpen néhány szervezet vonakodik az adatkezeléshez szükséges felhőalapú infrastruktúra megvalósításában, az észlelt biztonsági kockázatok miatt. A probléma nagy része azonban a Microsoft Azureba beépített biztonsági funkciók jobb megismerésével és Microsoft Azure SQL Databaseával is enyhíthető.

## <a name="checklist"></a>Ellenőrzőlista
Javasoljuk, hogy az ellenőrzőlista áttekintése előtt olvassa el az [Azure-adatbázis biztonsági eljárásairól](database-best-practices.md) szóló cikket. Az ajánlott eljárások megismerése után a lehető legtöbbet hozhatja ki ebből az ellenőrzőlistából. Ezt a feladatlistát követve meggyőződhet arról, hogy az Azure Database biztonságával kapcsolatos fontos problémák merültek fel.


|Ellenőrzőlista kategóriája| Description|
| ------------ | -------- |
|**Az adatvédelem**||
| <br> Titkosítás a mozgásban/átvitelben| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol)az adattitkosításhoz, amikor az adatátvitelt a hálózatokra helyezi.</li><li>Az adatbázishoz biztonságos kommunikáció szükséges az ügyfelektől a (z) [TDS (táblázatos adatfolyam)](https://msdn.microsoft.com/library/dd357628.aspx) protokollon keresztül a TLS protokoll (Transport Layer Security) alapján.</li></ul> |
|<br>Titkosítás inaktív állapotban| <ul><li>[Transzparens adattitkosítás](https://go.microsoft.com/fwlink/?LinkId=526242), ha az inaktív adattárolást fizikailag bármilyen digitális formában tárolják.</li></ul>|
|**Hozzáférés vezérlése**||  
|<br> Adatbázis-hozzáférés | <ul><li>A [hitelesítési](../../azure-sql/database/logins-create-manage.md) (Azure Active Directory hitelesítés) ad-hitelesítés Azure Active Directory által felügyelt identitásokat használ.</li><li>Az [engedélyezési](../../azure-sql/database/logins-create-manage.md) jogosultsággal rendelkező felhasználóknak a legkevésbé szükséges jogosultságokkal kell rendelkezniük.</li></ul> |
|<br>Alkalmazás-hozzáférés| <ul><li>A [sor szintjének biztonsága](https://msdn.microsoft.com/library/dn765131) (a biztonsági házirend használatával egyidejűleg a felhasználó identitása, szerepköre vagy végrehajtási környezete alapján korlátozza a sor szintű hozzáférést).</li><li>[Dinamikus adatmaszkolás](../../azure-sql/database/dynamic-data-masking-overview.md) (engedélyezési & házirend használata, a bizalmas adatok expozíciójának korlátozása a nem privilegizált felhasználóknak való maszkolással)</li></ul>|
|**Proaktív figyelés**||  
| <br>& észlelésének nyomon követése| <ul><li>A [naplózás](../../sql-database/sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és az [Azure Storage-fiókban](../../storage/common/storage-create-storage-account.md)lévő naplóba/tevékenység naplóba írja azokat.</li><li>Az Azure-adatbázis állapotának nyomon követése [Azure monitor tevékenységi naplók](../../azure-monitor/platform/platform-logs-overview.md)használatával.</li><li>A [veszélyforrások észlelése](../../azure-sql/database/threat-detection-configure.md) rendellenes adatbázis-tevékenységeket észlel, ami az adatbázis potenciális biztonsági fenyegetéseket jelez. </li></ul> |
|<br>Azure Security Center| <ul><li>[Adatfigyelés](../../security-center/security-center-enable-auditing-on-sql-databases.md) A Azure Security Center központi biztonsági monitorozási megoldásként használható az SQL és más Azure-szolgáltatások számára.</li></ul>|        

## <a name="conclusion"></a>Összegzés
Az Azure Database egy robusztus adatbázis-platform, amely a számos szervezeti és szabályozási megfelelőségi követelménynek megfelelő biztonsági funkciók széles skáláját tartalmazza. Az adatvédelmet az adataihoz való fizikai hozzáférés szabályozásával, valamint a fájl-, oszlop-vagy sorcsoport szintjén a transzparens adattitkosítás, a sejtek szintjének titkosításával vagy a sorok szintjének biztonságával könnyedén védetté teheti. A Always Encrypted a titkosított adatok elleni műveleteket is lehetővé teszi, leegyszerűsítve az alkalmazások frissítéseinek folyamatát. A SQL Database tevékenység naplózási naplóihoz való hozzáférés pedig a szükséges információkat nyújtja, így megtudhatja, hogyan és mikor férhet hozzá az adatokhoz.

## <a name="next-steps"></a>További lépések
Mindössze néhány lépés végrehajtásával fokozhatja az adatbázis védelmét a rosszindulatú felhasználókkal és a jogosulatlan hozzáféréssel szemben. Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

- [Tűzfalszabályok](../../azure-sql/database/firewall-configure.md) beállítása a kiszolgálóhoz és az adatbázishoz.
- Az adatainak védelme [titkosítással](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- [SQL Database naplózás](../../sql-database/sql-database-auditing.md)engedélyezése.

