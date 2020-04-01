---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68728815"
---
1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.
2. A projekt létrehozása után válassza az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) lehetőséget. 

    ![Firebase hozzáadása Android-alkalmazáshoz](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. A **Firebase hozzáadása az Android-alkalmazáshoz** lapon tegye a következő lépéseket: 
    1. Az **Android csomag nevéhez**adja meg a csomag nevét. Például: `tutorials.tutoria1.xamarinfcmapp`. 

        ![A csomag nevének megadása](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Válassza **az Alkalmazás regisztrálása lehetőséget.**  
    1. Válassza **a Google-services.json letöltése lehetőséget.** Ezután mentse a fájlt a projekt mappájába, és válassza a **Tovább**gombot. Ha még nem hozta létre a Visual Studio-projektet, ezt a lépést a projekt létrehozása után teheti meg. 

        ![Google-services.json letöltése](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Válassza a **Tovább lehetőséget.** 
    7. Válassza **a Lépés kihagyása lehetőséget.** 

        ![Az utolsó lépés kihagyása](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. A Firebase konzolján kattintson a projektjéhez tartozó fogaskerékre. Ezután kattintson a **Project Settings** (Projektbeállítások) elemre.

    ![A projektbeállítások kiválasztása](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Ha még nem töltötte le a **google-services.json** fájlt, akkor töltse le ezen az oldalon. 

    ![Töltse le a google-services.json-t az Általános lapról](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Váltson a **felhőalapú üzenetek** lapra a tetején. Másolja és mentse a **Kiszolgálókulcsot** későbbi használatra. Ezzel az értékkel konfigurálhatja az értesítési központot.

    ![Kiszolgálókulcs másolása](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
