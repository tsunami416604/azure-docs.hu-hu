---
title: IoT Central kezelése a Azure Portalból | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet IoT Central-alkalmazásokat a Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 27517c375265b552d2e1dec4d8c167d1bc86549d
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137656"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>IoT Central kezelése a Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

IoT Central alkalmazások az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén való létrehozása és kezelése helyett a [Azure Portal](https://portal.azure.com) használatával kezelheti az alkalmazásokat.

## <a name="create-iot-central-applications"></a>IoT Central-alkalmazások létrehozása

Alkalmazás létrehozásához navigáljon a [Azure Portal](https://ms.portal.azure.com) , és válassza az **erőforrás létrehozása**lehetőséget.

A **Keresés a piactéren** sávon írja be *IoT Central*:

![Felügyeleti portál: keresés](media/howto-manage-iot-central-from-portal/image0a1.png)

Válassza ki a **IoT Central alkalmazás** csempét a keresési eredmények között:

![Felügyeleti portál: keresési eredmények](media/howto-manage-iot-central-from-portal/image0b1.png)

Most válassza a **Létrehozás**elemet:

![Felügyeleti portál: IoT Central erőforrás](media/howto-manage-iot-central-from-portal/image0c1.png)

Töltse ki az űrlap összes mezőjét. Ez az űrlap hasonló az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén az alkalmazások létrehozásához kitöltött űrlaphoz. További információt az [IoT Central-alkalmazás létrehozása](quick-deploy-iot-central.md) című témakörben talál.

![IoT Central űrlap létrehozása](media/howto-manage-iot-central-from-portal/image6a.png)

A hely az a [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) **hely** , ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában ki kell választania az eszközökhöz legközelebb eső helyet. Az Azure IoT Central jelenleg a **Egyesült Államok**, **Ausztráliában**, **Ázsia és a csendes-óceáni térség**vagy **Európában**érhető el.  A hely kiválasztása után később nem helyezheti át az alkalmazást egy másik helyre.


Az összes mező kitöltése után válassza a **Létrehozás**lehetőséget.

## <a name="manage-existing-iot-central-applications"></a>Meglévő IoT Central alkalmazások kezelése

Ha már rendelkezik Azure IoT Central-alkalmazással, törölheti, vagy áthelyezheti egy másik előfizetésre vagy erőforráscsoporthoz a Azure Portal.

> [!NOTE]
> A Azure Portal ingyenes díjszabási csomagjában létrehozott alkalmazások nem láthatók, mert nincsenek társítva az előfizetéséhez.

Első lépésként válassza ki az **összes erőforrást** a portálon. Válassza a **rejtett típusok megjelenítése** lehetőséget, és kezdje el begépelni az alkalmazás nevét a **szűrés név alapján** , hogy megkeresse. Ezután válassza ki azt a IoT Central alkalmazást, amelyet kezelni szeretne.

Az alkalmazás eléréséhez válassza ki a **IoT Central alkalmazás URL-címét**:

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image3.png)

Az alkalmazás más erőforráscsoporthoz való áthelyezéséhez válassza az erőforráscsoport melletti **módosítás** lehetőséget. Az **erőforrások áthelyezése** lapon válassza ki azt az erőforráscsoportot, amelyre át szeretné helyezni az alkalmazást:

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image4a.png)

Ha át szeretné helyezni az alkalmazást egy másik előfizetésre, válassza az előfizetés melletti **módosítás** lehetőséget. Az **erőforrások áthelyezése** lapon válassza ki azt az előfizetést, amelyre át szeretné helyezni az alkalmazást:

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan kezelheti az Azure IoT Central-alkalmazásokat a Azure Portalból, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)