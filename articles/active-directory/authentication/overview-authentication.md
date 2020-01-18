---
title: Azure Active Directory hitelesítés áttekintése
description: Ismerje meg a felhasználói bejelentkezések különböző hitelesítési módszereit és biztonsági funkcióit a Azure Active Directory.
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
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261262"
---
# <a name="what-is-azure-active-directory-authentication"></a>Mi az Azure Active Directory hitelesítés?

Az Identity platform egyik fő funkciója a hitelesítő adatok ellenőrzése vagy *hitelesítése*, amikor a felhasználó bejelentkezik egy eszközre, alkalmazásba vagy szolgáltatásba. Azure Active Directory (Azure AD) esetében a hitelesítés több, mint a Felhasználónév és a jelszó ellenőrzése. Az Azure AD-hitelesítés a következő összetevőket tartalmazza, így javíthatja a biztonságot, és csökkentheti az ügyfélszolgálati segítség szükségességét:

* Új jelszó önkiszolgáló kérése
* Azure Multi-Factor Authentication
* Hibrid integráció a jelszó-változtatások helyi környezetbe való visszaírásához
* Hibrid integráció egy helyi környezet jelszavas védelmi házirendjeinek betartatásához
* Jelszó nélküli hitelesítés

## <a name="improve-the-end-user-experience"></a>A végfelhasználói élmény javítása

Az Azure AD segítségével megvédheti a felhasználók identitását, és egyszerűsítheti a bejelentkezési élményt. Az önkiszolgáló jelszó-visszaállításhoz hasonló funkciók lehetővé teszik a felhasználók számára, hogy bármely eszközről webböngészővel frissítsenek vagy módosítsák a jelszavukat. Ez a funkció különösen akkor hasznos, ha a felhasználó elfelejtette a jelszavát, vagy a fiókja zárolva van. Anélkül, hogy az ügyfélszolgálatnak vagy a rendszergazdának támogatást kellene nyújtania, a felhasználók letilthatják magukat, és folytatják a munkát.

Az Azure Multi-Factor Authentication lehetővé teszi a felhasználók számára, hogy a bejelentkezés során további hitelesítési formát válasszák, például telefonhívást vagy Mobile apps-értesítést. Ez a képesség csökkenti a másodlagos hitelesítés (például a hardver jogkivonata) egyetlen, rögzített formáját. Ha a felhasználó jelenleg nem rendelkezik egy további hitelesítéssel, választhat másik módszert, és folytathatja a munkát.

![A bejelentkezési képernyőn használt hitelesítési módszerek](media/concept-authentication-methods/overview-login.png)

A jelszóval nem rendelkező hitelesítéssel nem szükséges, hogy a felhasználó a biztonságos jelszavakat egyáltalán ne hozzon létre és jegyezze fel. A Windows Hello for Business vagy a FIDO2 biztonsági kulcsokkal rendelkező funkciók lehetővé teszik a felhasználók számára, hogy jelszó nélkül jelentkezzenek be egy eszközre vagy alkalmazásba. Ez a képesség csökkentheti a jelszavak különböző környezetekben való kezelésének bonyolultságát.

## <a name="self-service-password-reset"></a>Új jelszó önkiszolgáló kérése

Az önkiszolgáló jelszó-visszaállítás lehetővé teszi a felhasználóknak a jelszavuk módosítását vagy alaphelyzetbe állítását, rendszergazdai vagy ügyfélszolgálati beavatkozás nélkül. Ha a felhasználó fiókja zárolva van, vagy elfelejti a jelszavát, akkor az utasításokat követve letilthatja önmagát, és visszatérhet a munkához. Ez a funkció csökkenti az ügyfélszolgálati hívásokat és a termelékenység elvesztését, ha a felhasználó nem tud bejelentkezni az eszközére vagy alkalmazására.

Az önkiszolgáló jelszó-visszaállítás a következő esetekben működik:

* **Jelszó módosítása –** ha egy felhasználó ismeri a jelszavát, de módosítani szeretné valami újat.
* **Jelszó alaphelyzetbe állítása –** ha a felhasználó nem tud bejelentkezni, például ha elfelejtette a jelszót, és szeretné alaphelyzetbe állítani a jelszavát.
* **Fiók zárolásának feloldása –** ha a felhasználó nem tud bejelentkezni, mert a fiókja zárolva van, és fel szeretné oldani a fiók zárolását.

Amikor egy felhasználó az önkiszolgáló jelszó-visszaállítási szolgáltatással frissíti vagy visszaállítja a jelszavát, a jelszó a helyszíni Active Directory környezetbe is visszaírható. A jelszó visszaírási gondoskodik arról, hogy a felhasználók azonnal használhassák a frissített hitelesítő adataikat a helyszíni eszközök és alkalmazások segítségével.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

A többtényezős hitelesítés olyan folyamat, amelyben a rendszer a bejelentkezési folyamat során megkéri a felhasználót egy további azonosítási módszer megadására, például egy kód megadására a mobiloddal vagy egy ujjlenyomat-vizsgálat megadásához.

Ha csak jelszó használatával hitelesíti a felhasználót, a támadás nem biztonságos vektort hagy. Ha a jelszó gyenge vagy máshol van kitéve, valóban a felhasználó bejelentkezik a felhasználónévvel és a jelszóval, vagy egy támadó? Ha a hitelesítés második formáját igényli, a biztonság megnövekszik, mivel ez a további tényező nem a támadók számára könnyen beszerezhető vagy duplikálható.

![A multi-Factor Authentication különböző formáinak fogalmi képe](./media/concept-mfa-howitworks/methods.png)

Az Azure Multi-Factor Authentication úgy működik, hogy a következő hitelesítési módszerek közül kettő vagy több használatát igényli:

* Amit ismer, általában egy jelszó.
* Valami, például egy nem könnyen duplikált megbízható eszköz, például telefon vagy hardver kulcsa.
* A biometrikus adatok például ujjlenyomatok vagy Arcfelismerés.

A felhasználók az önkiszolgáló jelszó-visszaállításhoz és az Azure Multi-Factor Authenticationhoz is regisztrálhatnak, így egyszerűbbé válik a beléptetési élmény. A rendszergazdák meghatározhatják, hogy milyen típusú másodlagos hitelesítést lehet használni. Az Azure Multi-Factor Authentication akkor is megkövetelhető, ha a felhasználók önkiszolgáló jelszó-visszaállítást végeznek a folyamat további biztonságosabbá tételéhez.

## <a name="password-protection"></a>Jelszavas védelem

Alapértelmezés szerint az Azure AD blokkolja a gyenge jelszavakat, például a *jelszó1*. A rendszer automatikusan frissíti és kikényszeríti a globálisan tiltott jelszavak listáját, amely az ismert gyenge jelszavakat is tartalmazza. Ha egy Azure AD-felhasználó megpróbálja beállítani a jelszavukat a gyenge jelszavak egyikére, a rendszer értesítést küld a biztonságosabb jelszó kiválasztásáról.

A biztonság fokozása érdekében egyéni jelszavas védelmi házirendeket adhat meg. Ezek a házirendek szűrők használatával letilthatják az olyan jelszavak bármely változatát, mint például a *contoso* vagy egy olyan hely, mint például a *London*.

A hibrid biztonság érdekében a helyi Active Directory környezettel integrálhatja az Azure AD jelszavas védelmét. A helyszíni környezetben telepített összetevők megkapják a globálisan tiltott jelszavak listáját és az egyéni jelszavas védelmet az Azure AD-ből, a tartományvezérlők pedig a jelszó-módosítási események feldolgozására használják őket. Ez a hibrid megközelítés gondoskodik arról, hogy a felhasználók hogyan vagy hol változtatják meg a hitelesítő adataikat, így kényszeríti az erős jelszavak használatát.

## <a name="passwordless-authentication"></a>Jelszó nélküli hitelesítés

Számos környezet esetében a cél a jelszavak használatának megszüntetése a bejelentkezési események részeként. A szolgáltatások, például az Azure Password Protection vagy az Azure Multi-Factor Authentication segítenek a biztonság javításában, de a Felhasználónév és a jelszó a hitelesítés gyenge formája marad, amely megtámadható vagy találgatásos támadásokat okozhat.

![Biztonság és kényelem a jelszóval nem rendelkező hitelesítési folyamattal](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Ha jelszó nélküli metódussal jelentkezik be, a hitelesítő adatok a biometria és a Windows Hello for Business használatával, vagy egy FIDO2 biztonsági kulccsal érhetők el. Ezeket a hitelesítési módszereket nem lehet könnyen duplikálni egy támadó.

Az Azure AD lehetővé teszi, hogy natív módon hitelesítse a jelszavak nélküli módszerekkel, így egyszerűbbé téve a felhasználók bejelentkezési élményét, és csökkentheti a támadások kockázatát.

## <a name="next-steps"></a>Következő lépések

Első lépésként tekintse meg az [önkiszolgáló jelszó-visszaállítást][quickstart-sspr] és az [Azure multi-Factor Authentication oktatóanyagát ismertető útmutatót][tutorial-mfa-applications].

Az önkiszolgáló jelszó-visszaállítási fogalmakkal kapcsolatos további tudnivalókért tekintse meg az [Azure ad önkiszolgáló jelszó-visszaállítás működéséről][concept-sspr]szóló témakört.

A többtényezős hitelesítési fogalmakkal kapcsolatos további tudnivalókért tekintse meg az [Azure multi-Factor Authentication működését][concept-mfa]ismertető témakört.

<!-- INTERNAL LINKS -->
[quickstart-sspr]: quickstart-sspr.md
[tutorial-mfa-applications]: tutorial-mfa-applications.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
