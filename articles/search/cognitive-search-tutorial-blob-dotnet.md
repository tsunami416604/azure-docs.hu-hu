---
title: C#Oktatóanyag Cognitive Services API-k az indexelési folyamatokban való meghívásához – Azure Search
description: Ez az oktatóanyag részletesen bemutat egy példát adatok mesterséges intelligencia segítségével történő kinyerésére, illetve természetes nyelvi és képfeldolgozásra az Azure Search adatkinyerési és -átalakítási indexelőszolgáltatásában.
manager: nitinme
author: MarkHeff
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: b40cd63062e961848eb1ab6b956e63a83a634817
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936941"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C#Oktatóanyag Cognitive Services API-k hívása egy Azure Search indexelési folyamatban

Eben az oktatóanyagban az adatok Azure Search-beli bővítésének programozási mechanikájával fogjuk megismertetni, *kognitív képességek* használatával. A szaktudást a természetes nyelvi feldolgozási (NLP) és a Cognitive Services képelemzési képességei is alátámasztják. A készségkészlet-összeállításon és-konfiguráción keresztül kinyerheti egy rendszerkép vagy beolvasott dokumentum szövegét és szöveges ábrázolását. Emellett a nyelv, az entitások, a legfontosabb kifejezések és egyebek is észlelhetők. A végeredmény egy Azure Search indexben gazdag további tartalmat hoz létre, amelyet egy AI-alapú indexelési folyamat hozott létre.

Ebben az oktatóanyagban a .NET SDK használatával hajtja végre a következő feladatokat:

> [!div class="checklist"]
> * Olyan indexelőfolyamat létrehozása, amely kibővíti a mintaadatokat egy indexhez vezető útvonalon
> * Beépített képességek alkalmazása: optikai karakterfelismerés, szöveges egyesítés, nyelvfelismerés, szöveg felosztása, entitások felismerése, fő kifejezés kibontása
> * Megtudhatja, hogyan kapcsolhat össze képességeket egy képességcsoporton belül a bemenetek kimenetekhez való társításával
> * Kérések végrehajtása és az eredmények áttekintése
> * Az index és az indexelők alaphelyzetbe állítása további fejlesztés céljából

A kimenet egy teljes szöveges, kereshető index az Azure Search szolgáltatásban. Az indexet egyéb standard képességekkel, például [szinonimákkal](search-synonyms.md), [pontozási profilokkal](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [elemzőkkel](search-analyzers.md) és [szűrőkkel](search-filters.md) is bővítheti.

Ez az oktatóanyag az ingyenes szolgáltatáson fut, de az ingyenes tranzakciók száma napi 20 dokumentumra korlátozódik. Ha az oktatóanyagot többször is futtatni szeretné ugyanazon a napon, használjon kisebb fájlméretet, hogy több futtatással is elfér.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor a számlázható Cognitive Services erőforrást kell csatolnia. Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedési szakaszának részeként Azure Search. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/) történik. A rendszerkép kibontásának díjszabását a [Azure Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a következő szolgáltatásokat, eszközöket és az adateszközöket használjuk. 

+ [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a mintaadatok tárolásához. Győződjön meg arról, hogy a Storage-fiók ugyanabban a régióban található, mint a Azure Search.

+ A [mintaadatok](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) egy kisméretű, különböző típusú fájlból állnak. 

+ [Telepítse a Visual studiót](https://visualstudio.microsoft.com/) ide-ként való használatra.

+ [Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást.

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A Azure Search szolgáltatással való kommunikációhoz szüksége lesz a szolgáltatás URL-címére és egy hozzáférési kulcsra. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   ![Http-végpont és elérési kulcs](media/search-get-started-postman/get-url-key.png "Http-végpont és elérési kulcs") beszerzése

Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="prepare-sample-data"></a>Mintaadatok előkészítése

A bővítési folyamat az Azure-adatforrásokból hívja le az adatokat. A forrásadatoknak egy [Azure Search-indexelő](search-indexer-overview.md) által támogatott adatforrástípusból kell származniuk. Ebben a gyakorlatban a blobtárolót használjuk több tartalomtípus bemutatásához.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), navigáljon az Azure Storage-fiókjához, kattintson a **Blobok**elemre, majd a **+ tároló**elemre.

1. [Hozzon létre egy blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) -tárolót a mintaadatok tárolásához. Megadhatja a nyilvános hozzáférési szintet bármelyik érvényes értékéhez. Ez az oktatóanyag azt feltételezi, hogy a tároló neve "Basic-demo-adat-PR".

1. A tároló létrehozása után nyissa meg, és válassza a parancssáv **feltöltés** elemét a [mintaadatok](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)feltöltéséhez.

   ![Forrásfájlok az Azure Blob Storage-ban](./media/cognitive-search-quickstart-blob/sample-data.png)

1. A mintafájlok feltöltése után kérje le a tároló nevét és a kapcsolati sztringet a Blob Storage-hoz. Ezt úgy teheti meg, hogy a Azure Portal a Storage-fiókjához navigál, és kiválasztja a **hozzáférési kulcsok**elemet, majd a **kapcsolati sztring** mezőt másolja.

   A kapcsolati sztringnek a következő példához hasonló URL-címnek kell lennie:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

A kapcsolati sztring megadásának egyéb módjai is vannak, például egy közös hozzáférésű jogosultságkód biztosítása. Az adatforrások hitelesítő adataival kapcsolatos további információért lásd: [Indexelés az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>A környezet kialakítása

Először nyissa meg a Visual studiót, és hozzon létre egy új, a .NET Core-on futtatható Console app-projektet.

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

A [Azure Search .net SDK](https://aka.ms/search-sdk) néhány ügyféloldali kódtárat tartalmaz, amelyek lehetővé teszik az indexek, az adatforrások, az indexelő és a szakértelmével kezelését, valamint dokumentumok feltöltését és kezelését, valamint lekérdezések futtatását, anélkül, hogy a http-és JSON-adatokkal kellene foglalkoznia. . Ezek az ügyféloldali kódtárak mind NuGet-csomagként vannak elosztva.

Ehhez a projekthez telepítenie kell a `Microsoft.Azure.Search` NuGet csomag 9-es verzióját és a legújabb `Microsoft.Extensions.Configuration.Json` NuGet-csomagot.

Telepítse a `Microsoft.Azure.Search` NuGet csomagot a Visual Studióban található Package Manager konzol használatával. A csomagkezelő konzol megnyitásához válassza az **eszközök** > **NuGet Package Manager** > **csomagkezelő konzolt**. A parancs futtatásához keresse meg a [Microsoft. Azure. Search NuGet-csomag lapot](https://www.nuget.org/packages/Microsoft.Azure.Search), válassza a 9-es verzió lehetőséget, és másolja a Package Manager parancsot. Futtassa ezt a parancsot a Package Manager konzolon.

Ha a Visual Studióban szeretné telepíteni a `Microsoft.Extensions.Configuration.Json` NuGet csomagot, válassza az **eszközök** > **NuGet csomagkezelő** > **NuGet-csomagok kezelése a megoldáshoz..** . lehetőséget. Válassza a Tallózás lehetőséget, és keresse meg a `Microsoft.Extensions.Configuration.Json` NuGet csomagot. Ha megtalálta, válassza ki a csomagot, válassza ki a projektet, erősítse meg, hogy a verzió a legújabb stabil verzió, majd válassza a telepítés lehetőséget.

## <a name="add-azure-search-service-information"></a>Azure Search szolgáltatás adatainak hozzáadása

Ahhoz, hogy csatlakozni tudjanak a Azure Search szolgáltatáshoz, hozzá kell adnia a keresési szolgáltatás információit a projekthez. Kattintson a jobb gombbal a projektre a Megoldáskezelő, majd válassza az **Add** > **új elem...** lehetőséget. Nevezze el a fájlt `appsettings.json`, majd válassza a **Hozzáadás**lehetőséget. 

A fájlnak szerepelnie kell a kimeneti könyvtárban. Ehhez kattintson a jobb gombbal a `appsettings.json` elemre, és válassza a **Tulajdonságok**lehetőséget. Módosítsa a másolás értékét a **kimeneti könyvtárba** az **újabb másolatként**.

Másolja az alábbi JSON-t az új JSON-fájlba.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Adja hozzá a keresési szolgáltatás és a blob Storage-fiók adatait.

A keresési szolgáltatás adatait a Azure Portal keresési fiók oldalán érheti el. A fiók neve a főoldalon jelenik meg, a kulcsok pedig a **kulcsok**elemre kattintva találhatók.

A blob-kapcsolati karakterláncot úgy érheti el, ha a Azure Portal a Storage-fiókjához navigál, majd a **hozzáférési kulcsok**elemre kattint, majd a **kapcsolati sztring** mezőt másolja.

## <a name="add-namespaces"></a>Névterek hozzáadása

Ez az oktatóanyag számos különböző típusú névteret használ a különböző névterekben. Ezen típusok használatához adja hozzá a következőt a következőhöz `Program.cs`:.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Ügyfél létrehozása

Hozza létre a `SearchServiceClient` osztály egy példányát.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`létrehoz egy új `SearchServiceClient` értéket az alkalmazás konfigurációs fájljában (appSettings. JSON) tárolt értékek használatával.

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> A `SearchServiceClient` osztály kezeli a keresőszolgáltatása kapcsolatait. A túl sok kapcsolat megnyitásának elkerülése érdekében, ha lehetséges, próbálja meg a `SearchServiceClient` egyetlen példányát megosztani az alkalmazásban. A módszerei szálbiztosak az ilyen megosztás engedélyezéséhez.
> 
> 

## <a name="create-a-data-source"></a>Adatforrás létrehozása

Hozzon létre `DataSource` egy új példányt `DataSource.AzureBlobStorage`a hívásával. `DataSource.AzureBlobStorage`Ehhez meg kell adnia az adatforrás nevét, a kapcsolati karakterláncot és a blob-tároló nevét.

Bár ez az oktatóanyag nem használatos, a törlési szabályzat is meg van határozva, amely a törölt Blobok azonosítására szolgál a Soft delete oszlop értéke alapján. A következő házirend azt a blobot veszi figyelembe, amelyet törölni kell, ha `IsDeleted` az értékkel `true`rendelkező metadata tulajdonsággal rendelkezik.

```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Most, hogy elvégezte az `DataSource` objektum inicializálását, hozza létre az adatforrást. A `SearchServiceClient` rendelkezik egy `DataSources` tulajdonsággal. Ez a tulajdonság biztosítja az Azure Search adatforrások létrehozásához, listázásához, frissítéséhez vagy törléséhez szükséges összes módszert.

Sikeres kérelem esetén a metódus a létrehozott adatforrást fogja visszaadni. Ha probléma merül fel a kéréssel, például egy érvénytelen paraméterrel, akkor a metódus kivételt jelez.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

Mivel ez az első kérése, az Azure Portalon ellenőrizze, hogy létrejött-e az adatforrás az Azure Searchhöz. A keresési szolgáltatás Irányítópult lapján ellenőrizze, hogy az Adatforrások csempén megjelent-e az új elem. Lehet, hogy várnia kell néhány percet, amíg a portáloldal frissül.

  ![Adatforrások csempe a portálon](./media/cognitive-search-tutorial-blob/data-source-tile.png "Adatforrások csempe a portálon")

## <a name="create-a-skillset"></a>Képességcsoport létrehozása

Ebben a szakaszban megadhatja az adataira alkalmazni kívánt gazdagító lépések készletét. Az egyes *alkoholtartalom-növelési lépéseket egy* *készségkészlet*nevezzük, és a dúsítási lépések egyike. Ez az oktatóanyag [előre meghatározott kognitív képességeket](cognitive-search-predefined-skills.md) használ a képességcsoporthoz:

+ [Optikai karakterfelismerés](cognitive-search-skill-ocr.md) a nyomtatott és a kézírásos szöveg felismeréséhez a képfájlokban.

+ [Szöveges egyesítéssel](cognitive-search-skill-textmerger.md) egyesítheti a mezőket egy gyűjteményből egyetlen mezőbe.

+ [Nyelvfelismeréssel](cognitive-search-skill-language-detection.md) azonosítja a tartalom nyelvét.

+ A kinyerési képesség és az entitás-felismerési képesség meghívása előtt a [szöveg felosztása](cognitive-search-skill-textsplit.md) a nagyméretű tartalmak kisebb adattömbökbe való bontásához. A fő kifejezés kinyerése és az entitások felismerése 50 000 karakterből álló vagy annál kevesebb bemenetet fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba.

+ [Entitások felismerése](cognitive-search-skill-entity-recognition.md) a blob-tároló tartalmából származó szervezetek nevének kinyeréséhez.

+ A [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) lehívja a leggyakoribb kulcskifejezéseket.

A kezdeti feldolgozás során Azure Search kipattintja az egyes dokumentumokat a különböző fájlformátumokból származó tartalmak olvasásához. A forrásfájlban talált szöveg a létrehozott ```content``` mezőbe kerül, amelyből dokumentumonként egy jön létre. Ennek megfelelően állítsa be a bemenetet úgy ```"/document/content"``` , hogy ezt a szöveget használja. 

A kimenetek hozzárendelhetők egy indexhez, bemenetként használhatók egy alsóbb rétegbeli képességhez, vagy a fentiek mindegyike lehetséges, akárcsak a nyelvkód esetében. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához.

A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>OCR-képesség

Az **OCR** -képesség Kinyeri a szöveget a képekből. Ez a képesség azt feltételezi, hogy létezik egy normalized_images mező. A mező létrehozásához az oktatóanyag későbbi részében a ```"imageAction"``` konfigurációt az indexelő definíciójában kell ```"generateNormalizedImages"```beállítani.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "image",
    source: "/document/normalized_images/*"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "text",
    targetName: "text"));

OcrSkill ocrSkill = new OcrSkill(
    description: "Extract text (plain and structured) from image",
    context: "/document/normalized_images/*",
    inputs: inputMappings,
    outputs: outputMappings,
    defaultLanguageCode: OcrSkillLanguage.En,
    shouldDetectOrientation: true);
```

### <a name="merge-skill"></a>Szaktudás egyesítése

Ebben a szakaszban egy **egyesítési** képességet hozunk létre, amely egyesíti a dokumentum tartalma MEZŐT az OCR-képesség által létrehozott szöveggel.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/content"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "itemsToInsert",
    source: "/document/normalized_images/*/text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "offsets",
    source: "/document/normalized_images/*/contentOffset"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "mergedText",
    targetName: "merged_text"));

MergeSkill mergeSkill = new MergeSkill(
    description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    insertPreTag: " ",
    insertPostTag: " ");
```

### <a name="language-detection-skill"></a>Nyelvi észlelési képesség

A **nyelvfelismerési** képesség észleli a bemeneti szöveg nyelvét, és egyetlen nyelvi kódot jelent a kérelemben elküldött összes dokumentumhoz. Az **nyelvfelismerés** skill kimenetét fogjuk használni a **szöveg felosztása** képességbe való bevitel részeként.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "languageCode",
    targetName: "languageCode"));

LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
    description: "Detect the language used in the document",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="text-split-skill"></a>Szöveg felosztása készség

Az alábbi **felosztott** képesség a szöveget lapok alapján osztja szét, és az oldal hosszát 4 000 karakterre korlátozza `String.Length` értékkel mérve. Az algoritmus megpróbálja felosztani a szöveget a nagy `maximumPageLength` méretű adattömbökbe. Ebben az esetben az algoritmus elvégzi a legjobbat, hogy a mondatot egy mondat határán megtörje, így a tömb mérete valamivel kisebb `maximumPageLength`lehet.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "textItems",
    targetName: "pages"));

SplitSkill splitSkill = new SplitSkill(
    description: "Split content into pages",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    textSplitMode: TextSplitMode.Pages,
    maximumPageLength: 4000);
```

### <a name="entity-recognition-skill"></a>Entitás-felismerési szakértelem

Ez `EntityRecognitionSkill` a példány a kategória típusának `organization`felismerésére van beállítva. Az **entitás-felismerési** képesség az `person` és a `location` kategória típusát is felismeri.

Figyelje meg, hogy a "Context" mező csillaggal ```"/document/pages/*"``` értékre van beállítva, ami azt jelenti, hogy a dúsítási lépést a ```"/document/pages"``` alatt lévő összes oldal esetében meghívjuk.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
    
List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "organizations",
    targetName: "organizations"));

List<EntityCategory> entityCategory = new List<EntityCategory>();
entityCategory.Add(EntityCategory.Organization);
    
EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
    description: "Recognize organizations",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings,
    categories: entityCategory,
    defaultLanguageCode: EntityRecognitionSkillLanguage.En);
```

### <a name="key-phrase-extraction-skill"></a>Kulcs kifejezés kinyerési készség

Az imént létrehozott `EntityRecognitionSkill` példányhoz hasonlóan a rendszer a dokumentum minden oldalához meghívja a **kulcsszókeresés** szakértelmet.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "keyPhrases",
    targetName: "keyPhrases"));

KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
    description: "Extract the key phrases",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="build-and-create-the-skillset"></a>A készségkészlet készítése és létrehozása

Hozza létre `Skillset` az Ön által létrehozott ismeretek használatával.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

Hozza létre a készségkészlet a keresési szolgáltatásban.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>Index létrehozása

Ebben a szakaszban egy indexsémát határoz meg a kereshető indexben szereplő mezők és az egyes mezők keresési attribútumainak megadásával. A mezők típussal is rendelkeznek, emellett olyan attribútumokat tartalmazhatnak, amelyek meghatározzák a mező használatának módját (kereshető, rendezhető stb.). Az indexben szereplő mezőneveknek nem kell pontosan megegyezniük a forrásban szereplő mezők nevével. Egy későbbi lépésben mezőleképezéseket fog hozzáadni egy indexelőhöz a forrás-cél mezőkhöz való csatlakozás céljából. Ebben a lépésben a keresőalkalmazásra vonatkozó mezőelnevezési konvenciók használatával határozza meg az indexet.

A gyakorlat során az alábbi mezőket és mezőtípusokat használjuk:

| mezőnevek: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| mezőtípusok: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>DemoIndex osztály létrehozása

Az index mezői a Model osztály használatával vannak meghatározva. A modellosztály minden tulajdonsága olyan attribútumokkal rendelkezik, amelyek meghatározzák a vonatkozó indexmező kereséssel kapcsolatos viselkedéseit. 

A Model osztályt hozzáadjuk egy új C# fájlhoz. Kattintson a jobb gombbal a projektre, és válassza az **add** > **új elem...** lehetőséget, válassza a Class (osztály) elemet, nevezze el a fájlt `DemoIndex.cs`, majd válassza a **Hozzáadás**lehetőséget.

Ügyeljen arra, hogy a és `Microsoft.Azure.Search` `Microsoft.Azure.Search.Models` a névterek típusait is használni kívánja.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Adja hozzá az alábbi modell osztály definícióját, `DemoIndex.cs` és vegye fel azt ugyanabban a névtérbe, amelyben létre kívánja hozni az indexet.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public class DemoIndex
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsSearchable, IsSortable]
    public string Id { get; set; }

    [IsSearchable]
    public string Content { get; set; }

    [IsSearchable]
    public string LanguageCode { get; set; }

    [IsSearchable]
    public string[] KeyPhrases { get; set; }

    [IsSearchable]
    public string[] Organizations { get; set; }
}
```

Most, hogy meghatározta a modell osztályt, a `Program.cs` vissza lehetőséggel könnyedén létrehozhat egy index-definíciót. Az index neve "demoindex" lesz.

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

A tesztelés során előfordulhat, hogy egynél többször próbálkozik az index létrehozásával. Emiatt ellenőrizze, hogy a létrehozni kívánt index már létezik-e, mielőtt megpróbálja létrehozni.

```csharp
try
{
    bool exists = serviceClient.Indexes.Exists(index.Name);

    if (exists)
    {
        serviceClient.Indexes.Delete(index.Name);
    }

    serviceClient.Indexes.Create(index);
}
catch (Exception e)
{
    // Handle exception
}
```

További információk az indexek meghatározásáról: [Index létrehozása (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Indexelő létrehozása, mezők leképezése és átalakítások elvégzése

Eddig létrehozott egy adatforrást, egy képességcsoportot és egy indexet. Ez a három összetevő egy olyan [indexelő](search-indexer-overview.md) része lesz, amely az egyes részeket egyetlen többszakaszos műveletben egyesíti. A három rész egy indexelőben való egyesítéséhez mezőleképezéseket kell meghatároznia.

+ A fieldMappings a rendszer az adatforrásból a készségkészlet, a leképezési forrás mezőinek feldolgozásával dolgozza fel az indexben lévő mezőkbe. Ha a mezők nevei és típusai mindkét végén azonosak, nincs szükség leképezésre.

+ A outputFieldMappings a készségkészlet után dolgozzák fel, hivatkozva a sourceFieldNames, amelyek nem léteznek a dokumentum repedésének vagy dúsításának létrehozásakor. A targetFieldName egy index mezője.

A bemenetek kimenetekhez való csatlakoztatása mellett mező-hozzárendelések is használhatók az adatstruktúrák leállításához. További információ: [a dúsított mezők leképezése kereshető indexbe](cognitive-search-output-field-mapping.md).

```csharp
IDictionary<string, object> config = new Dictionary<string, object>();
config.Add(
    key: "dataToExtract",
    value: "contentAndMetadata");
config.Add(
    key: "imageAction",
    value: "generateNormalizedImages");

List<FieldMapping> fieldMappings = new List<FieldMapping>();
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "metadata_storage_path",
    targetFieldName: "id",
    mappingFunction: new FieldMappingFunction(
        name: "base64Encode")));
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "content",
    targetFieldName: "content"));

List<FieldMapping> outputMappings = new List<FieldMapping>();
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/organizations/*",
    targetFieldName: "organizations"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/keyPhrases/*",
    targetFieldName: "keyPhrases"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/languageCode",
    targetFieldName: "languageCode"));

Indexer indexer = new Indexer(
    name: "demoindexer",
    dataSourceName: dataSource.Name,
    targetIndexName: index.Name,
    description: "Demo Indexer",
    skillsetName: skillSet.Name,
    parameters: new IndexingParameters(
        maxFailedItems: -1,
        maxFailedItemsPerBatch: -1,
        configuration: config),
    fieldMappings: fieldMappings,
    outputFieldMappings: outputMappings);

try
{
    bool exists = serviceClient.Indexers.Exists(indexer.Name);

    if (exists)
    {
        serviceClient.Indexers.Delete(indexer.Name);
    }

    serviceClient.Indexers.Create(indexer);
}
catch (Exception e)
{
    // Handle exception
}
```

Várható, hogy az indexelő létrehozása eltarthat egy kis ideig. Annak ellenére, hogy az adatkészlet kis méretű, az analitikai képességek számítási igénye nagy. Néhány képesség, például a képelemzés futásideje hosszú.

> [!TIP]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma lép fel az adatok elérésével, a bemenetek és kimenetek leképezésével vagy a műveletek sorrendjével kapcsolatban, az ebben a szakaszban jelenik meg.

### <a name="explore-creating-the-indexer"></a>Az indexelő létrehozásának megismerése

A kód a ```"maxFailedItems"``` -1 értékre van kijelölve, amely arra utasítja az indexelési motort, hogy hagyja figyelmen kívül a hibákat az adatimportálás során Ez azért hasznos, mert az adatforrás kevés dokumentumot tartalmaz. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Azt is figyelje ```"dataToExtract"``` meg, hogy ```"contentAndMetadata"```a be van állítva. Ez az utasítást meghatározza, hogy az indexelő automatikusan kinyerje a tartalmat a különböző fájlformátumokból, beleértve az egyes fájlokra vonatkozó metaadatokat is.

Tartalom kinyerésekor az `imageAction` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A ```"imageAction"``` ```"generateNormalizedImages"``` konfigurációra van beállítva, az OCR-képességgel és a szöveges egyesítési képességgel együtt, és azt jelzi, hogy az indexelő Kinyeri a szöveget a képekből (például a "Leállítás" szót egy forgalom leállításakor), és beágyazza azt a Content (tartalom) mező részeként. Ez a működés mind a dokumentumokban beágyazott képekre (például egy PDF-fájlban található képre), mind az adatforrásban talált képekre (például egy JPG-fájlra) vonatkozik.

## <a name="check-indexer-status"></a>Az indexelő állapotának ellenőrzése

Az indexelő meghatározását követően az indexelő a kérés elküldésekor automatikusan lefut. Az indexelés a vártnál tovább is eltarthat attól függően, hogy mely kognitív képességeket adta meg. Ha szeretné megtudni, hogy az indexelő továbbra is fut- `GetStatus` e, használja a metódust.

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
            break;
        case IndexerStatus.Running:
            Console.WriteLine("Indexer is running");
            break;
        case IndexerStatus.Unknown:
            Console.WriteLine("Indexer status is unknown");
            break;
        default:
            Console.WriteLine("No indexer information");
            break;
    }
}
catch (Exception e)
{
    // Handle exception
}
```

`IndexerExecutionInfo`az indexelő aktuális állapotát és végrehajtási előzményeit jelöli.

A figyelmeztetések bizonyos forrásfájl- és képességkombinációk esetében gyakoriak, és nem mindig utalnak hibára. Ebben az oktatóanyagban a figyelmeztetések jóindulatúak (például nincs szöveges bemenet a JPEG-fájlokból).
 
## <a name="query-your-index"></a>Az index lekérdezése

Az indexelés befejezése után futtathat olyan lekérdezéseket, amelyek az egyes mezők tartalmát adják vissza. Alapértelmezés szerint az Azure Search az első 50 eredményt adja vissza. A mintaadatok mérete kicsi, ezért az alapértelmezett beállítás megfelelő. Azonban nagyobb méretű adatkészletek esetén előfordulhat, hogy a lekérdezési sztringben meg kell adnia a megfelelő paramétereket, hogy a rendszer több eredményt adjon vissza. Részletes utasításokért lásd: [Eredmények lapokra bontása az Azure Searchben](search-pagination-page-layout.md).

Ellenőrzési lépésként kérdezze le az index összes mezőjét.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient`létrehoz egy új `SearchIndexClient` értéket az alkalmazás konfigurációs fájljában (appSettings. JSON) tárolt értékek használatával. Vegye figyelembe, hogy a keresési szolgáltatás lekérdezési API-kulcsát használja a rendszer, nem pedig a rendszergazdai kulcsot.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

A kimenet az egyes mezők nevét, típusát és attribútumait tartalmazó indexséma.

Indítson egy másodlagos lekérdezést a `"*"` kifejezésre egy mező, például az `organizations` összes tartalmának lekéréséhez.

```csharp
SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

Ismételje meg a műveletet további mezők esetén: tartalom, languageCode, kifejezés és szervezet ebben a gyakorlatban. Egyszerre több mezőt is lekérhet a vesszővel elválasztott listát használó `$select` megadásával.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszaiban a tervezési iterációk gyakorlati megközelítése az objektumok törlése a Azure Searchból, és a kód újraépítésének engedélyezése. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Ez az oktatóanyag a meglévő indexelő és indexek ellenőrzését és törlését vette igénybe, ha már léteztek, hogy újra lehessen futtatni a kódot.

A portál segítségével indexeket, indexelő és szakértelmével is törölhet.

Ahogy a kód egyre kiforrottabbá válik, jó ötlet lehet az újraépítési stratégia finomítása. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag azt mutatja be, hogyan hozhatók létre egy bővített indexelési folyamat az összetevők létrehozásakor: adatforrás, készségkészlet, index és indexelő.

Bemutattuk az [előre meghatározott képességeket](cognitive-search-predefined-skills.md), valamint a képességcsoport megadását, illetve a képességek bemenetek és kimenetek segítségével történő összekapcsolásának működését. Azt is megtanulta, hogy az indexelő definíciójának `outputFieldMappings` eleme szükséges a bővített értékek a folyamatból az Azure Search szolgáltatásban található, kereshető indexbe történő átirányításához.

Végül megismerte, hogyan tesztelheti az eredményeket, és hogyan állíthatja alaphelyzetbe a rendszert a későbbi futtatásokhoz. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. Emellett azt is megtanulta, hogyan ellenőrizheti az indexelő állapotát, illetve hogy melyik objektumokat kell törölnie a folyamat újrafuttatása előtt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után fölöslegessé vált elemek eltávolítása leggyorsabban az Azure Search és az Azure Blob szolgáltatást tartalmazó erőforráscsoport törlésével végezhető el. Feltéve, hogy mindkét szolgáltatást ugyanabban a csoportban helyezte üzembe, törölje az erőforráscsoportot, amellyel véglegesen eltávolíthatja annak teljes tartalmát, a rövid útmutató során létrehozott összes szolgáltatást és tárolt tartalmat is beleértve. A portálon az erőforráscsoport neve az egyes szolgáltatások Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

Egyéni képességekkel testre szabhatja vagy kibővítheti a folyamatot. Egyéni képességek létrehozása és egy képességcsoporthoz adása révén saját kezűleg írt szöveg- vagy képelemzést használhat.

> [!div class="nextstepaction"]
> [Példa: Egyéni képesség létrehozása a kognitív kereséshez](cognitive-search-create-custom-skill-example.md)
