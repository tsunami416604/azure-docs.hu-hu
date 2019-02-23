---
title: Az Azure IoT Hub frissítése |} A Microsoft Docs
description: Módosítsa a tarifacsomagot és méretet tarifacsomag IoT hubot, hogy további üzenetküldési és eszközfelügyeleti felügyeleti képességek beolvasása.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 6f6cf6de9b1f12451bf28a9bd7fc7077c1c6b1cd
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673458"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Az IoT hub frissítése

Az IoT-megoldás növekedésével Azure IoT Hub készen áll a vertikális felskálázás nyújt segítséget. Az Azure IoT Hub két szintet kínál, (B) alapszintű és a standard (S), befogadásához kívánó ügyfelek különböző funkciók használata. Az egyes szinteken belül vannak három méretben (1., 2, 3), amely naponta elküldött üzenetek számának meghatározásához. 

Ha több eszközt rendelkezik, és további funkciókat, három módja van az IoT hub igény szerint módosíthatja:

* Adja hozzá az egységeket az IoT hub-ban. Például minden további egység egy B1 IoT hub lehetővé teszi, hogy egy további napi 400 000 üzenet. 
* Az IoT hub méretének módosítása. Ha például át az B1 szint minden egység / nap által támogatott üzenetek számának növelése a B2 szintre.
* Váltson magasabb szintre. A speciális funkciókat biztosít az üzenetkezelési kapacitáson az S1 szintre, például frissítse a az B1 szint.

Ezek a változások meglévő műveletek megzavarása nélkül fordulhat.

Ha alacsonyabb szolgáltatásszintre váltásához az IoT hub szeretné, eltávolíthatja az egységeket, és az IoT hub méretének csökkentése azonban nem válthat alacsonyabb szintre. Például átválthat az S2 szint az S1 szintre, azonban nem az B1 szint, az S2 szint. Csak egyféle típusú [edition](https://azure.microsoft.com/pricing/details/iot-hub/) szinten belüli választható ki az IoT Hub száma. Létrehozhat például egy IoT hubot, több S1-egységet, de nem különböző kiadásait, például az S1 és B3, vagy S1 és S2 egységek vegyesen.

Ezek a példák segítségével megismerheti, hogyan állíthatja be az IoT hub, a megoldás módosítások hivatottak. Részletes információ az egyes szolgáltatásszintek lehetőségei, mindig hivatkozzon [Azure IoT Hub díjszabás](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>A meglévő IoT hub frissítése 

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) , és keresse meg az IoT hubot. 
2. Válassza ki **díjszabással**. 

   ![Díjszabás és méretezés](./media/iot-hub-upgrade/pricing-scale.png)

3. A réteg a hub módosításához válassza **díjszabással**. Válassza ki az új szintet, majd kattintson a **kiválasztása**.

   ![Tarifacsomag és méret](./media/iot-hub-upgrade/select-tier.png)

4. Ha módosítani szeretné az hub egységek számát, adja meg egy új értéket a **IoT Hub-egységek**. 
5. Válassza ki **mentése** a módosítások mentéséhez. 

Az IoT hub most módosul, és a beállítások nem változnak. A partíciók maximális korlátot, az alapszintű csomag az IoT Hub 8 és a standard csomag az 32. A legtöbb IoT-központok csak négy partíciót kell. A partíciós korlát akkor kell kiválasztani, amikor az IoT Hub jön létre, és az eszköz – felhő üzeneteket vonatkozik, az ezeket az üzeneteket az egyidejű olvasók. Ez az érték az alapszintű csomag a standard szintű csomag áttelepítésekor változatlan marad. 

## <a name="next-steps"></a>További lépések

További részletek [a megfelelő IoT Hub-csomag kiválasztása](iot-hub-scaling.md). 

