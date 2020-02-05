---
title: Az Azure IoT Central alkalmazás ingyenes díjszabási csomagjából kezelheti a számláját, és átalakíthatja azokat | Microsoft Docs
description: Rendszergazdaként megtudhatja, hogyan kezelheti számláját, és hogyan helyezheti át az ingyenes díjszabási csomagot az Azure IoT Central-alkalmazásban található standard díjszabási csomagra
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e45109842a081b437c20de81321bf70f909efa2a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982351"
---
# <a name="view-your-bill-in-iot-central-application"></a>Számla megtekintése IoT Central alkalmazásban

Ez a cikk azt ismerteti, hogyan kezelheti a számlát az Azure IoT Central alkalmazásban az adminisztráció szakaszban. Megtudhatja, hogyan helyezheti át alkalmazását az ingyenes díjszabási csomagból egy standard díjszabási csomagra, és hogyan frissítheti és minősítheti a díjszabási tervet.

Az **Adminisztráció** szakasz eléréséhez és használatához *rendszergazdai* szerepkörrel vagy *egyéni felhasználói szerepkörrel* kell rendelkeznie, amely lehetővé teszi az Azure IoT Central-alkalmazások számlázásának megtekintését. Ha létrehoz egy Azure IoT Central alkalmazást, a rendszer automatikusan hozzárendeli az adott alkalmazáshoz tartozó **rendszergazdai** szerepkörhöz.

## <a name="move-from-free-to-standard-pricing-plan"></a>Az ingyenesről a standard díjszabási csomagra való áttérés

- Az ingyenes díjszabási csomagot használó alkalmazások a lejárat előtt hét napig díjmentesek. Az adatvesztés elkerülése érdekében a lejárat előtt bármikor áthelyezheti őket egy standard díjszabási csomagba.

A díjszabás szakaszban az alkalmazást ingyenesen áthelyezheti a standard díjszabási csomagba.

Az önkiszolgáló folyamat végrehajtásához kövesse az alábbi lépéseket:

1. Lépjen az **Adminisztráció** szakasz **díjszabás** lapjára.

    ![Próbaverzió állapota](media/howto-administer/freetrialbilling.png)

1. Válassza **a konvertálás fizetős csomagra**lehetőséget.

    ![Próbaverzió konvertálása](media/howto-administer/convert.png)

1. Válassza ki a megfelelő Azure Active Directory, majd a fizetős csomagot használó alkalmazáshoz használni kívánt Azure-előfizetést.

1. Az **átalakítás**lehetőség kiválasztását követően az alkalmazás mostantól fizetős csomagot használ, és megkezdi a számlázást.

> [!Note]
> Alapértelmezés szerint a *Standard 2* díjszabási csomagra vált.

## <a name="how-to-change-your-application-pricing-plan"></a>Az alkalmazás díjszabási tervének módosítása

- A standard díjszabási csomagot használó alkalmazások díja eszközönként, az első két eszköz esetében pedig ingyenes.

A díjszabás szakaszban bármikor frissítheti vagy visszaállíthatja az Azure IoT díjszabási csomagját.

1. Lépjen az **Adminisztráció** szakasz **díjszabás** lapjára.

    ![Próbaverzió állapota](media/howto-administer/pricing.png)

1. Válassza ki a **csomagot** , és kattintson a **Mentés** gombra a frissítéshez vagy a visszalépéshez.

További információk a díjszabásról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/) találhatók.

## <a name="view-your-bill"></a>Számla megtekintése

A számla megtekintéséhez lépjen a **felügyelet** szakasz **díjszabás** lapjára. Megnyílik az Azure díjszabása oldal egy új lapon, ahol megtekintheti az egyes Azure IoT Central-alkalmazások számláit.


## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan tekintheti meg a számláját az Azure IoT Central alkalmazásban, a javasolt következő lépés az [alkalmazás felhasználói felületének testreszabása](howto-customize-ui.md) az Azure IoT Central-ban című témakörben olvashat.