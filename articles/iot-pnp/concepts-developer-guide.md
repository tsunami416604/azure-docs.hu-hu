---
title: Fejlesztői útmutató – IoT Plug and Play előzetes verzió | Microsoft Docs
description: A fejlesztők számára készült IoT-Plug and Play leírása
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f1860f9645a50789803d509a6a1ea98bc0ea1a9e
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950136"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>IoT Plug and Play előzetes verziójának fejlesztői útmutatója

A IoT Plug and Play Preview lehetővé teszi, hogy olyan intelligens eszközöket hozzon létre, amelyek az Azure IoT alkalmazásaiban hirdetik ki képességeiket. A IoT Plug and Play-eszközökhöz nincs szükség manuális konfigurálásra, ha az ügyfél a Plug and Play-kompatibilis alkalmazások IoT csatlakoztatja őket.

Ez az útmutató ismerteti azokat az alapvető lépéseket, amelyek szükségesek a [IoT Plug and Play konvenciókat](concepts-convention.md)követő eszközök létrehozásához, valamint az eszközkel való kommunikációhoz használható REST API-k létrehozásához.

IoT Plug and Play-eszköz létrehozásához kövesse a tézisek lépéseit:

1. Győződjön meg arról, hogy az eszköz vagy a MQTT vagy a MQTT over WebSockets protokoll használatával csatlakozik az Azure IoT Hubhoz.
1. Hozzon létre egy [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) modellt az eszköz leírásához. További információ: [az összetevők megértése a IoT Plug and Play-modellekben](concepts-components.md).
1. Frissítse eszközét, és jelentse be az `model-id` eszköz kapcsolatának részét.
1. Telemetria, tulajdonságok és parancsok implementálása a [IoT Plug and Play konvenciók](concepts-convention.md) használatával

Ha az eszköz megvalósítása elkészült, az [Azure IoT Explorer](howto-use-iot-explorer.md) segítségével ellenőrizheti, hogy az eszköz követi-e a IoT Plug and Play konvenciókat.

> [!Tip]
> A cikkben szereplő kódrészletek a C# nyelvet használják, de a fogalmak a C, a Python, a Node és a Java számára elérhető SDK-k bármelyikére alkalmazhatók.

## <a name="model-id-announcement"></a>Modell AZONOSÍTÓjának bejelentése

A modell AZONOSÍTÓjának bejelentéséhez az eszköznek tartalmaznia kell azt a kapcsolódási adatok között:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Az új `ClientOptions` túlterhelés a `DeviceClient` kapcsolatok inicializálásához használt összes módszernél elérhető.

A modell AZONOSÍTÓjának bejelentése az SDK-k következő verzióihoz lett hozzáadva

|SDK|Verzió|
|---|-------|
|C – SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|Csomópont|1.17.0|
|Python|2.1.4|

## <a name="dps-payload"></a>DPS-tartalom

A [Device kiépítési szolgáltatást (DPS)](../iot-dps/about-iot-dps.md) használó eszközök tartalmazhatják a `modelId` kiépítési folyamat során a következő JSON-adattartalommal használható eszközöket.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Telemetria, tulajdonságok és parancsok implementálása

A [IoT Plug and Play-modellek összetevőinek ismertetése](concepts-components.md)című témakörben leírtak szerint az eszköz-építőknek el kell dönteniük, hogy milyen összetevőket szeretnének használni az eszközeik leírásához. Az összetevők használatakor az eszközöknek az ebben a részben ismertetett szabályokat kell követniük.

### <a name="telemetry"></a>Telemetria

Az összetevők nélküli modellek nem igényelnek speciális tulajdonságot.

Az összetevők használatakor az eszközöknek egy üzenet tulajdonságot kell megadniuk az összetevő nevével:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Csak olvasható tulajdonságok

Az összetevők nélküli modellek nem igényelnek különleges szerkezetet:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

Az összetevők használatakor a tulajdonságokat az összetevő nevén belül kell létrehozni:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Írható tulajdonságok

Ezeket a tulajdonságokat beállíthatja az eszköz, vagy frissítheti a megoldás. Ha a megoldás frissíti a tulajdonságot, az ügyfél visszahívási értesítést kap a alkalmazásban `DeviceClient` . A IoT Plug and Play konvenciók követéséhez az eszköznek tájékoztatnia kell a szolgáltatást arról, hogy a tulajdonság sikeresen megérkezett.

#### <a name="report-a-writable-property"></a>Írható tulajdonság jelentése

Ha egy eszköz írható tulajdonságot jelent, tartalmaznia kell az `ack` egyezményekben definiált értékeket.

Írható tulajdonság bejelentése összetevők nélkül:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Egy írható tulajdonság egy összetevőből való jelentéséhez a Twin elemnek tartalmaznia kell egy jelölőt:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Előfizetés a kívánt tulajdonság frissítéseire

A szolgáltatások frissíthetik a csatlakoztatott eszközökön értesítést kiváltó kívánt tulajdonságokat. Ez az értesítés tartalmazza a frissített kívánt tulajdonságokat, beleértve a frissítést azonosító verziószámot is. Az eszközöknek a jelentett tulajdonságokkal megegyező üzenettel kell válaszolniuk `ack` .

Az összetevők nélküli modellek egyetlen tulajdonságot látnak, és létrehozzák a jelentett `ack` verziót a kapott verzióval:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Az eszköz Twin a kívánt és jelentett szakaszban található tulajdonságot jeleníti meg:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Az összetevőket tartalmazó modellek megkapják az összetevő nevével burkolt kívánt tulajdonságokat, és jelenteniük kell a `ack` jelentett tulajdonságot:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Az eszközök Twin összetevője a kívánt és jelentett szakaszt mutatja a következőképpen:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Parancsok

Az összetevők nélküli modellek a parancs nevét kapják meg, ahogy azt a szolgáltatás meghívja.

Az összetevőkkel rendelkező modellek megkapják az összetevővel és az `*` elválasztóval előtagozott parancsot.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Kérelmek és válaszok hasznos adatai

A parancsok a kérelem és a válasz hasznos adatai meghatározására használják a típusokat. Az eszköznek deszerializálnia kell a bejövő bemeneti paramétert, és szerializálnia kell a választ. Az alábbi példa bemutatja, hogyan implementálhat egy olyan parancsot, amely a hasznos adatokban definiált komplex típusokat tartalmazza:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

A következő kódrészletek azt mutatják be, hogyan valósítja meg egy eszköz a parancs definícióját, beleértve a szerializálás és a deszerializálás engedélyezéséhez használt típusokat is:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> A kérelem és a válasz neve nem szerepel a dróton továbbított szerializált adattartalomban.

## <a name="interact-with-the-device"></a>Az eszköz használata 

A IoT Plug and Play lehetővé teszi, hogy olyan eszközöket használjon, amelyeken bejelentette a modell AZONOSÍTÓját az IoT hub-ban. Az eszköz tulajdonságait és parancsait például közvetlenül elérheti.

Ha az IoT hubhoz csatlakoztatott IoT Plug and Play eszközt kíván használni, használja a IoT Hub REST API vagy az egyik IoT nyelvi SDK-t. Az alábbi példák a IoT Hub REST API használják. Az API jelenlegi verziója: `2020-05-31-preview` . Hozzáfűzés `?api-version=2020-05-31` a REST PI-hívásokhoz.

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

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az eszközök modellezését, íme néhány további erőforrás:

- [Digitális Twins-definíciós nyelv (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C eszköz SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Modell összetevői](./concepts-components.md)
