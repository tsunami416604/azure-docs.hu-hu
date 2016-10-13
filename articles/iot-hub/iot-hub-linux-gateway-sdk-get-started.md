<properties
    pageTitle="IoT Hub-átjáró SDK – Első lépések | Microsoft Azure"
    description="Az Azure IoT Hub-átjáró SDK-nak ez a bemutatója az Azure IoT Hub-átjáró SDK használatához szükséges alapvető fogalmak ismertetéséhez Linux rendszert használ."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>



# IoT-átjáró SDK (béta) – Első lépések a Linux használatával

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## A minta létrehozása

Mielőtt elkezdené, [be kell állítani a fejlesztési környezetet][lnk-setupdevbox] az SDK Linux rendszeren történő használatához.

1. Nyisson meg egy rendszerhéjat.
2. Az **azure-iot-gateway-sdk** adattár helyi példányában lépjen a gyökérmappába.
3. Futtassa a **tools/build.sh** szkriptet. Ez a szkript a **cmake** segédprogramot használja a **build** nevű mappa létrehozásához az **azure-iot-gateway-sdk** adattár helyi példányának gyökérmappájában, valamint a makefile előállításához. Ezt követően a szkript felépíti a megoldást, és futtatja a teszteket.

> [AZURE.NOTE]  A **build.sh** szkript minden futtatásakor törli, majd újra létrehozza a **build** mappát az **azure-iot-gateway-sdk** adattár helyi példányának gyökérmappájában.

## A minta futtatása

1. A **build.sh** szkript a kimenetét a **build** mappában hozza létre, az **azure-iot-gateway-sdk** adattár helyi példányában. Ezek közé tartozik a jelen példában használt két modul is.

    A felépítési szkript a **liblogger_hl.so** fájlt a **build/modules/logger/** mappába, a **libhello_world_hl.so** fájlt pedig a **build/modules/hello_world/** mappába menti. Ezen elérési utakat a **module path** értékeként adja meg, ahogyan az az alábbi JSON-beállításfájlban szerepel.

2. A **samples/hello_world/src** mappában lévő **hello_world_lin.json** fájl egy példa Linux rendszeren használható JSON-beállításfájlra, amelyet a minta futtatásához használhat. Az alábbiakban látható JSON-beállításfájl azzal a feltételezéssel lett létrehozva, hogy a mintát az **azure-iot-gateway-sdk** adattár helyi példányának gyökérmappájából fogja futtatni.

3. A **logger_hl** modul **args** szakaszában a **filename** értékeként annak a fájlnak a nevét és elérési útját adja meg, amely a naplóadatokat fogja tartalmazni.

    Itt egy olyan, Linux rendszeren használható JSON-beállításfájlra láthat példát, amely a minta futtatásához használt mappában található **log.txt** fájlba ír.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. A rendszerhéjban lépjen az **azure-iot-gateway-sdk** mappába.
4. Futtassa az alábbi parancsot:
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Oct16_HO1-->


