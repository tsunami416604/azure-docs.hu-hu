---
title: Biztonsági adatok (előzetes verzió) beállítása egy hitelesítő alkalmazást – az Azure Active Directory használatára |} A Microsoft Docs
description: Hogyan állítható be a biztonsági adatok a Microsoft Authenticator alkalmazással a személyazonosságát.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4757be00a3633f56aed52dd7af22923e49b0b62
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102383"
---
# <a name="set-up-security-info-preview-to-use-an-authenticator-app"></a>Állítsa be a biztonsági adatok (előzetes verzió) használata egy hitelesítő alkalmazást
Ezekkel a lépésekkel adja hozzá a kétfaktoros ellenőrzést, és a jelszó alaphelyzetbe állítási metódus. Miután beállította a először, visszatérhet a **biztonsági adatok** lap hozzáadása, frissítése vagy törlése a biztonsági információkat.

Ha kéri, jelentkezzen be munkahelyi vagy iskolai fiókjával után azonnal beállítaniuk ezt, tekintse meg a részletes lépéseit a [állítsa be a biztonsági adatok a bejelentkezési oldal használatával](security-info-setup-signin.md) cikk.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Az authenticator alkalmazás beállítás nem jelenik meg, ha lehetőség, hogy a szervezet nem engedélyezi, hogy az alkalmazás hitelesítési ellenőrzése. Ebben az esetben kell válasszon egy másik módszert, vagy további segítségért forduljon a rendszergazdához.

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>A Microsoft Authenticator alkalmazás beállítása a biztonsági adatok lapján
Szervezeti beállítások, függően hitelesítési alkalmazás használhatja a security info módszerekkel egyik előfordulhat, hogy lehet. Nem kell használni a Microsoft Authenticator alkalmazást, és a egy másik alkalmazást választhat a folyamat során. Ez a cikk azonban a Microsoft Authenticator alkalmazást használja. 

### <a name="to-set-up-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás beállítása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjával, és keresse meg a https://myprofile.microsoft.com/ lapot.

    ![Saját profil lapot, amely a kijelölt biztonsági adatai hivatkozások](media/security-info/securityinfo-myprofile-page.png)

2. Válassza ki **biztonsági adatok** a bal oldali navigációs ablaktáblán vagy a hivatkozás a **biztonsági adatok** letiltása, és válassza ki **metódus hozzáadása** a a **biztonsági adatai**  lapot.

    ![Biztonsági adatok weblapját a kiemelt Hozzáadás metódus beállítás](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Az a **adjon meg egy metódust** lapon jelölje be **hitelesítő alkalmazás** a legördülő listából válassza ki, és válassza ki a **Hozzáadás**.

    ![Adja hozzá a metódus be, az Authenticator alkalmazás kiválasztva](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Az a **úgy az alkalmazást indítja el** lapon jelölje be **letöltése** töltse le és telepítse a Microsoft Authenticator alkalmazást a mobileszközén, és válassza a **tovább**.

    Töltse le és telepítse az alkalmazást kapcsolatos további információkért lásd: [töltse le és telepítse a Microsoft Authenticator alkalmazás](user-help-auth-app-download-install.md).

    ![Az oldal első lépésként](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Ha szeretne egy hitelesítő alkalmazást a Microsoft Authenticator alkalmazástól használni, válassza ki a **egy másik hitelesítő alkalmazást használni kívánt** hivatkozásra.
   > 
   > Ha vállalata engedélyezi, hogy az authenticator alkalmazás mellett eltérő módszer kiválasztása, kiválaszthatja a **más módszert kapcsolat beállításához szeretnék**.

5. Továbbra is a **a fiók beállítása** oldalon, állítsa be a Microsoft Authenticator alkalmazást a mobileszközén.

    ![Állítsa be az authenticator alkalmazás oldalra](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Nyissa meg a Microsoft Authenticator alkalmazást, engedélyezi az értesítések (Ha a rendszer kéri), válassza ki **fiók hozzáadása** a a **testreszabása és vezérlési** ikonjára a jobb felső sarokban, majd válassza ki **munkahelyi vagy iskolai fiókkal**.

7. Lépjen vissza a **a fiók beállítása** lapot, majd válassza ki **tovább**.

    A **a QR-kód** lap jelenik meg.

    ![A QR-kód az Authenticator alkalmazás használatával](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

6. Olvassa be a megadott kódot a Microsoft Authenticator alkalmazás QR kód Reader, ami a mobileszközén jelent meg, miután a 6. lépésben létrehozott munkahelyi vagy iskolai fiókjával.

    Az authenticator alkalmazás kell sikeresen adja hozzá a munkahelyi vagy iskolai fiók, a további adatok nélkül. Azonban, ha a QR-kód olvasó nem tudja olvasni a kódot, válassza a **nem lehet beolvasni a QR-kód hivatkozás** és manuálisan adja meg a kódot és URL-CÍMÉT a Microsoft Authenticator alkalmazásba. Manuálisan adja hozzá a kódot kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

7. Válassza ki **tovább** a a **a QR-kód** lapot a.

    A rendszer értesítést küld a Microsoft Authenticator alkalmazást a mobileszközén, a fiók teszteléséhez.

    ![A fiók a hitelesítő alkalmazás tesztelése](media/security-info/securityinfo-myprofile-tryitauthapp.png)

8. Hagyja jóvá az értesítést a Microsoft Authenticator alkalmazásban, és válassza ki **tovább**.

     ![Sikeres címtármódosítást jelző értesítés, az alkalmazás és a fiók csatlakoztatása](media/security-info/securityinfo-myprofile-successauthapp.png)

     A biztonsági adatok frissül, és a Microsoft Authenticator alkalmazással igazolhatja a személyazonosságát a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása használatakor alapértelmezés szerint.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Törölje a hitelesítő alkalmazás a biztonsági adatok módszerek
Ha már nem szeretné a hitelesítő alkalmazás a biztonsági adatok módszerként használhatják, eltávolíthatja azt a **biztonsági adatok** lapot. Ez a módszer az összes authenticator alkalmazásba, nem csak a Microsoft Authenticator alkalmazást. Az alkalmazás törlését követően kell belépni az authenticator alkalmazás a mobileszközén, és törölje a fiókot.

>[!Important]
>Ha véletlenül törli az authenticator alkalmazás, nincs semmilyen módon nem lehet visszavonni. Kell az authenticator alkalmazás, vegye fel újra, a lépések a [a hitelesítő alkalmazás beállítása](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) című szakaszát.

### <a name="to-delete-the-authenticator-app"></a>Az authenticator alkalmazás törlése

1. Az a **biztonsági adatok** lapon válassza ki a **törlése** hivatkozást az Authenticator alkalmazás mellett.

    ![Az authenticator alkalmazás törlése a biztonsági adatok összekapcsolása](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Válassza ki **Igen** a megerősítés mezőben az authenticator alkalmazás törléséhez. Miután a hitelesítő alkalmazás törölve van, a rendszer eltávolítja a biztonsági adatok és, eltűnik a **biztonsági adatok** lap. Ha a hitelesítő alkalmazás az alapértelmezett módszer, egy másik használható módszer alapértelmezés szerint változik.

3. A mobileszközén, jelölje be az authenticator alkalmazás **fiókok szerkesztése**, és törölje a munkahelyi vagy iskolai fiókjával az authenticator alkalmazásból.

    A fiók a rendszer eltávolítja a kétfaktoros ellenőrzési hitelesítő alkalmazást, és a jelszó-átállítási kérelmet.

## <a name="change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatai módszer megváltoztatása
Ha azt szeretné, hogy az authenticator alkalmazás legyen az alapértelmezett módszer használatos, amikor bejelentkezik a munkahelyi vagy iskolai fiókjába kétfaktoros ellenőrzési, vagy a jelszó-átállítási kérelmek, között állítható be, a biztonsági **info** lapot.

### <a name="to-change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatai módszerének módosítása

1. Az a **biztonsági adatok** lapon válassza ki a **módosítása** mellett kapcsolni a **alapértelmezett bejelentkezési módszer** információkat.

    ![Módosítás hivatkozásra az alapértelmezett bejelentkezési módszer](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Válasszon **Microsoft Authenticator - értesítési** elérhető módszerek a legördülő listából. Ha nem használ a Microsoft Authenticator alkalmazást, válassza ki a **Authenticator alkalmazás és hardver token** lehetőséget.

    ![Az alapértelmezett bejelentkezési módszer kiválasztása](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Válassza ki **megerősítése**.

    Az alapértelmezett módszer, jelentkezzen be a Microsoft Authenticator alkalmazás módosítások használatos.

## <a name="additional-security-info-methods"></a>További biztonsági adatok módszerek
Hogyan a szervezet névjegyeket igazolhatja személyazonosságát, akkor mi alapján szeretne ehhez további lehetősége van. A lehetőségek a következők:

- **Mobileszköz szöveg.** Adja meg a mobileszközök számát, és értesítés SMS-ben egy kódot fogja használni a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Ellenőrizze identitását az szöveges üzenet (SMS) kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata a szöveges üzenetben (SMS)](security-info-setup-text-msg.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszközök számát, és telefonhívás beszerzése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Igazolja személyazonosságát, egy telefonszámmal kapcsolatos lépésenkénti útmutatót lásd: [állítsa be a biztonsági adatok telefonhívásokat használandó](security-info-setup-phone-number.md).

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címet egy e-mailt beolvasni a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. Állítsa be az e-mailben kapcsolatos lépésenkénti útmutatót lásd: [biztonsági adatok beállítása az e-mailben](security-info-setup-email.md).

- **Biztonsági kérdések.** Néhány a szervezet számára a rendszergazda által létrehozott biztonsági kérdések megválaszolása. Ez a beállítás csak akkor használható, a jelszó-visszaállításhoz, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállítása kapcsolatos lépésenkénti útmutatót lásd: a [beállítása a biztonsági adatok a biztonsági kérdések használata](security-info-setup-questions.md) cikk.
    
    >[!Note]
    >Ha ezek a beállítások némelyike hiányzik, azt azért nagy valószínűséggel a szervezet nem engedélyezi azokat a módszereket. Ha ez a helyzet, szüksége lesz egy rendelkezésre álló módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- A jelszó alaphelyzetbe állítása, ha elfelejti, az elveszett vagy a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/) vagy kövesse a a [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md) cikk.

- Hibaelhárítási tippek és a bejelentkezési problémákkal kapcsolatos súgó a [nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.