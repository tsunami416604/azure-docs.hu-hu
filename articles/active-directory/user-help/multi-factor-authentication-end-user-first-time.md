---
title: Hol található a további ellenőrzési oldal? - Az Azure Hirdetés
description: Hogyan juthat el a További biztonsági ellenőrzés oldalra a kétfaktoros ellenőrzéshez.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 5a7f0e10b23bf1a541fe83c3112962c38f7e1331
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062557"
---
# <a name="what-is-the-additional-verification-page"></a>Hol található a további ellenőrzési oldal?

Kapott egy e-mailt az informatikai részlegről vagy a főnökétől, amely szerint a szervezet biztonsági ellenőrzést adott a fiókjához. Mit jelent ez? Azt jelenti, hogy a cége további lépésekkel ellenőrzi, hogy Ön tényleg az-e, akinek a bejelentkezésnél mondja magát. Ez az extra ellenőrzés, más néven kétfaktoros ellenőrzés, a felhasználónév, a jelszó, valamint egy mobileszköz vagy telefon kombinációjával történik.

A kétfaktoros ellenőrzés biztonságosabb, mint a jelszó, mivel a hitelesítés két formájára támaszkodik: valami, amit tud, és valami, ami önnel van. Amit tud, az a jelszava. Ami Önnél van, az egy telefon vagy egy olyan eszköz, amit általában magánál tart. A kétfaktoros ellenőrzés segíthet megakadályozni, hogy a rosszindulatú hackerek úgy tegyenek, mintha ön lennének, mert még ha van is jelszavajuk, akkor is előfordulhat, hogy nem rendelkeznek az Ön eszközével.

>[!Important]
>Ez a cikk azoknak a felhasználóknak szól, akik kétfaktoros alain@contoso.comellenőrzést próbálnak használni egy munkahelyi vagy iskolai fiókkal (például) Ha Ön rendszergazda, és az alkalmazottak vagy más felhasználók kétfaktoros ellenőrzésének bekapcsolásáról szeretne tájékozódni, olvassa el az [Azure Active Directory hitelesítési dokumentációját.](https://docs.microsoft.com/azure/active-directory/authentication/)

## <a name="who-decides-if-you-use-this-feature"></a>Ki dönti el, hogy használja-e ezt a funkciót?

A fiók típusától függően a szervezet dönthet úgy, hogy kétfaktoros ellenőrzést kell használnia, vagy ön dönthet ön maga.

- **Munkahelyi vagy iskolai fiók.** Ha munkahelyi vagy iskolai fiókot használ (például a szervezeten múlik, alain@contoso.comhogy kétfaktoros ellenőrzést kell-e használnia az adott ellenőrzési módszerekkel együtt. Mivel a szervezet úgy döntött, hogy használnia kell ezt a funkciót, nincs mód arra, hogy egyenként kikapcsolja.

- **Személyes Microsoft-fiók.** Beállíthatja a kétfaktoros ellenőrzést a személyes Microsoft-fiókjaihoz alain@outlook.com(például). Ha problémái vannak a kétfaktoros ellenőrzéssel és a személyes Microsoft-fiókjával, olvassa el [a Kétfaktoros ellenőrzés be- és kikapcsolása a Microsoft-fiókjához](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Mivel ön dönti el, hogy használja-e ezt a funkciót, bármikor be- és kikapcsolhatja.

    >[!Note]
    >Ha problémái vannak a kétlépéses ellenőrzéssel és az egyik személyes danielle@outlook.comMicrosoft-fiókjával (például), akkor a [Kétlépéses ellenőrzés használata a Microsoft-fiókjával](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification)című javaslatokkal próbálkozhat.

## <a name="access-the-additional-security-verification-page"></a>A További biztonsági ellenőrzés lap elérése

Miután a szervezet bekapcsolja, és beállítja a kétfaktoros ellenőrzést, egy gyors üzenet jelenik meg, amely ből további információkat kell megadnia a fiók biztonságának megőrzéséhez.

![További információ szükséges kérdés](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>A További biztonsági ellenőrzés lap elérése

1. Válassza a **Tovább** lehetőséget a **További információ szükséges** kérdésben.

    Megjelenik **a További biztonsági ellenőrzés** lap.

2. A **További biztonsági ellenőrzés** oldalon el kell döntenie, hogy melyik kétfaktoros ellenőrzési módszert használja annak igazolására, hogy ön az, akinek mondja magát, miután bejelentkezett a munkahelyi vagy iskolai fiókjába. Az alábbiak közül választhat:

    | Kapcsolatfelvételi módszer | Leírás |
    | --- | --- |
    | Mobilalkalmazás | <ul><li>**Értesítésekfogadás ellenőrzésre.** Ez a beállítás értesítést küld a hitelesítő alkalmazásnak az okostelefonon vagy a táblagépen. Tekintse meg az értesítést, és ha az jogos, válassza a **Hitelesítés** lehetőséget az alkalmazásban. Előfordulhat, hogy a munkahelyi vagy iskolai hitelesítés előtt pin-kódot kell megadnia.</li><li>**Használjon ellenőrző kódot.** Ebben a módban a hitelesítő alkalmazás létrehoz egy ellenőrző kódot, amely 30 másodpercenként frissül. Adja meg a legfrissebb ellenőrző kódot a bejelentkezési képernyőn.<br>A Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594) és [iOS](https://go.microsoft.com/fwlink/?linkid=866594)rendszerhez érhető el.</li></ul> |
    | Hitelesítési telefon | <ul><li>**A telefonhívás** automatikus hanghívást kezdeményez a megadott telefonszámra. Fogadja a hívást, és a hitelesítéshez nyomja meg a telefon billentyűzetén a kettős gomb (#) billentyűt.</li><li>**A szöveges üzenet** egy ellenőrző kódot tartalmazó szöveges üzenetet ér véget. A szövegben lévő kérdést követően válaszoljon a szöveges üzenetre, vagy adja meg a bejelentkezési felületen megadott ellenőrző kódot.</li></ul> |
    | Munkahelyi telefon | Automatikus hanghívást kezdeményez a megadott telefonszámra. Fogadja a hívást, és a hitelesítéshez nyomja meg a telefon billentyűzetén a kettős gomb (#) billentyűt. |

## <a name="next-steps"></a>További lépések

Miután hozzáfért a **További biztonsági ellenőrzés** laphoz, ki kell választania és be kell állítania a kétfaktoros ellenőrzési módszert:

- [A mobileszköz beállítása ellenőrzési módszerként](multi-factor-authentication-setup-phone-number.md)

- [Az irodai telefon beállítása ellenőrzési módszerként](multi-factor-authentication-setup-office-phone.md)

- [A Microsoft Authenticator alkalmazás beállítása ellenőrzési módszerként](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [A kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

- [Alkalmazásjelszavak kezelése](multi-factor-authentication-end-user-app-passwords.md)

- [Bejelentkezés kétfaktoros ellenőrzéssel](multi-factor-authentication-end-user-signin.md)

- [Segítség a kétfaktoros ellenőrzéshez](multi-factor-authentication-end-user-troubleshoot.md) 
