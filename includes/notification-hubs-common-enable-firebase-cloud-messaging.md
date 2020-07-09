---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 71e4b0b3b7bf84938123acf70ac18a93c3015a4d
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081437"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>Firebase-projekt létrehozása és a Firebase Cloud Messaging engedélyezése Android rendszerhez

1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, amely a **PushDemo** adja meg a **projekt nevét**.

    > [!NOTE]
    > A rendszer létrehoz egy egyedi nevet. Alapértelmezés szerint ez a megadott név kisbetűs változatát, valamint egy kötőjeltel elválasztott generált számot tartalmaz. Ezt megváltoztathatja, ha azt szeretné, hogy a rendszer továbbra is globálisan egyedi legyen.

1. A projekt létrehozása után válassza az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) lehetőséget.

    ![Firebase hozzáadása Android-alkalmazáshoz](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

1. A **Firebase hozzáadása az Android-alkalmazáshoz** oldalon hajtsa végre a következő lépéseket.
    1. Az **android-csomag neve**mezőben adja meg a csomag nevét. Példa: `com.<organization_identifier>.<package_name>`.

        ![Adja meg a csomag nevét](./media/notification-hubs-common-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    1. Válassza az **alkalmazás regisztrálása**lehetőséget.  
    1. Válassza **a letöltés google-services.jsa**következőn:. Ezután mentse a fájlt egy helyi mappába a későbbi használathoz, és válassza a **tovább**lehetőséget.  

        ![google-services.jsletöltése](./media/notification-hubs-common-enable-firebase-cloud-messaging/download-google-service-button.png)
    1. Válassza a **Tovább** lehetőséget.
    1. Válassza **a folytatás a konzolon** lehetőséget

        > [!NOTE]
        > Ha a **Folytatás a konzolon** gomb nincs engedélyezve, a *telepítés ellenőrzése* után válassza a **lépés kihagyása**lehetőséget.

1. A Firebase konzolján kattintson a projektjéhez tartozó fogaskerékre. Ezután kattintson a **Project Settings** (Projektbeállítások) elemre.

    ![A projektbeállítások kiválasztása](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > Ha nem töltötte le a **google-services.js** fájlt, letöltheti azt ezen az oldalon.

1. Váltson a felül található **Cloud Messaging (Felhőbeli üzenetkezelés** ) lapra. Másolja és mentse a **kiszolgálói kulcsot** későbbi használatra. Ezt az értéket használja az értesítési központ konfigurálásához.

    ![Kiszolgáló kulcsának másolása](./media/notification-hubs-common-enable-firebase-cloud-messaging/server-key.png)
