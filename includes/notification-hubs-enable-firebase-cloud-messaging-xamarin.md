---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e047ba57a61d2f327544ec795f640f5066962f6
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509860"
---
1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.
2. A projekt létrehozása után válassza az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) lehetőséget. 

    ![Firebase hozzáadása Android-alkalmazáshoz](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Az a **Firebase hozzáadása Android-alkalmazáshoz** lapon, tegye a következőket: 
1. 
    1. Az a **Android-csomag neve**, adja meg a csomag nevét. Például: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Adja meg a csomag neve](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)

2. Válassza ki **alkalmazás regisztrálása**. 
1. 
1. Válassza ki **töltse le a google-services.json**. Mentse a fájlt, a **alkalmazás** a projektet, majd válassza ki a mappa **tovább**. 

    ![Google-services.json letöltése](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)

6. Kattintson a **Tovább** gombra. 
7. Válassza ki **kihagyhatja ezt a lépést**. 

    ![Az utolsó lépés kihagyása](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)

8. A Firebase konzolján kattintson a projektjéhez tartozó fogaskerékre. Ezután kattintson a **Project Settings** (Projektbeállítások) elemre.

    ![A projektbeállítások kiválasztása](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. Ha még nem töltötte le a **google-services.json** teheti a fájlt, ezen a lapon töltheti le. 

1. Váltson a **Cloud Messaging** a felső fülön. 

1. Másolja ki és mentse a **Legacy Server key** későbbi használatra. Ez az érték használatával az értesítési központ konfigurálása.
