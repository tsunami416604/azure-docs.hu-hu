---
title: Csatlakozás a Microsoft Advanced Threat Analytics az Azure Security Centerben |} Microsoft Docs
description: Ismerje meg, hogyan integrálható az Azure Security Center a Microsoft Advanced Threat Analytics.
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: yurid
ms.openlocfilehash: fb32fc55288229fb1d09dce98b6092d38c10abf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Csatlakozás a Microsoft Advanced Threat Analytics az Azure Security Centerben
Ez a dokumentum segítséget nyújt a Microsoft Advanced Threat Analytics és az Azure Security Center közötti integráció konfigurálásához.

## <a name="why-add-advanced-threat-analytics-data"></a>Miért bővítjük Advanced Threat Analytics?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) egy helyszíni platform, amely segítséget nyújt a gyanús felhasználói viselkedés. Csatlakoztatott, elkülönítésével tudja tekinteni a Security Center az ATA által észlelt gyanús tevékenységek. Ez az integráció lehetővé teszi nézetben összefüggéseket, és vizsgálja meg a hibrid felhő-munkaterhelések biztonsági központ kapcsolódó összes biztonsági értesítést. 

## <a name="how-do-i-configure-this-integration"></a>Ez az integráció konfigurálása
Feltételezve, hogy már rendelkezik az ATA telepítve, és megfelelően működik-e a helyszíni, kövesse az alábbi lépéseket az integráció konfigurálásához:

1. Jelentkezzen be az ATA-központ, és az ATA-portál eléréséhez.
2. Kattintson a **Syslog-kiszolgálónak** a bal oldali ablaktáblán.

    ![Syslog-kiszolgálónak](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. Az a **Syslog server endpoint** mezőben írja be a 127.0.0.7 (legyen ez a cím), majd írja be a 5114 a porton (ajánlott). Bár a portszám ajánlás olyan környezetekben, bármely egyedi portszámot kell működnie. Hagyja bejelölve az összes többi kapcsolóval, és kattintson a **mentése**.
4. Kattintson a **értesítések** a bal oldali ablaktáblán, és engedélyezése (ajánlott) összes Syslog-értesítéseket, a következő ábrán látható módon:

    ![Értesítések](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Kattintson a **Save** (Mentés) gombra.
6. Nyissa meg a **Security Center** irányítópultját.
7. A bal oldali ablaktábláján kattintson **biztonsági megoldások**.
8. A **Advanced Threat Analytics**, kattintson a **hozzáadása**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Ugrás az utolsó lépés, és kattintson a **letöltési ügynök**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. Az a **új-Azure számítógép hozzáadása** lapon, válassza ki a munkaterületen.

    ![Nem Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. Az a **közvetlen ügynök** lapon a megfelelő Windows-ügynök letöltése, és készítsen feljegyzéseket az **munkaterület azonosítója** és **elsődleges kulcs**.

    ![Közvetlen ügynök](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Telepítse az ügynököt az ATA-központ. A telepítés során ügyeljen arra, hogy a beállításnak a **az ügynök csatlakoztatása az Azure Naplóelemzés**, és adja meg a *munkaterület azonosítója*, és *elsődleges kulcs* kérésekor.


Fejezze be a telepítést, az integráció befejeződött, és új riasztásokat a Security Center az ATA által küldött látni fogja a **keresési** eredménye. A megoldás megjelenik a **biztonsági megoldások** lap **kapcsolódó megoldások**. 

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerte a Security Center a Microsoft ATA csatlakozni. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Az Azure Active Directory Identity Protection csatlakoztatása az Azure Security Centerhez](security-center-aadip-integration.md)
* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md) – útmutató az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) – útmutató kezelése és válaszadás a biztonsági riasztásokra.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Az Azure Security Center adatainak biztonsági](security-center-data-security.md) -megtudhatja, hogyan adatok felügyelt és a Security Center védelmét.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági híreket és információkat.


