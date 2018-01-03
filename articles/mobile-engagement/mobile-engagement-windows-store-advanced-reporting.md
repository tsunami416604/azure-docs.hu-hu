---
title: "Jelentéskészítés a MobileApps Engagement speciális Windows Universal"
description: "Az Azure Mobile Engagement integrálása univerzális Windows-alkalmazások"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: feac309db1ffce0945012e293bfc1df417aed876
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Jelentéskészítés a a Windows univerzális alkalmazások Engagement SDK speciális
> [!div class="op_single_selector"]
> * [Univerzális Windows](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Ez a témakör ismerteti az univerzális Windows-alkalmazás további jelentéskészítési forgatókönyvekhez. Ilyen például a létrehozott alkalmazás alkalmazandó választható lehetőségek az [bevezetés](mobile-engagement-windows-store-dotnet-get-started.md) oktatóanyag.

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Az oktatóanyag elindítása előtt először végezze el a [bevezetés](mobile-engagement-windows-store-dotnet-get-started.md) oktatóanyag, amely szándékosan közvetlen és egyszerű. Ez az oktatóanyag ismerteti a további beállítások közül választhat.

## <a name="specifying-engagement-configuration-at-runtime"></a>Futásidőben engagement konfiguráció megadása
A bevonási konfigurációs központosított a `Resources\EngagementConfiguration.xml` fájlt a projekt, amely ahol lett megadva a a [bevezetés](mobile-engagement-windows-store-dotnet-get-started.md) témakör.

De azt is megadhatja, hogy futásidőben: hívása előtt az Engagement ügynök inicializálása a következő metódust:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Ajánlott módszer: túlterhelés a `Page` osztályok
A felhasználók, a munkamenetek, a tevékenységek, az összeomlásokat és a műszaki statisztika számítási Engagement által igényelt összes napló jelentéskészítési aktiválásához végezze el az összes a `Page` alosztályokat örökli a `EngagementPage` osztályok.

Itt látható egy példa egy lap az alkalmazás. Ezt megteheti az ugyanaz az alkalmazás összes lapja esetében.

### <a name="c-source-file"></a>C# forrásfájl
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
> Ha az oldala felülírja az `OnNavigatedTo` metódust, hívja meg a következőt: `base.OnNavigatedTo(e)`. Ellenkező esetben a tevékenység nem kell jelenteni (a `EngagementPage` hívások `StartActivity` belül a `OnNavigatedTo` metódus).
> 
> 

### <a name="xaml-file"></a>XAML-fájl
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

### <a name="override-the-default-behaviour"></a>Bírálja felül az alapértelmezett viselkedése
Alapértelmezés szerint az osztály nevét a lap a tevékenység nevét, nem extra akkor számít. Ha az osztály a "Lap" utótagot használ, az Engagement eltávolítja.

Felülbírálhatja az alapértelmezett viselkedést, a neve, vegye fel ezt a kódot:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

További információ a tevékenységi jelentést, adja hozzá a kódot:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Ezek a módszerek nevezzük belül a `OnNavigatedTo` metódus a lap.

### <a name="alternate-method-call-startactivity-manually"></a>Másodlagos módszer: hívja `StartActivity()` manuálisan
Ha nem, vagy nem szeretné, hogy túlterhelés a `Page` osztályok, ehelyett megkezdheti a tevékenységek meghívásával `EngagementAgent` módszerek közvetlenül.

Azt javasoljuk, hogy hívása `StartActivity` belül a `OnNavigatedTo` metódus a lap.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Gondoskodjon arról, hogy megfelelően a munkamenet befejezéséhez.
> 
> A Windows Universal SDK automatikusan meghívja a `EndActivity` módszer az alkalmazás bezárása után. Így **magas** hívására ajánlott a `StartActivity` módszer, amikor a felhasználó tevékenységét módosításához, és **soha** hívja a `EndActivity` metódus. Ez a módszer értesíti az Engagement-kiszolgálót, hogy az aktuális felhasználó elhagyta az alkalmazásról, így hatással van-e az összes alkalmazás naplóiban.
> 
> 

## <a name="advanced-reporting"></a>Speciális jelentéskészítés
Szükség esetén előfordulhat, hogy szeretne jelentést készíteni, az alkalmazás-specifikus eseményeket, hibákat és feladatok, ehhez használja a többi metódusok találhatók a `EngagementAgent` osztály. A bevonási API lehetővé teszi, hogy minden bevonási speciális képességek használatát.

További információkért lásd: [a speciális a Mobile Engagement az univerzális Windows-alkalmazás szerinti címkézését API használatával](mobile-engagement-windows-store-use-engagement-api.md).

