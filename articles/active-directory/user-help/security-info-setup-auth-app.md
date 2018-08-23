---
title: Biztonsági adatainak beállítása egy hitelesítő alkalmazást – az Azure Active Directory használatára |} A Microsoft Docs
description: Állítsa be a biztonsági adatok a Microsoft Authenticator alkalmazással a személyazonosságát.
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
ms.openlocfilehash: e7b07ba892f8f904b1b2127fa8e76649eb004388
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060835"
---
# <a name="set-up-security-info-to-use-an-authenticator-app-preview"></a>Állítsa be a biztonsági adatok egy hitelesítő alkalmazást (előzetes verzió) használata

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

A biztonsági adatok beállításához megköveteli, hogy jelentkezzen be munkahelyi vagy iskolai fiókjával, és majd a regisztráció befejezéséhez. Soha ne beállította a biztonsági adatait, ha rendszer most tennie.

## <a name="set-up-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás beállítása

Szervezeti beállítások, attól függően előfordulhat, hogy kéri, amikor bejelentkezik a Microsoft Authenticator alkalmazás beállítása. Ellenkező esetben a biztonsági adatok a Microsoft Authenticator alkalmazás beállítása a kezdéshez kövesse [a biztonsági adatok kezelése](security-info-manage-settings.md).

Töltse le és ismerje meg, hogy a Microsoft Authenticator alkalmazással kapcsolatos további információkért lásd: [Ismerkedés a Microsoft Authenticator alkalmazás](microsoft-authenticator-app-how-to.md).

>[!Note]
>Ha nem szeretné használni a Microsoft Authenticator alkalmazást, választhat egy másik alkalmazás alatt állítsa be. Ez a cikk a Microsoft Authenticator alkalmazást használ. Az authenticator alkalmazás beállítás nem jelenik meg, ha, lehetséges, hogy a szervezet nem engedélyezi, hogy az alkalmazás hitelesítési ellenőrzése. Ha ez a helyzet, szüksége lesz egy másik módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

### <a name="to-use-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás használata

1. Válassza ki a **hitelesítőalkalmazása** lehetőséget.

    A **alkalmazást** varázsló jelenik meg.

    ![Az alkalmazás varázsló, indítási képernyő beolvasása](media/security-info/security-info-auth-app-wizard.png)

    Ha nem szeretné használni a Microsoft Authenticator alkalmazást, kattintson a **egy másik hitelesítő alkalmazást használni kívánt** hivatkozásra a **az alkalmazás letöltése** képernyő.

2. Miután telepítette a Microsoft Authenticator alkalmazást, válassza ki a **tovább**.

    Ha kéri, értesítések, adjon hozzá egy új fiókot, és válassza **munkahelyi vagy iskolai fiók**.

3. Kattintson a **Tovább** gombra.

    A **a QR-kód** képernyő jelenik meg.

    ![A QR-kód az Authenticator alkalmazás használatával](media/security-info/security-info-scan-qr.png)

4. Nyissa meg a Microsoft Authenticator alkalmazást, jelölje be **fiók hozzáadása** a a **testreszabása és vezérlési** ikonra a jobb felső sarokban lévő, majd válassza ki **munkahelyi vagy iskolai fiók**. 

5. Ha rendelkezik egy QR-kód olvasó alkalmazást, vizsgálja meg a megadott kódot. Ha egy kódot levélolvasó alkalmazás nem rendelkezik, válassza a **nem lehet beolvasni a QR-kód hivatkozás** és manuálisan adja meg a kódot és URL-CÍMÉT a Microsoft Authenticator alkalmazásba.

6. A Microsoft Authenticator alkalmazás használatával hagyja jóvá az értesítést, hogy aktiválja az alkalmazást.

    A biztonsági adatok frissül a személyazonosságát, ha a kétlépéses ellenőrzés vagy az önkiszolgáló jelszó-visszaállítás használata esetén a Microsoft Authenticator alkalmazás használatával.

    >[!Note]
    >Ha vállalata engedélyezi, kap egy ellenőrző kódot, valamint a Microsoft Authenticator alkalmazásban megjelenő értesítésre is. Ha azt szeretné, hogy a kód az alapértelmezett módszer, kövesse a [a biztonsági adatok kezelése](security-info-setup-auth-app.md).

## <a name="additional-security-info-options"></a>További biztonsági információ beállításai

Hogyan a szervezet névjegyeket igazolhatja személyazonosságát, akkor mi alapján szeretne ehhez további lehetősége van. A lehetőségek a következők:

- **Mobileszköz szöveg.** Adja meg a mobileszközök számát, és értesítés SMS-ben egy kódot fogja használni a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Ellenőrizze identitását az szöveges üzenet (SMS) kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata a szöveges üzenetben (SMS)](security-info-setup-text-msg.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszközök számát, és telefonhívás beszerzése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Igazolja személyazonosságát, egy telefonszámmal kapcsolatos lépésenkénti útmutatót lásd: [állítsa be a biztonsági adatok telefonhívásokat használandó](security-info-setup-phone-number.md).

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címet egy e-mailt beolvasni a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. Állítsa be az e-mailben kapcsolatos lépésenkénti útmutatót lásd: [biztonsági adatok beállítása az e-mailben](security-info-setup-email.md).

- **Biztonsági kérdések.** Néhány a szervezet számára a rendszergazda által létrehozott biztonsági kérdések megválaszolása. Ez a beállítás csak akkor használható, a jelszó-visszaállításhoz, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállítása kapcsolatos lépésenkénti útmutatót lásd: a [beállítása a biztonsági adatok a biztonsági kérdések használata](security-info-setup-questions.md) cikk.
    
    >[!Note]
    >Ha ezek a beállítások némelyike hiányzik, azt azért nagy valószínűséggel a szervezet nem engedélyezi azokat a módszereket. Ha ez a helyzet, szüksége lesz egy rendelkezésre álló módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- Ha kell biztonsági adatait, hajtsa végre a következő témakör utasításait a [a biztonsági adatok kezelése](security-info-manage-settings.md) cikk.

- A jelszó alaphelyzetbe állítása, ha elfelejti, az elveszett vagy a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/) vagy kövesse a a [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md) cikk.

- Hibaelhárítási tippek és a bejelentkezési problémákkal kapcsolatos súgó a [nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.