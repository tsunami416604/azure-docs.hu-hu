---
title: "A Helykapcsolathíd natív Mobile Engagement Android SDK az Android webes nézet"
description: "Hidat között, Javascript és a natív Mobile Engagement Android SDK-t futtató webes nézet létrehozása"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: cf272f3f-2b09-41b1-b190-944cdca8bba2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f4fc7b3c81747ec80974a99084eeb1acc311f11f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>A Helykapcsolathíd natív Mobile Engagement Android SDK az Android webes nézet
> [!div class="op_single_selector"]
> * [Android híd](mobile-engagement-bridge-webview-native-android.md)
> * [iOS híd](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Egyes mobil alkalmazások úgy lettek kialakítva, ahol maga az alkalmazás fejlesztett natív Android-fejlesztések, de még a képernyők listáját egy Android webes nézet belül megjelennek a hibrid alkalmazásként. Ilyen alkalmazásokon belül továbbra is használhatja a Mobile Engagement Android SDK-t, és ez az oktatóanyag leírja, hogyan érhetők el ezzel. Az alábbi példakód az Android dokumentáció alapján [Itt](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Leírja, hogyan ezt a módszert használja dokumentált megvalósításához ugyanaz a Mobile Engagement Android SDK gyakran használt módszerek úgy, hogy a webes nézet egy hibrid alkalmazásból is kezdeményezhet kérelmek nyomon követheti az eseményeket, a feladatok, a hibák, a app-info ismertetett azokat az Android SDK keresztül közben használható. 

1. Első lépésként gondoskodnia kell arról, hogy elvégezték-a [bevezető oktatóanyag](mobile-engagement-android-get-started.md) integrálható a Mobile Engagement Android SDK a hibrid alkalmazásban. Ezután, a `OnCreate` metódus a következőképpen fog kinézni.  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. Most győződjön meg arról, hogy rendelkezik-e a hibrid app egy webes nézet egy képernyőt rajta. A kódját hasonló lesz a következő ahol betöltése azt egy helyi HTML-fájlba **Sample.html** a a webes nézet a a `onCreate` metódus a képernyőn. 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. Most létrehoz egy híd nevű fájlt **WebAppInterface** amely egy burkolót hoz keresztül néhány gyakran használt Mobile Engagement Android SDK módszerek használatával a `@JavascriptInterface` megközelítést ismerteti a [Android dokumentáció](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):
   
        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
   
        import com.microsoft.azure.engagement.EngagementAgent;
   
        import org.json.JSONArray;
        import org.json.JSONObject;
   
        public class WebAppInterface {
            Context mContext;
   
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
   
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
   
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
   
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
   
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  
4. Miután a fenti híd fájl létrehoztunk, győződjön meg arról, hogy a webes nézet társítva kell. A módosítandó kell, hogy ez a `SetWebview` metódus úgy, hogy a következőképpen néznek:
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. A fenti kódrészletet a hívtuk `addJavascriptInterface` társítja a híd osztály a webes nézet és is létrejön, úgynevezett leírót **EngagementJs** a metódusok hívására híd fájlból. 
6. Most hozzon létre a következő nevű fájlt **Sample.html** a projekt egy mappát a nevű **eszközök** a webes nézet betöltődik, és ahol fog hívása a módszerek a híd fájlból.
   
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
                      log('window.onerror: ' + err);
                    }
   
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
   
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
   
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
   
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
   
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
   
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
   
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
   
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>
7. A fenti HTML-fájl a következő szempontokat vegye figyelembe:
   
   * A beviteli mezőbe, ahol megadhatja a esemény, a feladat, a hiba, a AppInfo névként használt adatok készletét tartalmazza. Amikor a látható gombra kattint, egy kérés érkezett a Javascript, ami végül az olyan módszereket hív meg felelt meg a Mobile Engagement Android SDK hívása híd fájlból. 
   * Azt a néhány statikus további információt az események, feladatok és bemutatják, hogyan ezt megteheti a még akkor is, hibákat vannak címkézést. A felesleges adatokat küldött, egy JSON karakterlánc, amely jelenik meg a `WebAppInterface` fájl elemzése, amely egy Android be `Bundle` és közlekednek az események, feladatok, hibák küldése mellett. 
   * A Mobile Engagement feladat kezdődött el a nevet, adjon meg a beviteli mezőbe, futtassa a 10 másodperc, és állítsa le. 
   * A Mobile Engagement appinfo vagy címke átadása "customer_name" statikus kulcs és a megadott bemeneti értékeként a címke értéke. 
8. Futtassa az alkalmazást, és a következő jelenik meg. Most adja meg a következőhöz hasonló vizsgálati esemény néhány nevét, majd kattintson **küldése** alá. 
   
    ![][1]
9. Ha most a **figyelő** fülre az alkalmazás, és kattintson duplán a **események -> részletek**, láthatja, hogy ez az esemény jelenik meg a statikus app-info azt küldő együtt. 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
