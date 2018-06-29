---
title: Új Azure IoT peremhálózati eszköz (portál) regisztrálása |} Microsoft Docs
description: Új IoT peremhálózati eszköz regisztrálása az Azure-portál használatával
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b61594469df33e11c23c9cbe0b9542da374fefa3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036033"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Azure-portálról új Azure IoT peremhálózati eszköz regisztrálása

Az IoT-eszközök Azure IoT oldala használata előtt kell regisztrálja őket az IoT hub. Eszköz regisztrálása után kapni egy kapcsolati karakterláncot, amely segítségével konfigurálja az eszközt, a peremhálózati munkaterhelésekhez. 

Ez a cikk bemutatja, hogyan kell regisztrálni egy új IoT peremhálózati eszköz, az Azure portál használatával.

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésben. 

## <a name="create-a-device"></a>Eszköz létrehozása

Az Azure-portálon az IoT-peremeszközök létrehozása és külön felügyelettel történik, az IoT hub csatlakozni, de nem edge-kompatibilis eszközökről. 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) , és keresse meg az IoT hub. 
2. Válassza ki **IoT peremhálózati** a menüből.
3. Válassza ki **IoT peremhálózati eszköz hozzáadása**. 
4. Adjon meg egy leíró eszköz. 
5. Kattintson a **Mentés** gombra. 

## <a name="view-all-devices"></a>Minden eszközök megtekintése

Minden az edge-képes eszközöket, amelyek kapcsolódnak az IoT hub szerepel a **IoT peremhálózati** lap. 

## <a name="retrieve-the-connection-string"></a>A kapcsolat-karakterlánc beolvasása

Amikor készen áll az eszköz beállításához, a kapcsolati karakterlánc, amely összekapcsolja a fizikai eszköz, az ad meg az IoT hub szüksége.

1. Az a **IoT peremhálózati** oldalra a portálon, kattintson a biztonsági eszközök a listából az Eszközazonosító. 
2. Másolja a értéke **kapcsolati karakterlánc – elsődleges kulcs** vagy **kapcsolati karakterlánc – másodlagos kulcs**. 

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [modulok telepítése egy eszközön az Azure-portálon](how-to-deploy-modules-portal.md)