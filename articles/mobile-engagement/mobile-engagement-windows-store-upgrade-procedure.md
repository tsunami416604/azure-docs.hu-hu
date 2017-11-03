---
title: "Univerzális Windows-alkalmazások SDK eljárások frissítése"
description: "Univerzális Windows-alkalmazások SDK és Azure Mobile Engagement frissítése a eljárások"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 4c898175-2cd6-43db-b350-bb408332f24d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fe85a99a92fb39082cafe7422b356de1f20f14bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-sdk-upgrade-procedures"></a>Univerzális Windows-alkalmazások SDK eljárások frissítése
Ha Ön már rendelkezik integrált Engagement régebbi verzióját az alkalmazásba, hogy az SDK-val történő frissítése során, vegye figyelembe a következő szempontokat.

Előfordulhat, hogy kell eljárnia számos eljárást, ha nem fogadta az SDK a különböző verzióiban. Például ha áttelepít 0.10.1 0.11.0 először hajtsa végre a "from a 0.10.1 0.9.0-s" eljárást kell majd a "from a 0.11.0 0.10.1" eljárást.

## <a name="from-330-to-340"></a>A 3.3.0 3.4.0 számára
### <a name="test-logs"></a>Vizsgálati naplók
Az SDK által előállított konzolnaplófájlokban mostantól lehet engedélyezve vagy letiltva/szűrve. Ez testreszabásához frissítse a tulajdonságát `EngagementAgent.Instance.TestLogEnabled` egy érhetők el az érték a `EngagementTestLogLevel` számbavételi, például:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Erőforrások
A Reach átfedő javult. Az SDK NuGet-csomag erőforrások része.

Amíg az SDK újabb verziójára frissít, hogy szeretné-e az átmeneti területre mappából az erőforrások mindig a meglévő fájlokat, vagy nem választhat:

* Ha az előző átfedés működik-e meg, vagy az integrációhoz a `WebView` manuálisan majd beállíthatja úgy, hogy a meglévő fájlok elemeket, hogy továbbra is működni fognak. 
* Ha szeretné frissíteni az új átmeneti területre, helyettesítse a teljes `overlay` mappa az erőforrások közül az újjal az SDK-csomagból (UWP-alkalmazások: a frissítés után az % USERPROFILE % kérheti le az új átfedő mappa\\.nuget\packages\ MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Az új átfedés használatával felülírja az előző verzió végzett testreszabások.
> 
> 

## <a name="from-320-to-330"></a>A 3.2.0 3.3.0 számára
### <a name="resources"></a>Erőforrások
Ez a lépés csak egyéni erőforrások vonatkozik. Ha az SDK-val (html, képek, átfedő) által biztosított erőforrások testreszabott akkor rendelkezik biztonsági mentése azokat a frissítés előtt, és alkalmazza újra a testreszabás, a frissített erőforrásokat.

## <a name="from-310-to-320"></a>A 3.1.0 3.2.0 számára
### <a name="resources"></a>Erőforrások
Ez a lépés csak egyéni erőforrások vonatkozik. Ha az SDK-val (html, képek, átfedő) által biztosított erőforrások testreszabott akkor rendelkezik biztonsági mentése azokat a frissítés előtt, és alkalmazza újra a testreszabás, a frissített erőforrásokat.

### <a name="webview-integration"></a>Webes nézet integráció
Néhány fejlesztései felel meg a különböző helyigényekhez ebben a verzióban lett bevezetve. Győződjön meg arról, hogy a webes nézet az integráció felel meg a következő:

Az az XAML lap ():

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

És a kapcsolódó .cs fájlban:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }

              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif

              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }

              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

## <a name="from-200-to-300"></a>A 2.0.0 3.0.0 számára
### <a name="resources"></a>Erőforrások
Ez a lépés csak egyéni erőforrások vonatkozik. Ha az SDK-val (html, képek, átfedő) által biztosított erőforrások testreszabott akkor rendelkezik biztonsági mentése azokat a frissítés előtt, és alkalmazza újra a testreszabás, a frissített erőforrásokat.

## <a name="from-111-to-200"></a>A 1.1.1 2.0.0 számára
A következő ismerteti, hogyan telepíthetők át az SDK-integráció az alkalmazásba az Azure Mobile Engagement technológiával Capptain SAS által kínált Capptain szolgáltatás. 

> [!IMPORTANT]
> Capptain és a Mobile Engagement nem ugyanazok a szolgáltatások és az alábbi eljárás csak emel ki, hogyan telepítheti át az ügyfélalkalmazás. Az SDK-t az alkalmazás áttelepítése rendszer nem telepíti át az adatok a Capptain kiszolgálókról a Mobile Engagement-kiszolgálókon
> 
> 

Ha egy korábbi verziójáról telepít, tekintse meg a Capptain webhely 1.1.1 először át, akkor alkalmazza az alábbi eljárás

### <a name="nuget-package"></a>Nuget-csomag
Cserélje le **Capptain.WindowsPhone** által **MicrosoftAzure.MobileEngagement** Nuget-csomagot.

### <a name="applying-mobile-engagement"></a>Mobilmarketing alkalmazása
Az SDK-t használ a kifejezés `Engagement`. Frissítenie kell a módosítás megfelelő a projekthez.

El kell távolítania a jelenlegi Capptain nuget-csomagot. Vegye figyelembe, hogy eltávolítja-e Capptain erőforrások mappában összes módosítást. Ha meg szeretné tartani ezeket a fájlokat készítsen egy másolatot kapnak.

Ezt követően a Microsoft Azure Engagement új nuget-csomag telepítése a projekten. Közvetlenül a [nuget webhelyén] találja. vagy az ide-index. Ez a művelet lecseréli a bevonási által használt összes erőforrások fájlokat, és az új Engagement DLL ad hozzá a projekt hivatkozásait.

A projekt hivatkozásait tisztítása Capptain DLL hivatkozások törlésével rendelkezik. Ha ez nem teszi meg, Capptain verziója ütközésbe fognak kerülni, és hiba történik.

Ha testre szabott Capptain erőforrásokat, a régi fájlok tartalmát, és illessze be őket az új Engagement fájlok. Vegye figyelembe, hogy az xaml-és cs is frissítenie kell őket.

Ha ezek lépéseket csak akkor kell cserélje le az új Engagement hivatkozást Capptain hivatkozásai.

1. Összes Capptain névteret kell frissíteni.
   
    Mielőtt áttelepítése:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    : Az áttelepítés után
   
        using Microsoft.Azure.Engagement;
2. Minden Capptain osztály, amely tartalmazza a "Capptain" tartalmaznia kell "Engagement".
   
    Mielőtt áttelepítése:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    : Az áttelepítés után
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Az xaml-fájlokkal Capptain névtér és attribútumok is módosíthatja.
   
    Mielőtt áttelepítése:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    : Az áttelepítés után
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Átfedő módosításai
   
   > [!IMPORTANT]
   > Átfedő is megváltozik. Az új névtér `Microsoft.Azure.Engagement.Overlay`. Xaml-és cs is használatban van. Továbbá `CapptainGrid` annak neve lehet `EngagementGrid`, `capptain_notification_content` és `capptain_announcement_content` megnevezett `engagement_notification_content` és `engagement_announcement_content`.
   > 
   > 
   
    Az átmeneti területre:
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    Válik:
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. Az egyéb erőforrások például Capptain képek és HTML-fájlok vegye figyelembe, hogy azok is átnevezték használható az "Engagement".

### <a name="project-declaration"></a>Projekt nyilatkozat
A Package.appxmanifest `File Type Associations` a frissült:

* capptain\_elérni\_tartalom engagement\_elérni\_tartalom
* capptain\_napló\_engagement fájl\_napló\_fájl

### <a name="application-id--sdk-key"></a>Alkalmazásazonosító / SDK kulcs
Bevonási egy kapcsolati karakterláncot használ. Nincs a Mobile Engagement egy alkalmazás és az SDK kulcs adható meg, csak akkor adjon meg egy kapcsolati karakterláncot. Állíthat be azt a EngagementConfiguration fájlokat.

A bevonási konfigurációs állítható be a `Resources\EngagementConfiguration.xml` fájlt a projekt.

Adja meg, hogy a fájl szerkesztése:

* Az alkalmazás kapcsolati karakterlánc címkék között `<connectionString>` és `<\connectionString>`.

Ha azt szeretné, ehelyett meg futásidőben, hívása előtt az Engagement ügynök inicializálása a következő metódust:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

A kapcsolati karakterlánc az alkalmazás a klasszikus Azure-portálon jelenik meg.

### <a name="items-name-change"></a>Elemek kiszolgálónév-változás
Minden elem nevű *capptain* megnevezett *engagement*. Ehhez hasonlóan az *Capptain* való *Engagement*.

Példák a gyakran használt Capptain elemeket:

* Most nevű EngagementConfiguration CapptainConfiguration
* Most nevű EngagementAgent CapptainAgent
* Most nevű EngagementReach CapptainReach
* Most nevű EngagementHttpConfig CapptainHttpConfig
* Most nevű GetEngagementPageName GetCapptainPageName

Vegye figyelembe, hogy az Átnevezés is hatással van a többszörösen definiált metódusok.

