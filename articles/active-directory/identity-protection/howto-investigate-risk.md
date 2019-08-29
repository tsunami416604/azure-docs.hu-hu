---
title: A kockázatok vizsgálata Azure Active Directory Identity Protectionben (frissítve) | Microsoft Docs
description: Ismerje meg, hogyan vizsgálhatja meg a kockázatos felhasználókat, az észleléseket és a bejelentkezéseket Azure Active Directory Identity Protectionben (frissítve).
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
ms.openlocfilehash: a6e9715b63ccfbf1da3bdaedf947ea4cd65109b7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129717"
---
# <a name="how-to-investigate-risk"></a>kézikönyv: Kockázat vizsgálata

A kockázatos bejelentkezések, a kockázatos felhasználók és a kockázati észlelési jelentések segítségével megvizsgálhatja és betekintést nyerhet a környezetbe. A kockázatos bejelentkezések, a felhasználók és az észlelések szűrése és rendezése révén jobban megismerheti a potenciális behatolást a szervezetben. 

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
- State

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/03.png)

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/04.png)

Az oszlopok párbeszédpanel lehetővé teszi további mezők megjelenítését vagy a már megjelenített mezők eltávolítását.

A listanézet egyik elemére kattintva megtekintheti az összes elérhető részletét vízszintes nézetben.

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/05.png)

A részletek nézet a következőket jeleníti meg:

- Alapvető adatok
- Legutóbbi kockázatos bejelentkezések
- Bejelentkezéshez nem társított kockázati észlelések
- Kockázati előzmények

Emellett a következőket teheti:

![Kockázatos felhasználók jelentés](./media/howto-investigate-risky-users-signins/08.png)

- Az összes bejelentkezési parancsikon megtekintése az adott felhasználó bejelentkezési jelentésének megtekintéséhez.
- Az összes kockázatos bejelentkezés megtekintéséhez tekintse meg az adott felhasználó számára kockázatnak kitett összes bejelentkezést.
- A felhasználó jelszavának alaphelyzetbe állítása, ha úgy véli, hogy a felhasználó identitása sérült.
- Ha úgy gondolja, hogy egy felhasználó aktív kockázati észlelése hamis pozitív, akkor utasítsa el a felhasználói kockázatot. További információkért tekintse [meg a Azure ad Identity Protection kockázati észlelésével](howto-provide-risk-event-feedback.md)kapcsolatos visszajelzéseket ismertető cikket.

### <a name="filter-risky-users"></a>Kockázatos felhasználók szűrése

A jelentett adat leszűkíthető egy olyan szintre, amely az Ön számára működik, a következő alapértelmezett mezők használatával szűrheti a kockázatos felhasználói adataikat:

- Name (Név)
- Felhasználónév
- Kockázat állapota
- Kockázati szint
- Type
- State

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

- Hány sikeres bejelentkezés történt a névtelen IP-címek kockázati észlelésével kapcsolatban a múlt héten?
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

További információkért tekintse [meg a Azure ad Identity Protection kockázati észlelésével](howto-provide-risk-event-feedback.md)kapcsolatos visszajelzéseket ismertető cikket.

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


## <a name="risk-detections-report"></a>Kockázati észlelési jelentés

A kockázati észlelésekről szóló jelentés betekintést nyújt a bérlő minden Identity Protection kockázatkezelési szolgáltatásával.


A kockázati észlelési jelentés alapértelmezett nézete a következőket mutatja:

- Date

- Felhasználó

- IP-cím

- Location

- Észlelés típusa

- Kockázat állapota

- Kockázati szint

- Kérés azonosítója
 

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni. Az oszlopok párbeszédpanel lehetővé teszi további mezők megjelenítését vagy a már megjelenített mezők eltávolítását.

A listanézet egyik elemére kattintva megtekintheti az összes elérhető részletét vízszintes nézetben.


A részletek nézet további információkat jelenít meg a kockázat észleléséről.

A részletek nézet a következő hivatkozásokat is tartalmazza

- Felhasználó kockázati jelentésének megtekintése
- Felhasználói bejelentkezések megtekintése
- A felhasználó kockázatos bejelentkezésének megtekintése
- Társított kockázatos bejelentkezés megtekintése
- A felhasználó kockázati észlelésének megtekintése


### <a name="filter-risk-detections"></a>Kockázati észlelések szűrése

A jelentett adat leszűkíthető egy olyan szintre, amely a következő alapértelmezett mezők használatával szűrheti a kockázat-észlelési értékeket:

- Észlelési időpont
- Észlelés típusa
- Kockázat állapota
- Kockázati szint
- Felhasználó
- Felhasználónév
- IP-cím
- Location
- Észlelési időzítés
- Tevékenység 
- Source
- Jogkivonat kiállítójának típusa
- Kérés azonosítója
- Korrelációs azonosító


Az **Észlelési idő** szűrője lehetővé teszi, hogy megadjon egy időkeretet a visszaadott adatértékhez. Ezzel a szűrővel a következőket választhatja ki:
- Az elmúlt 90 nap
- Az elmúlt 30 nap
- Az elmúlt 7 nap
- Utolsó 24 óra
- Egyéni időintervallum

Az **észlelési típus** szűrővel megadhatja a kockázat észlelésének típusát (például ismeretlen bejelentkezési tulajdonságok).

A **kockázati állapot** szűrővel a következőket választhatja ki:

- Kockázatos
- Feltörés megerősítve
- Megerősített biztonságos
- Elvetve
- Kijavítva

A **kockázati szint** szűrővel a következőket választhatja ki:

- Magas
- Közepes
- Alacsony

A **felhasználó** szűrővel megadhatja annak a felhasználónak a nevét vagy OBJEKTUMAZONOSÍTÓ-azonosítóját, akit érdekel.

A **Felhasználónév** szűrő segítségével megadhatja az Ön által törődő felhasználó egyszerű felhasználónevét (UPN).

Az **észlelési időzítési** szűrő segítségével megadhatja, hogy az észlelés valós idejű vagy offline állapotú-e. Megjegyzés: A bejelentkezési kockázati házirend segítségével valós idejű észlelésekkel is kipróbálhatja a bejelentkezéseket. 

A **tevékenység** szűrője lehetővé teszi annak megadását, hogy az észlelés egy bejelentkezéshez (például névtelen IP-címhez) vagy egy felhasználóhoz (például a kiszivárgott hitelesítő adatokhoz) legyen csatolva.

A **forrás** szűrő segítségével megadhatja a kockázatkezelés forrását (például Azure AD vagy Microsoft Cloud app Security). 

A **jogkivonat kiállítói típusának** szűrője lehetővé teszi, hogy megadja a jogkivonat kiadásának helyét (például Azure AD vagy ad összevonási szolgáltatások).


### <a name="download-risk-detections-data"></a>Kockázati észlelési adatfájlok letöltése

Ha azt szeretné, hogy a Azure Portalon kívül is működjön, töltse le a kockázati észlelési adatmennyiséget. A letöltés gombra kattintva létrejön egy CSV-fájl a legfrissebb 5 000-rekordokból. 

## <a name="next-steps"></a>További lépések

A Azure AD Identity Protection áttekintéséhez tekintse meg a [Azure ad Identity Protection áttekintését](overview-v2.md).
