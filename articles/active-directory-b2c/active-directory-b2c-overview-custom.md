---
title: 'Az Azure Active Directory B2C: Egyéni házirendek |} Microsoft Docs'
description: 'Témakör: Azure Active Directory B2C egyéni házirendek'
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.openlocfilehash: 22d34ac4128da1d1a9f20619aec2aaccc2425a21
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Az Azure Active Directory B2C: Egyéni házirendek

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Mik azok az egyéni szabályzatok?

Az egyéni szabályzatok olyan konfigurációs fájlok, amelyekkel meghatározható az Azure AD B2C-bérlő viselkedése. Mivel **beépített házirendek** előre meghatározott identitás leggyakoribb feladatokat az Azure AD B2C-portálon, egyéni házirendek teljes mértékben szerkeszthető egy identitás-fejlesztő közel korlátlan számú feladatok elvégzéséhez. Olvassa el a meghatározásához, hogy egyéni házirendek jobbra, és az identitás használatára.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Beépített és egyéni házirendeket összehasonlítása

| | Beépített szabályzatok | Egyéni szabályzatok |
|-|-------------------|-----------------|
|Azon felhasználók megcélzása | Minden alkalmazásfejlesztők vagy identitás szakértői nélkül | Identitás szakemberek: rendszerintegrátoroktól, tanácsadókkal és házon belüli identitás csoportok. OpenIDConnect folyamatokkal részeként, és identitás-szolgáltatóktól és szolgáltatáson alapuló Jogcímalapú hitelesítés |
|A konfigurációs módszert | Az Azure portál egy felhasználóbarát felhasználói felületen | XML-fájlok közvetlen szerkesztésével és majd feltölteni az Azure-portálon |
|Felhasználói felület testreszabása | Teljes felhasználói felület testreszabásával, HTML, CSS, javascript támogatja (egyéni tartomány igényel)<br><br>Egyéni karakterláncok többnyelvű támogatás | Azonos |
| Attribútum testreszabása | Szabványos és az egyéni attribútumok | Azonos |
|Token és munkamenet-kezelés | Egyéni token és több munkamenet-beállításokkal | Azonos |
|Identitásszolgáltatók| **Ma**: előre meghatározott helyi, közösségi szolgáltató<br><br>**Jövőbeli**: szabványalapú OIDC, SAML, OAuth | **Ma**: OIDC szabványalapú OAUTH, a SAML<br><br>**Jövőbeli**: WsFed |
|Identitáskezelési tevékenységek (példák) | Regisztráljon vagy jelentkezzen be helyi és sok közösségi fiókok<br><br>Új jelszó önkiszolgáló kérése<br><br>Profil szerkesztése<br><br>Többtényezős hitelesítéssel kapcsolatos forgatókönyvek<br><br>Jogkivonatok és munkamenetek testreszabása<br><br>Hozzáférési jogkivonat adatfolyamok | Végezze el az egyéni identitás-szolgáltatóktól beépített házirendek megegyező feladatok vagy egyéni hatókörök<br><br>A regisztráció alkalmával egy másik rendszer felhasználó létesítése<br><br>Egy üdvözlő e-mailek küldése a saját e-mail-szolgáltató<br><br>A felhasználókhoz tartozó tárolóban kívül B2C használata<br><br>Ellenőrzi, hogy a felhasználó megadott információkat egy megbízható rendszer API-n keresztül |

## <a name="policy-files"></a>Házirend-fájlok

Egyéni házirendet egy jelenik meg egy vagy több XML-formátumú fájlokat a hierarchikus lánc hivatkozhatnak egymásra. Az XML-elemek meghatározása: jogcímek séma jogcímek átalakítások, a tartalom definíciókat, a jogcím-szolgáltatók és technikai profilok és a Userjourney vezénylési lépésekből egyéb elemek mellett.

Három típusú házirend fájlok használatát javasoljuk:

- **Alap fájl**, amely tartalmazza a definíciók és amelyhez Azure biztosít-e a teljes minta a legtöbb.  Javasoljuk, hogy elvégezte ezt a fájlt a hibaelhárítás és a hosszú távú karbantartási a házirendek segítenek változások minimális száma
- **egy fájl kiterjesztések** a egyedi konfigurációs módosításokat, amely tartalmazza a bérlő számára
- **a függő entitás (RP) fájl** Ez a feladat-arra irányul, hogy az egyetlen fájl, amelyet közvetlenül az alkalmazáshoz vagy szolgáltatáshoz (más néven függő entitás).  Olvassa el a házirend fájl definíciók további információt.  Minden egyedi feladathoz saját RP, és attól függően, hogy a követelmények branding száma "teljes kérelmek teljes száma a használati esetek x" lehet.


Beépített házirendek az Azure AD B2C kövesse a fenti kitaláltak 3-fájl mintát, de a fejlesztői csak azokat a függő entitás (RP) fájlt, amíg a portál módosítást hajt végre a háttérben a bővítmények fájlt.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Ha egyéni házirendekkel tudnia kell alapvető jellemzői

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Azure felhasználói identitások és hozzáférések kezelése (CIAM) szolgáltatás. A szolgáltatás tartalmazza:

1. A speciális Azure Active Directory elérhetők a Microsoft Graph, és amely a helyi és összevont fiókok felhasználói adatokat tároló formájában felhasználói könyvtár 
2. A hozzáférést a **identitás élmény keretrendszer** amely koordinálja a felhasználók és entitások közötti megbízhatósági kapcsolat, és adja át a jogcímeket egy identitás/access felügyeleti tevékenységek végrehajtásához közöttük 
3. Biztonságijogkivonat-szolgáltatás (STS) kiállító azonosító jogkivonatokat, frissítési jogkivonatokat, és a hozzáférési jogkivonatok (vagy egyenértékű SAML helyességi feltételek), és azok az erőforrások védelméhez ellenőrzése.

Az Azure AD B2C együttműködő Identitásszolgáltatók, a felhasználók, a más rendszerekkel, és a helyi felhasználói könyvtárnak sorban egy identitás-feladat eléréséhez (például a felhasználó bejelentkezési regisztrálni egy új felhasználót, a jelszó alaphelyzetbe állítása). Az alapul szolgáló platform, amely több résztvevős megbízhatósági kapcsolatot hoz létre, és végrehajtja ezeket a lépéseket nevezik, és egy házirend (más néven az felhasználói út vagy egy megbízhatósági keretrendszer házirend) identitás élmény keretében explicit módon meghatározza a szereplője, a műveletek, a protokollok és a feladatütemezési lépés befejezéséhez.

### <a name="identity-experience-framework"></a>Identity Experience Framework

Például OpenIDConnect, OAuth, SAML, WSFed és néhány nem szabványos megfelelően (például REST formázza teljes mértékben konfigurálhatók, házirend-vezérelt, felhőalapú Azure platformon vezénylő (nagyjából Jogcímszolgáltatók) entitások közötti megbízhatósági kapcsolat a szabványos protokoll Operációs rendszerek API-based jogcímek cseréjét). A I2E hoz létre, amely támogatja a HTML, CSS és javascript felhasználóbarát, fehér címkével lép.  Ma identitás élmény keretében elérhető, csak az Azure AD B2C szolgáltatás kontextusában és rangsorolt CIAM kapcsolódó feladatok.

### <a name="built-in-policies"></a>Beépített szabályzatok

Előre definiált konfigurációs fájlokat, amelyek közvetlenül az Azure AD B2C a leggyakrabban használt (azaz a felhasználói regisztráció, bejelentkezés, a jelszó alaphelyzetbe állítása) feladatok és a megbízható entitások, amelynek kapcsolatot is az Azure AD B2C (az előre definiált végrehajtásához viselkedése Példa Facebook identitásszolgáltató, LinkedIn, Microsoft Account, Google-fiókot).  A jövőben beépített házirendek is rendelkezhetnek testreszabási Identitásszolgáltatók, amelyek általában a vállalati tartomány, például az Azure Active Directory Premium, az Active Directory vagy az AD FS, a Salesforce azonosító szolgáltató stb.


### <a name="custom-policies"></a>Egyéni szabályzatok

Az Azure AD B2C-bérlő identitását élmény keretrendszer viselkedését meghatározó konfigurációs fájlok. Egyéni házirendet egy vagy több XML-fájlként (lásd a Házirendfájljait definíciókat) egy függő entitás (például egy alkalmazás) általi meghívásakor identitás élmény keretében által végrehajtott érhető el. Egyéni házirendek közvetlenül szerkeszthetők az identitás fejlesztő közel korlátlan számú feladatok elvégzéséhez. Egyéni házirendek konfigurálásához a fejlesztők kell adnia a megbízható kapcsolatok metaadat-végpontokat felvenni gondos részletesen, pontos jogcímek exchange-definíciókat, és konfigurálja a titkos kulcsokat, a kulcsok és a tanúsítványok minden identitásszolgáltatótól igény szerint.

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Identitás élmény keretrendszer Trustframeworks házirendek fájl definíciói

### <a name="policy-files"></a>Házirend-fájlok

Egyéni házirendet egy jelenik meg egy vagy több XML-formátumú fájlok, amelyek hierarchikus lánc hivatkozhatnak egymásra. Az XML-elemek meghatározása: jogcímek séma jogcímek átalakítások, a tartalom definíciók, a jogcím-szolgáltatók és technikai profilok és a felhasználó út vezénylési lépésekből, egyéb elemek mellett.  Három típusú házirend fájlok használatát javasoljuk:

- **A KIINDULÓ fájl** a definíciók és amelyhez Azure biztosít-e a teljes minta a legtöbb tartalmazza.  Javasoljuk, hogy elvégezte ezt a fájlt a hibaelhárítás és a hosszú távú karbantartási a házirendek segítenek változások minimális száma
- **egy fájl kiterjesztések** a egyedi konfigurációs módosításokat, amely tartalmazza a bérlő számára
- **a függő entitás (RP) fájl** feladat-arra irányul, hogy az egyetlen fájl, amelyet közvetlenül az alkalmazáshoz vagy szolgáltatáshoz (más néven függő entitás).  Olvassa el a házirend fájl definíciók további információt.  Minden egyedi feladathoz saját RP, és attól függően, hogy a követelmények branding száma "teljes kérelmek teljes száma a használati esetek x" lehet.

![Házirend fájltípusok](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Házirend fájl típusa | Példák fájl neve | Ajánlott használata | Örököl |
|---------------------|--------------------|-----------------|---------------|
| ALAPJA |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | A core jogcímek séma, a jogcímek átalakítása, a Jogcímszolgáltatók és a felhasználó utak állította be a Microsoft tartalmazza<br><br>Minimális módosítja ezt a fájlt | None |
| A bővítmény (kiterjesztés) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | A KIINDULÓ fájl módosításait összesítése<br><br>Módosított Jogcímszolgáltatók<br><br>Módosított felhasználói utak<br><br>A saját egyéni sémadefiníciók | Alap fájl |
| Függő Entitásonkénti (RP) | B2C_1A_sign_up_sign_in.xml| Token alakzat és módosítása munkamenet Itt| Extensions(ext) fájl |

### <a name="inheritance-model"></a>Öröklési modellt

Amikor egy alkalmazás meghívja a függő Entitás házirendfájlt, a B2C identitás élmény keretében felveszi az elemek, talál, majd a BŐVÍTMÉNYEKET, és végül a függő Entitás házirendfájlból az aktuális házirend érvényes össze.  Elemek azonos típusú és a függő Entitás fájl neve lesz felülírják a bővítmények és bővítmények felülbírálások talál.

**Beépített házirendek** az Azure AD B2C fent kitaláltak 3-fájl mintát kell követnie, de a fejlesztői csak azokat a megbízható függő entitás (RP) fájlt, amíg a portál módosítást hajt végre a háttérben a EXTenstions fájl.  Az összes Azure AD B2C közösen használja az alap házirendfájl gyakran frissíteni és az Azure B2C csoport felügyelete alatt áll.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md)
