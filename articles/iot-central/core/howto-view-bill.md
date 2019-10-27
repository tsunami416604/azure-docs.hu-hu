---
title: A számla megtekintése és a próbaverzió konvertálása az Azure IoT Central alkalmazásban való befizetésre való váltáshoz | Microsoft Docs
description: Rendszergazdaként megtudhatja, hogyan tekintheti meg a számlát, és hogyan használhatja a próbaverziót az Azure IoT Central-alkalmazásban való fizetésre
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 16a58bfc3fa245ed1ede19b0439419ab4590234e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952575"
---
# <a name="view-your-bill-in-iot-central-application"></a>Számla megtekintése IoT Central alkalmazásban

Ez a cikk azt ismerteti, hogyan lehet rendszergazdaként megtekinteni a számlát az Azure IoT Central alkalmazásban az adminisztráció szakaszban, valamint azt is, hogyan alakíthatja át a próbaverzióját utólagos elszámolású verzióra.

Az **Adminisztráció** szakasz eléréséhez és használatához **rendszergazdai** szerepkörrel kell rendelkeznie egy Azure IoT Central-alkalmazáshoz. Ha létrehoz egy Azure IoT Central alkalmazást, a rendszer automatikusan hozzárendeli az adott alkalmazáshoz tartozó **rendszergazdai** szerepkörhöz.

## <a name="view-your-bill"></a>Számla megtekintése

A számla megtekintéséhez lépjen a **számlázás** lapra az **Adminisztráció** szakaszban. Megnyílik az Azure számlázási oldal egy új lapon, ahol megtekintheti az egyes Azure IoT Central-alkalmazások számláit.

## <a name="convert-your-trial-to-pay-as-you-go"></a>Próbaverzió átalakítása utólagos elszámolásra

- A **próbaverziós** alkalmazások érvényessége hét napig ingyenes. A lejárat előtt bármikor átalakíthatók használatalapú fizetéses alkalmazássá.
- Az **utólagos elszámolású alkalmazások** számlázása eszközönként történik, és az első öt eszköz díjmentes.

További információk a díjszabásról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/) találhatók.

A számlázási szakaszban elvégezheti a próbaverziós alkalmazások átváltását.

Az önkiszolgáló folyamat végrehajtásához kövesse az alábbi lépéseket:

1. Lépjen a **számlázás** lapra az **Adminisztráció** szakaszban.

    ![Próbaverzió állapota](media/howto-administer/freetrialbilling.png)

1. Válassza **az átalakítás**utólagos elszámolásra lehetőséget.

    ![Próbaverzió konvertálása](media/howto-administer/convert.png)

1. Válassza ki a megfelelő Azure Active Directory, majd az Azure-előfizetést, amelyet az utólagos elszámolású alkalmazáshoz szeretne használni.

1. Miután kiválasztotta az **átalakítás**lehetőséget, az alkalmazás mostantól utólagos elszámolású alkalmazás, és elkezdődik a számlázás.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan tekintheti meg a számláját az Azure IoT Central alkalmazásban, a javasolt következő lépés az [alkalmazás felhasználói felületének testreszabása](howto-customize-ui.md) az Azure IoT Central-ban című témakörben olvashat.