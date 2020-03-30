---
title: IoT Plug and Play előzetes verziójú eszközzel való együttműködés Egy Azure IoT-megoldásból | Microsoft dokumentumok
description: Megoldásfejlesztőként ismerje meg, hogyan használhatja az SDK szolgáltatást az IoT Plug and Play eszközökkel való együttműködéshez.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159217"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Csatlakozás IoT Plug and Play előzetes verziójú eszközhöz, és azokkal való interakció

Ez az útmutató bemutatja, hogyan használhatja a mintákat a Node szolgáltatás SDK, amely bemutatja, hogyan ioT-megoldás kölcsönhatásba léphet az IoT Plug and Play Preview eszközök.

Ha még nem fejezte be az [IoT Plug and Play eszköz csatlakoztatása a megoldás](quickstart-connect-pnp-device-solution-node.md) rövid útmutatóját, akkor most tegye meg. A rövid útmutató bemutatja, hogyan töltheti le és telepítheti az SDK-t, és hogyan futtathatja a minták egy részét.

A szolgáltatásminták futtatása előtt nyisson meg egy új terminált, nyissa meg a klónozott tárház gyökérmappáját, keresse meg a **digitaltwins/quickstarts/service** mappát, majd futtassa a következő parancsot a függőségek telepítéséhez:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>A szolgáltatásminták futtatása

A következő minták segítségével fedezze fel a Node.js szolgáltatás SDK képességeit. Győződjön meg `IOTHUB_CONNECTION_STRING` arról, hogy a környezeti változó be van állítva a használt rendszerhéjban:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Digitális ikerbeolvasás és a kapcsolódási pontok felsorolása

**get_digital_twin.js** megkapja az eszközhöz társított digitális ikerpárt, és az összetevőt a parancssorba nyomtatja. A sikerhez nincs szükség futó eszközmintára.

**get_digital_twin_interface_instance.js** kap egy interfész példánya a digitális iker társított eszköz és kinyomtatja azt a parancssorból. Nincs szükség az eszköz minta futtatásához.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Tulajdonságok beéselése és beállítása a Csomópontszolgáltatás SDK-jával

**update_digital_twin.js** egy írható tulajdonságot frissít a készülék digitális ikerkészülékén egy teljes javítás használatával. Több tulajdonságot is frissíthet több kapcsolaton, ha szeretné. Ahhoz, hogy sikeres legyen, az eszközmintának egyidejűleg kell futnia. A siker úgy néz ki, mintha az eszközminta nyomtatna valamit egy tulajdonság frissítéséről, a szolgáltatás minta egy frissített digitális ikerpárt nyomtat a terminálon.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Parancs küldése és a válasz beolvasása a Csomópont szolgáltatás SDK-jával

**invoke_command.js** szinkron parancsot hív meg az eszköz digitális ikerkészülékén. Ahhoz, hogy sikeres legyen, az eszközmintának egyidejűleg kell futnia. A siker úgy néz ki, mintha az eszközminta a parancs elismeréséről, a szolgáltatásügyfél pedig a parancs eredményét nyomtatna a terminálon.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Csatlakozás a nyilvános tárházhoz, és egy modelldefiníció lekérése a Csomópontszolgáltatás SDK-jával

A szolgáltatás- és eszközmintákkal megegyező utasításokat használva a következő környezeti változót kell beállítania:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Ez a kapcsolati karakterlánc az [Azure Certified for IoT portalon](https://preview.catalog.azureiotsolutions.com) található a **vállalati tárház** **Kapcsolatkarakterláncok** lapján.

A kapcsolati karakterlánc a következő példához hasonlóan néz ki:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Miután beállította ezt a négy környezeti változót, futtassa a mintát ugyanúgy, mint a többi mintát:

```cmd/sh
node model_repo.js
```

Ez a minta letölti a **ModelDiscovery** felületet, és kinyomtatja ezt a modellt a terminálon.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Lekérdezések futtatása az IoT Hubban képességmodellek és felületek alapján

Az IoT Hub lekérdezési nyelv támogatja, `HAS_INTERFACE` és `HAS_CAPABILITYMODEL` ahogy az alábbi példákban látható:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Digitális ikerútvonalak létrehozása

A megoldás értesítéseket kaphat a digitális ikermódosítási eseményekről. Ezekre az értesítésekre való előfizetéshez használja az [IoT Hub útválasztási szolgáltatás](../iot-hub/iot-hub-devguide-endpoints.md) átküldeni az értesítéseket egy végpontra, például blob storage, Event Hubs vagy a Service Bus-várólista.

Digitális ikerútvonal létrehozása:

1. Az Azure Portalon nyissa meg az IoT Hub-erőforrás.
1. Válassza **az Üzenetútválasztás lehetőséget.**
1. Az **Útvonalak** lapon válassza a **Hozzáadás gombot.**
1. Írjon be egy értéket a **Név** mezőbe, és válasszon **egy végpontot**. Ha még nem konfigurált végpontot, válassza **a Végpont hozzáadása**lehetőséget.
1. Az **Adatforrás** legördülő menüben válassza a **Digital Twin Change Events (Digitális ikermódosítási események) lehetőséget.**
1. Kattintson a **Mentés** gombra.

A következő JSON egy digitális ikermódosítási eseményt mutat be:

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

Most, hogy már megismerkedett az IoT Plug and Play eszközökkel kommunikáló szolgáltatási megoldásokkal, a javasolt következő lépés a [Modellfelderítés megismerése.](concepts-model-discovery.md)
