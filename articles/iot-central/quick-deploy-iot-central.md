---
title: Azure IoT Central-alkalmazás létrehozása | Microsoft Docs
description: Új Azure IoT Central-alkalmazást hozhat létre a hűtött eladóautomaták kezeléséhez. Megtekintheti a szimulált eszközökről létrehozott telemetriaadatokat.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/15/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: af06766d89804b2f3d0aaf061494fb836f6ec262
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465605"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

_Szerkesztőként_ az Azure IoT Central használatával határozza meg a Microsoft Azure IoT Central-alkalmazást. Ez a rövid útmutató a következőket mutatja be:

- _Eszközsablonmintát_ és szimulált _eszközöket_ tartalmazó Azure IoT Central-alkalmazás létrehozása.
- Az alkalmazásban a **Hűtött eladóautomata** eszközsablon jellemzőinek megtekintése.
- A szimulált **Hűtő** eszközök telemetriájának és elemzéseinek megtekintése.

Ebben a rövid útmutatóban egy szimulált **Hűtő** eszközt tekint meg egy eszközsablonból. A szimulált eszköz:

* Telemetriát, például hőmérséklet- és nyomásértéket küld az alkalmazásnak.
* Jelenti az eszköz tulajdonságértékeit az alkalmazásnak, például a mozgásriasztásokat.
* Az alkalmazásban beállítható eszközbeállításokkal (például a ventilátorsebesség szabályzása) rendelkezik.

Amikor szimulált eszközt hoz létre egy Azure IoT Central-alkalmazás egyik eszközsablonjából, a szimulált eszköz lehetővé teszi az alkalmazás tesztelését a valós eszköz csatlakoztatása előtt.

## <a name="create-the-application"></a>Az alkalmazás létrehozása

A rövid útmutató elvégzéséhez létre kell hoznia egy Azure IoT Central-alkalmazást a **Sample Contoso** alkalmazássablonból.

Lépjen az Azure IoT Central [Alkalmazáskezelő](https://aka.ms/iotcentral) oldalára. Ezután írja be az Azure-előfizetés eléréséhez használt e-mail-címet és jelszót:

![Lépjen a céges fiókjába](media/quick-deploy-iot-central/sign-in.png)

Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget:

![Azure IoT Central Alkalmazáskezelő oldal](media/quick-deploy-iot-central/iotcentralhome.png)

Új Azure IoT Central-alkalmazás létrehozása:

1. Válassza az **Ingyenes próbaalkalmazás** fizetési konstrukciót.
1. Válasszon egy rövid alkalmazásnevet, például a **Contoso IoT** nevet. Az Azure IoT Central létrehoz egy egyéni URL-előtagot. Ezt az URL-előtagot egy könnyebben megjegyezhető előtagra módosíthatja.
1. Válassza ki a **Sample Contoso** alkalmazássablont.
1. Ezután válassza a **Létrehozás** elemet.

![Azure IoT Central Alkalmazás létrehozása oldal](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy előre feltöltött Azure IoT Central-alkalmazást hozott létre, amely egy **Hűtött eladóautomata** eszközsablont és szimulált eszközöket tartalmaz. A saját eszközsablonok szerkesztőként történő meghatározásáról további tudnivalókért lásd az [új eszközsablon alkalmazásban való meghatározását](tutorial-define-device-type.md) ismertető szakaszt.
