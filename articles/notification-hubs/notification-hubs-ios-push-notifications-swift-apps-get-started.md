---
title: Leküldéses értesítések küldése az Azure Notification Hubsszolgáltatást használó Swift iOS-alkalmazásoknak | Microsoft dokumentumok
description: Megtudhatja, hogyan leküldéses értesítéseket swift iOS-alkalmazások, amelyek az Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336639"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Oktatóanyag: Leküldéses értesítések küldése swift iOS-alkalmazásoknak az Értesítési központok REST API-jával

> [!div class="op_single_selector"]
> * [Célkitűzés-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Ebben az oktatóanyagban az Azure Notification Hubs használatával leküldheti az értesítéseket egy Swift-alapú iOS-alkalmazásba a [REST API](/rest/api/notificationhubs/)használatával. Egy üres iOS-alkalmazást is létrehozhat, amely leküldéses értesítéseket kap az [Apple leküldéses értesítési szolgáltatás (APN)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)használatával.

Ez az oktatóanyag végigvezeti a következő lépéseken:

> [!div class="checklist"]
> * A tanúsítványaláíró kérelemfájl létrehozása.
> * Kérje az alkalmazást leküldéses értesítésekhez.
> * Hozzon létre egy kiépítési profilt az alkalmazáshoz.
> * Értesítési központ létrehozása.
> * Konfigurálja az értesítési központot az APN-adatokkal.
> * Csatlakoztassa az iOS-alkalmazást egy értesítési központhoz.
> * Tesztelje az oldatot.

Az oktatóanyag teljes kódja [megtalálható a GitHubon.](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest)

## <a name="prerequisites"></a>Előfeltételek

Követni, akkor szükség van:

- Az [Azure Notification Hubs áttekintése,](notification-hubs-push-notification-overview.md) ha nem ismeri a szolgáltatást.
- A regisztrációk és [a telepítés megismerése.](notification-hubs-push-notification-registration-management.md)
- Aktív [Apple fejlesztői fiók](https://developer.apple.com).
- Xcode-ot futtató Mac, valamint a kulcskarikába telepített érvényes fejlesztői tanúsítvány.
- Fizikai iPhone-eszköz, amelyet futtathat és debugolhat, mivel nem tesztelheti a leküldéses értesítéseket a szimulátorral.
- Az Ön fizikai iPhone-készüléke regisztrálva van az [Apple Portalon,](https://developer.apple.com) és a tanúsítványához van társítva.
- [Azure-előfizetés,](https://portal.azure.com) ahol erőforrásokat hozhat létre és kezelhet.

Még ha nincs is előzetes tapasztalata az iOS-fejlesztéssel kapcsolatban, képesnek kell lennie arra, hogy kövesse az első alapelvek példájának létrehozásához szükséges lépéseket. A következő fogalmak ismeretei azonban hasznosak:

- IOS-alkalmazások létrehozása Xcode és Swift segítségével.
- [Azure-értesítési központ](notification-hubs-ios-apple-push-notification-apns-get-started.md) konfigurálása iOS-hez.
- Az [Apple Developer Portal](https://developer.apple.com) és az Azure [Portal](https://portal.azure.com).

> [!NOTE]
> Az értesítési központ úgy lesz konfigurálva, hogy csak a **beésre beállított beküldési mód** legyen. Ezt a hitelesítési módot ne használja éles számítási feladatokhoz.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Az iOS-alkalmazás csatlakoztatása értesítési központhoz

Ebben a szakaszban létre kell hoznia az iOS-alkalmazást, amely csatlakozik az értesítési központhoz.  

### <a name="create-an-ios-project"></a>iOS-projekt létrehozása

1. Az Xcode-ban hozzon létre egy új iOS-projektet, és válassza az **Single View Application** (Egynézetes alkalmazás) sablont.

1. Az új projekt beállításainak megadásakor:

   1. Adja meg azt a **terméknevet** (PushDemo) és **szervezeti azonosítót** (`com.<organization>`), amelyet a **csomagazonosító** apple developer portalon való beállításakor használt.

   1. Válassza ki azt a **csapatot,** amelyhez az **alkalmazásazonosítót** beállította.

   1. Állítsa a **nyelvet** **Swift**.

   1. Válassza a **Tovább lehetőséget.**

1. Hozzon létre egy új mappát **Nevű SupportingFiles**.

1. Hozzon létre egy új p-list fájlt **nevű devsettings.plist** a **SupportingFiles** mappában. Ügyeljen arra, hogy ezt a mappát a **gitignore** fájlba adja, hogy ne legyen véglegesítve, amikor git tárhetóval dolgozik. Egy éles alkalmazásban valószínűleg feltételesen kell beállítani ezeket a titkos kulcsokat egy automatikus létrehozási folyamat részeként. Az ilyen beállításokat ez a forgatókönyv nem fedi le.

1. Frissítse **a fejlesztői beállítások.plist-et,** hogy a következő konfigurációs bejegyzéseket tartalmazza a saját értékei vel a kiépített értesítési központból:

   | Kulcs                            | Típus                     | Érték                     |
   |--------------------------------| -------------------------| --------------------------|
   | értesítésHubKey             | Sztring                   | \<hubKey>                  |
   | értesítésHubKeyName         | Sztring                   | \<hubKeyName>              |
   | értesítésHubName            | Sztring                   | \<hubName>                 |
   | értesítésHubNamespace       | Sztring                   | \<hubNamespace>            |

   A szükséges értékeket az Azure Portalon az értesítési központ erőforrásra navigálva találhatja meg. Különösen a **notificationHubName** és **notificationHubNamespace** értékek az **Essentials** összegzésének jobb felső sarkában találhatók az **Áttekintés** lapon.

   ![Értesítési központok Essentials összegzése](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Az **értesítésHubKeyName** és **notificationHubKey** értékeket az **Access-házirendek** elemre navigálva `DefaultFullSharedAccessSignature`is megtalálhatja, és kiválasztja a megfelelő hozzáférési **szabályzatot**, például . Ezt követően másolja az **elsődleges kapcsolati karakterláncból** `SharedAccessKeyName=` az előleges értéket `notificationHubKeyName` és a. előlegét. `SharedAccessKey=` `notificationHubKey`

   A kapcsolati karakterláncnak a következő formátumúnak kell lennie:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Annak érdekében, `DefaultFullSharedAccessSignature` hogy egyszerű, adja meg, így a jogkivonat ot értesítések küldésére használhatja. A gyakorlatban a `DefaultListenSharedAccessSignature` jobb választás lenne olyan helyzetekben, amikor csak értesítéseket szeretne kapni.

1. A **Project Navigátor**csoportban jelölje ki a **Projekt nevét,** majd az **Általános** lapot.

1. Keresse meg **az identitást,** majd állítsa `com.<organization>.PushDemo`be a **csomagazonosító** értékét úgy, hogy az megegyezik , amely az előző lépésből származó **alkalmazásazonosító** hoz használt érték.

1. Keresse meg **& képességek aláírása lehetőséget,** majd válassza ki az **Apple fejlesztői fiókjához megfelelő csapatot.** **Team** A **Csapat** értéknek meg kell egyeznie a tanúsítványokkal és profilokkal.

1. Az Xcode automatikusan lehúzza a **csomagazonosítón**alapuló megfelelő **kiépítési profil** értéket. Ha nem látja az új **kiépítési profil** értéket, próbálja meg frissíteni az **aláíró identitás profiljait** az **Xcode** > **beállítások** > **fiók** kiválasztásával, majd a Manuális **profilok letöltése** gombra kattintva töltse le a profilokat.

1. Továbbra is az **Aláíró & képességek** lapon kattintson a + **Képesség** gombra, és koppintson duplán a **leküldéses értesítések** elemre a listából, hogy a **leküldéses értesítések** engedélyezve legyenek.

1. Nyissa meg az **AppDelegate.swift** fájlt az **UNUserNotificationCenterDelegate** protokoll megvalósításához, és adja hozzá a következő kódot az osztály elejéhez:

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
        
        ...
    }
    ```

    Később használni fogod ezeket a tagokat. Pontosabban, akkor használja a **címkék** et tagja részeként a regisztráció egy **egyéni sablont**. A címkékről a [Címkék a regisztrációk](notification-hubs-tags-segment-push-message.md) és [a sablonregisztrációk](notification-hubs-templates-cross-platform-push-messages.md)címkék című témakörben olvashat bővebben.

1. Ugyanebben a fájlban adja hozzá a következő kódot a **didFinishLaunchWithOptions** függvényhez:

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

    Ez a kód lekéri a beállítási értékeket a **devsettings.plist list,** beállítja az **AppDelegate** osztály, mint az **UNUserNotificationCenter** delegált, kéri az engedélyezési leküldéses értesítések, majd kéri **registerForRemoteNotifications**.

    Annak érdekében, hogy egyszerű legyen, a kód csak az *iOS 10 és később*támogatja. A korábbi operációsrendszer-verziók támogatását feltételesen a megfelelő API-k és megközelítések használatával, aszokásos módon hozzáadhat.

1. Ugyanebben a fájlban adja hozzá a következő függvényeket:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    A kód a **installationId** és **pushChannel** értékeket használja az értesítési központtal való regisztrációhoz. Ebben az esetben az **UIDevice.current.identifierForVendor** használatával egyedi értéket biztosít az eszköz azonosítására, majd formázza az **eszköztokenjét** a kívánt **pushChannel-érték** megadásához. A **showAlert** funkció egyszerűen csak azért létezik, hogy megjelenítsen néhány üzenet szöveget demonstrációs célokra.

1. Még mindig az **AppDelegate.swift** fájlban adja hozzá a **willPresent** és **didReceive** függvényeket **UNUserNotificationCenterDelegate**. Ezek a függvények riasztást jelenítenek meg, ha értesítést kapnak arról, hogy egy alkalmazás az előtérben vagy a háttérben fut.

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

1. Adjon hozzá nyomtatási utasításokat a **didRegisterForRemoteNotificationsWithDeviceToken** függvény aljához annak ellenőrzéséhez, hogy a **installationId** és **a pushChannel** hozzá van-e rendelve az értékekhez.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Hozza létre a **Modellek,** **szolgáltatások**és **segédprogramok** mappákat a projekthez később hozzáadni kívánt alapvető összetevőkhöz.

1. Ellenőrizze, hogy a projekt fizikai eszközön épül-e fel és fut-e. A leküldéses értesítések nem tesztelhetők a szimulátor használatával.

### <a name="create-models"></a>Modellek létrehozása

Ebben a lépésben hozzon létre egy modellkészletet, amely az [Értesítési központok REST API-hasznos](/rest/api/notificationhubs/) adatait képviseli, és tárolja a szükséges megosztott hozzáférésű aláírás (SAS) tokenadatait.

1. Adjon hozzá egy **PushTemplate.swift** nevű új Swift fájlt a **Modellek** mappához. Ez a modell egy struct-t biztosít, amely egy egyedi sablon **testét** képviseli a **DeviceInstallation** hasznos adat részeként.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Adjon hozzá egy **DeviceInstallation.swift** nevű új Swift fájlt a **Modellek** mappához. Ez a fájl egy struct-ot határoz meg, amely az eszköztelepítés létrehozásához vagy frissítéséhez a hasznos adatot **jelöli.** Adja hozzá az alábbi kódot a fájlhoz:

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

1. Adjon hozzá egy **TokenData.swift** nevű új Swift fájlt a **Modellek** mappához. Ez a modell egy SAS-jogkivonat és annak lejárati tárolására lesz használva.

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

### <a name="generate-a-sas-token"></a>SAS-jogkivonat létrehozása

Az értesítési központok ugyanazt a biztonsági infrastruktúrát használják, mint az Azure Service Bus. A REST API hívásához programozott módon létre kell [hoznia egy SAS-jogkivonatot,](/rest/api/eventhub/generate-sas-token) amely a kérelem **engedélyezési** fejlécében használható.  

Az eredményül kapott token formátuma a következő:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Maga a folyamat magában foglalja ugyanazt a hat kulcsfontosságú lépést:  

1. A lejárat [imázsa UNIX Epoch](https://en.wikipedia.org/wiki/Unix_time) időformátumban, ami azt jelenti, hogy az 1970.
1. Az elérni kívánt erőforrást jelölő **ResourceUrl** formázása, így százalékban kódolt és kisbetűs. A **ResourceUrl** rendelkezik az űrlappal. `'https://<namespace>.servicebus.windows.net/<hubName>'`
1. A **StringToSign**előkészítése , amely `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`a formátumú.
1. Számítástechnika és Base64 kódolás az **aláírás** segítségével a HMAC-SHA256 kivonat a **StringToSign** érték. A kivonatérték a **kapcsolati karakterlánc** **kulcsrészével** együtt használatos a megfelelő **engedélyezési szabályhoz.**
1. A Base64 kódolású **aláírás formázása,** így százalékban kódolt.
1. A token összeállítása a várt formátumban **urlEncodedSignature**, **ExpiryEpoch**, **KeyName**és **UrlEncodedResourceUrl** értékek használatával.

Tekintse meg az [Azure Service Bus dokumentációját](../service-bus-messaging/service-bus-sas.md) a megosztott hozzáférés-aláírás alaposabb áttekintéséhez, valamint arról, hogy az Azure Service Bus és az Értesítési központok hogyan használják azt.

Ennek a Swift példának az alkalmazásában az Apple nyílt forráskódú **CommonCrypto** könyvtárát fogja használni az aláírás kivonatolásához. Mivel ez egy C könyvtár, swift-ben nem érhető el a dobozból. A könyvtárat áthidaló fejléc használatával teheti elérhetővé.

Az áthidaló fejléc hozzáadása és konfigurálása:

1. Az Xcode-ban válassza > a **Fájlúj** > **New** > **fájlfejlécfájl lehetőséget.****File** Nevezze el a **Fejlécfájlt A BridgingHeader.h fájlnak.**

1. A **CommonHMAC.h**fájl importálásához szerkeszti a fájlt:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Frissítse a cél **buildbeállításait,** hogy az áthidaló fejlécre hivatkozzon:

   1. Koppintson a **PushDemo** projektre, és görgessen le a **Swift Compiler** szakaszhoz.

   1. Győződjön meg arról, hogy a **C jelű kompatibilitási fejléc telepítése** beállítás **Igen**.

   1. Adja meg `'<ProjectName>/BridgingHeader.h'` a fájl elérési útját az **Objective-C áthidaló fejléc beállításba.** Ez az áthidaló fejlécünk fájlelérési útja.

   Ha nem találja ezeket a beállításokat, győződjön meg arról, hogy az **Alapszintű** vagy a Testreszabott nézet helyett a **Minden** nézet van **kiválasztva.**

   Sok harmadik féltől származó nyílt forráskódú burkoló könyvtár áll rendelkezésre, amelyek egy kicsit megkönnyíthetik a **CommonCrypto** használatát. Az ilyen könyvtárak megvitatása azonban nem tartozik e cikk hatálya alá.

1. Adjon hozzá egy **TokenUtility.swift** nevű új Swift fájlt a **Segédprogramok mappába,** és adja hozzá a következő kódot:

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

   Ez a segédprogram magában foglalja a SAS-jogkivonat létrehozásáért felelős logikát.

   Ahogy azt korábban vázoltuk, a **getSasToken** függvény vezényli a jogkivonat előkészítéséhez szükséges magas szintű lépéseket. A függvényt az oktatóanyag későbbi részében a telepítési szolgáltatás fogja meghívni.

   A másik két függvényt a **getSasToken** függvény nevezi meg: **sha256HMac** az aláírás kiszámításához és **urlEncodedString** a kapcsolódó URL-karakterlánc kódolásához. Az **urlEncodedString** függvény szükséges, mivel a beépített **addingPercentEncoding** függvény használatával nem lehet elérni a szükséges kimenetet.

   Az [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) kiváló példa arra, hogyan közelíti meg ezeket a műveleteket a C célkitűzés. Az Azure Service Bus SAS-tokenekkel kapcsolatos további információk az [Azure Service Bus dokumentációjában](../service-bus-messaging/service-bus-sas.md)találhatók.

1. Az **AppDelegate.swift**alkalmazásban adja hozzá a következő kódot a *didRegisterForRemoteNotificationsWithDeviceToken* függvényhez annak ellenőrzéséhez, hogy a **TokenUtility.getSasToken** érvényes jogkivonatot hoz-e létre.
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Ügyeljen arra, hogy cserélje le a helyőrző értékeket a **baseAddress** karakterlánc saját

### <a name="verify-the-sas-token"></a>A SAS-jogkivonat ellenőrzése

Mielőtt megvalósítana a telepítési szolgáltatást az ügyfélben, ellenőrizze, hogy az alkalmazás megfelelően hozza-e létre a SAS-jogkivonatot a választott HTTP-segédprogram használatával. Abból a célból, ez a bemutató, a mi eszköz a választás lesz **Postman**.

Jegyezze fel az alkalmazás által létrehozott **telepítési azonosítóés** **jogkivonat-értékeket.**

A **telepítésAPI** meghívásához kövesse az alábbi lépéseket:

1. A **Postman**alkalmazásban nyisson meg egy új lapot.

1. Állítsa be a **GET kérést,** és adja meg a következő címet:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. A kérelemfejlécek konfigurálása a következőképpen:

   | Kulcs           | Érték            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Engedélyezés | \<sasToken>       |
   | x-ms-verzió  | 2015-01          |

1. Válassza a Jobb felső sarokban, a **Mentés** gomb alatt megjelenő **Kód** gombot. A kérelemnek a következő példához hasonlóan kell kinéznie:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Kattintson a **Küldés** gombra.

Ezen a ponton nincs regisztráció a megadott **telepítésazonosítóhoz.** Az ellenőrzésnek "404 nem található" választ kell eredményeznie, nem pedig "401 jogosulatlan" választ. Ennek az eredménynek meg kell erősítenie, hogy a SAS-jogkivonat elfogadásra került.

### <a name="implement-the-installation-service-class"></a>A telepítési szolgáltatásosztály megvalósítása

Ezután valósítja meg az alapvető wrapper körül [a létesítmények REST API.](/rest/api/notificationhubs/create-overwrite-installation)  

Adjon hozzá egy Új Swift **fájlt, a(z) NotificationRegistrationService.swift** fájlt a **Szolgáltatások** mappába, majd adja hozzá a következő kódot a fájlhoz:

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
    private var tokenExpiryDate : Date? = nil
    
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
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
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

A szükséges részletek az inicializálás részeként szolgálnak. Címkék és sablonok opcionálisan átadják a **regiszter** függvény részét képezik az **eszköz telepítési** JSON hasznos teher.  

A **regiszter** funkció meghívja a többi magánfüggvényt a kérelem előkészítéséhez. A válasz beérkezése után a program megnevezi a befejezést, és jelzi, hogy a regisztráció sikeres volt-e.  

A kérésvégpontot a **getBaseAddress** függvény hozza létre. Az építés az értesítési központ paramétereit *használja, névteret* és *nevet,* amelyet az inicializálás során adtak meg.  

A **getSasToken** függvény ellenőrzi, hogy az aktuálisan tárolt jogkivonat érvényes-e. Ha a jogkivonat nem érvényes, a függvény meghívja a **TokenUtility-t** egy új jogkivonat létrehozásához, majd tárolja azt, mielőtt visszaadna egy értéket.

Végül **az encodeToJson** a megfelelő modellobjektumokat JSON-sá alakítja a kérelem törzsének részeként.

### <a name="invoke-the-notification-hubs-rest-api"></a>Az értesítési központok REST API-jának meghívása

Az utolsó lépés az **AppDelegate** frissítése, hogy **az NotificationRegistrationService használatával** regisztráljon **a NotificationHub**szolgáltatással.

1. Nyissa meg **az AppDelegate.swift** et, és adjon hozzá osztályszintű változókat a **NoficiationRegistrationService** és az általános PushTemplate szolgáltatásra mutató hivatkozás **tárolásához:**

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. Ugyanebben a fájlban frissítse a **didRegisterForRemoteNotificationsWithDeviceToken** függvényt, hogy inicializálja a **NotificationRegistrationService** szolgáltatást a szükséges paraméterekkel, majd hívja meg a **regiszter** függvényt.

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

    Annak érdekében, hogy egyszerű legyen, néhány nyomtatási utasítást fog használni a kimeneti ablak frissítéséhez a **regiszterművelet** eredményével.

1. Most építse fel és futtassa az alkalmazást egy fizikai eszközön. A kimeneti ablakban a "Regisztrált" jelenik meg.

## <a name="test-the-solution"></a>Tesztelje az oldatot

Alkalmazásunk ebben a szakaszban regisztrálva van a **NotificationHub-on,** és leküldéses értesítéseket kaphat. Az Xcode-ban állítsa le a hibakeresőt, és zárja be az alkalmazást, ha az még fut. Ezután ellenőrizze, hogy az **eszköztelepítési** adatok a várt módon jelennek-e meg, és hogy az alkalmazásunk most már fogadhat-e leküldéses értesítéseket.  

### <a name="verify-the-device-installation"></a>Az eszköz telepítésének ellenőrzése

Most már ugyanazt a kérést, mint korábban a **Postman** segítségével [ellenőrzi a SAS token](#verify-the-sas-token). Feltételezve, hogy a SAS-jogkivonat még nem járt le, a válasznak tartalmaznia kell a megadott telepítési részleteket, például a sablonokat és a címkéket.

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

Ha az előző **SAS-jogkivonat** lejárt, hozzáadhat egy **töréspontot** a **TokenUtility** osztály **24-es sorához** egy új **SAS-jogkivonat** lekéréséhez és az **engedélyezési** fejléc frissítéséhez az új értékkel.

### <a name="send-a-test-notification-azure-portal"></a>Tesztértesítés küldése (Azure Portal)

Az értesítések fogadásának leggyorsabb tesztelésére az Azure Portalon található értesítési központ tallózása:

1. Az Azure Portalon keresse meg az **értesítési** központ Áttekintés lapját.

1. Válassza a **Küldés teszt**lehetőséget, amely a portálablak bal felső részén az **Essentials** összegzés efölött található:

    ![Értesítési központok Essentials összefoglaló teszt küldési gombja](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Válassza az **Egyéni sablon elemet** a **Platformok** listából.

1. Írja be az **12345 értéket** a **Küldés címke kifejezéshez.** Ezt a címkét már korábban megadta a telepítésben.

1. A JSON-tartalomban tetszés szerint szerkeszti az **üzenetet:**

    ![Értesítési központok tesztküldés](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Válassza a **Küldés**lehetőséget. A portálnak jeleznie kell, hogy az értesítés sikeresen elküldve lett-e az eszközre:

    ![Értesítési központok tesztküldési eredményei](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Feltéve, hogy az alkalmazás nem fut az előtérben, az eszköz **értesítési központjában** is meg kell jelennie egy értesítésnek. Az értesítésre koppintva meg kell nyitnia az alkalmazást, és meg kell jelenítenie a riasztást.

    ![Példa értesítés érkezett](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Tesztértesítés küldése (Levélszolgáltató)

Értesítéseket küldhet a [REST API-n](/rest/api/notificationhubs/) keresztül a **Postman**használatával, ami kényelmesebb módja lehet a tesztelésnek.

1. Nyisson meg egy új lapot a **Postman**ben.

1. Állítsa be a **postai**kérelmet, és adja meg a következő címet:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. A kérelemfejlécek konfigurálása a következőképpen:

   | Kulcs                            | Érték                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | alkalmazás/json;charset=utf-8 |
   | Engedélyezés                  | \<sasToken>                     |
   | ServiceBusNotification-formátum  | sablon                       |
   | Címkék                           | "12345"                        |

1. Állítsa be a kérelem **törzsét** a **RAW - JSON (application.json)** használatára a következő JSON tartalommal:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Válassza a **Kód** gombot, amely az ablak jobb felső részén, a **Mentés** gomb alatt található. A kérelemnek a következő példához hasonlóan kell kinéznie:

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

1. Kattintson a **Küldés** gombra.

Meg kell **kapnia egy 201 Létrehozott** sikerességi állapotkódot, és meg kell kapnia az értesítést az ügyféleszközön..

## <a name="next-steps"></a>További lépések
Most már rendelkezik egy alapvető iOS Swift alkalmazással, amely a [REST API-n](/rest/api/notificationhubs/) keresztül csatlakozik egy értesítési központhoz, és értesítéseket küldhet és fogadhat. További információkért tekintse át a következő cikkeket:

- [Az Azure értesítési központok áttekintése](notification-hubs-push-notification-overview.md)
- [Értesítési központok REST API-jai](/rest/api/notificationhubs/)
- [Értesítési központok SDK a háttér-műveletek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Értesítési központok SDK a GitHubon](https://github.com/Azure/azure-notificationhubs)
- [Regisztráció alkalmazás háttérablakával](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Regisztráció kezelése](notification-hubs-push-notification-registration-management.md)
- [Címkék használata](notification-hubs-tags-segment-push-message.md) 
- [Egyéni sablonok használata](notification-hubs-templates-cross-platform-push-messages.md)
- [A Service Bus hozzáférés-vezérlése megosztott hozzáférés-aláírásokkal](../service-bus-messaging/service-bus-sas.md)
- [SAS-tokenek programozott létrehozása](/rest/api/eventhub/generate-sas-token)
- [Apple biztonság: közös titkosítás](https://developer.apple.com/security/)
- [UNIX-korszak idő](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC között](https://en.wikipedia.org/wiki/HMAC)
