---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 3cdfa74c5f124e57b125399e39b7981b1dfb6d09
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43096363"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Testreszabhatja és kibővítheti az eszköz felügyeleti műveletei

Az IoT-megoldások eszközfelügyeleti minták meghatározott készletével kibontható vagy egyéni minták engedélyezése az ikereszköz és a felhőből az eszközre metódus primitívek használatával. Más eszközök felügyeleti műveletei közé tartoznak a gyári beállítások visszaállítása, belső vezérlőprogram frissítése, szoftverfrissítés, az energiagazdálkodás, hálózati és a kapcsolódási felügyeleti és adattitkosítás.

## <a name="device-maintenance-windows"></a>Eszköz karbantartási időszakok

Általában konfigurálnia eszközök, amelyek segítségével csökkentheti a megszakítások és állásidő egyszerre műveletek végrehajtásához. Eszköz karbantartási időszakok egy gyakran használt minta határozzák meg, amikor egy eszköz frissítenie kell annak konfigurációját. A háttér-megoldások az ikereszköz kívánt tulajdonságait segítségével határozza meg, és aktiválja a szabályzat az eszközön, amely lehetővé teszi, hogy a karbantartási időszak. Ha egy eszköz a karbantartási időszak házirendet kap, azt az ikereszköz jelentett tulajdonsága segítségével jelenteni a házirend állapotát. A háttéralkalmazás számára annak igazolását, az eszközök és az egyes szabályzatok megfelelőségi ikereszköz-lekérdezések felhasználhatja.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban használt közvetlen metódus a-trigger egy távoli újraindítása az eszközön. A jelentett tulajdonságok az eszköz legutóbbi újraindítás jelentésére használhatók, és az ikereszköz felderíteni a felhőből az eszköz legutóbbi újraindítás kérdezhető le.

Ismerkedés az IoT Hub és az eszközfelügyeleti minták például távolról keresztül a vezeték nélküli belső vezérlőprogram frissítését a folytatáshoz tekintse meg:

[Oktatóanyag: Hogyan belső vezérlőprogram frissítése](../articles/iot-hub/tutorial-firmware-update.md)

Ismerje meg, hogyan bővítheti az IoT-megoldás és az ütemezés metódus meghívja a több eszközre, tekintse meg a [feladatok ütemezése és szórása](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md) oktatóanyag.

Ismerkedés az IoT Hub a folytatáshoz tekintse meg a [Ismerkedés az IoT Edge szolgáltatással](../articles/iot-edge/tutorial-simulate-device-linux.md).