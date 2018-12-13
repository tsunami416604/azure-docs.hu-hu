---
title: Regisztráljon egy új eszközt parancssorból – Azure IoT Edge |} A Microsoft Docs
description: Le a kapcsolati karakterláncot, és a egy új IoT Edge-eszköz regisztrálása az IoT-bővítmény, az Azure CLI használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/27/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 28f20157de603cf20cb33e9db569064f87922bf8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081833"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Egy új Azure IoT Edge-eszköz regisztrálása az Azure CLI-vel

Az IoT-eszközök Azure IoT Edge használata előtt kell regisztrálni őket az IoT hubbal. Miután regisztrált egy eszközt, kap egy kapcsolati karakterláncot, amely segítségével konfigurálja az eszközt, a peremhálózati számítási feladatokhoz. 

[Az Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú közötti platform parancssori eszköz, például az IoT Edge Azure-erőforrások kezeléséhez. Lehetővé teszi, hogy az Azure IoT Hub-erőforrások, eszközregisztrációs szolgáltatáspéldányok és csatolt központok beépített kezelése. Az új IoT-bővítmény Azure CLI-vel bővíti, például az Eszközfelügyelet és teljes körű IoT Edge-képességek.

Ez a cikk bemutatja, hogyan regisztrálhat egy új IoT Edge-eszköz az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében. 
* [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI 2.0.24-es verzióját kell vagy újabb. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 
* A [IoT-bővítmény az Azure CLI-vel](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Eszköz létrehozása

A következő paranccsal hozzon létre egy új eszközidentitást az IoT hubban: 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Ez a parancs három paramétereket tartalmazza:
* **eszközazonosító**: Adjon meg egy leíró nevet, amely egyedi az IoT hubnak.
* **eseményközpont-neve**: Adja meg az IoT hub nevét.
* **Edge-kompatibilis**: Ez a paraméter kijelenti, hogy az eszköz IoT Edge való használatra.

   ![az iot hub-eszközidentitást create kimenete](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Minden eszköz megjelenítése

A következő paranccsal minden eszköz megtekintése az IoT hubban:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Bármely eszköz, amely regisztrálva van, az IoT Edge-eszköz lesz a tulajdonság **capabilities.iotEdge** beállítása **igaz**. 

## <a name="retrieve-the-connection-string"></a>A kapcsolati karakterlánc

Amikor készen áll az eszköz beállításához, a kapcsolati karakterláncot, amely összekapcsolja a fizikai eszköz az IoT hub az identitással kell. A következő parancs használatával adja vissza egy adott eszköz a kapcsolati karakterlánc:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

Az eszköz azonosító paraméter értéke a kis-és nagybetűket. Ne másolja a kapcsolati karakterláncot az idézőjelek közé. 

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [modulok telepítése egy eszközön, az Azure CLI-vel](how-to-deploy-modules-cli.md)
