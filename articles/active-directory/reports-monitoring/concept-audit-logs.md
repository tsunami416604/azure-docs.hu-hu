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
ms.openlocfilehash: cf069e053068eaee3f36153c3888e6c711724267
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182166"
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

* A felhasználók a **biztonsági rendszergazdai**, **biztonsági olvasó**, **jelentés olvasó** vagy **globális rendszergazdai** szerepkörök
* Emellett minden felhasználója (nem rendszergazda) láthatja-e a saját naplózási tevékenységek

## <a name="audit-logs"></a>Naplók

Az Azure AD naplózási naplók, adja meg a megfelelőség a rendszertevékenységek rekordjait. A naplózási jelentések eléréséhez, válassza ki a **Auditnaplók** a a **tevékenység** szakaszában **Azure Active Directory**. Vegye figyelembe, hogy auditnaplók előfordulhat, hogy egy legfeljebb egy órát, késését, is igénybe vehet, hogy hosszú, és naplózási adatok megjelennek a portálon, a feladat befejezése után.

![Naplók](./media/concept-audit-logs/61.png "Naplók")

Az auditnapló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- az előfordulás dátuma és időpontját
- a szolgáltatás, amely az eseményt naplózza
- a kategória- és a tevékenység neve (*mi*) 
- a tevékenység (sikeres vagy sikertelen) állapota
- a célt
- A kezdeményező / aktor (ki) a tevékenységek

![Naplók](./media/concept-audit-logs/listview.png "Naplók")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Naplók](./media/concept-audit-logs/columns.png "Naplók")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Naplók](./media/concept-audit-logs/columnselect.png "Naplók")

Jelöljön ki egy elemet a listanézet, melyeken részletesebb információkat találhat.

![Naplók](./media/concept-audit-logs/details.png "Naplók")


## <a name="filtering-audit-logs"></a>Auditnaplók szűrése

A naplózási adatok az alábbi mezőkkel szűrheti:

- Szolgáltatás
- Kategória
- Tevékenység
- status
- Cél
- Kezdeményező (Szereplő)
- Dátumtartomány

![Naplók](./media/concept-audit-logs/filter.png "Naplók")

A **szolgáltatás** szűrő lehetővé teszi, hogy a következő szolgáltatások a legördülő listából választhatja ki:

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
- Önkiszolgáló Passord kezelése
- Használati feltételek

A **kategória** szűrővel az alábbi szűrők egyikét választhatja ki:

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

A **tevékenység** szűrő alapján a kategória és tevékenység erőforrás adattípus-választást meg. Választhat egy adott tevékenységet, amelyet meg szeretne tekinteni, vagy kiválaszthatja az összeset. 

A Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta használatával lekérheti az összes naplózási tevékenység listáját, ahol a $tenantdomain a tartománynév, illetve megtekintheti [naplózási jelentési eseményekkel kapcsolatos](reference-audit-activities.md) cikket.

A **állapot** szűrő lehetővé teszi a szűrést, egy naplózási művelet állapota alapján. Az állapot a következők egyike lehet:

- Összes
- Sikeres
- Hiba

A **cél** szűrő lehetővé teszi, hogy egy adott célhoz keresése név vagy egyszerű felhasználónév (UPN) alapján. A cél neve és az egyszerű felhasználónév-és nagybetűk. 

A **által kezdeményezett** szűrő lehetővé teszi egy szereplő nevének vagy egy univerzális egyszerű felhasználónév (UPN). A nevét és az egyszerű felhasználónév-és nagybetűk.

A **dátumtartomány** szűrővel időkeretet lehet a visszaadott adatokhoz.  
Lehetséges értékek:

- 1 hónap
- 7 nap
- 24 óra
- Egyéni

Egyéni időkeret kiválasztásakor beállíthatja a kezdő és a záró időpontot.

Azt is beállíthatja, töltse le a szűrt adatokat, legfeljebb 250 000 rekordot kiválasztásával a **letöltése** gombra. Választhatja a CSV vagy JSON formátumban a naplók letöltéséhez. Letöltheti a rekordok száma korlátozza a [Azure Active Directory-jelentések adatmegőrzési szabályzatában](reference-reports-data-retention.md).

![Naplók](./media/concept-audit-logs/download.png "Naplók")

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

Ha csak át szeretné tekintse át a felhasználók kapcsolódó naplózási adatokat, Észreveheti, hogy egy szűrt nézetet **Auditnaplók** a a **tevékenység** szakaszában a **felhasználók** fülre. Ez a belépési pont rendelkezik **UserManagement** telepítendő, előre kiválasztott kategória szerint.

![Naplók](./media/concept-audit-logs/users.png "Naplók")

Ha csak szeretné ellenőrizni a csoportokhoz kapcsolódó naplózási adatokat, Észreveheti, hogy egy szűrt nézetet **Auditnaplók** a a **tevékenység** szakaszában a **csoportok** fülre. Ez a belépési pont rendelkezik **GroupManagement** telepítendő, előre kiválasztott kategória szerint.

![Naplók](./media/concept-audit-logs/groups.png "Naplók")

### <a name="enterprise-applications-audit-logs"></a>Vállalati alkalmazások naplói

Az alkalmazásalapú naplózási jelentésekkel az alábbi kérdésekre kaphat választ:

* Milyen alkalmazásokat lettek hozzáadva vagy frissítve?
* Mely alkalmazások lettek eltávolítva?
* Megváltozott egy egyszerű szolgáltatást az alkalmazás?
* Történt változás az alkalmazások nevében?
* Ki hagyott jóvá egy adott alkalmazást?

Ha szeretné ellenőrizni az alkalmazásaihoz kapcsolódó naplózási adatokat, Észreveheti, hogy egy szűrt nézetet **Auditnaplók** a a **tevékenység** szakaszában a **vállalati alkalmazások** panel. Ez a belépési pont rendelkezik **vállalati alkalmazások** előre kiválasztott, mint a **alkalmazástípus**.

![Naplók](./media/concept-audit-logs/enterpriseapplications.png "Naplók")

## <a name="office-365-activity-logs"></a>Az Office 365-Tevékenységnaplók

Az Office 365-Tevékenységnaplók is megtekintheti a [Microsoft 365 felügyeleti központban](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Annak ellenére, hogy az Office 365-Tevékenységnaplók és az Azure AD tevékenység naplók a könyvtár-erőforrások jelentős részén osztoznak, csak a Microsoft 365 felügyeleti központban az Office 365-Tevékenységnaplók teljes nézetét jeleníti meg. 

Az Office 365 tevékenységeket tartalmazó naplók programozott módon használatával is elérheti a [Office 365 felügyeleti API-k](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>További lépések

- [Az Azure AD naplózási tevékenységeire vonatkozó referencia](reference-audit-activities.md)
- [Az Azure AD-jelentések adatmegőrzési-referencia](reference-reports-data-retention.md)
- [Az Azure AD naplózási késések hivatkozhat.](reference-reports-latencies.md)
