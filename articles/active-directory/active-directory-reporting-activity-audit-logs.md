---
title: Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon – bevezetés
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/31/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 97ea32a1e0f8815accff6201251771ab8c088859
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon 

Az Azure Portalon az Azure Active Directory (Azure AD) jelentéskészítési funkciójával minden szükséges információhoz hozzájuthat a környezetével kapcsolatban.

Az Azure AD jelentéskészítési architektúrája a következő elemekből áll:

- **Tevékenység** 
    - **Bejelentkezési tevékenységek** – A felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk
    - **Naplók** – Rendszertevékenység információk a felhasználó- és csoportfelügyeletre, valamint a felügyelt alkalmazásokra és a címtártevékenységekre vonatkozóan.
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



## <a name="azure-ad-audit-activity-list"></a>Az Azure AD naplózási tevékenységeinek listája

Ebben a szakaszban felsoroljuk az összes naplózható tevékenységet. 


|Szolgáltatásnév|Naplózási kategória|Tevékenység erőforrástípusa|Tevékenység|
|---|---|---|---|
|Fiók kiépítése|Alkalmazáskezelés|Alkalmazás|Adminisztráció|
|Fiók kiépítése|Alkalmazáskezelés|Alkalmazás|Címtárművelet|
|Fiók kiépítése|Alkalmazáskezelés|Alkalmazás|Exportálás|
|Fiók kiépítése|Alkalmazáskezelés|Alkalmazás|Importálás|
|Fiók kiépítése|Alkalmazáskezelés|Alkalmazás|Egyéb|
|Fiók kiépítése|Alkalmazáskezelés|Alkalmazás|Folyamatletét|
|Fiók kiépítése|Alkalmazáskezelés|Alkalmazás|Szinkronizálási szabályművelet|
|Alkalmazásproxy|Alkalmazáskezelés|Alkalmazás|Alkalmazás hozzáadása|
|Alkalmazásproxy|Erőforrás|Erőforrás|Alkalmazás SSL-tanúsítványának hozzáadása|
|Alkalmazásproxy|Erőforrás|Erőforrás|SSL-kötés törlése|
|Alkalmazásproxy|Alkalmazáskezelés|Alkalmazás|Alkalmazás törlése|
|Alkalmazásproxy|Címtárkezelés|Címtár|Asztali egyszeri bejelentkezés letiltása|
|Alkalmazásproxy|Címtárkezelés|Címtár|Asztali egyszeri bejelentkezés letiltása adott tartományon|
|Alkalmazásproxy|Címtárkezelés|Címtár|Alkalmazásproxy letiltása|
|Alkalmazásproxy|Címtárkezelés|Címtár|Átmenő hitelesítés letiltása|
|Alkalmazásproxy|Címtárkezelés|Címtár|Asztali egyszeri bejelentkezés engedélyezése|
|Alkalmazásproxy|Címtárkezelés|Címtár|Asztali egyszeri bejelentkezés engedélyezése adott tartományhoz|
|Alkalmazásproxy|Címtárkezelés|Címtár|Alkalmazásproxy engedélyezése|
|Alkalmazásproxy|Címtárkezelés|Címtár|Átmenő hitelesítés engedélyezése|
|Alkalmazásproxy|Erőforrás|Erőforrás|Összekötő regisztrálása|
|Alkalmazásproxy|Alkalmazáskezelés|Alkalmazás|Az alkalmazás frissítése|
|Alkalmazásproxy|Alkalmazáskezelés|Alkalmazás|Alkalmazás frissítése: egyszeri bejelentkezés üzemmód|
|Automatizált jelszóváltás|Alkalmazáskezelés|Alkalmazás|Automatizált jelszóváltás|
|B2C|Alkalmazáskezelés|Alkalmazás|V2 alkalmazásengedélyek hozzáadása|
|B2C|Engedélyezés|Engedélyezés|V2 alkalmazásengedélyek hozzáadása|
|B2C|Kulcs|Kulcs|ASCII-titkosításon alapuló kulcs hozzáadása CPIM-kulcstárolóhoz|
|B2C|Engedélyezés|Engedélyezés|ASCII-titkosításon alapuló kulcs hozzáadása CPIM-kulcstárolóhoz|
|B2C|Kulcs|Kulcs|Kulcs hozzáadása CPIM-kulcstárolóhoz|
|B2C|Engedélyezés|Engedélyezés|Kulcs hozzáadása CPIM-kulcstárolóhoz|
|B2C|Erőforrás|Erőforrás|AdminPolicyDatas-RemoveResources|
|B2C|Engedélyezés|Engedélyezés|AdminPolicyDatas-SetResources|
|B2C|Erőforrás|Erőforrás|AdminPolicyDatas-SetResources|
|B2C|Erőforrás|Erőforrás|AdminUserJourneys-GetResources|
|B2C|Engedélyezés|Engedélyezés|AdminUserJourneys-GetResources|
|B2C|Engedélyezés|Engedélyezés|AdminUserJourneys-RemoveResources|
|B2C|Erőforrás|Erőforrás|AdminUserJourneys-RemoveResources|
|B2C|Erőforrás|Erőforrás|AdminUserJourneys-SetResources|
|B2C|Engedélyezés|Engedélyezés|AdminUserJourneys-SetResources|
|B2C|Engedélyezés|Engedélyezés|IdentityProvider létrehozása|
|B2C|Erőforrás|Erőforrás|IdentityProvider létrehozása|
|B2C|Engedélyezés|Engedélyezés|V1 alkalmazás létrehozása|
|B2C|Alkalmazáskezelés|Alkalmazás|V1 alkalmazás létrehozása|
|B2C|Alkalmazáskezelés|Alkalmazás|V2 alkalmazás létrehozása|
|B2C|Engedélyezés|Engedélyezés|V2 alkalmazás létrehozása|
|B2C|Címtárkezelés|Címtár|Egyéni tartomány létrehozása a bérlőben|
|B2C|Engedélyezés|Engedélyezés|Egyéni tartomány létrehozása a bérlőben|
|B2C|Engedélyezés|Engedélyezés|Új AdminUserJourney létrehozása|
|B2C|Erőforrás|Erőforrás|Új AdminUserJourney létrehozása|
|B2C|Erőforrás|Erőforrás|Honosított json-erőforrás létrehozása|
|B2C|Engedélyezés|Engedélyezés|Honosított json-erőforrás létrehozása|
|B2C|Engedélyezés|Engedélyezés|Új egyéni identitásszolgáltató létrehozása|
|B2C|Erőforrás|Erőforrás|Új egyéni identitásszolgáltató létrehozása|
|B2C|Erőforrás|Erőforrás|Új identitásszolgáltató létrehozása|
|B2C|Engedélyezés|Engedélyezés|Új identitásszolgáltató létrehozása|
|B2C|Engedélyezés|Engedélyezés|B2C címtárerőforrás létrehozása vagy frissítése|
|B2C|Erőforrás|Erőforrás|B2C címtárerőforrás létrehozása vagy frissítése|
|B2C|Erőforrás|Erőforrás|Szabályzat létrehozása|
|B2C|Engedélyezés|Engedélyezés|Szabályzat létrehozása|
|B2C|Engedélyezés|Engedélyezés|trustFramework szabályzat létrehozása|
|B2C|Erőforrás|Erőforrás|trustFramework szabályzat létrehozása|
|B2C|Engedélyezés|Engedélyezés|Konfigurálható előtaggal rendelkező trustFramework szabályzat létrehozása|
|B2C|Erőforrás|Erőforrás|Konfigurálható előtaggal rendelkező trustFramework szabályzat létrehozása|
|B2C|Erőforrás|Erőforrás|Felhasználói attribútum létrehozása|
|B2C|Engedélyezés|Engedélyezés|Felhasználói attribútum létrehozása|
|B2C|Engedélyezés|Engedélyezés|CreateTrustFrameworkPolicy|
|B2C|Erőforrás|Erőforrás|CreateTrustFrameworkPolicy|
|B2C|Engedélyezés|Engedélyezés|Új AdminUserJourney létrehozása vagy frissítése|
|B2C|Erőforrás|Erőforrás|Identitásszolgáltató törlése|
|B2C|Engedélyezés|Engedélyezés|Identitásszolgáltató törlése|
|B2C|Erőforrás|Erőforrás|IdentityProvider törlése|
|B2C|Engedélyezés|Engedélyezés|IdentityProvider törlése|
|B2C|Alkalmazáskezelés|Alkalmazás|V1 alkalmazás törlése|
|B2C|Engedélyezés|Engedélyezés|V1 alkalmazás törlése|
|B2C|Alkalmazáskezelés|Alkalmazás|V2 alkalmazás törlése|
|B2C|Engedélyezés|Engedélyezés|V2 alkalmazás törlése|
|B2C|Engedélyezés|Engedélyezés|V2 alkalmazás engedélymegadásának törlése|
|B2C|Alkalmazáskezelés|Alkalmazás|V2 alkalmazás engedélymegadásának törlése|
|B2C|Erőforrás|Erőforrás|B2C címtárerőforrás törlése|
|B2C|Engedélyezés|Engedélyezés|B2C címtárerőforrás törlése|
|B2C|Kulcs|Kulcs|CPIM-kulcstároló törlése|
|B2C|Engedélyezés|Engedélyezés|CPIM-kulcstároló törlése|
|B2C|Kulcs|Kulcs|Kulcstároló törlése|
|B2C|Erőforrás|Erőforrás|trustFramework szabályzat törlése|
|B2C|Engedélyezés|Engedélyezés|trustFramework szabályzat törlése|
|B2C|Erőforrás|Erőforrás|Felhasználói attribútum törlése|
|B2C|Engedélyezés|Engedélyezés|Felhasználói attribútum törlése|
|B2C|Engedélyezés|Engedélyezés|B2C funkció engedélyezése|
|B2C|Címtárkezelés|Címtár|B2C funkció engedélyezése|
|B2C|Erőforrás|Erőforrás|Erőforráscsoporthoz tartozó B2C címtárerőforrások lekérése|
|B2C|Erőforrás|Erőforrás|Előfizetéshez tartozó B2C címtárerőforrások lekérése|
|B2C|Engedélyezés|Engedélyezés|Előfizetéshez tartozó B2C címtárerőforrások lekérése|
|B2C|Engedélyezés|Engedélyezés|Egyéni identitásszolgáltató lekérése|
|B2C|Erőforrás|Erőforrás|Egyéni identitásszolgáltató lekérése|
|B2C|Erőforrás|Erőforrás|Identitásszolgáltató lekérése|
|B2C|Engedélyezés|Engedélyezés|Identitásszolgáltató lekérése|
|B2C|Engedélyezés|Engedélyezés|V1 és V2 alkalmazások lekérése|
|B2C|Alkalmazáskezelés|Alkalmazás|V1 és V2 alkalmazások lekérése|
|B2C|Engedélyezés|Engedélyezés|V1 alkalmazás lekérése|
|B2C|Alkalmazáskezelés|Alkalmazás|V1 alkalmazás lekérése|
|B2C|Engedélyezés|Engedélyezés|V1 alkalmazások lekérése|
|B2C|Alkalmazáskezelés|Alkalmazás|V1 alkalmazások lekérése|
|B2C|Alkalmazáskezelés|Alkalmazás|V2 alkalmazás lekérése|
|B2C|Engedélyezés|Engedélyezés|V2 alkalmazás lekérése|
|B2C|Alkalmazáskezelés|Alkalmazás|V2 alkalmazások lekérése|
|B2C|Engedélyezés|Engedélyezés|V2 alkalmazások lekérése|
|B2C|Erőforrás|Erőforrás|B2C címtárerőforrás lekérése|
|B2C|Engedélyezés|Engedélyezés|B2C címtárerőforrás lekérése|
|B2C|Engedélyezés|Engedélyezés|A bérlőben lévő egyéni tartományok listájának lekérése|
|B2C|Címtárkezelés|Címtár|A bérlőben lévő egyéni tartományok listájának lekérése|
|B2C|Engedélyezés|Engedélyezés|Felhasználói út lekérése|
|B2C|Erőforrás|Erőforrás|Felhasználói út lekérése|
|B2C|Erőforrás|Erőforrás|A felhasználói út engedélyezett alkalmazásjogcímeinek lekérése|
|B2C|Engedélyezés|Engedélyezés|A felhasználói út engedélyezett alkalmazásjogcímeinek lekérése|
|B2C|Erőforrás|Erőforrás|A felhasználói út engedélyezett önellenőrzött jogcímeinek lekérése|
|B2C|Engedélyezés|Engedélyezés|A felhasználói út engedélyezett önellenőrzött jogcímeinek lekérése|
|B2C|Erőforrás|Erőforrás|A szabályzat engedélyezett önellenőrzött jogcímeinek lekérése|
|B2C|Engedélyezés|Engedélyezés|A szabályzat engedélyezett önellenőrzött jogcímeinek lekérése|
|B2C|Erőforrás|Erőforrás|Elérhető kimeneti jogcímek listájának lekérése|
|B2C|Engedélyezés|Engedélyezés|Elérhető kimeneti jogcímek listájának lekérése|
|B2C|Erőforrás|Erőforrás|A felhasználói út tartalomdefinícióinak lekérése|
|B2C|Engedélyezés|Engedélyezés|A felhasználói út tartalomdefinícióinak lekérése|
|B2C|Engedélyezés|Engedélyezés|Adott rendszergazdai folyamathoz tartozó identitásszolgáltatók lekérése|
|B2C|Erőforrás|Erőforrás|Adott rendszergazdai folyamathoz tartozó identitásszolgáltatók lekérése|
|B2C|Kulcs|Kulcs|Kulcstároló aktív kulcsaihoz tartozó metaadatok lekérése JWK-ban|
|B2C|Engedélyezés|Engedélyezés|Kulcstároló aktív kulcsaihoz tartozó metaadatok lekérése JWK-ban|
|B2C|Kulcs|Kulcs|Kulcstároló metaadatainak lekérése|
|B2C|Erőforrás|Erőforrás|Az összes rendszergazdai folyamat|
|B2C|Engedélyezés|Engedélyezés|Az összes rendszergazdai folyamat|
|B2C|Engedélyezés|Engedélyezés|Az összes felhasználó összes rendszergazdai folyamatához tartozó címkék listájának lekérése|
|B2C|Erőforrás|Erőforrás|Az összes felhasználó összes rendszergazdai folyamatához tartozó címkék listájának lekérése|
|B2C|Erőforrás|Erőforrás|Felhasználó bérlői listájának lekérése|
|B2C|Engedélyezés|Engedélyezés|Felhasználó bérlői listájának lekérése|
|B2C|Erőforrás|Erőforrás|Helyi fiókok önellenőrző jogcímeinek lekérése|
|B2C|Engedélyezés|Engedélyezés|Helyi fiókok önellenőrző jogcímeinek lekérése|
|B2C|Erőforrás|Erőforrás|Honosított json-erőforrás lekérése|
|B2C|Engedélyezés|Engedélyezés|Honosított json-erőforrás lekérése|
|B2C|Engedélyezés|Engedélyezés|A Microsoft.AzureActiveDirectory erőforrás-szolgáltató műveleteinek lekérése|
|B2C|Erőforrás|Erőforrás|A Microsoft.AzureActiveDirectory erőforrás-szolgáltató műveleteinek lekérése|
|B2C|Erőforrás|Erőforrás|Szabályzatok lekérése|
|B2C|Engedélyezés|Engedélyezés|Szabályzatok lekérése|
|B2C|Erőforrás|Erőforrás|Szabályzat lekérése|
|B2C|Engedélyezés|Engedélyezés|Szabályzat lekérése|
|B2C|Címtárkezelés|Címtár|Bérlő erőforrás-tulajdonságainak lekérése|
|B2C|Engedélyezés|Engedélyezés|Bérlő erőforrás-tulajdonságainak lekérése|
|B2C|Erőforrás|Erőforrás|Támogatott identitásszolgáltatók listájának lekérése|
|B2C|Engedélyezés|Engedélyezés|Támogatott identitásszolgáltatók listájának lekérése|
|B2C|Erőforrás|Erőforrás|A felhasználói út támogatott identitásszolgáltatói listájának lekérése|
|B2C|Engedélyezés|Engedélyezés|A felhasználói út támogatott identitásszolgáltatói listájának lekérése|
|B2C|Címtárkezelés|Címtár|Bérlői adatok lekérése|
|B2C|Engedélyezés|Engedélyezés|Bérlői adatok lekérése|
|B2C|Címtárkezelés|Címtár|Bérlő számára engedélyezett funkciók lekérése|
|B2C|Engedélyezés|Engedélyezés|Bérlő számára engedélyezett funkciók lekérése|
|B2C|Engedélyezés|Engedélyezés|Bérlő által definiált egyéni identitásszolgáltatók listájának lekérése|
|B2C|Erőforrás|Erőforrás|Bérlő által definiált egyéni identitásszolgáltatók listájának lekérése|
|B2C|Erőforrás|Erőforrás|Bérlő által definiált identitásszolgáltatók listájának lekérése|
|B2C|Engedélyezés|Engedélyezés|Bérlő által definiált identitásszolgáltatók listájának lekérése|
|B2C|Erőforrás|Erőforrás|Bérlő által definiált helyi identitásszolgáltató-lista lekérése|
|B2C|Engedélyezés|Engedélyezés|Bérlő által definiált helyi identitásszolgáltató-lista lekérése|
|B2C|Erőforrás|Erőforrás|Felhasználó bérlői információinak lekérése erőforrások létrehozásához|
|B2C|Engedélyezés|Engedélyezés|Felhasználó bérlői információinak lekérése erőforrások létrehozásához|
|B2C|Engedélyezés|Engedélyezés|Bérlők listájának lekérése|
|B2C|Engedélyezés|Engedélyezés|tenantDomains lekérése|
|B2C|Címtárkezelés|Címtár|tenantDomains lekérése|
|B2C|Erőforrás|Erőforrás|A CPIM alapértelmezett támogatott kultúrájának lekérése|
|B2C|Engedélyezés|Engedélyezés|A CPIM alapértelmezett támogatott kultúrájának lekérése|
|B2C|Erőforrás|Erőforrás|Rendszergazdai folyamathoz tartozó információk lekérése|
|B2C|Engedélyezés|Engedélyezés|Rendszergazdai folyamathoz tartozó információk lekérése|
|B2C|Engedélyezés|Engedélyezés|A bérlőhöz tartozó UserJourneys lista lekérése|
|B2C|Erőforrás|Erőforrás|A bérlőhöz tartozó UserJourneys lista lekérése|
|B2C|Engedélyezés|Engedélyezés|A CPIM-hez tartozó elérhető, támogatott kultúrák készletének lekérése|
|B2C|Erőforrás|Erőforrás|A CPIM-hez tartozó elérhető, támogatott kultúrák készletének lekérése|
|B2C|Engedélyezés|Engedélyezés|TrustFramework szabályzat lekérése|
|B2C|Erőforrás|Erőforrás|TrustFramework szabályzat lekérése|
|B2C|Engedélyezés|Engedélyezés|TrustFramework szabályzat lekérése xml-ként|
|B2C|Erőforrás|Erőforrás|TrustFramework szabályzat lekérése xml-ként|
|B2C|Erőforrás|Erőforrás|Felhasználói attribútum lekérése|
|B2C|Engedélyezés|Engedélyezés|Felhasználói attribútum lekérése|
|B2C|Engedélyezés|Engedélyezés|Felhasználói attribútumok lekérése|
|B2C|Erőforrás|Erőforrás|Felhasználói attribútumok lekérése|
|B2C|Engedélyezés|Engedélyezés|Felhasználói utak listájának lekérése|
|B2C|Erőforrás|Erőforrás|Felhasználói utak listájának lekérése|
|B2C|Engedélyezés|Engedélyezés|GetIEFPolicies|
|B2C|Erőforrás|Erőforrás|GetIEFPolicies|
|B2C|Engedélyezés|Engedélyezés|GetIdentityProviders|
|B2C|Erőforrás|Erőforrás|GetIdentityProviders|
|B2C|Erőforrás|Erőforrás|GetTrustFrameworkPolicy|
|B2C|Engedélyezés|Engedélyezés|GetTrustFrameworkPolicy|
|B2C|Kulcs|Kulcs|Lekérdez egy CPIM-kulcstárolót jwk formátumban|
|B2C|Engedélyezés|Engedélyezés|Lekérdez egy CPIM-kulcstárolót jwk formátumban|
|B2C|Kulcs|Kulcs|Lekérdezi a bérlőben lévő kulcstárolók listáját|
|B2C|Engedélyezés|Engedélyezés|Lekérdezi a bérlőben lévő kulcstárolók listáját|
|B2C|Engedélyezés|Engedélyezés|Lekérdezi a bérlő típusát|
|B2C|Címtárkezelés|Címtár|Lekérdezi a bérlő típusát|
|B2C|Hitelesítés|Hitelesítés|Hozzáférési jogkivonat kiadása az alkalmazásnak|
|B2C|Hitelesítés|Hitelesítés|Hitelesítési kód kiadása az alkalmazásnak|
|B2C|Egyéb|Egyéb|Hitelesítési kód kiadása az alkalmazásnak|
|B2C|Hitelesítés|Hitelesítés|id_token kiadása az alkalmazásnak|
|B2C|Egyéb|Egyéb|id_token kiadása az alkalmazásnak|
|B2C|Engedélyezés|Engedélyezés|MigrateTenantMetadata|
|B2C|Erőforrás|Erőforrás|MigrateTenantMetadata|
|B2C|Erőforrás|Erőforrás|Erőforrások áthelyezése|
|B2C|Engedélyezés|Engedélyezés|IdentityProvider javítása|
|B2C|Erőforrás|Erőforrás|IdentityProvider javítása|
|B2C|Erőforrás|Erőforrás|PutTrustFrameworkPolicy|
|B2C|Engedélyezés|Engedélyezés|PutTrustFrameworkPolicy|
|B2C|Engedélyezés|Engedélyezés|PutTrustFrameworkpolicy|
|B2C|Erőforrás|Erőforrás|PutTrustFrameworkpolicy|
|B2C|Erőforrás|Erőforrás|Felhasználói út eltávolítása|
|B2C|Engedélyezés|Engedélyezés|Felhasználói út eltávolítása|
|B2C|Engedélyezés|Engedélyezés|CPIM-kulcstároló biztonsági másolatának visszaállítása|
|B2C|Kulcs|Kulcs|CPIM-kulcstároló biztonsági másolatának visszaállítása|
|B2C|Alkalmazáskezelés|Alkalmazás|V2 alkalmazás engedélymegadásainak beolvasása|
|B2C|Engedélyezés|Engedélyezés|V2 alkalmazás engedélymegadásainak beolvasása|
|B2C|Alkalmazáskezelés|Alkalmazás|V2 alkalmazások szolgáltatásnevének lekérése az aktuális bérlőben|
|B2C|Engedélyezés|Engedélyezés|V2 alkalmazások szolgáltatásnevének lekérése az aktuális bérlőben|
|B2C|Kulcs|Kulcs|Kulcstároló mentése|
|B2C|Engedélyezés|Engedélyezés|Egyéni identitásszolgáltató frissítése|
|B2C|Erőforrás|Erőforrás|Egyéni identitásszolgáltató frissítése|
|B2C|Erőforrás|Erőforrás|Identitásszolgáltató frissítése|
|B2C|Engedélyezés|Engedélyezés|Identitásszolgáltató frissítése|
|B2C|Erőforrás|Erőforrás|Helyi identitásszolgáltató frissítése|
|B2C|Engedélyezés|Engedélyezés|Helyi identitásszolgáltató frissítése|
|B2C|Alkalmazáskezelés|Alkalmazás|V1 alkalmazás frissítése|
|B2C|Engedélyezés|Engedélyezés|V1 alkalmazás frissítése|
|B2C|Alkalmazáskezelés|Alkalmazás|V2 alkalmazás frissítése|
|B2C|Engedélyezés|Engedélyezés|V2 alkalmazás frissítése|
|B2C|Alkalmazáskezelés|Alkalmazás|V2 alkalmazás engedélymegadásának frissítése|
|B2C|Engedélyezés|Engedélyezés|V2 alkalmazás engedélymegadásának frissítése|
|B2C|Erőforrás|Erőforrás|B2C címtárerőforrás frissítése|
|B2C|Erőforrás|Erőforrás|Szabályzat frissítése|
|B2C|Engedélyezés|Engedélyezés|Szabályzat frissítése|
|B2C|Erőforrás|Erőforrás|Előfizetés állapotának frissítése|
|B2C|Erőforrás|Erőforrás|Felhasználói attribútum frissítése|
|B2C|Engedélyezés|Engedélyezés|Felhasználói attribútum frissítése|
|B2C|Kulcs|Kulcs|CPIM titkosított kulcs feltöltése|
|B2C|Engedélyezés|Engedélyezés|CPIM titkosított kulcs feltöltése|
|B2C|Engedélyezés|Engedélyezés|Felhasználói hitelesítés: az API le van tiltva a bérlő funkciókészletéhez|
|B2C|Engedélyezés|Engedélyezés|Felhasználói hitelesítés: A felhasználó bérlői rendszergazdaként kap hozzáférést|
|B2C|Engedélyezés|Engedélyezés|Felhasználói hitelesítés: Felhasználó hitelesített felhasználóként kapott hozzáférési jogokat|
|B2C|Hitelesítés|Hitelesítés|Helyi fiók hitelesítő adatainak ellenőrzése|
|B2C|Erőforrás|Erőforrás|Erőforrások áthelyezésének ellenőrzése|
|B2C|Hitelesítés|Hitelesítés|Felhasználóhitelesítés ellenőrzése|
|B2C|Címtárkezelés|Címtár|B2C funkció engedélyezésének ellenőrzése|
|B2C|Engedélyezés|Engedélyezés|B2C funkció engedélyezésének ellenőrzése|
|B2C|Engedélyezés|Engedélyezés|Ellenőrizze, hogy a funkció engedélyezve van-e|
|B2C|Címtárkezelés|Címtár|Ellenőrizze, hogy a funkció engedélyezve van-e|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Oauth2Permissiongrant hozzáadása|
|Alapvető könyvtár|Adminisztratív egységek kezelése|AdministrativeUnit|Adminisztratív egység hozzáadása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Alkalmazásszerepkör-hozzárendelés megadása a felhasználónak|
|Alapvető könyvtár|Csoportkezelés|Csoport|Alkalmazásszerepkör-hozzárendelés hozzáadása csoporthoz|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Alkalmazásszerepkör-hozzárendelés hozzáadása a szolgáltatásnévhez|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Alkalmazás hozzáadása|
|Alapvető könyvtár|Erőforrás|Erőforrás|Eszköz hozzáadása|
|Alapvető könyvtár|Erőforrás|Erőforrás|Eszközkonfiguráció hozzáadása|
|Alapvető könyvtár|Szerepkörkezelés|Szerepkör|Jogosult tag hozzáadása szerepkörhöz|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoport hozzáadása|
|Alapvető könyvtár|Adminisztratív egységek kezelése|AdministrativeUnit|Tag hozzáadása adminisztratív egységhez|
|Alapvető könyvtár|Csoportkezelés|Csoport|Tag hozzáadása csoporthoz|
|Alapvető könyvtár|Szerepkörkezelés|Szerepkör|Tag hozzáadása szerepkörhöz|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Tulajdonos hozzáadása alkalmazáshoz|
|Alapvető könyvtár|Csoportkezelés|Csoport|Tulajdonos hozzáadása csoporthoz|
|Alapvető könyvtár|Szabályzatkezelés|Szabályzat|Tulajdonos hozzáadása szabályzathoz|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Tulajdonos hozzáadása szolgáltatásnévhez|
|Alapvető könyvtár|Címtárkezelés|Címtár|Partner hozzáadása vállalathoz|
|Alapvető könyvtár|Szabályzatkezelés|Szabályzat|Házirend hozzáadása|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Szabályzat hozzáadása szolgáltatásnévhez|
|Alapvető könyvtár|Erőforrás|Erőforrás|Regisztrált felhasználó hozzáadása eszközhöz|
|Alapvető könyvtár|Erőforrás|Erőforrás|Regisztrált felhasználók hozzáadása eszközhöz|
|Alapvető könyvtár|Szerepkörkezelés|Szerepkör|Szerepkör-hozzárendelés hozzáadása szerepkör-definícióhoz|
|Alapvető könyvtár|Szerepkörkezelés|Szerepkör|Szerepkör hozzáadása sablonból|
|Alapvető könyvtár|Szerepkörkezelés|Szerepkör|Hatókörön belüli tag hozzáadása a szerepkörhöz|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Szolgáltatásnév hozzáadása|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Szolgáltatásnév hitelesítő adatainak hozzáadása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Nem ellenőrzött tartomány hozzáadása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználó hozzáadása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználók erős hitelesítési telefonos alkalmazásra vonatkozó információinak hozzáadása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Ellenőrzött tartomány hozzáadása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználói licenc módosítása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználói jelszó módosítása|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Hozzájárulás az alkalmazáshoz|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Összevont felhasználó konvertálása felügyeltre|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Alkalmazásjelszó létrehozása a felhasználó számára|
|Alapvető könyvtár|Címtárkezelés|Címtár|Vállalat létrehozása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Vállalati beállítások létrehozása|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoportbeállítások létrehozása|
|Alapvető könyvtár|Adminisztratív egységek kezelése|AdministrativeUnit|Adminisztratív egység törlése|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Alkalmazás törlése|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználó alkalmazásjelszavának törlése|
|Alapvető könyvtár|Címtárkezelés|Címtár|Vállalati beállítások törlése|
|Alapvető könyvtár|Erőforrás|Erőforrás|Eszköz törlése|
|Alapvető könyvtár|Erőforrás|Erőforrás|Eszköz konfigurációjának törlése|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoport törlése|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoportbeállítások törlése|
|Alapvető könyvtár|Szabályzatkezelés|Szabályzat|Szabályzat törlése|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználó törlése|
|Alapvető könyvtár|Címtárkezelés|Címtár|Partner lefokozása|
|Alapvető könyvtár|Erőforrás|Erőforrás|Az eszköz már nem megfelelő|
|Alapvető könyvtár|Erőforrás|Erőforrás|Az eszköz már nincs felügyelve|
|Alapvető könyvtár|Címtárkezelés|Címtár|Címtár törölve|
|Alapvető könyvtár|Címtárkezelés|Címtár|Címtár véglegesen törölve|
|Alapvető könyvtár|Címtárkezelés|Címtár|Ez a címtár törlésre van ütemezve|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Fiók letiltása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Erős hitelesítés engedélyezése|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoportalapú licenc felhasználókra való alkalmazásának befejezése|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Alkalmazás végleges törlése|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoport végleges törlése|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználó végleges törlése|
|Alapvető könyvtár|Címtárkezelés|Címtár|Vállalat előléptetése partnerré|
|Alapvető könyvtár|Címtárkezelés|Címtár|Rights Management-tulajdonságok végleges törlése|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Az Oauth2Permissiongrant eltávolítása|
|Alapvető könyvtár|Csoportkezelés|Csoport|Alkalmazás szerepkör-hozzárendelésének eltávolítása a csoportból|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Alkalmazásszerepkör-hozzárendelés eltávolítása a szolgáltatásnévből|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Alkalmazásszerepkör-hozzárendelés eltávolítása a felhasználóból|
|Alapvető könyvtár|Szerepkörkezelés|Szerepkör|Jogosult tag eltávolítása a szerepkörből|
|Alapvető könyvtár|Adminisztratív egységek kezelése|AdministrativeUnit|Tag eltávolítása az adminisztratív egységből|
|Alapvető könyvtár|Csoportkezelés|Csoport|Tag eltávolítása a csoportból|
|Alapvető könyvtár|Szerepkörkezelés|Szerepkör|Tag eltávolítása szerepkörből|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Tulajdonos eltávolítása az alkalmazásból|
|Alapvető könyvtár|Csoportkezelés|Csoport|Tulajdonos eltávolítása a csoportból|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Tulajdonos eltávolítása az egyszerű szolgáltatásnévből|
|Alapvető könyvtár|Címtárkezelés|Címtár|Partner eltávolítása a vállalatból|
|Alapvető könyvtár|Szabályzatkezelés|Szabályzat|Szabályzat hitelesítő adatainak eltávolítása|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Szabályzat eltávolítása az egyszerű szolgáltatásnévből|
|Alapvető könyvtár|Erőforrás|Erőforrás|Regisztrált felhasználó eltávolítása az eszközről|
|Alapvető könyvtár|Erőforrás|Erőforrás|Regisztrált felhasználók eltávolítása az eszközről|
|Alapvető könyvtár|Szerepkörkezelés|Szerepkör|Szerepkör-hozzárendelés eltávolítása a szerepkör-definícióból|
|Alapvető könyvtár|Szerepkörkezelés|Szerepkör|Hatókörön belüli tag eltávolítása a szerepkörből|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Szolgáltatásnév eltávolítása|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Egyszerű szolgáltatásnév hitelesítő adatainak eltávolítása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Nem ellenőrzött tartomány eltávolítása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználók erős hitelesítési telefonos alkalmazásra vonatkozó információinak eltávolítása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Ellenőrzött tartomány eltávolítása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Új felhasználói jelszó kérése|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoport visszaállítása|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Alkalmazás visszaállítása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználó visszaállítása|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Jóváhagyás visszavonása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Cégadatok beállítása|
|Alapvető könyvtár|Címtárkezelés|Címtár|A DirSync funkció beállítása|
|Alapvető könyvtár|Címtárkezelés|Címtár|A DirSyncEnabled jelölő beállítása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Partnerség beállítása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Véletlen törlés küszöbértékének beállítása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Vállalati adatok engedélyezett helyének beállítása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Vállalati nemzetközi szolgáltatás engedélyezésének beállítása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Címtárszolgáltatás beállítása a bérlőn|
|Alapvető könyvtár|Címtárkezelés|Címtár|Tartomány hitelesítésének beállítása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Tartomány összevonási beállításainak beállítása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználói jelszó kényszerített módosításának beállítása|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoportlicenc beállítása|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoport beállítása felhasználó által kezeltként|
|Alapvető könyvtár|Címtárkezelés|Címtár|Jelszószabályzat beállítása|
|Alapvető könyvtár|Címtárkezelés|Címtár|Rights Management tulajdonságainak beállítása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználókezelő beállítása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|A felhasználók oath token metaadatainak engedélyezettre állítása|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoportalapú licenc felhasználókra való alkalmazásának megkezdése|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoportlicenc újraszámolásának aktiválása|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|StsRefreshTokenValidFrom időbélyeg frissítése|
|Alapvető könyvtár|Adminisztratív egységek kezelése|AdministrativeUnit|Adminisztratív egység frissítése|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Az alkalmazás frissítése|
|Alapvető könyvtár|Címtárkezelés|Címtár|Vállalat frissítése|
|Alapvető könyvtár|Címtárkezelés|Címtár|Vállalati beállítások frissítése|
|Alapvető könyvtár|Erőforrás|Erőforrás|Eszköz frissítése|
|Alapvető könyvtár|Erőforrás|Erőforrás|Eszköz konfigurációjának frissítése|
|Alapvető könyvtár|Címtárkezelés|Címtár|Frissítési tartomány|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Külső titkos kódok frissítése|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Külső titkos kódok frissítése|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoport frissítése|
|Alapvető könyvtár|Csoportkezelés|Csoport|Csoportbeállítások frissítése|
|Alapvető könyvtár|Szabályzatkezelés|Szabályzat|Szabályzat frissítése|
|Alapvető könyvtár|Szerepkörkezelés|Szerepkör|Szerepkör frissítése|
|Alapvető könyvtár|Alkalmazáskezelés|Alkalmazás|Szolgáltatásnév frissítése|
|Alapvető könyvtár|Felhasználókezelés|Felhasználó|Felhasználó frissítése|
|Alapvető könyvtár|Címtárkezelés|Címtár|Tartomány ellenőrzése|
|Alapvető könyvtár|Címtárkezelés|Címtár|E-mailek ellenőrzött tartományának ellenőrzése|
|Identity Protection|Felhasználókezelés|Felhasználó|A rendszergazda ideiglenes jelszót hoz létre|
|Identity Protection|Felhasználókezelés|Felhasználó|A rendszergazda kötelezővé teszi a felhasználó számára az új jelszó kérését|
|Identity Protection|Egyéb|Egyéb|Egy kockázatiesemény-típus letöltése|
|Identity Protection|Egyéb|Egyéb|Rendszergazdák és a heti összefoglalók letöltésének jóváhagyása|
|Identity Protection|Egyéb|Egyéb|Összes kockázatiesemény-típus letöltése|
|Identity Protection|Egyéb|Egyéb|Ingyenes felhasználói kockázati események letöltése|
|Identity Protection|Egyéb|Egyéb|Kockázatosként megjelölt felhasználók letöltése|
|Identity Protection|Címtárkezelés|Címtár|Előkészítés|
|Identity Protection|Szabályzatkezelés|Szabályzat|Az MFA regisztrációs szabályzatának beállítása|
|Identity Protection|Szabályzatkezelés|Szabályzat|Bejelentkezési kockázati szabályzat beállítása|
|Identity Protection|Szabályzatkezelés|Szabályzat|Felhasználói kockázati szabályzat beállítása|
|Identity Protection|Címtárkezelés|Címtár|Riasztási beállítások frissítése|
|Identity Protection|Címtárkezelés|Címtár|Heti összefoglaló beállításainak frissítése|
|Meghívott felhasználók|Felhasználókezelés|Felhasználó|Külső felhasználó hozzárendelése alkalmazáshoz|
|Meghívott felhasználók|Egyéb|Egyéb|Feldolgozott kötegelt meghívók|
|Meghívott felhasználók|Egyéb|Egyéb|Kötegelt meghívók feltöltve|
|Meghívott felhasználók|Felhasználókezelés|Felhasználó|E-mail nincs elküldve, a felhasználó leiratkozott|
|Meghívott felhasználók|Felhasználókezelés|Felhasználó|Külső felhasználó meghívása|
|Meghívott felhasználók|Felhasználókezelés|Felhasználó|Külső felhasználó meghívásának beváltása|
|Meghívott felhasználók|Felhasználókezelés|Felhasználó|Bérlő ugrásszerű létrehozása|
|Meghívott felhasználók|Felhasználókezelés|Felhasználó|Felhasználó ugrásszerű létrehozása|
|Microsoft Identity Manager (MIM)|Csoportkezelés|Csoport|Tag hozzáadása|
|Microsoft Identity Manager (MIM)|Csoportkezelés|Csoport|Csoport létrehozása|
|Microsoft Identity Manager (MIM)|Csoportkezelés|Csoport|Csoport törlése|
|Microsoft Identity Manager (MIM)|Csoportkezelés|Csoport|Tag eltávolítása|
|Microsoft Identity Manager (MIM)|Csoportkezelés|Csoport|Csoport frissítése|
|Microsoft Identity Manager (MIM)|Felhasználókezelés|Felhasználó|Felhasználói jelszó regisztrációja|
|Microsoft Identity Manager (MIM)|Felhasználókezelés|Felhasználó|Új felhasználói jelszó kérése|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|AccessReview_Review|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|AccessReview_Update|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|ActivationAborted|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|ActivationApproved|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|ActivationCanceled|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|ActivationRequested|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|Hozzáadva|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|Hozzárendelés|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|Jogosultságszint emelése|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|Eltávolítva|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|Szerepkör-beállítások módosításai|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|ScanAlertsNow|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|Regisztráció|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|Jogosultságszint csökkentése|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|UpdateAlertSettings|
|Privileged Identity Management|Szerepkörkezelés|Szerepkör|UpdateCurrentState|
|Önkiszolgáló csoportkezelés|Csoportkezelés|Csoport|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem jóváhagyása|
|Önkiszolgáló csoportkezelés|Csoportkezelés|Csoport|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem visszavonása|
|Önkiszolgáló csoportkezelés|Csoportkezelés|Csoport|Életciklus-kezelési szabályzat létrehozása|
|Önkiszolgáló csoportkezelés|Csoportkezelés|Csoport|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem törlése|
|Önkiszolgáló csoportkezelés|Csoportkezelés|Csoport|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem elutasítása|
|Önkiszolgáló csoportkezelés|Csoportkezelés|Csoport|Csoport megújítása|
|Önkiszolgáló csoportkezelés|Csoportkezelés|Csoport|Csoporttagság kérése|
|Önkiszolgáló csoportkezelés|Csoportkezelés|Csoport|Dinamikus csoport tulajdonságainak beállítása|
|Önkiszolgáló csoportkezelés|Csoportkezelés|Csoport|Életciklus-kezelési szabályzat frissítése|
|Önkiszolgáló jelszókezelés|Felhasználókezelés|Felhasználó|Új jelszó önkiszolgáló kérésének blokkolása|
|Önkiszolgáló jelszókezelés|Felhasználókezelés|Felhasználó|Jelszó módosítása (önkiszolgáló)|
|Önkiszolgáló jelszókezelés|Címtárkezelés|Címtár|Jelszóvisszaíró letiltása a címtárhoz|
|Önkiszolgáló jelszókezelés|Címtárkezelés|Címtár|Jelszóvisszaíró engedélyezése a címtárhoz|
|Önkiszolgáló jelszókezelés|Felhasználókezelés|Felhasználó|Jelszó átállítása (a rendszergazda által)|
|Önkiszolgáló jelszókezelés|Felhasználókezelés|Felhasználó|Új jelszó kérése (önkiszolgáló)|
|Önkiszolgáló jelszókezelés|Felhasználókezelés|Felhasználó|Új jelszó önkiszolgáló kérésének tevékenységállapota|
|Önkiszolgáló jelszókezelés|Felhasználókezelés|Felhasználó|Új jelszó önkiszolgáló kérésének tevékenységállapota|
|Önkiszolgáló jelszókezelés|Felhasználókezelés|Felhasználó|Felhasználói fiók zárolásának feloldása (önkiszolgáló)|
|Önkiszolgáló jelszókezelés|Felhasználókezelés|Felhasználó|A felhasználó új jelszó önkiszolgáló kérésére regisztrált|
|Használati feltételek|Szabályzatkezelés|Szabályzat|Használati feltételek elfogadása|
|Használati feltételek|Szabályzatkezelés|Szabályzat|Használati feltételek létrehozása|
|Használati feltételek|Szabályzatkezelés|Szabályzat|Használati feltételek elutasítása|
|Használati feltételek|Szabályzatkezelés|Szabályzat|Használati feltételek törlése|
|Használati feltételek|Szabályzatkezelés|Szabályzat|Használati feltételek szerkesztése|
|Használati feltételek|Szabályzatkezelés|Szabályzat|Használati feltételek közzététele|
|Használati feltételek|Szabályzatkezelés|Szabályzat|Használati feltételek közzétételének visszavonása|




## <a name="next-steps"></a>További lépések

A jelentéskészítés áttekintéséért lásd: [Jelentéskészítés az Azure Active Directoryban](active-directory-reporting-azure-portal.md).

