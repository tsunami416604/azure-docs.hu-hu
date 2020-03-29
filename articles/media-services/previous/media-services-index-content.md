---
title: Médiafájlok indexelése az Azure Media Indexelővel
description: Az Azure Media Indexer lehetővé teszi, hogy a médiafájlok tartalmát kereshetővé tegye, és teljes szöveges átiratot hozzon létre a feliratozáshoz és a kulcsszavakhoz. Ez a témakör a Media Indexer használatát mutatja be.
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
ms.openlocfilehash: 7ccc2d5956b44a8cd85f19e0905539c32f58bc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163998"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Médiafájlok indexelése az Azure Media Indexelővel

> [!NOTE]
> Az **Azure Media Indexer** médiaprocesszor kilesz vonva. A nyugdíjazási dátumokat lásd az [örökölt összetevők](legacy-components.md) témakörben. [Az Azure Media Services videoindexelője](https://docs.microsoft.com/azure/media-services/video-indexer/) lecseréli ezt az örökölt médiaprocesszort. További információ: Áttelepítés az [Azure Media Indexerről és az Azure Media Indexer 2-ről az Azure Media Services videoindexelőre](migrate-indexer-v1-v2.md)című témakörben.

Az Azure Media Indexer lehetővé teszi, hogy a médiafájlok tartalmát kereshetővé tegye, és teljes szöveges átiratot hozzon létre a feliratozáshoz és a kulcsszavakhoz. Egy kötegben egy médiafájlt vagy több médiafájlt is feldolgozhat.  

Tartalom indexelésekor ügyeljen arra, hogy tiszta beszéddel rendelkező médiafájlokat használjon (háttérzene, zaj, effektusok vagy mikrofonszij nélkül). Néhány példa a megfelelő tartalomra: rögzített értekezletek, előadások vagy előadások. Előfordulhat, hogy a következő tartalom nem alkalmas indexelésre: filmek, tv-műsorok, vegyes hang- és hangeffektusokkal rendelkező tartalmak, rosszul rögzített, háttérzajú tartalom (szimás).

Az indexelési feladat a következő kimeneteket hozhatja létre:

* A feliratfájlok a következő formátumokban jelennek meg: **TTML**és **WebVTT**.
  
    A feliratfájlok közé tartozik a Felismerhetőség nevű címke, amely indexelési feladatot ad a forrásvideóban lévő beszéd felismerésének megalapozottságátalapul.  A Felismerhetőség értékével szűrheti a kimeneti fájlokat a használhatóság érdekében. Az alacsony pontszám a hangminőség miatt gyenge indexelési eredményeket jelentene.
* Kulcsszófájl (XML).

Ez a cikk bemutatja, hogyan hozhat létre indexelési feladatokat **egy eszköz indexeléséhez** és **több fájl indexeléséhez.**

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Konfigurációs és jegyzékfájlok használata indexelési feladatokhoz
Az indexelési feladatoktovábbi részleteit egy feladatkonfiguráció használatával adhatja meg. Megadhatja például, hogy mely metaadatokat használja a médiafájlhoz. Ezeket a metaadatokat a nyelvi motor használja a szókincs bővítésére, és jelentősen javítja a beszédfelismerés pontosságát.  Megadhatja a kívánt kimeneti fájlokat is.

Egy jegyzékfájl használatával egyszerre több médiafájlt is feldolgozhat.

További információ: [Task Preset for Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Eszköz indexelése
A következő módszer feltölt egy médiafájlt eszközként, és létrehoz egy feladatot az eszköz indexelésére.

Ha nincs megadva konfigurációs fájl, a médiafájl az összes alapértelmezett beállítással lesz indexelve.

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
### <a name="output-files"></a><a id="output_files"></a>Kimeneti fájlok
Alapértelmezés szerint egy indexelési feladat a következő kimeneti fájlokat hozza létre. A fájlok az első kimeneti eszközben tárolódnak.

Ha egynél több bemeneti médiafájl van, az Indexelő létrehoz egy "JobResult.txt" nevű jegyzékfájl-fájlt a feladatkimenetekhez. Az egyes bemeneti médiafájlok hozlétre kerülő TTML-, WebVTT- és kulcsszófájljai egymás után számozottak és elnevezett ek ként jelennek meg az "Alias" használatával.

| Fájlnév | Leírás |
| --- | --- |
| **Bemeneti fájlnév.ttml**<br/>**InputFileName.vtt** |TTML és WebVTT formátumban.<br/><br/>Ezeket arra is fel lehet használni, hogy a hang- és videofájlokat hozzáférhetővé tegyék a hallássérült ek számára.<br/><br/>A feliratfájlok közé tartozik a Felismerhetőség nevű <b>címke,</b> amely indexelési feladatot ad a forrásvideóban lévő beszéd felismerésének megalapozottságátalapul.  A <b>Felismerhetőség</b> értékével szűrheti a kimeneti fájlokat a használhatóság érdekében. Az alacsony pontszám a hangminőség miatt gyenge indexelési eredményeket jelentene. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Kulcsszó- és információs fájlok. <br/><br/>A kulcsszófájl egy XML-fájl, amely a beszédtartalomból kinyert kulcsszavakat tartalmazza, gyakorisági és eltolási információkkal. <br/><br/>Az információs fájl egy egyszerű szöveges fájl, amely részletes információkat tartalmaz az egyes felismert kifejezésekről. Az első sor különleges, és tartalmazza a felismerhetőség pontszám. Minden további sor a következő adatok tabulátorlistája: kezdési időpont, befejezési időpont, szó/kifejezés, megbízhatóság. Az idők másodpercben vannak megadva, és a megbízhatóság 0-1 számként van megadva. <br/><br/>Példa sor: "1,20 1,45 szó 0,67" <br/><br/>Ezek a fájlok számos célra használhatók, például beszédelemzéselvégzésére, vagy olyan keresőmotoroknak vannak kitéve, mint a Bing, a Google vagy a Microsoft SharePoint, hogy a médiafájlok at felfedezhetővé tegyék, vagy akár relevánsabb hirdetések megjelenítésére is használják. |
| **JobResult.txt fájl** |Kimeneti jegyzékfájl, amely csak több fájl indexelésekéneknél jelenik meg, és a következő információkat tartalmazza:<br/><br/><table border="1"><tr><th>Bemeneti fájl</th><th>Alias</th><th>MediaLength (MediaLength)</th><th>Hiba</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Ha nem minden bemeneti médiafájl van sikeresen indexelve, az indexelési feladat a 4000-es hibakóddal sikertelen lesz. További információt a [Hibakódok című témakörben talál.](#error_codes)

## <a name="index-multiple-files"></a>Több fájl indexelése
A következő módszer több médiafájlt tölt fel eszközként, és létrehoz egy feladatot, amely indexeli ezeket a fájlokat egy kötegben.

Létrejön egy jegyzékfájl a ".lst" kiterjesztéssel, és feltöltésre kerül az eszközbe. A jegyzékfájl tartalmazza az összes eszközfájl listáját. További információ: [Task Preset for Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

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
Ha nem sikerül az összes bemeneti médiafájl indexelése, az indexelő feladat a 4000-es hibakóddal sikertelen lesz. További információt a [Hibakódok című témakörben talál.](#error_codes)

Ugyanazok a kimenetek (a sikeres feladatokhoz képest) jönnek létre. A kimeneti jegyzékfájlra hivatkozva megtudhatja, hogy mely bemeneti fájlok sikertelenek, a Hiba oszlop értékei szerint. A sikertelen bemeneti fájlok esetében az eredményül kapott TTML-, WebVTT- és kulcsszófájlok NEM jönnek létre.

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a>Feladatkészlet az Azure Media Indexelőhez
Az Azure Media Indexelő feldolgozását testre szabhatja egy opcionális feladatkészlet a feladat mellett.  Az alábbiakban a konfigurációs xml formátumát ismertetik.

| Név | Kötelező | Leírás |
| --- | --- | --- |
| **bemenet** |hamis |Az indexozni kívánt eszközfájl(ok).</p><p>Az Azure Media Indexer a következő médiafájlformátumokat támogatja: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>A bemeneti elem **nevében** vagy **listaattribútumában** megadhatja a fájlnevet (eke)t (lásd alább). **input** Ha nem adja meg, hogy melyik eszközfájlt indexelje, a rendszer kiveszi az elsődleges fájlt. Ha nincs beállítva elsődleges eszközfájl, a bemeneti eszköz első fájlja indexelésre kerül.</p><p>Az eszközfájl nevének explicit megadásához tegye a következőket:<br/>`<input name="TestFile.wmv">`<br/><br/>Egyszerre több eszközfájlt is indexelhet (legfeljebb 10 fájl). Ehhez tegye a következőket:<br/><br/><ol class="ordered"><li><p>Hozzon létre egy szövegfájlt (jegyzékfájl) és adjon neki .lst kiterjesztést. </p></li><li><p>Adja hozzá a bemeneti eszközben lévő összes eszközfájl nevét ehhez a jegyzékfájlhoz. </p></li><li><p>Adja hozzá (töltse fel) a jegyzékfájlaz eszközhöz.  </p></li><li><p>Adja meg a jegyzékfájl nevét a bemeneti lista attribútumában.<br/>`<input list="input.lst">`</li></ol><br/><br/>Megjegyzés: Ha 10-nél több fájlt ad hozzá a jegyzékfájlhoz, az indexelési feladat sikertelen lesz a 2006-os hibakóddal. |
| **Metaadat** |hamis |A szókincs-adaptációhoz használt meghatározott eszközfájl(ok) metaadatai.  Hasznos lehet az Indexer előkészítése a nem szabványos szókincs, például a megfelelő főnév felismerésére.<br/>`<metadata key="..." value="..."/>` <br/><br/>Az előre definiált kulcsok **értékeit** **megadhatja.** Jelenleg a következő kulcsok támogatottak:<br/><br/>"title" és "description" - szókincs-adaptációhoz használt, hogy a nyelvi modellt finomítsa a munkájához, és javítsa a beszédfelismerés pontosságát.  Az értékek az internetes kereséseket a kontextusszempontjából releváns szöveges dokumentumok kereséséhez használja a tartalom használatával a belső szótár kiegészítésére az indexelési feladat időtartama alatt.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Funkciók** <br/><br/> Hozzáadva az 1.2-es verzióban. Jelenleg az egyetlen támogatott funkció a beszédfelismerés ("ASR"). |hamis |A Beszédfelismerés szolgáltatás a következő beállítási gombokkal rendelkezik:<table><tr><th><p>Kulcs</p></th>        <th><p>Leírás</p></th><th><p>Példaérték</p></th></tr><tr><td><p>Nyelv</p></td><td><p>A multimédiás fájlban felismerendő természetes nyelv.</p></td><td><p>Angol, spanyol</p></td></tr><tr><td><p>Feliratformátumok</p></td><td><p>a kívánt kimeneti feliratformátumok pontosvesszővel elválasztott listája (ha van ilyen)</p></td><td><p>ttml;webvtt</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>Igaz; Hamis</p></td></tr><tr><td><p>Kulcsszavak létrehozása</p></td><td><p>Logikai jelző, amely meghatározza, hogy szükség van-e kulcsszó XML-fájlra.</p></td><td><p>Igaz; Hamis. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Logikai jelző, amely meghatározza, hogy a teljes feliratok kikényszerítése legyen-e (a megbízhatósági szinttől függetlenül).  </p><p>Az alapértelmezett érték hamis, ebben az esetben az 50%-nál kisebb megbízhatósági szinttel rendelkező szavak és kifejezések kimaradnak a végső feliratkimenetből, és három ponttal helyettesítik ("...").  A három pont a feliratminőség-ellenőrzéshez és -naplózáshoz hasznos.</p></td><td><p>Igaz; Hamis. </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>Hibakódok
Hiba esetén az Azure Media Indexernek az alábbi hibakódok egyikéről kell jelentést tennie:

| Kód | Név | Lehetséges okok |
| --- | --- | --- |
| 2000 |Érvénytelen konfiguráció |Érvénytelen konfiguráció |
| 2001 |Érvénytelen input eszközök |Hiányzó bemeneti eszközök vagy üres eszköz. |
| 2002 |Érvénytelen jegyzékfájl |A jegyzékfájl üres, vagy érvénytelen elemeket tartalmaz. |
| 2003 |Nem sikerült letölteni a médiafájlt |Érvénytelen URL a jegyzékfájlban. |
| 2004 |Nem támogatott protokoll |A média URL-címének protokollja nem támogatott. |
| 2005 |Nem támogatott fájltípus. |A bemeneti médiafájl típusa nem támogatott. |
| 2006 |Túl sok a bemeneti fájl |A bemeneti jegyzékben több mint 10 fájl található. |
| 3000 |Nem sikerült dekódolni a médiafájlt |Nem támogatott adathordozó-kodek <br/>vagy<br/> Sérült médiafájl <br/>vagy<br/> Nincs hangadatfolyam a bemeneti adathordozón. |
| 4000 |A kötegelt indexelés részben sikeres volt |A bemeneti médiafájlok egy része nem indexelhető. További információt a Kimeneti fájlok című <a href="#output_files">témakörben talál.</a> |
| egyéb |Belső hibák |Kérjük, lépjen kapcsolatba az ügyfélszolgálattal. indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>Támogatott nyelvek
Jelenleg az angol és a spanyol nyelv támogatott.  

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Médiafájlok indexelése az Azure Media Indexer 2 előzetes verzióval](media-services-process-content-with-indexer2.md)

