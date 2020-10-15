---
title: Felügyeleti konzol telepítése az Azure Defender for IoT
description: Ismerje meg, hogyan helyezheti üzembe a felügyeleti konzolt az Azure Defender for IoT szolgáltatásban.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094417"
---
# <a name="deploy-the-management-console"></a>A felügyeleti konzol üzembe helyezése
Ez a cikk azt ismerteti, hogyan helyezhető üzembe az Azure Defender a IoT helyszíni felügyeleti konzolján.

## <a name="the-on-premises-management-console"></a>A helyszíni felügyeleti konzol

A helyszíni felügyeleti konzol az összes hálózati eszköz összevont nézetét biztosítja, és valós idejű áttekintést nyújt a legfontosabb IoT és az OT érintő kockázati mutatókról és riasztásokról az összes létesítményben. A SOC-munkafolyamatokkal szorosan integrálva és a könyvek futtatásával egyszerűen rangsorolhatja a kockázatcsökkentő tevékenységeket és a fenyegetések közötti összefüggéseket.

- Holisztikus – a bonyolultság csökkentése egyetlen egységesített platformmal az eszközkezelés, a kockázatkezelés és a sebezhetőségek kezelése terén, valamint a fenyegetések figyelése az incidensek megválaszolásával.

- Összesítés és korreláció – megjeleníti, összesíti és elemzi az összes helyről összegyűjtött adatokat és riasztásokat.

- Az összes érzékelő vezérlése – egyetlen helyről konfigurálhatja és figyelheti az összes érzékelőt.

   ![Azure Defender a IoT-hely felügyelet nézetéhez](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>A helyszíni felügyeleti konzol berendezésének üzembe helyezése

Ehhez a folyamathoz a saját hardver beszerzése és a szoftverek telepítése szükséges. A megoldás hitelesített készülékeken fut. A Certified berendezés megvásárlásakor tekintse meg az [Azure Defender for IoT hardveres specifikációs útmutatóját](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) .

Az ISO-rendszerkép letöltésével és az érzékelő szoftver telepítésével kapcsolatos információkért tekintse meg az [Azure Defender for IoT telepítési útmutatóját](https://aka.ms/AzureDefenderforIoTInstallSensorISO) .

**A helyszíni felügyeleti konzol üzembe helyezése:**

1. Navigáljon Microsoft Azure Defender for IoT.

2. Válassza **a helyszíni felügyeleti konzolt**.

   ![Azure Defender a IoT helyszíni felügyeleti konzol nézetéhez](media/updates/image15.png)

3. A **verzió kiválasztása** menüben válassza a 3,1-es verziót.

4. Válassza a **Letöltés** lehetőséget, és mentse a fájlt.

5. A szoftver telepítése után hajtsa végre a hálózat telepítési feladatait. A részletekért tekintse meg az [Azure Defender for IoT Network telepítési útmutatóját](https://aka.ms/AzureDefenderForIoTNetworkSetup) .

## <a name="next-steps"></a>Következő lépések

A konfigurációs beállításokkal kapcsolatos további információkért folytassa a modul konfigurálásának útmutatójában.
> [!div class="nextstepaction"]
> [Útmutató a modul konfigurálásához](./how-to-agent-configuration.md)
