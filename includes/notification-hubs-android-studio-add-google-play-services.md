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
ms.openlocfilehash: bc920493b32d500602a5b683c098d23aff855150
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823191"
---
1. A **Android Studio**válassza **eszközök** a menüben, és válassza a **SDK Manager**. 
2. Válassza ki a projekthez használt Android SDK célverzióját, és válassza ki **csomag részleteinek**. 

    ![Android SDK Manager - verzió cél kiválasztása](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Válassza ki **Google API-k**, ha nem telepítette.

    ![Android SDK Manager – Google API-k kiválasztva](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Váltson a **SDK Tools** fülre. Ha még nem telepítette a Google Play szolgáltatást, válassza ki a **Google Play-szolgáltatások** az alábbi képen látható módon. Ezután kattintson az **Apply** (Alkalmaz) gombra. Jegyezze fel az SDK elérési útját, mert szükség lesz rá egy későbbi lépésben.

    ![Android SDK Manager – Google Play-szolgáltatások kiválasztva](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Ha megjelenik a **Módosítás megerősítése** párbeszédpanelen jelölje ki **OK**. Az összetevő-telepítő telepíti a kért összetevőket. Válassza ki **Befejezés** az összetevők telepítése után.
4. Válassza ki **OK** gombra kattintva zárja be a **beállításait az új projektek** párbeszédpanel bezárásához.  
5. Nyissa meg a `build.gradle` fájlt a **alkalmazás** könyvtárat, és adja hozzá ezt a sort a `dependencies`. 

    ```text
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Válassza ki **szinkronizálás most** ikonra az eszköztárban.

    ![Szinkronizálás a gradle-lel](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Nyissa meg az **AndroidManifest.xml** fájlt, és adja ezt a címkét az *application* (alkalmazás) címkéhez.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
