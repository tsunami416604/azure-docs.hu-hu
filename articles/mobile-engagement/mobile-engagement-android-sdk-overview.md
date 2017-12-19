---
title: "Az Azure Mobile Engagement Android SDK-integráció"
description: "Ismerteti, hogyan integrálható az Azure Mobile Engagement SDK az Android-alkalmazások"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: 35935e911f1f17989beb71978396c6d1b7d601d6
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Az Azure Mobile Engagement Android SDK-integráció
> [!div class="op_single_selector"]
> * [Univerzális Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

Ez a dokumentum ismerteti az összes integrációs és konfigurációs beállítást érhető el az Azure Mobile Engagement Android SDK.

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Speciális funkciók
### <a name="reporting-features"></a>Jelentési szolgáltatások
Ezeket a szolgáltatásokat is hozzáadhat:

1. [Speciális jelentéskészítési beállítások](mobile-engagement-android-advanced-reporting.md)
2. [Hely jelentéskészítési lehetőségek](mobile-engagement-android-location-reporting.md)
3. [Speciális konfigurációs beállítások](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Értesítések:
[Az androidos Reach (értesítések) integrálása](mobile-engagement-android-integrate-engagement-reach.md)

1. A Google Cloud Messaging (GCM): [GCM integrálása Mobilmarketing](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM): [ADM integrálása Mobilmarketing](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Címke terv végrehajtására:
[A speciális a Mobile Engagement az Android-alkalmazás szerinti címkézését API használata](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Javítsa ki, amelyek ritkán fordulhat elő, amikor a program meghívja crash `EngagementAgentUtils.isInDedicatedEngagementProcess`, amely is használják a `EngagementApplication` osztály.

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Android-8 támogatás (az SDK korábbi verziói nem fog működni a Android 8).
* Nincs több függőség támogatási függvénytára.
* Távolítsa el `EngagementFragmentActivity` osztály.
* Miatt [háttér végrehajtási korlátozások](https://developer.android.com/preview/features/background.html) Android 8 háttér-naplók előfordulhat, hogy később, amíg a felhasználó kommunikál az eszköz, ez hatással lesz a leküldéses kampány **kézbesített** és **Rendszerértesítő jelenik meg** statisztika alatt késleltetett lesz, ha az eszköz alvó lett (az értesítési továbbra is megjelenik, fog gyűrű és mozog, valós idejű hibák nélkül).
* Miatt [háttér hely korlátok](https://developer.android.com/preview/features/background-location-limits.html), a valós idejű háttérben hely nem fog frissülni gyakran Android 8.

Az összes verzió, tekintse meg a [végezze el a kibocsátási megjegyzések](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Frissítési eljárások
Ha Ön már rendelkezik integrálva egy régebbi verzióját az SDK az alkalmazás, tekintse meg [frissítése eljárások](mobile-engagement-android-upgrade-procedure.md).

