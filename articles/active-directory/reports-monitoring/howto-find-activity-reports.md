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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fab94088d1d54012a955b0663b078d03b13d6299
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624912"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Tevékenységjelentések keresése az Azure Portalon

Ebből a cikkből megismerheti, hogyan találhatja meg az Azure Active Directory (Azure AD) felhasználói tevékenységre vonatkozó jelentések az Azure Portalon.

## <a name="audit-logs-report"></a>Naplók jelentés

A naplók jelentés több jelentések alkalmazás tevékenységek környezetfüggő jelentéskészítési egyetlen nézetben egyesíti. A naplók jelentés elérése:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. A jobb felső sarokban válassza ki a címtárat, majd válassza ki a **Azure Active Directory** a bal oldali navigációs panelen.
3. Válassza ki **Auditnaplók** származó a **tevékenység** szakaszában az Azure Active Directory panel. 

    ![Naplók](./media/howto-find-activity-reports/482.png "Naplók")

A naplók jelentés összesíti az alábbi jelentések:

* Naplózási jelentés
* Jelszó-visszaállítási tevékenység
* Jelszó-visszaállítási regisztrációs tevékenység
* Önkiszolgáló csoportok tevékenysége
* Office 365-csoport módosítása
* Alkalmazás-kiépítési tevékenység
* Jelszó váltása állapota
* Alkalmazás-kiépítési hibák

### <a name="filtering-on-audit-logs"></a>Auditnaplók szűrése

Használhatja a naplózási jelentésben Speciális szűrés megadásával, a naplózási adatokat, konkrét kategóriáját eléréséhez a **tevékenység kategóriája** szűrőt. Válassza ki például az önkiszolgáló jelszó-visszaállítás kapcsolódó összes tevékenységének megtekintéséhez, a **önkiszolgáló jelszókezelés** kategória. 

    ![Category options on the Filter Audit Logs page](./media/howto-find-activity-reports/06.png "Category options on the Filter Audit Logs page")

Tevékenységkategóriák a következők:

- Alapvető könyvtár
- Önkiszolgáló jelszókezelés
- Önkiszolgáló csoportkezelés
- Fiók kiépítése


## <a name="sign-ins-report"></a>Bejelentkezések jelentés 

A **bejelentkezések** nézet tartalmazza az összes felhasználói bejelentkezéseket, valamint a **Alkalmazáshasználat** jelentést. Alkalmazás használati adatai is megtekinthetők a **kezelés** szakaszában a **vállalati alkalmazások** áttekintése.

    ![Enterprise applications](./media/howto-find-activity-reports/484.png "Enterprise applications")

A bejelentkezési jelentések elérése:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. A jobb felső sarokban válassza ki a címtárat, majd válassza ki a **Azure Active Directory** a bal oldali navigációs panelen.
3. Válassza ki **bejelentkezések** származó a **tevékenység** szakaszában az Azure Active Directory panel. 

    ![Bejelentkezések megtekintése](./media/howto-find-activity-reports/483.png "bejelentkezések megtekintése")


### <a name="filtering-on-application-name"></a>Szűrés alkalmazásnév

Használhatja a bejelentkezési jelentések szűrésével felhasználó vagy alkalmazás nevét az alkalmazás használatának részleteinek megtekintéséhez.

![Szűrő bejelentkezési eseményeket felsoroló weblapunkon](./media/howto-find-activity-reports/07.png "bejelentkezési események szűrése lap")

## <a name="security-reports"></a>Biztonsági jelentések

### <a name="anomalous-activity-reports"></a>Rendellenes tevékenységekre vonatkozó jelentések

Rendellenes tevékenységekre vonatkozó jelentések információkat tartalmaz, amely az Azure AD észleli, és a jelentés a biztonsággal kapcsolatos kockázati eseményeket.

A következő táblázat a listák az Azure AD rendellenes tevékenység biztonsági jelentéseket, és megfelelő kockázati események típusai az Azure Portalon. További információkért tekintse át [Az Azure Active Directory kockázati eseményeivel](concept-risk-events.md) foglalkozó cikket.  


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


### <a name="detected-risk-events"></a>Észlelt kockázati események

Jelentésekhez észlelt kockázati eseményeket a kapcsolatos a **biztonsági** szakaszában a **Azure Active Directory** paneljén a [az Azure portal](https://portal.azure.com). Észlelt kockázati eseményeket a rendszer a következő jelentések nyomon követi:   

- [Veszélyeztetett felhasználók](concept-user-at-risk.md)
- [Kockázatos bejelentkezések](concept-risky-sign-ins.md)

    ![Biztonsági jelentések](./media/howto-find-activity-reports/04.png "biztonsági jelentések")

## <a name="next-steps"></a>További lépések

* [Naplózási naplók áttekintése](concept-audit-logs.md)
* [Bejelentkezések áttekintése](concept-sign-ins.md)
* [Kockázatos események áttekintése](concept-risk-events.md)
