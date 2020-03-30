---
title: Az Azure Active Directory hitelesítésének áttekintése
description: Ismerje meg a különböző hitelesítési módszerek és biztonsági funkciók a felhasználói bejelentkezések az Azure Active Directoryval.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4c2fa8488490561e8f11746e8e737718ee9f37
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76261262"
---
# <a name="what-is-azure-active-directory-authentication"></a>Mi az Azure Active Directory-hitelesítés?

Az identitásplatform egyik fő jellemzője a hitelesítő adatok ellenőrzése vagy *hitelesítése,* amikor a felhasználó bejelentkezik egy eszközre, alkalmazásra vagy szolgáltatásra. Az Azure Active Directoryban (Azure AD) a hitelesítés nem csak a felhasználónév és a jelszó ellenőrzését foglalja magában. A biztonság növelése és az ügyfélszolgálati segítség szükségességének csökkentése érdekében az Azure AD-hitelesítés a következő összetevőket tartalmazza:

* Új jelszó önkiszolgáló kérése
* Azure Multi-Factor Authentication
* Hibrid integráció a jelszómódosítások helyszíni környezetbe való visszaírásához
* Hibrid integráció a jelszóvédelmi házirendek helyszíni környezetben való érvényesítéséhez
* Jelszó nélküli hitelesítés

## <a name="improve-the-end-user-experience"></a>A végfelhasználói élmény javítása

Az Azure AD segít megvédeni a felhasználó identitását, és egyszerűsíti a bejelentkezési élményt. Az olyan funkciók, mint az önkiszolgáló jelszó-visszaállítás lehetővé teszik a felhasználók számára, hogy bármilyen eszközről származó webböngészővel frissítsék vagy módosítsák jelszavukat. Ez a funkció különösen akkor hasznos, ha a felhasználó elfelejtette a jelszavát, vagy ha a fiókja zárolva van. Anélkül, hogy megvárná az ügyfélszolgálatot vagy a rendszergazdát, hogy támogatást nyújtson, a felhasználó feloldhatja a blokkolást, és folytathatja a munkát.

Az Azure többtényezős hitelesítés lehetővé teszi a felhasználók számára, hogy válasszanak egy további hitelesítési űrlapot a bejelentkezés során, például egy telefonhívás vagy mobilalkalmazás-értesítés. Ez a képesség csökkenti a másodlagos hitelesítés egyetlen, rögzített formájának, például a hardvertokennek a követelményét. Ha a felhasználó jelenleg nem rendelkezik egy további hitelesítési formával, választhat egy másik módszert, és folytathatja a munkát.

![A bejelentkezési képernyőn használt hitelesítési módszerek](media/concept-authentication-methods/overview-login.png)

A jelszó nélküli hitelesítés szükségtelenné teszi, hogy a felhasználó egyáltalán hozzon létre és emlékezzen egy biztonságos jelszót. Az olyan képességek, mint a Windows Hello for Business vagy a FIDO2 biztonsági kulcsok lehetővé teszik a felhasználók számára, hogy jelszó nélkül jelentkezzenek be egy eszközre vagy alkalmazásba. Ez a képesség csökkentheti a jelszavak kezelésének összetettségét a különböző környezetekben.

## <a name="self-service-password-reset"></a>Új jelszó önkiszolgáló kérése

Az önkiszolgáló jelszó-visszaállítás lehetővé teszi a felhasználók számára a jelszó módosítását vagy alaphelyzetbe állítását, rendszergazda vagy az ügyfélszolgálat bevonása nélkül. Ha egy felhasználó fiókja zárolva van, vagy elfelejti a jelszavát, követheti a figyelmeztetést, hogy feloldja a blokkolást, és visszatérjen a munkához. Ez a képesség csökkenti az ügyfélszolgálati hívásokat és a termelékenység csökkenését, ha a felhasználó nem tud bejelentkezni az eszközére vagy egy alkalmazásba.

Az önkiszolgáló jelszó-visszaállítás a következő esetekben működik:

* **Jelszócsere -** ha a felhasználó tudja a jelszavát, de valami újra akarja változtatni.
* **Jelszó-visszaállítás –** ha a felhasználó nem tud bejelentkezni, például amikor elfelejtette a jelszót, és vissza szeretné állítani a jelszavát.
* **Fiók zárolásának feloldása –** ha a felhasználó nem tud bejelentkezni, mert a fiókja zárolva van, és fel szeretné oldani a fiókját.

Amikor egy felhasználó frissíti vagy alaphelyzetbe állítja a jelszavát az önkiszolgáló jelszó alaphelyzetbe állításával, a jelszó visszaírható egy helyszíni Active Directory-környezetbe is. A jelszó-visszaírás biztosítja, hogy a felhasználó azonnal használhassa a frissített hitelesítő adatait a helyszíni eszközökkel és alkalmazásokkal.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

A többtényezős hitelesítés olyan folyamat, amelynek során a felhasználó a bejelentkezési folyamat során egy további azonosítási formát kér, például kódot kell megadnia a mobiltelefonján, vagy ujjlenyomat-vizsgálatot kell biztosítania.

Ha csak egy felhasználó hitelesítésére használ jelszót, az nem biztonságos támadási vektot hagy. Ha a jelszó gyenge, vagy máshol volt kitéve, akkor valóban a felhasználó jelentkezik be a felhasználónévvel és jelszóval, vagy támadó? Ha a hitelesítés második formájára van szüksége, a biztonság megnő, mivel ez a további tényező nem olyan dolog, amelyet a támadó könnyen beszerezhet vagy lemásolhat.

![A többtényezős hitelesítés különböző formáinak koncepcionális képe](./media/concept-mfa-howitworks/methods.png)

Az Azure többtényezős hitelesítés ekét vagy több hitelesítési módszer megkövetelésével működik:

* Valami, amit tudsz, általában egy jelszót.
* Valami, például egy megbízható eszköz, amely et nem könnyű lemásolni, például telefon vagy hardverkulcs.
* Valami, ami vagy - biometria, mint egy ujjlenyomat vagy arcvizsgálat.

A felhasználók egy lépésben regisztrálhatják magukat az önkiszolgáló jelszó-visszaállításhoz és az Azure többtényezős hitelesítéshez, hogy egyszerűsítsék a beszállásélményét. A rendszergazdák meghatározhatják, hogy milyen másodlagos hitelesítési formák használhatók. Az Azure többtényezős hitelesítése akkor is szükség lehet, ha a felhasználók önkiszolgáló jelszó-visszaállítást hajtanak végre a folyamat további biztonságossá tétele érdekében.

## <a name="password-protection"></a>Jelszavas védelem

Alapértelmezés szerint az Azure AD blokkolja a gyenge jelszavakat, például a Password1.Default, Azure AD blocks weak passwords such as *Password1*. A globális tiltott jelszavak listája automatikusan frissül, és kikényszeríti, amely tartalmazza az ismert gyenge jelszavakat. Ha egy Azure AD-felhasználó megpróbálja beállítani a jelszavát, hogy az egyik ilyen gyenge jelszavakat, értesítést kapnak, hogy válasszon egy biztonságosabb jelszót.

A biztonság növelése érdekében egyéni jelszavas védelmi házirendeket határozhat meg. Ezek a házirendek szűrők használatával blokkolhatják a jelszó bármely változatát, amely például *a Contoso* nevet vagy egy olyan helyet tartalmaz, mint *például London.*

A hibrid biztonság érdekében integrálhatja az Azure AD jelszavas védelmét egy helyszíni Active Directory-környezettel. A helyszíni környezetben telepített összetevő megkapja a globális tiltott jelszólistát és az egyéni jelszavas védelmi szabályzatokat az Azure AD-től, és a tartományvezérlők ezeket használják a jelszómódosítási események feldolgozásához. Ez a hibrid megközelítés gondoskodik arról, hogy függetlenül attól, hogy a felhasználó hogyan és hol módosítja a hitelesítő adatait, erős jelszavak használatát kényszeríti ki.

## <a name="passwordless-authentication"></a>Jelszó nélküli hitelesítés

A végső cél számos környezetben, hogy távolítsa el a jelszavak használatát a bejelentkezési események részeként. Az olyan funkciók, mint az Azure jelszavas védelme vagy az Azure többtényezős hitelesítése, növelik a biztonságot, de a felhasználónév és a jelszó továbbra is gyenge hitelesítési forma marad, amely elérhetővé tehető vagy találgatásos annektálni.

![Biztonság és kényelem a jelszó nélküli hitelesítési folyamattal](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Ha jelszó nélküli módszerrel jelentkezik be, a hitelesítő adatokat olyan módszerek használatával biztosítjuk, mint a biometria a Windows Hello for Business szolgáltatással vagy egy FIDO2 biztonsági kulccsal. Ezeket a hitelesítési módszereket a támadó nem tudja könnyen lemásolni.

Az Azure AD segítségével natív módon hitelesíthető jelszó nélküli módszerekkel, hogy egyszerűsítse a bejelentkezési élményt a felhasználók számára, és csökkenti a támadások kockázatát.

## <a name="next-steps"></a>További lépések

Első lépésekhez tekintse meg az [önkiszolgáló jelszó-visszaállítás][quickstart-sspr] és az [Azure többtényezős hitelesítésoktatóanyag][tutorial-mfa-applications]rövid útmutatója című témakört.

Ha többet szeretne megtudni az önkiszolgáló jelszó-visszaállítási fogalmakról, olvassa el [az Azure AD önkiszolgáló jelszó-visszaállítási működésének módját.][concept-sspr]

A többtényezős hitelesítéssel kapcsolatos fogalmakról az [Azure többtényezős hitelesítésének működése című][concept-mfa]témakörben olvashat bővebben.

<!-- INTERNAL LINKS -->
[quickstart-sspr]: quickstart-sspr.md
[tutorial-mfa-applications]: tutorial-mfa-applications.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
