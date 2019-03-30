---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670982"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Következő lépésként hozzon létre egy eszközidentitást, és mentse a hozzá tartozó kulcsot későbbi használatra. Ezt az eszközidentitást a szimulált alkalmazás használja az IoT Hubnak való üzenetküldésre. Ez a funkció esetén nem érhető el a PowerShell vagy egy Azure Resource Manager-sablon használatával. Az alábbi lépéseket, hogyan hozhat létre a szimulált eszköz használatával a [az Azure portal](https://portal.azure.com).

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjába.

2. Válassza ki **erőforráscsoportok** majd válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

3. Az erőforrások listájában válassza ki az IoT hubnak. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja. Válassza ki az **IoT-eszközök** lehetőséget a Hub panelről.

4. Válassza a **+ Hozzáadás** lehetőséget. Az Eszköz hozzáadása panelen adja meg az eszköz azonosítóját. Ez az oktatóanyag a **Contoso-Test-Device** eszközt használja. Hagyja üresen a kulcsokat, majd jelölje be a **Kulcsok automatikus létrehozása** jelölőnégyzetet. Ellenőrizze, hogy az **Eszköz csatlakoztatása az IoT Hubhoz** engedélyezve van-e. Kattintson a **Mentés** gombra.

   ![Az add-eszköz képernyője](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Most, hogy elkészült, válassza ki az eszköz számára a generált kulcsok megtekintéséhez. A másolási ikonra az elsődleges kulcsra, és a tesztelési folyamatba, a jelen oktatóanyag esetében például a Jegyzettömbben mentse valahova.

   ![Az eszköz adatait, például a kulcsok](./media/iot-hub-include-create-simulated-device-portal/device-details.png)