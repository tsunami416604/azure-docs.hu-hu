---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 69fdc6cf678107ef64ea1fe7b819738fd4a4ff4f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56741537"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Testreszabhatja és kibővítheti az eszköz felügyeleti műveletei

Az IoT-megoldások eszközfelügyeleti minták meghatározott készletével kibontható vagy egyéni minták engedélyezése az ikereszköz és a felhőből az eszközre metódus primitívek használatával. Más eszközök felügyeleti műveletei közé tartoznak a gyári beállítások visszaállítása, belső vezérlőprogram frissítése, szoftverfrissítés, az energiagazdálkodás, hálózati és a kapcsolódási felügyeleti és adattitkosítás.

## <a name="device-maintenance-windows"></a>Eszköz karbantartási időszakok

Általában konfigurálnia eszközök, amelyek segítségével csökkentheti a megszakítások és állásidő egyszerre műveletek végrehajtásához. Eszköz karbantartási időszakok egy gyakran használt minta határozzák meg, amikor egy eszköz frissítenie kell annak konfigurációját. A háttér-megoldások az ikereszköz kívánt tulajdonságait segítségével határozza meg, és aktiválja a szabályzat az eszközön, amely lehetővé teszi, hogy a karbantartási időszak. Ha egy eszköz a karbantartási időszak házirendet kap, azt az ikereszköz jelentett tulajdonsága segítségével jelenteni a házirend állapotát. A háttéralkalmazás számára annak igazolását, az eszközök és az egyes szabályzatok megfelelőségi ikereszköz-lekérdezések felhasználhatja.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban használt közvetlen metódus a-trigger egy távoli újraindítása az eszközön. A jelentett tulajdonságok az eszköz legutóbbi újraindítás jelentésére használhatók, és az ikereszköz felderíteni a felhőből az eszköz legutóbbi újraindítás kérdezhető le.

Ismerkedés az IoT Hub és az eszközfelügyeleti minták például távolról keresztül a vezeték nélküli belső vezérlőprogram frissítését a folytatáshoz tekintse meg a [hogyan belső vezérlőprogram frissítése](../articles/iot-hub/tutorial-firmware-update.md)

Ismerje meg, hogyan bővítheti az IoT-megoldás és az ütemezés metódus meghívja a több eszközre, tekintse meg [feladatok ütemezése és szórása](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).