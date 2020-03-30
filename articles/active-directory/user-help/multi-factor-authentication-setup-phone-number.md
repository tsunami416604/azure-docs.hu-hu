---
title: Mobileszköz beállítása kétfaktoros ellenőrzési módszerként – Azure Active Directory | Microsoft dokumentumok
description: További információ arról, hogyan állíthat be mobileszközt kétfaktoros ellenőrzési módszerként.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: b0bfaa87c77ba9fff9f6605c1989e48ffbc3fb35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062506"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Mobileszköz beállítása kétfaktoros ellenőrzési módszerként

Beállíthatja, hogy a mobileszköz kétfaktoros ellenőrzési módszerként működjön. A mobiltelefon vagy kap egy szöveges üzenetet egy ellenőrző kódot, vagy egy telefonhívást.

>[!Note]
> Ha a hitelesítési telefon beállítás szürkén jelenik meg, lehetséges, hogy a szervezet nem engedélyezi telefonszám vagy szöveges üzenet használatát ellenőrzésre. Ebben az esetben másik módszert kell választania, vagy további segítségért forduljon a rendszergazdához.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>A mobileszköz beállítása szöveges üzenet használatára ellenőrzési módszerként

1. A **További biztonsági ellenőrzés** lapon válassza a **Hitelesítési telefon** lehetőséget az **1.**

2. Válassza **a Kód küldése szöveges üzenettel** lehetőséget a **Metódus** területről, majd válassza a **Tovább**gombot.

    ![További biztonsági ellenőrző lap hitelesítési telefonnal és szöveges üzenettel](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Írja be az ellenőrző kódot a Microsoft által küldött szöveges üzenetből a **2.** **Verify**

    ![További biztonsági ellenőrző lap hitelesítési telefonnal és szöveges üzenettel](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. **3. lépés: Továbbra is használja a meglévő alkalmazásterületet,** másolja a megadott alkalmazásjelszót, és illessze be egy biztonságos helyre.

    ![A További biztonsági ellenőrzés lap alkalmazásjelszavak területe](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Az alkalmazásjelszó régebbi alkalmazásokkal való használatáról az [Alkalmazásjelszavak kezelése című](multi-factor-authentication-end-user-app-passwords.md)témakörben talál további információt. Csak akkor kell alkalmazásjelszavakat használnia, ha továbbra is olyan régebbi alkalmazásokat használ, amelyek nem támogatják a kétfaktoros ellenőrzést.

5. Válassza a **Done** (Kész) lehetőséget.

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>A mobileszköz beállítása telefonhívás fogadására

1. A **További biztonsági ellenőrzés** lapon válassza a **Hitelesítési telefon** lehetőséget az **1.**

2. Válassza a **Metódus** terület **Hívjon meg** lehetőséget, majd válassza a **Tovább**gombot.

    ![További biztonsági ellenőrző oldal hitelesítési telefonhívással és telefonhívással](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. A Microsoft telefonhívást fog kapni, amelyben arra kéri, hogy a mobileszközén nyomja meg a font (#) jelet a személyazonosságának igazolására.

    ![A megadott telefonszám tesztelése](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. **3. lépés: Továbbra is használja a meglévő alkalmazásterületet,** másolja a megadott alkalmazásjelszót, és illessze be egy biztonságos helyre.

    ![A További biztonsági ellenőrzés lap alkalmazásjelszavak területe](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Az alkalmazásjelszó régebbi alkalmazásokkal való használatáról az [Alkalmazásjelszavak kezelése című](multi-factor-authentication-end-user-app-passwords.md)témakörben talál további információt. Csak akkor kell alkalmazásjelszavakat használnia, ha továbbra is olyan régebbi alkalmazásokat használ, amelyek nem támogatják a kétfaktoros ellenőrzést.

5. Válassza a **Done** (Kész) lehetőséget.

## <a name="next-steps"></a>További lépések

A kétfaktoros ellenőrzési módszer beállítása után további módszereket adhat hozzá, kezelheti a beállításokat és az alkalmazásjelszavakat, bejelentkezhet, vagy segítséget kaphat néhány gyakori, kéttényezős ellenőrzéssel kapcsolatos problémához.

- [A kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

- [Alkalmazásjelszavak kezelése](multi-factor-authentication-end-user-app-passwords.md)

- [Bejelentkezés kétfaktoros ellenőrzéssel](multi-factor-authentication-end-user-signin.md)

- [Segítség a kétfaktoros ellenőrzéshez](multi-factor-authentication-end-user-troubleshoot.md)
