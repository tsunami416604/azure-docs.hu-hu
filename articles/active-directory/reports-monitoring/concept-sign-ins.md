---
title: Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: A bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon – bevezetés
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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0826614c22809eba7a86f683aa970a664ed9825
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438563"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon

Az Azure Active Directory (Azure AD) jelentéskészítési architektúrája a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – a felügyelt alkalmazások és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk.
    - **Auditnaplók** - [Auditnaplók](concept-audit-logs.md) rendszertevékenység információk a felhasználók és a eszközcsoport-kezelés, a felügyelt alkalmazások és a címtártevékenységekre vonatkozóan adja meg.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – [kockázatos bejelentkezés](concept-risky-sign-ins.md) egy bejelentkezési kísérlet, aki nem a felhasználói fiók jogos tulajdonosa által végrehajtott előfordulhat, hogy a mutató.
    - **Kockázatosként megjelölt felhasználók** – [kockázatos felhasználó](concept-user-at-risk.md) mutató egy felhasználói fiókot, amely a előfordulhat, hogy sérült a biztonsága.

Ez a témakör a bejelentkezések jelentés áttekintést nyújt.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
* A biztonsági rendszergazdai, biztonsági olvasó és a jelentés olvasói szerepkörök felhasználók
* A globális rendszergazdák
* Emellett bármely (nem rendszergazda jogosultságú) felhasználó hozzáférhet-e a saját bejelentkezések 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?
* A bérlőnek rendelkeznie kell egy társított az összes bejelentkezési tevékenység jelentésének megtekintéséhez prémium szintű Azure AD-licenc. Lásd: [Ismerkedés az Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) az Azure Active Directory-kiadás frissítése. Vegye figyelembe, hogy a frissítés előtt tevékenységek adatokat nem rendelkezett, ha igénybe vesz néhány nap alatt az adatok megjelennek a jelentések prémium licencre történő frissítés után.

## <a name="sign-ins-report"></a>Bejelentkezések jelentés

A felhasználói bejelentkezésekre biztosítja az alábbi kérdésekre kaphat választ:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

A bejelentkezések jelentés kiválasztásával érheti **bejelentkezések** a a **tevékenység** szakaszában a **Azure Active Directory** paneljén a [AzurePortalon](https://portal.azure.com). Vegye figyelembe, hogy legfeljebb néhány megjelenjen a portál a bejelentkezési rekord két óráig is eltarthat.

![Bejelentkezési tevékenység](./media/concept-sign-ins/61.png "Sign-in activity")

> [!IMPORTANT]
> A bejelentkezések jelentés csak megjeleníti az **interaktív** bejelentkezések, amely, bejelentkezési, ahol a felhasználó manuálisan jelentkezik be felhasználónevével és jelszavával. A nem interaktív bejelentkezési, például a szolgáltatások közötti hitelesítés, a bejelentkezési jelentésben nem jelennek meg. 

A bejelentkezési napló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- A bejelentkezés dátuma
- A kapcsolódó felhasználó
- Az alkalmazás, amelybe a felhasználó bejelentkezett
- A bejelentkezési állapot
- A kockázatészlelés állapota
- A többtényezős hitelesítési (MFA-) követelmény állapota

![Bejelentkezési tevékenység](./media/concept-sign-ins/01.png "Sign-in activity")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Bejelentkezési tevékenység](./media/concept-sign-ins/19.png "Sign-in activity")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Bejelentkezési tevékenység](./media/concept-sign-ins/02.png "Sign-in activity")

Jelöljön ki egy elemet a listanézet, melyeken részletesebb információkat találhat.

![Bejelentkezési tevékenység](./media/concept-sign-ins/03.png "Sign-in activity")

> [!NOTE]
> Ügyfeleink most elháríthatja a feltételes hozzáférési szabályzatokat az összes bejelentkezési jelentéseken keresztül. Kattintson a a **feltételes hozzáférési** fülre a bejelentkezési rekord, az ügyfelek tekintheti át, a feltételes hozzáférési állapotát és adatait, a bejelentkezés és az eredmény az egyes szabályzatok alkalmazott házirendek bemutatása.
> További információkért lásd: a [– gyakran ismételt kérdések az összes bejelentkezés hitelesítésszolgáltató adatainak](reports-faq.md#conditional-access).

![Bejelentkezési tevékenység](./media/concept-sign-ins/ConditionalAccess.png "Sign-in activity")


## <a name="filter-sign-in-activities"></a>A bejelentkezési tevékenységek szűrése

A jelentésben lévő adatok megfelelő szintű szűkítéséhez az alábbi alapértelmezett mezőkkel szűrheti a bejelentkezési adatokat:

- Felhasználó
- Alkalmazás
- Bejelentkezési állapot
- Feltételes hozzáférés
- Dátum

![Bejelentkezési tevékenység](./media/concept-sign-ins/04.png "Sign-in activity")

A **Felhasználó** szűrővel egy konkrét felhasználó nevét vagy egyszerű felhasználónevét (UPN) adhatja meg.

Az **Alkalmazás** szűrővel egy konkrét alkalmazás nevét adhatja meg.

A **Bejelentkezési állapot** szűrővel az alábbi lehetőségek közül választhat:

- Összes
- Sikeres
- Hiba

A **feltételes hozzáférési** szűrő lehetővé teszi, hogy válassza ki a bejelentkezéshez hitelesítésszolgáltató szabályzat állapotát:

- Összes
- Nem alkalmazott
- Sikeres
- Hiba

A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek:

- 1 hónap
- 7 nap
- 24 óra
- Egyéni időintervallum

Egyéni időkeret kiválasztásakor beállíthatja a kezdő és a záró időpontot.

Ha további mezőket ad hozzá a bejelentkezési nézethez, a rendszer automatikusan hozzáadja a mezőket a szűrőlistához. Például az **Ügyfélalkalmazás** mező listához való hozzáadásával kap egy további szűrőlehetőséget, amellyel a következő szűrőket állíthatja be:

- Böngésző      
- Exchange ActiveSync (támogatott)               
- Exchange ActiveSync (nem támogatott)
- Más ügyfelek               
    - IMAP
    - MAPI
    - Régebbi Office-ügyfelek
    - POP
    - SMTP


![Bejelentkezési tevékenység](./media/concept-sign-ins/12.png "Sign-in activity")


## <a name="download-sign-in-activities"></a>Bejelentkezési tevékenységek letöltése

Is [töltse le a bejelentkezési adatokat](quickstart-download-sign-in-report.md) Ha azt szeretné használni az Azure Portalon kívül. Kattintson a **letöltése** teszi lehetővé a legutóbbi 250 000 rekordot CSV vagy JSON-fájl létrehozásához.  

![Letöltés](./media/concept-sign-ins/71.png "Letöltés")

> [!IMPORTANT]
> Letöltheti a rekordok száma korlátozza a [Azure Active Directory-jelentések adatmegőrzési szabályzatában](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Bejelentkezési adatok parancsikonok

Azure AD-ben mellett az Azure portal nyújt a bejelentkezési adatokat a további belépési pontokra:

- Az Identitásbiztonság védelmének áttekintése
- Felhasználók
- Csoportok
- Vállalati alkalmazások

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Felhasználók bejelentkezési adatokat az Identitásbiztonság védelmének

A felhasználók bejelentkezési grafikonjával kezdheti a a **Identitásbiztonság védelmének** áttekintése lapon megjelenik az összes felhasználó bejelentkezéseinek összesítését ábrázolja egy adott időszakban. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/concept-sign-ins/06.png "Sign-in activity")

A bejelentkezési grafikon egyik napjára kattintva áttekintést kap az adott nap bejelentkezési tevékenységeiről.

A bejelentkezési tevékenységek listájának minden sora a következőkről ad információkat:

* Ki jelentkezett be?
* Melyik alkalmazás volt a bejelentkezés célja?
* Mi a bejelentkezés állapota?
* Mi a bejelentkezés MFA-állapota?

Az elemekre kattintva részletes információk érhetők el a bejelentkezési műveletről:

- Felhasználóazonosító
- Felhasználó
- Felhasználónév
- Alkalmazásazonosító
- Alkalmazás
- Ügyfél
- Hely
- IP-cím
- Dátum
- Az MFA megadása kötelező
- Bejelentkezési állapot

> [!NOTE]
> IP-címeket úgy, hogy ez nem végleges közötti IP-cím és a számítógép ezzel a címmel fizikailag helyét adják ki. IP-címek leképezése a tény, hogy mobilalkalmazás-szolgáltatók és a VPN-eket adja ki az IP-címeket, amelyek nagyon gyakran, ahol használja ténylegesen az ügyféleszközön messze központi készletek bonyolult. Jelenleg az Azure AD-jelentések, IP-cím átalakítása egy fizikai helyhez célja a legjobb nyomkövetéseket, beállításjegyzék-adatok, fordított keresések és egyéb információk alapján.

A **Felhasználók** oldalon teljes körű áttekintést kaphat az összes felhasználói bejelentkezésről a **Tevékenységek** szakaszban található **Bejelentkezések** elemre kattintva.

![Bejelentkezési tevékenység](./media/concept-sign-ins/08.png "Sign-in activity")

## <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai

A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* Melyik a szervezet 3 legnépszerűbb alkalmazása?
* Nemrégiben új alkalmazást adtam ki. Mennyire sikeres?

Az adatok megtekintését a szervezet az elmúlt 30 nap alatt legnépszerűbb 3 alkalmazásáról szóló jelentéssel kezdheti az **Áttekintés** szakaszban, a **Vállalati alkalmazások** területen.

![Bejelentkezési tevékenység](./media/concept-sign-ins/10.png "Sign-in activity")

Az alkalmazás használati graph összesítését ábrázolja egy adott időszakban a 3 leggyakoribb alkalmazások bejelentkezések. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/concept-sign-ins/47.png "Sign-in activity")

Igény esetén egy adott alkalmazást is kiemelhet.

![Jelentéskészítés](./media/concept-sign-ins/single_spp_usage_graph.png "Jelentéskészítés")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

![Bejelentkezési tevékenység](./media/concept-sign-ins/11.png "Sign-in activity")

## <a name="office-365-activity-logs"></a>Az Office 365-Tevékenységnaplók

Az Office 365-Tevékenységnaplók is megtekintheti a [Microsoft 365 felügyeleti központban](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Annak ellenére, hogy az Office 365-Tevékenységnaplók és az Azure AD tevékenység naplók a könyvtár-erőforrások jelentős részén osztoznak, csak a Microsoft 365 felügyeleti központban az Office 365-Tevékenységnaplók teljes nézetét jeleníti meg. 

Az Office 365 tevékenységeket tartalmazó naplók programozott módon használatával is elérheti a [Office 365 felügyeleti API-k](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>További lépések

* [Bejelentkezési tevékenységre vonatkozó jelentések hibakódjai](reference-sign-ins-error-codes.md)
* [Az Azure AD adatmegőrzési házirendek](reference-reports-data-retention.md)
* [Az Azure AD-jelentés késleltetései](reference-reports-latencies.md)

