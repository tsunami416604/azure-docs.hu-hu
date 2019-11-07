---
title: Naplók kiépítés a Azure Active Directory portálon (előzetes verzió) | Microsoft Docs
description: A Azure Active Directory-portálon végzett kiépítési tevékenységekről szóló jelentések bemutatása
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
ms.openlocfilehash: c6e0c697f9ab9796feade9b4d5c2a64794f3980b
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612803"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Jelentések kiépítési jelentései a Azure Active Directory portálon (előzetes verzió)

A Azure Active Directory (Azure AD) jelentéskészítési architektúrája a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – információk a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
    - **Naplók** - a [naplók](concept-audit-logs.md) rendszertevékenységi információkat biztosítanak a felhasználókról és a csoport kezeléséről, a felügyelt alkalmazásokról és a címtárbeli tevékenységekről.
    - **Naplók** kiosztása – rendszertevékenység nyújtása az Azure ad-kiépítési szolgáltatás által kiépített felhasználókkal, csoportokkal és szerepkörökkel kapcsolatban. 

- **Biztonság** 
    - **Kockázatos bejelentkezések** – a [kockázatos bejelentkezés](concept-risky-sign-ins.md) egy olyan bejelentkezési kísérletre utal, amelyet a felhasználói fiók jogos tulajdonosaként elvégeztek.
    - **Kockázatnak** kitett felhasználók – a [kockázatos felhasználók](concept-user-at-risk.md) egy olyan felhasználói fiókra vonatkozó jelző, amely esetleg sérült.

Ez a témakör áttekintést nyújt a kiépítési jelentésről.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
* Felhasználók a biztonsági rendszergazda, a biztonsági olvasó, a jelentéskészítő, az alkalmazás rendszergazdája és a Felhőbeli alkalmazás rendszergazdai szerepkörei
* Globális rendszergazdák


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Milyen Azure AD-licencre van szükség a kiépítési tevékenységekhez való hozzáféréshez?

A bérlőnek prémium szintű Azure AD licenccel kell rendelkeznie ahhoz, hogy láthassa a teljes kiépítési tevékenység jelentését. A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört. 

## <a name="provisioning-logs"></a>Üzembehelyezési naplók

A kiépítési naplók a következő kérdésekre adnak választ:

* Milyen csoportokat sikerült létrehozni a ServiceNow-ben?
* Hogyan lettek importálva a szerepkörök a Amazon Web Services?
* Mely felhasználók lettek sikeresen létrehozva a DropBoxban?

A kiépítési naplókat úgy érheti el, ha kijelöli a **kiépítési** naplókat a [Azure Portal](https://portal.azure.com) **Azure Active Directory** paneljének **figyelés** szakaszában. Akár két órát is igénybe vehet, hogy egyes kiépítési rekordok megjelenjenek a portálon.

![Naplók kiépítés](./media/concept-provisioning-logs/access-provisioning-logs.png "Üzembehelyezési naplók")


A kiépítési napló egy alapértelmezett listanézet, amely a következőket jeleníti meg:

- Az identitás
- A művelet
- A forrásoldali System
- A célként megadott System
- Az állapot
- A dátum


![Alapértelmezett oszlopok](./media/concept-provisioning-logs/default-columns.png "Alapértelmezett oszlopok")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Oszlop kiválasztása](./media/concept-provisioning-logs/column-chooser.png "Oszlop kiválasztása")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Elérhető oszlopok](./media/concept-provisioning-logs/available-columns.png "Elérhető oszlopok")

Részletesebb információkhoz jelöljön ki egy elemet a listanézet nézetben.

![Részletes információk](./media/concept-provisioning-logs/steps.png "Szűrés")


## <a name="filter-provisioning-activities"></a>Kiépítési tevékenységek szűrése

A jelentett adat leszűkíthető egy olyan szintre, amely az Ön számára működik, a következő alapértelmezett mezők használatával szűrheti a kiépítési adatait. Vegye figyelembe, hogy a szűrőkben szereplő értékek dinamikusan vannak feltöltve a bérlő alapján. Ha például nem rendelkezik létrehozási eseményekkel a bérlőben, a létrehozáshoz nem lesz szűrő lehetőség.

- Identitáskezelés
- Műveletek
- Forrásoldali rendszerek
- Célrendszer
- Állapot
- Dátum


![Szűrő](./media/concept-provisioning-logs/filter.png "Szűrés")

Az **Identity** szűrő segítségével megadhatja a nevet vagy az Ön számára fontos identitást. Ez az identitás lehet felhasználó, csoport, szerepkör vagy más objektum. Az objektum neve vagy azonosítója alapján kereshet. Az azonosító forgatókönyv szerint változik. Ha például egy objektumot kiépít az Azure AD-ből a SalesForce-be, a forrás azonosítója az Azure AD-beli felhasználó objektumazonosítóa, míg a TargetID a Salesforce felhasználójának azonosítója. Ha a munkahelyről a Active Directoryre való kiépítés után a forrás azonosítója a munkanap munkavégző alkalmazottjának azonosítója. Vegye figyelembe, hogy a felhasználó neve nem mindig szerepel az Identity oszlopban. Mindig egy azonosító lesz. 

A **forrásrendszer** szűrője lehetővé teszi annak megadását, hogy az identitás honnan legyen kiépítve. Ha például egy objektumot kiépít az Azure AD-ből a ServiceNow-be, akkor a forrásoldali rendszer az Azure AD. 

A célként megadott rendszerszűrő segítségével megadhatja, hogy az identitás hová legyen kiépítve a **szolgáltatásba** . Ha például egy objektumot kiépít az Azure AD-ből a ServiceNow-be, a rendszer ServiceNow. 

Az **állapot** szűrő segítségével a következőket választhatja ki:

- Összes
- Sikeres
- Hiba
- Kimarad

A **művelet** szűrője lehetővé teszi a következő szűrését:

- Létrehozás 
- Frissítés
- Törlés
- Letiltás
- Egyéb

A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek:

- 1 hónap
- 7 nap
- 30 nap
- 24 óra
- Egyéni időintervallum

Amikor kiválaszt egy egyéni időkeretet, beállíthatja a kezdési és a befejezési dátumot.


Az alapértelmezett mezőkön kívül a kiválasztáskor a következő mezőket is megadhatja a szűrőben:

- **Job ID** – a rendszer minden olyan alkalmazáshoz társít egy egyedi azonosítójú feladatot, amelyhez engedélyezte az üzembe helyezést.   

- **Ciklus azonosítója** – egyedi módon azonosítja a létesítési ciklust. Ezt az azonosítót megoszthatja a támogatással, hogy megkeresse azt a ciklust, amelyben ez az esemény történt.

- A kiépítési esemény egyedi azonosítójának **módosítása** . Megoszthatja ezt az azonosítót, hogy támogassa a kiépítési esemény megkeresését.   



  

## <a name="provisioning-details"></a>Kiépítés részletei 

Amikor kiválaszt egy elemet a kiépítési lista nézetben, az elemről további részleteket talál.
A részletek a következő kategóriák alapján vannak csoportosítva:

- Lépések

- Hibák és javaslatok

- Módosított tulajdonságok

- Összefoglalás


![Szűrő](./media/concept-provisioning-logs/provisioning-tabs.png "Lapok")



### <a name="steps"></a>Lépések

A **lépések** lapon az objektum kiépítéséhez szükséges lépések szerepelnek. Az objektumok kiépítés négy lépésből állhat: 

- Objektum importálása
- Annak megállapítása, hogy az objektum hatókörben van-e
- Objektum egyeztetése a forrás és a cél között
- Objektum kiépítése (művelet elvégzése – ez lehet létrehozás, frissítés, törlés vagy Letiltás)



![Szűrő](./media/concept-provisioning-logs/steps.png "Szűrés")


### <a name="troubleshoot-and-recommendations"></a>Hibák és javaslatok


A **hibakeresés és javaslatok** lap a hibakódot és az okot adja meg. A hiba adatai csak meghibásodás esetén érhetők el. 


### <a name="modified-properties"></a>Módosított tulajdonságok

A **módosított tulajdonságok** a régi értéket és az új értéket jelenítik meg. Olyan esetekben, amikor nincs régi érték, a régi érték oszlop üres. 


### <a name="summary"></a>Összefoglalás

Az **Összefoglalás** lapon áttekintheti, hogy mi történt, és milyen azonosítókat tartalmaz a forrás és a cél rendszer objektumához. 

## <a name="what-you-should-know"></a>Alapismeretek

- A Azure Portal 30 napig tárolja a kiépítési adatgyűjtési jelentést, ha rendelkezik prémium szintű kiadással, és 7 nap van, ha ingyenes kiadással rendelkezik.

- A Change ID attribútum egyedi azonosítóként használható. Ez például a terméktámogatással való interakció esetén hasznos.

- Jelenleg nincs lehetőség az üzembe helyezési adatszolgáltatások letöltésére.

- A log Analytics jelenleg nem támogatott.

- Amikor egy alkalmazás környezetében fér hozzá a kiépítési naplókhoz, nem szűri automatikusan az eseményeket az adott alkalmazáshoz a naplók módon.

## <a name="error-codes"></a>Hibakódok

Az alábbi táblázat segítségével jobban megismerheti, Hogyan oldhatók meg a kiépítési naplókban esetlegesen felmerülő hibák. A hiányzó hibakódok esetében küldjön visszajelzést az oldal alján található hivatkozás használatával. 

|Hibakód|Leírás|
|---|---|
|Ütközés, EntryConflict|Javítsa ki az ütköző attribútum értékeit az Azure AD-ben vagy az alkalmazásban, vagy tekintse át a megfelelő attribútum-konfigurációt, ha az ütköző felhasználói fióknak meg kell egyeznie és át kellene vennie. Az egyeztetési attribútumok konfigurálásával kapcsolatos további információkért tekintse át az alábbi [dokumentációt](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) .|
|TooManyRequests|A célalkalmazás elutasította ezt a kísérletet a felhasználó frissítésére, mert túlterhelt, és túl sok kérést fogad. Semmi teendő. A rendszer automatikusan kivonja ezt a kísérletet. A Microsoft értesítette a problémát is.|
|InternalServerError |A célalkalmazás váratlan hibát adott vissza. Előfordulhat, hogy egy szolgáltatási probléma van a célalkalmazás számára, amely megakadályozza ennek működését. Ez a kísérlet 40 percen belül automatikusan kimarad.|
|InsufficientRights, MethodNotAllowed, NotPermitted, jogosulatlan| Az Azure AD képes volt hitelesíteni a megcélzott alkalmazást, de nem jogosult a frissítés végrehajtására. Tekintse át a célalkalmazás által biztosított utasításokat, valamint az alkalmazásra vonatkozó [oktatóanyagot](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list).|
|UnprocessableEntity|A célalkalmazás váratlan választ adott vissza. Lehetséges, hogy a célalkalmazás konfigurációja nem megfelelő, vagy a célalkalmazás olyan szolgáltatási problémával jár, amely megakadályozza ennek működését.|
|WebExceptionProtocolError |HTTP protokoll hiba történt a célalkalmazáshoz való csatlakozás során. Semmi teendő. Ez a kísérlet 40 percen belül automatikusan kimarad.|
|InvalidAnchor|Már nem létezik olyan felhasználó, aki korábban létrehozta vagy egyeztette a kiépítési szolgáltatás. Győződjön meg arról, hogy a felhasználó létezik. Az összes felhasználó újraegyezésének kényszerítéséhez az MS Graph API használatával [indítsa újra a feladatot](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Vegye figyelembe, hogy a kiépítés újraindítása elindítja a kezdeti ciklust, amely időt vehet igénybe. Emellett törli a kiépítési szolgáltatás által a működéshez használt gyorsítótárat is, ami azt jelenti, hogy a bérlő minden felhasználóját és csoportját újra ki kell értékelni, és bizonyos kiépítési eseményeket el lehet dobni.|
|Nincs implementálva | A célalkalmazás váratlan választ adott vissza. Lehetséges, hogy az alkalmazás konfigurációja nem megfelelő, vagy előfordulhat, hogy probléma van a célalkalmazás szolgáltatással, amely megakadályozza ennek működését. Tekintse át a célalkalmazás által biztosított utasításokat, valamint az alkalmazásra vonatkozó [oktatóanyagot](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). |
|MandatoryFieldsMissing, MissingValues |A felhasználó nem hozható létre, mert hiányoznak a szükséges értékek. Javítsa ki a hiányzó attribútum-értékeket a forrás rekordban, vagy tekintse át a megfelelő attribútumok konfigurációját, hogy a kötelező mezők ne legyenek kihagyva. [További](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) információ a megfeleltetési attribútumok konfigurálásáról.|
|SchemaAttributeNotFound |A művelet nem hajtható végre, mert egy olyan attribútum lett megadva, amely nem létezik a célalkalmazás alkalmazásban. Tekintse meg az attribútumok testreszabásával kapcsolatos [dokumentációt](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) , és győződjön meg arról, hogy a konfiguráció helyes.|
|InternalError |Belső szolgáltatási hiba történt az Azure AD-létesítési szolgáltatásban. Semmi teendő. Ez a kísérlet 40 perc múlva automatikusan újra próbálkozik.|
|InvalidDomain |A műveletet nem lehetett végrehajtani, mert egy attribútumérték érvénytelen tartománynevet tartalmaz. Frissítse a tartománynevet a felhasználón, vagy adja hozzá azt a célalkalmazás engedélyezett listájához. |
|időtúllépés |A műveletet nem lehetett befejezni, mert a célalkalmazás túl sokáig tartott a válaszadáshoz. Semmi teendő. Ez a kísérlet 40 perc múlva automatikusan újra próbálkozik.|
|LicenseLimitExceeded|A felhasználó nem hozható létre a célalkalmazás alkalmazásban, mert nincsenek elérhető licencek ehhez a felhasználóhoz. További licenceket is megadhat a célalkalmazás számára, vagy áttekintheti a felhasználói hozzárendelések és attribútumok leképezésének konfigurációját, hogy a megfelelő felhasználók hozzá legyenek rendelve a megfelelő attribútumokhoz.|
|DuplicateTargetEntries  |A műveletet nem lehetett befejezni, mert a célalkalmazás több felhasználója található a konfigurált egyező attribútumokkal. Távolítsa el az ismétlődő felhasználót a célalkalmazás alkalmazásból, vagy konfigurálja újra az attribútum-hozzárendeléseket az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)leírtak szerint.|
|DuplicateSourceEntries | A műveletet nem lehetett befejezni, mert egynél több felhasználó található a konfigurált egyező attribútumokkal. Távolítsa el az ismétlődő felhasználót, vagy konfigurálja újra az attribútum-hozzárendeléseket az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)leírtak szerint.|

## <a name="next-steps"></a>További lépések

* [A felhasználó kiépítési állapotának megtekintése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Hiba történt a felhasználók Azure AD Gallery-alkalmazásba való konfigurálásának beállításakor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


