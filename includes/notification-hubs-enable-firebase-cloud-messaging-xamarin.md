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
ms.openlocfilehash: 4ceff7d59443fe78fb8cf7164e5f31cf1acc189a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420748"
---
1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.
2. A projekt létrehozása után válassza az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) lehetőséget. 

    ![Firebase hozzáadása Android-alkalmazáshoz](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Az a **Firebase hozzáadása Android-alkalmazáshoz** lapon, tegye a következőket: 
    1. A **Android-csomag neve**, adja meg a csomag nevét. Például: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Adja meg a csomag neve](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Válassza ki **alkalmazás regisztrálása**. 
4. Válassza ki **töltse le a google-services.json**, mentse a fájlt, a **alkalmazás** a projektet, és válassza ki a mappa **tovább**. 

    ![Google-services.json letöltése](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. Válassza ki **tovább** az oldalon. 
7. Válassza ki **kihagyhatja ezt a lépést** az oldalon. 

    ![Az utolsó lépés kihagyása](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. A Firebase konzolján kattintson a projektjéhez tartozó fogaskerékre. Ezután kattintson a **Project Settings** (Projektbeállítások) elemre.

    ![A projektbeállítások kiválasztása](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Ha még nem töltötte le a **google-services.json** fájl, megteheti ezt oldal stb. 
5. Váltson a **Cloud Messaging** a felső fülön. 
6. Másolja ki és mentse a **Legacy Server key** későbbi használatra. Ez az érték használatával az értesítési központ konfigurálása.
