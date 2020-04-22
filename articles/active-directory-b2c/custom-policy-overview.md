---
title: Az Azure Active Directory B2C egyéni házirendjei | Microsoft dokumentumok
description: További információ az Azure Active Directory B2C egyéni szabályzatairól.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f18f44208b97ab5bc8d9cd9ff01d604c62deb963
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678166"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Egyéni házirendek az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az egyéni szabályzatok olyan konfigurációs fájlok, amelyek meghatározzák az Azure Active Directory B2C (Azure AD B2C) bérlőjének viselkedését. A felhasználói folyamatok előre definiálva vannak az Azure AD B2C portálon a leggyakoribb identitáskezelési feladatokhoz. Az egyéni házirendeket egy identitásfejlesztő teljes mértékben szerkesztheti számos különböző feladat elvégzéséhez.

## <a name="comparing-user-flows-and-custom-policies"></a>Felhasználói folyamatok és egyéni házirendek összehasonlítása

| | Felhasználói folyamatok | Egyéni szabályzatok |
|-|-------------------|-----------------|
| Felhasználók megcélzása | Minden alkalmazásfejlesztők vagy anélkül identitás szakértelemmel. | Identitás-profik, rendszerintegrátorok, tanácsadók és házon belüli identitáscsoportok. Ezek kényelmes OpenID Connect folyamatok és megérteni identitásszolgáltatók és jogcímalapú hitelesítés. |
| Konfigurációs módszer | Az Azure Portal felhasználóbarát felhasználói felülettel (UI) rendelkezik. | Közvetlenül szerkesztheti az XML-fájlokat, majd feltöltheti az Azure Portalra. |
| A felhasználói felület testreszabása | Teljes felhasználói felület testreszabása, beleértve a HTML, CSS és JavaScript.<br><br>Többnyelvű támogatás egyéni karakterláncokkal. | Ugyanaz |
| Attribútum testreszabása | Normál és egyéni attribútumok. | Ugyanaz |
| Jogkivonat- és munkamenet-kezelés | Egyéni jogkivonat és több munkamenet-beállítás. | Ugyanaz |
| Identitásszolgáltatók | Előre definiált helyi vagy közösségi szolgáltató és a legtöbb OIDC-identitásszolgáltató, például az Azure Active Directory-bérlőkösszevonása. | Szabványokon alapuló OIDC, OAUTH és SAML.  A rest API-kkal való integrációval is lehetséges a hitelesítés. |
| Identitáskezelési feladatok | Bejelentkezés vagy bejelentkezés helyi vagy számos közösségi fiókkal.<br><br>Önkiszolgáló jelszó-visszaállítás.<br><br>Profil szerkesztése.<br><br>Többtényezős hitelesítés.<br><br>Testreszabása tokenek és munkamenetek.<br><br>A hozzáférési jogkivonat-folyamatok. | Végezze el ugyanazokat a feladatokat, mint a felhasználói folyamatok egyéni identitásszolgáltatók használatával, vagy használjon egyéni hatóköröket.<br><br>Felhasználói fiók kiépítése egy másik rendszerben a regisztráció időpontjában.<br><br>Küldjön üdvözlő e-mailt saját e-mail szolgáltatójával.<br><br>Az Azure AD B2C-n kívüli felhasználói tároló használata.<br><br>Ellenőrizze a felhasználó által megadott adatokat egy megbízható rendszerrel egy API használatával. |

## <a name="policy-files"></a>Házirendfájlok

A házirendfájlok alábbi három típusát használjuk:

- **Alapfájl** - tartalmazza a legtöbb definíciót. Javasoljuk, hogy a hibaelhárítás és a szabályzatok hosszú távú karbantartása érdekében hajtson végre egy minimális számú módosítást a fájlon.
- **Extensions file** – rendelkezik a bérlő egyedi konfigurációs módosításai.
- **Függő entitás (RP) fájl** – Az egyetlen feladat-központú fájl, amely et közvetlenül az alkalmazás vagy szolgáltatás (más néven függő entitás) hív meg. Minden egyedi feladathoz saját RP szükséges, és a márkajelzési követelményektől függően a szám "az alkalmazások száma x a használati esetek teljes száma" lehet.

Az Azure AD B2C felhasználói folyamatai a fent látható fájlmintát követik, de a fejlesztő csak az RP-fájlt látja, míg az Azure Portal módosítja a bővítmények fájljának hátterét.

Bár háromféle házirendfájl létezik, nem csak három fájlra korlátozódik. Lehet, hogy minden fájltípusból több fájl is található. Ha például nem szeretné módosítani a Bővítmények fájlt, létrehozhat egy Extensions2 fájlt a Bővítmények fájl további bővítéséhez.

## <a name="custom-policy-core-concepts"></a>Egyéni házirend alapfogalmai

Az Azure-beli ügyfélidentitás- és hozzáférés-kezelési (CIAM) szolgáltatás a következőket tartalmazza:

- Olyan felhasználói könyvtár, amely a Microsoft Graph segítségével érhető el, és amely felhasználói adatokat tartalmaz mind a helyi fiókok, mind az összevont fiókok számára.
- Hozzáférés az **identitásélmény-keretrendszerhez,** amely vezényli a felhasználók és az entitások közötti bizalmat, és átadja a jogcímeket közöttük egy identitás- vagy hozzáférés-kezelési feladat végrehajtásához.
- Egy biztonsági jogkivonat-szolgáltatás (STS), amely azonosító jogkivonatokat, frissítési jogkivonatokat és hozzáférési jogkivonatokat (és egyenértékű SAML-állításokat) ad ki, és ellenőrzi azokat az erőforrások védelme érdekében.

Az Azure AD B2C együttműködik az identitásszolgáltatókkal, a felhasználókkal, más rendszerekkel és a helyi felhasználói címtárral egy identitáskezelési feladat elérése érdekében. Például jelentkezzen be egy felhasználót, regisztráljon egy új felhasználót, vagy állítsa alaphelyzetbe a jelszót. Az identitáskezelési keretrendszer és egy házirend (más néven felhasználói út vagy megbízhatósági keretházirend) többrésztvevős megbízhatóságot hoz létre, és kifejezetten meghatározza a szereplőket, a műveleteket, a protokollokat és a végrehajtandó lépések sorrendjét.

Az Identity Experience Framework egy teljes mértékben konfigurálható, házirend-alapú, felhőalapú Azure-platform, amely koordinálja a bizalmat az entitások között a szabványos protokollformátumokban, például OpenID Connect, OAuth, SAML és néhány nem szabványos, például REST API-alapú rendszer-rendszer jogcímek cseréje között. A keretrendszer felhasználóbarát, fehér címkével ellátott, HTML- és CSS-t támogató élményeket hoz létre.

Az egyéni szabályzatok egy vagy több XML formátumú fájlként jelennek meg, egymásra hierarchikus sorrendben hivatkozva. Az XML-elemek többek között a jogcímsémát, a jogcímátalakításokat, a tartalomdefiníciókat, a jogcímszolgáltatókat, a technikai profilokat és a felhasználói út vezénylési lépéseit határozzák meg. Az egyéni házirend egy vagy több XML-fájlként érhető el, amelyeket az identitáskezelési keretrendszer hajt végre, ha egy függő entitás hívja meg őket. Az egyéni házirendeket konfiguráló fejlesztőknek részletesen meg kell határozniuk a megbízható kapcsolatokat, hogy tartalmazzák a metaadat-végpontokat, a pontos jogcímcsere-definíciókat, és szükség szerint konfigurálják a titkos kulcsokat, kulcsokat és tanúsítványokat az egyes identitásszolgáltatók által szükség szerint.

### <a name="inheritance-model"></a>Öröklési modell

Amikor egy alkalmazás meghívja az RP-házirendfájlt, az Azure AD B2C identitáskezelési keretrendszere hozzáadja az összes elemet az alapfájlból, a bővítményfájlból, majd az RP-házirendfájlból az aktuális szabályzat összeállításához.  Az RP-fájlban lévő azonos típusú és nevű elemek felülírják a kiterjesztésekben lévőket, a kiterjesztések pedig felülírják az alapot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni szabályzatok – első lépések](custom-policy-get-started.md)
