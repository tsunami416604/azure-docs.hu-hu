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
ms.openlocfilehash: fef6122eceda213fb6353ada53033d0d1e27fd7e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509083"
---
1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.
2. A projekt létrehozása után válassza az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) lehetőséget. 

    ![Firebase hozzáadása Android-alkalmazáshoz](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Az a **Firebase hozzáadása Android-alkalmazáshoz** lapon, tegye a következőket: 
    1. A **Android-csomag neve**, másolja az értéket, a **applicationId** a az alkalmazás build.gradle fájllal. Ebben a példában van `com.fabrikam.fcmtutorial1app`. 

        ![Adja meg a csomag neve](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Válassza ki **alkalmazás regisztrálása**. 
4. Válassza ki **töltse le a google-services.json**, mentse a fájlt, a **alkalmazás** a projektet, és válassza ki a mappa **tovább**. 

    ![Google-services.json letöltése](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Győződjön meg arról, a következő **konfigurációs módosítások** az Android Studióban a projekthez. 
    1.  A projekt szintű build.gradle fájlban (&lt;projekt&gt;/build.gradle), adja hozzá a következő utasítást a **függőségek** szakaszban. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Az alkalmazásszintű build.gradle fájlban (&lt;projekt&gt;/&lt;app-module&gt;/build.gradle), adja hozzá a következő utasítást a **függőségek** szakaszban. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. A következő sort a függőségek szakasz után adja hozzá az alkalmazásszintű build.gradle fájl végéhez. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Válassza ki **szinkronizálás** az eszköztáron. 
 
        ![build.gradle konfigurációs módosítások](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Kattintson a **Tovább** gombra. 
7. Válassza ki **kihagyhatja ezt a lépést**. 

    ![Az utolsó lépés kihagyása](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. A Firebase konzolján kattintson a projektjéhez tartozó fogaskerékre. Ezután kattintson a **Project Settings** (Projektbeállítások) elemre.

    ![A projektbeállítások kiválasztása](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Ha még nem le a fájlt a google-services.json alkalmazásba a **alkalmazás** teheti az Android Studio projekt mappában, ez az oldal így tovább. 
5. Váltson a **Cloud Messaging** a felső fülön. 
6. Másolja ki és mentse a **kiszolgálókulcs** későbbi használatra. Ez az érték használatával a központ konfigurálása.
