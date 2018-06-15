---
title: Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon – bevezetés
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 04/19/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: ae6e2b25ccc7b0d3f29b62e56279f60034ef728c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588641"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon 

Az Azure Portalon az Azure Active Directory (Azure AD) jelentéskészítési funkciójával minden szükséges információhoz hozzájuthat a környezetével kapcsolatban.

Az Azure AD jelentéskészítési architektúrája a következő elemekből áll:

- **Tevékenység** 
    - **Bejelentkezési tevékenységek** – A felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk
    - **Auditnaplók** – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók például rögzítik bármely erőforrás módosításait az Azure AD-n belül, úgymint: felhasználók, alkalmazások, csoportok, szerepkörök, szabályzatok, hitelesítések stb.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. További részletek: Kockázatos bejelentkezések.
    - **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. További részletek: Kockázatosként megjelölt felhasználók.

Ezen témakör áttekintést nyújt a naplózási tevékenységekről.
 
## <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
* A biztonsági rendszergazda vagy biztonsági olvasó szerepkörű felhasználók
* A globális rendszergazdák
* Az egyedi (nem rendszergazda jogosultságú) felhasználók csak a saját tevékenységüket láthatják


## <a name="audit-logs"></a>Naplók

Az Azure Active Directory naplói a rendszertevékenységek rekordjait tartalmazzák megfelelőségi célokból.  
A **Naplók** menüponton át vezet az út az összes naplózott adathoz – a menüpont az **Azure Active Directory** **Tevékenység** szakaszában található.

![Naplók](./media/active-directory-reporting-activity-audit-logs/61.png "Naplók")

Az auditnapló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- az előfordulás dátuma és időpontját
- a tevékenység kezdeményezőjét / szereplőjét (*ki?*) 
- a tevékenységet (*mi?*) 
- a célt

![Naplók](./media/active-directory-reporting-activity-audit-logs/18.png "Naplók")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Naplók](./media/active-directory-reporting-activity-audit-logs/19.png "Naplók")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Naplók](./media/active-directory-reporting-activity-audit-logs/21.png "Naplók")


A listanézet egyik elemére kattintva megtekintheti annak elérhető összes részletét.

![Naplók](./media/active-directory-reporting-activity-audit-logs/22.png "Naplók")


## <a name="filtering-audit-logs"></a>Auditnaplók szűrése

A jelentésben lévő adatok megfelelő szintű szűkítéséhez az alábbi mezőkkel szűrheti a naplózott adatokat:

- Dátumtartomány
- Kezdeményező (Szereplő)
- Kategória
- Tevékenység erőforrástípusa
- Tevékenység

![Naplók](./media/active-directory-reporting-activity-audit-logs/23.png "Naplók")


A **dátumtartomány** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek:

- 1 hónap
- 7 nap
- 24 óra
- Egyéni

Egyéni időkeret kiválasztásakor beállíthatja a kezdő és a záró időpontot.

A **Kezdeményező** szűrő lehetővé teszi egy szereplő nevének vagy UPN-jének megadását.

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


A **tevékenység** szűrő a kiválasztott kategórián és tevékenység-erőforrástípuson alapul. Választhat egy adott tevékenységet, amelyet meg szeretne tekinteni, vagy kiválaszthatja az összeset. 

A Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta használatával lekérheti az összes naplózási tevékenység listáját, ahol a $tenantdomain a tartománynév, illetve megtekintheti [naplózási jelentési eseményekkel kapcsolatos](active-directory-reporting-audit-events.md) cikket.


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

![Naplók](./media/active-directory-reporting-activity-audit-logs/93.png "Naplók")

### <a name="enterprise-applications-audit-logs"></a>Vállalati alkalmazások naplói

Az alkalmazásalapú naplózási jelentésekkel az alábbi kérdésekre kaphat választ:

* Mely alkalmazások lettek hozzáadva vagy frissítve?
* Mely alkalmazások lettek eltávolítva?
* Megváltozott valamelyik alkalmazás egyszerű szolgáltatásneve?
* Történt változás az alkalmazások nevében?
* Ki hagyott jóvá egy adott alkalmazást?

Ha csak át szeretné tekinteni az alkalmazásaihoz kapcsolódó naplózási adatokat, megnyithat egy szűrt nézetet az **Auditnaplók** menüpontból, amely a **Vállalati alkalmazások** panel **Tevékenység** szakaszában található. Ennél a lehetőségnél a **Vállalati alkalmazások** van előre kiválasztva **tevékenység-erőforrástípusként**.

![Naplók](./media/active-directory-reporting-activity-audit-logs/134.png "Naplók")

A nézetet tovább szűrheti csak a **csoportok** vagy csak a **felhasználók** megjelenítéséhez.

![Naplók](./media/active-directory-reporting-activity-audit-logs/25.png "Naplók")



## <a name="next-steps"></a>További lépések

- A jelentéskészítés áttekintéséért lásd: [Jelentéskészítés az Azure Active Directoryban](active-directory-reporting-azure-portal.md).

- A naplózási tevékenységek teljes listájáért tekintse meg az [Azure AD naplózási tevékenységeire vonatkozó referenciadokumentumokat](active-directory-reporting-activity-audit-reference.md).

