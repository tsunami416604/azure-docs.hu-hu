---
title: A Microsoft Authenticator alkalmazás letöltése és telepítése – Azure AD
description: Töltse le és telepítse a Microsoft Authenticator alkalmazást, hogy ellenőrizze személyazonosságát a kétfaktoros ellenőrzés használata közben.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 9d8ea02c91dc6aec41a1b548eff148e85111fc32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063900"
---
# <a name="download-and-install-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás letöltése és telepítése

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Amennyiben Ön rendszergazda, az Azure Active Directory- (Azure AD-) környezet beállításával és kezelésével kapcsolatosan további információt az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory) talál.

A Microsoft Authenticator alkalmazás segít a fiókokba való bejelentkezésben, ha kétfaktoros ellenőrzést használ. A kétfaktoros ellenőrzés segítségével biztonságosabban férhet hozzá fiókjaihoz, különösen a bizalmas adatok megtekintése közben. Mivel a jelszavak elfelejthetők, ellophatók vagy feltörhetők, a kétfaktoros ellenőrzés egy további biztonsági lépés, amely segít megvédeni fiókját azáltal, hogy megnehezíti mások számára a betörést.

A Microsoft Authenticator alkalmazást többféleképpen is használhatja, többek között:

- **Kétfaktoros ellenőrzés.** A szabványos ellenőrzési módszer, ahol az egyik tényező a jelszó. Miután felhasználónévvel és jelszóval jelentkezett be, jóváhagyhat egy értesítést, vagy megadhat egy megadott ellenőrző kódot.

- **Telefonos bejelentkezés.** A kétfaktoros ellenőrzés olyan verziója, amely lehetővé teszi, hogy jelszó nélkül jelentkezzen be, a felhasználónevével és mobileszközével ujjlenyomattal, arccal vagy PIN-kóddal.

- **Kódgenerálás.** Kódgenerátorként minden más, hitelesítő alkalmazásokat támogató fiókhoz.

> [!Important]
> A Microsoft Authenticator alkalmazás minden olyan fiókkal működik, amely kétfaktoros ellenőrzést használ, és támogatja az időalapú egyszeri jelszó (TOTP) szabványokat.
>
> Előfordulhat, hogy a szervezet nek hitelesítő alkalmazást kell használnia a bejelentkezéshez és a szervezeti adatok és dokumentumok eléréséhez. Bár a felhasználónév megjelenhet az alkalmazásban, a fiók valójában nincs beállítva ellenőrzési módszerként, amíg be nem fejezi a regisztrációs folyamatot. További információt a [Munkahelyi vagy iskolai fiók hozzáadása című témakörben talál.](user-help-auth-app-add-work-school-account.md)
> 
> [!NOTE]
> Ha problémái vannak a fiókjába való bejelentkezéssel, olvassa el [a Ha nem tud bejelentkezni a Microsoft-fiókjába,](https://support.microsoft.com/help/12429) és olvassa el a Segítségért a Ha nem tud bejelentkezni a Microsoft-fiókjába.  További információ arról, hogy mi a teendő, ha a Microsoft-fiókjába való bejelentkezéskor a ["Microsoft-fiók nem létezik"](https://support.microsoft.com/help/13811) üzenet jelenik meg.

## <a name="download-and-install-the-app"></a>Az alkalmazás letöltése és telepítése

Telepítse a Microsoft Authenticator alkalmazás operációs rendszerének legújabb verzióját:

- **Google Android.** Android-eszközén nyissa meg a Google Play webhelyet [a Microsoft Authenticator alkalmazás letöltéséhez és telepítéséhez.](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator)

- **Apple iOS.** Apple iOS-készülékén nyissa meg az App Store-t [a Microsoft Authenticator alkalmazás letöltéséhez és telepítéséhez.](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)

>[!Important]
>Ha jelenleg nem a mobileszközén tartózkodik, a Microsoft Authenticator alkalmazás továbbra is beszerezhető, ha küld egy letöltési hivatkozást a [Microsoft Hitelesítő lapról.](https://www.microsoft.com/en-us/account/authenticator)

## <a name="next-steps"></a>További lépések

Az alkalmazás letöltése és telepítése után hozzá kell adnia a különböző fiókokat. További információkért lásd:

- **Hitelesítő alkalmazás.** Töltse le és használja a hitelesítő alkalmazást, hogy jóváhagyási értesítést vagy véletlenszerűen generált jóváhagyási kódot kapjon a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A Microsoft Authenticator alkalmazás beállításával és használatával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a hitelesítő alkalmazás használatához](security-info-setup-auth-app.md)című témakörben találja.

- **Mobileszköz szövege.** Adja meg mobileszköz-számát, és kapjon egy kódot, amelyet a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához használ. A személyazonosság szöveges üzenettel (SMS) történő ellenőrzéséről a [Biztonsági adatok beállítása szöveges üzenetküldés (SMS) használatához](security-info-setup-text-msg.md)című témakörben talál.

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg mobileszköz-számát, és kapjon telefonhívást a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A személyazonosság telefonszámmal történő igazolásával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása telefonhívások használatához](security-info-setup-phone-number.md).

- **Biztonsági kulcs.** Regisztrálja a Microsofttal kompatibilis biztonsági kulcsot, és használja azt pin-kóddal együtt a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A személyazonosság biztonsági kulccsal történő ellenőrzéséről a [Biztonsági adatok beállítása biztonsági kulcs használatához](security-info-setup-security-key.md)lépésenként.

- **E-mail cím.** Adja meg munkahelyi vagy iskolai e-mail címét, hogy e-mailt kapjon a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. A levelezés beállításával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a levelezés használatához.](security-info-setup-email.md)

- **Biztonsági kérdések.** Válaszoljon a rendszergazda által a szervezet számára létrehozott néhány biztonsági kérdésre. Ez a beállítás csak jelszó-visszaállításesetén érhető el, a kétlépéses ellenőrzésnél nem. A biztonsági kérdések beállításával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a biztonsági kérdések használatához](security-info-setup-questions.md) című cikkben találja.
