---
title: Azure Active Directory – Használati feltételek| Microsoft Docs
description: Az Azure AD Használati feltételek funkción keresztül Ön és vállalata felhasználási feltételeket biztosíthat az Azure AD-szolgáltatások felhasználóinak.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 06/29/2018
ms.author: rolyon
ms.openlocfilehash: 7833c9da2303d119f0cb421f21bea455ab449898
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856416"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Azure Active Directory – Használati feltételek funkció
Az Azure AD Használati feltételek funkcióján keresztül a vállalatok egyszerűen láthatják el a végfelhasználókat a szükséges információkkal. Ez a bemutató gondoskodik arról, hogy a felhasználók megkapják a jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatokat. Ez a cikk az Azure AD Használati feltételek funkcióval kapcsolatos kezdeti lépéseket ismerteti.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-can-i-do-with-terms-of-use"></a>Mire használhatom a Használati feltételek funkciót?
Az Azure AD Használati feltételek funkció a következőket teszi lehetővé:
- Kötelezi az alkalmazottakat vagy a vendégfelhasználókat arra, hogy a hozzáférés előtt elfogadják a használati feltételeket.
- A vállalat minden felhasználójára vonatkozó általános használati feltételeket nyújt.
- Specifikus használati feltételek biztosít a felhasználó jellemzői alapján (például orvosok kontra nővérek, belföldi kontra nemzetközi alkalmazottak [dinamikus csoportjai](users-groups-roles/groups-dynamic-membership.md)).
- Specifikus használati feltételeket biztosít a kulcsfontosságú üzleti alkalmazások (például Salesforce) elérése esetén.
- Megjeleníti a használati feltételeket különböző nyelveken.
- Felsorolja, hogy ki fogadta el a használati feltételeket, és ki nem.
- Auditnaplót készít a használati feltételekkel kapcsolatos tevékenységekről.

## <a name="prerequisites"></a>Előfeltételek
Az Azure AD Használati feltételek funkció konfigurálásához a következők szükségesek:

- Prémium szintű Azure AD P1, P2, EMS E3 vagy EMS E5 előfizetés.
    - Ha még nem rendelkezik ezen előfizetések egyikével sem, [beszerezhet Prémium szintű Azure AD előfizetést](fundamentals/active-directory-get-started-premium.md), vagy [engedélyezheti a Prémium szintű Azure AD előfizetés próbaverzióját](https://azure.microsoft.com/trial/get-started-active-directory/).
- A következő rendszergazda fiókok egyike a konfigurálni kívánt könyvtárhoz:
    - Globális rendszergazda
    - Biztonsági rendszergazda
    - Feltételes hozzáférésű rendszergazda

## <a name="terms-of-use-document"></a>A használati feltételek dokumentuma

Az Azure AD Használati feltételek PDF formátumot használ a tartalmak megjelenítéséhez. A PDF-fájl is bármilyen tartalmat jelenthet, mint a meglévő szerződés dokumentumokat, így begyűjtheti a végfelhasználói megállapodások során a felhasználói bejelentkezés. Ajánlott betűméret a PDF-ben: 24.

## <a name="add-terms-of-use"></a>Használati feltételek hozzáadása
A használati feltételek dokumentum véglegesítése után a hozzáadáshoz kövesse a következő eljárást.

1. Jelentkezzen be az Azure-ba globális, biztonsági vagy feltételes hozzáférésű rendszergazdaként.

1. Lépjen a **Használati feltételekre** a következőn: [https://aka.ms/catou](https://aka.ms/catou).

    ![Használati feltételek panel](media/active-directory-tou/tou-blade.png)

1. Kattintson az **Új feltételek** lehetőségre.

    ![Használati feltételek hozzáadása](media/active-directory-tou/new-tou.png)

1. Adjon **nevet** a használati feltételeknek

2. Adja meg a **megjelenítendő nevet**.  A felhasználók ezt a fejlécet látják bejelentkezéskor.

3. A **Tallózás** lehetőséggel keresse meg a véglegesített használati feltételeket tartalmazó PDF-fájlt, és válassza ki.

4. **Válassza ki** a használati feltételek nyelvét.  A nyelvi beállítás lehetővé teszi, hogy több használati feltételt töltsön fel különböző nyelveken.  A használati feltételek végfelhasználó számára megjelenített verziója a böngésző beállításaitól függ.

5. Kapcsolja be vagy ki **A felhasználóknak legyen kötelező a használati feltételek megtekintése** beállítást.  Ha bekapcsolja ezt a beállítást, a végfelhasználóknak meg kell tekinteniük a használati feltételeket, mielőtt elfogadhatnák őket.

6. A feltöltött használati feltételeket a legördülő menüből választott sablon vagy egy egyéni feltételes hozzáférési szabályzat segítségével **Kényszerítheti ki** a **Feltételes hozzáférés** szakaszban.  Az egyéni feltételes hozzáférési szabályzat részletes használati feltételek alkalmazását teszi lehetővé, amelyeket akár egy adott felhőalkalmazásra vagy felhasználói csoportra is leszűkíthet.  További információkért tekintse meg a [feltételes hozzáférés konfigurálását bemutató részt](active-directory-conditional-access-best-practices.md).

    >[!IMPORTANT]
    >A feltételes hozzáférési szabályzat vezérlői (beleértve a használati feltételeket) nem támogatják a szolgáltatásfiókokra irányuló kényszerítést.  Javasoljuk, hogy zárjon ki minden szolgáltatásfiókot a feltételes hozzáférési szabályzatból.

7. Kattintson a **Create** (Létrehozás) gombra.

8. Ha egyéni feltételes hozzáférési sablont választott, akkor egy új képernyő jelenik meg, amelyen testre szabható a feltételes hozzáférési szabályzat.

    Ezután már az új használati feltételek jelennek meg.

    ![Használati feltételek hozzáadása](media/active-directory-tou/create-tou.png)

## <a name="view-who-has-accepted-and-declined"></a>A használati feltételeket elfogadó és elutasító felhasználók megtekintése
A használati feltételek panel megjeleníti a feltételeket elfogadó, illetve elutasító felhasználók számát. A rendszer mindaddig megőrzi ezeket az eredményeket, illetve az elfogadó/elutasító felhasználók adatait, amíg a használati feltételek léteznek.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

    ![Esemény naplózása](media/active-directory-tou/view-tou.png)

1. A felhasználók aktuális állapotának megtekintéséhez kattintson a használati feltételeket **Elfogadó** vagy **Elutasító** felhasználókat jelölő számra.

    ![Esemény naplózása](media/active-directory-tou/accepted-tou.png)

## <a name="view-audit-logs"></a>Auditnaplók megtekintése
Az Azure AD Használati feltételek auditnaplókkal is rendelkezik, ha megtekintené a feltételekkel kapcsolatos további tevékenységeket is. Minden egyes felhasználói beleegyezés elindít egy eseményt az auditnaplókban tárolt 30 napig. Ezeket a naplókat megtekintheti a portálon, vagy letöltheti egy .csv fájlban.

Az auditnaplók használatának megkezdéséhez kövesse az alábbi eljárást:

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

1. Kattintson a **Naplók megtekintése** elemre.

    ![Esemény naplózása](media/active-directory-tou/audit-tou.png)

1. Az Azure AD naplókat megjelenítő képernyőjén a rendelkezésre álló legördülő menük segítségével szűrheti az információkat, amennyiben adott naplózási információkra kíváncsi.

    ![Esemény naplózása](media/active-directory-tou/audit-logs-tou.png)

1. A **Letöltés** elemre kattintva az információkat helyi felhasználás céljából egy .csv fájlba is mentheti.

## <a name="what-terms-of-use-looks-like-for-users"></a>A használati feltételek megjelenése a felhasználók számára
A használati feltételeket tartalmazó fájl létrehozása és kényszerítése, ha felhasználók, akik érintett bejelentkezés során a következő képernyő jelenik meg.

![Esemény naplózása](media/active-directory-tou/user-tou.png)

Az alábbi képernyő bemutatja a használati feltételek megjelenését a mobileszközökön.

![Esemény naplózása](media/active-directory-tou/mobile-tou.png)

### <a name="how-users-can-review-their-terms-of-use"></a>A használati feltételek áttekintése a felhasználók által
A felhasználók az alábbi eljárással tekinthetik át az általuk elfogadott használati feltételeket.

1. Jelentkezzen be a [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

1. A jobb felső sarokban kattintson a nevére, és válassza a **Profil** lehetőséget a legördülő menüből.

    ![Profil](media/active-directory-tou/tou14.png)

1. A Profi lapon kattintson a **Használati feltételek áttekintése** elemre.

    ![Esemény naplózása](media/active-directory-tou/tou13a.png)

1. Itt áttekintheti az elfogadott használati feltételeket. 

## <a name="delete-terms-of-use"></a>Használati feltételek törlése
A korábbi használati feltételeket az alábbi eljárással törölheti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

1. Válassza ki az eltávolítani kívánt használati feltételeket.

1. Kattintson a **Feltételek törlése** lehetőségre.

1. A megjelenő üzenetben, amely a törlés megerősítését kéri, kattintson az **Igen** lehetőségre.

    ![Használati feltételek hozzáadása](media/active-directory-tou/delete-tou.png)

    A használati feltételek ezt követően már nem láthatók.

## <a name="deleted-users-and-active-terms-of-use"></a>Törölt felhasználók az aktív használati feltételekben
Alapértelmezés szerint a törölt felhasználók 30 napig még törölt állapotban megmaradnak az Azure AD-ban, amely idő alatt szükség esetén egy rendszergazda visszaállíthatja őket.  A felhasználók 30 nap után véglegesen törlődnek.  Ezenkívül egy globális rendszergazda az Azure Active Directory portálon explicit módon [véglegesen törölhet egy közelmúltban törölt felhasználót](fundamentals/active-directory-users-restore.md) az időszak lejárta előtt.  Egy felhasználó végleges törlése után a felhasználó későbbi adatai el lesznek távolítva az aktív használati feltételekből.  A törölt felhasználókkal kapcsolatos naplózási információk az auditnaplóban maradnak.

## <a name="policy-changes"></a>A szabályzatok változásai
A feltételes hozzáférési szabályzatok azonnal hatályba lépnek. Ha ez történik, a rendszergazda "Szomorú felhők" vagy "A tokenekkel kapcsolatos problémák az Azure AD" indul el. A rendszergazda kell jelentkezzen ki, és jelentkezzen be újra annak érdekében, hogy megfelelhessen az új szabályzatnak.

>[!IMPORTANT]
> Az érintett felhasználóknak újra be kell jelentkezniük, hogy megfelelhessenek az új szabályzatnak, ha:
> - egy feltételes hozzáférési szabályzat van engedélyezve a használati feltételekhez
> - vagy újabb használati feltételek lettek létrehozva

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Hogyan ellenőrizhetem, hogy egy felhasználó elfogadta-e a használati feltételeket (és ha igen, mikor)?**</br>
V: a használati panel a feltételeket, kattintson a számra **elfogadva**. Is megtekintése vagy keressen az elfogadás tevékenység a naplók. További információkért lásd: [nézet, aki rendelkezik elfogadó és elutasító](#view-who-has-accepted-and-declined) és [naplók megtekintése](#view-audit-logs).

**K: Ha megváltoznak a használati feltételeket, azt igényel a felhasználóknak újra el kell fogadniuk?**</br>
V: Igen, rendszergazda módosíthatja a használati feltételeket, és ez megköveteli a felhasználóktól az új feltételeket hatókörébe.

**K: A használati feltételek több nyelv használatát is támogatják?**</br>
V: Igen.  A rendszergazdák jelenleg 18 különböző nyelven konfigurálhatják a használati feltételeket. 

**K: Mikor lépnek életbe a használati feltételek?**</br>
V: A használati feltételek a bejelentkezéskor lépnek életbe.

**K: Mely alkalmazásokhoz használhatom a használati feltételeket?**</br>
V: Feltételes hozzáférési szabályzatot a modern hitelesítést használó vállalati alkalmazásokhoz hozhat létre.  Bővebb információ: [vállalati alkalmazások](./manage-apps/view-applications-portal.md).

**K: Több használati feltételt is hozzáadhatok egy adott felhasználóhoz vagy alkalmazáshoz?**</br>
V: Igen, ha több feltételes hozzáférési szabályzatot is létrehoz az adott csoportokhoz vagy alkalmazásokhoz. Ha egy felhasználó több használati feltétel hatókörébe is beletartozik, külön-külön kell elfogadnia őket.
 
**K: Mi történik akkor, ha egy felhasználó nem fogadja el a használati feltételeket?**</br>
V: Ebben az esetben a felhasználón nem kaphat hozzáférést az alkalmazáshoz. Jelentkezzen be újra, és fogadja el a feltételeket ahhoz, hogy a hozzáférést a felhasználónak kell.
 
**K: van unaccept a korábban elfogadott használati feltételeket?**</br>
V: is [felülvizsgálati korábban elfogadott használati feltételeket tartalmazó fájl](#how-users-can-review-their-terms-of-use), de jelenleg nincs unaccept lehetővé.
 
**K: Mennyi ideig tárolódnak az információk?**</br>
V: A rendszer addig őrzi meg a felhasználók számát, illetve az elfogadó/elutasító felhasználók adatait, amíg a használati feltételek léteznek. Az auditnaplók 30 napon keresztül érhetők el.
