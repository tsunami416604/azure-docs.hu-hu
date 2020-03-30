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
ms.openlocfilehash: 2514325c93f0f72aa979f871e86e465093479402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69558781"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Az eszközkezelési műveletek testreszabása és kiterjesztése

Az IoT-megoldások kiterjeszthetik az eszközfelügyeleti minták meghatározott készletét, vagy engedélyezhetik az egyéni mintákat az iker- és felhőalapú metódusprimitívek használatával. Az eszközkezelési műveletek közé tartozik még a gyári beállítások visszaállítása, a belső vezérlőprogram frissítése, a szoftverfrissítés, az energiagazdálkodás, a hálózat- és kapcsolatkezelés, valamint az adattitkosítás.

## <a name="device-maintenance-windows"></a>Eszközkarbantartási ablakok

Az eszközöket általában úgy kell beállítani, hogy olyan műveleteket hajtsanak végre, amelyek minimálisra csökkentik a megszakításokat és az állásidőt. Az eszközkarbantartási időszakok egy gyakran használt minta, amely meghatározza, hogy az eszköznek mikor kell frissítenie a konfigurációját. A háttérmegoldások az ikereszköz kívánt tulajdonságaival határozhatják meg és aktiválhatják az eszközön lévő olyan házirendet, amely lehetővé teszi a karbantartási időszakot. Amikor egy eszköz megkapja a karbantartási időszak házirendjét, használhatja az ikereszköz jelentett tulajdonságát a házirend állapotának jelentéséhez. A háttéralkalmazás ezután az eszközök és az egyes szabályzatok megfelelőségének teszteléséhez használhatja az ikereszközök lekérdezéseit.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy közvetlen módszert használt egy távoli újraindítás indításához egy eszközön. A jelentett tulajdonságok segítségével jelentette az eszköz utolsó újraindítási idejét, és lekérdezte az ikereszközt, hogy felderítse az eszköz utolsó újraindítási idejét a felhőből.

Az IoT Hub és az eszközfelügyeleti minták , például a távoli belső vezérlőprogram frissítésének megkezdéséhez olvassa el [a Belső vezérlőprogram frissítésének lépéseit.](../articles/iot-hub/tutorial-firmware-update.md)

Ha meg szeretné tudni, hogyan bővítheti az IoT-megoldást, és hogyan ütemezheti a metódushívásokat több eszközön, olvassa [el a Feladatok ütemezése és közvetítése című témakört.](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md)