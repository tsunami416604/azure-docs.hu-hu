---
title: "Azure AD: Jelszó visszaállítása | Microsoft Docs"
description: "Az önkiszolgáló jelszó-változtatási újra hozzáférést nyerni a munkahelyi vagy iskolai fiók használata"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: gahug
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 763248b1b7adf821d37f2308195a0934a0aee152
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="reset-your-work-or-school-password"></a>A munkahelyi vagy iskolai jelszó alaphelyzetbe állítása

Ha elfelejtette a jelszavát, soha nem kapott a vállalat támogatási közül, zárolja a fiókot, vagy módosítani azt szeretnénk, segítünk. Ha tudja, a jelszót, és csak kell módosítása továbbra is le a szakasz [jelszó módosítása](#change-my-password) alatt.

   > [!NOTE]
   > Ha például Xbox, hotmail.com vagy Outlook.com-os próbálja meg a személyes fiókja visszaszerzéséhez szükséges segítségnyújtáshoz kívánt a [ebben a cikkben található javaslatok](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Jelszó visszaállítása vagy zárolásának feloldása munkahelyi vagy iskolai fiókon

Ha Ön nem fér hozzá a fiókjához a következők valamelyike lehet:

* A jelszó nem működik, és szeretné, hogy állítsa alaphelyzetbe.
* A jelszóra, de a fiók zárolva van, és szeretné, hogy a fiók zárolásának feloldásához.

Az Azure AD az önkiszolgáló jelszó-visszaállítás, önkiszolgáló jelszó-Változtatási, hívják, és a fiókja visszaszerzéséhez szükséges segítségnyújtáshoz hangvételünk eléréséhez az alábbi lépésekkel.

1. Bármelyik munkahelyi vagy iskolai bejelentkezési oldalon kattintson a **Nem érhető el a fiókja?** hivatkozásra, majd a **Munkahelyi vagy iskolai fiók** lehetőségre, vagy lépjen közvetlenül a [jelszó-visszaállítási oldalra](https://passwordreset.microsoftonline.com/).

    ![Nem érhető el a fiókja?][Login]

2. Adja meg munkahelyi vagy iskolai **felhasználói azonosítóját**, a képernyőn megjelenő karakterek beírásával igazolja, hogy Ön nem robot, majd kattintson a **Tovább** gombra.

   > [!NOTE]
   > Ha az informatikai részleg nem engedélyezte ezt a funkciót, egy hivatkozás a rendszergazdához irányítja, aki e-mailben vagy saját webes portálon nyújt segítséget.
   >
   > Ha a fiók zárolását kívánja feloldani van szüksége, ezen a ponton választania "Tudom a jelszavam, de mégsem tudok bejelentkezni".
   >

3. Attól függően, hogy az informatikai munkatársak konfigurálta az önkiszolgáló jelszó-Változtatási láthatja az alábbi hitelesítési módszerek közül legalább egyet. Ön vagy az informatikai munkatársak az információk a cikk használata előtt van kitöltve [az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md).

   * **E-mail küldése a másodlagos e-mail-címemre**
   * **Szöveges üzenet a mobiltelefonomra**
   * **Hívást kérek a mobiltelefonomra**
   * **Hívást kérek az irodai telefonomra**
   * **Biztonsági kérdések megválaszolása**

   Válasszon egy lehetőséget, adja meg a helyes válaszokat, és kattintson a **Tovább** gombra.

   ![A hitelesítési adatok megerősítése][Verification]

4. A rendszergazdák megkövetelhetnek egy további jóváhagyási lépést is, és ilyenkor még egyszer végre kell hajtania a 3. lépést egy másik lehetőséget választva.
5. Az **Új jelszó megadása** oldalon adjon meg egy új jelszót, erősítse meg, majd kattintson a **Befejezés** gombra. A munkahelyi vagy iskolai jelszó rendelkezhetnek bizonyos követelmények; Javasoljuk, hogy a jelszó 8 – 16 karakterből állhat, és a kis- és nagybetűk, számok és speciális karakterek lehet.
6. Miután megjelent **A jelszó vissza lett állítva** üzenet, bejelentkezhet új jelszavával.

    ![A jelszó vissza lett állítva][Complete]

Most már be kell tudnia lépni a fiókjába. Ha mégsem, forduljon a vállalati informatikai részleghez további segítségért.

Egy visszaigazoló e-mailben, például a fiókból előre jelenhet meg "a következő nevében: Microsoft \<a szervezet >". Ha egy ilyen e-mailt kap, de nem végezte el az önkiszolgáló jelszó-visszaállítási eljárást a fiókja feloldásához, forduljon a vállalat informatikai részlegéhez.

## <a name="change-my-password"></a>Saját jelszó módosítása

Ha már ismeri a jelszavát, és módosítani szeretné, kövesse az alábbi lépéseket.

### <a name="change-your-password-from-the-office-365-portal"></a>A jelszó módosítása az Office 365 portálról

Ezt a módszert akkor használja, ha az általában rendesen az Office portálon keresztül éri el

1. Jelentkezzen be a [Office 365-fiók](https://www.office.com) a meglévő jelszóval
2. Kattintson a profiljára felül a jobb oldalon, majd kattintson a **Fiók megtekintése** elemre.
3. Kattintson a **Biztonság és adatvédelem** > **Jelszó** elemre.
4. Adja meg régi jelszavát, állítsa be és erősítse meg az új jelszót, majd kattintson a **Küldés** gombra.

### <a name="change-your-password-from-the-azure-access-panel"></a>A jelszó módosítása az Azure hozzáférési panelén

Ezt a módszert akkor használja, ha az alkalmazásait általában az Azure Access Portalon keresztül éri el

1. Jelentkezzen be az [Azure Access Portalra](https://myapps.microsoft.com/) meglévő jelszavával
2. Kattintson a profiljára felül a jobb oldalon, majd kattintson a **Profil** elemre.
3. Kattintson a **Jelszó módosítása** elemre.
4. Adja meg régi jelszavát, állítsa be és erősítse meg az új jelszót, majd kattintson a **Küldés** gombra.

## <a name="common-problems-and-their-solutions"></a>Gyakori problémák és megoldásuk

 Az alábbiakban néhány gyakori hibák és a megoldások:

| Hiba eset| Milyen hiba látható?| Megoldás |
| --- | --- | --- |
| "Forduljon a rendszergazdához" oldal jelenik meg a felhasználói azonosító megadása után | Lépjen kapcsolatba a rendszergazdával <br> <br> A rendszer azt észlelte, hogy a felhasználói fiók jelszava nem Microsoft által felügyelt. Ennek eredményeképpen jelenleg nem lehet automatikusan alaphelyzetbe állítja a jelszót. <br> <br> További segítségért forduljon az informatikai munkatársak kell. | Ez az üzenet azért jelent meg, mert az informatikai munkatársak kezeli a jelszót a helyszíni környezetben, és nem teszi lehetővé a jelszó a nem tudja elérni a fiókot hivatkozásra. <br> <br> A jelszó alaphelyzetbe állításához, közvetlenül segítségért lépjen kapcsolatba az informatikai munkatársak, és tájékoztassa, alaphelyzetbe állítja a jelszót, ez a funkció az Ön is engedélyezhető.|
| A felhasználói azonosító megadása után jelenik meg a "a nem engedélyezett a jelszó alaphelyzetbe állítása" hiba | A fiók nincs engedélyezve a jelszó alaphelyzetbe állítása <br> <br> Sajnáljuk, de az informatikai munkatársak nem állította be a fiók a szolgáltatással való használatra. <br> <br> Ha azt szeretné, a jelszó alaphelyzetbe állításához a szervezet rendszergazda is megkereshetjük. | Ez az üzenet azért jelent, mert az informatikai munkatársak nincs engedélyezve a jelszó alaphelyzetbe állítása a szervezete számára a nem tudja elérni a fiókot hivatkozásra, vagy még nem rendelkezik licenccel a funkció használatát. <br> <br> A jelszó alaphelyzetbe állításához hivatkozásra az ügyfél egy rendszergazda e-mailt küldhet a vállalat informatikai személyzetet tart fenn, és tájékoztassa, alaphelyzetbe állítja a jelszót, ez a funkció az Ön is engedélyezhető. |
| A felhasználói azonosító megadása után jelenik meg a "nem tudtuk ellenőrizni fiókját" hiba | Nem tudtuk ellenőrizni a fiókját <br> <br> Ha azt szeretné, a jelszó alaphelyzetbe állításához a szervezet rendszergazda is megkereshetjük. | Ez az üzenet azért jelent meg, mert a jelszó-visszaállításhoz engedélyezve vannak, de a szolgáltatás használatára nem regisztrált. Regisztrálhatnak a jelszóváltoztatásra, http://aka.ms/ssprsetup után lépjen a fiókjába rendelkeznie helyreállt a hozzáférést. <br> <br> A jelszó alaphelyzetbe állításához hivatkozásra az ügyfél egy rendszergazda e-mailt küldhet a vállalat informatikai személyzetet tart fenn. |

## <a name="next-steps"></a>Következő lépések

* [Regisztrálás az önkiszolgáló jelszó-visszaállítás használatára](active-directory-passwords-reset-register.md)
* [Jelszó-visszaállítási regisztrációs oldal](http://aka.ms/ssprsetup)
* [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)
* [Nem tud bejelentkezni a Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Bejelentkezési oldal – Nem érhető el a fiókja?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "A hitelesítési adatok megerősítése"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Jelszó módosítása"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A jelszó vissza lett állítva"
