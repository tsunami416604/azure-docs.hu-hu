---
title: C#-oktatóanyag az AI használatával az Azure-blobokon
titleSuffix: Azure Cognitive Search
description: A C# és az Azure Cognitive Search .NET SDK használatával a blob Storage-ban található tartalommal kapcsolatos szöveg-kinyerési és természetes nyelvi feldolgozás példája.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 0ad3e6dbb63d7c89919d6d341bd62c5d57960a43
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511653"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Oktatóanyag: AI által generált kereshető tartalom az Azure-blobokból a .NET SDK használatával

Ha strukturálatlan szöveget vagy rendszerképeket használ az Azure Blob Storage-ban, egy [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítési folyamat kinyerheti az adatokat, és létrehozhat olyan új tartalmakat, amelyek hasznosak a teljes szöveges kereséshez és az adatbányászati forgatókönyvekhez. Ebben a C# oktatóanyagban az optikai karakterfelismerést (OCR) alkalmazzuk a képeken, és természetes nyelvi feldolgozást végeznek olyan új mezők létrehozásához, amelyeket használhat a lekérdezésekben, a dimenziókban és a szűrőkben.

Ez az oktatóanyag a C# és a [.net SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search) használatával hajtja végre a következő feladatokat:

> [!div class="checklist"]
> * Az alkalmazás fájljait és lemezképeit az Azure Blob Storage-ban indíthatja el.
> * Definiáljon egy folyamatot az OCR, a szöveg kinyerése, a nyelvfelismerés, az entitás és a kulcs kifejezésének felismeréséhez.
> * Definiáljon egy indexet a kimenet (nyers tartalom, valamint a folyamat által generált név-érték párok) tárolására.
> * A folyamat végrehajtásával megkezdheti az átalakításokat és az elemzést, valamint az index létrehozását és betöltését.
> * A teljes szöveges kereséssel és a részletes lekérdezési szintaxissal megismerheti az eredményeket.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt nyisson meg egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Meglévő keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást. Az ingyenes keresési szolgáltatás három indexre, három indexelő elemre és három adatforrásra korlátozza a szolgáltatást. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a szolgáltatásban az új erőforrások elfogadására szolgáló helyiséggel.

## <a name="download-files"></a>Fájlok letöltése

1. Nyissa meg ezt a [OneDrive mappát](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) , és a bal felső sarokban kattintson a **Letöltés** elemre a fájlok számítógépre másolásához. 

1. Kattintson a jobb gombbal a zip-fájlra, és válassza az **összes kibontása**lehetőséget. A különböző típusok 14 fájlból állnak. Ehhez a gyakorlathoz a 7-et fogja használni.

Az oktatóanyag forráskódját le is töltheti. A forráskód az oktatóanyag-AI-gazdagodás mappában található az [Azure-Search-DotNet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) adattárban.

## <a name="1---create-services"></a>1 – szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Searcht használja az indexeléshez és a lekérdezésekhez, Cognitive Services a mesterséges intelligencia-bővítéshez és az Azure Blob Storage-hoz, hogy megadja az adatforrásokat. Ez az oktatóanyag napi 20 tranzakció ingyenes kiosztása alatt marad Cognitive Serviceson, így az egyetlen szükséges szolgáltatás a keresés és a tárolás.

Ha lehetséges, hozzon létre mindkettőt ugyanabban a régióban és erőforráscsoporthoz a közelség és a kezelhetőség érdekében. A gyakorlatban az Azure Storage-fiók bármely régióban lehet.

### <a name="start-with-azure-storage"></a>Első lépések az Azure Storage-ban

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/) , és kattintson az **+ erőforrás létrehozása**elemre.

1. Keressen rá a *Storage-fiókra* , és válassza ki a Microsoft Storage-fiók ajánlatát.

   ![Storage-fiók létrehozása](media/cognitive-search-tutorial-blob/storage-account.png "Storage-fiók létrehozása")

1. Az alapok lapon a következő elemek szükségesek. Minden más esetében fogadja el az alapértelmezett értékeket.

   + **Erőforráscsoport**. Válasszon ki egy meglévőt, vagy hozzon létre egy újat, de ugyanazt a csoportot használja az összes szolgáltatáshoz, hogy együtt lehessen kezelni őket.

   + A **Storage-fiók neve**. Ha úgy gondolja, hogy több erőforrása is van ugyanazzal a típussal, használja a nevet típus és régió szerint egyértelműsítse, például *blobstoragewestus*. 

   + **Hely**. Ha lehetséges, válassza ki ugyanazt a helyet, amelyet az Azure Cognitive Search és Cognitive Services használ. Egyetlen hely érvényteleníti A sávszélességgel kapcsolatos díjakat.

   + **Fiók típusa**. Válassza ki az alapértelmezett *StorageV2 (általános célú v2)*.

1. A szolgáltatás létrehozásához kattintson a **felülvizsgálat + létrehozás** lehetőségre.

1. A létrehozás után kattintson **az erőforrás** megnyitása lehetőségre az Áttekintés lap megnyitásához.

1. Kattintson a **Blobok** szolgáltatás elemre.

1. Kattintson a **+ tároló** elemre egy tároló létrehozásához, és nevezze el a *fogaskerék-Search-demo kifejezést*.

1. Válassza a *fogaskerék-keresés-bemutató* lehetőséget, majd kattintson a **feltöltés** gombra, és nyissa meg azt a mappát, ahová a letöltött fájlokat mentette. Válassza az összes tizennégy fájl lehetőséget, majd kattintson **az OK** gombra a feltöltéshez.

   ![Minta fájlok feltöltése](media/cognitive-search-quickstart-blob/sample-data.png "Minta fájlok feltöltése")

1. Mielőtt elkezdené az Azure Storage-t, szerezzen be egy kapcsolatok karakterláncot, hogy az Azure Cognitive Searchban is létrehozhat egy kapcsolatokat. 

   1. Lépjen vissza a Storage-fiók áttekintés lapjára (az általunk használt *blobstoragewestus* példaként használták). 
   
   1. A bal oldali navigációs panelen válassza a **hozzáférési kulcsok** lehetőséget, és másolja a kapcsolati karakterláncok egyikét. 

   A kapcsolódási karakterlánc az alábbi példához hasonló URL-cím:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Mentse a kapcsolódási karakterláncot a Jegyzettömbbe. Később szüksége lesz rá az adatforrás-kapcsolatok beállításakor.

### <a name="cognitive-services"></a>Cognitive Services

A mesterséges intelligenciát Cognitive Services támogatja, beleértve a természetes nyelv és a képfeldolgozás Text Analytics és Computer Vision. Ha a cél egy tényleges prototípus vagy projekt teljesítése volt, akkor Cognitive Services (ugyanabban a régióban, mint az Azure Cognitive Search), hogy az indexelési műveletekhez csatolható legyen.

Ennél a gyakorlatnál azonban kihagyhatja az erőforrások kiosztását, mivel az Azure Cognitive Search képes csatlakozni a háttérben a Cognitive Serviceshoz, és az indexelő futtatásakor 20 ingyenes tranzakciót biztosít. Mivel ez az oktatóanyag 14 tranzakciót használ, az ingyenes kiosztás elegendő. Nagyobb projektek esetében tervezze meg Cognitive Services kiépítés az utólagos elszámolású S0 szinten. További információ: [Cognitive Services csatolása](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A harmadik összetevő az Azure Cognitive Search, amelyet [a portálon lehet létrehozni](search-create-service-portal.md). A bemutató elvégzéséhez használhatja az ingyenes szintet. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Rendszergazdai API-kulcs és URL-cím beszerzése az Azure Cognitive Search

Az Azure Cognitive Search szolgáltatással való kommunikációhoz szüksége lesz a szolgáltatás URL-címére és egy hozzáférési kulcsra. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   Kérje le a lekérdezési kulcsot is. Ajánlott a lekérdezési kérelmeket csak olvasási hozzáféréssel kibocsátani.

   ![A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése](media/search-get-started-nodejs/service-name-and-keys.png)

Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="2---set-up-your-environment"></a>2 – a környezet beállítása

Először nyissa meg a Visual studiót, és hozzon létre egy új, a .NET Core-on futtatható Console app-projektet.

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

Az [Azure Cognitive Search .net SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search) néhány ügyféloldali kódtárat tartalmaz, amelyek lehetővé teszik az indexek, az adatforrások, az indexelő és a szakértelmével kezelését, valamint dokumentumok feltöltését és kezelését, valamint lekérdezések futtatását, anélkül, hogy a http-és JSON-adatokkal kellene foglalkoznia. Ezek az ügyféloldali kódtárak mind NuGet-csomagként vannak elosztva.

Ebben a projektben telepítse a NuGet-csomag 9-es vagy újabb verzióját `Microsoft.Azure.Search` .

1. A böngészőben nyissa meg a [Microsoft. Azure. Search NuGet-csomag lapot](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Válassza ki a legújabb verziót (9 vagy újabb).

1. Másolja a Package Manager-parancsot.

1. Nyissa meg a Package Manager konzolt. Válassza az **eszközök**  >  **NuGet Package**Manager  >  **csomagkezelő konzolt**. 

1. Illessze be és futtassa az előző lépésben másolt parancsot.

Ezután telepítse a legújabb `Microsoft.Extensions.Configuration.Json` NuGet-csomagot.

1. Válassza az **eszközök**  >  **NuGet csomagkezelő**  >  **NuGet-csomagok kezelése a megoldáshoz...** lehetőséget. 

1. Kattintson a **Tallózás** gombra, és keresse meg a `Microsoft.Extensions.Configuration.Json` NuGet csomagot. 

1. Válassza ki a csomagot, válassza ki a projektet, és ellenőrizze, hogy a verzió a legújabb stabil verzió-e, majd kattintson a **telepítés**gombra.

### <a name="add-service-connection-information"></a>Szolgáltatás-összekapcsolási adatok hozzáadása

1. Kattintson a jobb gombbal a projektre a megoldáskezelő, majd **Add**válassza az  >  **új elem hozzáadása..** . lehetőséget. 

1. Nevezze el a fájlt `appsettings.json` , majd válassza a **Hozzáadás**lehetőséget. 

1. A fájl belefoglalása a kimeneti könyvtárba.
    1. Kattintson a jobb gombbal a elemre `appsettings.json` , és válassza a **Tulajdonságok**lehetőséget. 
    1. Módosítsa a másolás értékét a **kimeneti könyvtárba** a **másoláshoz, ha újabb**.

1. Másolja az alábbi JSON-t az új JSON-fájlba.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```
    
Adja hozzá a keresési szolgáltatás és a blob Storage-fiók adatait. Ne felejtse el, hogy ezt az információt az előző szakaszban jelzett szolgáltatás létesítési lépéseiből kérheti le.

A **SearchServiceName**mezőben adja meg a rövid szolgáltatásnév nevét, és ne a teljes URL-címet.

### <a name="add-namespaces"></a>Névterek hozzáadása

A alkalmazásban `Program.cs` adja hozzá a következő névtereket.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Ügyfél létrehozása

Hozza létre a osztály egy példányát a `SearchServiceClient` alatt `Main` .

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`létrehoz egy új `SearchServiceClient` értéket az alkalmazás konfigurációs fájljában (appsettings.js) tárolt értékek használatával.

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

### <a name="add-function-to-exit-the-program-during-failure"></a>Függvény hozzáadása a programból való kilépéshez a hiba során

Ez az oktatóanyag az indexelési folyamat egyes lépéseinek megismerését segíti. Ha van olyan kritikus probléma, amely megakadályozza, hogy a program létrehozza az adatforrást, a készségkészlet, az indexet vagy az indexelő, a program kiírja a hibaüzenetet, és kilép, így a probléma érthető és orvosolható lesz.

A hozzáadásával `ExitProgram` `Main` kezelheti azokat a forgatókönyveket, amelyekhez szükség van a program kilépésére.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 – a folyamat létrehozása

Az Azure Cognitive Searchban az AI-feldolgozás az indexelés (vagy az adatfeldolgozás) során történik. Az útmutató ezen része négy objektumot hoz létre: adatforrás, index definíció, készségkészlet, indexelő. 

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

A `SearchServiceClient` rendelkezik egy `DataSources` tulajdonsággal. Ez a tulajdonság biztosítja az Azure Cognitive Search-adatforrások létrehozásához, listázásához, frissítéséhez vagy törléséhez szükséges összes módszert.

Hozzon létre egy új `DataSource` példányt a hívásával `serviceClient.DataSources.CreateOrUpdate(dataSource)` . `DataSource.AzureBlobStorage`Ehhez meg kell adnia az adatforrás nevét, a kapcsolati karakterláncot és a blob-tároló nevét.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "cog-search-demo",
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

Sikeres kérelem esetén a metódus a létrehozott adatforrást fogja visszaadni. Ha probléma merül fel a kéréssel, például egy érvénytelen paraméterrel, akkor a metódus kivételt jelez.

Most adjon hozzá egy sort a-ben `Main` az `CreateOrUpdateDataSource` imént hozzáadott függvény meghívásához.

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

Fordítsa le és futtassa a megoldást. Mivel ez az első kérés, a Azure Portal ellenőrizheti, hogy az adatforrás létrejött-e az Azure Cognitive Searchban. A keresési szolgáltatás Irányítópult lapján ellenőrizze, hogy az Adatforrások csempén megjelent-e az új elem. Lehet, hogy várnia kell néhány percet, amíg a portáloldal frissül.

  ![Adatforrások csempéje a portálon](./media/cognitive-search-tutorial-blob/data-source-tile.png "Adatforrások csempéje a portálon")

### <a name="step-2-create-a-skillset"></a>2. lépés: készségkészlet létrehozása

Ebben a szakaszban megadhatja az adataira alkalmazni kívánt gazdagító lépések készletét. Az egyes *alkoholtartalom-növelési lépéseket egy* *készségkészlet*nevezzük, és a dúsítási lépések egyike. Ez az oktatóanyag [beépített kognitív képességeket](cognitive-search-predefined-skills.md) használ a készségkészlet:

+ [Optikai karakterfelismerés](cognitive-search-skill-ocr.md) a nyomtatott és a kézírásos szöveg felismeréséhez a képfájlokban.

+ [Szöveges egyesítéssel](cognitive-search-skill-textmerger.md) egyesítheti a mezőket egy gyűjteményből egyetlen mezőbe.

+ [Nyelvfelismeréssel](cognitive-search-skill-language-detection.md) azonosítja a tartalom nyelvét.

+ A kinyerési képesség és az entitás-felismerési képesség meghívása előtt a [szöveg felosztása](cognitive-search-skill-textsplit.md) a nagyméretű tartalmak kisebb adattömbökbe való bontásához. A fő kifejezés kinyerése és az entitások felismerése 50 000 karakterből álló vagy annál kevesebb bemenetet fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba.

+ [Entitások felismerése](cognitive-search-skill-entity-recognition.md) a blob-tároló tartalmából származó szervezetek nevének kinyeréséhez.

+ A [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) lehívja a leggyakoribb kulcskifejezéseket.

A kezdeti feldolgozás során az Azure Cognitive Search kihasználja az egyes dokumentumokat a különböző fájlformátumokból származó tartalmak olvasásához. A forrásfájlban talált szöveg a létrehozott ```content``` mezőbe kerül, amelyből dokumentumonként egy jön létre. Ennek megfelelően állítsa be a bemenetet úgy, ```"/document/content"``` hogy ezt a szöveget használja. 

A kimenetek hozzárendelhetők egy indexhez, bemenetként használhatók egy alsóbb rétegbeli képességhez, vagy a fentiek mindegyike lehetséges, akárcsak a nyelvkód esetében. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához.

A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>OCR-képesség

Az **OCR** -képesség Kinyeri a szöveget a képekből. Ez a szaktudás azt feltételezi, hogy egy normalized_images mező létezik. A mező létrehozásához az oktatóanyag későbbi részében a ```"imageAction"``` konfigurációt az indexelő definíciójában kell beállítani ```"generateNormalizedImages"``` .

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

### <a name="merge-skill"></a>Szaktudás egyesítése

Ebben a szakaszban egy **egyesítési** képességet hozunk létre, amely egyesíti a dokumentum tartalma MEZŐT az OCR-képesség által létrehozott szöveggel.

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

### <a name="language-detection-skill"></a>Nyelvi észlelési képesség

A **nyelvfelismerési** képesség észleli a bemeneti szöveg nyelvét, és egyetlen nyelvi kódot jelent a kérelemben elküldött összes dokumentumhoz. Az **nyelvfelismerés** skill kimenetét fogjuk használni a **szöveg felosztása** képességbe való bevitel részeként.

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

### <a name="text-split-skill"></a>Szöveg felosztása készség

Az alábbi **felosztott** képesség a szövegeket lapok alapján osztja szét, és az oldal hosszát 4 000 karakterre korlátozza `String.Length` . Az algoritmus megpróbálja felosztani a szöveget a nagy méretű adattömbökbe `maximumPageLength` . Ebben az esetben az algoritmus elvégzi a legjobbat, hogy a mondatot egy mondat határán megtörje, így a tömb mérete valamivel kisebb lehet `maximumPageLength` .

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

### <a name="entity-recognition-skill"></a>Entitás-felismerési szakértelem

Ez a `EntityRecognitionSkill` példány a kategória típusának felismerésére van beállítva `organization` . Az **entitás-felismerési** képesség is felismerheti a kategóriák típusát `person` és a `location` .

Figyelje meg, hogy a "Context" mező ```"/document/pages/*"``` csillaggal van beállítva, ami azt jelenti, hogy a dúsítási lépést minden egyes lapon meg kell hívni ```"/document/pages"``` .

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

### <a name="key-phrase-extraction-skill"></a>Kulcs kifejezés kinyerési készség

Az `EntityRecognitionSkill` imént létrehozott példányhoz hasonlóan a **kulcsszókeresés** skill is meghívva lesz a dokumentum minden oldalára.

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

### <a name="build-and-create-the-skillset"></a>A készségkészlet készítése és létrehozása

`Skillset`Hozza létre az Ön által létrehozott ismeretek használatával.

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

Adja hozzá az alábbi sorokat a következőhöz: `Main` .

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

### <a name="step-3-create-an-index"></a>3. lépés: index létrehozása

Ebben a szakaszban egy indexsémát határoz meg a kereshető indexben szereplő mezők és az egyes mezők keresési attribútumainak megadásával. A mezők típussal is rendelkeznek, emellett olyan attribútumokat tartalmazhatnak, amelyek meghatározzák a mező használatának módját (kereshető, rendezhető stb.). Az indexben szereplő mezőneveknek nem kell pontosan megegyezniük a forrásban szereplő mezők nevével. Egy későbbi lépésben mezőleképezéseket fog hozzáadni egy indexelőhöz a forrás-cél mezőkhöz való csatlakozás céljából. Ebben a lépésben a keresőalkalmazásra vonatkozó mezőelnevezési konvenciók használatával határozza meg az indexet.

A gyakorlat során az alábbi mezőket és mezőtípusokat használjuk:

| Mezőnevek | Mezőtípusok |
| --- | --- |
| id | Edm.String |
| tartalom | Edm.String |
| languageCode | Edm.String |
| keyPhrases | List<Edm.String> |
| organizations | List<Edm.String> |

#### <a name="create-demoindex-class"></a>DemoIndex osztály létrehozása

Az index mezői a Model osztály használatával vannak meghatározva. A modellosztály minden tulajdonsága olyan attribútumokkal rendelkezik, amelyek meghatározzák a vonatkozó indexmező kereséssel kapcsolatos viselkedéseit. 

Hozzáadjuk a modell osztályt egy új C#-fájlhoz. Kattintson a jobb gombbal a projektre **Add**, és válassza  >  az**új elem hozzáadása...** lehetőséget, válassza a "class" elemet, és nevezze el a fájlt `DemoIndex.cs` , majd válassza a **Hozzáadás**lehetőséget.

Ügyeljen arra, hogy a és a névterek típusait is használni kívánja `Microsoft.Azure.Search` `Microsoft.Azure.Search.Models` .

Adja hozzá az alábbi modell osztály definícióját, `DemoIndex.cs` és vegye fel azt ugyanabban a névtérbe, amelyben létre kívánja hozni az indexet.

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

Most, hogy meghatározta a modell osztályt, a vissza lehetőséggel `Program.cs` könnyedén létrehozhat egy index-definíciót. Az index neve a következő lesz: `demoindex` . Ha már létezik ilyen nevű index, a rendszer törli.

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

A tesztelés során előfordulhat, hogy egynél többször próbálkozik az index létrehozásával. Emiatt ellenőrizze, hogy a létrehozni kívánt index már létezik-e, mielőtt megpróbálja létrehozni.

Adja hozzá az alábbi sorokat a következőhöz: `Main` .

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Microsoft.Azure.Search.Models.Index demoIndex = CreateDemoIndex(serviceClient);
```

Adja hozzá a következő using utasítást a egyértelműsítse-hivatkozás feloldásához.

```csharp
using Index = Microsoft.Azure.Search.Models.Index;
```

Az index definiálásával kapcsolatos további tudnivalókért tekintse meg az [index létrehozása (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)című témakört.

### <a name="step-4-create-and-run-an-indexer"></a>4. lépés: indexelő létrehozása és futtatása

Eddig létrehozott egy adatforrást, egy képességcsoportot és egy indexet. Ez a három összetevő egy olyan [indexelő](search-indexer-overview.md) része lesz, amely az egyes részeket egyetlen többszakaszos műveletben egyesíti. A három rész egy indexelőben való egyesítéséhez mezőleképezéseket kell meghatároznia.

+ A fieldMappings a rendszer az adatforrásból a készségkészlet, a leképezési forrás mezőinek feldolgozásával dolgozza fel az indexben lévő mezőkbe. Ha a mezők nevei és típusai mindkét végén azonosak, nincs szükség leképezésre.

+ A outputFieldMappings a készségkészlet után dolgozzák fel, hivatkozva a sourceFieldNames, amelyek nem léteznek a dokumentum repedésének vagy dúsításának létrehozásakor. A targetFieldName egy index mezője.

A bemenetek kimenetekhez való csatlakoztatása mellett mező-hozzárendelések is használhatók az adatstruktúrák leállításához. További információ: [a dúsított mezők leképezése kereshető indexbe](cognitive-search-output-field-mapping.md).

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
Adja hozzá az alábbi sorokat a következőhöz: `Main` .

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer and executing the pipeline...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Várható, hogy az indexelő létrehozása eltarthat egy kis ideig. Annak ellenére, hogy az adatkészlet kis méretű, az analitikai képességek számítási igénye nagy. Néhány képesség, például a képelemzés futásideje hosszú.

> [!TIP]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma lép fel az adatok elérésével, a bemenetek és kimenetek leképezésével vagy a műveletek sorrendjével kapcsolatban, az ebben a szakaszban jelenik meg.

### <a name="explore-creating-the-indexer"></a>Az indexelő létrehozásának megismerése

A kód a ```"maxFailedItems"``` -1 értékre van kijelölve, amely arra utasítja az indexelési motort, hogy hagyja figyelmen kívül a hibákat az adatimportálás során Ez azért hasznos, mert az adatforrás kevés dokumentumot tartalmaz. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Azt is figyelje ```"dataToExtract"``` meg, hogy a be van állítva ```"contentAndMetadata"``` . Ez az utasítást meghatározza, hogy az indexelő automatikusan kinyerje a tartalmat a különböző fájlformátumokból, beleértve az egyes fájlokra vonatkozó metaadatokat is.

Tartalom kinyerésekor az `imageAction` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A ```"imageAction"``` konfiguráció beállítása ```"generateNormalizedImages"``` , az OCR-képesség és a szöveges egyesítési képesség kombinálva azt jelzi, hogy az indexelő Kinyeri a szöveget a képekből (például a "Leállítás" szót egy forgalom leállításakor), és beágyazza a tartalom mezőbe. Ez a működés mind a dokumentumokban beágyazott képekre (például egy PDF-fájlban található képre), mind az adatforrásban talált képekre (például egy JPG-fájlra) vonatkozik.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 – indexelés figyelése

Az indexelő meghatározását követően az indexelő a kérés elküldésekor automatikusan lefut. Az indexelés a vártnál tovább is eltarthat attól függően, hogy mely kognitív képességeket adta meg. Ha szeretné megtudni, hogy az indexelő továbbra is fut-e, használja a `GetStatus` metódust.

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

`IndexerExecutionInfo`az indexelő aktuális állapotát és végrehajtási előzményeit jelöli.

A figyelmeztetések bizonyos forrásfájl- és képességkombinációk esetében gyakoriak, és nem mindig utalnak hibára. Ebben az oktatóanyagban a figyelmeztetések jóindulatúak (például nincs szöveges bemenet a JPEG-fájlokból).

Adja hozzá az alábbi sorokat a következőhöz: `Main` .

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 – keresés

Az indexelés befejezése után futtathat olyan lekérdezéseket, amelyek az egyes mezők tartalmát adják vissza. Alapértelmezés szerint az Azure Cognitive Search a legfontosabb 50 eredményeket adja vissza. A mintaadatok mérete kicsi, ezért az alapértelmezett beállítás megfelelő. Azonban nagyobb méretű adatkészletek esetén előfordulhat, hogy a lekérdezési sztringben meg kell adnia a megfelelő paramétereket, hogy a rendszer több eredményt adjon vissza. Útmutatásért lásd: [How to Page results in Azure Cognitive Search](search-pagination-page-layout.md).

Ellenőrzési lépésként kérdezze le az index összes mezőjét.

Adja hozzá az alábbi sorokat a következőhöz: `Main` .

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

`CreateSearchIndexClient`létrehoz egy új `SearchIndexClient` értéket az alkalmazás konfigurációs fájljában (appsettings.js) tárolt értékek használatával. Figyelje meg, hogy a keresési szolgáltatás lekérdezési API-kulcsa használatos, nem pedig a rendszergazdai kulcs.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Adja hozzá az alábbi kódot a következőhöz: `Main` . Az első try-catch az index definícióját adja vissza az egyes mezők neve, típusa és attribútumai szerint. A második egy paraméteres lekérdezés, ahol `Select` megadhatja, hogy mely mezők szerepeljenek az eredményekben, például: `organizations` . A keresési sztring egy adott `"*"` mező összes tartalmát adja vissza.

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

Ismételje meg a műveletet további mezők esetén: tartalom, languageCode, kifejezés és szervezet ebben a gyakorlatban. Vesszővel tagolt lista használatával több mezőt is visszaadhat a [Select](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) tulajdonságon keresztül.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszaiban a tervezési iteráció legalkalmasabb megközelítése az objektumok törlése az Azure Cognitive Search és a kód újraépítésének engedélyezése. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Az oktatóanyaghoz tartozó mintakód ellenőrzi a meglévő objektumokat, és törli őket, hogy újra lehessen futtatni a kódot.

A portál segítségével indexeket, indexelő fájlokat, adatforrásokat és szakértelmével is törölhet.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag azt mutatja be, hogyan hozhatók létre egy bővített indexelési folyamat az összetevők létrehozásakor: adatforrás, készségkészlet, index és indexelő.

A [beépített készségek](cognitive-search-predefined-skills.md) a készségkészlet-definícióval és a képességek láncolásával együtt a bemeneteken és kimeneteken keresztül is elérhetők. Azt is megtanulta, hogy `outputFieldMappings` az indexelő definíciója szerint a folyamatból származó, az Azure Cognitive Search szolgáltatásban kereshető indexre való átirányításhoz szükséges.

Végül megismerte, hogyan tesztelheti az eredményeket, és hogyan állíthatja alaphelyzetbe a rendszert a későbbi futtatásokhoz. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. Emellett azt is megtanulta, hogyan ellenőrizheti az indexelő állapotát, illetve hogy melyik objektumokat kell törölnie a folyamat újrafuttatása előtt.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha a saját előfizetésében dolgozik, a projekt végén érdemes lehet eltávolítani a már nem szükséges erőforrásokat. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a minden erőforrás vagy erőforráscsoport hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a mesterséges intelligencia-bővítési folyamat összes objektumát, ismerkedjen meg közelebbről a készségkészlet-definíciókkal és az egyéni ismeretekkel.

> [!div class="nextstepaction"]
> [Készségkészlet létrehozása](cognitive-search-defining-skillset.md)
