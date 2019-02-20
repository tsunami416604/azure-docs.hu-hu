---
title: Az Azure AD jelszó protection előzetes verziója
description: A gyenge jelszavakat letiltása az Azure AD jelszó protection előzetes verziójának a helyszíni Active Directoryban
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1beae186f6eb276b9aa302d3d51f0ba8688e591
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415748"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Előzetes verzió: Az Azure AD jelszóvédelem a Windows Server Active Directory kényszerítése

|     |
| --- |
| Az Azure AD jelszóvédelem és a letiltott jelszavak egyéni lista a nyilvános előzetes verziójú funkciók az Azure Active Directory. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Az Azure AD jelszóvédelem működteti az Azure Active Directory (Azure AD) használatával javíthatja a szervezeten belüli jelszóházirendek nyilvános előzetes verzióban érhető el az egyik új szolgáltatása. Az Azure AD jelszóvédelem helyi telepítését használja is a globális és egyéni le van tiltva az Azure AD-ben tárolt jelszavak listáit, és végrehajtja az azonos ellenőrzi a helyszíni Azure AD felhőalapú változásának megfelelően.

## <a name="design-principles"></a>Tervezési alapelvek

Az Active Directory az Azure AD jelszóvédelem szem előtt az alábbi elvek célja:

* Tartományvezérlők soha nem szükségesek közvetlenül kommunikáljon az internettel
* Nincs új hálózati portok nyitva vannak azokon a tartományvezérlőkön.
* Az Active Directory-séma módosítása nélkül szükség. A szoftver a meglévő Active Directory-tárolót és a serviceConnectionPoint sémaobjektumok használja.
* Nincs minimális Active Directory-tartomány vagy erdő működési szintjét (DFL\FFL) nem szükséges.
* A szoftver hozzon létre vagy nem szükséges minden olyan fiókok, az általa védett munkaterhelésekkel Active Directory-tartománynak.
* Felhasználói tiszta szöveges jelszavak soha nem hagyja a tartományvezérlő, (jelszó-ellenőrzési műveletek során, vagy bármikor).
* Növekményes üzembe helyezést támogatott azzal a kompromisszummal jár, hogy jelszót házirend csak van érvényben, a domain controller ügynök telepítve van.
* Javasoljuk, hogy a tartományvezérlő ügynököt telepíteni annak biztosítására, széles körben használt jelszó védelmi biztonság kényszerítése minden tartományvezérlő.

## <a name="architectural-diagram"></a>Architekturális diagramja

Fontos rendelkezik az alapul szolgáló tervezési és működési fogalmak megismerése az Azure AD jelszavas védelem az Active Directory a helyszíni környezetben üzembe helyezése előtt. Az alábbi ábrán látható, hogyan működik az Azure AD jelszóvédelem összetevői együtt:

![Hogyan működnek együtt az Azure AD jelszó-védelem összetevői](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

A fenti ábrán az Azure AD jelszóvédelem alkotó három alapvető szoftveres összetevők:

* Az Azure AD-jelszó védelme Proxy szolgáltatás minden olyan tartományhoz csatlakoztatott gép az aktuális Active Directory-erdő futtat. Elsődleges célja, hogy a jelszó házirend letöltési kérelmek tartományvezérlőkről továbbítja az Azure AD és az Azure ad-ből a választ adja vissza a tartományvezérlőre.
* Az Azure AD-jelszó Protection tartományvezérlő ügynök jelszó szűrő dll felhasználói jelszó-érvényesítési kéréseket fogad az operációs rendszer, majd továbbítja azokat az Azure AD-jelszó DC védelmi ügynök szolgáltatás helyben fut a tartományvezérlő.
* Az Azure AD-jelszó DC védelmi ügynök szolgáltatás jelszó érvényesítése kéréseket fogad a tartományvezérlő ügynök jelszó szűrő dll, feldolgozza őket az aktuális (helyben érhető el) jelszóházirend használatával, és visszaadja az eredményt (pass\fail).

## <a name="theory-of-operations"></a>A műveletek elmélet

A fenti ábra és tervezési elvek, így megadott Azure AD jelszóvédelem ténylegesen működése?

Minden egyes Azure AD-jelszó védelme Proxy szolgáltatás meghirdetése maga az erdő tartományvezérlőinek hozzon létre egy serviceConnectionPoint objektumot az Active Directoryban.

Minden egyes Azure AD-jelszó DC védelmi ügynök szolgáltatás Active Directoryban is egy serviceConnectionPoint objektumot hoz létre. Azonban ez szolgál elsősorban a jelentéskészítés és a diagnosztika.

Az Azure AD-jelszó DC védelmi ügynök szolgáltatás felelős a letöltés, az Azure AD új jelszó házirendek kezdeményezése. Az első lépés, hogy keresse meg az Azure AD-jelszó védelme Proxy szolgáltatás az erdő proxy serviceConnectionPoint objektumok lekérdezésével. Után egy elérhető proxy szolgáltatás található, és a proxykiszolgáló, amely ezután elküldi őket az Azure AD, és ezután visszaadja a választ a tartományvezérlő-ügynökszolgáltatás jelszó házirend letöltési kérés küldi a tartományvezérlő agent szolgáltatást. Miután kapott egy új jelszót a házirend az Azure ad-ből, a tartományvezérlő-ügynökszolgáltatás tárolja a szabályzat egy dedikált mappával a gyökérkönyvtárban a tartomány sysvol-megosztás. A tartományvezérlő-ügynökszolgáltatás is figyeli ezt a mappát, abban az esetben, ha újabb házirendek replikálni a többi tartományvezérlő ügynök szolgáltatást a tartományban.

Az Azure AD-jelszó DC védelmi ügynök szolgáltatás mindig kér egy új házirendet a szolgáltatás indítása. Miután a tartományvezérlő-ügynökszolgáltatás elindult, akkor rendszeres időközönként (óránként) Ellenőrizze a jelenlegi helyileg elérhető házirend; kora a jelenlegi szabályzat a régebbi, mint egy óra a tartományvezérlő-ügynökszolgáltatás kérelmezi egy új szabályzatot az Azure ad-ből fent leírtak szerint, ellenkező esetben a tartományvezérlő-ügynök továbbra is az aktuális házirend használatára.

Az Azure AD-jelszó védelmi DC Agent szolgáltatás az Azure AD-jelszó védelmi proxyszolgáltatás használatával (távoli eljáráshívási) RPC TCP-n keresztül kommunikál. A Proxy szolgáltatás (szerint) ezeket a hívásokat az vagy egy statikus vagy dinamikus RPC-portot figyeli.

Az Azure AD-jelszó DC védelmi ügynök soha nem egy hálózati rendelkezésre álló portot figyeli, és a Proxy szolgáltatás soha nem próbál meghívja a tartományvezérlő-ügynök szolgáltatást.

Az Azure AD-jelszó védelme Proxy szolgáltatás az állapot nélküli; soha nem gyorsítótárazza a szabályzatok, vagy más Azure-ból letöltött.

Az Azure AD-jelszó DC védelmi ügynök szolgáltatás csak egyre kiértékelik a legújabb elérhető helyi jelszóházirend használatával a felhasználó jelszavát. A helyi tartományvezérlő nem jelszóházirend érhető el, ha a jelszó fogadja el a rendszer automatikusan, és a egy eseménynapló-üzenet figyelmezteti a felhasználót a rendszergazda lesz naplózva.

Az Azure AD jelszóvédelem nem egy valós idejű alkalmazás házirendmotor. Előfordulhat, a szabályzat konfigurációs jelszómódosítás közötti idő következtében késedelem jön létre az Azure ad-ben és az idő eléri a, és a rendszer kényszeríti az összes tartományvezérlő.

## <a name="foresttenant-binding-for-azure-ad-password-protection"></a>Az Azure AD jelszóvédelem Forest\tenant kötés

Az Azure AD jelszavas védelem az Active Directory-erdő telepítése az Active Directory-erdőben, és minden üzembe helyezett Azure AD-jelszó védelmi alkalmazásproxy-szolgáltatásokat az Azure AD-regisztrációs igényel. Mindkét ilyen regisztrációk (erdő és proxyk) társítva egy adott Azure AD-bérlőhöz, a regisztráció során használt hitelesítő adatok használatával implicit módon azonosított. Visszaállít egy Azure AD jelszóvédelem jelszó házirend letöltődik minden, a rendszer mindig a bérlőjéhez tartozik (azaz, hogy a szabályzat minden esetben lesz a Microsoft globális kombinációját le van tiltva jelszót és a bérlőnkénti egyéni letiltott jelszavak listában). Az erdő más Azure ad között eltérő tartományokban vagy proxy konfigurálása nem támogatott bérlők számára.

## <a name="license-requirements"></a>Licenckövetelmények

A globális letiltott jelszavak lista előnyeit az Azure Active Directory (Azure AD) minden felhasználóra érvényes.

A letiltott jelszavak egyéni lista alapszintű Azure AD-licenc szükséges.

Az Azure AD-jelszó védelmét Windows Server Active Directory prémium szintű Azure AD-licenc szükséges.

További licencelési információk, beleértve a költségek, találhatók a [Azure Active Directory díjszabását ismertető a hely](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Letöltés

A két, amely letölthető az Azure AD jelszó-védelemhez szükséges ügynök telepítőcsomagjai a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="next-steps"></a>További lépések

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
