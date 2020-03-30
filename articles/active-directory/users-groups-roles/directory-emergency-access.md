---
title: Vészelérési rendszergazdai fiókok kezelése – Azure AD | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan használatával vészelérési fiókok segítségével véletlenül kizárt az Azure Active Directory (Azure AD) szervezet.
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/08/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80ab7e0603f63fb395832b0da887916dc032c3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028134"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Vészelérési fiókok kezelése az Azure AD-ben

Fontos, hogy megakadályozza, hogy véletlenül kizárva az Azure Active Directory (Azure AD) szervezet, mert nem tud bejelentkezni, vagy aktiválni egy másik felhasználó fiókját rendszergazdaként. A felügyeleti hozzáférés véletlen hiánya hatását két vagy több *vészelérési fiók* létrehozásával csökkentheti a szervezetben.

A vészelérési fiókok magas szintű jogosultsággal rendelkeznek, és nincsenek hozzárendelve bizonyos személyekhez. A vészhelyzeti hozzáférési fiókok vészhelyzeti vagy "üvegtörés" esetekre korlátozódnak, ahol a normál felügyeleti fiókok nem használhatók. Azt javasoljuk, hogy a vészhelyzeti fiók használatának korlátozása csak azokat az időpontokat, amikor feltétlenül szükséges.

Ez a cikk az Azure AD-ben a vészelérési fiókok kezelésével kapcsolatos irányelveket ismerteti.

## <a name="why-use-an-emergency-access-account"></a>Miért érdemes vészelérési fiókot használni?

Előfordulhat, hogy egy szervezetnek vészhelyzeti hozzáférési fiókot kell használnia a következő esetekben:

- A felhasználói fiókok összevannak kötve, és az összevonás jelenleg nem érhető el a cellahálózat megszakadása vagy az identitásszolgáltató kimaradása miatt. Ha például a környezetben lévő identitásszolgáltató gazdagépe leállt, előfordulhat, hogy a felhasználók nem tudnak bejelentkezni, amikor az Azure AD átirányítja az identitásszolgáltatójukra.
- A rendszergazdák regisztrálva vannak az Azure többtényezős hitelesítésén keresztül, és az összes egyes eszköz nem érhető el, vagy a szolgáltatás nem érhető el. Előfordulhat, hogy a felhasználók nem tudják végrehajtani a többtényezős hitelesítést egy szerepkör aktiválásához. Például egy mobilhálózati kimaradás megakadályozza őket abban, hogy telefonhívásokat fogadjanak vagy szöveges üzeneteket fogadjanak, ez az a két hitelesítési mechanizmus, amelyet az eszközükön regisztráltak.
- A legutóbbi globális rendszergazdai hozzáféréssel rendelkező személy elhagyta a szervezetet. Az Azure AD megakadályozza az utolsó globális rendszergazdai fiók törlését, de nem akadályozza meg a fiók törlését vagy letiltását a helyszínen. Bármelyik helyzet miatt a szervezet nem tudja helyreállítani a fiókot.
- Előre nem látható körülmények, például természeti katasztrófa esetén előfordulhat, hogy egy mobiltelefon vagy más hálózat nem érhető el. 

## <a name="create-emergency-access-accounts"></a>Segélyhívó fiókok létrehozása

Hozzon létre két vagy több vészelérési fiókot. Ezek a fiókok csak felhőalapú \*fiókok, amelyek a .onmicrosoft.com tartományt használják, és amelyek nincsenek összekapcsolva vagy nem szinkronizálva a helyszíni környezetből.

A fiókok konfigurálásakor a következő követelményeknek kell megfelelni:

- A vészelérési fiókok nem társíthatók a szervezet egyetlen felhasználójához sem. Győződjön meg arról, hogy fiókjai nem kapcsolódnak az alkalmazottak által biztosított mobiltelefonokhoz, az egyes alkalmazottakkal utazó hardvertokenekhez vagy más, az alkalmazottakra vonatkozó hitelesítő adatokhoz. Ez az óvintézkedés azokra az esetekre vonatkozik, amikor egy adott alkalmazott nem érhető el, amikor a hitelesítő adatokra szükség van. Fontos annak biztosítása, hogy minden regisztrált eszközök egy ismert, biztonságos helyen, amely több módon kommunikál az Azure AD-vel.
- A vészelérési fiókokhoz használt hitelesítési mechanizmusnak különböznie kell a többi felügyeleti fiók által használttól, beleértve az egyéb vészelérési fiókokat is.  Például ha a normál rendszergazdai bejelentkezés a helyszíni MFA-n keresztül történik, akkor az Azure MFA egy másik mechanizmus lenne.  Azonban ha az Azure MFA a rendszergazdai fiókok hitelesítésének elsődleges része, akkor fontolja meg egy másik megközelítést ezekhez, például a feltételes hozzáférés használata egy külső MFA-szolgáltatóval.
- Az eszköz vagy a hitelesítő adat nem járhat le, vagy a használat hiánya miatt automatikus törlési lehetőség nek lehet része.  
- A globális rendszergazdai szerepkör-hozzárendelést állandóvá kell tenni a vészelérési fiókokhoz. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Legalább egy fiók kizárása a telefonalapú többtényezős hitelesítésből

A feltört jelszóból eredő támadások kockázatának csökkentése érdekében az Azure AD azt javasolja, hogy minden egyes felhasználó számára többtényezős hitelesítést igényeljen. Ebbe a csoportba tartoznak a rendszergazdák és az összes többi (például pénzügyi tisztviselő), akiknek a feltört számlája jelentős hatást gyakorolna.

Azonban legalább egy vészelérési fiókok nem rendelkezik ugyanazt a többtényezős hitelesítési mechanizmus, mint a többi nem sürgősségi fiókok. Ez magában foglalja a harmadik féltől származó többtényezős hitelesítési megoldásokat. Ha feltételes hozzáférési szabályzattal rendelkezik, amely [többtényezős hitelesítést](../authentication/howto-mfa-userstates.md) igényel az Azure AD és más csatlakoztatott szoftverek szolgáltatásként (SaaS) minden rendszergazdájának, ki kell zárnia a vészhelyzeti hozzáférési fiókokat ebből a követelményből, és helyette egy másik mechanizmust kell konfigurálnia. Ezenkívül győződjön meg arról, hogy a fiókok nem rendelkeznek felhasználónkénti többtényezős hitelesítési házirenddel.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Legalább egy fiók kizárása a feltételes hozzáférési házirendekből

Vészhelyzet esetén nem szeretné, hogy egy szabályzat letiltsa a hozzáférést egy probléma megoldásához. Legalább egy vészelérési fiókot ki kell zárni az összes feltételes hozzáférési szabályzatból. Ha engedélyezte az [alapházirendet,](../conditional-access/baseline-protection.md)ki kell zárnia a vészelérési fiókokat.

## <a name="federation-guidance"></a>Összevonási útmutató

Egy további lehetőség a szervezetek, amelyek az AD tartományi szolgáltatások és Az ADFS vagy hasonló identitásszolgáltató az Azure AD-vel, konfigurálása egy vészelérési fiók, amelynek MFA-jog lehet az adott identitásszolgáltató által megadott.  A vészelérési fiókot például egy tanúsítvány és egy kulcspár, például egy intelligens kártyán tárolt kulcspár is támogathatja.  Amikor a felhasználó a D-ben hitelesítve van, az ADFS jogcímet adhat az Azure AD-nek, jelezve, hogy a felhasználó megfelel t az MFA-követelményeknek.  Még ezzel a megközelítéssel is a szervezeteknek továbbra is felhőalapú vészhelyzeti hozzáférési fiókokkal kell rendelkezniük abban az esetben, ha az összevonás nem hozható létre. 

## <a name="store-account-credentials-safely"></a>A fiók hitelesítő adatainak biztonságos tárolása

A szervezeteknek gondoskodniuk kell arról, hogy a segélyhívó fiókok hitelesítő adatai biztonságban legyenek, és csak azok számára ismertek legyenek, akik jogosultak azok használatára. Egyes ügyfelek intelligens kártyát, mások pedig jelszavakat használnak. A vészelérési fiók jelszavát általában két vagy három részre bontják, külön papírra írják, és biztonságos, tűzálló széfekben tárolják, amelyek biztonságos, külön helyeken találhatók.

Jelszavak használata esetén győződjön meg arról, hogy a fiókok erős jelszóval rendelkeznek, amelyek nem járnak le a jelszóval. Ideális esetben a jelszavaknak legalább 16 karakter hosszúnak és véletlenszerűen generáltnak kell lenniük.

## <a name="monitor-sign-in-and-audit-logs"></a>Bejelentkezési és naplózási naplók figyelése

A szervezeteknek figyelniük kell a bejelentkezési és naplózási naplótevékenységeket a segélyhívási fiókokból, és értesítéseket kell kezdeményezniük más rendszergazdáknak. Ha üvegtörési fiókokban figyeli a tevékenységet, ellenőrizheti, hogy ezek a fiókok csak tesztelésre vagy tényleges vészhelyzetekre szolgálnak-e. Az Azure Log Analytics segítségével figyelheti a bejelentkezési naplókat, és e-mail- és SMS-riasztásokat indíthat el a rendszergazdáknak, amikor üvegfiókok at törnek be.

### <a name="prerequisites"></a>Előfeltételek

1. [Az Azure AD bejelentkezési naplóinak küldése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) az Azure Monitorba.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>A törésüveg-fiókok objektumazonosítóinak beszerzése

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) a felhasználói rendszergazdai szerepkörhöz rendelt fiókkal.
1. Válassza az Azure Active > **Directory-felhasználók lehetőséget.** **Azure Active Directory**
1. Keresse meg a törtüveg-fiókot, és válassza ki a felhasználó nevét.
1. Másolja és mentse az Objektumazonosító attribútumot, hogy később is használhassa.
1. Ismételje meg az előző lépéseket a második törésüveg-fiókhoz.

### <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy fiókkal az Azure Monitor figyelőben a közreműködői szerepkörhöz rendelve.
1. Válassza **a Minden szolgáltatás**", írja be a "log analytics" szót a Keresés mezőbe, majd válassza a Log **Analytics-munkaterületeket.**
1. Jelöljön ki egy munkaterületet.
1. A munkaterületen válassza a Riasztások új **riasztási** > **szabályt.**
    1. Az **Erőforrás csoportban**ellenőrizze, hogy az előfizetés hez szeretné-e társítani a riasztási szabályt.
    1. A **Feltétel csoportban**válassza **a Hozzáadás**lehetőséget.
    1. Válassza **az Egyéni naplókeresés** lehetőséget a **Jel neve csoportban.**
    1. A **Keresés lekérdezés**csoportban adja meg a következő lekérdezést, és szúrja be a két törésüveg-fiók objektumazonosítóit.
        > [!NOTE]
        > Minden további törésüveg-fiókhoz, amelyet fel szeretne venni, adjon hozzá egy másik "vagy UserId == "ObjectGuid""-ot a lekérdezéshez.

        ![A törésüveg-fiókok objektumazonosítóinak hozzáadása riasztási szabályhoz](./media/directory-emergency-access/query-image1.png)

    1. A **Riasztási logika csoportban**adja meg a következőket:

        - Az eredmények száma alapján
        - Operátor: Nagyobb, mint
        - Küszöbérték: 0

    1. A **Kiértékelés alapján csoportban**válassza ki a lekérdezés futtatásának időtartamára **vonatkozó időszakot (percben),** és a **gyakoriságot (percben)** a lekérdezés futtatásának gyakoriságát (percben). A gyakoriságnak kisebbnek vagy egyenlőnek kell lennie az időszakkal.

        ![riasztási logika](./media/directory-emergency-access/alert-image2.png)

    1. Válassza a **Done** (Kész) lehetőséget. Most már megtekintheti a riasztás becsült havi költségét.
1. Válassza ki a felhasználók azon műveletcsoportját, akiket a riasztás szeretne értesíteni. Ha létre szeretne hozni egyet, olvassa el a [Műveletcsoport létrehozása](#create-an-action-group)című témakört.
1. A műveletcsoport tagjainak küldött e-mail értesítés testreszabásához válassza a **Műveletek testreszabása csoportban**a Műveletek testreszabása csoportban.
1. A **Riasztás részletei csoportban**adja meg a riasztási szabály nevét, és adjon meg egy nem kötelező leírást.
1. Állítsa be az esemény **súlyossági szintjét.** Javasoljuk, hogy állítsa **kritikus(Sev 0)** értékre.
1. A **Szabály engedélyezése létrehozáskor csoportban** **hagyja,** hogy igen legyen.
1. A riasztások egy ideig történő kikapcsolását, jelölje be a **Riasztások letiltása jelölőnégyzetet,** és adja meg a várakozási időtartamot a figyelmeztetés előtt, majd kattintson a **Mentés gombra.**
1. Kattintson **a Figyelmeztetési szabály létrehozása gombra.**

### <a name="create-an-action-group"></a>Műveletcsoport létrehozása

1. Válassza **a Műveletcsoport létrehozása lehetőséget.**

    ![műveletcsoport létrehozása értesítési műveletekhez](./media/directory-emergency-access/action-group-image3.png)

1. Adja meg a műveletcsoport nevét és egy rövid nevet.
1. Ellenőrizze az előfizetést és az erőforráscsoportot.
1. A művelettípus csoportban válassza **az E-mail/SMS/Push/Voice**lehetőséget.
1. Adjon meg egy műveletnevet, például **Értesítse a globális rendszergazdát**.
1. Válassza ki a **művelettípust** **e-mail/SMS/Push/Voice néven.**
1. Válassza **a Részletek szerkesztése** lehetőséget a konfigurálni kívánt értesítési módszerek kiválasztásához és a szükséges kapcsolattartási adatok megadásához, majd a Részletek mentéséhez **kattintson** az Ok gombra.
1. Adja hozzá az aktiválni kívánt további műveleteket.
1. Válassza **az OK gombot.**

## <a name="validate-accounts-regularly"></a>Rendszeresen ellenőrizze a fiókokat

Amikor betanítja a személyzet tagjait a vészelérési fiókok használatára és a vészelérési fiókok ellenőrzésére, legalább rendszeres időközönként tegye meg a következő lépéseket:

- Győződjön meg arról, hogy a biztonsági figyelő személyzet tisztában van azzal, hogy a fiókellenőrzési tevékenység folyamatban van.
- Győződjön meg arról, hogy a fiókok használatához szükséges vésztörési üvegfolyamat dokumentált és aktuális.
- Győződjön meg arról, hogy a rendszergazdák és a biztonsági tisztviselők, akiknek szükség lehet végrehajtani ezeket a lépéseket vészhelyzet esetén képzett a folyamatról.
- Frissítse a fiók hitelesítő adatait, különösen a jelszavakat a segélyhívó fiókokhoz, majd ellenőrizze, hogy a segélyhívó fiókok be tudnak-e jelentkezni és felügyeleti feladatokat hajthatnak végre.
- Győződjön meg arról, hogy a felhasználók nem regisztrálttöbbtényezős hitelesítés vagy önkiszolgáló jelszó-visszaállítás (SSPR) bármely egyéni felhasználó eszközvagy személyes adatait. 
- Ha a fiókok regisztrálva vannak a többtényezős hitelesítés egy eszközre, a bejelentkezés vagy a szerepkör aktiválása során, győződjön meg arról, hogy az eszköz elérhető az összes rendszergazdák, akik esetleg használni vészhelyzet esetén. Ellenőrizze azt is, hogy az eszköz képes-e legalább két olyan hálózati útvonalon keresztül kommunikálni, amelyek nem rendelkeznek közös hibamóddal. Az eszköz például a létesítmény vezeték nélküli hálózatán és egy mobilszolgáltatói hálózaton keresztül is kommunikálhat az internettel.

Ezeket a lépéseket rendszeres időközönként és a legfontosabb változtatások esetében kell végrehajtani:

- Legalább 90 naponta
- Ha a közelmúltban megváltozott az informatikai személyzet, például a munkahely-változás, az indulás vagy az új
- Ha az Azure AD-előfizetések a szervezetben megváltoztak

## <a name="next-steps"></a>További lépések

- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](directory-admin-roles-secure.md)
- [Felhasználók hozzáadása az Azure AD használatával,](../fundamentals/add-users-azure-active-directory.md) és [az új felhasználó hozzárendelése a globális rendszergazdai szerepkörhöz](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Regisztráljon az Azure AD Premium szolgáltatásra,](../fundamentals/active-directory-get-started-premium.md)ha még nem regisztrált
- [Kétlépéses ellenőrzés megkövetelése egy felhasználó számára](../authentication/howto-mfa-userstates.md)
- [További védelem konfigurálása globális rendszergazdák számára az Office 365-ben,](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts)ha Office 365-öt használ
- [Hozzáférés-ellenőrzés indítása a globális rendszergazdák számára,](../privileged-identity-management/pim-how-to-start-security-review.md) és [a meglévő globális rendszergazdák átváltása konkrétabb rendszergazdai szerepkörökre](directory-assign-admin-roles.md)
