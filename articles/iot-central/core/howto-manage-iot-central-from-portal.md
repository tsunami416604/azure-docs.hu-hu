---
title: Az IoT Central kezelése az Azure Portalról | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan hozhat létre és kezelhet ioT Central-alkalmazásokat az Azure Portalról.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: e7efda8efa27044168386e3ebbc557bf7fb74e8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157925"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Az IoT Central kezelése az Azure Portalról

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Az IoT Central-alkalmazások létrehozása és kezelése az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyén létrehozása és kezelése helyett az [Azure Portal](https://portal.azure.com) segítségével kezelheti alkalmazásait.

## <a name="create-iot-central-applications"></a>IoT Central-alkalmazások létrehozása

Alkalmazás létrehozásához keresse meg az [Azure Portalt,](https://ms.portal.azure.com) és válassza **az Erőforrás létrehozása lehetőséget.**

A **Piactér sávkeresése** mezőbe írja be az *IoT Central kifejezést:*

![Felügyeleti portál: keresés](media/howto-manage-iot-central-from-portal/image0a1.png)

Válassza ki az **IoT központi alkalmazás** csempéjét a keresési eredmények között:

![Felügyeleti portál: keresési eredmények](media/howto-manage-iot-central-from-portal/image0b1.png)

Most válassza a **Létrehozás lehetőséget:**

![Felügyeleti portál: IoT Central erőforrás](media/howto-manage-iot-central-from-portal/image0c1.png)

Töltse ki az űrlap összes mezőjét. Ez az űrlap hasonló ahhoz az űrlaphoz, amelyet az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyén alkalmazások létrehozásához tölt ki. További információkért tekintse meg az [IoT Central alkalmazás létrehozása](quick-deploy-iot-central.md) rövid útmutató.

![IoT Central űrlap létrehozása](media/howto-manage-iot-central-from-portal/image6a.png)

**A hely** az a [földrajzi hely,](https://azure.microsoft.com/global-infrastructure/geographies/) ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában azt a helyet kell kiválasztania, amely fizikailag a legközelebb van az eszközökhöz. Az Azure IoT Central jelenleg **Ausztrália,** **Ázsia csendes-óceáni**térsége, **Európa,** **Egyesült Államok**, **Egyesült Királyság**és **Japán** földrajzi helységén érhető el. Miután kiválasztotta a helyet, később nem helyezheti át az alkalmazást egy másik helyre.

Az összes mező kitöltése után válassza a **Létrehozás gombot.**

## <a name="manage-existing-iot-central-applications"></a>Meglévő IoT Central-alkalmazások kezelése

Ha már rendelkezik egy Azure IoT Central-alkalmazással, törölheti, vagy áthelyezheti egy másik előfizetésbe vagy erőforráscsoportba az Azure Portalon.

> [!NOTE]
> Az ingyenes díjszabási csomagban az Azure Portalon létrehozott alkalmazások at nem láthatja, mert nincsenek társítva az előfizetéshez.

Első lépésekhez válassza a Portál **Minden erőforrás a** lehetőséget. Jelölje be **a Rejtett típusok megjelenítése** jelölőnégyzetet, és kezdje el beírni az alkalmazás nevét a Név szerinti **szűrés** mezőbe a kereséshez. Ezután válassza ki a kezelni kívánt IoT Central alkalmazást.

Az alkalmazáshoz való navigáláshoz válassza az **IoT központi alkalmazás URL-címét:**

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image3.png)

Ha az alkalmazást egy másik erőforráscsoportba szeretné áthelyezni, válassza a **Módosítás** lehetőséget az erőforráscsoport mellett. Az **Erőforrások áthelyezése** lapon válassza ki azt az erőforráscsoportot, amelybe át szeretné helyezni az alkalmazást:

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image4a.png)

Ha az alkalmazást másik előfizetésbe szeretné áthelyezni, válassza a **Módosítás** lehetőséget az előfizetés mellett. Az **Erőforrások áthelyezése** lapon válassza ki azt az előfizetést, amelybe át szeretné helyezni az alkalmazást:

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az Azure IoT Central-alkalmazások azure-portálról történő kezelését, az alábbiakban a következő javasolt lépést ismertetem:

> [!div class="nextstepaction"]
> [Alkalmazás felügyelése](howto-administer.md)