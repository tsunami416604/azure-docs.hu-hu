---
title: Biztonsági adatainak beállítása telefonhívásokat – Azure Active Directory használatára |} A Microsoft Docs
description: Állítsa be a mobileszközükön személyazonosságát, vagy a munkahelyi telefonszám biztonsági adatait.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: f33982a584f831fbab190d94ce89a8d7c93d7913
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825221"
---
# <a name="set-up-security-info-to-use-phone-calls-preview"></a>Állítsa be a biztonsági adatok telefonhívásokat (előzetes verzió) használata

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

A biztonsági adatok beállításához megköveteli, hogy jelentkezzen be munkahelyi vagy iskolai fiókjával, és majd a regisztráció befejezéséhez. Soha ne beállította a biztonsági adatait, ha rendszer most tennie.

## <a name="set-up-phone-calls"></a>Telefonhívások beállítása

Attól függően, a szervezet beállításait a rendszer telefonszám megadása hozzá biztonsági adataihoz, amikor bejelentkezik. Ellenkező esetben telefonhívásokat biztonsági adatainak beállítása a kezdéshez kövesse [a biztonsági adatok kezelése](security-info-manage-settings.md).

>[!Note]
>Biztonsági adatok használatával telefonos mellékek nem támogatja. Akkor is, ha a megfelelő formátumban ad hozzá, + 1 4255551234 X 12345, a bővítmények törlődnek, mielőtt a hívást kezdeményeznek.<br>Ha nem látja a telefonos elrendezésre, akkor lehet, hogy a szervezet nem engedélyezi, hogy az ellenőrző telefonhívásokat. Ha ez a helyzet, szüksége lesz egy másik módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

### <a name="to-use-your-phone-number"></a>A telefonszám használata

1. Válassza ki a **Phone** lehetőséget.

    A **a telefon** varázsló jelenik meg.

    ![Állítsa be az országot vagy régiót és a telefonszámot száma](media/security-info/security-info-keep-secure-setup-phone.png)

2. Válassza ki a **ország vagy régió** a legördülő listából, írja be a telefonszámát (többek között a körzetszámot, ha van ilyen) a **telefonszám** jelölje ki a **hívást kérek** lehetőséget, majd **tovább**.

    Győződjön meg arról, hogy a megfelelő telefonszámot adta meg a telefonhívás kap. Ugyanakkor a rendszer elküldi a kettőskereszt (#) kulcs megerősítése és a telepítés befejezéséhez.

    ![Ellenőrizze a telefonos képernyő, hogy a hívás sikeres megválaszolva](media/security-info/security-info-keep-secure-verify-phone-call.png)

    Telefonszám használata a személyazonosságát, ha a kétlépéses ellenőrzés vagy az önkiszolgáló jelszó-visszaállítás használata esetén a biztonsági adatok frissítése.

    >[!Note]
    >Ha telefonon hívja fel a mobil eszközére helyett szöveges üzenetet fogadni szeretné, kövesse a [beállítása a biztonsági adatok használata a szöveges üzenetben (SMS)](security-info-setup-text-msg.md) cikk.

## <a name="additional-security-info-options"></a>További biztonsági információ beállításai

Hogyan a szervezet névjegyeket igazolhatja személyazonosságát, akkor mi alapján szeretne ehhez további lehetősége van. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le, és a egy hitelesítő alkalmazást használja, vagy egy jóváhagyási értesítést, vagy egy véletlenszerűen létrehozott jóváhagyási kód lekérése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Állítsa be és a Microsoft Authenticator alkalmazással kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata egy hitelesítő alkalmazást](security-info-setup-auth-app.md).

- **Mobileszköz szöveg.** Adja meg a mobileszközök számát, és értesítés SMS-ben egy kódot fogja használni a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Ellenőrizze identitását az szöveges üzenet (SMS) kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata a szöveges üzenetben (SMS)](security-info-setup-text-msg.md).

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címet egy e-mailt beolvasni a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. Állítsa be az e-mailben kapcsolatos lépésenkénti útmutatót lásd: [biztonsági adatok beállítása az e-mailben](security-info-setup-email.md).

- **Biztonsági kérdések.** Néhány a szervezet számára a rendszergazda által létrehozott biztonsági kérdések megválaszolása. Ez a beállítás csak akkor használható, a jelszó-visszaállításhoz, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállítása kapcsolatos lépésenkénti útmutatót lásd: a [beállítása a biztonsági adatok a biztonsági kérdések használata](security-info-setup-questions.md) cikk.
    
    >[!Note]
    >Ha ezek a beállítások némelyike hiányzik, azt azért nagy valószínűséggel a szervezet nem engedélyezi azokat a módszereket. Ha ez a helyzet, szüksége lesz egy rendelkezésre álló módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- Ha kell biztonsági adatait, hajtsa végre a következő témakör utasításait a [a biztonsági adatok kezelése](security-info-manage-settings.md) cikk.

- A jelszó alaphelyzetbe állítása, ha elfelejti, az elveszett vagy a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/) vagy kövesse a a [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md) cikk.

- Hibaelhárítási tippek és a bejelentkezési problémákkal kapcsolatos súgó a [nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.