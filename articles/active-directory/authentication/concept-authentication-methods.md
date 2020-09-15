---
title: Hitelesítési módszerek és szolgáltatások – Azure Active Directory
description: További információ a Azure Active Directoryban elérhető különböző hitelesítési módszerekről és szolgáltatásokról a bejelentkezési események javításához és biztonságossá tételéhez
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: dcbfd05df84e32423df425f3bdd231a26e4f3bca
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527044"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Milyen hitelesítési és ellenőrzési módszerek érhetők el az Azure Active Directoryban?

A Azure Active Directory (Azure AD) fiókjaihoz tartozó bejelentkezési élmény részeként különböző módokon végezheti el a felhasználók hitelesítését. A Felhasználónév és a jelszó a leggyakoribb módszer a felhasználók számára a hitelesítő adatok megadására. Az Azure AD modern hitelesítési és biztonsági funkcióival az alapszintű jelszót a biztonságosabb hitelesítési módszerekkel kell kiegészíteni vagy helyettesíteni.

![Az Azure AD erősségeit és előnyben részesített hitelesítési módszereinek táblázata](media/concept-authentication-methods/authentication-methods.png)

A jelszó nélküli hitelesítési módszerek, például a Windows Hello, a FIDO2 biztonsági kulcsok és a Microsoft Authenticator alkalmazás biztosítja a legbiztonságosabb bejelentkezési eseményeket.

Az Azure Multi-Factor Authentication további biztonsági feladatokat is felvesz, ha a felhasználó bejelentkezésekor jelszót használ. A felhasználónak további hitelesítési formákat kell megadnia, például egy leküldéses értesítésre való reagáláshoz, egy szoftver-vagy hardver-jogkivonat kódjának megadásához, illetve az SMS-vagy telefonhívások megválaszolásához.

Javasoljuk, hogy egyszerűsítse a felhasználói felületi élményt, és regisztráljon mind az MFA-, mind a SSPR esetében, javasoljuk, hogy [engedélyezze a kombinált biztonsági információk regisztrálását](howto-registration-mfa-sspr-combined.md). A rugalmasság érdekében javasoljuk, hogy a felhasználóknak több hitelesítési módszert kell regisztrálniuk. Ha egy felhasználó nem érhető el a bejelentkezés vagy a SSPR során, dönthet úgy, hogy egy másik metódussal végez hitelesítést. További információ: [rugalmas hozzáférés-vezérlési kezelési stratégia létrehozása az Azure ad-ben](concept-resilient-controls.md).

## <a name="authentication-method-strength-and-security"></a>Hitelesítési módszer erőssége és biztonsága

Ha olyan szolgáltatásokat telepít, mint például az Azure Multi-Factor Authentication a szervezetben, tekintse át az elérhető hitelesítési módszereket. Válassza ki azokat a módszereket, amelyek megfelelnek a követelményeknek, vagy meghaladják a biztonságot, a használhatóságot és a rendelkezésre állást. Ha lehetséges, használja a legmagasabb szintű biztonságú hitelesítési módszereket.

Az alábbi táblázat az elérhető hitelesítési módszerek biztonsági szempontjait ismerteti. A rendelkezésre állás azt jelzi, hogy a felhasználó használhatja-e a hitelesítési módszert, nem a szolgáltatás rendelkezésre állását az Azure AD-ben:

| Hitelesítési módszer       | Biztonság | Használhatóság | Phisable? | A csatorna megjelenő? | Rendelkezésre állás |
|-----------------------------|:--------:|:---------:|:---------:|:-----------------:|:------------:|
| FIDO2 biztonsági kulcs          | Magas     | Magas      | Nem        | Nem                | Magas         |
| A Microsoft Authenticator alkalmazás | Magas     | Magas      | Igen       | Nem <sup>1</sup>   | Magas         |
| Vállalati Windows Hello  | Magas     | Magas      | Nem        | Nem                | Magas         |
| Hardveres eskü tokenek        | Közepes   | Közepes    | Igen       | Nem                | Magas         |
| Szoftveres eskü-tokenek        | Közepes   | Közepes    | Igen       | Nem <sup>2</sup>   | Magas         |
| SMS                         | Közepes   | Magas      | Igen       | Igen               | Közepes       |
| Hang                       | Közepes   | Közepes    | Igen       | Igen               | Közepes       |
| Jelszó                    | Alacsony      | Magas      | Igen       | Igen               | Magas         |

<sup>1</sup> a jelszóval nem rendelkező módban, ha az alkalmazás regisztrálva van egy adott eszközön<br />
<sup>2</sup> ha az alkalmazásnak PIN-kódot kell megadnia a zárolás feloldásához

A biztonsági rések és a támadási vektorok részletes ismertetését lásd: [Channel-Jacks és valós idejű adathalászat](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124).

> [!TIP]
> A rugalmasság és a használhatóság érdekében javasoljuk, hogy használja a Microsoft Authenticator alkalmazást. Ez a hitelesítési módszer a legjobb felhasználói élményt és több módot kínál, például a jelszavakat, az MFA leküldéses értesítéseit és az eskü-kódokat.

## <a name="how-each-authentication-method-works"></a>Az egyes hitelesítési módszerek működése

Egyes hitelesítési módszerek elsődleges tényezőként használhatók egy alkalmazásba vagy eszközre való bejelentkezéskor, például FIDO2 biztonsági kulcs vagy jelszó használatával. Az Azure Multi-Factor Authentication vagy SSPR használata esetén más hitelesítési módszerek csak másodlagos tényezőként érhetők el.

A következő táblázat azt ismerteti, hogy mikor lehet hitelesítési módszert használni a bejelentkezési esemény során:

| Metódus                         | Elsődleges hitelesítés | Másodlagos hitelesítés  |
|--------------------------------|:----------------------:|:-------------------------:|
| FIDO2 biztonsági kulcsok (előzetes verzió)  | Igen                    | MFA                       |
| A Microsoft Authenticator alkalmazás    | Igen (előzetes verzió)          | MFA és SSPR              |
| Vállalati Windows Hello     | Igen                    | MFA                       |
| A hardver-tokenek ESKÜje (előzetes verzió) | No                     | MFA                       |
| Az eskü szoftver jogkivonatai           | No                     | MFA                       |
| SMS                            | Igen (előzetes verzió)          | MFA és SSPR              |
| Hanghívás                     | No                     | MFA és SSPR              |
| Jelszó                       | Igen                    |                           |

Az összes hitelesítési módszer konfigurálható a Azure Portalban, és egyre inkább a [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)használatával.

Ha többet szeretne megtudni az egyes hitelesítési módszerek működéséről, tekintse meg a következő külön fogalmi cikkeket:

* [FIDO2 biztonsági kulcsok (előzetes verzió)](concept-authentication-passwordless.md#fido2-security-keys)
* [A Microsoft Authenticator alkalmazás](concept-authentication-authenticator-app.md)
* [Vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Az eskü szoftver jogkivonatai](concept-authentication-oath-tokens.md#oath-software-tokens)
* [A hardver-tokenek ESKÜje (előzetes verzió)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* SMS- [Bejelentkezés (előzetes verzió)](howto-authentication-sms-signin.md) és [ellenőrzés](concept-authentication-phone-options.md#mobile-phone-verification)
* [Telefonhívás ellenőrzése](concept-authentication-phone-options.md)
* Jelszó

> [!NOTE]
> Az Azure AD-ben a jelszó gyakran az egyik elsődleges hitelesítési módszer. A jelszó-hitelesítési módszer nem tiltható le. Ha az elsődleges hitelesítési tényezőként jelszót használ, növelje a bejelentkezési események biztonságát az Azure Multi-Factor Authentication használatával.

A következő további ellenőrzési módszerek használhatók bizonyos helyzetekben:

* [Alkalmazás jelszavai](howto-mfa-app-passwords.md) – olyan régi alkalmazásokhoz használható, amelyek nem támogatják a modern hitelesítést, és konfigurálhatók felhasználónkénti Azure-multi-Factor Authentication.
* [Biztonsági kérdések](concept-authentication-security-questions.md) – csak a SSPR esetében használatos
* [E-mail-cím](concept-sspr-howitworks.md#authentication-methods) – csak a SSPR esetében használatos

## <a name="next-steps"></a>Következő lépések

Első lépésként tekintse meg az önkiszolgáló [jelszó-visszaállítás (SSPR)][tutorial-sspr] és az [Azure multi-Factor Authentication][tutorial-azure-mfa]című oktatóanyagot.

További információ a SSPR fogalmakról: az [Azure ad önkiszolgáló jelszó-visszaállításának működése][concept-sspr].

További információ az MFA-fogalmakról: [how Azure multi-Factor Authentication Works][concept-mfa].

További információ a hitelesítési módszerek konfigurálásáról a [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)használatával.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
