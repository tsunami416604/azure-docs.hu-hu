---
title: A számla kezelése és a próbaverzió átalakítása az Azure IoT Central alkalmazásban való kifizetéshez | Microsoft Docs
description: Rendszergazdaként megtudhatja, hogyan kezelheti számláját, és hogyan használhatja a próbaverziót az Azure IoT Central-alkalmazásban való fizetésre
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 11bcdaab34cf4d82b688a578ab2d9ee686ffe07e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894811"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Számla kezelése IoT Central alkalmazásban

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez a cikk azt ismerteti, hogyan kezelheti a számlát az Azure IoT Central alkalmazásban az adminisztráció szakaszban, valamint azt is, hogyan alakíthatja át a próbaverzióját az utólagos elszámolású verzióra.

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

    ![Próbaverzió állapota](media/howto-view-bill/freetrialbilling.png)

1. Válassza **az átalakítás**utólagos elszámolásra lehetőséget.

    ![Próbaverzió konvertálása](media/howto-view-bill/convert.png)

1. Válassza ki a megfelelő Azure Active Directory, majd az Azure-előfizetést, amelyet az utólagos elszámolású alkalmazáshoz szeretne használni.

1. Miután kiválasztotta az **átalakítás**lehetőséget, az alkalmazás mostantól utólagos elszámolású alkalmazás, és elkezdődik a számlázás.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan kezelheti a számláját az Azure IoT Central alkalmazásban, a javasolt következő lépés az [alkalmazás felhasználói felületének testreszabása](howto-customize-ui.md) az Azure IoT Central-ban című témakörben olvashat.