---
title: Biztonsági adatok (előzetes verzió) beállítása az e-mail-cím – Azure Active Directory használatára |} A Microsoft Docs
description: Hogyan állítható be a biztonsági adatok a személyazonosságát, e-mail-címét.
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
ms.openlocfilehash: 720aafac79a67f64b0974dba0dd60c6aa24a8c54
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342071"
---
# <a name="set-up-security-info-preview-to-use-your-email-address"></a>Állítsa be a biztonsági adatok (előzetes verzió) az e-mail cím használata
Követheti a következő lépésekkel adhatja hozzá a jelszó-visszaállítási mód. Miután beállította a először, visszatérhet a **biztonsági adatok** lap hozzáadása, frissítése vagy törlése a biztonsági információkat.

Miután beállította a jelszó-visszaállítási mód, is be kell állítania a kétfaktoros hitelesítési módszer, használatával egy [hitelesítő alkalmazás](security-info-setup-auth-app.md), [szöveges üzenetküldés](security-info-setup-text-msg.md), vagy egy [telefonhívás](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Állítsa be az e-mail-címét a biztonsági adatok lapján
Szervezeti beállítások, attól függően fogja tudni használni az e-mail-címét a biztonsági adatok módszerekkel egyik.

>[!Note]
>Azt javasoljuk, hogy az e-mail-cím, amely nem igényel a hálózati jelszót eléréséhez. Ha nem látja az e-mailt a beállítást, lehetséges, hogy a szervezet nem engedélyezi, hogy az ellenőrző e-mailt. Ha ez a helyzet, szüksége lesz egy másik módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

### <a name="to-set-up-your-email-address"></a>Az e-mail cím beállítása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjával, és keresse meg a https://myprofile.microsoft.com/ lapot.

    ![Saját profil lapot, amely a kijelölt biztonsági adatai hivatkozások](media/security-info/securityinfo-myprofile-page.png)

2. Válassza ki **biztonsági adatok** a bal oldali navigációs ablaktáblán vagy a hivatkozás a **biztonsági adatok** letiltása, és válassza ki **metódus hozzáadása** a a **biztonsági adatai**  lapot.

    ![Biztonsági adatok weblapját a kiemelt Hozzáadás metódus beállítás](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Az a **adjon meg egy metódust** lapon jelölje be **E-mail** a legördülő listából válassza ki, és válassza ki a **Hozzáadás**.

    ![Adja hozzá a metódus mezőben kiválasztott e-mail-címmel](media/security-info/securityinfo-myprofile-addemail.png)

4. Az a **E-mail** írja be az e-mail-címét (például alain@gmail.com), majd válassza ki **tovább**.

    ![Telefonszám hozzáadása, és válassza ki a telefonhívások](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Ez az e-mail cím nem lehet a munkahelyi vagy iskolai e-mail címét.

5. Írja be a kódot, a megadott e-mail címre, és válassza **tovább**.

    ![Adja hozzá a telefonszámot, és válassza ki a szöveges üzenetek](media/security-info/securityinfo-myprofile-emailcode.png)

    A biztonsági adatok frissítése, és az e-mail-cím segítségével igazolhatja a személyazonosságát, jelszó-visszaállítás használata esetén.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Törölje a biztonsági adatok módszerek az e-mail-címét
Ha már nem szeretné az e-mail-címét használja a biztonsági adatok módszerként, eltávolíthatja azt a **biztonsági adatok** lapot.

>[!Important]
>Ha véletlenül törli az e-mail-címét, nincs semmilyen módon nem lehet visszavonni. Kell adja hozzá újra, a módszer lépései a [állítsa be az e-mail-címét](#set-up-your-email-address-from-the-security-info-page) című szakaszát.

### <a name="to-delete-your-email-address"></a>Az e-mail-cím törlése

1. Az a **biztonsági adatok** lapon válassza ki a **törlése** mellett kapcsolni a **E-mail** lehetőséget.

    ![A telefonos metódus törölni a biztonsági adatok összekapcsolása](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Válassza ki **Igen** a megerősítés mezőben törlése a **E-mail** fiókot. Miután az e-mail-fiókot törölték, a rendszer eltávolítja a biztonsági adatait és, eltűnik a **biztonsági adatok** lap.

## <a name="additional-security-info-methods"></a>További biztonsági adatok módszerek
Hogyan a szervezet névjegyeket igazolhatja személyazonosságát, akkor mi alapján szeretne ehhez további lehetősége van. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le, és a egy hitelesítő alkalmazást használja, vagy egy jóváhagyási értesítést, vagy egy véletlenszerűen létrehozott jóváhagyási kód lekérése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Állítsa be és a Microsoft Authenticator alkalmazással kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata egy hitelesítő alkalmazást](security-info-setup-auth-app.md).

- **Mobileszköz szöveg.** Adja meg a mobileszközök számát, és értesítés SMS-ben egy kódot fogja használni a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Ellenőrizze identitását az szöveges üzenet (SMS) kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata a szöveges üzenetben (SMS)](security-info-setup-text-msg.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszközök számát, és telefonhívás beszerzése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Igazolja személyazonosságát, egy telefonszámmal kapcsolatos lépésenkénti útmutatót lásd: [állítsa be a biztonsági adatok telefonhívásokat használandó](security-info-setup-phone-number.md).

- **Biztonsági kérdések.** Néhány a szervezet számára a rendszergazda által létrehozott biztonsági kérdések megválaszolása. Ez a beállítás csak akkor használható, a jelszó-visszaállításhoz, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállítása kapcsolatos lépésenkénti útmutatót lásd: a [beállítása a biztonsági adatok a biztonsági kérdések használata](security-info-setup-questions.md) cikk.
    
    >[!Note]
    >Ha ezek a beállítások némelyike hiányzik, azt azért nagy valószínűséggel a szervezet nem engedélyezi azokat a módszereket. Ha ez a helyzet, szüksége lesz egy rendelkezésre álló módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- A jelszó alaphelyzetbe állítása, ha elfelejti, az elveszett vagy a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/) vagy kövesse a a [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md) cikk.

- Hibaelhárítási tippek és a bejelentkezési problémákkal kapcsolatos súgó a [nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.
