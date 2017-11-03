---
title: "Windows Phone Silverlight Engagement SDK-integráció"
description: "Windows Phone Silverlight-alkalmazásokhoz az Azure Mobile Engagement integrálása"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 447fea8d-f4e3-4ad4-8ec0-8e3cf1ad3ab0
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 29b18aecff783cebf617995e2a19f16f0b68b51b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-engagement-sdk-integration"></a>Windows Phone Silverlight Engagement SDK-integráció
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Ez az eljárás ismerteti az Azure Mobile Engagement elemzés és Monitorozási funkciók a Windows Phone Silverlight alkalmazás aktiválása legegyszerűbb módja.

Az alábbi lépéseket kell aktiválni a jelentés az összes statisztikai adatok felhasználók, a munkamenetek, a tevékenységek, az összeomlásokat és a Technicals kiszámításához szükséges naplók elegendő. A jelentés más hasonló eseményeket, hibákat és feladatok statisztika kiszámításához szükséges naplók az Engagement API segítségével manuálisan hajtható végre (lásd: [használata a speciális API szerinti címkézését a Windows Phone Silverlight-alkalmazást a Mobile Engagement](mobile-engagement-windows-phone-use-engagement-api.md) alatt) mivel ezek a statisztikákat a függő alkalmazást.

## <a name="supported-versions"></a>Támogatott verziók
A Mobile Engagement SDK a Windows Silverlight csak integrálhatók célcsoport-kezelési alkalmazások:

* Windows Phone 8.0
* Windows Phone 8.1 Silverlight

> [!NOTE]
> Ha a Windows Phone 8.1 (nem Silverlight) céloz meg, majd tekintse meg a [univerzális Windows-integrációs eljárás](mobile-engagement-windows-store-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-silverlight-sdk"></a>A Mobile Engagement Silverlight SDK telepítése
A Mobile Engagement SDK a Windows Silverlight nevű Nuget-csomagként érhető el *MicrosoftAzure.MobileEngagement*. Telepítheti azt a Visual Studio Nuget-Csomagkezelőjét. 

## <a name="add-the-capabilities"></a>A képességek hozzáadása
Az Engagement SDK-t kell bizonyos funkciók, a Windows Phone Silverlight SDK a megfelelő működéshez.

Nyissa meg a `WMAppManifest.xml` fájlt, és ne feledje, hogy az alábbi képességeket deklarálva van a `Capabilities` panel:

* `ID_CAP_NETWORKING`
* `ID_CAP_IDENTITY_DEVICE`

## <a name="initialize-the-engagement-sdk"></a>Az Engagement SDK inicializálása
### <a name="engagement-configuration"></a>Bevonási konfiguráció
A bevonási konfigurációs központosított a `Resources\EngagementConfiguration.xml` fájlt a projekt.

Adja meg, hogy a fájl szerkesztése:

* Az alkalmazás kapcsolati karakterlánc címkék között `<connectionString>` és `<\connectionString>`.

Ha azt szeretné, ehelyett meg futásidőben, hívása előtt az Engagement ügynök inicializálása a következő metódust:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

A kapcsolati karakterlánc az alkalmazás a klasszikus Azure-portálon jelenik meg.

### <a name="engagement-initialization"></a>Bevonási inicializálása
Amikor létrehoz egy új projektet egy `App.xaml.cs` fájl jön létre. Ez az osztály örökli `Application` és számos fontos metódust tartalmaz. Azt az Engagement SDK inicializálása is használható.

Módosítsa a `App.xaml.cs`:

* Adja hozzá a `using` utasításokat:
  
      using Microsoft.Azure.Engagement;
* Helyezze be `EngagementAgent.Instance.Init` a a `Application_Launching` módszert:
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
        EngagementAgent.Instance.Init();
      }
* Helyezze be `EngagementAgent.Instance.OnActivated` a a `Application_Activated` módszert:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
      }

> [!WARNING]
> Kifejezetten nem ajánlott, hogy az alkalmazás egy másik helyen adja hozzá az Engagement inicializálására. Azonban figyelembe, hogy a `EngagementAgent.Instance.Init` metódus egy dedikált szálon, nem pedig a felhasználói felület szálán futtatja.
> 
> 

## <a name="basic-reporting"></a>Alapvető jelentéskészítési
### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Ajánlott módszer: túlterhelés a `PhoneApplicationPage` osztályok
A jelentés minden, a felhasználók, a munkamenetek, a tevékenységek, az összeomlásokat és a műszaki statisztika számítási Engagement által igényelt naplók aktiválásához egyszerűen végezheti el az összes a `PhoneApplicationPage` alosztályokat örökli a `EngagementPage` osztályok.

Íme egy példa bemutatja, hogyan ehhez az alkalmazás egy lap. Ezt megteheti az ugyanaz az alkalmazás összes lapja esetében.

#### <a name="c-source-file"></a>C# forrásfájl
A lap módosítása `.xaml.cs` fájlt:

* Adja hozzá a `using` utasításokat:
  
      using Microsoft.Azure.Engagement;
* Cserélje le `PhoneApplicationPage` rendelkező `EngagementPage` :

**Nélkül Engagement:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Az Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!WARNING]
> Ha az oldala örökli a `OnNavigatedTo` módszer, ügyeljen arra, hogy lehetővé teszik a `base.OnNavigatedTo(e)` hívható meg. Ellenkező esetben a tevékenység nem fog szerepelni. Valójában a `EngagementPage` hívja a következő `StartActivity` belül a `OnNavigatedTo` metódust.
> 
> 

#### <a name="xaml-file"></a>XAML-fájl
A lap módosítása `.xaml` fájlt:

* A névtér-deklarációk hozzáadása:
  
      xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
* Cserélje le `phone:PhoneApplicationPage` rendelkező `engagement:EngagementPage` :

**Nélkül Engagement:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Az Engagement:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">

            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Az alapértelmezett viselkedés felülbírálásához
Alapértelmezés szerint az osztály nevét a lap a tevékenység nevét, nem extra akkor számít. Ha az osztály a "Lap" utótagot használ, Engagement is törlődik.

Ha azt szeretné, felülbírálhatja az alapértelmezett viselkedést, a neve, vegyük fel ezt a kódot:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Ha szeretne jelentést készíteni néhány további információt a tevékenységet, adhat hozzá ezt a kódot:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

Ezek a módszerek nevezzük belül a `OnNavigatedTo` metódus a lap.

### <a name="alternate-method-call-startactivity-manually"></a>Másodlagos módszer: hívja `StartActivity()` manuálisan
Ha nem, vagy nem szeretné, hogy túlterhelés a `PhoneApplicationPage` osztályok, ehelyett megkezdheti a tevékenységek meghívásával `EngagementAgent` módszerek közvetlenül.

Azt javasoljuk, hogy hívása `StartActivity` belül a `OnNavigatedTo` a PhoneApplicationPage metódusában.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [!IMPORTANT]
> Gondoskodjon arról, hogy megfelelően a munkamenet befejezéséhez.
> 
> Az SDK automatikusan meghívja a `EndActivity` módszer az alkalmazás bezárása után. Így **magas** hívására ajánlott a `StartActivity` módszer, amikor a felhasználó tevékenységét módosításához, és **soha** hívja a `EndActivity` metódus. Ez a módszer egy üzenetet küld a Engagement kiszolgálónak, hogy az aktuális felhasználó elhagyta az alkalmazást, és ez hatással van az összes alkalmazás naplóiban.
> 
> 

## <a name="advanced-reporting"></a>Speciális jelentéskészítés
A jelentés adott alkalmazásesemények, a hibák és a feladatok, érdemes lehet ehhez használhatja a többi módszerek található a `EngagementAgent` osztály. A bevonási API lehetővé teszi, hogy Engagement speciális funkciók használatára.

További információkért lásd: [használata a speciális API szerinti címkézését a Windows Phone Silverlight-alkalmazást a Mobile Engagement](mobile-engagement-windows-phone-use-engagement-api.md).

## <a name="advanced-configuration"></a>Speciális konfiguráció
### <a name="disable-automatic-crash-reporting"></a>Automatikus összeomlási jelentések letiltása
A jelentéskészítési funkció bekapcsolása automatikus összeomlási letilthatja. Ezután amikor nem kezelt kivétel lép fel, Engagement nem semmit.

> [!WARNING]
> Ha le szeretné letiltani ezt a funkciót, vegye figyelembe, hogy ha egy nem kezelt összeomlási történik, az alkalmazásban, Engagement nem küld a összeomlási **és** azt a munkamenetet, és a feladatok nem zárható be.
> 
> 

Automatikus összeomlási jelentések letiltásához testreszabása a attól függően, hogy a módját, deklarált, akkor a konfiguráció:

#### <a name="from-engagementconfigurationxml-file"></a>A `EngagementConfiguration.xml` fájl
Jelentés összeomlási beállítása `false` közötti `<reportCrash>` és `</reportCrash>` címkék.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>A `EngagementConfiguration` futási időben objektum
A jelentés összeomlási értéke HAMIS, a EngagementConfiguration objektum használatával.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Nagy sebességű átvitel
Alapértelmezés szerint a bevonási service-jelentéseken naplók valós időben. Ha az alkalmazás naplók nagyon gyakran, érdemes meghajtóin a naplókat, és jelentést őket egyszerre egy rendszeres időközönként alap (Ez a "kapacitásnövelés mód" nevezzük).

Ehhez a metódus meghívására:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Az argumentum értéke a **ezredmásodperc**. Bármikor Ha a valós idejű naplózási újraaktiválni kívánt, csak hívja a módszer minden paraméter nélkül, vagy a 0 érték.

A kapacitásnövelés módja kissé az eszközakkumulátor élettartamának növelhető, de hatással van a bevonási figyelő: minden munkamenetek és feladatok időtartama a rendszer kerekíti a kapacitásnövelés küszöbértéket (így munkamenetek és feladatok rövidebb, mint a kapacitásnövelés küszöbértéke nem lehet látható). Javasoljuk, hogy egy mint 30000 (30s) kapacitásnövelés küszöbértékkel. Akkor érdemes figyelembe vennie, amelyet a naplók 300 elemet korlátozódnak. Ha a küldő túl hosszú néhány naplók elveszhet.

> [!WARNING]
> A kapacitásnövelés küszöbértéket nem állítható be egy időszak kisebb mint egy második. Ha ehhez az SDK jelennek meg a hiba, és automatikusan visszaáll az alapértelmezett érték lesz a nyomkövetési Ez azt jelenti, hogy nulla másodperc. Ez akkor indul el, az SDK jelentheti a naplók valós időben.
> 
> 

