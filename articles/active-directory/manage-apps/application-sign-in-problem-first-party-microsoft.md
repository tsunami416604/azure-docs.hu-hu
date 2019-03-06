---
title: Egy Microsoft-alkalmazásba történő bejelentkezésnél |} A Microsoft Docs
description: Belső Microsoft Applications (például Office 365) az Azure AD-vel való bejelentkezéskor problémákkal kapcsolatos gyakori problémák elhárítása
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09f1ef137bbad50e72771c4368346716336c4faf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447098"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Egy Microsoft-alkalmazásba történő bejelentkezésnél

A Microsoft Applications (például Office 365 Exchange, SharePoint, a Yammer stb.) társítva, vagy mint 3. fél SaaS-alkalmazások vagy más alkalmazásokhoz, az egyszeri bejelentkezéshez az Azure AD integrálása egy kicsit másképp kezeli.

Hogy egy felhasználó kaphat-e a Microsoft által közzétett alkalmazás három fő módja van.

-   Az Office 365 vagy más fizetős csomagok az alkalmazások, felhasználók kapjanak hozzáférést keresztül **licenc-hozzárendelés** közvetlenül a saját felhasználói fiókot, vagy egy csoport, a Csoportalapú licenc-hozzárendelés funkcióval.

-   A alkalmazások számára, amely közzéteszi a Microsoft vagy egy harmadik féltől származó megkötések nélkül bárki használandó, felhasználókat lehet, hogy hozzáférést keresztül **felhasználói beleegyezés**. This0 azt jelenti, hogy azok jelentkezzen be az alkalmazás Azure AD munkahelyi vagy iskolai fiókjával, és lehetővé teszi, hogy a fiók lehet elérni egyes adatok korlátozott készletét.

-   A alkalmazások számára, amely közzéteszi a Microsoft vagy a 3. fél megkötések nélkül bárki használandó, felhasználókat lehet, hogy is hozzáférhetnek, keresztül **rendszergazdai jóváhagyást**. Ez azt jelenti, hogy a rendszergazda azt észlelte, az alkalmazás által a szervezet minden tagja használhatók, így jelentkezzen be az alkalmazás egy globális rendszergazdai fiókkal, és a hozzáférési jogot a szervezet minden tagja.

A probléma elhárításához, kezdje a [fontolja meg az alkalmazás-hozzáférés az általános problémás területek](#general-problem-areas-with-application-access-to-consider) majd beolvassa a forgatókönyv: Microsoft Application hozzáférést kaphat a részletek hibaelhárítási lépéseit.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Alkalmazás-hozzáférést, fontolja meg az általános problémás területek

Az alábbiakban olyan, az általános problémás területek, amely részletesen is megtekintheti, ha van egy ötlete, hol kell elkezdeni, de javasoljuk, hogy elolvasta a forgatókönyv a gyors induláshoz: Forgatókönyv: Microsoft Application hozzáférést hibaelhárítási lépéseit.

-   [A felhasználói fiókkal kapcsolatos problémák](#problems-with-the-users-account)

-   [Csoportokkal kapcsolatos problémák](#problems-with-groups)

-   [A feltételes hozzáférési szabályzatokkal kapcsolatos problémák](#problems-with-conditional-access-policies)

-   [Alkalmazás jóváhagyásának kapcsolatos problémák](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Microsoft Application hozzáférés hibaelhárítása

Az alábbiakban néhány gyakori probléma, azok számára be futtassa, amikor a felhasználók nem jelentkezhetnek be egy Microsoft-alkalmazásba.

-   Először ellenőrizze a általános problémák

  * Ellenőrizze, hogy a felhasználó bejelentkezik a **javítsa ki az URL-cím** , és nem egy helyi alkalmazás URL-címet.

  * Ellenőrizze, hogy a felhasználó fiókjának **nincs zárolva.**

  * Győződjön meg arról, hogy a **felhasználói fiók létezik** az Azure Active Directoryban. [Ellenőrizze, hogy egy felhasználói fiókot az Azure Active Directoryban](#problems-with-the-users-account)

  * Ellenőrizze, hogy a felhasználó fiókjának **engedélyezve** a bejelentkezések. [Ellenőrizze a felhasználói fiók állapota](#problems-with-the-users-account)

  * Győződjön meg arról, hogy a felhasználó **nem lejárt vagy elfelejtett jelszó.** [A jelszó alaphelyzetbe állítása](#reset-a-users-password) vagy [önkiszolgáló jelszó-visszaállítás engedélyezése](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * Győződjön meg arról, hogy **multi-factor Authentication** nem blokkolja a felhasználói hozzáférést. [A felhasználó a multi-factor authentication szolgáltatás állapotának ellenőrzése](#check-a-users-multi-factor-authentication-status) vagy [ellenőrizze a felhasználó hitelesítési kapcsolattartási adatok](#check-a-users-authentication-contact-info)

   * Győződjön meg arról, hogy egy **feltételes hozzáférési szabályzat** vagy **Identity Protection** házirend nem blokkolja a felhasználói hozzáférést. [Ellenőrizze az adott feltételes hozzáférési szabályzat](#problems-with-conditional-access-policies) vagy [egy adott alkalmazás feltételes hozzáférési szabályzat ellenőrzése](#check-a-specific-applications-conditional-access-policy) vagy [adott feltételes hozzáférési szabályzat letiltása](#disable-a-specific-conditional-access-policy)

   * Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatok** naprakésznek, hogy a multi-factor Authentication vagy feltételes hozzáférési házirendek kikényszerítéséhez. [A felhasználó a multi-factor authentication szolgáltatás állapotának ellenőrzése](#check-a-users-multi-factor-authentication-status) vagy [ellenőrizze a felhasználó hitelesítési kapcsolattartási adatok](#check-a-users-authentication-contact-info)

-   A **Microsoft** **licencet igénylő alkalmazások** (például Office 365), az alábbiakban néhány konkrét problémák ellenőrzése után kiderül, hogy a fenti általános problémák:

   * Győződjön meg, hogy a felhasználó vagy van egy **licenccel.** [Ellenőrizze a felhasználó hozzárendelt licencek](#check-a-users-assigned-licenses) vagy [ellenőrizze a csoport hozzárendelt licencek](#check-a-groups-assigned-licenses)

   * Ha a licenc **rendelve egy** **statikus csoport**, ügyeljen arra, hogy a **felhasználó tagja** a csoportnak. [A vendégfelhasználói csoporttagságok ellenőrzése](#check-a-users-group-memberships)

   * Ha a licenc **hozzárendelt egy** **dinamikus csoport**, ügyeljen arra, hogy a **dinamikus csoportot a szabály megfelelően van-e állítva**. [Ellenőrizze a dinamikus csoport tagsági feltételek](#check-a-dynamic-groups-membership-criteria)

   * Ha a licenc **rendelve egy** **dinamikus csoport**, győződjön meg arról, hogy rendelkezik-e a dinamikus csoport **feldolgozása befejeződött** tagságát, és hogy a **felhasználó tagja**  (Ez eltarthat egy ideig). [A vendégfelhasználói csoporttagságok ellenőrzése](#check-a-users-group-memberships)

   *  Miután ellenőrizze a licenc van hozzárendelve, győződjön meg arról, a licenc **nem járt le**.

   *  Ellenőrizze, hogy a licence **az alkalmazás** érnek el.

-   A **Microsoft** **alkalmazások, amelyeknek nincs szükségük licencre**, néhány további tevékenység következik, ellenőrizheti:

   * Ha az alkalmazás által kért **felhasználói szintű engedélyek** (például "hozzáférni a felhasználó postaládájához"), győződjön meg arról, hogy a felhasználó bejelentkezett az alkalmazás- és hajtott végre egy **felhasználóiszintűengedéllyelaművelet** ahhoz, hogy az alkalmazás számára az adatok eléréséhez.

   * Ha az alkalmazás által kért **rendszergazdai szintű engedélyekkel a** (például "hozzáférés az összes felhasználói postaládákhoz"), győződjön meg arról, hogy elvégezte-e globális rendszergazda egy **rendszergazdai jóváhagyás művelet az összes olyan felhasználó nevében** a szervezetben.

## <a name="problems-with-the-users-account"></a>A felhasználói fiókkal kapcsolatos problémák

Alkalmazás-hozzáférést az alkalmazáshoz rendelt felhasználó kapcsolatos probléma miatt blokkolva lesz. Az alábbiakban néhány módszert, és hibaelhárítást kapcsolatos problémák megoldásához a felhasználók és a fiók beállításait:

-   [Ellenőrizze, hogy egy felhasználói fiókot az Azure Active Directoryban](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Ellenőrizze a felhasználói fiók állapota](#check-a-users-account-status)

-   [A jelszó alaphelyzetbe állítása](#reset-a-users-password)

-   [Önkiszolgáló jelszóátállítás engedélyezése](#enable-self-service-password-reset)

-   [A felhasználó a multi-factor authentication szolgáltatás állapotának ellenőrzése](#check-a-users-multi-factor-authentication-status)

-   [A felhasználó hitelesítési kapcsolattartási adatok ellenőrzése](#check-a-users-authentication-contact-info)

-   [A vendégfelhasználói csoporttagságok ellenőrzése](#check-a-users-group-memberships)

-   [Ellenőrizze a felhasználó hozzárendelt licencek](#check-a-users-assigned-licenses)

-   [Egy felhasználó egy licenc hozzárendelése](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Ellenőrizze, hogy egy felhasználói fiókot az Azure Active Directoryban

Ellenőrizze, hogy jelen-e egy felhasználó fiókját, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Ellenőrizze, győződjön meg arról, hogy néznek várt, és nincs adat nem található a felhasználói objektum tulajdonságait.

### <a name="check-a-users-account-status"></a>Ellenőrizze a felhasználói fiók állapota

Egy felhasználói fiók állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **profil**.

8.  A **beállítások** ügyeljen arra, hogy **bejelentkezés blokkolása** értékre van állítva **nem**.

### <a name="reset-a-users-password"></a>A jelszó alaphelyzetbe állítása

A jelszó alaphelyzetbe állítása, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **jelszó alaphelyzetbe állítása** gombra a felhasználó panel tetején.

8.  Kattintson a **jelszó alaphelyzetbe állítása** gombot a **jelszó alaphelyzetbe állítása** megjelenő panelen.

9.  Másolás a **ideiglenes jelszó** vagy **adjon meg egy új jelszót** a felhasználó számára.

10. Kommunikáció az új jelszó a felhasználónak, akkor lehet szükség a jelszó módosítására a következő bejelentkezéskor során az Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

Új jelszó önkiszolgáló kérésének engedélyezéséhez kövesse az alábbi üzembe helyezési lépéseket:

-   [Engedélyezése a felhasználók számára az Azure Active Directory-jelszavaikat](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Segítségével a felhasználók visszaállíthassák vagy a helyszíni Active Directory-jelszavaikat](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>A felhasználó a multi-factor authentication szolgáltatás állapotának ellenőrzése

A felhasználó a multi-factor authentication szolgáltatás állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  Kattintson a **multi-factor Authentication** gombra a panel tetején.

7.  Egyszer a **multi-factor Authentication felügyeleti portálján** terhelés esetén ellenőrizze, hogy az a **felhasználók** fülre.

8.  A kiválasztott felhasználók által keresése, szűrése és rendezése a felhasználó található.

9.  A felhasználók listájából válassza ki a felhasználót és **engedélyezése**, **letiltása**, vagy **érvényesítése** a multi-factor authentication igény szerint.

  * **Megjegyzés**: Ha egy felhasználó egy **kényszerített** állapotba, előfordulhat, hogy beállította azokat **letiltott** ideiglenesen ahhoz, hogy azokat vissza a fiókba. Vissza a k, módosíthatja az állapotuk **engedélyezve** újra a kapcsolattartási adatait a következő bejelentkezéskor során regisztrálja újra hitelesíteni. Azt is megteheti, követheti a lépések a [ellenőrizze a felhasználó hitelesítési kapcsolattartási adatok](#check-a-users-authentication-contact-info) ellenőrizze vagy állítsa be ezeket az adatokat a számukra.

### <a name="check-a-users-authentication-contact-info"></a>A felhasználó hitelesítési kapcsolattartási adatok ellenőrzése

A felhasználó hitelesítési kapcsolattartási adatok a multi-factor Authentication hitelesítéshez használt ellenőrzéséhez feltételes hozzáférés, az Identity Protection és a jelszó-visszaállítás, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **profil**.

8.  Görgessen le a **hitelesítési kapcsolattartási adatok**.

9.  **Felülvizsgálat** az adatok regisztrálva a felhasználó és a frissítési igény szerint.

### <a name="check-a-users-group-memberships"></a>A vendégfelhasználói csoporttagságok ellenőrzése

A vendégfelhasználói csoporttagságok ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **csoportok** megtekintéséhez, amely csoportosítja a felhasználó tagja.

### <a name="check-a-users-assigned-licenses"></a>Ellenőrizze a felhasználó hozzárendelt licencek

A felhasználó hozzárendelt licencek ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amelyek licencek, a felhasználó jelenleg hozzá van rendelve.

### <a name="assign-a-user-a-license"></a>Egy felhasználó egy licenc hozzárendelése 

Licenc hozzárendelése egy felhasználóhoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amelyek licencek, a felhasználó jelenleg hozzá van rendelve.

8.  Kattintson a **hozzárendelése** gombra.

9.  Válassza ki **egy vagy több termék** elérhető termékek listájában.

10. **Nem kötelező** kattintson a **hozzárendelési beállítások** rendeljen kínálja a termékek az elemet. Kattintson a **Ok** amikor a művelet befejeződött.

11. Kattintson a **hozzárendelése** gomb ezek a licencek hozzárendelése a felhasználóhoz.

## <a name="problems-with-groups"></a>Csoportokkal kapcsolatos problémák

Alkalmazás-hozzáférés blokkolva lesz egy csoportot, amely az alkalmazás hozzá van rendelve egy probléma miatt. Néhány módszer az csoportokkal vagy csoporttagsággal kapcsolatos problémák megoldásában és hibaelhárítást a következők:

-   [Ellenőrizze a csoport tagságát](#check-a-groups-membership)

-   [Ellenőrizze a dinamikus csoport tagsági feltételek](#check-a-dynamic-groups-membership-criteria)

-   [Ellenőrizze a csoport hozzárendelt licencek](#check-a-groups-assigned-licenses)

-   [Egy csoport licencek újrafeldolgozása](#reprocess-a-groups-licenses)

-   [Egy csoport egy licenc hozzárendelése](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Ellenőrizze a csoport tagságát

Ellenőrizze a csoport tagságát, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresés** a csoport Önt érdeklő és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **tagok** , tekintse át a ehhez a csoporthoz tartozó felhasználók listáját.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Ellenőrizze a dinamikus csoport tagsági feltételek 

Egy dinamikus csoport tagsági feltételek ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresés** a csoport Önt érdeklő és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **dinamikus tagsági szabályok.**

8.  Tekintse át a **egyszerű** vagy **speciális** szabály definiálva ehhez a csoporthoz, és győződjön meg arról, hogy ez a csoport tagjának lennie kívánt felhasználó megfelel ezeknek a feltételeknek.

### <a name="check-a-groups-assigned-licenses"></a>Ellenőrizze a csoport hozzárendelt licencek

A csoport hozzárendelt licencek ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresés** a csoport Önt érdeklő és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amelyek licencek, a csoport jelenleg hozzá van rendelve.

### <a name="reprocess-a-groups-licenses"></a>Egy csoport licencek újrafeldolgozása

A csoport hozzárendelt licencek újrafeldolgozása, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresés** a csoport Önt érdeklő és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amelyek licencek, a csoport jelenleg hozzá van rendelve.

8.  Kattintson a **újrafeldolgozása** gombra kattintva győződjön meg arról, hogy naprakészek-e a csoport tagjai a licenccel. Ez eltarthat egy ideig, a mérete és összetettsége, a csoport.

   >[!NOTE]
   >Gyorsabb ehhez érdemes lehet ideiglenesen rendel egy licencet a felhasználó közvetlenül. [Rendelje hozzá egy felhasználói licenccel](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Egy csoport egy licenc hozzárendelése

Licenc hozzárendelése egy csoporthoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresés** a csoport Önt érdeklő és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amelyek licencek, a csoport jelenleg hozzá van rendelve.

8.  Kattintson a **hozzárendelése** gombra.

9.  Válassza ki **egy vagy több termék** elérhető termékek listájában.

10. **Nem kötelező** kattintson a **hozzárendelési beállítások** rendeljen kínálja a termékek az elemet. Kattintson a **Ok** amikor a művelet befejeződött.

11. Kattintson a **hozzárendelése** gomb ezek a licencek hozzárendelése ehhez a csoporthoz. Ez eltarthat egy ideig, a mérete és összetettsége, a csoport.

   >[!NOTE]
   >Gyorsabb ehhez érdemes lehet ideiglenesen rendel egy licencet a felhasználó közvetlenül. [Rendelje hozzá egy felhasználói licenccel](#problems-with-application-consent).
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>A feltételes hozzáférési szabályzatokkal kapcsolatos problémák

### <a name="check-a-specific-conditional-access-policy"></a>Egy adott feltételes hozzáférési szabályzat ellenőrzése

Ellenőrizze, vagy ellenőrizheti a egyetlen feltételes hozzáférési szabályzatot:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** a navigációs menü.

5.  Kattintson a **feltételes hozzáférési** navigációs elemre.

6.  Jelölje ki az Önt érdeklő vizsgálatával házirendet.

7.  Tekintse át, hogy nincsenek-e nincsenek konkrét feltételek, hozzárendelések vagy más beállítást, amellyel a felhasználók hozzáférését blokkolja.

   >[!NOTE]
   >Érdemes lehet ideiglenesen letiltja ezt a házirendet, győződjön meg arról, nincs hatással a bejelentkezések. Ehhez állítsa be a **házirend engedélyezése** kapcsolót **nem** , és kattintson a **mentése** gombra.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Egy adott alkalmazás feltételes hozzáférési szabályzat ellenőrzése

Ellenőrizze, vagy ellenőrizheti a egyetlen alkalmazás jelenleg a feltételes hozzáférési szabályzat konfigurálva:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** a navigációs menü.

5.  Kattintson a **minden alkalmazás**.

6.  Keresse meg az Önt érdeklő alkalmazás vagy a felhasználó megpróbálja jelentkezzen be az alkalmazás megjelenített neve vagy azonosítója.

     >[!NOTE]
     >Ha nem látja a keresett alkalmazást, kattintson a **szűrő** gombra, és bontsa ki a lista hatókörét **minden alkalmazás**. Ha meg szeretné tekinteni a több oszlopot, kattintson a **oszlopok** gombra kattintva adhat hozzá további részletek az alkalmazások számára.
     >
     >

7.  Kattintson a **feltételes hozzáférési** navigációs elemre.

8.  Jelölje ki az Önt érdeklő vizsgálatával házirendet.

9.  Tekintse át, hogy nincsenek-e nincsenek konkrét feltételek, hozzárendelések vagy más beállítást, amellyel a felhasználók hozzáférését blokkolja.

     >[!NOTE]
     >Érdemes lehet ideiglenesen letiltja ezt a házirendet, győződjön meg arról, nincs hatással a bejelentkezések. Ehhez állítsa be a **házirend engedélyezése** kapcsolót **nem** , és kattintson a **mentése** gombra.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Egy adott feltételes hozzáférési szabályzat letiltása

Ellenőrizze, vagy ellenőrizheti a egyetlen feltételes hozzáférési szabályzatot:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** a navigációs menü.

5.  Kattintson a **feltételes hozzáférési** navigációs elemre.

6.  Jelölje ki az Önt érdeklő vizsgálatával házirendet.

7.  Beállításával tiltsa le a szabályzatot a **házirend engedélyezése** kapcsolót **nem** , és kattintson a **mentése** gombra.

## <a name="problems-with-application-consent"></a>Alkalmazás jóváhagyásának kapcsolatos problémák

Alkalmazás-hozzáférés blokkolva lesz, mert a megfelelő engedélyekkel a jóváhagyási művelet nem történt. Az alábbiakban néhány módszert, és hibaelhárítást jóváhagyási alkalmazásproblémák megoldása:

-   [A felhasználói szintű engedéllyel a művelet végrehajtása](#perform-a-user-level-consent-operation)

-   [Rendszergazdai jóváhagyás művelet bármely alkalmazás](#perform-administrator-level-consent-operation-for-any-application)

-   [Rendszergazdai jóváhagyás végrehajtani egy egybérlős alkalmazást](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Rendszergazdai jóváhagyás végrehajtani a több-bérlős alkalmazás](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>A felhasználói szintű engedéllyel a művelet végrehajtása

-   Bármely Open ID Connect-kompatibilis alkalmazás, amely engedélyt kér ellenőrizheti, hogy az alkalmazás bejelentkezési képernyőn egy felhasználói szintű beleegyezés az alkalmazásnak a bejelentkezett felhasználó hajt végre.

-   Ha szeretné ezt megteheti szoftveresen, [egyedi felhasználói jóváhagyás kérése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Rendszergazdai jóváhagyás művelet bármely alkalmazás

-   A **csak a V1-alkalmazásmodell használatával fejlesztett alkalmazások**, kényszerítheti a rendszergazdai szintű jóváhagyást hozzáadásával történjen "**? rendszergazdai parancssorból =\_hozzájárulás**" végére egy alkalmazás a bejelentkezési URL-CÍMÉT.

-   A **bármilyen alkalmazást fejleszteni a V2-alkalmazásmodell használatával**, kényszerítheti a rendszergazdai jóváhagyás alatt utasításai alapján történjen a **engedélyeket kérhet a directory-rendszergazda** szakaszában [a rendszergazdai jóváhagyás végpont használatával](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Rendszergazdai jóváhagyás végrehajtani egy egybérlős alkalmazást

-   A **egybérlős alkalmazások** (mint fejleszt, vagy a szervezet saját), engedélyek kéréséhez, amely hajthat végre egy **rendszergazdai szintű hozzájárulási** nevében az összes művelet Jelentkezzen be globális rendszergazdaként, és kattintson a felhasználók a **engedélyeket** gombot a felső részén a **alkalmazás beállításjegyzék -&gt; összes alkalmazás -&gt; válasszon ki egy alkalmazást –&gt; Szükséges engedélyek** ablaktáblán.

-   A **bármilyen alkalmazást fejleszteni a V1 vagy v2-es alkalmazás modellel**, kényszerítheti a rendszergazdai jóváhagyás alatt utasításai alapján történjen a **engedélyeket kérhet a directory-rendszergazda**  szakaszában [a rendszergazdai jóváhagyás végpont használatával](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Rendszergazdai jóváhagyás végrehajtani a több-bérlős alkalmazás

-   A **több-bérlős alkalmazások** adott kérelem engedélyek (például a harmadik féltől származó alkalmazás, vagy a Microsoft vagy szakmai fejlődése szempontjából) hajthat végre egy **rendszergazdai szintű hozzájárulási** műveletet. Jelentkezzen be globális rendszergazdaként, és kattintson a a **engedélyeket** gomb alatt a **vállalati alkalmazások -&gt; összes alkalmazás -&gt; válasszon ki egy alkalmazást –&gt; engedélyek**  ablaktáblán (elérhető hamarosan).

-   Is kényszeríthető a rendszergazdai jóváhagyás alatt utasításai alapján történjen a **engedélyeket kérhet a directory-rendszergazda** szakaszában [a rendszergazdai jóváhagyás végpont használatával](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>További lépések
[A rendszergazdai jóváhagyás végpont használatával](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

