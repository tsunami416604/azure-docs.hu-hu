---
title: "Ismerkedés az Azure Mobile Engagement Unity iOS üzemelő példánnyal való használatával"
description: "Ismerje meg, hogyan használható az Azure Mobile Engagement az iOS-eszközökön üzembe helyezett Unity-alkalmazásokhoz kapcsolódó elemzések és leküldéses értesítések tekintetében."
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c8f50404771965ec636065346ac04e059d264c3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Ismerkedés az Azure Mobile Engagement Unity iOS üzemelő példánnyal való használatával
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Ebben a témakörben elsajátíthatja, hogy miként használható az Azure Mobile Engagement az alkalmazáshasználat megértéséhez, valamint leküldéses értesítések Unity-alkalmazás szegmentált felhasználói számára való küldéséhez iOS-eszközön való üzembe helyezés esetén.
Ennek az oktatóanyagnak a Unity klasszikus Roll a Ball (Guruló golyó) oktatóanyaga a kiindulási pontja. Végezze el [ennek az oktatóanyagnak](mobile-engagement-unity-roll-a-ball.md) a lépéseit, mielőtt az alábbi Mobile Engagement-integrációs oktatóanyaggal folytatná. 

Az oktatóanyaghoz az alábbiakra lesz szükség:

* [Unity Editor](http://unity3d.com/get-unity)
* [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
* XCode Editor

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).
> 
> 

## <a id="setup-azme"></a>A Mobile Engagement beállítása az iOS-alkalmazáshoz
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
### <a name="import-the-unity-package"></a>A Unity-csomag importálása
1. Töltse le a [Mobile Engagement Unity-csomagot](https://aka.ms/azmeunitysdk), és mentse a helyi gépére. 
2. Lépjen az **Assets -> Import Package -> Custom Package** (Eszközök -> Csomag importálása -> Egyéni csomag) lehetőségre, majd válassza ki az előző lépésben letöltött csomagot. 
   
    ![][70] 
3. Ügyeljen arra, hogy minden fájl ki legyen választva, és kattintson az **Import** (Importálás) gombra. 
   
    ![][71] 
4. Az importálás sikeres elvégzése után az importált SDK-fájlok megjelennek a projektben.  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>Az EngagementConfiguration frissítése
1. Nyissa meg az **EngagementConfiguration** parancsfájlt az SDK mappából, és frissítse az **IOS\_CONNECTION\_STRING** elemet az Azure Portalról korábban beszerzett kapcsolati karakterlánccal.  
   
    ![][73]
2. Mentse a fájlt. 

### <a name="configure-the-app-for-basic-tracking"></a>Az alkalmazás konfigurálása alapszintű nyomkövetéshez
1. Nyissa meg szerkesztésre a lejátszóobjektumhoz csatolt **PlayerController** parancsfájlt. 
2. Adja hozzá a következő using utasítást:
   
        using Microsoft.Azure.Engagement.Unity;
3. Adja hozzá a következőt a `Start()` metódushoz:
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása
1. Csatlakoztasson a gépéhez egy iOS-eszközt. 
2. Nyissa meg a **File -> Build Settings** (Fájl -> Létrehozási beállítások) menüpontot. 
   
    ![][40]
3. Válassza az **iOS** lehetőséget, majd kattintson a **Switch Platform** (Platformváltás) gombra.
   
    ![][41]
   
    ![][42]
4. Kattintson a **Player settings** (Lejátszó beállításai) lehetőségre, és adjon meg egy érvényes értéket a Bundle Identifier (Csomagazonosító) számára. 
   
    ![][53]
5. Végül kattintson a **Build And Run** (Létrehozás és futtatás) gombra.
   
    ![][54]
6. Előfordulhat, hogy a rendszer arra kéri, hogy adja meg annak a mappának nevét, ahol az iOS-csomag tárolva lesz. 
   
    ![][43]
7. Ha a művelet sikeres, akkor a projekt le lesz fordítva, és megnyitható az XCode-alkalmazással. 
8. Ellenőrizze, hogy a projektben a **Bundle identifier** (Csomagazonosító) helyes-e.  
   
    ![][75]
9. Most futtassa az alkalmazást XCode-ban. Ekkor a csomag üzembe lesz helyezve a csatlakoztatott eszközön, és a Unity-játék megjelenik a telefonon. 

## <a id="monitor"></a>Az alkalmazás csatlakoztatása a valós idejű megfigyeléshez
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Leküldéses értesítések és alkalmazáson belüli üzenetek engedélyezése
A Mobile Engagement lehetővé teszi a felhasználókkal folytatott interakciót és a felhasználók elérését a kampányok részeként megjelenő leküldéses értesítésekkel és alkalmazáson belüli üzenetekkel. Ez a modul REACH (Elérés) néven érhető el a Mobile Engagement portálon.
Az alkalmazásban nem kell további konfigurációt elvégezni az értesítések fogadásához, mivel az már be van erre állítva.

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
