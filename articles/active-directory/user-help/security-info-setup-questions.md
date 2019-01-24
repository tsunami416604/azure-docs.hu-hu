---
title: Biztonsági adatok beállítása a biztonsági kérdések – Azure Active Directory használatára |} A Microsoft Docs
description: Állítsa be a biztonsági adatok a személyazonosságát, előre definiált biztonsági kérdések használatával.
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
ms.openlocfilehash: 0ee486c41193608887763b611ed2a879cb5e0ef4
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814783"
---
# <a name="set-up-security-info-to-use-pre-defined-security-questions-preview"></a>Állítsa be a biztonsági adatai előre definiált biztonsági kérdések (előzetes verzió) használata

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

A biztonsági adatok beállításához megköveteli, hogy jelentkezzen be munkahelyi vagy iskolai fiókjával, és majd a regisztráció befejezéséhez. Soha ne beállította a biztonsági adatait, ha rendszer most tennie.

## <a name="set-up-security-questions"></a>Biztonsági kérdések beállítása

Attól függően, a szervezet beállítások rendszer felkérheti biztonsági kérdések hozzáadása a biztonsági adatait, amikor bejelentkezik. Ellenkező esetben a biztonsági adatok biztonsági kérdések beállítása a kezdéshez kövesse [a biztonsági adatok kezelése](security-info-manage-settings.md).

Biztonsági kérdések használatakor javasoljuk egy másik módszerrel együtt használja őket. Biztonsági kérdések kevésbé biztonságos, mint más módszerekkel is lehet, mivel vannak, akik előfordulhat, hogy egy másik személy kérdésekre adott válaszokat.

>[!Note]
>Biztonsági kérdések a címtárban lévő felhasználói objektum a privát módon és biztonságosan tárolja, és csak választ, a regisztráció során. Nincs lehetőség a olvasni vagy módosítani a kérdések és válaszok a rendszergazdának.<br>Ha a biztonsági kérdések beállítás nem jelenik meg, lehetséges, hogy a szervezet nem engedélyezi, hogy az ellenőrző biztonsági kérdéseket. Ha ez a helyzet, szüksége lesz egy másik módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

### <a name="to-choose-and-answer-your-security-questions"></a>Válassza ki, és a biztonsági kérdések megválaszolása

1. Válassza ki **biztonsági kérdések**, majd válassza a mely biztonsági kérdéseket megválaszolni kívánt. 

    A törléshez biztonsági kérdések számát, a rendszergazda határozza meg.

    ![Biztonsági adatok lapon, válassza ki a biztonsági kérdések](media/security-info/security-info-keep-secure-setup-pick-questions.png)

2. Adja meg a kiválasztott kérdések válaszokat, és válassza ki **kész**.

## <a name="additional-security-info-options"></a>További biztonsági információ beállításai

Hogyan a szervezet névjegyeket igazolhatja személyazonosságát, akkor mi alapján kívánt tegye lehetősége van. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le, és a egy hitelesítő alkalmazást használja, vagy egy jóváhagyási értesítést, vagy egy véletlenszerűen létrehozott jóváhagyási kód lekérése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Állítsa be és a Microsoft Authenticator alkalmazással kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata egy hitelesítő alkalmazást](security-info-setup-auth-app.md).

- **Mobileszköz szöveg.** Adja meg a mobileszközök számát, és értesítés SMS-ben egy kódot fogja használni a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Ellenőrizze identitását az szöveges üzenet (SMS) kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata a szöveges üzenetben (SMS)](security-info-setup-text-msg.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszközök számát, és telefonhívás beszerzése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Igazolja személyazonosságát, egy telefonszámmal kapcsolatos lépésenkénti útmutatót lásd: [állítsa be a biztonsági adatok telefonhívásokat használandó](security-info-setup-phone-number.md).

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címet egy e-mailt beolvasni a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. Állítsa be az e-mailben kapcsolatos lépésenkénti útmutatót lásd: [biztonsági adatok beállítása az e-mailben](security-info-setup-email.md).
   
    >[!Note]
    >Ha ezek a beállítások némelyike hiányzik, azt azért nagy valószínűséggel a szervezet nem engedélyezi azokat a módszereket. Ha ez a helyzet, szüksége lesz egy rendelkezésre álló módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- Ha kell biztonsági adatait, hajtsa végre a következő témakör utasításait a [a biztonsági adatok kezelése](security-info-manage-settings.md) cikk.

- A jelszó alaphelyzetbe állítása, ha elfelejti, az elveszett vagy a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/) vagy kövesse a a [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md) cikk.

- Hibaelhárítási tippek és a bejelentkezési problémákkal kapcsolatos súgó a [nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.