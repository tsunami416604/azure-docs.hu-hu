---
title: IoT-központ kezelése az Azure Portalról |} A Microsoft Docs
description: IoT-központ kezelése az Azure Portalon.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2018
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 73c8d64c53e23601b19a775a07b0b630c675b91f
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200468"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>IoT-központ kezelése az Azure Portalról 
Létrehozása és kezelése – IoT Central-alkalmazást az IoT Central webhelye kívül is kezelheti IoT-központ az Azure Portalról. Ez a cikk a lehetőségeket, és megtudhatja, hogyan teheti vezeti végig.

## <a name="create-iot-central-applications"></a>Alkalmazások létrehozása az IoT-központ
Hozzon létre egy új alkalmazást, lépjen a [az Azure portal](https://ms.portal.azure.com) , és kattintson az "Erőforrás létrehozása" lehetőségre a fő navigációs menüt a bal oldalon. 

![Felügyeleti portál: navigációs menü](media/howto-manage-iot-central-from-portal/image0.png)

Keresés sáv írja be az "IoT Central" kifejezést.

![Felügyeleti portál: keresés](media/howto-manage-iot-central-from-portal/image0a.png)

Kattintson a keresési eredmények IoT központi alkalmazás-sortételt.

![Felügyeleti portálon: a keresési eredmények a](media/howto-manage-iot-central-from-portal/image0b.png)

Most kattintson a "Létrehozás" gombra a képernyő kitöltéséhez kell megtekintéséhez.

![Felügyeleti portál: IoT-központ erőforrás](media/howto-manage-iot-central-from-portal/image0c.png)

Töltse ki az űrlap összes mezőjét. Az űrlap kell alkalmazásokat hozhat létre az IoT-központ webhelyről töltse ki ezt a kérdőívet hasonlít. Minden mező kitöltésére kapcsolatos további információkért tekintse meg a [IoT központi alkalmazás létrehozása](quick-deploy-iot-central.md) rövid. 

![Felügyeleti portál: IoT Central-erőforrás létrehozása](media/howto-manage-iot-central-from-portal/image1.png)  

Összes mező kitöltése, után a "Létrehozás" gombra.

## <a name="manage-existing-iot-central-applications"></a>Meglévő IoT Central-alkalmazások kezelése
Ha már rendelkezik egy Azure IoT Central alkalmazáshoz törölheti, áthelyezése egy másik előfizetést vagy az erőforrást csoporthoz az Azure Portalon. Próbaverzió alkalmazások az Azure Portalon nem látható, mivel nincs előfizetés biztonsági mentést készít, ezeket a kísérleteket.

A kezdéshez kattintson a "Minden erőforrás" a fő navigációs menüt a bal oldalon található. Használja a Keresés mezőbe írja be az alkalmazás nevét, és keresse meg azt az erőforrások listájában. Ezután kattintson az IoT-központ alkalmazás, amelyet kezelni szeretne.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image2.png)

Keresse meg az alkalmazást, kattintson az IoT központi alkalmazás URL-címe.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image3.png)

Az alkalmazás egy másik erőforráscsoportba helyezni, kattintson a **módosítása** hivatkozást az erőforráscsoport mellett. Válassza ki az erőforráscsoportot, amelyre szeretné áttelepíteni a megjelenő párbeszédpanelen lévő alkalmazás.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image4.png)

Az alkalmazás egy másik előfizetésbe áthelyezni, kattintson a **módosítása** hivatkozás az előfizetés mellett. Válassza ki az előfizetést, amelyre szeretné áttelepíteni a megjelenő párbeszédpanelen lévő alkalmazás.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT Central alkalmazások felügyelete az Azure Portalról, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)