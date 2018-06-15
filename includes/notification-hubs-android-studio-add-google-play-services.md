---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 540c9775ae56798ce2d2d87fed4924244c31412f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33835765"
---
1. Nyissa meg az Android SDK Manager alkalmazást az Android Studióban található ikonnal, vagy a **Tools** (Eszközök) > **Android** > **SDK Manager** lehetőségre kattintva a menüben. Keresse meg az alkalmazásában használt Android SDK célverzióját, nyissa meg a **Csomag részleteinek megtekintése** elemre kattintva, és válassza a **Google API-k** elemet, ha még nincs telepítve.
2. Kattintson az **SDK Tools** (SDK-eszközök) fülre. Ha még nem telepítette a Google Play szolgáltatást, kattintson a **Google Play Services** (Google Play szolgáltatások) elemre, ahogy az ábra mutatja. Ezután kattintson az **Apply** (Alkalmaz) gombra. 
   
    Jegyezze fel az SDK elérési útját, mert szükség lesz rá egy későbbi lépésben. 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Nyissa meg a **build.gradle** fájlt az alkalmazáskönyvtárban.
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Adja hozzá ezt a sort a *dependencies* (függőségek) sor alatt: 
    
    ```java
        compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. Kattintson a **Sync Project with Gradle Files** (Projekt szinkronizálása a Gradle-fájlokkal) ikonra az eszköztárban.
6. Nyissa meg az **AndroidManifest.xml** fájlt, és adja ezt a címkét az *application* (alkalmazás) címkéhez.
   
    ```java
    <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
    ```

