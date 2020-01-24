---
title: Töltse le és telepítse a Microsoft Authenticator alkalmazást – Azure AD
description: Töltse le és telepítse a Microsoft Authenticator alkalmazást, hogy ellenőrizze az identitását a kétfaktoros ellenőrzés használata során.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: ba6f4d8d7c8bae73d998ee09de3adba7601e25b8
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704714"
---
# <a name="download-and-install-the-microsoft-authenticator-app"></a>Töltse le és telepítse a Microsoft Authenticator alkalmazást

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Amennyiben Ön rendszergazda, az Azure Active Directory- (Azure AD-) környezet beállításával és kezelésével kapcsolatosan további információt az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory) talál.

A Microsoft Authenticator alkalmazás segít bejelentkezni a fiókjába, ha kétfaktoros ellenőrzést használ. A kétfaktoros ellenőrzés segítségével biztonságosabban férhet hozzá a fiókokhoz, különösen a bizalmas adatok megtekintésekor. Mivel a jelszavak elfelejthető, ellopott vagy sérült, a kétfaktoros ellenőrzés egy további biztonsági lépés, amely segít a fiók védelmében azáltal, hogy nehezebbé teszi a más személyek számára a betörést.

A Microsoft Authenticator alkalmazást többféleképpen is használhatja, többek között:

- **Kétfaktoros ellenőrzés.** A standard ellenőrzési módszer, amelyben az egyik tényező a jelszava. Miután bejelentkezett felhasználónevével és jelszavával, jóváhagyhat egy értesítést, vagy megadhat egy megadott ellenőrző kódot.

- **Telefonos bejelentkezés.** A kétfaktoros ellenőrzés olyan verziója, amely jelszó kérése nélkül, a felhasználónévvel és a mobileszköz ujjlenyomattal, képpel vagy PIN-kódjával való bejelentkezését teszi lehetővé.

- **Kód létrehozása.** A hitelesítő alkalmazásokat támogató egyéb fiókok számára.

> [!Important]
> A Microsoft Authenticator alkalmazás minden olyan fiókkal működik, amely kétfaktoros ellenőrzést használ, és támogatja az idő-alapú egyszeri jelszó (TOTP) szabványait.
>
> Előfordulhat, hogy a szervezete hitelesítő alkalmazást használ a bejelentkezéshez, és hozzáfér a szervezeti adataihoz és dokumentumaihoz. Bár a Felhasználónév megjelenhet az alkalmazásban, a fiók valójában nem úgy van beállítva, hogy ellenőrzési módszerként működjön, amíg el nem végzi a regisztrációs folyamatot. További információkért lásd: [munkahelyi vagy iskolai fiók hozzáadása](user-help-auth-app-add-work-school-account.md).
> 
> [!NOTE]
> Ha problémába ütközik a fiókjába való bejelentkezéssel, akkor a következő témakörben talál segítséget: [Ha nem tud bejelentkezni a Microsoft-fiókba](https://support.microsoft.com/help/12429) .  További információ arról, hogy mi a teendő, ha ["a Microsoft-fiók nem létezik"](https://support.microsoft.com/help/13811) üzenet jelenik meg, amikor megpróbál bejelentkezni a Microsoft-fiókba.

## <a name="download-and-install-the-app"></a>Az alkalmazás letöltése és telepítése

Telepítse a Microsoft Authenticator alkalmazás legújabb verzióját az operációs rendszere alapján:

- **Google Android.** Az Android-eszközön nyissa meg a Google Play eszközt [a Microsoft Authenticator alkalmazás letöltéséhez és telepítéséhez](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator).

- **Apple iOS.** Az Apple iOS-eszközön nyissa meg az App Store-t a [Microsoft Authenticator alkalmazás letöltéséhez és telepítéséhez](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458).

>[!Important]
>Ha jelenleg nem a mobileszközön található, akkor továbbra is letöltheti a Microsoft Authenticator alkalmazást a [Microsoft Authenticator oldaláról](https://www.microsoft.com/en-us/account/authenticator).

## <a name="next-steps"></a>Következő lépések

Miután letöltötte és telepítette az alkalmazást, hozzá kell adnia a különböző fiókokat. További információ eléréséhez lásd:

- **Hitelesítő alkalmazás.** Töltse le és használjon egy hitelesítő alkalmazást a jóváhagyási értesítés vagy a véletlenszerűen generált jóváhagyási kód beszerzéséhez a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz. A Microsoft Authenticator alkalmazás beállításával és használatával kapcsolatos részletes utasításokért lásd: [biztonsági adatok beállítása hitelesítő alkalmazás használatára](security-info-setup-auth-app.md).

- **Mobileszköz szövege.** Adja meg a mobileszköz számát, és szerezzen be egy, a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz használni kívánt kódot. Az identitás szöveges üzenettel (SMS) való ellenőrzésével kapcsolatos részletes utasításokért lásd: [biztonsági információk beállítása szöveges üzenetküldés (SMS) használatára](security-info-setup-text-msg.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszköz számát, és telefonhívást kap a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Az identitás telefonszámmal történő ellenőrzésével kapcsolatos részletes útmutatásért lásd: [biztonsági információk beállítása telefonhívások használatához](security-info-setup-phone-number.md).

- **Biztonsági kulcs.** Regisztrálja Microsoft-kompatibilis biztonsági kulcsát, és használja PIN-kóddal a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Ha részletes útmutatást szeretne arról, hogyan ellenőrizheti személyazonosságát egy biztonsági kulccsal, tekintse meg a biztonsági [adatok beállítása biztonsági kulcs használatára](security-info-setup-security-key.md)című témakört.

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címét, hogy e-mailt kapjon a jelszó alaphelyzetbe állításához. Ez a beállítás kétlépéses ellenőrzés esetén nem érhető el. Az e-mailek beállításával kapcsolatos részletes útmutatásért lásd: [biztonsági adatok beállítása e-mailek használatára](security-info-setup-email.md).

- **Biztonsági kérdések.** Válaszoljon a szervezete rendszergazdája által létrehozott biztonsági kérdésekre. Ez a beállítás csak a jelszó-visszaállításhoz érhető el, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállításával kapcsolatos részletes utasításokért tekintse meg a biztonsági [kérdések beállítása a biztonsági kérdésekre](security-info-setup-questions.md) című cikket.
