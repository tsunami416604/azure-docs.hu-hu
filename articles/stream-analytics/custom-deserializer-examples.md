---
title: A bemenetek olvasása bármilyen formátumban az Azure Stream Analytics .NET egyéni deszerializálóival
description: Ez a cikk ismerteti a szerializálási formátumot és azokat a felületeket, amelyek egyéni .NET deszerializálókat határoznak meg az Azure Stream Analytics felhő- és peremhálózati feladatokhoz.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845259"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Bevitel olvasása bármilyen formátumban a .NET egyéni deszerializálókkal

A .NET egyéni deszerializálók lehetővé teszik, hogy az Azure Stream Analytics-feladat a három beépített adatformátumon kívüli formátumokból [olvasson adatokat.](stream-analytics-parsing-json.md) Ez a cikk ismerteti a szerializálási formátumot és a felületeket, amelyek meghatározzák a .NET egyéni deszerializálók az Azure Stream Analytics felhő- és peremhálózati feladatok. Vannak példa deszerializálók protokoll puffer és CSV formátumban.

## <a name="net-custom-deserializer"></a>.NET egyéni deszerializáló

A következő kódminták azok az interfészek, amelyek `StreamDeserializer<T>`meghatározzák az egyéni deszerializálót és implementálják.

`UserDefinedOperator`az összes egyéni streamelési szolgáltató alaposztálya. Inicializálja `StreamingContext`, amely magában foglalja a diagnosztikai eszközök közzétételének mechanizmusát, amelyhez a deszerializálóval kapcsolatos problémákat hibakeresésre kell helyeznie.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

A következő kódrészlet a streamelési adatok deszerializálása. 

Az átadható 's `IStreamingDiagnostics` Initialize `UserDefinedOperator`metóduson keresztül átadható hibákat átadva kell kibocsátani. Minden kivételt a rendszer hibaként kezel, és a deszerializáló újra létrejön. Bizonyos számú hiba után a feladat sikertelen állapotba kerül.

`StreamDeserializer<T>`deszerializálja az adatfolyamot a típusú `T`objektumba. A következő feltételeknek kell teljesülniük:

1. T egy osztály vagy egy struct.
1. A T-ben minden nyilvános mező
    1. Az egyik [sbyte, byte, rövid, ushort, int, uint, hosszú, DateTime, string, float, double] vagy azok semlegesíthető megfelelői.
    1. Egy másik struct vagy osztály követi ugyanazokat a szabályokat.
    1. Azonos szabályokat követő típusú tömb. `T2`
    1. IList,`T2` ahol T2 követi ugyanazokat a szabályokat.
    1. Nincs rekurzív típusa.

A `stream` paraméter a szerializált objektumot tartalmazó adatfolyam. `Deserialize`példányok gyűjteményét `T` adja vissza.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`olyan környezetet biztosít, amely magában foglalja a felhasználói operátor diagnosztikájának közzétételére szolgáló mechanizmust.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`a felhasználó által definiált operátorok diagnosztikája, beleértve a szerializálót, a deszerializálót és a felhasználó által definiált függvényeket.

`WriteError`hibaüzenetet küld a diagnosztikai naplóknak, és elküldi a hibát a diagnosztikának.

`briefMessage`egy rövid hibaüzenet. Ez az üzenet megjelenik a diagnosztikában, és a termékcsapat hibakeresési célokra használja. Ne adjon meg bizalmas adatokat, és tartsa az üzenetet 200 karakternél kisebb karakternél

`detailedMessage`egy részletes hibaüzenet, amely csak a tárolóban lévő diagnosztikai naplókhoz kerül. Ennek az üzenetnek 2000 karakternél rövidebbnek kell lennie.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Példák deszerializáló

Ez a szakasz bemutatja, hogyan írhat egyéni deszerializálókat a Protobuf és a CSV számára. További példákért, például az Event Hub-rögzítés AVRO-formátumához látogasson el [az Azure Stream Analytics szolgáltatásba a GitHubon.](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)

### <a name="protocol-buffer-protobuf-format"></a>Protokollpuffer (Protobuf) formátum

Ez egy példa protokollpuffer-formátum használatával.

Tegyük fel a következő protokollpuffer-definíciót.

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

Futás `protoc.exe` a **Google.Protobuf.Tools** NuGet létrehoz egy .cs fájlt a meghatározást. A létrehozott fájl itt nem jelenik meg.

A következő kódrészlet a deszerializáló implementáció, feltéve, hogy a létrehozott fájl szerepel a projektben. Ez a megvalósítás csak egy vékony burkoló a létrehozott fájl felett.

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

A következő kódrészlet egy egyszerű CSV deszerializáló, amely szintén bemutatja a propagálási hibákat.

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

Minden Stream Analytics-bemenetnek van **szerializálási formátuma.** A bemeneti beállításokról további információt a [Bemeneti REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) dokumentációjában talál.

A rest API használatakor a következő Javascript-kód a .NET deszerializáló szerializálási formátumra mutat be példát:

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

`serializationClassName`kell egy osztály, `StreamDeserializer<T>`amely végrehajtja . Ezt a következő szakasz ismerteti.

## <a name="region-support"></a>Régió támogatása

Ez a funkció a következő régiókban érhető el:

* USA nyugati középső régiója
* Észak-Európa
* USA keleti régiója
* USA nyugati régiója
* USA 2. keleti régiója
* Nyugat-Európa

További [régiókhoz](https://aka.ms/ccodereqregion) is kérhet támogatást.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Mikor lesz elérhető ez a funkció az Összes Azure-régióban?

Ez a funkció [6 régióban](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support)érhető el. Ha szeretné ezt a funkciót egy másik régióban használni, [benyújthat egy kérelmet.](https://aka.ms/ccodereqregion) Az összes Azure-régió támogatása szerepel az ütemtervben.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>A GetMetadataPropertyValue függvényhez hasonló bemeneteimből is hozzáférhetek a MetadataPropertyValue értékhez?

Ez a funkció nem támogatott. Ha szüksége van erre a képességre, szavazhat erre a kérésre a [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Megoszthatom a deszerializáló implementációmat a közösséggel, hogy mások is élvezhessék?

Miután végrehajtotta a deszerializálóját, segíthet másoknak, ha megosztja azt a közösséggel. Küldje el a kódot az [Azure Stream Analytics GitHub tártárába.](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)

## <a name="next-steps"></a>Következő lépések

* [.NET egyéni deszerializálók az Azure Stream Analytics felhőalapú feladatokhoz](custom-deserializer.md)
