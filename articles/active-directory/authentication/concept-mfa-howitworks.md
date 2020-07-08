---
title: Az Azure Multi-Factor Authentication áttekintése
description: Ismerje meg, hogyan segít az Azure Multi-Factor Authentication az adatkezeléshez és az alkalmazásokhoz való hozzáférésben, miközben egy egyszerű bejelentkezési folyamatra vonatkozó felhasználói igényeket is kielégít.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c50232abd12c8c0390409bd7bf72833b4f153e02
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80667362"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication működése

A többtényezős hitelesítés olyan folyamat, amelyben a rendszer a bejelentkezési folyamat során megkéri a felhasználót egy további azonosítási módszer megadására, például egy kód megadására a mobiloddal vagy egy ujjlenyomat-vizsgálat megadásához.

Ha csak jelszó használatával hitelesíti a felhasználót, a támadás nem biztonságos vektort hagy. Ha a jelszó gyenge vagy máshol van kitéve, valóban a felhasználó bejelentkezik a felhasználónévvel és a jelszóval, vagy egy támadó? Ha a hitelesítés második formáját igényli, a biztonság megnövekszik, mivel ez a további tényező nem a támadók számára könnyen beszerezhető vagy duplikálható.

![A multi-Factor Authentication különböző formáinak fogalmi képe](./media/concept-mfa-howitworks/methods.png)

Az Azure Multi-Factor Authentication úgy működik, hogy a következő hitelesítési módszerek közül kettő vagy több használatát igényli:

* Amit ismer, általában egy jelszó.
* Valami, például egy nem könnyen duplikált megbízható eszköz, például telefon vagy hardver kulcsa.
* A biometrikus adatok például ujjlenyomatok vagy Arcfelismerés.

A felhasználók az önkiszolgáló jelszó-visszaállításhoz és az Azure Multi-Factor Authenticationhoz is regisztrálhatnak, így egyszerűbbé válik a beléptetési élmény. A rendszergazdák meghatározhatják, hogy milyen típusú másodlagos hitelesítést lehet használni. Az Azure Multi-Factor Authentication akkor is megkövetelhető, ha a felhasználók önkiszolgáló jelszó-visszaállítást végeznek a folyamat további biztonságosabbá tételéhez.

![A bejelentkezési képernyőn használt hitelesítési módszerek](media/concept-authentication-methods/overview-login.png)

Az Azure Multi-Factor Authentication segít az adatkezelésben és az alkalmazásokban való hozzáférésben, miközben a felhasználók egyszerűségét is fenntartja. További biztonságot nyújt a hitelesítés második formáját igényli, és erős hitelesítést tesz lehetővé számos könnyen használható [hitelesítési módszer](concept-authentication-methods.md)segítségével. A rendszergazda által létrehozott konfigurációs döntések alapján előfordulhat, hogy a felhasználók nem tudják feltámadni az MFA-t.

Alkalmazásait vagy szolgáltatásait nem szükséges módosítania az Azure Multi-Factor Authentication használatához. Az ellenőrzési kérések az Azure AD bejelentkezési eseményének részét képezik, amely szükség esetén automatikusan kéri és dolgozza fel az MFA-feladatot.

## <a name="available-verification-methods"></a>Rendelkezésre álló ellenőrzési módszerek

Amikor egy felhasználó bejelentkezik egy alkalmazásba vagy szolgáltatásba, és egy MFA-kérést kap, a további ellenőrzésük egyik regisztrált formája közül választhat. A rendszergazda megkövetelheti ezeknek az Azure Multi-Factor Authentication ellenőrzési módszereknek a regisztrálását, vagy a felhasználó hozzáférhet a saját [profiljához](https://myprofile.microsoft.com) az ellenőrzési módszerek szerkesztéséhez vagy hozzáadásához.

Az Azure Multi-Factor Authentication a következő további ellenőrzési formákat használhatja:

* A Microsoft Authenticator alkalmazás
* A hardver-token ESKÜje
* SMS
* Hanghívás

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication engedélyezése és használata

A felhasználók és csoportok engedélyezhető az Azure Multi-Factor Authentication számára, hogy a bejelentkezési esemény során további ellenőrzésre is rákérdezzenek. A [biztonsági beállítások](../fundamentals/concept-fundamentals-security-defaults.md) minden Azure ad-bérlő számára elérhetők, hogy gyorsan engedélyezzék a Microsoft Authenticator alkalmazás használatát az összes felhasználó számára.

Részletesebb szabályozáshoz a [feltételes hozzáférési](../conditional-access/overview.md) szabályzatok az MFA-t igénylő események és alkalmazások definiálására használhatók. Ezek a házirendek lehetővé teszik a rendszeres bejelentkezési eseményeket, ha a felhasználó a vállalati hálózaton vagy egy regisztrált eszközön van, de a távoli vagy a személyes eszközön további ellenőrzési tényezőket kér.

![Áttekintő diagram a feltételes hozzáférés működéséről a bejelentkezési folyamat biztonságossá tételéhez](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>További lépések

A licenceléssel kapcsolatos további tudnivalókért tekintse meg [Az Azure multi-Factor Authentication funkcióit és licenceit](concept-mfa-licensing.md).

Az MFA működés közbeni megjelenítéséhez engedélyezze az Azure Multi-Factor Authenticationt a következő oktatóanyagban a tesztelési felhasználók számára:

> [!div class="nextstepaction"]
> [Az Azure Multi-Factor Authentication engedélyezése](tutorial-mfa-applications.md)
