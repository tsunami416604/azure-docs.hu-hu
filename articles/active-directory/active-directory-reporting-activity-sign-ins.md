---
title: "Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs"
description: "A bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon – bevezetés"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ad7b1aae4ee14e46a5df6be0ebc29bfd455852d4
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
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

## <a name="pre-requisite"></a>Előfeltétel

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
* A biztonsági rendszergazda vagy biztonsági olvasó szerepkörű felhasználók
* A globális rendszergazdák
* Bármely (nem rendszergazda jogosultságú) felhasználó hozzáfér a saját bejelentkezéseihez 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?
* A bérlőjének prémium szintű Azure AD-licenccel kell rendelkeznie az összes bejelentkezési tevékenység jelentésének megtekintéséhez


## <a name="signs-in-activities"></a>Bejelentkezési tevékenységek

A felhasználók bejelentkezési jelentésében szereplő információkból az alábbi kérdésekre kaphat választ:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

A **Bejelentkezések** menüponton át vezet az út a bejelentkezési tevékenység adataihoz, a menüpont az **Azure Active** Tevékenység szakaszában található.


![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/61.png "Sign-in activity")


Az auditnapló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- a kapcsolódó felhasználó
- az alkalmazás, amelybe a felhasználó bejelentkezett
- a bejelentkezési állapot
- a bejelentkezési időpont

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/41.png "Sign-in activity")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/19.png "Sign-in activity")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/42.png "Sign-in activity")

A listanézet egyik elemére kattintva megtekintheti annak elérhető összes részletét.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/43.png "Sign-in activity")


## <a name="filtering-sign-in-activities"></a>A bejelentkezési tevékenységek szűrése

A jelentésben lévő adatok megfelelő szintű szűkítéséhez az alábbi mezőkkel szűrheti a bejelentkezési adatokat:

- Időintervallum
- Felhasználó
- Alkalmazás
- Ügyfél
- Bejelentkezési állapot

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/44.png "Sign-in activity")


Az **Időintervallum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek:

- 1 hónap
- 7 nap
- 24 óra
- Egyéni

Egyéni időkeret kiválasztásakor beállíthatja a kezdő és a záró időpontot.

A **Felhasználó** szűrővel egy konkrét felhasználó nevét vagy egyszerű felhasználónevét adhatja meg.

Az **Alkalmazás** szűrővel egy konkrét alkalmazás nevét adhatja meg.

Az **Ügyfél** szűrővel egy konkrét eszközhöz tartozó információt adhat meg.

A **Bejelentkezési állapot** szűrővel az alábbi szűrők egyikét választhatja ki:

- Összes
- Sikeres
- Hiba


## <a name="sign-in-activities-shortcuts"></a>Bejelentkezési tevékenységek parancsikonjai

Az Azure Active Directory mellett az Azure Portal két további lehetőséget biztosít a bejelentkezési tevékenységek adatainak elérésére:

- Felhasználók és csoportok
- Vállalati alkalmazások


### <a name="users-and-groups-sign-ins-activities"></a>Felhasználók és csoportok bejelentkezési tevékenységei

A felhasználók bejelentkezési jelentésében szereplő információkból az alábbi kérdésekre kaphat választ:

- Milyen egy adott felhasználó bejelentkezési mintázata?
- Hány felhasználó jelentkezett be egy adott héten?
- Milyen állapotúak ezek a bejelentkezések?



Az adatok megtekintését a felhasználók bejelentkezési grafikonjával kezdheti, amely az **Áttekintés** szakaszban, a **Felhasználók és csoportok** területen.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/45.png "Sign-in activity")

A felhasználók bejelentkezési grafikonja az összes felhasználó bejelentkezéseinek összesítését ábrázolja egy adott időszakban. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/46.png "Sign-in activity")

A bejelentkezési grafikon egyik napjára kattintva részletes listát kap az adott nap bejelentkezési tevékenységeiről.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/41.png "Sign-in activity")

A bejelentkezési tevékenységek listájának minden sora részletes információkat tartalmaz a kijelölt bejelentkezésről:

* Ki jelentkezett be?
* Mi volt a kapcsolódó egyszerű felhasználónév?
* Melyik alkalmazás volt a bejelentkezés célja?
* Mi a bejelentkezéshez tartozó IP-cím?
* Mi volt a bejelentkezés állapota?

A **Bejelentkezések** lehetőség teljes körű áttekintést biztosít az összes felhasználói bejelentkezésről.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/51.png "Sign-in activity")



## <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai

A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* Melyik a szervezet 3 legnépszerűbb alkalmazása?
* Nemrégiben új alkalmazást adtam ki. Mennyire sikeres?

Az adatok megtekintését a szervezet az elmúlt 30 nap alatt legnépszerűbb 3 alkalmazásáról szóló jelentéssel kezdheti az **Áttekintés** szakaszban, a **Vállalati alkalmazások** területen.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/64.png "Sign-in activity")

A 3 legnépszerűbb alkalmazásba való bejelentkezések heti összesítő grafikonja egy adott időszak során. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/47.png "Sign-in activity")

Igény esetén egy adott alkalmazást is kiemelhet.


![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Jelentéskészítés")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.


![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/48.png "Sign-in activity")


A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins/49.png "Sign-in activity")



## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a bejelentkezési tevékenységek hibakódjairól, lásd: [Bejelentkezési tevékenységekre vonatkozó jelentések hibakódjai az Azure Active Directory portálon](active-directory-reporting-activity-sign-ins-errors.md).

