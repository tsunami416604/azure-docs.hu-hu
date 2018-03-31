---
title: Windows univerzális alkalmazások Engagement SDK-integráció
description: Az Azure Mobile Engagement integrálása univerzális Windows-alkalmazások
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 71236b68-5ebd-44aa-8c82-c7ca8098ea05
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: dc075ffe6565b4ef60e77f74ba4f065629c99952
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-engagement-sdk-integration"></a>Windows univerzális alkalmazások Engagement SDK-integráció
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

> [!div class="op_single_selector"]
> * [Univerzális Windows](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Ez az eljárás ismerteti a legegyszerűbben úgy Engagement elemzés és Monitorozási funkciók az univerzális Windows-alkalmazás aktiválása.

Az alábbi lépéseket kell aktiválni a jelentés az összes statisztikai adatok felhasználók, a munkamenetek, a tevékenységek, az összeomlásokat és a Technicals kiszámításához szükséges naplók elegendő. A jelentés más hasonló eseményeket, hibákat és feladatok statisztika kiszámításához szükséges naplók az Engagement API segítségével manuálisan hajtható végre (lásd: [a speciális a Mobile Engagement az univerzális Windows-alkalmazás szerinti címkézését API használatával](mobile-engagement-windows-store-use-engagement-api.md) óta ezek a statisztikákat a függő alkalmazást.

## <a name="supported-versions"></a>Támogatott verziók
A Mobile Engagement SDK Windows Universal alkalmazásokkal való csak integrálhatók a Windows-futtatókörnyezet és célzó univerzális Windows Platform-alkalmazások:

* Windows 8
* Windows 8.1
* Windows Phone 8.1
* Windows 10 (asztali és mobil családok)

> [!NOTE]
> Ha Windows Phone Silverlight céloz meg, akkor tekintse meg a [Windows Phone Silverlight-integráció eljárás](mobile-engagement-windows-phone-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>A Mobile Engagement univerzális alkalmazások SDK telepítése
### <a name="all-platforms"></a>Összes platform
A Mobile Engagement SDK az univerzális Windows-alkalmazás neve Nuget-csomagként érhető el *MicrosoftAzure.MobileEngagement*. Telepítheti azt a Visual Studio Nuget-Csomagkezelőjét.

### <a name="windows-8x-and-windows-phone-81"></a>A Windows 8.x és Windows Phone 8.1
NuGet automatikusan telepíti az SDK-erőforrások a `Resources` mappa a projekt gyökerében.

### <a name="windows-10-universal-windows-platform-applications"></a>Windows 10 univerzális Windows Platform-alkalmazások
NuGet nem automatikus központi telepítése még az UWP-alkalmazás az SDK-erőforrások. Meg kell azt manuálisan mindaddig, amíg az erőforrások telepítése a NuGet bevezetik:

1. Nyissa meg a Fájlkezelőben.
2. Nyissa meg azt a következő (**x.x.x** telepíti Engagement verziója): *% USERPROFILE %\\.nuget\packages\MicrosoftAzure.MobileEngagement\\  **x.x.x**\\content\win81*
3. Húzza a **erőforrások** a Fájlkezelőben a Visual Studio projekt gyökerében mappát.
4. A Visual Studióban válassza ki a projektet, és aktiválja a **megjelenítése minden fájl** a ikon a **Megoldáskezelőben**.
5. Egyes fájlok nem szerepelnek a projektet. Importálandó őket egyszerre kattintson a jobb gombbal a **erőforrások** mappa, **zárja ki a projekt** egy másikra a kattintson a jobb gombbal, majd a **erőforrások** mappa, **szerepeljenek projekt** újból felvenni az egész mappa. Az összes fájlt a **erőforrások** mappa mostantól beletartoznak a projekthez.

## <a name="add-the-capabilities"></a>A képességek hozzáadása
Az Engagement SDK-t kell bizonyos funkciók, a Windows SDK a megfelelő működéshez.

Nyissa meg a `Package.appxmanifest` fájlt, és ne feledje, hogy a következő lehetőségei vannak deklarálva:

* `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Az Engagement SDK inicializálása
### <a name="engagement-configuration"></a>Bevonási konfiguráció
A bevonási konfigurációs központosított a `Resources\EngagementConfiguration.xml` fájlt a projekt.

Adja meg, hogy a fájl szerkesztése:

* Az alkalmazás kapcsolati karakterlánc címkék között `<connectionString>` és `<\connectionString>`.

Ha azt szeretné, ehelyett meg futásidőben, hívása előtt az Engagement ügynök inicializálása a következő metódust:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

A kapcsolati karakterlánc az alkalmazás az Azure-portálon jelenik meg.

### <a name="engagement-initialization"></a>Bevonási inicializálása
Amikor létrehoz egy új projektet egy `App.xaml.cs` fájl jön létre. Ez az osztály örökli `Application` és számos fontos metódust tartalmaz. Azt az Engagement SDK inicializálása is használható.

Módosítsa a `App.xaml.cs`:

* Adja hozzá a `using` utasításokat:
  
      using Microsoft.Azure.Engagement;
* Adja meg az Engagement inicializálására egyszer minden hívásoknál megosztásához metódus:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
  
        // or
  
        EngagementAgent.Instance.Init(e, engagementConfiguration);
      }
* Hívás `InitEngagement` a a `OnLaunched` módszert:
  
      protected override void OnLaunched(LaunchActivatedEventArgs e)
      {
        InitEngagement(e);
      }
* Az alkalmazás indításakor egy egyéni séma, egy másik alkalmazás vagy a parancssor használatával, majd a `OnActivated` metódust. Is kell kezdeményezni az Engagement SDK-t, az alkalmazás aktiválásakor. Ehhez az szükséges, bírálja felül `OnActivated` módszert:
  
      protected override void OnActivated(IActivatedEventArgs args)
      {
        InitEngagement(args);
      }

> [!IMPORTANT]
> Kifejezetten nem ajánlott, hogy az alkalmazás egy másik helyen adja hozzá az Engagement inicializálására.
> 
> 

## <a name="basic-reporting"></a>Alapvető jelentéskészítési
### <a name="recommended-method-overload-your-page-classes"></a>Ajánlott módszer: túlterhelés a `Page` osztályok
A jelentés minden, a felhasználók, a munkamenetek, a tevékenységek, az összeomlásokat és a műszaki statisztika számítási Engagement által igényelt naplók aktiválásához egyszerűen végezheti el az összes a `Page` alosztályokat örökli a `EngagementPage` osztályok.

Íme egy példa bemutatja, hogyan ehhez az alkalmazás egy lap. Ezt megteheti az ugyanaz az alkalmazás összes lapja esetében.

#### <a name="c-source-file"></a>C# forrásfájl
A lap módosítása `.xaml.cs` fájlt:

* Adja hozzá a `using` utasításokat:
  
      using Microsoft.Azure.Engagement;
* Cserélje le `Page` rendelkező `EngagementPage`:

**Nélkül Engagement:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Az Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!IMPORTANT]
> Ha az oldala felülírja az `OnNavigatedTo` metódust, hívja meg a következőt: `base.OnNavigatedTo(e)`. Ellenkező esetben a tevékenységről nem készül jelentés (az `EngagementPage` meghívja a `StartActivity` tevékenységet az `OnNavigatedTo` metódusán belül).
> 
> 

#### <a name="xaml-file"></a>XAML-fájl
A lap módosítása `.xaml` fájlt:

* Adja hozzá a következőt a névtér-deklarációkhoz:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Cserélje le `Page` rendelkező `engagement:EngagementPage`:

**Nélkül Engagement:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Az Engagement:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>Bírálja felül az alapértelmezett viselkedése
Alapértelmezés szerint az osztály nevét a lap a tevékenység nevét, nem extra akkor számít. Ha az osztály a "Lap" utótagot használ, Engagement is törlődik.

Ha azt szeretné, felülbírálhatja az alapértelmezett viselkedést, a neve, vegyük fel ezt a kódot:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Ha szeretne jelentést készíteni a tevékenységi néhány további informations, adhat hozzá ezt a kódot:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Ezek a módszerek nevezzük belül a `OnNavigatedTo` metódus a lap.

### <a name="alternate-method-call-startactivity-manually"></a>Másodlagos módszer: hívja `StartActivity()` manuálisan
Ha nem, vagy nem szeretné, hogy túlterhelés a `Page` osztályok, ehelyett megkezdheti a tevékenységek meghívásával `EngagementAgent` módszerek közvetlenül.

Ajánlott hívására `StartActivity` belül a `OnNavigatedTo` metódus a lap.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Gondoskodjon arról, hogy megfelelően a munkamenet befejezéséhez.
> 
> A Windows Universal SDK automatikusan meghívja a `EndActivity` módszer az alkalmazás bezárása után. Így **magas** hívására ajánlott a `StartActivity` módszer, amikor a felhasználó tevékenységét módosításához, és **soha** hívja a `EndActivity` metódus, ez a módszer kiszolgálónak küldi az Engagement, amely aktuális felhasználó rendelkezik kilép az alkalmazásból, a rendszer minden alkalmazásnaplók hatással van.
> 
> 

## <a name="advanced-reporting"></a>Speciális jelentéskészítés
A jelentés adott alkalmazásesemények, a hibák és a feladatok, érdemes lehet ehhez használhatja a többi módszerek található a `EngagementAgent` osztály. A bevonási API lehetővé teszi, hogy Engagement speciális funkciók használatára.

További információkért lásd: [a speciális a Mobile Engagement az univerzális Windows-alkalmazás szerinti címkézését API használatával](mobile-engagement-windows-store-use-engagement-api.md).

## <a name="advanced-configuration"></a>Speciális konfiguráció
### <a name="disable-automatic-crash-reporting"></a>Automatikus összeomlási jelentések letiltása
A jelentéskészítési funkció bekapcsolása automatikus összeomlási letilthatja. Ezután amikor nem kezelt kivétel lép fel, Engagement nem semmit.

> [!WARNING]
> Ha le szeretné letiltani ezt a funkciót, vegye figyelembe, hogy ha egy nem kezelt összeomlási történik, az alkalmazásban, Engagement nem küld a összeomlási **és** nem zárja be a munkamenet és feladatok.
> 
> 

Automatikus összeomlási jelentések letiltásához testreszabása a attól függően, hogy a módját, deklarált, akkor a konfiguráció:

#### <a name="from-engagementconfigurationxml-file"></a>A `EngagementConfiguration.xml` fájl
Jelentés összeomlási beállítása `false` közötti `<reportCrash>` és `</reportCrash>` címkék.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>A `EngagementConfiguration` futási időben objektum
A jelentés összeomlási értéke HAMIS, a EngagementConfiguration objektum használatával.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Nagy sebességű átvitel
Alapértelmezés szerint a bevonási service-jelentéseken naplók valós időben. Ha az alkalmazás naplók nagyon gyakran, érdemes meghajtóin a naplókat, és jelentést őket egyszerre egy rendszeres időközönként alap (Ez a "kapacitásnövelés mód" nevezzük).

Ehhez a metódus meghívására:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Az argumentum értéke a **ezredmásodperc**. Bármikor Ha a valós idejű naplózási újraaktiválni kívánt, csak hívja a módszer minden paraméter nélkül, vagy a 0 érték.

A kapacitásnövelés módja kissé az eszközakkumulátor élettartamának növelhető, de hatással van a bevonási figyelő: minden munkamenetek és feladatok időtartama a rendszer kerekíti a kapacitásnövelés küszöbértéket (így munkamenetek és feladatok rövidebb, mint a kapacitásnövelés küszöbértéke nem lehet látható). Javasoljuk, hogy egy mint 30000 (30s) kapacitásnövelés küszöbértékkel. Akkor érdemes figyelembe vennie, amelyet a naplók 300 elemet korlátozódnak. Ha a küldő túl hosszú néhány naplók elveszhet.

> [!WARNING]
> A kapacitásnövelés küszöbértéke kisebb, mint 1s időszakra nem konfigurálható. Ha erre, az SDK jelennek meg a hiba: a nyomkövetés, és automatikusan visszaállítja az alapértelmezett érték, azaz 0s. Ez akkor indul el, az SDK jelentheti a naplók valós időben.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview

