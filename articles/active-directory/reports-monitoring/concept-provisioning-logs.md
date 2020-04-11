---
title: Naplók kiépítése az Azure Active Directory portálon (előzetes verzió) | Microsoft dokumentumok
description: A tevékenységjelentések kiépítése az Azure Active Directory portálon
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30cc8be6ad9ebffcad58c5b2412ae15ff3f26fa5
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113320"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Jelentések kiépítése az Azure Active Directory portálon (előzetes verzió)

Az Azure Active Directory (Azure AD) jelentéskészítési architektúrája a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – A felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatával kapcsolatos információk.
    - **Naplók Naplónaplók**rendszertevékenységi információkat nyújtanak a felhasználókról és a csoportkezelésről, a felügyelt alkalmazásokról és a címtártevékenységekről.[Audit logs](concept-audit-logs.md)  - 
    - **Kiépítési naplók** – Az Azure AD-létesítési szolgáltatás által kiépített felhasználói, csoportok és szerepkörök rendszertevékenység biztosítása. 

- **Biztonság** 
    - **Kockázatos bejelentkezések** – A [kockázatos bejelentkezés](concept-risky-sign-ins.md) egy olyan bejelentkezési kísérlet jelzője, amelyet olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.
    - **A kockázatra megjelölt felhasználók** – A [kockázatos felhasználó](concept-user-at-risk.md) egy olyan felhasználói fiók jelzője, amely veszélybe kerülhetett.

Ez a témakör áttekintést nyújt a kiépítési jelentésről.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
* A Biztonsági rendszergazda, a Biztonsági olvasó, a Jelentésolvasó, az Alkalmazás- és a Felhőalkalmazás-rendszergazda szerepkör felhasználói
* Globális rendszergazdák


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Milyen Azure AD-licencre van szüksége a kiépítési tevékenységek eléréséhez?

A bérlőnek rendelkeznie kell egy Azure AD Premium-licenccel, hogy láthassa az összes kiépítési tevékenységjelentést. Az [Azure Active Directory-kiadás](../fundamentals/active-directory-get-started-premium.md) frissítéséhez olvassa el az Azure Active Directory Premium szolgáltatás első lépéseit. 

## <a name="provisioning-logs"></a>Üzembehelyezési naplók

A létesítési naplók választ adnak a következő kérdésekre:

* Milyen csoportok at sikerült létrehozni a ServiceNow?
* Hogyan importálták a szerepköröket az Amazon Web Services szolgáltatásból?
* Milyen felhasználókat sikerült létrehozni a DropBox?What users were successfully created in DropBox?

A kiépítési naplók eléréséhez válassza **kiépítési naplók** az **Azure Active Directory** panel az [Azure Portalon](https://portal.azure.com)a **figyelési** szakaszban. Néhány kiépítési rekord megjelenítése akár két órát is igénybe vehet.

![Üzembehelyezési naplók](./media/concept-provisioning-logs/access-provisioning-logs.png "Üzembehelyezési naplók")


A létesítési napló alapértelmezett listanézete a következőket jeleníti meg:

- Az identitás
- Az intézkedés
- A forrásrendszer
- A célrendszer
- Az állapot
- A dátum


![Alapértelmezett oszlopok](./media/concept-provisioning-logs/default-columns.png "Alapértelmezett oszlopok")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Oszlopválasztó](./media/concept-provisioning-logs/column-chooser.png "Oszlopválasztó")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Elérhető oszlopok](./media/concept-provisioning-logs/available-columns.png "Elérhető oszlopok")

A részletesebb információk megtekintéséhez jelöljön ki egy elemet a listanézetben.

![Részletes információk](./media/concept-provisioning-logs/steps.png "Szűrés")


## <a name="filter-provisioning-activities"></a>Kiépítési tevékenységek szűrése

Szűrheti a létesítési adatokat. Egyes szűrőértékek dinamikusan vannak feltöltve a bérlő alapján. Ha például nincs enek létrehozási esemény a bérlőben, nem lesz szűrési lehetőség a létrehozáshoz.
Az alapértelmezett nézetben a következő szűrőket választhatja ki:

- Identitás
- Dátum
- status
- Műveletek


![Szűrő](./media/concept-provisioning-logs/default-filter.png "Szűrés")

Az **identitásszűrő** lehetővé teszi a fontos név vagy identitás megadását. Ez az identitás lehet felhasználó, csoport, szerepkör vagy más objektum. Az objektum neve vagy azonosítója alapján kereshet. Az azonosító forgatókönyvtől függően változik. Például amikor egy objektumot létesít az Azure AD-től a SalesForce-hoz, a forrásazonosító a felhasználó objektumazonosítója az Azure AD-ben, míg a targetid a salesforce-i felhasználó azonosítója. A Workday szolgáltatásból az Active Directoryba történő kiépítés során a forrásazonosító a Workday dolgozói alkalmazottazonosítója. Vegye figyelembe, hogy a felhasználó neve nem mindig jelen iktatható meg az Identitás oszlopban. Mindig lesz egy azonosító. 


A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek:

- 1 hónap
- 7 nap
- 30 nap
- 24 óra
- Egyéni időintervallum

Egyéni időkeret kiválasztásakor beállíthatja a kezdési és a záró dátumot.


Az **Állapot** szűrő lehetővé teszi a következők kiválasztását:

- Összes
- Sikeres
- Hiba
- Kimarad



A **Művelet** szűrő lehetővé teszi a következők szűrését:

- Létrehozás 
- Frissítés
- Törlés
- Letiltás
- Egyéb

Az alapértelmezett nézet szűrőihez a következő szűrőket is beállíthatja:

- Feladat azonosítója
- Ciklus azonosítója
- Módosítási azonosító
- Forrásazonosító
- Célazonosító
- Alkalmazás


![Mező kivétele](./media/concept-provisioning-logs/add-filter.png "Mező kivétele")


- **Feladatazonosító** – egy egyedi feladatazonosító van társítva minden olyan alkalmazáshoz, amelyhez engedélyezte a kiépítést.   

- **Ciklusazonosító** – egyedileg azonosítja a kiépítési ciklust. Megoszthatja ezt az azonosítót, hogy támogassa a ciklus, amelyben ez az esemény történt.

- **Módosításazonosító** – a létesítési esemény egyedi azonosítója. Megoszthatja ezt az azonosítót a kiépítési esemény kivizsgálási támogatásához.   


- **Forrásrendszer** – Lehetővé teszi, hogy megadja, honnan történik az identitás. Például egy objektum kiépítése az Azure AD servicenow, a forrásrendszer az Azure AD. 

- **Célrendszer** – Lehetővé teszi, hogy megadja, hogy az identitás hol van kiépítve. Például egy objektum kiépítése az Azure AD servicenow, a célrendszer ServiceNow. 

- **Alkalmazás** – Lehetővé teszi, hogy csak egy adott karakterláncot tartalmazó megjelenítendő névvel rendelkező alkalmazások rekordjait jelenítse meg.

 

## <a name="provisioning-details"></a>Kiépítés részletei 

Amikor kijelöl egy elemet a kiépítési lista nézetben, további részleteket kaphat erről az elemről.
A részletek a következő kategóriák alapján vannak csoportosítva:

- Lépések

- Hibaelhárítás és javaslatok

- Módosított tulajdonságok

- Összefoglalás


![Szűrő](./media/concept-provisioning-logs/provisioning-tabs.png "Lapok")



### <a name="steps"></a>Lépések

A **Lépések** lap ismerteti az objektum kiépítéséhez szükséges lépéseket. Egy objektum kiépítése négy lépésből állhat: 

- Objektum importálása
- Annak megállapítása, hogy az objektum hatóköre van-e
- Objektum egyeztetése a forrás és a cél között
- Provision objektum (művelet - ez lehet egy létrehozás, frissítés, törlés vagy letiltás)



![Szűrő](./media/concept-provisioning-logs/steps.png "Szűrés")


### <a name="troubleshoot-and-recommendations"></a>Hibaelhárítás és javaslatok


A **hibaelhárítás és a javaslatok** lapon található a hibakód és az ok. A hibainformáció csak hiba esetén érhető el. 


### <a name="modified-properties"></a>Módosított tulajdonságok

A **módosított tulajdonságok** a régi és az új értéket jelenítik meg. Azokban az esetekben, ahol nincs régi érték, a régi érték oszlop üres. 


### <a name="summary"></a>Összefoglalás

Az **összegzés** lap áttekintést nyújt a történtekről és az objektum azonosítóiról a forrás- és célrendszerben. 

## <a name="what-you-should-know"></a>Alapismeretek

- Az Azure Portal tárolja jelentett kiépítési adatok 30 napig, ha van egy prémium kiadás, és 7 nap, ha van egy ingyenes kiadás..

- A Változásazonosító attribútumot egyedi azonosítóként használhatja. Ez például akkor hasznos, ha a terméktámogatással kommunikál.

- Jelenleg nincs lehetőség a kiépítési adatok letöltésére.

- Jelenleg nincs támogatás a log analytics.

- Amikor egy alkalmazás környezetéből éri el a létesítési naplókat, az nem szűri automatikusan az eseményeket az adott alkalmazásra a naplónaplók hozadékához.

## <a name="error-codes"></a>Hibakódok

Az alábbi táblázat segítségével jobban megismerheti, hogyan oldhatja meg a kiépítési naplókban található hibákat. A hiányzó hibakódok esetén adjon visszajelzést az oldal alján található hivatkozás sal. 

|Hibakód|Leírás|
|---|---|
|Ütközés, EntryConflict|Javítsa ki az ütköző attribútumértékeket az Azure AD-ben vagy az alkalmazásban, vagy tekintse át az egyező attribútumkonfigurációt, ha az ütköző felhasználói fiók nak egyeztetésre és átvettnek kell lennie. Az egyező attribútumok konfigurálásával kapcsolatos további információkért tekintse át az alábbi [dokumentációt.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)|
|TooManyKéri|A célalkalmazás elutasította a felhasználó frissítésére irányuló kísérletet, mert túl terhelt, és túl sok kérést fogad. Nincs mit tenni. Ez a kísérlet automatikusan megszűnik. A Microsoft is értesítést kapott a problémáról.|
|InternalServerError |A célalkalmazás váratlan hibát adott vissza. Előfordulhat, hogy a célalkalmazással olyan szolgáltatásprobléma van, amely megakadályozza ennek működését. Ez a kísérlet 40 perc múlva automatikusan megszűnik.|
|InsufficientRights, MethodNotAllowed, NotAlloweded, Unauthorized| Az Azure AD képes volt hitelesíteni a célalkalmazással, de nem volt jogosult a frissítés végrehajtására. Kérjük, tekintse át a célalkalmazás által biztosított utasításokat, valamint a megfelelő [alkalmazásbemutatót.](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)|
|Feldolgozhatatlan entitás|A célalkalmazás váratlan választ adott vissza. Lehet, hogy a célalkalmazás konfigurációja nem megfelelő, vagy a célalkalmazással kapcsolatban olyan szolgáltatásprobléma van, amely megakadályozza ennek működését.|
|WebExceptionProtocolHiba |Http protokollhiba történt a célalkalmazáshoz való csatlakozás közben. Nincs mit tenni. Ez a kísérlet 40 perc múlva automatikusan megszűnik.|
|Érvénytelen horgony|A kiépítési szolgáltatás által korábban létrehozott vagy egyeztetett felhasználó már nem létezik. Ellenőrizze, hogy a felhasználó létezik-e. Az összes felhasználó újraegyeztetésének kényszerítéséhez az MS Graph API-val indítsa újra a [feladatot.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) Vegye figyelembe, hogy a kiépítés újraindítása egy kezdeti ciklust indít el, amely időbe telhet. Emellett törli a gyorsítótárat, amelyet a létesítési szolgáltatás használ a működéshez, ami azt jelenti, hogy a bérlő összes felhasználóját és csoportját újra ki kell értékelni, és bizonyos létesítési eseményeket el lehet dobni.|
|Nincs megvalósítva | A célalkalmazás nem várt választ adott vissza. Lehet, hogy az alkalmazás konfigurációja nem megfelelő, vagy a célalkalmazással olyan szolgáltatásprobléma merül fel, amely megakadályozza ennek működését. Kérjük, tekintse át a célalkalmazás által biztosított utasításokat, valamint a megfelelő [alkalmazásbemutatót.](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) |
|Hiányzik, hiányzó értékek kötelező mezők |A felhasználó nem hozható létre, mert hiányoznak a szükséges értékek. Javítsa ki a hiányzó attribútumértékeket a forrásrekordban, vagy tekintse át az egyező attribútumkonfigurációt, hogy megbizonyosodjon a szükséges mezők elhagyásának biztosításáról. [További információ](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) az egyező attribútumok konfigurálásáról.|
|SchemaAttributeNotFound |A művelet nem hajtható végre, mert olyan attribútum ot adott meg, amely nem létezik a célalkalmazásban. Tekintse meg az attribútum testreszabására vonatkozó [dokumentációt,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) és győződjön meg arról, hogy a konfiguráció helyes.|
|InternalError |Belső szolgáltatáshiba történt az Azure AD-kiépítési szolgáltatáson belül. Nincs mit tenni. A kísérlet 40 perc múlva automatikusan megkezdődik.|
|Érvénytelen tartomány |A művelet nem hajtható végre érvénytelen tartománynevet tartalmazó attribútumérték miatt. Frissítse a felhasználó tartománynevét, vagy adja hozzá a célalkalmazás engedélyezett listájához. |
|Időkorlát |A művelet nem hajtható végre, mert a célalkalmazás túl sokáig tartott a válaszadáshoz. Nincs mit tenni. A kísérlet 40 perc múlva automatikusan megkezdődik.|
|LicenseLimitExceeded|A felhasználó nem hozható létre a célalkalmazásban, mert ehhez a felhasználóhoz nincsenek elérhető licencek. Szerezzen be további licenceket a célalkalmazáshoz, vagy tekintse át a felhasználói hozzárendeléseket és az attribútumleképezési konfigurációt, hogy a megfelelő felhasználók a megfelelő attribútumokkal legyenek hozzárendelve.|
|DuplicateTargetEntries  |A művelet nem hajtható végre, mert a célalkalmazásban egynél több felhasználó található a konfigurált egyező attribútumokkal. Távolítsa el az ismétlődő felhasználót a célalkalmazásból, vagy konfigurálja újra az attribútumleképezéseket [az itt](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)leírtak szerint.|
|Duplikáltforrás-bejegyzések | A művelet nem hajtható végre, mert egynél több felhasználó található a konfigurált egyező attribútumokkal. Távolítsa el az ismétlődő felhasználót, vagy konfigurálja újra az attribútum-hozzárendeléseket [az itt](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)leírtak szerint.|

## <a name="next-steps"></a>További lépések

* [A felhasználói kiépítés állapotának ellenőrzése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Probléma a felhasználók Azure AD Gallery-alkalmazásba való kiépítésének konfigurálásakor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


