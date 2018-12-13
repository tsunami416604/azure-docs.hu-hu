---
title: Exportálhatja az adatokat az Azure IoT Central |} A Microsoft Docs
description: Adatok exportálása az Azure IoT Central alkalmazásból
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: cba0bad2e81ffddedfc4ca04e82e17e4286b389b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312119"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportálhatja az adatokat az Azure IoT Central

*Ez a témakör a rendszergazdák vonatkozik.*

Ez a cikk bemutatja, hogyan használható a folyamatos exportálás funkció az Azure IoT Central exportálhatja az adatokat a saját **Azure Blob Storage**, **Azure Event Hubs**, és **Azure Service Bus** példányok. Exportálhatja **mérések**, **eszközök**, és **eszközsablonok** saját célra a meleg folyamatból és a ritka elérésű útvonal analitika. Hosszú távú trendek elemzése a Microsoft Power bi-ban való futtatásához a Blob storage-adatok exportálása, vagy exportálja az adatokat az Event Hubs és a Service Bus, átalakíthatja és kiegészítheti a közel valós idejű az Azure Logic Apps vagy az Azure Functions-adatok.

> [!Note]
> Folyamatos adatexportálás bekapcsolásakor kap csak az adatok ettől a pillanattól kezdve. Jelenleg adatokat nem lehet beolvasni egy alkalommal, amikor folyamatos adatexportálás ki volt kapcsolva. További korábbi adatok megőrzése, kapcsolja be a folyamatos exportálás korai.

## <a name="prerequisites"></a>Előfeltételek

- Egy rendszergazdának kell lennie az IoT-központ alkalmazásában

## <a name="export-to-blob-storage"></a>Blob Storage-bA exportálása

Mértékek, eszközök és sablonok eszközadatok lesznek exportálva az percenként egyszer, a tárfiók az egyes fájlt, amely tartalmazza a változások a batch, mivel az utolsó exportált fájl. Az exportált adatok [Apache AVRO](https://avro.apache.org/docs/current/index.html) formátumban.

Tudjon meg többet [exportálása a Blob storage](howto-export-data-blob-storage.md).

## <a name="export-to-event-hubs-and-service-bus"></a>Az Event Hubs és a Service Bus exportálása

Mértékek, eszközök és eszközadatok sablonok exportálja az event hubs vagy a Service Bus-üzenetsorba vagy témakörbe. Exportált mérések adatok közel valós idejű érkezik, és tartalmazza a teljes az üzenetet küld az IoT-központ, az eszközök nem csak a maguk mérések értékeinek. Exportált eszközök adatok percenként kötegekben érkezik, és tulajdonságokat és beállításokat az összes eszköz módosításokat tartalmaz, és exportált eszközsablonok összes eszközsablonok módosításokat tartalmaz.


Tudjon meg többet [exportálna az Event Hubs és a Service Bus](howto-export-data-event-hubs-service-bus.md).

## <a name="set-up-export-destination"></a>Exportálási cél beállítása

Ha nem rendelkezik egy meglévő tároló/Event Hubs/Service Bus exportálása, kövesse az alábbi lépéseket:

### <a name="create-storage-account"></a>Storage-fiók létrehozása

1. Hozzon létre egy [új storage-fiókot az Azure Portalon](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). További a [Azure Storage-docs](https://aka.ms/blobdocscreatestorageaccount).
2. Adja meg a fiók típusú tárfiók, **általános célú** vagy **a Blob storage-**.
3. Válasszon egy előfizetést. 

    > [!Note] 
    > Most már exportálhatja az adatokat más előfizetésekre, amelyek **nem azonos** azzal, az utólagos elszámolású IoT Central alkalmazáshoz. Ebben az esetben a kapcsolati karakterlánc használatával csatlakozik.

4. Hozzon létre egy tárolót a tárfiókjában. Lépjen a tárfiókhoz. A **Blob Service**válassza **Blobok tallózása**. Válassza ki **+ tároló** hozzon létre egy új tárolót a tetején.

### <a name="create-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

1. Hozzon létre egy [új Event Hubs-névtér az Azure Portalon](https://ms.portal.azure.com/#create/Microsoft.EventHub). További a [Azure Event Hubs docs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Válasszon egy előfizetést. 

    > [!Note] 
    > Most már exportálhatja az adatokat más előfizetésekre, amelyek **nem azonos** azzal, az utólagos elszámolású IoT Central alkalmazáshoz. Ebben az esetben a kapcsolati karakterlánc használatával csatlakozik.
3. Az Event Hubs-névtér az eseményközpont létrehozásához. Lépjen a névteréhez, majd válassza **+ Event Hub** felső hozhat létre egy event hubs-példánnyal.

### <a name="create-service-bus-namespace"></a>A Service Bus-névtér létrehozása

1. Hozzon létre egy [új Service Bus-névteret, az Azure Portalon](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . További a [Azure Service Bus docs](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Válasszon egy előfizetést. 

    > [!Note] 
    > Most már exportálhatja az adatokat más előfizetésekre, amelyek **nem azonos** azzal, az utólagos elszámolású IoT Central alkalmazáshoz. Ebben az esetben a kapcsolati karakterlánc használatával csatlakozik.

3. Nyissa meg a Service Bus-névteret, és válassza ki **+ üzenetsor** vagy **+ témakör** hozhat létre egy üzenetsorba vagy témakörbe történő exportálása tetején.

## <a name="set-up-continuous-data-export"></a>Állítsa be a folyamatos exportálás

Most, hogy a tárolási és Event Hubs és a Service Bus cél exportálhatja az adatokat, kövesse az alábbi lépéseket a folyamatos exportálás beállítása. 

1. Jelentkezzen be az IoT Central alkalmazáshoz.

2. A bal oldali menüben kattintson a **folyamatos adatexportálás**.

    > [!Note]
    > Ha nem látja a folyamatos adatexportálás bal oldali menüben lévő, Ön nem rendszergazda az alkalmazásban. Kérdezze meg a rendszergazda állíthatja be az adatok exportálása.

    ![Új cde Eseményközpont létrehozása](media/howto-export-data/export_menu.PNG)

3. Kattintson a **+ új** gombra a jobb felső sarokban. Válasszon egyet az **Azure Blob Storage**, **Azure Event Hubs**, vagy **Azure Service Bus** az exportálás céljaként. 

    > [!NOTE] 
    > Export alkalmazásonként maximális száma öt. 

    ![Hozzon létre új folyamatos adatexportálás](media/howto-export-data/export_new.PNG)

4. A legördülő listában jelölje ki a **tárolási fiók és az Event Hubs-névtér/Service Bus-névtér**. A legutóbbi lehetőséget is kiválaszthat a listában, amely **adjon meg egy kapcsolati karakterláncot**. 

    > [!NOTE] 
    > Storage-fiókok és az Event Hubs névterekben/Service Bus-névterek az csak akkor jelenik meg a **megegyező előfizetésben, az IoT-központ alkalmazás**. Ha szeretne exportálni egy célhelyre kívül ehhez az előfizetéshez, válasszon **adjon meg egy kapcsolati karakterláncot** , és tekintse meg az 5. lépés.

    > [!NOTE] 
    > Próbaverziós alkalmazások, csak úgy konfigurálja a folyamatos exportálás 7 napon keresztül egy kapcsolati karakterláncot történik. Ennek oka az, 7 napos próbaverziós alkalmazások nem rendelkeznek társított Azure-előfizetéssel.

    ![Új cde Eseményközpont létrehozása](media/howto-export-data/export_create.PNG)

5. (Nem kötelező) Ha úgy döntött **adjon meg egy kapcsolati karakterláncot**, egy új mező jelenik meg, hogy illessze be a kapcsolati karakterláncot. Kapcsolati karakterláncára beolvasni a:
    - Storage-fiókot, nyissa meg a Storage-fiókba az Azure Portalon.
        - A **beállítások**, kattintson a **hozzáférési kulcsok**
        - 1. kulcs kapcsolati karakterláncát vagy a 2. kulcs kapcsolati karakterlánc másolása
    - Az Event Hubs vagy a Service Bus, nyissa meg a névteret, az Azure Portalon.
        - A **beállítások**, kattintson a **megosztott hozzáférési házirendek**
        - Válassza ki az alapértelmezett **RootManageSharedAccessKey** , vagy hozzon létre egy újat
        - Vagy az elsődleges vagy másodlagos kapcsolati karakterlánc másolása
 
6. A legördülő listából válassza ki a tároló vagy Event hub/üzenetsor vagy témakör.

7. A **exportálható adatot**, adja meg az egyes adattípusok úgy, hogy a típus exportálása **a**.

6. Folyamatos adatexportálás bekapcsolása, ellenőrizze, hogy **adatexportálás** van **a**. Kattintson a **Mentés** gombra.

  ![Folyamatos adatexportálás konfigurálása](media/howto-export-data/export_list.PNG)

7. Néhány perc elteltével az adatok megjelennek a kiválasztott cél.

## <a name="next-steps"></a>További lépések

Most, hogy tudja, hogyan exportálhatja az adatokat, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [Az Azure Blob Storage-adatok exportálása](howto-export-data-blob-storage.md)

> [!div class="nextstepaction"]
> [Adatok exportálása az Azure Event Hubs és Azure Service Bus](howto-export-data-event-hubs-service-bus.md)

> [!div class="nextstepaction"]
> [Hogyan jelenítheti meg az adatok a Power bi-ban](howto-connect-powerbi.md)
