---
title: Feltételes hozzáférés az Azure Active Directory fejlesztői útmutatója
description: Fejlesztői útmutatás és az Azure AD feltételes hozzáférési forgatókönyvek
services: active-directory
keywords: ''
author: CelesteDG
manager: mtillman
ms.author: celested
ms.reviewer: dadobali
ms.date: 09/24/2018
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: 24644faab85305f18fe4b657d3e982a306a41c16
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157073"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Feltételes hozzáférés az Azure Active Directory fejlesztői útmutatója

Az Azure Active Directoryban (Azure AD) feltételes hozzáférés funkciójának kínál, amellyel az alkalmazás biztonságos és védelme a szolgáltatás számos módon. Feltételes hozzáférés lehetővé teszi a fejlesztők és a nagyvállalati vásárlók többféle, beleértve a szolgáltatások védelme érdekében:

* Multi-Factor Authentication
* Így csak az Intune-ban regisztrált eszközök adott szolgáltatásokhoz való hozzáférést
* Korlátozhatja a felhasználó tartózkodási helye és IP-címtartományok

A feltételes hozzáférés az összes funkciójáról további információkért lásd: [feltételes hozzáférés az Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Alkalmazások létrehozása az Azure ad fejlesztőknek Ez a cikk bemutatja, hogyan használhatja a feltételes hozzáférés, és is megismerkedhet a hatását, amelyek nem rendelkeznek erőforrásokhoz való hozzáférés felett, előfordulhat, hogy alkalmazza a feltételes hozzáférési szabályzatok. A cikk ismerteti a feltételes hozzáférés a alapú meghatalmazásos folyamat következményei webalkalmazások, Microsoft Graph elérése és API-k hívása is.

Ismerete [egyetlen](quickstart-v1-integrate-apps-with-azure-ad.md) és [több-bérlős](howto-convert-app-to-be-multi-tenant.md) alkalmazásokat és [gyakori hitelesítési minták](authentication-scenarios.md) feltételezi.

## <a name="how-does-conditional-access-impact-an-app"></a>Milyen hatással van a feltételes hozzáférés egy alkalmazás?

### <a name="app-types-impacted"></a>Érintett alkalmazástípusok

Leggyakoribb esetekben feltételes hozzáférés nem változtatja meg az alkalmazás viselkedését, vagy a fejlesztőtől származó módosításokat igényel. Csak bizonyos esetekben a alkalmazás közvetett módon vagy beavatkozás nélkül tokent kér egy szolgáltatáshoz, amikor egy alkalmazásban szükséges kódmódosításokat "kihívások" feltételes hozzáférés kezelésére. Elképzelhető, hogy egyszerűen egy interaktív bejelentkezési kérelem végrehajtása.

Pontosabban a következő esetekben szükséges "kihívások" feltételes hozzáférés kezeléséhez szükséges kódot:

* Alkalmazások a Microsoft Graph elérése
* Alkalmazások a alapú meghatalmazásos folyamat végrehajtása
* Az alkalmazások több szolgáltatásokhoz és erőforrásokhoz való hozzáférés
* Egyoldalas alkalmazások ADAL.js használatával
* A Web Apps erőforrás hívása

Feltételes hozzáférési szabályzatok alkalmazhatók az alkalmazást, de is alkalmazható egy webes API-t az alkalmazás hozzáfér. Feltételes hozzáférési szabályzat konfigurálásával kapcsolatos további tudnivalókért lásd: [a rövid útmutató: Többtényezős hitelesítés az Azure Active Directory feltételes hozzáférés az adott alkalmazások](../conditional-access/app-based-mfa.md).

A forgatókönyvtől függően a nagyvállalati ügyfelek fiókazonosítóját vonatkoznak, és bármikor eltávolíthatja a feltételes hozzáférési szabályzatokat. Ahhoz, hogy az alkalmazás működését, amikor egy új házirendet alkalmaznak a "kérdés" kezelési megvalósításához szüksége. Az alábbi példák bemutatják, challenge kezelését.

### <a name="conditional-access-examples"></a>Feltételes hozzáférési példa

Egyes forgatókönyvekben feltételes hozzáférés kezelésére, míg mások működik, kódmódosítás szükséges. Az alábbiakban néhány forgatókönyv feltételes hozzáférés használata ehhez a multi-factor authentication szolgáltatás, amely a különbség a betekintést biztosít.

* Egy egybérlős iOS-alkalmazást hoz létre, és a alkalmazni a feltételes hozzáférési szabályzat. Az alkalmazás a felhasználó bejelentkezik, és nem igényelhet hozzáférést egy API-t. A felhasználó bejelentkezésekor automatikusan megnyílik, a szabályzatot, és a felhasználónak a multi-factor authentication (MFA) végrehajtása.
* Egyéb szolgáltatások közötti Exchange, eléréséhez a Microsoft Graph használó több-bérlős webalkalmazást hoz létre. Ki az alkalmazás fogad a nagyvállalati ügyfelek fiókazonosítóját Exchange-szabályzat beállítása. Ha a webes alkalmazás tokent kér az MS Graph, az alkalmazás a rendszer nem lekéri a szabályzatának. A végfelhasználónak érvényes jogkivonatok van bejelentkezve. Az alkalmazás megpróbálja használni ezt a tokent a Microsoft Graph szemben az Exchange-adatokhoz való hozzáféréshez, a "kérdés" jogcímek visszatér a webalkalmazás a ```WWW-Authenticate``` fejléc. Az alkalmazás használhatja a ```claims``` egy új kérés esetén és a végfelhasználónak meg kell adnia a feltételeket ahhoz, hogy.
* A középső réteg szolgáltatást használ egy alárendelt API eléréséhez natív alkalmazást fejleszt. A vállalat az alkalmazás használatával a nagyvállalati ügyfelek fiókazonosítóját alkalmazza a szabályzatot az alsóbb rétegbeli API-hoz. Ha egy végfelhasználó bejelentkezik, a natív alkalmazás a középső réteg hozzáférést kér, és elküldi. A középső réteg hajt végre a-meghatalmazásos folyamat az alsóbb rétegbeli API-hoz való hozzáférés kéréséhez. A "kérdés" jogcímek ezen a ponton a középső réteg számára. A középső réteg küld a kihívás az natív alkalmazás, amelynek meg kell felelnie azoknak a feltételes hozzáférési szabályzat.

### <a name="complying-with-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat betartása

Számos különböző alkalmazás-topológiákat a feltételes hozzáférési szabályzat lesz kiértékelve, amikor a munkamenet. Feltételes hozzáférési szabályzat működtethető alkalmazások és szolgáltatások közül, mint a a pont, amelyen meghívása a megvalósítani kívánt forgatókönyv erősen függ.

Amikor az alkalmazás megpróbálja elérni egy szolgáltatást, a feltételes hozzáférési szabályzattal együtt, akkor feltételes hozzáférési kihívást merülhetnek fel. Ez kihívást a kódolt a `claims` paraméter, amely tartalmaz egy adott válaszként az Azure ad-ből vagy a Microsoft Graph. Íme egy példa a kérdés paraméter:

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

A fejlesztők mutatták igénybe és fűzze hozzá egy új kérelmet az Azure AD-be. Ebben az állapotban passing kéri a végfelhasználó számára, amelyek szükségesek ahhoz a feltételes hozzáférési szabályzat bármely művelet elvégzésére. A következő esetekben a hiba, és hogyan lehet kigyűjteni a paraméter tulajdonságairól magyarázata.

## <a name="scenarios"></a>Forgatókönyvek

### <a name="prerequisites"></a>Előfeltételek

Az Azure AD feltételes hozzáférés lehetővé teszi a [prémium szintű Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis#choose-an-edition). További információ a licencelésről a követelmények a [licenccel nem rendelkező használati jelentés](../active-directory-conditional-access-unlicensed-usage-report.md). A fejlesztők csatlakozhat a [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), amely tartalmazza a nagyvállalati mobilitási csomag, amely magában foglalja a prémium szintű Azure AD-előfizetés ingyenes.

### <a name="considerations-for-specific-scenarios"></a>Bizonyos forgatókönyvek esetén megfontolandó szempontok

Feltételes hozzáférés forgatókönyvekben csak vonatkozik a következő információkat:

* Alkalmazások a Microsoft Graph elérése
* Alkalmazások a alapú meghatalmazásos folyamat végrehajtása
* Az alkalmazások több szolgáltatásokhoz és erőforrásokhoz való hozzáférés
* Egyoldalas alkalmazások ADAL.js használatával

A következő részekben bemutatjuk a gyakori forgatókönyvek összetettebb. A működési elv középpontjában feltételes hozzáférési szabályzatok értékeli ki a szolgáltatás, amely rendelkezik a alkalmazni, ha a Microsoft Graphon keresztül van használatban a feltételes hozzáférési szabályzat a jogkivonatot kért idő.

## <a name="scenario-app-accessing-microsoft-graph"></a>Forgatókönyv: Az alkalmazás a Microsoft Graph elérése

Ebben a forgatókönyvben megtudhatja, hogyan webes alkalmazás a Microsoft Graph hozzáférést kér. A feltételes hozzáférési szabályzat ebben az esetben sikerült hozzárendelni a SharePoint, Exchange vagy egy másik megoldás lehet a Microsoft Graphon keresztül elérhető szolgáltatásban. Ebben a példában feltételezzük még nincs a SharePoint online-on a feltételes hozzáférési szabályzat.

![Az alkalmazás elérése Microsoft Graph folyamatábrája](./media/conditional-access-dev-guide/app-accessing-microsoft-graph-scenario.png)

Microsoft Graph elérése a feltételes hozzáférés nélkül egy alsóbb rétegbeli munkaterhelés igénylő engedélyezési először kér az alkalmazás. A kérelem sikeres bármely házirend meghívása nélkül, és az alkalmazás a Microsoft Graph jogkivonatokat kap. Ezen a ponton az alkalmazás felhasználhatja a hozzáférési jogkivonatot a tulajdonosi kérelem a végpont a kért. Az alkalmazás, például a SharePoint online-hoz a Microsoft Graph, végpont elérésére van szüksége: `https://graph.microsoft.com/v1.0/me/mySite`

Az alkalmazás már rendelkezik a Microsoft Graph, érvényes jogkivonatot, így anélkül, hogy folyamatban van egy új jogkivonatot állít ki az új kérelem végrehajtása. A kérelem meghiúsul, és a Microsoft Graph egy HTTP 403 Tiltott az formájában kiadott jogcímeket kihívást egy ```WWW-Authenticate``` kérdés.

Íme egy példa a válasz:

```
HTTP 403; Forbidden
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

A jogcímek kihívás belül van a ```WWW-Authenticate``` fejléc, a program értelmezni tudja a jogcímek paraméter a következő kérelmet kibontásához. Amint azt a rendszer hozzáfűzi az új kérés, Azure ad-ben tudja, hogy a feltételes hozzáférési házirend kiértékeléséhez, amikor a felhasználó bejelentkezik, és az alkalmazás most már nem felelnek meg a feltételes hozzáférési szabályzat. Ismételje meg a SharePoint Online-végpontra a kérés sikeres lesz.

A ```WWW-Authenticate``` fejléc egy egyedi struktúrával rendelkeznek, és nem triviális elemezni értékek kinyerése érdekében. Itt látható egy rövid metódus segítségével.

```csharp
        /// <summary>
        /// This method extracts the claims value from the 403 error response from MS Graph.
        /// </summary>
        /// <param name="wwwAuthHeader"></param>
        /// <returns>Value of the claims entry. This should be considered an opaque string.
        /// Returns null if the wwwAuthheader does not contain the claims value. </returns>
        private String extractClaims(String wwwAuthHeader)
        {
            String ClaimsKey = "claims=";
            String ClaimsSubstring = "";
            if (wwwAuthHeader.Contains(ClaimsKey))
            {
                int Index = wwwAuthHeader.IndexOf(ClaimsKey);
                ClaimsSubstring = wwwAuthHeader.Substring(Index, wwwAuthHeader.Length - Index);
                string ClaimsChallenge;
                if (Regex.Match(ClaimsSubstring, @"}$").Success)
                {
                    ClaimsChallenge = ClaimsSubstring.Split('=')[1];
                }
                else
                {
                    ClaimsChallenge = ClaimsSubstring.Substring(0, ClaimsSubstring.IndexOf("},") + 1);
                }
                return ClaimsChallenge;
            }
            return null;
        }
```

Bemutatják, hogyan lehet kezelni a jogcímek kihívás kódmintákért tekintse a [--meghatalmazásos kódminta](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) ADAL .NET-keretrendszerhez készült.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Forgatókönyv: Az alkalmazás végrehajtása a--meghatalmazásos folyamat

Ebben a forgatókönyvben azt végig a helyzet, amelyben egy natív alkalmazást meghív egy webes szolgáltatás és az API is. Ezután a szolgáltatásnak nincs-e ["he a-meghatalmazásos" folyamat egy alsóbb rétegbeli szolgáltatás hívásához. Ebben az esetben azt alkalmazott a feltételes hozzáférési szabályzatot az alárendelt szolgáltatás (Web API 2) és egy kiszolgáló/démon alkalmazások helyett inkább egy natív alkalmazást használ.

![Az alkalmazás végrehajtása a alapú meghatalmazásos folyamat diagramja](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Webes API-t 1. a kezdeti jogkivonat kérése nem kéri a felhasználó a multi-factor authentication, a webes API-t 1 mindig nem ütközhet az alsóbb rétegbeli API-t. Webes API-t 1 próbál Web API 2--meghatalmazásos jogkivonat a felhasználó kérése, ha a kérés nem teljesíthető, mert a felhasználó nem jelentkezett be a multi-factor authentication szolgáltatás.

Az Azure AD néhány érdekes adatokkal HTTP-választ adja vissza:

> [!NOTE]
> Ebben a példában a multi-factor authentication hiba leírását, de nincs széles `interaction_required` lehetséges vonatkozó feltételes hozzáférést.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

A webes API-1, hogy a hiba catch `error=interaction_required`, és visszaküldi a `claims` vonatkozó kérdést állít be az asztali alkalmazás. Az asztali alkalmazás ezen a ponton végezhet egy új `acquireToken()` meghívása, és fűzze hozzá a `claims`challenge további lekérdezési karakterlánc paraméterként. Az új kérelem a felhasználónak a többtényezős hitelesítést kell végezniük, majd az új jogkivonatot küld vissza a webes API-t 1 és a alapú meghatalmazásos folyamat befejezése.

Próbálja ki ebben a forgatókönyvben, tekintse meg a [.NET-Kódmintát](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ebben bemutatjuk, hogyan adnak át a jogcímeket kihívás vissza a webes API-t 1-től a natív alkalmazás, és hozhat létre egy új kérelmet az ügyfél-alkalmazáson belül.

## <a name="scenario-app-accessing-multiple-services"></a>Forgatókönyv: Az alkalmazás több szolgáltatást elérése

Ebben a forgatókönyvben azt végig a helyzet, amelyben webes alkalmazás hozzáfér a két szolgáltatás egyike rendelkezik hozzárendelt feltételes hozzáférési szabályzat is. Az alkalmazás logikáját, attól függően, amelyben az alkalmazás nem igényel a két webes szolgáltatásokhoz való hozzáférés elérési előfordulhat, hogy létezik. Ebben a forgatókönyvben a rendelés egy tokent kér a felhasználói élmény a fontos szerepet játszik.

Tegyük fel, A és B webszolgáltatás van, és a B webszolgáltatáshoz a alkalmazni feltételes hozzáférési szabályzat. A kezdeti interaktív hitelesítési kérelmek mindkét szolgáltatás jóváhagyási igényel, míg a feltételes hozzáférési szabályzat nem minden esetben szükséges. Webszolgáltatás B jogkivonatot kér az alkalmazás, ha a szabályzat hív, és A webszolgáltatások későbbi kérelmeket is sikeres a következőképpen.

![Az alkalmazás eléréséhez több-szolgáltatások folyamatábrája](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Azt is megteheti Ha az alkalmazás kezdetben a web service A tokent kér, a végfelhasználó nem hívja meg a feltételes hozzáférési szabályzat. Ez lehetővé teszi az alkalmazás fejlesztőjének szabályozhatja a végfelhasználói élményt, és nem kényszeríti a feltételes hozzáférési szabályzat minden olyan esetben meg kell hívni. A legbonyolultabb helyzet, ha az alkalmazás ezt követően webszolgáltatás b tokent kér Ezen a ponton a végfelhasználónak meg kell felelnie azoknak a feltételes hozzáférési szabályzat. Ha az alkalmazás próbál `acquireToken`, előfordulhat, hogy létrehozni (az alábbi ábrán szemléltetett) a következő hibával:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Az alkalmazás több szolgáltatást egy új jogkivonatot igénylő elérése](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Ha az alkalmazás nem használja az ADAL könyvtár, a jogkivonat beszerzése nem sikerült mindig újra megpróbálja interaktív módon. A interaktív kérelem esetén a végfelhasználónak van a lehetőséget, hogy megfeleljen a feltételes hozzáférés. Ez igaz, ha a kérés nem egy `AcquireTokenSilentAsync` vagy `PromptBehavior.Never` ebben az esetben az alkalmazás kell végrehajtania egy interaktív ```AcquireToken``` a végfelhasználás adni a lehetőséget a szabályzat ahhoz, hogy a kérelmet.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Forgatókönyv: Egyoldalas alkalmazás (SPA) ADAL.js használatával

Ebben a forgatókönyvben azt végig az esetben, ha van egy egyoldalas alkalmazás (SPA), a feltételes hozzáférés által védett webes API-hívás ADAL.js használatával. Ez egy egyszerű architektúra, de van néhány apró sajátosságaival szeretne feltételes hozzáférési körül fejlesztése során figyelembe kell venni.

ADAL.js, a jogkivonatok lekérésére néhány funkciók találhatók: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, és `acquireTokenRedirect(…)`.

* `login()` interaktív bejelentkezési kérés egy azonosító jogkivonat szerzi be, de nem szerezhető be a hozzáférési jogkivonatok az egyes szolgáltatásokhoz (beleértve a feltételes hozzáférés által védett webes API-k).
* `acquireToken(…)` Ezután segítségével csendes szerezzen be egy hozzáférési jogkivonatot, azaz a felhasználói felület semmilyen körülmények között nem szerepelnek.
* `acquireTokenPopup(…)` és `acquireTokenRedirect(…)` vannak is lehet interaktív módon igényelni az erőforrás-jogkivonat tehát, mindig bejelentkezési felhasználói felület megjelenítése.

Amikor egy alkalmazásnak egy webes API hívása egy hozzáférési jogkivonatot kell, akkor próbálja meg egy `acquireToken(…)`. Ha a jogkivonat munkamenet lejárt, vagy meg kell felelnie egy feltételes hozzáférési szabályzat, majd a *acquireToken* függvény meghiúsul, és az alkalmazás által használt `acquireTokenPopup()` vagy `acquireTokenRedirect()`.

![Egyoldalas alkalmazás használatával az ADAL folyamatábrája](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Vegyük végig egy példa a feltételes hozzáférési forgatókönyv. Csak a felhasználó betöltötte a webhelyen, és nem rendelkezik a munkamenet. Teljesítjük egy `login()` hívja, kaphat azonosító tokent a multi-factor authentication nélkül. Ezután a felhasználó eléri egy gombot, amely szükséges az alkalmazás adatokat kérjen a webes API-t. Az alkalmazást próbál meg végrehajtani egy `acquireToken()` hívás azonban sikertelen lesz, mivel a felhasználó nem végezte el még a multi-factor authentication és a feltételes hozzáférési szabályzatának igényeinek.

Az Azure AD küld vissza a következő HTTP-választ:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Az alkalmazás tényleges kell a `error=interaction_required`. Az alkalmazás ezután használhatja `acquireTokenPopup()` vagy `acquireTokenRedirect()` ugyanazon az erőforráson futó. A felhasználónak meg kell tennie a multi-factor Authentication hitelesítést. A felhasználó a multi-factor Authentication hitelesítés befejezése után az alkalmazás egy új hozzáférési jogkivonatot a kért erőforrás jelenik meg.

Próbálja ki ebben a forgatókönyvben, tekintse meg a [JS SPA--meghatalmazásos kódminta](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ez a kódminta a feltételes hozzáférési szabályzat és a webes API-t egy JS SPA bemutatásához ebben a forgatókönyvben a korábban regisztrált használja. Azt mutatja be megfelelően a jogcímek kihívás, és a webes API-hoz használható hozzáférési jogkivonatot kapjon. Azt is megteheti, az Általános kivétel [Angular.js kódminta](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) útmutató az Angular SPA-ALKALMAZÁSOKBA


## <a name="see-also"></a>Lásd még

* A képességekkel kapcsolatos további információkért lásd: [feltételes hozzáférés az Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* További Azure AD-mintakódok, lásd: [kódmintát GitHub-adattárat](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* További információ az ADAL SDK-t és a hozzáférés a dokumentációja: [könyvtár útmutató](active-directory-authentication-libraries.md).
* Több-bérlős forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [a felhasználók a több-bérlős minta használatával hogyan](howto-convert-app-to-be-multi-tenant.md).
