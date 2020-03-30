---
title: Azure-adatbázis biztonsági ellenőrzőlistája| Microsoft dokumentumok
description: Ez a cikk az Azure-adatbázis biztonságához ellenőrzőlista készletét tartalmazza.
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
ms.openlocfilehash: d9283a36d5f7ccb82b2cc211485487d5a3dcce7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79201025"
---
# <a name="azure-database-security-checklist"></a>Az Azure adatbázis biztonsági ellenőrzőlistája

A biztonság növelése érdekében az Azure Database számos beépített biztonsági vezérlőt tartalmaz, amelyek segítségével korlátozhatja és szabályozhatja a hozzáférést.

Ezek a következők:

-    Tűzfal, amely lehetővé teszi az IP-cím szerint internetkapcsolatkorlátozást korlátozó [tűzfalszabályok](../../sql-database/sql-database-firewall-configure.md) létrehozását,
-    Az Azure Portalról elérhető kiszolgálószintű tűzfal
-    Az SSMS-ből elérhető adatbázis-szintű tűzfalszabályok
-    Biztonságos kapcsolat az adatbázissal biztonságos kapcsolati karakterláncokkal
-    Hozzáférés-kezelés használata
-    Adattitkosítás
-    Az SQL Database naplózási funkciója
-    SQL Database fenyegetésészlelése

## <a name="introduction"></a>Bevezetés
A felhőalapú számítástechnika új biztonsági paradigmákat igényel, amelyek sok alkalmazásfelhasználó, adatbázis-rendszergazda és programozó számára ismeretlenek. Ennek eredményeképpen egyes szervezetek nem szívesen valósítanak meg egy felhőalapú infrastruktúrát az adatkezeléshez az észlelt biztonsági kockázatok miatt. Ennek az aggálynak a nagy része azonban enyhíthető a Microsoft Azure és a Microsoft Azure SQL Database beépített biztonsági funkcióinak jobb megértése révén.

## <a name="checklist"></a>Ellenőrzőlista
Azt javasoljuk, hogy olvassa el az [Azure Database Security gyakorlati tanácsok](database-best-practices.md) cikket, mielőtt áttekintette ezt az ellenőrzőlistát. Ön képes lesz arra, hogy a legtöbbet hozza ki ezt az ellenőrzőlistát, miután megértette a legjobb gyakorlatokat. Ezután használhatja ezt az ellenőrzőlistát, hogy megbizonyosodjon arról, hogy az Azure-adatbázis biztonsága fontos problémák at.


|Ellenőrzőlista kategória| Leírás|
| ------------ | -------- |
|**Adatok védelme**||
| <br> Titkosítás mozgásban/átvitelben| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), adattitkosításhoz, amikor az adatok a hálózatokba költöznek.</li><li>Az adatbázis biztonságos kommunikációt igényel az ügyfelektől a [TLS protokollon (TLS) alapuló TLS](https://msdn.microsoft.com/library/dd357628.aspx) protokoll (Transport Layer Security) alapján.</li></ul> |
|<br>Titkosítás inaktív állapotban| <ul><li>[Transzparens adattitkosítás](https://go.microsoft.com/fwlink/?LinkId=526242), ha az inaktív adatok fizikailag bármilyen digitális formában tárolódnak.</li></ul>|
|**Hozzáférés vezérlése**||  
|<br> Adatbázis-hozzáférés | <ul><li>[Hitelesítés](../../sql-database/sql-database-manage-logins.md) (Azure Active Directory-hitelesítés) AD-hitelesítés az Azure Active Directory által kezelt identitásokat használja.</li><li>[Az engedélyezés](../../sql-database/sql-database-manage-logins.md) a szükséges legkevesebb jogosultságot biztosítja a felhasználóknak.</li></ul> |
|<br>Alkalmazás-hozzáférés| <ul><li>[Sorszintű biztonság](https://msdn.microsoft.com/library/dn765131) (A biztonsági házirend használata, ugyanakkor a felhasználó identitása, szerepköre vagy végrehajtási környezete alapján korlátozza a sorszintű hozzáférést).</li><li>[Dinamikus adatmaszkolás](../../sql-database/sql-database-dynamic-data-masking-get-started.md) (Az engedély& házirend használatával korlátozza a bizalmas adatok nak való kitettséget azáltal, hogy nem emelt szintű jogosultsággal rendelkező felhasználóknak használja)</li></ul>|
|**Proaktív figyelés**||  
| <br>Követés & észlelés| <ul><li>[A naplózás](../../sql-database/sql-database-auditing.md) nyomon követi az adatbázis-eseményeket, és az [Azure Storage-fióknaplóba/](../../storage/common/storage-create-storage-account.md)Tevékenységnaplóba írja őket.</li><li>Az Azure-adatbázis állapotának nyomon követése az [Azure Monitor tevékenységnaplók](../../azure-monitor/platform/platform-logs-overview.md)használatával.</li><li>[A fenyegetésészlelés](../../sql-database/sql-database-threat-detection.md) észleli az adatbázispotenciális biztonsági fenyegetéseit jelző rendellenes adatbázis-tevékenységeket. </li></ul> |
|<br>Azure Security Center| <ul><li>[Adatfigyelés](../../security-center/security-center-enable-auditing-on-sql-databases.md) Használja az Azure Security Centert az SQL és más Azure-szolgáltatások központosított biztonsági figyelési megoldásaként.</li></ul>|        

## <a name="conclusion"></a>Összegzés
Az Azure Database egy robusztus adatbázisplatform, amely számos szervezeti és szabályozási megfelelőségi követelménynek megfelelő biztonsági funkciók teljes skálájával rendelkezik. Az adatok védelme az adatokhoz való fizikai hozzáférés szabályozásával, valamint az adatbiztonság különböző lehetőségeivel az Átlátszó adattitkosítás, cellaszintű titkosítás vagy sorszintű biztonság segítségével. Mindig titkosított is lehetővé teszi a titkosított adatok elleni műveleteket, leegyszerűsítve az alkalmazásfrissítések folyamatát. Az SQL Database-tevékenység naplózási naplóihoz való hozzáférés viszont biztosítja a szükséges információkat, így megtudhatja, hogyan és mikor érhetők el az adatokhoz.

## <a name="next-steps"></a>További lépések
Mindössze néhány lépés végrehajtásával fokozhatja az adatbázis védelmét a rosszindulatú felhasználókkal és a jogosulatlan hozzáféréssel szemben. Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

- Állítson be [tűzfalszabályokat](../../sql-database/sql-database-firewall-configure.md) a kiszolgálóhoz és adatbázishoz.
- Védje adatait [titkosítással.](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption)
- Engedélyezze [az SQL Database naplózását](../../sql-database/sql-database-auditing.md).

