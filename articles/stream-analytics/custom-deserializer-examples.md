---
title: .NET-deszerializálók használata Azure Stream Analytics feladatokhoz
description: Ez a cikk ismerteti a szerializálási formátumot, valamint azokat az interfészeket, amelyek egyéni .NET-deszerializálók definiálását Azure Stream Analytics Felhőbeli és peremhálózati feladatok számára.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: f1452e56054948edffc6e9b3c98fa48d2589cb2a
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024940"
---
# <a name="use-net-deserializers-for-azure-stream-analytics-jobs"></a>.NET-deszerializálók használata Azure Stream Analytics feladatokhoz

Az egyéni .NET-deszerializálók lehetővé teszik, hogy az Azure Stream Analytics-feladatok a három [beépített adatformátumon](stream-analytics-parsing-json.md)kívülről is olvassák az adatformátumokat. Ez a cikk ismerteti a szerializálási formátumot, valamint azokat az interfészeket, amelyek egyéni .NET-deszerializálók definiálását Azure Stream Analytics Felhőbeli és peremhálózati feladatok számára. A protokoll-és CSV-formátumhoz is vannak példa deszerializálók.

## <a name="custom-net-deserializer"></a>Egyéni .NET deszerializáló

A következő kódrészletek az egyéni deszerializáló definiáló felületek, valamint a `StreamDeserializer<T>`implementálása.

a `UserDefinedOperator` az összes egyéni adatfolyam-kezelő alaposztálya. A `StreamingContext`inicializálja a környezetet, amely magában foglalja a diagnosztika közzétételi mechanizmusát, amelynek a deszerializáló hibáit kell elhárítani.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

A következő kódrészlet a streaming-adatátvitelek deszerializálása. 

A kihagyható hibákat a `UserDefinedOperator`inicializálási metódusával `IStreamingDiagnostics` átadott módon kell kiállítani. A rendszer az összes kivételt hibákat fogja kezelni, és a deszerializáló újra létrejön. Bizonyos számú hiba után a feladatok sikertelen állapotba kerülnek.

`StreamDeserializer<T>` `T`típusú objektummá deszerializál egy adatfolyamot. A következő feltételeknek kell teljesülniük:

1. A T egy osztály vagy egy struct.
1. A T összes nyilvános mezője a következők egyike:
    1. Az egyik a [sbyte érték, a byte, a short, a ushort, az int, a uint, a Long, a DateTime, a string, a float, a Double] vagy a nullával egyenértékű.
    1. Egy másik Struct vagy osztály, amely ugyanezeket a szabályokat követi.
    1. `T2` típusú tömb, amely ugyanazokat a szabályokat követi.
    1. IList`T2`, ahol a T2 ugyanazokat a szabályokat követi.
    1. Nem rendelkezik rekurzív típusokkal.

A `stream` paraméter a szerializált objektumot tartalmazó adatfolyam. `Deserialize` `T` példányok gyűjteményét adja vissza.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` olyan kontextust biztosít, amely magában foglalja a felhasználói kezelő diagnosztika-közzétételi mechanizmusát.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` a felhasználó által definiált operátorok diagnosztikája, többek között a szerializáló, a deszerializáló és a felhasználó által definiált függvények.

`WriteError` hibaüzenetet ír a diagnosztikai naplókhoz, és a hibát a diagnosztika számára küldi el.

a `briefMessage` rövid hibaüzenetet jelenít meg. Ez az üzenet a diagnosztika szolgáltatásban jelenik meg, és a termék csapata hibakeresési célokat szolgál. Ne tartalmazzon bizalmas adatokat, és tartsa meg a 200 karakternél rövidebb üzenetet

`detailedMessage` egy részletes hibaüzenet, amely csak a tárolóban lévő diagnosztikai naplókba kerül. Az üzenetnek 2000 karakternél rövidebbnek kell lennie.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Deszerializáló – példák

Ez a szakasz bemutatja, hogyan írhat egyéni deszerializált a Protopuf és a CSV-hez. További példákért látogasson el [Azure stream Analytics a githubon](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Protokoll-puffer (Protopuf) formátuma

Ez egy példa a protokoll pufferének formátumára.

Tegyük fel a következő protokoll-puffer definícióját.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

`protoc.exe` futtatása a **Google. protopuf. Tools** NuGet. cs fájlt hoz létre a definícióval. A létrehozott fájl itt nem jelenik meg.

A következő kódrészlet a deszerializáló implementációja, feltételezve, hogy a létrehozott fájl szerepel a projektben. Ez a megvalósítás csak egy vékony burkoló a generált fájlon.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

A következő kódrészlet egy egyszerű CSV-deszerializáló, amely a terjesztési hibákat is bemutatja.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>A REST API-k szerializálási formátuma

Minden Stream Analytics-bemenet **szerializálási formátummal**rendelkezik. A bemeneti beállításokkal kapcsolatos további információkért tekintse meg a [input REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) dokumentációját.

A következő JavaScript-kód példa a .NET deszerializáló szerializálási formátumára a REST API használatakor:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

a `serializationClassName` `StreamDeserializer<T>`t megvalósító osztálynak kell lennie. Ezt a következő szakaszban ismertetjük.

## <a name="region-support"></a>Régió támogatása

Ez a funkció a következő régiókban érhető el:

* USA nyugati középső régiója (elérhető)
* Észak-Európa (elérhető)
* USA keleti régiója (elérhető)
* USA nyugati régiója (hamarosan elérhető)
* USA 2. keleti régiója (hamarosan kivezetve)
* Nyugat-Európa (hamarosan kivezetve)

További régiók [támogatását is kérheti](https://aka.ms/ccodereqregion) .

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Mikor lesz elérhető ez a szolgáltatás az összes Azure-régióban?

Ez a funkció 6 régióban érhető el (#region támogatással). Ha egy másik régióban szeretné használni ezt a funkciót, [elküldhet egy kérelmet](https://aka.ms/ccodereqregion). Az összes Azure-régió támogatása az ütemterv szerint történik.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Hozzáférhetek a MetadataPropertyValue a saját bemenetekhez a GetMetadataPropertyValue függvényhez hasonló módon?

Ez a funkció nem támogatott. Ha erre a képességre van szüksége, szavazzon erre a kérelemre a [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Megoszthatom a deszerializáló implementációját a Közösséggel, hogy mások is részesülhessenek?

A deszerializáló megvalósítását követően a Közösséggel megoszthatja másokkal. Küldje be a kódot a [Azure stream Analytics GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)-tárházba.

## <a name="next-steps"></a>Következő lépések

* [Egyéni .NET-deszerializálók Azure Stream Analytics felhőalapú feladatokhoz](custom-deserializer.md)
