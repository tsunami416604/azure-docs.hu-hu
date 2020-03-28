---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd202c98ed605209f5600965ecdb6c0b4c03c17e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67509100"
---
1. Az Android Studio alkalmazásban válassza a menü **Eszközök parancsát,** majd az **SDK-kezelő t.** 
2. Válassza ki a projektben használt Android SDK célverzióját. Ezután válassza **a Csomag részleteinek megjelenítése**lehetőséget. 

    ![Android SDK Manager - célverzió kiválasztása](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Válassza a **Google API-k**lehetőséget, ha még nincs telepítve.

    ![Android SDK Manager - Google API-k kiválasztva](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Váltson az **SDK-eszközök** lapra. Ha még nem telepítette a Google Play-szolgáltatásokat, válassza a **Google Play-szolgáltatásokat** az alábbi képen látható módon. Ezután válassza az **Alkalmaz** a telepítéshez lehetőséget. Jegyezze fel az SDK elérési útját, mert szükség lesz rá egy későbbi lépésben.

    ![Android SDK Manager - Google Play Szolgáltatások kiválasztva](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Ha megjelenik a **Módosítás megerősítése** párbeszédpanel, válassza az **OK gombot.** A Komponenstelepítő telepíti a kért összetevőket. Az összetevők telepítése után válassza a **Befejezés** lehetőséget.
4. Az **ÚJ projektek beállításai** párbeszédpanel bezárásához válassza az **OK** gombot.  
5. Nyissa meg a build.gradle fájlt az alkalmazáskönyvtárban, `dependencies`majd adja hozzá a következő sort a területen. **app** 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Az eszköztáron válassza a **Szinkronizálás most** ikont.

    ![Szinkronizálás a gradle-lel](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Nyissa meg az AndroidManifest.xml fájlt, és *application* adja hozzá a következő címkét az alkalmazáscímkéhez.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
