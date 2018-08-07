---
title: IOS-es adal-t használó alkalmazások közötti SSO engedélyezése |} A Microsoft Docs
description: 'Hogyan használható az ADAL SDK funkcióit az egyszeri bejelentkezés engedélyezése az alkalmazások között. '
services: active-directory
author: CelesteDG
manager: mtillman
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/07/2017
ms.author: celested
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 203cb4f57cfa50a17b66a9b70a44568e57ec4835
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581650"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>IOS-es adal-t használó alkalmazások közötti SSO engedélyezése

Biztosítása, hogy a felhasználók csak egyszer adja meg a hitelesítő adataikat, és ezeket a hitelesítő adatokat, hogy automatikusan kell közötti használathoz egyszeri bejelentkezéses (SSO) alkalmazások már ügyfelek általi várható. A nehézség a felhasználónevüket és jelszavukat módosítani kis képernyőn, gyakran alkalommal kombinálva egy további tényezőt (2FA) például telefonhívást vagy a rendszer kódot, gyors elégedetlenséget, ha egy felhasználó eredményez rendelkezik ehhez a termékhez egynél több alkalommal.

Emellett ha olyan más alkalmazások is használhatnak, például a Microsoft Accounts vagy egy munkahelyi fiókot az Office 365 identity platform alkalmaz, ügyfelek várható, hogy azokat, függetlenül attól, gyártó az alkalmazások közötti használható hitelesítő adatokat.

A Microsoft Identity platform, a Microsoft Identity SDK-k, valamint az összes a nehezét nem az Ön számára, és teszi lehetővé a lenyűgöző SSO-val az ügyfelek vagy a saját alkalmazások suite belül vagy a közvetítő képesség és a hitelesítő alkalmazások, a teljes eszköz között.

Ez a forgatókönyv megtudhatja, hogyan konfigurálhatja az SDK ezt az előnyt biztosít az ügyfelek számára az alkalmazáson belül.

Ez a forgatókönyv a következőkre vonatkozik:

* Azure Active Directory
* Azure Active Directory B2C
* Az Azure Active Directory B2B
* Azure Active Directory feltételes hozzáférés

Az előző dokumentum céljából feltételezzük, hogy tudja, hogyan [üzembe alkalmazásokat a régi portálon az Azure Active Directory](active-directory-how-to-integrate.md) , és beépíthető az alkalmazásokba a [Microsoft Identity iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>A Microsoft Identity platform SSO-fogalmak

### <a name="microsoft-identity-brokers"></a>A Microsoft Identitásszervezői

A Microsoft, amely lehetővé teszi az adatközponthíd-képzés hitelesítő adatokat a különböző alkalmazások különböző szállítóktól származó alkalmazások minden mobilplatformra biztosít, és lehetővé teszi, hogy speciális amelyek speciális funkcióival megkövetelhető egy adott biztonságos hely, ahonnan a hitelesítő adatok ellenőrzése. Ezeket nevezzük **közvetítők**. IOS és Android rendszeren ezek közvetítők szolgáltatáson keresztül letölthető alkalmazás, hogy ügyfeleink egymástól függetlenül telepítése vagy egy vállalat vagy azok egy részét az eszköz az alkalmazott felügyelő lehet leküldeni az eszközre. Ezeket a közvetítők, és az egyes alkalmazások vagy a rendszergazdák kívánják alapján a teljes eszköz kezelése biztonsági támogatja. A Windows Ez a funkció-szervezőként ismert szakszóval Webeshitelesítés-szervező a az operációs rendszer beépített biztosítja.

Olvassa el a további információ a közvetítők felhasználási módját, és hogyan az ügyfelek előfordulhat, hogy azok megjelenjenek a bejelentkezési folyamatot a Microsoft Identity platform.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>A mobil eszközökön naplózási minták

Hitelesítő adatok az eszközökön való hozzáférés a Microsoft Identity platform két alapvető mintázatokból kövesse:

* Nem közvetített személyes bejelentkezések
* A közvetítő személyes bejelentkezések

#### <a name="non-broker-assisted-logins"></a>Nem közvetített személyes bejelentkezések

Nem közvetített személyes bejelentkezések olyan bejelentkezési élményt, amely beágyazott az alkalmazással együtt történik, és a helyi tároló használata az eszközön, az adott alkalmazáshoz. Ez a tároló előfordulhat, hogy az alkalmazások megoszthatók, de a hitelesítő adatokat az alkalmazás vagy a hitelesítő adatok használatával alkalmazáscsomagunkkal szorosan vannak kötve. Nagy valószínűséggel tapasztal Ez sok mobilalkalmazások a felhasználónév és jelszó belül magának az alkalmazásnak megadásakor.

Ezek a bejelentkezések rendelkezik a következő előnyökkel jár:

* Felhasználói élmény létezik-e az alkalmazás egészében.
* Hitelesítő adatok, amelyek ugyanazt a tanúsítványt, egyszeri bejelentkezés biztosítása érdekében az alkalmazások Suite által aláírt alkalmazások között is megoszthatók.
* A kérelem előtt és után jelentkezzen be a vezérlő körül a bejelentkezési élmény biztosítunk.

Ezek a bejelentkezések a következő hátrányokkal rendelkezik:

* Felhasználó minden olyan alkalmazás, amely csak azokat, amelyek az alkalmazás konfigurálva van a Microsoft Identities használja a Microsoft Identity nem egyszeri bejelentkezéses terén.
* Az alkalmazás az Intune-ban termékcsomagot használja, illetve összetettebb üzleti funkciók, például a feltételes hozzáférés nem használható.
* Az alkalmazás nem támogatja a tanúsítványalapú hitelesítést az üzleti felhasználóknak.

Íme egy ábrázolása a Microsoft Identity SDK-k az alkalmazások egyszeri bejelentkezés engedélyezéséhez a megosztott tárolóhellyel rendelkező működése:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAK SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>A közvetítő személyes bejelentkezések

Bejelentkezések Broker támogatású olyan bejelentkezési élmény a közvetítő alkalmazás belül, és a storage és a közvetítő biztonsági hitelesítő adatok megosztása az eszközön minden olyan alkalmazás, amely a alkalmazni a Microsoft Identity platform használatával. Ez azt jelenti, hogy az alkalmazások támaszkodnak, jelentkezzen be a felhasználók a közvetítőn. IOS és Android rendszeren ezeket a közvetítők letölthető alkalmazásokkal, hogy ügyfeleink egymástól függetlenül telepítése vagy leküldeni az eszközre által biztosított egy olyan cég, az eszközt a felhasználó kezeli. Ez az alkalmazástípus egyik példája a Microsoft Authenticator alkalmazás IOS rendszerű eszközökön. Ezt a funkciót a Windows-szervezőként ismert szakszóval Webeshitelesítés-szervező a az operációs rendszer beépített biztosítja.
A felhasználói élményt platformonként és néha a felhasználók számára zavaró lehet ha nem megfelelően kezeli. Ismerkedjen meg valószínűleg legtöbb ezt a mintát, ha a Facebook-alkalmazás telepítve van, és a Facebook Connect használata egy másik alkalmazás. A Microsoft Identity platform hasonló mintát alkalmaz.

Ez egy "átmeneti" vezet iOS-es animáció, ahol az alkalmazás, miközben a Microsoft Authenticator alkalmazást a háttérben zajlik az előtérben, a felhasználó számára, válassza ki a bejelentkezéshez szeretnék, hogy melyik fiók származnak. 

Android és Windows, a fiók kiválasztásakor az alkalmazás fölött jelenik meg, amelyek kevésbé zavaró a felhasználónak.

#### <a name="how-the-broker-gets-invoked"></a>Hogyan a közvetítő meghívása beolvasása

Ha az eszközt, például a Microsoft Authenticator alkalmazás kompatibilis ügynök telepítve van a Microsoft Identity SDK-k automatikusan elvégzi az Ön számára a közvetítő meghívása, amikor egy felhasználó azt jelzi, hogy jelentkezzen be a Microsoft minden olyan fiók használatával történő munkájának Identity platform. Ez a fiók lehet a személyes Microsoft-Account, egy munkahelyi vagy iskolai fiókját, vagy egy Ön által megadott fiók és az Azure-ban a B2C-vel és a B2B termékeink gazdagép.

#### <a name="how-we-ensure-the-application-is-valid"></a>Hogyan biztosítható az alkalmazás érvénytelen.
 
 Az identitás-átvitelszervező kínálunk biztonsági elengedhetetlen a közvetítő alkalmazás hívás szükséges bejelentkezések segítik. IOS és Android nem biztonsága ne sérüljön egyedi azonosítók, amelyek csak egy adott alkalmazás esetében érvényes, így rosszindulatú alkalmazások, előfordulhat, hogy "agresszívebb" egy megbízható alkalmazás azonosítója és a megbízható alkalmazás webszolgáltatásokban a jogkivonatokat fogadni. Annak érdekében, hogy mindig a megfelelő alkalmazás a futásideje kommunikál, megkérjük, ha az alkalmazás regisztrálása a Microsoft egyéni redirectURI a fejlesztő. **Az alábbiakban tárgyalt hogyan a fejlesztők formázhatják az átirányítási URI-t kell.** Az egyéni redirectURI tartalmazza az alkalmazás Csomagazonosítóját, és az Apple App Store által biztosított az alkalmazás egyedinek kell lennie. Amikor egy alkalmazás a közvetítő hív, a közvetítő megkérdezi, hogy biztosítsa az alkalmazáscsomag-Azonosítót, amelynek a neve a közvetítő számára az iOS operációs rendszer. A közvetítő az Alkalmazásköteg-azonosító biztosít a Microsoft identity rendszerben hívásában. Ha az alkalmazás Csomagazonosítóját nem egyezik meg a regisztráció során fejlesztője által készített velünk a kapcsolatot az Alkalmazásköteg-azonosító, hogy megtagadja a jogkivonatok hozzáférést az erőforráshoz, az alkalmazás által kért. Ez az ellenőrzés biztosítja, hogy csak az alkalmazás fejlesztője által regisztrált jogkivonatokat kap-e.

**A fejlesztői lehetősége van, ha a Microsoft Identity SDK meghívja a közvetítő, vagy a nem közvetített személyes folyamatot használ.** Azonban ha a fejlesztői úgy dönt, hogy nem használja a közvetítő támogatású folyamat megszakad az egyszeri bejelentkezés használatának előnye, hogy a felhasználó már felvett előfordulhat, hogy az eszköz hitelesítő adatait, és megakadályozza, hogy a Microsoft biztosít üzleti funkciókat használja el az alkalmazását a ügyfelek, például a feltételes hozzáférés Intune-felügyeleti képességek és Tanúsítványalapú hitelesítés.

Ezek a bejelentkezések rendelkezik a következő előnyökkel jár:

* Felhasználói függetlenül attól, gyártó az alkalmazások közötti SSO teljesen.
* Az alkalmazás összetettebb üzleti funkciók, például a feltételes hozzáférés vagy az Intune-ban termékcsomagot használhatja.
* Az alkalmazás az üzleti felhasználók is támogatja a Tanúsítványalapú hitelesítés.
* A közvetítő alkalmazás további biztonsági algoritmusokkal és a titkosítás sokkal biztonságosabb bejelentkezés rendelkezik, mint az alkalmazás és a felhasználó identitásának ellenőrzése.

Ezek a bejelentkezések a következő hátrányokkal rendelkezik:

* Az iOS-ben a felhasználó továbbítjuk kívül az alkalmazás felhasználói élményt közben a hitelesítő adatok közül választ.
* Lehetővé teszi a bejelentkezési élmény az ügyfeleknek az alkalmazáson belül kezelését elvesztését.

Íme egy ábrázolása a Microsoft Identity SDK-k működését a közvetítő alkalmazásokkal való egyszeri bejelentkezés engedélyezése:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

Képességekkel, jobb megismerésében és egyszeri bejelentkezés megvalósítása az alkalmazásban, a Microsoft Identity platform és az SDK-k használatával képesnek kell lennie a háttér-információkat.

## <a name="enabling-cross-app-sso-using-adal"></a>Adal-t használó alkalmazások közötti SSO engedélyezése

Itt az ADAL iOS SDK-t használjuk az:

* Nem közvetített személyes egyszeri bejelentkezés az alkalmazások a suite bekapcsolása
* Kapcsolja be a közvetítő támogatású egyszeri bejelentkezés támogatása

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Nem közvetített egyszeri Bejelentkezést bekapcsolás segítik az egyszeri bejelentkezés

Nem közvetített személyes egyszeri bejelentkezéshez különböző alkalmazások a Microsoft Identity SDK-k kezelése nagy része az egyszeri bejelentkezés összetettsége az Ön számára. Ez magában foglalja a megfelelő felhasználói keresése a gyorsítótárban, és karbantartása, hogy a lekérdezés bejelentkezett felhasználók listáját.

Alkalmazások közötti SSO engedélyezése saját kell tegye a következőket:

1. Az azonos ügyfél-azonosító vagy alkalmazásazonosítót, győződjön meg arról, hogy az alkalmazások az összes felhasználó
2. Győződjön meg arról, hogy ugyanazt a aláíró tanúsítványt az Apple-től az összes alkalmazás megosztott, hogy keychains megoszthatja
3. Az alkalmazások mindegyike ugyanazon kulcslánc jogosultság kérelmet.
4. Mutassa be a Microsoft Identity SDK-k a megosztott kulcslánc azt szeretné, hogy mi legyen.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Az azonos Ügyfélazonosítót használó / alkalmazást az alkalmazások a csomagban található összes alkalmazás AZONOSÍTÓJÁT

Ahhoz, hogy a Microsoft Identity platform tudnia, hogy engedélyezett jogkivonatok megosztása az alkalmazások az alkalmazások mindegyike kell megosztani az azonos ügyfél-azonosító vagy alkalmazás azonosítóját. Ez az Ön számára a portálon az első alkalmazás regisztrációja során megadott egyedi azonosítója.

Megfordulhat, hogyan határozható meg a Microsoft Identity Service különböző alkalmazásokat Ha használja az ugyanazon alkalmazás azonosítóját. A válasz a rendszer a **átirányítási URI-k**. Minden alkalmazás több átirányítási URI-k a felvételi portálra a regisztrálni lehet. Minden alkalmazás a csomagban található különböző átirányítási URI-t tartalmaz. Ez kinézetét egy példa alatt van:

Az App1 átirányítási URI-ja: `x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 átirányítási URI-ja: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 átirányítási URI-ja: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Ezek azonos Ügyfélazonosítót vannak beágyazva / alkalmazás azonosítója és az átirányítási URI-t, vissza velünk a kapcsolatot az SDK konfigurációjában alapján kulcskeresési.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Vegye figyelembe, hogy az átirányítási URI-k formátuma alább kifejtett. Használhat bármilyen átirányítási URI-t, ha kíván támogatni a közvetítő ebben az esetben akkor kell megjelenése a fenti*

#### <a name="create-keychain-sharing-between-applications"></a>Hozzon létre a kulcslánc megosztása az alkalmazások között

A kulcslánc megosztása a dokumentum nem terjed, és a dokumentum az Apple által kezelt [Adding Capabilities](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Fontos, hogy úgy dönt, hogy mi a meghívandó kulcslánc, és adja hozzá ezt a képességet az alkalmazások közötti.

Ha jogosultságokat állítsa be a megfelelő, tekintse meg a egy fájlt a projekt könyvtárában jogosult `entitlements.plist` , amely tartalmazza a hiba, amely a következőhöz hasonló:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

A kulcslánc-jogosultság engedélyezve van az alkalmazások minden rendelkezik, és készen áll az egyszeri bejelentkezés használata után mutassa be a Microsoft Identity SDK a kulcskarikában a következő beállítás használatával a `ADAuthenticationSettings` a következő beállítással:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Ha a kulcslánc megosztása az alkalmazások bármely alkalmazás törölheti felhasználók vagy rosszabb összes jogkivonatok az alkalmazásról. Ez akkor különösen katasztrofális, ha a háttérben futó munkáját a jogkivonatokat használó alkalmazások. Kulcslánc megosztása azt jelenti, hogy minden a nagyon gondosan kell eltávolítási műveletek a Microsoft Identity SDK-inak használatával.

Ennyi az egész! A Microsoft Identity SDK mostantól fájlmegosztási hitelesítő adatokat az alkalmazások közötti. A Felhasználólista alkalmazáspéldányok között is megosztható.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Egyszeri bejelentkezés bekapcsolásával Broker támogatott egyszeri bejelentkezés

Az, hogy az alkalmazás számára az eszközön telepített bármely brokert **alapértelmezés szerint ki van kapcsolva**. A közvetítő alkalmazás használatához néhány további konfigurációs lehetőségek és kód hozzáadása az alkalmazáshoz.

A követendő lépések a következők:

1. Az MS SDK az alkalmazás kódjában hívásában broker mód engedélyezéséhez.
2. Hozzon létre egy új átirányítási URI-t, és adja meg, hogy az alkalmazás és az alkalmazás regisztrációját is.
3. Regisztrálás egy URL-sémát.
4. iOS9 támogatási: engedélyt ad hozzá az info.plist fájlban.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>1. lépés: Az alkalmazás átvitelszervező-mód engedélyezése

Lehetővé teszi, hogy az alkalmazás a közvetítő használata be van kapcsolva a "környezet" vagy a kezdeti beállítás és a hitelesítési objektum létrehozásakor. Ehhez beállítás a hitelesítő adatok típusa a kódban:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
A `AD_CREDENTIALS_AUTO` beállítás lehetővé teszi a Microsoft Identity SDK, próbálja a közvetítőn meghívni `AD_CREDENTIALS_EMBEDDED` megakadályozza, hogy a Microsoft Identity SDK hívása a közvetítőn.

#### <a name="step-2-registering-a-url-scheme"></a>2. lépés: Egy URL-sémát regisztrálása

A Microsoft Identity platform indítja el a közvetítő, és visszatér a vezérlő az alkalmazásnak URL-címeket használ. Befejezés adott adatváltási egy URL-sémát, a Microsoft Identity platform ismert lesz az alkalmazás regisztrálva van szükség. Ez lehet kívül más alkalmazás rendszerek lehet, hogy korábban már regisztrált az alkalmazását.

> [!WARNING]
> Azt javasoljuk, így az URL-séma viszonylag egyedi, a szoftver-és a egy másik alkalmazás használja az ugyanazon URL-séma minimalizálása érdekében. Apple nem kényszeríti ki az app store-ban regisztrált URL-sémákat egyediségét.
> 
> 

Alább egy példát, hogyan Ez jelenik meg a projekt konfigurációs van. Előfordulhat, hogy még ehhez az xcode-ban is:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>3. lépés: Új átirányítási URI az az URL-séma létrehozása

Győződjön meg arról, hogy a Microsoft bármikor visszatérhet a hitelesítőadat-jogkivonatokat a megfelelő alkalmazáshoz, kell, hogy vissza úgy, hogy az iOS operációs rendszer ellenőrizheti az alkalmazás nevezzük. Az iOS operációs rendszer a jelentések a Microsoft-közvetítő alkalmazások hívása, az alkalmazás Csomagazonosítóját. Ez a nem egy engedélyezetlen alkalmazás megtévesztésre. Ezért azt használja ki a Ez az URI-t, győződjön meg arról, hogy a megfelelő alkalmazáshoz a jogkivonatokat a rendszer visszairányítja a közvetítő alkalmazás együtt. Kérjük, az egyedi átirányítási URI-t mind az alkalmazás és átirányítási URI-t a fejlesztői portálon állítja be.

Az átirányítási URI-t, a megfelelő formátumban kell lennie:

`<app-scheme>://<your.bundle.id>`

például: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Az átirányítási URI-t kell megadni a regisztráció segítségével a [az Azure portal](https://portal.azure.com/). További információ az Azure AD-alkalmazás regisztrációjának: [integrálása az Azure Active Directoryval](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>3a. lépés: Adjon hozzá egy átirányítási URI-t az alkalmazás és a fejlesztői portál támogatja a Tanúsítványalapú hitelesítés

Támogatás Tanúsítványalapú hitelesítés egy második "msauth" regisztrálva kell lennie az alkalmazás és a [az Azure portal](https://portal.azure.com/) Tanúsítványalapú hitelesítés kezelésére, ha szeretné, hogy az alkalmazás hozzáadása.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

például: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>4. lépés: iOS9: egy konfigurációs paraméter hozzáadása az alkalmazáshoz

Adal-t használ – canOpenURL: Ellenőrizze, hogy ha az ügynök telepítve van-e az eszközön. IOS 9-es Apple zárolva mi kérdezhetők le egy rendszerek. Az Info.plist szakaszt az "msauth" hozzá kell adnia a `info.plist file`.

<key>LSApplicationQueriesSchemes</key> <array> <string>msauth</string></array>

### <a name="youve-configured-sso"></a>Egyszeri bejelentkezés konfigurálása!

Most már a Microsoft Identity SDK automatikusan is hitelesítő adatok megosztása az alkalmazások és a közvetítő meghívása az eszközön telepítve, ha.