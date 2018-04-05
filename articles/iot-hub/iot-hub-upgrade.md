---
title: Az Azure IoT-központ frissítése |} Microsoft Docs
description: Módosítsa a tarifa- és méretcsomag IoT hub több üzenetkezelési és az eszköz felügyeleti lehetőségek elérése.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: d383d26b406c012b6b76225faf89f4b5dbd6bb9c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-upgrade-your-iot-hub"></a>Az IoT-központ frissítése

Az IoT-megoldásból növekedésével Azure IoT Hub készen áll a vertikális felskálázás nyújt segítséget. Az Azure IoT Hub kínál a két réteg, basic (B) és a standard (S) olyan ügyfelek számára, szeretné használni a különböző szolgáltatásokat. Minden egyes réteg van három méretben kaphatók (1., 2, 3), amelyek meghatározzák, naponta elküldött üzenetek száma. 

Ha több eszközt, és további képességeket kell, három módon úgy, hogy az IoT hub az igényeinek:

* Adja hozzá a egység az IoT hub belül. Például minden további egységhez egy B1 IoT-központ lehetővé teszi 400 000 üzenet naponta. 
* Az IoT hub méretének módosításához. Például telepíthet át a B1 rétegtől a K2 szintet növelje meg az üzenetek támogató tárolóegységekhez / nap.
* Váltson egy magasabb szintű csomagra. Például váltson a B1 rétegtől azonos üzenetkezelési képességét, de a normál rétegben, a speciális szolgáltatások S1 csomagra.

Ezek a változások meglévő műveletek megszakítása nélkül fordulhat.

Ha szeretné használni az IoT hub, távolítsa el az egységet, és az IoT hub méretének csökkentése. Azonban Ön nem visszaminősítését alacsonyabb réteghez. Például áthelyezheti a S2 rétegtől S1 csomagra, de nem a S2 réteg B1 csomagra. 

Ezek a példák segítségével megtudhatja, hogyan lehet beállítani az IoT hub a megoldás módosítás célja. Szeretne többet megtudni az egyes szolgáltatásszintek lehetőségei mindig tekintse át [Azure IoT Hub árképzési](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>A meglévő IoT-központ frissítése 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) , és keresse meg az IoT hub. 
2. Válassza ki **árak és a skála**. 

   ![Díjszabás és méretezés](./media/iot-hub-upgrade/pricing-scale.png)

3. A réteg a központ módosításához válassza **árazás és méretcsomag**. Válassza ki az új réteget, majd kattintson a **válasszon**.

   ![Díjszabás és méretezés](./media/iot-hub-upgrade/select-tier.png)

4. A központban egységek számának módosításához adjon meg egy új értéket a **IoT Hub-egységek**. 
5. Válassza ki **mentése** menti a módosításokat. 

Az IoT hub most módosul, és a beállítások nem változnak. 

## <a name="next-steps"></a>További lépések

További részletek [kiválasztása a megfelelő IoT-központ réteg](iot-hub-scaling.md). 

