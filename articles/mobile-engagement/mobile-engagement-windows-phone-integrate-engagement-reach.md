---
title: Windows Phone Silverlight Reach SDK-integráció
description: Windows Phone Silverlight-alkalmazásokhoz az Azure Mobile Engagement Reach integrálása
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d3516a6b-db9f-4cdb-a475-4148edf81af1
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 34e5ce414ebf72fbecef6c921e57128e2658c921
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone Silverlight Reach SDK-integráció
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Az integráció az ismertetett eljárást kell követni a [Windows Phone Silverlight Engagement SDK-integráció](mobile-engagement-windows-phone-integrate-engagement.md) Ez az útmutató követése előtt.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Az Engagement Reach SDK beágyazása a Windows Phone Silverlight-projekt
Nincs olyan hozzáadása. `EngagementReach` hivatkozások és erőforrások még a projektben.

> [!TIP]
> Lemezképek található, testreszabhatja a `Resources` mappa a projekt, különösen a márka ikon (hogy az Engagement ikonra az alapértelmezett).
> 
> 

## <a name="add-the-capabilities"></a>A képességek hozzáadása
Az Engagement Reach SDK kell néhány további képességeket.

Nyissa meg a `WMAppManifest.xml` fájlt, és ne feledje, hogy a következő lehetőségei vannak deklarálva:

* `ID_CAP_PUSH_NOTIFICATION`
* `ID_CAP_WEBBROWSERCOMPONENT`

Az első egy bejelentési értesítés megjelenítésének használják az MPNS szolgáltatáshoz. A második egy böngésző feladat beágyazása az SDK szolgál.

Szerkessze a `WMAppManifest.xml` fájlt, és belül a `<Capabilities />` címke:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

## <a name="enable-the-microsoft-push-notification-service"></a>A Microsoft leküldéses értesítési szolgáltatás engedélyezése
Ahhoz, hogy a **a Microsoft leküldéses értesítéseket kezelő szolgáltatásában** (MPNS néven) a `WMAppManifest.xml` fájl rendelkeznie kell egy `<App />` rendelkező címke egy `Publisher` attribútum értékének beállítása a projekt nevét.

## <a name="initialize-the-engagement-reach-sdk"></a>Az Engagement Reach SDK inicializálása
### <a name="engagement-configuration"></a>Bevonási konfiguráció
A bevonási konfigurációs központosított a `Resources\EngagementConfiguration.xml` fájlt a projekt.

Ezt a fájlt adja meg a reach-konfiguráció szerkesztése:

* *Nem kötelező*, jelzik, hogy a natív leküldéses (MPNS) aktiválva van, vagy nem közötti `<enableNativePush>` és `</enableNativePush>` címkék, (`true` alapértelmezés szerint).
* *Nem kötelező*, közötti leküldéses csatorna jelzésére `<channelName>` és `</channelName>` címkék, adja meg, hogy jelenleg lehet-e használni az alkalmazás, vagy hagyja üresen.

Ha azt szeretné, ehelyett meg futásidőben, hívása előtt az Engagement ügynök inicializálása a következő metódust:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */

    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [!TIP]
> Megadhatja az MPNS leküldéses csatorna az alkalmazás nevét. Alapértelmezés szerint az Engagement egy nevet a appId alapján hoz létre. Adja meg a nevét, kivéve, ha szeretné-e használni a leküldéses csatorna Engagement kívül nincs szükség van.
> 
> 

### <a name="engagement-initialization"></a>Bevonási inicializálása
Módosítsa a `App.xaml.cs`:

* Adja hozzá a `using` utasításokat:
  
      using Microsoft.Azure.Engagement;
* Helyezze be `EngagementReach.Instance.Init` után csak `EngagementAgent.Instance.Init` a `Application_Launching` :
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
         EngagementAgent.Instance.Init();
         EngagementReach.Instance.Init();
      }
* Helyezze be `EngagementReach.Instance.OnActivated` a a `Application_Activated` módszert:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
         EngagementReach.Instance.OnActivated(e);
      }

> [!IMPORTANT]
> A `EngagementReach.Instance.Init` egy dedikált szálat futtat. Nem kell saját kezűleg elvégezni.
> 
> 

## <a name="app-store-submission-considerations"></a>Tároló küldésének szempontjai
Microsoft néhány szabály írja elő, amikor a leküldéses értesítések használata:

A Microsoft [alkalmazás-házirendek] -dokumentáció, szakasz 2.9:

1) Meg kell kérnie a felhasználót, hogy fogadja el a leküldéses értesítéseket. A beállításokat, majd adja hozzá egy módszerre, amellyel a leküldéses értesítések letiltása.

A EngagementReach objektum kezelheti a megújításra-a/lemondásra, két módszert kínál a `EnableNativePush()` és `DisableNativePush()`. Például létrehozhat egy beállítást a beállítások a Váltás az MPNS engedélyezése vagy letiltása.

Úgy is dönt, hogy MPNS inaktiválása az Engagement konfigurálással\<windows phone-sdk-reach-konfiguráció\>.

> 2.9.1) az alkalmazás kell először írja le az értesítéseket meg kell adni, és **szerezze be a felhasználó engedélye (részt)**, és **kell egy olyan mechanizmust, amelyen keresztül a felhasználó is tilthatják le az leküldéses fogadása értesítések**. Az értesítések a Microsoft leküldéses értesítéseket kezelő szolgáltatással megadott a leírás, a felhasználó számára biztosított konzisztensnek kell lennie, és meg kell felelnie az összes alkalmazható [alkalmazás-házirendek] [ Content Policies] és [Adott alkalmazás esetében további követelmények].
> 
> 

2) Ne használjon túl sok leküldéses értesítéseket. Bevonási értesítéseket meg fogja kezelni.

> 2.9.2) az alkalmazás és annak a Microsoft leküldéses értesítéseket kezelő szolgáltatásában kell túlzottan használjon hálózati kapacitás vagy a Microsoft leküldéses értesítéseket kezelő szolgáltatásában, vagy más módon jogosulatlanul terheket egy Windows Phone-vagy más Microsoft eszköz vagy szolgáltatást, amely a sávszélesség túl sok leküldéses értesítések alapján a Microsoft méltányosan, és nem sérüléséhez vagy bármely Microsoft Networkshöz vagy kiszolgálók, vagy bármely harmadik felek kiszolgálóival vagy hálózatokhoz kapcsolódik a Microsoft leküldéses értesítéseket kezelő szolgáltatásában.
> 
> 

3) Ne használja az MPNS criticals információk küldése. Engagement használ MPNS, ezért ez a szabály a kampányok előtér az Engagement létrehozott is vonatkozik.

> 2.9.3) a Microsoft leküldéses értesítéseket kezelő szolgáltatásában nem lehet, amelyek kritikus vagy egyéb kritikus értesítések befolyásolhatja a fontos információk vagy halál küldéséhez használt, kritikus értesítések korlátozás nélkül ideértve kapcsolódó orvosi eszköz vagy az állapot. A MICROSOFT KIFEJEZETTEN ELHÁRÍT SEMMIFÉLE, HOGY A MICROSOFT LEKÜLDÉSES ÉRTESÍTÉSI SZOLGÁLTATÁS HASZNÁLATÁT VAGY A MICROSOFT LEKÜLDÉSES ÉRTESÍTÉSI SZOLGÁLTATÁSHOZ ÉRTESÍTÉST KÉZBESÍTÉSI FOLYAMATOS, SZABAD VAGY EGYÉB HIBA VALÓSZÍNŰ, HOGY A VALÓS IDEJŰ ALAPON TÖRTÉNIK.
> 
> 

**A Microsoft nem garantálja, hogy az alkalmazás az érvényesítési folyamat fogja továbbítani, ha ezek a javaslatok nem veszik figyelembe.**

## <a name="handle-data-push-optional"></a>Kezeli az adatleküldés (nem kötelező)
Ha azt szeretné, hogy az alkalmazás fogadhat Reach adatleküldések, két esemény EngagementReach osztály végrehajtásához rendelkezik:

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

Állítsa a tartalmát a `EngagementReach.Instance.Handler` mező található az egyéni objektum a `App.xaml.cs` belül osztály a `Application_Launching` metódus.

**Mintakód:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [!NOTE]
> Alapértelmezés szerint az Engagement használja a saját végrehajtásának `EngagementReachHandler`. Nem kell létrehoznia a saját, és ha így tesz, nem kell minden metódus felülbírálására. Az alapértelmezett viselkedés, jelölje be a bevonási objektum.
> 
> 

### <a name="layouts"></a>Elrendezés
Alapértelmezés szerint Reach az értesítések és a lap megjelenítése fogja használni a beágyazott erőforrások a dll-fájl.

Azonban ha dönt, hogy saját erőforrásokat használnak, a márka, ezen összetevők megfelelően.

Ha szeretné felülbírálni az `EngagementReachHandler` a alosztályának kell tudniuk a bevonási a elrendezések használandó módszerek:

**Mintakód:**

    // In your subclass of EngagementReachHandler

    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetPollUri()
    {
       // return the path of your own xaml
    }

    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [!TIP]
> A `CreateNotification` metódus adhat vissza null értékű. Az értesítés nem jelennek meg, és a reach-kampány a rendszer eldobja.
> 
> 

Az elrendezés megvalósítási leegyszerűsítése is biztosítunk saját XAML-kódot, amely a kódot is szolgálhatnak. Az Engagement SDK archívumban találhatók (/ src/reach /).

> [!WARNING]
> Az általunk biztosított forrásai pontos ugyanazokat a portokat, amelyek jelenleg használják. Ezért ha azokat közvetlenül módosítani szeretné, ne feledje módosítani a névtér és a neve.
> 
> 

### <a name="notification-position"></a>Értesítési pozíciója
Alapértelmezés szerint bal oldalán található az alkalmazás alján egy alkalmazásbeli értesítés jelenik meg. Ez a viselkedés felülbírálható módosíthatja a `GetNotificationPosition` metódusában a `EngagementReachHandler` objektum.

    // In your subclass of EngagementReachHandler

    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

Jelenleg, választhat a `BOTTOM` (alapértelmezett) és `TOP` pozíciók.

### <a name="launch-message"></a>Indítsa el az üzenet
Amikor egy felhasználó egy Rendszerértesítő (egy bejelentési) kattint, az Engagement elindítja az alkalmazást, a leküldéses üzenetek a tartalom betöltése, és a megfelelő kampány lap megjelenítéséhez.

A késleltetés van a indítsa el az alkalmazás és a lap (attól függően, hogy a hálózat sebességétől) megjelenítési között.

Jelzi a felhasználóknak, hogy valami tölt, egy visual adatok, például egy folyamatjelző vagy egy folyamatjelző kell megadnia. Bevonási nem tudja kezelni, hogy saját magát, de itt néhány kezelők meg.

A visszahívás megvalósításához, a következőket kell tennie:

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

Állíthatja be a visszahívást a `Application_Launching` metódusában a `App.xaml.cs` fájlt, mielőtt lehetőleg a `EngagementReach.Instance.Init()` hívható meg.

> [!TIP]
> A felhasználói felület szálából minden kezelő hívja. Nincs a MessageBox vagy a felhasználói felület kapcsolatos valami használatakor foglalkoznia.
> 
> 

[alkalmazás-házirendek]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Adott alkalmazás esetében további követelmények]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

