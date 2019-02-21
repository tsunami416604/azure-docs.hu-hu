---
title: Biztonsági adatok (előzetes verzió) beállítása a biztonsági kérdések – Azure Active Directory használatára |} A Microsoft Docs
description: Hogyan állítható be a biztonsági adatok ellenőrzése az identitás használatával előre definiált biztonsági kérdések.
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
ms.openlocfilehash: b0fd2cd0250e3b3074e5632f30c8dbcc8db41e6e
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455024"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>Biztonsági adatok (előzetes verzió) beállítása a biztonsági kérdések használata
Követheti a következő lépésekkel adhatja hozzá a jelszó-visszaállítási mód. Miután beállította a először, visszatérhet a **biztonsági adatok** lap hozzáadása, frissítése vagy törlése a biztonsági információkat.

Miután beállította a jelszó-visszaállítási mód, is be kell állítania a kétfaktoros hitelesítési módszer, használatával egy [hitelesítő alkalmazás](security-info-setup-auth-app.md), [szöveges üzenetküldés](security-info-setup-text-msg.md), vagy egy [telefonhívás](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>A biztonsági adatok weblapját a biztonsági kérdések beállítása
Attól függően, a szervezet beállításait válassza ki, és néhány biztonsági kérdések megválaszolása, a biztonsági adatok módszerek egyike előfordulhat, hogy lehet. A rendszergazda állítja be, hogy válassza ki, és válaszoljon a biztonsági kérdések számát.

Biztonsági kérdések használatakor javasoljuk egy másik módszerrel együtt használja őket. Biztonsági kérdések kevésbé biztonságos, mint más módszerekkel lehet, mivel vannak, akik előfordulhat, hogy a kérdésekre adott válaszokat.

>[!Note]
>Biztonsági kérdések a címtárban lévő felhasználói objektum a privát módon és biztonságosan tárolja, és csak választ, a regisztráció során. Nincs lehetőség a olvasni vagy módosítani a kérdések és válaszok a rendszergazdának.

>Ha a biztonsági kérdések beállítás nem jelenik meg, lehetséges, hogy a szervezet nem engedélyezi, hogy az ellenőrző biztonsági kérdéseket. Ha ez a helyzet, szüksége lesz egy másik módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

### <a name="to-set-up-your-security-questions"></a>A biztonsági kérdések beállítása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjával, és keresse meg a https://myprofile.microsoft.com/ lapot.

    ![Saját profil lapot, amely a kijelölt biztonsági adatai hivatkozások](media/security-info/securityinfo-myprofile.png)

2. Válassza ki **biztonsági adatok** a bal oldali navigációs ablaktáblán vagy a hivatkozás a **biztonsági adatok** letiltása, és válassza ki **metódus hozzáadása** a a **biztonsági adatai**  lapot.

    ![Biztonsági adatok weblapját a kiemelt Hozzáadás metódus beállítás](media/security-info/securityinfo-myprofile-addmethod.png)

3. Az a **adjon meg egy metódust** lapon jelölje be **biztonsági kérdések** a legördülő listából válassza ki, és válassza ki a **Hozzáadás**.

    ![Adja hozzá a metódus be, a biztonsági kérdés kijelölve](media/security-info/securityinfo-myprofile-addquestions.png)

4. Az a **biztonsági kérdések** lapon válassza ki, és a biztonsági kérdések megválaszolása, és válassza ki **mentése**.

    ![Telefonszám hozzáadása, és válassza ki a telefonhívások](media/security-info/securityinfo-myprofile-securityquestions.png)

    A biztonsági adatok frissítése, és a biztonsági kérdések segítségével igazolhatja a személyazonosságát, jelszó-visszaállítás használata esetén.

## <a name="delete-security-questions-from-your-security-info-methods"></a>A biztonsági adatok módszerek a biztonsági kérdések törlése
Ha már nem szeretné a biztonsági kérdések security info módszerként használhatják, eltávolíthatja azokat a **biztonsági adatok** lapot.

>[!Important]
>Ha véletlenül törli a biztonsági kérdések, nincs semmilyen módon nem lehet visszavonni. Kell adja hozzá újra, a módszer lépései a [a biztonsági kérdések beállítása](#set-up-your-security-questions-from-the-security-info-page) című szakaszát.

### <a name="to-delete-your-security-questions"></a>A biztonsági kérdések törlése

1. Az a **biztonsági adatok** lapon válassza ki a **törlése** mellett kapcsolni a **biztonsági kérdések** lehetőséget.

    ![A telefonos metódus törölni a biztonsági adatok összekapcsolása](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Válassza ki **Igen** a megerősítés mezőben törlése a **biztonsági kérdések**. Miután a biztonsági kérdések törlése, a metódus eltávolítják a biztonsági adatok és, eltűnik a **biztonsági adatok** lapot.

## <a name="additional-security-info-methods"></a>További biztonsági adatok módszerek
Hogyan a szervezet névjegyeket igazolhatja személyazonosságát, akkor mi alapján szeretne ehhez további lehetősége van. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le, és a egy hitelesítő alkalmazást használja, vagy egy jóváhagyási értesítést, vagy egy véletlenszerűen létrehozott jóváhagyási kód lekérése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Állítsa be és a Microsoft Authenticator alkalmazással kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata egy hitelesítő alkalmazást](security-info-setup-auth-app.md).

- **Mobileszköz szöveg.** Adja meg a mobileszközök számát, és értesítés SMS-ben egy kódot fogja használni a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Ellenőrizze identitását az szöveges üzenet (SMS) kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata a szöveges üzenetben (SMS)](security-info-setup-text-msg.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszközök számát, és telefonhívás beszerzése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Igazolja személyazonosságát, egy telefonszámmal kapcsolatos lépésenkénti útmutatót lásd: [állítsa be a biztonsági adatok telefonhívásokat használandó](security-info-setup-phone-number.md).

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címet egy e-mailt beolvasni a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. Állítsa be az e-mailben kapcsolatos lépésenkénti útmutatót lásd: [biztonsági adatok beállítása az e-mailben](security-info-setup-email.md).
   
    >[!Note]
    >Ha ezek a beállítások némelyike hiányzik, azt azért nagy valószínűséggel a szervezet nem engedélyezi azokat a módszereket. Ha ez a helyzet, szüksége lesz egy rendelkezésre álló módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- A jelszó alaphelyzetbe állítása, ha elfelejti, az elveszett vagy a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/) vagy kövesse a a [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md) cikk.

- Hibaelhárítási tippek és a bejelentkezési problémákkal kapcsolatos súgó a [nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.