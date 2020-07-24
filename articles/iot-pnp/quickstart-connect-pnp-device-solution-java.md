---
title: Interakció a IoT Plug and Play a megoldáshoz csatlakoztatott előzetes verziójú eszközzel – Java | Microsoft Docs
description: A Java használatával csatlakozhat egy IoT Plug and Play előnézeti eszközhöz, amely az Azure IoT-megoldáshoz csatlakozik.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 43136e1a09cc771f39b92a83ca4c452fc1276091
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044032"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Gyors útmutató: interakció egy IoT Plug and Play a megoldáshoz csatlakoztatott előnézeti eszközzel (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

A IoT Plug and Play Preview leegyszerűsíti a IoT azáltal, hogy lehetővé teszi, hogy a mögöttes eszköz megvalósításának ismerete nélkül kommunikáljon az eszköz képességeivel. Ez a rövid útmutató bemutatja, hogyan használható a Java a megoldáshoz csatlakoztatott IoT Plug and Play-eszköz csatlakoztatásához és vezérléséhez.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a fejlesztői gépen a Java SE 8 rendszerre van szükség. Telepítenie kell a Maven 3 programot is.

Az ezekkel való beállításával kapcsolatos további információkért lásd: [a fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) a javához készült Microsoft Azure IOT Device SDK-ban.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot a hub _IoT hub-beli kapcsolódási karakterláncának_ lekéréséhez (jegyezze fel később a használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

Ebben a rövid útmutatóban egy olyan minta környezeti érzékelőt használ, amelyet Java-ban írt IoT Plug and Play eszközként. Az alábbi utasítások bemutatják, hogyan telepítheti és futtathatja az eszközt:

1. Nyisson meg egy terminált az Ön által választott könyvtárban. Futtassa a következő parancsot a Java GitHub-tárházhoz [tartozó Azure IoT-minták](https://github.com/Azure-Samples/azure-iot-samples-java) ezen a helyen történő klónozásához:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. A rendszer most ezt a terminált fogja használni az _eszköz_ -terminálként. Lépjen a klónozott adattár mappájába, és lépjen a **/Azure-IOT-Samples-Java/Digital-Twin/Samples/Device/JdkSample** mappára. Telepítse a szükséges kódtárakat, és hozza létre a szimulált eszköz alkalmazást a következő parancs futtatásával:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Az _eszköz-kapcsolatok karakterláncának_konfigurálása:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Futtassa a következő parancsot a minta az eszköz mappájából való futtatásához.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Láthatja, hogy az eszköz csatlakoztatva van, különböző telepítési lépéseket hajt végre, és várakozás a szolgáltatás frissítéseire, majd a telemetria-naplók elemre. Ez azt jelzi, hogy az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát. Ne zárjuk be ezt a terminált, ezért később meg kell győződnie arról, hogy a szolgáltatási minták is működőképesek.

## <a name="run-the-sample-solution"></a>A minta megoldás futtatása

Ebben a rövid útmutatóban egy példaként szolgáló IoT-megoldást használ a Javaban, hogy együttműködjön a minta eszközzel.

1. Nyisson meg egy másik Terminálablak (ez lesz a _szolgáltatás_ terminálja). Lépjen a klónozott adattár mappájába, és lépjen a **/Azure-IOT-Samples-java\digital-twin\Samples\service\JdkSample** mappára.

1. Telepítse a szükséges kódtárakat, és hozza létre a szolgáltatási mintát a következő parancs futtatásával:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurálja az _IoT hub kapcsolati karakterláncát_ és az _eszköz azonosítóját_ , hogy a szolgáltatás a következőhöz kapcsolódjon:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Tulajdonság beolvasása

1. Amikor csatlakoztatta az _eszközt_ a termináljában, az egyik kimeneti üzenet a következő üzenet volt, amely jelzi az online állapotát. A `state` tulajdonság, amely azt jelzi, hogy az eszköz online állapotban van-e, _igaz_:

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Nyissa meg a _szolgáltatás_ terminálját, és a következő paranccsal futtassa az eszköz adatainak olvasásához használt szolgáltatási mintát:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. A _szolgáltatás_ -terminál kimenetében görgessen az `environmentalSensor` összetevőhöz. Láthatja, hogy a `state` tulajdonság _értéke TRUE (igaz_):
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Írható tulajdonság frissítése

1. Nyissa meg a _szolgáltatás_ -terminált, és állítsa be a következő változókat a frissíteni kívánt tulajdonság megadásához:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. A következő parancs futtatásával futtathatja a tulajdonság frissítéséhez használandó szolgáltatási mintát:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. A _szolgáltatás_ -terminál kimenete a frissített eszköz információit jeleníti meg. Görgessen az `environmentalSensor` összetevőhöz az új fényerő 42-es értékének megtekintéséhez.

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. Nyissa meg az _eszköz_ terminálját, és láthatja, hogy az eszköz megkapta a frissítést:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Lépjen vissza a _szolgáltatás_ -terminálhoz, és futtassa az alábbi parancsot az eszköz adatainak újbóli beszerzéséhez, hogy megbizonyosodjon róla, hogy a tulajdonság frissítve lett.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. A _szolgáltatás_ -terminál kimenetében az `environmentalSensor` összetevő alatt látható a frissített fényerő értékének jelentése. Megjegyzés: eltarthat egy ideig, amíg az eszköz befejezi a frissítést. Ezt a lépést csak akkor ismételheti meg, ha az eszköz ténylegesen feldolgozta a tulajdonság frissítését.
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>Parancs meghívása

1. Nyissa meg a _szolgáltatás_ -terminált, és állítsa be a következő változókat a meghívni kívánt parancs megadásához:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. A következő parancs használatával futtassa a szolgáltatási mintát a parancs meghívásához:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. A _szolgáltatás_ -terminál kimenetében a következő megerősítésnek kell megjelennie:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Nyissa meg az _eszköz_ terminálját, és láthatja, hogy a parancs meg lett ismerve:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztatható egy IoT Plug and Play-eszköz egy IoT-megoldáshoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás az eszközhöz](howto-develop-solution.md)
