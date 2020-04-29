---
title: Leküldéses értesítések küldése az Azure Notification Hubst használó Swift iOS-alkalmazásoknak | Microsoft Docs
description: Ismerje meg, hogyan küldhet értesítéseket az Azure Notification Hubst használó Swift iOS-alkalmazásokba.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336639"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Oktatóanyag: leküldéses értesítések küldése Swift iOS-alkalmazásoknak Notification Hubs-REST API használatával

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Ebben az oktatóanyagban az Azure Notification Hubs használatával leküldheti az értesítéseket egy Swift-alapú iOS-alkalmazásba a [REST API](/rest/api/notificationhubs/)használatával. Létrehoz egy üres iOS-alkalmazást is, amely leküldéses értesítéseket fogad az [Apple push Notification szolgáltatás (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)használatával.

Ez az oktatóanyag végigvezeti a következő lépéseken:

> [!div class="checklist"]
> * A tanúsítvány-aláírási kérelem fájljának előállítása.
> * Kérje le az alkalmazást leküldéses értesítésekre.
> * Hozzon létre egy létesítési profilt az alkalmazáshoz.
> * Értesítési központ létrehozása.
> * Konfigurálja az értesítési központot APNs-információkkal.
> * Az iOS-alkalmazás csatlakoztatása egy értesítési központhoz.
> * Tesztelje a megoldást.

Az oktatóanyag teljes kódja [a githubon](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest)érhető el.

## <a name="prerequisites"></a>Előfeltételek

A lépések végrehajtásához a következőkre lesz szüksége:

- Ha nem ismeri a szolgáltatást, folytassa az [Azure Notification Hubs áttekintésével](notification-hubs-push-notification-overview.md) .
- További információ a [regisztrációról és a telepítésről](notification-hubs-push-notification-registration-management.md).
- Aktív [Apple fejlesztői fiók](https://developer.apple.com).
- Egy Xcode-t futtató Mac, valamint egy érvényes, a kulcstartóba telepített fejlesztői tanúsítvány.
- Egy fizikai iPhone-eszköz, amelyen futtathat és hibakeresést végezhet, mivel nem tud leküldéses értesítéseket tesztelni a szimulátorral.
- A fizikai iPhone-eszköz regisztrálva van az [Apple Portalon](https://developer.apple.com) , és a tanúsítványhoz van társítva.
- [Azure-előfizetés](https://portal.azure.com) , ahol erőforrásokat hozhat létre és kezelhet.

Ha még nem rendelkezik az iOS-fejlesztéssel kapcsolatos korábbi tapasztalatokkal, kövesse az első alapelvek létrehozásához szükséges lépéseket. A következő fogalmakkal azonban élvezheti a megismerését:

- IOS-alkalmazások fejlesztése a Xcode és a Swift szolgáltatással.
- [Azure Notification hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) konfigurálása iOS rendszerhez.
- Az [Apple fejlesztői portál](https://developer.apple.com) és a [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Az értesítési központ csak a **sandbox** hitelesítési mód használatára lesz konfigurálva. Az éles számítási feladatokhoz ne használja ezt a hitelesítési módot.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>IOS-alkalmazás összekötése egy értesítési központtal

Ebben a szakaszban az értesítési központhoz csatlakozó iOS-alkalmazást fogja létrehozni.  

### <a name="create-an-ios-project"></a>IOS-projekt létrehozása

1. Az Xcode-ban hozzon létre egy új iOS-projektet, és válassza az **Single View Application** (Egynézetes alkalmazás) sablont.

1. Az új projekt beállításainak megadásakor:

   1. Adja meg a **termék nevét** (PushDemo) és a **szervezet azonosítóját** (`com.<organization>`), amelyet az Apple fejlesztői portálon a **Bundle azonosító** beállításakor használt.

   1. Válassza ki azt a **csoportot** , amelyhez be lett állítva az **alkalmazás azonosítója** .

   1. Állítsa a **nyelvet** a **Swift**értékre.

   1. Kattintson a **Tovább** gombra.

1. Hozzon létre egy új, **SupportingFiles**nevű mappát.

1. Hozzon létre egy **devsettings. plist** nevű új p-list fájlt a **SupportingFiles** mappában. Ügyeljen arra, hogy ezt a mappát hozzáadja a **gitignore** -fájlhoz, így a git-tárház használatakor nem véglegesíti a rendszer. Éles alkalmazásokban valószínűleg feltételesen állítja be ezeket a titkokat egy automatizált fordítási folyamat részeként. Az ilyen beállítások nem szerepelnek ebben az útmutatóban.

1. Frissítse a **devsettings. plist** fájlt, hogy a következő konfigurációs bejegyzéseket használja az Ön által kiépített értesítési központ saját értékeivel:

   | Kulcs                            | Típus                     | Érték                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | Sztring                   | \<hubKey>                  |
   | notificationHubKeyName         | Sztring                   | \<hubKeyName>              |
   | notificationHubName            | Sztring                   | \<hubName>                 |
   | notificationHubNamespace       | Sztring                   | \<hubNamespace>            |

   A szükséges értékeket megkeresheti az értesítési központ erőforrásának Azure Portalban való navigálásával. Különösen a **notificationHubName** és a **NotificationHubNamespace** értékek az **alapvető** erőforrások összegzésének jobb felső sarkában találhatók az **Áttekintés** oldalon.

   ![Notification Hubs Essentials Összefoglaló](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   A **notificationHubKeyName** és a **notificationHubKey** értékeket úgy is megtalálhatja, hogy megkeresi a hozzáférési házirendeket, és kiválasztja a `DefaultFullSharedAccessSignature`megfelelő **hozzáférési** **szabályzatot** , például:. Ezt követően másolja az értéket az **elsődleges** `SharedAccessKeyName=` `notificationHubKeyName` és a értékhez előtaggal, a esetében pedig az előtaggal ellátott `SharedAccessKey=` értéket `notificationHubKey`.

   A következő formátumúnak kell lennie a kapcsolatok karakterláncának:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Az egyszerű használat érdekében megadhatja `DefaultFullSharedAccessSignature` , hogy a token használatával küldjön értesítéseket. A gyakorlatban a `DefaultListenSharedAccessSignature` jobb választás olyan helyzetekben, amikor csak az értesítéseket szeretné kapni.

1. A **Project Navigator**alatt válassza ki a **projekt nevét** , majd válassza az **általános** lapot.

1. Keresse meg az **identitást** , majd állítsa be a **köteg azonosító** értékét `com.<organization>.PushDemo`úgy, hogy az megfeleljen az előző lépésben az **alkalmazás-azonosítóhoz** használt értéknek.

1. Keresse meg az **aláírási & képességeket**, majd válassza ki a megfelelő **csapatot** az **Apple Developer-fiókjához**. A **csapat** értékének meg kell egyeznie azzal, amelynek alapján létrehozta a tanúsítványokat és a profilokat.

1. A Xcode automatikusan le kell húznia a megfelelő **létesítési profil** értékét a **köteg azonosítója**alapján. Ha nem látja az új **létesítési profil** értékét, próbálja meg frissíteni a profilokat az **aláíró identitáshoz** a **Xcode** > **Beállítások** > **fiók** kiválasztásával, majd a profilok letöltéséhez kattintson a **manuális profilok letöltése** gombra.

1. Továbbra is az **aláírási & képességek** lapon kattintson a **+ képesség** gombra, és koppintson duplán a **leküldéses értesítések** a listából lehetőségre, hogy a **leküldéses értesítések** engedélyezve legyenek.

1. Nyissa meg a **AppDelegate. Swift** fájlt a **UNUserNotificationCenterDelegate** protokoll megvalósításához, és adja hozzá a következő kódot az osztály tetejéhez:

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

    Ezeket a tagokat később fogja használni. Konkrétan a **címkék** tagot a regisztráció részeként fogja használni egy **egyéni sablon**használatával. A címkékkel kapcsolatos további információkért lásd: [címkék a regisztrációhoz](notification-hubs-tags-segment-push-message.md) és a [sablonok regisztrálásához](notification-hubs-templates-cross-platform-push-messages.md).

1. Ugyanebben a fájlban adja hozzá a következő kódot a **didFinishLaunchingWithOptions** függvényhez:

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

    Ez a kód a **devsettings. plist**fájlból kéri le a beállítási értékeket, beállítja a **AppDelegate** osztályt **UNUserNotificationCenter** delegált, leküldéses értesítések engedélyezését kéri, majd meghívja a **registerForRemoteNotifications**.

    Az egyszerű megtartáshoz a kód *csak az iOS 10 és újabb verziókat*támogatja. A korábbi operációsrendszer-verziókkal kapcsolatos támogatást feltételesen a megfelelő API-k és megközelítések használatával veheti igénybe, ahogyan azt a szokásos módon tenné.

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

    A kód a **telepítésazonosító** és a **pushChannel** értékeket használja az értesítési központban való regisztráláshoz. Ebben az esetben a **UIDevice. Current. identifierForVendor** használatával egyedi értéket kell megadnia az eszköz azonosításához, majd formáznia kell a **DeviceToken** a kívánt **pushChannel** érték megadásához. A **showAlert** függvény csak egy szöveges üzenet szövegének megjelenítésére szolgál.

1. Továbbra is a **AppDelegate. Swift** fájlban adja hozzá a **WillPresent** és a **didReceive** függvényt a **UNUserNotificationCenterDelegate**. Ezek a függvények riasztást jelenítenek meg, ha értesítést kapnak arról, hogy az alkalmazás az előtérben vagy a háttérben fut.

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

1. Vegyen fel nyomtatási utasításokat a **didRegisterForRemoteNotificationsWithDeviceToken** függvény aljára annak ellenőrzéséhez, hogy a **telepítésazonosító** és a **pushChannel** vannak-e hozzárendelve.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Hozza létre a **modelleket**, **szolgáltatásokat**és **segédprogramokat** a projekthez később felvenni kívánt alapvető összetevőkhöz.

1. Ellenőrizze, hogy a projekt egy fizikai eszközön épít és fut-e. A leküldéses értesítések nem vizsgálhatók a szimulátor használatával.

### <a name="create-models"></a>Modellek létrehozása

Ebben a lépésben létrehoz egy modellt, amely a [Notification Hubs REST API](/rest/api/notificationhubs/) hasznos adatokat jelöli, és tárolja a szükséges megosztott hozzáférési aláírási (SAS-) token adatait.

1. Vegyen fel egy **PushTemplate. Swift** nevű új Swift-fájlt a **models** mappába. Ez a modell egy egyedi sablon **törzsét** jelképező struct-t biztosít a **DeviceInstallation** hasznos adattartalom részeként.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Vegyen fel egy **DeviceInstallation. Swift** nevű új Swift-fájlt a **models** mappába. Ez a fájl egy olyan struct-t határoz meg, amely az **eszköz telepítésének**létrehozására vagy frissítésére szolgáló adattartalmat jelképezi. Adja hozzá az alábbi kódot a fájlhoz:

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

1. Vegyen fel egy **TokenData. Swift** nevű új Swift-fájlt a **models** mappába. Ez a modell egy SAS-token tárolására szolgál majd a lejáratával együtt.

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

### <a name="generate-a-sas-token"></a>SAS-token létrehozása

Notification Hubs ugyanazt a biztonsági infrastruktúrát használja, mint Azure Service Bus. A REST API meghívásához programozott módon kell [létrehoznia egy sas-tokent](/rest/api/eventhub/generate-sas-token) , amelyet a kérelem **engedélyezési** fejlécében használhat.  

Az eredményül kapott token a következő formátumban fog megjelenni:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Maga a folyamat hat kulcsfontosságú lépést is magában foglal:  

1. A lejárati [idő a UNIX-kor](https://en.wikipedia.org/wiki/Unix_time) időformátumában történik, ami azt jelenti, hogy az éjfél óta eltelt másodpercek száma a 1970. január 1-től érvényes.
1. Formázza azt a **ResourceUrl** , amely az elérni kívánt erőforrást jelképezi, ezért a kódolás és a kisbetűs. A **ResourceUrl** rendelkezik az űrlappal `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. A **StringToSign**előkészítése, amely a következőképpen `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`van formázva:.
1. Számítástechnika és Base64 – a **StringToSign** érték HMAC-sha256 kivonatának használatával kódolja az **aláírást** . A kivonat értékét a rendszer a megfelelő **engedélyezési szabály** **kapcsolati karakterláncának** **kulcs** részeként használja.
1. A Base64 kódolású **aláírás** formázása úgy, hogy a kódolás százalékosan történjen.
1. A token létrehozása a várt formátumban a **UrlEncodedSignature**, a **ExpiryEpoch**, a **Kulcsnév**és a **UrlEncodedResourceUrl** értékek használatával.

Tekintse meg a [Azure Service Bus dokumentációját](../service-bus-messaging/service-bus-sas.md) a közös hozzáférési aláírás részletesebb áttekintéséhez, valamint a Azure Service Bus és a Notification Hubs használatáról.

Ebben a Swift-példában az Apple nyílt forráskódú **CommonCrypto** könyvtárát fogja használni az aláírás kivonatának támogatásához. Mivel ez egy C könyvtár, nem érhető el a Swift-ben. A függvénytárat áthidaló fejléc használatával is elérhetővé teheti.

Az áthidaló fejléc hozzáadása és konfigurálása:

1. A Xcode területen válassza a **fájl** > **új** > **File** > fájl**fejléc fájl**elemet. Nevezze el a fejlécet a **BridgingHeader. h**fájlban.

1. Szerkessze a fájlt a **CommonHMAC. h**fájl importálásához:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Frissítse a cél **Build-beállításait** az áthidaló fejlécre való hivatkozáshoz:

   1. Koppintson a **PushDemo** projektre, és görgessen le a **Swift Compiler** szakaszhoz.

   1. Győződjön meg arról, hogy az **Objective-C kompatibilitási fejléc telepítése** beállítás **Igen**értékre van állítva.

   1. Adja meg a fájl `'<ProjectName>/BridgingHeader.h'` elérési útját a **Objective-C áthidaló fejléc** beállításban. Ez az áthidaló fejléc fájljának elérési útja.

   Ha nem találja ezeket a beállításokat, győződjön meg arról, hogy az **összes** nézet ki van választva az **alapszintű** vagy a **testreszabott**beállítás helyett.

   Számos olyan, harmadik féltől származó nyílt forráskódú burkoló könyvtár érhető el, amely a **CommonCrypto** egyszerűbb használatát teszi elérhetővé. Az ilyen kódtárak megvitatása azonban a jelen cikk hatókörén kívül esik.

1. Vegyen fel egy **TokenUtility. Swift** nevű új Swift-fájlt a **Utilities** mappában, és adja hozzá a következő kódot:

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

   Ez a segédprogram beágyazza az SAS-token generálásához felelős logikát.

   Az előzőekben leírtaknak megfelelően a **getSasToken** függvény a token előkészítéséhez szükséges magas szintű lépéseket hangolja össze. Ezt a funkciót a telepítési szolgáltatás fogja meghívni az oktatóanyag későbbi részében.

   A másik két függvényt a **getSasToken** függvény hívja meg: **sha256HMac** az aláírás és a **urlEncodedString** a társított URL-karakterlánc kódolásához. A **urlEncodedString** függvényt kötelező megadni, mert nem lehet elérni a szükséges kimenetet a beépített **addingPercentEncoding** függvénnyel.

   Az [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) kiváló példa arra, Hogyan közelítheti meg ezeket a műveleteket az Objective-C-ben. Azure Service Bus SAS-jogkivonatokkal kapcsolatos további információkért tekintse meg a [Azure Service Bus dokumentációját](../service-bus-messaging/service-bus-sas.md).

1. A **AppDelegate. Swift**-ben adja hozzá a következő kódot a *didRegisterForRemoteNotificationsWithDeviceToken* függvényhez annak ellenőrzéséhez, hogy a **TokenUtility. getSasToken** érvényes tokent generál-e.
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Ügyeljen arra, hogy a **BaseAddress** karakterláncban lévő helyőrző értékeket cserélje le a saját

### <a name="verify-the-sas-token"></a>Az SAS-jogkivonat ellenőrzése

Mielőtt implementálja a telepítési szolgáltatást az ügyfélen, győződjön meg arról, hogy az alkalmazás helyesen hozza létre az SAS-tokent a választott HTTP-segédprogram használatával. Ebben az oktatóanyagban a választott eszköze lesz **Poster**.

Jegyezze fel az alkalmazás által generált **telepítésazonosító** és **jogkivonat** -értékeket.

A **telepítési** API meghívásához kövesse az alábbi lépéseket:

1. Nyisson meg egy új lapot a **Poster**-ben.

1. Állítsa be a kérést a **beolvasáshoz** , és adja meg a következő címeket:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Konfigurálja a kérelmek fejléceit az alábbiak szerint:

   | Kulcs           | Érték            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Engedélyezés | \<sasToken>       |
   | x-MS-Version  | 2015-01          |

1. Kattintson a **Save (Mentés** ) gombra a jobb felső sarokban megjelenő **kód** gombra. A kérelemnek az alábbi példához hasonlóan kell kinéznie:

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

Ezen a ponton nem létezik regisztráció a megadott **telepítésazonosító** . Az ellenőrzés "404 nem található" választ eredményez, nem pedig "401 jogosulatlan" választ. Ennek az eredménynek meg kell győződnie arról, hogy a SAS-jogkivonat el lett fogadva.

### <a name="implement-the-installation-service-class"></a>A telepítési szolgáltatás osztályának implementálása

A következő lépésben az alapszintű burkolót fogjuk megvalósítani a [telepítések REST API](/rest/api/notificationhubs/create-overwrite-installation).  

Adjon hozzá egy **NotificationRegistrationService. Swift** nevű új Swift-fájlt a **szolgáltatások** mappában, majd adja hozzá a következő kódot a fájlhoz:

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

A szükséges részleteket az inicializálás részeként kell megadnia. A címkék és a sablonok opcionálisan átadhatók a **Register** függvénynek az **eszköz telepítésére** szolgáló JSON-adattartalom részét képezve.  

A **Register** függvény meghívja a többi privát függvényt a kérelem előkészítéséhez. A válasz fogadása után a rendszer meghívja a befejezést, és jelzi, hogy a regisztráció sikeres volt-e.  

A kérelem végpontját a **getBaseAddress** függvény építi ki. Az építés az értesítési központ paraméterek *névterét* és *nevét* használja az inicializálás során.  

A **getSasToken** függvény ellenőrzi, hogy a jelenleg tárolt jogkivonat érvényes-e. Ha a jogkivonat érvénytelen, a függvény meghívja a **TokenUtility** -t új jogkivonat létrehozásához, majd egy érték visszaadása előtt tárolja azt.

Végül a **encodeToJson** a megfelelő objektummodell-OBJEKTUMOKAT a JSON-ba konvertálja a kérelem törzsének részeként való használatra.

### <a name="invoke-the-notification-hubs-rest-api"></a>A Notification Hubs meghívása REST API

Az utolsó lépés a **AppDelegate** frissítése a **NotificationRegistrationService** használatára a **NotificationHub**való regisztráláshoz.

1. Nyissa meg a **AppDelegate. Swift** programot, és adjon hozzá osztály szintű változókat a **NoficiationRegistrationService** és az általános **PushTemplate**mutató hivatkozás tárolására:

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. Ugyanebben a fájlban frissítse a **didRegisterForRemoteNotificationsWithDeviceToken** függvényt a **NotificationRegistrationService** inicializálásához a szükséges paraméterekkel, majd hívja meg a **Register** függvényt.

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

    Annak érdekében, hogy egyszerű legyen, néhány nyomtatási utasítást fog használni a kimeneti ablak frissítéséhez a **regisztrálási** művelet eredményével.

1. Most hozza létre és futtassa az alkalmazást egy fizikai eszközön. A kimeneti ablakban a "Registered" kifejezés látható.

## <a name="test-the-solution"></a>A megoldás tesztelése

Az alkalmazás ezen a ponton regisztrálva van a **NotificationHub** -ben, és leküldéses értesítéseket kaphat. A Xcode-ben állítsa le a hibakeresőt, és ha éppen fut, akkor az alkalmazás bezárásához. Ezután győződjön meg arról, hogy az **eszköz telepítésének** részletei a várt módon történtek, és hogy az alkalmazás most már fogadhat leküldéses értesítéseket.  

### <a name="verify-the-device-installation"></a>Az eszköz telepítésének ellenőrzése

Mostantól megteheti ugyanazt a kérést, mint korábban a **Poster** használatával [ellenőrizheti az SAS-tokent](#verify-the-sas-token). Feltételezve, hogy az SAS-jogkivonat nem járt le, a válasznak tartalmaznia kell a megadott telepítési adatokat, például a sablonokat és a címkéket.

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

Ha a korábbi **sas-jogkivonat** lejárt, hozzáadhat egy **töréspontot** a **TokenUtility** osztály **24. sorához** egy új **sas-token** beszerzéséhez, és az új értékkel frissítheti az **engedélyezési** fejlécet.

### <a name="send-a-test-notification-azure-portal"></a>Teszt értesítés küldése (Azure Portal)

A leggyorsabban tesztelhető, hogy most már fogadhat értesítéseket az értesítési központ tallózásával a Azure Portalban:

1. A Azure Portal keresse meg az értesítési központ **Áttekintés** lapját.

1. Válassza a **küldési teszt**lehetőséget, amely meghaladja a portál ablakának bal felső részén található **Essentials** összefoglalót:

    ![Notification Hubs Essentials összefoglaló teszt küldése gomb](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Válassza az **egyéni sablon** lehetőséget a **platformok** listából.

1. Adja meg a **12345** értéket a **Send to tag kifejezéshez**. Korábban már megadta ezt a címkét a telepítésben.

1. Opcionálisan szerkesztheti az **üzenetet** a JSON-adattartalomban:

    ![Notification Hubs teszt küldése](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Válassza a **Küldés**lehetőséget. A portálnak jeleznie kell, hogy az értesítés sikeresen elküldve az eszközre:

    ![Notification Hubs eredmények küldésének tesztelése](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Feltételezve, hogy az alkalmazás nem az előtérben fut, az értesítési **központban** is megjelenik egy értesítés az eszközön. Az értesítésre koppintva nyissa meg az alkalmazást, és jelenítse meg a riasztást.

    ![Értesítés érkezett példa](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Teszt értesítés küldése (levelezési szolgáltató)

Értesítéseket küldhet a [REST API](/rest/api/notificationhubs/) a **Poster**használatával, ami kényelmesebb módszer lehet a teszteléshez.

1. Nyisson meg egy új lapot a **Poster**-ben.

1. Állítsa be a **post**értékre a kérést, és adja meg a következő címeket:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Konfigurálja a kérelmek fejléceit az alábbiak szerint:

   | Kulcs                            | Érték                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | alkalmazás/JSON; charset = UTF-8 |
   | Engedélyezés                  | \<sasToken>                     |
   | ServiceBusNotification – formátum  | sablon                       |
   | Címkék                           | "12345"                        |

1. Konfigurálja a kérelem **törzsét** a **RAW-JSON (Application. JSON)** használatára a következő JSON-adattartalommal:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Kattintson a **kód** gombra, amely az ablak jobb felső sarkában található **Mentés** gombra kattint. A kérelemnek az alábbi példához hasonlóan kell kinéznie:

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

Szerezze be a sikeres **201** -as kódú sikerességi állapotkódot, és fogadja el az értesítést az ügyfélszámítógépen.

## <a name="next-steps"></a>További lépések
Most már rendelkezik egy, az értesítési központhoz csatlakoztatott alapszintű iOS Swift-alkalmazással a [Rest APIon](/rest/api/notificationhubs/) keresztül, és küldhet és fogadhat értesítéseket. További információkért tekintse át a következő cikkeket:

- [Az Azure Notification Hubs áttekintése](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API-k](/rest/api/notificationhubs/)
- [Notification Hubs SDK a háttérbeli műveletekhez](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK a GitHubon](https://github.com/Azure/azure-notificationhubs)
- [Regisztrálás az alkalmazás háttérben](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md)
- [Címkék használata](notification-hubs-tags-segment-push-message.md) 
- [Egyéni sablonok használata](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus hozzáférés-vezérlés közös hozzáférési aláírásokkal](../service-bus-messaging/service-bus-sas.md)
- [SAS-tokenek programozott módon történő előállítása](/rest/api/eventhub/generate-sas-token)
- [Apple Security: általános kriptográfia](https://developer.apple.com/security/)
- [UNIX-kor időpontja](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
