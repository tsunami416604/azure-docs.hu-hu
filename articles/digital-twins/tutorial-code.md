---
title: Ügyfélalkalmazás kódolása
titleSuffix: Azure Digital Twins
description: Oktatóanyag egy ügyfélalkalmazás minimális kódjának megírásához a .NET (C#) SDK használatával.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 52a22dd215769208b60f180b576ae5763d67eade
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723469"
---
# <a name="coding-with-the-azure-digital-twins-apis"></a>Kódolás az Azure Digital Twins API-kkal

Gyakori, hogy az Azure Digital Twins-szel dolgozó fejlesztők az Azure Digital Twins szolgáltatás példányaival együttműködve írhatnak be egy ügyfélalkalmazás-alkalmazást. Ez a fejlesztői témájú oktatóanyag bevezetést nyújt az Azure Digital Twins szolgáltatással való programozáshoz, amely az [Azure IoT Digital Twin ügyféloldali kódtárat használja a .net-hez (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Részletesen ismerteti a C#-konzol ügyfélalkalmazás lépésről lépésre történő írását.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag a telepítő és a projekthez használható parancssort használja. Ezért bármilyen Kódszerkesztő használatával megtekintheti a gyakorlatokat.

A kezdéshez szükséges lépések:
* Bármely Kódszerkesztő
* **.Net Core 3,1** a fejlesztői gépen. A .NET Core SDK ezen verzióját több platformra is letöltheti a [.net Core 3,1 letöltésével](https://dotnet.microsoft.com/download/dotnet-core/3.1).

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>Projekt beállítása

Ha készen áll az Azure Digital Twins-példánnyal való használatra, kezdje el beállítani az ügyfélalkalmazás-projektet. 

Nyisson meg egy parancssort vagy egy másik konzolablak-ablakot a gépen, és hozzon létre egy üres Project könyvtárat, ahol az oktatóanyag során szeretné tárolni a munkáját. Nevezze el a könyvtárat, amit szeretne (például *DigitalTwinsCodeTutorial*).

Navigáljon az új könyvtárba.

Egyszer a projekt könyvtárában hozzon létre egy üres .NET-konzol alkalmazás-projektet. A parancssori ablakban futtassa a következő parancsot egy minimális C#-projekt létrehozásához a konzolon:

```cmd/sh
dotnet new console
```

Ez több fájlt hoz létre a címtárban, például egy *program.cs* , ahol a kód nagy részét fogja írni.

Következő lépésként vegyen fel két szükséges függőséget az Azure Digital Twins-használathoz:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.1.1
```

Az első függőség az [Azure IoT Digital Twin ügyféloldali kódtára a .net-hez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). A második függőség olyan eszközöket biztosít, amelyek segítenek az Azure-beli hitelesítésben.

Tartsa megnyitva a parancssorablakot, ahogy az oktatóanyag során is használni fogja.

## <a name="get-started-with-project-code"></a>Első lépések a Project Code-ban

Ebben a szakaszban megkezdi a kód megírását az új alkalmazás-projekthez az Azure digitális Twins szolgáltatással való együttműködéshez. Az érintett műveletek a következők:
* Hitelesítés a szolgáltatáson keresztül
* Modell feltöltése
* Kifogási hibák
* Digitális ikrek létrehozása
* Kapcsolatok létrehozása
* Digitális ikrek lekérdezése

Létezik egy szakasz is, amely az oktatóanyag végén található teljes kódot mutatja. Ez a hivatkozásként használható a program elindításához.

A kezdéshez nyissa meg a *program.cs* fájlt bármilyen szerkesztőprogramban. Ehhez a következőhöz hasonló kódot kell megnéznie:

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

Először vegyen fel néhány `using` sort a kód elejére a szükséges függőségek lekéréséhez.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

Ezután adjon hozzá kódot ehhez a fájlhoz, hogy kitöltsön néhány funkciót. 

### <a name="authenticate-against-the-service"></a>Hitelesítés a szolgáltatással

Az alkalmazás első lépéseként hitelesítenie kell magát az Azure Digital Twins szolgáltatásban. Ezután létrehozhat egy szolgáltatás-ügyfél osztályt az SDK-függvények eléréséhez.

A hitelesítéshez három információra van szüksége:
* Az előfizetéshez tartozó *címtár (bérlő) azonosítója*
* Az Azure Digital Twins-példány beállításakor létrehozott *alkalmazás-(ügyfél-) azonosító*
* Az Azure Digital Twins-példány *állomásneve*

>[!TIP]
> Ha nem ismeri a *címtár-(bérlői) azonosítóját*, a következő parancs futtatásával kérheti le [Azure Cloud Shell](https://shell.azure.com):
> 
> ```azurecli-interactive
> az account show --query tenantId
> ```

A *program.cs*illessze be a következő kódot a "Hello, World!" alá. a metódus nyomtatási sora `Main` . Állítsa be az értéket az `adtInstanceUrl` Azure Digital Twins-példány *állomásneve*, az `clientId` alkalmazás- *azonosító*és `tenantId` a címtár- *azonosító*értékre.

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

Mentse a fájlt. 

Vegye figyelembe, hogy ez a példa egy interaktív böngészőbeli hitelesítő adatot használ:
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

Ez a hitelesítő adat a böngészőablak megnyitását eredményezi, és kéri az Azure-beli hitelesítő adatok megadását. 

>[!NOTE]
> Más típusú hitelesítő adatokkal kapcsolatos információkért tekintse meg a [Microsoft Identity platform hitelesítési kódtárainak](../active-directory/develop/reference-v2-libraries.md)dokumentációját.

A következő paranccsal futtassa a kódot a parancsablakban: 

```cmd/sh
dotnet run
```

Ezzel visszaállítja a függőségeket az első futtatás után, majd végrehajtja a programot. 
* Ha nem fordul elő hiba, a program kinyomtatja a *szolgáltatáshoz létrehozott ügyfelet, amely készen áll*.
* Mivel ebben a projektben még nincsenek hibák, ha bármilyen hiba történik, a kód kivételt fog látni.

### <a name="upload-a-model"></a>Modell feltöltése

Az Azure Digital Twins nem rendelkezik belső tartománybeli szókincstel. A környezetben az Azure Digital Twins-ban ábrázolt elemek típusai a **modellek**használatával definiálhatók. A [modellek](concepts-models.md) hasonlók az objektumorientált programozási nyelvekben található osztályokhoz. felhasználói sablonokat biztosítanak a [digitális ikrek](concepts-twins-graph.md) számára a későbbi követéshez és létrehozáshoz. A rendszer a **digitális Twins Definition Language (DTDL)** nevű JSON-szerű nyelven íródott.

Az Azure digitális Twins-megoldások létrehozásának első lépéseként legalább egy modellt DTDL-fájlban kell meghatározni.

Hozzon létre egy *SampleModel.js*nevű új *. JSON* fájlt a címtárban, ahol létrehozta a projektet. Illessze be a következő fájlt a szövegtörzsbe: 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> Ha a Visual studiót használja ehhez az oktatóanyaghoz, érdemes kijelölni az újonnan létrehozott JSON-fájlt, és a tulajdonság-ellenőrben a *Másolás a kimeneti könyvtárba* tulajdonságot úgy kell beállítani, hogy az *újabb* vagy a *Másolás Always*. Ez lehetővé teszi a Visual Studio számára, hogy megkeresse a JSON-fájlt az alapértelmezett elérési úttal, amikor az oktatóanyag többi részében futtatja a programot az **F5 billentyűvel** .

> [!TIP] 
> A DTDL érvényességének ellenőrzéséhez használhatja a Language-agnosztikus [DTDL-érvényesítő mintát](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) , amellyel ellenőrizhetők a modell dokumentumai. A szolgáltatás a DTDL-elemző könyvtárra épül, amelyről további információt a modellek elemzése [*és ellenőrzése*](how-to-parse-models.md)című témakörben olvashat.

Ezután adjon hozzá még egy kódot a *program.cs* az imént létrehozott modell feltöltéséhez az Azure Digital Twins-példányba.

Először adjon hozzá néhány `using` utasítást a fájl elejéhez:

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

Következő lépésként készítse elő az aszinkron metódusok használatát a C# Service SDK-ban úgy, hogy módosítja a `Main` metódus aláírását, hogy engedélyezze az aszinkron végrehajtást. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> `async`A használata nem feltétlenül szükséges, mivel az SDK az összes hívás szinkron verzióját is tartalmazza. Az oktatóanyag gyakorlata a használatával `async` .

A következő lépés az Azure Digital Twins szolgáltatással kommunikáló kód első kis része. Ez a kód betölti a lemezről létrehozott DTDL-fájlt, majd feltölti az Azure Digital Twins Service-példányba. 

Illessze be a következő kódot a korábban hozzáadott engedélyezési kód alá.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

A parancsablakban futtassa a programot a következő paranccsal: 

```cmd/sh
dotnet run
```
A "modell feltöltése" a kimenetben lesz kinyomtatva, de még nincs kimenet, amely azt jelzi, hogy a modellek feltöltése sikeres volt-e.

Ha olyan Print utasítást szeretne felvenni, amely azt jelzi, hogy a modellek feltöltése ténylegesen megtörtént-e, adja hozzá a következő kódot közvetlenül az előző szakasz után:

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Mielőtt újra futtatja a programot az új kód teszteléséhez, emlékezteti arra, hogy a program legutóbbi futtatásakor már feltöltötte a modellt. Az Azure Digital Twins nem teszi lehetővé, hogy kétszer töltse fel ugyanazt a modellt, ezért a program újbóli futtatásakor a rendszer kivételt vár.

Most futtassa újra a programot ezzel a paranccsal a parancsablakban:

```cmd/sh
dotnet run
```

A program kivételt jelez. Ha olyan modellt próbál meg feltölteni, amely már fel van töltve, a szolgáltatás "hibás kérés" hibát ad vissza a REST APIon keresztül. Ennek eredményeképpen az Azure-beli digitális Twins ügyféloldali SDK kivételt jelez minden szolgáltatáshoz, amely nem a sikertől függ. 

A következő szakasz a kivételeket tárgyalja, és azt, hogyan kezelheti őket a kódban.

### <a name="catch-errors"></a>Fogási hibák

Ahhoz, hogy a program összeomlik, a modell feltöltési kódjában adhat hozzá kivételi kódot. A meglévő ügyfél hívásának becsomagolása `client.CreateModelsAsync` egy try/catch kezelőben, például:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

Ha most futtatja a programot a `dotnet run` parancssori ablakban, látni fogja, hogy hibakódot kap vissza. A kimenet valahogy így néz ki:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"DocumentAlreadyExists","message":"A document with same identifier already exists.","details":[]}}

Headers:
api-supported-versions: REDACTED
Date: Tue, 05 May 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

Ettől a ponttól kezdve az oktatóanyag a kipróbálási és a fogási kezelőben a szolgáltatási metódusok összes hívását lezárja.

### <a name="create-digital-twins"></a>Digitális ikrek létrehozása

Most, hogy feltöltött egy modellt az Azure digitális Twins-ba, a modell definíciójában **digitális ikreket**hozhat létre. A [digitális ikrek](concepts-twins-graph.md) egy modell példányai, és az üzleti környezetben található entitásokat képviselik, például a farmon lévő érzékelők, a helyiségek egy épületben vagy egy autóban található fények. Ez a szakasz néhány digitális ikreket hoz létre a korábban feltöltött modell alapján.

Vegyen fel egy új `using` utasítást a tetején, mivel szüksége lesz a beépített .net JSON-szerializáló elemre a következőben `System.Text.Json` :

```csharp
using System.Text.Json;
using Azure.DigitalTwins.Core.Serialization;
```

Ezután adja hozzá a következő kódot a metódus végéhez a `Main` modell alapján három digitális ikrek létrehozásához és inicializálásához.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

A parancsablakban futtassa a programot a paranccsal `dotnet run` . Ezután ismételje meg a program ismételt futtatását. 

Figyelje meg, hogy az ikrek létrehozásakor a második alkalommal nem fordul elő hiba, még akkor is, ha az ikrek már léteznek az első futtatása után. A modell-létrehozástól eltérően a kettős létrehozás, a REST szinten, egy *put* hívás *upsert* szemantikai feladatokkal. Ez azt jelenti, hogy ha egy iker már létezik, az újbóli létrehozását követően a rendszer csak lecseréli azt. Nem szükséges hiba.

### <a name="create-relationships"></a>Kapcsolatok létrehozása

Ezután létrehozhat **kapcsolatokat** a létrehozott ikrek között, és összekapcsolhatja őket egy **különálló gráfban**. A [két gráf](concepts-twins-graph.md) a teljes környezet ábrázolására szolgál.

Kapcsolatok létrehozásához vegyen fel egy utasítást a `using` kapcsolati alaptípushoz az SDK-ban: Ha már hozzá van adva, hagyja ki ezt a lehetőséget.
```csharp
using Azure.DigitalTwins.Core.Serialization;
```

Ezután adjon hozzá egy új statikus metódust a `Program` osztályhoz a `Main` metódus alá:
```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Ezután adja hozzá a következő kódot a metódus végéhez a `Main` kód meghívásához `CreateRelationship` :
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

A parancsablakban futtassa a programot a paranccsal `dotnet run` .

Vegye figyelembe, hogy az Azure Digital Twins nem teszi lehetővé, hogy kapcsolatot hozzon létre, ha már létezik ugyanezzel az AZONOSÍTÓval, tehát ha többször futtatja a programot, akkor a kapcsolatok létrehozásával kapcsolatos kivételek jelennek meg. Ez a kód elveszi a kivételeket, és figyelmen kívül hagyja őket. 

### <a name="list-relationships"></a>Kapcsolatok listázása

A hozzáadni kívánt következő kód lehetővé teszi, hogy megtekintse a létrehozott kapcsolatok listáját.

Adja hozzá a következő új metódust a `Program` osztályhoz:

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

Ezután adja hozzá a következő kódot a metódus végéhez a `Main` kód meghívásához `ListRelationships` :

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

A parancsablakban futtassa a programot a paranccsal `dotnet run` . Ekkor meg kell jelennie a létrehozott kapcsolatok listájának.

### <a name="query-digital-twins"></a>Digitális ikrek lekérdezése

Az Azure Digital Twins egyik fő funkciója, hogy könnyen és hatékonyan [lekérdezheti](concepts-query-language.md) a Twin Graphot a környezettel kapcsolatos kérdések megválaszolásához.

Az oktatóanyagban felvenni kívánt kód utolsó szakasza egy lekérdezést futtat az Azure Digital Twins-példányon. Az ebben a példában használt lekérdezés az összes digitális ikreket adja vissza a példányban.

Adja hozzá a következő kódot a metódus végéhez `Main` :

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

A parancsablakban futtassa a programot a paranccsal `dotnet run` . Ebben a példányban az összes digitális Twins megjelenik a kimenetben.

## <a name="complete-code-example"></a>Példa teljes kódra

Az oktatóanyag ezen pontján egy teljes ügyfélalkalmazás van, amely képes az Azure digitális Twins alapszintű műveleteinek elvégzésére. Az alábbi listában a *program.cs* program teljes kódja látható:

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Models;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = "contains"
            };
        
            try
            {
                string relId = $"{srcId}-contains->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
 
Az oktatóanyagban használt példány újra felhasználható a következő oktatóanyagban, [*oktatóanyag: az alapokat egy minta ügyfélalkalmazás segítségével tárja fel*](tutorial-command-line-app.md). Ha továbbra is a következő oktatóanyagot tervezi, megtarthatja az itt beállított Azure digitális Twins-példányt.
 
Ha már nincs szüksége az oktatóanyagban létrehozott erőforrásokra, a következő lépésekkel törölheti őket.

A [Azure Cloud Shell](https://shell.azure.com)használatával törölheti az erőforráscsoport összes Azure-erőforrását az az [Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal. Ezzel eltávolítja az erőforráscsoportot és az Azure Digital Twins-példányt.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

Nyisson meg egy Azure Cloud Shell, és futtassa a következő parancsot az erőforráscsoport és a benne található összes elem törléséhez.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ezután törölje az ügyfélalkalmazás számára a következő paranccsal létrehozott Azure Active Directory-alkalmazás regisztrációját:

```azurecli
az ad app delete --id <your-application-ID>
```

Végül törölje a helyi gépen létrehozott Project mappát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozta a .NET-konzol ügyfélprogramját a semmiből. Az ügyfélalkalmazás kódját az Azure Digital Twins-példányon végzett alapszintű műveletek végrehajtásához írta.

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan teheti meg az ilyen minta ügyfélprogramot: 

> [!div class="nextstepaction"]
> [*Oktatóanyag: az alapok megismerése egy minta ügyfélalkalmazás alkalmazásával*](tutorial-command-line-app.md)

Azt is megteheti, hogy az oktatóanyagban írt kóddal több felügyeleti műveletet tanul, vagy megkezdi a koncepció dokumentációját, hogy többet tudjon meg az oktatóanyagban használt elemekről.
* [*Útmutató: egyéni modellek kezelése*](how-to-manage-model.md)
* [*Fogalmak: egyéni modellek*](concepts-models.md)
