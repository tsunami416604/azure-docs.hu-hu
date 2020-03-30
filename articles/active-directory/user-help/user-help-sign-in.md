---
title: Bejelentkezés az identitás-hitelesítési adatok használatával - Azure AD
description: Ismerje meg, hogyan jelentkezz be a különböző identitás-ellenőrzési módszerek használatával a biztonsági adatokban.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 7d1d27cede6c593150d3937bb3aa0ca59394eea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062166"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Bejelentkezés kétlépéses ellenőrzéssel vagy biztonsági adatokkal

A kétlépéses ellenőrzési vagy biztonsági adatok beállítása után a megadott ellenőrzési módszerrel jelentkezhet be fiókjába.

> [!Note]
> Ha még mindig használja a kétlépéses ellenőrzési élményt, akkor be kell állítania az ellenőrzési módszereket a [Fiók beállítása a kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikk utasításait követve.
>
> Ha a rendszergazda bekapcsolta a biztonsági adatok használatát, az alábbi lépésekkel kell beállítania az ellenőrzési módszereket:<ul><li>[Biztonsági adatok beállítása hitelesítési alkalmazás használatához](security-info-setup-auth-app.md)</li><li>[A biztonsági adatok szöveges üzenetben történő hitelesítésének beállítása](security-info-setup-text-msg.md)</li><li>[A biztonsági adatok beállítása telefonhívás használatához](security-info-setup-phone-number.md)</li><li>[Biztonsági adatok beállítása biztonsági kulcs használatához](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Bejelentkezés hitelesítő alkalmazásértesítéssel mobileszközön

1. Jelentkezzen be fiókjába felhasználónevével és jelszavával.

2. Válassza a **Jóváhagyás** lehetőséget a mobileszközére küldött jóváhagyási értesítésből.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Bejelentkezés hitelesítő alkalmazáskóddal mobileszközön

1. Jelentkezzen be fiókjába felhasználónevével és jelszavával.

2. Nyissa meg hitelesítő alkalmazását, és írja be a fiókjához véletlenszerűen generált kódot az **Enter kód** mezőbe.

## <a name="sign-in-using-your-phone-number"></a>Bejelentkezés a telefonszámával

1. Jelentkezzen be fiókjába felhasználónevével és jelszavával.

2. Vegye fel a telefont, és kövesse az utasításokat.

## <a name="sign-in-using-a-text-message"></a>Bejelentkezés szöveges üzenetben

1. Jelentkezzen be fiókjába felhasználónevével és jelszavával.

2. Nyissa meg a szöveges üzenetet, és írja be a kódot a szöveges üzenetből az **Enter kód** mezőbe.

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Bejelentkezés biztonsági kulccsal a zárolási képernyőn

1. Miután regisztrálta a biztonsági kulcsot, válassza ki a biztonsági kulcs képét a Windows 10 zárolási képernyőjén.

2. Helyezze be a biztonsági kulcsot az eszköz USB-portjába, és jelentkezzen be a Windows rendszerbe a biztonsági kulcs PIN-kódjával.

    ![Biztonsági kulcs bejelentkezésa a Windows 10 zárolási képernyőn](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Bejelentkezés biztonsági kulccsal és a Microsoft Edge böngészővel

1. Miután regisztrálta a biztonsági kulcsot, nyissa meg a Microsoft Edge böngészőt.

2. Amikor a rendszer bejelentkezést kér, helyezze be a biztonsági kulcsot az eszköz USB-portjába, és jelentkezzen be a Windows rendszerbe a biztonsági kulcs PIN-kódjával.

    ![Biztonsági kulcs bejelentkezése a Microsoft Edge böngészővel](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >A Microsoft Authenticator alkalmazással történő bejelentkezésről a [Bejelentkezés a fiókokba a Microsoft Authenticator alkalmazással című cikkben](user-help-auth-app-sign-in.md)olvashat.

## <a name="sign-in-using-another-verification-method"></a>Bejelentkezés más ellenőrzési módszerrel

Ha valamilyen okból nem tudja használni az elsődleges bejelentkezési módszert, használhat egy másik, korábban beállított ellenőrzési módszert.

1. Jelentkezzen be a fiókjába a szokásos módon, majd válassza a **Bejelentkezés más módon** hivatkozást a **kétlépéses ellenőrzés** oldalon.

    ![Bejelentkezési ellenőrzési módszer módosítása](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Ha nem látja a **Sign in other way** linket, az azt jelenti, hogy nem állított be más ellenőrzési módszert, és a rendszergazdától kell segítséget kérnie a fiókba való bejelentkezéshez. Miután a rendszergazda segít a bejelentkezésben, adjon hozzá további ellenőrzési módszereket. Az ellenőrzési módszerek hozzáadásáról a [Kétlépéses ellenőrzés beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikkben olvashat bővebben.
    >
    >Ha a **Bejelentkezés más módon** hivatkozást látja, de más ellenőrzési módszert még nem lát, akkor a rendszergazdától kell segítséget kérnie a fiókjába való bejelentkezéshez.

2. Válassza ki az alternatív ellenőrzési módszert, és folytassa a kétlépéses ellenőrzési folyamatot.

3. Miután visszatért a fiókjába, szükség esetén frissítheti az ellenőrzési módszereket. A módszerek hozzáadásáról és módosításáról a [Kétlépéses ellenőrzés beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikkben olvashat bővebben.

## <a name="next-steps"></a>További lépések

- A biztonsági adatokról a [Biztonsági adatok (előzetes verzió) című cikkben](user-help-security-info-overview.md) olvashat.

- A kétlépéses ellenőrzésről a [kétlépéses ellenőrzés áttekintéséről](user-help-two-step-verification-overview.md) szóló cikkben olvashat.

- A jelszó alaphelyzetbe állítása, ha elvesztette vagy elfelejtette azt, a [Jelszó-visszaállítás portálról](https://passwordreset.microsoftonline.com/)

- Hibaelhárítási tippeket és segítséget kaphat a Bejelentkezési problémákról a [Nem lehet bejelentkezni a Microsoft-fiókkal](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) kapcsolatos cikkben.
