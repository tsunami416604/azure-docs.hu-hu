---
title: "Fájlfeltöltés konfigurálása az Azure-portál használatával |} Microsoft Docs"
description: "Hogyan használható az Azure-portálon az IoT hub engedélyezéséhez a csatlakoztatott eszközökből fájlfeltöltéseket konfigurálásához. A cél Azure storage-fiók konfigurálásával kapcsolatos információkat tartalmazza."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: 149dd84d7d8f4ff9cd30f9fc649ced3cb364cfb7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Az Azure portál használatával fájlfeltöltések IoT-központ konfigurálása

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Fájl feltöltése

Használatához a [feltöltés funkció fájlt az IoT-központ][lnk-upload], először társítania kell egy Azure Storage-fiókot a központ. Válassza ki **fájlfeltöltés** és jelenítse meg a fájl feltöltése tulajdonságait az IoT hub, amely módosítás alatt áll.

![Az IoT-központ fájlfeltöltés a portál beállítások megtekintése][13]

**A tároló**: az Azure portál segítségével válassza ki a blob-tároló az IoT Hub társítani az aktuális Azure-előfizetéshez az Azure Storage-fiók. Ha szükséges, létrehozhat egy Azure Storage-fiókot a **tárfiókok** a panel megnyitásához, és a blob tároló a **tárolók** panelen. Az IoT-központ automatikusan létrehozza a SAS URI-azonosítók eszközöket használja, ha ezek a fájlok feltöltése a blob tároló írási engedéllyel rendelkező.

![A fájl feltöltése a tároló megtekintése a portálon][14]

**A feltöltött fájlok értesítéseket**: engedélyezheti vagy tilthatja le a fájl feltöltése értesítések a váltógomb keresztül.

**SAS-élettartam**: Ez a beállítás akkor a idő élettartamát az az eszközt az IoT-központ által visszaadott SAS URI-azonosítók. Alapértelmezés szerint egyórás beállítva, de más értékek, a csúszka segítségével testre szabható.

**Az értesítési beállítások alapértelmezett élettartam**: az idő TTL-fájl feltöltése értesítési, előtt lejárt. Alapértelmezés szerint egy nap beállítva, de más értékek, a csúszka segítségével testre szabható.

**Értesítési maximális száma fájl**: A szám, ahányszor az IoT Hub megpróbál egy fájl feltöltése értesítést. Alapértelmezés szerint 10-re állítva, de más értékek, a csúszka segítségével testre szabható.

![Konfigurálja a portálon az IoT Hub-fájl feltöltése][15]

## <a name="next-steps"></a>Következő lépések

Az IoT-központ a fájl feltöltése képességeivel kapcsolatos további információk: [egy eszközről tölt fel] [ lnk-upload] az IoT Hub fejlesztői útmutató.

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Tömeges az IoT-eszközök kezelése][lnk-bulk]
* [Az IoT-központ metrikák][lnk-metrics]
* [Figyelési műveletek][lnk-monitor]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Egy eszköz szimulálva IoT oldala][lnk-iotedge]
* [Az IoT-megoldásból az alapoktól biztonságos mentése][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md
