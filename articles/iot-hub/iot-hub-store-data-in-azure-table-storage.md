---
title: "Az IoT hub üzenetek mentése az Azure data Storage |} Microsoft Docs"
description: "Az IoT-központ üzenet útválasztás használatával az IoT hub üzenetek mentése az Azure blob storage. Az IoT hub üzeneteket tartalmaz információkat, például az IoT-eszközről küldött érzékelőadatait."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "az IOT adattárolás, iot érzékelő adatokat tároló"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: xshi
ms.openlocfilehash: f6b334dbc9903d0080b74052062de7564aa4a993
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Az IoT hub üzeneteket, amelyek tartalmazzák az érzékelők adataiból, hogy az Azure blob storage mentése

![Végpontok közötti diagramja](media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti az Azure-tárfiók és az IoT hub üzenetek tárolásához a blob Storage Azure függvény alkalmazás létrehozása.

## <a name="what-you-do"></a>Mit

- Hozzon létre egy Azure-tárfiókot.
- Készítse elő az IoT hub üzenetek tárhelyre.

## <a name="what-you-need"></a>Mi szükséges

- [Konfigurálja az eszközt](iot-hub-raspberry-pi-kit-node-get-started.md) fedik le a következő követelményeknek:
  - Aktív Azure-előfizetés
  - Az IoT-központ az előfizetéshez tartozó 
  - Egy futó alkalmazást, amely üzeneteket küld az IoT hub

## <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

1. Az a [Azure-portálon](https://portal.azure.com/), kattintson a **hozzon létre egy erőforrást** > **tárolási** > **tárfiók**  >  **Létrehozása**.

2. Adja meg a tárfiók szükséges adatokat:

   ![A storage-fiók létrehozása az Azure-portálon](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **Név**: a tárfiók nevét. A névnek globálisan egyedinek kell lennie.

   * **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   * **Rögzítés az irányítópulton**: Ezt a lehetőséget kiválasztva könnyen hozzáférhet az IoT Hubhoz az irányítópultról.

3. Kattintson a **Create** (Létrehozás) gombra.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Az IoT hub üzenetek tárhely előkészítése

Az IoT-központ natív módon támogatja az Azure storage üzenetek blobként.

### <a name="add-storage-as-a-custom-endpoint"></a>Egyéni végpontként tároló hozzáadása

Nyissa meg az Azure-portálon az IoT hub. Kattintson a **végpontok** > **hozzáadása**. A végpont neve, és válassza ki **Azure-tárolót** a végpont típusa. Az objektumválasztó segítségével válassza ki az előző szakaszban létrehozott tárfiókot. Hozzon létre egy tárolót, és válassza ki azt, majd kattintson **OK**.

  ![Az IoT hubon egyéni-végpont létrehozása](media\iot-hub-store-data-in-azure-table-storage\2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Egy útvonalat az útvonal-adatokat hozzáadni a tárhelyhez

Kattintson a **útvonalak** > **Hozzáadás** és adjon meg egy nevet az útvonal. Válassza ki **eszközre küldött üzenetek** az adatforrást, és válassza ki a tárolási végpont most létrehozott és a végpontnak a útvonal. Adja meg `true` lekérdezési karakterláncként kell beállítani, majd kattintson a **mentése**.

  ![Az IoT hubon útvonal létrehozása](media\iot-hub-store-data-in-azure-table-storage\3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Adjon hozzá egy útvonalat a gyakran használt adatok elérési útja telemetriai (nem kötelező)

Alapértelmezés szerint az IoT-központ továbbítja az összes üzenetet, amelyek nem felelnek meg a beépített végpontnak egyéb útvonalakat. Mivel az összes telemetriai üzenet most egyezik a szabályt, amely továbbítja az üzeneteket tároló, kell egy másik útvonal hozzáadása az üzenetek a beépített végpont írandó. Nincs nem több végpontot üzeneteknek kell külön fizetni.

> [!NOTE]
> Ezt a lépést kihagyhatja, ha a nem további a telemetriai adatok üzenetek feldolgozása során.

Kattintson a **Hozzáadás** a útvonalak panelről, és adjon meg egy nevet az útvonal. Válassza ki **eszközre küldött üzenetek** adatforrásként és **események** és a végpontnak. Adja meg `true` lekérdezési karakterláncként kell beállítani, majd kattintson a **mentése**.

  ![Az IoT hubon közbeni elérési útvonal létrehozása](media\iot-hub-store-data-in-azure-table-storage\4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Ellenőrizze a tároló az üzenet

1. Futtassa a mintaalkalmazást üzeneteket küldhet az IoT hub az eszközön.

2. [Töltse le és telepítse az Azure Tártallózó](http://storageexplorer.com/).

3. Nyissa meg a Tártallózót, kattintson a **vegyen fel egy Azure-fiókot** > **bejelentkezés**, majd jelentkezzen be az Azure-fiókjával.

4. Kattintson az Azure-előfizetéshez > **Tárfiókok** > a tárfiók > **Blobtárolók** > a tárolóban.

   Az IoT hub blobtárolóba naplózza az eszközről küldött üzenetek kell megjelennie.

## <a name="next-steps"></a>További lépések

Sikeresen létrehozta az Azure storage-fiók és irányított üzenetek IoT-központ a blob storage-fiókhoz tartozó tárolót.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
