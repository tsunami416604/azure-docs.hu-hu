---
title: "A Helykapcsolathíd iOS webes nézet natív a Mobile Engagement IOS SDK"
description: "Hidat között, Javascript és a natív a Mobile Engagement iOS SDK szolgáltatást futtató webes nézet létrehozása"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 35f7bdbeb480122513ae2a0b04a6d8cfd426802a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>A Helykapcsolathíd iOS webes nézet natív a Mobile Engagement IOS SDK
> [!div class="op_single_selector"]
> * [Android híd](mobile-engagement-bridge-webview-native-android.md)
> * [iOS híd](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Egyes mobil alkalmazások úgy lettek kialakítva, ahol maga az alkalmazás fejlesztett Objective-C natív iOS-fejlesztések, de még a képernyők listáját megjelennek-e egy webes nézet iOS belül hibrid alkalmazásként. Ön továbbra is használhatja a Mobile Engagement iOS SDK ilyen alkalmazásokon belül, és ez az oktatóanyag leírja, hogyan érhetők el ezzel. 

Ennek ellenére nem dokumentált mindkettő elérése kétféleképpen történhet:

* Először egy leírását itt [hivatkozás](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) amely magában foglalja a regisztrálása a `UIWebViewDelegate` a webes nézet és a catch-és-azonnal-Mégse gombra a hely módosítása Javascript végezheti el. 
* Második egy alapján ez [WWDC 2013 munkamenet](https://developer.apple.com/videos/play/wwdc2013/615), tisztító, mint az első, amely azt követi a jelen útmutató, amely egy módszert. Vegye figyelembe, hogy ez a módszer csak akkor működik ios7-en és újabb verziók. 

Az iOS minta hidat képeznek a, kövesse az alábbi lépéseket:

1. Első lépésként gondoskodnia kell arról, hogy elvégezték-a [bevezető oktatóanyag](mobile-engagement-ios-get-started.md) integrálható a Mobile Engagement iOS SDK a hibrid alkalmazásban. Szükség esetén is engedélyezheti, hogy az SDK módszerek látható módon azt hajtható végre ezeket a webes nézet teszt naplózás az alábbiak szerint. 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. Most győződjön meg arról, hogy rendelkezik-e a hibrid app egy webes nézet egy képernyőt rajta. Hozzáadhat, úgy, hogy a `Main.storyboard` az alkalmazás. 
3. Ez a webes nézet társítsa a **ViewController** kattintással és húzással a webes nézet a View vezérlő leképezni kívánt jelenetben a a `ViewController.h` képernyőn helyezés szerkesztése csak az alábbiakban a `@interface` sor. 
4. Ezután a, egy párbeszédpanel jelenik kérő nevét. Adja meg a nevet a következőként **webes nézet**. A `ViewController.h` fájl a következő hasonlóan kell kinéznie:
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. A Microsoft frissíti a `ViewController.m` később fájlt, de először létrehozunk a híd fájlt, amely egy burkolót hoz keresztül néhány gyakran használt Mobile Engagement iOS SDK módszerek. Hozzon létre egy új fejlécfájlt nevű **EngagementJsExports.h** használó a `JSExport` a fent ismertetett mechanizmus [munkamenet](https://developer.apple.com/videos/play/wwdc2013/615) teszi közzé a natív iOS módszerek. 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. Ezután létre fogunk hozni a híd fájl második részét. Hozzon létre egy nevű fájlt **EngagementJsExports.m** tartalmazó fogja a tényleges burkolók létrehozása a Mobile Engagement iOS SDK metódusok meghívása végrehajtására. Is vegye figyelembe, hogy azt elemzése a `extras` átadta-e a webes nézet JavaScript, és azokat, amelyek üzembe egy `NSMutableDictionary` objektumot az Engagement SDK metódushívások átadni.  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. Most azt térjen vissza a **ViewController.m** és frissíti a következő kódot: 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. A következő szempontokat vegye figyelembe a kapcsolatos a **ViewController.m** fájlt:
   
   * Az a `loadWebView` módszer, azt letöltése folyamatban van egy helyi HTML-fájl neve **LocalPage.html** azt a következő felülvizsgálja azonosítójú. 
   * Az a `webViewDidFinishLoad` metódust, hogy vannak grabbing a `JsContext` és a burkoló osztályt társít. Ez lehetővé teszi a a burkoló a leíró használatával SDK metódusok meghívása **EngagementJs** a a webes nézet. 
9. Hozzon létre egy nevű fájlt **LocalPage.html** az alábbi kódra:
   
        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
   
               <script type="text/javascript">
   
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. A fenti HTML-fájl a következő szempontokat vegye figyelembe:
    
    * A beviteli mezőbe, ahol megadhatja a esemény, a feladat, a hiba, a AppInfo névként használt adatok készletét tartalmazza. Amikor a látható gombra kattint, egy kérés érkezett a Javascript, ami végül az olyan módszereket hív meg felelt meg a Mobile Engagement iOS SDK hívása híd fájlból. 
    * Azt a néhány statikus további információt az események, feladatok és bemutatják, hogyan ezt megteheti a még akkor is, hibákat vannak címkézést. A felesleges adatokat küldött, egy JSON karakterlánc, amely jelenik meg a `EngagementJsExports.m` fájl elemzése, amely átadott események, feladatok, hibák küldése mellett. 
    * A Mobile Engagement feladat kezdődött el a nevet, adjon meg a beviteli mezőbe, futtassa a 10 másodperc, és állítsa le. 
    * A Mobile Engagement appinfo vagy címke átadása "customer_name" statikus kulcs és a megadott bemeneti értékeként a címke értéke. 
11. Futtassa az alkalmazást, és a következő jelenik meg. Most adja meg a következőhöz hasonló vizsgálati esemény néhány nevét, majd kattintson **küldése** látható. 
    
     ![][1]
12. Ha most a **figyelő** fülre az alkalmazás, és kattintson duplán a **események -> részletek**, láthatja, hogy ez az esemény jelenik meg a statikus app-info azt küldő együtt. 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
