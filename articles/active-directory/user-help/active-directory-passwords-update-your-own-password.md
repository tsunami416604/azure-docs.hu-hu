---
title: Új Azure AD - jelszó |} A Microsoft Docs
description: Az önkiszolgáló jelszó-visszaállítás, hozzáférhet a munkahelyi vagy iskolai fiók használata
services: active-directory
keywords: ''
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.custom: end-user
ms.openlocfilehash: cf26ba4048f4bd7a7ca3f1f2c456e3f2ec7d3b99
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059787"
---
# <a name="reset-your-work-or-school-password"></a>A munkahelyi vagy iskolai jelszó visszaállítása

Ha elfelejtette a jelszavát, soha nem kapott a cég informatikai támogatási szolgálata, használja ki a fiók zárolva van, vagy módosítani szeretné, forduljon hozzánk bizalommal. Ha ismeri a jelszavát, és csak meg kell módosítani, továbbra is a [jelszavamra](#change-my-password) szakaszban.

   > [!NOTE]
   > Ha szeretne visszaszerzésében a személyes fiókot, mint az Xbox, a hotmail.com vagy Outlook.com-os, próbálja meg a javaslatok a [mikor nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Jelszó visszaállítása vagy zárolásának feloldása munkahelyi vagy iskolai fiókon

Előfordulhat, hogy nem tud elérni az Azure Active Directory (Azure AD-) fiók az alábbi okok egyike miatt:

* A jelszó nem működik, és azt szeretné, hogy állítsa alaphelyzetbe.
* Ismeri a jelszavát, de a fiók zárolva van, és azt szeretné, hogy bekapcsoljon.

Használja az alábbi lépéseket eléréséhez az Azure AD önkiszolgáló jelszó-visszaállítás (SSPR), majd fiókjába.

1. Bármelyik munkahelyi vagy iskolai **bejelentkezési** lapon válassza a **nem tudja elérni a fiókját?** hivatkozásra, és válassza ki **munkahelyi vagy iskolai fiók** vagy lépjen közvetlenül a [ Jelszó-visszaállítási oldalra](https://passwordreset.microsoftonline.com/).

    ![Nem érhető el a fiókja?][Login]

2. Adja meg a munkahelyi vagy iskolai **Felhasználóazonosító**, Ön nem robot, a képernyőn látható, és válassza a karakterek megadásával igazolja **tovább**.

   > [!NOTE]
   > Ha az informatikai részleg nem engedélyezte ezt a funkciót, a "Forduljon a rendszergazdához" hivatkozást jelenik meg, így az informatikai részleg segíthetnek e-mailben vagy saját webes portálon.
   >
   > Fiók zárolásának feloldása van szüksége, ha ezen a ponton a lehetőséggel a **tudom a jelszavam, de mégsem tudok bejelentkezni.**
   >

3. Attól függően, hogy az informatikai részleg konfigurálta az SSPR kell megjelennie a következő hitelesítési módszerek közül legalább egyet. Ön vagy az informatikai részleg kell ki vannak töltve az információk a lépéseket követve a [regisztráció önkiszolgáló jelszó-visszaállításra](active-directory-passwords-reset-register.md) cikk.

   * **E-mail küldése a másodlagos e-mail-címemre**
   * **Szöveges üzenet a mobiltelefonomra**
   * **Hívást kérek a mobiltelefonomra**
   * **Hívást kérek az irodai telefonomra**
   * **Biztonsági kérdések megválaszolása**

   Válasszon egy lehetőséget, adja meg a helyes válaszokat, és válassza **tovább**.

   ![A hitelesítési adatok megerősítése][Verification]

4. Szükség lehet a ismételje meg a 3. lépés egy másik lehetőséget választva, és az informatikai részleg további ellenőrzés szükséges.
5. Az a **új jelszó** lapon adjon meg egy új jelszót, erősítse meg a jelszót, és válassza ki **Befejezés**. A munkahelyi vagy iskolai jelszó előfordulhat, hogy meg kell felelniük az egyes követelmények. Javasoljuk, hogy válassza ki a jelszó 8 – 16 karakter hosszúságú, és a kis- és kisbetűs karakterek, egy szám és egy speciális karaktert tartalmazó.
6. Amikor látja az üzenetet **jelszavát átállították**, bejelentkezhet új jelszavával.

    ![A jelszó vissza lett állítva][Complete]

Most kell tudni elérni a fiókját. Ha nem tud belépni fiókjába, forduljon a szervezet informatikai részleghez további segítségért.

Előfordulhat, hogy kap egy megerősítő e-mailt, amely egy olyan fiók, mint például "a Microsoft nevében \<a szervezet >." Ha ehhez hasonló e-mailt kap, és nem használja az önkiszolgáló jelszó-visszaállítás lehet hozzáférni a fiókjához, lépjen kapcsolatba a szervezet informatikai személyzetet tart fenn.

## <a name="change-my-password"></a>Saját jelszó módosítása

Ha már ismeri a jelszavát, és módosítani szeretné, kövesse az alábbi lépéseket.

### <a name="change-your-password-from-the-office-365-portal"></a>A jelszó módosítása az Office 365 portálról

Ezt a módszert használja, ha az alkalmazások általában éri el az Office portálon keresztül:

1. Jelentkezzen be a [Office 365-fiókja](https://www.office.com) a meglévő jelszóval.
2. A jobb felső oldalon, majd válassza ki és **fiók megtekintése**.
3. Válassza ki **biztonság és adatvédelem** > **jelszó**.
4. Adja meg régi jelszavát, állítsa be és erősítse meg az új jelszót, és válassza **küldés**.

### <a name="change-your-password-from-the-azure-access-panel"></a>A jelszó módosítása az Azure hozzáférési panelén

Ezt a módszert akkor használja, ha a szokásos módon éri el az alkalmazások az Azure hozzáférési panelén (MyApps):

1. Jelentkezzen be a [Azure hozzáférési panelén](https://myapps.microsoft.com/) a meglévő jelszóval.
2. A jobb felső oldalon, majd válassza ki és **profil**.
3. Válassza ki **jelszó módosítása**.
4. Adja meg régi jelszavát, állítsa be és erősítse meg az új jelszót, és válassza **küldés**.

## <a name="reset-password-at-sign-in"></a>Bejelentkezés jelszó alaphelyzetbe állítása

Ha a rendszergazda engedélyezte a funkciót, mutató hivatkozást most már megtekintheti **jelszó alaphelyzetbe állítása** a Windows 10 Fall Creators Update bejelentkezési képernyőt.

![Bejelentkezési képernyő][LoginScreen]

Válassza ki a **jelszó alaphelyzetbe állítása** hivatkozásra kattintva nyissa meg a bejelentkezési képernyőn az SSPR-felület, úgy, hogy jelentkezzen be a szokásos webes élményt eléréséhez nélkül alaphelyzetbe állíthatja a jelszót.

1. Erősítse meg a felhasználói Azonosítóját, és válassza ki **tovább**.
2. Válassza ki, és erősítse meg a kapcsolattartási módszer az ellenőrzéshez. Szükség lehet a ismételje ezt a lépést egy másik lehetőséget választva, és az informatikai részleg további ellenőrzés szükséges.

   ![Kapcsolatfelvétel módja][ContactMethod]

3. Az a **hozzon létre egy új jelszót** lapon adjon meg egy új jelszót, erősítse meg a jelszót, és válassza ki **tovább**. Javasoljuk, hogy a jelszó 8 – 16 karakter hosszú, és kis-és nagybetűk, számok és speciális karaktereket tartalmaz.

   ![Új jelszó létrehozása][ResetPassword]

4. Amikor látja az üzenetet **jelszavát átállították**válassza **Befejezés**.

Most kell tudni elérni a fiókját. Ha nem, forduljon a szervezet informatikai részleghez további segítségért.

## <a name="common-problems-and-their-solutions"></a>Gyakori problémák és megoldásaik

 Az alábbiakban néhány gyakori hibák és megoldásaik:

| Hibaesetét| Milyen hibaüzenetet lát?| Megoldás |
| --- | --- | --- |
| Látható hiba jelenik meg a jelszó módosítására. | Sajnos a jelszó tartalmaz egy szót, kifejezést vagy mintát, amely lehetővé teszi a jelszó könnyen kitalálható. Próbálkozzon újra egy másik jelszóval. | Válasszon egy nehezebben kitalálható jelszót. |
| A felhasználói azonosító megadása után jelenik meg a "Forduljon a rendszergazdához" lap | Forduljon a rendszergazdához. <br> <br> Azt észleltük, hogy a Microsoft által nem felügyelt felhasználói fiókjának jelszavát. Ennek eredményeképpen nem tudjuk automatikusan alaphelyzetbe állítja a jelszót. <br> <br> További segítségért forduljon az informatikai részleg kell. | Ezt az üzenetet lát, mivel az informatikai részleg kezeli a jelszót a helyszíni környezetben. A "Nem tud belépni fiókjába" hivatkozásra a jelszó nem állítható alaphelyzetbe. <br> <br> A jelszó alaphelyzetbe állításához, közvetlenül kérje az informatikai részleg segítségét, és tudassa vele, hogy azt szeretné, így az Ön számára ez a funkció is engedélyezhető a jelszó alaphelyzetbe állításához.|
| Egy "a fiók a nem engedélyezett a jelszó-visszaállításhoz" hiba jelenik meg a felhasználói azonosító megadása után | A fiók nincs engedélyezve a jelszó-visszaállításhoz. <br> <br> Sajnáljuk, de az informatikai részleg nem állította be a fiókját a szolgáltatás használatához. <br> <br> Ha szeretné, elküldhetjük a rendszergazda a szervezet a jelszó alaphelyzetbe állításához. | Ezt az üzenetet lát, mert az informatikai részleg nem engedélyezte a jelszó-visszaállítást a "Nem tud belépni fiókjába" hivatkozást a szervezet számára, vagy még nem rendelkezik licenccel, hogy a funkció használatához. <br> <br> Új jelszót, válassza a "forduljon egy rendszergazdai kapcsolat" egy e-mailt küldhet a vállalat informatikai személyzetet tart fenn, és tudassa vele, hogy azt szeretné, így az Ön számára ez a funkció is engedélyezhető a jelszó alaphelyzetbe állításához. |
| A felhasználói azonosító megadása után jelenik meg a "Nem tudtuk ellenőrizni a fiók" hiba | Nem tudtuk ellenőrizni fiókját. <br> <br> Ha szeretné, elküldhetjük a rendszergazda a szervezet a jelszó alaphelyzetbe állításához. | Ezt az üzenetet lát, mert a jelszó-visszaállítás engedélyezve vannak, de nem regisztrált a szolgáltatás használatához. Regisztráljanak a jelszóátállításra, lépjen a https://aka.ms/ssprsetup után, rendelkezik helyreállt hozzáférést a fiókjához. <br> <br> A jelszó alaphelyzetbe állításához válassza ki a "forduljon a rendszergazdához" hivatkozásra kattintva egy e-mailt küldhet a vállalati informatikai személyzetet tart fenn. |

## <a name="next-steps"></a>További lépések

* [Regisztrálás az önkiszolgáló jelszó-visszaállítás használatára](active-directory-passwords-reset-register.md)
* [Jelszó-visszaállítási regisztrációs oldal](https://aka.ms/ssprsetup)
* [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)
* [Nem tud bejelentkezni a Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Bejelentkezési oldal – Nem érhető el a fiókja?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "A hitelesítési adatok megerősítése"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Jelszó módosítása"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A jelszó vissza lett állítva"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Windows 10 Fall Creators Update bejelentkezési képernyő jelszó-visszaállítási hivatkozás"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "A hitelesítési adatok megerősítése"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Jelszó módosítása"
