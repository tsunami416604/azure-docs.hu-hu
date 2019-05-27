---
title: Leküldéses értesítések az Azure Notification Hubs használatával Swift iOS-alkalmazások |} A Microsoft Docs
description: Megtudhatja, hogyan küldhet leküldéses értesítéseket a Swift iOS-alkalmazások Azure Notification Hubs használatával.
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
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994766"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>Oktatóanyag: Leküldéses értesítések küldése a Notification Hubs – REST API használatával Swift iOS-alkalmazások

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Ebben az oktatóanyagban az Azure Notification Hubs használatával leküldéses értesítések küldéséhez egy iOS Swift-alapú alkalmazás használja a [REST API-val](/rest/api/notificationhubs/). Létre fog hozni egy üres iOS-alkalmazást, amely leküldéses értesítéseket fogad az [Apple Push Notification szolgáltatás (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) használatával.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * A tanúsítvány-aláírási kérelem fájljának létrehozása
> * Az alkalmazás regisztrálása leküldéses értesítésekhez
> * Üzembe helyezési profil létrehozása az alkalmazáshoz
> * Értesítési központ létrehozása
> * Az APNS-adatait az értesítési központ konfigurálása
> * Az iOS-alkalmazás összekapcsolása a Notification Hubs szolgáltatással
> * A megoldás teszteléséhez

## <a name="prerequisites"></a>Előfeltételek
A lépések követéséhez le lesz szüksége:

- Lépkedjen végig [Azure Notification Hubs – áttekintés](notification-hubs-push-notification-overview.md) Ha nem ismeri a szolgáltatást. 
- Ismerje meg a regisztráció és a telepítés: [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md)
- Az aktív [Apple Developer-fiók](https://developer.apple.com) 
- Az xcode-ban és a egy érvényes fejlesztői tanúsítvány a kulcskarikában telepített Mac
- Egy fizikai iPhone eszköz futtathat, és a hibakereséshez (nem lehet a leküldéses értesítések tesztelése a szimulátor)
- A fizikai iPhone eszköz regisztrálva a [Apple-portál](https://developer.apple.com) és a tanúsítvánnyal társított
- Egy [Azure-előfizetés](https://portal.azure.com) ahol létrehozni és -erőforrások kezelése

Előzetes tapasztalatok nélkül első – alapelvek példában létrehozásához kövesse a lépéseket kell lennie. A következő fogalmak ismeretét azonban hasznos lesz:

- Az xcode-ban és a Swift iOS-alkalmazások készítése
- Konfigurálása egy [Azure Notification Hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) iOS-hez
- Való ismerkedés során bizonyulhat a [Apple fejlesztői portál](https://developer.apple.com) és a [Azure Portalon](https://portal.azure.com)

> [!NOTE]
> Az értesítési központ használatára lesz konfigurálva a *védőfal* csak a hitelesítési módot. Ez a hitelesítési módszer ne használjon a termelési számítási feladatokhoz.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>iOS-alkalmazás összekapcsolása a Notification Hubs szogáltatással
Ebben a szakaszban az iOS-alkalmazás, amely csatlakozni fog az értesítési központ fog létrehozni.  

### <a name="create-an-ios-project"></a>Egy iOS-projekt létrehozása
1. A **Xcode**, hozzon létre egy új iOS-projektet, és válassza ki a **egyetlen nézetben alkalmazás** sablont.
2. Ha az új projekt beállításait, kövesse az alábbi lépéseket:
    1. Ugyanaz, mint **Terméknév** (azt jelenti, **PushDemo**) és **Organization Identifier** (azt jelenti, `com.<organization>`) során használt a **csomag Azonosító** be lett állítva a **Apple fejlesztői portál**. 
    2. Válassza ki a **csapat** , amely a **Alkalmazásazonosító** lett beállítva a.
    3. Állítsa be a **nyelvi** való **Swift**.
    4. Kattintson a **Tovább** gombra
3. Hozzon létre egy új nevű **SupportingFiles**.
4. Hozzon létre egy új **plist** nevű **devsettings.plist** alatt a **SupportingFiles** mappát. Adja hozzá ezt a mappát a **gitignore** fájlt, ezért nem véglegesített használatakor egy **git-tárház**. Az éles alkalmazások esetén, akkor valószínűleg kell feltételesen beállítás titkos adatokat egy automatizált összeállítási folyamatból részeként. Azonban ez nem vonatkozik ez a bemutató részeként.
5. Frissítés **devsettings.plist** a következő konfigurációs bejegyzéseket tartalmazza (a saját értékeit használja a **értesítési központ** , kiépített):

   | Kulcs                            | Típus                     | Érték                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey\>                |
   | notificationHubKeyName         | String                   | \<hubKeyName\>            |
   | notificationHubName            | String                   | \<hubName\>               |
   | notificationHubNamespace       | String                   | \<hubNamespace\>          |
    
   A szükséges értékeket megtalálhatja az a **értesítési központ** erőforrást a **az Azure portal**. Annak a **notificationHubName** és a **notificationHubNamespace** jobb felső sarkában lévő értékek a **Essentials** összefoglaló belül a  **Áttekintés** lapot.

   ![Notification Hubs Essentials Summary](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Annak a **notificationHubKeyName** és **notificationHubKey** az értékek **hozzáférési szabályzatok**, kattintson a megfelelő a **hozzáférés A házirend**. Például: `DefaultFullSharedAccessSignature`. Majd átmásolhatja a **elsődleges kapcsolati karakterlánc** előtaggal van ellátva a érték `SharedAccessKeyName=` a `notificationHubKeyName` és az érték előtaggal `SharedAccessKey=` a a `notificationHubKey`. A kapcsolati karakterláncot a következő formátumban kell megadni:
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Legyen egyszerű, a használni kívánt *DefaultFullSharedAccessSignature* így használhatja a jogkivonatot, valamint az értesítések küldéséhez. Azonban a gyakorlatban a `DefaultListenSharedAccessSignature` lehet jobb választás olyan helyzetben, amikor csak ha értesítéseket szeretne kapni.       
6. Alatt **Project Navigator**, kattintson a **projektnév**, majd kattintson a **általános** lap
7. Keresse meg **identitás**, majd állítsa be a **Bundle Identifier** használt a beállított érték a **Alkalmazásazonosító** (az a korábbi lépésben), `'com.<organization>.PushDemo'`
8. Keresse meg **aláírás**, majd jelölje ki a megfelelő **csapat** számára a **Apple Developer-fiók** (az egy amelyben hozta létre a tanúsítványok és a profilok korábbi).  **Xcode** automatikusan lekéri a megfelelő **létesítési profil** alapján a **Bundle Identifier**. Ha nem látja az új **létesítési profil**, próbálja meg frissíteni a profilok a **aláíró identitása** (*Xcode > Beállítások > fiók > részleteinek megtekintése*). Kattintson a a **aláíró identitása**, majd kattintson a **frissítése** gombra a jobb alsó le kell töltenie a profilokat.
9. Válassza ki a **képességek** lapon és ellenőrizze, hogy **leküldéses értesítések** engedélyezve vannak.
10. Nyissa meg a **AppDelegate.swift** fájlt, végrehajtására a *UNUserNotificationCenterDelegate* protokollt, és adja hozzá a következő kódot az osztály elejéhez:
    
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

    Ezekről a tagokról később fogja használni. A *címkék* és *genericTemplate* a regisztráció részeként fogja használni. A címkék további információkért lásd: [regisztrációs címkék](notification-hubs-tags-segment-push-message.md) és [sablonregisztrációk](notification-hubs-templates-cross-platform-push-messages.md).
 
11. Ugyanebben a fájlban adja hozzá a következő kódot a *didFinishLaunchingWithOptions* függvény:

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

    Ez a kód lekéri a beállítás értékét **devsettings.plist**, beállítja a **AppDelegate** osztály a *UNUserNotificationCenter* delegálása, a kérések engedélyezése leküldéses értesítések, majd a hívások *registerForRemoteNotifications*.
    
    Legyen egyszerű, hogy a kód támogatja **iOS 10 vagy újabb csak**. A korábbi operációsrendszer-verziók támogatását a megfelelő API-k és megközelítések feltételesen használatával, mint a szokásos módon adhat hozzá.

12. Ugyanebben a fájlban adja hozzá a következő funkciókat:

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

    A kód a *installationId* és *pushChannel* értékeket szeretne regisztrálni a **értesítési központ**. Ebben az esetben használ *UIDevice.current.identifierForVendor* , adja meg egy egyedi értéket az eszköz azonosítására, és majd formázza a *deviceToken* , adja meg a kívánt *pushChannel* értéket. A *showAlert* függvény egyszerűen csak akkor jeleníti meg a néhány üzenet szövege, bemutatási céllal.

13. Még mindig **AppDelegate.swift**, adja hozzá a *willPresent* és *didReceive* **UNUserNotificationCenterDelegate** látja el riasztás megjelenítése, amikor az értesítés érkezett az alkalmazás futása során az előtér és a háttér pedig
    
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

14. Adja hozzá a nyomtatási utasításokat alján a *didRegisterForRemoteNotificationsWithDeviceToken* függvényt, hogy ellenőrizze, hogy *installationId* és *pushChannel* folyamatban van hozzárendelt értékek
15. Mappák létrehozása (**modellek**, **szolgáltatások**, és **segédprogramok**) akkor adunk hozzá a projekthez később az alapvető összetevők
16. Ellenőrizze, hogy a projekt épít, és futtat egy fizikai eszköz (leküldéses értesítések nem tesztelhető, hogy a szimulátor használatával)

### <a name="create-models"></a>Modellek létrehozása
Ebben a lépésben létrehozhat egy csoportot, a modellek, amelyek a [Notification Hubs – REST API](/rest/api/notificationhubs/) hasznos adat található, és tárolja a szükséges **SAS-jogkivonat** adatokat.


1.  Adjon hozzá egy új swift fájlt a **modellek** nevű **PushTemplate.swift**. Ez a modell biztosít egy struct jelölő a **törzs** egy egyéni sablon részeként a **DeviceInstallation** hasznos:
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Adjon hozzá egy új swift fájlt a **modellek** nevű mappát **DeviceInstallation.swift**. Ez a fájl egy jelölő a tartalom létrehozása vagy frissítése struktura meghatározása egy **eszköz telepítése**. Adja hozzá a következő kódot a fájlhoz:
    
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

3.  Adjon hozzá egy új swift fájlt alatt **modellek** nevű **TokenData.swift**. Ez a modell fogja tárolni egy **SAS-jogkivonat** együtt a lejárat

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
**A Notification Hubs** használja az ugyanazon biztonsági infrastruktúra **Azure Service Bus**. A REST API hívása, kell [programozott módon az SAS-token létrehozásához](/rest/api/eventhub/generate-sas-token) , amely a használható a **engedélyezési** a kérelem fejlécében.  

Az így létrejövő jogkivonatot az alábbi formátumban lesz: 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Maga a folyamat az azonos hat fő lépésből áll:  

1.  Lejártakor a számítástechnika [UNIX alapidőpont](https://en.wikipedia.org/wiki/Unix_time) formátum (1970. január 1. 00:00:00 UTC óta eltelt másodperc)
2.  Formázás a **ResourceUrl** (próbál hozzáférni, azaz erőforrásokra `'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`), hogy legyen kódolt- és nagybetűket
3.  Felkészülés a **StringToSign**, amely áll `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'`
4.  Számítástechnika (és base 64 kódolás) a **aláírás** használatával a **HMAC-SHA256 algoritmust** , a **StringToSign** értéket a **kulcs** része a **Kapcsolati karakterlánc** (számára a megfelelő **engedélyezési szabály**)
5.  A base 64 kódolású formázás **aláírás** , hogy legyen kódolású százalék
6.  Hozhat létre, amely a **token** a várt formátum használatával az **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, és **UrlEncodedResourceUrl** értékek

Tekintse meg a [Azure Service Bus dokumentációja](../service-bus-messaging/service-bus-sas.md) alaposabb áttekintése **közös hozzáférésű Jogosultságkód** és azok felhasználási módjáról a **Azure Service Bus** és  **A Notification Hubs**.

Ebben a példában a Swift az alkalmazásában fog használni az Apple nyílt forráskódú **CommonCrypto** könyvtár az aláírás-kivonatoló kiküszöbölni. A C-kódtár, mert nem érhető el a Swift-a-beépített. Azonban ennél is elérhető egy áthidalási fejlécet. Adja hozzá, és a áthidalási fejléc konfigurálása:

1. A **Xcode**, lépjen a **fájl**, majd **új**, majd **fájl** válassza **Fejlécfájlt** elnevezési azt *"BridgingHeader.h"*
2. Az importálandó fájl szerkesztése **CommonHMAC**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. Frissítse a céloldali **Build Settings** való hivatkozáshoz a áthidalási fejlécet. Nyissa meg a **épület beállítások** lapra, és görgessen le a **Swift fordítóprogram** szakaszban. Ügyeljen arra, hogy a **telepítése Objective-C kompatibilitási fejléc** beállítás **Igen** , és adja meg a fájl elérési útja az áthidalási fejlécet, a **Objective-C bridging fejléc**   beállítást, amely `'\<ProjectName\>/BridgingHeader.h'`. Ha ezek a beállítások nem található, győződjön meg arról, hogy a **összes** kijelölt nézet (helyett **alapszintű** vagy **testre szabott**).
    
   Nincsenek számos külső nyílt forráskódú burkoló kódtár érhető el, amelyek használatával győződjön meg, előfordulhat, hogy **CommonCrypto** könnyebb. Legyen ez a cikk nem foglalkozik.

4. Adjon hozzá egy új Swift fájlt alatt a **segédprogramok** nevű mappát **TokenUtility.swift** , és adja hozzá a következő kódot:

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
    
    Ez a segédprogram létrehozásáért felelős logikai magában foglalja a **SAS-jogkivonat**. A *getSasToken* függvény összehangolja a magas szintű előkészítéséhez szükséges lépéseket a jogkivonatot, ahogyan korábban, és a lépések a telepítési szolgáltatás által az oktatóanyag későbbi részében fogja meghívni. A többi két funkció által meghívott a *getSasToken függvény*; *sha256HMac* számítástechnikához az aláírás és *urlEncodedString* Encoding a megfelelő URL-karakterlánc. A *urlEncodedString* függvény volt szükség, azt nem lehet elérni a használatával a beépített megkövetelt kimeneti *addingPercentEncoding* függvény. A [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) kiszolgált kiváló példa bemutatja, hogyan készíthető elő ezeket a műveleteket, jóllehet az Objective-c-hez További információk a **Azure Service Bus SAS-tokeneket** tekintheti meg a [Azure Service Bus dokumentációja](../service-bus-messaging/service-bus-sas.md). 

### <a name="verify-the-sas-token"></a>A SAS-token ellenőrzése
Az ügyfél a telepítési szolgáltatás megvalósítása, előtt, hogy az alkalmazás megfelelően generáló ellenőrizheti a **SAS-jogkivonat** a választott http-segédprogram használatával. Ebben a bejegyzésben az alkalmazásában, a kívánt eszközben dolgozhat lesz **Postman**.

Jegyezze fel az a *installationId* és a *token* értékek használatával egy megfelelő eszközcsoportba helyezik az alkalmazás által létrehozott nyomtatási utasítást vagy töréspontot. 

Kövesse az alábbi lépéseket hívja a *telepítések* API:

1. A **Postman**, nyisson meg egy új lap
2. A kérelem beállítása **első** és a következő címre:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. A kérelem fejlécében a következőképpen konfigurálja:
    
   | Kulcs           | Érték            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Engedélyezés | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Kattintson a a **kód** gomb (jobb felső, alatt a **mentése** gombra). A kérelem az alábbi példához hasonlóan kell kinéznie:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. Kattintson a **küldése** gomb

Regisztráció nem létezik a megadott *installationId* ezen a ponton azonban kell eredményeznie egy **404 nem található** válasz helyett **401 Unauthorized**. Ezt az eredményt kell ellenőrizze, hogy a **SAS-jogkivonat** elfogadták.

### <a name="implement-the-installation-service-class"></a>A telepítés szolgáltatásosztály megvalósítása
Ezután az alapszintű burkolója implementálása a [telepítések REST API-val](/rest/api/notificationhubs/create-overwrite-installation).  

Adjon hozzá egy új Swift fájlt alatt a **szolgáltatások** nevű mappát **NotificationRegistrationService.swift**, majd adja hozzá a következő kódot a fájlhoz:

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
 
Az előfeltételként szükséges részletei is szerepelnek az inicializálás során. Címkékkel és sablonokkal igény szerint átadott a *regisztrálása* részét képező függvény a **eszköz telepítése** JSON-adattartalmat.  

A *regisztrálása* függvény hívások más titkos függvények a kérelem előkészítése. A választ, miután befejezése után neve jelzi-e a regisztráció sikeres volt-e.  

A kérelem-végpont alapján áll össze a *getBaseAddress* függvény használatával a **értesítési központ** paraméterek **névtér** és **neve**inicializálása során.  

A *getSasToken* függvény ellenőrzi, hogy a jelenleg tárolt jogkivonat érvényes, ellenkező esetben bude volat meg, hogy a **TokenUtility** egy új létrehozni és menteni, mielőtt érték visszaadása. 

Végül pedig a *encodeToJson* a megfelelő adatmodell-objektumokat konvertálja a JSON-ba való használatra, a kérelem törzsének részeként.

### <a name="invoke-the-notification-hubs-rest-api"></a>A Notification Hubs – REST API meghívása
Az utolsó lépés az, hogy frissíteni **AppDelegate** használata a **NotifiationRegistrationService** szeretne regisztrálni a **Értesítésiközpont**. 

1. Nyissa meg **AppDelegate.swift** , és adja hozzá a osztályszintű változót tárolják való hivatkozást a **NoficiationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. Ugyanebben a fájlban frissítse a *didRegisterForRemoteNotificationsWithDeviceToken* függvény inicializálása a **NotificationRegistrationService** a szükséges paraméterekkel, majd hívja meg a *regisztrálása* függvény.

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
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Nyomtatási utasításokat néhány használatával frissítse a kimeneti ablakban eredményét fog tartani a egyszerű, a *regisztrálása* műveletet. 

3. Most már lefordíthatja, és futtassa az alkalmazást (a fizikai eszközön). Megtekintheti az **"Registered"** a kimeneti ablakban.

## <a name="test-the-solution"></a>A megoldás teszteléséhez
Ezen a ponton az alkalmazás regisztrálva van **Értesítésiközpont** és megkaphatja a leküldéses értesítéseket. A **Xcode**, a hibakereső leállítása, és zárja be az alkalmazást (ha éppen fut). Ezután fogja ellenőrizze, hogy a **eszköz telepítése** részletek elvárt módon vannak, és az alkalmazás valóban mostantól fogadni tudják leküldéses értesítéseket.  

### <a name="verify-the-device-installation"></a>Az eszköz telepítésének ellenőrzése
Hajtsa végre a kérésben korábban ugyanúgy **Postman** a [az SAS-token ellenőrzése](#verify-the-sas-token). Feltételezve, hogy a **SAS-jogkivonat** még nem járt le, a válaszban most már tartalmazza a telepítés részleteit, például a sablonokat és címkék a megadott.  

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
Tesztelje, hogy Ön már fogadhat értesítéseket a leggyorsabb módja az, hogy nyissa meg a **értesítési központ** a a **az Azure portal**.

1. Az a **az Azure portal**, keresse meg a **áttekintése** lapján a **értesítési központ**
2. Kattintson a **Tesztküldés** (bal felső) felett a **Essentials** összegzése

    ![Notification Hubs alapvető erőforrások összefoglaló Tesztküldés gomb](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. Válasszon **egyéni sablon** listájából **platformok**
4. Adja meg **12345** számára a **küldeni a címkét alkotó kifejezés** (korábban megadott ezt a címkét a telepítés)
5. Szükség esetén szerkessze a **üzenet** a JSON-adattartalmat
    
    ![Notification Hubs küldés tesztelése](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. Kattintson a **küldése** és a portál jelezni kell-e a küldött értesítést az eszköz sikeresen megtörtént

    ![Eredmények küldése a Notification Hubs teszt](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Megjelenik egy értesítés is a **értesítési központ** (feltéve, hogy az alkalmazás nem az előtérben futó), az eszközön. Az értesítésre koppintva kell nyissa meg az alkalmazást, és a riasztás megjelenítése.

    ![Értesítés fogadása példa](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>(Postman) tesztértesítés küldése
A megfelelő keresztül értesítéseket küldhet [REST API-val](/rest/api/notificationhubs/) keresztül **Postman** jól, és lehet, hogy több kényelmesen teszteléséhez. 

1. A **Postman**, nyisson meg egy új lap
2. A kérelem beállítása **POST** , és adja meg a következő címre:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. A kérelem fejlécében a következőképpen konfigurálja:
    
   | Kulcs                            | Érték                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Engedélyezés                  | \<sasToken\>                   |
   | ServiceBusNotification-Format  | sablon                       |
   | Tags                           | "12345"                        |

4. A kérelmek **törzs** használandó **RAW - JSON (application.json)** a következő JSON-adattartalom:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. Kattintson a a **kód** gomb (jobb felső, alatt a **mentése** gombra). A kérelem az alábbi példához hasonlóan kell kinéznie:

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

5. Kattintson a **küldése** gomb

Első sikeres állapotkódot kell, és megkapja az értesítést az ügyféleszközön.

## <a name="next-steps"></a>További lépések
Egy csatlakoztatott Swift alapszintű iOS-alkalmazás most már rendelkezik egy **értesítési központ** keresztül a [REST API-val](/rest/api/notificationhubs/) és küldhet és fogadhat értesítéseket. További információkért tekintse át a következő cikkeket: 

- [Az Azure Notification Hubs – áttekintés](notification-hubs-push-notification-overview.md)
- [A Notification Hubs – REST API-k](/rest/api/notificationhubs/)
- [Notification Hubs SDK háttérbeli műveletek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [A Notification Hubs SDK a Githubon](https://github.com/Azure/azure-notificationhubs)
- [Regisztráció az alkalmazás háttérrendszerével](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md)
- [Címkék használata](notification-hubs-tags-segment-push-message.md) 
- [Egyéni sablonok használata](notification-hubs-templates-cross-platform-push-messages.md)
- [A Service Bus hozzáférés-vezérlés közös hozzáférésű jogosultságkódokkal](../service-bus-messaging/service-bus-sas.md)
- [Programozott módon a SAS-jogkivonatokat hoz létre](/rest/api/eventhub/generate-sas-token)
- [Apple biztonsági: általános kriptográfiai](https://developer.apple.com/security/)
- [UNIX alapidőpont szerint](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
