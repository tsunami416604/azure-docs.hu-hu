---
title: Egyéni modellek kezelése
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan hozhat létre, szerkeszthet és törölhet modelleket az Azure digitális Twins-n belül.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 7d9fd0386f13f6c3e6df7b9d8bcf120b55c6446d
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362781"
---
# <a name="manage-azure-digital-twins-models"></a>Azure digitális Twins-modellek kezelése

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

A [**DigitalTwinsModels API**](how-to-use-apis-sdks.md)-k, a [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)vagy az [Azure Digital Twins CLI](how-to-use-cli.md)használatával felügyelheti a [modelleket](concepts-models.md) , amelyeket az Azure digitális Twins-példánya tud. 

A kezelési műveletek közé tartozik a modellek feltöltése, ellenőrzése, beolvasása és törlése. 

## <a name="create-models"></a>Modellek létrehozása

Az Azure Digital Twins modelljei a DTDL-ben íródnak, és *. JSON* fájlként lesznek mentve. Létezik egy DTDL- [bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) is a [Visual Studio Code](https://code.visualstudio.com/)-hoz, amely szintaxis-ellenőrzést és egyéb funkciókat biztosít a DTDL dokumentumok írásához.

Vegyünk például egy olyan példát, amelyben a kórház a saját szobáit szeretné digitálisan kijelölni. Minden helyiség tartalmaz egy intelligens SOAP-adagolót, amely figyeli a kézmosást, és érzékelőkkel figyeli a forgalmat a helyiségen keresztül.

A megoldás első lépéseként létre kell hoznia a kórházi aspektusait képviselő modelleket. Ebben a forgatókönyvben az alábbihoz hasonló kórházi helyiségeket lehet leírta:

```json
{
  "@id": "dtmi:com:contoso:PatientRoom;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "displayName": "Patient Room",
  "contents": [
    {
      "@type": "Property",
      "name": "visitorCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashPercentage",
      "schema": "double"
    },
    {
      "@type": "Relationship",
      "name": "hasDevices"
    }
  ]
}
```

> [!NOTE]
> Ez egy minta törzs egy. JSON-fájlhoz, amelyben a modell definiálva és mentve lett, az ügyfél-projekt részeként való feltöltéshez. A REST API hívást a másik oldalon a modell definícióinak egy tömbjét veszi igénybe (amely a `IEnumerable<string>` .net SDK-ban van leképezve). Ha ezt a modellt közvetlenül a REST API szeretné használni, szögletes zárójelekkel.

Ez a modell definiálja a beteg helyiség nevét és egyedi AZONOSÍTÓját, valamint a látogatók és a kézmosás állapotát jelölő tulajdonságokat (ezek a számlálók frissülnek a mozgásérzékelők és az intelligens SOAP-adagolók között, és együtt használhatók a *kézmosás százalékos* tulajdonságainak kiszámításához). A modell egy kapcsolati *hasDevices*is definiál, amely az adott *szobatípus* [alapján a](concepts-twins-graph.md) tényleges eszközökre való kapcsolódáshoz használható.

Ezt a módszert követve megadhatja a kórházi részleg, a zónák vagy a kórház modelljeit.

> [!TIP]
> Létezik egy ügyféloldali kódtár, amely a DTDL elemzéséhez és érvényesítéséhez használható. Létrehoz egy C#-objektummodell a DTDL-tartalomból, amely modellen alapuló fejlesztési forgatókönyvekben használható, például felhasználói felületi elemek létrehozásához. Ezt a kódtárat is használhatja annak biztosítására, hogy a modellek ne legyenek szintaktikai hibák a feltöltésük előtt. További információ erről a könyvtárról és a DTDL-érvényesítő számára készült mintához való hozzáférésről [: útmutató: a modellek elemzése és érvényesítése](how-to-use-parser.md).

## <a name="manage-models-with-apis"></a>Modellek kezelése API-kkal.

A következő részben bemutatjuk, hogyan végezheti el a különböző modell-kezelési műveleteket az [Azure Digital Twins API-k és SDK-](how-to-use-apis-sdks.md)k használatával.

> [!NOTE]
> Az alábbi példák nem tartalmazzák a rövid leírások hibáinak kezelését. Azonban határozottan ajánlott a projekteken belül a szolgáltatási hívások becsomagolása a try/catch blokkokban.

> [!TIP] 
> Ne feledje, hogy minden SDK-módszer szinkron és aszinkron verziókban érhető el. A lapozási hívások esetében az aszinkron metódusok a `AsyncPageable<T>` szinkron verziók visszaadása után térnek vissza `Pageable<T>` .

### <a name="upload-models"></a>Modellek feltöltése

A modellek létrehozása után feltöltheti őket az Azure Digital Twins-példányba.

Az alábbi kódrészlet a következőt mutatja be:

```csharp
// 'client' is an instance of DigitalTwinsClient
// Read model file into string (not part of SDK)
StreamReader r = new StreamReader("MyModelFile.json");
string dtdl = r.ReadToEnd(); r.Close();
string[] dtdls = new string[] { dtdl };
client.CreateModels(dtdls);
```

Figyelje meg, hogy a `CreateModels` metódus több fájlt is elfogad egyetlen tranzakcióban. Az alábbi példa szemlélteti a következőket:

```csharp
var dtdlFiles = Directory.EnumerateFiles(sourceDirectory, "*.json");

List<string> dtdlStrings = new List<string>();
foreach (string fileName in dtdlFiles)
{
    // Read model file into string (not part of SDK)
    StreamReader r = new StreamReader(fileName);
    string dtdl = r.ReadToEnd(); r.Close();
    dtdlStrings.Add(dtdl);
}
client.CreateModels(dtdlStrings);
```

A modell fájljai több modellt is tartalmazhatnak. Ebben az esetben a modelleket JSON-tömbbe kell helyezni. Például:

```json
[
  {
    "@id": "dtmi:com:contoso:Planet",
    "@type": "Interface",
    //...
  },
  {
    "@id": "dtmi:com:contoso:Moon",
    "@type": "Interface",
    //...
  }
]
```
 
A feltöltéskor a rendszer érvényesíti a modell fájljait.

> [!TIP] 
> Vegye figyelembe, hogy a [DTDL ügyféloldali elemző függvénytárat](how-to-use-parser.md) is használhatja az ügyféloldali modellek ellenőrzéséhez.

### <a name="retrieve-models"></a>Modellek beolvasása

Az Azure Digital Twins-példányon tárolt modelleket listázhatja és lekérheti. 

A következő lehetőségek közül választhat:
* Az összes modell lekérése
* Egyetlen modell lekérése
* Egyetlen, függőségekkel rendelkező modell beolvasása
* Modellek metaadatainak beolvasása

Íme néhány példa a hívásokra:

```csharp
// 'client' is a valid DigitalTwinsClient object

// Get a single model, metadata and data
ModelData md1 = client.GetModel(id);

// Get a list of the metadata of all available models
Pageable<ModelData> pmd2 = client.GetModels();

// Get a list of metadata and full model definitions
Pageable<ModelData> pmd3 = client.GetModels(null, true);

// Get models and metadata for a model ID, including all dependencies (models that it inherits from, components it references)
Pageable<ModelData> pmd4 = client.GetModels(new string[] { modelId }, true);
```

Az API-hívások lekérik a modelleket az összes visszaadott `ModelData` objektumra vonatkozóan. `ModelData`Az Azure Digital Twins-példányban tárolt modellel kapcsolatos metaadatokat tartalmaz, például a név, a DTMI és a modell létrehozási dátuma. Az `ModelData` objektum opcionálisan magába foglalja a modellt is. A paraméterektől függően a hívások lekérése paranccsal lekérheti a csak metaadatokat (ez olyan esetekben hasznos, amikor az elérhető eszközök felhasználói felületi listáját szeretné megjeleníteni, például) vagy a teljes modellt.

A `RetrieveModelWithDependencies` hívás nem csak a kért modellt adja vissza, hanem az összes olyan modellt is, amelytől a kért modell függ.

A modelleket nem feltétlenül adja vissza pontosan abban a dokumentum űrlapon, amelyet feltöltöttek. Az Azure Digital Twins csak azt garantálja, hogy a visszatérési űrlap szemantikailag egyenértékű lesz. 

### <a name="remove-models"></a>Modellek eltávolítása

A modelleket a szolgáltatásból is el lehet távolítani a következő két módszer egyikével:
* **Leszerelés** : a modell leszerelése után már nem használhatja új digitális Twins létrehozásához. A modellt már használó meglévő digitális ikrek nem érintettek, így továbbra is frissítheti azokat olyan dolgokkal, mint a tulajdonságok módosítása, valamint kapcsolatok hozzáadása vagy törlése.
* **Törlés** : ezzel teljesen eltávolítja a modellt a megoldásból. Az ezt a modellt használó ikrek már nem tartoznak egyetlen érvényes modellel sem, így azok úgy vannak kezelve, mintha egyáltalán nem rendelkeznek modellel. Ezeket az ikreket továbbra is elolvashatja, de nem fog tudni frissítéseket készíteni, amíg hozzá nem rendeli őket egy másik modellhez.

Ezek különálló funkciók, és nem érintik egymást, bár ezek együtt használhatók a modellek fokozatos eltávolítására. 

### <a name="decommissioning"></a>Leszerelési

Itt látható a modell leszerelésének kódja:

```csharp
// 'client' is a valid DigitalTwinsClient  
client.DecommissionModel(dtmiOfPlanetInterface);
// Write some code that deletes or transitions digital twins
//...
```

A modell leszerelési állapota a `ModelData` modell lekérési API-k által visszaadott rekordokban szerepel.

#### <a name="deletion"></a>Törlés

A példány összes modelljét egyszerre is törölheti, vagy megteheti azt egyéni alapon.

Az összes modell törlésére példaként töltse le az oktatóanyagban használt minta alkalmazást [: Fedezze fel az alapokat egy minta ügyfélalkalmazás](tutorial-command-line-app.md)használatával. A *CommandLoop.cs* fájl ezt egy `CommandDeleteAllModels` függvényben végzi el.

A szakasz további részében lebonthatja a modell törlésének részleteit, és bemutatja, hogyan végezheti el azt egy adott modell esetében.

##### <a name="before-deletion-deletion-requirements"></a>Törlés előtt: törlési követelmények

A modelleket általában bármikor törölni lehet.

Kivételt képeznek a más modellektől függő modellek, vagy egy `extends` kapcsolattal vagy egy összetevővel. Ha például egy *ConferenceRoom* modell kibővít egy *helyiség* modellt, és egy *ACUnit* modellt tartalmaz összetevőként, akkor nem törölheti a *szobát* vagy a *ACUnit* , amíg a *ConferenceRoom* el nem távolítja a vonatkozó hivatkozásokat. 

Ezt úgy teheti meg, hogy a függő modell frissítésével eltávolítja a függőségeket, vagy teljesen törli a függő modellt.

##### <a name="during-deletion-deletion-process"></a>Törlés közben: törlési folyamat

Még ha a modell megfelel a azonnal törölni kívánt követelményeknek, érdemes lehet néhány lépést megtennie, hogy elkerülje a nem szándékolt következményeket az ikrek mögött. Íme néhány lépés, amely segíthet a folyamat kezelésében:
1. Első lépésként szerelje le a modellt
2. Várjon néhány percet, és győződjön meg arról, hogy a szolgáltatás feldolgozta a leszerelés előtt küldött, Last minute kettős létrehozási kérelmeket.
3. Az ikrek lekérdezése modell szerint az összes olyan ikrek megjelenítéséhez, amely a most leszerelt modellt használja
4. Ha már nincs szüksége rájuk, törölje az ikreket, vagy ha szükséges, egy új modellre. Dönthet úgy is, hogy egyedül hagyja őket, ebben az esetben a modell törlése után az ikrek nélkül válnak elérhetővé. Tekintse meg a következő szakaszt ezen állapot következményeiről.
5. Várjon még néhány percet, hogy ellenőrizze, hogy a módosítások főzött-e
6. A modell törlése 

A modell törléséhez használja a következő hívást:
```csharp
// 'client' is a valid DigitalTwinsClient
await client.DeleteModelAsync(IDToDelete);
```

##### <a name="after-deletion-twins-without-models"></a>Törlés után: ikrek modellek nélkül

A modell törlését követően a modellt használó digitális ikrek már nem modellnek minősülnek. Vegye figyelembe, hogy nincs olyan lekérdezés, amely az összes ikrek listáját megadja ebben az állapotban, bár a törölt modellből *továbbra is* lekérdezheti az ikreket, hogy megtudja, milyen hatással van az ikrekre.

Az alábbiakban áttekintheti, hogy mit tehet és nem tud olyan ikrekkel dolgozni, amelyek nem rendelkeznek modellel.

**Elvégezhető műveletek:**
* A Twin lekérdezése
* Tulajdonságok olvasása
* Kimenő kapcsolatok olvasása
* Bejövő kapcsolatok hozzáadása és törlése (a verzióban a más ikrek továbbra is *létrehozhatnak kapcsolatokat ehhez a Twin* )
  - A `target` kapcsolat definíciója továbbra is tükrözheti a törölt modell DTMI. Itt is működhet egy meghatározott céllal nem rendelkező kapcsolat.
* Kapcsolatok törlése
* A Twin törlése

Nem **hajthatók** végre a következők:
* Kimenő kapcsolatok szerkesztése (a-ben, *a Twin* és más ikrek közötti kapcsolatok)
* Tulajdonságok szerkesztése

##### <a name="after-deletion-re-uploading-a-model"></a>Törlés után: modell ismételt feltöltése

A modell törlését követően később dönthet úgy, hogy új modellt tölt fel ugyanazzal az AZONOSÍTÓval, amelyet a törölt. Ebben az esetben ez történik.
* A megoldás áruházának szemszögéből ez ugyanaz, mint egy teljesen új modell feltöltése. A szolgáltatás soha nem emlékszik a régire.   
* Ha a gráf a törölt modellre hivatkozó további ikreket tartalmaz, azok már nem elárvultak. Ez a modell-azonosító ismét érvényes az új definícióval. Ha azonban a modell új definíciója eltér a törölt modellel, akkor ezek az ikrek olyan tulajdonságokkal és kapcsolatokkal rendelkezhetnek, amelyek megfelelnek a törölt definíciónak, és nem érvényesek az újat.

Az Azure digitális Twins nem akadályozza ezt az állapotot, ezért ügyeljen arra, hogy az ikrek megfelelő javításával győződjön meg arról, hogy azok érvényesek maradnak a modell definíciós kapcsolóján keresztül.

## <a name="manage-models-with-cli"></a>Modellek kezelése a CLI-vel

A modellek az Azure Digital Twins CLI használatával is kezelhetők. A parancsok a következő [útmutatóban találhatók: az Azure digitális Twins parancssori](how-to-use-cli.md)felületének használata.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozhat létre és kezelhet digitális ikreket a modelljei alapján:
* [Útmutató: digitális ikrek kezelése](how-to-manage-twin.md)