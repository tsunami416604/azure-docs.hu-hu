---
title: A médiafájlokat az Azure Media Indexerrel
description: Az Azure Media Indexer lehetővé teszi a médiafájlok tartalom kereshetővé és a egy lezárt a feliratozás és a kulcsszavak teljes szövegű átiratot létrehozása érdekében. Ez a témakör bemutatja, hogyan használja a Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: cfowler
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/20/2017
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: b6e0b2027dd4edbf1688a6c71efe2dde37fbbf96
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473367"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>A médiafájlokat az Azure Media Indexerrel
Az Azure Media Indexer lehetővé teszi a médiafájlok tartalom kereshetővé és a egy lezárt a feliratozás és a kulcsszavak teljes szövegű átiratot létrehozása érdekében. Feldolgozható egyetlen médiafájl is, vagy egy kötegben több médiafájl is.  

> [!IMPORTANT]
> Ha a tartalom indexelése, ügyeljen arra, hogy egyértelmű speech (nélkül háttér zene, zaj, hatások vagy mikrofon hiss) rendelkező médiafájlokat használó. Néhány példa a megfelelő tartalom: értekezletek, előadások vagy bemutatók rögzíti. A következő tartalmat nem lehet megfelelő, az indexelés: filmek, tévéműsorok karaktereket a vegyes hang és a hatást, rosszul rögzíti a tartalmat, a háttérzaj (hiss).
> 
> 

Egy indexelési feladat a következő kimenetek hozhat létre:

* Az alábbi formátumokban feliratfájlok: **SZÁMI**, **TTML**, és **WebVTT**.
  
    Feliratfájlok Recognizability, mely egy indexelési feladat alapján hogyan ismerhető fel a beszéd, a forrás-videóban pontszámok nevű címke tartalmazza.  Recognizability értékét segítségével képernyő kimeneti fájlok megkönnyítettük a használatát. Alacsony pontszámmal minőségi hang miatt gyenge indexelési eredmények azt.
* Kulcsszófájl (XML).
* Hang blobfájlra (AIB) indexelő az SQL server való használatra.
  
    További információkért lásd: [AIB-fájlok használata az Azure Media Indexer és az SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

Ez a cikk bemutatja, hogyan hozhat létre az indexelési feladatok **adategység Index** és **Index több fájlra**.

A legújabb Azure Media Indexer frissítésekkel kapcsolatban lásd: [Media Services-blogok](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Az indexelési feladatok konfigurációs és a jegyzék használatával
Megadhat további részleteket az indexelési feladatok egy feladat konfiguráció használatával. Megadhatja például, mely a médiafájl használandó metaadatok. A metaadatok segítségével a nyelv motor bontsa ki a szöveg szóhasználati, és jelentősen csökkenti a beszédfelismerés pontosságát.  Emellett is tudja adja meg a kívánt kimeneti fájlokat.

Több médiafájl egyszerre is fel Alkalmazásjegyzék-fájl használatával.

További információkért lásd: [feladat készletet az Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Egy eszköz indexelése
A következő metódust feltölt egy médiafájlt eszközként, és létrehoz egy feladatot az adategység indexelése.

Ha nincsenek konfigurációs fájl van megadva, a médiafájl indexelt az összes alapértelmezett beállítást.

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
Alapértelmezés szerint egy indexelési feladat a következő kimeneti fájlokat hoz létre. A fájlok az első kimeneti adategység vannak tárolva.

Ha egynél több bemeneti fájllal, az indexelő a jegyzékfájlt a feladatkimenetek "JobResult.txt" nevű számára hoz létre. Az egyes bemeneti médiafájl, az eredményül kapott AIB, SZÁMI, TTML, WebVTT kulcsszó fájlok, egymás után számozott és elnevezni az "Alias."

| Fájlnév | Leírás |
| --- | --- |
| **InputFileName.aib** |Az indexelő a blob hangfájl. <br/><br/> Hangfájl indexelő a Blob (aib) formájában egy bináris fájl, amely a Microsoft SQL server teljes szöveges kereséssel lehet keresni.  AIB-fájl sokkal hatékonyabb, mint az egyszerű feliratfájlok, mert az alternatív megoldások minden egyes szó, így sokkal gazdagabb keresési funkciókat tartalmaz. <br/> <br/>Azt kell az indexelő az SQL-bővítményt a gépen futó Microsoft SQL server 2008 vagy újabb verzióját. Keresés a AIB használatával a Microsoft SQL server teljes szöveges keresés pontosabb keresési eredmények WAMI által generált feliratfájlok keresés, mint biztosít. Ennek az oka a AIB word alternatíva, amely hasonló hang, mivel a feliratfájlok tartalmaz minden egyes szegmens a hanganyag hosszának végéig a legmagasabb megbízhatósági szót tartalmaz. Ha a videóban elhangzó szövegre keressenek keresése upmost jelentőségű, akkor javasoljuk, hogy a AIB az együttes használata a Microsoft SQL Server.<br/><br/> A bővítmény letöltése a <a href="https://aka.ms/indexersql">Azure Media Indexer SQL bővítmény</a>. <br/><br/>Használják a más keresőmotor, például az Apache Lucene/Solr egyszerűen indexelése a videó akadálymentes felirat és XML-fájlok kulcsszó alapján lehetőség arra is, de ez kevésbé pontos keresés eredménye eredményez. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |SZÁMI, TTML és WebVTT formátumú lezárt felirata (CC) fájlokat.<br/><br/>Elérhetővé a hang- és videofájlok, a hallás fogyatékkal személyek használható.<br/><br/>Lezárt feliratfájlok tartalmaznak nevű címke <b>Recognizability</b> amely pontszámmodell alapján hogyan ismerhető fel a beszéd, a forrás-videóban egy indexelési feladat.  Használhatja a értékét <b>Recognizability</b> képernyő kimeneti fájlok megkönnyítettük a használatát. Alacsony pontszámmal minőségi hang miatt gyenge indexelési eredmények azt. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Kulcsszó információkat és fájlok. <br/><br/>Kulcsszó fájlja egy XML-fájlt, amely gyakorisági és eltérési információkkal együtt a beszédtartalomból kinyert kulcsszavakat tartalmazza. <br/><br/>A fájl adatait egy egyszerű szöveges fájlt, amely minden kifejezéshez ismerhető fel a részletes információkat tartalmaz. Az első sor speciális, és a Recognizability pontszámot tartalmaz. Minden egyes további sor az az alábbi adatok tabulátorral tagolt listája: Indítsa el az idő, befejezési időpontja, szó vagy kifejezés, a megbízhatóság. A időtartamok másodpercek alatt, és magabiztosan számként van megadva a 0-1. <br/><br/>Példa vonal: "a 1.20 1.45 word-0.67." <br/><br/>Ezek a fájlok lehetnek használt különböző célokra, például a beszéd elemzését, vagy keresőmotorok, mint például a Bing, a Google vagy a Microsoft SharePoint a médiafájlok könnyebben felfedezhetővé teheti, vagy akár több megfelelő ads biztosításához használt vannak kitéve. |
| **JobResult.txt** |Kimeneti jegyzékfájl, jelenik meg, csak akkor, ha az indexelés több fájlt, amely tartalmazza a következő információkat:<br/><br/><table border="1"><tr><th>Bemeneti_fájl</th><th>Alias</th><th>MediaLength</th><th>Hiba</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Ha nem az összes bemeneti médiafájlok indexelése sikeresen, 4000 kóddal meghiúsul az indexelési feladatot. További információkért lásd: [hibakódok](#error_codes).

## <a name="index-multiple-files"></a>Több fájlok indexelése
A következő metódust feltölt több médiafájl eszközként, és létrehoz egy feladatot, ezeket a fájlokat egy kötegbe indexelése.

Egy ".lst" kiterjesztésű jegyzékfájl létrehozása és feltöltése az objektumba. A jegyzékfájl az adategység-fájlok listáját tartalmazza. További információkért lásd: [feladat készletet az Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

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

### <a name="partially-succeeded-job"></a>Részben sikeres feladat
Ha nem az összes bemeneti médiafájlok indexelése sikeresen, akkor az indexelési feladatot 4000-es hibakód sikertelen. További információkért lásd: [hibakódok](#error_codes).

Az azonos kimenetek (a sikeres feladat) jönnek létre. A kimeneti jegyzékfájl megtudhatja, mely bemeneti fájlok meghibásodott, a hiba oszlop értékei alapján is hivatkozunk. A bemeneti fájlokat nem sikerült, az eredményül kapott AIB, SZÁMI, TTML, WebVTT és kulcsszó fájlok nem jön létre.

### <a id="preset"></a> Az Azure Media Indexer az előre beállított feladat
Az Azure Media Indexer a feldolgozás testre szabható azáltal, hogy egy választható feladat mellett a feladat előbeállítást.  A következő ismerteti a konfigurációs xml formátuma.

| Name (Név) | Kötelező | Leírás |
| --- | --- | --- |
| **Bemenet** |false |Az eszközintelligencia fájl, amelyet szeretne indexelni.</p><p>Az Azure Media Indexer a következő fájl médiaformátumok támogatja: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Megadhatja a fájl neve (s) a **neve** vagy **lista** attribútuma a **bemeneti** elem (ahogy az alább látható). Ha nem ad meg melyik adategységfájlon indexre, az elsődleges fájl kivétele történik. Ha nincsenek elsődleges eszközfájl van beállítva, az első fájl a bemeneti objektuma indexelve van.</p><p>Az eszköz nevét adja meg explicit módon, hajtsa végre:<br/>`<input name="TestFile.wmv">`<br/><br/>Több eszköz fájlok egyszerre (legfeljebb 10) indexelésére is használhatja. Ehhez tegye a következőket:<br/><br/><ol class="ordered"><li><p>Hozzon létre egy szövegfájlt (Alkalmazásjegyzék-fájl), és adjon meg egy .lst bővítmény. </p></li><li><p>A bemeneti adategység adja hozzá az összes eszközintelligencia-nevek listája az Alkalmazásjegyzék-fájl. </p></li><li><p>Adja hozzá az eszköz (feltöltés) a jegyzékfájl fájlt.  </p></li><li><p>A bemeneti lista attribútum adja meg a jegyzékfájl neve.<br/>`<input list="input.lst">`</li></ol><br/><br/>Megjegyzés: Ha több mint 10 fájlokat ad hozzá a jegyzékfájlt, az indexelési feladatot 2006 hibakóddal sikertelen lesz. |
| **metadata** |false |A megadott eszköz fájl(ok) adaptálásához használt metaadatait.  Hasznos készíti elő az indexelő nem szabványos szókincsből eredőket szavakat, például a tulajdonnevek felismerni.<br/>`<metadata key="..." value="..."/>` <br/><br/>Megadhat **értékek** az előre meghatározott **kulcsok**. Jelenleg a következő kulcsok támogatottak:<br/><br/>"title" és "description" - használt adaptálásához módosítania a nyelvi modell a feladat, és beszédfelismerés pontosságának javítása.  Az értékek ültet be internetes keresés található kontextusban megfelelő szöveg-dokumentumok, a tartalmát, mivel megvédi a belső szótárához az indexelési feladat időtartama.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Szolgáltatások** <br/><br/> Az 1.2-es verziójában hozzáadva. Az egyetlen támogatott szolgáltatás jelenleg a beszédfelismerés ("ASR"). |false |A beszédfelismerés funkció az alábbi beállítások kulcsok rendelkezik:<table><tr><th><p>Kulcs</p></th>        <th><p>Leírás</p></th><th><p>Példaérték</p></th></tr><tr><td><p>Nyelv</p></td><td><p>A multimédiás fájlban ismeri fel a természetes nyelv.</p></td><td><p>Angol, spanyol</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>a kívánt kimeneti felirat formátumok (ha vannak) pontosvesszővel tagolt listája</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Egy logikai jelzőt megadó AIB-fájl-e szükséges (az SQL Server és az ügyfél az indexelő ifilterrel való használatra).  További információkért lásd: <a href="https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">AIB-fájlok használata az Azure Media Indexer és az SQL Server</a>.</p></td><td><p>True; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Egy logikai jelzőt megadó-e egy kulcsszót XML-fájl megadása kötelező.</p></td><td><p>Igaz FALSE (hamis). </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Egy logikai jelzőt megadó e teljes feliratok (függetlenül a megbízhatósági szint) kényszerítése.  </p><p>Alapértelmezett érték FALSE (hamis), ebben az esetben szavak és kifejezések, amelyek rendelkeznek egy kisebb, mint 50 %-os megbízhatósági szintet kimarad a az utolsó felirat kimenetek és helyébe a három pontot ("...").  A három pontra hasznosak felirat minőség-ellenőrzés és naplózás.</p></td><td><p>Igaz FALSE (hamis). </p></td></tr></table> |

### <a id="error_codes"></a>Hibakódok
Egy hiba esetén az Azure Media Indexer jelentést kell biztonsági másolatot a következő hibakódok egyikét:

| Kód | Name (Név) | Lehetséges okok |
| --- | --- | --- |
| 2000 |Érvénytelen konfiguráció |Érvénytelen konfiguráció |
| 2001 |Érvénytelen bemeneti eszközök |Hiányzik a bemeneti eszközök, illetve üres eszköz. |
| 2002 |Érvénytelen a jegyzékfájl. |Jegyzékfájl üres vagy jegyzékfájlja érvénytelen elemet tartalmaz. |
| 2003 |Nem sikerült letölteni a médiafájl |Érvénytelen URL-cím-jegyzékfájl. |
| 2004 |Protokoll nem támogatott |Médiafájl URL-címe, protokoll nem támogatott. |
| 2005 |Nem támogatott fájltípus |Bemeneti fájl típusa nem támogatott. |
| 2006 |Túl sok a bemeneti fájl |Több mint 10 fájlok vannak a bemeneti jegyzékfájlban. |
| 3000 |Nem sikerült dekódolni a médiafájl |Nem támogatott adathordozó kodek <br/>vagy<br/> Sérült médiafájl <br/>vagy<br/> Nincs a bemeneti audio stream. |
| 4000 |Részlegesen sikerült a Batch-indexelő |Egyes bemeneti fájlokat nem sikerült indexelhetők. További információkért lásd: <a href="#output_files">kimeneti fájlok</a>. |
| egyéb |Belső hiba |Forduljon az ügyfélszolgálathoz. indexer@microsoft.com |

## <a id="supported_languages"></a>Támogatott nyelvek
Jelenleg az angol és spanyol nyelven támogatottak. További információkért lásd: [az 1.2-es verzió kibocsátási blogbejegyzés](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services analitikai funkcióinak áttekintése](media-services-analytics-overview.md)

[AIB-fájl, és az Azure Media Indexer és az SQL Server használatával](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Médiafájlokat az Azure Media Indexer 2 előzetes verzió](media-services-process-content-with-indexer2.md)

