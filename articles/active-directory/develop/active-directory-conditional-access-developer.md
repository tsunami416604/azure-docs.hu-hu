---
title: A feltételes hozzáférés Azure Active Directory fejlesztői útmutató
description: Fejlesztői útmutatás és az Azure AD feltételes hozzáférési forgatókönyvek
services: active-directory
keywords: ''
author: CelesteDG
manager: mtillman
editor: PatAltimore
ms.author: celested
ms.reviewer: dadobali
ms.date: 07/19/2017
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: 922af0d860528902bc1eb36d72476852c67fcadf
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157440"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>A feltételes hozzáférés Azure Active Directory fejlesztői útmutató

Azure Active Directory (Azure AD) biztonságos az alkalmazás és szolgáltatás védelme számos lehetőséget kínál. Ezek a egyedi szolgáltatások egyik feltételes hozzáférés. Feltételes hozzáférés lehetővé teszi a fejlesztők és a vállalati ügyfelek többféle, beleértve a szolgáltatások védelmét:

* Multi-Factor Authentication
* Így csak az Intune-ban regisztrált eszközök férhessenek hozzá az egyes szolgáltatások
* Korlátozza a felhasználó helyét és IP-címtartományok

A feltételes hozzáférés a szolgáltatás összes funkciójáról további információkért lásd: [feltételes hozzáférés az Azure Active Directoryban](../active-directory-conditional-access-azure-portal.md). 

Ez a cikk azt összpontosítani milyen feltételes hozzáférési azt jelenti, hogy a fejlesztők számára hozhat létre alkalmazásokat az Azure AD. Ismeretét feltételezi [egyetlen](active-directory-integrating-applications.md) és [több-bérlős](active-directory-devhowto-multi-tenant-overview.md) alkalmazások és [közös hitelesítési minták](active-directory-authentication-scenarios.md).

Azt kell alaposabban tanulmányozhatja a hatása lehet alkalmazni a feltételes hozzáférési szabályzatokat vezérlő nincs keresztül erőforrások eléréséhez. Továbbá azt Fedezze fel a web apps, az a-meghatalmazásos folyamat, a feltételes hozzáférés megvalósítását elérése a Microsoft Graph, hívja az API-k.

## <a name="how-does-conditional-access-impact-an-app"></a>Milyen hatással van a feltételes hozzáférés az alkalmazások?

### <a name="app-types-impacted"></a>Érintett alkalmazástípusok

Leggyakoribb esetekben a feltételes hozzáférés nem változtatja meg az alkalmazások viselkedését, vagy a fejlesztőtől származó módosításokat igényel. Bizonyos esetekben csak amikor egy alkalmazás közvetve vagy csendes kér egy szolgáltatás jogkivonat egy alkalmazás módosítását igényli az kód kezelni a feltételes hozzáférés "kihívást". Elképzelhető, hogy más dolga, mint egy interaktív bejelentkezési kérelem végrehajtása. 

Pontosabban a következő esetekben igényel kezelésére "kihívást" feltételes hozzáférési kódot: 

* A Microsoft Graph hozzáférő alkalmazásokat
* Az a-meghatalmazásos folyamat végrehajtása alkalmazások
* Az alkalmazások több szolgáltatások/erőforrások elérése
* Egylapos alkalmazások ADAL.js
* Web Apps erőforrás hívása

Feltételes hozzáférési szabályzatok alkalmazhatók az alkalmazás, de is alkalmazható egy webes API-k éri el az alkalmazást. A feltételes hozzáférési házirend konfigurálásával kapcsolatos további tudnivalókért tekintse meg [Azure Active Directory feltételes hozzáférés – első lépések](../active-directory-conditional-access-azure-portal-get-started.md).

A helyzettől függően az ügyfél egy vállalati alkalmazni, és bármikor eltávolíthatja a feltételes hozzáférési szabályzatok. Ahhoz, hogy az alkalmazás működését, amikor egy új házirendet alkalmaz meg kell a "kérdés" kezelése. A következő példák bemutatják a challenge kezelését. 

### <a name="conditional-access-examples"></a>Feltételes hozzáférési példa

Egyes esetekben szükséges kódmódosításokat feltételes hozzáférés kezelésére, míg mások a vártnak megfelelően van. Az alábbiakban néhány olyan forgatókönyvet, többtényezős hitelesítést, amely néhány különbség betekintést biztosít a feltételes hozzáférés segítségével.

* Egy egyszeres-bérlő iOS-alkalmazást fejleszt, és a feltételes hozzáférési házirend alkalmazása. Az alkalmazás a felhasználó bejelentkezik, és nem igényelhet hozzáférést egy API-t. Amikor a felhasználó bejelentkezik, a házirend automatikusan megnyílik, és a felhasználó számára szükséges-e többtényezős hitelesítés (MFA). 
* A több-bérlős webes alkalmazás által használt Microsoft Graph Exchange-hozzáférést, más szolgáltatások között készítésekor. Ez az alkalmazás fogad el egy vállalati ügyfelünk Exchange házirend beállítása. Ha a webes alkalmazás jogkivonat MS Graph kér, az alkalmazást a rendszer nem lekéri a szabályzatot ahhoz, hogy. A végfelhasználói érvényes jogkivonatokat van bejelentkezve. Az alkalmazás megkísérli az Exchange-adatok eléréséhez használja ezt a tokent a Microsoft Graph ellen, egy jogcímeket "kérdés" visszatér a web app használatával a ```WWW-Authenticate``` fejléc. Az alkalmazás ezután használhatja a ```claims``` egy új kérés és a feltételeknek ahhoz, hogy a felhasználó kéri. 
* A natív alkalmazással középső réteg szolgáltatást használ egy alárendelt API eléréséhez készítésekor. Egy vállalati ügyfél használja az alkalmazást a vállalatnál a házirend vonatkozik az alsóbb rétegbeli API-t. Ha felhasználó jelentkezik be, a natív alkalmazással a középső réteg hozzáférést kér, és elküldi azt. A középső réteg a-meghatalmazásos folyamat kattintva kérhet hozzáférést az alsóbb rétegbeli API-t az hajt végre. A "kérdés" jogcímek ezen a ponton a középső réteg áll rendelkezésre. A középső réteg elküldi a probléma a natív alkalmazással, amely szükséges ahhoz, hogy a feltételes hozzáférési házirend.

### <a name="complying-with-a-conditional-access-policy"></a>A feltételes hozzáférési házirend megfelel

Számos különböző alkalmazás topológia esetén a feltételes hozzáférési házirend van kiértékelve, amikor a munkamenet. Mint a lépésköz legyen az alkalmazások és szolgáltatások a feltételes hozzáférési szabályzatot, a a pont, ahol meghívták a forgatókönyv megvalósításához próbál fokozottan függ.

Amikor az alkalmazás megpróbálja elérni egy szolgáltatást, a feltételes hozzáférési házirenddel, akkor a feltételes hozzáférés kihívást merülhetnek fel. Ez a kérdés kódolású a `claims` paraméter, amely rendelkezik az Azure AD választ vagy a Microsoft Graph. Íme egy példa a challenge paraméter: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

A fejlesztők a challenge igénybe, és fűzi azokat egy új kérelmet az Azure AD-kiszolgálóra. Sikeres ebben az állapotban kérni fogja a végfelhasználó számára, amelyek szükségesek ahhoz a feltételes hozzáférési házirend bármely művelet elvégzésére. A következő esetekben a hiba vagy a paraméter kicsomagolása magyarázatát olvashatja. 

## <a name="scenarios"></a>Forgatókönyvek

### <a name="prerequisites"></a>Előfeltételek

Azure AD feltételes hozzáférés lehetővé teszi a [Azure AD Premium](../active-directory-whatis.md#choose-an-edition). További követelmények licencelésével kapcsolatban a [licenc nélküli használati jelentés](../active-directory-conditional-access-unlicensed-usage-report.md). A fejlesztők csatlakozhat a [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), mely tartalmazza a nagyvállalati mobilitási csomag, amely magában foglalja a prémium szintű Azure AD ingyenes előfizetés.

### <a name="considerations-for-specific-scenarios"></a>Meghatározott forgatókönyvek szempontjai

Feltételes hozzáférés forgatókönyvekben csak alkalmazza a következő információkat:

* A Microsoft Graph hozzáférő alkalmazásokat
* Az a-meghatalmazásos folyamat végrehajtása alkalmazások
* Az alkalmazások több szolgáltatások/erőforrások elérése
* Egylapos alkalmazások ADAL.js

A következő szakaszokban azt jelenik meg a közös bemutató forgatókönyvek összetettebbek. Az alapvető működési elv feltételes hozzáférési házirendek kiértékelése időpontjában a token van szükség a szolgáltatás, amely rendelkezik egy feltételes hozzáférési házirend alkalmazása, kivéve, ha a Microsoft Graph keresztül van használatban.

## <a name="scenario-app-accessing-microsoft-graph"></a>Forgatókönyv: Alkalmazás elérése a Microsoft Graph

Ebben a forgatókönyvben azt végezze el az esetben, ha a webes alkalmazás kérelmek eléréséhez a Microsoft Graph. A feltételes hozzáférési házirend ebben az esetben sikerült hozzárendelni a SharePoint, Exchange vagy néhány egyéb szolgáltatás, amelyhez a Microsoft Graph keresztül feladatként. Ebben a példában tegyük fel nincs Sharepoint online feltételes hozzáférési házirendet.

![Az alkalmazás elérése a Microsoft Graph folyamatábrán](media/active-directory-conditional-access-developer/app-accessing-microsoft-graph-scenario.png)

Engedély szükséges a feltételes hozzáférés nélkül egy alsóbb rétegbeli munkaterhelés elérése a Microsoft Graph először kér az alkalmazás. A kérelem sikeres bármely házirend meghívása nélkül, és az alkalmazás a Microsoft Graph-jogkivonatokat kap. Ezen a ponton az alkalmazás lehet használni a hozzáférési jogkivonat tulajdonosi kérést a kért végpont. Most az alkalmazás van szüksége, például a Sharepoint Online Microsoft Graph-végpont elérésére: `https://graph.microsoft.com/v1.0/me/mySite`

Az alkalmazás már rendelkezik Microsoft Graph, egy érvényes tokent, így műveleteket hajthat végre az új kérelem nélkül egy új jogkivonat kiállítása történik. A kérelem sikertelen lesz, és a Microsoft Graph egy HTTP 403 Tiltott a formájában kiadott jogcímeket kihívást egy ```WWW-Authenticate``` kérdés.

Íme egy példa a válasz: 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

A jogcímek kihívás belül van a ```WWW-Authenticate``` fejlécet, amely értelmezni tudja a jogcímek paraméter a következő kérelem kibontásához. Amint azt a rendszer hozzáfűzi az új kérelem, az Azure AD tudni fogja, hogy a feltételes hozzáférési házirend kiértékeléséhez, amikor a felhasználó bejelentkezik, és az alkalmazás most már megfelel a feltételes hozzáférési házirend van. A kérés a Sharepoint Online végpontra ismétlődő sikeres lesz.

A ```WWW-Authenticate``` fejléc egyedi struktúrával rendelkezik, és nem triviális érték kivonása elemzése. Íme egy rövid módszer segítségével.

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

Kód mintákat, amelyek bemutatják, hogyan lehet kezelni a jogcímek kihívás, tekintse meg a [a nevében-az kódminta](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) ADAL .NET-keretrendszerhez készült.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Forgatókönyv: Alkalmazás a a-meghatalmazásos folyamat végrehajtása

Ebben a forgatókönyvben azt végezze el az esetet, amelyben a natív alkalmazással hívja a webes szolgáltatás/API-t. Viszont teszi a szolgáltatás [az "a-meghatalmazásos" folyamat](active-directory-authentication-scenarios.md#application-types-and-scenarios) egy alsóbb rétegbeli szolgáltatás hívásához. Ebben az esetben azt alkalmazott a feltételes hozzáférési szabályzatot az alárendelt szolgáltatás (Web API 2) és a kiszolgáló/démon alkalmazás helyett a natív alkalmazással használ. 

![Alkalmazás végrehajtása a-nevében-: folyamatábrája](media/active-directory-conditional-access-developer/app-performing-on-behalf-of-scenario.png)

Az eredeti token kérelem webes API 1 nem kéri a felhasználót a multi-factor authentication végfelhasználói, webes API-1 lehet, hogy mindig éri el az alsóbb rétegbeli API-t. Webes API 1 megkísérli egy jogkivonatot a nevében – az a felhasználó a Web API 2, ha a kérés nem teljesíthető, mivel a felhasználó nem jelentkezett be többtényezős hitelesítést.

Az Azure AD egy HTTP-válasz érdekes adatokkal tér vissza: 

> [!NOTE]
> Ebben a példában a multi-factor Authentication hitelesítés hibaleírás, de számos különböző `interaction_required` lehetséges vonatkozó feltételes hozzáférés. 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

A webes API 1. a hiba azt catch `error=interaction_required`, és elküldi a `claims` kihívás az asztal alkalmazásra. Ezen a ponton a egy asztali alkalmazás egy új tehet `acquireToken()` hívja, és a hozzáfűző a `claims`challenge extra lekérdezési karakterlánc paraméterként. Az új kérelemben a felhasználónak kell a többtényezős hitelesítést és az új jogkivonat visszaküldi a webes API-1 és az a-meghatalmazásos folyamat befejezéséhez.

Próbálja ki ebben a forgatókönyvben, tekintse meg a [.NET kódminta](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Azt mutatja be egy új kérelmet az ügyfélalkalmazás belül, és a jogcímek kihívás vissza a webes API 1 átadása a natív alkalmazással. 

## <a name="scenario-app-accessing-multiple-services"></a>Forgatókönyv: Alkalmazás több szolgáltatás használata

Ebben a forgatókönyvben azt végezze el az eset webalkalmazás fér hozzá két szolgáltatást, amelyek egyike a feltételes hozzáférési házirend hozzárendelése. Attól függően, hogy az alkalmazás logika egy elérési utat, ahol az alkalmazás nem igényel a két webes szolgáltatásokhoz való hozzáférés előfordulhat, hogy létezik. Ebben a forgatókönyvben jogkivonat kér le a sorrend fontos szerepet a végfelhasználói élményt nyújt.

Tegyük fel, A és B webszolgáltatás tudunk és B webszolgáltatás rendelkezik a feltételes hozzáférési házirend is tartozik. A kezdeti interaktív hitelesítési kérelmek mindkét szolgáltatás hozzájárulási igényel, míg a feltételes hozzáférési házirend nem minden esetben szükséges. Webszolgáltatás B jogkivonat kér az alkalmazás, ha a házirend meghívták, és webszolgáltatás A későbbi kérelmek is sikeres a következőképpen.

![Az alkalmazás eléréséhez több-szolgáltatások folyamatábrája](media/active-directory-conditional-access-developer/app-accessing-multiple-services-scenario.png)

Azt is megteheti Ha az alkalmazás a webes szolgáltatás a jogkivonat kezdetben kér, a végfelhasználó nem lép működésbe a feltételes hozzáférési szabályzat. Ez lehetővé teszi az alkalmazás fejlesztőjének nem kényszerítik ki minden olyan esetben meg kell hívni a feltételes hozzáférési szabályzatot, és szabályozhatja a végfelhasználói élmény. A legbonyolultabb helyzet, ha az alkalmazás ezt követően kérelmeket webszolgáltatáshoz b jogkivonat Ezen a ponton a végfelhasználónak kell tartania a feltételes hozzáférési szabályzat. Ha az alkalmazás próbál `acquireToken`, akkor hozhat létre a következő hiba (mutatja be a következő ábra): 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![Az alkalmazás egy új jogkivonatot kérő több szolgáltatás eléréséhez](media/active-directory-conditional-access-developer/app-accessing-multiple-services-new-token.png)

Ha az alkalmazás ADAL-könyvtárhoz, a jogkivonat nem mindig a rendszer ismét megkísérli interaktív módon. Az interaktív kérelem esetén a végfelhasználónak a lehetőség, hogy megfeleljen a feltételes hozzáférés az. Ez érvényét veszti, ha a kérés egy `AcquireTokenSilentAsync` vagy `PromptBehavior.Never` ebben az esetben az alkalmazás számára szükséges-e az interaktív ```AcquireToken``` ahhoz, hogy megkapja a végső felhasználási lehetőséget a házirend ahhoz, hogy a kérelmet. 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Forgatókönyv: Egy lap App (SPA) ADAL.js használatával

Ebben a forgatókönyvben azt végezze el az esetben, ha van a egylapos alkalmazások (SPA), ADAL.js használatával védett feltételes hozzáférés webes API-hívás. Ez egy egyszerű architektúra, de néhány apró, amelyeket figyelembe kell venni, a feltételes hozzáférés körül fejlesztése során.

ADAL.js, nincsenek tokenek beszerzése néhány függvények: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, és `acquireTokenRedirect(…)`. 

* `login()` beolvassa az azonosító tokent egy interaktív bejelentkezési kérelem keresztül, de nem olvasható be a hozzáférési jogkivonatok valamelyik (beleértve a feltételes hozzáférés védett webes API-k) szolgáltatás. 
* `acquireToken(…)` majd segítségével csendes szerezzen be egy jogkivonatot, azaz a felhasználói felület semmilyen körülmények között nem szerepelnek. 
* `acquireTokenPopup(…)` és `acquireTokenRedirect(…)` vannak is lehet párbeszédes formában történő igényelni az erőforrás jogkivonat tehát, mindig bejelentkezési felhasználói felület megjelenítése.

Amikor egy alkalmazásnak kell egy webes API hívásához hozzáférési tokent, az megpróbál egy `acquireToken(…)`. Ha a token munkamenet lejárt, vagy igazolnia kell a feltételes hozzáférési házirend ahhoz, hogy a *acquireToken* függvény meghiúsul, és az alkalmazás által használt `acquireTokenPopup()` vagy `acquireTokenRedirect()`.

![Egyetlen lapnak alkalmazást ADAL folyamatábrája](media/active-directory-conditional-access-developer/spa-using-adal-scenario.png)

Bemutatjuk, a feltételes hozzáférési forgatókönyv egy példán keresztül. A végfelhasználó csak a helyen lévő és a munkamenet nem rendelkezik. Most elvégezni egy `login()` hívja, szerezze be a Azonosítót tokent multi-factor authentication nélkül. Ezután a felhasználó találatok gomb, az alkalmazás szükséges hozzá kérelem adatai egy webes API-t. Az alkalmazás megkísérli egy `acquireToken()` hívás azonban sikertelen lesz, mivel még a többtényezős hitelesítés és a vonatkozó igényeket a feltételes hozzáférési házirend ahhoz, hogy a felhasználó nem végezte el.

Az Azure AD a következő HTTP-választ küld vissza: 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

A tényleges kell a `error=interaction_required`. Az alkalmazás ezután használhatja `acquireTokenPopup()` vagy `acquireTokenRedirect()` ugyanazon az erőforráson futó. A felhasználónak meg kell a többtényezős hitelesítést. A felhasználó a multi-factor Authentication hitelesítés befejezése után az alkalmazás a kért erőforrás friss jogkivonatot ad ki.

Próbálja ki ebben a forgatókönyvben, tekintse meg a [JS SPA a nevében-az kódminta](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). A fenti használ, a feltételes hozzáférési házirend és a webes API-t korábban regisztrált egy JS SPA bemutatásához ebben a forgatókönyvben. Azt illusztrálja, hogyan megfelelően kezeli a jogcímek kihívás, és egy hozzáférési jogkivonatot, amely a webes API nem használható. Alternatív megoldásként az Általános kivétel [Angular.js kódminta](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) egy szögben kifejezett SPA útmutatót


## <a name="see-also"></a>Lásd még

* A képességeivel kapcsolatos további tudnivalókért lásd: [feltételes hozzáférés az Azure Active Directoryban](../active-directory-conditional-access-azure-portal.md).
* További Azure AD-mintakódok, lásd: [Github-tárház, kódmintákat](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory). 
* További információk az ADAL SDK és a hozzáférés a referenciadokumentációt: [könyvtár útmutató](active-directory-authentication-libraries.md).
* Több-bérlős forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [a több-bérlős minta használatával a felhasználók bejelentkezés](active-directory-devhowto-multi-tenant-overview.md).
