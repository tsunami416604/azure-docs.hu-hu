---
title: Ismerkedés az Azure Mobile Engagement Unity Android üzemelő példánnyal való használatával
description: Ismerje meg, hogyan használható az Azure Mobile Engagement az iOS-eszközökön üzembe helyezett Unity-alkalmazásokhoz kapcsolódó elemzések és leküldéses értesítések tekintetében.
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d5f0ef79-be00-4cec-97a5-a0b2fdaa380e
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: cd126fc656fc00667c4c09298409f920ab8160fc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-android-deployment"></a>Ismerkedés az Azure Mobile Engagement Unity Android üzemelő példánnyal való használatával
> [!IMPORTANT]
> Az Azure Mobile Engagement támogatása 2018. március 31-én megszűnik. Az oldal nem sokkal ezután törlésre kerül.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Ebben a témakörben elsajátíthatja, hogy miként használható az Azure Mobile Engagement az alkalmazáshasználat megértéséhez, valamint leküldéses értesítések Unity-alkalmazás szegmentált felhasználói számára való küldéséhez Android-eszközön való üzembe helyezés esetén.
Ennek az oktatóanyagnak a Unity klasszikus Roll a Ball (Guruló golyó) oktatóanyaga a kiindulási pontja. Végezze el [ennek az oktatóanyagnak](mobile-engagement-unity-roll-a-ball.md) a lépéseit, mielőtt az alábbi Mobile Engagement-integrációs oktatóanyaggal folytatná. 

Az oktatóanyaghoz az alábbiakra lesz szükség:

* [Unity Editor](http://unity3d.com/get-unity)
* [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
* Google Android SDK

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).
> 
> 

## <a id="setup-azme"></a>A Mobile Engagement beállítása az Android-alkalmazáshoz
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
1. Nyissa meg az **EngagementConfiguration** parancsfájlt az SDK mappából, és frissítse az **ANDROID\_CONNECTION\_STRING** elemet az Azure Portalról korábban beszerzett kapcsolati karakterlánccal.  
   
    ![][73]
2. Mentse a fájlt. 
3. Futtassa a következőt: **File -> Engagement -> Generate Android Manifest** (Fájl -> Engagement -> Android-jegyzék létrehozása). Ez a Mobile Engagement SDK által hozzáadott beépülő modul, amely automatikusan frissíti a projektbeállításokat, miután rákattint. 
   
    ![][74]

> [!IMPORTANT]
> Ne felejtse el ezt az **EngagementConfiguration** fájl minden egyes frissítése után elvégezni, különben a módosítások nem fognak megjelenni az alkalmazásban. 
> 
> 

### <a name="configure-the-app-for-basic-tracking"></a>Az alkalmazás konfigurálása alapszintű nyomkövetéshez
1. Nyissa meg szerkesztésre a lejátszóobjektumhoz csatolt **PlayerController** parancsfájlt. 
2. Adja hozzá a következő using utasítást:
   
        using Microsoft.Azure.Engagement.Unity;
3. Adja hozzá a következőt a `Start()` metódushoz:
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása
Ellenőrizze, hogy az Android SDK telepítve van-e a gépén, mielőtt a Unity-alkalmazást megpróbálná üzembe helyezni az eszközén. 

1. Csatlakoztasson a gépéhez egy Android-eszközt. 
2. Nyissa meg a **File -> Build Settings** (Fájl -> Létrehozási beállítások) menüpontot. 
   
    ![][40]
3. Válassza az **Android** lehetőséget, majd kattintson a **Switch Platform** (Platformváltás) gombra.
   
    ![][51]
   
    ![][52]
4. Kattintson a **Player settings** (Lejátszó beállításai) lehetőségre, és adjon meg egy érvényes értéket a Bundle Identifier (Csomagazonosító) számára. 
   
    ![][53]
5. Végül kattintson a **Build And Run** (Létrehozás és futtatás) gombra.
   
    ![][54]
6. Előfordulhat, hogy a rendszer arra kéri, hogy adja meg annak a mappának nevét, ahol az Android-csomag tárolva lesz. 
7. Ha a művelet sikeres, akkor a csomag üzembe lesz helyezve a csatlakoztatott eszközön, és a Unity-játék megjelenik a telefonon. 

## <a id="monitor"></a>Az alkalmazás csatlakoztatása a valós idejű megfigyeléshez
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Leküldéses értesítések és alkalmazáson belüli üzenetek engedélyezése
[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

### <a name="update-the-engagementconfiguration"></a>Az EngagementConfiguration frissítése
1. Nyissa meg az **EngagementConfiguration** parancsfájlt az SDK mappából, és frissítse az **ANDROID\_GOOGLE\_NUMBER** elemet a Google Cloud Developer portálról korábban beszerzett **Google projektszámmal**. Ez egy karakterláncérték, ezért tegye idézőjelek közé. 
   
    ![][75]
2. Mentse a fájlt. 
3. Futtassa a következőt: **File -> Engagement -> Generate Android Manifest** (Fájl -> Engagement -> Android-jegyzék létrehozása). Ez a Mobile Engagement SDK által hozzáadott beépülő modul, amely automatikusan frissíti a projektbeállításokat, miután rákattint. 
   
    ![][74]

### <a name="configure-the-app-to-receive-notifications"></a>Az alkalmazás konfigurálása értesítések fogadására
1. Nyissa meg szerkesztésre a lejátszóobjektumhoz csatolt **PlayerController** parancsfájlt. 
2. Adja hozzá a következőt a `Start()` metódushoz:
   
        EngagementReachAgent.Initialize();
3. Most, hogy frissítette az alkalmazást, helyezze üzembe és futtassa azt egy eszközön az alábbi utasítások szerint. 

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png
