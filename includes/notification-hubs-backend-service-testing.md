---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5e75c5d5510f596eb7911cae0310e60b6bef67bf
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146468"
---
### <a name="send-a-test-notification"></a>Tesztértesítés küldése

1. Nyisson meg egy új lapot a [Poster](https://www.postman.com/downloads/)-ben.

1. Állítsa be a **post**értékre a kérést, és adja meg a következő címeket:

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. Ha úgy dönt, hogy a [hitelesítő ügyfeleket egy API-kulcs szakasz használatával](#authenticate-clients-using-an-api-key-optional) tölti be, ügyeljen arra, hogy a kérések fejléceit adja meg a **apikey** értékének.

   | Kulcs                            | Érték                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Válassza ki a **törzs** **nyers** beállítását, majd a formázási beállítások listájában válassza a **JSON** lehetőséget, majd adjon meg néhány helyőrző **JSON** -tartalmat:

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. Kattintson a **kód** gombra, amely az ablak jobb felső sarkában található **Mentés** gombra kattint. A kérésnek az alábbi példához hasonlóan kell kinéznie a **HTML** -ben (attól függően, hogy **apikey** -fejlécet adott-e meg):

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. Futtassa a **PushDemo** alkalmazást az egyik vagy mindkét megcélzott platformon (**Android** és **iOS**).

    > [!NOTE]
    > Ha **Androidon** végzi a tesztelést, győződjön meg arról, hogy nem fut a **hibakeresésben**, vagy ha az alkalmazást az alkalmazás futtatásával telepítette, akkor kényszerítse az alkalmazás bezárását, és indítsa el újra az indítóból.

1. A **PushDemo** alkalmazásban koppintson a **regisztrálás** gombra.

1. Vissza a **[Poster](https://www.postman.com/downloads)**( **kódrészletek létrehozása** ) ablak bezárásához (ha még nem tette meg), kattintson a **Küldés** gombra.

1. Győződjön meg arról, hogy a **[Poster](https://www.postman.com/downloads)** -ben egy **200 OK** választ kap, és a riasztás megjelenik az alkalmazásban, amely a **actiona műveletet fogadta**.  

1. Zárjuk be a **PushDemo** alkalmazást, majd kattintson ismét a **Send (Küldés** ) gombra a **[Poster](https://www.postman.com/downloads)**-ben.

1. Győződjön meg arról, hogy egy **200 OK** választ kap a **[Poster](https://www.postman.com/downloads)** -ben. Ellenőrizze, hogy megjelenik-e egy értesítés a **PushDemo** alkalmazás értesítési területén a megfelelő üzenettel.

1. Az értesítésre koppintva ellenőrizze, hogy megnyílik-e az alkalmazás, és megjelenik-e a **műveletre vonatkozó művelet** riasztás.

1. A **[Poster](https://www.postman.com/downloads)**-ban módosítsa az előző kérelem törzsét, hogy a **művelet** értékének *action_a* helyett csendes értesítést küldjön a *action_b* megadásához.

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. Ha az alkalmazás továbbra is meg van nyitva, kattintson a **[Poster](https://www.postman.com/downloads)** **Send (Küldés** ) gombjára.

1. Győződjön meg arról, hogy egy **200 OK** választ kap a **[Poster](https://www.postman.com/downloads)** szolgáltatásban, és hogy a riasztás megjelenik az alkalmazásban, amely a **művelet végrehajtása művelet**helyett a **ActionB műveletet** mutatja.

1. Zárjuk be a **PushDemo** alkalmazást, majd kattintson ismét a **Send (Küldés** ) gombra a **[Poster](https://www.postman.com/downloads)**-ben.

1. Győződjön meg arról, hogy **200 OK** választ kap a **[Poster](https://www.postman.com/downloads)** szolgáltatásban, és hogy a csendes értesítés nem jelenik meg az értesítési régióban.
