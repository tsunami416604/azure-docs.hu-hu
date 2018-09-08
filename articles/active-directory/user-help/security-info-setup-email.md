---
title: Biztonsági adatok beállítása az e-mailben – Azure Active Directory |} A Microsoft Docs
description: Állítsa be a személyazonosságát a munkahelyi vagy iskolai e-mail-címet a biztonsági adatait.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 3f98b4e34c4c76efc33395530ef016b26e1ad523
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163039"
---
# <a name="set-up-security-info-to-use-email-preview"></a>Biztonsági adatok beállítása az e-mailben (előzetes verzió)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

A biztonsági adatok beállításához megköveteli, hogy jelentkezzen be munkahelyi vagy iskolai fiókjával, és majd a regisztráció befejezéséhez. Soha ne beállította a biztonsági adatait, ha rendszer most tennie.

## <a name="set-up-email"></a>E-mailek beállítása

Szervezeti beállítások, attól függően előfordulhat, hogy kéri egy e-mail-cím hozzáadása a biztonsági adatait, amikor bejelentkezik. Ellenkező esetben e-mailt a biztonsági adatok beállítása a kezdéshez kövesse [a biztonsági adatok kezelése](security-info-manage-settings.md).

>[!Note]
>Egy e-mail-fiókot, amely nem igényel a hálózati jelszó használatát javasoljuk.<br>Ha nem látja az e-mailt a beállítást, lehetséges, hogy a szervezet nem engedélyezi, hogy az ellenőrző e-mailt. Ha ez a helyzet, szüksége lesz egy másik módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

### <a name="to-use-your-email-address"></a>Az e-mail cím használata

1. Válassza ki a **E-mail** lehetőséget, majd a mezőbe írja be az e-mail-címét. Ez az e-mail cím nem lehet a munkahelyi vagy iskolai e-mail címét.

     ![Biztonsági adatok weblapját, az e-mailek beviteli mezője](media/security-info/security-info-keep-secure-setup-email.png)

2. Keressen egy e-mailt a Microsoft a szervezet számára, írja be a csomagban foglalt ellenőrző kódot a **e-mail cím ellenőrzése** mezőbe, majd válassza ki **kész**.

     ![Biztonsági adatok weblapját, az e-mail ellenőrző kód beviteli mezője](media/security-info/security-info-verify-email.png)

    >[!Note]
    >Ha nem látja a Microsoft e-mailt a szervezet nevében, győződjön meg arról, hogy helyesen írta be az e-mail-címét, és ezután ellenőrizze a levélszemét vagy a Levélszemét mappát.

3. Az a **a fiókja biztonságának megőrzéséhez** lapon jelölje be **kész**.

    A biztonsági adatok frissítése a személyazonosságát, ha a kétlépéses ellenőrzés használata esetén az e-mail-címét használja.

## <a name="additional-security-info-options"></a>További biztonsági információ beállításai

Hogyan a szervezet névjegyeket igazolhatja személyazonosságát, akkor mi alapján kívánt tegye lehetősége van. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le, és a egy hitelesítő alkalmazást használja, vagy egy jóváhagyási értesítést, vagy egy véletlenszerűen létrehozott jóváhagyási kód lekérése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Állítsa be és a Microsoft Authenticator alkalmazással kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata egy hitelesítő alkalmazást](security-info-setup-auth-app.md).

- **Mobileszköz szöveg.** Adja meg a mobileszközök számát, és értesítés SMS-ben egy kódot fogja használni a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Ellenőrizze identitását az szöveges üzenet (SMS) kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata a szöveges üzenetben (SMS)](security-info-setup-text-msg.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszközök számát, és telefonhívás beszerzése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Igazolja személyazonosságát, egy telefonszámmal kapcsolatos lépésenkénti útmutatót lásd: [állítsa be a biztonsági adatok telefonhívásokat használandó](security-info-setup-phone-number.md).

- **Biztonsági kérdések.** Néhány a szervezet számára a rendszergazda által létrehozott biztonsági kérdések megválaszolása. Ez a beállítás csak akkor használható, a jelszó-visszaállításhoz, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállítása kapcsolatos lépésenkénti útmutatót lásd: a [beállítása a biztonsági adatok a biztonsági kérdések használata](security-info-setup-questions.md) cikk.
    
    >[!Note]
    >Ha ezek a beállítások némelyike hiányzik, azt azért nagy valószínűséggel a szervezet nem engedélyezi azokat a módszereket. Ha ez a helyzet, szüksége lesz egy rendelkezésre álló módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- Ha kell biztonsági adatait, hajtsa végre a következő témakör utasításait a [a biztonsági adatok kezelése](security-info-manage-settings.md) cikk.

- A jelszó alaphelyzetbe állítása, ha elfelejti, az elveszett vagy a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/) vagy kövesse a a [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md) cikk.

- Hibaelhárítási tippek és a bejelentkezési problémákkal kapcsolatos súgó a [nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.