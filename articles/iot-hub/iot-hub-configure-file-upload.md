---
title: Fájlfeltöltés konfigurálása az Azure Portalon | Microsoft dokumentumok
description: Az Azure Portal használata az IoT-központ konfigurálásához a csatlakoztatott eszközökről történő fájlfeltöltések engedélyezéséhez. A cél Azure-tárfiók konfigurálásával kapcsolatos információkat tartalmazza.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: bd7cc37b8fc81fc9d4109826743f2243913d0604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60735024"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>IoT Hub-fájlfeltöltések konfigurálása az Azure Portallal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Fájl feltöltése

A [fájlfeltöltési funkció az IoT Hubban](iot-hub-devguide-file-upload.md)használatához először hozzá kell rendelnie egy Azure Storage-fiókot a központi hoz. Válassza **a Fájlfeltöltés** lehetőséget a módosítás alatt álló IoT-központ fájlfeltöltési tulajdonságainak listájának megjelenítéséhez.

![IoT Hub-fájlfeltöltési beállításainak megtekintése a portálon](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Storage-tároló:** Az Azure Portalon válassza ki a blob tároló egy Azure Storage-fiók a jelenlegi Azure-előfizetés az IoT Hub társítására. Ha szükséges, létrehozhat egy Azure Storage-fiókot a **Storage-fiókok** panelen és blob tároló a **tárolók** panelen. Az IoT Hub automatikusan létrehozza a SAS URI-k írási engedélyekkel ezt a blob tárolót az eszközök számára, amelyeket a fájlok feltöltésekor használni.

   ![Fájlfeltöltéstárolók megtekintése a portálon](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Értesítéseket kaphat a feltöltött fájlokról**: A fájlfeltöltési értesítések engedélyezése vagy letiltása a kapcsolón keresztül.

* **SAS TTL**: Ez a beállítás az IoT Hub által az eszközre visszaadott SAS URI-k élő ideje. Alapértelmezés szerint egy órára van beállítva, de a csúszka segítségével más értékekre szabható.

* **Fájl értesítési beállítások alapértelmezett TTL**: A fájlfeltöltési értesítés lejárta előtti ideje. Alapértelmezés szerint egy napra van beállítva, de a csúszka segítségével más értékekre szabható.

* **A fájlok kézbesítésének maximális kézbesítési száma:** Az IoT Hub hányszor kísérel meg fájlfeltöltési értesítést kézbesíteni. Alapértelmezés szerint 10-re van állítva, de a csúszka segítségével más értékekre szabható.

   ![IoT Hub-fájlfeltöltés konfigurálása a portálon](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>További lépések

Az IoT Hub fájlfeltöltési lehetőségeiről további információt a [Fájlok feltöltése eszközről](iot-hub-devguide-file-upload.md) az IoT Hub fejlesztői útmutatójában talál.

Az Azure IoT Hub kezeléséről az alábbi hivatkozásokra kattintva olvashat bővebben:

* [IoT-eszközök tömeges felügyelete](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
* [Biztosítsa IoT-megoldását az alapoktól kezdve](../iot-fundamentals/iot-security-ground-up.md)
