---
title: "Ismerkedés az Azure IoT peremhálózati (Windows) |} Microsoft Docs"
description: "Megtudhatja, hogyan lehet létrehozni az Azure IoT peremhálózati átjáró Windows-gépen, és alapfogalmakat például modulok és a JSON-konfigurációs fájlokat az Azure IoT Edge megismerése."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>A Windows Azure IoT peremhálózati architektúra felfedezés

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>A minta futtatásához

A **build.cmd** parancsfájlt hoz létre a kimenetét a **build** mappa a helyi példánya a **iot-edge** tárház. A kimeneti sok fájlt tartalmaz, de ez a minta három koncentrál:
- Két IoT peremhálózati modulok: **build\\modulok\\naplózó\\Debug\\logger.dll** és **build\\modulok\\hello_world\\ Debug\\hello\_world.dll**. 
- Egy végrehajtható fájl: **build\\minták\\hello\_globális\\Debug\\hello\_globális\_sample.exe**. Ez a folyamat egy JSON-konfigurációs fájl parancssori argumentumként vesz igénybe.

A negyedik fájlt, amely ezt a mintát használ a build mappában nem, de ha iot-edge tárház klónozása azt:
- A JSON-konfigurációs fájlt: **minták\\hello\_globális\\src\\hello\_globális\_win.json**. A fájl elérési útját a két modulokat tartalmaz. Azt is deklarálja, ahol a logger.dll a kimenetet a írja. Az alapértelmezett érték **log.txt** az aktuális munkakönyvtárban a. 

   >[!NOTE]
   >Helyezze át a mintavételi modulok, vagy adja hozzá a saját teszteket, frissíteni a **module.path** a konfigurációs fájlban a megfelelő értékeket. A modul elérési útvonalai képest a könyvtár ahol **hello\_globális\_sample.exe** található. 

A minta futtatásához kövesse az alábbi lépéseket:

1. Keresse meg a **build** mappa gyökérkönyvtárában található a helyi másolat készítése a **iot-edge** tárház.

1. Futtassa az alábbi parancsot:

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. A következő kimeneti azt jelenti, hogy a minta sikeresen fut:

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. Nyomja meg a **Enter** kulcs leállítja a folyamatot.


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
