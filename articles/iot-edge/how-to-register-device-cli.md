---
title: Új Azure IoT peremhálózati eszköz (CLI) regisztrálása |} Microsoft Docs
description: Az IoT-bővítmény az Azure CLI 2.0 használatával új IoT peremhálózati eszköz regisztrálása
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/30/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6935dfe5b32364e402017cd3005ab17969ce1ce
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036081"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli-20"></a>Egy új Azure IoT peremhálózati eszköz regisztrálása az Azure CLI 2.0

Az IoT-eszközök Azure IoT oldala használata előtt kell regisztrálja őket az IoT hub. Eszköz regisztrálása után kapni egy kapcsolati karakterláncot, amely segítségével konfigurálja az eszközt, a peremhálózati munkaterhelésekhez. 

Az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forrású, több platformon is használható parancssori eszköz különböző Azure-erőforrások, például az IoT Edge felügyeletére. Ez lehetővé teszi, hogy Azure IoT Hub erőforrások, eszköz üzembe helyezési szolgáltatáspéldány és kapcsolódó hubok a kezdő verzióról kezelésére. Az új IoT-bővítmény az Azure CLI 2.0 funkcióinak körét olyan szolgáltatásokkal egészíti ki, amilyen például az eszközfelügyelet és a teljes körű IoT Edge-képességek.

Ez a cikk bemutatja, hogyan kell regisztrálni egy új IoT peremhálózati eszköz, az Azure CLI 2.0 verziót használja.

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésben. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) a saját környezetében. Az Azure CLI 2.0 legalább 2.0.24-es verzióját kell használnia. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 
* A [IoT-bővítményt az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Eszköz létrehozása

Az alábbi parancs segítségével hozzon létre egy új eszközidentitás az IoT hub: 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Ez a parancs három paramétereket tartalmaz:
* **eszközazonosító**: Adjon meg egy leíró nevet az IoT hub egyedi.
* **központnév**: Adja meg az IoT hub nevét.
* **Edge-kompatibilis**: Ez a paraméter kijelenti, hogy az eszköz IoT peremhálózati való használatra.

   ![IoT Edge-eszköz létrehozása](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Minden eszközök megtekintése

A következő paranccsal tekintse meg az IoT hub minden eszköz:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Minden olyan eszköz, amely regisztrálva van-e, IoT peremhálózati eszköz lesz a tulajdonság **capabilities.iotEdge** beállítása **igaz**. 

## <a name="retrieve-the-connection-string"></a>A kapcsolat-karakterlánc beolvasása

Amikor készen áll az eszköz beállításához, a kapcsolati karakterlánc, amely összekapcsolja a fizikai eszköz, az ad meg az IoT hub szüksége. Az alábbi parancs segítségével egyetlen eszközt a kapcsolati karakterláncot adja vissza:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

Az eszköz azonosítója paraméter kis-és nagybetűket. 

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [rendelkező Azure CLI 2.0 eszköz modulok telepítése](how-to-deploy-modules-cli.md)