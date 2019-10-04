---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179625"
---
1. A megoldás nézetben (vagy **Megoldáskezelőben** a Visual Studióban), kattintson a jobb gombbal a **összetevők** mappát, kattintson a **további összetevők beszerzése...** , keresse meg a **Google Cloud Messaging Client** összetevőt, és adja hozzá a projekthez.
2. Nyissa meg a ToDoActivity.cs projekt fájlt, és adja hozzá a következő using utasítást az osztályhoz:

    ```csharp
    using Gcm.Client;
    ```

3. Az a **ToDoActivity** osztályhoz, adja hozzá a következő új kódot: 

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

    Ez lehetővé teszi, hogy a folyamat leküldéses kezelő szolgáltatás a mobilügyfél-példány eléréséhez.
4. Adja hozzá a következő kódot a **OnCreate** metódust, miután a **MobileServiceClient** jön létre:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

A **ToDoActivity** van készítve az leküldéses értesítések hozzáadása.
