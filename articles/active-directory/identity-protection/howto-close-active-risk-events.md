---
title: Aktív kockázati események lezárása Azure Active Directory Identity Protectionban | Microsoft Docs
description: Ismerje meg az aktív kockázati eseményekkel kapcsolatos lehetőségeket.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5e24c12b72852ee7009533c8dc24d231fe636f2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333999"
---
# <a name="how-to-close-active-risk-events"></a>kézikönyv: Az aktív kockázati események bezárása

A [kockázati események](../reports-monitoring/concept-risk-events.md)Azure Active Directory észlelik a potenciálisan feltört felhasználói fiókok kijelzőit. Rendszergazdaként szeretné lezárni az összes kockázati eseményt, hogy az érintett felhasználók többé ne legyenek kitéve.

Ez a cikk áttekintést nyújt az aktív kockázati események bezárásához szükséges további lehetőségekről.

## <a name="options-to-close-risk-events"></a>A kockázati események bezárásának lehetőségei 

A kockázati esemény állapota **aktív** vagy lezárt. Minden aktív kockázati esemény hozzájárul a felhasználói kockázati szint nevű érték kiszámításához. A felhasználói kockázati szint egy kijelző (alacsony, közepes és magas), amely a fiók biztonságának valószínűségét jelzi. 

Az aktív kockázati események bezárásához a következő lehetőségek állnak rendelkezésre:

- Jelszó-visszaállítás megkövetelése felhasználói kockázati házirenddel
- Jelszó manuális alaphelyzetbe állítása
- Az összes kockázati esemény bezárása 
- Az egyes kockázati események manuális lezárása

## <a name="require-password-reset-with-a-user-risk-policy"></a>Jelszó-visszaállítás megkövetelése felhasználói kockázati házirenddel

A [felhasználói kockázat feltételes hozzáférési szabályzatának](howto-user-risk-policy.md)konfigurálásával jelszó megváltoztatására van szükség, ha a rendszer automatikusan észlel egy adott felhasználói kockázati szintet. 

![Másik jelszó kérése](./media/howto-close-active-risk-events/13.png)

A jelszó alaphelyzetbe állítása lezárja a kapcsolódó felhasználó összes aktív kockázati eseményét, és biztonságos állapotba helyezi vissza az identitást. A felhasználói kockázati házirend használata az aktív kockázati események bezárásának előnyben részesített módja, mivel ez a módszer automatizált. Az érintett felhasználó és az ügyfélszolgálat vagy a rendszergazda között nincs szükség beavatkozásra.

A felhasználói kockázati házirend használata azonban nem mindig alkalmazható. Ez például a következőkre vonatkozik:

- Azok a felhasználók, akik nem regisztráltak a többtényezős hitelesítéshez (MFA).
- A törölt aktív kockázati eseményekkel rendelkező felhasználók.
- Egy vizsgálat, amely azt mutatja, hogy egy jelentett kockázati eseményt hajtottak végre a legitim felhasználó.

## <a name="manual-password-reset"></a>Jelszó manuális alaphelyzetbe állítása

Ha a jelszó alaphelyzetbe állítása a felhasználói kockázati házirend használatával nem lehetséges, akkor az összes kockázati eseményt lezárta egy manuális jelszó-visszaállítással.

![Másik jelszó kérése](./media/howto-close-active-risk-events/04.png)

A kapcsolódó párbeszédpanel két különböző módszert biztosít a jelszó alaphelyzetbe állításához:

![Másik jelszó kérése](./media/howto-close-active-risk-events/05.png)

**Ideiglenes jelszó létrehozása** – ideiglenes jelszó létrehozásával azonnal visszaállíthatja az identitást biztonságos állapotba. Ennek a módszernek az érintett felhasználókkal való interakcióra van szüksége, mivel tudniuk kell, hogy mi az ideiglenes jelszó. Például elküldheti az új ideiglenes jelszót egy másodlagos e-mail-címre a felhasználónak vagy a felhasználó felettesének. Mivel a jelszó ideiglenes, a rendszer a következő bejelentkezéskor megkéri a felhasználót a jelszó módosítására.

**A jelszó** alaphelyzetbe állításának megkövetelése a felhasználótól – a jelszavak alaphelyzetbe állítását igénylő felhasználók számára lehetővé teszi az önhelyreállítást az ügyfélszolgálat vagy a rendszergazda segítsége nélkül A felhasználói kockázati házirendhez hasonlóan ez a módszer csak az MFA-hoz regisztrált felhasználókra vonatkozik. Az MFA-hoz még nem regisztrált felhasználók esetében ez a lehetőség nem érhető el.

## <a name="dismiss-all-risk-events"></a>Az összes kockázati esemény bezárása

Ha a jelszó alaphelyzetbe állítása nem lehetséges, akkor az összes kockázati eseményt el is zárja. 

![Másik jelszó kérése](./media/howto-close-active-risk-events/03.png)

Ha az **összes esemény**elvetése gombra kattint, az összes esemény be van zárva, és az érintett felhasználó már nem veszélyeztetett. Mivel azonban ez a módszer nem befolyásolja a meglévő jelszót, nem vonja vissza a kapcsolódó identitást biztonságos állapotba. Ennek a módszernek az előnyben részesített használati esete egy aktív kockázati eseményekkel rendelkező törölt felhasználó. 

## <a name="close-individual-risk-events-manually"></a>Az egyes kockázati események manuális lezárása

Az egyes kockázati eseményeket manuálisan is lezárhatja. A kockázati események manuális bezárásával csökkentheti a felhasználói kockázati szintet. A kockázati események általában manuálisan vannak lezárva egy kapcsolódó vizsgálatra válaszul. Ha például egy felhasználóval beszél, az azt mutatja, hogy egy aktív kockázati esemény már nem szükséges. 
 
A kockázati események manuális bezárásakor dönthet úgy, hogy a következő műveletek bármelyikét elvégezte a kockázati esemény állapotának módosításához:

![Műveletek](./media/howto-close-active-risk-events/06.png)

- **Megoldás** – ha a kockázati esemény kivizsgálását követően egy megfelelő szervizelési műveletet végzett az Identity protectionn kívül, és úgy gondolja, hogy a kockázati eseményt lezártnak kell tekinteni, és megoldottként kell megjelölni az eseményt. A megoldott események beállíthatják a kockázati esemény állapotát, és a kockázati esemény már nem járul hozzá a felhasználói kockázathoz.
- **Megjelölés hamis értékként** – bizonyos esetekben előfordulhat, hogy egy kockázati eseményt vizsgál, és felderíti, hogy nem a kockázatos jelöléssel lett megjelölve. Az ilyen előfordulások számának csökkentéséhez a kockázati eseményt hamis-pozitív értékre kell megjelölni. Ez a művelet segít a gépi tanulási algoritmusok számára a hasonló események jövőbeli besorolásának fejlesztésében. A hamis pozitív események állapota le van zárva, és többé nem járul hozzá a felhasználói kockázathoz.
- **Figyelmen kívül hagyás** – ha nem végez szervizelési műveletet, de azt szeretné, hogy a rendszer eltávolítsa a kockázati eseményt az aktív listából, megjelölheti a kockázati eseményt, és a rendszer bezárja az esemény állapotát. A figyelmen kívül hagyott események nem járulnak hozzá a felhasználói kockázatokhoz. Ezt a lehetőséget csak szokatlan körülmények között lehet használni.
- **Újraaktiválás** – a manuálisan lezárt kockázati események (a megoldás kiválasztásával, hamis pozitív vagy figyelmen kívül hagyása) újra aktiválható, az esemény állapotának aktívra állítása. Az újraaktivált kockázati események hozzájárulnak a felhasználói kockázati szint kiszámításához. Szervizelés során lezárt kockázati események (például biztonságos jelszó-visszaállítás) nem aktiválható újra.

## <a name="next-steps"></a>További lépések

A Azure AD Identity Protection áttekintéséhez tekintse meg a [Azure ad Identity Protection áttekintését](overview.md).
