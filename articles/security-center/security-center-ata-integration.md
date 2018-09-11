---
title: Csatlakozás a Microsoft Advanced Threat Analytics az Azure Security Centerbe |} A Microsoft Docs
description: Ismerje meg, hogy az Azure Security Center hogyan integrálható a Microsoft Advanced Threat Analytics.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: yurid
ms.openlocfilehash: 1922bc25eb7072341848dd7081c31ba6b1f07a3c
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298828"
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Csatlakozás a Microsoft Advanced Threat Analytics az Azure Security centerhez
Ebből a dokumentumból megismerheti, hogy a Microsoft Advanced Threat Analytics és az Azure Security Center közötti integráció beállítása.

## <a name="why-add-advanced-threat-analytics-data"></a>Miért az Advanced Threat Analytics-adatok hozzáadása?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) egy helyszíni platform, amely segít a gyanús felhasználói viselkedés észlelését. Ha csatlakoztatva van, Ön megtekintheti a Security Center ATA által észlelt gyanús tevékenységeket. Ez az integráció lehetővé teszi a megtekintése, összehasonlítása és a hibrid felhőalapú számítási feladatokhoz, a Security Center összes biztonsági riasztások vizsgálata. 

## <a name="how-do-i-configure-this-integration"></a>Hogyan konfigurálhatom ezt az integrációt?
Feltételezve, hogy már rendelkezik az ATA telepítve van, és megfelelően működik a helyszíni, kövesse az alábbi lépéseket az integráció konfigurálásához:

1. Jelentkezzen be az ATA-központ és az ATA-portál eléréséhez.
2. Kattintson a **Syslog-kiszolgáló** a bal oldali panelen.

    ![Syslog-kiszolgáló](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. Az a **Syslog server endpoint** mezőben írja be a 127.0.0.7 címet (ezt a címet kell lennie), majd írja be az 5114 a portszámot (javasolt). Bár a portszám csupán javaslat, bármilyen egyedi port használható. Hagyja meg az összes többi beállítást, és kattintson a **mentése**.
4. Kattintson a **értesítések** a bal oldali panelen, és a (javasolt) az összes Syslog-értesítések engedélyezése a következő képen látható módon:

    ![Értesítések](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Kattintson a **Save** (Mentés) gombra.
6. Nyissa meg a **Security Center** irányítópultját.
7. A bal oldali panelen kattintson a **biztonsági megoldások**.
8. A **Advanced Threat Analytics**, kattintson a **hozzáadása**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Ugrás az utolsó lépést, és kattintson a **letöltési ügynök**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. Az a **új nem Azure-beli számítógép hozzáadása** lapon, válassza ki a munkaterületet.

    ![Nem Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. Az a **közvetlen ügynök** lapon, a megfelelő Windows-ügynök letöltése és feljegyzéseket az **munkaterület-Azonosítót** és **elsődleges kulcs**.

    ![Közvetlen ügynök](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Ez az ügynök telepítése az ATA-központ. A telepítés során ügyeljen arra, hogy válassza a **az ügynök csatlakoztatása az Azure Log Analyticshez**, és adja meg a *munkaterület-Azonosítót*, és *elsődleges kulcs* igénylésekor.


Miután a telepítés befejezéséhez, az integráció befejezése és új riasztásokat a Security Center az ATA által küldött látni fogja a **keresési** eredményt. A megoldás megjelenik a **biztonsági megoldások** lap **csatlakoztatott megoldások**. 

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerkedhetett a Security Center a Microsoft ATA csatlakoztatása. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Az Azure Active Directory Identity Protection csatlakoztatása az Azure Security Centerhez](security-center-aadip-integration.md)
* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és -erőforráscsoportok.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) – ismerje meg, hogyan kezelése és válaszadás a biztonsági riasztásokra.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Az Azure Security Center által nyújtott adatbiztonság](security-center-data-security.md) – ismerje meg, hogy az adatokat, és hogyan gondoskodik a védelmükről a Security Centerben.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Az Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági hírek és információ.


