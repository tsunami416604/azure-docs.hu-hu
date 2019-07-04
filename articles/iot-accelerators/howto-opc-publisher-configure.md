---
title: Konfigurálja az OPC-közzétevő – Azure |} A Microsoft Docs
description: Az OPC-közzétevő konfigurálása
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: d262c83b0ae886806b8c4bce2375ffc10ad22c75
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450757"
---
# <a name="configure-opc-publisher"></a>Az OPC Publisher konfigurálása

Adja meg, hogy az OPC-közzétevő konfigurálhatja:

- Csomópont közzététele adatokat az OPC UA megváltozik.
- Az OPC UA események közzététele.
- A telemetriai adatok formátuma.

Konfigurálhatja az OPC-közzétevő konfigurációs fájlokat, vagy pedig metódust hívja.

## <a name="use-configuration-files"></a>Konfigurációs fájlok használata

Ez a szakasz ismerteti a beállítások konfigurálásához az OPC UA-csomópont közzététele a konfigurációs fájlok.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Egy konfigurációs fájl segítségével konfigurálhatja az adatok módosítások közzététele

A legegyszerűbben úgy konfigurálja az OPC UA-csomópontokon történő közzététele egy konfigurációs fájllal van. A konfigurációs fájl formátuma leírása itt található [publishednodes.json](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) a tárházban.

Konfigurációs fájl szintaxisával idővel megváltozott. Továbbra is az OPC-közzétevő beolvassa a régi formátumot, de a legújabb formátumba konvertálja azokat, ha ez a konfiguráció továbbra is fennáll.

Az alábbi példa bemutatja a konfigurációs fájl formátuma:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Egy konfigurációs fájl segítségével közzétételi események konfigurálása

OPC UA-események közzétételére, mint az adatok változásának ugyanazt a konfigurációs fájlt használja.

A következő példa bemutatja, hogyan által előállított, a közzétételhez konfigurálása a [SimpleEvents kiszolgáló](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server). A SimpleEvents kiszolgálón található a [OPC Foundation tárház](https://github.com/OPCFoundation/UA-.NETStandard) van:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Metódushívások használata

Ez a szakasz ismerteti a segítségével konfigurálhatja az OPC-közzétevő metódust hívja.

### <a name="configure-using-opc-ua-method-calls"></a>Az OPC UA-metódust hívja használatával konfigurálása

Az OPC-közzétevő magában foglalja az OPC UA-kiszolgáló, amely 62222 porton érhető el. Ha az állomásnév **közzétevő**, majd a végpont URI-ja: `opc.tcp://publisher:62222/UA/Publisher`.

Ennek a végpontnak az alábbi négy módszert mutatja:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>Az IoT Hub közvetlen metódust hívja használatával konfigurálja

Az OPC-közzétevő valósít meg a következő IoT Hub közvetlen metódust hívja:

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

A metódus kérelmek és válaszok hasznos JSON formátumban definiált [opcpublisher/HubMethodModel.cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs).

Ha egy ismeretlen metódust hívja meg a modulban, válaszol egy karakterlánccal, amely szerint a metódus nincs implementálva. Egy ismeretlen módszer arra, hogy a modul pingelni hívása.

### <a name="configure-username-and-password-for-authentication"></a>Felhasználónév és jelszó-hitelesítés konfigurálása

A hitelesítési mód állíthatja be az IoT Hub közvetlen metódust hívja. A hasznos adatokat kell tartalmaznia a tulajdonság **OpcAuthenticationMode** és a felhasználónevet és jelszót:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

A jelszó titkosítja az IoT Hub munkaterhelés ügyfél, és tárolja a közzétevő konfigurálása. Vissza a névtelen hitelesítés módosításához használja a következő hasznos módszer:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Ha a **OpcAuthenticationMode** tulajdonság nincs beállítva az adattartalomban szereplő, a hitelesítési beállítások nem változnak a konfigurációban.

## <a name="configure-telemetry-publishing"></a>Telemetriai adatok közzétételének konfigurálása

Az OPC-közzétevő közzétett csomópont egy értékre módosul, értesítést kap, amikor egy JSON formátumú üzenetet az IoT hubnak küldött állít elő.

A JSON konfigurációs fájl használatával formázott üzenet tartalma is beállíthatja. Ha nincsenek konfigurációs fájl meg van adva a `--tc` , egy alapértelmezett konfigurációs beállítással, amely kompatibilis a [csatlakoztatott gyár megoldásgyorsító](https://github.com/Azure/azure-iot-connected-factory).

Ha az OPC-közzétevő kötegelt üzenetek van konfigurálva, majd azok éppen küldi el a egy érvényes JSON-tömböt.

A következő forrásokból származó telemetria származtatott:

- A csomópontra az OPC-közzétevő csomópont-konfiguráció
- A **MonitoredItem** , amelynek az OPC-közzétevő értesítést kapott OPC UA-verem objektumához.
- Az értesítések, amelyek részletesen az adatmódosítás értéket az argumentumot.

A telemetriai adatokból, hogy a JSON-formázott üzenet a rendszer elhelyezi egy kijelölt ezek az objektumok tulajdonságait fontos. Ha további tulajdonságok van szüksége, az OPC-közzétevő kódbázis módosítani szeretné.

A konfigurációs fájl szintaxisa a következő:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is ommited (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

Most már megtanulta, hogyan konfigurálhatja az OPC-közzétevő,-e a javasolt következő lépésre megtudhatja, hogyan [futtassa az OPC-közzétevő](howto-opc-publisher-run.md).
