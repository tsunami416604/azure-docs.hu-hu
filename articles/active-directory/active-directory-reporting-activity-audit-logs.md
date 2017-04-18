---
title: "Naplózási tevékenységre vonatkozó jelentések az Azure Active Directory portál előzetes kiadásában | Microsoft Docs"
description: "Naplózási tevékenységre vonatkozó jelentések az Azure Active Directory portál előzetes kiadásában – bevezetés"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: aef5bce6f440f4a0a57763f915d307297f50281b
ms.lasthandoff: 04/10/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal---preview"></a>Naplózási tevékenységre vonatkozó jelentések az Azure Active Directory portál előzetes kiadásában

Az Azure Active Directory [előzetes kiadásában](active-directory-preview-explainer.md) elérhető jelentéskészítéssel minden szükséges információhoz hozzájuthat a környezetével kapcsolatban.

Az Azure Active Directory jelentéskészítési architektúrája a következő elemekből áll:

- **Tevékenység** 
    - **Bejelentkezési tevékenységek** – A felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk
    - **Naplók** – Rendszertevékenység információk a felhasználó- és csoportfelügyeletre, valamint a felügyelt alkalmazásokra és a címtártevékenységekre vonatkozóan.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. További részletek: Kockázatos bejelentkezések.
    - **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. További részletek: Kockázatosként megjelölt felhasználók.

Ezen témakör áttekintést nyújt a naplózási tevékenységekről.
 


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
- Kezdeményező
- Kategória
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

A **kezdeményező** szűrő lehetővé teszi egy szereplő nevének vagy egyszerű felhasználónevének (UPN) megadását.

A **kategória** szűrővel az alábbi szűrők egyikét választhatja ki:

- Összes
- Alapvető kategória
- Alapvető könyvtár
- Önkiszolgáló jelszókezelés
- Önkiszolgáló csoportkezelés
- Fiók kiépítése
- Automatizált jelszóváltás
- meghívott felhasználók
- MIM szolgáltatás

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

| Tevékenység kategóriája| Tevékenység erőforrástípusa| Tevékenység |
| :-- | :-: | :-- |
| Alapvető könyvtár| Csoport| Csoportbeállítások törlése|
| Alapvető könyvtár| Címtár| Tartomány frissítése|
| Alapvető könyvtár| Címtár| Partner eltávolítása a vállalatból|
| Alapvető könyvtár| Felhasználó| Szerepkör frissítése|
| Alapvető könyvtár| Felhasználó| Szerepkör hozzáadása sablonból|
| Alapvető könyvtár| Csoport| Alkalmazás szerepkör-kijelölésének hozzáadása csoporthoz|
| Alapvető könyvtár| Csoport| Csoport alapú licenc alkalmazásának megkezdése felhasználókra|
| Alapvető könyvtár| Alkalmazás| Egyszerű szolgáltatásnév hozzáadása|
| Alapvető könyvtár| Szabályzat| Szabályzat frissítése|
| Alapvető könyvtár| Szabályzat| Szabályzat hozzáadása szolgáltatásnévhez|
| Alapvető könyvtár| Eszköz| Regisztrált felhasználó hozzáadása eszközhöz|
| Alapvető könyvtár| Eszköz| Regisztrált felhasználók hozzáadása eszközhöz|
| Alapvető könyvtár| Eszköz| Eszköz konfigurációjának frissítése|
| Önkiszolgáló jelszókezelés| Felhasználó| Új jelszó kérése (önkiszolgáló)|
| Önkiszolgáló jelszókezelés| Felhasználó| Felhasználói fiók feloldása (önkiszolgáló)|
| Önkiszolgáló jelszókezelés| Felhasználó| Jelszó átállítása (a rendszergazda által)|
| Önkiszolgáló csoportkezelés| Csoport| Függőben lévő kérelem törlése egy csoporthoz való csatlakoztatáshoz|
| Fiók kiépítése| Alkalmazás| Folyamatletét|
| Automatizált jelszóváltás| Alkalmazás| Automatizált jelszóváltás|
| Meghívott felhasználók| Egyéb| Feldolgozott kötegelt meghívók|
| Alapvető könyvtár| Címtár| Ellenőrzött tartomány eltávolítása|
| Alapvető könyvtár| Címtár| Nem ellenőrzött tartomány hozzáadása|
| Alapvető könyvtár| Címtár| Ellenőrzött tartomány hozzáadása|
| Alapvető könyvtár| Címtár| Címtár szolgáltatás beállítása a bérlőn|
| Alapvető könyvtár| Címtár| Dirsyncenabled jelölő beállítása|
| Alapvető könyvtár| Címtár| Vállalati beállítások létrehozása|
| Alapvető könyvtár| Címtár| Vállalati beállítások frissítése|
| Alapvető könyvtár| Címtár| Vállalati beállítások törlése|
| Alapvető könyvtár| Címtár| Vállalati adatok engedélyezett helyének beállítása|
| Alapvető könyvtár| Címtár| Vállalati nemzetközi szolgáltatás engedélyezésének beállítása|
| Alapvető könyvtár| Felhasználó| Felhasználó frissítése|
| Alapvető könyvtár| Felhasználó| Felhasználó törlése|
| Alapvető könyvtár| Csoport| Tag eltávolítása a csoportból|
| Alapvető könyvtár| Csoport| Csoportlicenc beállítása|
| Alapvető könyvtár| Csoport| Csoportbeállítások létrehozása|
| Alapvető könyvtár| Alkalmazás| Szolgáltatásnév frissítése|
| Alapvető könyvtár| Alkalmazás| Alkalmazás törlése|
| Alapvető könyvtár| Alkalmazás| Alkalmazás frissítése|
| Alapvető könyvtár| Alkalmazás| Szolgáltatásnév eltávolítása|
| Alapvető könyvtár| Alkalmazás| Egyszerű szolgáltatásnév hitelesítő adatainak hozzáadása|
| Alapvető könyvtár| Alkalmazás| Alkalmazás szerepkör-kijelölésének eltávolítása a szolgáltatásnévből|
| Alapvető könyvtár| Alkalmazás| Tulajdonos eltávolítása az alkalmazásból|
| Alapvető könyvtár| Eszköz| Regisztrált felhasználó eltávolítása az eszközről|
| Önkiszolgáló jelszókezelés| Felhasználó| Önkiszolgáló jelszókérés folyamatállapota|
| Fiók kiépítése| Alkalmazás| Adminisztráció|
| Fiók kiépítése| Alkalmazás| Címtárművelet|
| MIM szolgáltatás| Csoport| Tag eltávolítása|
| Alapvető könyvtár| Szabályzat| Szabályzat törlése|
| Meghívott felhasználók| Felhasználó| Bérlő ugrásszerű létrehozása|
| Alapvető könyvtár| Címtár| Külső titkos kódok frissítése|
| Alapvető könyvtár| Címtár| Rights Management tulajdonságainak beállítása|
| Alapvető könyvtár| Címtár| Vállalat frissítése|
| Alapvető könyvtár| Felhasználó| Felhasználó hozzáadása|
| Alapvető könyvtár| Felhasználó| Összevont felhasználó konvertálása felügyeltre|
| Alapvető könyvtár| Felhasználó| Alkalmazásjelszó létrehozása a felhasználó számára|
| Alapvető könyvtár| Csoport| Tag hozzáadása csoporthoz|
| Alapvető könyvtár| Csoport| Csoport hozzáadása|
| Alapvető könyvtár| Alkalmazás| Hozzájárulás az alkalmazáshoz|
| Alapvető könyvtár| Alkalmazás| Alkalmazás hozzáadása|
| Alapvető könyvtár| Alkalmazás| Tulajdonos hozzáadása szolgáltatásnévhez|
| Alapvető könyvtár| Alkalmazás| Az Oauth2Permissiongrant eltávolítása|
| Alapvető könyvtár| Szabályzat| Szabályzat hitelesítő adatainak eltávolítása|
| Alapvető könyvtár| Eszköz| Eszköz konfigurációjának törlése|
| Önkiszolgáló csoportkezelés| Csoport| Dinamikus csoport tulajdonságainak beállítása|
| Önkiszolgáló csoportkezelés| Csoport| Életciklus-kezelési szabályzat frissítése|
| Fiók kiépítése| Alkalmazás| Szinkronizálási szabályművelet|
| Meghívott felhasználók| Egyéb| Meghívók kötegelt feltöltése|
| MIM szolgáltatás| Csoport| Tag hozzáadása|
| Alapvető könyvtár| Felhasználó| Licenc tulajdonságainak beállítása|
| Alapvető könyvtár| Felhasználó| Felhasználó visszaállítása|
| Alapvető könyvtár| Felhasználó| Tag eltávolítása szerepkörből|
| Alapvető könyvtár| Felhasználó| Alkalmazás szerepkör-kijelölésének eltávolítása a felhasználóból|
| Alapvető könyvtár| Felhasználó| Hatókört használó tag eltávolítása a szerepkörből|
| Alapvető könyvtár| Csoport| Csoport frissítése|
| Alapvető könyvtár| Csoport| Tulajdonos hozzáadása csoporthoz|
| Alapvető könyvtár| Csoport| Csoport alapú licenc alkalmazásának befejezése felhasználókra|
| Alapvető könyvtár| Csoport| Alkalmazás szerepkör-kijelölésének eltávolítása a csoportból|
| Alapvető könyvtár| Csoport| Csoport beállítása felhasználó által kezeltként|
| Alapvető könyvtár| Alkalmazás| Oauth2Permissiongrant hozzáadása|
| Alapvető könyvtár| Alkalmazás| Alkalmazás szerepkör-kijelölésének hozzáadása a szolgáltatásnévhez|
| Alapvető könyvtár| Alkalmazás| Egyszerű szolgáltatásnév hitelesítő adatainak eltávolítása|
| Alapvető könyvtár| Szabályzat| Szabályzat eltávolítása az egyszerű szolgáltatásnévből|
| Alapvető könyvtár| Eszköz| Eszköz frissítése|
| Alapvető könyvtár| Eszköz| Eszköz hozzáadása|
| Alapvető könyvtár| Eszköz| Eszközkonfiguráció hozzáadása|
| Önkiszolgáló jelszókezelés| Felhasználó| Jelszó módosítása (önkiszolgáló)|
| Önkiszolgáló jelszókezelés| Felhasználó| A felhasználó regisztrált új jelszó önkiszolgáló kéréséhez|
| Önkiszolgáló csoportkezelés| Csoport| Függőben lévő kérelem jóváhagyása egy csoporthoz való csatlakoztatáshoz|
| Alapvető könyvtár| Címtár| Nem ellenőrzött tartomány eltávolítása|
| Alapvető könyvtár| Címtár| Tartomány ellenőrzése|
| Alapvető könyvtár| Címtár| Tartomány hitelesítésének beállítása|
| Alapvető könyvtár| Címtár| Jelszószabályzat beállítása|
| Alapvető könyvtár| Címtár| Partner hozzáadása vállalathoz|
| Alapvető könyvtár| Címtár| Vállalat előléptetése partnerré|
| Alapvető könyvtár| Címtár| Partnerség beállítása|
| Alapvető könyvtár| Címtár| Véletlen törlés küszöbértékének beállítása|
| Alapvető könyvtár| Címtár| Partner lefokozása|
| Meghívott felhasználók| Felhasználó| Külső felhasználó meghívása|
| Fiók kiépítése| Alkalmazás| Importálás|
| Alapvető könyvtár| Alkalmazás| Tulajdonos eltávolítása az egyszerű szolgáltatásnévből|
| Alapvető könyvtár| Eszköz| Regisztrált felhasználók eltávolítása az eszközről|
| Alapvető könyvtár| Címtár| Cégadatok beállítása|
| Alapvető könyvtár| Címtár| Tartomány összevonási beállításainak beállítása|
| Alapvető könyvtár| Címtár| Vállalat létrehozása|
| Alapvető könyvtár| Címtár| Rights Management tulajdonságainak végleges törlése|
| Alapvető könyvtár| Címtár| A Dirsync szolgáltatás beállítása|
| Alapvető könyvtár| Címtár| E-mailek ellenőrzött tartományának ellenőrzése|
| Alapvető könyvtár| Felhasználó| Felhasználói licenc váltása|
| Alapvető könyvtár| Felhasználó| Felhasználói jelszó váltása|
| Alapvető könyvtár| Felhasználó| Új felhasználói jelszó kérése|
| Alapvető könyvtár| Felhasználó| Alkalmazás szerepkör-kijelölésének megadása a felhasználónak|
| Alapvető könyvtár| Felhasználó| Tag hozzáadása szerepkörhöz|
| Alapvető könyvtár| Felhasználó| Alkalmazásjelszó törlése a felhasználóhoz|
| Alapvető könyvtár| Felhasználó| A felhasználó hitelesítő adatainak frissítése|
| Alapvető könyvtár| Felhasználó| Felhasználókezelő beállítása|
| Alapvető könyvtár| Felhasználó| Szerepkör-hatókörön belüli tag hozzáadása|
| Alapvető könyvtár| Csoport| Csoport törlése|
| Alapvető könyvtár| Csoport| Tulajdonos eltávolítása a csoportból|
| Alapvető könyvtár| Csoport| Csoportbeállítások frissítése|
| Alapvető könyvtár| Alkalmazás| Tulajdonosa hozzá az alkalmazáshoz|
| Alapvető könyvtár| Alkalmazás| Jóváhagyás visszavonása|
| Alapvető könyvtár| Szabályzat| Szabályzat hozzáadása|
| Alapvető könyvtár| Eszköz| Eszköz törlése|
| Önkiszolgáló jelszókezelés| Felhasználó| Új jelszó önkiszolgáló kérésének blokkolása|
| Önkiszolgáló csoportkezelés| Csoport| Csoporttagság kérése|
| Önkiszolgáló csoportkezelés| Csoport| Életciklus-kezelési szabályzat létrehozása|
| Önkiszolgáló csoportkezelés| Csoport| Függőben lévő kérelem elutasítása egy csoporthoz való csatlakoztatáshoz|
| Önkiszolgáló csoportkezelés| Csoport| Függőben lévő kérelem megszakítása egy csoporthoz való csatlakoztatáshoz|
| Önkiszolgáló csoportkezelés| Csoport| Csoport megújítása|
| Fiók kiépítése| Alkalmazás| Exportálás|
| Fiók kiépítése| Alkalmazás| Egyéb|
| Meghívott felhasználók| Felhasználó| Külső felhasználó meghívásának beváltása|
| Meghívott felhasználók| Felhasználó| Felhasználó ugrásszerű létrehozása|
| Meghívott felhasználók| Felhasználó| Külső felhasználó hozzárendelése alkalmazáshoz|




## <a name="audit-logs-shortcuts"></a>Rövidebb utak a naplók eléréséhez

Az **Azure Active Directory** mellett az Azure Portal két további lehetőséget biztosít a naplózási adatok elérésére:

- Felhasználók és csoportok
- Vállalati alkalmazások

A naplózási jelentési tevékenységek teljes listáját [a naplózási jelentési események listájában](active-directory-reporting-audit-events.md#list-of-audit-report-events) tekintheti meg.


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

Ha csak át szeretné tekinteni az alkalmazásaihoz kapcsolódó naplózási adatokat, megnyithat egy szűrt nézetet az **Auditnaplók** menüpontból, amely a **Vállalati alkalmazások** panel **Tevékenység** szakaszában található. Ennél a lehetőségnél az **Alkalmazás** van előre kiválasztva **tevékenység-erőforrástípusként**.

![Naplók](./media/active-directory-reporting-activity-audit-logs/134.png "Naplók")

A nézetet tovább szűrheti csak a **csoportok** vagy csak a **felhasználók** megjelenítéséhez.

![Naplók](./media/active-directory-reporting-activity-audit-logs/25.png "Naplók")


## <a name="next-steps"></a>Következő lépések
Lásd az [Azure Active Directory Reporting-útmutatót](active-directory-reporting-guide.md).


