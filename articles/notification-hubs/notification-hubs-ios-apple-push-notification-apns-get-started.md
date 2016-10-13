<properties
    pageTitle="Leküldéses értesítések küldése iOS-re az Azure Notification Hubs használatával | Microsoft Azure"
    description="Ebből az oktatóanyagból elsajátíthatja, hogy miként használható az Azure Notification Hubs leküldéses értesítések küldéséhez iOS-alkalmazások esetén."
    services="notification-hubs"
    documentationCenter="ios"
    keywords="leküldéses értesítés,leküldéses értesítések,ios leküldéses értesítések"
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="wesmc"/>


# Leküldéses értesítések küldése iOS-re az Azure Notification Hubs használatával

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Áttekintés

> [AZURE.NOTE] Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére iOS-alkalmazásokba. Létre fog hozni egy üres iOS-alkalmazást, amely leküldéses értesítéseket fogad az [Apple Push Notification szolgáltatás (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html) használatával. 

Amikor végzett, képes lesz az értesítési központ használatával leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre.

## Előkészületek

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Az oktatóanyag teljes kódja megtalálható a [GitHubon](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

##Előfeltételek

Az oktatóanyaghoz az alábbiakra lesz szükség:

+ [Mobile Services iOS SDK 1.2.4-es verzióját]
+ Az [Xcode] legújabb verziója
+ Az iOS 8-cal (vagy újabb verzióval) kompatibilis eszköz
+ Tagság az [Apple fejlesztői programjában](https://developer.apple.com/programs/).

   > [AZURE.NOTE] A leküldéses értesítések konfigurációs követelményei miatt a leküldéses értesítéseket az iOS-szimulátor helyett egy fizikai iOS-eszközön (iPhone-on vagy iPaden) kell üzembe helyeznie és tesztelnie.

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, iOS-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##Az értesítési központ konfigurálása iOS leküldéses értesítésekhez

Ez a szakasz végigvezeti egy új értesítési központ létrehozásának és az APNS-hitelesítés konfigurálásának folyamatán a létrehozott **.p12** leküldéses tanúsítvány használatával. Ha egy már korábban létrehozott értesítési központot kíván használni, egyből az 5. lépésre ugorhat.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li>
<p>A <b>Beállítások</b> panelen kattintson az <b>Értesítési szolgáltatások</b> gombra, majd válassza az <b>Apple (APNS)</b> lehetőséget. Kattintson a <b>Tanúsítvány feltöltése</b> lehetőségre, és válassza a korábban exportált <b>.p12</b> fájlt. Ügyeljen rá, hogy a helyes jelszót adja meg.</p>
<p>Válassza a <b>Védőfal</b> módot, mivel ez használható fejlesztéshez. Az <b>Éles</b> beállítást kizárólag akkor használja, ha olyan felhasználóknak szeretne leküldéses értesítéseket küldeni, akik megvásárolták az alkalmazást az áruházból.</p>
</li>
</ol>
&emsp;&emsp;![APNS konfigurálása az Azure Portalon](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![APNS-tanúsítvány konfigurálása az Azure Portalon](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)



Az értesítési központ konfigurálva lett az APNS-sel való együttműködésre, és rendelkezik a kapcsolati karakterláncokkal az alkalmazás regisztrálásához és leküldéses értesítések küldéséhez.

##iOS-alkalmazás összekapcsolása a Notification Hubs szogáltatással

1. Az Xcode-ban hozzon létre egy új iOS-projektet, és válassza az **Single View Application** (Egynézetes alkalmazás) sablont.

    ![Xcode – Egynézetes alkalmazás][8]

2. Amikor megadja az új projekt beállításait, győződjön meg róla, hogy a **Product Name** (Terméknév) és az **Organization Identifier** (Szervezetazonosító) ugyanaz, mint amit az Apple fejlesztői portálján a csomagazonosító beállításakor használt.

    ![Xcode – projektbeállítások][11]

3. A **Targets** (Célok) alatt kattintson a projektnévre, majd a **Build Settings** (Létrehozási beállítások) lapra, és bontsa ki a **Code Signing Identity** (Kódaláíró identitás) lehetőséget, majd a **Debug** (Hibakeresés) alatt állítsa be a kódaláíró identitását. A **Levels** (Szintek) esetében a **Basic** (Alapszintű) beállítást módosítsa **All** (Összes) értékre, és a **Provisioning Profile** (Létesítési profil) értékeként állítsa be a korábban létrehozott létesítési profilt.

    Ha nem jelenik meg az Xcode-ban létrehozott új létesítési profil, frissítse az aláíró identitása profiljait. A menüsoron kattintson az **Xcode** elemre, majd a **Preferences** (Beállítások) lehetőségre, az **Account** (Fiók) lapra és a **View Details** (Részletek megtekintése) gombra. Ezután kattintson az aláírási identitására, majd kattintson a frissítési gombra a jobb alsó sarokban.

    ![Xcode – Létesítési profil][9]

4. Töltse le a [Mobile Services iOS SDK 1.2.4-es verzióját], és bontsa ki a fájlt. Az Xcode-ban kattintson a jobb gombbal a projektjére, majd kattintson az **Add Files to** (Fájlok hozzáadása a következőhöz:) lehetőségre a **WindowsAzureMessaging.framework** mappa az Xcode-projektjéhez adásához. Válassza a **Copy items if needed** (Elemek másolása, ha szükséges) lehetőséget, majd kattintson az **Add** (Hozzáadás) gombra.

    >[AZURE.NOTE] A Notification Hubs SDK jelenleg nem támogatja a bitcode-ot az Xcode 7-ben.  Állítsa az **Enable Bitcode** (Bitcode engedélyezése) beállítást **No** (Nem) értékűre a projektre vonatkozó **Build Options** (Létrehozási beállítások) között.

    ![Az Azure SDK kicsomagolása][10]

5. Adjon hozzá egy `HubInfo.h` nevű új fejlécfájlt a projektjéhez. Ez a fájl tárolja majd az állandókat az értesítési központjához.  Adja hozzá a következő definíciókat, és cserélje le a szövegkonstans helyőrzőit a *központnévre* és a *DefaultListenSharedAccessSignature* változóra, amelyet korábban feljegyzett.

        #ifndef HubInfo_h
        #define HubInfo_h
        
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
        
        #endif /* HubInfo_h */

6. Nyissa meg az `AppDelegate.h` fájlt, és adja hozzá a következő importálási irányelveket:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
        
7. Adja hozzá a következő kódot az `AppDelegate.m file` `didFinishLaunchingWithOptions` metódusában az iOS verziójától függően. Ez a kód regisztrálja az eszközleíróját az APNs szolgáltatással:

    iOS 8 esetén:

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    Az iOS 8-nál régebbi verziók esetén:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


8. Ugyanebben a fájlban adja hozzá a következő metódusokat. Ez a kód a HubInfo.h-ban megadott kapcsolati információk használatával kapcsolódik az értesítési központhoz. Ezután megadja az eszköz jogkivonatát az értesítési központnak, így az képes értesítéseket küldeni:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }


9. Ugyanebben a fájlban adja hozzá a következő metódust egy **UIAlert** megjelenítéséhez, ha értesítés érkezik, amikor az alkalmazás aktív:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

10. Hozza létre és futtassa az alkalmazást az eszközön a hibák ellenőrzése érdekében.

## Teszt leküldéses értesítések küldése


A leküldéses értesítések fogadásának az alkalmazásban való teszteléséhez leküldéses értesítéseket küldhet az [Azure Portal], a központ paneljének **Hibaelhárítás** részéből (használja a *Küldés tesztelése* lehetőséget).

![Azure portál – Küldés tesztelése][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## (Nem kötelező) Leküldéses értesítések küldése az alkalmazásból

>[AZURE.IMPORTANT] Az ügyfélalkalmazásból értesítések küldését bemutató jelen példa kizárólag tanulási célt szolgál. Mivel ehhez a `DefaultFullSharedAccessSignature` meglétére van szükség az ügyfélalkalmazásban, ez olyan kockázatot jelent az értesítési központ számára, hogy egyes felhasználók jogosulatlan értesítésküldést hajthatnak végre az ügyfelei irányába.

Ha az alkalmazáson belülről szeretne leküldéses értesítéseket küldeni, ez a szakasz egy példát mutat be, hogy ez hogyan lehetséges a REST-felület használatával.

1. Az Xcode-ban nyissa meg a `Main.storyboard` elemet, majd adja hozzá a következő felhasználói felületi összetevőket az objektumtárból, hogy engedélyezze a felhasználó számára a leküldéses értesítések küldését az alkalmazásban:

    - Egy címke címkeszöveg nélkül. Ez az értesítések küldése közbeni hibák jelentéséhez lesz használva. A **Vonalak** tulajdonságot állítsa **0** értékre, így automatikusan korlátozza a méretet a jobb és bal oldali margókhoz és a nézet tetejéhez.
    - Egy **Helyőrző** szöveggel rendelkező szövegmező **Értesítési üzenet beírása** beállításra állítva. Kényszerezze a mezőt pont a címke alá, ahogy az az alábbi ábrán látszik. Állítsa be a Nézetvezérlőt kimeneti delegáltként.
    - Egy **Értesítés küldése** című gomb pont a szövegmező alá és vízszintesen középre kényszerezve.

    A nézetnek az alábbihoz kell hasonlítania:

    ![Xcode-tervező][32]


2. [Adjon hozzá kimeneteket](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) a nézethez kapcsolt felirat- és szövegmezőkhöz, és frissítse az `interface` definícióját a `UITextFieldDelegate` és az `NSXMLParserDelegate` támogatása érdekében. Adja hozzá az alábbi három tulajdonságdeklarációt a REST API hívásának és a válasz elemzésének támogatása érdekében.

    A ViewController.h fájlnak az alábbihoz kell hasonlítania:

        #import <UIKit/UIKit.h>

        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }

        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;

        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;

        @end

3. Nyissa meg a `HubInfo.h`-t, majd adja hozzá a következő állandókat, amelyek az értesítések a központnak történő elküldéséhez lesznek használva. Cserélje le a helyőrző szövegkonstanst az aktuális *DefaultFullSharedAccessSignature* kapcsolati karakterláncra.

        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. Adja hozzá a következő `#import` utasításokat a `ViewController.h` fájlhoz.

        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"

5. A `ViewController.m` fájlban adja hozzá a következő kódot a felhasználói felület megvalósításához. Ez a kód fogja elemezni a *DefaultFullSharedAccessSignature* kapcsolati karakterláncot. A [REST API-referenciában](http://msdn.microsoft.com/library/azure/dn495627.aspx) említetteknek megfelelően ezzel az elemzett információval lesz létrehozva egy SaS-jogkivonat a **hitelesítési** kérelem fejlécéhez.

        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;

        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;

            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];

                @throw parseException;
            }

            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }

6. Frissítse a `ViewController.m` `viewDidLoad` metódusát, hogy a rendszer elemezze a kapcsolati karakterláncot a nézet betöltésekor. Emellett adja hozzá az alább látható segédprogram-metódusokat a felhasználói felület megvalósításához.  


        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





7. A(z) `ViewController.m` helyen adja hozzá a következő kódot a felhasználói felület bevezetéséhez az SaS-hitelesítő jogkivonatának létrehozásához, amely a **Hitelesítés** fejlécben lesz tartalmazva a [REST API-referenciában](http://msdn.microsoft.com/library/azure/dn495627.aspx) említettek szerint.

        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;

            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }

            return token;
        }


8. A Ctrl gomb lenyomása közben húzza az **Értesítés küldése** gombtól a(z) `ViewController.m` helyig, hogy hozzáadjon egy **SendNotificationMessage** nevű műveletet a **Touch Down** eseményhez. Frissítse a metódust a következő kóddal az értesítés a REST API használatával való elküldéséhez.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }

        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];

            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];

            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }


9. Adja hozzá a következő delegált metódust a szövegmező számára a billentyűzet bezárásának támogatásához itt: `ViewController.m`. A Ctrl gomb lenyomása közben húzza a szövegmezőtől a Nézetvezérlő ikonra a felülettervezőben, hogy a kimeneti delegáltra állítsa a nézetvezérlőt.

        //===[ Implement UITextFieldDelegate methods ]===

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }


10. Adja hozzá a következő delegáltmetódusokat a válasz `NSXMLParser` használatával történő elemzésének támogatásához itt: `ViewController.m`.

        //===[ Implement NSXMLParserDelegate methods ]===

        -(void)parserDidStartDocument:(NSXMLParser *)parser
        {
            self.statusResult = @"";
        }

        -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
            namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
            attributes:(NSDictionary *)attributeDict
        {
            NSString * element = [elementName lowercaseString];
            NSLog(@"*** New element parsed : %@ ***",element);

            if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
            {
                self.currentElement = element;
            }
        }

        -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
        {
            self.statusResult = [self.statusResult stringByAppendingString:
                [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
        }

        -(void)parserDidEndDocument:(NSXMLParser *)parser
        {
            // Set the status label text on the UI thread
            dispatch_async(dispatch_get_main_queue(),
            ^{
                [self.sendResults setText:self.statusResult];
            });
        }



11. Hozza létre a projektet, és ellenőrizze, hogy nincsenek-e hibák.


> [AZURE.NOTE] Ha egy bitcode-támogatással kapcsolatos létrehozási hibával találkozik az Xcode 7-ben, meg kell változtatni a **Build Settings (Létrehozási beállítások)** > **Enable Bitcode (ENABLE_BITCODE) (Bitcode engedélyezése (ENABLE_BITCODE))** beállítását **NO (NEM)** értékűre az Xcode-ban. A Notification Hubs SDK jelenleg nem támogatja a bitcode-ot. 

Az összes lehetséges hasznos értesítési adatot megtalálja az Apple [helyi és leküldéses értesítések programozásával foglalkozó útmutatójában].


##Annak ellenőrzése, hogy az alkalmazás képes-e leküldéses értesítéseket fogadni

A leküldéses értesítések iOS rendszeren történő teszteléséhez üzembe kell helyezni az alkalmazást egy fizikai iOS-eszközön. Nem lehet Apple leküldéses értesítéseket küldeni az iOS-szimulátor használatával.

1. Futtassa az alkalmazást és ellenőrizze, hogy a regisztráció sikeres-e, majd nyomja meg az **OK** gombot.

    ![iOS-alkalmazás leküldésesértesítés-regisztrációs tesztje][33]

2. Az [Azure Portal] a fent leírtak szerint küldhet teszt leküldéses értesítést. Ha hozzáadott kódot leküldési értesítések az alkalmazásban való küldéséhez, érintse meg a szövegmezőt értesítési üzenet beírásához. Ezután nyomja meg a **Küldés** gombot a billentyűzeten, vagy az **Értesítés küldése** gombot a nézetben az értesítési üzenet elküldéséhez.

    ![iOS-alkalmazás leküldésesértesítés-küldési tesztje][34]

3. A leküldéses értesítést a rendszer az összes, az adott értesítési központból érkező értesítések fogadására regisztrált eszközre elküldi.

    ![iOS-alkalmazás leküldésesértesítés-fogadási tesztje][35]


##Következő lépések

Ebben az egyszerű példában leküldéses értesítéseket küldött az összes regisztrált iOS-eszközre. A tanulás következő lépéseként azt javasoljuk, hogy folytassa az [Azure Notification Hubs – felhasználók értesítése iOS rendszerhez .NET-háttérrendszerrel] oktatóanyaggal, amely végigvezeti leküldéses értesítések címkék használatával való küldéséhez használható háttérrendszer létrehozásának lépésein. 

Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, továbbléphet a [Use Notification Hubs to send breaking news] (Friss hírek küldése Notification Hubs használatával) című témakörre. 

A Notification Hubs használatával kapcsolatban a [Notification Hubs használatával] foglalkozó témakörben tekinthet meg további általános információt.



<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Mobile Services iOS SDK 1.2.4-es verzióját]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Alkalmazáslap elküldése]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Saját alkalmazások]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[A Mobile Services használatának első lépéseit]: /develop/mobile/tutorials/get-started-ios
[klasszikus Azure portál]: https://manage.windowsazure.com/
[Notification Hubs használatával]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Bevezetés a leküldéses értesítések használatába a Mobile Servicesben]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs – felhasználók értesítése iOS rendszerhez .NET-háttérrendszerrel]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[A legfrissebb hírek elküldése a Notification Hubs használatával]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[helyi és leküldéses értesítések programozásával foglalkozó útmutatójában]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com


<!--HONumber=Oct16_HO1-->


