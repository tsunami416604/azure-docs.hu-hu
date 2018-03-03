---
title: "Az Azure Media Indexer médiafájlok indexelő"
description: "Az Azure Media Indexer lehetővé teszi, hogy a médiafájlok tartalmának kereshető és a teljes szöveges Beszélgetés szövegének lezárt feliratok és kulcsszavak készítése. Ez a témakör bemutatja, hogyan Media Indexer használatára."
services: media-services
documentationcenter: 
author: Asolanki
manager: cfowler
editor: 
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/20/2017
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: 9893372fe00cf1a6b15f9b358b64d77ee5d34872
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Az Azure Media Indexer médiafájlok indexelő
Az Azure Media Indexer lehetővé teszi, hogy a médiafájlok tartalmának kereshető és a teljes szöveges Beszélgetés szövegének lezárt feliratok és kulcsszavak készítése. Feldolgozható egyetlen médiafájl is, vagy egy kötegben több médiafájl is.  

> [!IMPORTANT]
> Tartalom indexelő, ellenőrizze, törölje a jelet beszéd (nélkül háttér zene, zaj, hatások vagy mikrofon hiss) rendelkező médiafájlok használandó. Néhány példa a megfelelő tartalom: értekezletek, előadások vagy bemutatók rögzíti. A következő tartalmat nem lehet megfelelő indexelő: filmek, tévéműsorok, semmi a vegyes hang- és hatást, rosszul rögzített háttérzaj (hiss) tartalmát.
> 
> 

Az indexelő feladat a következő kimenetek hozhat létre:

* Lezárt felirat fájlok a következő formátumban: **SZÁMI**, **TTML**, és **WebVTT**.
  
    Feliratfájlokat-fájlok közé tartoznak a Recognizability, mely egy indexelő feladat alapján, hogyan ismerhető forrás videóban beszéd pontszámok nevű címkét.  Képernyő kimeneti fájlok használhatóság Recognizability értékének segítségével. Alacsony pontszámmal jelentené rossz indexelési eredmények minőségi hang miatt.
* Kulcsszó fájl (XML).
* Hang való használathoz az SQL server az indexelés (AIB) fájlját.
  
    További információkért lásd: [AIB fájlok használata az Azure Media Indexer és az SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

Ez a cikk bemutatja, hogyan hozzon létre az indexelő feladatokat az **egy eszköz Index** és **több fájl Index**.

A legújabb Azure Media Indexer frissítéseket, lásd: [Media Services blogok](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Konfigurációs és a jegyzékfájl-fájlokat használ a indexelési feladatok
Az indexelő tevékenységek további részleteket adhat meg a feladat konfigurációja segítségével. Megadhatja például, mely metaadatok a médiafájl használatára. A metaadatok segítségével a nyelv motor bontsa ki a szóhasználatának, és nagy mértékben csökkenti a beszédfelismerés pontosságát.  Akkor is láthatóvá válnak adja meg a kívánt kimeneti fájlokat.

Több médiafájlok egyszerre is feldolgozhat jegyzékfájlt segítségével.

További információkért lásd: [feladat az adott néven beállítás az Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Egy eszköz index
A következő metódus feltölt egy médiafájlt eszközként, és létrehoz egy feladatot az eszköz indexelésre.

Ha nincs konfigurációs fájl van megadva, a médiafájl indexelt alapértelmezett beállításokkal.

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
Alapértelmezés szerint egy indexelő feladat a következő kimeneti fájlokat generál. A fájlok az első kimeneti eszköz találhatók.

Ha egynél több bemeneti médiafájlt, indexelő állít elő, a feladatot kimenetek "JobResult.txt" nevű jegyzékfájlt. Az egyes bemeneti médiafájlt, az eredményül kapott AIB, SZÁMI, TTML, WebVTT kulcsszó fájlok, egymás után számozott és nevű, "Aliasneve." használata

| Fájlnév | Leírás |
| --- | --- |
| **InputFileName.aib** |Az indexelő blob hangfájl. <br/><br/> Hangfájl indexelő Blob (AIB) egy olyan bináris fájl, a Microsoft SQL server teljes szöveges keresés kereshető.  A AIB fájl nem sokkal hatékonyabb, mint az egyszerű felirat fájlok, mert minden egyes szó, így jóval szélesebb keresésekhez alternatívák tartalmaz. <br/> <br/>A számítógépen futó Microsoft SQL server 2008 vagy újabb indexelő SQL bővítmény telepítése szükséges hozzá. Keresés a AIB használatával a Microsoft SQL server teljes szöveges keresés biztosít pontosabb keresési eredmények, mint a WAMI által előállított feliratfájlokat fájlokat. Ennek az az oka a AIB tartalmaz, amely hasonló hangkártya, mivel a feliratfájlokat fájlok tartalmazzák a legmagasabb abban, hogy minden szegmensben, a hang word word-alternatívák. Ha szóbeli szavak keresése upmost fontos, akkor javasoljuk, hogy a Microsoft SQL Server a AIB a együtt használja.<br/><br/> A bővítmény letöltése a <a href="http://aka.ms/indexersql">Azure Media Indexer SQL bővítmény</a>. <br/><br/>Is lehetséges, hogy használják a más keresőprogramok, például az Apache Lucene/Solr egyszerűen indexelésre a videó feliratfájlokat és XML-fájlok kulcsszó alapján, de ez kevésbé pontos keresés eredménye eredményez. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |Bezárt felirat (CC) fájlok SZÁMI TTML és WebVTT formátumban.<br/><br/>Hang- és fájlok elérhetővé a személyek nyújtanak segítséget fogyatékkal használható.<br/><br/>Lezárt felirat-fájlok közé tartoznak a címke neve <b>Recognizability</b> amely pontszámaihoz egy alapján, hogyan ismerhető forrás videóban beszéd indexelő feladat.  Használhatja a értékének <b>Recognizability</b> képernyő kimeneti fájlok használhatóság. Alacsony pontszámmal jelentené rossz indexelési eredmények minőségi hang miatt. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Kulcsszó és az adatok fájlokat. <br/><br/>A fájl kulcsszó gyakorisággal és oszlopeltolási információ a beszédfelismerés tartalom kinyert kulcsszavak tartalmazó XML-fájl. <br/><br/>A fájl adatait egy egyszerű szöveges fájl, amely minden kifejezéshez ismeri fel a részletes információkat tartalmaz. Az első sor speciális, és tartalmazza a Recognizability pontszámot. Minden egyes őket követő sorban a következő adatok tabulátorral tagolt listáját: Indítsa el az idő, befejezési idő, szó vagy kifejezés, benne. Az idő másodpercben kap, és az vetett bizalmat kap egy számot a 0-1. <br/><br/>Példa sor: "1,20 1.45 word 0.67" <br/><br/>Ezek a fájlok használt több célból, például a beszéd elemzés végrehajtásához, vagy keresőprogramok, például a több megfelelő ads elérhetővé tételére használt Bing, Google vagy a Microsoft SharePoint a médiafájlokat felfedezését, vagy akár kitéve. |
| **JobResult.txt** |Kimeneti jegyzék, jelenik meg, csak ha indexelő több fájl, amely a következő információkat tartalmazza:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Hiba</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Ha nem az összes bemeneti médiafájljait sikeresen indexelt, az indexelő feladat sikertelen lesz, hibakód: 4000. További információkért lásd: [hibakódok](#error_codes).

## <a name="index-multiple-files"></a>Több fájl index
A következő metódus több médiafájlok feltölti eszközként, és létrehoz egy feladatot, ezeket a fájlokat egy kötegben indexelésre.

A jegyzék ".lst" kiterjesztésű fájl létrehozása és feltöltése az objektumba. A jegyzékfájl az adategység-fájloknak listáját tartalmazza. További információkért lásd: [feladat az adott néven beállítás az Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

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

### <a name="partially-succeeded-job"></a>Részlegesen sikeres feladat
Ha nem bemeneti médiafájljait sikeresen indexelt, az indexelő feladat 4000 hibakóddal meghiúsul. További információkért lásd: [hibakódok](#error_codes).

(A sikeres feladat) azonos kimenetek jönnek létre. A kimeneti jegyzékfájl, hogy megtudja, mely a bemeneti fájlokat nem sikerült, hiba oszlop értékek hivatkozhat. A bemeneti fájlokat nem sikerült, az eredményül kapott AIB, SZÁMI, TTML, WebVTT és kulcsszó fájlok nem lesz létrehozva.

### <a id="preset"></a> Az Azure Media indexelő feladat készlet
Az Azure Media Indexer feldolgozása egy választható feladat mellett a feladat az adott néven beállítás megadásával testreszabható.  A következőkben a konfigurációs xml formátumát.

| Name (Név) | Kötelező | Leírás |
| --- | --- | --- |
| **Bemeneti** |false |Eszköz (oka) t, amelyet szeretne index.</p><p>Az Azure Media Indexer támogatja a következő media formátumok: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Adhat meg a fájl neve (s) a **neve** vagy **lista** attribútuma a **bemeneti** elem (mivel lásd alább). Ha nem adja meg a melyik index eszköz fájlt, az elsődleges fájl nek. Ha nem elsődleges eszköz fájl be van állítva, az első fájl a bemeneti objektum indexelt.</p><p>Explicit módon adja meg az eszköz fájl nevét, a következőket kell tennie:<br/>`<input name="TestFile.wmv">`<br/><br/>Több eszköz fájlok egyszerre (legfeljebb 10) is elvégezheti az indexelést. Ehhez tegye a következőket:<br/><br/><ol class="ordered"><li><p>Hozzon létre egy szövegfájlt (jegyzékfájl), és adjon neki egy .lst bővítmény. </p></li><li><p>Az eszköz fájlnevek listáját a bemeneti eszköz hozzáadása a jegyzékfájlt. </p></li><li><p>(Feltöltés) thanifest fájl hozzáadása az eszköz.  </p></li><li><p>A bemeneti lista attribútumban adja meg a jegyzékfájl neve.<br/>`<input list="input.lst">`</li></ol><br/><br/>Megjegyzés: Ha több mint 10 fájlokat ad hozzá a jegyzékfájlt, az indexelő feladat sikertelen lesz, és a 2006 hibakód. |
| **metadata** |false |A megadott objektum (ok) ban szóhasználatának kiigazítása használt metaadatait.  Hasznos ismeri fel a nem szabványos szóhasználatának szavakat, például a tulajdonnevek indexelő előkészítéséhez.<br/>`<metadata key="..." value="..."/>` <br/><br/>Megadhat olyan **értékek** az előre definiált **kulcsok**. Jelenleg a következő kulcsok támogatottak:<br/><br/>"title" és "description" - végeznünk a nyelvi szóhasználatának kiigazítása használt modell, a feladat, és növeli a beszédfelismerés pontosságát.  Az értékek magtípusú leképezést internetes keresés használatával kiegészíthető, mivel a belső szótárban az indexelő feladat időtartama elemzését vonatkozó szövegfájlok kereséséhez.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Szolgáltatások** <br/><br/> A hozzáadott 1.2-es verzióját. Az egyetlen támogatott szolgáltatás jelenleg Beszédfelismerés ("automatikus"). |false |A beszédfelismerés funkció az alábbi beállítások kulcsok rendelkezik:<table><tr><th><p>Kulcs</p></th>        <th><p>Leírás</p></th><th><p>Példaérték</p></th></tr><tr><td><p>Nyelv</p></td><td><p>A természetes nyelvű, hogy felismerje a multimédia fájlban.</p></td><td><p>Angol, spanyol</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>a kívánt felirat formátumokban (ha van ilyen) pontosvesszővel elválasztott listája</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Egy logikai jelző, adja meg, hogy-e egy AIB fájl szükséges (az SQL Server és az ügyfél indexelő IFilter való használatra).  További információkért lásd: <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">AIB fájlok használata az Azure Media Indexer és az SQL Server</a>.</p></td><td><p>Igaz; Hamis</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Egy logikai jelző, adja meg, hogy-e a kulcsszó XML-fájl szükséges.</p></td><td><p>Igaz; Hamis. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>A logikai jelzőt megadó-e teljes feliratok (függetlenül a megbízhatósági szint) kényszerítése.  </p><p>Alapértelmezett érték hamis, ebben az esetben szavak és kifejezések, amelyhez tartozik egy kisebb, mint 50 %-os megbízhatósági szintet kimarad a a végső felirat kimenetek és szerepét a három pontot ("...").  A három pontot felirat minőség-ellenőrzési és a naplózási hasznosak.</p></td><td><p>Igaz; Hamis. </p></td></tr></table> |

### <a id="error_codes"></a>Hibakódok
Hiba, ha az Azure Media Indexer jelenteniük kell biztonsági másolatot a következő hibakódok egyikét:

| Kód | Name (Név) | Lehetséges okok |
| --- | --- | --- |
| 2000 |Érvénytelen konfiguráció |Érvénytelen konfiguráció |
| 2001 |Érvénytelen bemeneti eszközök |Hiányzik a bemeneti eszközök vagy üres eszköz. |
| 2002 |Érvénytelen jegyzék |Jegyzékfájl üres vagy jegyzék érvénytelen elemet tartalmaz. |
| 2003 |Nem sikerült a médiafájl letöltése |URL-cím érvénytelen a jegyzékfájl. |
| 2004 |A protokoll nem támogatott |Media URL-címének protokoll nem támogatott. |
| 2005 |Nem támogatott fájltípus |Bemeneti media fájl típusa nem támogatott. |
| 2006 |Túl sok bemeneti fájl |A bemeneti jegyzékben 10-nél több fájl van. |
| 3000 |Nem sikerült dekódolni a médiafájl |Nem támogatott adathordozó kodek <br/>vagy<br/> Sérült médiafájl <br/>vagy<br/> Nincs hangadatfolyam a bemeneti adathordozóján található. |
| 4000 |Kötegelt indexelő részlegesen sikeres volt. |A bemeneti médiafájljait némelyike indexelése nem sikerült. További információkért lásd: <a href="#output_files">kimeneti fájlok</a>. |
| egyéb |Belső hiba |Lépjen kapcsolatba a terméktámogatási csapathoz. indexer@microsoft.com |

## <a id="supported_languages"></a>Támogatott nyelvek
Jelenleg az angol és spanyol nyelven használható. További információkért lásd: [1.2-es verzió kiadás blogbejegyzést](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services elemző áttekintése](media-services-analytics-overview.md)

[Az Azure Media Indexer és az SQL Server AIB fájlok használata](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Az Azure Media Indexer 2 előzetes verziójú médiafájlok indexelő](media-services-process-content-with-indexer2.md)

