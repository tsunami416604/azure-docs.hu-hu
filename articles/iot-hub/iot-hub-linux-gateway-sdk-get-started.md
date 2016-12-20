---
title: "IoT Hub-átjáró SDK – Első lépések | Microsoft Docs"
description: "Az Azure IoT Gateway SDK-útmutató Linux rendszerben ismerteti az Azure IoT Gateway SDK-val kapcsolatos alapvető fogalmakat."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: a76320718f0cefa015728cb79df944e0d34bbf74
ms.openlocfilehash: cbb909adc2d29f9b80a4c97d06176fe74b64a75a


---
# <a name="azure-iot-gateway-sdk---get-started-using-linux"></a>Azure IoT Gateway SDK – Első lépések a Linux használatával
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>A minta létrehozása
Mielőtt elkezdené, [be kell állítani a fejlesztési környezetet][lnk-setupdevbox] az SDK Linux rendszeren történő használatához.

1. Nyisson meg egy rendszerhéjat.
2. Az **azure-iot-gateway-sdk** adattár helyi példányában lépjen a gyökérmappába.
3. Futtassa a **tools/build.sh** szkriptet. Ez a szkript a **cmake** segédprogramot használja a **build** nevű mappa létrehozásához az **azure-iot-gateway-sdk** adattár helyi példányának gyökérmappájában, valamint a makefile előállításához. Ezt követően a szkript felépíti a megoldást, és futtatja a teszteket.

> [!NOTE]
> A **build.sh** szkript minden futtatásakor törli, majd újra létrehozza a **build** mappát az **azure-iot-gateway-sdk** adattár helyi példányának gyökérmappájában.
> 
> 

## <a name="how-to-run-the-sample"></a>A minta futtatása
1. A **build.sh** szkript a kimenetét a **build** mappában hozza létre, az **azure-iot-gateway-sdk** adattár helyi példányában. Ezek közé tartozik a jelen példában használt két modul is.
   
    A buildszkript a **liblogger.so** fájlt a **build/modules/logger/** mappába, a **libhello_world.so** fájlt pedig a **build/modules/hello_world/** mappába menti. Ezen elérési utakat a **module path** értékeként adja meg, ahogyan az az alábbi JSON-beállításfájlban szerepel.
2. A hello_world_sample folyamat egy JSON konfigurációs fájl elérési útját fogadja argumentumként a parancssorban. Egy példa JSON-fájl is szerepel a tárban az **azure-iot-gateway-sdk/samples/hello_world/src/hello_world_win.json** helyen. A fájl tartalma az alábbiakban látható. A jelenlegi formájában is működik, hacsak nem módosította a buildszkriptet, hogy az a modulokat és a végrehajtható mintafájlokat ne az alapértelmezett helyekre helyezze.

   > [!NOTE]
   > A modul az aktuális munkakönyvtárhoz képest relatív útvonalakat használ. A munkamappa az ahonnan a hello_world_sample végrehajtható fájlt elindította, és nem a végrehajtható fájl könyvtára. A JSON konfigurációs mintafájl a „log.txt” naplófájlokat alapértelmezetten az aktuális munkakönyvtárba írja.
   
    ```
    {
        "modules" :
        [
            {
              "name" : "logger",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/logger/liblogger.so"
                }
              },
              "args" : {"filename":"log.txt"}
            },
            {
                "name" : "hello_world",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/hello_world/libhello_world.so"
                }
              },
                "args" : null
            }
        ],
        "links": 
        [
            {
                "source": "hello_world",
                "sink": "logger"
            }
        ]
    }
    ```
3. Lépjen az **azure-iot-gateway-sdk** mappába.
4. Futtassa az alábbi parancsot:
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO4-->


