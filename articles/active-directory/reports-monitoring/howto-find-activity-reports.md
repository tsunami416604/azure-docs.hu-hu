---
title: Keresse meg az Azure Active Directory felhasználói tevékenységre vonatkozó jelentések az Azure Portalon |} A Microsoft Docs
description: Ismerje meg, hol vannak a az Azure Active Directory felhasználói tevékenységre vonatkozó jelentések az Azure Portalon.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65e803ca373fb9853fc23d17f1a27ecadc6a209c
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295215"
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

Használhatja a naplózási jelentésben Speciális szűrés megadásával, a naplózási adatokat, konkrét kategóriáját eléréséhez a **kategória** szűrőt. Jelölje be például a felhasználók számára kapcsolódó összes tevékenységének megtekintéséhez, a **UserManagement** kategória. 

Kategóriák a következők:

- Összes
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Engedélyezés
- Kapcsolattartó
- Eszköz
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Egyéb
- Szabályzat
- ResourceManagement
- RoleManagement
- UserManagement

Egy adott szolgáltatás használatával is szűrheti a **szolgáltatás** legördülő menü Szűrés. Önkiszolgáló jelszókezelés kapcsolódó összes naplózási eseményt lekéréséhez válassza például a **önkiszolgáló jelszókezelés** szűrőt.

A szolgáltatások az alábbiak:

- Összes
- Hozzáférési felülvizsgálatok
- Fiók kiépítése 
- Alkalmazás egyszeri Bejelentkezéssel
- Hitelesítési módszerek
- B2C
- Feltételes hozzáférés
- Alapvető könyvtár
- Jogosultságkezelés
- Identity Protection
- Meghívott felhasználók
- PIM
- Önkiszolgáló csoportkezelés
- Önkiszolgáló jelszókezelés
- Használati feltételek

## <a name="sign-ins-report"></a>Bejelentkezések jelentés 

A **bejelentkezések** nézet tartalmazza az összes felhasználói bejelentkezéseket, valamint a **Alkalmazáshasználat** jelentést. Alkalmazás használati adatai is megtekinthetők a **kezelés** szakaszában a **vállalati alkalmazások** áttekintése.

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

## <a name="troubleshoot-issues-with-activity-reports"></a>Tevékenységjelentések hibáinak elhárítása

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Hiányzó adatok a letöltött Tevékenységnaplókban

#### <a name="symptoms"></a>Probléma 

Letöltöttem a tevékenységnaplókat (audit vagy bejelentkezési), és nem látom a kiválasztott időre vonatkozó összes rekordot. Hogy miért? 

 ![Jelentéskészítés](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Ok

Az Azure Portalon tevékenységnaplókat tölt le, amikor azt korlátozza a méretezési csoport 250000 rekordot legutóbbi van legelöl szerint rendezve. 

#### <a name="resolution"></a>Megoldás:

Az [Azure AD Reporting API-kkal](concept-reporting-api.md) akár egymillió rekordot is lekérdezhet.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Hiányzó naplózási adatok legutóbbi műveletek az Azure Portalon

#### <a name="symptoms"></a>Probléma

Végrehajtottam bizonyos műveleteket az Azure Portalon, és arra számítottam, hogy látom a műveletek naplóit a `Activity logs > Audit Logs` panelen, de nem találtam meg őket.

 ![Jelentéskészítés](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Ok

A műveletek nem jelennek meg azonnal a tevékenységnaplókban. Az alábbi táblázat a tevékenységnaplók késési számait sorolja fel. 

| Jelentés | &nbsp; | Késés (P95) | Késés (P99) |
|--------|--------|---------------|---------------|
| Címtárnaplózás | &nbsp; | 2 perc | 5 perc |
| Bejelentkezési tevékenység | &nbsp; | 2 perc | 5 perc | 

#### <a name="resolution"></a>Megoldás:

Várjon, hogy a műveletek úgy 15 perc és két óra közötti időtartam elteltével megjelennek-e a naplóban. Ha két óra elteltével sem látja a naplókat, [küldjön be egy támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), és kivizsgáljuk az ügyet.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>A legutóbbi felhasználói bejelentkezéseket az Azure AD bejelentkezési tevékenység a hiányzó naplók naplózása

#### <a name="symptoms"></a>Probléma

Nemrég bejelentkeztem az Azure Portalra, és arra számítottam, hogy látom ezeknek a bejelentkezéseknek a naplóit a `Activity logs > Sign-ins` panelen, de nem találom őket.

 ![Jelentéskészítés](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Ok

A műveletek nem jelennek meg azonnal a tevékenységnaplókban. Az alábbi táblázat a tevékenységnaplók késési számait sorolja fel. 

| Jelentés | &nbsp; | Késés (P95) | Késés (P99) |
|--------|--------|---------------|---------------|
| Címtárnaplózás | &nbsp; | 2 perc | 5 perc |
| Bejelentkezési tevékenység | &nbsp; | 2 perc | 5 perc | 

#### <a name="resolution"></a>Megoldás:

Várjon, hogy a műveletek úgy 15 perc és két óra közötti időtartam elteltével megjelennek-e a naplóban. Ha két óra elteltével sem látja a naplókat, [küldjön be egy támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), és kivizsgáljuk az ügyet.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Nem jelenik meg 30 napnál több adat a jelentésekben az Azure Portalon

#### <a name="symptoms"></a>Probléma

Nem jelenik meg 30 napnál több bejelentkezési és auditadat az Azure Portalról. Hogy miért? 

 ![Jelentéskészítés](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Ok

A licencétől függően az Azure Active Directory-műveletek az alábbi időtartamokig tárolják a tevékenységjelentéseket:

| Jelentés           | &nbsp; |  Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Címtárnaplózás  | &nbsp; |   7 nap     | 30 nap             | 30 nap             |
| Bejelentkezési tevékenységek | &nbsp; | Nem érhető el. A saját bejelentkezéseit 7 napig érheti el az egyedi felhasználói profil panelről | 30 nap | 30 nap             |

További információk: [Az Azure Active Directory jelentésmegőrzési házirendje](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Megoldás:

Az adatokat kétféleképpen őrizheti meg 30 napnál hosszabban. Az [Azure AD Reporting API-kkal](concept-reporting-api.md) az adatokat programozott módon kérheti le és tárolhatja egy adatbázisban. Másik megoldásként az auditnaplókat egy külső SIEM-rendszerbe, például a Splunk vagy a SumoLogic rendszerbe integrálhatja.

## <a name="next-steps"></a>További lépések

* [Naplózási naplók áttekintése](concept-audit-logs.md)
* [Bejelentkezések áttekintése](concept-sign-ins.md)
* [Kockázatos események áttekintése](concept-risk-events.md)
