---
title: Kockázatos felhasználók és bejelentkezések Azure Active Directory identity Protection (frissítve) |} A Microsoft Docs
description: Ismerje meg a kockázatos felhasználók és bejelentkezések Azure Active Directory identity Protection (frissítve).
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.author: markvi
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.date: 01/25/2019
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.reviewer: raluthra
ms.openlocfilehash: f72819bacd7c65ced7ccff3665789d3b96c5e30f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478365"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>kézikönyv: Kockázatos felhasználók és bejelentkezések vizsgálata 


A kockázatos bejelentkezések és a kockázatos felhasználók jelentésekkel vizsgálata és kockázati folyamatnaplókkal információt szerezhet a környezetben. Lehetővé teszi az szűrését és rendezését, a kockázatos bejelentkezések és felhasználók jobban megérthetők esetleges behatolás a szervezetben. 


## <a name="risky-users-report"></a>Kockázatos felhasználók jelentés

A kockázatos felhasználók jelentés által biztosított információkkal mint például megtalálhatja kérdésekre kaphat választ:

- Magas kockázatú felhasználók közül ki?
- Mely felhasználók rendelkeznek egy kockázati állapotának szervizelt?



Ez a jelentés első belépési pontjához van a **vizsgálat** szakasz a biztonság lapon.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/01.png)


A kockázatos felhasználók jelentés egy alapértelmezett-nézetet tartalmaz:

- Name (Név)

- Kockázat állapota

- Kockázati szint

- Kockázat részletei

- Kockázat legutóbbi frissítése

- Typo

- status
 

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/03.png)


A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/04.png)

Az oszlopok párbeszédpanel lehetővé teszi további mezők megjelenítéséhez, vagy eltávolíthatja a már megjelenített mezőket.

A listanézet egyik elemére kattintva megtekintheti az összes elérhető részletét vízszintes nézetben.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/05.png)


A részletek megtekintéséhez mutat be:

- Alapvető adatok

- Legutóbbi kockázatos bejelentkezések

- Bejelentkezéshez nem hozzárendelt kockázatos események

- Kockázati előzmények



Emellett a következőket teheti:

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/08.png)

- A bejelentkezések megtekintési az adott felhasználó összes bejelentkezések helyi megtekintése.

- Tekintse meg az összes kockázatos bejelentkezések összes a bejelentkezés, hogy a felhasználó a kockázatosként megjelölt megtekintéséhez.

- A jelszó alaphelyzetbe állítása, ha úgy véli, hogy a felhasználói identitás biztonsága sérült.

- Zárja be a felhasználói kockázat, ha úgy véli, hogy egy felhasználó az aktív kockázati események-e a vakriasztások. További információkért lásd: [észlelési pontosságának növelése](howto-improve-detection-accuracy.md).



### <a name="filter-risky-users"></a>Kockázatos felhasználók szűrése

A jelentésben lévő adatok az Ön számára megfelelő szintű szűkítéséhez, a kockázatos felhasználó adatait az alábbi alapértelmezett mezőkkel szűrheti:

- Name (Név)

- Felhasználónév

- Kockázat állapota

- Kockázati szint

- Typo

- status

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/06.png)



A **neve** szűrő lehetővé teszi, hogy adja meg a nevét vagy a felhasználó egyszerű felhasználóneve (UPN) az Ön számára.


A **állapot kockázati** szűrővel kiválasztása:

- Kockázatos
- Javítva
- Elvetve


A **kockázati szint** szűrővel kiválasztása:

- Magas
- Közepes
- Alacsony


A **típus** szűrővel kiválasztása:

- Vendég
- Tag

A **állapot** szűrővel kiválasztása:

- Törölve
- Aktív


### <a name="download-risky-users-data"></a>Kockázatos felhasználók adatok letöltése

Ha azt szeretné, hogy az Azure Portalon kívül, a használata a kockázatos felhasználók adatait töltheti le. Letöltés gombra kattintva létrehoz egy CSV-fájlt a legutóbbi 5 KB rekordok. 

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/07.png)


Testre szabhatja a listanézetet az eszköztár oszlopok parancsra kattintva.
 
További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.
 
További tudnivalók a kockázatos felhasználó, kattintson a részletek navigációs menüben bontsa ki a

 



## <a name="risky-sign-ins-report"></a>Kockázatos bejelentkezési jelentések

A kockázatos bejelentkezések jelentés által biztosított információkkal mint például megtalálhatja kérdésekre kaphat választ:

- Névtelen IP-kockázati események hány sikeres bejelentkezések küldtek, amely korábban a múlt héten?

- Mely felhasználók voltak megerősítette sérült a biztonsága az elmúlt hónapban?

- Mely felhasználók az Office 365 portálra kockázatos bejelentkezések volt?




Ez a jelentés első belépési pontjához van a **vizsgálat** szakasz a biztonság lapon.

![Kockázatos bejelentkezési jelentések](./media/howto-investigate-risky-users-signins/02.png)

A kockázatos bejelentkezések jelentés rendelkezik egy adott alapértelmezett nézeten:

- Dátum

- Felhasználó

- Alkalmazás

- Bejelentkezési állapot

- Kockázat állapota

- Kockázati szint (összesítés)

- Kockázati szint (valós idejű)

- Feltételes hozzáférés

- MFA szükséges  
 

![Kockázatos bejelentkezési jelentések](./media/howto-investigate-risky-users-signins/09.png)


A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/11.png)

Az oszlopok párbeszédpanel lehetővé teszi további mezők megjelenítéséhez, vagy eltávolíthatja a már megjelenített mezőket.

A listanézet egyik elemére kattintva megtekintheti az összes elérhető részletét vízszintes nézetben.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/12.png)


A részletek megtekintéséhez mutat be:

- Alapvető adatok

- Eszközadatok

- Kockázati adatok

- MFA-adatok

- Feltételes hozzáférés





Emellett a következőket teheti:

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/13.png)

- Megerősítés kockázatosként 

- Megerősítés biztonságosként

További információkért lásd: [észlelési pontosságának növelése](howto-improve-detection-accuracy.md).




### <a name="filter-risky-sign-ins"></a>Kockázatos bejelentkezések szűrése

A jelentésben lévő adatok az Ön számára megfelelő szintű szűkítéséhez, a kockázatos felhasználó adatait az alábbi alapértelmezett mezőkkel szűrheti:

- Felhasználó
- Alkalmazás
- Bejelentkezési állapot
- Kockázat állapota
- Kockázati szint (összesítés)
- Kockázati szint (valós idejű)
- Feltételes hozzáférés
- Dátum
- Kockázati szint típusa

![Kockázatos bejelentkezési jelentések](./media/howto-investigate-risky-users-signins/14.png)



A **neve** szűrő lehetővé teszi, hogy adja meg a nevét vagy a felhasználó egyszerű felhasználóneve (UPN) az Ön számára.

A **alkalmazás** szűrő lehetővé teszi, hogy adja meg a felhőalkalmazás a felhasználó megpróbált hozzáférni.

A **Bejelentkezési állapot** szűrővel az alábbi lehetőségek közül választhat:

- Összes
- Sikeres
- Hiba


A **állapot kockázati** szűrővel kiválasztása:

- Kockázatos
- Megerősített kockázatos
- Megerősített biztonságos
- Elvetve
- Javítva


A **kockázati szint (összesítés)** szűrővel kiválasztása:

- Magas
- Közepes
- Alacsony

A **kockázati szint (valós idejű)** szűrővel kiválasztása:

- Magas
- Közepes
- Alacsony


A **feltételes hozzáférési** szűrővel kiválasztása:

- Összes
- Nem alkalmazott
- Sikeres
- Hiba


A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.
Lehetséges értékek:

- Az elmúlt 1 hónap
- Az elmúlt 7 nap
- Utolsó 24 óra
- Egyéni időintervallum





### <a name="download-risky-sign-ins-data"></a>Kockázatos bejelentkezések adatok letöltése

Ha azt szeretné, hogy az Azure Portalon kívül, a használata a kockázatos bejelentkezések adatok töltheti le. Letöltés gombra kattintva létrehoz egy CSV-fájlt a legutóbbi 5 KB rekordok. 

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/15.png)



## <a name="next-steps"></a>További lépések

Az Azure AD Identity Protection áttekintést kaphat, tekintse meg a [áttekintése az Azure AD Identity Protection](overview-v2.md).
