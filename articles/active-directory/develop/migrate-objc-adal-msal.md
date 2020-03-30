---
title: ADAL a MSAL migrációs útmutató (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az iOS/macOS-alapú MSAL és az Azure AD authentication library for ObjectiveC (ADAL) közötti különbségeket. ObjC) és hogyan lehet áttérni az MSAL-ra iOS/macOS rendszeren.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085176"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Alkalmazások áttelepítése iOS és macOS rendszerre az MSAL-ra

Az Azure Active Directory hitelesítési könyvtár ([ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) jött létre, hogy működjön együtt az Azure Active Directory-fiókok a v1.0-végponton keresztül.

Az iOS- és macOS-alapú Microsoft hitelesítési könyvtár (MSAL) úgy készült, hogy az összes Microsoft-identitással, például az Azure Active Directory-fiókokkal, a személyes Microsoft-fiókokkal és az Azure AD B2C-fiókokkal működjön együtt a Microsoft identitásplatformon keresztül (hivatalosan) az Azure AD 2.0-s végpont).

A Microsoft identity platform rendelkezik néhány fő különbség az Azure Active Directory 1.0-s verzióval. Ez a cikk kiemeli ezeket a különbségeket, és útmutatást nyújt az alkalmazások ADAL-ról Az MSAL-ra való áttelepítéséhez.

## <a name="adal-and-msal-app-capability-differences"></a>ADAL és MSAL alkalmazás képességbeli különbségei

### <a name="who-can-sign-in"></a>Ki jelentkezhet be?

* Az ADAL csak a munkahelyi és iskolai fiókokat támogatja – más néven Az Azure AD-fiókokat.
* Az MSAL támogatja a személyes Microsoft-fiókokat (MSA-fiókokat), például a Hotmail.com, a Outlook.com és a Live.com.
* Az MSAL támogatja a munkahelyi és iskolai fiókokat, valamint az Azure AD B2C-fiókokat.

### <a name="standards-compliance"></a>Szabványoknak való megfelelés

* A Microsoft identity Platform végpont az OAuth 2.0 és az OpenId Connect szabványokat követi.

### <a name="incremental-and-dynamic-consent"></a>Növekményes és dinamikus hozzájárulás

* Az Azure Active Directory 1.0-s virtuális pont megköveteli, hogy az összes engedélyt előre deklarálni kell az alkalmazás regisztrációja során. Ez azt jelenti, hogy ezek az engedélyek statikusak.
* A Microsoft identity platform lehetővé teszi, hogy dinamikusan kérjen engedélyeket. Az alkalmazások csak szükség esetén kérhetnek engedélyeket, és többet kérhetnek, mivel az alkalmazásnak szüksége van rájuk.

Az Azure Active Directory 1.0-s verzióés a Microsoft identity platform közötti különbségekről a [Miért frissítsen a Microsoft identity platformra (2.0-s verzió)](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)című témakörben.

## <a name="adal-and-msal-library-differences"></a>ADAL és MSAL könyvtárkülönbségek

Az MSAL nyilvános API tükrözi néhány fő különbség az Azure AD 1.0-s verzió és a Microsoft identitás platform.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication az ADAuthenticationContext helyett

`ADAuthenticationContext`az első objektum, amelyet az ADAL alkalmazás létrehoz. Az ADAL példányosítását jelenti. Az alkalmazások minden `ADAuthenticationContext` Egyes Azure Active Directory-felhő- és bérlői (hatósági) kombinációhoz új példányt hoznak létre. Ugyanez `ADAuthenticationContext` használható több nyilvános ügyfélalkalmazás jogkivonatának lekéréséhez.

Az MSAL-ban a fő `MSALPublicClientApplication` interakció egy objektumon keresztül történik, amely az [OAuth 2.0 nyilvános ügyfél](https://tools.ietf.org/html/rfc6749#section-2.1)után van modellezve. Egy példányban `MSALPublicClientApplication` lehet használni, hogy kölcsönhatásba lépnek a több AAD-felhők, és a bérlők, anélkül, hogy hozzon létre egy új példányt az egyes hatóságok. A legtöbb alkalmazás `MSALPublicClientApplication` esetében egy példány elegendő.

### <a name="scopes-instead-of-resources"></a>Hatókörök az erőforrások helyett

Az ADAL-ban egy alkalmazásnak meg `https://graph.microsoft.com` kellett adnia egy erőforrás-azonosítót, például jogkivonatokat az Azure Active Directory 1.0-s végpontjából. *resource* Egy erőforrás számos hatókört vagy oAuth2Engedélyeket definiálhat az alkalmazásjegyzékben, amelyeket megért. Ez lehetővé tette az ügyfélalkalmazások számára, hogy jogkivonatokat kérjenek az adott erőforrásból az alkalmazásregisztráció során előre meghatározott hatókörök bizonyos készletéhez.

Az MSAL-ban egyetlen erőforrás-azonosító helyett az alkalmazások kérésenként hatókörkészletet biztosítanak. A hatókör egy erőforrás-azonosító, amelyet az űrlaperőforrásban/-engedélyben egy engedélynév követ. Például: `https://graph.microsoft.com/user.read`

Az MSAL-ban kétféleképpen lehet hatóköröket biztosítani:

* Adja meg az alkalmazások igényeinek listáját. Példa: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    Ebben az esetben az `directory.read` alkalmazás `directory.write` kéri a és az engedélyeket. A felhasználónak beleegyezését kell kérnie ezekhez az engedélyekhez, ha korábban nem egyezett bele az alkalmazáshoz. Az alkalmazás további engedélyeket is kaphat, amelyekhez a felhasználó már hozzájárult az alkalmazáshoz. A felhasználó csak a meg nem adott új engedélyek vagy engedélyek beleegyezését kéri.

* A `/.default` távcső.

Ez a beépített hatókör minden alkalmazáshoz. Az alkalmazás regisztrálásakor konfigurált engedélyek statikus listájára hivatkozik. Viselkedése hasonló a `resource`. Ez akkor lehet hasznos, ha áttelepítése annak érdekében, hogy egy hasonló hatókör-és felhasználói élmény fenntartása érdekében.

A `/.default` hatókör használatához fűzz hozzá `/.default` az erőforrás-azonosítóhoz. Például: `https://graph.microsoft.com/.default`. Ha az erőforrás perjellel (`/`végződik `/.default`), akkor is hozzá kell férnie , beleértve`//`a vezető perjelet is, ami olyan hatókört eredményez, amelyben kettős perjel ( ) van.

A "/.default" hatókör használatáról itt olvashat [bővebben.](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Különböző WebView-típusok & böngészők támogatása

Az ADAL csak az iOS UIWebView/WKWebView és a webview for macOS rendszert támogatja. Az MSAL for iOS további lehetőségeket támogat a webes tartalom megjelenítésére, `UIWebView`amikor engedélyezési kódot kér, és már nem támogatja ; amely javíthatja a felhasználói élményt és a biztonságot.

Alapértelmezés szerint az iOS-alapú MSAL az [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)programot használja, amely az Apple által az iOS 12+ készülékeken a hitelesítéshez javasolt webes összetevő. Egyszeri bejelentkezési (SSO) előnyöket biztosít az alkalmazások és a Safari böngésző közötti cookie-megosztáson keresztül.

Az alkalmazás követelményeitől és a kívánt végfelhasználói élménytől függően választhat egy másik webes összetevő t. További lehetőségeket a [támogatott webes nézettípusok](customize-webviews.md) című témakörben talál.

Az ADAL-ról az MSAL-ra való `WKWebView` áttéréskor az iOS és a macOS rendszeren az ADAL-hoz leginkább hasonló felhasználói élményt biztosít. Javasoljuk, hogy `ASWebAuthenticationSession` lehetőség szerint térjen át az iOS-re. MacOS esetén javasoljuk, hogy `WKWebView`használja a használatát.

### <a name="account-management-api-differences"></a>Fiókkezelési API-különbségek

Amikor meghívja az `acquireToken()` ADAL metódusokat, vagy `acquireTokenSilent()`, olyan `ADUserInformation` objektumot kap, amely a `id_token` hitelesítés alatt álló fiókot jelképező jogcímek listáját tartalmazza. Ezenkívül `ADUserInformation` a `upn` `userId` követelés alapján ad vissza. A kezdeti interaktív tokenek beszerzése `userId` után az ADAL elvárja a fejlesztőtől, hogy minden néma hívásban biztosítsa.

Az ADAL nem biztosít API-t az ismert felhasználói identitások lekéréséhez. Az alkalmazásra támaszkodik a fiókok mentéséhez és kezeléséhez.

Az MSAL api-k készletét biztosítja az MSAL által ismert összes fiók listázásához anélkül, hogy jogkivonatot kellene beszereznie.

Az ADAL-hoz hasonlóan az MSAL is visszaadja azokat a számlaadatokat, amelyek a `id_token`ból származó követelések listáját tartalmazza. Az objektum része `MSALAccount` az `MSALResult` objektumban.

Az MSAL API-k készletét biztosítja a fiókok eltávolításához, így az eltávolított fiókok elérhetetlenné válikk az alkalmazás számára. A fiók eltávolítása után a későbbi token-beszerzési hívások interaktív jogkivonat-beszerzésre kérik a felhasználót. A fiók eltávolítása csak az azt elinduló ügyfélalkalmazásra vonatkozik, és nem távolítja el a fiókot az eszközön futó többi alkalmazásból vagy a rendszerböngészőből. Ez biztosítja, hogy a felhasználó továbbra is sso-élményt nyújtson az eszközön, még akkor is, ha kijelentkezik egy adott alkalmazásból.

Emellett az MSAL egy fiókazonosítót is visszaad, amely később egy token csendes igénylésére használható. A fiókazonosító (amely `identifier` az `MSALAccount` objektumban lévő tulajdonságon keresztül érhető el) azonban nem jeleníthető meg, és nem feltételezheti, hogy milyen formátumban van, és nem is kell megpróbálni értelmezni vagy elemezni.

### <a name="migrating-the-account-cache"></a>A fiókgyorsítótár áttelepítése

Az ADAL-ról való áttelepítéskor az `userId`alkalmazások általában ADAL-okat tárolnak, amelyek nem rendelkeznek az `identifier` MSAL által megkövetelt módon. Egyszeri áttelepítési lépésként az alkalmazások lekérdezhetnek egy MSAL-fiókot az ADAL userId használatával a következő API-val:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Ez az API beolvassa az MSAL és az ADAL gyorsítótárát is az ADAL userId (UPN) fiók megkereséséhez.

Ha a fiók található, a fejlesztő kell használnia a fiókot, hogy csendes token megszerzése. Az első csendes token akvizíció hatékonyan frissíti a fiókot, és a fejlesztő kap`identifier`egy MSAL-kompatibilis fiókazonosítót az MSAL eredményében ( ). Ezt követően `identifier` csak a következő API használatával használható fióktámadásokhoz:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Bár lehetséges, hogy továbbra is az `userId` ADAL az összes művelet `userId` MSAL, mivel az UPN alapul, ez több korlátozás, ami rossz felhasználói élményt eredményez. Ha például az upn megváltozik, a felhasználónak újra be kell jelentkeznie. Azt javasoljuk, hogy minden alkalmazás `identifier` használja a nem megjeleníthető fiókot az összes művelethez.

További információ a [gyorsítótár állapotának áttelepítéséről.](sso-between-adal-msal-apps-macos-ios.md)

### <a name="token-acquisition-changes"></a>Token-akvizíció módosításai

Az MSAL bevezet néhány token akvizíciós hívásmódosítást:

* Az ADAL-hoz `acquireTokenSilent` hasonlóan mindig csendes kérést eredményez.
* Az ADAL-lal `acquireToken` ellentétben mindig a felhasználó által végrehajtható felhasználói felületet eredményez a webes nézeten vagy a Microsoft Authenticator alkalmazáson keresztül. A webview/Microsoft Authenticator szolgáltatáson belüli SSO-állapottól függően a rendszer kérheti a felhasználónak a hitelesítő adatai megadását.
* Az ADAL-ban `acquireToken` `AD_PROMPT_AUTO` először megpróbálja a csendes tokenek megszerzését, és csak akkor jeleníti meg a felhasználói felületet, ha a csendes kérelem sikertelen. Az MSAL-ban ez a logika `acquireTokenSilent` első hívással érhető el, és csak akkor hív, `acquireToken` ha a csendes akvizíció sikertelen. Ez lehetővé teszi a fejlesztők számára, hogy az interaktív tokenek beszerzése előtt testre szabják a felhasználói élményt.

### <a name="error-handling-differences"></a>Hibakezelési különbségek

Az MSAL egyértelműbbé teszi az alkalmazás által kezelhető és a felhasználó beavatkozását igénylő hibák között. A fejlesztőknek korlátozott számú hibát kell kezelniük:

* `MSALErrorInteractionRequired`: A felhasználónak interaktív kérést kell tennie. Ennek különböző okai lehetnek, például egy lejárt hitelesítési munkamenet, feltételes hozzáférési házirend megváltozott, a frissítési jogkivonat lejárt vagy visszavonták, nincsenek érvényes jogkivonatok a gyorsítótárban, és így tovább.
* `MSALErrorServerDeclinedScopes`: A kérelem nem fejeződött be teljesen, és egyes hatókörök nem kaptak hozzáférést. Ennek oka az lehet, hogy a felhasználó egy vagy több hatókörhöz nem járul hozzá.

A [ `MSALError` lista](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) összes többi hibakezelése nem kötelező. A felhasználói élmény javítása érdekében felhasználhatja a hibákban szereplő információkat.

Az MSAL-hibakezelésről további [témakörök: Az MSAL használatával kapcsolatos kivételek és hibák](msal-handling-exceptions.md) kezelése.

### <a name="broker-support"></a>Bróker támogatás

A 0.3.0-s verziótól kezdődően az MSAL támogatja a Microsoft Authenticator alkalmazással történő felügyelt hitelesítést. A Microsoft Authenticator a feltételes hozzáférési forgatókönyvek támogatását is lehetővé teszi. Feltételes hozzáférés forgatókönyvek közé tartozik az eszköz megfelelőségi szabályzatok, amelyek megkövetelik a felhasználónak, hogy regisztrálja az eszközt az Intune-on keresztül, vagy regisztráljon az AAD-hoz egy jogkivonat beolvasása. És a mobilalkalmazás-kezelés (MAM) feltételes hozzáférési szabályzatok, amelyek megkövetelik a megfelelőség igazolását, mielőtt az alkalmazás kaphat egy jogkivonatot.

Ahhoz, hogy bróker az alkalmazás:

1. Regisztráljon egy brókerkompatibilis átirányítási URI-formátumot az alkalmazáshoz. A bróker kompatibilis átirányítási `msauth.<app.bundle.id>://auth`URI formátum . Cserélje `<app.bundle.id>` le az alkalmazás csomagazonosítóját. Ha az ADAL-ról migrál, és az alkalmazás már képes brókerképes, nincs semmi extra, amit tennie kell. A korábbi átirányítási URI teljes mértékben kompatibilis az MSAL-lal, így a 3.

2. Adja hozzá az alkalmazás átirányítási URI-sémát az info.plist fájlhoz. Az alapértelmezett MSAL átirányítási URI `msauth.<app.bundle.id>`esetén a formátum a . Példa:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Adja hozzá a következő sémákat az alkalmazás Info.p listájához az LSApplicationQueriesSchemes csoportban:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Adja hozzá a következőket az AppDelegate.m fájlhoz a visszahívások kezeléséhez: C célkitűzés:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Vállalatközi (B2B)

Az ADAL-ban külön példányokat hoz létre minden olyan `ADAuthenticationContext` bérlőhöz, amelyhez az alkalmazás jogkivonatokat kér. Ez már nem követelmény az MSAL-ban. Az MSAL-ban létrehozhat egy `MSALPublicClientApplication` példányt, és használhatja azt bármely AAD-felhőhöz és szervezethez, ha egy másik jogosultságot ad meg a acquireToken és acquireTokenSilent hívásokhoz.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO más SDK-kkal együttműködve

Az iOS MSAL egységes gyorsítótáron keresztül érhető el a következő SDK-kkal:

- ADAL Objektív-C 2.7.x+
- MSAL.NET Xamarin 2.4.x+
- ADAL.NET Xamarin 4.4.x+

Az SSO az iOS kulcskarika-megosztáson keresztül érhető el, és csak az ugyanattól az Apple Developer-fiókból közzétett alkalmazások között érhető el.

Az sso az iOS kulcskarika-megosztáson keresztül az egyetlen csendes SSO-típus.

MacOS rendszeren az MSAL más MSAL iOS és macOS alapú alkalmazásokkal és ADAL Objective-C alapú alkalmazásokkal is elérhető.

Az iOS-es MSAL két másik típusú sso-t is támogat:

* SSO a webböngészőn keresztül. Az MSAL for `ASWebAuthenticationSession`iOS támogatja az SSO-t az eszközön lévő más alkalmazások és különösen a Safari böngésző között megosztott cookie-kon keresztül.
* SSO egy hitelesítési brókeren keresztül. IOS-eszközön a Microsoft Authenticator hitelesítési brókerként működik. Feltételes hozzáférési szabályzatok, például egy megfelelő eszköz megkövetelése, és a regisztrált eszközök sso biztosít. A 0.3.0-s verzióval kezdődő MSAL SDK-k alapértelmezés szerint támogatják a brókert.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

Az [Intune MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started) támogatja az MSAL-t iOS-hez a [11.1.2-es](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2) verziótól kezdve

## <a name="msal-and-adal-in-the-same-app"></a>MSAL és ADAL ugyanabban az alkalmazásban

Az ADAL 2.7.0-s és újabb verziója nem létezhet együtt az MSAL-lal ugyanabban az alkalmazásban. Ennek fő oka a közös almodul közös kódja. Mivel a C célkitűzés nem támogatja a névtereket, ha ADAL és MSAL keretrendszereket is hozzáad az alkalmazáshoz, akkor ugyanannak az osztálynak két példánya lesz. Nincs garancia arra, hogy melyiket választják ki futásidőben. Ha mindkét SDK az ütköző osztály ugyanazon verzióját használja, az alkalmazás továbbra is működhet. Ha azonban ez egy másik verzió, az alkalmazás váratlan összeomlásokat tapasztalhat, amelyeket nehéz diagnosztizálni.

Az ADAL és az MSAL futtatása ugyanabban az éles alkalmazásban nem támogatott. Ha azonban csak teszteli és áttelepíti a felhasználókat az ADAL Objective-C-ről az MSAL-ra iOS és macOS rendszeren, továbbra is használhatja [az ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10)használatát. Ez az egyetlen verzió, amely együttműködik az MSAL-lal ugyanabban az alkalmazásban. Ehhez az ADAL-verzióhoz nem lesznek új szolgáltatásfrissítések, ezért csak áttelepítési és tesztelési célokra használható. Az alkalmazás nem támaszkodhat az ADAL és az MSAL hosszú távú együttélésére.

Az ADAL és az MSAL együttélése ugyanabban az alkalmazásban nem támogatott.
Az ADAL és az MSAL egymás mellett élése több alkalmazás között teljes mértékben támogatott.

## <a name="practical-migration-steps"></a>Gyakorlati áttelepítési lépések

### <a name="app-registration-migration"></a>Alkalmazásregisztráció áttelepítése

Nem kell módosítania a meglévő AAD-alkalmazást az MSAL-ra való váltáshoz és az AAD-fiókok engedélyezéséhez. Ha azonban az ADAL-alapú alkalmazás nem támogatja a felügyelt hitelesítést, regisztrálnia kell egy új átirányítási URI-t az alkalmazáshoz, mielőtt átválthatna az MSAL-ra.

Az átirányítási URI-nak a `msauth.<app.bundle.id>://auth`következő formátumban kell lennie: . Cserélje `<app.bundle.id>` le az alkalmazás csomagazonosítóját. Adja meg az átirányítási URI-t az [Azure Portalon.](https://aka.ms/MobileAppReg)

Csak iOS esetén a tanúsítványalapú hitelesítés támogatásához egy további átirányítási URI-t kell regisztrálni az `msauth://code/<broker-redirect-uri-in-url-encoded-form>`alkalmazásban és az Azure Portalon a következő formátumban: . Például: `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Azt javasoljuk, hogy minden alkalmazás regisztrálja mindkét átirányítási URI-k.

Ha szeretné, hogy a növekményes hozzájárulás támogatása, válassza ki az API-k és engedélyek az alkalmazás úgy van beállítva, hogy hozzáférést kérjen az alkalmazás regisztrációja az **API-engedélyek** lapon.

Ha Az ADAL-ról való áttelepítést, és az AAD- és MSA-fiókokat is támogatni szeretné, a meglévő alkalmazásregisztrációt frissíteni kell, hogy mindkettőt támogassa. Nem javasoljuk, hogy frissítse a meglévő éles alkalmazást, hogy azonnal támogassa az AAD-t és az MSA-t. Ehelyett hozzon létre egy másik ügyfélazonosítót, amely támogatja mind az AAD, mind az MSA tesztelési, és miután ellenőrizte, hogy minden forgatókönyv működik, frissítse a meglévő alkalmazást.

### <a name="add-msal-to-your-app"></a>MSAL hozzáadása az alkalmazáshoz

Az MSAL SDK-t hozzáadhatja az alkalmazáshoz a kívánt csomagkezelő eszközzel. Lásd [a részletes utasításokat itt](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Az alkalmazás Információs.plist fájljának frissítése

Csak iOS rendszerben adja hozzá az alkalmazás átirányítási URI-sémát az info.plist fájlhoz. Az ADAL bróker kompatibilis alkalmazások, meg kell már ott. Az alapértelmezett MSAL átirányítási URI-séma a következő formátumú lesz: `msauth.<app.bundle.id>`.  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Adja hozzá a következő sémákat `LSApplicationQueriesSchemes`az alkalmazás info.plistájához a csoportban.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Az AppDelegate-kód frissítése

Csak iOS rendszerben adja hozzá a következőket az AppDelegate.m fájlhoz:

C célkitűzés:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Ha Xcode 11-et használ,** akkor az MSAL visszahívását a `SceneDelegate` fájlba kell helyeznie.
Ha támogatja mind az UISceneDelegate, mind az UIApplicationDelegate programot a régebbi iOS rendszerrel való kompatibilitásérdekében, az MSAL-visszahívást mindkét fájlba be kell helyezni.

C célkitűzés:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

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

Ez lehetővé teszi az MSAL számára a bróker és a webes összetevő válaszainak kezelését.
Erre nem volt szükség az ADAL-ban, mivel automatikusan "swizzled" alkalmazásdelegálási módszereket használ. Hozzáadása manuálisan kevésbé hibahajlamos, és ad az alkalmazás több ellenőrzést.

### <a name="enable-token-caching"></a>Token-gyorsítótárazás engedélyezése

Alapértelmezés szerint az MSAL gyorsítótárazza az alkalmazás tokenjeit az iOS vagy a macOS kulcskarikában. 

Token-gyorsítótárazás engedélyezése:
1. Az alkalmazás megfelelő aláírása
2. Nyissa meg az Xcode Projektbeállítások > **képességek lapot** > **Kulcskarika megosztásának engedélyezése**
3. Kattintson **+** a következő Kulcskarika csoportok bejegyzésre, és `com.microsoft.adalcache` írja be a következő **billentyűt:** 3.a IOS esetén írja be a 3.b A macOS-t írja be`com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Hozzon létre MSALPublicClientApplication és váltson a acquireToken és acquireTokeSilent hívások

A következő `MSALPublicClientApplication` kóddal hozhat létre:

C célkitűzés:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Ezután hívja meg a fiókkezelő API-t, hogy lássa, vannak-e fiókok a gyorsítótárban:

C célkitűzés:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

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



vagy olvassa el az összes beszámolót:

C célkitűzés:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Ha talál fiókot, hívja `acquireTokenSilent` meg az MSAL API-t:

C célkitűzés:

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

Swift:

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



## <a name="next-steps"></a>További lépések

További információ a [hitelesítési folyamatokról és az alkalmazásforgatókönyvekről](authentication-flows-app-scenarios.md)
