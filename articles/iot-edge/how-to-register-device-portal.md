---
title: Regisztráljon egy új eszközt az Azure portal – Azure IoT Edge |} A Microsoft Docs
description: Az Azure portal használatával egy új IoT Edge-eszköz regisztrálása, és a kapcsolati karakterlánc
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7f3d0037bcf0fd33ae23c298679e3157046247cb
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983528"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Az Azure Portalról egy új Azure IoT Edge-eszköz regisztrálása

Az IoT-eszközök Azure IoT Edge használata előtt kell regisztrálni őket az IoT hubbal. Az eszköz regisztrálása után egy kapcsolódási sztringet kap, amellyel beállíthatja az eszközt IoT Edge számítási feladatokhoz.

Ez a cikk bemutatja, hogyan regisztrálhat egy új IoT Edge-eszköz az Azure portal használatával.

## <a name="prerequisites"></a>Előfeltételek

Az Azure-előfizetésében ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) .

## <a name="create-a-device"></a>Eszköz létrehozása

Az Azure Portalon IoT Edge-eszközök létrehozása és csatlakoztatása az IoT hubhoz, de nem az edge-kompatibilis elkülönülten kezelhetőek.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot.
2. Válassza ki **IoT Edge** a menüből.
3. Válassza **a IoT Edge eszköz hozzáadása**lehetőséget.
4. Adjon meg egy leíró eszközazonosító. Használja az alapértelmezett beállításokat a hitelesítési kulcsok automatikus létrehozásához és az új eszköz csatlakoztatásához a központhoz.
5. Kattintson a **Mentés** gombra.

## <a name="view-all-devices"></a>Minden eszköz megjelenítése

A felsorolt összes az edge-kompatibilis eszközök csatlakoztatása az IoT hubhoz a **IoT Edge** lapot.

## <a name="retrieve-the-connection-string"></a>A kapcsolati karakterlánc

Amikor készen áll az eszköz beállításához, a kapcsolati karakterláncot, amely összekapcsolja a fizikai eszköz az IoT hub az identitással kell.

1. A portál **IoT Edge** lapján kattintson az eszköz azonosítójára a IoT Edge eszközök listájáról.
2. Másolja az értéket **kapcsolati karakterlánc (elsődleges kulcs)** vagy **kapcsolati karakterlánc (másodlagos kulcs)** .

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [helyezhet üzembe modulokat egy eszközön a Azure Portal](how-to-deploy-modules-portal.md)használatával.
