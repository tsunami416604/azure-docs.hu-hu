---
title: Modellek elemzése és ellenőrzése
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan elemezheti a DTDL-modelleket az elemző függvénytár használatával.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 69b52be3a3eca2ab48ed09f6401780ea033f223c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88723979"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Modellek elemzése és ellenőrzése a DTDL Parser Library-vel

Az Azure Digital Twins [modelljei](concepts-models.md) a JSON-ld-alapú digitális Twins Definition Language (DTDL) használatával vannak meghatározva. **Javasoljuk, hogy az Azure Digital Twins-példányba való feltöltés előtt ellenőrizze a modelleket offline állapotba.**

Ennek elvégzéséhez .NET ügyféloldali DTDL-elemzési függvénytár van megadva a következő NuGet: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

Az elemző függvénytárat közvetlenül a C#-kódban is használhatja, vagy használhatja a Language-agnosztikus kód minta projektjét, amely az elemző könyvtárra épül: [**DTDL validator minta**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

## <a name="use-the-dtdl-validator-sample"></a>A DTDL-érvényesítő minta használata

A [**DTDL-érvényesítő**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) olyan minta projekt, amely a DTDL érvényességének ellenőrzésére képes a modell dokumentumainak ellenőrzéséhez. A szolgáltatás a .NET Parser Library-re épül, és nyelvtől független. A minta hivatkozásnál a *zip letöltése* gombra kattintva kérheti le.

A forráskód példákat mutat be az elemző függvénytár használatára. Az érvényesítő mintát parancssori segédprogramként használhatja a DTDL-fájlok címtár-fájának ellenőrzéséhez. Emellett interaktív módot is biztosít.

A DTDL-érvényesítő minta mappájába tekintse meg a *readme.MD* -fájlt, amely útmutatást nyújt a minta önálló végrehajtható fájlba való csomagolásához.

Miután létrehozott egy önálló csomagot, és hozzáadta a végrehajtható fájlt az elérési úthoz, a következő paranccsal futtathatja a validatort egy konzolon a gépen:

```cmd/sh
DTDLValidator
```

Az alapértelmezett beállításokkal a minta az `*.json` aktuális könyvtárban és az összes alkönyvtárban található fájlokat fogja keresni. Azt is megteheti, hogy a következő lehetőséget is hozzáadja a minta kereséséhez a jelzett könyvtárban és a kiterjesztésű fájlok összes alkönyvtárában *. dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

A `-i` minta beállítását az interaktív mód megadására is felveheti:

```cmd/sh
DTDLValidator -i
```

A mintával kapcsolatos további információkért tekintse meg a forráskódot, vagy futtassa a parancsot `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>A .NET Parser Library használata 

A [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) függvénytár modell hozzáférést biztosít a DTDL-definícióhoz, amely lényegében a DTDL C#-reflexiójának megfelelőként működik. Ez a könyvtár az [Azure Digital Twins SDK](how-to-use-apis-sdks.md)-val függetlenül is használható, különösen a DTDL érvényesítéséhez egy vizualizációban vagy szövegszerkesztőben. A modell-definíciós fájlok érvényességének biztosításához hasznos, mielőtt feltölti őket a szolgáltatásba.

Az elemző függvénytár használatához a DTDL-dokumentumok készletét adja meg. Ezeket a modelleket általában a szolgáltatásból kell lekérnie, de előfordulhat, hogy helyileg is elérhetővé válik, ha az ügyfél felelős volt a szolgáltatásnak az első helyen való feltöltéséhez. 

Itt látható az elemző használatának általános munkafolyamata:
1. Egy vagy több DTDL-dokumentum beolvasása a szolgáltatásból.
2. Adja át a visszaadott, memóriában tárolt DTDL-dokumentumokat az elemzőnek.
3. Az elemző ellenőrzi az átadott dokumentumok készletét, és részletes hibaüzeneteket ad vissza. Ez a képesség szerkesztői helyzetekben hasznos.
4. A dokumentumkészlet által tartalmazott modellek elemzésének folytatásához használja az elemző API-kat. 

Az elemző képességei a következők:
* Szerezze be az összes megvalósított modell felületét (az interfész `extends` szakaszának tartalmát).
* A modellben deklarált összes tulajdonság, telemetria, parancs, összetevő és kapcsolat beolvasása. Ez a parancs a definíciókban szereplő összes metaadatot is beolvassa, és figyelembe veszi az öröklést ( `extends` szakaszt).
* Az összes összetett modell definíciójának beolvasása.
* Annak megállapítása, hogy egy modell kiosztható-e egy másik modellből.

> [!NOTE]
> A [IoT Plug and Play-(PNP-)](../iot-pnp/overview-iot-plug-and-play.md) eszközök kis szintaxist használnak a funkciók leírására. Ez a szintaxis az Azure digitális Ikrekben használt DTDL szemantikailag kompatibilis részhalmaza. Az elemző függvénytár használatakor nem kell tudnia, hogy melyik szintaxis-változatot használták a DTDL létrehozásához a digitális Twin számára. Alapértelmezés szerint az elemző a PnP és az Azure Digital Twins szintaxisának is ugyanazt a modellt fogja visszaadni.

### <a name="code-with-the-parser-library"></a>Kód az elemző könyvtárral

Az elemzési függvénytárat közvetlenül is használhatja olyan dolgokhoz, mint például a saját alkalmazásban lévő modellek ellenőrzése, vagy dinamikus, modell-vezérelt felhasználói felület, irányítópultok és jelentések létrehozása.

Az alábbi elemzési kód támogatásához vegye fontolóra az Azure Digital Twins-példányban definiált több modellt:

> [!TIP] 
> A `dtmi:com:contoso:coffeeMaker` modell a *képesség modell* szintaxisát használja, amely azt jelenti, hogy a szolgáltatásba való telepítése egy olyan PnP-eszköz csatlakoztatásával történik, amely kiteszi ezt a modellt.

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

Az alábbi kód bemutatja, hogyan használható az elemző függvénytár a következő definíciók megjelenítéséhez a C#-ban:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>További lépések

Ha befejezte a modellek írását, tekintse meg az DigitalTwinsModels API-k használatával történő feltöltését ismertető témakört (és végezze el a többi felügyeleti műveletet):
* [*Útmutató: egyéni modellek kezelése*](how-to-manage-model.md)