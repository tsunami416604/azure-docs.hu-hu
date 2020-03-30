---
title: Az Azure IoT Hub frissítése | Microsoft dokumentumok
description: Módosítsa az IoT Hub díjszabási és méretezési szintjét, hogy több üzenetkezelési és eszközkezelési képesség hez jusson hozzá.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61440233"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Az IoT Hub frissítése

Az IoT-megoldás növekedésével az Azure IoT Hub készen áll a felskálázáselősegítésére. Az Azure IoT Hub két réteget kínál, alapszintű (B) és standard (S) a különböző funkciókat használni kívánó ügyfelek befogadására. Az egyes rétegeken belül három méret (1, 2 és 3), amelyek meghatározzák a naponta elküldhető üzenetek számát.

Ha több eszközzel rendelkezik, és további képességekre van szüksége, háromféleképpen állíthatja be az IoT hubot az igényeinek megfelelően:

* Egységek hozzáadása az IoT-központon belül. Például egy B1 IoT hub minden további egysége további 400 000 üzenetet tesz lehetővé naponta.

* Módosítsa az IoT hub méretét. Például migráljon a B1 szintről a B2 szintre az egyes egységek által naponta támogatott üzenetek számának növelése érdekében.

* Frissítsen magasabb szintre. Például frissítsen a B1 szintről az S1 szintre az azonos üzenetküldési kapacitással rendelkező speciális funkciók eléréséhez.

Ezek a módosítások a meglévő műveletek megszakítása nélkül is előfordulhatnak.

Ha azt szeretné, hogy az IoT hub, eltávolíthatja az egységeket, és csökkenti az IoT hub méretét, de nem lehet leminősítés alacsonyabb szintre. Például az S2 szintről az S1 szintre léphet, de az S2 szintről a B1 szintre nem. IoT Hub-on ként csak egy [iot Hub-kiadás](https://azure.microsoft.com/pricing/details/iot-hub/) típus választható ki egy rétegen belül. Létrehozhat például egy IoT Hubot több S1 egységgel, de nem különböző kiadásokból származó egységek kombinációjával, például S1 és B3, vagy S1 és S2.

Ezek a példák célja, hogy segítsen megérteni, hogyan módosíthatja az IoT hub, mint a megoldás változásait. Az egyes rétegek képességeivel kapcsolatos konkrét információkért mindig olvassa el az [Azure IoT Hub díjszabását.](https://azure.microsoft.com/pricing/details/iot-hub/)

## <a name="upgrade-your-existing-iot-hub"></a>A meglévő IoT-központ frissítése

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com/) és keresse meg az IoT-központot.

2. Válassza **a Díjszabás és méretezés**lehetőséget.

   ![Díjszabás és méretezés](./media/iot-hub-upgrade/pricing-scale.png)

3. A központi réteg módosításához válassza **a Díjszabási és méretezési szint lehetőséget.** Válassza ki az új réteget, majd kattintson **a gombra.**

   ![Tarifacsomag és méret](./media/iot-hub-upgrade/select-tier.png)

4. A hubegységeinek számának módosításához adjon meg egy új értéket az **IoT Hub-egységek**csoportban.

5. A módosítások mentéséhez válassza a **Mentés** gombot.

Az IoT hub most módosult, és a konfigurációk változatlanok maradnak.

Az alapszintű IoT Hub és a standard szintű IoT Hub maximális partíciókori korlátja 32. A legtöbb IoT Hubs csak 4 partíciót kell. A partíciókorlát az IoT Hub létrehozásakor kerül kiválasztásra, és az eszközről a felhőbe irányuló üzeneteket az üzenetek egyidejű olvasóinak számához kapcsolja. Ez az érték változatlan marad, amikor áttelepíti az alapszintű szintről a standard szintre.

## <a name="next-steps"></a>További lépések

További részletek a [megfelelő IoT Hub-szint kiválasztásáról.](iot-hub-scaling.md)