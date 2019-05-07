---
title: C#Az oktatóanyag a Cognitive Services API-k hívása egy indexelési folyamat – Azure Search
description: Ez az oktatóanyag részletesen bemutat egy példát adatok mesterséges intelligencia segítségével történő kinyerésére, illetve természetes nyelvi és képfeldolgozásra az Azure Search adatkinyerési és -átalakítási indexelőszolgáltatásában.
manager: eladz
author: MarkHeff
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: 1b3353cae73bb5710dc9343f1d211266d15743a2
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153210"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C#Oktatóanyag: Az egy Azure Search szolgáltatásban az indexelés folyamat a Cognitive Services API-k meghívása

Eben az oktatóanyagban az adatok Azure Search-beli bővítésének programozási mechanikájával fogjuk megismertetni, *kognitív képességek* használatával. Képességek természetes nyelvi feldolgozást (NLP) és a lemezkép elemzési képességeket biztosítanak a Cognitive Services élvezik. Képességcsoport összeállítás és konfigurációja kibonthatja a szöveget, és képet vagy beolvasott dokumentum fájl szöveges ábrázolását. Nyelv, az entitások, kulcskifejezéseket és további is felismeri. A végeredmény gazdag további Azure Search-index, mesterséges Intelligencia által működtetett indexelése folyamat által létrehozott tartalom.

Ebben az oktatóanyagban, a .NET SDK használatával a következő feladatokat:

> [!div class="checklist"]
> * Olyan indexelőfolyamat létrehozása, amely kibővíti a mintaadatokat egy indexhez vezető útvonalon
> * Beépített ismeretek alkalmazására: optikai karakterfelismerés, szövegegyesítő, nyelv észlelése, szöveg felosztása, entitásfelismerés, kulcsszókeresés
> * Megtudhatja, hogyan kapcsolhat össze képességeket egy képességcsoporton belül a bemenetek kimenetekhez való társításával
> * Kérések végrehajtása és az eredmények áttekintése
> * Az index és az indexelők alaphelyzetbe állítása további fejlesztés céljából

A kimenet egy teljes szöveges, kereshető index az Azure Search szolgáltatásban. Az indexet egyéb standard képességekkel, például [szinonimákkal](search-synonyms.md), [pontozási profilokkal](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [elemzőkkel](search-analyzers.md) és [szűrőkkel](search-filters.md) is bővítheti.

Ebben az oktatóanyagban az ingyenes szolgáltatás fut, de az ingyenes tranzakciók száma korlátozott a naponta 20 dokumentumokhoz. Ha azt szeretné, ez az oktatóanyag többször futtatása ugyanazon a napon, használja az egy kisebb fájlt, hogy a további futtatási illeszkednek.

> [!NOTE]
> Növelje a feldolgozása, további dokumentumok hozzáadása, illetve további AI-algoritmusokat hozzáadása gyakorisága bontsa ki a hatókört, szüksége lesz egy számlázható Cognitive Services-erőforrás csatolása. A díjakat API-k hívásakor, a Cognitive Services, valamint a lemezkép kinyerése a az Azure Search-dokumentumfeltörést fázis részeként. Nem számítunk fel díjat a szövegkinyerés dokumentumok közül.
>
> Végrehajtási beépített képességek a meglévő díjakat [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services/) . A kép kinyerési díjszabás leírása a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a következő szolgáltatások, eszközök és adatok szerepelnek. 

[Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben az oktatóanyagban egy ingyenes szolgáltatás használhatja.

[Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a mintaadatok tárolásához.

[Mintaadatok](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) lemezkészletből egy kis fájlt különböző típusú. 

[A Visual Studio telepítése](https://visualstudio.microsoft.com/) az IDE adatokként.

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

Az Azure Search szolgáltatás kezelése szüksége lesz a szolgáltatás URL-CÍMÉT és a egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

   ![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-fiddler/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="prepare-sample-data"></a>Mintaadatok létrehozása

A bővítési folyamat az Azure-adatforrásokból hívja le az adatokat. A forrásadatoknak egy [Azure Search-indexelő](search-indexer-overview.md) által támogatott adatforrástípusból kell származniuk. Az Azure Table Storage a kognitív keresés nem támogatott. Ebben a gyakorlatban a blobtárolót használjuk több tartalomtípus bemutatásához.

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com)lépjen az Azure storage-fiókot, kattintson a **Blobok**, és kattintson a **+ tároló**.

1. [Hozzon létre egy blobtárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) mintaadatok tárolásához. A nyilvános hozzáférés szintje beállíthatja az érvényes értékek bármelyikére. Ez az oktatóanyag feltételezi, hogy a tároló neve "basic-demo-data-pr".

1. A tároló létrehozása után nyissa meg és jelölje ki **feltöltése** feltölteni a parancssávon az [mintaadatok](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Forrásfájlok az Azure Blob Storage-ban](./media/cognitive-search-quickstart-blob/sample-data.png)

1. A mintafájlok feltöltése után kérje le a tároló nevét és a kapcsolati karakterláncot a Blob Storage-hoz. Ezt ellenőrizheti, hogy a storage-fiókot az Azure Portalon, és jelölje ki **hozzáférési kulcsok**, majd másolja a **kapcsolati karakterlánc** mező.

   A kapcsolati karakterláncnak a következő példához hasonló URL-címnek kell lennie:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

A kapcsolati karakterlánc megadásának egyéb módjai is vannak, például egy közös hozzáférésű jogosultságkód biztosítása. Az adatforrások hitelesítő adataival kapcsolatos további információért lásd: [Indexelés az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>A környezet kialakítása

Első lépésként nyissa meg a Visual Studio és a egy új Konzolalkalmazás-projektet, .NET Core-on futtatható létrehozása.

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

A [Azure Search .NET SDK](https://aka.ms/search-sdk) áll, amelyek lehetővé teszik az indexek, adatforrások, indexelők és ismereteket, valamint töltse fel és kezelheti a dokumentumokat, és hajtsa végre a lekérdezéseket, anélkül hogy bajlódnia kezelése néhány klienskódtárak a HTTP- és JSON-adatokat. Ezen klienskódtárak NuGet-csomagként oszlanak.

Ehhez a projekthez, telepítenie kell a 9-es verzió a `Microsoft.Azure.Search` NuGet csomag és a legújabb `Microsoft.Extensions.Configuration.Json` NuGet-csomagot.

Telepítse a `Microsoft.Azure.Search` a Package Manager konzol használatával a Visual Studióban NuGet-csomagot. Válassza a Package Manager console megnyitásához **eszközök** > **NuGet-Csomagkezelő** > **Package Manager Console**. A parancs futtatása lekéréséhez lépjen a [Microsoft.Azure.Search NuGet csomag lapon](https://www.nuget.org/packages/Microsoft.Azure.Search), válassza a 9-es verzió, és másolja a Package Manager parancsot. A Package Manager konzol, a következő parancs futtatásával.

Telepítése a `Microsoft.Extensions.Configuration.Json` NuGet-csomagot a Visual Studióban válassza **eszközök** > **NuGet-Csomagkezelő** > **NuGet-csomagok kezelése megoldáshoz...** . Válassza a Tallózás gombra, és keresse meg a `Microsoft.Extensions.Configuration.Json` NuGet-csomagot. Miután azt tapasztaltuk, válassza ki a csomagot, jelölje ki a projektet, győződjön meg arról, a rendszer a a legújabb stabil verzióra, majd válassza ki a telepítés.

## <a name="add-azure-search-service-information"></a>Adja hozzá az Azure Search szolgáltatás adatai

Az Azure Search szolgáltatáshoz való csatlakozáshoz szüksége lesz a keresési szolgáltatás információinak hozzáadása a projekthez. Kattintson jobb gombbal a projektre a Megoldáskezelőben, és válassza ki **Hozzáadás** > **új elem...**  . A fájl neve `appsettings.json` válassza **Hozzáadás**. 

Ezt a fájlt kell szerepelnie a kimeneti könyvtárat. Ehhez kattintson a jobb gombbal `appsettings.json` válassza **tulajdonságok**. Módosítsa az értéket a **Copy to Output Directory** való **újabb példányát**.

Másolás az alábbi JSON-t az új JSON-fájlt.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Adja hozzá a keresési szolgáltatás és a blob storage-fiók adatait.

A keresési szolgáltatás információkat szerezhet a keresési fiókoldalon az Azure Portalon. A fiók neve lesz a fő lapján, és a kulcsok kiválasztásával található **kulcsok**.

Az Azure Portalon a tárfiókja blob kapcsolati karakterlánc kaphat kiválasztásával **hozzáférési kulcsok**, majd másolja a **kapcsolati karakterlánc** mező.

## <a name="add-namespaces"></a>Névterek

Ebben az oktatóanyagban a különböző névterek számos különböző lemeztípusokat használnak. Annak érdekében, hogy használja ezeket a feldolgozástípusokat adja hozzá a következőt `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>-Ügyfél létrehozása

Hozzon létre egy példányt a `SearchServiceClient` osztály.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` létrehoz egy új `SearchServiceClient` az alkalmazás konfigurációs fájljában (appsettings.json) tárolt értékének a felhasználásával.

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

Hozzon létre egy új `DataSource` meghívásával példány `DataSource.AzureBlobStorage`. `DataSource.AzureBlobStorage` megköveteli, hogy az adatforrás neve, a kapcsolati karakterlánc, és a blob-tároló nevének megadása.

Bár nem szerepel ebben az oktatóanyagban egy helyreállítható törlésre vonatkozó házirendet is definiálva van ami helyreállítható törlés oszlop értékei alapján törölt blobok azonosítására szolgál. A következő szabályzatot is figyelembe veszi a blob metaadat-tulajdonságot, ha a törlendő `IsDeleted` értékkel `true`.

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

Most, hogy van inicializálva a `DataSource` objektumazonosító, az adatforrás létrehozása. A `SearchServiceClient` rendelkezik egy `DataSources` tulajdonsággal. Ez a tulajdonság biztosítja mindazokat a módszereket kell létrehozásához, listázásához, frissítéséhez vagy törléséhez Azure Search-adatforrásokhoz.

A kérelem sikeres a metódus visszaadja az adatforrás, amely lett létrehozva. Ha a probléma a kérelemmel, például érvénytelen egy paraméter, a metódus kivételt.

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

Ebben a szakaszban adja meg, hogy az adatok a alkalmazni kívánt Adatbővítés lépések csoportját. Minden egyes Adatbővítés lépést nevezzük regisztrációnak egy *szakértelem* és az Adatbővítés lépéseket egy *indexmezők*. Ez az oktatóanyag [előre meghatározott kognitív képességeket](cognitive-search-predefined-skills.md) használ a képességcsoporthoz:

+ [Optikai karakterfelismerés](cognitive-search-skill-ocr.md) a képfájlok nyomtatott és kézzel írt szöveg felismerése.

+ [Szövegegyesítő](cognitive-search-skill-textmerger.md) egyesíthetők a szöveget a mezők gyűjteménye egyetlen mezőbe.

+ [Nyelvfelismeréssel](cognitive-search-skill-language-detection.md) azonosítja a tartalom nyelvét.

+ [Szöveg felosztása](cognitive-search-skill-textsplit.md) nagy méretű tartalom kezdetét szeletekre a kulcsfontosságú kifejezések kinyerése szakértelem és az entitások felismerése szakértelem hívása előtt. Kulcskifejezések kinyerése és entitások felismerése elfogadja a bemenetek legfeljebb 50 000 karakter. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba.

+ [Entitások felismerése](cognitive-search-skill-entity-recognition.md) szervezetek nevei kinyerését tartalom blob-tárolóban.

+ A [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) lehívja a leggyakoribb kulcskifejezéseket.

Kezdeti a feldolgozás során az Azure Search repedések minden egyes dokumentum számára különböző fájlformátumokban lévő tartalmak olvasását. A forrásfájlban talált szöveg a létrehozott ```content``` mezőbe kerül, amelyből dokumentumonként egy jön létre. Ennek megfelelően állítsa be, bemeneti ```"/document/content"``` használni ezt a szöveget. 

A kimenetek hozzárendelhetők egy indexhez, bemenetként használhatók egy alsóbb rétegbeli képességhez, vagy a fentiek mindegyike lehetséges, akárcsak a nyelvkód esetében. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához.

A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Optikai Karakterfelismerés szakértelem

A **OCR** szakértelem szöveges állomány kinyerésére lemezképek. Ezen a képzettségi azt feltételezi, hogy létezik-e normalized_images mező. Ez a mező létrehozásához értékre állítjuk az oktatóanyag későbbi részében a ```"imageAction"``` az indexelő meghatározását, hogy a konfigurációs ```"generateNormalizedImages"```.

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

### <a name="merge-skill"></a>Ismeretek egyesítése

Ebben a szakaszban létrehozunk egy **egyesítése** szakértelem, amely egyesíti a dokumentum tartalom mezőben az a szöveg, amelyben a OCR szakértelem állította elő.

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

### <a name="language-detection-skill"></a>Nyelv észlelése szakértelem

A **nyelvfelismerés** szakértelem észleli a nyelvet, amely a bemeneti szöveg és a jelentések minden egyes dokumentum, a kérelem küldése egyetlen nyelvkód. Használjuk a kimenetét a **nyelvfelismerés** szakértelem bemeneti részeként a **szöveg felosztása** szakértelem.

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

### <a name="text-split-skill"></a>Szöveg felosztása szakértelem

Az alábbi **Split** szakértelem fog szöveg felosztása lapok, és korlátozza az oldal megőrzési által mért 4000 karakter hosszúságú lehet `String.Length`. Az algoritmus a szöveg felosztása adattömbökre legfeljebb próbál `maximumPageLength` mérete. Ebben az esetben az algoritmus fog tenni a lehető legjobb megáll a mondat mondat határok, így előfordulhat, hogy az adatrészlet méretének valamivel kisebb, mint `maximumPageLength`.

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

### <a name="entity-recognition-skill"></a>Entitások felismerése szakértelem

Ez `EntityRecognitionSkill` példány beállítása ismeri fel a kategóriája `organization`. A **entitások felismerése** szakértelem is képes felismerni a kategória adattípusokat `person` és `location`.

Figyelje meg, hogy a "környezet" mező értéke ```"/document/pages/*"``` csillaggal, tehát a Adatbővítés lépést nevezzük alatt minden olyan lap ```"/document/pages"```.

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

### <a name="key-phrase-extraction-skill"></a>A kulcsfontosságú kifejezések kinyerése szakértelem

Például a `EntityRecognitionSkill` példányt, amely az imént létrehozott, a **kulcs kulcsszókeresés** szakértelem nevezzük a dokumentum minden egyes oldalához.

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

### <a name="build-and-create-the-skillset"></a>Hozhat létre, és a képességcsoport létrehozása

Hozhat létre a `Skillset` a létrehozott készségeit.

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

A keresési szolgáltatás létrehozása a képességek alkalmazási lehetőségét.

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

Ez az index a mezőket egy modellosztály segítségével vannak meghatározva. A modellosztály minden tulajdonsága olyan attribútumokkal rendelkezik, amelyek meghatározzák a vonatkozó indexmező kereséssel kapcsolatos viselkedéseit. 

A modellosztály hozzáadunk egy új C# fájlt. A jobb gombbal a projekt, és válassza a **Hozzáadás** > **új elem...** válassza ki a "Class", a fájl neve `DemoIndex.cs`, majd **Hozzáadás**.

Ügyeljen arra, hogy szeretné-e használni a típusát, hogy a `Microsoft.Azure.Search` és `Microsoft.Azure.Search.Models` névterek.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Adja hozzá a modell osztály definícióját az alábbi `DemoIndex.cs` adja hozzá ugyanazt a névteret, amelyben fog létrehozni az indexet.

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

Most, hogy meghatározta a modellosztályt, biztonsági `Program.cs` egy indexdefiníciót viszonylag egyszerűen hozhat létre. Ez az index neve "demoindex" lesz.

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Tesztelés során azt tapasztalhatja, hogy végrehajtani kívánt egynél többször az index létrehozásához. Ez az oka ellenőrizze, hogy létezik-e, hogy Ön már létre az index létrehozása előtt.

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

+ A fieldMappings dolgozza fel előbb a képességek alkalmazási lehetőségét, az adatforrásból származó mezők forrás az indexben lévő cél mezők leképezése. Ha a mező nevét és típusát azonosak mindkét végén, nincs leképezés nem szükséges.

+ A outputFieldMappings feldolgozása után hivatkozik, amely még nem léteznek, amíg dokumentumfeltörést sourceFieldNames készségeitől vagy Adatbővítés létrehozza őket. A targetFieldName egy mezőt az indexben.

Bemenetek, kimenetek csatlakoztatása, mellett is használhatja Mezőleképezések datové struktury simítják. További információkért lásd: [képi elemekben gazdag mezők leképezése egy kereshető indexet](cognitive-search-output-field-mapping.md).

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

A várt, hogy az indexelő létrehozása eltarthat egy kis ideig. Annak ellenére, hogy az adatkészlet kis méretű, az analitikai képességek számítási igénye nagy. Néhány képesség, például a képelemzés futásideje hosszú.

> [!TIP]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma lép fel az adatok elérésével, a bemenetek és kimenetek leképezésével vagy a műveletek sorrendjével kapcsolatban, az ebben a szakaszban jelenik meg.

### <a name="explore-creating-the-indexer"></a>Ismerje meg, az indexelő létrehozása

A kód készletek ```"maxFailedItems"``` -1 értéket, amely arra utasítja az indexelési motor adatok importálása során figyelmen kívül a hibát. Ez azért hasznos, mert az adatforrás kevés dokumentumot tartalmaz. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Szintén figyelje meg, hogy a ```"dataToExtract"``` értékre van állítva ```"contentAndMetadata"```. Ez az utasítást meghatározza, hogy az indexelő automatikusan kinyerje a tartalmat a különböző fájlformátumokból, beleértve az egyes fájlokra vonatkozó metaadatokat is.

Tartalom kinyerésekor az `imageAction` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A ```"imageAction"``` beállítása ```"generateNormalizedImages"``` konfigurációs, optikai Karakterfelismerés szakértelem és szöveges egyesítése szakértelem arra utasítja a szöveg kinyerése a rendszerképek (például a word-forgalom bejelentkezéshez leállítása a "stop"), és beágyazza azt content mezőjének részeként az indexelő. Ez a működés mind a dokumentumokban beágyazott képekre (például egy PDF-fájlban található képre), mind az adatforrásban talált képekre (például egy JPG-fájlra) vonatkozik.

## <a name="check-indexer-status"></a>Az indexelő állapotának ellenőrzése

Az indexelő meghatározását követően az indexelő a kérés elküldésekor automatikusan lefut. Az indexelés a vártnál tovább is eltarthat attól függően, hogy mely kognitív képességeket adta meg. Ismerje meg, hogy az indexelő még mindig fut, használja a `GetStatus` metódust.

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

`IndexerExecutionInfo` az indexelő aktuális állapot és végrehajtási előzményeinek jelöli.

A figyelmeztetések bizonyos forrásfájl- és képességkombinációk esetében gyakoriak, és nem mindig utalnak hibára. Ebben az oktatóanyagban a figyelmeztetések jóindulatúak (például nincs szöveges bemenet a JPEG-fájlokból).
 
## <a name="verify-content"></a>Tartalom ellenőrzése

Indexelő befejezése után futtathatja az egyes mezők tartalmát visszaadó lekérdezések. Alapértelmezés szerint az Azure Search az első 50 eredményt adja vissza. A mintaadatok mérete kicsi, ezért az alapértelmezett beállítás megfelelő. Azonban nagyobb méretű adatkészletek esetén előfordulhat, hogy a lekérdezési karakterláncban meg kell adnia a megfelelő paramétereket, hogy a rendszer több eredményt adjon vissza. Részletes utasításokért lásd: [Eredmények lapokra bontása az Azure Searchben](search-pagination-page-layout.md).

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

`CreateSearchIndexClient` létrehoz egy új `SearchIndexClient` az alkalmazás konfigurációs fájljában (appsettings.json) tárolt értékének a felhasználásával. Vegye figyelembe, hogy search szolgáltatás lekérdezési API-t használja a rendszer, és nem az adminisztrációs kulcsot.

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

Ismételje meg a műveletet további mezőket: tartalom, languageCode, keyPhrases és szervezetek ebben a gyakorlatban. Egyszerre több mezőt is lekérhet a vesszővel elválasztott listát használó `$select` megadásával.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

Korai szakaszaiban kísérleti, fejlesztési a legtöbb gyakorlati megközelítést tervezési iteráció során, hogy törölni az objektumokat az Azure Search, és lehetővé teszi a kód újraépítése őket. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Ebben az oktatóanyagban tartott gondoskodik a meglévő indexelők és az indexek ellenőrzése és törlése őket, ha már van ilyen, hogy a kódot futtathatja.

A portál használatával törölje az indexek, indexelők és ismereteket.

Ahogy a kód egyre kiforrottabbá válik, jó ötlet lehet az újraépítési stratégia finomítása. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag, amellyel összetevőit kell létrehozni egy képi elemekben gazdag indexelési folyamat alapvető lépéseit mutatja be: egy adatforrást, indexmezők, index és indexelő.

Bemutattuk az [előre meghatározott képességeket](cognitive-search-predefined-skills.md), valamint a képességcsoport megadását, illetve a képességek bemenetek és kimenetek segítségével történő összekapcsolásának működését. Azt is megtanulta, hogy az indexelő definíciójának `outputFieldMappings` eleme szükséges a bővített értékek a folyamatból az Azure Search szolgáltatásban található, kereshető indexbe történő átirányításához.

Végül megismerte, hogyan tesztelheti az eredményeket, és hogyan állíthatja alaphelyzetbe a rendszert a későbbi futtatásokhoz. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. Emellett azt is megtanulta, hogyan ellenőrizheti az indexelő állapotát, illetve hogy melyik objektumokat kell törölnie a folyamat újrafuttatása előtt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után fölöslegessé vált elemek eltávolítása leggyorsabban az Azure Search és az Azure Blob szolgáltatást tartalmazó erőforráscsoport törlésével végezhető el. Feltéve, hogy mindkét szolgáltatást ugyanabban a csoportban helyezte üzembe, törölje az erőforráscsoportot, amellyel véglegesen eltávolíthatja annak teljes tartalmát, a rövid útmutató során létrehozott összes szolgáltatást és tárolt tartalmat is beleértve. A portálon az erőforráscsoport neve az egyes szolgáltatások Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

Egyéni képességekkel testre szabhatja vagy kibővítheti a folyamatot. Egyéni képességek létrehozása és egy képességcsoporthoz adása révén saját kezűleg írt szöveg- vagy képelemzést használhat.

> [!div class="nextstepaction"]
> [Példa: egyéni képesség létrehozása](cognitive-search-create-custom-skill-example.md)
