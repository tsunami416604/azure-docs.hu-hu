---
title: 'Rövid útmutató: Blob-tárolási események küldése a webvégpontra – portál'
description: 'Rövid útmutató: Az Azure Event Grid és az Azure Portal használatával blobtár-fiókot hozhat létre, és előfizethet az eseményeket. Küldje el az eseményeket egy Webhookba.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 04/16/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: ada451b6bb3578a2903e9bd832b98981d7029d1d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605823"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-with-the-azure-portal"></a>Rövid útmutató: Blob-tárolási események irányítása a webvégpontra az Azure Portalon

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben létrehoz egy Blob Storage-fiókot az Azure Portallal, feliratkozik a Blob Storage-hoz tartozó eseményekre, és aktivál egy eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan webalkalmazásnak küldjük el, amely az üzenetek gyűjtésével és megjelenítésével foglalkozik.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

A folyamat végén látni fogja, hogy a rendszer elküldte az eseményadatokat a webalkalmazásnak.

![Eredmények megtekintése](./media/blob-event-quickstart-portal/view-results.png)

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Blob Storage létrehozásához kattintson az **Erőforrás létrehozása** elemre. 

1. A **Storage** elemet kiválasztva szűrje az elérhető lehetőségeket, majd válassza a **Storage-fiók – blob, fájl, táblázat, üzenetsor** lehetőséget.

   ![Tárhely kiválasztása](./media/blob-event-quickstart-portal/create-storage.png)

   Az eseményekre feliratkozáshoz vagy egy általános célú v2 tárfiókot vagy Blob Storage-fiókot hozzon létre.
   
1. A **Tárfiók létrehozása** lapon tegye a következő lépéseket:
    1. Válassza ki az Azure-előfizetését. 
    2. Az **Erőforráscsoport**csoportban hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt. 
    3. Adja meg a tárfiók nevét. 
    4. Válassza az **Áttekintés + létrehozás** lehetőséget. 

       ![Kezdeti lépések](./media/blob-event-quickstart-portal/provide-blob-values.png)    
    5. A **Véleményezés + létrehozás** lapon tekintse át a beállításokat, és válassza a **Létrehozás gombot.** 

        >[!NOTE]
        > Csak **a storageV2 (általános célú v2)** és **a BlobStorage** támogatási eseményintegrációs tárfiókok. **A storage (genral purpose v1)** *nem* támogatja az Event Griddel való integrációt.

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A Blob Storage-eseményekre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. 

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Az **Egyéni telepítés** lapon tegye a következő lépéseket: 
    1. Az **Erőforráscsoport csoport**ban válassza ki a tárfiók létrehozásakor létrehozott erőforráscsoportot. Az erőforráscsoport törlésével könnyebb lesz megtisztítani, miután végzett az oktatóanyaggal.  
    2. A **Webhely neve**mezőbe írja be a webalkalmazás nevét.
    3. Az **Üzemeltetési csomag neve**esetén adja meg az App Service-csomag nevét a webalkalmazás üzemeltetéséhez.
    4. Jelölje be az **I agree (Elfogadom a fenti feltételeket )** jelölőnégyzet. 
    5. Válassza a **Beszerzés** lehetőséget. 

       ![Telepítési paraméterek](./media/blob-event-quickstart-portal/template-deploy-parameters.png)
1. Az üzembe helyezés befejezése eltarthat néhány percig. Válassza a Riasztások (csengő ikon) lehetőséget a portálon, majd válassza az **Ugrás az erőforráscsoportra**lehetőséget. 

    ![Riasztás - navigálás az erőforráscsoportra](./media/blob-event-quickstart-portal/navigate-resource-group.png)
4. Az **Erőforráscsoport** lap erőforráslistájában válassza ki a létrehozott webalkalmazást. Ebben a listában az App Service-csomag és a tárfiók is megjelenik. 

    ![Webhely kiválasztása](./media/blob-event-quickstart-portal/resource-group-resources.png)
5. A webalkalmazás **App Service-lapján** válassza ki a webhelyre való navigáláshoz szükséges URL-címet. Az URL-címnek a `https://<your-site-name>.azurewebsites.net`következő formátumban kell lennie: .
    
    ![Navigálás a webhelyre](./media/blob-event-quickstart-portal/web-site.png)

6. Erősítse meg, hogy látja a webhelyet, de még nem történt esemény.

   ![Új hely megtekintése](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-blob-storage"></a>Feliratkozás a Blob Storage-ra

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni az eseményeket.

1. A portálon keresse meg a korábban létrehozott Azure Storage-fiók. A bal oldali menüben válassza a **Minden erőforrás** lehetőséget, és válassza ki a tárfiókot. 
2. A **Tárfiók** lapon válassza az **Események** lehetőséget a bal oldali menüben.
1. Válassza a **További beállítások**, majd a **Webhook** elemet. Eseményeket küld a megjelenítő alkalmazás egy web hook a végponthoz. 

   ![Webhook kiválasztása](./media/blob-event-quickstart-portal/select-web-hook.png)
3. Az **Esemény-előfizetés létrehozása** lapon tegye a következő lépéseket: 
    1. Adja meg az esemény-előfizetés **nevét.**
    2. Válassza a **Web Hook** lehetőséget a **Végpont típushoz**. 

       ![Webhook-végpont típusának kiválasztása](./media/blob-event-quickstart-portal/select-web-hook-end-point-type.png)
4. A **Végpont**területen kattintson **a Végpont kiválasztása gombra,** írja `api/updates` be a webalkalmazás URL-címét, és adja hozzá a kezdőlap URL-címét (például: `https://spegridsite.azurewebsites.net/api/updates`), majd válassza a Kijelölés megerősítése **lehetőséget.**

   ![Végpontkijelölés megerősítése](./media/blob-event-quickstart-portal/confirm-endpoint-selection.png)
5. Most az **Esemény-előfizetés létrehozása** lapon válassza a **Létrehozás** lehetőséget az esemény-előfizetés létrehozásához. 

   ![Naplók kiválasztása](./media/blob-event-quickstart-portal/create-subscription.png)

1. Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

   ![Előfizetési esemény megtekintése](./media/blob-event-quickstart-portal/view-subscription-event.png)

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak.

## <a name="send-an-event-to-your-endpoint"></a>Esemény elküldése a végpontra

A Blob Storage-hoz egy eseményt egy fájl feltöltésével aktiválhat. A fájlnak nem kell tartalommal rendelkeznie. A cikkek feltételezik, hogy van egy testfile.txt fájlja, de bármilyen fájlt használhat.

1. Az Azure Portalon keresse meg a Blob storage-fiók, és válassza **a tárolók** az **áttekintés** lapon.

   ![Blobok kiválasztása](./media/blob-event-quickstart-portal/select-blobs.png)

1. Válassza a **+ Tároló** lehetőséget. Adjon nevet a tárolónak, és használjon bármilyen hozzáférési szintet, és válassza a **Létrehozás gombot.** 

   ![Tároló hozzáadása](./media/blob-event-quickstart-portal/add-container.png)

1. Válassza ki az új tárolót.

   ![Tároló kiválasztása](./media/blob-event-quickstart-portal/select-container.png)

1. Fájl feltöltéséhez válassza a **Feltöltés** lehetőséget. A **Blob feltöltése** lapon tallózzon és jelöljön ki egy fájlt, amelyet tesztelésre fel szeretne tölteni, majd válassza a **Feltöltés** az adott oldalon lehetőséget. 

   ![Feltöltés kiválasztása](./media/blob-event-quickstart-portal/upload-file.png)

1. Keresse meg tallózással a tesztfájlt, és töltse fel.

1. Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Az üzenet JSON formátumú, és egy vagy több eseményt tartalmazó tömböt tartalmaz. A következő példában a JSON-üzenet egy tömböt tartalmaz egy eseménnyel. Tekintse meg a webalkalmazást, és figyelje meg, hogy egy **blob létrehozott** esemény érkezett. 

   ![Blob létrehozott esemény](./media/blob-event-quickstart-portal/blob-created-event.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább kívánja használni az eseményt, akkor ne törölje a cikkben létrehozott erőforrásokat. Ellenkező esetben törölje a cikkben létrehozott erőforrásokat.

Válassza ki az erőforráscsoportot, majd válassza az **Erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre egyéni témaköröket és esemény-előfizetéseket, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
