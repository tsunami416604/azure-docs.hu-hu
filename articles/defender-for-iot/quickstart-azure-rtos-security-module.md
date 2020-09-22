---
title: 'Gyors útmutató: az Azure RTOS biztonsági moduljának konfigurálása és engedélyezése'
description: Ismerje meg, hogyan hozhatja be és engedélyezheti az Azure RTOS szolgáltatás biztonsági modulját az Azure-IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 6f438bd7dd0b3e45fd292947a3b08db80401b825
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947164"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Gyors útmutató: biztonsági modul az Azure RTOS (előzetes verzió)

Ez a cikk az első lépések előtt ismerteti az előfeltételeket, és bemutatja, hogyan engedélyezheti az Azure RTOS Service biztonsági modulját egy IoT Hub. Ha jelenleg nem rendelkezik IoT Hubval, a kezdéshez tekintse meg [a IoT hub létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) című témakört.

> [!NOTE]
> Az Azure RTOS biztonsági modulja csak a standard szintű IoT hubok esetében támogatott.

## <a name="prerequisites"></a>Előfeltételek 

### <a name="supported-devices"></a>Támogatott eszközök

- ST STM32F746G-felderítési készlet
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Az [Azure RTOS GitHub-erőforrás biztonsági moduljának](hhtps://github.com/azure-rtos/azure-iot-preview/releases)használatával letöltheti, lefordíthatja és futtathatja az adott tábla és eszköz (IAR, fél ide vagy számítógép) egyik. zip fájlját.

### <a name="azure-resources"></a>Azure-erőforrások

Az első lépések előkészítésének következő lépése az Azure-erőforrások előkészítése. Szüksége lesz egy IoT Hubra, és javaslatot teszünk egy Log Analytics-munkaterületre. IoT Hub esetén az eszközhöz való kapcsolódáshoz IoT Hub kapcsolati karakterláncra lesz szüksége. 
  
### <a name="iot-hub-connection"></a>IoT Hub-kapcsolatok

A kezdéshez IoT Hub-kapcsolatok szükségesek. 

1. Nyissa meg a **IoT Hubt** Azure Portal.
1. Másolja a IoT-kapcsolódási karakterláncot a [konfigurációs fájlba](how-to-azure-rtos-security-module.md).


A kapcsolatok hitelesítő adatai a felhasználói alkalmazás konfigurációjának **HOST_NAME**, **DEVICE_ID**és **DEVICE_SYMMETRIC_KEY**.

Az Azure RTOS biztonsági modulja az Azure IoT middleware-kapcsolatokat használja a **MQTT** protokoll alapján.


### <a name="log-analytics-workspace"></a>Log Analytics-munkaterület

A IoT Hub Log Analytics betöltését a IoT-megoldás alapértelmezett védelmezője kikapcsolja. Az Azure RTOS biztonsági moduljának használatának engedélyezéséhez tegye a következőket: 
1. A Azure Portal lépjen a IoT Hub.
1. A **Biztonság** menüben válassza a **Beállítások** lehetőséget.
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Az Azure RTOS Access adatgyűjtési beállítása"::: 
1. Válassza **az adatgyűjtés**lehetőséget. 
1. A **munkaterület-konfiguráció** lehetőségnél kapcsolja be a váltás **a következőre: be.** 
1. Hozzon létre egy új Log Analytics munkaterületet, vagy csatoljon egy meglévőt. Győződjön meg arról, hogy be van jelölve a **nyers biztonsági adathozzáférés** beállítása beállítás. 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="Az Azure RTOS konfigurálása, amely az adatgyűjtési lehetőséget és a nyers biztonsági adatbeállításokat mutatja mind a kiválasztott":::
1. Válassza a **Mentés** lehetőséget
1. Térjen vissza az Azure-erőforrások listájához, és ellenőrizze, hogy a létrehozott vagy csatolt Log Analytics munkaterület engedélyezve van-e a IoT Hub.
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="Ellenőrizze az Azure-erőforrások listáját, és erősítse meg a megfelelő Log Analytics munkaterület hozzáadását IoT Hub"::: 

## <a name="next-steps"></a>Következő lépések

Folytassa a következő cikkel a megoldás konfigurálásának és testreszabásának befejezéséhez.

> [!div class="nextstepaction"]
> [Az Azure RTOS biztonsági moduljának konfigurálása](how-to-azure-rtos-security-module.md)
