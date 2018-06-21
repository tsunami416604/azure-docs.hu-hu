---
title: Jelszavak dinamikusan tiltani az Azure AD-ben
description: A környezet dinamikusan tiltani az Azure AD-jelszavakkal működő gyenge jelszavakat bA
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 89cbe386d87c6ccb81df7fabd86b197bb69e41e1
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295609"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>A szervezet rossz jelszavak megszüntetéséhez

|     |
| --- |
| Az Azure AD-jelszó protection és a tiltott egyénijelszó listájának az Azure Active Directory nyilvános előzetes verziójú funkciók. Az előzetes verziójú funkciók kapcsolatos további információkért lásd: [kiegészítő használati feltételek Microsoft Azure előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Iparág közli, hogy nem használja ugyanazt a jelszót több helyen összetett, és nem leegyszerűsítik a Password123 például. Hogyan lehet szervezetek garantálja, hogy a felhasználói útmutató következő? Hogyan is azokat, hogy meg arról, hogy a felhasználók a nem használt közös jelszavak és a jelszót, amely a legutóbbi adatszivárgáshoz szereplő ismert?

## <a name="global-banned-password-list"></a>Globális tiltott jelszó listája

A Microsoft mindig dolgozik egy lépésben előre számítógépes-bűnözők marad. Ezért az Azure AD Identity Protection-csapatnak folyamatosan keressen széles körben használt és feltört jelszavakat. Ezek a jelszavak fontosságúnak ítélt túl gyakori a globális tiltott jelszó lista úgynevezett majd letiltják. Számítógépes-bűnözők is használhatja a hasonló stratégiák a támadások, ezért a Microsoft nem teszi közzé a lista tartalma nyilvánosan. Ezek sebezhető a jelszavak le vannak tiltva, így elkerülhetők a Microsoft ügyfelei valós fenyegetést. Az aktuális biztonsági erőfeszítéseket kapcsolatos további információkért tekintse meg a [Microsoft biztonsági az Eszközintelligencia-jelentés](https://www.microsoft.com/security/intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Kép: Egyéni tiltott jelszó listája

Egyes szervezetek lépéssel biztonsági egy további saját testre szabott elem felett a globális tiltott jelszó listája a Microsoft által az egyéni tiltott jelszó lista hozzáadásával lehet. A vállalati felhasználók például Contoso majd kiválaszthatják a védjegyek, vállalatra egyedileg jellemző használati vagy egyéb elemek változatának blokkolására.

Az egyéni jelszó lista, és engedélyezze a helyszíni Active Directory-integráció kezelése az Azure portál használatával képes tiltott.

![Az Azure-portálon a hitelesítési módszerek egyéni tiltott jelszó listájának módosítása](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>A helyszíni hibrid forgatókönyvek

Csak felhőalapú fiókok védelme akkor hasznos, de számos szervezet karbantartása, beleértve a helyi Windows Server Active Directory hibrid környezetekben. Akkor is telepíthető a Windows Server Active Directory (előzetes verzió) ügynökök helyszíni kiterjesztése a tiltott jelszavak listája, a meglévő infrastruktúra az Azure AD jelszavas védelmet. Most a felhasználók és rendszergazdák számára, akik módosítja, állítsa be, vagy a jelszavak alaphelyzetbe állítása a helyszíni szükségesek a azonos jelszóházirend csak felhőalapú felhasználóként ahhoz, hogy.

## <a name="how-does-the-banned-password-list-work"></a>Hogyan működik a tiltott jelszó lista

A tiltott jelszó lista megegyezik a jelszavak a listában a karakterlánc kis- és összehasonlító a belül egy intelligens egyeztetésével 1 Szerkesztés távolságát ismert tiltott jelszavuk alakítja át.

Példa: A word jelszó blokkolva van, egy szervezet
   - A felhasználó megkísérli a jelszó beállítása "P@ssword", amely alakítja át a "password", és mivel a jelszó variant már le van tiltva.
   - A rendszergazda megkísérli "Password123!" a felhasználók jelszó megadása "password123!", amely konvertálni és mivel a jelszó variant le van tiltva.

Minden alkalommal, amikor a felhasználó visszaállítja vagy megváltoztatja a Azure AD jelszavát, akkor ez a folyamat, ellenőrizze, hogy nincs a tiltott jelszó listában áthaladó. Ez az ellenőrzés hibrid forgatókönyvek használatával az önkiszolgáló jelszó alaphelyzetbe állítása, a Jelszókivonat-szinkronizálás és az átmenő hitelesítés szerepel.

## <a name="license-requirements"></a>Licenckövetelmények

A globális tiltott jelszó lista előnyeit az Azure Active Directory (Azure AD) minden felhasználóra vonatkoznak.

Az egyéni tiltott jelszó lista Azure AD alapvető licencek igényel.

Az Azure AD a Windows Server Active Directory jelszavas védelem Azure AD Premium licenc szükséges. 

További licencelési információt, beleértve a költségek, található meg a [Azure Active Directory-hely árképzési](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor a felhasználó megkísérli a jelszó alaphelyzetbe állítása, amelyet akkor kell tiltani, akkor a következő hibaüzenet jelenik:

Sajnos a jelszó egy szó, kifejezés vagy mintát, amely alapján a jelszó könnyen kitalálható tartalmaz. Próbálkozzon újra egy másik jelszót.

## <a name="next-steps"></a>További lépések

* [Az egyéni tiltott jelszó listájának konfigurálása](howto-password-ban-bad.md)
* [Engedélyezze az Azure AD jelszó védelmi ügynököket a helyszíni](howto-password-ban-bad-on-premises.md)
