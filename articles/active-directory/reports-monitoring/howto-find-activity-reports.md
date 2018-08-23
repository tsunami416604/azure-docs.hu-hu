---
title: Keresse meg az Azure Active Directory felhasználói tevékenységre vonatkozó jelentések az Azure Portalon |} A Microsoft Docs
description: Ismerje meg, hol vannak a az Azure Active Directory felhasználói tevékenységre vonatkozó jelentések az Azure Portalon.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: report-monitor
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 182537d6f07b624f2395f591681ed4596579bde0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42059126"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Tevékenységjelentések keresése az Azure Portalon

Ebben a cikkben azt ismertetjük, hogyan található Azure Active Directory felhasználói tevékenységre vonatkozó jelentések az Azure Portalon.

## <a name="activity-and-integrated-app-reports"></a>Tevékenység és az integrált alkalmazás jelentések

Környezetfüggő jelentéskészítés az Azure Portalon, a meglévő jelentések egyesítve egyetlen nézetben. Egyetlen, az alapul szolgáló API-t biztosít az adatok a nézethez.

Ebben a nézetben tekintheti meg a **Azure Active Directory** panel alatt **tevékenység**válassza **Auditnaplók**.

![Naplók](./media/howto-find-activity-reports/482.png "Naplók")

Az alábbi jelentések ebben a nézetben van összevonva:

* Ellenőrzési jelentés
* Jelszó-visszaállítási tevékenység
* Jelszó-visszaállítási regisztrációs tevékenység
* Önkiszolgáló csoportok tevékenysége
* Office 365-csoport módosítása
* Alkalmazás-kiépítési tevékenység
* Jelszó váltása állapota
* Alkalmazás-kiépítési hibák


Az alkalmazás használati jelentés bővült, és tartalmazza a **bejelentkezések** megtekintése. Ebben a nézetben tekintheti meg a **Azure Active Directory** panel alatt **tevékenység**válassza **bejelentkezések**.

![Bejelentkezések megtekintése](./media/howto-find-activity-reports/483.png "bejelentkezések megtekintése")

A **bejelentkezések** nézet tartalmazza az összes felhasználói bejelentkezés. Ezen információk használatával alkalmazás használati adatai olvashatók. Alkalmazás használati adatai is megtekinthetők a **vállalati alkalmazások** áttekintése, a a **kezelés** szakaszban.

![Vállalati alkalmazások](./media/howto-find-activity-reports/484.png "vállalati alkalmazások")

## <a name="access-a-specific-report"></a>Egy adott jelentés eléréséhez

Bár az Azure Portalon egyetlen nézetben kínál, továbbá tekintse meg adott jelentéseket.

### <a name="audit-logs"></a>Naplók

Az ügyfelek visszajelzései alapján, az Azure Portalon használhatja a Speciális szűrés a kívánt adatok eléréséhez. Használhat egy szűrő egy *tevékenység kategóriája*, amely felsorolja a különböző típusú tevékenység naplózza az Azure ad-ben. Szűkítheti az eredményeket, amit keres, akkor is válasszon egy kategóriát.

Például, ha szüksége, csak az önkiszolgáló jelszóátállítással kapcsolatos tevékenységeit, választhatja a **önkiszolgáló jelszókezelés** kategória. Megjelenik a kategóriák dolgozik, az erőforrás alapulnak.  

![Kategória lehetőségek szűrő Naplók lapján](./media/howto-find-activity-reports/06.png "kategória lehetőségek szűrő Naplók lapján")

Tevékenységkategóriák a következők:

- Alapvető könyvtár
- Önkiszolgáló jelszókezelés
- Önkiszolgáló csoportkezelés
- Fiók kiépítése

### <a name="application-usage"></a>Alkalmazáshasználat

Az alkalmazás használatának minden alkalmazás vagy egy önálló alkalmazás részleteinek megtekintéséhez a **tevékenység**válassza **bejelentkezések**. Az eredmények szűkítéséhez szűrheti a felhasználó vagy alkalmazás nevét.

![Szűrő bejelentkezési eseményeket felsoroló weblapunkon](./media/howto-find-activity-reports/07.png "bejelentkezési események szűrése lap")

### <a name="security-reports"></a>Biztonsági jelentések

#### <a name="azure-ad-anomalous-activity-reports"></a>Az Azure AD rendellenes tevékenységre vonatkozó jelentések

Az Azure AD rendellenes tevékenység biztonsági jelentések konszolidálni vannak egy központi nézetet. Ez a nézet megjeleníti az összes biztonsági kockázati események, hogy az Azure AD észleli, és jelentést.

A következő táblázat a listák az Azure AD rendellenes tevékenység biztonsági jelentéseket, és megfelelő kockázati események típusai az Azure Portalon.

| Az Azure AD rendellenes tevékenység jelentés |  Identity protection kockázati esemény típusa|
| :--- | :--- |
| Felhasználók, akiknek kiszivárogtak a hitelesítő adatai | Kiszivárgott hitelesítő adatok |
| Rendszertelen bejelentkezési tevékenység | Bejelentkezés szokatlan helyekről |
| Bejelentkezések potenciálisan fertőzött eszközökről | Bejelentkezések fertőzött eszközökről|
| Bejelentkezések ismeretlen forrásokról | Bejelentkezések névtelen IP-címről |
| Bejelentkezések gyanús tevékenységeket mutató IP-címekkel | Bejelentkezések gyanús tevékenységeket mutató IP-címekkel |
| - | Bejelentkezések ismeretlen helyekről |

Az alábbi Azure ad-ben rendellenes tevékenység biztonsági jelentések kockázati események az Azure Portalon nem tartoznak:

* Több hibát követő bejelentkezések
* Bejelentkezések különböző földrajzi régiókból

További információkért tekintse át [Az Azure Active Directory kockázati eseményeivel](concept-risk-events.md) foglalkozó cikket.  


#### <a name="detected-risk-events"></a>Észlelt kockázati események

Az Azure Portalon érheti jelentéseket észlelt kockázati eseményeket a a **Azure Active Directory** panel alatt **biztonsági**. Észlelt kockázati eseményeket a rendszer a következő jelentések nyomon követi:   

- Kockázatos felhasználók
- Kockázatos bejelentkezések

![Biztonsági jelentések](./media/howto-find-activity-reports/04.png "biztonsági jelentések")

Biztonsági jelentésekkel kapcsolatos további információkért lásd:

- [Felhasználókról szóló biztonsági jelentés az Azure Active Directory portálon](concept-user-at-risk.md)
- [Kockázatos bejelentkezések jelentés az Azure Active Directory portálon](concept-risky-sign-ins.md)


Megtekintéséhez a **Alkalmazáshasználat** jelentésben a **Azure Active Directory** panel alatt **kezelés**, jelölje be **vállalati alkalmazások**, majd **bejelentkezések**.


![Vállalati alkalmazások bejelentkezési jelentések](./media/howto-find-activity-reports/199.png)

## <a name="next-steps"></a>További lépések

A jelentéskészítés áttekintéséért lásd: [Jelentéskészítés az Azure Active Directoryban](overview-reports.md).
