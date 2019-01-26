---
title: Dinamikusan letiltott jelszavak az Azure ad-ben
description: A gyenge jelszavakat a környezetből az Azure ad-ben dinamikusan letiltott jelszavak letiltása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.openlocfilehash: 430a0b3ade96019ae07c032fd9733562c981960e
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075727"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Rossz jelszavak, a szervezet számára

|     |
| --- |
| Az Azure AD jelszóvédelem és a letiltott jelszavak egyéni lista a nyilvános előzetes verziójú funkciók az Azure Active Directory. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Iparági vezetők mondja el, hogy nem összetett, és nem révén egyszerűen/Password123 például a több helyen, ugyanazt a jelszót. Hogyan biztosítható a szervezetek számára, hogy a felhasználók útmutatást követi? Hogyan lehet, győződjön meg arról, felhasználók nem közös jelszavak vagy szereplő legutóbbi adatszivárgásokat ismert jelszavak?

## <a name="global-banned-password-list"></a>Globális letiltott jelszavak listája

A Microsoft mindig azon dolgozik, hogy egy lépéssel a számítógépes bűnözők előtt járjon. Ezért az Azure AD Identity Protection csapata folyamatosan keresse meg a gyakran használt és a feltört jelszavakat. Ezután letiltják ezeket a jelszavakat, hogy mi a globális letiltott jelszavak lista neve túl gyakori beállításkulcsoknak. Bűnözők is hasonló stratégiákat használnak a saját támadások, ezért a Microsoft nem teszi közzé a lista tartalma nyilvánosan. Ezek a sebezhető jelszavak le vannak tiltva, mielőtt azok a valódi fenyegetést Microsoft ügyfeleire vonatkozik. Az aktuális biztonsági erőfeszítések kapcsolatos további információkért tekintse meg a [a Microsoft Security Intelligence Report](https://www.microsoft.com/security/intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Előzetes verzió: Letiltott jelszavak egyéni listája

Egyes szervezetek biztonsági egy lépéssel tovább igénybe a Microsoft által a letiltott jelszavak egyéni lista a globális letiltott jelszavak lista fölött saját testreszabások hozzáadásával lehet. Vállalati felhasználók például Contoso Ezután dönthet blokkolása a márka nevüket, a vállalatra jellemző használati vagy más elemeket tartalmazza.

Az egyéni le van tiltva, jelszó listáját és az ezekben a helyszíni Active Directory-integráció kezeli az Azure portal használatával.

![Az Azure Portalon a hitelesítési módszerek egyéni letiltott jelszavak listájának módosítása](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>A helyszíni hibrid forgatókönyvek

Kizárólag felhőalapú fiókok védelme akkor lehet hasznos, de számos szervezet hibrid környezetekben, beleértve a helyszíni Windows Server Active Directory karbantartása. A Windows Server Active Directory (előzetes verzió) ügynökök a helyszíni a letiltott jelszavak listáit, a meglévő infrastruktúra kiterjesztésére az Azure AD jelszóvédelem telepítése lehetőség. Most, hogy a felhasználók és rendszergazdák, akik módosítja, állítsa be, vagy jelszavakat a helyszíni szükségesek a csak felhőalapú felhasználói is azonos jelszóházirend ahhoz, hogy.

## <a name="how-does-the-banned-password-list-work"></a>Hogyan működik a letiltott jelszavak lista

A letiltott jelszavak lista megegyezik a jelszavakat a listában való visszaváltás a karakterlánc kis- és a egy intelligens egyeztetésével 1 szerkesztési távolságon belül ismert letiltott jelszavakkal összehasonlítása.

Példa: A word-jelszót a szervezet számára le van tiltva
   - A felhasználó megpróbál jelszó beállítása "P@ssword", amely alakítja át a "password", és a jelszó variant, mert le van tiltva.
   - A rendszergazda megkísérli "/ Password123!" új jelszó beállítása amely a "/!"password123 alakítani és mivel a jelszó variant le van tiltva.

Minden alkalommal, amikor a felhasználó visszaállítja vagy megváltoztatja, győződjön meg arról, hogy nem szerepel a tiltott jelszavak lista Ez a folyamat végig az Azure AD-jelszavát. Ez az ellenőrzés hibrid forgatókönyvek használatával az önkiszolgáló jelszó-visszaállítási Jelszókivonat-szinkronizálás és átmenő hitelesítés tartalmazza.

## <a name="license-requirements"></a>Licenckövetelmények

|   | Az Azure AD jelszóvédelem globális letiltott jelszavak listájával | Az Azure AD jelszóvédelem egyéni letiltott jelszavak listájával|
| --- | --- | --- |
| Csak felhőbeli felhasználók | Azure AD Free | Azure AD Basic |
| Szinkronizálja a helyszíni Windows Server Active Directory felhasználók | Az Azure AD Premium P1 vagy P2 | Az Azure AD Premium P1 vagy P2 |

További licencelési információk, beleértve a költségek, találhatók a [Azure Active Directory díjszabását ismertető a hely](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor egy felhasználó megpróbál úgy, hogy az lenne le van tiltva a jelszó alaphelyzetbe állítása, megjelenik a következő hibaüzenetet kapja:

Sajnos a jelszó tartalmaz egy szót, kifejezést vagy mintát, amely lehetővé teszi a jelszó könnyen kitalálható. Próbálkozzon újra egy másik jelszóval.

## <a name="next-steps"></a>További lépések

* [Az egyéni letiltott jelszavak listájának konfigurálása](howto-password-ban-bad.md)
* [Engedélyezze az Azure AD jelszó védelmi ügynököket a helyszíni](howto-password-ban-bad-on-premises-deploy.md)
