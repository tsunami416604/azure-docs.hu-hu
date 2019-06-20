---
title: Az Azure Security Centerben az Azure adat- és tárolási szolgáltatások védelme |} A Microsoft Docs
description: Ez a dokumentum címek javaslatok az Azure Security Centerben, amelyekkel védje meg adatait és az Azure SQL-szolgáltatás, és megfelel a biztonsági házirendek maradjon.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: monhaber
ms.openlocfilehash: 2ac0e4ebaafb8b0c9c79e885cecbefc5a65c1823
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275333"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Az Azure Security Centerben az Azure adat- és tárolási szolgáltatások védelme
Ez a témakör bemutatja, hogyan megtekintéséhez és adat-és a biztonsági javaslatok implementálása. Az Azure Security Center ezeket a javaslatokat is található, az Azure-erőforrások biztonsági állapotának elemzésekor.

## <a name="view-your-data-security-information"></a>Az adatok biztonsági információinak megtekintése

1. Az a **erőforrás biztonsági higiéniai** területén kattintson **adat-és**.

   ![Adatok és a tárolási erőforrásokhoz](./media/security-center-monitoring/click-data.png)

    A **adatbiztonság** lap, melyen az adatok erőforrásokkal kapcsolatos ajánlásokat.

     ![Adatforrások](./media/security-center-monitoring/sql-overview.png)

Ezen az oldalon a következőket teheti:

* Kattintson a **áttekintése** lap felsorolja az összes adat erőforrások vonatkozó javaslatok kijavítható. 
* Az egyes fülekre, és megtekintheti az ajánlások erőforrástípus szerint.

    > [!NOTE]
    > A tárolás titkosításáról további információkat az [Azure-tárfiókok titkosításának engedélyezése az Azure Security Centerben](security-center-enable-encryption-for-storage-account.md) című cikkben találhat.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Egy adatforrás, melyhez a javaslat szervizelése

1. Bármelyik erőforrás lapon kattintson egy erőforrást. A információkat tartalmazó oldal megnyitása kijavítható vonatkozó javaslatok listázása.

    ![Erőforrásadatok](./media/security-center-monitoring/sql-recommendations.png)

2. Egy javaslatra kattint. Az ajánlás oldal megnyitja és megjeleníti a **javítási lépések** történő megvalósításához.

   ![Javítási lépések](./media/security-center-monitoring/remediate1.png)

3. Kattintson a **művelet végrehajtása**. Az erőforrás-beállítások oldal jelenik meg.

    ![Az ajánlás engedélyezése](./media/security-center-monitoring/remediate2.png)

4. Kövesse a **javítási lépések** kattintson **mentése**.

## <a name="data-and-storage-recommendations"></a>Adatok és tárolás javaslatok

|Erőforrás típusa|Biztonsági pontszám|Ajánlás|Leírás|
|----|----|----|----|
|Tárfiók|20|Engedélyezni kell a biztonságos átvitelt a storage-fiókok|Biztonságos átvitel, amely arra kényszeríti a storage-fiók csak a biztonságos kapcsolat (HTTPS) érkező kérések fogadására lehetőség. HTTPS biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez, és védi az adatokat átvitel közben a hálózati réteg támadásoktól, például a man-in-the-middle, lehallgatást, és munkamenet-eltérítés.|
|Redis|20|A Redis gyorsítótárhoz kizárólag biztonságos kapcsolatot engedélyezni kell|Engedélyezi a Redis Azure Cache SSL-en keresztül csak kapcsolatokat. Biztonságos kapcsolatok használata biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez, és az átvitt adatokat védi a hálózati réteg támadásoktól, például a man-in-the-middle, lehallgatást, és munkamenet-eltérítés.|
|SQL|15|Engedélyezni kell az SQL Database-adatbázisok transzparens adattitkosítás|Inaktív adatok védelmét és megfelelőségi követelmények teljesítése érdekében a transzparens adattitkosítás engedélyezése.|
|SQL|15|SQL server-naplózás engedélyezni kell|Az Azure SQL-kiszolgáló naplózásának engedélyezése. (Csak az azure SQL-szolgáltatás esetében. Nem tartalmazza a virtuális gépeken futó SQL.)|
|A Data lake analytics|5|Engedélyezni kell a Data Lake Analytics-diagnosztikai naplók|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|A Data lake store|5|Az Azure Data Lake Store diagnosztikai naplóinak engedélyezni kell|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|SQL|30|Az SQL-adatbázisok biztonsági rések kell kijavítható.|SQL-sebezhetőségi felmérés megvizsgálja az adatbázis a biztonsági kockázatokat, és elérhetővé teszi a bármely gyakorlattól eltérő jelenségeket, például a konfigurációs hibák, a túlságosan széleskörű engedélyeket és a bizalmas adatok nem védett. A biztonsági réseket talált feloldása nagy mértékben javíthatják a az adatbázis biztonsági helyzetében.|
|SQL|20|Azure AD-rendszergazda az SQL server üzembe helyezése|Az Azure AD-hitelesítés engedélyezése az SQL Serverhez Azure AD-rendszergazda üzembe helyezhető. Az Azure AD-hitelesítés lehetővé teszi, hogy egyszerűsített engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezeléssel.|
|Tárfiók|15|Tűzfal és a virtuális hálózati konfiguráció a storage-fiókok hozzáférést korlátozva kell lennie.|A storage-fiók tűzfal beállításait a nem korlátozott hálózati hozzáférés naplózása. Konfigurálja a hálózati szabályok, az engedélyezett hálózatok csak alkalmazások férhetnek hozzá a tárfiók. Ahhoz, hogy a kapcsolatok meghatározott internetről, vagy olyan helyszíni ügyfelek, biztosíthat hozzáférést az adott Azure virtuális hálózatok forgalmát, vagy nyilvános internetes IP-címtartományok.|
|Tárfiók|1|Storage-fiókokat kell áttelepíteni az új Azure Resource Manager-erőforrások|A storage-fiókok új Azure Resource Manager-v2 használatával adja meg például a biztonsági fejlesztések: erősebb hozzáférés-vezérlés (RBAC), a jobb naplózás, a Resource Manager-alapú üzembe helyezés és a cégirányítási, elérését a felügyelt identitások, a key vaulthoz való hozzáférés titkos adatait, és az Azure AD-alapú hitelesítést és címkék támogatása és -erőforráscsoportok egyszerűbb biztonság kezelése.|

## <a name="see-also"></a>Lásd még
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következő témaköröket:

* [Virtuális gépek védelme az Azure Security Centerben](security-center-virtual-machine-recommendations.md)
* [Alkalmazások védelme az Azure Security Centerben](security-center-application-recommendations.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)

A Security Centerrel kapcsolatos további tudnivalókért lásd a következő témaköröket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
