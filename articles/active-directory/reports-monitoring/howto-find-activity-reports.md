---
title: Felhasználói tevékenységgel kapcsolatos jelentések keresése a Azure Portalban | Microsoft Docs
description: Megtudhatja, hol találhatók a Azure Active Directory felhasználói tevékenység jelentései a Azure Portalban.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d356f8c10ae3770d9f4ade100ab0496ee58d772f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008213"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Tevékenység-jelentések keresése a Azure Portal

Ebből a cikkből megtudhatja, hogyan keresheti meg Azure Active Directory (Azure AD) felhasználói tevékenységek jelentéseit a Azure Portal.

## <a name="audit-logs-report"></a>Naplózott jelentés

A naplók jelentés több jelentést is egyesít az alkalmazások tevékenységeit illetően egyetlen nézetben a kontextus-alapú jelentéskészítéshez. A naplós jelentés elérése:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a címtárat a jobb felső sarokban, majd válassza ki a **Azure Active Directory** panelt a bal oldali navigációs ablaktáblán.
3. Válassza a **naplók** lehetőséget a Azure Active Directory panel **tevékenység** szakaszában. 

    ![Naplók](./media/howto-find-activity-reports/482.png "Naplók")

A naplózott jelentés a következő jelentéseket összesíti:

* Naplózási jelentés
* Jelszó-visszaállítási tevékenység
* Új jelszó kérése regisztrációs tevékenység
* Önkiszolgáló csoportok tevékenység
* Office 365-csoport nevének módosítása
* Fiók létesítési tevékenysége
* Jelszó-átváltási állapot
* Alkalmazás-kiépítési hibák

### <a name="filtering-on-audit-logs"></a>Naplózási naplók szűrése

A naplózási jelentésben a speciális szűrés használatával férhet hozzá a naplózási adatkategóriákhoz, ha megadja azt a **Kategória** szűrőben. Ha például meg szeretné tekinteni a felhasználókhoz kapcsolódó összes tevékenységet, válassza ki a **UserManagement** kategóriát. 

A kategóriák a következők:

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

A **szolgáltatás** legördülő szűrővel is szűrheti egy adott szolgáltatást. Ha például az önkiszolgáló jelszavas felügyelettel kapcsolatos összes naplózási eseményt le szeretné kérni, válassza az **önkiszolgáló jelszó-kezelési** szűrőt.

A szolgáltatások az alábbiak:

- Összes
- Hozzáférési felülvizsgálatok
- Fiók kiépítése 
- Alkalmazás egyszeri bejelentkezés
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

A **bejelentkezési** nézet az összes felhasználói bejelentkezést, valamint az **alkalmazás használati** jelentését is tartalmazza. Az alkalmazás-használati adatokat a **vállalati alkalmazások** áttekintése témakör **kezelés** szakaszában is megtekintheti.

A bejelentkezések jelentésének elérése:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a címtárat a jobb felső sarokban, majd válassza ki a **Azure Active Directory** panelt a bal oldali navigációs ablaktáblán.
3. Válassza a **bejelentkezések** lehetőséget a Azure Active Directory panel **tevékenység** szakaszában. 

    ![Bejelentkezések nézet](./media/howto-find-activity-reports/483.png "Bejelentkezések nézet")


### <a name="filtering-on-application-name"></a>Alkalmazás neve szerinti szűrés

A bejelentkezési jelentés használatával megtekintheti az alkalmazás használatának részleteit a Felhasználónév vagy az alkalmazás nevének szűrésével.

![Bejelentkezési események szűrése lap](./media/howto-find-activity-reports/07.png "Bejelentkezési események szűrése lap")

## <a name="security-reports"></a>Biztonsági jelentések

### <a name="anomalous-activity-reports"></a>Rendellenes tevékenységgel kapcsolatos jelentések

A rendellenes tevékenységekről szóló jelentések információt nyújtanak a biztonsággal kapcsolatos, az Azure AD által észlelhető és jelentett biztonsági kockázatokról.

A következő táblázat felsorolja az Azure AD rendellenes tevékenységek biztonsági jelentéseit és a Azure Portal megfelelő kockázati észlelési típusokat. További információ: [Azure Active Directory kockázati észlelések](concept-risk-events.md).  


| Azure AD rendellenes tevékenységekről szóló jelentés |  Identity Protection kockázati észlelési típusa|
| :--- | :--- |
| Felhasználók, akiknek kiszivárogtak a hitelesítő adatai | Kiszivárgott hitelesítő adatok |
| Rendszertelen bejelentkezési tevékenység | Bejelentkezés szokatlan helyekről |
| Bejelentkezések potenciálisan fertőzött eszközökről | Bejelentkezések fertőzött eszközökről|
| Bejelentkezések ismeretlen forrásokról | Bejelentkezések névtelen IP-címről |
| Bejelentkezések gyanús tevékenységeket mutató IP-címekkel | Bejelentkezések gyanús tevékenységeket mutató IP-címekkel |
| - | Bejelentkezések ismeretlen helyekről |

A következő Azure AD rendellenes tevékenység biztonsági jelentései nem szerepelnek a Azure Portalban felderített kockázati észlelésekben:

* Több hibát követő bejelentkezések
* Bejelentkezések különböző földrajzi régiókból


### <a name="detected-risk-detections"></a>Észlelt kockázati észlelések

Az észlelt kockázati észlelésekkel kapcsolatos jelentések a [Azure Portal](https://portal.azure.com) **Azure Active Directory** paneljének **biztonsági** szakaszában érhetők el. Az észlelt kockázati észleléseket a következő jelentések követik nyomon:   

- [Veszélyeztetett felhasználók](concept-user-at-risk.md)
- [Kockázatos bejelentkezések](concept-risky-sign-ins.md)

    ![Biztonsági jelentések](./media/howto-find-activity-reports/04.png "Biztonsági jelentések")

## <a name="troubleshoot-issues-with-activity-reports"></a>A tevékenységek jelentéseivel kapcsolatos problémák elhárítása

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Hiányzó adatnaplók a letöltött tevékenység naplóiban

#### <a name="symptoms"></a>Probléma 

Letöltöttem a tevékenységnaplókat (audit vagy bejelentkezési), és nem látom a kiválasztott időre vonatkozó összes rekordot. Hogy miért? 

 ![Jelentéskészítés](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Ok

Amikor letölti a tevékenység naplóit a Azure Portalban, a skálázást 250000 rekordra korlátozzuk, a legutóbbiek szerint rendezve. 

#### <a name="resolution"></a>Megoldás:

Az [Azure AD Reporting API-kkal](concept-reporting-api.md) akár egymillió rekordot is lekérdezhet.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Hiányoznak a Azure Portal legutóbbi műveleteinek naplózási adatkészletei

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

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Hiányzó naplók a legutóbbi felhasználói bejelentkezésekhez az Azure AD bejelentkezési tevékenység naplójában

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

## <a name="next-steps"></a>Következő lépések

* [Naplófájlok áttekintése](concept-audit-logs.md)
* [Bejelentkezések – áttekintés](concept-sign-ins.md)
* [Kockázatos események áttekintése](concept-risk-events.md)
