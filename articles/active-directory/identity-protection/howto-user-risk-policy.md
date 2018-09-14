---
title: A felhasználói kockázati szabályzat beállítása az Azure Active Directory Identity Protection |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure AD Identity Protection felhasználói kockázati házirend.
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: bcfab9ab95e41b723cb8a8e49d7390a2894d5219
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581369"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Útmutató: A felhasználói kockázati szabályzat beállítása

Összes aktív [kockázati események](../reports-monitoring/concept-risk-events.md) , amely az Azure Active Directory által észlelt, a felhasználó hozzájárul a felhasználói kockázat nevű egy logikai fogalom. Kockázatosként megjelölt felhasználók egy mutató a egy felhasználói fiókot, előfordulhat, hogy sérült a biztonsága.

![Kockázatosként megjelölt felhasználók](./media/howto-user-risk-policy/1200.png)


## <a name="user-risk-level"></a>Felhasználói kockázati szint

Felhasználói kockázati szint annak a valószínűségét, hogy a felhasználói identitás biztonsága sérült megjelölése (magas, közepes vagy alacsony). Ez alapján lesz kiszámítva az a felhasználó identitását társított felhasználói kockázati események.

Egy kockázati esemény állapota vagy **aktív** vagy **lezárva**. Csak a kockázati események, amelyek **aktív** járulnak hozzá a felhasználó kockázati szintű számítás.

A felhasználói kockázat szintje számítja ki az alábbi ráfordítások:

* A felhasználót érintő aktív kockázati események
* Ezek az események kockázati szint
* E esetleg elvégzett szervizelési műveleteket került sor

![Felhasználói kockázat](./media/howto-user-risk-policy/1031.png "felhasználói kockázat")

A felhasználó kockázati szintek segítségével, amely meggátolja a kockázatos felhasználókat jelentkezik be feltételes hozzáférési szabályzatokat hozhat létre, vagy kényszerítse azok biztonságosan a jelszó módosítására.

## <a name="closing-risk-events-manually"></a>Kockázati események manuális lezárása

A legtöbb esetben, például a biztonságos jelszó-visszaállításra automatikus lezárása a kockázati események szervizelési műveleteket végzi el. Azonban ez valószínűleg nem mindig lehetséges.  
Ez, például a helyzet, ha:

* Az aktív kockázati események egy felhasználó törölve lett
* A vizsgálat során kiderül, hogy a jelentett kockázati esemény jogos felhasználója lett-e végre.

Mivel a kockázati események, amelyek **aktív** járulnak hozzá a felhasználói kockázat számítása, kockázati események manuális lezárása manuálisan csökkentheti a kockázati szint lehet.  
A vizsgálat során választhat tennie egy kockázati esemény állapotának módosítása ezen műveletek egyikét:

![Műveletek](./media/howto-user-risk-policy/34.png "műveletek")

* **Oldja meg** – Ha vizsgálatát, hogy egy kockázati esemény után egy megfelelő korrekciós műveletet identitásvédelem kívül tartott, és úgy gondolja, hogy a kockázati esemény tekintendők-e zárva, jelölje meg az esemény megoldva. Esemény lezárva a kockázati esemény állapotát állítja be, és a kockázati esemény már nem hozzájárul ahhoz, hogy a felhasználói kockázat feloldani.
* **Jelölje meg a hamis pozitív** – néhány esetben Ön egy kockázati esemény vizsgálata és helytelenül lett megjelölve, a kockázatos felderítése. Segíthet az ilyen előfordulások száma csökkenthető a vakriasztások kockázati esemény megjelölése. Ez segít a gépi tanulási algoritmusok hasonló események besorolása a jövőben javítása érdekében. A hamis pozitív események állapotát, hogy **lezárva** és azok többé nem hozzájárul ahhoz, hogy a felhasználói kockázat.
* **Hagyja figyelmen kívül** – Ha a szervizelés semmit nem tett, de szeretné, el kell távolítani a aktív listából a kockázati esemény egy kockázati esemény figyelmen kívül hagyása jelölheti meg, és be lesz zárva az esemény állapota. Felhasználói kockázat nem jelentenek figyelmen kívül hagyta eseményt. Ez a beállítás csak szokatlan körülmények között nem használható.
* **Aktiválja újra** -kockázati eseményeket, amelyek manuálisan lettek lezárva (kiválasztásával **megoldásához**, **vakriasztás**, vagy **figyelmen kívül hagyása**) aktiválhatók, az esemény beállítása vissza a **aktív**. Újraaktivált kockázati események járulnak hozzá a felhasználó kockázati szintű számítás. Nem lehet újraaktiválni (például egy biztonságos jelszó-visszaállítási) szervizelés útján lezárt kockázati események.

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

1. Az a **Azure AD Identity Protection** panel alatt **vizsgálat**, kattintson a **kockázati események**.

    ![Manuális jelszó-visszaállítási](./media/howto-user-risk-policy/1002.png "manuális jelszó alaphelyzetbe állítása")
2. Az a **kockázati események** listában, kattintson a kockázatot jelent.

    ![Manuális jelszó-visszaállítási](./media/howto-user-risk-policy/1003.png "manuális jelszó alaphelyzetbe állítása")
3. A kockázati panelen kattintson a jobb gombbal a felhasználó.

    ![Manuális jelszó-visszaállítási](./media/howto-user-risk-policy/1004.png "manuális jelszó alaphelyzetbe állítása")

## <a name="closing-all-risk-events-for-a-user-manually"></a>Egy felhasználó az összes kockázati események manuális lezárása
Helyett manuálisan bezárása külön-külön egy felhasználóval kapcsolatos kockázati eseményeket, az Azure Active Directory Identity Protection emellett lehetőséget biztosít a metódus egyetlen kattintással a felhasználó az összes esemény bezárása.

![Műveletek](./media/howto-user-risk-policy/2222.png "műveletek")

Amikor rákattint **az összes esemény elvetését**, az összes esemény bezárása és az érintett felhasználó már nem veszélyben van.

## <a name="remediating-user-risk-events"></a>Védekezhet felhasználói kockázati események

A szervizelés egy olyan műveletet, biztonságos identitás vagy egy eszköz, amely korábban gyanús vagy ismert legyen feltörni. A javítási művelet visszaállítja a identitás vagy az eszköz biztonságos állapotban, és oldja fel az előző kockázati események a identitás vagy az eszköz társítva.

Felhasználói kockázati események javítása, a következőket teheti:

* Hajtsa végre a biztonságos jelszó-visszaállításra felhasználói kockázati események manuális javítása
* Biztonsági szabályzat felhasználói kockázat csökkentése, vagy automatikusan kijavítani a felhasználói kockázati események konfigurálása
* A fertőzött eszköz újratelepítése rendszerképből  

### <a name="manual-secure-password-reset"></a>Biztonságos jelszó manuális alaphelyzetbe állítása
Egy biztonságos jelszó-visszaállítási egy hatékony szervizelési számos kockázati eseményekhez, és hajtja végre, ha automatikusan bezárja a kockázati eseményeket, és újraszámítja a felhasználói kockázat szintje. Az Identity Protection irányítópultjának használatával jelszó-visszaállításra egy kockázatos felhasználó kezdeményezhet.

A kapcsolódó párbeszédpanel kétféleképpen jelszó alaphelyzetbe állítása itt:

**Jelszó alaphelyzetbe állítása** – válassza ki **a felhasználók új jelszót kérnek** , hogy a felhasználó helyi helyreállíthatja, ha a felhasználó regisztrálta a multi-factor Authentication hitelesítéshez. A felhasználó tovább bejelentkezés során, a felhasználó lesz a multi-factor authentication kihívást sikeresen megoldásához szükséges, és ezután kényszerített a jelszó módosítására. Ez a lehetőség nem érhető el, ha a felhasználói fiók még nem regisztrált multi-factor Authentication hitelesítést.

**Ideiglenes jelszó** – ki **ideiglenes jelszó létrehozása** azonnal érvényteleníti a meglévő jelszót, és hozzon létre egy új, ideiglenes jelszót a felhasználónak. A felhasználó másodlagos e-mail cím vagy a felhasználó felettesét elküldi az új, ideiglenes jelszót. A jelszó nem átmeneti, mert a felhasználó bekéri után jelentkezzen be a jelszó módosítására.

![A házirend](./media/howto-user-risk-policy/1005.png "házirend")

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

1. Az a **Azure AD Identity Protection** panelen kattintson a **kockázatosként megjelölt felhasználók**.

    ![Manuális jelszó-visszaállítási](./media/howto-user-risk-policy/1006.png "manuális jelszó alaphelyzetbe állítása")
2. A felhasználók a listában legalább egy kockázati események rendelkező felhasználót válasszon.

    ![Manuális jelszó-visszaállítási](./media/howto-user-risk-policy/1007.png "manuális jelszó alaphelyzetbe állítása")
3. A felhasználói panelen kattintson a **jelszó alaphelyzetbe állítása**.

    ![Manuális jelszó-visszaállítási](./media/howto-user-risk-policy/1008.png "manuális jelszó alaphelyzetbe állítása")

## <a name="user-risk-security-policy"></a>Felhasználói kockázati biztonsági házirend
A felhasználó kockázati biztonsági házirend egy feltételes hozzáférési szabályzatot, amely kiértékeli a kockázati szintjét egy adott felhasználó számára, és előre meghatározott feltételeknek és szabályoknak alapján szervizelési és kockázatcsökkentési műveleteket hajt végre.

![Felhasználói kockázati házirend](./media/howto-user-risk-policy/1009.png "felhasználói kockázati házirend")

Az Azure AD Identity Protection segítségével kockázatcsökkentési és, hogy lehetővé teszi, hogy a kockázatosként megjelölt felhasználók szervizelése kezelheti:

* Állítsa be a felhasználókat és csoportokat a szabályzat vonatkozik:

    ![Felhasználói kockázati házirend](./media/howto-user-risk-policy/1010.png "felhasználói kockázati házirend")
* A felhasználó kockázati szint küszöbértéke (alacsony, közepes vagy magas), amely elindítja a szabályzat beállítása:

    ![Felhasználói kockázati házirend](./media/howto-user-risk-policy/1011.png "felhasználói kockázati házirend")
* Állítsa be az aktiválásakor a szabályzatot érvényesíteni kívánt vezérlőket:

    ![Felhasználói kockázati házirend](./media/howto-user-risk-policy/1012.png "felhasználói kockázati házirend")
* Váltás a házirend állapota:

    ![Felhasználói kockázati házirend](./media/howto-user-risk-policy/403.png "MFA-regisztráció")
* Tekintse át, és azt aktiválása előtt változás hatásának vizsgálatában:

    ![Felhasználói kockázati házirend](./media/howto-user-risk-policy/1013.png "felhasználói kockázati házirend")

Kiválasztása egy **magas** küszöbérték csökkenti a szabályzat akkor aktiválódik, és minimalizálja a felhasználók számát.
Azonban nem tartalmazza **alacsony** és **Közepes** a szabályzatot, amely előfordulhat, hogy nem biztonságos identitások vagy eszközöket, amelyek a kockázatosként megjelölt felhasználók korábban gyanús vagy ismert legyen feltörni.

A házirend beállításakor

* Zárja ki a felhasználók, akik várhatóan sok vakriasztások (a fejlesztők, adatbiztonsági elemzők) létrehozása
* Zárja ki a területi beállításokat, amennyiben a szabályzat engedélyezése nem célszerű a felhasználók (például segélyszolgálat nincs hozzáférés)
* Használata egy **magas** küszöbérték során kezdeti házirend, vagy ha kell minimálisra csökkenthető a végfelhasználók által látott kihívásokat.
* Használja a **alacsony** küszöbértéket, ha a szervezet megköveteli a nagyobb biztonság. Válassza a **alacsony** küszöbérték vezet be a további felhasználói bejelentkezési kihívásokat, de a nagyobb biztonság.

A legtöbb szervezet számára ajánlott alapértelmezés szerint a rendszer a szabály konfigurálása egy **Közepes** küszöbérték egyensúlyozni használhatósági és a biztonság között.

A kapcsolódó felhasználói szolgáltatások áttekintését lásd:

* [Feltört fiók helyreállítási folyamat](flows.md#compromised-account-recovery).  
* [Feltört fiók blokkolása folyamat](flows.md#compromised-account-blocked).  

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

- Az a **Azure AD Identity Protection** panelen, a a **konfigurálása** területén kattintson **felhasználói kockázati házirend**.

    ![Felhasználói kockázati házirend](./media/howto-user-risk-policy/1009.png "felhasználói kockázati házirend")

## <a name="mitigating-user-risk-events"></a>Felhasználói kockázati események csökkentése
A rendszergazdák felhasználói kockázati biztonsági házirendet állíthatja be függően a kockázati szint alapján a felhasználók.

A bejelentkezés blokkolása:

* Megakadályozza a létrehozását az új felhasználói kockázati események az érintett felhasználók
* Lehetővé teszi a rendszergazdáknak manuálisan kockázatcsökkentési műveleteket az hatással lenne a felhasználó identitását, és állítsa vissza azt egy biztonságos állapotba


## <a name="next-steps"></a>További lépések

Az Azure AD Identity Protection áttekintést kaphat, tekintse meg a [áttekintése az Azure AD Identity Protection](overview.md).
