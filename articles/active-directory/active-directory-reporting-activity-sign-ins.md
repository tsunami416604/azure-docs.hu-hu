---
title: "Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portál előzetes kiadásában | Microsoft Docs"
description: "A bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portál előzetes kiadásában – bevezetés"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 78617dc7e3a4b6eb4fc32d32b6850b8c0832d6d8
ms.openlocfilehash: 9819c5f6a3aea53664d86e3a23b25946c0f2b731
ms.lasthandoff: 02/22/2017


---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal---preview"></a>Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portál előzetes kiadásában

Az Azure Active Directory [előzetes kiadásában](active-directory-preview-explainer.md) elérhető jelentéskészítéssel minden szükséges információhoz hozzájuthat a környezetével kapcsolatban.

Az Azure Active Directory jelentéskészítési architektúrája a következő elemekből áll:

- **Tevékenység** 
    - **Bejelentkezési tevékenységek** – A felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk
    - **Naplók** – Rendszertevékenység információk a felhasználó- és csoportfelügyeletre, valamint a felügyelt alkalmazásokra és a címtártevékenységekre vonatkozóan.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. További részletek: Kockázatos bejelentkezések.
    - **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. További részletek: Kockázatosként megjelölt felhasználók.

Ez a témakör áttekintést nyújt a bejelentkezési tevékenységekről.

## <a name="signs-in-activities"></a>Bejelentkezési tevékenységek

A felhasználók bejelentkezési jelentésében szereplő információkból az alábbi kérdésekre kaphat választ:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

Az adatok megtekintését a felhasználók bejelentkezési grafikonjával kezdheti, amely az **Áttekintés** szakaszban, a **Felhasználók és csoportok** területen.

 ![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/05.png "Jelentéskészítés")

A felhasználók bejelentkezési grafikonja az összes felhasználó bejelentkezéseinek összesítését ábrázolja egy adott időszakban. Az alapértelmezett időszak 30 nap.

![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/02.png "Jelentéskészítés")

A bejelentkezési grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/03.png "Jelentéskészítés")

A bejelentkezési tevékenységek listájának minden sora részletes információkat tartalmaz a kijelölt bejelentkezésről:

* Ki jelentkezett be?
* Mi volt a kapcsolódó egyszerű felhasználónév?
* Melyik alkalmazás volt a bejelentkezés célja?
* Mi a bejelentkezéshez tartozó IP-cím?
* Mi volt a bejelentkezés állapota?

## <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai

A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* Melyik a szervezet 3 legnépszerűbb alkalmazása?
* Nemrégiben új alkalmazást adtam ki. Mennyire sikeres?

Az adatok megtekintését a szervezet az elmúlt 30 nap alatt legnépszerűbb 3 alkalmazásáról szóló jelentéssel kezdheti az **Áttekintés** szakaszban, a **Vállalati alkalmazások** területen.

 ![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/06.png "Jelentéskészítés")

A 3 legnépszerűbb alkalmazásba való bejelentkezések heti összesítő grafikonja egy adott időszak során. Az alapértelmezett időszak 30 nap.

![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/78.png "Jelentéskészítés")

Igény esetén egy adott alkalmazást is kiemelhet.

![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Jelentéskészítés")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/top_app_sign_ins.png "Jelentéskészítés")

A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/85.png "Jelentéskészítés")

Az oszlopválasztóval kiválaszthatja a megjelenítendő adatmezőket.

![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/column_chooser.png "Jelentéskészítés")

## <a name="filtering-sign-ins"></a>Bejelentkezések szűrése
A következő mezőkkel szűrheti a bejelentkezéseket a megjelenített adatok korlátozása érdekében:

* Dátum és idő 
* Felhasználó egyszerű neve
* Alkalmazásnév
* Ügyfél neve
* Bejelentkezési állapot

![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/293.png "Jelentéskészítés")

A bejelentkezési tevékenységek bejegyzéseit konkrét bejegyzések keresésével is szűrheti.
Ezzel a keresési módszerrel konkrét **felhasználókra**, **csoportokra** vagy **alkalmazásokra** szűkítheti a bejelentkezések hatókörét.

![Jelentéskészítés](./media/active-directory-reporting-activity-sign-ins/84.png "Jelentéskészítés")


## <a name="next-steps"></a>Következő lépések
Lásd az [Azure Active Directory Reporting-útmutatót](active-directory-reporting-guide.md).


