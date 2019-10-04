---
title: Aktív kockázati észlelések lezárása Azure Active Directory Identity Protectionban | Microsoft Docs
description: Ismerje meg az aktív kockázatok észlelésével kapcsolatos lehetőségeket.
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
ms.openlocfilehash: d24ec94d0381fc2e79fdef97b6d525b7cec33fef
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126470"
---
# <a name="how-to-close-active-risk-detections"></a>kézikönyv: Aktív kockázati észlelések lezárása

A [kockázati észlelések](../reports-monitoring/concept-risk-events.md)Azure Active Directory észleli a potenciálisan feltört felhasználói fiókok kijelzőit. Rendszergazdaként az összes kockázati észlelést le szeretné állítani, hogy az érintett felhasználók többé ne legyenek kitéve.

Ez a cikk áttekintést nyújt az aktív kockázati észlelések lezárásához szükséges további lehetőségekről.

## <a name="options-to-close-risk-detections"></a>A kockázati észlelések lezárásának lehetőségei 

A kockázatok észlelésének állapota **aktív** vagy lezárt. Minden aktív kockázati észlelés hozzájárul a felhasználói kockázati szint nevű érték kiszámításához. A felhasználói kockázati szint egy kijelző (alacsony, közepes és magas), amely a fiók biztonságának valószínűségét jelzi. 

Az aktív kockázati észlelések lezárásához a következő lehetőségek közül választhat:

- Jelszó-visszaállítás megkövetelése felhasználói kockázati házirenddel
- Jelszó manuális alaphelyzetbe állítása
- Az összes kockázati észlelés bezárása 
- Egyéni kockázati észlelések manuális lezárása

## <a name="require-password-reset-with-a-user-risk-policy"></a>Jelszó-visszaállítás megkövetelése felhasználói kockázati házirenddel

A [felhasználói kockázat feltételes hozzáférési szabályzatának](howto-user-risk-policy.md)konfigurálásával jelszó megváltoztatására van szükség, ha a rendszer automatikusan észlel egy adott felhasználói kockázati szintet. 

![Másik jelszó kérése](./media/howto-close-active-risk-events/13.png)

A jelszó alaphelyzetbe állítása lezárja a kapcsolódó felhasználó összes aktív kockázati eseményét, és biztonságos állapotba helyezi vissza az identitást. A felhasználói kockázati házirend használata az aktív kockázati észlelések bezárására szolgáló előnyben részesített módszer, mivel ez a módszer automatizált. Az érintett felhasználó és az ügyfélszolgálat vagy a rendszergazda között nincs szükség beavatkozásra.

A felhasználói kockázati házirend használata azonban nem mindig alkalmazható. Ez például a következőkre vonatkozik:

- Azok a felhasználók, akik nem regisztráltak a többtényezős hitelesítéshez (MFA).
- A törölt aktív kockázati észlelésekkel rendelkező felhasználók.
- Egy vizsgálat, amely azt mutatja, hogy a megbízható felhasználó a jelentett kockázati észlelést végezte.

## <a name="manual-password-reset"></a>Jelszó manuális alaphelyzetbe állítása

Ha a jelszó alaphelyzetbe állítása a felhasználói kockázati házirend használatával nem lehetséges, akkor az összes kockázati észlelést beolvashatja egy manuális jelszó-visszaállítással lezárt felhasználó számára.

![Másik jelszó kérése](./media/howto-close-active-risk-events/04.png)

A kapcsolódó párbeszédpanel két különböző módszert biztosít a jelszó alaphelyzetbe állításához:

![Másik jelszó kérése](./media/howto-close-active-risk-events/05.png)

**Ideiglenes jelszó létrehozása** – ideiglenes jelszó létrehozásával azonnal visszaállíthatja az identitást biztonságos állapotba. Ennek a módszernek az érintett felhasználókkal való interakcióra van szüksége, mivel tudniuk kell, hogy mi az ideiglenes jelszó. Például elküldheti az új ideiglenes jelszót egy másodlagos e-mail-címre a felhasználónak vagy a felhasználó felettesének. Mivel a jelszó ideiglenes, a rendszer a következő bejelentkezéskor megkéri a felhasználót a jelszó módosítására.

**A jelszó** alaphelyzetbe állításának megkövetelése a felhasználótól – a jelszavak alaphelyzetbe állítását igénylő felhasználók számára lehetővé teszi az önhelyreállítást az ügyfélszolgálat vagy a rendszergazda segítsége nélkül A felhasználói kockázati házirendhez hasonlóan ez a módszer csak az MFA-hoz regisztrált felhasználókra vonatkozik. Az MFA-hoz még nem regisztrált felhasználók esetében ez a lehetőség nem érhető el.

## <a name="dismiss-all-risk-detections"></a>Az összes kockázati észlelés bezárása

Ha a jelszó alaphelyzetbe állítása nem lehetséges, zárja be az összes kockázati észlelést is. 

![Másik jelszó kérése](./media/howto-close-active-risk-events/03.png)

Ha az **összes esemény**elvetése gombra kattint, az összes esemény be van zárva, és az érintett felhasználó már nem veszélyeztetett. Mivel azonban ez a módszer nem befolyásolja a meglévő jelszót, nem vonja vissza a kapcsolódó identitást biztonságos állapotba. Ennek a módszernek az előnyben részesített felhasználási esete egy aktív kockázati észleléssel rendelkező törölt felhasználó. 

## <a name="close-individual-risk-detections-manually"></a>Egyéni kockázati észlelések manuális lezárása

Az egyes kockázati észleléseket manuálisan is lezárhatja. A kockázati észlelések manuális bezárásával csökkentheti a felhasználói kockázati szintet. A kockázati észlelések általában manuálisan vannak bezárva egy kapcsolódó vizsgálatra válaszul. Ha például egy felhasználóval beszél, az aktív kockázatok észlelése többé nem szükséges. 
 
A kockázati észlelések manuális bezárásakor dönthet úgy, hogy a következő műveletek bármelyikét elvégezte a kockázatkezelés állapotának módosításához:

![Műveletek](./media/howto-close-active-risk-events/06.png)

- **Megoldás** – ha a kockázatfelmérés kivizsgálása után egy megfelelő szervizelési műveletet végzett az Identity protectionn kívül, és úgy gondolja, hogy a kockázat észlelését zártnak kell tekinteni, az eseményt megoldottként kell megjelölni. A megoldott események beállíthatják a kockázat észlelésének állapotát, és a kockázat észlelése már nem járul hozzá a felhasználói kockázathoz.
- **Megjelölés hamis értékként** – bizonyos esetekben előfordulhat, hogy a kockázatok észlelését vizsgálja, és azt is észleli, hogy a kockázatos jelölése helytelen. Az ilyen előfordulások számának csökkentése érdekében a kockázat észlelését hamis-pozitív értékre kell megjelölni. Ez a művelet segít a gépi tanulási algoritmusok számára a hasonló események jövőbeli besorolásának fejlesztésében. A hamis pozitív események állapota le van zárva, és többé nem járul hozzá a felhasználói kockázathoz.
- **Figyelmen kívül hagyás** – ha nem végez szervizelési műveletet, de azt szeretné, hogy a kockázat észlelése el legyen távolítva az aktív listából, megadhatja a kockázatok észlelésének mellőzését, és az esemény állapota le lesz zárva. A figyelmen kívül hagyott események nem járulnak hozzá a felhasználói kockázatokhoz. Ezt a lehetőséget csak szokatlan körülmények között lehet használni.
- **Újraaktiválás** – a manuálisan lezárt kockázati észlelések (a megoldás kiválasztásával, a hamis pozitív vagy figyelmen kívül hagyása) újraaktiválható, az esemény állapotának aktívra állítása. Az újraaktivált kockázati észlelések hozzájárulnak a felhasználói kockázati szint kiszámításához. A kockázatos észlelések szervizelésen keresztüli lezárása (például biztonságos jelszó-visszaállítás) nem aktiválható újra.

## <a name="next-steps"></a>További lépések

A Azure AD Identity Protection áttekintéséhez tekintse meg a [Azure ad Identity Protection áttekintését](overview.md).
