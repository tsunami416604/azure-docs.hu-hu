---
title: Mi az az Azure Active Directory B2C? | Microsoft Docs
description: Ismerje meg, hozzon létre és kezelhet identitással kapcsolatos műveletet, például a regisztrációs bejelentkezési és profilok kezelése az alkalmazás Azure Active Directory B2C használatával.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9e01ba8ae53dbcca686a9844600a5df416a685ae
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455500"
---
# <a name="what-is-azure-active-directory-b2c"></a>Mi az az Azure Active Directory B2C?

Az Azure Active Directory (Azure AD) B2C egy identitáskezelő szolgáltatás. Ez a szolgáltatás lehetővé teszi testre szabhatja és irányíthatja a felhasználók hogyan biztonságosan használják a webes, asztali, mobil, vagy egyoldalas alkalmazások. Azure AD B2C használatával felhasználók is regisztráció, jelentkezzen be, új jelszót, és profilok szerkesztése. Az Azure AD B2C az OpenID Connect és az OAuth 2.0 protokoll egy formája implementál. Ezeket a protokollokat végrehajtása során a fontos kulcs a biztonsági jogkivonatokat, és azok erőforrásokhoz való biztonságos hozzáférést nyújtanak, amelyekkel jogcímeket.

A *felhasználói interakciósorozat* egy kérelmet, amely meghatározza egy szabályzatot, amely az Azure AD B2C-vel a felhasználó és az alkalmazás együttműködését viselkedését vezérlő van. Két útvonalon lesznek elérhetők az Azure AD B2C felhasználói utak meghatározása. 

Ha Ön egy alkalmazásfejlesztő vagy identitás szakértelem nélkül, választhatja az Azure portal használatával közös identitás felhasználói folyamatok meghatározása. Ha Ön egy identitás professional, rendszerintegrátor, tanácsadóként, vagy egy belső fejlesztésű identitás csapata nem okoz gondot az OpenID Connect folyamatokat, és identitás-szolgáltatóktól és jogcímalapú hitelesítést, választhatja az egyéni házirendek XML-alapú.

Felhasználói út meghatározása előtt kell egy Azure AD B2C bérlő létrehozása és regisztrálása az alkalmazás- és API-t a bérlőben. Miután végrehajtotta ezeket a feladatokat, megkezdheti a felhasználói folyamatok vagy egyéni szabályzatok felhasználói út meghatározása. Emellett, ha szükséges, adja hozzá vagy Identitásszolgáltatók módosítása és testreszabása a felhasználói élmény az utazás módját.

## <a name="protocols-and-tokens"></a>Protokollok és a jogkivonatok

Az Azure AD B2C támogatja a [OpenID Connect és az OAuth 2.0 protokoll](active-directory-b2c-reference-protocols.md) felhasználói utak esetében. Az OpenID Connect Azure AD B2C-implementációjában az alkalmazás az Azure AD B2C felé irányuló hitelesítési kérések küldésével indítja el a felhasználói utat. 

Az Azure AD B2C-vel való irányuló kérelem eredménye egy biztonsági jogkivonatot, például egy [azonosító jogkivonat vagy a hozzáférési jogkivonat](active-directory-b2c-reference-tokens.md). Ez a biztonsági jogkivonat a felhasználói azonosító definiálása. Jogkivonatok érkezett az Azure AD B2C-végpontok, például egy `/token` vagy `/authorize` végpont. Ezek a jogkivonatok használhatók az identitás ellenőrzése és biztonságos erőforrásokhoz való hozzáférés engedélyezése jogcímeket érheti el.

## <a name="tenants-and-applications"></a>Bérlőt és alkalmazást

Az Azure AD B2C egy *bérlői* a munkahelye és a egy könyvtár a felhasználók. Mindegyik Azure AD B2C-bérlő önálló, és elkülönül a többi Azure AD B2C-bérlőtől. Előfordulhat, hogy már rendelkezik Azure Active Directory-bérlővel, az Azure AD B2C-bérlő egy másik bérlőben. Egy bérlő a regisztrált felhasználóktól rendelkezik az alkalmazás használatával kapcsolatos információt tartalmazza. Ilyen adatok a jelszavaik, a profiljuk adatai és az engedélyeik. További információkért lásd: [oktatóanyag: Az Azure Active Directory B2C-bérlő létrehozása](tutorial-create-tenant.md).

Előtt konfigurálja alkalmazását az Azure AD B2C segítségével, akkor először a bérlő az Azure portal használatával regisztrálhatja. Az alkalmazásregisztrációs művelet során a rendszer összegyűjt bizonyos adatokat, majd értékeket rendel az alkalmazáshoz. Ezek az értékek közé tartozik egy Alkalmazásazonosítót, amely egyedileg azonosítja az alkalmazást és a egy átirányítási URI-JÁNAK küldött közvetlen válaszokhoz az alkalmazáshoz használt.

Az alkalmazások közötti interakció minden esetben hasonló felső szintű mintát követ:

1. Az alkalmazás futtatásához egy szabályzatot a felhasználó irányítja.
2. A felhasználó a szabályzat definíciója szerint teljesíti a szabályzat feltételeit.
3. Az alkalmazás fogad a jogkivonatot.
4. Az alkalmazás a jogkivonat használatával megpróbál hozzáférni egy erőforrást.
5. Az erőforrás-kiszolgáló érvényesíti a jogkivonatot, győződjön meg arról, hogy a hozzáférés adható.
6. Az alkalmazás rendszeres időközönként frissíti a tokent.

Webalkalmazás regisztrációja, hajtsa végre a lépéseket a [oktatóanyag: Regisztráció és bejelentkezés az Azure AD B2C használatával engedélyezése egy alkalmazás regisztrálása](tutorial-register-applications.md). Emellett [adjon hozzá egy webes API-alkalmazás az Azure Active Directory B2C-bérlő](add-web-application.md) vagy [hozzáadása az Azure Active Directory B2C-bérlő natív ügyfélalkalmazás](add-native-application.md).

## <a name="user-journeys"></a>Felhasználói utak

A házirendet a felhasználói út adható meg egy [felhasználói folyamat](active-directory-b2c-reference-policies.md) vagy egy [egyéni házirendet](active-directory-b2c-overview-custom.md). Előre definiált felhasználókövetési adatai a leggyakrabban használt identitás feladatok, például a regisztrációs, bejelentkezési és profilszerkesztést, érhető el az Azure Portalon.

Felhasználói utak lehetővé teszi, hogy viselkedés úgy konfigurálja a következő beállításokat:

- A felhasználó regisztrálhat az alkalmazást használó közösségi fiókok
- Adatokat kér a felhasználótól például a Keresztnév vagy irányítószámot
- Multi-Factor Authentication
- Oldalak megjelenését és működését
- Az alkalmazásnak visszaadott adatok

Egyéni szabályzatok olyan konfigurációs fájlok, amelyek viselkedésének megadása a [identitás-kezelőfelületi keretrendszer](trustframeworkpolicy.md) az Azure AD B2C-bérlőben. Az identitás-kezelőfelületi keretrendszer az alapul szolgáló platform, amely több résztvevős megbízhatósági kapcsolatot hoz létre, és a egy felhasználói interakciósorozatban szereplő lépéseket kell megtennie. 

Az egyéni szabályzatok némi módosítással számos feladatot végrehajthatnak. Az egyéni szabályzatot egy vagy több XML formátumú fájl alkotja, amelyek hierarchikus sorrendben hivatkoznak egymásra. A [alapszintű csomagja](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) érhető el a egyéni szabályzatait általános identitás-feladatokat. 

Egyéni házirendek vagy a különböző típusú felhasználói folyamatok igény szerint az Azure AD B2C-bérlő használja, és alkalmazások felhasználhatók. Ezt a rugalmasságot és módosíthatja a felhasználói identitások jellemzőit, minimális konfigurációval vagy a programkód módosítása nélkül teszi lehetővé. A szabályzatok használata egy speciális lekérdezési paraméternek a HTTP-hitelesítési kérésekhez való hozzáadásával történik. A saját egyéni házirend létrehozása: [az Azure Active Directory B2C-vel egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).

## <a name="identity-providers"></a>Identitásszolgáltatók 

Az Ön alkalmazásai érdemes engedélyezése a felhasználók számára, hogy jelentkezzen be a különböző identitásszolgáltatókkal. Egy *identitásszolgáltató* hoz létre, tárolja és kezeli a azonosító adatok, miközben alkalmazások hitelesítési szolgáltatásokat biztosít. Azure AD B2C által támogatott Identitásszolgáltatók is hozzáadhat az Azure portal használatával. 

Csak egy identitásszolgáltató az alkalmazás általában használ, de lehetősége van Továbbiak hozzáadásához. Az identitásszolgáltató konfigurálása az Azure AD B2C-bérlőben, először hozzon létre egy alkalmazást az identity provider fejlesztői helyen, és ezután rögzítheti az alkalmazásazonosító vagy ügyfél-azonosítója és a jelszó vagy titkos az identitásszolgáltatótól az ügyfél a létrehozott alkalmazás. Az alkalmazás konfigurálásához ezt az azonosítót és jelszót használja. 

Az alábbi cikkek ismertetik a lépések hozzáadása a közös identitás-szolgáltatóktól némelyike felhasználókövetési adatai:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Microsoft-fiók](active-directory-b2c-setup-msa-app.md)

Az alábbi cikkek ismertetik a lépések hozzáadása a közös identitás-szolgáltatóktól néhány egyéni szabályzatok:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Microsoft-fiók](active-directory-b2c-custom-setup-msa-idp.md)

További információkért lásd: [oktatóanyag: Identitásszolgáltató hozzáadása az alkalmazásait az Azure Active Directory B2C](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Oldalának testreszabása

Az ügyfelek számára a felhasználói út megjelenő HTML és CSS tartalmának nagy része vezérelhető. Oldalának testreszabása használatával testre szabhatja a megjelenését és működését egyéni szabályzat vagy felhasználói folyamatot. Ezzel a testreszabási funkcióval lehet fenntartani az alkalmazás és az Azure AD B2C közötti márkabeli és vizuális egységességet. 

Az Azure AD B2C kód fut, a felhasználó böngészőjében, és eltérő eredetű erőforrások megosztása (CORS) nevű modern megközelítését használja. Először adjon meg egy URL-t egy testreszabott HTML-tartalommal bíró szabályzatban. Az Azure AD B2C felhasználói felületi elemekre, amely be töltve az URL-címről, és ezután megjeleníti a felhasználónak az oldal HTML-tartalmakat a egyesíti.

Paramétereket lekérdezési sztringben küldhet az Azure AD B2C-nek. Ha átadja a paramétereket a HTML-végpontnak, az oldal tartalma dinamikusan változik. Például a háttérkép, a regisztrálási vagy bejelentkezési oldalon, amely adja át a webes vagy mobilalkalmazás paraméterérték alapján módosítja.

Felhasználói folyamat oldalainak testreszabása, lásd: [oktatóanyag: Testre szabhatja a felület az Azure Active Directory B2C felhasználói élmények](tutorial-customize-ui.md). Egyéni szabályzat oldalainak testreszabása, lásd: [egyéni szabályzat használata az Azure Active Directory B2C az alkalmazás a felhasználói felület testreszabása](active-directory-b2c-ui-customization-custom.md) vagy [konfigurálása a felhasználói felület dinamikus tartalom az Azure-beli egyéni szabályzatok használatával Az Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="developer-resources"></a>Fejlesztői forrásanyagok

### <a name="client-applications"></a>Ügyfélalkalmazások

Választhat, hogy az alkalmazások [iOS](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md), és a .NET, többek között. Az Azure AD B2C lehetővé teszi, hogy ezek a műveletek során a felhasználói identitások védelme egyszerre.

Ha Ön egy ASP.NET webes alkalmazás fejlesztője, az alkalmazás hitelesítéséhez a lépésekkel, a fiókok beállítása [oktatóanyag: A webalkalmazás az Azure AD B2C használatával fiókokkal történő hitelesítésének engedélyezése](active-directory-b2c-tutorials-web-app.md).

Ha Ön egy asztali alkalmazás fejlesztője, az alkalmazás hitelesítéséhez a lépésekkel, a fiókok beállítása [oktatóanyag: Egy asztali alkalmazás az Azure AD B2C használatával fiókokkal történő hitelesítésének engedélyezése](active-directory-b2c-tutorials-desktop-app.md).

Ha Ön egy egyoldalas alkalmazás fejlesztője Node.js használatával, az alkalmazás hitelesítéséhez a lépésekkel, a fiókok beállítása [oktatóanyag: Egy egyoldalas alkalmazás az Azure AD B2C használatával fiókokkal történő hitelesítésének engedélyezése](active-directory-b2c-tutorials-spa.md).

### <a name="apis"></a>API-k
Ha az ügyfél vagy a webes alkalmazások API-k meghívásához szükséges, beállíthat is ezeket az erőforrásokat az Azure AD B2C-vel való biztonságos hozzáférést.

Ha Ön egy ASP.NET webes alkalmazás fejlesztője, az alkalmazás használata a lépések a védett API hívása beállítása [oktatóanyag: Hozzáférést biztosít az ASP.NET webes API-hoz az Azure Active Directory B2C](active-directory-b2c-tutorials-web-api.md).

Ha Ön egy asztali alkalmazás fejlesztője, az alkalmazás használata a lépések a védett API hívása beállítása [oktatóanyag: Egy Node.js webes API-t egy asztali alkalmazásból az Azure Active Directory B2C hozzáférési jogot](active-directory-b2c-tutorials-desktop-app-webapi.md).

Ha Ön egy egyoldalas alkalmazás fejlesztője Node.js használatával, az alkalmazás hitelesítéséhez a lépésekkel, a fiókok beállítása [oktatóanyag: Az ASP.NET Core webes API-t egy egyoldalas alkalmazásból az Azure Active Directory B2C hozzáférési jogot](active-directory-b2c-tutorials-spa-webapi.md).

### <a name="javascript"></a>JavaScript

A saját ügyféloldali JavaScript-kódot adhat hozzá az alkalmazások Azure AD B2C-ben. Az alkalmazás beállítása a JavaScript, megadhat egy [lap szerződés](page-contract.md) , és engedélyezze [JavaScript](javascript-samples.md) a felhasználói folyamatok vagy egyéni szabályzatok.

### <a name="user-accounts"></a>Felhasználói fiókok

Számos gyakori bérlő felügyeleti feladatokat kell végrehajtani ahhoz, programozott módon. Egy elsődleges például, felhasználók kezelése. Szüksége lehet egy meglévő felhasználó-tároló áttelepítése az Azure AD B2C-bérlő. Érdemes a saját oldalon a felhasználói regisztráció üzemeltethet, és a háttérben az Azure AD B2C-címtárban lévő felhasználói fiókokat hozhat létre. Ilyen típusú feladatok van szükség, hogy a létrehozás, Olvasás, frissítés, és törölje a felhasználói fiókokat. Az alábbi feladatokat is végezhet a [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

## <a name="next-steps"></a>További lépések

Lépjen tovább az oktatóanyagra alkalmazása regisztrációs és bejelentkezési folyamata konfigurálásának megkezdéséhez.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Active Directory B2C-bérlő létrehozása](tutorial-create-tenant.md)
