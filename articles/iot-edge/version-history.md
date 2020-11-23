---
title: IoT Edge verziók navigációja és előzményei – Azure IoT Edge
description: Ismerje meg a IoT Edge újdonságait, és tekintse meg a legújabb kiadásokban található új funkciókkal és képességekkel kapcsolatos információkat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cdb84f817f63e6401d17f18319e161f4c3477293
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024653"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge verziók és kibocsátási megjegyzések

A Azure IoT Edge a GitHubon üzemeltetett nyílt forráskódú IoT Edge projektből készült termék. Minden új kiadás elérhetővé válik a [Azure IoT Edge projektben](https://github.com/Azure/azure-iotedge). A [nyílt forráskódú IoT Edge projekthez](https://github.com/Azure/iotedge)hozzájárulások és hibajelentések is készíthetők.

## <a name="documented-versions"></a>Dokumentált verziók

A webhelyhez tartozó IoT Edge dokumentáció a termék két különböző verziójához érhető el, így kiválaszthatja a IoT Edge-környezethez tartozó tartalmat. Jelenleg a két támogatott verzió a következők:

* **IoT Edge a 1.0.10** az összes szolgáltatásra és képességre kiterjed a legújabb általánosan elérhető kiadáson keresztül: [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10).
* A **IoT Edge 1,2 (előzetes verzió)** további tartalmakat tartalmaz a legújabb előzetes kiadásban elérhető funkciókhoz és képességekhez: [1,2-RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)
  * Habár a IoT Edge 1,2 előzetes verzióban érhető el, telepítenie kell a Release Candidate verzióit. További információ: [Offline vagy adott verzió telepítése](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation).

## <a name="version-history"></a>Verzióelőzmények

Ez a táblázat a IoT Edge csomagok kiadásának legújabb verzióit tartalmazza, és kiemeli az egyes verziókra vonatkozó dokumentációs frissítéseket.

| Kibocsátási megjegyzések és eszközök | Típus | Dátum | Legfontosabb tudnivalók |
| ------------------------ | ---- | ---- | ---------- |
| [1,2 – RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Előnézet | 2020. november | [Az átjárók mögött IoT Edge eszközök](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT-közvetítő](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | 2020. október | [UploadSupportBundle Direct metódus](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Futásidejű mérőszámok feltöltése](how-to-access-built-in-metrics.md)<br>[Útvonal prioritása és élettartama](module-composition.md#priority-and-time-to-live)<br>[Modul indítási sorrendje](module-composition.md#configure-modules)<br>[X. 509 manuális kiépítés](how-to-manual-provision-x509.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | 2020. március | [X. 509 automatikus kiépítés a DPS-vel](how-to-auto-provision-x509-certs.md)<br>[RestartModule Direct metódus](how-to-edgeagent-direct-method.md#restart-module)<br>[támogatás – Bundle parancs](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>További lépések

* [Az összes Azure IoT Edge-kiadás megtekintése](https://github.com/Azure/azure-iotedge/releases)
* [Szolgáltatási kérelmek elkészítése vagy áttekintése a visszajelzési fórumban](https://feedback.azure.com/forums/907045-azure-iot-edge)