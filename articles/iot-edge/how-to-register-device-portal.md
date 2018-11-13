---
title: Regisztráljon egy új Azure IoT Edge-eszköz (portál) |} A Microsoft Docs
description: Egy új IoT Edge-eszköz regisztrálása az Azure portal használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6657203c76bc03a262fbcbd30b5bf74b5be140eb
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577498"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Az Azure Portalról egy új Azure IoT Edge-eszköz regisztrálása

Az IoT-eszközök Azure IoT Edge használata előtt kell regisztrálni őket az IoT hubbal. Miután regisztrált egy eszközt, kap egy kapcsolati karakterláncot, amely segítségével konfigurálja az eszközt, a peremhálózati számítási feladatokhoz. 

Ez a cikk bemutatja, hogyan regisztrálhat egy új IoT Edge-eszköz az Azure portal használatával.

## <a name="prerequisites"></a>Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében. 

## <a name="create-a-device"></a>Eszköz létrehozása

Az Azure Portalon IoT Edge-eszközök létrehozása és csatlakoztatása az IoT hubhoz, de nem az edge-kompatibilis elkülönülten kezelhetőek. 

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot. 
2. Válassza ki **IoT Edge** a menüből.
3. Válassza ki **adja hozzá az IoT Edge-eszköz**. 
4. Adjon meg egy leíró eszközazonosító. 
5. Kattintson a **Mentés** gombra. 

## <a name="view-all-devices"></a>Minden eszköz megjelenítése

A felsorolt összes az edge-kompatibilis eszközök csatlakoztatása az IoT hubhoz a **IoT Edge** lapot. 

## <a name="retrieve-the-connection-string"></a>A kapcsolati karakterlánc

Amikor készen áll az eszköz beállításához, a kapcsolati karakterláncot, amely összekapcsolja a fizikai eszköz az IoT hub az identitással kell.

1. Az a **IoT Edge** oldalra a portálon, kattintson az Edge-eszközök a listából az eszköz azonosítója. 
2. Másolja az értéket **kapcsolati karakterlánc (elsődleges kulcs)** vagy **kapcsolati karakterlánc (másodlagos kulcs)**. 

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [modulok telepítése egy eszközön, az Azure portal használatával](how-to-deploy-modules-portal.md)
