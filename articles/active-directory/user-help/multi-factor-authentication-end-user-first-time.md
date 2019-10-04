---
title: A kétfaktoros ellenőrzési módszerek beállítása – Azure Active Directory | Microsoft Docs
description: A kétfaktoros ellenőrzési módszerek beállításának áttekintése a kétfaktoros ellenőrzéshez.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bba343ac73fd0df575ae1e8a83c589a0bc15ac
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616168"
---
# <a name="set-up-your-two-factor-verification-methods-overview"></a>A kétfaktoros ellenőrzési módszerek beállítása – áttekintés

A szervezet bekapcsolta a kétfaktoros ellenőrzést, ami azt jelenti, hogy a munkahelyi vagy iskolai fiókja most már a Felhasználónév, a jelszó, valamint a mobileszköz vagy telefon kombinációját igényli. A szervezet bekapcsolta ezt az extra ellenőrzést, mert az sokkal biztonságosabb, mint a jelszó, és a hitelesítés két formájára támaszkodik. A kétfaktoros ellenőrzés segítségével megakadályozható, hogy rosszindulatú hackerek ne legyenek az Ön számára, mert akkor is, ha a jelszavuk is van, de az is előfordulhat, hogy nem rendelkeznek az eszközzel.

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Amennyiben Ön rendszergazda, az Azure Active Directory- (Azure AD-) környezet beállításával és kezelésével kapcsolatosan további információt az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory) talál.

## <a name="who-decides-if-you-use-this-feature"></a>Ki dönti el, hogy használja-e ezt a funkciót?

A fiók típusától függően a szervezet dönthet úgy, hogy kétfaktoros ellenőrzést kell használnia, vagy Ön is dönthet úgy, hogy eldönti.

- **Munkahelyi vagy iskolai fiókjával.** Ha munkahelyi vagy iskolai fiókot használ (például alain@contoso.com:), akkor akár a szervezet számára is, akár kétfaktoros ellenőrzést kell használnia, az adott ellenőrzési módszerekkel együtt. Mivel a szervezet úgy döntött, hogy ezt a funkciót kell használnia, nincs mód arra, hogy önállóan kikapcsolja.

- **Személyes Microsoft-fiók.** Dönthet úgy, hogy a személyes Microsoft-fiókjaihoz (például alain@outlook.com:) kétfaktoros ellenőrzést állít be. Ha problémák merülnek fel a kétfaktoros ellenőrzéssel és a személyes Microsoft-fiókával kapcsolatban, tekintse meg a kétfaktoros ellenőrzés bekapcsolását a [Microsoft-fiók](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Mivel Ön dönti el, hogy szeretné-e használni ezt a funkciót, bármikor be-és kikapcsolhatja azt.

## <a name="access-the-additional-security-verification-page"></a>A további biztonsági ellenőrzés oldal elérése

Miután a szervezet bekapcsolta és beállítja a kétfaktoros ellenőrzést, a rendszer felszólítja, hogy adjon meg további információkat a fiókja biztonságának megőrzéséhez.

![További információ kérése szükséges](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>A további biztonsági ellenőrzés oldalának elérése

1. Kattintson a **Tovább gombra** a **További információk** megadásához.

    Megjelenik a **további biztonsági ellenőrzés** oldal.

2. A **további biztonsági ellenőrzés** oldalon el kell döntenie, hogy melyik kétfaktoros ellenőrzési módszert használja a rendszer annak ellenőrzésére, hogy ki a munkahelyi vagy iskolai fiókjába való bejelentkezés után. Az alábbiak közül választhat:

    | Kapcsolattartási mód | Leírás |
    | --- | --- |
    | Mobilalkalmazás | <ul><li>**Értesítések fogadása az ellenőrzéshez.** Ez a beállítás egy értesítést küld az okostelefonján vagy a táblaszámítógépen lévő hitelesítő alkalmazásnak. Tekintse meg az értesítést, és ha az megbízható, válassza a **hitelesítés** lehetőséget az alkalmazásban. Előfordulhat, hogy a munkahelye vagy iskolája megköveteli, hogy a hitelesítés előtt PIN-kódot adjon meg.</li><li>**Ellenőrző kód használata.** Ebben a módban a hitelesítő alkalmazás 30 másodpercenként frissülő ellenőrző kódot hoz létre. Adja meg a jelenlegi ellenőrző kódot a bejelentkezési képernyőn.<br>Az Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594) és [iOS](https://go.microsoft.com/fwlink/?linkid=866594)rendszerhez érhető el.</li></ul> |
    | Hitelesítéshez használt telefon | <ul><li>A **telefonhívás** egy automatikus hanghívást helyez el az Ön által megadott telefonszámra. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a telefon billentyűzetén a font (#) gombot.</li><li>A **szöveges üzenet** egy ellenőrző kódot tartalmazó szöveges üzenetet ér véget. A szövegben szereplő Rákérdezés után válaszoljon a szöveges üzenetre, vagy adja meg a bejelentkezési felületen megadott ellenőrző kódot.</li></ul> |
    | Irodai telefon | Elhelyez egy automatikus hanghívást az Ön által megadott telefonszámon. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a telefon billentyűzetén a font (#) gombot. |

## <a name="next-steps"></a>További lépések

A **további biztonsági ellenőrzés** lap elérését követően ki kell választania és be kell állítania a kétfaktoros ellenőrzési módszert:

- [Mobileszköz beállítása ellenőrzési módszerként](multi-factor-authentication-setup-phone-number.md)

- [Az irodai telefon beállítása ellenőrzési módszerként](multi-factor-authentication-setup-office-phone.md)

- [A Microsoft Authenticator alkalmazás beállítása ellenőrzési módszerként](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Kapcsolódó erőforrások

- [A kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

- [Alkalmazásjelszavak kezelése](multi-factor-authentication-end-user-app-passwords.md)

- [Bejelentkezés kétfaktoros ellenőrzés használatával](multi-factor-authentication-end-user-signin.md)

- [Segítség kérése kétfaktoros ellenőrzéssel](multi-factor-authentication-end-user-troubleshoot.md) 
