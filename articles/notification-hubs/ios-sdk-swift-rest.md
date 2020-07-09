---
title: Leküldéses értesítések küldése Swift iOS-alkalmazásokba az Azure Notification Hubs és a REST API-k használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan használható az Azure Notification Hubs és a REST API-k leküldéses értesítések küldésére iOS-eszközökre.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126346"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>Oktatóanyag: leküldéses értesítések küldése Swift iOS-alkalmazásoknak Notification Hubs REST API-k használatával

Ez az oktatóanyag azt ismerteti, hogyan használható az Azure Notification Hubs, hogy leküldéses értesítéseket küldjön egy iOS-alkalmazásnak a REST API-k használatával.

Ez az oktatóanyag a következő lépéseken vezet végig:

- Hozzon létre egy minta iOS-alkalmazást.
- Az iOS-alkalmazás csatlakoztatható az Azure Notification Hubshoz.
- Teszt leküldéses értesítések küldése.
- Ellenőrizze, hogy az alkalmazás fogad-e értesítéseket.

Az oktatóanyag teljes kódja letölthető a [githubról](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- Egy [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)-t futtató Mac, valamint egy érvényes, a kulcstartóba telepített fejlesztői tanúsítvány.

- Egy iPhone vagy iPad, amely az iOS 10-es vagy újabb verzióját futtatja.

- A fizikai eszköz regisztrálva van az [Apple Portalon](https://developer.apple.com/),   és a tanúsítványhoz van társítva.

Mielőtt továbblépne, mindenképpen tekintse át az [Azure Notification Hubs for iOS-alkalmazások](https://go.microsoft.com/fwlink/?linkid=2129801) első lépéseit ismertető oktatóanyagot, amellyel leküldéses hitelesítő adatokat állíthat be és konfigurálhat az értesítési központban. Ha még nem rendelkezik az iOS-fejlesztéssel kapcsolatos korábbi tapasztalatokkal, kövesse az alábbi lépéseket.

> [!NOTE]
> A leküldéses értesítések konfigurációs követelményei miatt a leküldéses értesítéseket az iOS-emulátor helyett fizikai iOS-eszközön (iPhone vagy iPad) kell üzembe helyeznie és tesztelni.

A következő fejezetekben olyan iOS-alkalmazást hoz létre, amely csatlakozik az értesítési központhoz.

## <a name="create-an-ios-project"></a>IOS-projekt létrehozása

1. A Xcode-ben hozzon létre egy új iOS-projektet, és válassza ki az **Egynézetes alkalmazás**   sablonját.

2. Az új projekt beállításainak megadásakor:
   - Adja meg a **termék nevét**   (PushDemo) és a **szervezet azonosítóját**   ( `com.<organization>` ), amelyet a **köteg azonosítójának**   az Apple Developer Portalon való beállításakor használt.
   - Válassza ki azt a **csoportot**   , amelyhez be lett ÁLLÍTVA az **alkalmazás azonosítója**   .
   - Állítsa a **nyelvet**   a **Swift**értékre.
   - Kattintson a  **Tovább** gombra.

3. Hozzon létre egy új, **SupportingFiles**nevű mappát.

4. Hozzon létre egy **devsettings. plist**nevű új p-list fájlt   a **SupportingFiles**   mappában. Ügyeljen arra, hogy ezt a mappát hozzáadja a **gitignore**-   fájlhoz, így a git-tárház használatakor nem véglegesíti a rendszer. Éles alkalmazásokban feltételesen állíthatja be ezeket a titkokat egy automatizált fordítási folyamat részeként. Ezek a beállítások nem szerepelnek ebben az oktatóanyagban.

5. Frissítse a **devsettings. plist**fájlt   , hogy a következő konfigurációs bejegyzéseket használja az Ön által kiépített értesítési központ saját értékeivel:

   | **Kulcs**                  | **Típus** | **Érték**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | Sztring   | `<hubKey>`       |
   | notificationHubKeyName   | Sztring   | `<hubKeyName>`   |
   | notificationHubName      | Sztring   | `<hubName>`      |
   | notificationHubNamespace | Sztring   | `<hubNamespace>` |

   A szükséges értékeket megkeresheti az értesítési központ erőforrásának Azure Portalban való navigálásával. Különösen a **notificationHubName**   és a **notificationHubNamespace**   értékek az alapvető erőforrások összegzésének jobb felső sarkában találhatók **Essentials**   az **Áttekintés**   oldalon.

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="Kötelező értékek":::

   A **notificationHubKeyName**és a notificationHubKey értékeket úgy is megtalálhatja, hogy megkeresi a hozzáférési házirendeket    **notificationHubKey**   , és kiválasztja a megfelelő hozzáférési **szabályzatot**   , például a **DefaultFullSharedAccessSignature**-t. **Access Policy** Ezután másolja az **elsődleges**   notificationHubKeyName értékkel előre rögzített értéket,  `SharedAccessKeyName=`   valamint a notificationHubKey előtaggal ellátott értéket **notificationHubKeyName**  `SharedAccessKey=`   . **notificationHubKey** A következő formátumúnak kell lennie a kapcsolatok karakterláncának:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Az egyszerűség kedvéért válassza a **DefaultFullSharedAccessSignature**lehetőséget, hogy a token használatával küldjön értesítéseket. A gyakorlatban a **DefaultListenSharedAccessSignature**   jobb választás olyan helyzetekben, amikor csak az értesítéseket szeretné kapni.

6. A **Project Navigator**alatt válassza ki a **projekt nevét**,   majd válassza az **általános**   lapot.

7. Keresse meg az **identitást**   , majd állítsa be a **köteg azonosító**   értékét úgy, hogy az megfeleljen az  `com.<organization>.PushDemo` előző lépésben az **alkalmazás-azonosítóhoz**használt értéknek   .

8. Keresse meg az **aláírási & képességeket**, majd válassza ki a megfelelő **csapatot**az    **Apple Developer-fiókjához**. A **csapat**   értékének meg kell egyeznie azzal, amelynek alapján létrehozta a tanúsítványokat és a profilokat.

9. A Xcode automatikusan le kell töltenie a megfelelő **létesítési profil**   értékét a **köteg azonosítója**alapján. Ha nem látja az új **létesítési profil**   értékét, próbálja meg frissíteni az **aláíró identitás**profiljait a    **Xcode**, a **Beállítások**, a **fiók**, majd a **kézi profilok letöltése**   gombra kattintva a profilok letöltéséhez.

10. Továbbra is az **aláírási & képességek**   lapon kattintson a **+ képesség**   gombra, és koppintson duplán a **leküldéses értesítések**a listából lehetőségre,   hogy a **leküldéses értesítések**   engedélyezve legyenek.

11. Nyissa meg a **AppDelegate. Swift**   fájlt a **UNUserNotificationCenterDelegate**   protokoll megvalósításához, és adja hozzá a következő kódot az osztály tetejéhez:

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

    Ezeket a tagokat később fogja használni. Konkrétan a **címkék**   tagot a regisztráció részeként fogja használni egy **egyéni sablon**használatával. A címkékkel kapcsolatos további információkért lásd: [címkék a regisztrációhoz](notification-hubs-tags-segment-push-message.md)   és a [sablonok regisztrálásához](notification-hubs-templates-cross-platform-push-messages.md).

12. Ugyanebben a fájlban adja hozzá a következő kódot a **didFinishLaunchingWithOptions** függvényhez:

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

    Ez a kód a **devsettings. plist**fájlból kérdezi le a beállításokat, beállítja a **AppDelegate**   osztályt **UNUserNotificationCenter**   delegált, leküldéses értesítések engedélyezését kéri, majd meghívja a **registerForRemoteNotifications**.

    Az egyszerűség kedvéért a kód csak az iOS 10-es és újabb verzióit támogatja. A korábbi iOS-verziók támogatását a megfelelő API-k és megközelítések feltételeit követve is hozzáadhatja, ahogy azt a szokásos módon tenné.

13. Ugyanebben a fájlban adja hozzá a következő kódot:

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

    Ez a kód a **telepítésazonosító**   és a **pushChannel**   értékeket használja az értesítési központban való regisztráláshoz. Ebben az esetben a **UIDevice. Current. identifierForVendor**használatával   egyedi értéket kell megadnia az eszköz azonosításához, majd formáznia kell a **deviceToken**a   kívánt **pushChannel**érték megadásához   . A **showAlert**   függvény csak egy szöveges üzenet szövegének megjelenítésére szolgál.

14. Továbbra is a **AppDelegate. Swift**   fájlban adja hozzá a **willPresent**   és a **didReceive**   függvényt a **UNUserNotificationCenterDelegate**. Ezek a függvények riasztást jelenítenek meg, ha értesítést kapnak arról, hogy az alkalmazás az előtérben vagy a háttérben fut.

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

15. Adjon hozzá `print` utasításokat a **didRegisterForRemoteNotificationsWithDeviceToken**függvény aljához   annak ellenőrzéséhez, hogy a **telepítésazonosító**   és a **pushChannel**   vannak-e hozzárendelve:

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. Hozza létre a **modelleket**, **szolgáltatásokat**és **segédprogramokat**   a projekthez később felvenni kívánt alapvető összetevőkhöz.

17. Ellenőrizze, hogy a projekt egy fizikai eszközön épít és fut-e. A leküldéses értesítések nem vizsgálhatók a szimulátor használatával.

## <a name="create-models"></a>Modellek létrehozása

Ebben a lépésben létrehoz egy modellt, amely a [Notification Hubs REST API](/rest/api/notificationhubs/)   hasznos adatokat jelöli, és tárolja a szükséges közös hozzáférésű aláírási (SAS-) token adatait.

1. Vegyen fel egy **PushTemplate. Swift**nevű új Swift   -fájlt a **models**   mappába. Ez a modell egy olyan struktúrát határoz meg, amely egy adott sablon **törzsét**jelöli a    **DeviceInstallation**   hasznos adattartalom részeként.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Vegyen fel egy **DeviceInstallation. Swift**nevű új Swift   -fájlt a **models**   mappába. Ez a fájl egy olyan struktúrát határoz meg, amely az **eszköz telepítésének**létrehozására vagy frissítésére szolgáló adattartalmat jelöli. Adja hozzá az alábbi kódot a fájlhoz:

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

3. Vegyen fel egy **TokenData. Swift**nevű új Swift   -fájlt a **models**   mappába. Ez a modell egy SAS-token tárolására szolgál a lejáratával együtt. Adja hozzá az alábbi kódot a fájlhoz:

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

## <a name="generate-a-sas-token"></a>SAS-token létrehozása

Notification Hubs ugyanazt a biztonsági infrastruktúrát használja, mint Azure Service Bus. A REST API meghívásához [programozott módon létrehozhat egy sas-jogkivonatot](/rest/api/eventhub/generate-sas-token),   amely a kérelem **engedélyezési**   fejlécében használható.

Az eredményül kapott token a következő formátumban fog megjelenni:

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

Maga a folyamat hat lépést is magában foglal:

1. A lejárati időt a [UNIX](https://en.wikipedia.org/wiki/Unix_time)   időformátuma szerint számítja ki, ami azt jelenti, hogy az éjfél óta eltelt idő másodpercben, 1970. január 1-től.

2. Formázza azt a **ResourceUrl**   , amely az elérni kívánt erőforrást jelöli, ezért a százalékos kódolású és kisbetűs. A **ResourceUrl**   formátuma `https://<namespace>.servicebus.windows.net/<hubName>` .

3. Készítse elő a **StringToSign**, amely a következőképpen van formázva: `<UrlEncodedResourceUrl>\n<ExpiryEpoch>` .

4. Számítás és Base64 – az **aláírást**a    **STRINGTOSIGN**érték HMAC-sha256 kivonatának használatával kódolja   . A kivonat értékét a rendszer a **Key**   megfelelő engedélyezési szabály **kapcsolati karakterláncának**kulcs részeként használja   . **Authorization Rule**

5. Formázza a Base64 kódolású **aláírást**   , hogy a százalékos kódolású legyen.

6. Hozza létre a tokent a várt formátumban a **UrlEncodedSignature**, a **ExpiryEpoch**, a **Kulcsnév**és a **UrlEncodedResourceUrl**   értékek használatával.

Tekintse meg a [Azure Service Bus dokumentációt](../service-bus-messaging/service-bus-sas.md)a   közös hozzáférési aláírások részletesebb áttekintéséhez, valamint a Azure Service Bus és a Notification Hubs használatáról.

Ebben a Swift-példában az Apple nyílt forráskódú **CommonCrypto**   könyvtára segíti az aláírás kivonatolását. Mivel ez egy C könyvtár, nem érhető el a Swift-ben. A függvénytárat áthidaló fejléc használatával is elérhetővé teheti.

Az áthidaló fejléc hozzáadása és konfigurálása:

1. A Xcode-ben válassza a **fájl**, majd az **új**, majd a **fájl**lehetőséget, majd válassza a **fejléc fájl**elemet. Nevezze el a fejlécet a **BridgingHeader. h**fájlban.

2. Szerkessze a fájlt a **CommonHMAC. h**fájl importálásához:

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. Frissítse a cél- **létrehozási beállításokat**az   áthidaló fejlécre való hivatkozáshoz:

   1. Válassza ki a **PushDemo**   projektet, és görgessen le a **Swift Compiler**   szakaszhoz.

   2. Győződjön meg arról, hogy az **Objective-C kompatibilitási fejléc telepítése**   beállítás **Igen**értékre van állítva.

   3. Adja meg a fájl elérési útját  `<ProjectName>/BridgingHeader.h`   a **Objective-C áthidaló fejléc**   beállításban. Ez az áthidaló fejléc fájljának elérési útja.

   Ha nem találja ezeket a beállításokat, győződjön meg arról, hogy az **összes**   nézet ki van választva az **alapszintű**   vagy a **testreszabott**beállítás helyett.

   Számos olyan, harmadik féltől származó nyílt forráskódú burkoló könyvtár érhető el, amely a **CommonCrypto**egyszerűbb használatát teszi elérhetővé   . A kódtárak megvitatása azonban a jelen cikk hatókörén kívül esik.

4. Vegyen fel egy **TokenUtility. Swift**nevű új Swift-fájlt   a **Utilities**   mappában, és adja hozzá a következő kódot:

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

   Az előzőekben leírtaknak megfelelően a **getSasToken**   függvény a token előkészítéséhez szükséges magas szintű lépéseket hangolja össze. Ezt a funkciót a telepítési szolgáltatás fogja meghívni az oktatóanyag későbbi részében.

   A másik két függvényt a **getSasToken**   függvény hívja meg: **sha256HMac**az   aláírás feldolgozásához, valamint a **urlEncodedString**a   társított URL-karakterlánc kódolásához. A **urlEncodedString**   függvényt kötelező megadni, mivel a beépített **addingPercentEncoding**függvény használatával nem lehet elérni a szükséges kimenetet   .

   Az [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)   kiváló példa arra, Hogyan közelítheti meg ezeket a műveleteket az Objective-C-ben. Azure Service Bus SAS-jogkivonatokkal kapcsolatos további információkért tekintse meg a [Azure Service Bus dokumentációját](../service-bus-messaging/service-bus-sas.md).

5. A **AppDelegate. Swift**-ben adja hozzá a következő kódot a  `didRegisterForRemoteNotificationsWithDeviceToken`   függvényhez annak ellenőrzéséhez, hogy a **TokenUtility. getSasToken**   érvényes tokent generál-e.

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   Ügyeljen arra, hogy a **BaseAddress**karakterláncban lévő helyőrzőket cserélje le a   saját értékeire.

## <a name="verify-the-sas-token"></a>Az SAS-jogkivonat ellenőrzése

Mielőtt implementálja a telepítési szolgáltatást az ügyfélen, győződjön meg arról, hogy az alkalmazás helyesen hozza létre az SAS-tokent egy HTTP-segédprogram használatával. Ebben az oktatóanyagban az általunk választott eszköz a **Poster**.

Jegyezze fel az **installationId**   alkalmazás által generált telepítésazonosító és **jogkivonat**-   értékeket.

A **telepítési**API meghívásához kövesse az alábbi lépéseket   :

1. Nyisson meg egy új lapot a **Poster**-ben.
2. Állítsa be a kérést a **beolvasáshoz**   , és adja meg a következő címeket:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. Konfigurálja a kérelmek fejléceit az alábbiak szerint:

   | **Kulcs**       | **Érték**        |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Engedélyezés | \<sasToken\>     |
   | x-MS-Version  | 2015-01          |

4. Kattintson a **Code**    **Save (Mentés**) gombra a jobb felső sarokban megjelenő kód gombra   . A kérelemnek az alábbi példához hasonlóan kell kinéznie:

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. Kattintson a **Küldés**   gombra.

Ezen a ponton nem létezik regisztráció a megadott **telepítésazonosító**   . Az ellenőrzés "404 nem található" választ eredményez, nem pedig "401 jogosulatlan" választ. Ennek az eredménynek meg kell győződnie arról, hogy a SAS-jogkivonat el lett fogadva.

## <a name="implement-the-installation-service-class"></a>A telepítési szolgáltatás osztályának implementálása

Ezután hozzon létre egy alapszintű burkolót a [telepítések REST API](/rest/api/notificationhubs/create-overwrite-installation).

Adjon hozzá egy **NotificationRegistrationService. Swift**nevű új Swift-fájlt   a **szolgáltatások**   mappában, majd adja hozzá a következő kódot a fájlhoz:

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

A szükséges részleteket az inicializálás részeként kell megadnia. A címkék és a sablonok opcionálisan átadhatók a **Register**   függvénynek az **eszköz telepítésére**szolgáló JSON-adattartalom részét képezve   .

A **Register**   függvény meghívja a többi privát függvényt a kérelem előkészítéséhez. A válasz fogadása után a rendszer meghívja a befejezést, és jelzi, hogy a regisztráció sikeres volt-e.

A kérelem végpontját a **getBaseAddress**függvény építi ki   . Az építés az értesítési központ paraméterek *névterét*   és *nevét*használja az   inicializálás során.

A **getSasToken**   függvény ellenőrzi, hogy a jelenleg tárolt jogkivonat érvényes-e. Ha a jogkivonat érvénytelen, a függvény meghívja a **TokenUtility**   -t új jogkivonat létrehozásához, majd egy érték visszaadása előtt tárolja azt.

Végül a **encodeToJson**a   megfelelő objektummodell-objektumokat a JSON-ba konvertálja a kérelem törzsének részeként való használatra.

## <a name="invoke-the-notification-hubs-rest-api"></a>A Notification Hubs meghívása REST API

Az utolsó lépés a **AppDelegate**frissítése a    **NotificationRegistrationService**használatára a    **NotificationHub**való regisztráláshoz.

1. Nyissa meg a **AppDelegate. Swift**programot   , és adjon hozzá osztály szintű változókat a **NoficiationRegistrationService**   és az általános **PushTemplate**mutató hivatkozás tárolására:

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. Ugyanebben a fájlban frissítse a **didRegisterForRemoteNotificationsWithDeviceToken**   függvényt a **NotificationRegistrationService**inicializálásához   a szükséges paraméterekkel, majd hívja meg a **Register**   függvényt.

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

   Az egyszerűség kedvéért a kód `print` utasítások használatával frissíti a kimeneti ablakot a **regisztrálási**   művelet eredményével.

3. Hozza létre és futtassa az alkalmazást egy fizikai eszközön. A kimeneti ablakban meg kell jelennie a **regisztrációnak** .

## <a name="test-the-solution"></a>A megoldás tesztelése

Ezen a ponton az alkalmazás regisztrálva van a **NotificationHub**   -ben, és leküldéses értesítéseket kaphat. A Xcode-ben állítsa le a hibakeresőt, és ha éppen fut, akkor az alkalmazás bezárásához. Ezután győződjön meg arról, hogy az **eszköz telepítésének**   részletei a várt módon jelennek meg, és hogy az alkalmazás mostantól képes fogadni a leküldéses értesítéseket.

### <a name="verify-the-device-installation"></a>Az eszköz telepítésének ellenőrzése

Mostantól megteheti ugyanazt a kérést, mint korábban, a **Poster**   használatával [ellenőrizheti az SAS-tokent](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token). Feltételezve, hogy az SAS-jogkivonat nem járt le, a válasznak tartalmaznia kell a megadott telepítési adatokat, például a sablonokat és a címkéket.

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

Ha a korábbi SAS-jogkivonat lejárt, hozzáadhat egy töréspontot a **TokenUtility**osztály 24. sorához   egy új sas-token beszerzéséhez, és az új értékkel frissítheti az **engedélyezési**   fejlécet.

### <a name="send-a-test-notification-azure-portal"></a>Teszt értesítés küldése (Azure Portal)

A leggyorsabban tesztelhető, hogy most már fogadhat értesítéseket az értesítési központ tallózásával a Azure Portalban:

1. A Azure Portal keresse meg az **Overview**   értesítési központ áttekintés lapját.

2. Válassza a **küldési teszt**lehetőséget, amely **Essentials**meghaladja a   portál ablakának bal felső részén található Essentials összefoglalót:

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Notification Hubs Essentials összefoglaló teszt küldése":::

3. Válassza az **egyéni sablon**lehetőséget   a **platformok**   listából.

4. Adja meg a **12345**értéket   a **Send to tag kifejezéshez**. Korábban már megadta ezt a címkét a telepítésben.

5. Szükség esetén szerkessze az **üzenetet**   a JSON-adattartalomban:

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Notification Hubs teszt küldése":::

6. Válassza a **Küldés**lehetőséget. A portálnak jeleznie kell, hogy az értesítés sikeresen elküldve az eszközre:

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="Küldés eredményének tesztelése":::

   Feltételezve, hogy az alkalmazás nem az előtérben fut, az értesítési **központban**is megjelenik egy értesítés az   eszközön. Az értesítésre koppintva nyissa meg az alkalmazást, és jelenítse meg a riasztást.

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="Teszt értesítés":::

### <a name="send-a-test-notification-mail-carrier"></a>Teszt értesítés küldése (levelezési szolgáltató)

A Poster használatával küldhet értesítéseket a [Rest APIon](/rest/api/notificationhubs/)keresztül   , ami kényelmesebb módszer lehet a teszteléshez. **Postman**

1. Nyisson meg egy új lapot a **Poster**-ben.

2. Állítsa be a **post**értékre a kérést, és adja meg a következő címeket:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. Konfigurálja a kérelmek fejléceit az alábbiak szerint:

   | Kulcs                           | Érték                          |
   | ----------------------------- | ------------------------------ |
   | Content-Type                  | alkalmazás/JSON; charset = UTF-8 |
   | Engedélyezés                 | \<sasToken\>                   |
   | ServiceBusNotification – formátum | sablon                       |
   | Címkék                          | "12345"                        |

4. Konfigurálja a kérés **törzsét**   a **RAW-JSON (application.json)** használatára   a következő JSON-adattartalommal:

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. Kattintson a **kód**   gombra, amely az ablak jobb felső sarkában található **Mentés**   gombra kattint. A kérelemnek az alábbi példához hasonlóan kell kinéznie:

   ```xml
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

6. Kattintson a **Küldés**   gombra.

Szerezze be a sikeres **201**   -as kódú sikerességi állapotkódot, és fogadja el az értesítést az ügyfélszámítógépen.

## <a name="next-steps"></a>További lépések

Mostantól egy alapszintű iOS Swift-alkalmazás kapcsolódik az értesítési központhoz a [Notification Hubs REST API](/rest/api/notificationhubs/)segítségével, és értesítéseket küldhet és fogadhat. Ha többet szeretne megtudni arról, hogyan küldhet értesítéseket adott eszközökre, folytassa a következő oktatóanyaggal:

- [Oktatóanyag: leküldéses értesítések adott eszközökre](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

További információért tekintse át a következő cikkeket:

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
