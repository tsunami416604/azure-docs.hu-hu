---
title: Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon – bevezetés
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad399fc24b2cdfbdc51e7feccba2c05786216b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253233"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon 

Az Azure Active Directory (Azure AD) jelentéseivel megkaphatja a környezet működésének meghatározásához szükséges információkat.

A jelentéskészítési architektúra a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – A [bejelentkezések jelentés](concept-sign-ins.md) a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatával kapcsolatos információkat tartalmaz.
    - **Auditnaplók** – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplónaplók közé tartoznak például az Azure AD-n belüli erőforrásokon végrehajtott módosítások, például felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadása vagy eltávolítása.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – A [kockázatos bejelentkezés](concept-risky-sign-ins.md) egy olyan bejelentkezési kísérlet jelzője, amelyet olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 
    - **A kockázatra megjelölt felhasználók** – A [kockázatos felhasználó](concept-user-at-risk.md) egy olyan felhasználói fiók jelzője, amely veszélybe kerülhetett.

Ez a cikk áttekintést nyújt a könyvvizsgálati jelentésről.
 
## <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?

* A **Biztonsági rendszergazda**, a Biztonsági **olvasó**, a **Jelentésolvasó** , a **Globális olvasó** vagy a **Globális rendszergazda** szerepkör ben lévő felhasználók

## <a name="audit-logs"></a>Naplók

Az Azure AD naplózási naplók a rendszer tevékenységek megfelelőségi nyilvántartást biztosít. A naplózási jelentés eléréséhez válassza **a Naplók naplózása** lehetőséget az Azure Active Directory **Figyelés** **szakaszában.** Vegye figyelembe, hogy a naplónaplók késése akár egy óra is lehet, ezért a feladat befejezése után a naplózási tevékenység adatai nak ennyi ideig kell tartania.



Az auditnapló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- az előfordulás dátuma és időpontját
- az előfordulást naplózó szolgáltatás
- a tevékenység kategóriája és neve (*mi*) 
- a tevékenység állapota (sikeres vagy sikertelen)
- a célt
- a tevékenység kezdeményezőjét / szereplőjét (ki?)

![Naplók](./media/concept-audit-logs/listview.png "Naplók")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Naplók](./media/concept-audit-logs/columns.png "Naplók")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Naplók](./media/concept-audit-logs/columnselect.png "Naplók")

A részletesebb információk megtekintéséhez jelöljön ki egy elemet a listanézetben.

![Naplók](./media/concept-audit-logs/details.png "Naplók")


## <a name="filtering-audit-logs"></a>Auditnaplók szűrése

A naplózási adatok a következő mezőkön szűrhetők:

- Szolgáltatás
- Kategória
- Tevékenység
- status
- Cél
- Kezdeményező (Szereplő)
- Dátumtartomány

![Naplók](./media/concept-audit-logs/filter.png "Naplók")

A **Szolgáltatás** szűrő lehetővé teszi, hogy válasszon a következő szolgáltatások legördülő listájából:

- Összes
- AAD felügyeleti felhasználói felület
- Hozzáférési felülvizsgálatok
- Fiók kiépítése
- Alkalmazásproxy
- Hitelesítési módszerek
- B2C
- Feltételes hozzáférés
- Alapvető könyvtár
- Jogosultságok kezelése
- Hibrid hitelesítés
- Identity Protection
- Meghívott felhasználók
- MIM szolgáltatás
- MyApps
- PIM
- Önkiszolgáló csoportkezelés
- Önkiszolgáló jelszókezelés
- Használati feltételek

A **Kategória** szűrő lehetővé teszi az alábbi szűrők egyikének kiválasztását:

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
- KerberosTartomány
- Kulcskezelés
- Címke
- Egyéb
- PermissionGrantPolicy (PermissionGrantPolicy)
- Szabályzat
- Erőforrás-felügyelet
- Szerepkör-kezelés
- UserManagement (Felhasználókezelése)

A **Tevékenység** szűrő a kategória- és tevékenységerőforrás-típus kiválasztásán alapul. Választhat egy adott tevékenységet, amelyet meg szeretne tekinteni, vagy kiválaszthatja az összeset. 

Az összes naplózási tevékenység listáját a Graph API használatával szerezheti be:`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Az **Állapot** szűrő lehetővé teszi a naplózási művelet állapota alapján szűrést. Az állapot a következők egyike lehet:

- Összes
- Sikeres
- Hiba

A **Célszűrő** lehetővé teszi egy adott cél keresését a név vagy egyszerű felhasználónév (UPN) kezdetével. A célnév és az upn a kis- és nagybetűket is figyelembe veszi. 

A **Kezdeményezte** szűrő lehetővé teszi, hogy meghatározza, mi az aktor neve vagy egy univerzális egyszerű név (UPN) kezdődik. A név és az upn a kis- és nagybetűket megkülönböztető.

A **Dátumtartomány** szűrő lehetővé teszi a visszaadott adatok időkeretének meghatározását.  
Lehetséges értékek:

- 7 nap
- 24 óra
- Egyéni

Egyéni időkeret kiválasztásakor beállíthatja a kezdő és a záró időpontot.

A **Letöltés** gombra kattintva a szűrt adatokat akár 250 000 rekordot is letöltheti. A naplókcsv vagy JSON formátumban tölthetők le. A letölthető rekordok számát az [Azure Active Directory jelentésmegőrzési szabályzata](reference-reports-data-retention.md)korlátozza.

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

Ha csak a felhasználókhoz kapcsolódó naplózási adatokat szeretné áttekinteni, a **Naplók naplózása** csoportban, a **Felhasználók** lap **Figyelés** csoportjában található szűrt nézetet találhat. Ez a belépési pont **UserManagement** előkijelölt kategóriaként rendelkezik.

![Naplók](./media/concept-audit-logs/users.png "Naplók")

Ha csak a csoportokhoz kapcsolódó naplózási adatokat szeretné áttekinteni, a **Naplók naplózása** csoportban, a **Csoportok** lap **Figyelés** szakaszában található szűrt nézetet találhat. Ez a belépési pont **GroupManagement** előkijelölt kategóriaként rendelkezik.

![Naplók](./media/concept-audit-logs/groups.png "Naplók")

### <a name="enterprise-applications-audit-logs"></a>Vállalati alkalmazások naplói

Az alkalmazásalapú naplózási jelentésekkel az alábbi kérdésekre kaphat választ:

* Milyen alkalmazásokat adtak hozzá vagy frissítettek?
* Milyen alkalmazásokat távolítottak el?
* Megváltozott egy alkalmazás szolgáltatásnév?
* Történt változás az alkalmazások nevében?
* Ki hagyott jóvá egy adott alkalmazást?

Ha az alkalmazásokkal kapcsolatos naplózási adatokat szeretné áttekinteni, a **Naplók naplózása** csoportban, a **Vállalati alkalmazások** panel **Tevékenység** szakaszában található szűrt nézetet találhat. Ebben a belépési pontban a **vállalati alkalmazások** előre ki vannak jelölve **alkalmazástípusként.**

![Naplók](./media/concept-audit-logs/enterpriseapplications.png "Naplók")

## <a name="office-365-activity-logs"></a>Office 365 tevékenységi naplói

Az Office 365 tevékenységnaplóit a [Microsoft 365 Felügyeleti központból](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)tekintheti meg. Annak ellenére, hogy az Office 365-tevékenység és az Azure AD tevékenységnaplók sok címtár-erőforrást osztanak meg, csak a Microsoft 365 Felügyeleti központ nyújt teljes képet az Office 365 tevékenységnaplóiról. 

Az Office 365 tevékenységnaplóit programozott módon is elérheti az [Office 365 Felügyeleti API-k](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)használatával.

## <a name="next-steps"></a>További lépések

- [Azure AD naplózott tevékenységekre vonatkozó referencia](reference-audit-activities.md)
- [Az Azure AD-jelentések megőrzési hivatkozása](reference-reports-data-retention.md)
- [Azure AD-napló késési referencia](reference-reports-latencies.md)
