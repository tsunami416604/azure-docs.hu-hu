---
title: Az Azure AD jelszó protection előzetes verziója
description: A gyenge jelszavakat a helyszíni Active Directoryban letiltása az Azure AD jelszó protection előzetes verziója
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
ms.openlocfilehash: 2fdf308ff6178dcb51ec73e46d43b853f62e7777
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840953"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Előzetes verzió: Az Azure AD jelszóvédelem a Windows Server Active Directory kényszerítése

|     |
| --- |
| Az Azure Active Directory (Azure AD) jelszavas védelem és a letiltott jelszavak egyéni lista a nyilvános előzetes verziójú funkciók az Azure AD. Előzetes verziók kapcsolatos információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Az Azure AD jelszóvédelem új funkciója, amely növeli a szervezeten belüli jelszóházirendek nyilvános előzetes verzióban érhető el. A helyszíni üzembe helyezés jelszavas védelem is a globális és egyéni le van tiltva – jelszó listák az Azure ad-ben tárolt használ. Az azonos ellenőrzi a helyszíni, az Azure AD a felhőalapú módosításokat végez el.

## <a name="design-principles"></a>Tervezési alapelvek

Az Azure AD jelszóvédelem szem előtt ezeket az alapelveket a célja:

* Tartományvezérlők soha nem kell közvetlenül kommunikáljon az internettel.
* Nincs új hálózati portok nyitva vannak azokon a tartományvezérlőkön.
* Az Active Directory-séma módosítása nélkül szükség. A szoftver használja a meglévő Active Directory **tároló** és **serviceConnectionPoint** adatbázisséma objektumaiban.
* Nincs minimális Active Directory tartomány vagy erdő működési szintje (DFL/FFL) nem szükséges.
* A szoftver nem hozzon létre vagy fiókok az Active Directory-tartományok, amely megvédi a szükséges.
* Felhasználói tiszta szöveges jelszavak ne hagyja a tartományvezérlő, jelszó-ellenőrzési műveletek során vagy a tetszőleges időpontban.
* A növekményes üzembe helyezést. De a jelszóházirend csak kényszerítve van, ahol a Domain Controller ügynök (DC ügynök) telepítve van.
* Azt javasoljuk, hogy az összes olyan tartományvezérlőn annak biztosítására, univerzális jelszó védelmi biztonság kényszerítése az DC-ügynök telepítése.

## <a name="architectural-diagram"></a>Architekturális diagramja

Fontos, az alapul szolgáló tervezési és a függvény fogalmak megértéséhez, az Azure AD jelszavas védelem az Active Directory a helyszíni környezetben üzembe helyezése előtt. Az alábbi ábrán látható, hogyan működik a jelszavas védelem összetevői együtt:

![Hogyan működnek együtt az Azure AD jelszó-védelem összetevői](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Az Azure AD-jelszó védelme Proxy szolgáltatás minden olyan tartományhoz csatlakoztatott gép az aktuális Active Directory-erdő futtat. Az elsődleges célja tartományvezérlők az Azure AD-jelszó házirend letöltési kérelmek továbbításához. Majd visszaadja a válaszok az Azure ad-ből a tartományvezérlőre.
* A jelszószűrő DLL, a tartományvezérlő-ügynök a felhasználói jelszó-érvényesítési kéréseket fogad az operációs rendszer. Azt továbbítja őket a tartományvezérlő ügynök szolgáltatás, amely helyben fut a tartományvezérlő.
* A tartományvezérlő Agent szolgáltatást a jelszavas védelem jelszó-érvényesítési kéréseket fogad a jelszószűrő DLL, a tartományvezérlő-ügynök. Azt az aktuális (helyben érhető el) jelszóházirend használatával dolgozza fel, és visszaadja az eredményt: *átadni* vagy *sikertelen*.

## <a name="how-password-protection-works"></a>Jelszavas védelem működése

Minden egyes Azure AD-jelszó védelme Proxy szolgáltatáspéldány azonosítja magát a tartományvezérlőn, az erdő hozzon létre egy **serviceConnectionPoint** objektumot az Active Directory.

Minden egyes tartományvezérlő ügynökszolgáltatás jelszavas védelmet is létrehoz egy **serviceConnectionPoint** objektumot az Active Directory. Ez az objektum elsősorban a jelentéskészítés és a diagnosztika szolgál.

A tartományvezérlő-ügynökszolgáltatás elindítása az Azure AD új jelszó házirend letöltése felelős. Az első lépés az, hogy keresse meg az Azure AD-jelszó védelme Proxy szolgáltatás az erdő proxy lekérdezésével **serviceConnectionPoint** objektumokat. Egy elérhető proxy szolgáltatás található, ha a tartományvezérlő-ügynök egy jelszó házirend letöltése kérést küld a proxy szolgáltatást. A proxy szolgáltatást ezután elküldi a kérést az Azure ad-hez. A proxy szolgáltatás majd visszaadja a választ a tartományvezérlő Agent szolgáltatást.

A tartományvezérlő Agent szolgáltatást az Azure AD új jelszó szabályzat kap, miután a szolgáltatás a tartomány gyökerében egy dedikált mappában tárolja a szabályzat *sysvol* mappamegosztáshoz. A tartományvezérlő-ügynökszolgáltatás is figyeli ezt a mappát, abban az esetben, ha újabb házirendek replikálni a többi tartományvezérlő ügynökszolgáltatások a tartományban.

A tartományvezérlő-ügynökszolgáltatás mindig egy új házirendet a szolgáltatás indításakor kér. Miután a tartományvezérlő-ügynökszolgáltatás elindult, ellenőrzi az aktuális helyileg elérhető házirend óránként korát. A szabályzat a régebbi, mint egy óra, a tartományvezérlő ügynök kér egy új szabályzatot az Azure AD-ben korábban leírtak szerint. Ha a jelenlegi házirend nincs egy óránál régebbi, a tartományvezérlő-ügynök továbbra is használja az adott házirendnek.

Minden alkalommal, amikor egy Azure AD jelszó védelmi jelszó házirend letöltődik minden, az adott házirendnek csak egy bérlőt. Más szóval jelszóházirendek mindig a Microsoft globális le van tiltva – jelszó és listája, a bérlőnkénti egyéni le van tiltva – jelszó kombinációja.

A tartományvezérlő-ügynök TCP protokollon keresztül kommunikál a proxy-szolgáltatás RPC-n keresztül. A proxy szolgáltatást figyeli a hívásokat a statikus vagy dinamikus RPC port, a konfigurációtól függően.

A tartományvezérlő ügynök soha nem egy hálózati elérhető porton figyel.

A proxy szolgáltatás soha nem hívja a tartományvezérlő Agent szolgáltatást.

A proxy szolgáltatás az állapot nélküli. Soha nem gyorsítótárazza a szabályzatok, vagy más Azure-ból letöltött.

A tartományvezérlő Agent szolgáltatást a legújabb elérhető helyi jelszóházirend mindig használja a jelszó kiértékelése. Ha nincs jelszóházirend nem érhető el a helyi tartományvezérlőn, a jelszó automatikusan elfogadja. Ha ez történik, egy üzenet figyelmezteti a felhasználót a rendszergazda rendszer naplózza.

Az Azure AD jelszóvédelem egy valós idejű alkalmazás házirendmotor nem. Amikor jelszó házirend konfigurációs módosításakor az Azure ad-ben, és amikor, amely eléri módosítása és kényszerítve van az összes olyan tartományvezérlőn közötti késleltetés is lehet.

## <a name="foresttenant-binding-for-password-protection"></a>Erdő vagy a bérlőjéhez kötést a jelszavas védelem

Az Azure AD jelszavas védelem az Active Directory-erdő telepítése az adott erdőben, az Azure AD-regisztrációs igényel. Minden üzembe helyezett proxy szolgáltatás is regisztrálni kell az Azure ad-ben. Egy adott erdő és a proxy regisztrációk tartoznak az Azure AD-bérlőjéhez, amely implicit módon azonosíthatók a regisztráció során használt hitelesítő adatokkal.

Az Active Directory-erdő és a egy erdőn belül minden üzembe helyezett alkalmazásproxy-szolgáltatásokat a ugyanazt bérlőhöz regisztrálva kell lennie. Szeretné, hogy az Active Directory-erdőben vagy bármely alkalmazásproxy-szolgáltatásokat, hogy az erdő regisztrálja a másik Azure AD-bérlők nem támogatott. Az ilyen egy rosszul konfigurált telepítési tünetei a jelszóházirendek letöltése nem.

## <a name="license-requirements"></a>Licenckövetelmények

A globális letiltott jelszavak lista előnyeit az Azure AD minden felhasználóra érvényes.

Az egyéni le van tiltva – jelszó-lista alapszintű Azure AD-licenc szükséges.

Az Azure AD jelszóvédelem a Windows Server Active Directory prémium szintű Azure AD-licenc szükséges.

További licencelési információkért lásd: [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Letöltés

A két szükséges ügynök telepítőcsomagjai az Azure AD jelszóvédelem érhetők el a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>További lépések
[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
