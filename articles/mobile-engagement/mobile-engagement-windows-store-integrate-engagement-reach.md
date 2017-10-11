---
title: "Univerzális Windows-alkalmazások Reach SDK-integráció"
description: "Az Azure Mobile Engagement Reach integrálása univerzális Windows-alkalmazások"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9311e998e67d8d0d56da68fc9460df32ce7ce5a9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Univerzális Windows-alkalmazások Reach SDK-integráció
Az integráció az ismertetett eljárást kell követni a [Windows Universal Engagement SDK-integráció](mobile-engagement-windows-store-integrate-engagement.md) Ez az útmutató követése előtt.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Az Engagement Reach SDK beágyazása a univerzális Windows-projekt
Nincs olyan hozzáadása. `EngagementReach`hivatkozások és erőforrások még a projektben.

> [!TIP]
> Lemezképek található, testreszabhatja a `Resources` mappa a projekt, különösen a márka ikon (hogy az Engagement ikonra az alapértelmezett). Univerzális alkalmazások át is helyezheti a `Resources` közötti alkalmazásokat, de a tartalmak megosztása a megosztott projekt mappájába kell tartani a `Resources\EngagementConfiguration.xml` alapértelmezett helyére, a fájlt, mert függő platformra.
> 
> 

## <a name="enable-the-windows-notification-service"></a>A Windows értesítési szolgáltatás engedélyezése
### <a name="windows-8x-and-windows-phone-81-only"></a>A Windows 8.x és Windows Phone 8.1 esetén
Ahhoz, hogy a **Windows értesítési szolgáltatásával** (WNS néven) az a `Package.appxmanifest` fájlja `Application UI` kattintson a `All Image Assets` a bal oldali botot mezőbe. A mező a jobb `Notifications`, módosítsa `toast capable` a `(not set)` való `(Yes)`.

### <a name="all-platforms"></a>Összes platform
Az alkalmazás a Microsoft-fiókjával, és az engagement platform szinkronizálni kell. Ehhez hozzon létre egy fiókot, vagy jelentkezzen be szüksége [windows fejlesztői központ](https://dev.windows.com). Ezután hozzon létre egy új alkalmazást, és keresse meg a biztonsági AZONOSÍTÓT és titkos kulcsot. A bevonási előtér nyissa meg az Alkalmazásbeállítás a `native push` , majd illessze be a hitelesítő adatait. Ezt követően kattintson a jobb gombbal a projektre, válassza `store` és `Associate App with the Store...`. Egyszerűen jelölje be az alkalmazás rendelkezik létrehozása előtt a szinkronizáláshoz.

## <a name="initialize-the-engagement-reach-sdk"></a>Az Engagement Reach SDK inicializálása
Módosítsa a `App.xaml.cs`:

* Helyezze be `EngagementReach.Instance.Init` után csak `EngagementAgent.Instance.Init` a a `InitEngagement` módszert:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  A `EngagementReach.Instance.Init` egy dedikált szálat futtat. Nem kell saját kezűleg elvégezni.

> [!NOTE]
> Ha máshol használják a leküldéses értesítések a az alkalmazás, akkor el kell [megosztani a leküldéses csatorna](#push-channel-sharing) az Engagement Reach.
> 
> 

## <a name="integration"></a>Integráció
Engagement két módot biztosít a Reach alkalmazásbeli szalagok és a hirdetmények és szavazások esetén közbeszúrt nézetek hozzáadása az alkalmazásban: a átfedéses integráció és a webes nézetek manuális integráció. Nem ötvözze az mindkét megközelítés ugyanazon az oldalon.

A két integrációs közötti választás sikerült összesíthető ily módon:

* A átfedéses integráció úgy is dönthet, ha a lapok már örököl az ügynök `EngagementPage`, csak cseréje kérdése `EngagementPage` által `EngagementPageOverlay` és `xmlns:engagement="using:Microsoft.Azure.Engagement"` által `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` a lapokon.
* Választhatja, hogy a webes nézetek manuális integrációs Ha azt szeretné, hogy pontosan helyezhető el a Reach felhasználói felületén a lapokon belül, vagy ha nem szeretne egy másik öröklési szint hozzáadása a lapokon. 

### <a name="overlay-integration"></a>Átfedéses integráció
Az Engagement átfedés dinamikusan kerülnek be a Reach-kampányokat a lapon való megjelenítéséhez használt felhasználói felületi elemeket. Ha az átmeneti területre nem felelnek meg az elrendezésben vegye figyelembe a webes nézetek manuális integrációs helyette.

A .xaml fájlban változásával `EngagementPage` hivatkozás`EngagementPageOverlay`

* Adja hozzá a következőt a névtér-deklarációkhoz:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* Cserélje le `engagement:EngagementPage` rendelkező `engagement:EngagementPageOverlay`:

**A EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**A EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Ezután a .cs fájlban a lap a címke `EngagementPageOverlay` helyett `EngagementPage` , majd importálja `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

* Cserélje le `EngagementPage` rendelkező `EngagementPageOverlay`:

**A EngagementPage:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**A EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Az Engagement átfedés ad hozzá egy `Grid` elem felett a lap a elrendezés és a két állnak `WebView` elemei egy szalagcím, a másik a közbeszúrt nézet.

Testre szabhatja az átmeneti területre elemek közvetlenül a `EngagementPageOverlay.cs` fájlt.

### <a name="web-views-manual-integration"></a>Webes nézetek manuális integráció
Reach keresnek a lapokon a két `WebView` elemek megjelenítése a szalagcím, valamint a közbeszúrt nézet felelős. Meg kell nyitnia egyedül az, hogy adja hozzá ezeket a két `WebView` elemek valahol a lapokon Íme egy példa:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


Ebben a példában a `WebView` megfelelően a tároló, amely automatikusan újra méretezi őket képernyő elforgatásának vagy ablak méretének módosítása esetén elemek archiválva a felhőbe.

> [!WARNING]
> Fontos, hogy az azonos elnevezési tartsa `engagement_notification_content` és `engagement_announcement_content` a a `WebView` elemek. Reach azonosítja azokat a nevük számára. 
> 
> 

## <a name="handle-datapush-optional"></a>Leíró datapush (nem kötelező)
Ha azt szeretné, hogy az alkalmazás fogadhat Reach adatleküldések, két esemény EngagementReach osztály végrehajtásához rendelkezik:

A App() konstruktorban App.xaml.cs fájlban adja hozzá:

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };

            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

Láthatja, hogy az egyes módszerek visszahívási olyan logikai érték beolvasása. Bevonási egy visszajelzést küld a a háttér-után az adatleküldés terjesztéséhez. A visszahívási hamis értéket ad vissza, ha a `exit` visszajelzés küldése lesz. Ellenkező esetben lesz `action`. Ha nem visszahívási események, be van állítva a `drop` visszajelzést az eredmény engagement.

> [!WARNING]
> Bevonási nincs Többszörösök visszajelzése van, az adatokat fogadhat. Ha azt tervezi, hogy több kezelők be egy eseményt, vegye figyelembe, hogy a visszajelzés utolsó felel meg egyik küldött. Ebben az esetben ajánlott mindig adja meg ugyanazt az értéket ne használjon egyértelmű visszajelzést az előtér-a.
> 
> 

## <a name="customize-ui-optional"></a>(Választható) felhasználói felület testreszabása
### <a name="first-step"></a>Első lépés
Azt teszik lehetővé a reach felhasználói felület testreszabása.

Ehhez létre kell hoznia egy alosztálya a `EngagementReachHandler` osztály.

**Mintakód:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

Állítsa a tartalmát a `EngagementReach.Instance.Handler` mező található az egyéni objektum a `App.xaml.cs` belül osztály a `App()` metódus.

**Mintakód:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> Alapértelmezés szerint az Engagement használja a saját végrehajtásának `EngagementReachHandler`.
> Nem kell létrehoznia a saját, és ha így tesz, nem kell minden metódus felülbírálására. Az alapértelmezett viselkedés, jelölje be a bevonási objektum.
> 
> 

### <a name="web-view"></a>Webes nézet
Alapértelmezés szerint Reach az értesítések és a lap megjelenítése fogja használni a beágyazott erőforrások a dll-fájl.

Adjon meg egy teljes testreszabási lehetőségek a webes nézet csak használjuk. Ha szeretné testre szabni elrendezések, bírálja felül közvetlenül az erőforrások fájlok `EngagementAnnouncement.html` és `EngagementNotification.html`. Bevonási kell az összes kódot `<body></body>` működéséhez. Külső címkét adhat hozzá, de `engagement_webview_area`.

Azonban dönthet úgy is használja a saját erőforrásait.

Ha szeretné felülbírálni az `EngagementReachHandler` módszereket a alosztály állapítható meg, Engagement is használható a elrendezések, de ügyeljen arra, hogy a beágyazott az engagement mechanizmus:

**Mintakód:**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Alapértelmezés szerint AnnouncementHTML van `ms-appx-web:///Resources/EngagementAnnouncement.html`. A HTML-fájl, amely a leküldéses üzenet (szöveges közlemény, webes anoucement és lekérdezési közlemény) tartalma tervezése képviseli. AnnouncementName van `engagement_announcement_content`. A webes nézet tervéhez az xaml-oldal neve.

NotfificationHTML van `ms-appx-web:///Resources/EngagementNotification.html`. Azt jelenti, hogy a HTML-fájl, amely a leküldéses üzenet értesítésének megtervezésére. NotfificationName van `engagement_notification_content`. A webes nézet tervéhez az xaml-oldal neve.

### <a name="customization"></a>Testreszabás
Értesítés és a webes nézet azt szeretné, hogy ha megőrizheti a bevonási objektum rendelkezik közlemény személyre is szabhatja. Ügyelni kell arra, hogy a webes nézet objektum leírt három alkalommal – az első alkalommal a xaml-kódban, még egyszer a .cs fájlban a "setwebview()" metódusban és harmadik idő a HTML-fájlban.

* Az XAML-kódot az aktuális grafikus elrendezés webes nézet összetevő ismerteti.
* A .cs fájlban definiálhat "setwebview()" beállította a két webes nézet (értesítés, bejelentés) dimenziója. Akkor nagyon hatékony az alkalmazás átméretezésekor.
* A bevonási html-fájlba azt írják le, a webes nézet tartalmat, tervezési és az elemek pozíciók egymás között.

### <a name="launch-message"></a>Indítsa el az üzenet
Amikor egy felhasználó egy Rendszerértesítő (egy bejelentési) kattint, az Engagement elindítja az alkalmazást, a leküldéses üzenetek a tartalom betöltése, és a megfelelő kampány lap megjelenítéséhez.

A késleltetés van a indítsa el az alkalmazás és a lap (attól függően, hogy a hálózat sebességétől) megjelenítési között.

Jelzi a felhasználóknak, hogy valami tölt, egy visual adatok, például egy folyamatjelző vagy egy folyamatjelző kell megadnia. Bevonási nem tudja kezelni, hogy saját magát, de itt néhány kezelők meg.

A visszahívás megvalósításához, "Nyilvános App() megkeresése" App.xaml.cs fájlban adja hozzá:

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

A visszahívási állíthatja be a "Nyilvános App() {}" metódusában a `App.xaml.cs` fájlt, mielőtt lehetőleg a `EngagementReach.Instance.Init()` hívható meg.

> [!TIP]
> A felhasználói felület szálából minden kezelő hívja. Nincs a MessageBox vagy a felhasználói felület kapcsolatos valami használatakor foglalkoznia.
> 
> 

## <a id="push-channel-sharing"></a>Leküldéses csatorna megosztása
Ha használ a leküldéses értesítések más célra az alkalmazás akkor rendelkezik a leküldéses csatorna megosztása Engagement SDK-szolgáltatás használatára. Ez a kihagyott leküldéses elkerülése érdekében.

* Megadhatja a saját az Engagement Reach inicializálás leküldéses csatornát. Az SDK helyett egy új kérő használja.

Frissítse az Engagement Reach inicializálása a leküldéses csatorna a `InitEngagement` metódust a `App.xaml.cs` fájlt:

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* Azt is megteheti Ha szeretné a leküldéses csatorna felhasználását követően a Reach inicializálás, akkor beállíthatja az egy visszahívási Engagement Reach egyszer lekérni a leküldéses csatorna létrejön az SDK-ban.

Állítsa be a visszahívási bárhol **után** Reach inicializálása:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Egyéni séma tipp
Azt adja meg az egyéni séma használja. A bevonási frontend alhálózatból az engagement alkalmazásban használható különböző típusú URI küldhet. Alapértelmezett séma például `http, ftp, ...` vannak kezelése Windows, illetve egy ablak lesz kérdezzen rá, ha telepítve az eszközön alapértelmezés szerinti alkalmazás. Az alkalmazás a saját egyéni séma is létrehozhat.

Az egyéni séma beállítása az alkalmazás legegyszerűbb megnyitásához a `Package.appxmanifest` nyissa meg a `Declarations` panel. Válassza ki `Protocol` a rendelkezésre álló deklarációjában görgessen a mezőbe, majd vegye fel azt. Szerkessze a `Name` mező az új protokoll a kívánt nevet.

Ez a protokoll használatához szerkesztésével a `App.xaml.cs` rendelkező a `OnActivated` metódust, és ne feledkezzen meg itt az engagement inicializálására is:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

