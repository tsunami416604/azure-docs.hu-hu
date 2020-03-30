---
title: A számla kezelése és az ingyenes díjszabási csomagból való konvertálás az Azure IoT Central alkalmazásban | Microsoft dokumentumok
description: Rendszergazdaként ismerje meg, hogyan kezelheti a számlát, és hogyan léphet át az ingyenes díjcsomagról egy szabványos díjcsomagra az Azure IoT Central alkalmazásban
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157507"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>A számla kezelése egy IoT Central alkalmazásban

Ez a cikk ismerteti, hogyan, rendszergazdaként kezelheti a számlát az Azure IoT Central alkalmazás a felügyeleti szakaszban. Megtudhatja, hogyan helyezheti át alkalmazását az ingyenes díjszabási tervről egy szabványos díjcsomagra, valamint azt is, hogyan frissítheti vagy csökkentheti az árképzési tervet.

A **Felügyeleti** szakasz eléréséhez és használatához *rendszergazdai* szerepkörben kell lennie, vagy egyéni felhasználói szerepkörrel kell *rendelkeznie,* amely lehetővé teszi az Azure IoT Central alkalmazás számlázásának megtekintését. Ha létrehoz egy Azure IoT Central-alkalmazást, a rendszer automatikusan hozzárendeli az adott alkalmazás **rendszergazdai** szerepköréhez.

## <a name="move-from-free-to-standard-pricing-plan"></a>Áttérés az ingyenes díjcsomagról a normál árképzési tervre

- Az ingyenes díjszabási csomagot használó alkalmazások hét napig ingyenesek, mielőtt lejárnak. Az adatok elvesztésének elkerülése érdekében bármikor áthelyezheti őket egy szabványos díjcsomagba, mielőtt azok lejárnának.
- A szabványos díjszabási csomagot használó alkalmazások díja eszközönként kerül felszámításra, az első két eszköz pedig alkalmazásonként ingyenes.

További információk a díjszabásról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/) találhatók.

A díjszabási szakaszban áthelyezheti az alkalmazást az ingyenesről egy szabványos díjcsomagra.

Az önkiszolgáló folyamat végrehajtásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg az **Adminisztráció** szakasz **Árképzés** lapját.

    ![Próbaállapota](media/howto-view-bill/freetrialbilling.png)

1. Válassza **a Konvertálás fizetős sémává**lehetőséget.

    ![Próbaverzió konvertálása](media/howto-view-bill/convert.png)

1. Válassza ki a megfelelő Azure Active Directoryt, majd az Azure-előfizetést, amelyet fizetős csomagot használó alkalmazáshoz kell használni.

1. Miután **kiválasztotta**a Konvertálás lehetőséget, az alkalmazás most már fizetős csomagot használ, és elkezdi a számlázást.

> [!Note]
> Alapértelmezés szerint *standard 2* árképzési séma lesz.

## <a name="how-to-change-your-application-pricing-plan"></a>Az alkalmazás díjszabási tervének módosítása

A szabványos díjszabási csomagot használó alkalmazások díja eszközönként kerül felszámításra, az első két eszköz pedig alkalmazásonként ingyenes.

A díjszabási szakaszban bármikor frissítheti vagy visszaminősítheti az Azure IoT-díjszabási csomagot.

1. Nyissa meg az **Adminisztráció** szakasz **Árképzés** lapját.

    ![Próbaállapota](media/howto-view-bill/pricing.png)

1. Válassza a **Sémát,** és kattintson a **Mentés** gombra a frissítéshez vagy a visszaminősítéshez.

## <a name="view-your-bill"></a>Számla megtekintése

1. Válassza ki a megfelelő Azure Active Directoryt, majd az Azure-előfizetést, amelyet fizetős csomagot használó alkalmazáshoz kell használni.

1. Miután **kiválasztotta**a Konvertálás lehetőséget, az alkalmazás most már fizetős csomagot használ, és elkezdi a számlázást.

> [!Note]
> Alapértelmezés szerint *standard 2* árképzési séma lesz.

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett a számlával az Azure IoT Central alkalmazásban, a javasolt következő lépés az [alkalmazás felhasználói felületének testreszabása](howto-customize-ui.md) az Azure IoT Centralban.