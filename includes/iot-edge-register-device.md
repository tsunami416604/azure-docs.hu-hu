---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/25/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: bacafdc8f7fd8e206335f3be0a086df1c54f1081
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263616"
---
Hozzon létre egy eszközidentitást a szimulált eszközhöz, hogy az kommunikálhasson az IoT Hubbal. Mivel az IoT Edge-eszközök másként viselkednek, mint a hagyományos IoT-eszközök, és kezelésük is másként történik, ezért IoT Edge-eszközként kell deklarálni a kezdetektől fogva. 

1. Az Azure Portalon keresse meg az IoT-központot.
1. Válassza az **IoT Edge**, majd az **Add IoT Edge Device** (IoT Edge-eszköz hozzáadása) lehetőséget.

   ![IoT Edge-eszköz hozzáadása](./media/iot-edge-register-device/add-device.png)

1. Adjon meg egy egyedi eszközazonosítót szimulált eszközének.
1. Az eszköz hozzáadásához kattintson a **Save** (Mentés) gombra.
1. Válassza ki új eszközét az eszközök listájából.
1. Másolja és mentse a **kapcsolati sztring elsődleges kulcsának** értékét. Erre az értékre a következő szakaszban, az IoT Edge-futtatókörnyezet konfigurálásához lesz szükség. 

