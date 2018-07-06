---
title: Az IoT hub-üzenetek mentése Azure-adattárba |} A Microsoft Docs
description: IoT Hub üzenet-útválasztása használatával az IoT hub-üzenetek mentése az Azure blob storage. Az IoT hub-üzenetek tartalmaz információkat, például érzékelőktől kapott adatok, IoT-eszközök által küldött.
author: rangv
manager: ''
keywords: IOT, iot érzékelő adattárolás adattárolás
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 84355453a5cb8d8f42abdcbde5432651c9c035b0
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856290"
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Az Azure blob storage-érzékelő adatokat tartalmazó IoT hub-üzenetek mentése

![Végpontok közötti diagram](./media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti, hogyan hozhat létre egy Azure storage-fiókot és a egy Azure-függvényalkalmazás IoT hub-üzenetek tárolására az Azure Blob storage-ban.

## <a name="what-you-do"></a>TEENDŐ

- Hozzon létre egy Azure-tárfiókot.
- Állítsa be az IoT hub üzenetek továbbítását a tárolóba.

## <a name="what-you-need"></a>Mi szükséges

- [Az eszköz beállítása](iot-hub-raspberry-pi-kit-node-get-started.md) ahhoz, hogy biztosítsák az alábbi követelményeknek:
  - Aktív Azure-előfizetéssel
  - Az IoT hub, az előfizetéséhez 
  - A futó alkalmazást, amely üzeneteket küld az IoT hubhoz

## <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

1. Az a [az Azure portal](https://portal.azure.com/), kattintson a **erőforrás létrehozása** > **tárolási** > **tárfiók**.

2. Adja meg a szükséges információkat a storage-fiók:

   ![Storage-fiók létrehozása az Azure Portalon](./media/iot-hub-store-data-in-azure-table-storage/1_azure-portal-create-storage-account.png)

   * **Név**: a tárfiók nevére. A névnek globálisan egyedinek kell lennie.

   * **Fiók típusa**: válasszon `Storage (general purpose v1)`.

   * **Hely**: válassza ki az ugyanazon a helyen, amely az IoT hub használja.

   * **Replikációs**: válasszon `Locally-redundant storage (LRS)`.

   * **Teljesítmény**: válasszon `Standard`.

   * **Biztonságos átvitelre van szükség**: válasszon `Disabled`.

   * **Előfizetés**: Válassza ki az Azure-előfizetést.

   * **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   * **Rögzítés az irányítópulton**: Ezt a lehetőséget kiválasztva könnyen hozzáférhet az IoT Hubhoz az irányítópultról.

3. Kattintson a **Create** (Létrehozás) gombra.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Az IoT hub üzenetek továbbítását a tárolás előkészítése

Az IoT Hub natív módon támogatja az Azure storage-útválasztási üzenetek blobként. Az Azure IoT Hub az egyedi végpontok megismernie, olvassa el [beépített IoT Hub-végpontok listája](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints).

### <a name="add-storage-as-a-custom-endpoint"></a>Tárhely hozzáadása egy egyéni végpontként

1. Keresse meg az IoT hub, az Azure Portalon. 

2. Kattintson a **végpontok** > **hozzáadása**. 

3. A végpont neve, és válassza ki **Azure Storage-tároló** a végpont típusa. 

4. A Színválasztó segítségével válassza ki az előző szakaszban létrehozott tárfiókot. Hozzon létre egy storage-tárolót, és válassza ki, majd kattintson **OK**.

   ![Egyéni végpont létrehozásához az IoT hubon](./media/iot-hub-store-data-in-azure-table-storage/2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Egy útvonalat az útvonal-adatok hozzáadása a tárolóhoz

1. Kattintson a **útvonalak** > **Hozzáadás** , és adjon meg egy nevet az útvonal. 

2. Válassza ki **Eszközüzenetek** az adatforrást, és válassza ki a storage-végpont imént létrehozott útvonalban végpontként. 

3. Adja meg `true` a lekérdezési karakterláncot, majd kattintson **mentése**.

   ![Az IoT Hub útvonal létrehozása](./media/iot-hub-store-data-in-azure-table-storage/3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Adjon hozzá egy útvonalat a gyakori elérésű útvonal telemetriát (nem kötelező)

Alapértelmezés szerint az IoT Hub irányítja az összes üzenetet, amely nem egyezik meg a beépített végpont bármely másik útvonalon. Mivel az összes telemetriai üzeneteket mostantól egyezik a szabályt, amely az üzeneteket irányítja a storage, adjon hozzá egy másik útvonalat az üzenetek a beépített végpont lehet írni kell. Nem jár további költségekkel üzenetek útválasztását a több végpontot.

> [!NOTE]
> Ezt a lépést kihagyhatja, ha nem végez a telemetriai üzeneteket kapcsolatos további.

1. Kattintson a **Hozzáadás** a útvonalak panelről, és adjon meg egy nevet az útvonal. 

2. Válassza ki **Eszközüzenetek** adatforrásként és **események** végpontjaként. 

3. Adja meg `true` a lekérdezési karakterláncot, majd kattintson **mentése**.

  ![Gyakori elérési útvonal az IoT Hub létrehozása](./media/iot-hub-store-data-in-azure-table-storage/4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Az üzenet a storage-tároló ellenőrzése

1. A mintaalkalmazás futtatása üzenetek küldése az IoT hubon az eszközön.

2. [Töltse le és telepítse az Azure Storage Explorer](http://storageexplorer.com/).

3. Nyissa meg a Storage Explorerben, kattintson a **Azure-fiók hozzáadása** > **jelentkezzen be a**, majd jelentkezzen be Azure-fiókjába.

4. Kattintson az Azure-előfizetés > **Tárfiókok** > a tárfiók > **Blobtárolók** > a tárolót.

   Az IoT hub blob-tárolóban naplózza az eszközről küldött üzenetek kell megjelennie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ebben az oktatóanyagban egy storage-fiók hozzáadva, és majd a útválasztás az üzeneteket az IoT hubról a tárfiók lehet írni hozzá. A létrehozott erőforrások törléséhez távolítsa el az útválasztási adatokat, és ezután törölje a storage-fiók. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Kattintson a **erőforráscsoportok** , és válassza ki a használt erőforráscsoportot. A csoportban lévő erőforrások listájában jelenik meg. 

   > [!NOTE]
   > Ha el kívánja távolítani az összes erőforrást erőforráscsoportban, kattintson a **törlése** , törölje az erőforráscsoportot, majd kövesse az utasításokat. Ez a művelet eltávolítja minden az erőforráscsoport, így az oktatóanyag végére az erőforrások karbantartása, és továbbléphet a következő szakaszban.

3. Kattintson az IoT hub ebben az oktatóanyagban használt. 

4. Az IoT Hub paneljén kattintson **útvonalak**. Kattintson a jelölőnégyzetre hozzáadva, majd kattintson a útválasztási szabály mellett **törlése**. Ha a rendszer kéri, ha biztos abban, hogy törölni kívánja, kattintson a **Igen**.

   ![Útválasztási szabály eltávolítása](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-routing.png)

   Zárja be az Útválasztás panelt. Az erőforráscsoport panelen a rendszer visszairányítja.

5. Kattintson újra az IoT hubon. 

6. Az IoT Hub paneljén kattintson **végpontok**. Kattintson a végpontra a tároló hozzáadott jelölőnégyzetét, majd **törlése**. Ha a rendszer kéri, ha biztos abban, hogy törli a kiválasztott végpont, kattintson a **Igen**.

    ![Végpont eltávolítása](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-endpoint.png)

    A végpontok panel bezárásához. Az erőforráscsoport panelen a rendszer visszairányítja. 

7.  Kattintson a storage-fiók ebben az oktatóanyagban beállította. 

8.  A Storage-fiók panelen kattintson **törlése** , távolítsa el a storage-fiókot. Ekkor megnyílik a **tárfiók törlése** ablaktáblán.

   ![Távolítsa el a storage-fiók](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-storageaccount.png)

8.  Írja be a tárfiók nevét, majd kattintson a **törlése** a panel alján. 

## <a name="next-steps"></a>További lépések

Sikeresen létrehozta az Azure storage-fiók és irányított üzeneteket az IoT hubról a tárfiók blob-tárolóba.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
