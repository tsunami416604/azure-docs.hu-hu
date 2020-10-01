---
title: Szolgáltatás-fejlesztői útmutató – IoT Plug and Play | Microsoft Docs
description: A IoT Plug and Play leírása a szolgáltatás-fejlesztőknek
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f082e4d4c6c71e460842f80a5aa17130b6a41279
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614223"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT Plug and Play Service – fejlesztői útmutató

A IoT Plug and Play lehetővé teszi, hogy intelligens eszközöket hozzon létre, amelyek az Azure IoT alkalmazásaiban hirdetik ki képességeiket. A IoT Plug and Play-eszközökhöz nincs szükség manuális konfigurálásra, ha az ügyfél a Plug and Play-kompatibilis alkalmazások IoT csatlakoztatja őket.

A IoT Plug and Play lehetővé teszi, hogy olyan eszközöket használjon, amelyeken bejelentette a modell AZONOSÍTÓját az IoT hub-ban. Az eszköz tulajdonságait és parancsait például közvetlenül elérheti.

Ha az IoT hubhoz csatlakoztatott IoT Plug and Play eszközt kíván használni, használja a IoT Service SDK-k vagy a IoT Hub REST API:

## <a name="service-sdks"></a>Szolgáltatási SDK-k

Használja az Azure IoT Service SDK-kat a megoldásban az eszközök és modulok használatához. Használhatja például a szolgáltatás SDK-kat a Twin tulajdonságok olvasására és frissítésére, valamint a parancsok meghívására. A támogatott nyelvek közé tartozik a C#, a Java, a Node.js és a Python.

A szolgáltatás SDK-k lehetővé teszik, hogy egy megoldásról, például egy asztali vagy webalkalmazásból férhessenek hozzá az eszköz adataihoz. A szolgáltatás SDK-k két névteret és objektummodell-modellt tartalmaznak, amelyekkel lekérheti a modell AZONOSÍTÓját:

- IOT hub szolgáltatás ügyfelének. Ez a szolgáltatás a modell AZONOSÍTÓját a Device Twin tulajdonságként teszi elérhetővé.

- Digitális Twins szolgáltatás-ügyfél. Az új digitális Twins API olyan magas szintű szerkezeteken működik, mint például az összetevők, a tulajdonságok és a digitális Twins-definíciós nyelvi modellt definiáló parancsok. A digitális Twin API-k megkönnyítik a megoldás-építők számára, hogy IoT Plug and Play megoldásokat hozzanak létre.

| Platform | IoT Hub szolgáltatási ügyfél | Digitális Twins szolgáltatás ügyfele |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [Dokumentáció](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> [Példák](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [Példák](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Dokumentáció](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [Példák](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [Példák](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Dokumentáció](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Minta](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Dokumentáció](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) <br/> [Minta](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [Dokumentáció](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Minta](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Dokumentáció](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) <br/> [Minta](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="rest-api"></a>REST API

Az alábbi példák a IoT Hub REST API használatával kommunikálnak a csatlakoztatott IoT Plug and Play eszközzel. Az API jelenlegi verziója: `2020-09-30` . Hozzáfűzés `?api-version=2020-09-30` a REST PI-hívásokhoz.

> [!NOTE]
> Az API jelenleg nem támogatja az ikrek modulját `digitalTwins` .

Ha a termosztátos eszközt hívja meg `t-123` , az eszköz összes tulajdonságát egy REST API Get hívással érheti el:

```REST
GET /digitalTwins/t-123
```

Ez a hívás tartalmazza a JSON-tulajdonságot az `$metadata.$model` eszköz által bejelentett modell-azonosítóval.

Az összes csatoló összes tulajdonsága a REST API sablonnal érhető el, ahol az az `GET /DigitalTwin/{device-id}` `{device-id}` eszköz azonosítója:

```REST
GET /digitalTwins/{device-id}
```

Közvetlenül is meghívhatja a IoT Plug and Play eszköz parancsait. Ha az `Thermostat` `t-123` eszközön található összetevő rendelkezik `restart` paranccsal, meghívhatja REST API post hívással:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Általánosabban a parancsok a REST API sablonnal is meghívhatók:

- `device-id`: az eszköz azonosítója.
- `component-name`: az eszköz képességei modellének megvalósítások szakaszában található felület neve.
- `command-name`: a parancs neve.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az eszközök modellezését, íme néhány további erőforrás:

- [Digitális Twins-definíciós nyelv (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C eszköz SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Modell összetevői](./concepts-components.md)
- [A DTDL authoring Tools telepítése és használata](howto-use-dtdl-authoring-tools.md)
