---
title: A kétlépéses ellenőrzés – Azure Active Directory hibaelhárítása |} A Microsoft Docs
description: Mi a teendő, ha azok problémába az Azure multi-factor Authentication és a kétlépéses ellenőrzés felhasználók ad útmutatást.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.component: user-help
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.openlocfilehash: fd722da236f3e1fff18e2561756635cd95f098e0
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346523"
---
# <a name="get-help-with-two-step-verification"></a>Segítség a kétlépcsős ellenőrzéshez

A kétlépéses ellenőrzés egy olyan biztonsági funkció, a szervezet védelme érdekében a fiókok az. A kétlépéses ellenőrzés azért biztonságosabb, mint a jelszavak kétféle hitelesítési támaszkodik: valami ismeri, és valami Önnél. A felhasználó által ismert információ a jelszavát, a telefon vagy egy eszköz valami Önnél közben. A kétlépéses ellenőrzés használata segíthet állítsa le a rosszindulatú támadók nem jelentkezik be, Ön akkor is, ha kapnak a jelszavát.

A Microsoft biztosít a kétlépéses ellenőrzést, célszerű a szervezet, amely úgy dönt, hogy e funkció használata. Nem tilthatók le Ha a szervezet megköveteli, ugyanúgy, mint a fiók védelme érdekében jelszóval nem tilthatók le.

>[!Note]
>Ha a kétlépéses ellenőrzés be személyes Microsoft-fiókjával való használatával kapcsolatos további információk keres, tekintse meg a [tudnivalók a kétlépéses ellenőrzés](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) cikk.

## <a name="why-do-i-need-to-use-another-verification-method"></a>Miért kell egy másik ellenőrzési módszert használni?

Több oka is lehet, hogy miért egy másik ellenőrzési módszert a fiókjával használni kell. Példa:

- **Ha elfelejtette a telefonján vagy eszköz.** Néhány nap, hagyja a telefon otthon, de továbbra is be kell jelentkeznie a munkahelyi. Először meg kell próbáljon meg bejelentkezni egy másik módszerrel, amelyhez nincs szükség a telefonjára.

- **A telefon elvesztése vagy itt van egy új telefonszámot.** Ha telefonja elveszett vagy új közben, jelentkezzen be egy másik módszerrel, vagy kérje meg a rendszergazdát, hogy törölje a beállításokat. Javasoljuk, hogy a rendszergazda tudja, ha a telefonja volt elvesztette vagy ellopták, így a megfelelő frissítéseket lehet tenni a fiókjához. A beállítások törlődnek, miután kérni fogja a [regisztrálja a kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) amikor legközelebb bejelentkezik.

- **Nem kapják meg a hitelesítési szöveges vagy telefonhíváson alapuló.** Miért nem kaphat a szöveges vagy telefonhíváson alapuló több oka is van. Ha korábban sikeresen szövegek vagy telefonhívások megkapta, ez a valószínűleg probléma-szolgáltatóval telefon, a fiók nem. Ha gyakran az késleltetések miatt rossz jel, azt javasoljuk, használja a [Microsoft Authenticator alkalmazás](microsoft-authenticator-app-how-to.md) a mobileszközén. Ez az alkalmazás képes véletlenszerű biztonsági kódokat generálhat az jelentkezzen be, minden cella jel vagy internetkapcsolat nélkül.<br><br>Ha szöveges üzenetet fogadni, kérje meg egy szöveges ismerős, ellenőrizze, hogy be egyet, és ha is megkapta több üzenetet, győződjön meg arról, hogy a vizsgálat, használja a kód az aktuális.

- **Az alkalmazásjelszavak nem működik.** Alkalmazásjelszók cserélje le a régebbi asztali alkalmazásokat, amelyek nem támogatják a kétlépéses ellenőrzés normál jelszavát. Győződjön meg róla, hogy helyesen írta be a jelszót. Ha ez nem oldja, próbáljon meg bejelentkezni [hozzon létre egy új jelszót](multi-factor-authentication-end-user-app-passwords.md) vegye fel a kapcsolatot a rendszergazdával vagy [törölje a meglévő alkalmazásjelszavak](../authentication/howto-mfa-userdevicesettings.md) így létrehozhat egy újat.

## <a name="sign-in-using-another-verification-method"></a>Jelentkezzen be egy másik ellenőrzési módszerrel

1. általában bejelentkezni a fiókjába, és válassza ki a **bejelentkezés másik módszerrel** hivatkozásra a **kétlépéses ellenőrzés** lapot.

    ![Ellenőrzési módszert bejelentkezés módosítása](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Ha nem látja a **bejelentkezés másik módszerrel** hivatkozásra, az azt jelenti, hogy Ön még nem állította be bármely egyéb ellenőrzési módot választanak. Jelentkezzen be a fiók segítségért forduljon a rendszergazdához kell. Miután bejelentkezett, győződjön meg arról, adjon hozzá további ellenőrzési módszert. Ellenőrzési módszerek sorát hozzáadásával kapcsolatos további információkért tekintse meg a [felügyelheti a kétlépéses ellenőrzés](multi-factor-authentication-end-user-manage-settings.md) cikk.<br><br>Ha a hivatkozás, de továbbra sem látja bármely egyéb ellenőrzési módot választanak, meg kell jelentkezik be a fiókjába, segítségért forduljon a rendszergazdához.

2. Válassza ki a másik ellenőrzési módszert, és folytassa a kétlépéses ellenőrzési folyamatot.

3. Miután Ön vissza a fiókjában, frissítheti az ellenőrzési módszerek sorát, (ha szükséges). További információ készül hozzáadása vagy módosítása a módszereket, tekintse meg a [felügyelheti a kétlépéses ellenőrzés](multi-factor-authentication-end-user-manage-settings.md) cikk.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nem találok választ a problémára

Ha próbálkozott ezeket a lépéseket, de továbbra is fut, problémák, forduljon a rendszergazdához további segítségért.

## <a name="related-topics"></a>Kapcsolódó témakörök

* [A kétlépéses ellenőrzés beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

* [A Microsoft Authenticator alkalmazás – gyakori kérdések](microsoft-authenticator-app-faq.md)
