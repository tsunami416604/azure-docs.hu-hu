---
title: Fájlfeltöltés konfigurálása az Azure portal használatával |} A Microsoft Docs
description: Hogyan konfigurálása az IoT hubhoz csatlakoztatott eszközökről feltöltésének engedélyezése az Azure portal használatával. A cél az Azure storage-fiók konfigurálásával kapcsolatos információkat tartalmaz.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: a9f9eeaed2716c5d492099568fd6f90080471af2
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42055728"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurálja az IoT Hub fájlt tölt fel az Azure portal használatával

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Fájl feltöltése

Használatához a [fájlba feltöltésének működését az IoT Hub](iot-hub-devguide-file-upload.md), először társítania kell egy Azure Storage-fiókot a hub. Válassza ki **fájlfeltöltés** Fájlfeltöltés tulajdonságai az IoT hub éppen módosított listájának megjelenítéséhez.

![Nézet az IoT Hub-fájl feltöltése a portál beállítások](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **A tároló**: válassza ki az aktuális Azure-előfizetés társítása az IoT Hub az Azure Storage-fiók egy blob-tárolóba az Azure portal használatával. Ha szükséges, létrehozhat egy Azure Storage-fiókot a a **tárfiókok** panelre, és a blob-tároló a **tárolók** panelen. Az IoT Hub automatikusan létrehozza az SAS URI-k eszközöket használja, ha azok a fájlok feltöltése a blob-tárolóba írási engedéllyel rendelkező.

   ![Fájl feltöltése a storage-tárolók megtekintése a portálon](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **A feltöltött fájlokkra vonatkozó értesítések fogadása**: a váltógomb keresztül fájl feltöltése értesítések engedélyezése vagy letiltása.

* **SAS-élettartam**: Ez a beállítás akkor a time-to-live, az eszközt az IoT Hub által visszaadott SAS URI-azonosítókat. Alapértelmezés szerint egy óra beállítva, de más értékeket, a csúszka segítségével testre szabható.

* **Értesítési beállítások alapértelmezett TTL fájl**: A time-to-live-fájl feltöltése értesítési, mielőtt lejár. Alapértelmezés szerint állítsa be egy nap, de más értékeket, a csúszka segítségével testre szabható.

* **A fájl értesítési kézbesítések maximális száma**: hányszor az IoT Hub fájlt próbál meg feltölteni értesítés. Alapértelmezés szerint a 10-re állítva, de más értékeket, a csúszka segítségével testre szabható.

   ![Konfigurálás a portálon az IoT Hub-fájl feltöltése](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>További lépések

Az IoT Hub a fájl feltöltése képességeivel kapcsolatos további információkért lásd: [fájlfeltöltés az eszközökről](iot-hub-devguide-file-upload.md) az IoT Hub fejlesztői útmutatójában található.

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [IoT-eszközök tömeges felügyelete](iot-hub-bulk-identity-mgmt.md)
* [Az IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
* [Az IoT-megoldás az alapoktól biztonságos mentése](../iot-fundamentals/iot-security-ground-up.md)
