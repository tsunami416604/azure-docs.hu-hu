---
title: "Speciális az Azure Mobile Engagement Android SDK jelentéskészítési beállítások"
description: "Ismerteti, hogyan lehet analytics rögzítheti az Azure Mobile Engagement Android SDK fejlett jelentéskészítési lehetőségek"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 2a1445afa2c2fca1a31ad9c012b9c8a917ebf65c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="advanced-reporting-with-engagement-on-android"></a>Speciális hibajelentési az Engagement Android rendszeren
> [!div class="op_single_selector"]
> * [Univerzális Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Ez a témakör az Android alkalmazás további jelentéskészítési forgatókönyvekhez. Ezek a beállítások a létrehozott alkalmazás alkalmazhat a [bevezetés](mobile-engagement-android-get-started.md) oktatóanyag.

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

Az oktatóanyag elvégzésének szándékosan közvetlen és egyszerű, de vannak speciális lehetősége választható.

## <a name="modifying-your-activity-classes"></a>Módosítja a `Activity` osztályok
Az a [bevezető oktatóanyag](mobile-engagement-android-get-started.md), hajtsa végre kellett az összes volt, hogy ellenőrizze a `*Activity` alosztályok öröklik a megfelelő `Engagement*Activity` osztályok. Például, ha az örökölt tevékenység kiterjesztett `ListActivity`, hogy könnyebben kiterjesztése `EngagementListActivity`.

> [!IMPORTANT]
> Használata esetén `EngagementListActivity` vagy `EngagementExpandableListActivity`, győződjön meg arról, hogy semmilyen hívásban való `requestWindowFeature(...);` hívása előtt történik `super.onCreate(...);`, különben a összeomlási történik.
> 
> 

Ezeket az osztályokat is megtalálhatja a `src` mappa, és a projektben másolja őket. Az osztályok szerepelnek a **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Másodlagos módszer: hívja `startActivity()` és `endActivity()` manuálisan
Ha nem, vagy nem szeretné, hogy túlterhelés a `Activity` osztályok, ehelyett elindításához és a tevékenységek befejezése után hívja a `EngagementAgent`tartozó módszerek közvetlenül.

> [!IMPORTANT]
> Az Android SDK soha nem hívja a `endActivity()` metódust, akkor is, ha az alkalmazás bezárása (Android, az alkalmazások soha nem Lezáródnak). Tehát, *magas* hívására ajánlott a `startActivity()` metódust a `onResume` a visszahívási *összes* a tevékenységeket, és a `endActivity()` metódus a a `onPause()` a visszahívási *összes* a tevékenységek. Ez az az egyetlen lehetőség, győződjön meg arról, hogy munkamenetek nem szivárognak. Ha a munkamenet kiszivárgott, az Engagement szolgáltatás soha nem bontja a kapcsolatot az Engagement háttérrendszeréhez (mivel a szolgáltatás továbbra is csatlakoztatva marad mindaddig, amíg függőben egy munkamenet).
> 
> 

Például:

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

Ez a példa hasonlít a `EngagementActivity` osztály és annak változataihoz, amelynek forráskódban szerepel a `src` mappát.

## <a name="using-applicationoncreate"></a>Application.onCreate() használatával
A kód helyez `Application.onCreate()` és más alkalmazásban visszahívások az alkalmazás folyamatok, többek között a bevonási szolgáltatás futtatásához. Lehet, hogy a nem kívánt hatásai, például a szükségtelen memória kiosztásokat és a bevonási folyamat, vagy ismétlődő szórási fogadók vagy szolgáltatások szálak.

Ha Ön felülbírálása `Application.onCreate()`, azt javasoljuk, hogy a következő kódrészletet elején a `Application.onCreate()` függvény:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Ugyanezt megteheti `Application.onTerminate()`, `Application.onLowMemory()`, és `Application.onConfigurationChanged(...)`.

Emellett kibővítheti `EngagementApplication` helyett kiterjesztése `Application`: a visszahívás `Application.onCreate()` a folyamat ellenőrzés és hívások `Application.onApplicationProcessCreate()` csak ha az aktuális folyamat nem azt az Engagement szolgáltatást tartalmazó, ugyanazok a szabályok vonatkoznak a más visszahívások.

## <a name="tags-in-the-androidmanifestxml-file"></a>Az AndroidManifest.xml fájlban címkék
A szolgáltatás címkén belül az AndroidManifest.xml fájlban a `android:label` attribútum lehetővé teszi, hogy a szolgáltatás neve a bevonási kiválaszthatja a telefont "Szolgáltatásokat futtató" képernyőjén a végfelhasználók számára megjelenik. Javasoljuk, hogy ha az attribútum értéke `"<Your application name>Service"` (például `"AcmeFunGameService"`).

Adja meg a `android:process` attribútum biztosítja, hogy a bevonási szolgáltatás folyamatban (Engagement egy folyamatban futó a az alkalmazás a fő/felhasználói felület szálán potenciálisan kevésbé rugalmas teszi).

## <a name="building-with-proguard"></a>A ProGuard felépítése
Ha a ProGuard alkalmazáscsomag, szeretne rögzíteni bizonyos osztályokat. A következő konfigurációs részlet használhatja:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }
