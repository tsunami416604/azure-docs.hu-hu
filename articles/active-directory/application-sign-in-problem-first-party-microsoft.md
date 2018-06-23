---
title: Bejelentkezés Microsoft-alkalmazáshoz problémák |} Microsoft Docs
description: A belső Microsoft Applications (például Office 365) az Azure AD használatával történő bejelentkezés során tapasztalt gyakori problémák elhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 4053c272fe78647ac646e0feefa884cf014a6b72
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334224"
---
## <a name="problems-signing-in-to-a-microsoft-application"></a>Bejelentkezés Microsoft-alkalmazáshoz problémák

Microsoft Applications (például Office 365 Exchange, SharePoint, Yammer, stb.) rendelve, és egy kicsit másképp, mint 3. fél SaaS-alkalmazások vagy más alkalmazásokat, az egyszeri bejelentkezéshez az Azure AD integrálása felügyelni.

Háromféleképpen fő, hogy a felhasználó beszerezheti a Microsoft közzétett alkalmazások eléréséhez.

-   Az Office 365- vagy más fizetős csomagok-alkalmazások, a felhasználók keresztül hozzáférést kapnak **licenc hozzárendelése** vagy közvetlenül a felhasználói fiókot, vagy egy csoport, a licenc csoport-alapú hozzárendelés funkcióval.

-   Alkalmazások a Microsoft vagy valamely harmadik fél közzétevő szabadon bárki, aki használni, a felhasználók adható hozzáférés a **felhasználói hozzájárulás**. This0 azt jelenti, hogy azok az alkalmazás az Azure AD munkahelyi vagy iskolai fiókkal bejelentkezni, és engedélyezze a fiókjuk lehet elérni egyes adatok korlátozott készletét.

-   Alkalmazások a Microsoft vagy a 3. fél közzétevő szabadon bárki, aki használni, a felhasználók is adható hozzáférés a **rendszergazda jóváhagyását**. Ez azt jelenti, hogy a rendszergazda azt észlelte, az alkalmazás is használhat a szervezet minden tagja számára, hogy jelentkezzen be az alkalmazás a globális rendszergazdai fiókkal, és hozzáférést biztosítson a szervezet minden tagja számára.

A probléma elhárításához indítsa el a [általános probléma területet, és fontolja meg az alkalmazás-hozzáférés](#general-problem-areas-with-application-access-to-consider) majd elolvashatják a [forgatókönyv: hibaelhárítása a Microsoft Application hozzáférés](#walkthrough-steps-to-troubleshoot-microsoft-application-access) be a részletes adatokat.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Általános probléma területet, és fontolja meg az alkalmazás-hozzáférés

Az alábbiakban olvashat egy listát részletezhető Ha egy meghatározni, hogy hol kell elkezdeni általános problémát területének, de ajánlott elolvasni a bemutató lépéseit az induláshoz gyorsan: [forgatókönyv: hibaelhárítása a Microsoft Application hozzáférés](#walkthrough-steps-to-troubleshoot-microsoft-application-access).

-   [A felhasználói fiókkal kapcsolatos problémák](#problems-with-the-users-account)

-   [A csoportokkal kapcsolatos problémák](#problems-with-groups)

-   [Feltételes hozzáférési szabályzatokkal kapcsolatos problémák](#problems-with-conditional-access-policies)

-   [Az alkalmazás hozzájárulásával problémák](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Microsoft Application hozzáférés hibaelhárítása

Az alábbiakban néhány gyakori probléma, segítsen a futtatását, amikor a felhasználók nem jelentkezhetnek be egy Microsoft-alkalmazáshoz.

-   Először ellenőrizze a általános problémák

  * Győződjön meg arról, hogy a felhasználó próbál bejelentkezni a **javítsa ki az URL-cím** és nem egy helyi alkalmazás URL-címet.

  * Győződjön meg arról, hogy a felhasználói fiók **nincs zárolva.**

  * Győződjön meg arról, hogy a **felhasználói fiók létezik** az Azure Active Directoryban. [Ellenőrizze, hogy egy felhasználói fiók létezik-e az Azure Active Directoryban](#problems-with-the-users-account)

  * Győződjön meg arról, hogy a felhasználói fiók **engedélyezett** a indított bejelentkezések. [A felhasználói fiók állapotának ellenőrzése](#problems-with-the-users-account)

  * Győződjön meg arról, hogy a felhasználó **nem lejárt vagy elfelejtett jelszó.** [A jelszó alaphelyzetbe állítása](#reset-a-users-password) vagy [önkiszolgáló jelszó-visszaállítás engedélyezése](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * Győződjön meg arról, hogy **multi-factor Authentication** nem blokkolja a hozzáférést. [A felhasználó a multi-factor authentication állapotának](#check-a-users-multi-factor-authentication-status) vagy [ellenőrizni kell a felhasználó hitelesítési kapcsolattartási adatait](#check-a-users-authentication-contact-info)

   * Győződjön meg arról, hogy egy **feltételes hozzáférési házirend** vagy **Identity Protection** házirend nem blokkolja a hozzáférést. [Ellenőrizze a megadott feltételes hozzáférési házirend](#problems-with-conditional-access-policies) vagy [egy adott alkalmazás feltételes hozzáférési házirendben](#check-a-specific-applications-conditional-access-policy) vagy [letiltása adott feltételes hozzáférési házirend](#disable-a-specific-conditional-access-policy)

   * Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatai** esetén a multi-factor Authentication vagy feltételes hozzáférési házirendek kényszerítését dátum. [A felhasználó a multi-factor authentication állapotának](#check-a-users-multi-factor-authentication-status) vagy [ellenőrizni kell a felhasználó hitelesítési kapcsolattartási adatait](#check-a-users-authentication-contact-info)

-   A **Microsoft** **licenc igénylő alkalmazások** (például az Office365), az alábbiakban néhány konkrét problémák ellenőrzése után kiderül, hogy a fenti általános problémák:

   * Ellenőrizze a felhasználó vagy van egy **licenccel.** [Ellenőrizze a felhasználó licenc-hozzárendeléseket](#check-a-users-assigned-licenses) vagy [egy csoporthoz hozzárendelt licencekkel ellenőrzése](#check-a-groups-assigned-licenses)

   * Ha a licenc **rendelt egy** **statikus csoport**, ügyeljen arra, hogy a **felhasználó tagja** csoport. [A felhasználói csoporttagság ellenőrzése](#check-a-users-group-memberships)

   * Ha a licenc **rendelt egy** **dinamikus csoport**, ügyeljen arra, hogy a **dinamikus csoport szabály megfelelően van-e beállítva**. [A dinamikus csoport tagsági feltételek ellenőrzése](#check-a-dynamic-groups-membership-criteria)

   * Ha a licenc **rendelt egy** **dinamikus csoport**, győződjön meg arról, hogy rendelkezik-e a dinamikus csoport **fejezett** tagságát, és hogy a **felhasználó tagja**  (Ez eltarthat egy ideig). [A felhasználói csoporttagság ellenőrzése](#check-a-users-group-memberships)

   *  Ha mindenképpen rendel hozzá a licencet, ellenőrizze, hogy a licenc **nem járt le**.

   *  Győződjön meg arról, hogy a licenc **az alkalmazás** érik el.

-   A **Microsoft** **alkalmazások, amelyek nem igényelnek licencet**, a következőkben más kereséséhez:

   * Ha az alkalmazás **felhasználói szintű engedélyek** (például "érhetik el a felhasználó postaládájához"), ellenőrizze, hogy a felhasználó van bejelentkezve az alkalmazás, és végrehajtotta a **felhasználói szintű hozzájárulási művelet** ahhoz, hogy az alkalmazás saját adatok eléréséhez.

   * Ha az alkalmazás **rendszergazdai engedélyek** (például "érhetik el az összes felhasználó postaládák"), győződjön meg arról, hogy elvégezte-e globális rendszergazda egy **rendszergazdai hozzájárulási művelet az összes olyan felhasználó nevében** a szervezetében.

## <a name="problems-with-the-users-account"></a>A felhasználói fiókkal kapcsolatos problémák

Alkalmazás-hozzáférés blokkolható az alkalmazáshoz rendelt felhasználó kapcsolatos probléma miatt. Az alábbiakban néhány módszert hibákat, és a felhasználó és a fiók beállításainak problémáinak megoldásával:

-   [Ellenőrizze, hogy egy felhasználói fiók létezik-e az Azure Active Directoryban](#check-if-a-user-account-exists-in-azure-active-directory)

-   [A felhasználói fiók állapotának ellenőrzése](#check-a-users-account-status)

-   [A jelszó alaphelyzetbe állítása](#reset-a-users-password)

-   [Önkiszolgáló jelszóátállítás engedélyezése](#enable-self-service-password-reset)

-   [A felhasználó a multi-factor authentication állapotának ellenőrzése](#check-a-users-multi-factor-authentication-status)

-   [Ellenőrizze a felhasználó hitelesítési kapcsolattartási adatai](#check-a-users-authentication-contact-info)

-   [A felhasználói csoporttagság ellenőrzése](#check-a-users-group-memberships)

-   [A felhasználó licenc-hozzárendeléseket ellenőrzése](#check-a-users-assigned-licenses)

-   [A felhasználó a licenc hozzárendelése](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Ellenőrizze, hogy egy felhasználói fiók létezik-e az Azure Active Directoryban

Ellenőrizze, hogy jelen-e a felhasználói fiók, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Ellenőrizze, győződjön meg arról, hogy azok meg, mint a várt, és nincs adat hiányzik a user objektum tulajdonságait.

### <a name="check-a-users-account-status"></a>A felhasználói fiók állapotának ellenőrzése

A felhasználói fiók állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **profil**.

8.  A **beállítások** ügyeljen arra, hogy **blokk bejelentkezés** értékre van állítva **nem**.

### <a name="reset-a-users-password"></a>A jelszó alaphelyzetbe állítása

A jelszó visszaállításához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **jelszó-átállítási** gombra, ha a felhasználó panel tetején.

8.  Kattintson a **jelszó-átállítási** gombra a **jelszó-átállítási** ablaktábla akkor jelenik meg.

9.  Másolás a **ideiglenes jelszó** vagy **adjon meg egy új jelszót** a felhasználó számára.

10. Az új jelszót, amely a felhasználó kommunikációhoz, azok szükséges jelszó módosításához a következő bejelentkezéskor a során az Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Az önkiszolgáló jelszó-visszaállítás engedélyezése

Önkiszolgáló jelszóváltoztatás, kövesse az alábbi telepítési lépéseket:

-   [Segítségével a felhasználók visszaállíthassák az Azure Active Directory-jelszavaikat](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Lehetővé teszi a felhasználók visszaállíthassák vagy módosíthassák a helyszíni Active Directory-jelszavaikat](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>A felhasználó a multi-factor authentication állapotának ellenőrzése

A felhasználó a multi-factor authentication állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  Kattintson a **multi-factor Authentication** gombra, ha a panel tetején.

7.  Egyszer a **multi-factor Authentication felügyeleti portál** terhelés esetén gondoskodjon arról, hogy a **felhasználók** fülre.

8.  Keresés, rendezés vagy szűréssel keresse meg a felhasználót a felhasználók listáját.

9.  Válassza ki a felhasználót a felhasználók listájának és **engedélyezése**, **tiltsa le a**, vagy **érvényesítése** többtényezős hitelesítést a kívánt módon működjenek.

  * **Megjegyzés:**: Ha egy felhasználó egy **kényszerített** állapotba kerül, előfordulhat, hogy meg őket **letiltott** ideiglenesen ahhoz, hogy azokat vissza a figyelembe. Miután bekerültek vissza, módosíthatja az állapot **engedélyezve** újra a megköveteli tőlük, hogy regisztrálja újra a során a következő bejelentkezéskor a kapcsolattartási adatait. Azt is megteheti, hogy is kövesse a [ellenőrizni kell a felhasználó hitelesítési kapcsolattartási adatait](#check-a-users-authentication-contact-info) ellenőrizze vagy állítsa be ezeket az adatokat a számukra.

### <a name="check-a-users-authentication-contact-info"></a>Ellenőrizze a felhasználó hitelesítési kapcsolattartási adatai

A felhasználó hitelesítési használt kapcsolattartási adatokat a többtényezős hitelesítést, a feltételes hozzáférés, a Identity Protection és a jelszó-átállítási ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **profil**.

8.  Görgessen le a **hitelesítési kapcsolattartási adatai**.

9.  **Tekintse át** az adatok regisztrálva a felhasználó és a frissítési igény szerint.

### <a name="check-a-users-group-memberships"></a>A felhasználói csoporttagság ellenőrzése

A felhasználói csoporttagság ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **csoportok** megtekintéséhez, amely csoportosítja a felhasználó tagja legyen.

### <a name="check-a-users-assigned-licenses"></a>A felhasználó licenc-hozzárendeléseket ellenőrzése

A felhasználó licenc-hozzárendeléseket ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amely licencek, a felhasználó jelenleg hozzá van rendelve.

### <a name="assign-a-user-a-license"></a>A felhasználó a licenc hozzárendelése 

A licenc hozzárendelése egy felhasználóhoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amely licencek, a felhasználó jelenleg hozzá van rendelve.

8.  Kattintson a **hozzárendelése** gombra.

9.  Válassza ki **egy vagy több termék** választható termékek közül.

10. **Nem kötelező** kattintson a **hozzárendelés beállításai** elem termékek granularly hozzárendelni. Kattintson a **Ok** amikor ez befejeződik.

11. Kattintson a **hozzárendelése** gomb a licencek hozzárendelése a felhasználóhoz.

## <a name="problems-with-groups"></a>A csoportokkal kapcsolatos problémák

Alkalmazás-hozzáférés blokkolható egy csoportot, amely hozzá van rendelve az alkalmazás kapcsolatos probléma miatt. Bizonyos értelemben csoportokkal vagy csoporttagsággal kapcsolatos problémák megoldásához és hibaelhárítást végezhessen a következők:

-   [Ellenőrizze a csoport tagságát](#check-a-groups-membership)

-   [A dinamikus csoport tagsági feltételek ellenőrzése](#check-a-dynamic-groups-membership-criteria)

-   [Ellenőrizze a csoporthoz hozzárendelt licencekkel](#check-a-groups-assigned-licenses)

-   [Újból feldolgozza a csoport licencek](#reprocess-a-groups-licenses)

-   [Egy csoport a licenc hozzárendelése](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Ellenőrizze a csoport tagságát

Ellenőrizze a csoport tagságát, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresési** érdekli csoport és **sorára kattintson** kiválasztásához.

7.  Kattintson a **tagok** tekintse át a listában, ehhez a csoporthoz tartozó felhasználók számára.

### <a name="check-a-dynamic-groups-membership-criteria"></a>A dinamikus csoport tagsági feltételek ellenőrzése 

A dinamikus csoport tagsági feltételek ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresési** érdekli csoport és **sorára kattintson** kiválasztásához.

7.  Kattintson a **dinamikus tagsági szabályok szempontjából.**

8.  Tekintse át a **egyszerű** vagy **speciális** szabály definiálva, és győződjön meg arról, hogy ez a csoport tagjai kívánt felhasználó megfelel-e ezek a feltételek.

### <a name="check-a-groups-assigned-licenses"></a>Ellenőrizze a csoporthoz hozzárendelt licencekkel

A csoporthoz hozzárendelt licencekkel ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresési** érdekli csoport és **sorára kattintson** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amely licencek, a csoport jelenleg hozzá van rendelve.

### <a name="reprocess-a-groups-licenses"></a>Újból feldolgozza a csoport licencek

Újból feldolgozza a csoporthoz hozzárendelt licenceket, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresési** érdekli csoport és **sorára kattintson** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amely licencek, a csoport jelenleg hozzá van rendelve.

8.  Kattintson a **újból feldolgozza** gombra kattintva győződjön meg arról, hogy a csoport tagjai a licenccel naprakészek legyenek. Ez eltarthat egy ideig, attól függően, hogy méretét és összetettségét, a csoport.

   >[!NOTE]
   >Ehhez gyorsabb, érdemes lehet ideiglenesen rendel egy licencet a felhasználó közvetlenül. [A felhasználó rendel egy licencet](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Egy csoport a licenc hozzárendelése

Licenc hozzárendelése egy csoportot, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresési** érdekli csoport és **sorára kattintson** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amely licencek, a csoport jelenleg hozzá van rendelve.

8.  Kattintson a **hozzárendelése** gombra.

9.  Válassza ki **egy vagy több termék** választható termékek közül.

10. **Nem kötelező** kattintson a **hozzárendelés beállításai** elem termékek granularly hozzárendelni. Kattintson a **Ok** amikor ez befejeződik.

11. Kattintson a **hozzárendelése** gomb a licencek hozzárendelése ehhez a csoporthoz. Ez eltarthat egy ideig, attól függően, hogy méretét és összetettségét, a csoport.

   >[!NOTE]
   >Ehhez gyorsabb, érdemes lehet ideiglenesen rendel egy licencet a felhasználó közvetlenül. [A felhasználó rendel egy licencet](#problems-with-application-consent).
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>Feltételes hozzáférési szabályzatokkal kapcsolatos problémák

### <a name="check-a-specific-conditional-access-policy"></a>Egy adott feltételes hozzáférési házirend ellenőrzése

Ellenőrizze, illetve ellenőrizhető egy feltételes hozzáférési szabályzatot:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** a navigációs menü.

5.  Kattintson a **feltételes hozzáférés** navigációs elem.

6.  Kattintson a érdekli ellenőrzése.

7.  Ellenőrizze, hogy nincsenek-e nincs meghatározott feltételek, hozzárendelések vagy egyéb beállításokat, amelyek letilthatja a felhasználói hozzáférés.

   >[!NOTE]
   >Kezdésként érdemes lehet ideiglenesen letilthatja ezt a házirendet, győződjön meg arról, hogy nem érinti a indított bejelentkezések. Ehhez az szükséges, állítsa be a **házirend engedélyezése** kapcsolót **nem** , és kattintson a **mentése** gombra.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Egy adott alkalmazás feltételes hozzáférési házirend ellenőrzése

Ellenőrizze, vagy egyetlen alkalmazás jelenleg ellenőrizni konfigurált feltételes hozzáférési szabályzatot:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** a navigációs menü.

5.  Kattintson a **összes alkalmazás**.

6.  Keresse meg az alkalmazás érdekli, vagy a felhasználó próbál bejelentkezni az alkalmazás megjelenített neve vagy azonosítója.

     >[!NOTE]
     >Ha nem látja a keresett alkalmazás, kattintson a **szűrő** gombra, majd bontsa ki a listában hatóköre **összes alkalmazás**. Ha meg szeretné tekinteni a további oszlopok, kattintson a **oszlopok** gombra kattintva adja hozzá a további részletek az alkalmazások számára.
     >
     >

7.  Kattintson a **feltételes hozzáférés** navigációs elem.

8.  Kattintson a érdekli ellenőrzése.

9.  Ellenőrizze, hogy nincsenek-e nincs meghatározott feltételek, hozzárendelések vagy egyéb beállításokat, amelyek letilthatja a felhasználói hozzáférés.

     >[!NOTE]
     >Kezdésként érdemes lehet ideiglenesen letilthatja ezt a házirendet, győződjön meg arról, hogy nem érinti a indított bejelentkezések. Ehhez az szükséges, állítsa be a **házirend engedélyezése** kapcsolót **nem** , és kattintson a **mentése** gombra.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Egy adott feltételes hozzáférési házirend letiltása

Ellenőrizze, illetve ellenőrizhető egy feltételes hozzáférési szabályzatot:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** a navigációs menü.

5.  Kattintson a **feltételes hozzáférés** navigációs elem.

6.  Kattintson a érdekli ellenőrzése.

7.  Tiltsa le a házirendet úgy, hogy a **házirend engedélyezése** kapcsolót **nem** , és kattintson a **mentése** gombra.

## <a name="problems-with-application-consent"></a>Az alkalmazás hozzájárulásával problémák

Alkalmazás-hozzáférés blokkolható, mert a megfelelő engedélyekkel a hozzájárulási művelet nem történt. Az alábbiakban néhány alkalmazás hozzájárulási problémák megoldásához és hibaelhárítást végezhessen módját:

-   [A felhasználói szintű hozzájárulási művelet végrehajtása](#perform-a-user-level-consent-operation)

-   [Bármely alkalmazás rendszergazdai hozzájárulási művelethez](#perform-administrator-level-consent-operation-for-any-application)

-   [Hajtsa végre a rendszergazdai hozzájárulási egyetlen bérlői alkalmazások](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Hajtsa végre a rendszergazdai hozzájárulási egy több-bérlős alkalmazáshoz](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>A felhasználói szintű hozzájárulási művelet végrehajtása

-   Bármely Open ID Connect-kompatibilis alkalmazás, amelyet engedélyeket igényel az alkalmazás bejelentkezési képernyő, lépjen az alkalmazásnak a bejelentkezett felhasználó számára egy felhasználói szintű hozzájárulás hajt végre.

-   Ha szeretne ehhez programozott módon, lásd: [egyes felhasználói hozzájárulás kérése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Bármely alkalmazás rendszergazdai hozzájárulási művelethez

-   A **csak a V1 alkalmazásmodell használatával fejlesztett alkalmazások**, beállíthatja, hogy a rendszergazda szintű hozzájárulási hozzáadásával megtörténik "**? rendszergazdai parancssorból =\_hozzájárulás**" végére egy az alkalmazás bejelentkezési URL-címben.

-   A **bármely alkalmazás fejlesztett az V2 alkalmazásmodell**, kényszerítheti az utasítások alapján történik a rendszergazdai hozzájárul a **az engedélyeket kérhet a directory-rendszergazda** szakasza [használatával a rendszergazda jóváhagyását végpont](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Hajtsa végre a rendszergazdai hozzájárulási egyetlen bérlői alkalmazások

-   A **egyetlen bérlői alkalmazások** (mint fejleszt, vagy a szervezet saját), engedélyek kéréséhez, amely hajthat végre egy **rendszergazdai szintű hozzájárulási** nevében összes művelet Jelentkezzen be globális rendszergazdaként, majd kattintson a felhasználók a **engedélyeket** gomb tetején a **alkalmazás beállításjegyzék -&gt; összes alkalmazás -&gt; válasszon ki egy alkalmazást -&gt; Szükséges engedélyek** ablaktáblán.

-   A **bármely alkalmazás fejlesztett a V1 vagy V2 alkalmazásmodell**, kényszerítheti a rendszergazdai hozzájárul az utasítások alapján történik a **az engedélyeket kérhet a directory-rendszergazda**  szakasza [használatával a rendszergazda jóváhagyását végpont](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Hajtsa végre a rendszergazdai hozzájárulási egy több-bérlős alkalmazáshoz

-   A **több-bérlős alkalmazásokhoz** adott kérelem engedélyek (például egy alkalmazás egy harmadik féltől származó, vagy a Microsoft, házon belül fejlesztett alkalmazásokra) hajthat végre egy **rendszergazdai szintű hozzájárulási** műveletet. Jelentkezzen be globális rendszergazdaként, és kattintson a a **engedélyeket** alatt gombra a **vállalati alkalmazások –&gt; összes alkalmazás -&gt; válasszon ki egy alkalmazást -&gt; engedélyek**  ablaktáblán (rendelkezésre álló hamarosan).

-   Is előírható, a rendszergazdai hozzájárul az utasítások alapján történik a **az engedélyeket kérhet a directory-rendszergazda** szakasza [használatával a rendszergazda jóváhagyását végpont](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>További lépések
[A rendszergazda jóváhagyását végpont használatával](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

