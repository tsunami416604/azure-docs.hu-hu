---
title: Az Azure IoT Hub frissítése |} A Microsoft Docs
description: Módosítsa a tarifacsomagot és méretet tarifacsomag IoT hubot, hogy további üzenetküldési és eszközfelügyeleti felügyeleti képességek beolvasása.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: 1f60b7d30c073c49d5e0a7d35e7263c2181ed744
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903064"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Az IoT hub frissítése

Az IoT-megoldás növekedésével Azure IoT Hub készen áll a vertikális felskálázás nyújt segítséget. Az Azure IoT Hub két szintet kínál, (B) alapszintű és a standard (S), befogadásához kívánó ügyfelek különböző funkciók használata. Az egyes szinteken belül vannak három méretben (1., 2, 3), amely naponta elküldött üzenetek számának meghatározásához. 

Ha több eszközt rendelkezik, és további funkciókat, három módja van az IoT hub igény szerint módosíthatja:

* Adja hozzá az egységeket az IoT hub-ban. Például minden további egység egy B1 IoT hub lehetővé teszi, hogy egy további napi 400 000 üzenet. 
* Az IoT hub méretének módosítása. Például át az B1 szint minden egység / nap által támogatott üzenetek növeléséhez a B2 szintre.
* Váltson magasabb szintre. Az S1 szintre, az üzenetkezelési kapacitáson, de a standard szintű csomag olyan fejlett szolgáltatásokat, például frissítse a az B1 szint.

Ezek a változások meglévő műveletek megzavarása nélkül fordulhat.

Ha alacsonyabb szolgáltatásszintre váltásához az IoT hub, egységek eltávolítása, és az IoT hub méretének csökkentésére. Azonban nem léphet vissza egy alacsonyabb szintre. Például átválthat az S2 szint az S1 szintre, azonban nem az B1 szint, az S2 szint. 

Ezek a példák segítségével megismerheti, hogyan állíthatja be az IoT hub, a megoldás módosítások hivatottak. Részletes információ az egyes szolgáltatásszintek lehetőségei mindig hivatkozzon [Azure IoT Hub díjszabás](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>A meglévő IoT hub frissítése 

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) , és keresse meg az IoT hubot. 
2. Válassza ki **díjszabással**. 

   ![Díjszabás és méretezés](./media/iot-hub-upgrade/pricing-scale.png)

3. A réteg a hub módosításához válassza **díjszabással**. Válassza ki az új szintet, majd kattintson a **kiválasztása**.

   ![Díjszabás és méretezés](./media/iot-hub-upgrade/select-tier.png)

4. Ha módosítani szeretné az hub egységek számát, adja meg egy új értéket a **IoT Hub-egységek**. 
5. Válassza ki **mentése** a módosítások mentéséhez. 

Az IoT hub most módosul, és a beállítások nem változnak. Vegye figyelembe, hogy a partíció korlátot, az alapszintű díjcsomag az IoT Hub 8. Ez a korlátozás változatlan marad, az alapszintű csomag a standard szintű csomag áttelepítésekor.

## <a name="next-steps"></a>További lépések

További részletek [a megfelelő IoT Hub-csomag kiválasztása](iot-hub-scaling.md). 

