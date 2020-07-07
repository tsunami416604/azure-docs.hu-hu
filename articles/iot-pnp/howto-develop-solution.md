---
title: IoT-Plug and Play előnézeti eszköz használata Azure IoT-megoldásból | Microsoft Docs
description: Megoldás fejlesztőként megtudhatja, hogyan használhatja a Service SDK-t a IoT Plug and Play-eszközök használatához.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159217"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Kapcsolódás IoT Plug and Play előnézeti eszközhöz

Ez az útmutató bemutatja, hogyan használhatók a Node Service SDK-ban található minták, amelyek bemutatják, hogyan kezelhetik a IoT-megoldás a IoT Plug and Play előnézeti eszközökkel.

Ha még nem fejezte be a [IoT Plug and Play eszköz csatlakoztatása a megoldáshoz](quickstart-connect-pnp-device-solution-node.md) – rövid útmutató, ezt most végezze el. A rövid útmutató bemutatja, hogyan töltheti le és telepítheti az SDK-t, és hogyan futtathat néhány mintát.

A szolgáltatási minták futtatása előtt nyisson meg egy új terminált, nyissa meg a klónozott tárház gyökérkönyvtárát, lépjen a **digitaltwins/Gyorsindítás/szolgáltatás** mappába, majd futtassa a következő parancsot a függőségek telepítéséhez:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>A szolgáltatási minták futtatása

A következő minták segítségével megismerheti a Node.js Service SDK képességeit. Győződjön meg arról, hogy a `IOTHUB_CONNECTION_STRING` környezeti változó a használt rendszerhéjban van beállítva:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Digitális iker beolvasása és a felületek listázása

**get_digital_twin.js** beolvassa az eszközhöz társított digitális IKeret, és kinyomtatja az összetevőjét a parancssorból. Nem igényli, hogy a futó eszköz mintája sikeres legyen.

**get_digital_twin_interface_instance.js** beolvassa az eszközhöz társított digitális Twin-példányokat, és kiírja a parancssorból. Nem igényli, hogy az eszköz minta fusson.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Tulajdonságok beolvasása és beállítása a Node Service SDK használatával

**update_digital_twin.js** frissít egy írható tulajdonságot az eszköz Digital Twin-re egy teljes javítás használatával. Ha szeretne, több tulajdonságot is frissíthet több felületen. Ahhoz, hogy sikeres legyen, az eszköz mintájának egy időben kell futnia. A siker úgy néz ki, mint az eszköz mintája, amely egy olyan tulajdonság frissítését nyomtatja ki a szolgáltatásban, amely egy frissített digitális Twin-t nyomtat a terminálon.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Parancs küldése és a válasz beolvasása a Node Service SDK használatával

**invoke_command.js** egy szinkron parancsot hív meg az eszköz Digital Twin szolgáltatásában. Ahhoz, hogy sikeres legyen, az eszköz mintájának egy időben kell futnia. A siker úgy néz ki, mint az eszköz mintája, hogy a rendszer kinyomtassa a parancsot, és a szolgáltatás ügyfele kinyomtatja a parancs eredményét a terminálon.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Kapcsolódás a nyilvános tárházhoz és a modell definíciójának beolvasása a Node Service SDK használatával

A szolgáltatás-és eszköz-mintákkal megegyező utasítások használatával a következő környezeti változót kell megadnia:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Ez a [IoT-portálon](https://preview.catalog.azureiotsolutions.com) található, a **vállalati tárházhoz**tartozó **kapcsolatok karakterláncok** lapján megtalálhatja ezt a kapcsolatokat megadó karakterláncot.

A kapcsolatok karakterlánca a következő példához hasonlít:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Miután beállította a négy környezeti változót, futtassa a mintát ugyanúgy, ahogy futtatta a többi mintát:

```cmd/sh
node model_repo.js
```

Ez a minta letölti az **ModelDiscovery** felületet, és kinyomtatja ezt a modellt a terminálon.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Lekérdezések futtatása IoT Hub a képességi modellek és felületek alapján

A IoT Hub lekérdezési nyelv támogatja a `HAS_INTERFACE` `HAS_CAPABILITYMODEL` következő példákban láthatókat:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Digitális dupla útvonalak létrehozása

A megoldás a digitális kettős változási eseményekről is fogadhat értesítéseket. Az értesítésekre való előfizetéshez használja az [IoT hub Routing funkciót](../iot-hub/iot-hub-devguide-endpoints.md) , hogy az értesítéseket egy végpontba küldje, például blob storage, Event Hubs vagy Service Bus üzenetsor.

Digitális kettős útvonal létrehozása:

1. A Azure Portal lépjen a IoT Hub erőforráshoz.
1. Válassza az **üzenet-útválasztás**lehetőséget.
1. Az **útvonalak** lapon válassza a **Hozzáadás**lehetőséget.
1. Adjon meg egy értéket a **név** mezőben, és válasszon egy **végpontot**. Ha még nem konfigurált végpontot, válassza a **végpont hozzáadása**elemet.
1. Az **adatforrás** legördülő menüben válassza a **digitális kettős változási események**lehetőséget.
1. Kattintson a **Mentés** gombra.

A következő JSON egy példát mutat be egy digitális kettős változási eseményre:

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a IoT Plug and Play eszközökkel kommunikáló szolgáltatási megoldásokat, a következő lépés a [modell felderítésének](concepts-model-discovery.md)megismerése.
