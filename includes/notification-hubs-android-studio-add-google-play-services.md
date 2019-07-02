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
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509100"
---
1. Az Android Studióban válassza a **eszközök** a menüben, és válassza ki a **SDK Manager**. 
2. Válassza ki a használt Android SDK célverzióját a projektben. Válassza ki **csomag részleteinek**. 

    ![Android SDK Manager - verzió cél kiválasztása](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Válassza ki **Google API-k**, ha az még nem telepítette.

    ![Android SDK Manager – Google API-k kiválasztva](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Váltson a **SDK Tools** fülre. Ha még nem telepítette a Google Play-szolgáltatások, válassza ki a **Google Play-szolgáltatások** az alábbi képen látható módon. Válassza ki **alkalmaz** telepítéséhez. Jegyezze fel az SDK elérési útját, mert szükség lesz rá egy későbbi lépésben.

    ![Android SDK Manager – Google Play-szolgáltatások kiválasztva](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Ha megjelenik a **Módosítás megerősítése** párbeszédpanelen jelölje ki **OK**. Az összetevő-telepítő telepíti a kért összetevőket. Válassza ki **Befejezés** az összetevők telepítése után.
4. Válassza ki **OK** gombra kattintva zárja be a **beállításait az új projektek** párbeszédpanel bezárásához.  
5. Nyissa meg a build.gradle fájllal a a **alkalmazás** könyvtárat, majd adja hozzá a következő sort a `dependencies`. 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Válassza ki **szinkronizálás most** ikonra az eszköztárban.

    ![Szinkronizálás a gradle-lel](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Nyissa meg az AndroidManifest.xml fájlhoz, és adja a következő címkét a *alkalmazás* címke.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
