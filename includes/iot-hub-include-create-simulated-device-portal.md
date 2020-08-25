---
title: fájlbefoglalás
description: fájlbefoglalás
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "67179741"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Következő lépésként hozzon létre egy eszközidentitást, és mentse a hozzá tartozó kulcsot későbbi használatra. Ezt az eszközidentitást a szimulált alkalmazás használja az IoT Hubnak való üzenetküldésre. Ez a funkció nem érhető el a PowerShellben vagy Azure Resource Manager-sablon használatakor. Az alábbi lépésekből megtudhatja, hogyan hozhatja létre a szimulált eszközt a [Azure Portal](https://portal.azure.com)használatával.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjába.

2. Válassza az **erőforráscsoportok** lehetőséget, majd válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

3. Az erőforrások listájában válassza ki az IoT hubot. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja. Válassza ki az **IoT-eszközök** lehetőséget a Hub panelről.

4. Válassza a **+ Hozzáadás** lehetőséget. Az Eszköz hozzáadása panelen adja meg az eszköz azonosítóját. Ez az oktatóanyag a **Contoso-Test-Device** eszközt használja. Hagyja üresen a kulcsokat, majd jelölje be a **Kulcsok automatikus létrehozása** jelölőnégyzetet. Ellenőrizze, hogy az **Eszköz csatlakoztatása az IoT Hubhoz** engedélyezve van-e. Kattintson a **Mentés** gombra.

   ![Az eszköz hozzáadása képernyő](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Most, hogy létrejött, válassza ki az eszközt a generált kulcsok megtekintéséhez. Válassza ki a másolás ikont az elsődleges kulcson, és mentse el valahol, például a Jegyzettömbbe az oktatóanyag tesztelési fázisában.

   ![Az eszköz adatai, beleértve a kulcsokat](./media/iot-hub-include-create-simulated-device-portal/device-details.png)