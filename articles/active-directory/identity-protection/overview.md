---
title: Az Azure Active Directory Identity Protection |} A Microsoft Docs
description: Ismerje meg, hogyan Azure AD Identity Protection segítségével is korlátozhatja, hogy a támadó biztonsági rés kiaknázása elleni egy sérült biztonságú identitás vagy egy eszközt, és biztonságos identitás vagy egy eszköz, amely korábban gyanús vagy ismert legyen feltörni.
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 791abd52ff4c016fe873288008e9d9b6adec6480
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378496"
---
# <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

Az Azure Active Directory Identity Protection az Azure AD Premium P2 kiadás, amely lehetővé teszi, hogy funkciója:

- A szervezet identitásait érintő esetleges biztonsági rések észlelése

- Automatikus válaszok észlelt gyanús tevékenységeket, a szervezet identitásait kapcsolódó konfigurálása  

- Gyanús incidensek kivizsgálásában, és azok megoldását megfelelő művelet végrehajtása   


## <a name="getting-started"></a>Első lépések

A Microsoft több mint egy évtizede nyújt védelmet a felhőalapú Identitások biztonságos vannak. Az Azure Active Directory Identity Protection a környezetben használhatja ugyanezeket a védelmi rendszereket Microsoft identitások védelmére használja.

Biztonsági rések túlnyomó többsége kerül sor, amikor a támadók próbál a jeggyel hozzáférést egy környezethez lopásának megjelölése a felhasználó identitását. Az évek során a támadók egyre inkább hatékony kihasználva külső illetéktelen behatolásokat és adathalászati támadások használatával váltak. Amint egy támadó hozzáférést még alacsony jogosultsági szintű felhasználó fiókok, viszonylag egyszerű, az oldalirányú mozgás keresztül fontos vállalati erőforrásokhoz való hozzáférést nyerhetnek a hozzájuk.

Ennek következtében kell tennie:

- Függetlenül attól, hogy jogosultsági szint összes identitások védelme

- Proaktív módon megakadályozza, hogy feltört identitásokat visszaélt folyamatban

Feltört identitásokat felderítése nem könnyű feladat. Az Azure Active Directory adaptív gépi tanulási algoritmusokat használ, és heurisztikus észlelheti a rendellenességeket és gyanús incidensek felderíthetik a vélhetően feltört identitásokat. Ezen adatok alapján Identity Protection állít elő, jelentéseket és riasztásokat, amelyek lehetővé teszik, hogy kiértékelhetik az észlelt problémákat, és a megfelelő kockázatcsökkentési, illetve a szervizelési műveletek végrehajtása.

Az Azure Active Directory Identity Protection több mint egy olyan figyelési és jelentéskészítési eszköz. Védelme érdekében a szervezet identitásait, konfigurálhatja a kockázatalapú szabályzatok, amelyek az automatikusan észlelt problémák reagálnak adott kockázati szint elérésekor. Ezek a szabályzatok mellett más feltételes hozzáférés-vezérlés az Azure Active Directory és az EMS által biztosított automatikusan letiltása, vagy kezdeményezzen adaptív szervizelési műveletek, például jelszó alaphelyzetbe állítása és a többtényezős hitelesítés kényszerítése.


#### <a name="identity-protection-capabilities"></a>Identitásvédelmi képességeket

**Biztonsági rések és a kockázatos fiókok észlelése:**  

* Így egyéni kapcsolatos ajánlásokat átfogó biztonsági rendszer kialakításához biztonsági rések kiemelésével
* Bejelentkezési kockázati szintek kiszámítása
* Felhasználói kockázati szintek kiszámítása


**Kockázati események vizsgálata:**

* Kockázati események értesítések küldése
* Megfelelő és környezetfüggő adatok felhasználásával kockázati események vizsgálata
* Biztosít a vizsgálatokhoz nyomon követéséhez alapvető munkafolyamatok
* Szervizelési műveleteket hajthat végre, például a jelszó-visszaállítás könnyű hozzáférést biztosít

**Kockázatalapú feltételes hozzáférési szabályzatok:**

* Szabályzat blokkolja a bejelentkezéseket, vagy a multi-factor authentication kihívások igénylő csökkentése érdekében a kockázatos bejelentkezések
* Szabályzat letiltása vagy biztonságos kockázatos felhasználói fiókok
* Felhasználók regisztráljanak a multi-factor Authentication hitelesítést megkövetelő szabályzata



## <a name="identity-protection-roles"></a>Identity Protection szerepkörök

Terheléselosztás a felügyeleti tevékenységek körül az Identity Protection implementáció számos szerepköröket rendelhet. Az Azure AD Identity Protection 3 címtárszerepkört támogat:

| Szerepkör                         | Teheti meg                          | Nem hajtható végre
| :--                          | ---                                |  ---   |
| Globális rendszergazda         | Teljes hozzáférés az Identity Protection előkészítése Identity Protection| |
| Biztonsági rendszergazda       | Teljes hozzáférés az Identity Protection | Identity Protection előkészítése, a felhasználó a jelszavak alaphelyzetbe állítása |
| Biztonsági olvasó              | Csak olvasási hozzáférés az Identity Protection | Identity Protection előkészítése, felhasználók szervizelése, a szabályzatok konfigurálására, jelszavak alaphelyzetbe állítása |




További részletekért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)



## <a name="detection"></a>Észlelés

### <a name="vulnerabilities"></a>Biztonsági rések

Az Azure Active Directory Identity Protection elemzi a konfigurációt, és észleli a sérülékenységeket, amelyek hatással lehetnek a felhasználói identitásokat. További részletekért lásd: [Azure Active Directory Identity Protection által észlelt biztonsági rések](vulnerabilities.md).

### <a name="risk-events"></a>Kockázati események

Az Azure Active Directory adaptív gépi tanulási algoritmusok és heurisztika segítségével észleli a felhasználói identitások kapcsolódó gyanús tevékenységeket. A rendszer létrehoz egy rekordot észlelt gyanús műveletek. Ezeket a rekordokat kockázati események is ismertek.  
További részletek: [Az Azure Active Directory kockázati eseményei](../active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Vizsgálat
Identity Protection örömét általában az Identity Protection irányítópultjának kezdődik.

![Szervizelési](./media/overview/1000.png "szervizelés")

Az irányítópult teszi elérhetővé:

* Például a jelentések **kockázatosként megjelölt felhasználók**, **kockázati események** és **biztonsági rések**
* Beállítások, például konfigurációját a **biztonsági házirendek**, **értesítések** és **többtényezős hitelesítési regisztráció**

Általában célszerű a vizsgálat, amely azt a folyamatot, tekintse át a tevékenységeket, naplók és egyéb releváns információkkal kapcsolatos egy kockázati esemény eldönteni, hogy szervizelési vagy kockázatcsökkentési lépések szükségesek, és milyen a identitása volt a kiindulási pont biztonsága sérült, és megismerheti, hogyan lett megadva a feltört identitását.

A vizsgálat tevékenységek köthet a [értesítések](notifications.md) Azure Active Directory Protection küld egy e-mailt.

A következő szakaszok további részleteket és a vizsgálat kapcsolatos lépéseket.  


## <a name="risky-sign-ins"></a>Kockázatos bejelentkezések

Az Azure Active Directory észleli [kockázati események típusairól](../reports-monitoring/concept-risk-events.md#risk-event-types) a valós idejű és offline állapotban van. Minden kockázati esemény észlelt egy bejelentkezhet a felhasználó hozzájárul a kockázatos bejelentkezési neve egy logikai fogalom. Kockázatos bejelentkezés egy mutató a egy bejelentkezési kísérlet, amely előfordulhat, hogy nem hajtott végre egy felhasználói fiók jogos tulajdonosa.


### <a name="sign-in-risk-level"></a>Bejelentkezés kockázati szintje

A bejelentkezési kockázat szintje feltüntetése (magas, közepes vagy alacsony) annak a valószínűségét, hogy egy bejelentkezési kísérlet után nem hajtottak végre egy felhasználói fiók jogos tulajdonosa.

### <a name="mitigating-sign-in-risk-events"></a>Bejelentkezési kockázati események csökkentése

Egy megoldás egy olyan műveletet, korlátozhatja, hogy a támadó egy sérült biztonságú identitás vagy az eszköz biztonsági visszaállítja a identitás vagy az eszköz biztonságos állapotban. A megoldás nem oldja meg a identitás vagy az eszköz társított korábbi bejelentkezési kockázati események.

Csökkentése érdekében automatikusan a kockázatos bejelentkezések, konfigurálhatja a bejelentkezési kockázat biztonsági szabályzatokat. Használja ezeket a házirendeket, érdemes megfontolni a felhasználó vagy a bejelentkezés kockázatos bejelentkezések letiltása vagy a felhasználónak a multi-factor authentication végrehajtása a kockázati szintjét. Ezek a műveletek megakadályozhatja, hogy a támadó egy ellopott identitás okozhatnának kiaknázása, és előfordulhat, hogy adjon egy ideig a identitásának védelmét.

### <a name="sign-in-risk-security-policy"></a>Biztonsági házirend bejelentkezési kockázat
A bejelentkezési kockázati házirend egy feltételes hozzáférési szabályzatot, amely egy adott bejelentkezési kockázat és előre meghatározott feltételeknek és szabályoknak az alapján kockázatcsökkentő vonatkozik.

![Bejelentkezési kockázati házirend](./media/overview/1014.png "bejelentkezési kockázati házirend")

Az Azure AD Identity Protection, hogy lehetővé teszi a csökkenti a kockázatos bejelentkezések kezeléséhez nyújt segítséget:

* Állítsa be a felhasználókat és csoportokat a szabályzat vonatkozik:

    ![Bejelentkezési kockázati házirend](./media/overview/1015.png "bejelentkezési kockázati házirend")
* A bejelentkezési kockázati szint küszöbértéke (alacsony, közepes vagy magas), amely elindítja a szabályzat beállítása:

    ![Bejelentkezési kockázati házirend](./media/overview/1016.png "bejelentkezési kockázati házirend")
* Állítsa be az aktiválásakor a szabályzatot érvényesíteni kívánt vezérlőket:  

    ![Bejelentkezési kockázati házirend](./media/overview/1017.png "bejelentkezési kockázati házirend")
* Váltás a házirend állapota:

    ![MFA-regisztráció](./media/overview/403.png "MFA-regisztráció")
* Tekintse át, és azt aktiválása előtt változás hatásának vizsgálatában:

    ![Bejelentkezési kockázati házirend](./media/overview/1018.png "bejelentkezési kockázati házirend")

#### <a name="what-you-need-to-know"></a>Mit kell tudni
Konfigurálhatja a bejelentkezési kockázat biztonsági házirend többtényezős hitelesítés megkövetelése:

![Bejelentkezési kockázati házirend](./media/overview/1017.png "bejelentkezési kockázati házirend")

Azonban biztonsági okokból a beállítás csak akkor működik, amely a multi-factor authentication regisztrált felhasználók számára. Ha a többtényezős hitelesítés megkövetelése a feltétel teljesül egy felhasználóhoz, aki még nem regisztrált multi-factor Authentication a felhasználó le van tiltva.

Ajánlott eljárásként Ha azt szeretné, a többtényezős hitelesítés megkövetelése a kockázatos bejelentkezések el a következőket:

1. Engedélyezze a [többtényezős hitelesítési regisztrációs házirend](#multi-factor-authentication-registration-policy) az érintett felhasználók számára.
2. MFA-regisztráció végrehajtásához kockázatos munkamenetben az érintett felhasználók a bejelentkezéshez szükséges

Ezen lépések elvégzése biztosítja, hogy a többtényezős hitelesítés szükséges a kockázatos bejelentkezés.

#### <a name="best-practices"></a>Ajánlott eljárások
Kiválasztása egy **magas** küszöbérték csökkenti a szabályzat akkor aktiválódik, és minimalizálja a felhasználók számát.  

Azonban nem tartalmazza **alacsony** és **Közepes** a szabályzatot, amely esetleg blokkolja egy támadó a sérült biztonságú identitás kihasználhassa a kockázatosként megjelölt bejelentkezések.

A házirend beállításakor

* Felhasználók, akik nem / nem lehet többtényezős hitelesítés kihagyása
* Zárja ki a területi beállításokat, amennyiben a szabályzat engedélyezése nem célszerű a felhasználók (például segélyszolgálat nincs hozzáférés)
* Zárja ki a felhasználók, akik várhatóan sok vakriasztások (a fejlesztők, adatbiztonsági elemzők) létrehozása
* Használata egy **magas** küszöbérték során kezdeti házirend, vagy ha kell minimálisra csökkenthető a végfelhasználók által látott kihívásokat.
* Használja a **alacsony** küszöbértéket, ha a szervezet megköveteli a nagyobb biztonság. Válassza a **alacsony** küszöbérték vezet be a további felhasználói bejelentkezési kihívásokat, de a nagyobb biztonság.

A legtöbb szervezet számára ajánlott alapértelmezés szerint a rendszer a szabály konfigurálása egy **Közepes** küszöbérték egyensúlyozni használhatósági és a biztonság között.

A bejelentkezési kockázati házirend van:

* Böngésző forgalom és a modern hitelesítést használó bejelentkezések alkalmazza.
* Tiltsa le a WS-Trust végpont a következő az összevont Identitásszolgáltató, például az AD FS korábbi biztonsági protokollokat használó alkalmazások nem alkalmazza.

A **kockázati események** az Identity Protection konzolon lap felsorolja az összes eseményt:

* Ez a házirend lett alkalmazva
* Áttekintheti a tevékenység, és megtudhatja, hogy a művelet megfelelő volt-e vagy sem

A kapcsolódó felhasználói szolgáltatások áttekintését lásd:

* [Kockázatos bejelentkezési helyreállítási](flows.md#risky-sign-in-recovery)
* [Kockázatos bejelentkezés blokkolva](flows.md#risky-sign-in-blocked)  
* [Az Azure AD Identity Protection a bejelentkezési élmény](flows.md)  

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

- Az a **Azure AD Identity Protection** panelen, a a **konfigurálása** területén kattintson **bejelentkezési kockázati házirend**.

    ![Felhasználói kockázati házirend](./media/overview/1014.png "felhasználói kockázati házirend")



## <a name="users-flagged-for-risk"></a>Kockázatosként megjelölt felhasználók

Összes aktív [kockázati események](../reports-monitoring/concept-risk-events.md) , amely az Azure Active Directory által észlelt, a felhasználó hozzájárul a felhasználói kockázat nevű egy logikai fogalom. Kockázatosként megjelölt felhasználók egy mutató a egy felhasználói fiókot, előfordulhat, hogy sérült a biztonsága.

![Kockázatosként megjelölt felhasználók](./media/overview/1200.png)


### <a name="user-risk-level"></a>Felhasználói kockázati szint

Felhasználói kockázati szint annak a valószínűségét, hogy a felhasználói identitás biztonsága sérült megjelölése (magas, közepes vagy alacsony). Ez alapján lesz kiszámítva az a felhasználó identitását társított felhasználói kockázati események.

Egy kockázati esemény állapota vagy **aktív** vagy **lezárva**. Csak a kockázati események, amelyek **aktív** járulnak hozzá a felhasználó kockázati szintű számítás.

A felhasználói kockázat szintje számítja ki az alábbi ráfordítások:

* A felhasználót érintő aktív kockázati események
* Ezek az események kockázati szint
* E esetleg elvégzett szervizelési műveleteket került sor

![Felhasználói kockázat](./media/overview/1031.png "felhasználói kockázat")

A felhasználó kockázati szintek segítségével, amely meggátolja a kockázatos felhasználókat jelentkezik be feltételes hozzáférési szabályzatokat hozhat létre, vagy kényszerítse azok biztonságosan a jelszó módosítására.

### <a name="closing-risk-events-manually"></a>Kockázati események manuális lezárása

A legtöbb esetben, például a biztonságos jelszó-visszaállításra automatikus lezárása a kockázati események szervizelési műveleteket végzi el. Azonban ez valószínűleg nem mindig lehetséges.  
Ez, például a helyzet, ha:

* Az aktív kockázati események egy felhasználó törölve lett
* A vizsgálat során kiderül, hogy a jelentett kockázati esemény jogos felhasználója lett-e végre.

Mivel a kockázati események, amelyek **aktív** járulnak hozzá a felhasználói kockázat számítása, kockázati események manuális lezárása manuálisan csökkentheti a kockázati szint lehet.  
A vizsgálat során választhat tennie egy kockázati esemény állapotának módosítása ezen műveletek egyikét:

![Műveletek](./media/overview/34.png "műveletek")

* **Oldja meg** – Ha vizsgálatát, hogy egy kockázati esemény után egy megfelelő korrekciós műveletet identitásvédelem kívül tartott, és úgy gondolja, hogy a kockázati esemény tekintendők-e zárva, jelölje meg az esemény megoldva. Esemény lezárva a kockázati esemény állapotát állítja be, és a kockázati esemény már nem hozzájárul ahhoz, hogy a felhasználói kockázat feloldani.
* **Jelölje meg a hamis pozitív** – néhány esetben Ön egy kockázati esemény vizsgálata és helytelenül lett megjelölve, a kockázatos felderítése. Segíthet az ilyen előfordulások száma csökkenthető a vakriasztások kockázati esemény megjelölése. Ez segít a gépi tanulási algoritmusok hasonló események besorolása a jövőben javítása érdekében. A hamis pozitív események állapotát, hogy **lezárva** és azok többé nem hozzájárul ahhoz, hogy a felhasználói kockázat.
* **Hagyja figyelmen kívül** – Ha a szervizelés semmit nem tett, de szeretné, el kell távolítani a aktív listából a kockázati esemény egy kockázati esemény figyelmen kívül hagyása jelölheti meg, és be lesz zárva az esemény állapota. Felhasználói kockázat nem jelentenek figyelmen kívül hagyta eseményt. Ez a beállítás csak szokatlan körülmények között nem használható.
* **Aktiválja újra** -kockázati eseményeket, amelyek manuálisan lettek lezárva (kiválasztásával **megoldásához**, **vakriasztás**, vagy **figyelmen kívül hagyása**) aktiválhatók, az esemény beállítása vissza a **aktív**. Újraaktivált kockázati események járulnak hozzá a felhasználó kockázati szintű számítás. Nem lehet újraaktiválni (például egy biztonságos jelszó-visszaállítási) szervizelés útján lezárt kockázati események.

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

1. Az a **Azure AD Identity Protection** panel alatt **vizsgálat**, kattintson a **kockázati események**.

    ![Manuális jelszó-visszaállítási](./media/overview/1002.png "manuális jelszó alaphelyzetbe állítása")
2. Az a **kockázati események** listában, kattintson a kockázatot jelent.

    ![Manuális jelszó-visszaállítási](./media/overview/1003.png "manuális jelszó alaphelyzetbe állítása")
3. A kockázati panelen kattintson a jobb gombbal a felhasználó.

    ![Manuális jelszó-visszaállítási](./media/overview/1004.png "manuális jelszó alaphelyzetbe állítása")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Egy felhasználó az összes kockázati események manuális lezárása
Helyett manuálisan bezárása külön-külön egy felhasználóval kapcsolatos kockázati eseményeket, az Azure Active Directory Identity Protection emellett lehetőséget biztosít a metódus egyetlen kattintással a felhasználó az összes esemény bezárása.

![Műveletek](./media/overview/2222.png "műveletek")

Amikor rákattint **az összes esemény elvetését**, az összes esemény bezárása és az érintett felhasználó már nem veszélyben van.

### <a name="remediating-user-risk-events"></a>Védekezhet felhasználói kockázati események

A szervizelés egy olyan műveletet, biztonságos identitás vagy egy eszköz, amely korábban gyanús vagy ismert legyen feltörni. A javítási művelet visszaállítja a identitás vagy az eszköz biztonságos állapotban, és oldja fel az előző kockázati események a identitás vagy az eszköz társítva.

Felhasználói kockázati események javítása, a következőket teheti:

* Hajtsa végre a biztonságos jelszó-visszaállításra felhasználói kockázati események manuális javítása
* Biztonsági szabályzat felhasználói kockázat csökkentése, vagy automatikusan kijavítani a felhasználói kockázati események konfigurálása
* A fertőzött eszköz újratelepítése rendszerképből  

#### <a name="manual-secure-password-reset"></a>Biztonságos jelszó manuális alaphelyzetbe állítása
Egy biztonságos jelszó-visszaállítási egy hatékony szervizelési számos kockázati eseményekhez, és hajtja végre, ha automatikusan bezárja a kockázati eseményeket, és újraszámítja a felhasználói kockázat szintje. Az Identity Protection irányítópultjának használatával jelszó-visszaállításra egy kockázatos felhasználó kezdeményezhet.

A kapcsolódó párbeszédpanel kétféleképpen jelszó alaphelyzetbe állítása itt:

**Jelszó alaphelyzetbe állítása** – válassza ki **a felhasználók új jelszót kérnek** , hogy a felhasználó helyi helyreállíthatja, ha a felhasználó regisztrálta a multi-factor Authentication hitelesítéshez. A felhasználó tovább bejelentkezés során, a felhasználó lesz a multi-factor authentication kihívást sikeresen megoldásához szükséges, és ezután kényszerített a jelszó módosítására. Ez a lehetőség nem érhető el, ha a felhasználói fiók még nem regisztrált multi-factor Authentication hitelesítést.

**Ideiglenes jelszó** – ki **ideiglenes jelszó létrehozása** azonnal érvényteleníti a meglévő jelszót, és hozzon létre egy új, ideiglenes jelszót a felhasználónak. A felhasználó másodlagos e-mail cím vagy a felhasználó felettesét elküldi az új, ideiglenes jelszót. A jelszó nem átmeneti, mert a felhasználó bekéri után jelentkezzen be a jelszó módosítására.

![A házirend](./media/overview/1005.png "házirend")

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

1. Az a **Azure AD Identity Protection** panelen kattintson a **kockázatosként megjelölt felhasználók**.

    ![Manuális jelszó-visszaállítási](./media/overview/1006.png "manuális jelszó alaphelyzetbe állítása")
2. A felhasználók a listában legalább egy kockázati események rendelkező felhasználót válasszon.

    ![Manuális jelszó-visszaállítási](./media/overview/1007.png "manuális jelszó alaphelyzetbe állítása")
3. A felhasználói panelen kattintson a **jelszó alaphelyzetbe állítása**.

    ![Manuális jelszó-visszaállítási](./media/overview/1008.png "manuális jelszó alaphelyzetbe állítása")

### <a name="user-risk-security-policy"></a>Felhasználói kockázati biztonsági házirend
A felhasználó kockázati biztonsági házirend egy feltételes hozzáférési szabályzatot, amely kiértékeli a kockázati szintjét egy adott felhasználó számára, és előre meghatározott feltételeknek és szabályoknak alapján szervizelési és kockázatcsökkentési műveleteket hajt végre.

![Felhasználói kockázati házirend](./media/overview/1009.png "felhasználói kockázati házirend")

Az Azure AD Identity Protection segítségével kockázatcsökkentési és, hogy lehetővé teszi, hogy a kockázatosként megjelölt felhasználók szervizelése kezelheti:

* Állítsa be a felhasználókat és csoportokat a szabályzat vonatkozik:

    ![Felhasználói kockázati házirend](./media/overview/1010.png "felhasználói kockázati házirend")
* A felhasználó kockázati szint küszöbértéke (alacsony, közepes vagy magas), amely elindítja a szabályzat beállítása:

    ![Felhasználói kockázati házirend](./media/overview/1011.png "felhasználói kockázati házirend")
* Állítsa be az aktiválásakor a szabályzatot érvényesíteni kívánt vezérlőket:

    ![Felhasználói kockázati házirend](./media/overview/1012.png "felhasználói kockázati házirend")
* Váltás a házirend állapota:

    ![Felhasználói kockázati házirend](./media/overview/403.png "MFA-regisztráció")
* Tekintse át, és azt aktiválása előtt változás hatásának vizsgálatában:

    ![Felhasználói kockázati házirend](./media/overview/1013.png "felhasználói kockázati házirend")

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

    ![Felhasználói kockázati házirend](./media/overview/1009.png "felhasználói kockázati házirend")

### <a name="mitigating-user-risk-events"></a>Felhasználói kockázati események csökkentése
A rendszergazdák felhasználói kockázati biztonsági házirendet állíthatja be függően a kockázati szint alapján a felhasználók.

A bejelentkezés blokkolása:

* Megakadályozza a létrehozását az új felhasználói kockázati események az érintett felhasználók
* Lehetővé teszi a rendszergazdáknak manuálisan kockázatcsökkentési műveleteket az hatással lenne a felhasználó identitását, és állítsa vissza azt egy biztonságos állapotba



## <a name="multi-factor-authentication-registration-policy"></a>Többtényezős hitelesítési regisztrációs házirend
Az Azure multi-factor authentication a egy módszer annak ellenőrzése, akik, amely több, mint felhasználónév és jelszó szükséges. Biztosít egy második biztonsági szintként, felhasználói bejelentkezéseket és tranzakciókat.  
Azt javasoljuk, hogy az Azure multi-factor authentication a felhasználók bejelentkezési folyamatába igényel el, mert azt:

* Szolgáltatás egyszerű ellenőrzési lehetőség számos szigorú hitelesítést végez
* A szervezet védelme és helyreállítása a fiók feltörések előkészítése a kulcsfontosságú szerepet játszik az

![Felhasználói kockázati házirend](./media/overview/1019.png "felhasználói kockázati házirend")

További részletekért lásd: [Mi az Azure multi-factor Authentication?](../authentication/multi-factor-authentication.md)

Az Azure AD Identity Protection konfigurálása egy szabályzatot, amely lehetővé teszi, hogy az üzembe helyezést, a multi-factor authentication regisztrációs kezeléséhez nyújt segítséget:

* Állítsa be a felhasználókat és csoportokat a szabályzat vonatkozik:

    ![Többtényezős hitelesítési szabályzat](./media/overview/1020.png "többtényezős hitelesítési szabályzat")
* Állítsa be az aktiválásakor a szabályzatot érvényesíteni kívánt vezérlőket::  

    ![Többtényezős hitelesítési szabályzat](./media/overview/1021.png "többtényezős hitelesítési szabályzat")
* Váltás a házirend állapota:

    ![Többtényezős hitelesítési szabályzat](./media/overview/403.png "többtényezős hitelesítési szabályzat")
* Az aktuális regisztrációs állapot megtekintése:

    ![Többtényezős hitelesítési szabályzat](./media/overview/1022.png "többtényezős hitelesítési szabályzat")

A kapcsolódó felhasználói szolgáltatások áttekintését lásd:

* [A multi-factor authentication regisztrációs folyamat](flows.md#multi-factor-authentication-registration).  
* [Bejelentkezési élmény az Azure AD Identity Protection](flows.md).  

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

- Az a **Azure AD Identity Protection** panelen, a a **konfigurálása** területén kattintson **többtényezős hitelesítési regisztráció**.

    ![Többtényezős hitelesítési szabályzat](./media/overview/1019.png "többtényezős hitelesítési szabályzat")

## <a name="next-steps"></a>További lépések
* [Channel 9-on: Az Azure AD és az identitás: Identity Protection előzetes verziója](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Az Azure Active Directory Identity Protection engedélyezése](enable.md)

* [Az Azure Active Directory Identity Protection által észlelt biztonsági rések](vulnerabilities.md)

* [Az Azure Active Directory kockázati eseményei](../active-directory-identity-protection-risk-events.md)

* [Az Azure Active Directory Identity Protection-értesítések](notifications.md)

* [Az Azure Active Directory Identity Protection-forgatókönyv](playbook.md)

* [Az Azure Active Directory Identity Protection-szószedet](glossary.md)

* [Az Azure AD Identity Protection a bejelentkezési élmény](flows.md)

* [Az Azure Active Directory Identity Protection - hogyan felhasználók tiltásának feloldása](howto-unblock-user.md)

* [Az Azure Active Directory Identity Protection és a Microsoft Graph használatának első lépései](graph-get-started.md)
