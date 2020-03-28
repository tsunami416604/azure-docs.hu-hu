---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 9a20dca71727e83db98c4c97567949bd127fc7fb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77111198"
---
[Az üzenet-útválasztás](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) lehetővé teszi a telemetriai adatok küldését az IoT-eszközökről a beépített Event Hub-kompatibilis végpontok vagy egyéni végpontok, például a blob storage, a Service Bus-várólisták, a Service Bus-témakörök és az Event Hubs. Az egyéni üzenetútválasztás konfigurálásához [útválasztási lekérdezéseket](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) hozhat létre egy adott feltételnek megfelelő útvonal testreszabásához. A beállítás után az IoT Hub automatikusan a végpontokhoz irányítja át a bejövő adatokat. Ha egy üzenet nem egyezik meg a megadott útválasztási lekérdezések egyikével sem, a rendszer az alapértelmezett végponthoz irányítja.

Ebben a két részes oktatóanyagból megtudhatja, hogyan állíthatja be és használhatja ezeket az egyéni útválasztási lekérdezéseket az IoT Hubhasználatával. Az IoT-eszközről több végpont egyikére irányítüzeneteket, beleértve a blobstorage-ot és a Service Bus-várólistát. A Service Bus-várólistába küldött üzeneteket egy logikai alkalmazás veszi fel, és e-mailben küldi el. A nem egyéni üzenet-útválasztási definiált üzeneteket a rendszer elküldi az alapértelmezett végpontnak, majd az Azure Stream Analytics felveszi, és power bi-vizualizációban tekinti meg.

Az oktatóanyag 1.

**I. rész: Erőforrások létrehozása, üzenet-útválasztás beállítása**
> [!div class="checklist"]
> * Hozza létre az erőforrásokat – egy IoT hubot, egy tárfiókot, egy Service Bus-várólistát és egy szimulált eszközt. Ez az Azure Portal, egy Azure Resource Manager-sablon, az Azure CLI vagy az Azure PowerShell használatával végezhető el.
> * Konfigurálja a végpontokat és az üzenetútvonalakat az IoT Hubban a tárfiókhoz és a Service Bus-várólistához.

**II. rész: Üzenetek küldése a központba, útválasztásos eredmények megtekintése**
> [!div class="checklist"]
> * Egy logikai alkalmazás létrehozása, amely egy üzenet Service Bus-üzenetsorba való érkezésekor indul el és küld e-maileket.
> * Egy olyan alkalmazás letöltése és futtatása, amely a Hubhoz üzeneteket küldő IoT-eszközt szimulál a különböző útválasztási lehetőségekhez.
> * Power BI-vizualizáció létrehozása az alapértelmezett végpontra küldött adatokról.
> * Eredmények megtekintése...
> * ... a Service Bus-üzenetsorban és az e-mailekben.
> * ... a tárfiókban.
> * ... a Power BI-vizualizációban.

## <a name="prerequisites"></a>Előfeltételek

* A bemutató első részéhez:
  - Rendelkeznie kell egy Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

* A 2. rész a bemutató:
  - Az oktatóanyag 1.
  - A [Visual Studio](https://www.visualstudio.com/) telepítése.
  - Hozzáféréssel rendelkezik egy Power BI-fiókhoz az alapértelmezett végpont adatfolyam-elemzéséhez. ([A Power BI ingyenes kipróbálása](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Az értesítő e-mailek küldéséhez Office 365-fiókkal kell rendelkeznie.
  - Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. Az oktatóanyagban szereplő minta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
