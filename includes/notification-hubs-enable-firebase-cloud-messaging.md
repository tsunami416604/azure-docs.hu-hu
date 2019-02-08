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
ms.openlocfilehash: e6b949824ec5da60c5e2485be830e61d156a11ff
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55830730"
---
1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.
2. A projekt létrehozása után válassza az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) lehetőséget. 

    ![Firebase hozzáadása Android-alkalmazáshoz](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Az a **Firebase hozzáadása Android-alkalmazáshoz** lapon, tegye a következőket: 
    1. A **Android-csomag neve**, másolja az értéket, a **applicationId** az alkalmazás **build.gradle** fájlt. Ebben a példában van `com.fabrikam.fcmtutorial1app`. 

        ![Adja meg a csomag neve](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Válassza ki **alkalmazás regisztrálása**. 
4. Válassza ki **töltse le a google-services.json**, mentse a fájlt, a **alkalmazás** a projektet, és válassza ki a mappa **tovább**. 

    ![Google-services.json letöltése](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Győződjön meg arról, a következő **konfigurációs módosítások** az Android Studióban a projekthez. 
    1.  Az a **projektszintű build.gradle** fájlt (&lt;projekt&gt;/build.gradle), adja hozzá a következő utasítást a **függőségek** szakaszban. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Az a **alkalmazásszintű build.gradle** fájlt (&lt;projekt&gt;/&lt;app-module&gt;/build.gradle), adja hozzá a következő utasítást a  **függőségek** szakaszban. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Adja hozzá a következő sor végére a **alkalmazásszintű build.gradle** fájlt a dependenices szakasz után. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
 
        ![build.gradle konfigurációs módosítások](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Válassza ki **tovább** az oldalon. 
7. Válassza ki **kihagyhatja ezt a lépést** az oldalon. 

    ![Az utolsó lépés kihagyása](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)1. 
8. A Firebase konzolján kattintson a projektjéhez tartozó fogaskerékre. Ezután kattintson a **Project Settings** (Projektbeállítások) elemre.

    ![A projektbeállítások kiválasztása](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Ha még nem töltötte le a **google-services.json** fájlt a **alkalmazás** az Android Studio-projektben való mappájában, megteheti ezt oldal stb. 
5. Váltson a **Cloud Messaging** a felső fülön. 
6. Másolja ki és mentse a **kiszolgálókulcs** későbbi használatra. Ez az érték használatával az értesítési központ konfigurálása.
