---
title: Leküldéses értesítések az Azure Notification Hubs használatára a Swift iOS-alkalmazások |} A Microsoft Docs
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket az Azure Notification Hubs használatára a Swift iOS-alkalmazásokat.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: a4773ddd8114659118e89cfee57e73ddb39ff6b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116676"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Oktatóanyag: Leküldéses értesítések küldése a Notification Hubs – REST API használata a Swift iOS-alkalmazások

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Ebben az oktatóanyagban használhatja az Azure Notification Hubs leküldéses értesítések küldéséhez egy iOS Swift-alapú alkalmazás segítségével a [REST API-val](/rest/api/notificationhubs/). Egy üres iOS-alkalmazást, amely leküldéses értesítéseket fogad használatával is létrehozhat a [Apple Push Notification service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Ez az oktatóanyag végigvezeti az alábbi lépéseket:

> [!div class="checklist"]
> * Hozzon létre a tanúsítvány-aláírási kérelem fájlját.
> * Kérje az alkalmazást a leküldéses értesítésekre.
> * Hozzon létre egy olyan kiépítési profil az alkalmazáshoz.
> * Értesítési központ létrehozása.
> * Az értesítési központ konfigurálása az APNs-adatait.
> * Az iOS-alkalmazás csatlakoztatása egy értesítési központhoz.
> * A megoldás teszteléséhez.

## <a name="prerequisites"></a>Előfeltételek

A lépések követéséhez le lesz szüksége:

- Haladhat végig [Azure Notification Hubs – áttekintés](notification-hubs-push-notification-overview.md) Ha nem ismeri a szolgáltatást.
- További információ [regisztrációk és telepítési](notification-hubs-push-notification-registration-management.md).
- Az aktív [Apple Developer-fiók](https://developer.apple.com).
- Xcode-ban futó és a egy érvényes fejlesztői tanúsítvány a kulcskarikában telepített Mac.
- Futtathat, és a hibakereséshez, leküldéses értesítéseket a szimulátor nem tesztelése, fizikai iPhone eszköz.
- A fizikai iPhone eszköz regisztrálva a [Apple-portál](https://developer.apple.com) és a tanúsítványhoz.
- Egy [Azure-előfizetés](https://portal.azure.com) ahol létrehozni és -erőforrások kezeléséhez.

Ha már rendelkezik, hogy nincs korábbi tapasztalata az iOS-fejlesztések, tudja, hogy követni tudja a lépéseket ebben a példában először – alapelvek létrehozásához kell lennie. Azonban veheti az a következő fogalmak való ismerkedés során bizonyulhat:

- Az xcode-ban és a Swift iOS-alkalmazások készítéséhez.
- Konfigurálása egy [Azure Notification hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) iOS-hez.
- A [Apple fejlesztői portál](https://developer.apple.com) és a [az Azure portal](https://portal.azure.com).

> [!NOTE]
> Az értesítési központ használatára lesz konfigurálva a **védőfal** csak a hitelesítési módot. Ez a hitelesítési módszer ne használjon a termelési számítási feladatokhoz.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Az iOS-alkalmazás csatlakoztatása egy értesítési központhoz

Ebben a szakaszban az iOS-alkalmazás, amely csatlakozni fog az értesítési központ fog létrehozni.  

### <a name="create-an-ios-project"></a>Egy iOS-projekt létrehozása

1. Az Xcode-ban hozzon létre egy új iOS-projektet, és válassza az **Single View Application** (Egynézetes alkalmazás) sablont.

1. Ha az új projekt beállításait:

   1. Adja meg a **Terméknév** (PushDemo) és **Organization Identifier** (`com.<organization>`) beállításakor használt **Bundle Identifier** az Apple fejlesztői portálján.

   1. Válassza ki a **csapat** , amely a **Alkalmazásazonosító** lett beállítva a.

   1. Állítsa be a **nyelvi** való **Swift**.

   1. Kattintson a **Tovább** gombra.

1. Hozzon létre egy új nevű **SupportingFiles**.

1. Hozzon létre egy új p-lista fájlt nevű **devsettings.plist** a a **SupportingFiles** mappát. Adja hozzá ezt a mappát a **gitignore** fájlt, ezért nem véglegesített egy git-adattárral való munka során. Az éles alkalmazások esetén, akkor valószínűleg kell feltételesen beállítás titkos adatokat egy automatizált összeállítási folyamatból részeként. Ilyen beállítások Ez az útmutató nem tárgyalja.

1. Frissítés **devsettings.plist** , hogy tartalmazzák az Ön által üzembe helyezett az értesítési központot a saját értékeit használja a következő konfigurációs bejegyzéseket:

   | Kulcs                            | Típus                     | Érték                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | <hubKey>                  |
   | notificationHubKeyName         | String                   | <hubKeyName>              |
   | notificationHubName            | String                   | <hubName>                 |
   | notificationHubNamespace       | String                   | <hubNamespace>            |

   Az értesítésiközpont-erőforrás az Azure Portalon lépjen a szükséges értékeket is megtalálhatja. Különösen a **notificationHubName** és **notificationHubNamespace** értékei a jobb felső sarkában a **Essentials** összefoglaló belül a **Áttekintése** lapot.

   ![Notification Hubs alapvető szolgáltatások összegzése](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Is megkeresheti a **notificationHubKeyName** és **notificationHubKey** az értékek **hozzáférési szabályzatok** , és válassza a megfelelő  **Hozzáférési szabályzat**, mint például `DefaultFullSharedAccessSignature`. Ezt követően másolja át a **elsődleges kapcsolati karakterlánc** előtaggal van ellátva a érték `SharedAccessKeyName=` a `notificationHubKeyName` és az érték előtaggal `SharedAccessKey=` a a `notificationHubKey`.

   A kapcsolati karakterláncot a következő formátumban kell megadni:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Legyen egyszerű, adja meg a `DefaultFullSharedAccessSignature` így használhatja a token értesítések küldéséhez. A gyakorlatban a `DefaultListenSharedAccessSignature` lehet jobb választás olyan helyzetben, amikor csak ha értesítéseket szeretne kapni.

1. Alatt **Project Navigator**, jelölje be a **projektnév** majd válassza ki a **általános** fülre.

1. Keresse meg **identitás** majd állítsa be a **Bundle Identifier** értékét, hogy az megfeleljen `com.<organization>.PushDemo`, az érték használt a **Alkalmazásazonosító** az előző lépésben.

1. Keresse meg **aláírás**, és válassza ki a megfelelő **csapat** számára a **Apple Developer-fiók**. A **csapat** értéknek meg kell egyeznie az alapján, amelyek a tanúsítványokat és a profilok létrehozott.

1. Xcode automatikusan lekéri a megfelelő **létesítési profil** érték alapján az **Bundle Identifier**. Ha nem látja az új **létesítési profil** értéket, próbálja meg frissíteni a profilok a **aláíró identitása** kiválasztásával **Xcode**  >  **Beállítások** > **fiók** > **részleteinek megtekintéséhez**. Válassza ki **aláíró identitása**, majd válassza ki a **frissítése** letöltéséhez a profilok jobb alsó gomb.

1. Válassza ki a **képességek** lapon és ellenőrizze, hogy **leküldéses értesítések** engedélyezve vannak.

1. Nyissa meg a **AppDelegate.swift** fájlt, végrehajtására a **UNUserNotificationCenterDelegate** protokollt, és adja hozzá a következő kódot az osztály elejéhez:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    Ezekről a tagokról később fogja használni. Pontosabban szeretné használni a **címkék** és **genericTemplate** tagokat a regisztráció részeként. A címkék további információkért lásd: [regisztrációs címkék](notification-hubs-tags-segment-push-message.md) és [sablonregisztrációk](notification-hubs-templates-cross-platform-push-messages.md).

1. Ugyanebben a fájlban adja hozzá a következő kódot a **didFinishLaunchingWithOptions** függvény:

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Ez a kód lekéri a beállítás értékét **devsettings.plist**, beállítja a **AppDelegate** osztály a **UNUserNotificationCenter** delegálása, a kérések engedélyezése leküldéses értesítések, majd a hívások **registerForRemoteNotifications**.

    Legyen egyszerű, hogy a kód támogatja *iOS 10-es és annál újabb verzióknál érhető*. Előző operációsrendszer-verziók támogatását a megfelelő API-k és megközelítések feltételesen használatával, mint a szokásos módon adhat hozzá.

1. Ugyanebben a fájlban adja hozzá a következő funkciókat:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    A kód a **installationId** és **pushChannel** értékek regisztrálna az értesítési központban. Ebben az esetben használ **UIDevice.current.identifierForVendor** , adjon meg egy egyedi értéket, az eszköz és a majd formázása a **deviceToken** , adja meg a kívánt  **pushChannel** értéket. A **showAlert** függvény létezik néhány üzenet szövege bemutatási célokra egyszerűen megjeleníthető.

1. Még mindig a **AppDelegate.swift** fájlt, adja hozzá a **willPresent** és **didReceive** látja el **UNUserNotificationCenterDelegate**. Ezek a függvények riasztás megjelenítése, amikor azok a rendszer értesíti arról, hogy egy alkalmazás fut-e az előtérben vagy háttérben jelölik.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Adja hozzá a nyomtatási utasításokat alján a **didRegisterForRemoteNotificationsWithDeviceToken** függvényt, hogy ellenőrizze, hogy **installationId** és **pushChannel** folyamatban van hozzárendelt értékek.

1. Hozzon létre a **modellek**, **szolgáltatások**, és **segédprogramok** alaprendszeri összetevőkön mappáit, adunk hozzá a projekthez később.

1. Ellenőrizze, hogy a projekt épít, és a fizikai eszközön fut. Leküldéses értesítések nem tesztelhető a szimulátor használatával.

### <a name="create-models"></a>Modellek létrehozása

Ebben a lépésben létrehozhat egy csoportot, a modellek, amelyek a [Notification Hubs – REST API](/rest/api/notificationhubs/) is észleltünk adattartalmakat és tárolja a szükséges közös hozzáférésű jogosultságkód (SAS) jogkivonat-adatokat.

1. Adjon hozzá egy új Swift fájlt nevű **PushTemplate.swift** , a **modellek** mappát. Ez a modell biztosít egy struct jelölő a **törzs** egy egyéni sablon részeként a **DeviceInstallation** adattartalom.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Adjon hozzá egy új Swift fájlt nevű **DeviceInstallation.swift** , a **modellek** mappát. Ez a fájl egy jelölő a tartalom létrehozása vagy frissítése struktura meghatározása egy **eszköz telepítése**. Adja hozzá a következő kódot a fájlhoz:

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Adjon hozzá egy új Swift fájlt nevű **TokenData.swift** , a **modellek** mappát. Ez a modell egy SAS-token, a lejárat és tárolására használható.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Hozzon létre egy SAS-token

A Notification Hubs az Azure Service Bus ugyanazt a biztonsági-infrastruktúrát használja. A REST API hívása, kell [programozott módon az SAS-token létrehozásához](/rest/api/eventhub/generate-sas-token) , amely a használható a **engedélyezési** a kérelem fejlécében.  

Az így létrejövő jogkivonatot az alábbi formátumban lesz:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Maga a folyamat az azonos hat fő lépésből áll:  

1. Lejártakor a számítástechnika [UNIX alapidőpont](https://en.wikipedia.org/wiki/Unix_time) formátum, ami azt jelenti, hogy hány másodpercig az éjfél óta eltelt egyezményes világidő, 1970. január 1.
1. Formázás a **ResourceUrl** , amely képviseli az erőforráshoz próbál hozzáférni, ezért létfontosságú a kódolt- és nagybetűk. A **ResourceUrl** formában van `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Felkészülés a **StringToSign**, amely vannak formázva, `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Számítási és a Base64-kódolást a **aláírás** HMAC-SHA256-kivonatát használatával a **StringToSign** értéket. A kivonat értékével rendelkező szolgál a **kulcs** része a **kapcsolati karakterlánc** számára a megfelelő **engedélyezési szabály**.
1. A Base64-kódolású formázás **aláírás** , hogy legyen %-os kódolva.
1. Hozhat létre, a tokent az a várt formátum használatával az **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, és **UrlEncodedResourceUrl** értékeket.

Tekintse meg a [Azure Service Bus dokumentációja](../service-bus-messaging/service-bus-sas.md) alaposabb közös hozzáférésű jogosultságkód és Azure Service Bus és a Notification Hubs használatának áttekintése.

Ebben a példában a Swift az alkalmazásában fog használni az Apple nyílt forráskódú **CommonCrypto** könyvtár az aláírás-kivonatoló kiküszöbölni. A C-kódtár, mert nem érhető el a Swift beépített. Elérhetővé teheti a kódtár használatával egy áthidalási fejlécet.

Adja hozzá, és a áthidalási fejléc konfigurálása:

1. Válassza ki az xcode-ban **fájl** > **új** > **fájl** > **Fejlécfájlt**. A fejléc-fájl neve **BridgingHeader.h**.

1. Az importálandó fájl szerkesztése **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Frissítse a céloldali **Build Settings** az áthidalási fejléc hivatkozni:

   1. Nyissa meg a **épület beállítások** lapra, és görgessen le a **Swift fordítóprogram** szakaszban.

   1. Ügyeljen arra, hogy a **telepítése Objective-C kompatibilitási fejléc** beállítás **Igen**.

   1. Adja meg a fájl elérési útja `'<ProjectName>/BridgingHeader.h'` be a **Objective-C bridging fejléc** lehetőséget. Ez a fájl elérési útját az áthidalási fejlécet.

   Ha ezek a beállítások nem találja, gondoskodjon arról, hogy a **összes** kijelölt nézet helyett **alapszintű** vagy **testre szabott**.

   Nincsenek számos külső nyílt forráskódú burkoló kódtár érhető el, amelyek segítségével tehetik **CommonCrypto** könnyebb. Azonban ez a cikk nem foglalkozik, ilyen kódtárak veszik górcső alá.

1. Adjon hozzá egy új Swift fájlt nevű **TokenUtility.swift** belül a **segédprogramok** mappát, és adja hozzá a következő kódot:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   Ez a segédeszköz a SAS-jogkivonat létrehozásáért felelős logikai magában foglalja.

   Ahogyan korábban, a **getSasToken** funkció, amellyel előkészíthető a magas szintű lépéseket a token előkészítéséhez szükséges. A függvény neve lesz a telepítés szolgáltatás által az oktatóanyag későbbi részében.

   A többi két funkció által meghívott a **getSasToken** függvény: **sha256HMac** számítástechnikához az aláírás és **urlEncodedString** Encoding, a társított URL-címe karakterlánc. A **urlEncodedString** függvény szükség, mivel már nem érhető el a szükséges kimeneti a beépített használatával **addingPercentEncoding** függvény.

   A [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) kiváló példa bemutatja, hogyan készíthető elő ezeket a műveleteket az Objective-c-hez További információ az Azure Service Bus SAS-tokeneket megtalálható a [Azure Service Bus dokumentációja](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>A SAS-token ellenőrzése

Az ügyfél a telepítési szolgáltatás megvalósítása, előtt ellenőrizze, hogy az alkalmazás megfelelően létrehozza az SAS-token a választott HTTP-segédprogram használatával. Ez az oktatóanyag az alkalmazásában, a kívánt eszközben dolgozhat lesz **Postman**.

Használhat egy megfelelően elhelyezett nyomtatási utasítás vagy töréspontot vegye figyelembe a **installationId** és a **token** értékeket az alkalmazás által generált.

Kövesse az alábbi lépéseket hívja a **telepítések** API:

1. A **Postman**, nyisson meg egy új lapot.

1. A kérelem beállítása **első** , és adja meg a következő címre:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. A kérelem fejlécében a következőképpen konfigurálja:

   | Kulcs           | Érték            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Engedélyezés | <sasToken>       |
   | x-ms-version  | 2015-01          |

1. Válassza ki a **kód** a jobb felső sarokban alatt megjelenő gomb a **mentése** gombra. A kérelem a következő példához hasonlóan kell kinéznie:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Válassza ki a **küldése** gombra.

Regisztráció nem létezik a megadott **installationId** ezen a ponton. Az ellenőrzés "401-es nem engedélyezett" válasz helyett a "404 nem található" válasz kell eredményeznie. Ezt az eredményt kell győződjön meg arról, hogy a SAS-jogkivonat elfogadták.

### <a name="implement-the-installation-service-class"></a>A telepítés szolgáltatásosztály megvalósítása

Ezután az alapszintű burkolója implementálása a [telepítések REST API-val](/rest/api/notificationhubs/create-overwrite-installation).  

Adjon hozzá egy új Swift fájlt nevű **NotificationRegistrationService.swift** alatt a **szolgáltatások** mappát, majd adja hozzá a következő kódot a fájl:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil

    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }

    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {

        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)

        if let tags = tags {
            deviceInstallation.tags = tags
        }

        if let templates = templates {
            deviceInstallation.templates = templates
        }

        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"

            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"

            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }

            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)

            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }

    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

Az előfeltételként szükséges részletei is szerepelnek az inicializálás során. Címkékkel és sablonokkal igény szerint átadott a **regisztrálása** részét képező függvény a **eszköz telepítése** JSON-adattartalmat.  

A **regisztrálása** függvény meghívja a más titkos függvények a kérelem előkészítése. Válasz fogadását követően a befejezési nevezik, és azt jelzi, hogy a regisztráció sikeres volt-e.  

A kérelem-végpont alapján áll össze a **getBaseAddress** függvény. A konstrukció a notification hub paramétereket használja *névtér* és *neve* , amely az inicializálás során lett megadva.  

A **getSasToken** függvény ellenőrzi, hogy a jelenleg tárolt token érvényes. Ha a jogkivonat nem érvényes, a függvény meghívja **TokenUtility** , egy új jogkivonatot, majd tárolja azt megelőzően érték visszaadása.

Végül **encodeToJson** JSON használatra a kérelem törzsének részeként a megfelelő modellt objektumok alakítja.

### <a name="invoke-the-notification-hubs-rest-api"></a>A Notification Hubs – REST API meghívása

Az utolsó lépés frissítése folyamatban van **AppDelegate** használandó **NotificationRegistrationService** szeretne regisztrálni a **Értesítésiközpont**.

1. Nyissa meg **AppDelegate.swift** , és adja hozzá a osztályszintű változót tárolják való hivatkozást a **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. Ugyanebben a fájlban frissítse a **didRegisterForRemoteNotificationsWithDeviceToken** függvény inicializálása a **NotificationRegistrationService** az előfeltételként szükséges paramétereket, majd hívást a **regisztrálása** függvény.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Nyomtatási utasításokat néhány használatával frissítse a kimeneti ablakban eredményét fog tartani a egyszerű, a **regisztrálása** műveletet.

1. Most már lefordíthatja, és futtassa az alkalmazást egy fizikai eszköz. A kimeneti ablakban "regisztrálva" kell megjelennie.

## <a name="test-the-solution"></a>A megoldás teszteléséhez

Ezen a ponton az alkalmazás regisztrálva van a **Értesítésiközpont** és leküldéses értesítést kapjanak. Az xcode-ban a hibakereső leállítása, és ha éppen fut, zárja be az alkalmazást. Ezután ellenőrizze, hogy a **eszköz telepítése** részletek elvárt módon vannak, és az alkalmazások mostantól fogadni tudják leküldéses értesítéseket.  

### <a name="verify-the-device-installation"></a>Az eszköz telepítésének ellenőrzése

Hajtsa végre a kérésben ahogy korábban használatával **Postman** a [az SAS-token ellenőrzése](#verify-the-sas-token). Feltételezve, hogy a SAS-jogkivonatát még nem járt le, a válaszban most már tartalmazza a megadott, például a sablonokat és címkék telepítési adatok.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>(Az Azure portal) tesztértesítés küldése

A leggyorsabb mód a tesztelje, hogy Ön már fogadhat értesítéseket, hogy tallózással keresse meg az értesítési központban az Azure Portalon:

1. Az Azure Portalon keresse meg a **áttekintése** az értesítési központ lapján.

1. Válassza ki **Tesztküldés**, amely fölött van a **Essentials** a bal felső sarokban a portál ablakának összegzése:

    ![Notification Hubs alapvető erőforrások összefoglaló Tesztküldés gomb](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Válasszon **egyéni sablon** származó a **platformok** listája.

1. Adja meg **12345** számára a **küldés címkét alkotó kifejezésbe**. Ez a címke a telepítés volt korábban megadott.

1. Szükség esetén szerkessze a **üzenet** a JSON hasznos adatok:

    ![Notification Hubs küldés tesztelése](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Kattintson a **Küldés** gombra. A portálon azt kell jeleznie, hogy az értesítés elküldése sikerült az eszközön:

    ![Eredmények küldése a Notification Hubs teszt](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Feltételezve, hogy az alkalmazás nem az előtérben futó, megjelenik egy értesítés is a **értesítési központ** az eszközön. Az értesítésre való koppintással kell nyissa meg az alkalmazást, és a riasztás megjelenítéséhez.

    ![Értesítés fogadása példa](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>(Mail-szolgáltatója) tesztértesítés küldése

Keresztül értesítéseket küldhet a [REST API-val](/rest/api/notificationhubs/) használatával **Postman**, amely lehet több kényelmesen teszteléséhez.

1. Nyisson meg egy új lapon **Postman**.

1. A kérelem beállítása **POST**, és adja meg a következő címre:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. A kérelem fejlécében a következőképpen konfigurálja:

   | Kulcs                            | Érték                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Engedélyezés                  | <sasToken>                     |
   | ServiceBusNotification-Format  | sablon                       |
   | Tags                           | "12345"                        |

1. A kérelmek **törzs** használandó **RAW - JSON (application.json)** a következő JSON-adattartalom:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Válassza ki a **kód** gombra, amely alatt a **mentése** gombra a jobb felső sarokban az ablak. A kérelem a következő példához hasonlóan kell kinéznie:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Válassza ki a **küldése** gombra.

Első sikeres állapotkódot kell, és megkapja az értesítést az ügyféleszközön.

## <a name="next-steps"></a>További lépések
Most már rendelkezik egy Swift alapszintű iOS-alkalmazást, egy értesítési központ keresztül csatlakozik a [REST API-val](/rest/api/notificationhubs/) és küldhet és fogadhat értesítéseket. További információkért tekintse át a következő cikkeket:

- [Az Azure Notification Hubs – áttekintés](notification-hubs-push-notification-overview.md)
- [A Notification Hubs – REST API-k](/rest/api/notificationhubs/)
- [Notification Hubs SDK háttér-műveletek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [A Notification Hubs SDK a Githubon](https://github.com/Azure/azure-notificationhubs)
- [Az alkalmazás háttérrendszere regisztrálása](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md)
- [Címkék használata](notification-hubs-tags-segment-push-message.md) 
- [Egyéni sablonok használata](notification-hubs-templates-cross-platform-push-messages.md)
- [A Service Bus hozzáférés-vezérlés közös hozzáférésű jogosultságkódok használata](../service-bus-messaging/service-bus-sas.md)
- [Programozott módon a SAS-jogkivonatokat hoz létre](/rest/api/eventhub/generate-sas-token)
- [Apple biztonsági: általános kriptográfiai](https://developer.apple.com/security/)
- [UNIX alapidőpont szerint](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
