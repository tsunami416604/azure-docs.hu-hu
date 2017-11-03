---
title: "Az Azure Mobile Engagement Android SDK-integráció"
description: "Legújabb frissítések és az Azure Mobile Engagement Android SDK eljárásai"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: d72b5014-a22b-4a7f-a470-d2b8145b5b86
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/10/2016
ms.author: piyushjo
ms.openlocfilehash: 0282abbf44406cac89c13520bc2a4e375817ed1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-gcm-with-mobile-engagement"></a>GCM integrálása Mobilmarketing
> [!IMPORTANT]
> Meg kell követnie a integrációs ismertetett a hogyan integrálhatja Engagement Android dokumentumon Ez az útmutató követése előtt.
> 
> Ez a dokumentum akkor hasznos, csak akkor, ha már integrálva a Reach-modul és a Google Play-eszközök leküldéses terv. Az alkalmazás Reach-kampányokat integrálását, olvassa el először Engagement elérésével integrálni az Android.
> 
> 

## <a name="introduction"></a>Bevezetés
GCM integrálása lehetővé teszi, hogy az alkalmazás leküldött értesítést.

GCM vonatkozó Payload van jelen az SDK leküldött mindig tartalmazza a `azme` adatobjektum kulcsban. Így GCM más célra, az alkalmazás használatakor, leküldéses értesítések kulcs alapján szűrheti.

> [!IMPORTANT]
> Csak az Android 2.2-es futtató eszközökön vagy újabb, Google Play telepítve, és hogy Google rendelkező háttér-kapcsolat engedélyezett továbbíthatja a GCM; használatával azonban ez a kód biztonságosan eszközökön nem támogatott (csak a leképezések használ) integrálhatja.
> 
> 

## <a name="create-a-google-cloud-messaging-project-with-api-key"></a>Google Cloud Messaging-projekt létrehozása API-kulcs segítségével
[!INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## <a name="sdk-integration"></a>SDK-integráció
### <a name="managing-device-registrations"></a>Eszköz regisztrációk kezelése
Minden eszköz regisztrációs parancsot küld a Google kiszolgálóinak, ellenkező esetben ezek nem érhető el.

Egy eszköz is (az eszköz nem automatikusan regisztrált Ha az alkalmazás el lesz távolítva) GCM-értesítések is regisztrációját.

Ha nem adja meg [Google Play SDK] vagy nem már küldünk a regisztráció célt saját magának, az eszköz automatikus regisztrációját meg Engagement végezheti el.

Ennek engedélyezéséhez adja hozzá a következőt a `AndroidManifest.xml` fájlon belül a `<application/>` címke:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Regisztrációs azonosítót az Engagement leküldéses szolgáltatáshoz kommunikálnak, és értesítéseket kaphat
Ahhoz, hogy az eszköz az Engagement Push szolgáltatás regisztrációs azonosítót kommunikációhoz, és az értesítéseket, adja hozzá a következőt a `AndroidManifest.xml` fájlon belül a `<application/>` címke (még akkor is, ha a kezelt eszköz regisztrációját meg):

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Ellenőrizze, hogy a következő engedélyeket a `AndroidManifest.xml` (után a `</application>` címke).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

## <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>A GCM API-kulcshoz való hozzáférés biztosítása a Mobile Engagement számára
Hajtsa végre a [Ez az útmutató](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) a Mobile Engagement engedélyt a GCM API-kulcsot.

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
