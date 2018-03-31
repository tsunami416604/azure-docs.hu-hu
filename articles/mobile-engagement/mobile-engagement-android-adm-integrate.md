---
title: Az Azure Mobile Engagement Android SDK-integráció
description: Legújabb frissítések és az Azure Mobile Engagement Android SDK eljárásai
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4c3b8f60333e6096411aad8499bb4bfc36e53f3c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-adm-with-engagement"></a>ADM integrálása Engagement
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

> [!IMPORTANT]
> Meg kell követnie a integrációs ismertetett a hogyan integrálhatja Engagement Android dokumentumon Ez az útmutató követése előtt.
> 
> Ez a dokumentum akkor hasznos, csak akkor, ha már integrálva a Reach-modul és a terv Amazon eszközök leküldéses. Az alkalmazás Reach-kampányokat integrálását, olvassa el először Engagement elérésével integrálni az Android.
> 
> 

## <a name="introduction"></a>Bemutatás
ADM integrációja lehetővé teszi, hogy az alkalmazás leküldött a célcsoport-kezelési Amazon Android-eszközök számára.

ADM vonatkozó Payload van jelen az SDK leküldött mindig tartalmazza a `azme` adatobjektum kulcsban. Így ADM más célra, az alkalmazás használatakor, leküldéses értesítések kulcs alapján szűrheti.

> [!IMPORTANT]
> Csak Amazon Kindle futtató eszközökön Android 4.0.3 vagy újabb verziók támogatottak az Amazon Device Messaging; azonban ez a kód biztonságosan egyéb eszközein futó integrálhatja.
> 
> 

## <a name="sign-up-to-adm"></a>Regisztráljon az ADM
Ha nem tette meg, engedélyeznie kell ADM a Amazon-fiókjában.

Az eljárás részletes: [ <https://developer.amazon.com/sdk/adm/credentials.html> ].

Az eljárás befejezése után kap:

* OAuth-hitelesítőadatok (ügyfél-azonosító és a titkos Ügyfélkulcsot) a tenni az eszközök leküldéses bevonásra.
* API-kulcs, amely kell integrálni az alkalmazást.

## <a name="sdk-integration"></a>SDK-integráció
### <a name="managing-device-registrations"></a>Eszköz regisztrációk kezelése
Minden eszköz kell egy regisztrációs vonatkozó parancs küldése az ADM-kiszolgálókra, ellenkező esetben ezek nem érhető el.

Ha már használja a [ADM ügyféloldali kódtár], és már rendelkezik [ADM integrált] közvetlenül lépjen az android sdk-adm fogadására.

Ha Ön rendelkezik nem integrált ADM még, a bevonási rendelkezik leegyszerűsíti az alkalmazás az engedélyezéshez:

Szerkessze a `AndroidManifest.xml` fájlt:

* Adja hozzá a következő Amazon-névteret, a fájl kezdjen ehhez hasonló:
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* Belül a `<application/>` címkét, ez a szakasz hozzáadása:
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* Miután hozzáadta az amazon-címke, előfordulhat, hogy egy összeállítási hibát, ha a Project Build Target Android 2.1 alatt van. Szükség van egy **Android 2.1 +** build target (ne aggódjon, továbbra is rendelkezhet egy `minSdkVersion` értéke 4).
* Az ADM API-kulcsot integrálását az eszköz a következő [ezzel az eljárással].

Ezután kövesse a következő szakaszokból áll.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Regisztrációs azonosítót az Engagement leküldéses szolgáltatáshoz kommunikálnak, és értesítéseket kaphat
Ahhoz, hogy az eszköz az Engagement Push szolgáltatás regisztrációs azonosítót kommunikációhoz, és az értesítéseket, adja hozzá a következőt a `AndroidManifest.xml` fájlon belül a `<application/>` címke (Ha használja a ADM Engagement nélkül):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Ellenőrizze, hogy a következő engedélyeket a `AndroidManifest.xml` (előtt a `</application>` címke).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## <a name="grant-engagement-oauth-credentials"></a>Támogatás Engagement OAuth-hitelesítőadatok
Küldje el az OAuth-hitelesítőadatok (ügyfél-azonosító és a titkos ügyfélkódot) Engagement portálon.

[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[ADM ügyféloldali kódtár]:https://developer.amazon.com/sdk/adm/setup.html
[ADM integrált]:https://developer.amazon.com/sdk/adm/integrating-app.html
[ezzel az eljárással]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
