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
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: bb62d6973dff00932e72ddb4b2c344f070537828
ms.lasthandoff: 04/14/2017


---
# <a name="help-i-forgot-my-password"></a>Segítség, elfelejtettem a jelszavamat

1. lépés: ... Ne essen kétségbe

Ha az alábbi forgatókönyvek igazak Önre, segíthetünk.

* Nem biztos benne, hogyan férhetne hozzá a fiókjához, és nem emlékszik a jelszavára.
* Nem lett jelszó kiosztva, és a rendszergazda ide irányította.

## <a name="unlock-my-account"></a>Saját fiók feloldása

Ha azért van itt, hogy feloldja fiókját, kövesse az alábbi lépéseket. Amikor a 6. lépésben az **Új jelszó megadása** üzenetet látja, feloldhatja és módosíthatja a jelszavát, és feloldhatja a fiókot.

## <a name="reset-my-password"></a>Saját jelszó visszaállítása

A fiókjába a következő lépéseket követve léphet vissza.
1. Bármelyik munkahelyi vagy iskolai bejelentkezési oldalon kattintson a **Nem érhető el a fiókja? hivatkozásra**, majd a **Munkahelyi vagy iskolai fiók** lehetőségre, vagy lépjen közvetlenül a [jelszó-visszaállítási oldalra](https://passwordreset.microsoftonline.com/).

    ![Nem érhető el a fiókja?][Login]

2. Adja meg munkahelyi vagy iskolai **Felhasználói azonosítóját**, és a CAPTCHA-tesztben a megjelenő szöveg beírásával igazolja, hogy Ön nem robot, majd kattintson a **Tovább** gombra.

   > [!NOTE]
   > Ha a rendszergazda nem engedélyezte ezt a funkciót, egy hivatkozás ekkor a rendszergazdához irányítja, aki e-mailben vagy saját webes portálon nyújt segítséget.
   >

3. A rendszergazda által konfigurált beállításoktól függően a következők valamelyike jelenik meg.
    * **Másodlagos e-mail-cím** – A rendszer egy 6 jegyű kódot küld e-mail-üzenetben a másodlagos vagy a hitelesítési e-mail-címére (amelyiket választja).
    * **Szöveges üzenet küldése a mobiltelefonomra** – A rendszer egy 6 jegyű kódot küld szöveges üzenetben a mobiltelefonszámára vagy a hitelesítési telefonszámára (amelyiket választja).
    * **A mobiltelefonom felhívása** – A rendszer felhívja a mobiltelefonszámát vagy a hitelesítési telefonszámát (amelyiket választja). Nyomja meg a # billentyűt a hívás hitelesítéséhez.
    * **Az irodai telefonom felhívása** – A rendszer felhívja az irodai telefonszámát. Nyomja meg a # billentyűt a hívás hitelesítéséhez.
    * **Biztonsági kérdések megválaszolása** – A rendszer megjeleníti az előre regisztrált biztonsági kérdéseket, amelyeket megválaszolhat.
4. Írja be a kötelező mezőkbe a feltett kérdésekre adandó válaszokat, és kattintson a **Tovább** gombra.

    ![A hitelesítési adatok megerősítése][Verification]

5. A rendszergazda előírhat egy további jóváhagyási lépést, és ilyenkor még egyszer végre kell hajtania a 4. lépést egy másik lehetőséget választva.
6. Az **Új jelszó megadása** oldalon adjon meg egy új jelszót, amely megfelel a szervezet követelményeinek, erősítse meg a jelszót, majd kattintson a **Befejezés** gombra.

    ![Jelszó módosítása][Change]

7. Miután megjelent **A jelszó vissza lett állítva** üzenet, bejelentkezhet új jelszavával.

    ![A jelszó vissza lett állítva][Complete]

Miután ezzel a módszerrel feloldotta vagy visszaállította a jelszót, a rendszer esetleg egy e-mailben erősíti meg a folyamat végrehajtását, amelynek feladója a „Microsoft az [érintett szervezet] nevében” vagy hasonló. Ha egy ilyen e-mailt kap, de nem végezte el az önkiszolgáló jelszó-visszaállítás eljárást a fiókja feloldásához, forduljon a rendszergazdához.

## <a name="change-my-password"></a>Saját jelszó módosítása

Ha már ismeri a jelszavát, és módosítani szeretné, kövesse az alábbi lépéseket.

### <a name="change-your-password-from-the-office-365-portal"></a>A jelszó módosítása az Office 365 portálról

1. Kattintson a profiljára felül a jobb oldalon, majd a **Fiók megtekintése** elemre.
2. **Biztonság és adatvédelem**
3. **Jelszó**
4. Adja meg régi jelszavát, majd állítsa be és erősítse meg az új jelszót.
5. **Küldés**

### <a name="change-your-password-from-the-azure-access-panel"></a>A jelszó módosítása az Azure hozzáférési panelén

1. Jelentkezzen be az [Azure Access Portalra](https://myapps.microsoft.com/) meglévő jelszavával
2. Kattintson a profiljára felül a jobb oldalon, majd a **Profil** elemre.
3. **Jelszó módosítása**
4. Adja meg régi jelszavát, majd állítsa be és erősítse meg az új jelszót.
5. **Küldés**

## <a name="next-steps"></a>Következő lépések

* [Regisztrálás az önkiszolgáló jelszó-visszaállítás használatára](active-directory-passwords-reset-register.md)
* [Jelszó-visszaállítási regisztrációs oldal](http://aka.ms/ssprsetup)
* [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Bejelentkezési oldal – Nem érhető el a fiókja?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "A hitelesítési adatok megerősítése"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Jelszó módosítása"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A jelszó vissza lett állítva"

