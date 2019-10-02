---
title: IoT Central kezelése a Azure Portalból | Microsoft Docs
description: A Azure Portal IoT Central kezelése.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: a8a2ff5e98948030609328a3de33399ede154a3d
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815692"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>IoT Central kezelése a Azure Portal

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

IoT Central alkalmazások az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén való létrehozása és kezelése helyett a [Azure Portal](https://portal.azure.com) használatával kezelheti az alkalmazásokat.

## <a name="create-iot-central-applications"></a>IoT Central-alkalmazások létrehozása

Alkalmazás létrehozásához navigáljon a [Azure Portal](https://ms.portal.azure.com) , majd válassza az **erőforrás létrehozása** lehetőséget a bal oldali fő navigációs menüben.

![Felügyeleti portál: NAV menü](media/howto-manage-iot-central-from-portal/image0.png)

A keresősáv mezőbe írja be a következőt: **IoT Central**.

![Felügyeleti portál: keresés](media/howto-manage-iot-central-from-portal/image0a1.png)

Válassza ki a **IoT Central Application** line-Item elemet a keresési eredmények között.

![Felügyeleti portál: keresési eredmények](media/howto-manage-iot-central-from-portal/image0b1.png)

Most válassza a **Létrehozás**lehetőséget.

![Felügyeleti portál: Erőforrás IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Töltse ki az űrlap összes mezőjét. Ez az űrlap hasonló az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén az alkalmazások létrehozásához kitöltött űrlaphoz. További információt az [IoT Central-alkalmazás létrehozása](quick-deploy-iot-central.md) című témakörben talál.

A **hely** a fizikai hely vagy [Földrajz](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában ki kell választania az eszközökhöz legközelebb eső helyet. Megtekintheti azokat a régiókat, amelyekben az Azure IoT Central elérhető a régiók oldalon [elérhető termékek területen](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) . A hely kiválasztása után később nem helyezheti át az alkalmazást egy másik helyre.

> [!NOTE]
> Az **előzetes verziójú alkalmazás** sablonja jelenleg csak az **észak-európai** és az **USA középső** régiójában érhető el.

![Felügyeleti portál: IoT Central erőforrás létrehozása](media/howto-manage-iot-central-from-portal/image1a.png)  

Az összes mező kitöltése után válassza a **Létrehozás**lehetőséget.

## <a name="manage-existing-iot-central-applications"></a>Meglévő IoT Central alkalmazások kezelése

Ha már rendelkezik Azure IoT Central-alkalmazással, törölheti, vagy áthelyezheti egy másik előfizetésre vagy erőforráscsoporthoz a Azure Portal.

> [!NOTE]
> A próbaverziós alkalmazások nem jelennek meg a Azure Portalban, mert nincsenek társítva az előfizetéséhez.

Első lépésként válassza a bal oldali fő navigációs menü **összes erőforrás** elemét. A keresőmezőbe írja be az alkalmazás nevét, és keresse meg az erőforrások listájában. Ezután válassza ki azt a IoT Central alkalmazást, amelyet kezelni szeretne.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image2a.png)

Az alkalmazás eléréséhez válassza ki a IoT Central alkalmazás URL-címét.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image3.png)

Az alkalmazás más erőforráscsoporthoz való áthelyezéséhez válassza az erőforráscsoport melletti **módosítás** lehetőséget. Az **erőforrások áthelyezése** lapon válassza ki azt az erőforráscsoportot, amelyre át szeretné telepíteni az alkalmazást.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image4a.png)

Ha át szeretné helyezni az alkalmazást egy másik előfizetésre, válassza a **módosítás** hivatkozást az előfizetés mellett. Válassza ki azt az előfizetést, amelyre át szeretné telepíteni az alkalmazást a megjelenő párbeszédpanelen.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan kezelheti az Azure IoT Central-alkalmazásokat a Azure Portalból, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)