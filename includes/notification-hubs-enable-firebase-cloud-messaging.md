---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70935099"
---
1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.
2. A projekt létrehozása után válassza az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) lehetőséget. 

    ![Firebase hozzáadása Android-alkalmazáshoz](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. A **Firebase hozzáadása az Android-alkalmazáshoz** lapon tegye a következő lépéseket: 
    1. Az **Android-csomag nevéhez**másolja az **alkalmazásazonosító** értékét az alkalmazás build.gradle fájljába. Ebben a példában `com.fabrikam.fcmtutorial1app`ez . 

        ![A csomag nevének megadása](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Válassza **az Alkalmazás regisztrálása lehetőséget.** 
4. Válassza **a Google-services.json letöltése**lehetőséget, mentse a fájlt a projekt **alkalmazásmappájába,** majd válassza a **Tovább**gombot. 

    ![Google-services.json letöltése](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Hajtsa végre a következő **konfigurációs módosításokat** a projekten az Android Studio-ban. 
    1.  A projektszintű build.gradle fájlban (&lt;project&gt;/build.gradle) adja hozzá a következő utasítást a **függőségek** szakaszhoz. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Az alkalmazásszintű build.gradle&lt;fájlban&gt;/&lt;(&gt;projekt alkalmazásmodul /build.gradle) adja hozzá a következő állításokat a **függőségek** szakaszhoz. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Adja hozzá a következő sort az alkalmazásszintű build.gradle fájl végéhez a függőségek szakasz után. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Az eszköztáron válassza a **Szinkronizálás most** lehetőséget. 
 
        ![build.gradle konfigurációmódosításai](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Válassza a **Tovább lehetőséget.** 
7. Válassza **a Lépés kihagyása lehetőséget.** 

    ![Az utolsó lépés kihagyása](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. A Firebase konzolján kattintson a projektjéhez tartozó fogaskerékre. Ezután kattintson a **Project Settings** (Projektbeállítások) elemre.

    ![A projektbeállítások kiválasztása](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Ha még nem töltötte le a google-services.json fájlt az Android Studio projekt **alkalmazásmappájába,** ezt ezen az oldalon teheti meg. 
5. Váltson a **felhőalapú üzenetek** lapra a tetején. 
6. Másolja és mentse a **Kiszolgálókulcsot** későbbi használatra. Ezzel az értékkel konfigurálhatja a hubot.
