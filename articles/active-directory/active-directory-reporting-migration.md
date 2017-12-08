---
title: "Az Azure portálon található Tevékenységjelentések |} Microsoft Docs"
description: "Útmutató: Azure Active Directory Tevékenységjelentések az Azure portálon található."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6f1485ad57eb28841ef5fe585e8cf1b8eb29e4a7
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2017
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Az Azure portálon található Tevékenységjelentések

Ha a klasszikus Azure portálon az Azure portálra, kap egy új Azure Active Directory (Azure AD) tevékenységi naplóit pillantást. Az egy nemrég végrehajtott [blogbejegyzés](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/), azt ismertetik, hogyan láthatja, tevékenység naplózza az Azure-portálon dolgoznak erőforrás környezetében. Ez a cikk azt ismerteti, hogyan használt jelentéseket a klasszikus Azure-portálon az Azure portálon található.

## <a name="whats-new"></a>Újdonságok

A klasszikus Azure portálon jelentések kategóriába oszthatók:

1.  Biztonsági jelentések
2.  Tevékenységjelentések
3.  Integrált alkalmazás jelentések

### <a name="activity-and-integrated-app-reports"></a>Tevékenység és integrált alkalmazás jelentések

Környezetfüggő jelentéskészítés az Azure portálon, a meglévő jelentések egyesülnek egyetlen nézetben. Egyetlen, a nézet az adatokat az alapul szolgáló API nyújt.

Ebben a nézetben tekintheti meg a **Azure Active Directory** panel alatt **tevékenység**, jelölje be **naplók**.

![Naplók](./media/active-directory-reporting-migration/482.png "Naplók")

Az alábbi jelentések az ebben a nézetben van összevonva:

-   Ellenőrzési jelentés
-   Jelszó-visszaállítási tevékenység
-   Jelszó-visszaállítási regisztrációs tevékenység
-   Önkiszolgáló csoportok tevékenységéről
-   Office365 csoport nevének módosítása
-   Tevékenység-kiépítési
-   Jelszó-helyettesítő állapota
-   Alkalmazás-kiépítési hibák


Az alkalmazás használati jelentés bővült, és szerepel a **bejelentkezések** nézet. Ebben a nézetben tekintheti meg a **Azure Active Directory** panel alatt **tevékenység**, jelölje be **bejelentkezések**.

![Bejelentkezések nézet](./media/active-directory-reporting-migration/483.png "bejelentkezések megtekintése")

A **bejelentkezések** nézet tartalmazza az összes felhasználói bejelentkezéseket. Ez az információ segítségével alkalmazás használati adatok. Az alkalmazás használati adatai is megtekintheti a **vállalati alkalmazások** áttekintése, a a **kezelése** szakasz.

![Vállalati alkalmazások](./media/active-directory-reporting-migration/484.png "vállalati alkalmazások")

## <a name="access-a-specific-report"></a>Hozzáférés egy adott jelentés

Bár az Azure-portálon egyetlen nézetben kínál, is vessen egy pillantást konkrét jelentéseket.

### <a name="audit-logs"></a>Naplók

Az ügyfelek visszajelzései alapján, az Azure portálon használhatja a speciális szűrési férhetnek hozzá a kívánt adathoz. Használhat egy szűrő egy *tevékenységkategóriákkal*, amely felsorolja a különböző típusú tevékenység naplózza az Azure AD-ben. Éppen megtekintett eredmények szűkítéséhez is válasszon egy kategóriát.

Például ha érdekli, csak az önkiszolgáló jelszó-átállításra kapcsolatos tevékenységeit, választhatja a **önkiszolgáló jelszókezelés** kategóriát. A kategóriák, megjelenik az erőforrás-használata alapulnak.  

![A szűrő naplók oldalon kategória beállítások](./media/active-directory-reporting-migration/06.png "szűrő Naplók lapján található kategória beállítások")

Tevékenységkategóriák a következők:

- Alapvető könyvtár
- Önkiszolgáló jelszókezelés
- Önkiszolgáló csoportkezelés
- Fiók kiépítése

### <a name="application-usage"></a>Az alkalmazás használatának

Az összes olyan alkalmazáshoz, vagy egy önálló alkalmazás, az alkalmazás használati adatainak megtekintéséhez az **tevékenység**, jelölje be **bejelentkezések**. Az eredmények szűkítéséhez szűrheti a felhasználó vagy alkalmazás nevét.

![Szűrő bejelentkezési események lapot](./media/active-directory-reporting-migration/07.png "bejelentkezési események szűrése lap")

### <a name="security-reports"></a>Biztonsági jelentések

#### <a name="azure-ad-anomalous-activity-reports"></a>Az Azure AD rendellenes tevékenységet jelentések

Az Azure AD rendellenes tevékenységet biztonsági biztosítja, hogy a központi nézet egy, az engedélyezés jelentéseit a klasszikus Azure portálon. Ebben a nézetben látható az összes biztonsági kockázati eseményekről, hogy az Azure AD képesek észlelni és jelentést.

A következő tábla listák az Azure AD rendellenes tevékenységet biztonsági jelentések, és a kockázati esemény típusával azonos Azure-portálon.

| Az Azure AD rendellenes tevékenységgel kapcsolatos jelentés |  Identity protection kockázati esemény típusa|
| :--- | :--- |
| Felhasználók, akiknek kiszivárogtak a hitelesítő adatai | Kiszivárgott hitelesítő adatok |
| Rendszertelen bejelentkezési tevékenység | Bejelentkezés szokatlan helyekről |
| Bejelentkezések potenciálisan fertőzött eszközökről | Bejelentkezések fertőzött eszközökről|
| Bejelentkezések ismeretlen forrásokról | Névtelen IP-címről történő bejelentkezések |
| Bejelentkezések gyanús tevékenységeket mutató IP-címekkel | Bejelentkezések gyanús tevékenységeket mutató IP-címekkel |
| - | Ismeretlen helyekről történt bejelentkezések |

A következő Azure AD rendellenes tevékenységet biztonsági jelentések nem tartoznak kockázati eseményekről az Azure-portálon:

* Több hibát követő bejelentkezések
* Bejelentkezések különböző földrajzi régiókból

Ezek a jelentések továbbra is elérhetők, a klasszikus Azure portálon, de azok a jövőben elavulttá válik.

További információkért tekintse át [Az Azure Active Directory kockázati eseményeivel](active-directory-identity-protection-risk-events.md) foglalkozó cikket.  


#### <a name="detected-risk-events"></a>Észlelt kockázati események

Az Azure-portálon hozzáférhet észlelt kockázati események jelentéseket a a **Azure Active Directory** panel alatt **biztonsági**. Az alábbi jelentések észlelt kockázati események követi:   

- Felhasználók veszélyben
- Kockázatos bejelentkezések

![Biztonsági jelentések](./media/active-directory-reporting-migration/04.png "biztonsági jelentések")

Biztonsági jelentésekkel kapcsolatos további információkért lásd:

- [Kockázati biztonsági jelentést az Azure Active Directory portálon a felhasználók](active-directory-reporting-security-user-at-risk.md)
- [Az Azure Active Directory portálon kockázatos bejelentkezések jelentés](active-directory-reporting-security-risky-sign-ins.md)


## <a name="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal"></a>Az Azure-portál és a klasszikus Azure portálon Tevékenységjelentések

Ez a szakasz a táblázat meglévő jelentések a klasszikus Azure portálon. Azt is bemutatja, hogyan férhetnek ugyanazokat az információkat az Azure portálon.

Összes naplózási adatok megtekintéséhez a **Azure Active Directory** panelen, a **tevékenység**, és **naplók**.

![Naplók](./media/active-directory-reporting-migration/61.png "Naplók")

| klasszikus Azure portál                 | Az Azure portálon található                                                         |
| ---                                  | ---                                                                        |
| Naplók                           | A **Tevékenységkategóriákkal**, jelölje be **Core Directory**.                       |
| Jelszó-visszaállítási tevékenység              | A **Tevékenységkategóriákkal**, jelölje be **önkiszolgáló jelszókezelés**. |
| Jelszó-visszaállítási regisztrációs tevékenység | A **Tevékenységkategóriákkal**, jelölje be **önkiszolgáló jelszókezelés**.     |
| Önkiszolgáló csoportok tevékenységéről         | A **Tevékenységkategóriákkal**, jelölje be **önkiszolgáló csoportkezelési**.        |
| Tevékenység-kiépítési        | A **Tevékenységkategóriákkal**, jelölje be **fiók a felhasználók átadása**.         |
| Jelszó-helyettesítő állapota             | A **Tevékenységkategóriákkal**, jelölje be **automatikus App jelszó helyettesítő**.      |
| Alkalmazás-kiépítési hibák          | A **Tevékenységkategóriákkal**, jelölje be **fiók a felhasználók átadása**.        |
| Office365 csoport nevének módosítása         | A **Tevékenységkategóriákkal**, jelölje be **önkiszolgáló jelszókezelés**. A **tevékenység erőforrástípus**, jelölje be **csoport**. A **tevékenység forrása**, jelölje be **Office 365-csoportok**.|

Megtekintéséhez a **Alkalmazáshasználat** jelentés, az a **Azure Active Directory** panelen, a **kezelése**, jelölje be **vállalati alkalmazások**, Válassza ki és **bejelentkezések**.


![Vállalati alkalmazások bejelentkezések jelentés](./media/active-directory-reporting-migration/199.png "vállalati alkalmazások bejelentkezések jelentés")

## <a name="next-steps"></a>Következő lépések

A jelentéskészítés áttekintéséért lásd: [Jelentéskészítés az Azure Active Directoryban](active-directory-reporting-azure-portal.md).
