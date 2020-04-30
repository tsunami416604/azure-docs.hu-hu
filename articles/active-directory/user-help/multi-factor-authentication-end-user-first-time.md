---
title: Hol található a további ellenőrzési oldal? – Azure AD
description: A kétfaktoros ellenőrzés további biztonsági ellenőrzés oldalának beszerzése.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77062557"
---
# <a name="what-is-the-additional-verification-page"></a>Hol található a további ellenőrzési oldal?

Kapott egy e-mailt az informatikai részlegről vagy a főnökétől, amely szerint a szervezet biztonsági ellenőrzést adott a fiókjához. Mit jelent ez? Azt jelenti, hogy a cége további lépésekkel ellenőrzi, hogy Ön tényleg az-e, akinek a bejelentkezésnél mondja magát. Ez a további ellenőrzés, más néven kétfaktoros ellenőrzés, a Felhasználónév, a jelszó, valamint egy mobileszköz vagy telefon kombinációján keresztül történik.

A kétfaktoros ellenőrzés biztonságosabb, mint a jelszó, mivel a hitelesítés két formájára támaszkodik: amit ismer, és Önnek is van. Amit tud, az a jelszava. Ami Önnél van, az egy telefon vagy egy olyan eszköz, amit általában magánál tart. A kétfaktoros ellenőrzés segít megakadályozni, hogy a rosszindulatú hackerek az Ön számára legyenek, mert még ha a jelszavuk is van, az esélye, hogy nem rendelkezik az eszközzel.

>[!Important]
>Ez a cikk arra szolgál, hogy a felhasználók a kéttényezős ellenőrzést használják munkahelyi vagy iskolai fiókkal (például: alain@contoso.com). Ha Ön rendszergazda, aki az alkalmazottak vagy más felhasználók kétfaktoros ellenőrzésének bekapcsolásával kapcsolatos információkat keres, tekintse meg a [Azure Active Directory hitelesítési dokumentációját](https://docs.microsoft.com/azure/active-directory/authentication/).

## <a name="who-decides-if-you-use-this-feature"></a>Ki dönti el, hogy használja-e ezt a funkciót?

A fiók típusától függően a szervezet dönthet úgy, hogy kétfaktoros ellenőrzést kell használnia, vagy Ön is dönthet úgy, hogy eldönti.

- **Munkahelyi vagy iskolai fiók.** Ha munkahelyi vagy iskolai fiókot használ (például: alain@contoso.com), akkor akár a szervezet számára is, akár kétfaktoros ellenőrzést kell használnia, az adott ellenőrzési módszerekkel együtt. Mivel a szervezet úgy döntött, hogy ezt a funkciót kell használnia, nincs mód arra, hogy önállóan kikapcsolja.

- **Személyes Microsoft-fiók.** Dönthet úgy, hogy a személyes Microsoft-fiókjaihoz (például: alain@outlook.com) kétfaktoros ellenőrzést állít be. Ha problémák merülnek fel a kétfaktoros ellenőrzéssel és a személyes Microsoft-fiókával kapcsolatban, tekintse [meg a kétfaktoros ellenőrzés bekapcsolását a Microsoft-fiók](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Mivel Ön dönti el, hogy szeretné-e használni ezt a funkciót, bármikor be-és kikapcsolhatja azt.

    >[!Note]
    >Ha problémák merülnek fel a kétfaktoros ellenőrzés és az egyik személyes Microsoft-fiók (például: danielle@outlook.com) esetében, akkor a [kétlépéses ellenőrzés használata a Microsoft-fiók](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="access-the-additional-security-verification-page"></a>A további biztonsági ellenőrzés oldal elérése

Miután a szervezet bekapcsolta és beállítja a kétfaktoros ellenőrzést, a rendszer felszólítja, hogy adjon meg további információkat a fiókja biztonságának megőrzéséhez.

![További információ kérése szükséges](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>A további biztonsági ellenőrzés oldalának elérése

1. Kattintson a **Tovább gombra** a **További információk** megadásához.

    Megjelenik a **további biztonsági ellenőrzés** oldal.

2. A **további biztonsági ellenőrzés** oldalon el kell döntenie, hogy melyik kétfaktoros ellenőrzési módszert használja a rendszer annak ellenőrzésére, hogy ki a munkahelyi vagy iskolai fiókjába való bejelentkezés után. Az alábbiak közül választhat:

    | Kapcsolatfelvételi mód | Leírás |
    | --- | --- |
    | Mobilalkalmazás | <ul><li>**Értesítések fogadása az ellenőrzéshez.** Ez a beállítás egy értesítést küld az okostelefonján vagy a táblaszámítógépen lévő hitelesítő alkalmazásnak. Tekintse meg az értesítést, és ha az megbízható, válassza a **hitelesítés** lehetőséget az alkalmazásban. Előfordulhat, hogy a munkahelye vagy iskolája megköveteli, hogy a hitelesítés előtt PIN-kódot adjon meg.</li><li>**Ellenőrző kód használata.** Ebben a módban a hitelesítő alkalmazás 30 másodpercenként frissülő ellenőrző kódot hoz létre. Adja meg a jelenlegi ellenőrző kódot a bejelentkezési képernyőn.<br>Az Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594) és [iOS](https://go.microsoft.com/fwlink/?linkid=866594)rendszerhez érhető el.</li></ul> |
    | Hitelesítő telefon | <ul><li>A **telefonhívás** egy automatikus hanghívást helyez el az Ön által megadott telefonszámra. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a telefon billentyűzetén a font (#) gombot.</li><li>A **szöveges üzenet** egy ellenőrző kódot tartalmazó szöveges üzenetet ér véget. A szövegben szereplő Rákérdezés után válaszoljon a szöveges üzenetre, vagy adja meg a bejelentkezési felületen megadott ellenőrző kódot.</li></ul> |
    | Munkahelyi telefon | Elhelyez egy automatikus hanghívást az Ön által megadott telefonszámon. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a telefon billentyűzetén a font (#) gombot. |

## <a name="next-steps"></a>További lépések

A **további biztonsági ellenőrzés** lap elérését követően ki kell választania és be kell állítania a kétfaktoros ellenőrzési módszert:

- [Mobileszköz beállítása ellenőrzési módszerként](multi-factor-authentication-setup-phone-number.md)

- [Az irodai telefon beállítása ellenőrzési módszerként](multi-factor-authentication-setup-office-phone.md)

- [A Microsoft Authenticator alkalmazás beállítása ellenőrzési módszerként](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [A kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

- [Alkalmazásjelszavak kezelése](multi-factor-authentication-end-user-app-passwords.md)

- [Bejelentkezés kétfaktoros ellenőrzés használatával](multi-factor-authentication-end-user-signin.md)

- [Segítség kérése kétfaktoros ellenőrzéssel](multi-factor-authentication-end-user-troubleshoot.md) 
