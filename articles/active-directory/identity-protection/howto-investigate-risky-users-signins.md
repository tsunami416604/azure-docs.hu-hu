---
title: Kockázatos felhasználók és bejelentkezések vizsgálata Azure Active Directory Identity Protectionben (frissítve) | Microsoft Docs
description: Ismerje meg, hogyan vizsgálja ki a kockázatos felhasználókat és a bejelentkezéseket Azure Active Directory Identity Protectionben (frissítve).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c89658e962654f005eaee5ceff220d5fb343e86e
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370328"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>kézikönyv: Kockázatos felhasználók és bejelentkezések vizsgálata 

A kockázatos bejelentkezések és a kockázatos felhasználók jelentéseit követve megvizsgálhatja és betekintést nyerhet a környezetbe. A kockázatos bejelentkezések és felhasználók szűrése és rendezése révén jobban megismerheti a potenciális behatolást a szervezetben. 

## <a name="risky-users-report"></a>Kockázatos felhasználók jelentés

A kockázatos felhasználók jelentés által biztosított információkkal a következő kérdésekre kaphat választ:

- Mely felhasználók számára magas a kockázat?
- Mely felhasználók rendelkeznek szervizelési kockázattal?

A jelentés első belépési pontja a biztonság lapon lévő **vizsgálat** szakasz.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/01.png)

A kockázatos felhasználók jelentés alapértelmezett nézete a következőket jeleníti meg:

- Name (Név)
- Kockázat állapota
- Kockázati szint
- Kockázat részletei
- Kockázat legutóbbi frissítése
- Type
- Állapot

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/03.png)

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/04.png)

Az oszlopok párbeszédpanel lehetővé teszi további mezők megjelenítését vagy a már megjelenített mezők eltávolítását.

A listanézet egyik elemére kattintva megtekintheti az összes elérhető részletét vízszintes nézetben.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/05.png)

A részletek nézet a következőket jeleníti meg:

- Alapvető adatok
- Legutóbbi kockázatos bejelentkezések
- Bejelentkezéshez nem hozzárendelt kockázatos események
- Kockázati előzmények

Emellett a következőket teheti:

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/08.png)

- Az összes bejelentkezési parancsikon megtekintése az adott felhasználó bejelentkezési jelentésének megtekintéséhez.
- Az összes kockázatos bejelentkezés megtekintéséhez tekintse meg az adott felhasználó számára kockázatnak kitett összes bejelentkezést.
- A felhasználó jelszavának alaphelyzetbe állítása, ha úgy véli, hogy a felhasználó identitása sérült.
- Ha úgy gondolja, hogy a felhasználó aktív kockázati eseményeinek értéke hamis, a felhasználói kockázat elvetése. További információkért tekintse [meg a Azure ad Identity Protection kockázati eseményeivel](howto-provide-risk-event-feedback.md)kapcsolatos visszajelzéseket ismertető cikket.

### <a name="filter-risky-users"></a>Kockázatos felhasználók szűrése

A jelentett adat leszűkíthető egy olyan szintre, amely az Ön számára működik, a következő alapértelmezett mezők használatával szűrheti a kockázatos felhasználói adataikat:

- Name (Név)
- Felhasználónév
- Kockázat állapota
- Kockázati szint
- Type
- Állapot

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/06.png)

A **név** szűrő segítségével megadhatja a felhasználó nevét vagy egyszerű felhasználónevét (UPN).

A **kockázati állapot** szűrővel a következőket választhatja ki:

- Kockázatos
- Kijavítva
- Elvetve

A **kockázati szint** szűrővel a következőket választhatja ki:

- Magas
- Közepes
- Alacsony

A **Type (típus** ) szűrő segítségével a következőket választhatja ki:

- Vendég
- Tag

Az **állapot** szűrő segítségével a következőket választhatja ki:

- Törölve
- Aktív

### <a name="download-risky-users-data"></a>Kockázatos felhasználókra vonatkozó adatletöltés letöltése

Ha a Azure Portalon kívül szeretné használni, akkor letöltheti a kockázatos felhasználói adatmennyiséget. A letöltés gombra kattintva létrejön egy CSV-fájl a legfrissebb 2 500-rekordokból. 

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/07.png)

A lista nézetét az eszköztár oszlopok elemére kattintva szabhatja testre.
 
További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.
 
Ha többet szeretne megtudni a kockázatos felhasználóról, kattintson a részletek elemre a kibontásához.

## <a name="risky-sign-ins-report"></a>Kockázatos bejelentkezési jelentések

A kockázatos bejelentkezési jelentés által nyújtott információk alapján választ kaphat a következő kérdésekre:

- Hány sikeres bejelentkezés történt az elmúlt hét névtelen IP-címe kockázati eseményeivel?
- Mely felhasználókat erősítették meg az elmúlt hónapban?
- Mely felhasználók voltak kockázatos bejelentkezések az Office 365 portálra?

A jelentés első belépési pontja a biztonság lapon lévő **vizsgálat** szakasz.

![Kockázatos bejelentkezési jelentések](./media/howto-investigate-risky-users-signins/02.png)

A kockázatos bejelentkezések jelentésének alapértelmezett nézete a következőket jeleníti meg:

- Date
- Felhasználó
- Alkalmazás
- Bejelentkezési állapot
- Kockázat állapota
- Kockázati szint (aggregált)
- Kockázati szint (valós idejű)
- Feltételes hozzáférés
- MFA szükséges  

![Kockázatos bejelentkezési jelentések](./media/howto-investigate-risky-users-signins/09.png)

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/11.png)

Az oszlopok párbeszédpanel lehetővé teszi további mezők megjelenítését vagy a már megjelenített mezők eltávolítását.

A listanézet egyik elemére kattintva megtekintheti az összes elérhető részletét vízszintes nézetben.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/12.png)

A részletek nézet a következőket jeleníti meg:

- Alapvető adatok
- Eszközadatok
- Kockázati adatok
- MFA-adatok
- Feltételes hozzáférés

Emellett a következőket teheti:

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/13.png)

- Feltörés megerősítése 
- Megerősítés biztonságosként

További információkért tekintse [meg a Azure ad Identity Protection kockázati eseményeivel](howto-provide-risk-event-feedback.md)kapcsolatos visszajelzéseket ismertető cikket.

### <a name="filter-risky-sign-ins"></a>Kockázatos bejelentkezések szűrése

A jelentett adat leszűkíthető egy olyan szintre, amely az Ön számára működik, a következő alapértelmezett mezők használatával szűrheti a kockázatos felhasználói adataikat:

- Felhasználó
- Alkalmazás
- Bejelentkezési állapot
- Kockázat állapota
- Kockázati szint (aggregált)
- Kockázati szint (valós idejű)
- Feltételes hozzáférés
- Date
- Kockázati szint típusa

![Kockázatos bejelentkezési jelentések](./media/howto-investigate-risky-users-signins/14.png)

A **név** szűrő segítségével megadhatja a felhasználó nevét vagy egyszerű felhasználónevét (UPN).

Az **alkalmazás** szűrővel megadhatja azt a felhőalapú alkalmazást, amellyel a felhasználó megpróbált elérni.

A **Bejelentkezési állapot** szűrővel az alábbi lehetőségek közül választhat:

- Összes
- Sikeres
- Hiba

A **kockázati állapot** szűrővel a következőket választhatja ki:

- Kockázatos
- Feltörés megerősítve
- Megerősített biztonságos
- Elvetve
- Kijavítva

A **kockázati szint (aggregált)** szűrő segítségével a következőket választhatja ki:

- Magas
- Közepes
- Alacsony

A **kockázati szint (valós idejű)** szűrő segítségével a következőket választhatja ki:

- Magas
- Közepes
- Alacsony

A **feltételes hozzáférés** szűrővel a következőket választhatja ki:

- Összes
- Nincs alkalmazva
- Siker
- Hiba

A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.
Lehetséges értékek a következők:

- Az elmúlt 1 hónap
- Az elmúlt 7 nap
- Utolsó 24 óra
- Egyéni időintervallum

### <a name="download-risky-sign-ins-data"></a>Kockázatos bejelentkezési adatfájlok letöltése

Ha a Azure Portalon kívül szeretné használni, akkor letöltheti a kockázatos bejelentkezési adatmennyiséget. A letöltés gombra kattintva létrejön egy CSV-fájl a legfrissebb 2 500-rekordokból. 

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/15.png)

## <a name="next-steps"></a>További lépések

A Azure AD Identity Protection áttekintéséhez tekintse meg a [Azure ad Identity Protection áttekintését](overview-v2.md).
