---
title: Az Azure Active Directory B2C-vel egyéni szabályzatok |} A Microsoft Docs
description: Ismerje meg az Azure Active Directory B2C-vel egyéni szabályzatok.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0724227da425eb2faeee9ac4ae8449782e62a241
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445980"
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Az Azure Active Directory B2C: Egyéni szabályzatok

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Mik azok az egyéni szabályzatok?

Az egyéni szabályzatok olyan konfigurációs fájlok, amelyekkel meghatározható az Azure AD B2C-bérlő viselkedése. Mivel a **beépített szabályzatok** előre az identitás leggyakoribb feladatokat az Azure AD B2C-portálon egyéni szabályzatok teljes körűen szerkeszthetik az identitás fejlesztője gyakorlatilag korlátlan számú feladatok végrehajtásához. Látogasson el lapunkra meghatározásához, hogy egyéni szabályzatok jobbra, és az identitáskezelési forgatókönyvben.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Beépített és egyéni házirendeket összehasonlítása

| | Beépített szabályzatok | Egyéni szabályzatok |
|-|-------------------|-----------------|
|Felhasználói célcsoport számára | Minden alkalmazás vagy fejlesztők számára az identitás szakértelem nélkül is | Identitáskezelő szakemberek használhatják: rendszerintegrátoroktól, tanácsadókkal és házon belüli identitás csapatok. Nem okoz gondot az OpenIDConnect folyamatokat, és identitás-szolgáltatóktól és Jogcímalapú hitelesítés |
|Konfigurációs mód | Egy felhasználóbarát felhasználói felület az Azure Portalon | XML-fájlok közvetlen módosítása, majd feltölti az Azure Portalra |
|Felhasználói felület testreszabása | Teljes felhasználói felületének testreszabását, beleértve a HTML, CSS és javascript támogatási (szükséges az egyéni tartomány)<br><br>Egyéni karakterláncot tartalmazó többnyelvű támogatás | Azonos |
| Attribútum testreszabása | Standard és egyéni attribútumok | Azonos |
|Jogkivonat és munkamenet-kezelés | Egyéni jogkivonatot, és több munkamenet-beállításokkal | Azonos |
|Identitásszolgáltatók| **Ma**: előre definiált helyi, közösségi szolgáltató<br><br>**Jövőbeli**: OIDC szabványokon alapuló SAML, OAuth | **Ma**: OIDC szabványokon alapuló OAUTH, a SAML<br><br>**Jövőbeli**: WsFed |
|Identitáskezelési tevékenységek (példák) | Regisztrálási vagy bejelentkezési a helyi és számos közösségi fiókok<br><br>Új jelszó önkiszolgáló kérése<br><br>Profil szerkesztése<br><br>A multi-factor Auth-forgatókönyvek<br><br>Biztonsági jogkivonat és munkamenet testreszabása<br><br>Hozzáférési jogkivonat folyamatok | Beépített szabályzatok használatával egyéni Identitásszolgáltatók megegyező feladatok elvégzéséhez, vagy használja az egyéni hatókörök<br><br>Kiépítés felhasználói regisztráció alkalmával egy másik rendszerben<br><br>A saját e-mail-szolgáltató használatával üdvözlő e-mail küldése<br><br>A B2C kívüli felhasználókhoz tartozó tárolóban használata<br><br>Ellenőrizze a felhasználó által megadott információk egy megbízható rendszer API-n keresztül |

## <a name="policy-files"></a>A házirend-fájlok

Egyéni szabályzat jelenik meg egy vagy több XML-formátumú fájlt, amely egy hierarchikus láncban hivatkozzon egymásra. Az XML-elemeket meghatározása: jogcímek sémát és a jogcím-átalakítások, a tartalomdefiníciók, a jogcím-szolgáltatók és technikai profilok és a felhasználói interakciósorozat vezénylési lépésekből, más elemek között.

Három típusú házirend fájlok használatát javasoljuk:

- **Egy alap fájl**, amely tartalmazza a definíciók, és amely az Azure biztosít a teljes minta a legtöbb.  Javasoljuk, hogy a minimálisan szükséges módosításokat hajt végre ezt a fájlt a hibaelhárítás és a hosszú távú karbantartási a házirendek segítségével
- **egy fájl kiterjesztések** az egyedi konfigurációs módosítások, amely tartalmazza a bérlő számára
- **a függő entitás (RP) fájl** , amely az egyetlen feladat való használatra optimalizált fájl, amelyet közvetlenül az alkalmazáshoz vagy szolgáltatáshoz (más néven függő entitás) hív.  A cikk a szabályzatdefiníciók fájlt további részletekért.  Minden egyedi tevékenységhez szükséges a saját RP, és a követelmények márkajelzési függően előfordulhat, hogy lehet "az alkalmazások x használati esetek száma összesen."


Beépített szabályzatokat az Azure AD B2C hajtsa végre a fenti kitaláltak három Fájlmintát, de a fejlesztői csak látja a függő entitás (RP) fájlt, amíg a portál módosítást hajt végre a háttérben a bővítmények fájlt.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Ha tisztában van egyéni szabályzatok használatakor alapfogalmak

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Az Azure felhasználói identitás és hozzáférés (CIAM) felügyeleti szolgáltatás. A szolgáltatás tartalmazza:

1. Az űrlap egy speciális Azure Active Directory-en keresztül a Microsoft Graph, és amely elérhető a felhasználói könyvtár tárolja a felhasználói adatok helyi felhasználói fiókok és a összevont fiókok 
2. A hozzáférést a **identitás-kezelőfelületi keretrendszer** , hogy koordinálja a felhasználók és entitások közötti megbízhatósági kapcsolat, és átadja a jogcímeket közöttük egy identitás és hozzáférés-felügyeleti feladat végrehajtásához 
3. Biztonságijogkivonat-szolgáltatás (STS) kiállító azonosító jogkivonatok, frissítési jogkivonatok, és a hozzáférési jogkivonatok (és egyenértékű SAML helyességi feltételek), és ellenőrzése számukra erőforrások védelmét.

Az Azure AD B2C együttműködő-identitás feladat eléréséhez sorrendben identitás-szolgáltatóktól, a felhasználók, a más rendszerekkel és helyi felhasználói címtárhoz (például a felhasználó bejelentkezési regisztráljon egy új felhasználót, a jelszó alaphelyzetbe állítása). Az alapul szolgáló platform, amely több résztvevős megbízhatósági kapcsolatot hoz létre, és végrehajtja ezeket a lépéseket az identitás-kezelőfelületi keretrendszer és a egy szabályzatot a (más néven felhasználói út vagy a bizalmi keretrendszer házirend) nevezzük. explicit módon meghatározza az actors, a műveleteket, a protokollok és a feladatütemezési lépések végrehajtásához.

### <a name="identity-experience-framework"></a>Identity Experience Framework

Teljes mértékben konfigurálhatók, házirendekkel vezérelt, felhőalapú Azure platformon, a szabványos protokollt (széles körben Jogcímszolgáltatók) entitás közötti megbízhatósági kapcsolat vezénylő például OpenIDConnect, OAuth, SAML, WSFed és néhány nem szabványos eszközök (például REST-formátumok Operációs rendszerek API-based jogcím cseréje). A I2E felhasználóbarát, fehér címkével-megoldások, amelyek támogatják a HTML, CSS és javascript hoz létre.  Napjainkban az identitás-kezelőfelületi keretrendszer rendelkezésre áll, csak az Azure AD B2C-szolgáltatás kontextusában és CIAM kapcsolódó feladatok rangsorolt.

### <a name="built-in-policies"></a>Beépített szabályzatok

Az előre meghatározott konfigurációs fájlokat, az Azure AD B2C-vel gyakran hajtsa végre a legtöbb viselkedését közvetlen használt identitás feladatok (azaz a felhasználói regisztráció, bejelentkezés jelszó-visszaállítás), és kezelheti a megbízható feleknek, amelynek kapcsolat is előre az Azure AD B2C-vel) például Facebook identitásszolgáltató, LinkedIn, a Microsoft Account, Google-fiókok).  A jövőben a beépített szabályzatokat is rendelkezhetnek Identitásszolgáltatók, amelyek általában a vállalati tartomány, például az Azure Active Directory Premium, az Active Directory/ADFS Salesforce azonosító szolgáltató testre szabhatja.


### <a name="custom-policies"></a>Egyéni szabályzatok

Konfigurációs fájlok, amelyek meghatározzák az identitás-kezelőfelületi keretrendszer viselkedését az Azure AD B2C-bérlőben. Egyéni szabályzat egy vagy több XML-fájlként (lásd a fájlok szabályzat-definíciókat) által az identitás-kezelőfelületi keretrendszer, ha egy függő entitás (például egy alkalmazás) által végrehajtott érhető el. Egyéni szabályzatok közvetlenül szerkesztheti egy identitás-fejlesztő, gyakorlatilag korlátlan számú feladatok végrehajtásához. Egyéni házirendek konfigurálása a fejlesztők meg kell határoznia a megbízható kapcsolatok gondos részletes metaadatok végpontokat felvenni, a pontos jogcímek exchange-definíciók és igényei szerint egyes identitásszolgáltató konfigurálása a titkos kulcsok és tanúsítványok.

## <a name="policy-file-definitions-for-identity-experience-framework-trust-frameworks"></a>Identitás-kezelőfelületi keretrendszer megbízhatósági keretrendszerek fájl Szabályzatdefiníciók

### <a name="policy-files"></a>A házirend-fájlok

Egyéni szabályzat jelenik meg egy vagy több XML-formátumú fájlt, amely egy hierarchikus láncban hivatkozzon egymásra. Az XML-elemeket meghatározása: jogcímek sémát és a jogcím-átalakítások, a tartalomdefiníciók, a jogcím-szolgáltatók és technikai profilok és a felhasználói interakciósorozat vezénylési lépésekből, más elemek között.  Három típusú házirend fájlok használatát javasoljuk:

- **Egy alap fájl** tartalmaz definíciókat és amely az Azure biztosít a teljes minta a legtöbb.  Javasoljuk, hogy a minimálisan szükséges módosításokat hajt végre ezt a fájlt a hibaelhárítás és a hosszú távú karbantartási a házirendek segítségével
- **egy fájl kiterjesztések** az egyedi konfigurációs módosítások, amely tartalmazza a bérlő számára
- **a függő entitás (RP) fájl** feladat témájú az egyetlen fájl, amely közvetlenül az alkalmazáshoz vagy szolgáltatáshoz (más néven függő entitás) hív.  A cikk a szabályzatdefiníciók fájlt további részletekért.  Minden egyedi tevékenységhez szükséges a saját RP, és a követelmények márkajelzési függően száma "az alkalmazások x használati esetek száma összesen" lehet.

![A házirend fájltípusok](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Írja be a házirend-fájl | Példák fájl neve | Javasolt használata | Örökli az |
|---------------------|--------------------|-----------------|---------------|
| ALAPJA |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | A core jogcímek séma, a jogcímek átalakítása, Jogcímszolgáltatók és a Microsoft által konfigurált felhasználói utak tartalmaz<br><br>Minimális módosítja ezt a fájlt | None |
| A bővítmény (külső) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | A módosításokat a kiindulási fájl összesítése<br><br>Módosított Jogcímszolgáltatók<br><br>Módosított felhasználói utak<br><br>A saját egyéni sémadefiníciók | Alap fájl |
| Függő Entitásonkénti (RP) | B2C_1A_sign_up_sign_in.xml| Jogkivonat formázása és a munkamenet beállításokat itt módosítása| Extensions(ext) fájl |

### <a name="inheritance-model"></a>Öröklés modell

Amikor egy alkalmazás meghívja a helyreállítási pont Védettként házirendfájl, az az identitás-kezelőfelületi keretrendszer B2C-ben minden elem hozzáadása, talál, akkor bővítmények, végül pedig a helyreállítási pont Védettként házirend fájlból összegyűjtése, a jelenlegi házirend érvényben.  Azonos típusú és nevét az RP-fájl elemeinek fog felülírják a BŐVÍTMÉNYEKET, és bővítmények felülbírálások talál.

**Beépített szabályzatok** Azure AD B2C-ben kövesse a fenti kitaláltak 3 Fájlmintát, de a fejlesztői csak látja a függő entitás (RP) fájlt, amíg a portál módosítást hajt végre a háttérben a bővítmény fájlt.  Az összes Azure AD B2C-t oszt meg egy ALAPSZINTŰ csoportházirend-fájlt, amely az Azure B2C csoport felügyelete alatt áll, és rendszeresen frissül.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md)
