---
title: 'Oktatóanyag: C# és AI azure blobok felett'
titleSuffix: Azure Cognitive Search
description: A C# és az Azure Cognitive Search .NET SDK használatával végighaladegy példa a szövegkinyerésről és a természetes nyelvi feldolgozásról a Blob storage-ban lévő tartalmakon keresztül.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 169a33d12e98235dcb4e4f317dbb8d91eb7446a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78851136"
---
# <a name="tutorial-use-c-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Oktatóanyag: A C# és a AI használatával kereshető tartalmat hozhat létre az Azure blobokból

Ha strukturálatlan szöveggel vagy lemezképpel rendelkezik az Azure Blob storage-ban, az [AI-bővítési folyamat](cognitive-search-concept-intro.md) kinyerheti az információkat, és új tartalmat hozhat létre, amely hasznos a teljes szöveges kereséshez vagy a tudásbányászati forgatókönyvekhez. Ebben a C# oktatóanyagban alkalmazzon optikai karakterfelismerést (OCR) a képeken, és hajtson végre természetes nyelvi feldolgozást, hogy új mezőket hozzon létre, amelyeket a lekérdezésekben, a mezőkben és a szűrőkben használhat.

Ez az oktatóanyag a C# és a [.NET SDK](https://aka.ms/search-sdk) segítségével hajtja végre a következő feladatokat:

> [!div class="checklist"]
> * Kezdje az alkalmazásfájlokkal és lemezképekkel az Azure Blob storage-ban.
> * Definiáljon egy folyamatot az OCR hozzáadásához, a szövegkinyeréshez, a nyelvfelismeréshez, az entitáshoz és a kulcskifejezések felismeréséhez.
> * Index definiálása a kimenet tárolására (nyers tartalom, valamint a folyamat által generált név-érték párok).
> * A folyamat végrehajtása átalakítások és elemzések indításához, valamint az index létrehozásához és betöltéséhez.
> * Fedezze fel az eredményeket a teljes szöveges keresés és a gazdag lekérdezésszintaxis használatával.

Ha nem rendelkezik Azure-előfizetéssel, nyisson meg egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Vizuális stúdió](https://visualstudio.microsoft.com/downloads/)
+ Meglévő [keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Használhatja az ingyenes szolgáltatást az oktatóanyaghoz. Az ingyenes keresési szolgáltatás három indexre, három indexelőre és három adatforrásra korlátozza. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg róla, hogy van hely a szolgáltatás, hogy elfogadja az új forrásokat.

## <a name="download-files"></a>Fájlok letöltése

1. Nyissa meg ezt a [OneDrive mappát,](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) és a bal felső sarokban kattintson a **Letöltés gombra** a fájlok számítógépre másolásához. 

1. Kattintson a jobb gombbal a zip fájlra, és válassza **az Összes kibontása**parancsot. Jelenleg 14 kép különböző típusú. Használja az összeset ehhez az oktatóanyaghoz.

## <a name="1---create-services"></a>1 - Szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Search indexelési és lekérdezések, cognitive Services a háttérrendszer a ai-bővítés, és az Azure Blob storage az adatok biztosításához. Ez az oktatóanyag a Cognitive Services-en naponta 20 tranzakció ingyenes lefoglalása alatt marad, így csak a kereséshez és a tároláshoz szükséges szolgáltatások hoznak létre.

Ha lehetséges, hozzon létre mind ugyanabban a régióban, mind az erőforráscsoportban a közelség és a kezelhetőség érdekében. A gyakorlatban az Azure Storage-fiók bármely régióban lehet.

### <a name="start-with-azure-storage"></a>Kezdje az Azure Storage szolgáltatással

1. [Jelentkezzen be az Azure Portalra,](https://portal.azure.com/) és kattintson **a + Erőforrás létrehozása gombra.**

1. Keressen *tárfiókot,* és válassza a Microsoft tárfiók-ajánlatát.

   ![Tárfiók létrehozása](media/cognitive-search-tutorial-blob/storage-account.png "Tárfiók létrehozása")

1. Az Alapok lapon a következő elemek szükségesek. Fogadja el az alapértelmezett értékeket minden máshoz.

   + **Erőforráscsoport**. Jelöljön ki egy meglévőt, vagy hozzon létre egy újat, de használja ugyanazt a csoportot az összes szolgáltatáshoz, hogy együttesen kezelhesse őket.

   + **Tárfiók neve**. Ha úgy gondolja, hogy több azonos típusú erőforrással rendelkezik, használja a nevet típus és régió, például *blobstoragewestus*szerint. 

   + **Hely**. Ha lehetséges, válassza ki ugyanazt a helyet, amelyet az Azure Cognitive Search és a Cognitive Services használt. Egyetlen hely érvényteleníti a sávszélesség-díjakat.

   + **Számla fajta**. Válassza ki az alapértelmezett, *StorageV2 (általános célú v2)*.

1. A szolgáltatás létrehozásához kattintson a **Véleményezés + Létrehozás** gombra.

1. Létrehozása után kattintson **az Ugrás az erőforrásra** gombra az Áttekintés lap megnyitásához.

1. Kattintson **a Blobs** szolgáltatás elemre.

1. Kattintson **a + Container** elemre egy tároló létrehozásához és az *alap-demo-data-pr nevet.*

1. Válassza *az alap-demo-data-pr* lehetőséget, majd a **Feltöltés gombra** kattintva nyissa meg azt a mappát, ahová a fájlokat mentette. Jelölje ki mind a tizennégy fájlt, és kattintson **az OK** gombra a feltöltéshez.

   ![Mintafájlok feltöltése](media/cognitive-search-quickstart-blob/sample-data.png "Mintafájlok feltöltése")

1. Mielőtt elhagyja az Azure Storage-t, szerezzen be egy kapcsolati karakterláncot, hogy megfogalmazhassa a kapcsolatot az Azure Cognitive Search szolgáltatásban. 

   1. Tallózzon vissza a tárfiók áttekintése lapra (példaként *a blobstragewestust* használtuk). 
   
   1. A bal oldali navigációs ablakban válassza az **Access billentyűk** lehetőséget, és másolja az egyik kapcsolati karakterláncot. 

   A kapcsolati karakterlánc a következő példához hasonló URL-cím:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Mentse a kapcsolati karakterláncot a Jegyzettömbbe. Az adatforrás-kapcsolat beállításakor később szüksége lesz rá.

### <a name="cognitive-services"></a>Cognitive Services

A AI-bővítést a Cognitive Services támogatja, beleértve a Szövegelemzést és a Computer Vision-t a természetes nyelv és a képfeldolgozás érdekében. Ha a cél az volt, hogy egy tényleges prototípus vagy projekt, akkor ezen a ponton kiépítés Kognitív szolgáltatások (ugyanabban a régióban, mint az Azure Cognitive Search), így csatolja az indexelési műveletekhez.

Ebben a gyakorlatban azonban kihagyhatja az erőforrás-kiépítést, mert az Azure Cognitive Search a színfalak mögött csatlakozhat a Cognitive Serviceshez, és indexelőnként 20 ingyenes tranzakciót biztosíthat. Mivel ez az oktatóanyag 7 tranzakciót használ, az ingyenes kiosztás elegendő. Nagyobb projektek esetén tervezze meg a Cognitive Services üzembe helyezhető a felosztó-ki-megy S0 szinten. További információ: [A Cognitive Services csatolása.](cognitive-search-attach-cognitive-services.md)

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A harmadik összetevő az Azure Cognitive Search, amelyet [a portálon hozhat létre.](search-create-service-portal.md) Az ingyenes szint segítségével elvégezheti ezt a forgatókönyvet. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Rendszergazdai API-kulcs és URL-cím beszerezni e cognitive Search szolgáltatáshoz

Az Azure Cognitive Search szolgáltatással való interakcióhoz szüksége lesz a szolgáltatás URL-címére és egy hozzáférési kulcsra. A keresési szolgáltatás mindkettővel jön létre, így ha hozzáadta az Azure Cognitive Search-et az előfizetéséhez, kövesse az alábbi lépéseket a szükséges információk beszerezéséhez:

1. [Jelentkezzen be az Azure Portalon,](https://portal.azure.com/)és a keresési szolgáltatás **áttekintése** lapon lekell szereznie az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsok**párbeszédpanelen szerezzen be egy rendszergazdai kulcsot a szolgáltatás teljes jogához. Két cserélhető rendszergazdai kulcs van, amelyek az üzletmenet folytonosságát biztosítják arra az esetre, ha át kell görgetnie egyet. Az elsődleges vagy másodlagos kulcsot objektumok hozzáadására, módosítására és törlésére irányuló kérelmeken használhatja.

   A lekérdezési kulcs beszerezése is. Ajánlott csak olvasási hozzáféréssel rendelkező lekérdezési kérelmeket kiadni.

   ![A szolgáltatás névének, a rendszergazdai és lekérdezési kulcsoknak a beszereznie](media/search-get-started-nodejs/service-name-and-keys.png)

Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="2---set-up-your-environment"></a>2 - A környezet beállítása

Először nyissa meg a Visual Studio alkalmazást, és hozzon létre egy új konzolalkalmazás-projektet, amely futtatható a .NET Core rendszeren.

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

Az [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) néhány ügyfélkódtárak, amelyek lehetővé teszik az indexek, adatforrások, indexelők és skillsets, valamint a dokumentumok feltöltése és kezelése, és lekérdezések végrehajtása, mindezt anélkül, hogy a HTTP és a JSON részleteinek kezelése. Ezek az ügyfélkönyvtárak nuget csomagokként vannak terjesztve.

Ehhez a projekthez telepítse a `Microsoft.Azure.Search` NuGet csomag 9-es vagy újabb verzióját.

1. Nyissa meg a Csomagkezelő konzolt. Válassza **az Eszközök** > **NuGet Csomagkezelő csomagkezelő** > **konzolt.** 

1. Keresse meg a [Microsoft.Azure.Search NuGet csomaglapot](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Válassza ki a legújabb verziót (9 vagy újabb).

1. Másolja a Programkezelő parancsot.

1. Térjen vissza a Package Manager konzolra, és futtassa az előző lépésben másolt parancsot.

Ezután telepítse `Microsoft.Extensions.Configuration.Json` a legújabb NuGet csomagot.

1. Válassza **az Eszközök** > **NuGet csomagkezelő** > kezelése a**NuGet csomagokat a megoldáshoz...**. 

1. Kattintson a **Tallózás gombra,** és keresse meg a `Microsoft.Extensions.Configuration.Json` NuGet csomagot. 

1. Válassza ki a csomagot, válassza ki a projektet, ellenőrizze, hogy a verzió a legújabb stabil verzió-e, majd kattintson a **Telepítés gombra.**

### <a name="add-service-connection-information"></a>Szolgáltatáskapcsolatadatainak hozzáadása

1. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza az Új elem **hozzáadása...** > **New Item...** lehetőséget. 

1. Nevezze el `appsettings.json` a fájlt, és válassza a **Hozzáadás lehetőséget.** 

1. A fájl felvétele a kimeneti könyvtárba.
    1. Kattintson a `appsettings.json` jobb gombbal, és válassza **a Tulajdonságok parancsot.** 
    1. Módosítsa a **Másolás a kimeneti könyvtárba** értékét **másolásra, ha újabb.**

1. Másolja az alábbi JSON-fájlt az új JSON fájlba.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Adja hozzá a keresési szolgáltatás és a blob storage fiók adatait. Emlékezzünk vissza, hogy ezeket az információkat az előző szakaszban megadott szolgáltatáskiépítési lépésekből szerezheti be.

### <a name="add-namespaces"></a>Névterek hozzáadása

A `Program.cs`alkalmazásban adja hozzá a következő névtereket.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Ügyfél létrehozása

Hozzon létre `SearchServiceClient` egy `Main`példányt az osztályból a csoportban.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`új `SearchServiceClient` értékeket hoz létre, amelyek az alkalmazás konfigurációs fájljában (appsettings.json) tárolódnak.

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

### <a name="add-function-to-exit-the-program-during-failure"></a>Add funkció kilép a programból hiba esetén

Ez az oktatóanyag célja, hogy segítsen megérteni az indexelési folyamat egyes lépéseit. Ha olyan kritikus probléma merül fel, amely megakadályozza, hogy a program létrehozza az adatforrást, a képzettségkészletet, az indexet vagy az indexelőt, a program kiadja a hibaüzenetet, és kilép, hogy a probléma érthető és orvososdó legyen.

A `ExitProgram` `Main` hozzáadás a program kilépését igénylő esetek kezeléséhez.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - A folyamat létrehozása

Az Azure Cognitive Search, AI-feldolgozás történik indexelés (vagy adatbetöltés) során történik. A forgatókönyv ezen része négy objektumot hoz létre: adatforrás, indexdefiníció, skillset, indexelő. 

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

A `SearchServiceClient` rendelkezik egy `DataSources` tulajdonsággal. Ez a tulajdonság biztosítja az Azure Cognitive Search adatforrások létrehozásához, listázásához, frissítéséhez vagy törléséhez szükséges összes módszert.

Hozzon `DataSource` létre egy `serviceClient.DataSources.CreateOrUpdate(dataSource)`új példányt a hívással. `DataSource.AzureBlobStorage`meg kell adnia az adatforrás nevét, a kapcsolati karakterláncot és a blobtároló nevét.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "basic-demo-data-pr",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

A sikeres kérelem esetén a metódus a létrehozott adatforrást adja vissza. Ha probléma van a kéréssel, például érvénytelen paraméter, a metódus kivételt fog okozni.

Most adjon hozzá `Main` egy `CreateOrUpdateDataSource` vonalat az imént hozzáadott függvény hívásához.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```


<!-- 
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

Now that you have initialized the `DataSource` object, create the data source. `SearchServiceClient` has a `DataSources` property. This property provides all the methods you need to create, list, update, or delete Azure Cognitive Search data sources.

For a successful request, the method will return the data source that was created. If there is a problem with the request, such as an invalid parameter, the method will throw an exception.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
``` -->

Fordítsa le és futtassa a megoldást. Mivel ez az első kérés, ellenőrizze az Azure Portalon, hogy az adatforrás jött létre az Azure Cognitive Search. A keresési szolgáltatás Irányítópult lapján ellenőrizze, hogy az Adatforrások csempén megjelent-e az új elem. Lehet, hogy várnia kell néhány percet, amíg a portáloldal frissül.

  ![Adatforrások csempe a portálon](./media/cognitive-search-tutorial-blob/data-source-tile.png "Adatforrások csempe a portálon")

### <a name="step-2-create-a-skillset"></a>2. lépés: Szakértelem létrehozása

Ebben a szakaszban adhatja meg az adatokra alkalmazni kívánt dúsítási lépéseket. Minden dúsítási lépés neve *szakértelem,* és a dúsító lépések készlete *skillet.* Ez az oktatóanyag [beépített kognitív képességeket](cognitive-search-predefined-skills.md) használ a skillsethez:

+ [Optikai karakterfelismerés](cognitive-search-skill-ocr.md) a nyomtatott és kézzel írt szöveg felismeréséhez a képfájlokban.

+ [Szövegegyesítés](cognitive-search-skill-textmerger.md) mezők gyűjteményéből egyetlen mezőbe történő összevonásához.

+ [Nyelvfelismeréssel](cognitive-search-skill-language-detection.md) azonosítja a tartalom nyelvét.

+ [Text Split](cognitive-search-skill-textsplit.md) a nagy tartalom kisebb adattömbökre bontásához a kulcskifejezés-kinyerési szakértelem és az entitásfelismerési szakértelem hívása előtt. A kulcskifejezések kinyerése és az entitásfelismerés legkevesebb 50 000 karakterértékű bemenetet fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba.

+ [Entitásfelismerés](cognitive-search-skill-entity-recognition.md) a szervezetek nevének kibontása a blob tárolóban lévő tartalomból.

+ A [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) lehívja a leggyakoribb kulcskifejezéseket.

A kezdeti feldolgozás során az Azure Cognitive Search feltöri az egyes dokumentumokat, hogy különböző fájlformátumokból származó tartalmakat olvasson. A forrásfájlban talált szöveg a létrehozott ```content``` mezőbe kerül, amelyből dokumentumonként egy jön létre. Így állítsa be a ```"/document/content"``` szöveg et használni. 

A kimenetek hozzárendelhetők egy indexhez, bemenetként használhatók egy alsóbb rétegbeli képességhez, vagy a fentiek mindegyike lehetséges, akárcsak a nyelvkód esetében. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához.

A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>OCR-készség

Az **OCR** szakértelem szöveget bont ki a képekből. Ez a szakértelem feltételezi, hogy létezik normalized_images mező. A mező létrehozásához az oktatóanyag későbbi ```"imageAction"``` részében az indexelő ```"generateNormalizedImages"```definíciójának konfigurációját a - ra állítjuk.

```csharp
private static OcrSkill CreateOcrSkill()
{
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

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Szakértelem egyesítése

Ebben a szakaszban hozzon létre egy **egyesítési** szakértelem, amely egyesíti a dokumentum tartalmát mező a szöveget, amely et az OCR szakértelem.

```csharp
private static MergeSkill CreateMergeSkill()
{
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

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Nyelvfelismerési készség

A **Nyelvfelismerés** idoszak a bemeneti szöveg nyelvét észleli, és a kérelemre elküldött minden dokumentumhoz egyetlen nyelvi kódot jelent. A **nyelvi észlelési** szakértelem kimenetét a **Szövegfelosztás** szakértelem bemenetének részeként használjuk.

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
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

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Szövegfelosztási szakértelem

Az alábbi **Felosztási** szakértelem oldalak szerint osztja fel a szöveget, és `String.Length`az oldalhosszát 4000 karakterre korlátozza a. Az algoritmus megpróbálja felosztani a szöveget adattömbökre, amelyek legbénák. `maximumPageLength` Ebben az esetben az algoritmus mindent megtesz, hogy megtörje a mondatot egy mondat határán, így az adattömb mérete valamivel kisebb lehet, mint `maximumPageLength`.

```csharp
private static SplitSkill CreateSplitSkill()
{
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

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Entitás kivezetési képzettsége

Ez `EntityRecognitionSkill` a példány a `organization`kategóriatípus felismerése. Az **Entitás-kivezetési** szakértelem `person` képes `location`felismerni a kategóriatípusokat és a .

Figyelje meg, hogy a ```"/document/pages/*"``` "context" mező csillaggal van beállítva, ami azt ```"/document/pages"```jelenti, hogy a dúsítási lépés a csoportban lévő minden egyes oldalhoz meg van hívva.

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
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

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Kulcskifejezés-kivonási készség

Az `EntityRecognitionSkill` imént létrehozott példányhoz hasonlóan a **kulcsmondat-kivonási** szakértelem is a dokumentum minden egyes oldalához meg van hívva.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
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

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>A skillset összeállítása és létrehozása

Készítse `Skillset` el a létrehozott készségek használatával.

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Adja hozzá a `Main`következő sorokat a hoz.

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>();
    skills.Add(ocrSkill);
    skills.Add(mergeSkill);
    skills.Add(languageDetectionSkill);
    skills.Add(splitSkill);
    skills.Add(entityRecognitionSkill);
    skills.Add(keyPhraseExtractionSkill);

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>3. lépés: Index létrehozása

Ebben a szakaszban egy indexsémát határoz meg a kereshető indexben szereplő mezők és az egyes mezők keresési attribútumainak megadásával. A mezők típussal is rendelkeznek, emellett olyan attribútumokat tartalmazhatnak, amelyek meghatározzák a mező használatának módját (kereshető, rendezhető stb.). Az indexben szereplő mezőneveknek nem kell pontosan megegyezniük a forrásban szereplő mezők nevével. Egy későbbi lépésben mezőleképezéseket fog hozzáadni egy indexelőhöz a forrás-cél mezőkhöz való csatlakozás céljából. Ebben a lépésben a keresőalkalmazásra vonatkozó mezőelnevezési konvenciók használatával határozza meg az indexet.

A gyakorlat során az alábbi mezőket és mezőtípusokat használjuk:

| mezőnevek: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| mezőtípusok: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>DemoIndex osztály létrehozása

Az index mezői egy modellosztály használatával vannak definiálva. A modellosztály minden tulajdonsága olyan attribútumokkal rendelkezik, amelyek meghatározzák a vonatkozó indexmező kereséssel kapcsolatos viselkedéseit. 

Hozzáadjuk a modellosztályt egy új C# fájlhoz. Kattintson a jobb gombbal a projektre, és válassza `DemoIndex.cs`az Új elem **hozzáadása** > **parancsot...** válassza az "Osztály" lehetőséget, és nevezze el a fájlt, majd válassza a **Hozzáadás parancsot.**

Győződjön meg arról, hogy a `Microsoft.Azure.Search` `Microsoft.Azure.Search.Models` névterekből és a névterekből származó típusokat szeretne használni.

Adja hozzá az alábbi `DemoIndex.cs` modellosztály-definíciót, és vegye fel ugyanabba a névtérbe, ahol létre fogja hozni az indexet.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
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
}
```

<!-- Add the below model class definition to `DemoIndex.cs` and include it in the same namespace where you'll create the index.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Cognitive Search .NET SDK.
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
``` -->

Most, hogy definiált egy modellosztályt, `Program.cs` vissza hozhat létre egy indexdefiníciót viszonylag könnyen. Az index neve a `demoindex`. Ha már létezik ilyen nevű index, akkor az törlődik.

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

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
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

A tesztelés során előfordulhat, hogy többször is megpróbálja létrehozni az indexet. Emiatt ellenőrizze, hogy a létrehozni kívánt index létezik-e már a létrehozás megkísérlése előtt.

Adja hozzá a `Main`következő sorokat a hoz.

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Index demoIndex = CreateDemoIndex(serviceClient);
```

<!-- ```csharp
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
 -->

Az index definiálásáról az [Index létrehozása (Azure Cognitive Search REST API) című](https://docs.microsoft.com/rest/api/searchservice/create-index)témakörben olvashat bővebben.

### <a name="step-4-create-and-run-an-indexer"></a>4. lépés: Indexelő létrehozása és futtatása

Eddig létrehozott egy adatforrást, egy képességcsoportot és egy indexet. Ez a három összetevő egy olyan [indexelő](search-indexer-overview.md) része lesz, amely az egyes részeket egyetlen többszakaszos műveletben egyesíti. A három rész egy indexelőben való egyesítéséhez mezőleképezéseket kell meghatároznia.

+ A fieldMappings feldolgozása a skillset előtt lesz feldolgozva, leképezve a forrásmezőket az adatforrásból az index célmezőibe. Ha a mezőnevek és -típusok mindkét végén azonosak, nincs szükség leképezésre.

+ A outputFieldMappings feldolgozása a skillset után, hivatkozva sourceFieldNames, amelyek nem léteznek, amíg a dokumentum repedés vagy dúsítása létrehozza őket. A targetFieldName egy index mezője.

A bemenetek kimenetekhez való csatlakoztatása mellett mezőleképezéseket is használhat az adatstruktúrák összeolvasztására. További információt a [Bővített mezők leképezése kereshető indexhez](cognitive-search-output-field-mapping.md)című témakörben talál.

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
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
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
Adja hozzá a `Main`következő sorokat a hoz.

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Elvárják, hogy az indexelő létrehozása egy kis időt vesz igénybe. Annak ellenére, hogy az adatkészlet kis méretű, az analitikai képességek számítási igénye nagy. Néhány képesség, például a képelemzés futásideje hosszú.

> [!TIP]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma lép fel az adatok elérésével, a bemenetek és kimenetek leképezésével vagy a műveletek sorrendjével kapcsolatban, az ebben a szakaszban jelenik meg.

### <a name="explore-creating-the-indexer"></a>Az indexelő létrehozásának felfedezése

A kód ```"maxFailedItems"``` -1-re van állítva, ami arra utasítja az indexelő motort, hogy figyelmen kívül hagyja a hibákat az adatok importálása során. Ez azért hasznos, mert az adatforrás kevés dokumentumot tartalmaz. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Is észre ```"dataToExtract"``` a ```"contentAndMetadata"```van beállítva. Ez az utasítást meghatározza, hogy az indexelő automatikusan kinyerje a tartalmat a különböző fájlformátumokból, beleértve az egyes fájlokra vonatkozó metaadatokat is.

Tartalom kinyerésekor az `imageAction` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A ```"imageAction"``` konfigurációkészlet ```"generateNormalizedImages"``` az OCR szakértelemmel és a szövegegyesítési szakértelemmel kombinálva arra utasítja az indexelőt, hogy bontsa ki a szöveget a képekből (például a "stop" szót egy közlekedési stop táblából), és ágyazza be a tartalommező részeként. Ez a működés mind a dokumentumokban beágyazott képekre (például egy PDF-fájlban található képre), mind az adatforrásban talált képekre (például egy JPG-fájlra) vonatkozik.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitor indexelés

Az indexelő meghatározását követően az indexelő a kérés elküldésekor automatikusan lefut. Az indexelés a vártnál tovább is eltarthat attól függően, hogy mely kognitív képességeket adta meg. Ha meg szeretné tudni, hogy az `GetStatus` indexelő továbbra is fut-e, használja a módszert.

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
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
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo`egy indexelő aktuális állapotát és végrehajtási előzményeit jelöli.

A figyelmeztetések bizonyos forrásfájl- és képességkombinációk esetében gyakoriak, és nem mindig utalnak hibára. Ebben az oktatóanyagban a figyelmeztetések jóindulatúak (például nincs szöveges bemenet a JPEG-fájlokból).

Adja hozzá a `Main`következő sorokat a hoz.

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - Keresés

Az indexelés befejezése után olyan lekérdezéseket futtathat, amelyek az egyes mezők tartalmát adják vissza. Alapértelmezés szerint az Azure Cognitive Search az 50 legjobb eredményt adja vissza. A mintaadatok mérete kicsi, ezért az alapértelmezett beállítás megfelelő. Azonban nagyobb méretű adatkészletek esetén előfordulhat, hogy a lekérdezési sztringben meg kell adnia a megfelelő paramétereket, hogy a rendszer több eredményt adjon vissza. További információt a Találatokat az Azure Cognitive Search alkalmazásban található [oldaltalálatai](search-pagination-page-layout.md)között olvashat.

Ellenőrzési lépésként kérdezze le az index összes mezőjét.

Adja hozzá a `Main`következő sorokat a hoz.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

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

`CreateSearchIndexClient`új `SearchIndexClient` értékeket hoz létre, amelyek az alkalmazás konfigurációs fájljában (appsettings.json) tárolódnak. Figyelje meg, hogy a keresési szolgáltatás lekérdezési API-kulcs a rendszergazdai kulcs helyett.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Adja hozzá a következő kódot a `Main` fájlhoz. Az első próbafogás az indexdefiníciót adja vissza, az egyes mezők nevével, típusával és attribútumaival. A második egy paraméterezett `Select` lekérdezés, ahol megadja, hogy mely `organizations`mezők szerepeljenek az eredményekben, például . Egy keresési `"*"` karakterlánc egyetlen mező teljes tartalmát adja vissza.

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

Ismételje meg a műveletet további mezők esetén: tartalom, languageCode, keyPhrases és szervezetek ebben a gyakorlatban. A [Select](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) tulajdonságon keresztül több mezőt is visszaadhat egy vesszővel tagolt lista segítségével.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszában a legpraktikusabb módszer az iteráció tervezésére, hogy törölje az objektumokat az Azure Cognitive Search szolgáltatásból, és lehetővé tegye a kód újraépítését. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Az oktatóanyag mintakódja ellenőrzi a meglévő objektumokat, és törli őket, így újra futtathatja a kódot.

A portál használatával indexek, indexelők, adatforrások és skillsets törölheti.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag bemutatja a bővített indexelési folyamat létrehozásának alapvető lépéseit az összetevők: adatforrás, skillset, index és indexelő létrehozásával.

[Beépített készségek](cognitive-search-predefined-skills.md) kerültek bevezetésre, valamint skillset meghatározása és a mechanika láncolat készségek együtt bemenetek és outputok. Azt is `outputFieldMappings` megtanulta, hogy az indexelő definíciója szükséges a bővített értékek útválasztása a folyamatból egy kereshető index egy Azure Cognitive Search szolgáltatás.

Végül megismerte, hogyan tesztelheti az eredményeket, és hogyan állíthatja alaphelyzetbe a rendszert a későbbi futtatásokhoz. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. Emellett azt is megtanulta, hogyan ellenőrizheti az indexelő állapotát, illetve hogy melyik objektumokat kell törölnie a folyamat újrafuttatása előtt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén célszerű eltávolítani azokat az erőforrásokat, amelyekre már nincs szüksége. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak Minden erőforrás vagy Erőforráscsoport hivatkozásával.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri az AI-bővítési folyamat összes objektumát, vessünk egy közelebbi pillantást a skillset definíciókra és az egyéni készségekre.

> [!div class="nextstepaction"]
> [Hogyan hozzunk létre egy skillset](cognitive-search-defining-skillset.md)
