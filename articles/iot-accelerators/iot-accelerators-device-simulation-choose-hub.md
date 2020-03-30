---
title: Meglévő IoT-központ használata eszközszimulációs megoldással – Azure | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan konfigurálhatja az eszközszimulációs megoldás gyorsítót egy meglévő IoT Hub használatára.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889182"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Meglévő IoT Hub használata az Eszközszimulációs megoldásgyorsítóval

Eszközszimuláció telepítésekor is választhat, hogy egy IoT hub a szimulációban használható üzembe helyezése. Ez a beállítás [egyetlen méretezési egységgel rendelkező S2 szintű IoT-központot](../iot-hub/iot-hub-scaling.md)telepít. Ha telepíti ezt az opcionális IoT hub, továbbra is választhatja, hogy egy másik IoT Hub egy szimulációs futtatás.

Ha úgy dönt, hogy nem telepíti a választható IoT Hub, a saját hub használata a szimulációk futtatott.

Ha nem rendelkezik IoT-központtal, bármikor létrehozhat egy újat az [Azure Portalon.](https://portal.azure.com)

Egy már meglévő IoT hub használatához az **iothubowner** megosztott hozzáférési szabályzat kapcsolati karakterláncára van szükség. Ez a kapcsolati karakterlánc az [Azure Portalon](https://portal.azure.com)keresztül szerezhető be:

1. A központ konfigurációs lapján kattintson a **Megosztott hozzáférési házirendek**elemre.

1. Kattintson **iothubowner**.

1. Másolja az elsődleges vagy másodlagos kapcsolati karakterláncot.

[![Kapcsolati karakterlánc beszereznie](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

A szimuláció konfigurálásakor másolt kapcsolati karakterláncot használja:

![Szimuláció konfigurálása](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>További lépések

Ebben az útmutató útmutatóban megtanulta, hogyan használhatja a meglévő IoT-központot egy szimulációban. Ezután érdemes lehet megtanulni, hogyan [hozhat létre egy speciális eszközmodellt](iot-accelerators-device-simulation-advanced-device.md) egy szimulációhoz.
