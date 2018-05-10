---
title: Az adal-t használó iOS alkalmazások közötti SSO engedélyezése |} Microsoft Docs
description: 'Hogyan szolgáltatásait is használni az ADAL SDK való egyszeri bejelentkezés engedélyezése az alkalmazások között. '
services: active-directory
author: xerners
manager: mtillman
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: c477e7e6fb9ef9a93ceab53c1427b974bab99b37
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Az adal-t használó iOS alkalmazások közötti SSO engedélyezése
Megadása, hogy a felhasználók csak egyszer adja meg a hitelesítő adataikat, és ezen hitelesítő adatok automatikusan rendelkezésére kell közötti használathoz egyszeri bejelentkezés (SSO) alkalmazások már várt ügyfelek. Nehézsége kis képernyőjű, gyakran alkalommal együtt egy további tényezőt (2FA) például telefonhívást vagy egy fogva kódot, a felhasználónév és jelszó megadása ehhez a termékhez egynél többször van gyors kapcsolatos, ha a felhasználó eredményez.

Emellett ha egy identitás-platform, amely más alkalmazások is használhatnak, például a Microsoft Accounts vagy az Office365 munkahelyi fiókkal, elvárt, hogy a hitelesítő adatokat a felhasználható a szállító függetlenül az alkalmazások között.

A Microsoft Identity platform, a Microsoft Identity SDK-k, valamint a rögzített munkahelyi nem meg, és lehetőséget nyújt az ügyfelei egyszeri Bejelentkezéssel delight, vagy az alkalmazások saját suite belül vagy, például a broker funkció és a hitelesítő az alkalmazások, a teljes eszköz.

Ez a forgatókönyv jelzi az SDK az alkalmazás számára az előnyök nyújtsanak az ügyfeleinek belül konfigurálása.

Ez a forgatókönyv a következőkre vonatkozik:

* Azure Active Directory
* Azure Active Directory B2C
* Az Azure Active Directory B2B
* Azure Active Directory feltételes hozzáférés

Az előző lépéseknél tudja hogyan [kiépíteni a régi portál alkalmazásokat az Azure Active Directory](active-directory-how-to-integrate.md) és az alkalmazásba integrálva a [Microsoft Identity iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>A Microsoft Identity Platform SSO jellemzői
### <a name="microsoft-identity-brokers"></a>Microsoft – identitáskezelési brókerek
A Microsoft lehetővé teszi a minden mobileszköz platform, amely lehetővé teszi az adatközponthíd-képzés hitelesítő adatokat a különböző alkalmazások különböző szállítóktól származó, és lehetővé teszi, hogy honnan fiók hitelesítő adatainak érvényesítéséhez egy biztonságos helyen igénylő különleges továbbfejlesztett funkciók. Ezek nevezzük **brókerek**. IOS és Android rendszeren a brókerek szolgáltatáson keresztül letölthető alkalmazás, hogy az ügyfelek telepítése függetlenül vagy lehet leküldeni az eszközre a vállalati, akár az összeset az eszköz az alkalmazott felügyelő által. Ezek a brókerek kezelése biztonsági csak az egyes alkalmazások vagy a rendszergazdák kívánják alapján a teljes eszköz támogatja. Ez a funkció a Windows rendszerben egy fiók kiválasztásakor az operációs rendszer, a Webeshitelesítés-szervező műszaki ismert részét biztosítja.

További információt használjuk, a brókerek és hogyan az ügyfelek előfordulhat, hogy lássák a bejelentkezési folyamat a Microsoft Identity platform olvasni.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Bejelentkezés mobileszközök minták
Eszközök felhasználó hitelesítő adatai hozzáférést a Microsoft Identity platform két alapvető mintázatokból kövesse:

* Nem közvetített támogatott bejelentkezések
* Támogatott bejelentkezések replikaszervező

#### <a name="non-broker-assisted-logins"></a>Nem közvetített támogatott bejelentkezések
Nem közvetített támogatott bejelentkezések olyan bejelentkezési élményt, amely az alkalmazás együtt, beágyazottan történik, és használja a helyi tárolót, az eszközön az alkalmazás. Ez a tároló lehet osztani alkalmazásra, de a hitelesítő adatok szorosan kötött az alkalmazás vagy csomag alkalmazásaikat, hogy a hitelesítő adatok. Valószínűleg tapasztal Ez sok mobilalkalmazások a felhasználónév és jelszó belülről beírásakor.

Ezek bejelentkezések rendelkezik a következő előnyöket biztosítja:

* Felhasználói élmény létezik-e teljes mértékben az alkalmazáson belül.
* Hitelesítő adatok ugyanazt a tanúsítványt, biztosít egy egyszeri bejelentkezést az alkalmazások a csomag által aláírt alkalmazások között megosztható legyen.
* Naplózás a felhasználói élmény körül vezérlő előtt és után bejelentkezhet az alkalmazásba valósul meg.

Ezek bejelentkezések a következő hátrányokkal rendelkezik:

* A felhasználó csak azokat az alkalmazás van-e beállítva a Microsoft Identities között a Microsoft Identity használó összes alkalmazások között nem számára az egyszeri bejelentkezést.
* Az alkalmazás az Intune-ban termékcsomagot használja, illetve a Speciális üzleti szolgáltatásokat, mint a feltételes hozzáférés nem használható.
* Az alkalmazás nem támogatja a Tanúsítványalapú hitelesítés üzleti felhasználók számára.

Íme egy a Microsoft Identity SDK-k engedélyezése az egyszeri bejelentkezés az alkalmazások a megosztott tárolóhellyel rendelkező működése ábrázolása:

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

#### <a name="broker-assisted-logins"></a>Támogatott bejelentkezések replikaszervező
Bejelentkezések Broker támogatású olyan bejelentkezési élmény a broker alkalmazáson belül és a tárolás és a biztonsági közvetítő segítségével fájlmegosztási hitelesítő adatokat, amelyek érvényesek a Microsoft Identity platform az eszközön lévő összes alkalmazáshoz. Ez azt jelenti, hogy az alkalmazások támaszkodnak az átvitelszervező felhasználók bejelentkezni. IOS és Android rendszeren a brókerek szolgáltatáson keresztül letölthető alkalmazás, hogy az ügyfelek telepítése függetlenül vagy lehet leküldeni az eszközre a felhasználó az eszközt felügyelő által. Ez az alkalmazástípus példa: a Microsoft Authenticator alkalmazás IOS rendszerű eszközökön. Ez a funkció a Windows egy fiók kiválasztásakor az operációs rendszer, a Webeshitelesítés-szervező műszaki ismert részét biztosítja.
A felhasználói élmény platformonként változó, és egyes esetekben zavart okozhatnak a felhasználók számára nem esetén megfelelően. Ismeri valószínűleg legtöbb ebben a mintában Ha a Facebook alkalmazást telepítették, és Facebook csatlakozni egy másik alkalmazás használja. A Microsoft Identity platform ugyanilyen mintájú használja.

Ennek eredménye "átmenet" IOS animáció, ahol az alkalmazás a rendszer elküldi a háttérben, miközben a Microsoft Authenticator alkalmazás elérhető lesz, az az előtérben, válassza ki, melyik fiókot szeretné a bejelentkezéshez a felhasználónak.  

Android és Windows a fiók kiválasztásakor látható az alkalmazás, amely kevésbé zavaró, a felhasználó számára.

#### <a name="how-the-broker-gets-invoked"></a>Hogyan a broker meghívott beolvasása
Kompatibilis ügynök telepítve van az eszközön, például a Microsoft Authenticator alkalmazást, ha a Microsoft Identity SDK-k automatikusan elvégzi a munkát meg broker hívásának, amikor a felhasználó azt jelzi, hogy kívánják-e jelentkezni a Microsoft bármely fiók használatával Identitásplatformmal. Ez a fiók lehet személyes Microsoft-Account, munkahelyi vagy iskolai fiókját, vagy egy fiókot, amely megadja és az Azure-ban B2C és B2B termékeink állomás. 

 #### <a name="how-we-ensure-the-application-is-valid"></a>Hogyan gondoskodunk róla, hogy az alkalmazás érvénytelen.
 
 Egy alkalmazás hívás a broker elengedhetetlen a átvitelszervező nyújtunk biztonsági identitásának érdekében bejelentkezések támogatott. IOS és Android nem kényszeríti az egyedi azonosítók, amelyek csak egy adott alkalmazáshoz tartozó érvényes, így rosszindulatú alkalmazások előfordulhat, hogy "hamis" jogos alkalmazás azonosítója és a jogkivonatok azt jelentette, hogy a jogos alkalmazás fogadni. Gondoskodjon arról, hogy mindig a megfelelő alkalmazás futásidőben tájékoztatjuk, egy egyéni redirectURI megadására, amikor az alkalmazás regisztrálása a Microsoft developer kérjük. **Az alábbiakban tárgyalt hogyan a fejlesztők kézműipari az átirányítási URI-t kell.** Az egyéni redirectURI tartalmazza az alkalmazás Csomagazonosítóját, és az Apple App Store biztosítja az alkalmazás egyedinek kell lennie. Amikor egy alkalmazás meghívja az átvitelszervező, a broker megkérdezi, biztosítsa a broker nevezett Csomagazonosítóját az iOS operációs rendszerrel. Az átvitelszervező a Csomagazonosító biztosít a Microsoft identity rendszerünkben hívásában. Ha az alkalmazás Csomagazonosítóját nem egyezik meg a regisztráció során fejlesztője által biztosított számunkra alkalmazáscsomag-Azonosítót, azt megtagadja a hozzáférést a jogkivonatok az erőforrás az alkalmazás által kért. Ez az ellenőrzés biztosítja, hogy csak az alkalmazás által a fejlesztői jogkivonatokat kap.

**A fejlesztői lehetősége van, ha a Microsoft Identity SDK meghívja az átvitelszervező, vagy a nem közvetített támogatott folyamat használja.** Azonban ha a fejlesztői használatát választja, nem a broker támogatású folyamata megszakad a SSO használatának előnye, hogy a felhasználó már valószínűleg az eszköz-felhasználó hitelesítő adatait, és megakadályozza, hogy az alkalmazásokban történő használatát, az üzleti szolgáltatásokat biztosít a Microsoft a ügyfelek, például a feltételes hozzáférés, a Intune kezelési képességek és a tanúsítvány alapú hitelesítést.

Ezek bejelentkezések rendelkezik a következő előnyöket biztosítja:

* Felhasználói SSO észlel, függetlenül attól, a szállító az alkalmazások között.
* Az alkalmazás használhatja a Speciális üzleti szolgáltatásokat, mint a feltételes hozzáférés, vagy az Intune-ban termékcsomagot használja.
* Az alkalmazás tanúsítvány alapú hitelesítést is támogatja az üzleti felhasználók.
* A broker alkalmazás további biztonsági algoritmusokkal és a titkosítás sokkal biztonságosabb bejelentkezési élmény a kérelem és a felhasználói identitás ellenőrzése.

Ezek bejelentkezések a következő hátrányokkal rendelkezik:

* IOS-ban a felhasználó van állapotra váltott ki az alkalmazás élmény közben a hitelesítő adatok közül választ.
* Képes kezelni az ügyfeleknek az alkalmazáson belül a bejelentkezési élmény megszűnését.

Íme, hogy a Microsoft Identity SDK-k hogyan működnek együtt a broker alkalmazásokhoz, hogy egyszeri Bejelentkezéses ábrázolása:

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

Volt képes a háttér-információkat, hogy jobban megismerheti, és alkalmazhatja a egyszeri bejelentkezés a Microsoft Identity platform és SDK-k segítségével az alkalmazáson belül képesnek kell lennie felvértezve.

## <a name="enabling-cross-app-sso-using-adal"></a>Adal-t használó alkalmazások közötti SSO engedélyezése
Jelen példában használjuk az ADAL iOS SDK számára:

* Nem közvetített támogatott egyszeri Bejelentkezést a csomag az alkalmazások bekapcsolása
* Kapcsolja be az SSO broker támogatású támogatása

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Nem közvetített egyszeri Bejelentkezést bekapcsolás támogatott az egyszeri bejelentkezés
Nem közvetített támogatott egyszeri bejelentkezéshez különböző alkalmazások a Microsoft Identity SDK-k kezelése nagy részét SSO összetettsége meg. Ez magában foglalja a megfelelő felhasználói keresése a gyorsítótárban, és karbantartása, hogy a lekérdezés bejelentkezett felhasználók listáját.

Egyszeri bejelentkezés engedélyezése a különböző alkalmazások saját végre kell hajtani a következőket:

1. Győződjön meg arról az alkalmazások felhasználói a ugyanazon ügyfél-azonosító-azonosítót.
2. Győződjön meg arról, hogy ugyanazt a aláírási tanúsítványt az Apple-től az alkalmazásokat használ, így keychains megoszthatja
3. Ugyanez a kulcslánc jogosultság az összes az alkalmazáshoz kérelmet.
4. Adja a Microsoft Identity SDK-k a megosztott kulcslánchoz azt szeretné, hogy mi legyen.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Az azonos ügyfél-Azonosítót használó / Application ID, az alkalmazások a csomagban található összes alkalmazás
Ahhoz, hogy a Microsoft Identity platform tudni, hogy rendelkezik engedélyezett jogkivonatok megosztása az alkalmazások között az alkalmazások egyes kell, hogy az azonos ügyfél-azonosító-azonosítót. Ez az egyedi azonosítója, amely az Ön számára biztosított a portálon az első alkalmazás regisztrálásakor.

Előfordulhat, hogy lehet szeretné megtudni, hogy hogyan határozható meg a Microsoft Identity szolgáltatás különböző alkalmazások Ha használja az ugyanazon alkalmazás azonosítóját. A válasz a kérdésre a **átirányítási URI-azonosítók**. Minden alkalmazás több átirányítási URI-azonosítók a bevezetési portálon regisztrált rendelkezhet. A csomagban található összes alkalmazás egy másik átirányítási URI-t fog rendelkezni. Ez megjelenésének például nem éri el:

Az App1 átirányítási URI-ja: `x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 átirányítási URI-ja: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 átirányítási URI-ja: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Ezek az azonos ügyfél-azonosító alapján beágyazott / alkalmazás azonosítója és keresni az átirányítási URI-t ismét nekünk a SDK-konfiguráció alapján.

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


*Vegye figyelembe, hogy az átirányítási URI-k formátuma ismertetése az alábbiakban olvasható. Segítségével bármely átirányítási URI-kivéve, ha kíván támogatni a broker ebben az esetben azok kell kinéznie hasonlóan a fentiek közül.*

#### <a name="create-keychain-sharing-between-applications"></a>A kulcslánc megosztása az alkalmazások között létrehozása
A kulcslánc megosztása túlmutat a jelen dokumentum, és a dokumentum az Apple által szabályozott [Adding Capabilities](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). A fontos, hogy úgy dönt, hogy mi a kulcslánc hívása, és adja hozzá ezt a funkciót az alkalmazások között.

Ha készen áll, megfelelő jogosultságok tekintse meg a projektkönyvtárban jogosult a fájl `entitlements.plist` , amely tartalmazza, amelyet a következőhöz hasonló:

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

Miután a kulcslánc jogosultság minden alkalmazásból engedélyezve van, és készen áll a SSO használata, adja a Microsoft Identity SDK a kulcslánc a következő beállítás használatával a `ADAuthenticationSettings` a következő beállításokkal:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Az alkalmazások között egy kulcslánc megosztásakor bármely alkalmazás törölheti felhasználók vagy rosszabb a jogkivonatok az alkalmazás minden részében. Ez a különösen katasztrofális, ha a tokeneket a háttérben munkahelyi alkalmazások. A kulcslánc megosztása azt jelenti, hogy a minden nagyon gondosan kell eltávolítási műveletek a Microsoft Identity SDK-k használatával.
> 
> 

Ennyi az egész! A Microsoft Identity SDK most fájlmegosztási hitelesítő adatokat az alkalmazások között. A Felhasználólista lesznek megosztva alkalmazáspéldányok között.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Egyszeri bejelentkezés SSO Broker bekapcsolása esetén támogatott.
Az, hogy az alkalmazás számára bármilyen brokert, az eszközön telepített **alapértelmezés szerint ki van kapcsolva**. Ahhoz, hogy használhassa az alkalmazást a broker néhány további konfigurációs lehetőségek és néhány kódot hozzá az alkalmazáshoz.

A lépések a következők:

1. Az alkalmazás kódjában hívása közben a MS SDK broker mód engedélyezése.
2. Állítson be egy új átirányítási URI-t, és adja meg, hogy az alkalmazás és az alkalmazás regisztrációját is.
3. Regisztrálás egy URL-sémát.
4. iOS9 támogatási: engedélyt adni a info.plist fájlhoz.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>1. lépés: Az alkalmazás broker üzemmód engedélyezése
Az alkalmazás használatához az átvitelszervező lehetősége van kapcsolva, a "context" vagy a kezdeti beállítás és a hitelesítési objektum létrehozásakor. Ehhez úgy, hogy a hitelesítő adatok típusa a kódban:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
A `AD_CREDENTIALS_AUTO` beállítás lehetővé teszi a Microsoft Identity SDK hívásához a broker kipróbálásához `AD_CREDENTIALS_EMBEDDED` megakadályozza, hogy a Microsoft Identity SDK hívása a broker számára.

#### <a name="step-2-registering-a-url-scheme"></a>2. lépés: Egy URL-séma regisztrálása
A Microsoft Identity platform az átvitelszervező el, és térjen vissza az alkalmazás vezérlő URL-címeket használ. Befejezés adott oda-vissza egy URL-sémát, az alkalmazás, amely a Microsoft Identity platform tudni fogja regisztrálni kell. Ez lehet kívül más alkalmazás rendszerek lehet, hogy már korábban regisztrált az alkalmazással.

> [!WARNING]
> Ajánlatos, hogy az URL-séma viszonylag egyedi minimalizálása érdekében a veszélyét annak, hogy az azonos URL-séma használata egy másik alkalmazás. Apple nem kényszeríti ki a regisztrált alkalmazás-áruházbeli URL-sémákat egyediségét.
> 
> 

Alább példája hogyan Ez jelenik meg a projekt konfigurációjában. Előfordulhat, hogy is ehhez az xcode-ban, valamint:

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

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>3. lépés: Egy új átirányítási URI-t és az URL-séma létrehozása
Annak érdekében, hogy a Microsoft bármikor visszatérhet a hitelesítő adatok jogkivonatokat a megfelelő alkalmazás, meg kell győződnünk arról nevezzük vissza oly módon, hogy az iOS operációs rendszer ellenőrizheti az alkalmazás. Az iOS operációs rendszer jelentéseket a Microsoft broker alkalmazások hívja meg, hogy az alkalmazás Csomagazonosítóját. Ez a nem egy engedélyezetlen alkalmazás megtévesztésre. Ezért azt használja ki a győződjön meg arról, hogy a jogkivonatok visszatér a megfelelő alkalmazás broker az alkalmazás URI együtt. Kérjük, az egyedi átirányítási URI-t mindkét létrehozásához az alkalmazásban, és egy átirányítási URI-t a fejlesztői portálon állítja be.

Az átirányítási URI-t a megfelelő formátumban kell lennie:

`<app-scheme>://<your.bundle.id>`

például: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Az átirányítási URI-t kell adni az alkalmazás regisztrálása használatával a [Azure-portálon](https://portal.azure.com/). További információ az Azure AD alkalmazás-regisztrációval, lásd: [integrálása az Azure Active Directoryval](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>3a. lépés: az alkalmazás és a fejlesztői portálon Tanúsítványalapú hitelesítés támogatására egy átirányítási URI hozzáadása
Egy második "msauth" regisztrálva kell lennie az alkalmazás támogatási Tanúsítványalapú hitelesítés és a [Azure-portálon](https://portal.azure.com/) tanúsítványhitelesítés kezeléséhez, ha szeretné, hogy támogassa az alkalmazásban.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

például: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>4. lépés: iOS9: egy konfigurációs paraméter hozzáadása az alkalmazáshoz
Adal-t használ – canOpenURL: Ellenőrizze, hogy ha az ügynök telepítve van-e az eszközön. Az iOS 9-es Apple zárolva mi is kereshet séma egy alkalmazáskészletet. "Msauth" hozzáadása az info.plist részében szüksége lesz a `info.plist file`.

<key>LSApplicationQueriesSchemes</key> <array> <string>msauth</string></array>

### <a name="youve-configured-sso"></a>Egyszeri bejelentkezés konfigurálása!
Most már a Microsoft Identity SDK automatikusan is fájlmegosztási hitelesítő adatokat az alkalmazások között és a broker meghívni, ha az megtalálható az eszközükön.

