---
title: 'Oktatóanyag: GYIK-bot létrehozása Azure Bot Service'
description: Ebben az oktatóanyagban hozzon létre egy kód nélküli GYIK-robotot QnA Maker és Azure Bot Service.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: ab78ac41be2f900d84e82c21e566f5250b2afd18
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777391"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>Oktatóanyag: GYIK-bot létrehozása Azure Bot Service
Hozzon létre egy GYIK-robotot a QnA Maker és az Azure [bot Service](https://azure.microsoft.com/en-us/services/bot-service/) kód nélkül.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

<!-- green checkmark -->
> [!div class="checklist"]
> * QnA Maker Tudásbázis összekapcsolása egy Azure Bot Service
> * A robot üzembe helyezése
> * Csevegés a robottal a webes csevegésben
> * A robot kijelzése a támogatott csatornákon

## <a name="create-and-publish-a-knowledge-base"></a>Tudásbázis létrehozása és közzététele

Egy Tudásbázis [létrehozásához kövesse a rövid](../Quickstarts/create-publish-knowledge-base.md) útmutató lépéseit. Miután a Tudásbázis sikeresen közzé lett téve, az alábbi oldalra fog érkezni.

![A sikeres közzététel képernyőképe](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Robot létrehozása

A közzététel után létrehozhat egy robotot a **közzétételi lapról:**

* Több robotot is létrehozhat, amelyek mindegyike azonos tudásbázisra mutat a különböző régiókban vagy díjszabási tervekben az egyes robotok számára.
* Ha csak egy robotot szeretne használni a Tudásbázisban, használja a Azure Portal hivatkozásán látható **összes robot megtekintését** az aktuális robotok listájának megtekintéséhez.

Ha módosítja a tudásbázist, és újból közzéteszi, nem kell további műveleteket végrehajtania a robottal. Már konfigurálva van a Tudásbázisban való együttműködésre, és a Tudásbázis minden jövőbeli módosításával működik. Minden alkalommal, amikor közzétesz egy tudásbázist, az ahhoz kapcsolódó összes robot automatikusan frissül.

1. A QnA Maker portál **Közzététel** lapján válassza a **robot létrehozása**lehetőséget. Ez a gomb csak azután jelenik meg, hogy közzétette a tudásbázist.

    ![A robot létrehozásának képernyőképe](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Megnyílik egy új böngésző lap a Azure Portal számára a Azure Bot Service létrehozási oldalával. Konfigurálja az Azure bot Service-t. A robot és a QnA Maker megoszthatják a Web App Service-csomagot, de nem oszthatják meg a webalkalmazást. Ez azt jelenti, hogy a robot **alkalmazásának neve** nem lehet a QnA Maker szolgáltatás alkalmazásának neve.

    * **Ajánlott**
        * A bot fogantyújának módosítása – ha nem egyedi.
        * Válassza ki az SDK nyelvét. A robot létrehozása után letöltheti a kódot a helyi fejlesztési környezetbe, és folytathatja a fejlesztési folyamatot.
    * **Nem ajánlott**
        * A robot létrehozásakor módosítsa a Azure Portal következő beállításait. A meglévő Tudásbázisban előre ki vannak töltve:
           * QnA-hitelesítési kulcs
           * App Service-csomag és-hely


1. A robot létrehozása után nyissa meg a **bot Service** -erőforrást.
1. A **bot Management**alatt válassza **a tesztelés webes csevegésben**lehetőséget.
1. Írja be a következőt a csevegési **üzenetbe**:

    `Azure services?`

    A csevegési robot a Tudásbázisban válaszol.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Adjon meg egy felhasználói lekérdezést a teszt webes csevegésben.":::
1. A robotot a további [támogatott csatornákon](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0&preserve-view=true)is megvilágíthatja.
