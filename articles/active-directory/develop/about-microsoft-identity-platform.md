---
title: A Microsoft identitásplatformja | Azure
description: Ebben a cikkben megismerheti a Microsoft identitásplatformját, amely az Azure Active Directory identitásszolgáltatás és fejlesztői platform fejlődésének eredménye.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7b3eee08c036862e6ce9f0c590a596f7b1d3fb0
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258691"
---
# <a name="about-microsoft-identity-platform"></a>A Microsoft identitásplatformja

A Microsoft identitásplatformja az Azure Active Directory (Azure AD) identitásszolgáltatás és fejlesztői platform fejlődésének eredménye. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg a Microsoft Graphot, más Microsoft API-kat vagy olyan API-kat, amelyeket fejlesztők készítettek. Ez egy teljes szolgáltatásokat nyújtó platform, amely egy hitelesítési szolgáltatásból, nyílt forráskódú kódtárakból, alkalmazásregisztrációból és -konfigurációból (egy fejlesztői portál és egy alkalmazás API révén), teljes fejlesztői dokumentációból, kódmintákból és egyéb fejlesztői tartalmakból áll. A Microsoft identitásplatformja támogatja a nyílt szabványokat, többek között az OAuth 2.0-t és az OpenID Connectet.

Egészen mostanáig a legtöbb fejlesztő az Azure AD 1.0-s verzióját használta az Azure AD-identitások (munkahelyi és iskolai fiókok) hitelesítéséhez. Ez az Azure AD 1.0-s verziójú végpontjától kért jogkivonatokkal történik, alkalmazásregisztráció és -konfiguráció esetében az Azure AD Authentication Library (ADAL) és az Azure Portal, programozott alkalmazáskonfiguráció esetén pedig az Azure AD Graph API használatával. Az Azure AD 1.0-s verziójú platform egy kiforrott platformajánlat, amely továbbra is megfelelő vállalati alkalmazásokhoz.

A Microsoft-identitásplatform képességeinek bővítéséhez és fejlesztéséhez mostantól a Microsoft-identitások (Azure AD-identitások, Microsoft-fiókok (pl. outlook.com és hotmail.com), valamint közösségi és helyi fiókok az Azure AD B2C révén) szélesebb körét hitelesítheti az Azure AD 2.0-s verziójú végpontként ismert platformmal. Alkalmazásregisztrációhoz és -konfigurációhoz a Microsoft Authentication Libraryt (MSAL) vagy bármely nyílt forráskódú OAuth2.0- vagy OpenID Connect-kódtárat és az Azure Portalt használhatja, programozott alkalmazáskonfiguráláshoz pedig az Azure AD Graph API-t veheti igénybe. A frissített Microsoft-identitásplatform (különös tekintettel az MSAL-kódtárakra és az Azure Portal legújabb alkalmazásregisztrációs felületére) jelentős mértékben fejlődött az elmúlt évben. A kiadás véglegesítése érdekében arra bátorítjuk a fejlesztőket, hogy a legújabb Microsoft-identitásplatformmal fejlesszék és teszteljék az alkalmazásaikat.

A legújabb ADAL-t és a legújabb MSAL-t használó alkalmazások egyszeri bejelentkezést használnak egymással. AZ ADAL-ból MSAL-ra frissített alkalmazások megtartják a felhasználó bejelentkezési állapotát. A fejlesztők kedvük szerint dönthetnek, hogy frissítik-e MSAL-re az alkalmazásaikat, hiszen az ADAL-lel létrehozott alkalmazások továbbra is működnek és kapnak támogatást.

## <a name="microsoft-identity-platform-experience"></a>A Microsoft-identitásplatform környezete

Az alábbi ábra a Microsoft-identitásplatform környezetének áttekintését ábrázolja, beleértve az alkalmazásregisztrációs környezetet, az SDK-kat, a végpontokat és a támogatott identitásokat.

![A Microsoft-identitásplatform ma](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

A Microsoft identitásplatform két végponttal (1.0-s és 2.0-s verzió), és a végpontokat kezelő két kódtárral rendelkezik. Új alkalmazás fejlesztésekor vegye figyelembe a végpontok és a hitelesítési kódtárak előnyeit és aktuális állapotát. Az alábbi szempontokat is vegye figyelembe:

* Támogatott platformok

    * Az [ADAL](active-directory-authentication-libraries.md) a .NET, JavaScript, iOS, Android, Java és Python környezetet támogatja
    * Az [MSAL](reference-v2-libraries.md) a .NET, JavaScript, iOS és Android környezetet támogatja
    * Mindkét végpont támogatja a .NET és a Node.js kiszolgálóként használt közbenső szoftvert az API-k és a bejelentkezés védelme érdekében. 

* Az újítások java része, például a dinamikus és a növekményes hozzájárulás a 2.0-s végponton és az MSAL-kódtárban történik, de továbbra is támogatjuk az 1.0-s ADAL-t.

    Az Azure Portalon korábban statikusan kellett azonosítania az alkalmazás számára szükséges összes hatókört. A 2.0-s verziójú végponttal és a végponthoz társított portálokkal meghatározhatja statikusan a hatóköröket, mint eddig, vagy kérheti őket dinamikusan, amikor épp az alkalmazásnak szüksége van az engedélyre. A dinamikus mód biztosít még egy lehetséges képességet, a növekményes hozzájárulást. A növekményes hozzájárulás lehetővé teszi, hogy egy felhasználó első hitelesítésekor lekérje a szükséges hatókörök alkészletét, majd szükség szerint további hatóköröket kérjen. 
    
    Amikor például egy kameraalkalmazást használ valaki egy mobileszközön, az alkalmazás először a felhasználó hozzájárulását kéri a kamerahasználathoz, és csak a felhasználó hozzájárulása után kap engedélyt a kamerához való hozzáférésre és a fényképezésre.  Ha az alkalmazás készen áll az új fénykép mentésére, előfordulhat, hogy fényképolvasási/-írási engedélyt kér. 

* Lehetséges kompatibilitástörő változások

    Az MSAL alkalmas éles környezetben való használatra. Ugyanolyan éles szintű támogatást biztosítunk az MSAL-hez, mint az aktuális éles kódtárakhoz. Az előzetes verzió ideje alatt módosíthatjuk az API-t, a belső gyorsítótár formátumát és a kódtár egyéb mechanizmusait. Ezeket a változásokat a hibajavításokkal és a szolgáltatásfejlesztésekkel együtt vállalnia kell. Ez hatással lehet az alkalmazására. A gyorsítótár formátumának változása például érintheti a felhasználóit, például előfordulhat, hogy újból be kell jelentkezniük. Lehetséges, hogy egy API-módosítás miatt frissítenie kell a kódot. Amikor általánosan elérhető (GA) kiadást teszünk közzé, hat hónapon belül frissítenie kell a GA-verzióra, mert azután előfordulhat, hogy a kódtár előzetes verziójával írt alkalmazások nem működnek.

## <a name="next-steps"></a>További lépések

Tudjon meg többet az 1.0-s és 2.0-s verzióról.

* [Információk az 1.0-s verzió](v1-overview.md)
* [A 2.0-s verzió bemutatása](v2-overview.md)
