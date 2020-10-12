---
title: A digitális Twins modell elemző ismertetése | Microsoft Docs
description: Fejlesztőként megtudhatja, hogyan használhatja a DTDL-elemzőt a modellek ellenőrzésére
author: rido-min
ms.author: rmpablos
ms.date: 04/29/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: peterpr
ms.openlocfilehash: 20c4452a32c791f33e08c883d8cec89a345ab188
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87352284"
---
# <a name="understand-the-digital-twins-model-parser"></a>A Digital Twins modellelemző megismerése

A digitális ikrek definíciós nyelvét (DTDL) a [DTDL-specifikáció](https://github.com/Azure/opendigitaltwins-dtdl)írja le. A felhasználók a _digitális Twins Model Parser_ NuGet-csomagot használva ellenőrizhetik és lekérhetik a több fájlban definiált modelleket.

## <a name="install-the-dtdl-model-parser"></a>A DTDL Model Parser telepítése

Az elemző a NuGet.org-ben érhető el az azonosító: [Microsoft. Azure. DigitalTwins. Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Az elemző telepítéséhez használja a kompatibilis NuGet csomagkezelő (például a Visual Studióban vagy a parancssori felület) egyikét `dotnet` .

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

## <a name="use-the-parser-to-validate-a-model"></a>A modell érvényesítése az elemző használatával

Az érvényesíteni kívánt modell a JSON-fájlokban leírt egy vagy több csatolóból állhat. Az elemző használatával betöltheti az adott mappában található összes fájlt, és az elemző használatával ellenőrizheti az összes fájlt egészként, beleértve a fájlok közötti hivatkozásokat is:

1. Hozzon létre egy `IEnumerable<string>` listát az összes modell tartalmáról:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. A és a `ModelParser` hívás példánya `ParseAsync` :

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Ellenőrzési hibák ellenőrzése. Ha az elemző hibát talál, a `AggregateException` részletes hibaüzenetek listáját tartalmazza:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (AggregateException ae)
    {
        foreach (var e in ae.InnerExceptions)
        {
            Console.WriteLine(e.Message);
        }
    }
    ```

1. Vizsgálja meg a `Model` . Ha az ellenőrzés sikeres, a Model Parser API használatával ellenőrizheti a modellt. A következő kódrészlet azt mutatja be, hogyan lehet megismételni az összes elemzett modellt, és megjeleníti a meglévő tulajdonságokat:

    ```csharp
    foreach (var m in parseResult)
    {
        Console.WriteLine(m.Key);
        foreach (var item in m.Value.AsEnumerable<DTEntityInfo>())
        {
            var p = item as DTInterfaceInfo;
            if (p!=null)
            {
                Console.WriteLine($"\t{p.Id}");
                Console.WriteLine($"\t{p.Description.FirstOrDefault()}");
            }
            Console.WriteLine("--------------");
        }
    }
    ```

## <a name="next-steps"></a>További lépések

A cikkben áttekintett Model Parser API számos forgatókönyvet tesz lehetővé a DTDL-modelltől függő feladatok automatizálásához és érvényesítéséhez. Például dinamikusan hozhat létre felhasználói felületet a modellben található információk alapján.
