---
let application: MSALPublicClientApplication!
title: Alkalmazások migrálása a MSAL-be. ObjectiveC
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Authentication Library for ObjectiveC (iOS és macOS MSAL) és az Azure AD Authentication Library for ObjectiveC (ADAL) közötti különbségeket. ObjC) és a MSAL for iOS és macOS rendszerre való Migrálás.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 208c644c26006fb99139abe1b05c63f90eff448d
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803274"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Alkalmazások migrálása MSAL iOS és macOS rendszerű eszközökre

A Azure Active Directory hitelesítési függvénytár ([ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) úgy lett létrehozva, hogy együttműködjön Azure Active Directory-fiókokkal a 1.0-s végponton keresztül.

Az iOS és macOS rendszerhez készült Microsoft Authentication Library (MSAL) az összes Microsoft-identitással, például az Azure Active Directory (Azure AD) fiókokkal, a személyes Microsoft-fiókokkal és a Microsoft Identity platformon keresztül Azure AD B2C fiókokkal való együttműködésre van felépítve (formálisan Az Azure AD v 2.0-végpontja).

A Microsoft Identity platform néhány fő különbséggel rendelkezik a Azure Active Directory 1.0-s verziójában. Ez a cikk kiemeli ezeket a különbségeket, és útmutatást nyújt az alkalmazások ADAL-ről MSAL-re való átirányításához.

## <a name="adal-and-msal-app-capability-differences"></a>A ADAL és a MSAL alkalmazás képességeinek eltérései

### <a name="who-can-sign-in"></a>Kik jelentkezhetnek be

* A ADAL csak a munkahelyi és iskolai fiókok (más néven Azure AD-fiókok) használatát támogatja.
* A MSAL támogatja a személyes Microsoft-fiókokat (MSA-fiókokat), például a Hotmail.com, a Outlook.com és a Live.com.
* A MSAL támogatja a munkahelyi és iskolai fiókokat, valamint Azure AD B2C fiókokat.

### <a name="standards-compliance"></a>Szabványok megfelelősége

* A Microsoft Identity platform végpontja a OAuth 2,0 és az OpenId Connect szabványokat követi.

### <a name="incremental-and-dynamic-consent"></a>Növekményes és dinamikus beleegyezett

* A Azure Active Directory v 1.0 végpont megköveteli, hogy az alkalmazás regisztrációja során minden engedélyt előre be kell jelenteni. Ez azt jelenti, hogy ezek az engedélyek statikusak.
* A Microsoft Identity platform lehetővé teszi, hogy dinamikusan kérjen engedélyeket. Az alkalmazások csak szükség szerint igényelhetnek engedélyeket, és az alkalmazás igényeinek megfelelően több kérést is kérhetnek.

A Azure Active Directory v 1.0 és a Microsoft Identity platform közötti különbségekről további információt a [Microsoft Identity platform (v 2.0) frissítése](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)című témakörben talál.

## <a name="adal-and-msal-library-differences"></a>ADAL és MSAL könyvtárbeli különbségek

A MSAL nyilvános API az Azure AD v 1.0 és a Microsoft Identity platform közötti főbb különbségeket tükrözi.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication helyett ADAuthenticationContext

`ADAuthenticationContext` egy ADAL-alkalmazás által létrehozott első objektum. A ADAL egy példányát jelöli. Az alkalmazások `ADAuthenticationContext` új példányát hoznak létre minden Azure Active Directory Cloud and bérlői (Authority) kombinációhoz. Ugyanaz a `ADAuthenticationContext` használható több nyilvános ügyfélalkalmazás jogkivonatának lekérésére.

A MSAL-ben a fő interakció egy `MSALPublicClientApplication` objektumon keresztül történik, amely a [OAuth 2,0 nyilvános ügyfél](https://tools.ietf.org/html/rfc6749#section-2.1)után lett modellezve. `MSALPublicClientApplication` egy példánya használható több HRE-felhővel és-Bérlővel való interakcióra anélkül, hogy új példányt kellene létrehoznia minden egyes szolgáltatóhoz. A legtöbb alkalmazás esetében az egyik `MSALPublicClientApplication`-példány elegendő.

### <a name="scopes-instead-of-resources"></a>Hatókörök erőforrások helyett

A ADAL-ben az alkalmazásnak olyan *erőforrás* -azonosítót kellett megadnia, mint `https://graph.microsoft.com` a tokenek beszerzéséhez a Azure Active Directory v 1.0 végpontról. Egy erőforrás megadhat számos hatókört vagy oAuth2Permissions az alkalmazás jegyzékfájljában. Ez lehetővé teszi, hogy az ügyfélalkalmazások az adott erőforrástól származó jogkivonatokat igényeljenek az alkalmazás regisztrálásakor előre definiált hatókörökhöz.

Egyetlen erőforrás-azonosító helyett a MSAL-alkalmazások hatóköröket biztosítanak a kérelmekhez. A hatókör egy erőforrás-azonosító, amelyet az erőforrás/engedély űrlapon lévő engedély neve követ. Például: `https://graph.microsoft.com/user.read`

A MSAL kétféleképpen biztosítható hatókörök:

* Adja meg az alkalmazások által igényelt összes engedély listáját. Példa: 

    `@[@"https://graph.microsot.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    Ebben az esetben az alkalmazás kéri a `directory.read` és a `directory.write` engedélyeket. A rendszer arra kéri a felhasználót, hogy fogadja el ezeket az engedélyeket, ha azokat még nem adták hozzá az alkalmazáshoz. Előfordulhat, hogy az alkalmazás további engedélyeket is kap, amelyeket a felhasználó már beleegyezett az alkalmazáshoz. A rendszer csak az új engedélyek vagy a nem megadott engedélyek megadását kéri a felhasználótól.

* A `/.default` hatóköre.

Ez az összes alkalmazás beépített hatóköre. Az alkalmazás regisztrálásakor konfigurált engedélyek statikus listájára vonatkozik. A viselkedése hasonló a `resource`hoz. Ez akkor lehet hasznos, ha az áttelepítés során a rendszer megőrzi a hatókörök és a felhasználói élmény hasonló készletét.

Az `/.default` hatókör használatához fűzze hozzá a `/.default`t az erőforrás-azonosítóhoz. Például: `https://graph.microsoft.com/.default`. Ha az erőforrás perjelet (`/`) ér véget, továbbra is hozzá kell fűzni `/.default`t, beleértve a kezdő perjelet is, ami egy olyan hatókört eredményez, amely egy dupla továbbítási perjeltel (`//`) rendelkezik.

További információt a "/.default" hatókör használatáról [itt](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope) talál

### <a name="supporting-different-webview-types--browsers"></a>Különböző webnézet-típusok támogatása & böngészőkben

A ADAL csak az iOS-hez készült UIWebView/WKWebView, valamint a macOS-hez készült Webview-t támogatja. Az iOS-hez készült MSAL több lehetőséget is biztosít a webes tartalom megjelenítéséhez, amikor engedélyezési kódot kér, és a továbbiakban nem támogatja a `UIWebView`; így javíthatja a felhasználói élményt és a biztonságot.

Alapértelmezés szerint a MSAL on iOS a [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)-t használja, amely az Apple által az iOS 12 + rendszerű eszközökön történő hitelesítéshez javasolt. Az egyszeri bejelentkezés (SSO) előnyeit biztosítja az alkalmazások és a Safari böngésző közötti cookie-megosztáson keresztül.

Az alkalmazás követelményeitől és a kívánt végfelhasználói élménytől függően eltérő webes összetevőt is használhat. További lehetőségek: [támogatott webes nézettípus](customize-webviews.md) .

A ADAL-ből a MSAL-be való Migrálás során a `WKWebView` az iOS-és macOS-ADAL hasonló felhasználói élményt nyújt. Javasoljuk, hogy ha lehetséges, telepítse át `ASWebAuthenticationSession` iOS-re. A macOS esetében javasoljuk, hogy használja a `WKWebView`.

### <a name="account-management-api-differences"></a>Fiókkezelés API-eltérések

Ha meghívja a ADAL metódusokat `acquireToken()` vagy `acquireTokenSilent()`, akkor egy `ADUserInformation` objektum jelenik meg, amely tartalmazza a hitelesített fiókot jelölő `id_token` jogcímek listáját. Emellett a `ADUserInformation` egy `userId` ad vissza az `upn` jogcím alapján. A kezdeti interaktív jogkivonat-beszerzést követően a ADAL arra vár, hogy a fejlesztők minden csendes hívást `userId` biztosítanak.

A ADAL nem biztosít API-t az ismert felhasználói identitások beolvasásához. Az alkalmazásra támaszkodik, hogy mentse és kezelhesse ezeket a fiókokat.

A MSAL API-kat biztosít a MSAL számára ismert összes fiók listázásához a jogkivonat beszerzése nélkül.

A ADAL-hez hasonlóan a MSAL olyan fiókadatokat ad vissza, amely a jogcímek listáját tartalmazza a `id_token`. Része a `MSALAccount` objektumnak a `MSALResult` objektumon belül.

A MSAL API-kat biztosít a fiókok eltávolításához, így az eltávolított fiókok elérhetetlenné válnak az alkalmazás számára. A fiók eltávolítása után a későbbi jogkivonat-beszerzési hívások megkérik a felhasználót az interaktív jogkivonat-beszerzésre. A fiók eltávolítása csak az azt elindító ügyfélalkalmazás esetében érvényes, és nem távolítja el a fiókot az eszközön vagy a rendszerböngészőből futó többi alkalmazásból. Ez biztosítja, hogy a felhasználó továbbra is egyszeri bejelentkezéses felhasználói felülettel rendelkezzen az eszközön, még az egyes alkalmazások kijelentkezése után is.

Emellett a MSAL egy olyan fiókazonosító-azonosítót is ad vissza, amely a tokenek csendes lekérésére használható. A `MSALAccount` objektumban `identifier` tulajdonságon keresztül elérhető fiókazonosító azonban nem játszható le, és nem tudja ellátni, hogy milyen formátumban van, és hogy nem próbálja meg értelmezni vagy elemezni.

### <a name="migrating-the-account-cache"></a>A fiók gyorsítótárának áttelepítése

A ADAL-ból való áttelepítés során az alkalmazások általában a ADAL `userId`tárolja, amely nem rendelkezik a MSAL által igényelt `identifier`. Egyszeri áttelepítési lépésként az alkalmazások lekérhetnek egy MSAL-fiókot a ADAL felhasználóazonosító használatával a következő API-val:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Ez az API a MSAL és a ADAL gyorsítótárát is beolvassa a fiók megkereséséhez a ADAL userId (UPN) használatával.

Ha a fiók megtalálható, a fejlesztőnek a fiókot kell használnia a csendes jogkivonat beszerzéséhez. Az első csendes jogkivonat beszerzése hatékonyan frissíti a fiókot, és a fejlesztő a MSAL eredményében (`identifier`) a MSAL-kompatibilis fiók azonosítóját fogja kapni. Ezt követően a következő API-val csak `identifier`t kell használni a fiókok kereséséhez:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Habár lehetséges, hogy továbbra is használhatja a ADAL `userId` a MSAL összes műveletéhez, mivel a `userId` UPN-alapú, több korlátozást is felhasználhat, ami rossz felhasználói élményt eredményezhet. Ha például az UPN megváltozik, a felhasználónak újra be kell jelentkeznie. Azt javasoljuk, hogy az összes alkalmazás az összes művelethez használja a nem kijátszható fiókot `identifier`.

További információ a [gyorsítótár állapotának áttelepítéséről](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Tokenek beszerzésének módosításai

A MSAL a jogkivonat-beszerzési hívások változásait mutatja be:

* A ADAL-hez hasonlóan `acquireTokenSilent` mindig csendes kérést eredményez.
* A ADAL-től eltérően a `acquireToken` mindig a felhasználó által végrehajtható felhasználói FELÜLETET jeleníti meg a webes nézetben vagy a Microsoft Authenticator alkalmazásban. A Webview/Microsoft Authenticator szolgáltatáson belüli SSO-állapottól függően a rendszer kérheti a felhasználótól a hitelesítő adatok megadását.
* A ADAL `acquireToken` a `AD_PROMPT_AUTO` először a csendes jogkivonat-beszerzést kísérli meg, és csak akkor jeleníti meg a felhasználói felületet, ha a csendes kérelem meghiúsul. A MSAL ezt a logikát úgy érheti el, hogy az `acquireTokenSilent` és csak a `acquireToken` hívja meg, ha a csendes beszerzés meghiúsul. Ez lehetővé teszi a fejlesztők számára a felhasználói élmény testreszabását az interaktív jogkivonat-beszerzés megkezdése előtt.

### <a name="error-handling-differences"></a>Hibák kezelésére vonatkozó különbségek

A MSAL az alkalmazás által kezelt és a felhasználó által beavatkozást igénylő hibák közötti átláthatóságot biztosít. Korlátozott számú hiba fejlesztőnek kell kezelnie a következőket:

* `MSALErrorInteractionRequired`: a felhasználónak interaktív kérést kell tennie. Ennek számos oka lehet, például egy lejárt hitelesítési munkamenet, a feltételes hozzáférési szabályzat módosult, a frissítési token lejárt vagy vissza lett vonva, nincsenek érvényes jogkivonatok a gyorsítótárban, és így tovább.
* `MSALErrorServerDeclinedScopes`: a kérés nem fejeződött be teljesen, és néhány hatókör nem kapott hozzáférést. Ezt az okozhatja, hogy egy felhasználó egy vagy több hatókörhöz tartozó beleegyezett.

A [`MSALError` listán](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) szereplő összes egyéb hiba nem kötelező. A hibákkal kapcsolatos információkat a felhasználói élmény javítása érdekében használhatja.

A MSAL-hibák kezelésével kapcsolatban lásd: [kivételek és hibák kezelése a MSAL használatával](msal-handling-exceptions.md) .

### <a name="broker-support"></a>Közvetítői támogatás

A MSAL a 0.3.0 verziótól kezdődően a Microsoft Authenticator alkalmazással biztosítja a felügyelt hitelesítés támogatását. A Microsoft Authenticator a feltételes hozzáférési forgatókönyvek támogatását is lehetővé teszi. Példák a feltételes hozzáférési forgatókönyvekre: olyan eszközök megfelelőségi szabályzatai, amelyek megkövetelik a felhasználónak az eszköz Intune-beli regisztrálását vagy a HRE való regisztrációt a jogkivonat beszerzéséhez. És a Mobile Application Management (MAM) feltételes hozzáférési szabályzatai, amelyek megkövetelik a megfelelőség igazolását, mielőtt az alkalmazás megkapja a jogkivonatot.

Az alkalmazás brókerének engedélyezése:

1. Regisztráljon egy Broker-kompatibilis átirányítási URI-formátumot az alkalmazáshoz. A Broker-kompatibilis átirányítási URI formátuma `msauth.<app.bundle.id>://auth`. Cserélje le a `<app.bundle.id>`t az alkalmazás Bundle-azonosítójával. Ha a ADAL-ről végez áttelepítést, és az alkalmazása már közvetítő képességgel rendelkezik, semmi feleslegesen nem kell mást tennie. Az előző átirányítási URI-ja teljes mértékben kompatibilis a MSAL, így a 3. lépésre ugorhat.

2. Adja hozzá az alkalmazás átirányítási URI-sémáját az info. plist fájlhoz. Az alapértelmezett MSAL átirányítási URI esetében a formátum `msauth.<app.bundle.id>`. Példa:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Adja hozzá az alábbi sémákat az alkalmazás info. plist összes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Adja hozzá a következőt a AppDelegate. m fájlhoz a visszahívások kezeléséhez: Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Üzleti vállalatok közötti (B2B)

A ADAL-ben külön `ADAuthenticationContext` példányokat hoz létre minden olyan bérlőhöz, amelyhez az alkalmazás jogkivonatokat kér. Ez már nem követelmény a MSAL. A MSAL-ben létrehozhat egy `MSALPublicClientApplication` egyetlen példányát, és bármely HRE-felhőhöz és-szervezethez használhatja a acquireToken-és acquireTokenSilent-hívások eltérő szolgáltatójának megadásával.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO más SDK-k együttműködésével

Az iOS-hez készült MSAL az alábbi SDK-k segítségével hozhatnak létre SSO-t egy egyesített gyorsítótáron keresztül:

- ADAL Objective-C 2.7. x +
- MSAL.NET a Xamarin 2.4. x + esetében
- ADAL.NET a Xamarin 4.4. x + esetében

Az egyszeri bejelentkezés az iOS-es kulcstartó megosztásán keresztül érhető el, és csak az azonos Apple Developer-fiókból közzétett alkalmazások között érhető el.

Az egyszeri bejelentkezés az iOS-alapú kulcstartó használatával történik az egyetlen csendes SSO-típus.

MacOS rendszeren a MSAL az iOS-és macOS-alapú alkalmazásokhoz és a ADAL Objective-C-alapú alkalmazásokhoz használható egyéb MSAL is képes az SSO-t elérni.

Az iOS-MSAL két más típusú SSO-típust is támogat:

* Egyszeri bejelentkezés a böngészőben. A MSAL for iOS támogatja a `ASWebAuthenticationSession`, amely egyszeri bejelentkezést biztosít az eszközön lévő más alkalmazások és kifejezetten a Safari böngészője között megosztott cookie-kkal.
* Egyszeri bejelentkezés hitelesítési közvetítőn keresztül. IOS-eszközön Microsoft Authenticator a hitelesítési közvetítőként működik. Olyan feltételes hozzáférési szabályzatokat követhetnek, mint például a megfelelő eszköz megkövetelése, és egyszeri bejelentkezést biztosít a regisztrált eszközökhöz. Alapértelmezés szerint a MSAL SDK-val kezdődően a 0.3.0 verziója támogatja a közvetítőt.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

Az [INTUNE MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started) támogatja a MSAL for iOS verziót a [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2) verziótól kezdődően

## <a name="msal-and-adal-in-the-same-app"></a>MSAL és ADAL ugyanabban az alkalmazásban

A ADAL 2.7.0 és újabb verziója nem létezhet ugyanazon alkalmazás MSAL. Ennek fő oka a közös Almodul általános kódja. Mivel a Objective-C nem támogatja a névtereket, ha a ADAL-és MSAL-keretrendszereket is hozzáadja az alkalmazáshoz, akkor az azonos osztály két példánya lesz. Nincs garancia arra, hogy az egyik a futtatókörnyezetben legyen kiválasztva. Ha mindkét SDK az ütköző osztály azonos verzióját használja, az alkalmazás továbbra is működhet. Ha azonban egy másik verzióról van szó, az alkalmazás váratlan összeomlásokat tapasztalhat, amelyeket nehéz diagnosztizálni.

A ADAL és a MSAL ugyanabban a termelési alkalmazásban való futtatása nem támogatott. Ha azonban csak teszteli és áttelepíti a felhasználókat a ADAL Objective-C-ről az iOS és a macOS MSAL, továbbra is használhatja a [ADAL Objective-c 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). Ez az egyetlen olyan verzió, amely az MSAL-ben működik ugyanabban az alkalmazásban. Ehhez a ADAL-verzióhoz nem lesz új szolgáltatási frissítés, ezért csak áttelepítéshez és teszteléshez használható. Az alkalmazás nem hivatkozhat a ADAL és a MSAL hosszú távú létezésére.

A ADAL és a MSAL párhuzamos létezése nem támogatott ugyanabban az alkalmazásban.
A ADAL és a MSAL párhuzamos létezése több alkalmazás között teljes mértékben támogatott.

## <a name="practical-migration-steps"></a>Gyakorlati áttelepítési lépések

### <a name="app-registration-migration"></a>Alkalmazás regisztrációjának áttelepítése

Nem kell módosítania a meglévő HRE alkalmazást, hogy váltson MSAL, és engedélyezze a HRE-fiókokat. Ha azonban a ADAL-alapú alkalmazás nem támogatja a felügyelt hitelesítést, regisztrálnia kell egy új átirányítási URI-t az alkalmazáshoz, mielőtt át lehetne váltani a MSAL.

Az átirányítási URI-nak a következő formátumúnak kell lennie: `msauth.<app.bundle.id>://auth`. Cserélje le a `<app.bundle.id>`t az alkalmazás Bundle-azonosítójával. Az átirányítási URI megadása a [Azure Portalban](https://aka.ms/MobileAppReg).

Csak iOS esetén a tanúsítvány-alapú hitelesítés támogatásához egy további átirányítási URI-t kell regisztrálni az alkalmazásban és a Azure Portal a következő formátumban: `msauth://code/<broker-redirect-uri-in-url-encoded-form>`. Például: `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Javasoljuk, hogy minden alkalmazás regisztráljon mindkét átirányítási URI-t.

Ha további támogatásra van szüksége a növekményes jóváhagyáshoz, válassza ki azokat az API-kat és engedélyeket, amelyeket az alkalmazás úgy konfigurált, hogy hozzáférést kérjen az alkalmazás regisztrálásához az **API-engedélyek** lapon.

Ha a ADAL-ről végez áttelepítést, és támogatni szeretné mind a HRE, mind a MSA-fiókot, a meglévő alkalmazás-regisztrációt is frissíteni kell, hogy támogassa mindkettőt. Nem ajánlott frissíteni a meglévő éles alkalmazást, hogy a HRE és a MSA azonnal támogassa. Ehelyett hozzon létre egy másik ügyfél-azonosítót, amely támogatja mind a HRE, mind a MSA teszteléshez, és miután meggyőződött arról, hogy az összes forgatókönyv működik, frissítse a meglévő alkalmazást.

### <a name="add-msal-to-your-app"></a>MSAL hozzáadása az alkalmazáshoz

Az előnyben részesített csomagkezelő eszköz használatával hozzáadhat MSAL SDK-t az alkalmazáshoz. A [részletes utasításokat itt](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)találja.

### <a name="update-your-apps-infoplist-file"></a>Az alkalmazás info. plist fájljának frissítése

Csak iOS esetén adja hozzá az alkalmazás átirányítási URI-sémáját az info. plist fájlhoz. A ADAL Broker-kompatibilis alkalmazások esetében már ott kell lennie. Az alapértelmezett MSAL átirányítási URI-séma formátuma: `msauth.<app.bundle.id>`.  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Adja hozzá az alábbi sémákat az alkalmazás info. plist fájljában `LSApplicationQueriesSchemes`alatt.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>AppDelegate-kód frissítése

Csak iOS esetén adja hozzá a következőt a AppDelegate. m fájlhoz:

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Ha a Xcode 11**-et használja, helyette a MSAL-visszahívást kell elhelyeznie a `SceneDelegate` fájlba.
Ha mind a UISceneDelegate, mind a UIApplicationDelegate támogatja a régebbi iOS-kompatibilitást, akkor a MSAL visszahívást mindkét fájlba be kell helyezni.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

Ez lehetővé teszi, hogy a MSAL kezelje a közvetítő és a webes összetevő válaszait.
Ez nem volt szükséges a ADAL-ben, mivel "swizzled" alkalmazás-delegált metódusok automatikusan. A manuálisan való hozzáadás kevésbé érzékeny, és az alkalmazás további vezérlést biztosít.

### <a name="enable-token-caching"></a>Jogkivonat-gyorsítótárazás engedélyezése

Alapértelmezés szerint a MSAL az iOS-vagy macOS-kulcstartóban gyorsítótárazza az alkalmazás jogkivonatait. 

A jogkivonat-gyorsítótárazás engedélyezése:
1. Győződjön meg arról, hogy az alkalmazás megfelelően van aláírva
2. Nyissa meg a Xcode-projekt beállításai > **képességek lapon** > a **kulcstartó megosztásának engedélyezése** lehetőséget.
3. Kattintson a **+** elemre, és adja meg a következő **kulcstartó-csoportok** bejegyzést: 3. a for iOS, írja be a `com.microsoft.adalcache` 3. b MacOS-be `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Hozzon létre MSALPublicClientApplication, és váltson a acquireToken és a acquireTokeSilent-hívásokra

`MSALPublicClientApplication` a következő kóddal hozható létre:

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Ezután hívja meg a fiókkezelés API-t, és ellenőrizze, hogy vannak-e olyan fiókok a gyorsítótárban:

Objective-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



vagy olvassa el az összes fiókot:

Objective-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Ha a rendszer megtalál egy fiókot, hívja meg a MSAL `acquireTokenSilent` API-t:

Objective-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Következő lépések

További információ a [hitelesítési folyamatokról és az alkalmazási forgatókönyvekről](authentication-flows-app-scenarios.md)
