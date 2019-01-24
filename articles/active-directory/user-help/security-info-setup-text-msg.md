---
title: Állítsa be a biztonsági adatok szöveges üzenetküldés - használata az Azure Active Directory |} A Microsoft Docs
description: Állítsa be a biztonsági adatok a személyazonosságát, SMS-ben üzenet használatával.
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
ms.openlocfilehash: 64b3ba5d6f3c257fdbd462c0297ff6b0944163db
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814095"
---
# <a name="set-up-security-info-to-use-text-messaging-preview"></a>Állítsa be a biztonsági adatok szöveges üzenetben (előzetes verzió) használata

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

A biztonsági adatok beállításához megköveteli, hogy jelentkezzen be munkahelyi vagy iskolai fiókjával, és majd a regisztráció befejezéséhez. Soha ne beállította a biztonsági adatait, ha rendszer most tennie.

## <a name="set-up-text-messaging"></a>Szöveges üzenetküldés beállítása

Attól függően, a szervezet beállításait a rendszer hozzáadása a szöveges üzenetben hozzá biztonsági adataihoz, amikor bejelentkezik. Ellenkező esetben üzenetkezelési biztonsági adatainak beállítása a kezdéshez kövesse [a biztonsági adatok kezelése](security-info-manage-settings.md).

A szöveges üzenet lehetőséget a telefonos elrendezésre egy részét képezi, így a telefonszám mellékét értékre állítjuk mindent ugyanúgy azonos beállításokkal, de nem hívja meg a Microsoft, szöveges üzenetben használandó fogja választani. Ha nem látja a telefonos elrendezésre, lehetséges, hogy a szervezet nem engedélyezi, hogy az ellenőrzési telefonszám. Ha ez a helyzet, szüksége lesz egy másik módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

### <a name="to-use-a-text-message"></a>Szöveges üzenet használata

1. Válassza ki a **Phone** lehetőséget.

    A **a telefon** varázsló jelenik meg.

    ![Állítsa be az országot vagy régiót és a telefonszámot száma](media/security-info/security-info-keep-secure-setup-text.png)

2. Válassza ki a **ország vagy régió** a legördülő listából, írja be a telefonszámát (többek között a körzetszámot, ha van ilyen) a **telefonszám** jelölje ki a **kódot kérek szöveg** lehetőséget, majd válassza ki **tovább**.

    Meg kell adnia egy ellenőrzési lapot kell egy kódot tartalmazó szöveges üzenetet fog kapni.

    ![Szöveges üzenet kódjának ellenőrzési lap](media/security-info/security-info-keep-secure-verify-text-msg.png)

    A biztonsági adatok frissíti a személyazonosságát, ha a kétlépéses ellenőrzés vagy az önkiszolgáló jelszó-visszaállítás használata esetén szöveges üzenetet küld.

    >[!Note]
    >Ha szeretne kapni a telefonhívás, szöveges üzenet helyett, kövesse a [állítsa be a biztonsági adatok telefonhívásokat használandó](security-info-setup-phone-number.md) cikk.

## <a name="additional-security-info-options"></a>További biztonsági információ beállításai

Hogyan a szervezet névjegyeket igazolhatja személyazonosságát, akkor mi alapján kívánt tegye lehetősége van. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le, és a egy hitelesítő alkalmazást használja, vagy egy jóváhagyási értesítést, vagy egy véletlenszerűen létrehozott jóváhagyási kód lekérése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Állítsa be és a Microsoft Authenticator alkalmazással kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata egy hitelesítő alkalmazást](security-info-setup-auth-app.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszközök számát, és telefonhívás beszerzése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Igazolja személyazonosságát, egy telefonszámmal kapcsolatos lépésenkénti útmutatót lásd: [állítsa be a biztonsági adatok telefonhívásokat használandó](security-info-setup-phone-number.md).

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címet egy e-mailt beolvasni a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. Állítsa be az e-mailben kapcsolatos lépésenkénti útmutatót lásd: [biztonsági adatok beállítása az e-mailben](security-info-setup-email.md).
   
    >[!Note]
    >Ha ezek a beállítások némelyike hiányzik, azt azért nagy valószínűséggel a szervezet nem engedélyezi azokat a módszereket. Ha ez a helyzet, szüksége lesz egy rendelkezésre álló módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

- **Biztonsági kérdések.** Néhány a szervezet számára a rendszergazda által létrehozott biztonsági kérdések megválaszolása. Ez a beállítás csak akkor használható, a jelszó-visszaállításhoz, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállítása kapcsolatos lépésenkénti útmutatót lásd: a [beállítása a biztonsági adatok a biztonsági kérdések használata](security-info-setup-questions.md) cikk.

## <a name="next-steps"></a>További lépések

- Ha kell biztonsági adatait, hajtsa végre a következő témakör utasításait a [a biztonsági adatok kezelése](security-info-manage-settings.md) cikk.

- A jelszó alaphelyzetbe állítása, ha elfelejti, az elveszett vagy a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/) vagy kövesse a a [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md) cikk.

- Hibaelhárítási tippek és a bejelentkezési problémákkal kapcsolatos súgó a [nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.