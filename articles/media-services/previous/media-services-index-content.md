---
title: Médiafájlok indexelése Azure Media Indexer
description: Azure Media Indexer lehetővé teszi, hogy a médiafájlokat kereshetővé tegye, és teljes szöveges átiratot hozzon a kódolt feliratok és kulcsszavak számára. Ez a témakör a Media Indexer használatát mutatja be.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 0987e15b1619fcd4c1c79f9a61420c092a7da886
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529188"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Médiafájlok indexelése Azure Media Indexer

> [!NOTE]
> A [Azure Media Indexer](media-services-index-content.md) adathordozó-processzor a 2020-es október 1-én megszűnik. [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) lecseréli ezt az örökölt adathordozó-processzort. További információ: [áttelepítés Azure Media Indexerról és Azure Media Indexer 2 – Azure Media Services video Indexer](migrate-indexer-v1-v2.md).

Azure Media Indexer lehetővé teszi, hogy a médiafájlokat kereshetővé tegye, és teljes szöveges átiratot hozzon a kódolt feliratok és kulcsszavak számára. Feldolgozható egyetlen médiafájl is, vagy egy kötegben több médiafájl is.  

Tartalom indexelése esetén ügyeljen arra, hogy a tiszta beszédtel (háttérzene, zaj, effektusok vagy mikrofon nélkül sziszegve) médiafájlokat használjon. Néhány példa a megfelelő tartalomra: rögzített értekezletek, előadások vagy bemutatók. Előfordulhat, hogy az alábbi tartalmak nem alkalmasak indexelésre: filmek, TV-műsorok, bármilyen kevert hang-és hanghatások, a rosszul rögzített tartalom (sziszegés).

Az indexelési feladatok a következő kimeneteket hozhatják elő:

* A lezárt képaláírás-fájlok formátuma a következő: **Sami**, **TTML**és **WebVTT**.
  
    A kódolt feliratos fájlok közé tartozik a felismerhetőség nevű címke, amely a forrás videójában található beszéd felismerhetővé módját mutatja.  Használhatja a felismerhetőség értékét a felhasználhatóságra szolgáló kimeneti fájlok megjelenítéséhez. Az alacsony pontszám a hangminőség miatt gyenge indexelési eredményeket jelent.
* Kulcsszóválasztó fájl (XML).
* Az SQL Serverrel való használatra szolgáló AIB-fájl.
  
    További információ: [AIB-fájlok használata Azure Media Indexer és SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

Ez a cikk bemutatja, hogyan hozhat létre indexelési feladatokat **egy eszköz indexeléséhez** és **több fájl indexeléséhez**.

A legújabb Azure Media Indexer frissítésekkel kapcsolatban lásd: [Media Services Blogok](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Konfigurációs és MANIFEST-fájlok használata az indexelési feladatokhoz
Az indexelési feladatokhoz több részletet is megadhat a feladatok konfigurációjának használatával. Megadhatja például, hogy mely metaadatokat szeretné használni a médiafájlhoz. Ezt a metaadatokat a nyelvi motor használja a szókincs kibontásához, és nagy mértékben javítja a beszédfelismerés pontosságát.  A kívánt kimeneti fájlokat is meg tudja adni.

Egy jegyzékfájl használatával egyszerre több médiafájl is feldolgozható.

További információ: [Azure Media Indexer feladatának előre definiálása](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Eszköz indexelése
A következő módszer egy médiafájlt tölt fel egy eszközként, és létrehoz egy feladatot az eszköz indexeléséhez.

Ha nincs megadva konfigurációs fájl, a rendszer az összes alapértelmezett beállítással indexeli a médiafájlt.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a id="output_files"></a>Kimeneti fájlok
Alapértelmezés szerint az indexelési feladatok a következő kimeneti fájlokat generálják. A fájlokat az első kimeneti eszköz tárolja.

Ha egynél több bemeneti médiafájl található, az indexelő egy jegyzékfájlt hoz létre a feladatok kimenetéhez, amelynek neve "JobResult. txt". Minden bemeneti médiafájl esetében az eredményül kapott AIB, SAMI, TTML, WebVTT és keyword fájlok egymás után sorszámozással és névvel vannak elnevezve az "alias" használatával.

| Fájlnév | Leírás |
| --- | --- |
| **InputFileName. AIB** |Az audio-indexelési blob-fájl. <br/><br/> A hangindexelési blob (AIB) fájl egy bináris fájl, amely a teljes szöveges keresés használatával kereshető a Microsoft SQL Serverben.  A AIB-fájl nagyobb teljesítményű, mint az egyszerű képaláírás-fájl, mivel az egyes szavakra vonatkozó alternatívákat tartalmaz, így sokkal gazdagabb keresési élmény érhető el. <br/> <br/>Az indexelő SQL-bővítmény telepítését igényli a Microsoft SQL Server 2008-es vagy újabb verzióját futtató gépen. A AIB való keresés a Microsoft SQL Server teljes szöveges keresési funkciójával pontosabb keresési eredményeket biztosít, mint a WAMI által generált zárt feliratú fájlok keresése. Ennek az az oka, hogy a AIB olyan Word-alternatívákat tartalmaz, amelyek hasonlóak, míg a lezárt képaláírás-fájlok a legmagasabb megbízhatósági szót tartalmazzák a hang egyes szegmensei esetében. Ha a kimondottan felhasználható szavakat keresik, akkor azt javasoljuk, hogy a AIB együtt használja a Microsoft SQL Server. <br/><br/>Más keresőmotorokat is használhat, például az Apache Lucene/Solr-t, hogy egyszerűen indexelje a videót a kódolt felirat és a kulcsszó XML-fájlja alapján, de ez kevésbé pontos keresési eredményeket fog eredményezni. |
| **InputFileName. SMI**<br/>**InputFileName. ttml**<br/>**InputFileName. VTT** |Lezárt feliratú (CC) fájlok a SAMI, a TTML és a WebVTT formátumban.<br/><br/>Használhatók a hang-és videofájlok elérhetővé tételéhez a fogyatékkal élők számára.<br/><br/>A kódolt feliratú fájlok közé tartozik egy <b>felismerhetőség</b> nevű címke, amely a forrás videójában található beszéd felismerhetővé módját mutatja.  Használhatja a <b>felismerhetőség</b> értékét a felhasználhatóságra szolgáló kimeneti fájlok megjelenítéséhez. Az alacsony pontszám a hangminőség miatt gyenge indexelési eredményeket jelent. |
| **InputFileName. kW. XML <br/>InputFileName. info** |Kulcsszó-és információs fájlok. <br/><br/>A Kulcsszóválasztó fájl egy XML-fájl, amely a beszédfelismerési tartalomból kinyert kulcsszavakat tartalmazza a gyakorisággal és az eltolással kapcsolatos információkkal. <br/><br/>Az információs fájl egy egyszerű szöveges fájl, amely részletes információkat tartalmaz az egyes felismert feltételekről. Az első sor speciális, és tartalmazza a felismerhetőség pontszámát. Minden további sor a következő adatok tabulátorral tagolt listája: kezdési idő, befejezési idő, szó/kifejezés, megbízhatóság. A rendszer másodpercek alatt adja meg az időpontokat, és a megbízhatóságot a 0-1-es számként adja meg. <br/><br/>Példa sor: "1,20 1,45 Word 0,67" <br/><br/>Ezek a fájlok számos célra használhatók, például a beszédfelismerési elemzések elvégzéséhez, vagy a keresőprogramok, például a Bing, a Google vagy a Microsoft SharePoint használatával, hogy a médiafájlok könnyebben felderíthetők legyenek, vagy akár több releváns hirdetés nyújtására is használhatók. |
| **JobResult. txt** |Kimeneti jegyzékfájl, amely csak több fájl indexelése esetén jelenik meg, a következő információkat tartalmazza:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Hiba</th></tr><tr><td>egy. MP4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b. MP4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c. MP4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Ha nem az összes bemeneti médiafájl indexelve lett, az indexelési feladatok a 4000 hibakód miatt meghiúsulnak. További információ: [hibakódok](#error_codes).

## <a name="index-multiple-files"></a>Több fájl indexelése
A következő módszer több médiafájlt tölt fel egy eszközként, és létrehoz egy feladatot, amely az összes fájlt egy kötegben indexeli.

A rendszer létrehoz egy ". lst" kiterjesztésű jegyzékfájlt, és feltölti az eszközre. A manifest-fájl tartalmazza az összes objektum fájljának listáját. További információ: [Azure Media Indexer feladatának előre definiálása](https://msdn.microsoft.com/library/dn783454.aspx).

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>Részben sikeres feladatok
Ha nem az összes bemeneti médiafájl indexelve lett, az indexelési feladat sikertelen lesz, hibakód: 4000. További információ: [hibakódok](#error_codes).

Ugyanezek a kimenetek (a sikeres feladatok) jönnek létre. Tekintse át a kimeneti jegyzékfájlt, és ellenőrizze, hogy mely bemeneti fájlok sikertelenek, a hiba oszlop értékeinek megfelelően. A sikertelen bemeneti fájlok esetében az eredményül kapott AIB, SAMI, TTML, WebVTT és keyword fájlok nem lesznek létrehozva.

### <a id="preset"></a>Azure Media Indexer feladatának beállítása
A Azure Media Indexerból történő feldolgozás testreszabható úgy, hogy a feladattal együtt egy opcionális feladattal is rendelkezik.  Az alábbiakban a konfigurációs XML formátumát ismertetjük.

| Név | Kötelező | Leírás |
| --- | --- | --- |
| **bemeneti** |hamis |Az indexelni kívánt adatfájl (ok).</p><p>A Azure Media Indexer a következő médiafájl-formátumokat támogatja: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Megadhatja a fájl nevét (ke) t a **bemeneti** elem **Name** vagy **List** attribútumában (az alább látható módon). Ha nem határozza meg, hogy melyik adatfájlt szeretné indexelni, az elsődleges fájl lesz kiválasztva. Ha nincs beállítva elsődleges adatfájl, a bemeneti eszköz első fájlja indexelve lesz.</p><p>Ha explicit módon meg szeretné adni az eszköz fájljának nevét, tegye a következőket:<br/>`<input name="TestFile.wmv">`<br/><br/>Egyszerre több adatfájlt is indexelheti (legfeljebb 10 fájlt). Ehhez tegye a következőket:<br/><br/><ol class="ordered"><li><p>Hozzon létre egy szövegfájlt (manifest-fájlt), és adjon meg egy. lst kiterjesztést. </p></li><li><p>Adja meg a bemeneti objektumban található összes adatfájl nevét ebbe a jegyzékfájlba. </p></li><li><p>Adja hozzá (feltölti) a jegyzékfájlt az objektumhoz.  </p></li><li><p>Adja meg a jegyzékfájl nevét a bemenet List attribútumában.<br/>`<input list="input.lst">`</li></ol><br/><br/>Megjegyzés: Ha 10-nél több fájlt ad hozzá a jegyzékfájlhoz, az indexelési feladat sikertelen lesz az 2006-es hibakód miatt. |
| **metaadatok** |hamis |A szókincs-átalakításhoz használt megadott adatfájl (ok) metaadatai.  Az indexelő előkészítése hasznos lehet a nem szabványos szókincs-szavak, például a megfelelő főnevek felismerésére.<br/>`<metadata key="..." value="..."/>` <br/><br/>Megadhatja az előre definiált **kulcsok** **értékeit** . Jelenleg a következő kulcsok támogatottak:<br/><br/>"title" és "Description" – a szókincs adaptációja a feladatokhoz használt nyelvi modell finomhangolásához és a beszédfelismerés pontosságának javításához.  Az értékek a mag internetes keresésével keresik a kontextusban releváns szöveges dokumentumokat, a tartalom használatával kiegészítik a belső szótárt az indexelési feladat időtartamára.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **szolgáltatások** <br/><br/> Az 1,2-es verzióban lett hozzáadva. Jelenleg az egyetlen támogatott funkció a beszédfelismerés ("ASR"). |hamis |A beszédfelismerési funkció a következő beállítások kulcsokkal rendelkezik:<table><tr><th><p>Jelmagyarázat</p></th>        <th><p>Leírás</p></th><th><p>Példaérték</p></th></tr><tr><td><p>Nyelv</p></td><td><p>A multimédiás fájlban felismerhető természetes nyelv.</p></td><td><p>Angol, Spanyol</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>a kívánt kimeneti felirat formátumának pontosvesszővel tagolt listája (ha van ilyen)</p></td><td><p>ttml; számi; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Logikai jelző, amely meghatározza, hogy szükség van-e AIB-fájlra (a SQL Server és a Customer indexelő IFilter szolgáltatással való használatra).  További információ: <a href="https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">AIB-fájlok használata Azure Media Indexer és SQL Server</a>.</p></td><td><p>Igaz Hamis</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Logikai jelző, amely meghatározza, hogy szükség van-e egy kulcsszó XML-fájlra.</p></td><td><p>Igaz Hamis. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Logikai jelző, amely meghatározza, hogy a teljes feliratok (a megbízhatósági szinttől függetlenül) legyenek kényszerítve.  </p><p>Az alapértelmezett érték false (hamis), amelyben az olyan szavak és kifejezések, amelyek 50%-nál kevesebb megbízhatósági szinttel rendelkeznek, kimaradnak a végső képaláírás-kimenetből, és az ellipszisek ("...") helyébe lépnek.  Az ellipszisek hasznosak a feliratok minőségének ellenőrzéséhez és a naplózáshoz.</p></td><td><p>Igaz Hamis. </p></td></tr></table> |

### <a id="error_codes"></a>Hibakódok
Hiba esetén Azure Media Indexer a következő hibakódok egyikét kell jelentenie:

| Kód | Név | Lehetséges okok |
| --- | --- | --- |
| 2000 |Érvénytelen konfiguráció |Érvénytelen konfiguráció |
| 2001 |Érvénytelen bemeneti eszközök |Hiányzó bemeneti eszközök vagy üres eszköz. |
| 2002 |Érvénytelen jegyzékfájl |A jegyzékfájl üres, vagy a jegyzékfájl érvénytelen elemeket tartalmaz. |
| 2003 |A médiafájl letöltése nem sikerült |Érvénytelen URL-cím a jegyzékfájlban. |
| 2004 |Nem támogatott protokoll |A Media URL-cím protokollja nem támogatott. |
| 2005 |Nem támogatott fájltípus |A bemeneti médiafájl típusa nem támogatott. |
| 2006 |Túl sok bemeneti fájl |A bemeneti jegyzékfájlban több mint 10 fájl található. |
| 3000 |Nem sikerült dekódolni a médiafájlt |Nem támogatott adathordozó-kodek <br/>vagy<br/> Sérült médiafájl <br/>vagy<br/> Nincs hangadatfolyam a bemeneti adathordozón. |
| 4000 |A kötegelt indexelés részben sikerült |A bemeneti médiafájlok némelyikét nem sikerült indexelni. További információ: <a href="#output_files">kimeneti fájlok</a>. |
| other |Belső hibák |Forduljon a támogatási csoporthoz. indexer@microsoft.com |

## <a id="supported_languages"></a>Támogatott nyelvek
Jelenleg az angol és a spanyol nyelv támogatott. További információ: [a v 1.2 kiadási blogbejegyzést](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[AIB-fájlok használata Azure Media Indexer és SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Médiafájlok indexelése Azure Media Indexer 2 előzetes verzióval](media-services-process-content-with-indexer2.md)

