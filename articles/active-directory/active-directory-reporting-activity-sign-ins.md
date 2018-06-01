---
title: Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: A bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon – bevezetés
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/17/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 78d3399aab8e3c3b1d98946cb3ac6ffab353d95c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257713"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon

Az [Azure Portalon](https://portal.azure.com) az Azure Active Directory (Azure AD) jelentéskészítési funkciójával minden szükséges információhoz hozzájuthat a környezetével kapcsolatban.

Az Azure Active Directory jelentéskészítési architektúrája a következő elemekből áll:

- **Tevékenység** 
    - **Bejelentkezési tevékenységek** – A felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk
    - **Naplók** – Rendszertevékenység információk a felhasználó- és csoportfelügyeletre, valamint a felügyelt alkalmazásokra és a címtártevékenységekre vonatkozóan.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. További részletek: Kockázatos bejelentkezések.
    - **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. További részletek: Kockázatosként megjelölt felhasználók.

Ez a témakör áttekintést nyújt a bejelentkezési tevékenységekről.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
* A biztonsági rendszergazda, a biztonsági olvasó vagy a jelentésolvasó szerepkörű felhasználók
* A globális rendszergazdák
* Bármely (nem rendszergazda jogosultságú) felhasználó hozzáfér a saját bejelentkezéseihez 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?
* A bérlőjének prémium szintű Azure AD-licenccel kell rendelkeznie az összes bejelentkezési tevékenység jelentésének megtekintéséhez


## <a name="sign-in-activities"></a>Bejelentkezési tevékenységek

A felhasználók bejelentkezési jelentésében szereplő információkból az alábbi kérdésekre kaphat választ:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

A **Bejelentkezések** menüponton át vezet az út a bejelentkezési tevékenység adataihoz, a menüpont az **Azure Active** Tevékenység szakaszában található.


![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/61.png "Sign-in activity")


A bejelentkezési napló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- A bejelentkezés dátuma
- A kapcsolódó felhasználó
- Az alkalmazás, amelybe a felhasználó bejelentkezett
- A bejelentkezési állapot
- A kockázatészlelés állapota
- A többtényezős hitelesítési (MFA-) követelmény állapota 

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/01.png "Sign-in activity")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/19.png "Sign-in activity")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/02.png "Sign-in activity")

A listanézet egyik elemére kattintva megtekintheti az összes elérhető részletét vízszintes nézetben.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/03.png "Sign-in activity")


## <a name="filter-sign-in-activities"></a>A bejelentkezési tevékenységek szűrése

A jelentésben lévő adatok megfelelő szintű szűkítéséhez az alábbi alapértelmezett mezőkkel szűrheti a bejelentkezési adatokat:

- Felhasználó
- Alkalmazás
- Bejelentkezési állapot
- A kockázatészlelés állapota
- Dátum


![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/04.png "Sign-in activity")

A **Felhasználó** szűrővel egy konkrét felhasználó nevét vagy egyszerű felhasználónevét (UPN) adhatja meg. 

Az **Alkalmazás** szűrővel egy konkrét alkalmazás nevét adhatja meg.  

A **Bejelentkezési állapot** szűrővel az alábbi lehetőségek közül választhat:

- Összes 
- Sikeres
- Hiba

A **Kockázat észlelve** szűrővel az alábbi lehetőségek közül választhat:

- Összes
- Igen
- Nem 


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


![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/12.png "Sign-in activity")


> [!TIP] 
> Az alapértelmezett szűrők mellett a bejelentkezési nézethez adott összes további mező szűrőmező lesz.


## <a name="download-sign-in-activities"></a>Bejelentkezési tevékenységek letöltése

Letöltheti a bejelentkezési tevékenységek adatait, ha az Azure Portalon kívül szeretné használni őket. A letöltés gomb mellett az Azure Portalon létrehozhat egy szkriptet is az adatok letöltésére.  

![Letöltés](./media/active-directory-reporting-activity-sign-ins/71.png "Letöltés")

A **Letöltés** gombra kattintva létrejön egy CSV-fájl a legutóbbi 5 ezer rekordból. Ha nagyobb rugalmasságra van szüksége, használhat szkriptet. Ha a **Szkript** elemre kattint, létrejön egy szkript, amely az összes beállított szűrőt tartalmazza. A technikai megvalósítás mellett a letölthető rekordok számát az [Azure Active Directory jelentésmegőrzési szabályzata](active-directory-reporting-retention.md) is korlátozza.  



## <a name="sign-in-activities-shortcuts"></a>Bejelentkezési tevékenységek parancsikonjai

Az Azure Active Directory mellett az Azure Portal két további lehetőséget biztosít a bejelentkezési tevékenységek adatainak elérésére:

- Az identitásbiztonság védelmének áttekintése
- Felhasználók
- Csoportok
- Vállalati alkalmazások


### <a name="users-sign-ins-activities"></a>Felhasználók bejelentkezési tevékenységei

A felhasználók bejelentkezési jelentésében szereplő információkból az alábbi kérdésekre kaphat választ:

- Milyen egy adott felhasználó bejelentkezési mintázata?
- Hány felhasználó jelentkezett be egy adott héten?
- Milyen állapotúak ezek a bejelentkezések?



Az adatok megtekintését a felhasználók bejelentkezési grafikonjával kezdheti, amely **Az identitásbiztonság védelmének áttekintése** oldalon található. A felhasználók bejelentkezési grafikonja az összes felhasználó bejelentkezéseinek összesítését ábrázolja egy adott időszakban. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/06.png "Sign-in activity")

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

 
A **Felhasználók** oldalon teljes körű áttekintést kaphat az összes felhasználói bejelentkezésről a **Tevékenységek** szakaszban található **Bejelentkezések** elemre kattintva.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/08.png "Sign-in activity")




## <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai

A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* Melyik a szervezet 3 legnépszerűbb alkalmazása?
* Nemrégiben új alkalmazást adtam ki. Mennyire sikeres?

Az adatok megtekintését *a cég az elmúlt 30 nap alatt legnépszerűbb 3 alkalmazásáról* szóló jelentéssel kezdheti az **Áttekintés** szakaszban, a **Vállalati alkalmazások** oldalon.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/10.png "Sign-in activity")

A 3 legnépszerűbb alkalmazásba való bejelentkezések heti összesítő grafikonja egy adott időszak során. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/47.png "Sign-in activity")

Igény esetén egy adott alkalmazást is kiemelhet.


![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Jelentéskészítés")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.




A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/11.png "Sign-in activity")



## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a bejelentkezési tevékenységek hibakódjairól, lásd: [Bejelentkezési tevékenységekre vonatkozó jelentések hibakódjai az Azure Active Directory portálon](active-directory-reporting-activity-sign-ins-errors.md).

