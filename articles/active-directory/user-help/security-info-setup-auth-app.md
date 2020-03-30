---
title: A Microsoft Authenticator alkalmazás beállítása ellenőrzési módszerként – Azure AD
description: A Biztonsági adatok (előzetes verzió) lap beállítása személyazonosságának igazolására a Microsoft Authenticator alkalmazással, mint ellenőrzési módszerrel.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 16cb512d3f8f8afcc199cb52c13e09b12107576f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062421"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>A Microsoft Authenticator alkalmazás beállítása ellenőrzési módszerként

Az alábbi lépésekkel hozzáadhatja a kétfaktoros ellenőrzési és jelszó-visszaállítási módszereket. Miután első alkalommal beállította ezt, visszatérhet a **Biztonsági adatok** lapra a biztonsági adatok hozzáadásához, frissítéséhez vagy törléséhez.

Ha a rendszer a munkahelyi vagy iskolai fiókba való bejelentkezés után azonnal kéri, hogy állítsa be ezt, tekintse meg a [biztonsági adatok beállítása a bejelentkezési lap kérdésében](security-info-setup-signin.md) című témakör részletes lépéseit.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Ha nem látja a hitelesítő alkalmazás lehetőséget, lehetséges, hogy a szervezet nem engedélyezi, hogy ezt a beállítást ellenőrzésre használja. Ebben az esetben más módszert kell választania, vagy további segítségért forduljon a szervezet ügyfélszolgálatához.

## <a name="security-vs-password-reset-verification"></a>Biztonság vs jelszó-visszaállítás ellenőrzése

A biztonsági adatok módszerei a kétfaktoros biztonsági ellenőrzéshez és a jelszó alaphelyzetbe állításához is használatosak. Azonban nem minden módszer használható mindkettőre.

| Módszer | Alkalmazási cél |
| ------ | -------- |
| Hitelesítő alkalmazás | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| SMS-ek | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Telefonhívások | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Biztonsági kulcs | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| E-mail fiók | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |
| Biztonsági kérdések | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>A Microsoft Authenticator alkalmazás beállítása a Biztonsági adatok lapon

A szervezet beállításaitól függően előfordulhat, hogy egy hitelesítési alkalmazást használhat a biztonsági adatok egyik módszereként. Nem kell használnia a Microsoft Authenticator alkalmazást, és a beállítási folyamat során másik alkalmazást is választhat. Ez a cikk azonban a Microsoft Authenticator alkalmazást használja.

### <a name="to-set-up-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás beállítása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjába, https://myprofile.microsoft.com/ és lépjen az oldalra.

    ![Saját profil lap, kiemelt biztonsági adatok hivatkozásai](media/security-info/securityinfo-myprofile-page.png)

2. Válassza a **Biztonsági adatok lehetőséget** a bal oldali navigációs ablakban vagy a Biztonsági **adatok** blokkban található hivatkozáson, majd válassza a Metódus **hozzáadása** lehetőséget a **Biztonsági adatok** lapon.

    ![Biztonsági adatok lap kiemelt Hozzáadási módszer beállítással](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **Metódus hozzáadása** lapon válassza a **Hitelesítő alkalmazás** elemet a legördülő listából, majd a **Hozzáadás gombot.**

    ![Metódus hozzáadása mező, kijelölt Hitelesítő alkalmazással](media/security-info/securityinfo-myprofile-addauthapp.png)

4. A **Kezdőképernyőn válassza az alkalmazás letöltése** lehetőséget a **Letöltés gombra** a Microsoft Authenticator alkalmazás letöltéséhez és telepítéséhez mobileszközére, majd kattintson a **Tovább**gombra.

    Az alkalmazás letöltéséről és telepítéséről a [Microsoft Authenticator alkalmazás letöltése és telepítése című](user-help-auth-app-download-install.md)témakörben talál további információt.

    ![Első a beszerzésaz alkalmazás oldal](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Ha a Microsoft Authenticator alkalmazástól eltérő hitelesítő alkalmazást szeretne használni, válassza a **Másik hitelesítő alkalmazás** hivatkozását.
   >
   > Ha a szervezet lehetővé teszi, hogy válasszon egy másik módszer mellett a hitelesítő app, akkor válassza ki a **Szeretnék létrehozni egy másik módszer linket**.

5. Maradjon a **Fiók beállítása** lapon, amíg beállítja a Microsoft Authenticator alkalmazást a mobileszközén.

    ![A hitelesítő alkalmazásoldal beállítása](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Nyissa meg a Microsoft Authenticator alkalmazást, jelölje be az értesítések engedélyezéséhez (ha a rendszer kéri), válassza a **Fiók hozzáadása** lehetőséget a jobb felső **sarokban** lévő Testreszabás és vezérlés ikonon, majd válassza a **Munkahelyi vagy iskolai fiók lehetőséget**.

    >[!Note]
    >Ha ez az első alkalom, hogy beállítja a Microsoft Authenticator alkalmazást, előfordulhat, hogy egy kérdés jelenik meg arról, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készíthessen és videót (Android) rögzíthessen. Az **Engedélyezés** lehetőséget kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet ad a QR-kódról. Ha nem engedélyezi a kamerát, továbbra is beállíthatja a hitelesítő alkalmazást, de manuálisan kell megadnia a kódadatokat. A kód manuális hozzáadásáról a [Fiók hozzáadása az alkalmazáshoz című témakörben](user-help-auth-app-add-account-manual.md)talál további információt.

7. Térjen vissza a **Fiók beállítása** lapra a számítógépen, és válassza a **Tovább**gombot.

    Megjelenik **a QR-kódlap beszkaf.**

    ![A QR-kód beszkéselése a Hitelesítő alkalmazással](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. A megadott kód beolvasása a Microsoft Authenticator alkalmazás QR-kódolvasójával, amely a 6.

    A hitelesítő alkalmazásnak sikeresen hozzá kell adnia a munkahelyi vagy iskolai fiókját anélkül, hogy további adatokat kellene megadnia Öntől. Ha azonban a QR-kódolvasó nem tudja olvasni a kódot, kiválaszthatja a **Nem olvasható QR-kód hivatkozást,** és manuálisan beírhatja a kódot és az URL-t a Microsoft Authenticator alkalmazásba. A kód manuális hozzáadásáról a [Fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md)című témakörben talál további információt.

9. Válassza a **Tovább** gombot a számítógép **QR-kódlapján.**

    A rendszer értesítést küld a Microsoft Authenticator alkalmazásnak a mobileszközén, hogy tesztelje a fiókját.

    ![A fiók tesztelése a hitelesítő alkalmazással](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Hagyja jóvá az értesítést a Microsoft Authenticator alkalmazásban, majd válassza a **Tovább gombot.**

     ![Sikeres értesítés az alkalmazás és a fiók összekapcsolása](media/security-info/securityinfo-myprofile-successauthapp.png)

     A biztonsági adatok úgy frissülnek, hogy a Microsoft Authenticator alkalmazás alapértelmezés szerint a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása során ellenőrizze személyazonosságát.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>A hitelesítő alkalmazás törlése a biztonsági adatok kalkapcsolatos módszereiből

Ha a továbbiakban nem szeretné a hitelesítő alkalmazást biztonsági adatként használni, eltávolíthatja azt a **Biztonsági adatok** lapról. Ez minden hitelesítő alkalmazásesetében működik, nem csak a Microsoft Authenticator alkalmazásban. Az alkalmazás törlése után meg kell lépned a mobileszközödhitelesítő alkalmazásba, és törölni kell a fiókot.

>[!Important]
>Ha véletlenül törli a hitelesítő alkalmazást, nem lehet visszavonni. A cikk [hitelesítő alkalmazás ának beállítása](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) című szakaszában leírt lépéseket követve újra hozzá kell adnia a hitelesítő alkalmazást.

### <a name="to-delete-the-authenticator-app"></a>A hitelesítő alkalmazás törlése

1. A **Biztonsági adatok** lapon válassza a **Delete** hivatkozást a Hitelesítő alkalmazás mellett.

    ![Hivatkozás a hitelesítő alkalmazás biztonsági adatokból való törléséhez](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. A hitelesítő alkalmazás törléséhez válassza az **Igen** lehetőséget a megerősítő mezőben. A hitelesítő alkalmazás törlése után törlődik a biztonsági adatokból, és eltűnik a **Biztonsági adatok** lapról. Ha a hitelesítő alkalmazás az alapértelmezett módszer, az alapértelmezett mód egy másik elérhető módszerre változik.

3. Nyissa meg a hitelesítő alkalmazást mobileszközén, válassza **a Fiókok szerkesztése**lehetőséget, majd törölje a munkahelyi vagy iskolai fiókját a hitelesítő alkalmazásból.

    Fiókját teljesen eltávolítjuk a hitelesítő alkalmazásból a kétfaktoros ellenőrzési és jelszó-visszaállítási kérelmek hez.

## <a name="change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

Ha azt szeretné, hogy a hitelesítő alkalmazás legyen az alapértelmezett módszer, amelyet akkor használ, amikor kétfaktoros ellenőrzéssel vagy jelszó-visszaállítási kérelmekkel jelentkezik be a munkahelyi vagy iskolai fiókjába, beállíthatja azt a Biztonsági **adatok** lapon.

### <a name="to-change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

1. A **Biztonsági adatok** lapon válassza a **Módosítás** hivatkozást az **Alapértelmezett bejelentkezési módszer** adatai mellett.

    ![Az alapértelmezett bejelentkezési módszer hivatkozásának módosítása](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Válassza a **Microsoft Authenticator – értesítés lehetőséget** az elérhető módszerek legördülő listájából. Ha nem a Microsoft Authenticator alkalmazást használja, válassza a Hitelesítő alkalmazás vagy a **hardvertoken** lehetőséget.

    ![Az alapértelmezett bejelentkezés módjának kiválasztása](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Válassza a **Megerősítés**lehetőséget.

    A Microsoft Authenticator alkalmazás bejelentkezési módosításaihoz használt alapértelmezett módszer.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

További lehetőségek et is választhat arra vonatkozóan, hogy a szervezet hogyan veszi fel Önhez a személyazonosságának igazolását, attól függően, hogy mit próbál tenni. A lehetőségek a következők:

- **Mobileszköz szövege.** Adja meg mobileszköz-számát, és kapjon egy kódot, amelyet a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához használ. A személyazonosság szöveges üzenettel (SMS) történő ellenőrzéséről a [Biztonsági adatok beállítása szöveges üzenetküldés (SMS) használatához](security-info-setup-text-msg.md)című témakörben talál.

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg mobileszköz-számát, és kapjon telefonhívást a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A személyazonosság telefonszámmal történő igazolásával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása telefonhívások használatához](security-info-setup-phone-number.md).

- **Biztonsági kulcs.** Regisztrálja a Microsofttal kompatibilis biztonsági kulcsot, és használja azt pin-kóddal együtt a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A személyazonosság biztonsági kulccsal történő ellenőrzéséről a [Biztonsági adatok beállítása biztonsági kulcs használatához](security-info-setup-security-key.md)lépésenként.

- **E-mail cím.** Adja meg munkahelyi vagy iskolai e-mail címét, hogy e-mailt kapjon a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. A levelezés beállításával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a levelezés használatához.](security-info-setup-email.md)

- **Biztonsági kérdések.** Válaszoljon a rendszergazda által a szervezet számára létrehozott néhány biztonsági kérdésre. Ez a beállítás csak jelszó-visszaállításesetén érhető el, a kétlépéses ellenőrzésnél nem. A biztonsági kérdések beállításával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a biztonsági kérdések használatához](security-info-setup-questions.md) című cikkben találja.

    >[!Note]
    >Ha ezek közül a lehetőségek közül néhány hiányzik, akkor valószínűleg azért, mert a szervezet nem engedélyezi ezeket a módszereket. Ebben az esetben ki kell választania egy elérhető módszert, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- Jelentkezzen be a Microsoft Authenticator alkalmazással, a Bejelentkezés a [kétlépéses ellenőrzés vagy](security-info-setup-signin.md) a biztonsági adatokról szóló cikk ben leírt lépéseket követve.

- Állítsa alaphelyzetbe a jelszavát, ha elvesztette vagy elfelejtette azt a [Jelszó-visszaállítás portálról,](https://passwordreset.microsoftonline.com/) vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippeket és segítséget kaphat a Bejelentkezési problémákról a [Nem lehet bejelentkezni a Microsoft-fiókkal](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) kapcsolatos cikkben.
