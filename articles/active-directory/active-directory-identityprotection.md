---
title: Az Azure Active Directory azonosító adatok védelmét |} Microsoft Docs
description: Ismerje meg, az Azure AD Identity Protection miként korlátozhatja, hogy a támadó kihasználni a sérült biztonságú identitás vagy az eszköz és identitás vagy egy eszköz, amely korábban gyanús vagy megsértik ismert biztonságossá tételéhez.
services: active-directory
keywords: az Azure active directory azonosító adatok védelmét, a cloud app discovery, alkalmazások, biztonság, kockázat, kockázati szint, biztonsági rés, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: b8ae865e06e085ebe1bb71b35d812024190e2b21
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

Az Azure Active Directory azonosító adatok védelmét lehetővé teszi az Azure AD Premium P2 kiadás, amely lehetővé teszi:

- A szervezet identitásait érintő lehetséges biztonsági rések észlelése

- Az észlelt gyanús tevékenységek a szervezet identitásait kapcsolódó automatikus válaszok konfigurálása  

- Vizsgálja meg a gyanús incidensek és a megfelelő művelettel hárítsa el őket   


## <a name="getting-started"></a>Első lépések

A Microsoft felhőalapú Identitások biztonságossá teszi a több mint egy évtizedben. Az Azure Active Directory azonosító adatok védelmét a környezetben, használhatja a Microsoft használja a biztonságos identitások azonos védelmi rendszerek.

A biztonsági problémák többsége kerül sor, amikor a támadók hozzáférést egy olyan környezetben a felhasználók személyazonossága ellopása. Az évek során a támadók egyre hatékony, a harmadik féltől származó megszegése követését és kifinomult adathalász támadások használatával lettek. Amint a támadó hozzáfér még az alacsony szintű jogosultságokkal rendelkező felhasználói fiókok, is viszonylag egyszerűen azokat a fontos vállalati erőforrásokra oldalirányú mozgás keresztül.

Ennek következtében kell:

- A jogosultsági szint függetlenül minden identitások védelme

- Proaktív a sérült biztonságú identitások megakadályozása visszaélt folyamatban

Sérült biztonságú identitások felderítésére nem egyszerű feladat. Az Azure Active Directory adaptív gépi tanulási algoritmusok használ, és rendellenességek észlelését és jelző potenciálisan gyanús incidensek észlelése heurisztikus identitások sérült. Ezeket az adatokat használva Identity Protection állít elő, jelentéseket és riasztásokat, amelyek segítségével kiértékelheti az észlelt problémákat, és tegye meg a megfelelő megoldás vagy elvégzett szervizelési műveleteket.

Az Azure Active Directory azonosító adatok védelmét a rendszer több mint egy figyelési és jelentéskészítési eszköz. A szervezet identitásait védelme érdekében beállíthatja kockázat-alapú, amely automatikusan észlelt problémák válaszolni a megadott kockázati szint elérésekor. Ezek a házirendek mellett más feltételes hozzáférés-szabályozási EMS, és az Azure Active Directory által biztosított automatikusan blokkolja, vagy adaptív szervizelési műveletek, például jelszó alaphelyzetbe állítása és a többtényezős hitelesítés kényszerítése kezdeményezni.


#### <a name="identity-protection-capabilities"></a>Identitás-megelőzési képességek

**Biztonsági rések és kockázatos fiókok észlelése:**  

* Egyéni kapcsolatos ajánlásokat általános biztonsági állapotát biztonsági rések részt biztosítása
* Bejelentkezési kockázati szintek kiszámítása
* Kockázati szintek felhasználói kiszámítása


**Vizsgáló kockázati események:**

* Értesítésküldési kockázati események
* Megfelelő és környezetfüggő információk alapján kockázati események kivizsgálása
* Vizsgálatok követéséhez alapvető munkafolyamatok biztosítása
* Így könnyen elérhetők szervizelési műveletek, például a jelszó alaphelyzetbe állítása

**Kockázati-alapú feltételes hozzáférési házirendek:**

* Kockázatos bejelentkezések mérséklése bejelentkezések blokkolja, vagy a multi-factor authentication kihívások szükségessé tételével házirend
* Házirend letiltás vagy biztonságos kockázatos felhasználói fiókok
* A felhasználók a multi-factor authentication regisztrálása házirend



## <a name="identity-protection-roles"></a>Identity Protection szerepkörök

A terheléselosztás irányul a felügyeleti tevékenységek körül az Identity Protection végrehajtása több szerepköröket rendelhet. Az Azure AD Identity Protection 3 directory szerepkörök támogatja:

| Szerepkör                         | Teheti meg                          | Nem hajtható végre
| :--                          | ---                                |  ---   |
| Globális rendszergazda         | Teljes körű hozzáférés az Identity Protection előkészítésére Identity Protection| |
| Biztonsági rendszergazda       | Teljes körű hozzáférés az Identity Protection | Identity Protection előkészítésére, állítsa vissza egy felhasználó jelszavát |
| Biztonsági olvasó              | Azonosító adatok védelmét a csak olvasási hozzáféréssel | A bevezetni, Identity Protection, remidiate felhasználók, házirendeket konfigurálhat, jelszavak alaphelyzetbe állítása |




További részletekért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](active-directory-assign-admin-roles-azure-portal.md)



## <a name="detection"></a>Észlelés

### <a name="vulnerabilities"></a>Biztonsági rések

Az Azure Active Directory azonosító adatok védelmét a konfigurációs és a biztonsági réseket, amelyek hatással lehetnek a felhasználói identitások észleli. További részletekért lásd: [Azure Active Directory Identity Protection által észlelt biztonsági rések](active-directory-identityprotection-vulnerabilities.md).

### <a name="risk-events"></a>Kockázati események

Az Azure Active Directory adaptív gépi tanulási algoritmusok és heurisztikus használja, amely kapcsolódik a felhasználói identitások gyanús tevékenységek észlelése. A rendszer létrehoz egy rekordot észlelt gyanús műveleteket. Ezeket a rekordokat kockázati események is ismertek.  
További részletek: [Az Azure Active Directory kockázati eseményei](active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Vizsgálat
A használatában Identity Protection keresztül általában az Identity Protection-irányítópult kezdődik.

![Szervizelés](./media/active-directory-identityprotection/1000.png "szervizelés")

Az irányítópult biztosít hozzáférést:

* Például a jelentések **felhasználók meg van jelölve, a kockázat**, **kockázati események** és **biztonsági réseket**
* Például a konfigurációs beállításokat a **biztonsági házirendek**, **értesítések** és **a multi-factor authentication regisztráció**

Általában le a vizsgálat, amely a tevékenységek, a naplókat, és olyan releváns információkat eldöntheti, hogy szervizelési vagy enyhítési lépések szükségesek a kockázat eseményhez kapcsolódó ellenőrzése során a rendszer, és milyen identitásának volt a kiindulási pont sérült, és a sérült biztonságú identitás használatának megismerése.

Ön nagy terhelést jelent a vizsgálat tevékenységek a [értesítések](active-directory-identityprotection-notifications.md) Azure Active Directory Protection küld egy e-mailt.

Az alábbi szakaszokban további részleteket és a vizsgálat kapcsolatos lépéseket.  


## <a name="risky-sign-ins"></a>Kockázatos bejelentkezések

Az Azure Active Directory észleli [eseménytípusok kockázati](active-directory-reporting-risk-events.md#risk-event-types) valós idejű és offline állapotban. Minden kockázati esemény észlelhető a bejelentkezés felhasználói a(z) hozzájárul egy logikai fogalom kockázatos bejelentkezés nevezik. Egy kockázatos bejelentkezés egy bejelentkezési kísérlet után végrehajtott előfordulhat, hogy nem egy felhasználói fiókot jogos tulajdonosa mutatója.


### <a name="sign-in-risk-level"></a>Bejelentkezési kockázati szint

A bejelentkezési kockázati szint feltüntetése (magas, közepes vagy alacsony) annak a valószínűségét, hogy egy bejelentkezési kísérlet után nem lett végrehajtva egy felhasználói fiókot jogos tulajdonosa.

### <a name="mitigating-sign-in-risk-events"></a>Bejelentkezési kockázati események orvoslása

A megoldás korlátozhatja, hogy a támadó a sérült biztonságú identitás vagy az eszköz visszaállítja a identitás vagy az eszköz biztonságos kihasználásához műveletet. A megoldás nem oldja meg a identitás vagy az eszköz társított korábbi bejelentkezési kockázati eseményekről.

Kockázatos bejelentkezések automatikusan mérséklése érdekében bejelentkezési kockázat biztonsági házirendeket konfigurálhat. Ezek a házirendek használja, akkor fontolja meg a felhasználó vagy a bejelentkezési kockázatos bejelentkezések blokkolni, vagy a felhasználónak a multi-factor authentication végrehajtása a kockázati szintjét. Ezek a műveletek előfordulhat, hogy egy támadó a kihasználhassa egy ellopott identitás való megosztása kárt okozhat, és előfordulhat, hogy néhány időt biztosítanak a identitás biztonságossá.

### <a name="sign-in-risk-security-policy"></a>Bejelentkezési kockázat biztonsági házirend
A kockázat bejelentkezési házirend egy feltételes hozzáférési szabályzatot, amely egy adott jelentkezik be a kockázat és azok mérséklési előre meghatározott feltételt és a szabályok alapján alkalmazza.

![Bejelentkezési kockázat házirendnek](./media/active-directory-identityprotection/1014.png "bejelentkezési kockázat házirendnek")

Az Azure AD Identity Protection azáltal, hogy a megoldás a kockázatos bejelentkezések kezeléséhez nyújt segítséget:

* Állítsa be a felhasználókat és csoportokat a házirend vonatkozik:

    ![Bejelentkezési kockázat házirendnek](./media/active-directory-identityprotection/1015.png "bejelentkezési kockázat házirendnek")
* Állítsa be a bejelentkezési kockázati szint küszöbértéke (alacsony, közepes vagy magas), amely elindítja a házirend:

    ![Bejelentkezési kockázat házirendnek](./media/active-directory-identityprotection/1016.png "bejelentkezési kockázat házirendnek")
* A vezérlők kényszeríthető, amikor elindítja a házirend beállítása:  

    ![Bejelentkezési kockázat házirendnek](./media/active-directory-identityprotection/1017.png "bejelentkezési kockázat házirendnek")
* Váltás a házirend állapotát:

    ![Többtényezős hitelesítés regisztrációs](./media/active-directory-identityprotection/403.png "MFA-regisztráció")
* Tekintse át, és milyen hatással bír a változtatás előtt aktiválás kiértékelése:

    ![Bejelentkezési kockázat házirendnek](./media/active-directory-identityprotection/1018.png "bejelentkezési kockázat házirendnek")

#### <a name="what-you-need-to-know"></a>Mit kell tudnia
A bejelentkezési kockázat biztonsági házirend többtényezős hitelesítést is konfigurálhatja:

![Bejelentkezési kockázat házirendnek](./media/active-directory-identityprotection/1017.png "bejelentkezési kockázat házirendnek")

Azonban biztonsági okok miatt ez a beállítás csak akkor működik a felhasználók a multi-factor authentication már regisztrált. Ha a többtényezős hitelesítést a feltétel teljesül-e egy felhasználóhoz, aki még nincs regisztrálva a többtényezős hitelesítés, a felhasználó le van tiltva.

Ajánlott eljárásként Ha a többtényezős hitelesítést igényel a kockázatos bejelentkezések el a következőket:

1. Engedélyezze a [a multi-factor authentication regisztráció házirend](#multi-factor-authentication-registration-policy) az érintett felhasználók számára.
2. A felhasználóknak az érintett bejelentkezési MFA regisztráció végrehajtásához nem kockázatos munkamenetben

A lépések végrehajtása biztosítja, hogy a multi-factor authentication a kockázatos bejelentkezéshez szükséges.

#### <a name="best-practices"></a>Ajánlott eljárások
Válassza a **magas** küszöbérték csökkenti a szám, ahányszor egy házirendet elindul, és minimalizálja a felhasználók számára.  

Azonban nem tartalmazza **alacsony** és **Közepes** veszélyt a szabályzatot, amely nem blokkolhatják a támadó a sérült biztonságú identitás kihasználhassa a megjelölt bejelentkezéseket.

A házirend beállításakor

* Felhasználók, akik nem / nem lehet a többtényezős hitelesítés kihagyása
* Kizárhat felhasználókat azokon a területeken, ahol a házirend engedélyezése nincs gyakorlati (például nincs hozzáférése az ügyfélszolgálathoz)
* Felhasználók, akik várhatóan hamis-figyelmeztetéséket (fejlesztők, adatbiztonsági elemzők) számos létre kizárása
* Használja a **magas** küszöbérték alatt kezdeti házirend bevezetési, vagy ha a felhasználók számára kihívást kell minimálisra csökkenthető.
* Használja a **alacsony** küszöbértéket, ha a szervezet nagyobb biztonságot igényel. Válassza a **alacsony** küszöbérték vezet be további felhasználói bejelentkezési kihívás, de a biztonság fokozása érdekében.

A legtöbb szervezet számára ajánlott alapértelmezés szerint a rendszer a szabály konfigurálása egy **Közepes** küszöbértékének egyensúlyt biztosítanak a használhatóság és a biztonsági között.

A bejelentkezési kockázat házirendnek van:

* Alkalmazza a böngésző forgalom és a modern hitelesítést használó bejelentkezéseket.
* A WS-Trust végpont, az összevont IDP, például az AD FS letiltásával régebbi biztonsági protokollt használó alkalmazások nem vonatkozik.

A **kockázati események** az Identity Protection konzolon lap felsorolja az összes eseményt:

* Ez a házirend alkalmazott
* Tekintse át a tevékenység, és megtudhatja, hogy a művelet megfelelő volt-e vagy sem

A kapcsolódó felhasználói szolgáltatások áttekintését lásd:

* [Kockázatos bejelentkezési helyreállítási](active-directory-identityprotection-flows.md#risky-sign-in-recovery)
* [Kockázatos bejelentkezés letiltva](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  
* [Az Azure AD Identity Protection bejelentkezési élmény](active-directory-identityprotection-flows.md)  

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

- A a **Azure AD Identity Protection** panelen, a a **konfigurálása** kattintson **bejelentkezési kockázat házirendnek**.

    ![Felhasználói ridk házirend](./media/active-directory-identityprotection/1014.png "felhasználói ridk házirend")



## <a name="users-flagged-for-risk"></a>Kockázatosként megjelölt felhasználók

Összes aktív [kockázati események](active-directory-identity-protection-risk-events.md) , amely az Azure Active Directory által észlelt, az a felhasználó hozzájárul a felhasználó kockázati nevű egy logikai fogalom. A kockázat megjelölt felhasználó egy egy felhasználói fiókot, amely hatékonyságát.

![Kockázatosként megjelölt felhasználók](./media/active-directory-identityprotection/1200.png)


### <a name="user-risk-level"></a>Felhasználói kockázati szint

Egy felhasználó kockázati szint annak a valószínűségét, hogy a felhasználó identitását feltörték megjelölése (magas, közepes vagy alacsony). Ez alapján van kiszámítva a a felhasználók személyazonossága társított felhasználó kockázati eseményekről.

A kockázati események állapota vagy **aktív** vagy **lezárva**. Csak a megfelelő események kockázatát **aktív** hozzájárulnak a felhasználó kockázati szint számítási.

A felhasználó kockázati szint számítja ki az alábbi adatokat:

* A felhasználót érintő aktív kockázati események
* Ezek az események kockázati szint
* E került sor esetleg elvégzett szervizelési műveleteket

![Felhasználói kockázatok](./media/active-directory-identityprotection/1031.png "felhasználói kockázatok")

A felhasználó kockázati szintek segítségével hozzon létre feltételes hozzáférési szabályzatok, amely megakadályozhatja a kockázatos felhasználók jelentkezik be, vagy őket biztonságosan módosíthatják jelszavukat.

### <a name="closing-risk-events-manually"></a>Kockázati események lezárása manuálisan

A legtöbb esetben a szervizelés műveleteket végez, például egy biztonságos jelszó alaphelyzetbe állítása automatikus lezárása a kockázati eseményekről lépnek. Azonban ez nem mindig lehet lehetséges.  
Ez helyzet, például az, ha:

* A felhasználó Active kockázati események törölve lett
* A vizsgálat azt jelzi, hogy a jelentett kockázat esemény a hiteles felhasználó lett-e végre.

Mivel a kockázati eseményekről, amelyek **aktív** hozzájárulnak a felhasználó kockázati számítási, előfordulhat, hogy manuálisan csökkenthető a kockázati eseményekről manuálisan bezárásával a kockázati szintjét.  
A vizsgálat során választhat, ezek a műveletek a kockázati események állapotának módosítása érvénybe:

![Műveletek](./media/active-directory-identityprotection/34.png "műveletek")

* **Hárítsa el** – Ha után vizsgálja meg a kockázati események, egy megfelelő szervizelési művelet kívül Identity Protection tartott, és úgy véli, hogy a kockázati esemény tekintendő bezárul, jelölje meg az esemény megoldva. Események állítja be a kockázat eseményállapot lezárva, és a kockázati esemény már nem hozzájárul ahhoz, hogy a felhasználó kockázati feloldva.
* **Jelölje meg a vakriasztások** – néhány esetben is vizsgálja meg a kockázati események és helytelenül lett megjelölve, egy kockázatos felderítése. Csökkentheti az ilyen előfordulások száma a vakriasztások kockázat esemény megjelölésével. Ez segít a gépi tanulási algoritmusok hasonló események osztályozása a jövőben javítása érdekében. Vakriasztások események állapota **lezárva** és azok már nem hozzájárul ahhoz, hogy a felhasználó kockázatot jelent.
* **Figyelmen kívül hagyása** – Ha bármilyen szervizelési művelet nem tett, de szeretné, hogy a kockázati esemény aktív listájából távolíthatók el a kockázati események figyelmen kívül hagyása jelölheti meg, és be lesz zárva a eseményállapot. Figyelmen kívül hagyott események nem járulnak hozzá felhasználói kockázatot jelent. Ezt a beállítást csak ritka esetekben használható.
* **Aktiválja újra** -kockázati események manuálisan lezárt (kiválasztásával **megoldásához**, **vakriasztás**, vagy **figyelmen kívül hagyása**) is újra kell aktiválni, az esemény beállítása vissza a **aktív**. Újraaktivált kockázati események hozzájárulnak a felhasználó kockázati szint számítási. Nem lehet újraaktiválni a kockázati eseményekről zárva a szervizelésen keresztül (például egy biztonságos jelszó-átállítási).

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

1. A a **Azure AD Identity Protection** panel alatt **vizsgálat**, kattintson a **kockázati események**.

    ![Manuális jelszó-átállítási](./media/active-directory-identityprotection/1002.png "manuális jelszó alaphelyzetbe állítása")
2. Az a **kockázati események** listában, kattintson a kockázata.

    ![Manuális jelszó-átállítási](./media/active-directory-identityprotection/1003.png "manuális jelszó alaphelyzetbe állítása")
3. A kockázat panelen kattintson a jobb gombbal a felhasználó.

    ![Manuális jelszó-átállítási](./media/active-directory-identityprotection/1004.png "manuális jelszó alaphelyzetbe állítása")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Minden kockázati események a felhasználó manuálisan bezárása
Helyett manuálisan bezárása külön-külön egy felhasználó kockázati eseményekről, Azure Active Directory azonosító adatok védelmét is biztosít egy metódust, zárja be az összes esemény egy felhasználó egyetlen kattintással.

![Műveletek](./media/active-directory-identityprotection/2222.png "műveletek")

Amikor rákattint **hagyja figyelmen kívül az összes esemény**, az összes esemény be van zárva, és az érintett felhasználó már nem veszélyben.

### <a name="remediating-user-risk-events"></a>Felmérésével felhasználói kockázati események

A szervizelés identitás vagy egy eszköz, amely korábban gyanús vagy megsértik ismert biztonságos művelet. A javítási művelet visszaállítja a identitás vagy az eszköz biztonságos, és oldja fel a identitás vagy az eszköz társított előző kockázati eseményekről.

Felhasználói kockázati események kijavítani, a következőket teheti:

* Hajtsa végre a biztonságos jelszó alaphelyzetbe állítása manuálisan megoldásáról a felhasználó kockázati események
* Mérséklése vagy felhasználói kockázati események automatikusan kijavítani felhasználói kockázat biztonsági házirend konfigurálása
* Új lemezképet készíteni a fertőzött eszköz  

#### <a name="manual-secure-password-reset"></a>Manuális biztonságos jelszó alaphelyzetbe állítása
A biztonságos jelszó alaphelyzetbe állítása egy hatékony szervizelése sok kockázati eseményekről, és végrehajtásakor, automatikusan a kockázati eseményekről bezárja, és újraszámítja a felhasználó kockázati szintjét. Az Identity Protection-Irányítópult segítségével kezdeményezni egy kockázatos felhasználó új jelszót.

A kapcsolódó párbeszédpanel a jelszó alaphelyzetbe állítása két különböző módszert biztosít:

**Jelszó-átállítási** – Itt adhatja meg **a felhasználónak a jelszavuk** a felhasználó önállóan helyreállítja, ha a felhasználó a multi-factor authentication regisztrálva van. Bejelentkezéskor a felhasználó következő, a felhasználó lesz a multi-factor authentication kihívást sikeresen megoldására szükséges, és módosítsa a jelszót, majd kényszerítve. Ez a lehetőség nem érhető el, ha a felhasználói fiók már nem regisztrált multi-factor Authentication hitelesítést.

**Ideiglenes jelszó** – Itt adhatja meg **létrehozni egy ideiglenes jelszót** azonnal érvénytelenné válnak a meglévő jelszavát, és a felhasználó új ideiglenes jelszó létrehozása. Egy másodlagos e-mail címet a felhasználókhoz vagy a felhasználó kezelője elküldi az új ideiglenes jelszót. Mert a jelszó ideiglenes, a felhasználót a rendszer kéri-bejelentkezés után a jelszó módosítására.

![Házirend](./media/active-directory-identityprotection/1005.png "házirend")

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

1. Az a **Azure AD Identity Protection** panelen kattintson a **felhasználók meg van jelölve, a kockázat**.

    ![Manuális jelszó-átállítási](./media/active-directory-identityprotection/1006.png "manuális jelszó alaphelyzetbe állítása")
2. Felhasználók közül legalább egy kockázati események rendelkező felhasználót válasszon.

    ![Manuális jelszó-átállítási](./media/active-directory-identityprotection/1007.png "manuális jelszó alaphelyzetbe állítása")
3. A felhasználó paneljén kattintson **jelszó-átállítási**.

    ![Manuális jelszó-átállítási](./media/active-directory-identityprotection/1008.png "manuális jelszó alaphelyzetbe állítása")

### <a name="user-risk-security-policy"></a>Felhasználói kockázat biztonsági házirend
A felhasználó kockázati biztonsági házirend egy feltételes hozzáférési szabályzatot, amely kiértékeli a kockázati szintjét egy adott felhasználónak és előre meghatározott feltételt és a szabályok alapján a szervizelés és a megoldás műveleteket hajt végre.

![Felhasználói ridk házirend](./media/active-directory-identityprotection/1009.png "felhasználói ridk házirend")

Az Azure AD Identity Protection segítségével felügyelheti a megoldás és a szervizelés azáltal, hogy kockázat megjelölt felhasználók:

* Állítsa be a felhasználókat és csoportokat a házirend vonatkozik:

    ![Felhasználói ridk házirend](./media/active-directory-identityprotection/1010.png "felhasználói ridk házirend")
* Állítsa be a felhasználó kockázati szint küszöbértéke (alacsony, közepes vagy magas), amely elindítja a házirend:

    ![Felhasználói ridk házirend](./media/active-directory-identityprotection/1011.png "felhasználói ridk házirend")
* A vezérlők kényszeríthető, amikor elindítja a házirend beállítása:

    ![Felhasználói ridk házirend](./media/active-directory-identityprotection/1012.png "felhasználói ridk házirend")
* Váltás a házirend állapotát:

    ![Felhasználói ridk házirend](./media/active-directory-identityprotection/403.png "MFA-regisztráció")
* Tekintse át, és milyen hatással bír a változtatás előtt aktiválás kiértékelése:

    ![Felhasználói ridk házirend](./media/active-directory-identityprotection/1013.png "felhasználói ridk házirend")

Válassza a **magas** küszöbérték csökkenti a szám, ahányszor egy házirendet elindul, és minimalizálja a felhasználók számára.
Azonban nem tartalmazza **alacsony** és **Közepes** veszélyt a szabályzatot, amely előfordulhat, hogy nem biztonságos identitások vagy eszközöket, amelyek a megjelölt felhasználók korábban gyanús vagy megsértik ismert.

A házirend beállításakor

* Felhasználók, akik várhatóan hamis-figyelmeztetéséket (fejlesztők, adatbiztonsági elemzők) számos létre kizárása
* Kizárhat felhasználókat azokon a területeken, ahol a házirend engedélyezése nincs gyakorlati (például nincs hozzáférése az ügyfélszolgálathoz)
* Használja a **magas** küszöbérték alatt kezdeti házirend bevezetési, vagy ha a felhasználók számára kihívást kell minimálisra csökkenthető.
* Használja a **alacsony** küszöbértéket, ha a szervezet nagyobb biztonságot igényel. Válassza a **alacsony** küszöbérték vezet be további felhasználói bejelentkezési kihívás, de a biztonság fokozása érdekében.

A legtöbb szervezet számára ajánlott alapértelmezés szerint a rendszer a szabály konfigurálása egy **Közepes** küszöbértékének egyensúlyt biztosítanak a használhatóság és a biztonsági között.

A kapcsolódó felhasználói szolgáltatások áttekintését lásd:

* [Sérült a fiók a helyreállítási folyamat](active-directory-identityprotection-flows.md#compromised-account-recovery).  
* [Sérült a fiók blokkolva van folyamata](active-directory-identityprotection-flows.md#compromised-account-blocked).  

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

- A a **Azure AD Identity Protection** panelen, a a **konfigurálása** kattintson **felhasználói kockázat házirendnek**.

    ![Felhasználói ridk házirend](./media/active-directory-identityprotection/1009.png "felhasználói ridk házirend")

### <a name="mitigating-user-risk-events"></a>Felhasználói kockázati események orvoslása
Rendszergazdák állíthat be egy felhasználó kockázat biztonsági házirend felhasználók blokkolása után bejelentkezhet attól függően, hogy a kockázati szintjét.

Blokkolja a bejelentkezés:

* Megakadályozza, hogy az érintett felhasználó új felhasználói kockázati események előállítását
* Lehetővé teszi a rendszergazdáknak manuálisan javítsa ki a kockázati eseményekről, hogy az hatással lenne a felhasználó identitását, és visszaállítja a biztonságos állapotát



## <a name="multi-factor-authentication-registration-policy"></a>Többtényezős hitelesítési regisztrációs házirend
Az Azure többtényezős hitelesítés a ellenőrzésére, akik csupán felhasználónévvel és jelszóval használatát igényli módszer. A második biztonsági szintként, a felhasználói bejelentkezéseket és tranzakciókat tartalmaz.  
Azt javasoljuk, hogy az Azure többtényezős hitelesítés a felhasználói bejelentkezések szükséges el, mert azt:

* Erős hitelesítés, könnyen ellenőrzési lehetőségek széles kézbesíti
* A szervezet védelme és helyreállítása fiók biztonság sérüléseinek kulcsfontosságú szerepet játszik

![Felhasználói ridk házirend](./media/active-directory-identityprotection/1019.png "felhasználói ridk házirend")

További részletekért lásd: [Mi az Azure multi-factor Authentication?](authentication/multi-factor-authentication.md)

Az Azure AD Identity Protection úgy konfigurálja a házirendet, amely lehetővé teszi a multi-factor authentication regisztráció bevezetési kezeléséhez nyújt segítséget:

* Állítsa be a felhasználókat és csoportokat a házirend vonatkozik:

    ![Többtényezős hitelesítési szabályzat](./media/active-directory-identityprotection/1020.png "többtényezős hitelesítési szabályzat")
* Állítsa be kell lépnek érvénybe, ha a házirend elindítja a vezérlők::  

    ![Többtényezős hitelesítési szabályzat](./media/active-directory-identityprotection/1021.png "többtényezős hitelesítési szabályzat")
* Váltás a házirend állapotát:

    ![Többtényezős hitelesítési szabályzat](./media/active-directory-identityprotection/403.png "többtényezős hitelesítési szabályzat")
* Tekintse meg az aktuális regisztrációs állapotát:

    ![Többtényezős hitelesítési szabályzat](./media/active-directory-identityprotection/1022.png "többtényezős hitelesítési szabályzat")

A kapcsolódó felhasználói szolgáltatások áttekintését lásd:

* [A multi-factor authentication regisztrációs folyamat](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  
* [Bejelentkezés során lép az Azure AD Identity Protection](active-directory-identityprotection-flows.md).  

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

- A a **Azure AD Identity Protection** panelen, a a **konfigurálása** kattintson **a multi-factor authentication regisztráció**.

    ![Többtényezős hitelesítési szabályzat](./media/active-directory-identityprotection/1019.png "többtényezős hitelesítési szabályzat")

## <a name="next-steps"></a>További lépések
* [9. csatornán: Az Azure AD és az Identity: Identity Protection előzetes kiadásának](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Az Azure Active Directory-identitás védelem engedélyezése](active-directory-identityprotection-enable.md)

* [Azure Active Directory Identity Protection által észlelt biztonsági rések](active-directory-identityprotection-vulnerabilities.md)

* [Az Azure Active Directory kockázati események](active-directory-identity-protection-risk-events.md)

* [Az Azure Active Directory Identity Protection-értesítések](active-directory-identityprotection-notifications.md)

* [Az Azure Active Directory Identity Protection-forgatókönyv](active-directory-identityprotection-playbook.md)

* [Az Azure Active Directory-Identity Protection-szószedet](active-directory-identityprotection-glossary.md)

* [Az Azure AD Identity Protection bejelentkezési élmény](active-directory-identityprotection-flows.md)

* [Az Azure Active Directory Identity Protection - letiltásának feloldása felhasználók](active-directory-identityprotection-unblock-howto.md)

* [Ismerkedés az Azure Active Directory azonosító adatok védelmét és a Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
