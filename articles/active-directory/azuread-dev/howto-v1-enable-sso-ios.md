---
title: Hogyan lehet engedélyezni az alkalmazások közötti egyszeri szolgáltatásokat az iOS-en az ADAL használatával | Microsoft dokumentumok
description: Az ADAL SDK funkcióinak használata az egyszeri bejelentkezés engedélyezéséhez az alkalmazásokban.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 082cbb931c9dae60b39f9ee5323337bf051fb56d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154780"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Útmutató: Alkalmazásközi egyszeri szolgáltatások engedélyezése iOS rendszeren az ADAL használatával

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók számára, hogy csak egyszer adja meg a hitelesítő adatait, és ezeket a hitelesítő adatokat automatikusan működik az alkalmazások között, és a platformok között, hogy más alkalmazások is használhatják (például a Microsoft-fiókok vagy a munkahelyi fiók a Microsoft 365) nem a kiadótól.

A Microsoft identitásplatformja az SDK-kkal együtt megkönnyíti az SSO engedélyezését a saját alkalmazáscsomagján belül, vagy a közvetítői képességgel és a hitelesítő alkalmazásokkal az egész eszközön.

Ebben a útmutatóban megtudhatja, hogyan konfigurálhatja az SDK-t az alkalmazáson belül, hogy egyszeri bejelentkezést biztosítson az ügyfeleknek.

Ez a útmutató a következőkre vonatkozik:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Azure Active Directory feltételes hozzáférés

## <a name="prerequisites"></a>Előfeltételek

Ez a how-to feltételezi, hogy tudod, hogyan kell:

* Az alkalmazás kiépítése az Azure AD örökölt portálján. További információ: [App regisztrálása](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Integrálja alkalmazását az [Azure AD iOS SDK-val.](https://github.com/AzureAD/azure-activedirectory-library-for-objc)

## <a name="single-sign-on-concepts"></a>Egyszeri bejelentkezési fogalmak

### <a name="identity-brokers"></a>Identitás brókerek

A Microsoft minden olyan mobilplatformhoz biztosít alkalmazásokat, amelyek lehetővé teszik a hitelesítő adatok áthidalását a különböző szállítóktól származó alkalmazások között, valamint olyan továbbfejlesztett funkciókat, amelyek egyetlen biztonságos helyet igényelnek a hitelesítő adatok érvényesítéséhez. Ezek az úgynevezett **brókerek**.

IOS és Android rendszeren a brókerek olyan letölthető alkalmazásokon keresztül kerülnek rendelkezésre, amelyeket az ügyfelek önállóan telepítenek, vagy egy olyan vállalat tol az eszközre, amely az alkalmazottak számára kezeli az eszközök egy részét vagy egészét. A brókerek támogatják a biztonság kezelését csak bizonyos alkalmazások vagy a teljes eszköz informatikai rendszergazdai konfiguráció alapján. A Windows rendszerben ezt a funkciót az operációs rendszerbe beépített fiókválasztó biztosítja, amelyet technikailag webhitelesítési brókerként ismernek.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>A mobileszközökre való bejelentkezés mintái

Az eszközök hitelesítő adataihoz való hozzáférés két alapvető mintát követ:

* Nem bróker által támogatott bejelentkezések
* Bróker által támogatott bejelentkezések

#### <a name="non-broker-assisted-logins"></a>Nem bróker által támogatott bejelentkezések

A nem közvetítő által támogatott bejelentkezések olyan bejelentkezési élmények, amelyek az alkalmazással összhangban történnek, és az adott alkalmazáshoz az eszközön lévő helyi tárolót használják. Ez a tároló megosztható az alkalmazások között, de a hitelesítő adatok szorosan kötődnek az alkalmazáshoz vagy alkalmazáscsomaghoz az adott hitelesítő adatokhasználatával. Valószínűleg ezt tapasztalta sok mobilalkalmazásban, amikor felhasználónevet és jelszót ad meg magában az alkalmazásban.

Ezek a bejelentkezések a következő előnyökkel járnak:

* A felhasználói élmény teljes egészében az alkalmazáson belül létezik.
* A hitelesítő adatok megoszthatók az ugyanazon tanúsítvánnyal aláírt alkalmazások között, így egyetlen bejelentkezési élményt biztosítva az alkalmazáscsomagszámára.
* A bejelentkezés élményének szabályozása az alkalmazás bejelentkezés előtt és után érhető el.

Ezek a bejelentkezések a következő hátrányai:

* A felhasználók nem tapasztalhatják az egyszeri bejelentkezést a Microsoft-identitást használó összes alkalmazásban, csak az alkalmazás által konfigurált Microsoft-identitások között.
* Az alkalmazás nem használható fejlettebb üzleti funkciókkal, például feltételes hozzáféréssel, és nem használhatja az Intune termékcsomagját.
* Az alkalmazás nem támogatja a tanúsítványalapú hitelesítést az üzleti felhasználók számára.

Az SDK-k az SSO engedélyezéséhez az sso-k közös tárolójával való működését ábrázolják:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Bróker által támogatott bejelentkezések

A bróker által támogatott bejelentkezések olyan bejelentkezési élmények, amelyek a közvetítőalkalmazáson belül fordulnak elő, és a közvetítő tárolóját és biztonságát használják a hitelesítő adatok megosztására az eszközön lévő összes alkalmazás között, amelyek az identitásplatformot alkalmazzák. Ez azt jelenti, hogy az alkalmazások támaszkodnak a bróker, hogy jelentkezzen be a felhasználók. IOS és Android rendszeren ezek a brókerek letölthető alkalmazásokon keresztül kerülnek rendelkezésre, amelyeket az ügyfelek önállóan telepítenek, vagy egy olyan vállalat tolja az eszközre, amely kezeli az eszközt a felhasználó számára. Az ilyen típusú alkalmazások ra van példa a Microsoft Authenticator alkalmazás iOS rendszeren. A Windows rendszerben ezt a funkciót az operációs rendszerbe beépített fiókválasztó biztosítja, amelyet technikailag webhitelesítési brókernek neveznek.

A tapasztalat platformonként változik, és néha zavaró lehet a felhasználók számára, ha nem megfelelően kezelik. Valószínűleg akkor ismeri leginkább ezt a mintát, ha telepítve van a Facebook alkalmazás, és egy másik alkalmazásból használja a Facebook Connect alkalmazást. Az identitásplatform ugyanazt a mintát használja.

IOS esetében ez egy "átmeneti" animációhoz vezet, ahol az alkalmazást a háttérbe küldi, míg a Microsoft Authenticator alkalmazások előtérbe kerülnek, hogy a felhasználó kiválaszthassa, melyik fiókkal szeretne bejelentkezni.

Android és Windows esetén a fiókválasztó az alkalmazás tetején jelenik meg, ami kevésbé zavarja a felhasználót.

#### <a name="how-the-broker-gets-invoked"></a>Hogyan kap a bróker kap meghívni

Ha egy kompatibilis bróker telepítve van az eszközön, mint például a Microsoft Authenticator alkalmazás, az SDK-k automatikusan elvégezik a közvetítő meghívását, amikor a felhasználó jelzi, hogy az identitásplatformbármely fiókjával szeretne bejelentkezni. Ez a fiók lehet egy személyes Microsoft-fiók, egy munkahelyi vagy iskolai fiók, vagy egy fiók, amelyet Ön a B2C és B2B termékeink keletve biztosít és üzemeltet az Azure-ban.

#### <a name="how-we-ensure-the-application-is-valid"></a>Hogyan biztosítjuk a kérelem érvényességét?

Annak szükségességét, hogy biztosítsák a személyazonosságát egy alkalmazás, amely felhívja a bróker elengedhetetlen a biztonság nyújtunk a bróker segített bejelentkezések. Sem az iOS, sem az Android nem kényszeríti ki azokat az egyedi azonosítókat, amelyek csak egy adott alkalmazásra érvényesek, így a rosszindulatú alkalmazások "meghamisulhatnak" egy jogszerű alkalmazás azonosítóján, és megkapják a jogszerű alkalmazásnak szánt jogkivonatokat. Annak érdekében, hogy mindig a megfelelő alkalmazással kommunikálunk futásidőben, kérjük a fejlesztőt, hogy adjon meg egy egyéni redirectURI-t, amikor regisztrálja alkalmazását a Microsoftnál. Az alábbiakban részletesen tárgyaljuk, hogy a fejlesztők hogyan készítsék el ezt az átirányítási URI-t. Ez az egyéni redirectURI tartalmazza az alkalmazás csomagazonosítóját, és az Apple App Store biztosítja, hogy egyedi legyen az alkalmazásban. Amikor egy alkalmazás meghívja a brókert, a bróker megkéri az iOS operációs rendszert, hogy adja meg a közvetítőnek nevezett csomagazonosítót. A bróker biztosítja ezt a csomagazonosítót a Microsoft nak az identitásrendszerünk hívásában. Ha az alkalmazás csomagazonosítója nem egyezik meg a fejlesztő által a regisztráció során megadott csomagazonosítóval, akkor megtagadjuk a hozzáférést az alkalmazás által kért erőforrás jogkivonataihoz. Ez az ellenőrzés biztosítja, hogy csak a fejlesztő által regisztrált alkalmazás kap jogkivonatokat.

**A fejlesztő választhat, hogy az SDK felhívja-e a brókert, vagy a nem közvetítő által támogatott folyamatot használja.** Ha azonban a fejlesztő úgy dönt, hogy nem használja a bróker által támogatott folyamatot, elveszítik az SSO hitelesítő adatok használatának előnyeit, amelyeket a felhasználó esetleg már hozzáadott az eszközön, és megakadályozza, hogy alkalmazásukat olyan üzleti funkciókkal használják, amelyeket a Microsoft például feltételes hozzáférés, Intune-kezelési képességek és tanúsítványalapú hitelesítés.

Ezek a bejelentkezések a következő előnyökkel járnak:

* A felhasználó az összes alkalmazásában sso-t tapasztal, függetlenül a ttól, hogy a szállító.User experiences SSO across all their applications no matter the vendor.
* Az alkalmazás speciálisabb üzleti funkciókat, például feltételes hozzáférést használhat, vagy használhatja az Intune termékcsomagját.
* Az alkalmazás támogatja a tanúsítványalapú hitelesítést az üzleti felhasználók számára.
* Sokkal biztonságosabb bejelentkezési élményt, mint az alkalmazás és a felhasználó identitását a közvetítő alkalmazás további biztonsági algoritmusok és titkosítás ellenőrzése.

Ezek a bejelentkezések a következő hátrányai:

* Az iOS-ben a felhasználó az alkalmazás felhasználói élményéből kikerül, miközben a hitelesítő adatok at választják.
* A bejelentkezési élmény kezelésének elvesztése az alkalmazáson belül az ügyfelek számára.

Itt van egy ábrázolása, hogy az SDK-k működnek együtt a bróker alkalmazások, hogy SSO:

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

## <a name="enabling-cross-app-sso-using-adal"></a>Alkalmazásközi egyszeri szolgáltatások engedélyezése az ADAL használatával

Itt használjuk az ADAL iOS SDK-t:

* A nem közvetítőként támogatott egyszeri szolgáltatás bekapcsolása az alkalmazáscsomaghoz
* A bróker által támogatott egyszeri felsorolt szolgáltatás támogatásának bekapcsolása

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Az Egyszeri sso bekapcsolása nem közvetítő által támogatott egyszeri felruházó számára

A nem közvetítő által támogatott egyszeri felkínálás az alkalmazások között, az SDK-k az Egyszeri sso összetettségének nagy részét kezelik az Ön számára. Ez magában foglalja a megfelelő felhasználó megkeresését a gyorsítótárban, és a bejelentkezett felhasználók listájának karbantartását a lekérdezéshez.

Az SSO engedélyezéséhez a saját alkalmazások között a következőket kell tennie:

1. Győződjön meg arról, hogy minden alkalmazás ugyanazt az ügyfélazonosítót vagy alkalmazásazonosítót használja.
2. Győződjön meg arról, hogy az összes alkalmazás ugyanazt az aláírási tanúsítványt az Apple- től, hogy megoszthassa a kulcsláncokat.
3. Kérje ugyanazt a kulcskarika jogosultságot az egyes alkalmazásokhoz.
4. Tájékoztassa az SDK-kat a megosztott kulcskarikáról, amelyet használni szeretne.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Ugyanazt az ügyfélazonosítót / alkalmazásazonosítót használja az alkalmazáscsomag összes alkalmazásához

Annak érdekében, hogy az identitásplatform tudja, hogy a jogkivonatok megosztása az alkalmazások között, minden egyes alkalmazásnak meg kell osztania ugyanazt az ügyfélazonosítót vagy alkalmazásazonosítót. Ez az az egyedi azonosító, amelyet akkor kapott, amikor regisztrálta az első alkalmazást a portálon.

Az átirányítási URI-k lehetővé teszik, hogy azonosítsa a különböző alkalmazásokat a Microsoft-identitásszolgáltatásszámára, ha ugyanazt az alkalmazásazonosítót használja. Minden alkalmazás rendelkezhet több átirányítási URI-k regisztrálva a bevezetési portálon. A csomag minden alkalmazás a csomag ban lesz egy másik átirányítási URI.Each app in your suite will have a different redirect URI. Egy példa arra, hogyan néz ki az alábbi:

App1 átirányításURI:`x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 átirányításURI:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 átirányításURI:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Ezek ugyanazon ügyfélazonosító / alkalmazásazonosító alá vannak ágyazva, és az SDK-konfigurációban visszaadott átirányítási URI alapján keresnek.

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

Az átirányítási URI-k formátumát az alábbiakban ismertetjük. Bármilyen átirányítási URI-t használhat, hacsak nem kívánja támogatni a brókert, ebben az esetben a fentiekhez hasonlóan kell kinéznie*

#### <a name="create-keychain-sharing-between-applications"></a>Kulcskarika-megosztás létrehozása alkalmazások között

A kulcskarika-megosztás engedélyezése túlmutat a jelen dokumentum hatókörén, és az Apple dokumentumában, [a Capabilities összeadása](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)című dokumentumban szerepel. Az a fontos, hogy eldöntse, mit szeretne hívni a kulcskarikával, és adja hozzá ezt a képességet az összes alkalmazáshoz.

Ha helyesen van beállítva jogosultsága, akkor egy olyan fájlt `entitlements.plist` kell látnia a projektkönyvtárban, amely a következőre hasonlít:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
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

Miután minden alkalmazásban engedélyezve van a kulcskarika jogosultság, és készen áll az Egyszeri bejelentkezés használatára, tájékoztassa az `ADAuthenticationSettings` sdk identitást a kulcskarikáról a következő beállítás sal a következő beállítással:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Ha megosztja a kulcskarikát az alkalmazások között, bármely alkalmazás törölheti a felhasználókat, vagy ami még rosszabb, törölheti az összes jogkivonatot az alkalmazásban. Ez különösen katasztrofális, ha olyan alkalmazásokat, amelyek támaszkodnak a jogkivonatokat, hogy a háttérben munkát. A kulcskarika megosztása azt jelenti, hogy nagyon óvatosnak kell lennie az identitás-SDK-kon keresztül végzett műveletek minden és minden eltávolítása során.

Ennyi az egész! Az SDK mostantól megosztja a hitelesítő adatokat az összes alkalmazásban. A felhasználói lista az alkalmazáspéldányok között is meg lesz osztva.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Az SSO bekapcsolása a közvetítő által támogatott SSO-hoz

Az alkalmazás az eszközre telepített brókerek használatára vonatkozó lehetősége alapértelmezés szerint ki van **kapcsolva.** Annak érdekében, hogy használja az alkalmazást a bróker meg kell tennie néhány további konfigurációt, és adjunk hozzá néhány kódot az alkalmazáshoz.

A következő lépések a következők:

1. Engedélyezze a közvetítői módot az alkalmazáskód MS SDK-hoz intézett hívásában.
2. Hozzon létre egy új átirányítási URI-t, és biztosítsa mind az alkalmazás, mind az alkalmazás regisztrációja számára.
3. URL-séma regisztrálása.
4. Adjon engedélyt az info.plist fájlhoz.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>1. lépés: Bróker mód engedélyezése az alkalmazásban

Az alkalmazás a közvetítő használatára való képessége be van kapcsolva, amikor létrehozza a hitelesítési objektum "környezetét" vagy kezdeti beállítását. Ezt úgy teszi, hogy beállítja a hitelesítő adatok típusát a kódba:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
A `AD_CREDENTIALS_AUTO` beállítás lehetővé teszi, hogy az SDK `AD_CREDENTIALS_EMBEDDED` megpróbálja felhívni a bróker, megakadályozza, hogy az SDK hívja a bróker.

#### <a name="step-2-registering-a-url-scheme"></a>2. lépés: URL-séma regisztrálása

Az identitásplatform URL-címeket használ a közvetítő meghívásához, majd visszaadja a vezérlést az alkalmazásnak. Az oda-vissza út befejezéséhez szüksége van egy URL-séma regisztrálva az alkalmazáshoz, amelyről az identitásplatform tudni fog. Ez lehet az alkalmazással korábban regisztrált egyéb alkalmazássémák mellett.

> [!WARNING]
> Azt javasoljuk, hogy az URL-séma meglehetősen egyedi, hogy minimálisra csökkentsék az esélyét egy másik alkalmazás ugyanazt az URL-sémát. Az Apple nem érvényesíti az alkalmazásboltban regisztrált URL-sémák egyediségét.

Az alábbi példa bemutatja, hogyan jelenik meg ez a projektkonfigurációban. Ezt az XCode-ban is megteheti:

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

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>3. lépés: Új átirányítási URI létrehozása az URL-sémával

Annak érdekében, hogy mindig visszaadjuk a hitelesítő adatok tokenjeit a megfelelő alkalmazásnak, meg kell győződnünk arról, hogy visszahívjuk az alkalmazást oly módon, hogy az iOS operációs rendszer ellenőrizhesse. Az iOS operációs rendszer jelenti a Microsoft brókeralkalmazásoknak az azt hívó alkalmazás bundle azonosítóját. Ezt nem hamisítható meg egy szélhámos alkalmazás. Ezért ezt a közvetítői alkalmazás URI-jával együtt kihasználjuk annak érdekében, hogy a tokenek a megfelelő alkalmazásba kerüljenek vissza. Ezt az egyedi átirányítási URI-t mind az alkalmazásban, mind pedig a fejlesztői portálon átirányítási URI-ként kell beállítania.

Az átirányítási URI-nak a következő formában kell lennie:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Ezt az átirányítási URI-t meg kell adni az alkalmazás regisztrációjában az [Azure Portal](https://portal.azure.com/)használatával. Az Azure AD-alkalmazások regisztrációjáról az [Integráció az Azure Active Directoryval](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)című témakörben talál további információt.

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>3a. lépés: Átirányítási URI hozzáadása az alkalmazáshoz és a fejlesztői portálhoz a tanúsítványalapú hitelesítés támogatásához

A tanúsítványalapú hitelesítés támogatásához egy második "msauth" kell regisztrálni az alkalmazás és az [Azure Portal](https://portal.azure.com/) on tanúsítvány-hitelesítés kezelésére, ha szeretné hozzáadni, hogy a támogatás az alkalmazáshoz.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

pl.: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>4. lépés: Konfigurációs paraméter hozzáadása az alkalmazáshoz

ADAL használ -canOpenURL: annak ellenőrzésére, hogy a bróker telepítve van az eszközön. Az iOS 9-ben az Apple zárolta, hogy egy alkalmazás milyen sémákat kérdezhet le. Hozzá kell adnia az "msauth" szót a rendszer `info.plist file`LSApplicationQueriesSchemes szakaszához.

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Beállította az SSO-t!

Most az identitás SDK automatikusan megosztja hitelesítő adatait az alkalmazások között, és meghívja a közvetítő, ha jelen van az eszközön.

## <a name="next-steps"></a>További lépések

* Tudnivalók [az egyszeri bejelentkezési SAML protokollról](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
