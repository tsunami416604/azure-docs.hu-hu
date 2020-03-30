---
title: Felhasználói tevékenységjelentések keresése az Azure Portalon | Microsoft dokumentumok
description: Ismerje meg, hogy az Azure Active Directory felhasználói tevékenységjelentései hol találhatók az Azure Portalon.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008213"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Tevékenységjelentések keresése az Azure Portalon

Ebben a cikkben megtudhatja, hogyan keresheti meg az Azure Active Directory (Azure AD) felhasználói tevékenységjelentéseit az Azure Portalon.

## <a name="audit-logs-report"></a>Naplók jelentés

A naplójelentés az alkalmazástevékenységek körüli több jelentést egyetlen nézetben egyesíti a környezetalapú jelentésekhez. A naplójelentés elérése:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Válassza ki a könyvtárat a jobb felső sarokban, majd válassza ki az **Azure Active Directory** panela a bal oldali navigációs ablakban.
3. Válassza ki a **naplók naplózása** az Azure Active Directory panel **Tevékenység** szakaszában. 

    ![Naplók](./media/howto-find-activity-reports/482.png "Naplók")

A naplójelentés a következő jelentéseket összesíti:

* Naplózási jelentés
* Jelszó-visszaállítási tevékenység
* Jelszó-visszaállítási regisztrációs tevékenység
* Önkiszolgáló csoportok tevékenysége
* Az Office365-csoport névváltoztatásai
* Fiókkiépítési tevékenység
* Jelszóváltás állapota
* Alkalmazás-kiépítési hibák

### <a name="filtering-on-audit-logs"></a>Szűrés a naplókon

A naplózási jelentésben speciális szűréssel a naplózási adatok egy adott kategóriájához férhet hozzá, ha a **Kategória** szűrőben megadja azt. Ha például a felhasználókkal kapcsolatos összes tevékenységet szeretné megtekinteni, válassza a **UserManagement** kategóriát. 

A kategóriák a következők:

- Összes
- AdministrativeUnit
- Alkalmazáskezelés
- Hitelesítés
- Engedélyezés
- Kapcsolattartó
- Eszköz
- DeviceConfiguration
- Címtárkezelés
- Jogosultságkezelése
- Csoportkezelés
- Egyéb
- Szabályzat
- Erőforrás-felügyelet
- Szerepkör-kezelés
- UserManagement (Felhasználókezelése)

Egy adott szolgáltatásra is szűrhet a **Szolgáltatás** legördülő szűrő használatával. Ha például az önkiszolgáló jelszókezeléssel kapcsolatos összes naplózási eseményt szeretné lekérni, válassza az **Önkiszolgáló jelszókezelés** szűrőt.

A szolgáltatások a következőket foglalják magukban:

- Összes
- Hozzáférési felülvizsgálatok
- Fiók kiépítése 
- Alkalmazás-sso
- Hitelesítési módszerek
- B2C
- Feltételes hozzáférés
- Alapvető könyvtár
- Jogosultságok kezelése
- Identity Protection
- Meghívott felhasználók
- PIM
- Önkiszolgáló csoportkezelés
- Önkiszolgáló jelszókezelés
- Használati feltételek

## <a name="sign-ins-report"></a>Bejelentkezési jelentés 

A **Bejelentkezések** nézet tartalmazza az összes felhasználói bejelentkezést, valamint az **Alkalmazás használat a** jelentést. Az alkalmazáshasználati információkat a **Vállalati alkalmazások** **áttekintése Kezelés** szakaszában is megtekintheti.

A bejelentkezési jelentés elérése:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Válassza ki a könyvtárat a jobb felső sarokban, majd válassza ki az **Azure Active Directory** panela a bal oldali navigációs ablakban.
3. Válassza ki a **Bejelentkezések** az Azure Active Directory panel **Tevékenység** szakaszában. 

    ![Bejelentkezési nézet](./media/howto-find-activity-reports/483.png "Bejelentkezési nézet")


### <a name="filtering-on-application-name"></a>Szűrés az alkalmazás nevére

A bejelentkezések jelentés segítségével megtekintheti az alkalmazás használatának részleteit a felhasználónév vagy az alkalmazás nevének szűrésével.

![Bejelentkezési események szűrése lap](./media/howto-find-activity-reports/07.png "Bejelentkezési események szűrése lap")

## <a name="security-reports"></a>Biztonsági jelentések

### <a name="anomalous-activity-reports"></a>Rendellenes tevékenységjelentések

A rendellenes tevékenységjelentések információt nyújtanak a biztonsággal kapcsolatos kockázatészlelések, amelyek az Azure AD képes észlelni, és jelentést.

Az alábbi táblázat az Azure AD rendellenes tevékenységbiztonsági jelentéseket és a megfelelő kockázatészlelési típusokat sorolja fel az Azure Portalon. További információ: [Azure Active Directory-kockázatészlelések.](concept-risk-events.md)  


| Az Azure AD rendellenes tevékenységjelentése |  Identitásvédelmi kockázatészlelési típus|
| :--- | :--- |
| Felhasználók, akiknek kiszivárogtak a hitelesítő adatai | Kiszivárgott hitelesítő adatok |
| Rendszertelen bejelentkezési tevékenység | Bejelentkezés szokatlan helyekről |
| Bejelentkezések potenciálisan fertőzött eszközökről | Bejelentkezések fertőzött eszközökről|
| Bejelentkezések ismeretlen forrásokról | Bejelentkezések névtelen IP-címről |
| Bejelentkezések gyanús tevékenységeket mutató IP-címekkel | Bejelentkezések gyanús tevékenységeket mutató IP-címekkel |
| - | Bejelentkezések ismeretlen helyekről |

A következő Azure AD rendellenes tevékenység biztonsági jelentések nem szerepelnek a kockázatészlelések az Azure Portalon:

* Több hibát követő bejelentkezések
* Bejelentkezések különböző földrajzi régiókból


### <a name="detected-risk-detections"></a>Észlelt kockázatészlelések

Az észlelt kockázatészlelésekről szóló jelentésekaz **Azure Active Directory** panel az Azure [Portal](https://portal.azure.com) **on-ból** az észlelt kockázatészlelések. Az észlelt kockázatészlelések nyomon követése a következő jelentésekben történik:   

- [Veszélyeztetett felhasználók](concept-user-at-risk.md)
- [Kockázatos bejelentkezések](concept-risky-sign-ins.md)

    ![Biztonsági jelentések](./media/howto-find-activity-reports/04.png "Biztonsági jelentések")

## <a name="troubleshoot-issues-with-activity-reports"></a>Tevékenységjelentésekkel kapcsolatos problémák elhárítása

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Hiányzó adatok a letöltött tevékenységnaplókban

#### <a name="symptoms"></a>Probléma 

Letöltöttem a tevékenységnaplókat (audit vagy bejelentkezési), és nem látom a kiválasztott időre vonatkozó összes rekordot. Hogy miért? 

 ![Jelentéskészítés](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Ok

Amikor tevékenységnaplókat tölt le az Azure Portalon, a skálát 250000 rekordra korlátozzuk, először a legutóbbi szerint rendezve. 

#### <a name="resolution"></a>Megoldás:

Az [Azure AD Reporting API-kkal](concept-reporting-api.md) akár egymillió rekordot is lekérdezhet.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Hiányoznak az Azure Portalon végrehajtott legutóbbi műveletek naplózási adatai

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

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Hiányzó naplók a legutóbbi felhasználói bejelentkezések az Azure AD bejelentkezési tevékenységnaplójában

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

* [Naplók naplózása – áttekintés](concept-audit-logs.md)
* [Bejelentkezések – áttekintés](concept-sign-ins.md)
* [Kockázatos események áttekintése](concept-risk-events.md)
