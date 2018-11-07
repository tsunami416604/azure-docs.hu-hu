---
title: Azure database biztonsági ellenőrzőlista |} A Microsoft Docs
description: Ez a cikk biztosít az Azure adatbázis biztonsági ellenőrzőlista készletét.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: db976fb61c5659fbdcb7ba30aa4f619d52d441c4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252394"
---
# <a name="azure-database-security-checklist"></a>Azure database biztonsági ellenőrzőlista

A biztonság növelése érdekében az Azure-adatbázis számos olyan beépített biztonsági vezérlőkkel használható a korlát és hozzáférés.

Ezek a következők:

-   A tűzfal, amely lehetővé teszi, hogy hozzon létre [tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) kapcsolat korlátozza az IP-címe
-   Kiszolgálószintű tűzfal elérhető az Azure Portalról
-   Adatbázisszintű tűzfalszabályok ssms-ben elérhető
-   Az adatbázishoz biztonságos kapcsolati karakterláncok használatával biztonságos kapcsolatok
-   Access management használata
-   Adattitkosítás
-   Az SQL Database naplózási funkciója
-   SQL Database-fenyegetések észlelése

## <a name="introduction"></a>Bevezetés
A felhőalapú számítástechnika új biztonsági paradigmákat, amely számos alkalmazás felhasználói számára, adatbázis-rendszergazdák és programozók ismeri igényel. Ennek eredményeképpen, amelyek végrehajtása egy felhő-infrastruktúra az észlelt biztonsági kockázatok miatt felügyeletéhez szívesen. Azonban nagy része a veszélyt elháríthatók, hogy a Microsoft Azure, Microsoft Azure SQL Database beépített biztonsági funkciók révén.

## <a name="checklist"></a>Ellenőrzőlista
Azt javasoljuk, hogy elolvasta a [Azure adatbázis ajánlott biztonsági eljárások](https://docs.microsoft.com/azure/security/azure-database-security-best-practices) cikk előtt az alábbi ellenőrzőlista áttekintése. A lehető leghatékonyabban ezzel az ellenőrzőlistával az ajánlott eljárások megismerése után tekintse át tudják. Az alábbi ellenőrzőlista használatával győződjön meg arról, hogy van-e a fontos hibák javításával, az Azure adatbázis-biztonsági, majd.


|Ellenőrzőlista kategória| Leírás|
| ------------ | -------- |
|**Adatok védelme**||
| <br> Mozgásérzékelő/átvitel titkosítása| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), adat-hálózatokhoz való áthelyezésekor az adattitkosítás.</li><li>Adatbázis alapján ügyfelekről érkező biztonságos kommunikációt igényel a [(táblázatos adatok Stream) TDS](https://msdn.microsoft.com/library/dd357628.aspx) keresztüli TLS (Transport Layer Security) protokoll.</li></ul> |
|<br>Titkosítás inaktív állapotban| <ul><li>[Transzparens adattitkosítás](https://go.microsoft.com/fwlink/?LinkId=526242), amikor az inaktív adatok bármely digitális formában fizikailag tárolja.</li></ul>|
|**Hozzáférés vezérlése**||  
|<br> Adatbázis-hozzáférés | <ul><li>[Hitelesítési](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) (az Azure Active Directory-hitelesítés) AD-hitelesítés az Azure Active Directory által felügyelt identitásokat használ.</li><li>[Engedélyezési](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) biztosítson a felhasználó számára a minimálisan szükséges.</li></ul> |
|<br>Alkalmazás-hozzáférés| <ul><li>[Biztonsági szint sor](https://msdn.microsoft.com/library/dn765131) (használatával biztonsági házirend, a felhasználó identitását, szerepkör vagy végrehajtási környezet alapján sorszintű hozzáférés korlátozása egy időben).</li><li>[A dinamikus Adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (használatával engedély & házirend, korlátozza a bizalmas adatok adatmaszkolás segít Önnek nem kiemelt jogosultságú felhasználók által)</li></ul>|
|**Proaktív figyeléshez**||  
| <br>Nyomkövetési & észlelése| <ul><li>[Naplózás](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) nyomon követi az adatbázisok eseményeit, és írja őket az auditnapló / tevékenység jelentkezzen be a [Azure Storage-fiók](https://docs.microsoft.com/azure/storage/storage-create-storage-account).</li><li>Nyomon követheti az Azure-adatbázis állapotfigyelő használatával [Azure Monitor tevékenységeket tartalmazó naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Veszélyforrások Detektálása](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések az adatbázishoz. </li></ul> |
|<br>Azure Security Center| <ul><li>[Adatok figyelés](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) az Azure Security Center figyelési megoldás az SQL és más Azure-szolgáltatások központi biztonsági.</li></ul>|       

## <a name="conclusion"></a>Összegzés
Azure Database egy robusztus cassandrára, a számos szervezet és szabályozási megfelelőségi követelmények teljesítése érdekében biztonsági szolgáltatások széles köre. Adatok védelme érdekében egyszerűen a fizikai hozzáférés az adatokhoz, és számos lehetőség használatával az adatokat a fájl-, oszlop- vagy a transzparens adattitkosítás, a cellaszintű titkosítás vagy a sorszintű biztonság sorszintű biztonságot. Mindig titkosított is lehetővé teszi a titkosított adatok, kapcsolatos művelet-végrehajtási megkönnyíti a frissítések alkalmazása folyamatát. Ezután naplók az SQL Database-tevékenység naplózása a hozzáférést biztosít a szükséges információkat, amely lehetővé teszi, hogy tudja, hogyan és mikor az adatokhoz.

## <a name="next-steps"></a>További lépések
Mindössze néhány lépés végrehajtásával fokozhatja az adatbázis védelmét a rosszindulatú felhasználókkal és a jogosulatlan hozzáféréssel szemben. Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

- Állítsa be a [tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a kiszolgálón, és vagy az adatbázis számára.
- Az adatok védelméhez [titkosítási](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Engedélyezése [SQL Database naplózási funkciójának](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).

