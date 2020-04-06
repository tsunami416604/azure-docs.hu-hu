---
title: Az Azure többtényezős hitelesítés – áttekintés
description: Ismerje meg, hogy az Azure többtényezős hitelesítése hogyan segít az adatokhoz és alkalmazásokhoz való hozzáférés biztosításában, miközben kielégíti a felhasználói igényeket egy egyszerű bejelentkezési folyamat hoz.
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
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667362"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication működése

A többtényezős hitelesítés olyan folyamat, amelynek során a felhasználó a bejelentkezési folyamat során egy további azonosítási formát kér, például kódot kell megadnia a mobiltelefonján, vagy ujjlenyomat-vizsgálatot kell biztosítania.

Ha csak egy felhasználó hitelesítésére használ jelszót, az nem biztonságos támadási vektot hagy. Ha a jelszó gyenge, vagy máshol volt kitéve, akkor valóban a felhasználó jelentkezik be a felhasználónévvel és jelszóval, vagy támadó? Ha a hitelesítés második formájára van szüksége, a biztonság megnő, mivel ez a további tényező nem olyan dolog, amelyet a támadó könnyen beszerezhet vagy lemásolhat.

![A többtényezős hitelesítés különböző formáinak koncepcionális képe](./media/concept-mfa-howitworks/methods.png)

Az Azure többtényezős hitelesítés ekét vagy több hitelesítési módszer megkövetelésével működik:

* Valami, amit tudsz, általában egy jelszót.
* Valami, például egy megbízható eszköz, amely et nem könnyű lemásolni, például telefon vagy hardverkulcs.
* Valami, ami vagy - biometria, mint egy ujjlenyomat vagy arcvizsgálat.

A felhasználók egy lépésben regisztrálhatják magukat az önkiszolgáló jelszó-visszaállításhoz és az Azure többtényezős hitelesítéshez, hogy egyszerűsítsék a beszállásélményét. A rendszergazdák meghatározhatják, hogy milyen másodlagos hitelesítési formák használhatók. Az Azure többtényezős hitelesítése akkor is szükség lehet, ha a felhasználók önkiszolgáló jelszó-visszaállítást hajtanak végre a folyamat további biztonságossá tétele érdekében.

![A bejelentkezési képernyőn használt hitelesítési módszerek](media/concept-authentication-methods/overview-login.png)

Az Azure többtényezős hitelesítése segít megőrizni az adatokhoz és alkalmazásokhoz való hozzáférést, miközben megőrzi az egyszerűséget a felhasználók számára. További biztonságot nyújt a hitelesítés második formájának megkövetelésével, és erős hitelesítést biztosít számos könnyen használható [hitelesítési módszerrel.](concept-authentication-methods.md) A rendszergazdák által hozott konfigurációs döntések alapján a felhasználók at az MFA-ban nem lehet megtámadni.

Az alkalmazásoknak vagy szolgáltatásoknak nem kell módosítaniuk az Azure többtényezős hitelesítésének használatát. Az ellenőrző kérések az Azure AD bejelentkezési esemény részét képezik, amely szükség esetén automatikusan kéri és feldolgozza az MFA-kihívást.

## <a name="available-verification-methods"></a>Rendelkezésre álló ellenőrzési módszerek

Amikor egy felhasználó bejelentkezik egy alkalmazásba vagy szolgáltatásba, és mfa-kérdést kap, választhat a regisztrált kiegészítő ellenőrzési formák közül. A rendszergazda kérheti ezen Azure többtényezős hitelesítés imitoperhitelesítési ellenőrzési módszereinek regisztrációját, vagy a felhasználó hozzáférhet a saját [saját profiljához](https://myprofile.microsoft.com) az ellenőrzési módszerek szerkesztéséhez vagy hozzáadásához.

Az Azure többtényezős hitelesítésével a következő további ellenőrzési formák használhatók:

* A Microsoft Authenticator alkalmazás
* OATH hardvertoken
* SMS
* Hanghívás

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Az Azure többtényezős hitelesítésének engedélyezése és használata

Felhasználók és csoportok engedélyezhetők az Azure többtényezős hitelesítés további ellenőrzést a bejelentkezési esemény során. [A biztonsági alapértelmezések](../fundamentals/concept-fundamentals-security-defaults.md) minden Azure AD-bérlő számára elérhetők, így a Microsoft Authenticator alkalmazás használata gyorsan engedélyezható az összes felhasználó számára.

Részletesebb vezérlők esetén [a feltételes hozzáférési](../conditional-access/overview.md) házirendek többéves kori rendszert igénylő események vagy alkalmazások definiálására használhatók. Ezek a házirendek lehetővé tehetik a rendszeres bejelentkezési eseményeket, amikor a felhasználó a vállalati hálózaton vagy egy regisztrált eszközön van, de további ellenőrzési tényezőket kérnek, amikor távoli vagy személyes eszközön vannak.

![A feltételes hozzáférés működésének áttekintése a bejelentkezési folyamat biztonságossá tételéhez](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>További lépések

A licencelésről az [Azure többtényezős hitelesítéshez szükséges funkciók és licencek](concept-mfa-licensing.md)című témakörben olvashat.

Az MFA működés közbeni megtekintéséhez engedélyezze az Azure többtényezős hitelesítést a tesztfelhasználók egy készletéhez a következő oktatóanyagban:

> [!div class="nextstepaction"]
> [Az Azure Multi-Factor Authentication engedélyezése](tutorial-mfa-applications.md)
