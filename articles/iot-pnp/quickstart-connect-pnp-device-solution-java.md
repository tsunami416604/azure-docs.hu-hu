---
title: Az Azure IoT-megoldáshoz csatlakoztatott IoT Plug and Play előzetes verziójú eszközzel való együttműködés | Microsoft dokumentumok
description: A Java segítségével csatlakozhat az Azure IoT-megoldásához csatlakoztatott IoT Plug and Play előzetes verzióhoz, és kommunikálhat vele.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 53812e68fe397b81f29869565e0e4a0640a9ef23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964634"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Rövid útmutató: A megoldáshoz csatlakoztatott IoT Plug and Play előzetes verziójú eszközzel (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Az IoT Plug and Play Preview leegyszerűsíti az IoT-t azáltal, hogy lehetővé teszi az eszköz képességeinek kommunikálását az eszköz megvalósításának ismerete nélkül. Ez a rövid útmutató bemutatja, hogyan csatlakozhat a Java segítségével a megoldáshoz csatlakoztatott IoT Plug and Play-eszközökhöz, és hogyan vezérelheti azt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához java SE 8-ra van szüksége a fejlesztői gépen. Azt is telepítenie kell Maven 3.

A rról, hogy miként állíthatja be ezeket, [olvassa el a fejlesztői környezet előkészítése](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) a Microsoft Azure IoT-eszköz Java-hoz sdk-ban című témakört.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot az _IoT hub kapcsolati karakterláncának_ lekérnia a hubhoz (megjegyzés későbbi használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>A mintaeszköz futtatása

Ebben a rövid útmutatóban egy Java-ban írt környezetérzékelőt használ IoT Plug and Play eszközként. Az alábbi utasítások bemutatják, hogyan telepítheti és futtathatja az eszközt:

1. Nyisson meg egy terminálablakot az Ön által választott könyvtárban. A következő parancs végrehajtása a Java GitHub-tárház [Azure IoT-mintáinak](https://github.com/Azure-Samples/azure-iot-samples-java) ezen a helyre történő klónozásához:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Ez a terminálablak lesz az _eszköz_ terminálja. Nyissa meg a klónozott tárház mappáját, és keresse meg az **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample** mappát. Telepítse a szükséges könyvtárakat, és építse fel a szimulált eszközalkalmazást a következő parancs futtatásával:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Az _eszköz kapcsolati karakterláncának konfigurálása_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Futtassa a következő parancsot a minta eszközmappából történő futtatásához.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Megjelenik az üzenetek, amelyek arról szólnak, hogy az eszköz csatlakoztatva van, különböző telepítési lépéseket hajt végre, és várja a szolgáltatás frissítéseit, majd telemetriai naplók. Ez azt jelzi, hogy az eszköz most már készen áll a parancsok és a tulajdonságfrissítések fogadására, és megkezdte a telemetriai adatok küldését a hubra. A következő lépések végrehajtásával tartsa a mintát. Ne zárja be ezt a terminált, később szüksége lesz rá, hogy ellenőrizze, hogy a szolgáltatásminták is működtek.Don't close this terminal, you'll need it later to confirm the service samples also worked.

## <a name="run-the-sample-solution"></a>A mintamegoldás futtatása

Ebben a rövid útmutatóban egy minta IoT-megoldás Java-ban a mintaeszközzel való együttműködéshez.

1. Nyisson meg egy másik _service_ terminálablakot (ez lesz a szervizterminálja). Nyissa meg a klónozott tárház mappáját, és keresse meg az **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample** mappát.

1. Telepítse a szükséges könyvtárakat, és a következő parancs futtatásával készítse el a szolgáltatásmintát:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurálja az _IoT hub kapcsolati karakterláncát_ és _az eszközazonosítót_ úgy, hogy a szolgáltatás mindkét félhez csatlakozhasson:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Ingatlan olvasása

1. Amikor csatlakoztatta az _eszközt_ a terminálján, az egyik kimeneti üzenet a következő üzenet volt, amely jelzi az online állapotát. A `state` tulajdonság, amely annak jelzésére szolgál, hogy az eszköz online állapotban van-e, _igaz:_

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Lépjen a _service_ szervizterminálra, és a következő paranccsal futtassa a szolgáltatásmintát az eszközadatok olvasásához:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. A _szervizterminál_ kimenetében görgessen az `environmentalSensor` összetevőhöz. Láthatja, `state` hogy a tulajdonság _ot igazként_jelentették:
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

1. Lépjen a _service_ szervizterminálra, és állítsa be a következő változókat a frissítandó tulajdonság meghatározásához:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. A következő paranccsal futtatja a szolgáltatásmintát a tulajdonság frissítéséhez:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. A _szolgáltatásterminál_ kimenete a frissített eszközadatokat jeleníti meg. Görgessen `environmentalSensor` az összetevőhöz az új 42-es fényerőérték megtekintéséhez.

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
2. Lépjen vissza _service_ a szervizterminálhoz, és futtassa az alábbi parancsot az eszközadatok újbóli levezetéséhez, hogy ellenőrizze a tulajdonság frissítésének megerősítését.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. A _szolgáltatásterminál_ kimenetében `environmentalSensor` az összetevő alatt láthatja a frissített fényerő-értéket. Megjegyzés: eltarthat egy ideig, amíg az eszköz befejezi a frissítést. Ezt a lépést addig ismételheti, amíg az eszköz ténylegesen fel nem dolgozta a tulajdonságfrissítést.
    
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

1. Lépjen a _service_ szolgáltatásterminálra, és állítsa be a következő változókat a meghívni a parancs meghatározásához:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. A parancs meghívásához a következő paranccsal futtatja a szolgáltatásmintát:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. A _szolgáltatásterminál_ kimenetének a következő visszaigazolást kell mutatnia:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Menj az _eszköz_ terminál, látod a parancsot elismerte:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play eszközt egy IoT-megoldáshoz. Ha többet szeretne tudni arról, hogyan hozhat létre olyan megoldást, amely kölcsönhatásba lép az IoT Plug and Play eszközeivel, olvassa el a következő témaköröket:

> [!div class="nextstepaction"]
> [Útmutató: Eszközhöz való csatlakozás és az eszközzel való interakció](howto-develop-solution.md)
