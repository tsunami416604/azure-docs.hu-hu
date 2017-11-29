---
title: "Az Azure AD: A jelszó alaphelyzetbe állítása |} Microsoft Docs"
description: "Az önkiszolgáló jelszó-változtatási újra hozzáférést nyerni a munkahelyi vagy iskolai fiókkal való használata"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: b91902b13e7633a0724a57ff7fceb68e50abd13a
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="reset-your-work-or-school-password"></a>A munkahelyi vagy iskolai jelszó alaphelyzetbe állítása

Ha elfelejtette a jelszavát, soha nem kapott a vállalat támogatási közül, a fiókjából zárolva van, vagy szeretné módosítani, segítünk. A jelszó ismerete, és csak kell megváltoztatnia, ha továbbra is a [jelszó módosítása](#change-my-password) szakasz.

   > [!NOTE]
   > Ha próbál visszaszerzésében Xbox, hotmail.com vagy Outlook.com-os személyes fiókja, próbálja meg a javaslatok a [mikor nem tud bejelentkezni Microsoft-fiókja](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Jelszó visszaállítása vagy zárolásának feloldása munkahelyi vagy iskolai fiókon

Előfordulhat, hogy az Azure Active Directory (Azure AD-) fiók eléréséhez a következő okok valamelyike miatt nem tud:

* A jelszó nem működik, és azt szeretné, hogy állítsa alaphelyzetbe.
* Jelszó, de a fiók zárolva van, és szeretné oldani a zárolását.

A következő lépésekkel eléréséhez az Azure AD az önkiszolgáló jelszó-változtatási (SSPR), és visszatérhet fiókját.

1. Bármelyik munkahelyi vagy iskolai **bejelentkezési** lapon jelölje be a **nem fér hozzá a fiókjához?** hivatkozásra, és válassza ki **munkahelyi vagy iskolai fiók** vagy közvetlenül a [ Jelszó-átállítási lap](https://passwordreset.microsoftonline.com/).

    ![Nem érhető el a fiókja?][Login]

2. Adja meg a munkahelyi vagy iskolai **Felhasználóazonosító**, a rendszer még nem robot, írja be a képernyőn látható, és válassza a karakterek **tovább**.

   > [!NOTE]
   > Az informatikai részleg nem engedélyezte ezt a funkciót, ha az "Forduljon a rendszergazdához" hivatkozást, e-mailben vagy saját webes portál segítségével az informatikai munkatársak jelenik meg.
   >
   > Ha a fiók zárolását kívánja feloldani van szüksége, ezen a ponton a beállítást a **tudom a jelszavam, de mégsem tudok bejelentkezni.**
   >

3. Attól függően, hogy az informatikai munkatársak konfigurálta az önkiszolgáló jelszó-Változtatási meg kell jelennie az alábbi hitelesítési módszerek közül legalább egyet. Ön vagy az informatikai munkatársak kell van feltöltve az adatok egy részét a lépések a [az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md) cikk.

   * **E-mail küldése a másodlagos e-mail-címemre**
   * **Szöveges üzenet a mobiltelefonomra**
   * **Hívást kérek a mobiltelefonomra**
   * **Hívást kérek az irodai telefonomra**
   * **Biztonsági kérdések megválaszolása**

   Válasszon egy lehetőséget, adja meg a helyes választ, és válassza **következő**.

   ![A hitelesítési adatok megerősítése][Verification]

4. Az informatikai munkatársak módosítania kell a további ellenőrzést, és ismételje meg a 3. lépés a másik adatbázissal lehet.
5. Az a **írjon be egy új jelszót** lapon adjon meg egy új jelszót, erősítse meg a jelszót, és válassza ki **Befejezés**. A munkahelyi vagy iskolai jelszó bizonyos igazodnia kell igénylő igényei lehetnek az. Javasoljuk, hogy, hogy írjon be egy jelszót, amely 8 – 16 karakter hosszú, és magában foglalja a kis-és nagybetűk, számot és egy speciális karaktert.
6. Amikor megjelenik az üzenet **jelszavát átállították**, bejelentkezhet az új jelszót.

    ![A jelszó vissza lett állítva][Complete]

Most kell tudni elérni a fiókját. Ha nem fér hozzá a fiókjához, akkor lépjen kapcsolatba a szervezet informatikai munkatársak további segítségért.

Akkor fordulhat elő, egy visszaigazoló e-mailben, előre olyan fiókhoz, például a "Microsoft nevében \<a szervezet >." Ha egy ehhez hasonló e-mailt kap, és nem használja az önkiszolgáló jelszó-változtatási frissítésével visszanyerheti hozzáférését a fiókjához, lépjen kapcsolatba a szervezet informatikai személyzetet tart fenn.

## <a name="change-my-password"></a>Saját jelszó módosítása

Ha már ismeri a jelszót, és szeretné módosítani, használja az alábbi lépéseket.

### <a name="change-your-password-from-the-office-365-portal"></a>A jelszó módosítása az Office 365 portálról

Ezt a módszert használja, ha az általában az alkalmazások az Office-portál:

1. Jelentkezzen be a [Office 365-fiók](https://www.office.com) a meglévő jelszóval.
2. Felső – jobb oldalán a profilt, majd válassza ki és **fiók megtekintéséhez**.
3. Válassza ki **biztonsági és adatvédelmi** > **jelszó**.
4. Adja meg a régi jelszavát, állítsa be és erősítse meg az új jelszót, majd válassza ki **Submit**.

### <a name="change-your-password-from-the-azure-access-panel"></a>A jelszó módosítása az Azure hozzáférési panelén

Ezt a módszert használja, ha a szokásos módon éri el az alkalmazások az Azure hozzáférési Panel (MyApps):

1. Jelentkezzen be a [Azure hozzáférési Panel](https://myapps.microsoft.com/) a meglévő jelszóval.
2. Felső – jobb oldalán a profilt, majd válassza ki és **profil**.
3. Válassza ki **jelszó módosítása**.
4. Adja meg a régi jelszavát, állítsa be és erősítse meg az új jelszót, majd válassza ki **Submit**.

## <a name="reset-password-at-sign-in"></a>Bejelentkezéskor a jelszó alaphelyzetbe állítása

Ha a rendszergazda engedélyezte a funkciót, megtekintheti egy hivatkozást **jelszó-átállítási** a Windows 10 alá esik Creators frissítés bejelentkezési képernyő.

![Bejelentkezési képernyő][LoginScreen]

Válassza ki a **jelszó-átállítási** hivatkozásra kattintva nyissa meg az önkiszolgáló jelszó-Változtatási élmény, a bejelentkezési képernyő, úgy, hogy az új jelszó nélkül jelentkezzen be a normál web-alapú felhasználói élmény eléréséhez.

1. Erősítse meg a felhasználói Azonosítóját, és válassza ki **következő**.
2. Válassza ki, és erősítse meg a kapcsolattartási módot ellenőrzése. Az informatikai munkatársak módosítania kell a további ellenőrzést, és ismételje meg ezt a lépést, a másik adatbázissal lehet.

   ![Kapcsolatfelvétel módja][ContactMethod]

3. Az a **hozzon létre egy új jelszót** lapon adjon meg egy új jelszót, erősítse meg a jelszót, és válassza ki **következő**. Javasoljuk, hogy a jelszó 8 – 16 karakter hosszú, és kis-és nagybetűk, számok és speciális karaktereket tartalmaz.

   ![Új jelszó létrehozása][ResetPassword]

4. Amikor megjelenik az üzenet **jelszavát átállították**, jelölje be **Befejezés**.

Most kell tudni elérni a fiókját. Ha nem, lépjen kapcsolatba a szervezet informatikai munkatársak további segítségért.

## <a name="common-problems-and-their-solutions"></a>Gyakori problémák és megoldásuk

 Az alábbiakban néhány gyakori hibák és a megoldások:

| Hiba eset| Milyen hiba látható?| Megoldás |
| --- | --- | --- |
| "Forduljon a rendszergazdához" oldal jelenik meg a felhasználói azonosító megadása után | Lépjen kapcsolatba a rendszergazdával. <br> <br> A rendszer azt észlelte, hogy a felhasználói fiók jelszava nem Microsoft által felügyelt. Ennek eredményeképpen jelenleg nem lehet automatikusan alaphelyzetbe állítja a jelszót. <br> <br> További segítségért forduljon az informatikai munkatársak kell. | Ezt az üzenetet is lát, mert az informatikai munkatársak kezeli a jelszót a helyszíni környezetben. "Nem fér hozzá a fiókjához" hivatkozást a jelszó nem állítható alaphelyzetbe. <br> <br> A jelszó alaphelyzetbe állításához, közvetlenül segítségért lépjen kapcsolatba az informatikai munkatársak, és tájékoztassa, alaphelyzetbe állítja a jelszót, ez a funkció az Ön is engedélyezhető.|
| A felhasználói azonosító megadása után jelenik meg a "a nem engedélyezett a jelszó alaphelyzetbe állítása" hiba | A fiók nincs engedélyezve a jelszó alaphelyzetbe állítása. <br> <br> Sajnáljuk, de az informatikai munkatársak nem állította be a fiókját a szolgáltatás használatához. <br> <br> Ha azt szeretné, a jelszó alaphelyzetbe állításához a szervezet rendszergazda is megkereshetjük. | Ezt az üzenetet is lát, mert az informatikai munkatársak nem engedélyezte a jelszó alaphelyzetbe állítása a szervezet "Nem fér hozzá a fiókjához" hivatkozást, vagy még nem rendelkezik licenccel, hogy a szolgáltatás használatához. <br> <br> A jelszó alaphelyzetbe állításához, válassza a "lépjen kapcsolatba az rendszergazdai kapcsolat" egy e-mailt küldhet a vállalat informatikai személyzetet tart fenn, és tájékoztassa, alaphelyzetbe állítja a jelszót, ez a funkció az Ön is engedélyezhető. |
| A felhasználói azonosító megadása után jelenik meg a "Nem tudtuk ellenőrizni fiókját" hiba | Nem tudtuk ellenőrizni a fiókját. <br> <br> Ha azt szeretné, a jelszó alaphelyzetbe állításához a szervezet rendszergazda is megkereshetjük. | Most azt láthatja ezt az üzenetet, mert a jelszó alaphelyzetbe állítása folyamatban engedélyezve van, de a szolgáltatás használatára nem regisztrált. Regisztrálhatnak a jelszóváltoztatásra, http://aka.ms/ssprsetup után lépjen a fiókjába rendelkeznie helyreállt a hozzáférést. <br> <br> A jelszó alaphelyzetbe állításához, jelölje ki a "kérje a rendszergazda" kapcsolatot egy e-mailt küldhet a vállalat informatikai személyzetet tart fenn. |

## <a name="next-steps"></a>Következő lépések

* [Regisztrálás az önkiszolgáló jelszó-visszaállítás használatára](active-directory-passwords-reset-register.md)
* [Jelszó-visszaállítási regisztrációs oldal](http://aka.ms/ssprsetup)
* [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)
* [Nem tud bejelentkezni a Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Bejelentkezési oldal – Nem érhető el a fiókja?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "A hitelesítési adatok megerősítése"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Jelszó módosítása"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A jelszó vissza lett állítva"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Windows 10 alá esik Creators frissítés bejelentkezési képernyő alaphelyzetbe állítása jelszó-hivatkozás"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "A hitelesítési adatok megerősítése"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Jelszó módosítása"

