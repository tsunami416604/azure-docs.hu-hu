---
title: Mi az az Azure Active Directory B2C? | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és kezelhet személyazonossági tapasztalatokat, például a bejelentkezést és a profilok kezelését az alkalmazásban Azure Active Directory B2C használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ca636079439f811a887d16b627473e7d73930799
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065708"
---
# <a name="what-is-azure-active-directory-b2c"></a>Mi az az Azure Active Directory B2C?

A Azure Active Directory B2C (Azure AD B2C) a vállalattól a felhasználótól származó Identity Management szolgáltatás. Ezzel a szolgáltatással testreszabhatja és szabályozhatja, hogy a felhasználók hogyan kezelhetik biztonságosan a web-, asztali, mobil-vagy egyoldalas alkalmazásokkal. A Azure AD B2C használatával a felhasználók regisztrálhatnak, bejelentkezhetnek, visszaállíthatják a jelszavakat és szerkeszthetik a profilokat. Azure AD B2C az OpenID Connect és a OAuth 2,0 protokollok formáját valósítja meg. A protokollok megvalósításának fontos kulcsa a biztonsági jogkivonatok és jogcímek, amelyek lehetővé teszik biztonságos hozzáférés biztosítását az erőforrásokhoz.

A *felhasználói út* egy olyan házirendet meghatározó kérelem, amely a felhasználó és az alkalmazás működésének működését szabályozza Azure ad B2C. Két elérési út áll rendelkezésre a felhasználói útvonalak definiálásához Azure AD B2Cban.

Ha Ön az alkalmazás fejlesztője az identitással kapcsolatos szakértelemmel vagy anélkül, akkor a Azure Portal használatával meghatározhatja a közös identitású felhasználói folyamatokat. Ha Ön a személyazonossági szakember, a rendszerintegrátor, a tanácsadó vagy a házon belüli identitás csapata, az OpenID Connect-folyamatok kényelmesek, az identitás-szolgáltatók és a jogcímek hitelesítésének megismerése pedig XML-alapú egyéni házirendeket is választhat.

A felhasználói utazás definiálásának megkezdése előtt létre kell hoznia egy Azure AD B2C bérlőt, és regisztrálnia kell az alkalmazást és az API-t a bérlőben. A feladatok elvégzése után megkezdheti a felhasználói folyamatok vagy egyéni szabályzatok megadását. Lehetősége van továbbá identitás-szolgáltatók hozzáadására vagy módosítására, vagy testre szabhatja azt, ahogyan a felhasználó megtapasztalja az utazást.

## <a name="protocols-and-tokens"></a>Protokollok és tokenek

Azure AD B2C támogatja az [OpenID Connect és a OAuth 2,0 protokollt](active-directory-b2c-reference-protocols.md) a felhasználói útvonalakhoz. Az OpenID Connect Azure AD B2C-implementációjában az alkalmazás az Azure AD B2C felé irányuló hitelesítési kérések küldésével indítja el a felhasználói utat.

Az Azure AD B2Cre irányuló kérelem eredménye biztonsági jogkivonat, például [azonosító jogkivonat vagy hozzáférési jogkivonat](active-directory-b2c-reference-tokens.md). Ez a biztonsági jogkivonat határozza meg a felhasználó identitását. A tokenek Azure ad B2C végpontokból érkeznek, például egy `/token` vagy `/authorize` végpontból. Ezekből a jogkivonatokból olyan jogcímeket érhet el, amelyek segítségével érvényesítheti az identitást, és engedélyezheti a hozzáférést a biztonságos erőforrásokhoz.

## <a name="tenants-and-applications"></a>Bérlők és alkalmazások

Azure AD B2C a *bérlő* képviseli a szervezetét, és a felhasználók címtára. Mindegyik Azure AD B2C-bérlő önálló, és elkülönül a többi Azure AD B2C-bérlőtől. Lehet, hogy már rendelkezik Azure Active Directory Bérlővel, a Azure AD B2C bérlő egy másik bérlő. A bérlő az alkalmazás használatára regisztrált felhasználókkal kapcsolatos információkat tartalmaz. Ilyen adatok a jelszavaik, a profiljuk adatai és az engedélyeik. További információ [: oktatóanyag: Hozzon létre egy](tutorial-create-tenant.md)Azure Active Directory B2C bérlőt.

Mielőtt az alkalmazást a Azure AD B2C használatára konfigurálja, először regisztrálnia kell a bérlőben a Azure Portal használatával. Az alkalmazásregisztrációs művelet során a rendszer összegyűjt bizonyos adatokat, majd értékeket rendel az alkalmazáshoz. Ezek az értékek egy olyan alkalmazás-azonosítót tartalmaznak, amely egyedileg azonosítja az alkalmazást, valamint egy átirányítási URI-t, amely az alkalmazásra való közvetlen válaszadásra szolgál.

Az alkalmazások közötti interakció minden esetben hasonló felső szintű mintát követ:

1. Az alkalmazás arra utasítja a felhasználót, hogy futtasson egy házirendet.
2. A felhasználó a szabályzat definíciója szerint teljesíti a szabályzat feltételeit.
3. Az alkalmazás jogkivonatot kap.
4. Az alkalmazás a token használatával próbál hozzáférni egy erőforráshoz.
5. Az erőforrás-kiszolgáló ellenőrzi a jogkivonatot annak ellenőrzéséhez, hogy a hozzáférés megadható-e.
6. Az alkalmazás rendszeresen frissíti a jogkivonatot.

Webalkalmazás regisztrálásához hajtsa végre az oktatóanyagban [szereplő lépéseket: Regisztráljon egy alkalmazást, hogy engedélyezze a regisztrációt és a bejelentkezést Azure AD B2C](tutorial-register-applications.md)használatával. [Hozzáadhat egy webes API-alkalmazást is a Azure Active Directory B2C bérlőhöz](add-web-application.md) , vagy [hozzáadhat egy natív ügyfélalkalmazás a Azure Active Directory B2C bérlőhöz](add-native-application.md).

## <a name="user-journeys"></a>Felhasználói utazások

A felhasználói úton lévő szabályzat [felhasználói folyamatként](active-directory-b2c-reference-policies.md) vagy [Egyéni szabályzatként](active-directory-b2c-overview-custom.md)definiálható. A leggyakoribb identitási feladatokhoz, például a regisztráláshoz, a bejelentkezéshez és a profil szerkesztéséhez előre definiált felhasználói folyamatok érhetők el a Azure Portal.

A felhasználói útvonalak lehetővé teszik a viselkedés vezérlését a következő beállítások konfigurálásával:

- A felhasználó által az alkalmazásra való regisztrációhoz használt közösségi fiókok
- A felhasználótól gyűjtött adatok, például utónév vagy postai irányítószámok
- Multi-Factor Authentication
- Oldalak megjelenésének és működésének megtekintése
- Az alkalmazásnak visszaadott adatok

Az egyéni házirendek olyan konfigurációs fájlok, amelyek meghatározzák az [identitási élmény keretrendszerének](trustframeworkpolicy.md) viselkedését az Azure ad B2C-bérlőben. Az identitás-kezelési keretrendszer az alapul szolgáló platform, amely több résztvevős megbízhatóságot hoz létre, és elvégzi a felhasználói utazás lépéseit.

Az egyéni szabályzatok némi módosítással számos feladatot végrehajthatnak. Az egyéni szabályzatot egy vagy több XML formátumú fájl alkotja, amelyek hierarchikus sorrendben hivatkoznak egymásra. A közös identitású feladatok lehetővé tételéhez az egyéni házirendek számára elérhetővé válik egy alapszintű [csomag](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .

A Azure AD B2C bérlőn szükség szerint egyéni házirendeket vagy különböző típusú felhasználói folyamatokat használ a rendszer, és az alkalmazások között újra felhasználható. Ez a rugalmasság lehetővé teszi a felhasználói identitások megadását és módosítását a kód minimális vagy semmilyen módosításával. A szabályzatok használata egy speciális lekérdezési paraméternek a HTTP-hitelesítési kérésekhez való hozzáadásával történik. Saját egyéni házirend létrehozásához tekintse meg az [Egyéni szabályzatok](active-directory-b2c-get-started-custom.md)beszerzése Azure Active Directory B2Cban című témakört.

## <a name="identity-providers"></a>Identitásszolgáltatók

Az alkalmazásaiban engedélyezheti, hogy a felhasználók különböző identitás-szolgáltatókkal jelentkezzenek be. Az *identitás-szolgáltató* identitási adatokat hoz létre, tart karban és felügyel, miközben hitelesítési szolgáltatásokat biztosít az alkalmazásoknak. A Azure Portal használatával Azure AD B2C által támogatott identitás-szolgáltatókat adhat hozzá.

Általában csak egy identitás-szolgáltatót használ az alkalmazásban, de lehetősége van további hozzáadására. Ha a Azure AD B2C-bérlőben szeretné konfigurálni az identitás-szolgáltatót, először hozzon létre egy alkalmazást az Identity Provider fejlesztői webhelyén, majd jegyezze fel az alkalmazás azonosítóját vagy ügyfél-azonosítóját, valamint a jelszót vagy az ügyfél titkos kulcsát az identitás-szolgáltatótól. a létrehozott alkalmazás. Az alkalmazás konfigurálásához ezt az azonosítót és jelszót használja.

A következő cikkek ismertetik, hogy miként adhat hozzá néhány Common Identity providert a felhasználói folyamatokhoz:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Microsoft-fiók](active-directory-b2c-setup-msa-app.md)

A következő cikkek a Common Identity Providerek egyéni házirendekhez való hozzáadásának lépéseit ismertetik:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Microsoft-fiók](active-directory-b2c-custom-setup-msa-idp.md)

További információ [: oktatóanyag: Identitás-szolgáltatók hozzáadása az alkalmazásokhoz Azure Active Directory B2Cban](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Oldal testreszabása

A felhasználói úton lévő ügyfeleknek bemutatott HTML-és CSS-tartalmak többsége ellenőrizhető. A lap testreszabása segítségével testre szabhatja az egyéni szabályzatok vagy felhasználói folyamatok megjelenését és működését. Ezzel a testreszabási funkcióval lehet fenntartani az alkalmazás és az Azure AD B2C közötti márkabeli és vizuális egységességet.

Azure AD B2C futtatja a kódot a felhasználó böngészőjében, és egy modern megközelítést használ az eltérő eredetű erőforrás-megosztás (CORS) néven. Először adjon meg egy URL-t egy testreszabott HTML-tartalommal bíró szabályzatban. Azure AD B2C egyesíti a felhasználói felület elemeit az URL-címről betöltött HTML-tartalommal, majd megjeleníti a lapot a felhasználónak.

Paramétereket lekérdezési sztringben küldhet az Azure AD B2C-nek. Ha átadja a paramétereket a HTML-végpontnak, az oldal tartalma dinamikusan változik. Megváltoztathatja például a regisztrációs vagy bejelentkezési oldalon lévő háttérképet a webes vagy mobil alkalmazásából származó paraméter alapján.

A felhasználói folyamatok lapjainak testreszabásáról lásd [: oktatóanyag: Testreszabhatja a felhasználói élmények felületét](tutorial-customize-ui.md)Azure Active Directory B2Cban. Egyéni szabályzat lapjainak testreszabásához tekintse meg az [alkalmazás felhasználói felületének testreszabása Egyéni házirenddel Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md) vagy [a dinamikus tartalommal rendelkező felhasználói felületet a Azure Active Directory B2C egyéni házirendek használatával](active-directory-b2c-ui-customization-custom-dynamic.md)című témakörben.

## <a name="developer-resources"></a>Fejlesztői erőforrások

### <a name="client-applications"></a>Ügyfélalkalmazások

Többek között az [iOS](active-directory-b2c-devquickstarts-ios.md), az [Android](active-directory-b2c-devquickstarts-android.md)és a .net alkalmazásai közül választhat. A Azure AD B2C engedélyezi ezeket a műveleteket, miközben a felhasználói identitásokat egyszerre védi.

Ha Ön egy ASP.net webalkalmazás-fejlesztő, állítsa be az alkalmazást, hogy hitelesítse a fiókokat az [oktatóanyag lépéseinek használatával: Webalkalmazások engedélyezése a fiókok hitelesítéséhez Azure AD B2C](active-directory-b2c-tutorials-web-app.md)használatával.

Ha Ön asztali alkalmazás fejlesztője, állítsa be az alkalmazást a fiókok hitelesítéséhez az [oktatóanyag lépéseinek használatával: Az asztali alkalmazások Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md)használatával történő hitelesítésének engedélyezése a fiókokkal.

Ha a Node. js-t használó egyoldalas alkalmazás-fejlesztő, állítsa be az alkalmazást, hogy hitelesítse a fiókokat [az oktatóanyag lépéseinek használatával: Egy egyoldalas alkalmazás engedélyezése a fiókok hitelesítéséhez Azure AD B2C](active-directory-b2c-tutorials-spa.md)használatával.

### <a name="apis"></a>API-k
Ha az ügyfélnek vagy a webalkalmazásoknak API-kat kell meghívniuk, a Azure AD B2C biztonságos hozzáférést is beállíthat ezekhez az erőforrásokhoz.

Ha Ön egy ASP.net webalkalmazás-fejlesztő, állítsa be az alkalmazást egy védett API meghívásához az [oktatóanyag lépéseinek használatával: Hozzáférés biztosítása egy ASP.NET webes API-hoz a](active-directory-b2c-tutorials-web-api.md)Azure Active Directory B2C használatával.

Ha Ön asztali alkalmazás fejlesztője, állítsa be az alkalmazást egy védett API meghívásához az [oktatóanyag lépéseinek használatával: Hozzáférést biztosíthat egy Node. js webes API-hoz egy asztali alkalmazásból Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app-webapi.md)használatával.

Ha a Node. js-t használó egyoldalas alkalmazás-fejlesztő, állítsa be az alkalmazást, hogy hitelesítse a fiókokat [az oktatóanyag lépéseinek használatával: Hozzáférést biztosíthat egy ASP.NET Core webes API-hoz egy egyoldalas alkalmazásból Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)használatával.

### <a name="javascript"></a>JavaScript

Hozzáadhat saját JavaScript-ügyféloldali kódot az alkalmazásaihoz Azure AD B2Cban. Az alkalmazásban a JavaScript beállításához megadhat egy [lapelrendezést](page-layout.md) , és engedélyezheti [](javascript-samples.md) a JavaScriptet a felhasználói folyamatokban vagy az egyéni házirendekben.

### <a name="user-accounts"></a>Felhasználói fiókok

Számos gyakori bérlői felügyeleti feladatot programozott módon kell végrehajtani. Elsődleges példa a felhasználók kezelése. Előfordulhat, hogy egy meglévő felhasználói tárolót kell áttelepítenie egy Azure AD B2C-bérlőre. Előfordulhat, hogy a felhasználói regisztrációt a saját oldalán szeretné tárolni, és felhasználói fiókokat szeretne létrehozni a Azure AD B2C könyvtárban a jelenetek mögött. Az ilyen típusú feladatok lehetővé teszik felhasználói fiókok létrehozását, olvasását, frissítését és törlését. Ezeket a feladatokat az [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)használatával végezheti el.

## <a name="next-steps"></a>További lépések

Lépjen tovább az oktatóanyagra alkalmazása regisztrációs és bejelentkezési folyamata konfigurálásának megkezdéséhez.

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure Active Directory B2C bérlő létrehozása](tutorial-create-tenant.md)
