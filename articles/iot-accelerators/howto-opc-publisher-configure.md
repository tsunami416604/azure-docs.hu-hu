---
title: Az OPC Kiadó konfigurálása - Azure | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogyan konfigurálható az OPC Publisher az OPC UA-csomópont adatváltozásainak, az OPC UA-események közzétételének és a telemetriai formátum ának megadásához.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0ebbf0d41c05f71c571d9665903ba4ba44f71bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198803"
---
# <a name="configure-opc-publisher"></a>Az OPC Publisher konfigurálása

Az OPC Publisher konfigurálható a következők megadására:

- Az OPC UA-csomópont adatai közzétételre módosulnak.
- A közzéteve OPC UA-események.
- A telemetriai formátum.

Az OPC Publisher konfigurálható konfigurációs fájlokkal vagy metódushívásokkal.

## <a name="use-configuration-files"></a>Konfigurációs fájlok használata

Ez a szakasz az OPC UA-csomópont közzétételkonfigurációs fájlokkal történő konfigurálásának lehetőségeit ismerteti.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Közzétételi adatok módosításának konfigurálása konfigurációs fájllal

Az OPC UA-csomópontok közzétételének legegyszerűbb módja egy konfigurációs fájl. A konfigurációs fájlformátum a [tárházban található publishednodes.json](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) fájlban van dokumentálva.

A konfigurációs fájl szintaxisa az idő múlásával megváltozott. Az OPC Publisher továbbra is beolvassa a régi formátumokat, de a konfiguráció megőrzésekor a legújabb formátumba konvertálja azokat.

A következő példa a konfigurációs fájl formátumát mutatja be:

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

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Közzétételi események konfigurálása konfigurációs fájllal

Az OPC UA-események közzétételéhez ugyanazt a konfigurációs fájlt kell használnia, mint az adatmódosításokhoz.

A következő példa bemutatja, hogyan konfigurálható a közzététel a [SimpleEvents kiszolgáló](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server)által létrehozott eseményekhez. A SimpleEvents kiszolgáló az [OPC Foundation tárházban](https://github.com/OPCFoundation/UA-.NETStandard) található:

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

Ez a szakasz az OPC Publisher konfigurálásához használható metódushívásokat ismerteti.

### <a name="configure-using-opc-ua-method-calls"></a>Konfigurálás OPC UA metódushívásokkal

Az OPC Publisher tartalmaz egy OPC UA kiszolgálót, amely a 62222-es porton érhető el. Ha az állomásnév **közzétevő,** akkor a `opc.tcp://publisher:62222/UA/Publisher`végpont URI-je: .

Ez a végpont a következő négy módszert teszi ki:

- PublishNode (Közzétételnode)
- Közzététel visszavonásaNode
- GetPublishedNodes
- IoT hubdirectmetódus

### <a name="configure-using-iot-hub-direct-method-calls"></a>Konfigurálás az IoT Hub közvetlen metódushívásai használatával

Az OPC Publisher a következő IoT Hub-közvetlen metódushívásokat valósítja meg:

- PublishNodes
- Közzététel visszavonásaNodes
- Minden csomópont közzétételének visszavonása
- Getconfiguredendpontok
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog (GetDiagnosticLog) napló
- GetDiagnosticStartupLog
- ExitApplication alkalmazás
- GetInfo (GetInfo)

A metóduskérés és -válaszok JSON-hasznos adatának formátumát az [opcpublisher/HubMethodModel.cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs)határozza meg.

Ha meghívja egy ismeretlen metódust a modulon, akkor egy karakterlánccal válaszol, amely azt mondja, hogy a metódus nincs megvalósítva. A modul pingelésének módjaként ismeretlen metódust hívhat meg.

### <a name="configure-username-and-password-for-authentication"></a>Felhasználónév és jelszó konfigurálása hitelesítéshez

A hitelesítési mód az IoT Hub közvetlen metódushívásain keresztül állítható be. A hasznos adatnak tartalmaznia kell az **OpcAuthenticationMode** tulajdonságot, valamint a felhasználónevet és a jelszót:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

A jelszót az IoT Hub számítási feladat ügyfele titkosítja, és a közzétevő konfigurációjában tárolja. Ha a hitelesítést névtelenre szeretné módosítani, használja a következő hasznos adattal rendelkező módszert:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Ha az **OpcAuthenticationMode** tulajdonság nincs beállítva a hasznos adatban, a hitelesítési beállítások változatlanok maradnak a konfigurációban.

## <a name="configure-telemetry-publishing"></a>Telemetriai közzététel konfigurálása

Amikor az OPC Publisher értesítést kap egy közzétett csomópont értékváltozásáról, létrehoz egy JSON-formátumú üzenetet, amelyet az IoT Hubnak küld.

A JSON formátumú üzenet tartalmát konfigurációs fájl segítségével állíthatja be. Ha a beállítással nincs `--tc` megadva konfigurációs fájl, a rendszer olyan alapértelmezett konfigurációt használ, amely kompatibilis a [Csatlakoztatott gyári megoldásgyorsítóval.](https://github.com/Azure/azure-iot-connected-factory)

Ha az OPC Publisher úgy van beállítva, hogy kötegelje az üzeneteket, akkor érvényes JSON-tömbként lesz elküldve.

A telemetria a következő forrásokból származik:

- A csomópont OPC Publisher-csomópontkonfigurációja
- Annak az OPC UA veremnek a **FigyeltElem** objektuma, amelyről az OPC Publisher értesítést kapott.
- Az értesítésnek átadott argumentum, amely részletezi az adatérték változását.

A JSON-formátumú üzenetbe helyezett telemetriai adatok ezeknek az objektumoknak a fontos tulajdonságai. Ha több tulajdonságra van szüksége, módosítania kell az OPC Publisher kódbázisát.

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
            // If this key is omitted (which is the default), then no regex matching is done
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

Most, hogy megtanulta az OPC Publisher konfigurálását, a javasolt következő lépés az [OPC Publisher futtatásának megismerése.](howto-opc-publisher-run.md)
