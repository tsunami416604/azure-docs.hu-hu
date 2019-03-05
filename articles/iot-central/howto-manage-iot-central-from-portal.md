---
title: IoT-központ kezelése az Azure Portalról |} A Microsoft Docs
description: IoT-központ kezelése az Azure Portalon.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: f58d06cd191166f47d864241564bc57019b59132
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316574"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>IoT-központ kezelése az Azure Portalról

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Létrehozásához és IoT-központ alkalmazások kezelése – az IoT-központ [alkalmazáskezelő](https://aka.ms/iotcentral) , válassza a [az Azure portal](https://portal.azure.com) az alkalmazások kezeléséhez.

## <a name="create-iot-central-applications"></a>Alkalmazások létrehozása az IoT-központ

Hozzon létre egy alkalmazást, lépjen a [az Azure portal](https://ms.portal.azure.com) válassza **erőforrás létrehozása** a fő navigációs menüt a bal oldalon található.

![Felügyeleti portál: navigációs menü](media/howto-manage-iot-central-from-portal/image0.png)

Írja be a keresősávba, **IoT-központ**.

![Felügyeleti portál: keresés](media/howto-manage-iot-central-from-portal/image0a.png)

Válassza ki a **IoT központi alkalmazás** sortételt a keresési eredmények között.

![Felügyeleti portálon: a keresési eredmények a](media/howto-manage-iot-central-from-portal/image0b.png)

Most válassza ki **létrehozás**.

![Felügyeleti portál: IoT-központ erőforrás](media/howto-manage-iot-central-from-portal/image0c.png)

Töltse ki az űrlap összes mezőjét. Az űrlap az űrlap kitöltésekor az IoT-központ az alkalmazások létrehozásához hasonlít [alkalmazáskezelő](https://aka.ms/iotcentral) lapot. További információkért lásd: a [IoT központi alkalmazás létrehozása](quick-deploy-iot-central.md) rövid.

![Felügyeleti portál: IoT Central-erőforrás létrehozása](media/howto-manage-iot-central-from-portal/image1.png)  

Összes mező kitöltése, után válassza ki a **létrehozás**.

## <a name="manage-existing-iot-central-applications"></a>Meglévő IoT Central-alkalmazások kezelése

Ha már rendelkezik egy Azure IoT Central alkalmazáshoz, törölheti azt, vagy helyezze át egy másik előfizetést vagy az erőforrást csoporthoz az Azure Portalon.

> [!NOTE]
> Próbaverzió alkalmazások az Azure Portalon nem látható, mivel azok nem társít az előfizetéséhez.

Első lépésként válassza ki a **összes erőforrás** a fő navigációs menüt a bal oldalon található. A keresőmezőt használva írja be és az erőforrások listájában keresse meg az alkalmazás nevét. Ezután válassza ki a kezelni kívánt IoT Central alkalmazáshoz.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image2.png)

Keresse meg az alkalmazást, válassza ki az IoT központi alkalmazás URL-címe.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image3.png)

Az alkalmazás egy másik erőforráscsoportba való áthelyezéséhez jelölje ki **módosítása** mellett az erőforráscsoportot. Az a **erőforrások áthelyezése** lapon, válassza ki az erőforráscsoportot, amelyet szeretne áttelepíteni az alkalmazás.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image4.png)

Az alkalmazás másik előfizetésbe való áthelyezéséhez jelölje ki a **módosítása** hivatkozás az előfizetés mellett. Válassza ki az előfizetést, amelyre szeretné áttelepíteni a megjelenő párbeszédpanelen lévő alkalmazás.

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan kezelheti az Azure IoT Central alkalmazásait az Azure Portalról, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)