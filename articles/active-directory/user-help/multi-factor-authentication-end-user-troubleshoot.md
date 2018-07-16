---
title: A kétlépéses ellenőrzés – az Azure AD hibaelhárítása |} A Microsoft Docs
description: Ez a dokumentum információkat tartalmaz felhasználók Mi a teendő, ha azok problémába Azure multi-factor Authentication szolgáltatással.
services: multi-factor-authentication
keywords: többtényezős hitelesítés ügyfél, a hitelesítési probléma, a korrelációs azonosító
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/06/2017
ms.author: lizross
ms.reviewer: richagi
ms.custom: end-user
ms.openlocfilehash: deb75c2601fa55f7cdb1681d8f73e94d6b01310a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060074"
---
# <a name="get-help-with-two-step-verification"></a>Segítség a kétlépcsős ellenőrzéshez
Ez a cikk, amely felteszik azt a kérdést a kétlépéses ellenőrzés kapcsolatos leggyakoribb kérdésekre ad választ.

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>Miért kell végrehajtani a kétlépéses ellenőrzés? Lehet, a kikapcsolása után?

A kétlépéses ellenőrzés egy biztonsági szolgáltatás, amely a szervezet úgy döntött, a fiókok védelmét. Ennek az oka biztonságosabb, mint a jelszavak, kétféle hitelesítés támaszkodik: valami ismeri, és valami Önnél. A felhasználó által ismert információ a jelszavát. A hiba történt Önnél szó, amelyek azt gyakran meg telefonon vagy eszköz. Ha fiókját kétlépéses ellenőrzéssel védett, ez azt jelenti, hogy a támadóknak nem tud bejelentkezni, ha kapnak a jelszavát valamilyen módon, mert nem rendelkeznek hozzáféréssel a telefonjára, túl.

A Microsoft kínál a kétlépéses ellenőrzést, de a szervezet úgy dönt, hogy a funkció használatához. Nem tilthatók le Ha a cég informatikai támogatási szolgálata azt, hogy a igényel, ugyanúgy, mint a fiók védelme érdekében jelszóval nem tilthatók le.

Ha rendelkezik személyes Microsoft-fiókjához kapcsolva a kétlépéses ellenőrzést, és módosítani szeretné a beállításokat, olvassa el [tudnivalók a kétlépéses ellenőrzés](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) helyette.

## <a name="i-dont-have-my-phone-with-me-today"></a>Nem rendelkezem telefonom velem megosztva még ma

Néhány nap, hagyja a telefon otthon, de továbbra is be kell jelentkeznie a munkahelyi. Először is meg kell próbálnia bejelentkezés egy másik ellenőrzési módszert. A kétlépéses ellenőrzéshez regisztrálásakor fejeződött beállítása egynél több telefonszámot? Próbáljon meg azzal bejelentkezni egy másik módszert, kövesse az alábbi lépéseket:

1. Jelentkezzen be a megszokott módon.
2. Válassza a kétlépéses ellenőrzés lap megnyitásakor **másik ellenőrzési lehetőség használata**.

   ![Másik ellenőrzési](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Válassza ki a használni kívánt ellenőrzési lehetőséget.
4. Folytassa a kétlépéses ellenőrzést.

Ha nem látja a **másik ellenőrzési lehetőség használata** hivatkozásra, majd azt jelenti, hogy alternatív módszereket nem adott meg, amikor először regisztrálta a kétlépéses ellenőrzéshez. A céges ügyfélszolgálattól kaphat segítséget jelentkezik be a fiókjába. Miután bejelentkezett, ügyeljen arra, hogy [és beállítások kezelése](multi-factor-authentication-end-user-manage-settings.md) további hitelesítési módszerek hozzáadása a következő alkalomra.

Ha látja az **másik ellenőrzési lehetőség használata** hivatkozás, de nem fér hozzá az alternatív módszerek vagy, forduljon a céges ügyfélszolgálat segítséget jelentkezik be a fiókjába.

## <a name="i-lost-my-phone-or-got-a-new-number"></a>E telefonom elvész, vagy van egy új száma
Kétféleképpen visszatéréshez fiókjába. Az első, hogy jelentkezzen be a másik hitelesítési telefonszámát, ha az egyik beállítását. A második pedig kérje a cég informatikai támogatási, törölje a beállításokat.

Ha a telefonja volt elveszett vagy ellopták, azt javasoljuk, hogy, ossza meg a cég támogatási ahhoz, hogy az alkalmazás jelszavak alaphelyzetbe állítása, illetve törölje az összes megjegyzett eszközökön.

### <a name="use-an-alternate-phone-number"></a>Egy másik telefonszám használata
Ha meg van adva több ellenőrzési lehetőség, beleértve egy másodlagos telefonszámot vagy egy hitelesítő alkalmazást egy másik eszközön, használhatja az egyiket való bejelentkezéshez.

Jelentkezzen be a másodlagos telefonszámot, kövesse az alábbi lépéseket:

1. Jelentkezzen be a megszokott módon.
2. Amikor a rendszer kéri, további ellenőrizni a fiókját, válassza ki a **másik ellenőrzési lehetőség használata**.

   ![Másik ellenőrzési](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Válassza a telefon száma vagy eszköz, amely hozzáféréssel rendelkezik.
4. Vissza a fiókját, miután [és beállítások kezelése](multi-factor-authentication-end-user-manage-settings.md) módosíthatja a hitelesítési telefonszámát.

### <a name="clear-your-settings"></a>A beállítások törlése
Ha nem konfigurálta a másodlagos hitelesítő telefonszám, további segítségért forduljon a cég informatikai támogatási szolgálata rendelkezik. Rendelkezik törölje azokat a beállításokat, így amikor legközelebb bejelentkeznek, meg kell adnia a [regisztrálja a kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) újra.

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>E nem érkeznek meg egy szöveget, vagy hívja az kérek a telefonomra
Miért lehet, hogy próbál jelentkezni, de nem jelenik meg a szöveges vagy telefonhíváson alapuló több oka is van. Ha már sikeresen kapott szövegek vagy telefonhívások a telefonján a múltban, majd ennek oka valószínűleg a telefon szolgáltató, a fiók nem probléma. Győződjön meg arról, hogy a helyes cella jel rendelkezik, és ha fogadni kívánt szöveges üzenet ügyeljen arra, hogy meg tudja fogadni a szöveges üzenetek. Kérje meg egy ismerősök meghívásához, vagy SMS, teszteléshez használhat.

Ha egy szöveges vagy telefonhíváson alapuló több percig is várta, feltörni a fiókját a leggyorsabb módja az próbálkozzon egy másik lehetőséget.

1. Válassza ki **másik ellenőrzési lehetőség használata** azon az oldalon, az ellenőrzés vár.

    ![Másik ellenőrzési](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Válassza ki a használni kívánt telefon száma vagy a kézbesítési módszert.

    Ha több ellenőrző kódok érkezett, használja a legújabb.

Nincs konfigurálva másik módszert, ha a céges ügyfélszolgálattól, és kérje meg őket, törölje a beállításokat. Amikor legközelebb bejelentkezik, kérni fogja a [a multi-factor authentication szolgáltatás beállítása](multi-factor-authentication-end-user-first-time.md) újra.

Ha gyakran az késleltetések miatt rossz cella jel, azt javasoljuk, használja a [Microsoft Authenticator alkalmazás](microsoft-authenticator-app-how-to.md) okostelefonos. Az alkalmazás való bejelentkezéshez használt véletlenszerű biztonsági kódokat generálhat, és ezek a kódok nem igénylik bármilyen cella jel vagy internetes kapcsolatot.

## <a name="app-passwords-are-not-working"></a>Az alkalmazásjelszavak nem működnek.
Első lépésként ellenőrizze, hogy jelszót adott meg az alkalmazás megfelelően. A létrehozott alkalmazás jelszó váltja fel a normál jelszavát, de csak a régebbi asztali alkalmazásokat, amelyek nem támogatják a kétlépéses ellenőrzést. Ha az eszköz még nem működik, próbálja meg aláírás az és [hozzon létre egy új jelszót](multi-factor-authentication-end-user-app-passwords.md).  Ha továbbra sem működik, lépjen kapcsolatba a cég informatikai támogatási és őket [törölje a meglévő alkalmazásjelszavak](../authentication/howto-mfa-userdevicesettings.md) majd hozhat létre egy újat.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nem találok választ a problémára.
Ha próbálkozott a hibaelhárítási lépéseket, de továbbra is fut, problémák, lépjen kapcsolatba a cég informatikai támogatási szolgálatának. Segíthet kell lennie.

## <a name="related-topics"></a>Kapcsolódó témakörök
* [A kétlépéses ellenőrzés beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)  
* [A Microsoft Authenticator alkalmazás – gyakori kérdések](microsoft-authenticator-app-faq.md)
