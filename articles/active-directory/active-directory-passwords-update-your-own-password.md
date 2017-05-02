---
title: "Azure AD: Jelszó visszaállítása | Microsoft Docs"
description: "Fiókhoz való hozzáférés visszanyerése az önkiszolgáló jelszó-visszaállítás használatával"
services: active-directory
keywords: "Active Directory-jelszókezelés, jelszókezelés, Azure AD önkiszolgáló jelszó-visszaállítás, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c95f52f028cdfaed35821275d816a24035ff02fb
ms.lasthandoff: 04/25/2017


---
# <a name="help-i-forgot-my-azure-ad-password"></a>Segítség, elfelejtettem Azure AD-jelszavamat

Ha elfelejtette a jelszavát, vagy nem kapott jelszót az informatikai kollégáktól, ki lett zárva a fiókjából, vagy csak le szeretné cserélni a jelszót, mi segíthetünk.

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Jelszó visszaállítása vagy zárolásának feloldása munkahelyi vagy iskolai fiókon

A munkahelyi vagy iskolai fiókjába való belépéshez kövesse az alábbi lépéseket az Azure AD önkiszolgáló jelszó-visszaállítás (SSPR, self-service password reset) eléréséhez.

1. Bármelyik munkahelyi vagy iskolai bejelentkezési oldalon kattintson a **Nem érhető el a fiókja?** hivatkozásra, majd a **Munkahelyi vagy iskolai fiók** lehetőségre, vagy lépjen közvetlenül a [jelszó-visszaállítási oldalra](https://passwordreset.microsoftonline.com/).

   > [!NOTE]
   > Ha egy személyes fiókba, például hotmail.com vagy outlook.com fiókba szeretne belépni, próbálkozzon [az ebben a cikkben javasolt módszerekkel](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
   >
    ![Nem érhető el a fiókja?][Login]

2. Adja meg munkahelyi vagy iskolai **felhasználói azonosítóját**, a képernyőn megjelenő karakterek beírásával igazolja, hogy Ön nem robot, majd kattintson a **Tovább** gombra.

   > [!NOTE]
   > Ha az informatikai részleg nem engedélyezte ezt a funkciót, egy hivatkozás a rendszergazdához irányítja, aki e-mailben vagy saját webes portálon nyújt segítséget.
   >

3. Az informatikai részleg által konfigurált SSPR-beállításoktól függően a következők valamelyike jelenik meg. Ezeket az adatokat Ön vagy a rendszergazdák adták meg korábban az [itt](active-directory-passwords-reset-register.md) található információk segítségével.
    * **E-mail küldése a másodlagos e-mail-címemre**
    * **Szöveges üzenet a mobiltelefonomra**
    * **Hívást kérek a mobiltelefonomra**
    * **Hívást kérek az irodai telefonomra**
    * **Biztonsági kérdések megválaszolása**

    Válasszon egy lehetőséget, adja meg a helyes válaszokat, és kattintson a **Tovább** gombra.

    ![A hitelesítési adatok megerősítése][Verification]

4. A rendszergazdák megkövetelhetnek egy további jóváhagyási lépést is, és ilyenkor még egyszer végre kell hajtania a 3. lépést egy másik lehetőséget választva.
5. Az **Új jelszó megadása** oldalon adjon meg egy új jelszót, erősítse meg, majd kattintson a **Befejezés** gombra. Javasoljuk, hogy a jelszó legyen 8–16 karakter hosszúságú, és tartalmazzon kis- és nagybetűket, számokat és speciális karaktereket is.

   > [!NOTE]
   > Ha a fiók zárolását szeretné feloldani, ezen a ponton eldöntheti, hogy csak feloldja a fiók zárolását, vagy a jelszavát is módosítja.
   >

6. Miután megjelent **A jelszó vissza lett állítva** üzenet, bejelentkezhet új jelszavával.

    ![A jelszó vissza lett állítva][Complete]

Most már be kell tudnia lépni a fiókjába. Ha mégsem, forduljon a vállalati informatikai részleghez további segítségért.

Előfordulhat, hogy kap egy megerősítő e-mailt, amelynek a feladója a „Microsoft az \<érintett szervezet>\" nevében” vagy hasonló. Ha egy ilyen e-mailt kap, de nem végezte el az önkiszolgáló jelszó-visszaállítási eljárást a fiókja feloldásához, forduljon a vállalat informatikai részlegéhez.

## <a name="change-my-password"></a>Saját jelszó módosítása

Ha már ismeri a jelszavát, és módosítani szeretné, kövesse az alábbi lépéseket.

### <a name="change-your-password-from-the-office-365-portal"></a>A jelszó módosítása az Office 365 portálról

Ezt a módszert akkor használja, ha az általában rendesen az Office portálon keresztül éri el

1. Jelentkezzen be [Office 365-fiókjába](https://www.office.com).
2. Kattintson a profiljára felül a jobb oldalon, majd kattintson a **Fiók megtekintése** elemre.
3. Kattintson a **Biztonság és adatvédelem** > **Jelszó** elemre.
4. Adja meg régi jelszavát, állítsa be és erősítse meg az új jelszót, majd kattintson a **Küldés** gombra.

### <a name="change-your-password-from-the-azure-access-panel"></a>A jelszó módosítása az Azure hozzáférési panelén

Ezt a módszert akkor használja, ha az alkalmazásait általában az Azure Access Portalon keresztül éri el

1. Jelentkezzen be az [Azure Access Portalra](https://myapps.microsoft.com/) meglévő jelszavával
2. Kattintson a profiljára felül a jobb oldalon, majd kattintson a **Profil** elemre.
3. Kattintson a **Jelszó módosítása** elemre.
4. Adja meg régi jelszavát, állítsa be és erősítse meg az új jelszót, majd kattintson a **Küldés** gombra.

## <a name="next-steps"></a>Következő lépések

* [Regisztrálás az önkiszolgáló jelszó-visszaállítás használatára](active-directory-passwords-reset-register.md)
* [Jelszó-visszaállítási regisztrációs oldal](http://aka.ms/ssprsetup)
* [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)
* [Nem tud bejelentkezni a Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Bejelentkezési oldal – Nem érhető el a fiókja?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "A hitelesítési adatok megerősítése"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Jelszó módosítása"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A jelszó vissza lett állítva"

