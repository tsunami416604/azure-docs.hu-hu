---
title: Az Azure SQL-szolgáltatás és az Azure Security Center adatok védelme |} A Microsoft Docs
description: Ez a dokumentum címek javaslatok az Azure Security Centerben, amelyekkel védje meg adatait és az Azure SQL-szolgáltatás, és megfelel a biztonsági házirendek maradjon.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: terrylan
ms.openlocfilehash: ea014aae49ec322e9a1f1222c881885b84e87584
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311772"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Az Azure SQL-szolgáltatás és az Azure Security Center adatok védelme
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Ha a Security Center azonosítja a potenciális biztonsági réseket, javaslatok, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán hoz létre.  Javaslatok alkalmazása az Azure-erőforrástípus: virtuális gépek (VM), hálózati, SQL és az adatokhoz és alkalmazásokhoz.

Ez a cikk foglalkozik az Azure SQL-szolgáltatás és az adatokra vonatkozó javaslatok. Az Azure SQL-kiszolgáló és adatbázis-titkosítási SQL-adatbázisok és az Azure storage-fiók titkosításának engedélyezésével engedélyezése naplózásának engedélyezésével center javaslatokat.  Referenciaként az alábbi táblázat segítségével segítségével megismerheti az elérhető SQL szolgáltatás és adat javaslatokat, és mindegyik funkciója alkalmazásuk esetén.
### <a name="monitor-data-security"></a>Adatbiztonság monitorozása

A **Megelőzés** szakasz **Adatbiztonság** elemére kattintva megnyithatja az **Adatforrások** panelt, amelyen az SQL és a Storage használatával kapcsolatos javaslatokat olvashat. Ezenfelül általános [javaslatokat](security-center-sql-service-recommendations.md) is talál itt az adatbázis állapotára vonatkozóan. A tárolás titkosításáról további információkat az [Azure-tárfiókok titkosításának engedélyezése az Azure Security Centerben](security-center-enable-encryption-for-storage-account.md) című cikkben találhat.

![Adatforrások](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Az **SQL Recommendations** (Javaslatok az SQL használatával kapcsolatban) területen bármely javaslatra kattintva részletesebb információt kap a probléma megoldásához szükséges további intézkedésről. A következő példában a **Database Auditing & Threat detection on SQL databases** (Naplózás és fenyegetésészlelés az SQL-adatbázisokban) javaslat kibontott nézete látható.

![SQL-javaslat részletei](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Az **Naplózás és fenyegetésészlelés engedélyezése az SQL-adatbázisokban** panelen a következő információk láthatók:

* Az SQL-adatbázisok listája
* A kiszolgáló, amely tárolja ezeket
* Az, hogy a beállítás a kiszolgálóról öröklődött, vagy konkrétan az adott adatbázisra vonatkozik
* Az aktuális állapot
* A probléma súlyossága

Ha a javaslat megoldása érdekében rákattint az adatbázisra, az alábbi képernyőn látható módon megnyílik az **Naplózás és fenyegetésészlelés** panel.

![Naplózás és fenyegetésészlelés](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

A naplózás engedélyezéséhez módosítsa az **Auditing** (Naplózás) beállítását **ON** (BE) értékre.

## <a name="data-and-storage-recommendations"></a>Adatok és tárolás javaslatok

|Erőforrás típusa|Biztonsági pontszám|Ajánlás|Leírás|
|----|----|----|----|
|Tárfiók|20|A storage-fiók biztonságos átvitel megkövetelése|Biztonságos átvitel, amely arra kényszeríti a storage-fiók csak a biztonságos kapcsolat (HTTPS) érkező kérések fogadására lehetőség. HTTPS használata biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez, és man-in-the-middle, lehallgatást, és a munkamenet-eltérítés – például a hálózati réteg támadások ellen védi a az átvitt adatokat.|
|Redis|20|Csak a Redis Cache biztonságos kapcsolatok engedélyezése|Csak a Redis Cache SSL-en keresztül kapcsolatok engedélyezéséhez. Biztonságos kapcsolatok használata biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez, és az átvitt adatokat védi a hálózati réteg támadásoktól, például a man-in-the-middle, lehallgatást, és munkamenet-eltérítés.|
|SQL|15|Az SQL Database-adatbázisok transzparens adattitkosítás engedélyezése|Transzparens adattitkosítás engedélyezése az inaktív adatok védelméhez és a megfelelőségi követelmények betartásához.|
|SQL|15|SQL Serverek naplózásának engedélyezése|Az Azure SQL-kiszolgáló naplózásának engedélyezése. (Csak az azure SQL-szolgáltatás esetében. Nem tartalmazza a virtuális gépeken futó SQL.)|
|SQL|15|SQL-adatbázis naplózásának engedélyezése|Az Azure SQL-adatbázis naplózásának engedélyezése. (Csak az azure SQL-szolgáltatás esetében. Nem tartalmazza a virtuális gépeken futó SQL.)|
|A Data lake analytics|15|Titkosítás inaktív állapotban, a Data Lake Analytics engedélyezése|A Data Lake Analytics az inaktív adatok védelme transzparens adattitkosítás engedélyezése. Titkosítás inaktív állapotban transzparens módon történik, ami azt jelenti, hogy a Data Lake Analytics automatikusan titkosítja az adatokat, és mindig visszafejti az adatokat lekérés előtt. Nem változtak, szükséges az alkalmazások és szolgáltatások, amelyek a titkosítás miatt a Data Lake Analytics interakciót. Titkosítás inaktív állapotban minimalizálja az adatvesztést a lopás veszélye, és emellett segít az előírt megfelelőségi követelmények.|
|A Data lake store|15|Engedélyezze a titkosítást a Data Lake Store REST|A Data Lake Store az inaktív adatok védelme transzparens adattitkosítás engedélyezése. Titkosítás inaktív állapotban transzparens módon történik, ami azt jelenti, hogy a Data Lake Store automatikusan titkosítja az adatokat, és mindig visszafejti az adatokat lekérés előtt. Az alkalmazás- és titkosítási befogadásához Data Lake Store-ral kommunikáló szolgáltatások semmilyen módosítást nem kell. Titkosítás inaktív állapotban minimalizálja az adatvesztést a lopás veszélye, és emellett segít az előírt megfelelőségi követelmények.|
|Tárfiók|15|Az Azure Storage-fiók titkosításának engedélyezése|Az Azure Storage Service Encryption engedélyezze az inaktív adatok. Storage Service Encryption (SSE) működik, az adatok titkosítása az Azure storage-bA írt, és visszafejti őket a lekérés előtt. SSE jelenleg csak az Azure Blob service érhető el, és a blokkblobok, lapblobok és használható, és hozzáfűző blobok.|
|A Data lake analytics|5|A Data Lake Analytics diagnosztikai naplóinak engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|A Data lake store|5|Az Azure Data Lake Store diagnosztikai naplóinak engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|SQL|30|Az SQL-adatbázisok biztonsági rések|SQL-sebezhetőségi felmérés megvizsgálja az adatbázis a biztonsági kockázatokat, és elérhetővé teszi a bármely gyakorlattól eltérő jelenségeket, például a konfigurációs hibák, a túlságosan széleskörű engedélyeket és a bizalmas adatok nem védett. A biztonsági réseket talált feloldása nagy mértékben javíthatják a az adatbázis biztonsági helyzetében.|
|SQL|20|Azure AD-rendszergazda az SQL server üzembe helyezése|Az Azure AD-hitelesítés engedélyezése az SQL Serverhez Azure AD-rendszergazda üzembe helyezhető. Az Azure AD-hitelesítés lehetővé teszi, hogy egyszerűsített engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezeléssel.|
|Tárfiók|15|Tiltsa le a tárfiók nem korlátozott hálózati hozzáférést|A storage-fiók tűzfal beállításait a nem korlátozott hálózati hozzáférés naplózása. Konfigurálja a hálózati szabályok, az engedélyezett hálózatok csak alkalmazások férhetnek hozzá a tárfiók. Az adott internetről fogadjon kapcsolatokat, vagy egy helyszíni ügyfelek, a hozzáférés adható adott Azure virtuális hálózatok forgalmát, vagy nyilvános internetes IP-címtartományok.|
|Tárfiók|1||Storage-fiókok új AzureRM-erőforrások áttelepítése|Használja az új Azure Resource Manager 2-es verzió, a storage-fiókokat adja meg például a biztonsági fejlesztések: erősebb hozzáférés-vezérlés (RBAC), a jobb naplózás, a Resource Manager-alapú üzembe helyezés és a cégirányítási, elérését a felügyelt identitások, a key vaulthoz való hozzáférés titkos kódok, az Azure AD-alapú hitelesítést és címkék támogatása és -erőforráscsoportok egyszerűbb biztonság kezelése.|



## <a name="see-also"></a>Lásd még
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:

* [Virtuális gépek védelme az Azure Security Centerben](security-center-virtual-machine-recommendations.md)
* [Alkalmazások védelme az Azure Security Centerben](security-center-application-recommendations.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-azure-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
