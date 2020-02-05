---
title: IoT Central kezelése a Azure Portalból | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet IoT Central-alkalmazásokat a Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1e206d9a1b14fc8ff1b92e4eb7217824d81212cf
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990145"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>IoT Central kezelése a Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

IoT Central alkalmazások az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén való létrehozása és kezelése helyett a [Azure Portal](https://portal.azure.com) használatával kezelheti az alkalmazásokat.

## <a name="create-iot-central-applications"></a>IoT Central-alkalmazások létrehozása

Alkalmazás létrehozásához navigáljon a [Azure Portal](https://ms.portal.azure.com) , majd válassza az **erőforrás létrehozása** lehetőséget a bal oldali fő ablaktáblán.

![Felügyeleti portál: NAV menü](media/howto-manage-iot-central-from-portal/image0.png)

A keresősáv mezőbe írja be a következőt: **IoT Central**.

![Felügyeleti portál: keresés](media/howto-manage-iot-central-from-portal/image0a1.png)

Válassza ki a **IoT Central Application** line-Item elemet a keresési eredmények között.

![Felügyeleti portál: keresési eredmények](media/howto-manage-iot-central-from-portal/image0b1.png)

Most válassza a **Létrehozás**lehetőséget.

![Felügyeleti portál: IoT Central erőforrás](media/howto-manage-iot-central-from-portal/image0c1.png)

Töltse ki az űrlap összes mezőjét. Ez az űrlap hasonló az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén az alkalmazások létrehozásához kitöltött űrlaphoz. További információt az [IoT Central-alkalmazás létrehozása](quick-deploy-iot-central.md) című témakörben talál.

Az általánosan elérhető szolgáltatásokkal IoT Central alkalmazást az **örökölt alkalmazás** sablonként való kiválasztásával hozhatja létre, és az összes többi alkalmazás-sablon nyilvános előzetes verziójú funkciókat is használ.

![IoT Central űrlap létrehozása](media/howto-manage-iot-central-from-portal/image6a.png)

A hely az a [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) **hely** , ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában ki kell választania az eszközökhöz legközelebb eső helyet. Az Azure IoT Central jelenleg a **Egyesült Államok**, **Ausztráliában**, **Ázsia és a csendes-óceáni térség**vagy **Európában**érhető el.  A hely kiválasztása után később nem helyezheti át az alkalmazást egy másik helyre.

> [!NOTE]
> Az előzetes verziójú alkalmazás sablonjai jelenleg csak az **Európa** és **Egyesült Államok** helyen érhetők el.


Az összes mező kitöltése után válassza a **Létrehozás**lehetőséget.

## <a name="manage-existing-iot-central-applications"></a>Meglévő IoT Central alkalmazások kezelése

Ha már rendelkezik Azure IoT Central-alkalmazással, törölheti, vagy áthelyezheti egy másik előfizetésre vagy erőforráscsoporthoz a Azure Portal.

> [!NOTE]
> A Azure Portal ingyenes díjszabási csomagjában létrehozott alkalmazások nem láthatók, mert nincsenek társítva az előfizetéséhez.

Első lépésként válassza a bal oldali fő ablaktáblán a **minden erőforrás** elemet. A keresőmezőbe írja be az alkalmazás nevét, és keresse meg az erőforrások listájában. Ezután válassza ki azt a IoT Central alkalmazást, amelyet kezelni szeretne.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image2a.png)

Az alkalmazás eléréséhez válassza ki a IoT Central alkalmazás URL-címét.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image3.png)

Az alkalmazás más erőforráscsoporthoz való áthelyezéséhez válassza az erőforráscsoport melletti **módosítás** lehetőséget. Az **erőforrások áthelyezése** lapon válassza ki azt az erőforráscsoportot, amelyre át szeretné telepíteni az alkalmazást.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image4a.png)

Ha át szeretné helyezni az alkalmazást egy másik előfizetésre, válassza a **módosítás** hivatkozást az előfizetés mellett. Válassza ki azt az előfizetést, amelyre át szeretné telepíteni az alkalmazást a megjelenő párbeszédpanelen.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan kezelheti az Azure IoT Central-alkalmazásokat a Azure Portalból, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)