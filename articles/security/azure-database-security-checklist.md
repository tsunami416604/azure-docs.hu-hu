---
title: "Azure-adatbázis biztonsági ellenőrzőlista |} Microsoft Docs"
description: "Ez a cikk ellenőrzőlista biztosít az Azure-adatbázis biztonsági."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: tomsh
ms.openlocfilehash: 5047635555a6b4592c0714677c2b942e50bad344
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-security-checklist"></a>Azure-adatbázis biztonsági ellenőrzőlista

A biztonság növelése érdekében Azure adatbázis számos olyan beépített biztonsági vezérlők, amelyekkel korlátozás és a vezérlő eléréséhez.

Ezek a következők:

-   A tűzfal, amely lehetővé teszi, hogy hozzon létre [tűzfal-szabályok](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) kapcsolat korlátozza az IP-címe
-   Az Azure-portálon elérhető kiszolgálószintű tűzfal
-   Adatbázis-szintű tűzfalszabályok érhető el a szolgáltatáshoz az SSMS
-   Az adatbázis biztonságos kapcsolati karakterláncok használatával biztonságos kapcsolatot
-   Hozzáférés-kezelés
-   Adattitkosítás
-   SQL Database auditing
-   SQL-adatbázis fenyegetések észlelése

## <a name="introduction"></a>Bevezetés
A felhőalapú informatika igényel az új biztonsági mintáknak, amelyek számos alkalmazás felhasználóinak, adatbázis-rendszergazdák és programozók ismeretlen. Ennek eredményeképpen egyes szervezetek a következők határozatlanok adatkezelés észlelt biztonsági kockázatok miatt a felhőalapú infrastruktúra végrehajtásához. Azonban nagy részét ezen probléma enyhíthetők keresztül a Microsoft Azure és a Microsoft Azure SQL Database beépített biztonsági szolgáltatásai jobb megértése.

## <a name="checklist"></a>Feladatlista
Azt javasoljuk, hogy olvassa el a [Azure adatbázis ajánlott biztonsági eljárások](https://docs.microsoft.com/en-us/azure/security/azure-database-security-best-practices) cikk előtt tekintse át ezt az ellenőrző listát. Használja ki az alábbi ellenőrzőlista, Miután megismerte az ajánlott eljárásokkal lehet. Az alábbi ellenőrzőlista segítségével majd győződjön meg arról, hogy az Azure-adatbázis biztonsági már foglalkozott a fontos problémákat.


|Feladatlista kategória| Leírás|
| ------------ | -------- |
|**Adatok védelme**||
| <br> Mozgásérzékelő – / átvitel titkosítás| <ul><li>[Transport Layer Security](https://docs.microsoft.com/en-us/windows-server/security/tls/transport-layer-security-protocol), az adatok titkosításához adatok a hálózatokhoz való áthelyezésekor.</li><li>Adatbázis szükséges a biztonságos kommunikációt olyan ügyfelektől, alapján a [(tabulált adatfolyamban) TDS](https://msdn.microsoft.com/en-in/library/dd357628.aspx) keresztüli TLS (Transport Layer Security).</li></ul> |
|<br>Titkosítás inaktív állapotban| <ul><li>[Az átlátható adattitkosítási](http://go.microsoft.com/fwlink/?LinkId=526242), ha inaktív adatok fizikailag bármely digitális formában van tárolva.</li></ul>|
|**Elérés**||  
|<br> Adatbázis-hozzáférés | <ul><li>[Hitelesítési](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access) (Azure Active Directory-hitelesítés) AD hitelesítési használja az Azure Active Directory által kezelt identitások.</li><li>[Engedélyezési](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access) felhasználóknak adja meg a lehető legkevesebb szükséges jogosultsággal.</li></ul> |
|<br>Alkalmazás-hozzáférés| <ul><li>[Biztonsági szint sor](https://msdn.microsoft.com/library/dn765131) (használatával biztonsági házirend, a felhasználó identitását, a szerepkör vagy a végrehajtási környezet alapján sorszintű hozzáférés korlátozása egy időben).</li><li>[A dinamikus Adatmaszkolás](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started) (használatával engedély & házirend, korlátozza a bizalmas adatok veszélyeztetettségének által maszkolás a jogosulatlan felhasználók számára)</li></ul>|
|**Az előrejelzéses figyeléssel**||  
| <br>Követés & észlelése| <ul><li>[Naplózás](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) követi az adatbázisban, és egy naplófájlba írja őket / tevékenység jelentkezzen be a [Azure Storage-fiók](https://docs.microsoft.com/en-us/azure/storage/storage-create-storage-account).</li><li>Track Azure adatbázis állapotfigyelő segítségével [Azure figyelő tevékenységi naplóit](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Veszélyforrások Detektálása](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection) adatbázist érintő rendellenes tevékenységeket, amelyek esetleges biztonsági fenyegetéseket jelezhetnek a észleli. </li></ul> |
|<br>Azure Security Center| <ul><li>[Adatok figyelés](https://docs.microsoft.com/en-us/azure/security-center/security-center-enable-auditing-on-sql-databases) használja az Azure Security Center biztonsági központi felügyeleti megoldás az SQL és más Azure-szolgáltatásokkal.</li></ul>|     

## <a name="conclusion"></a>Összegzés
Azure-adatbázis egy robusztus adatbázis platform, amely sok szervezeti és szabályozási megfelelőségi követelményeknek funkciókat teljes tartománnyal. Adatok egyszerűen megvédheti a adatokhoz való fizikai hozzáférés vezérlése, és számos lehetőséget használ az adatok biztonsági a fájl-, oszlop-vagy sorszintű átlátható adattitkosítási, cellaszintű titkosítását alkalmazza vagy sorszintű biztonság. Mindig titkosított is lehetővé teszi a titkosított adatok műveleteket leegyszerűsítésével alkalmazás frissítések. Viszont naplófájlokat SQL adatbázis-tevékenység hozzáférést biztosít a szükséges információkat, így tudja, hogyan és mikor adatokhoz.

## <a name="next-steps"></a>Következő lépések
Az adatbázis rosszindulatú felhasználók vagy néhány egyszerű lépésben jogosulatlan hozzáférés elleni védelme is javítható. Ebben az oktatóanyagban elsajátíthatja, hogy:

- Állítson be [tűzfal-szabályok](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) adatbázis és a kiszolgálón.
- Az adatok védelméhez [titkosítási](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/sql-server-encryption).
- Engedélyezése [SQL Database auditing](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing).

