---
title: Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon – bevezetés
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5895ad1f1452b5a0c13765821c5b623472a0bb1e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447115"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon 

A jelentések az Azure Active Directory (Azure AD) megtekintheti az adatokat, meg kell határoznia, hogy a környezet működésébe.

A jelentéskészítési architektúrája a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – a [bejelentkezésekre](concept-sign-ins.md) arról nyújt tájékoztatást, a használati felügyelt alkalmazások és a felhasználó bejelentkezési tevékenységekről.
    - **Auditnaplók** – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók például olyan erőforrások hozzáadásával vagy eltávolításával, a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok például az Azure AD-ben végzett módosítások.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – [kockázatos bejelentkezés](concept-risky-sign-ins.md) egy bejelentkezési kísérlet, aki nem a felhasználói fiók jogos tulajdonosa által végrehajtott előfordulhat, hogy a mutató. 
    - **Kockázatosként megjelölt felhasználók** – [kockázatos felhasználó](concept-user-at-risk.md) mutató egy felhasználói fiókot, amely a előfordulhat, hogy sérült a biztonsága.

Ez a cikk áttekintést, a naplózási jelentésben.
 
## <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?

* A felhasználók a **biztonsági rendszergazda**, **biztonsági olvasó** vagy **globális rendszergazdai** szerepkörök
* Emellett minden felhasználója (nem rendszergazda) láthatja-e a saját naplózási tevékenységek

## <a name="audit-logs"></a>Naplók

Az Azure AD naplózási naplók, adja meg a megfelelőség a rendszertevékenységek rekordjait. A naplózási jelentések eléréséhez, válassza ki a **Auditnaplók** a a **tevékenység** szakaszában **Azure Active Directory**. Vegye figyelembe, hogy auditnaplók előfordulhat, hogy egy legfeljebb egy órát, késését, is igénybe vehet, hogy hosszú, és naplózási adatok megjelennek a portálon, a feladat befejezése után.

![Naplók](./media/concept-audit-logs/61.png "Naplók")

Az auditnapló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- az előfordulás dátuma és időpontját
- a tevékenység kezdeményezőjét / szereplőjét (*ki?*) 
- a tevékenységet (*mi?*) 
- a célt

![Naplók](./media/concept-audit-logs/18.png "Naplók")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Naplók](./media/concept-audit-logs/19.png "Naplók")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Naplók](./media/concept-audit-logs/21.png "Naplók")

Jelöljön ki egy elemet a listanézet, melyeken részletesebb információkat találhat.

![Naplók](./media/concept-audit-logs/22.png "Naplók")


## <a name="filtering-audit-logs"></a>Auditnaplók szűrése

A naplózási adatok az alábbi mezőkkel szűrheti:

- Dátumtartomány
- Kezdeményező (Szereplő)
- Kategória
- Tevékenység erőforrástípusa
- Tevékenység

![Naplók](./media/concept-audit-logs/23.png "Naplók")

A **dátumtartomány** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek:

- 1 hónap
- 7 nap
- 24 óra
- Egyéni

Egyéni időkeret kiválasztásakor beállíthatja a kezdő és a záró időpontot.

A **által kezdeményezett** szűrő lehetővé teszi egy szereplő nevének vagy egy univerzális egyszerű felhasználónév (UPN).

A **kategória** szűrővel az alábbi szűrők egyikét választhatja ki:

- Összes
- Alapvető kategória
- Alapvető könyvtár
- Önkiszolgáló jelszókezelés
- Önkiszolgáló csoportkezelés
- Fiók kiépítése – Automatizált jelszóváltás
- Meghívott felhasználók
- MIM szolgáltatás
- Identity Protection
- B2C

A **tevékenység erőforrástípusa** szűrővel az alábbi szűrők egyikét választhatja ki:

- Összes 
- Csoport
- Címtár
- Felhasználó
- Alkalmazás
- Szabályzat
- Eszköz
- Egyéb

Ha a **Csoport** elemet választja a **tevékenység erőforrástípusaként**, kap egy kiegészítő szűrőkategóriát, amelyben megadhatja a **forrást**:

- Azure AD
- O365


A **tevékenység** szűrő alapján a kategória és tevékenység erőforrás adattípus-választást meg. Választhat egy adott tevékenységet, amelyet meg szeretne tekinteni, vagy kiválaszthatja az összeset. 

A Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta használatával lekérheti az összes naplózási tevékenység listáját, ahol a $tenantdomain a tartománynév, illetve megtekintheti [naplózási jelentési eseményekkel kapcsolatos](reference-audit-activities.md) cikket.

## <a name="audit-logs-shortcuts"></a>Rövidebb utak a naplók eléréséhez

Az **Azure Active Directory** mellett az Azure Portal két további lehetőséget biztosít a naplózási adatok elérésére:

- Felhasználók és csoportok
- Vállalati alkalmazások

### <a name="users-and-groups-audit-logs"></a>Felhasználók és csoportok auditnaplói

A felhasználó- és csoportalapú naplózási jelentésekkel az alábbi kérdésekre kaphat választ:

- Milyen típusú frissítéseket telepítettek a felhasználók?

- Hány felhasználó lett módosítva?

- Hány jelszó lett módosítva?

- Mit csinált a rendszergazda egy adott címtárban?

- Mely csoportok lettek hozzáadva?

- Történt-e tagsági változás valamelyik csoportban?

- Változtak-e a csoportok tulajdonosai?

- Milyen licencek lettek hozzárendelve egy adott csoporthoz vagy felhasználóhoz?

Ha csak át szeretné tekinteni a felhasználókhoz és csoportokhoz kapcsolódó naplózási adatokat, megnyithat egy szűrt nézetet az **Auditnaplók** menüpontból, amely a **Felhasználók és csoportok** **Tevékenység** szakaszában található. Ennél a lehetőségnél a **Felhasználók és csoportok** van előre kiválasztva **tevékenység-erőforrástípusként**.

![Naplók](./media/concept-audit-logs/93.png "Naplók")

### <a name="enterprise-applications-audit-logs"></a>Vállalati alkalmazások naplói

Az alkalmazásalapú naplózási jelentésekkel az alábbi kérdésekre kaphat választ:

* Milyen alkalmazásokat lettek hozzáadva vagy frissítve?
* Mely alkalmazások lettek eltávolítva?
* Megváltozott egy egyszerű szolgáltatást az alkalmazás?
* Történt változás az alkalmazások nevében?
* Ki hagyott jóvá egy adott alkalmazást?

Ha szeretné ellenőrizni az alkalmazásaihoz kapcsolódó naplózási adatokat, Észreveheti, hogy egy szűrt nézetet **Auditnaplók** a a **tevékenység** szakaszában a **vállalati alkalmazások** panel. Ez a belépési pont rendelkezik **vállalati alkalmazások** előre kiválasztott, mint a **tevékenység erőforrástípusa**.

![Naplók](./media/concept-audit-logs/134.png "Naplók")

Ez a nézet le a szűrheti **csoportok** vagy **felhasználók**.

![Naplók](./media/concept-audit-logs/25.png "Naplók")

## <a name="office-365-activity-logs"></a>Az Office 365-Tevékenységnaplók

Az Office 365-Tevékenységnaplók is megtekintheti a [Office 365 felügyeleti központ](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Annak ellenére, hogy az Office 365-Tevékenységnaplók és az Azure AD tevékenység naplók a könyvtár-erőforrások jelentős részén osztoznak, csak az Office 365 felügyeleti központban az Office 365-Tevékenységnaplók teljes nézetét jeleníti meg. 

Az Office 365 tevékenységeket tartalmazó naplók programozott módon használatával is elérheti a [Office 365 felügyeleti API-k](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>További lépések

- [Az Azure AD naplózási tevékenységeire vonatkozó referencia](reference-audit-activities.md)
- [Az Azure AD-jelentések adatmegőrzési-referencia](reference-reports-data-retention.md)
- [Az Azure AD naplózási késések hivatkozhat.](reference-reports-latencies.md)
