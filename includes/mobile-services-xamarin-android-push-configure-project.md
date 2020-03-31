---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179625"
---
1. A Megoldás nézetben (vagy a Visual Studio **Megoldáskezelőjében** kattintson a jobb gombbal az **Összetevők** mappára, kattintson a **További összetevők beszerezése... parancsra,** keresse meg a **Google Cloud Messaging Ügyfél** összetevőt, és adja hozzá a projekthez.
2. Nyissa meg a ToDoActivity.cs projektfájlt, és adja hozzá a következő utasítást az osztályhoz:

    ```csharp
    using Gcm.Client;
    ```

3. A **ToDoActivity** osztályban adja hozzá a következő új kódot: 

    ```csharp
    // Create a new instance field for this activity.
    static ToDoActivity instance = new ToDoActivity();

    // Return the current activity instance.
    public static ToDoActivity CurrentActivity
    {
        get
        {
            return instance;
        }
    }
    // Return the Mobile Services client.
    public MobileServiceClient CurrentClient
    {
        get
        {
            return client;
        }
    }
    ```

    Ez lehetővé teszi a mobil ügyfélpéldány elérését a leküldéses kezelő szolgáltatásfolyamatból.
4. A **MobileServiceClient** létrehozása után adja hozzá a következő kódot az **OnCreate** metódushoz:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

A **ToDoActivity** most már készen áll a leküldéses értesítések hozzáadására.
