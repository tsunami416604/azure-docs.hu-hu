---
title: Windows Phone Silverlight SDK frissítési eljárások
description: Windows Phone Silverlight SDK frissítési eljárásokat és Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 87130026-9759-4659-9184-788a3627a165
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 489b005c37ddb842a2501e89c07fb34b091346e5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone Silverlight SDK frissítési eljárások
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Ha Ön már rendelkezik integrált egy régebbi verzióját az SDK az alkalmazásba, hogy az SDK-val történő frissítése során, vegye figyelembe a következő szempontokat.

Előfordulhat, hogy kell eljárnia számos eljárást, ha nem fogadta az SDK a különböző verzióiban. Például ha áttelepít 0.10.1 0.11.0 először hajtsa végre a "from a 0.10.1 0.9.0-s" eljárást kell majd a "from a 0.11.0 0.10.1" eljárást.

## <a name="from-200-to-330"></a>A 2.0.0 3.3.0 számára
### <a name="test-logs"></a>Vizsgálati naplók
Az SDK által előállított konzolnaplófájlokban mostantól lehet engedélyezve vagy letiltva/szűrve. Ez testreszabásához frissítse a tulajdonságát `EngagementAgent.Instance.TestLogEnabled` egy érhetők el az érték a `EngagementTestLogLevel` számbavételi, például:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

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

Ezt követően a Microsoft Azure Engagement új nuget-csomag telepítése a projekten. Megtalálhatja közvetlenül a [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Ez a művelet lecseréli a bevonási által használt összes erőforrások fájlokat, és az új Engagement DLL ad hozzá a projekt hivatkozásait.

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
4. Az egyéb erőforrások például Capptain képek vegye figyelembe, hogy azok is átnevezték használható az "Engagement".

### <a name="application-id--sdk-key"></a>Alkalmazásazonosító / SDK kulcs
Bevonási egy kapcsolati karakterláncot használ. Nincs a Mobile Engagement egy alkalmazás és az SDK kulcs adható meg, csak akkor adjon meg egy kapcsolati karakterláncot. Állíthat be azt a EngagementConfiguration fájlokat.

A bevonási konfigurációs állítható be a `Resources\EngagementConfiguration.xml` fájlt a projekt.

Adja meg, hogy a fájl szerkesztése:

* Az alkalmazás kapcsolati karakterlánc címkék között `<connectionString>` és `<\connectionString>`.

Ha azt szeretné, ehelyett meg futásidőben, hívása előtt az Engagement ügynök inicializálása a következő metódust:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

A kapcsolati karakterlánc az alkalmazás az Azure-portálon jelenik meg.

### <a name="items-name-change"></a>Elemek kiszolgálónév-változás
Minden elem nevű *capptain* megnevezett *engagement*. Ehhez hasonlóan az *Capptain* való *Engagement*.

Példák a gyakran használt Capptain elemeket:

* Most nevű EngagementConfiguration CapptainConfiguration
* Most nevű EngagementAgent CapptainAgent
* Most nevű EngagementReach CapptainReach
* Most nevű EngagementHttpConfig CapptainHttpConfig
* Most nevű GetEngagementPageName GetCapptainPageName

Vegye figyelembe, hogy az Átnevezés is hatással van a többszörösen definiált metódusok.

