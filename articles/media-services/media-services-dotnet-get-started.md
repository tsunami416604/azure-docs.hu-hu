<properties
    pageTitle="Tartalmak továbbítása igény szerint a .NET SDK használatával"
    description="Ez az útmutató lépésről lépésre ismerteti, hogyan valósíthat meg egy igény szerinti tartalomtovábbító alkalmazást a .NET-keretrendszert használó Azure Media Services segítségével."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="04/18/2016"
    ms.author="juliako"/>


# Tartalmak továbbítása igény szerint a .NET SDK használatával


[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](/pricing/free-trial/?WT.mc_id=A261C142F). 
 
##Áttekintés 

Ez az útmutató lépésről lépésre bemutatja, hogyan valósíthat meg egy Video-on-Demand (VoD) tartalomtovábbító alkalmazást a .NET-keretrendszerhez készült Azure Media Services (AMS) SDK segítségével.


Az útmutató bemutatja a Media Services alapvető munkafolyamatait és a Media Services-fejlesztéshez szükséges leggyakoribb programozási objektumokat és feladatokat. Az oktatóprogram elvégzése után képes lesz adatfolyamot továbbítani vagy fokozatosan letölteni egy saját maga által feltöltött, kódolt és letöltött példa médiafájlt.

## Ismertetett témák

Az útmutató a következő feladatok elvégzését mutatja be:

1.  Egy Media Services-fiók létrehozása (a klasszikus Azure portál használatával).
2.  A streamvégpont konfigurálása (a portál használatával).
3.  Egy Visual Studio-projekt létrehozása és konfigurálása.
5.  A Media Services-fiókhoz való csatlakozás.
6.  Egy új adategység létrehozása és egy videofájl feltöltése.
7.  A forrásfájl kódolása egy adaptív sávszélességű MP4-fájlsorozattá.
8.  Az adatkészlet közzététele, valamint az adatfolyam-továbbításhoz és progresszív letöltéshez szükséges URL-címek lekérése.
9.  Tesztelje a tartalom lejátszhatóságát.

## Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

- Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. 
    
    Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](/pricing/free-trial/?WT.mc_id=A261C142F). Jóváírásokat kap, amelyeket fizetős Azure-szolgáltatások kipróbálására használhat fel. Még ha a jóváírásokat el is használta, továbbra is megtarthatja a fiókot és használhatja az ingyenes szolgáltatásokat és lehetőségeket, mint például a Web Apps szolgáltatást az Azure App Service alatt.
- Operációs rendszerek: Windows 8 vagy újabb, Windows 2008 R2, Windows 7.
- A .NET-keretrendszer 4.0-s vagy újabb verziója.
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate vagy Express) vagy későbbi verzió.


##Minta letöltése

Töltsön le és futtasson egy mintát [innen](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##Media Services-fiók létrehozása a portál használatával

1. A klasszikus Azure portálon kattintson az **Új** > **Media Services** > **Gyors létrehozás** lehetőségre.

    ![Media Services-fiók gyors létrehozása](./media/media-services-dotnet-get-started/wams-QuickCreate.png)

2. A **NAME** (NÉV) mezőbe írja be az új fiók nevét. A Media Services-fiók nevének 3–24 karakter hosszúságúnak kell lennie, és csak kisbetűket és számokat tartalmazhat, szóközök nélkül.

3. A **REGION** (RÉGIÓ) mezőben válassza ki a földrajzi régiót, ahol a Media Services-fiók metaadatrekordjai tárolva lesznek. A legördülő listában kizárólag az elérhető Media Services-régiók jelennek meg.

4. A **STORAGE ACCOUNT** (TÁRFIÓK) mezőben válasszon ki egy tárfiókot, amely a Media Services-fiókhoz tartozó médiatartalmak blobtárolását biztosítja. Választhat, hogy egy meglévő, a Media Services-fiókkal azonos földrajzi régióban található tárfiókot használ, vagy létrehoz egy új tárfiókot. Az újonnan létrehozott tárfiókok ugyanabban a régióban jönnek létre.

5. Ha egy új tárfiókot hozott létre, a **NEW STORAGE ACCOUNT NAME** (ÚJ TÁRFIÓK NEVE) mezőben adjon meg egy nevet a tárfióknak. A tárfiókok elnevezési szabályai ugyanazok, mint a Media Services-fiókok esetében.

6. Az űrlap alsó részén kattintson a **Gyors létrehozás** elemre.

Az ablak alján az üzenetmezőben figyelemmel kísérheti a folyamat állapotát.

A fiók sikeres létrejöttekor az állapot **Aktívra** változik.

Az oldal alján megjelenik a **MANAGE KEYS** (KULCSOK KEZELÉSE) gomb. Ha erre a gombra kattint, egy párbeszédpanelen megjelenik a Media Services-fiók neve, valamint az elsődleges és másodlagos kulcsok. A Media Services-fiókhoz való szoftveres hozzáféréshez szükség lesz a fiók nevére és az elsődleges kulcsra.

![Media Services oldal](./media/media-services-dotnet-get-started/wams-mediaservices-page.png)

A fiók nevére való dupla kattintáskor alapértelmezés szerint megjelenik a **Gyorsindítás** oldal. Ezen az oldalon elvégezhet néhány olyan felügyeleti feladatot, amelyek a portál más oldalain is elérhetők. Például feltölthet videofájlokat erről az oldalról, vagy a CONTENT (TARTALOM) oldalról is.

##A streamvégpont konfigurálása a portál használatával

Az Azure Media Services használatának egyik leggyakoribb forgatókönyve az adaptív sávszélességű streamelés továbbítása az ügyfelek részére. Az adaptív sávszélességű streameléskor az ügyfél magasabb vagy alacsonyabb sávszélességű adatfolyam-továbbításra válthat a videó lejátszása közben, az aktuális hálózati sávszélességhez, CPU-használathoz és egyéb tényezőkhöz igazodva. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH, és HDS (csak Adobe PrimeTime/Access licenctulajdonosok esetében).

A Media Services dinamikus csomagolást biztosít, amely lehetővé teszi az adaptív sávszélességű, MP4- vagy Smooth Streaming-kódolású tartalmak továbbítását a Media Services által támogatott formátumokban (MPEG DASH, HLS, Smooth Streaming, HDS) anélkül, hogy a tartalmakat újra kellene csomagolni ezekbe az adatfolyam-továbbítási formátumokba.

A dinamikus csomagolás előnyeinek kihasználásához a következőket kell tennie:

- Kódolja a mezzanine (forrás) fájlt egy adaptív sávszélességű MP4- vagy Smooth Streaming-fájlsorozattá (az átkódolás lépéseit később mutatja be ez az oktatóanyag).
- Helyezzen legalább egy adategységet arra az **adatfolyam-továbbítási végpontra**, amelyről a tartalmat továbbítani tervezi.

A dinamikus csomagolás használatával csak egyféle formátumban kell tárolnia a fájlokat és fizetnie utánuk, a Media Services pedig az ügyfelek igényeihez igazodva hozza létre és továbbítja számukra a megfelelő választ.

A folyamatos átvitelhez fenntartott egységek számának módosításához tegye a következőt:

1. A [portálon](https://manage.windowsazure.com/) kattintson a **Media Services** elemre. Ezután kattintson a médiaszolgáltatás nevére.

2. Válassza ki az STREAMING ENDPOINTS (STREAMVÉGPONTOK) oldalt. Ezután kattintson a módosítani kívánt streamvégpontra.

3. Az adatfolyam-továbbítási egységek számának megadásához kattintson a SCALE (MÉRET) lapra, majd állítsa be a kívánt értéket a **reserved capacity** (Fenntartott kapacitás) csúszkán.

    ![Méretezés oldal](./media/media-services-dotnet-get-started/media-services-origin-scale.png)

4. A módosítások mentéséhez kattintson a **SAVE** (MENTÉS) gombra.

Az új egységek lefoglalásának teljesítése körülbelül 20 percet vesz igénybe.

>[AZURE.NOTE] Jelenleg ha bármely pozitív számú adategységről vált nullára, az akár egy órára letilthatja az adatfolyam-továbbítást.
>
> Az ár kiszámításakor a 24 órás periódus során felhasznált legmagasabb számú egység a mérvadó. További információk a díjszabásról: [A Media Services díjszabása](http://go.microsoft.com/fwlink/?LinkId=275107)



##Egy Visual Studio-projekt létrehozása és konfigurálása

1. Hozzon létre egy új C#-konzolalkalmazást a Visual Studio 2013, Visual Studio 2012 vagy Visual Studio 2010 SP1 használatával. Adja meg a **nevét**, a **helyét** és a **megoldás nevét**, majd kattintson az **OK** gombra.

2. A [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) Nuget-csomag használatával telepítse az **Azure Media Services .NET SDK-bővítményeket**.  A Media Services .NET SDK-bővítmények között olyan kiegészítő módszerek és segédfüggvények találhatók, amelyek egyszerűbbé teszik a kódolást és megkönnyítik a Media Services használatával történő fejlesztést. Ennek a csomagnak a telepítése a **Media Services .NET SDK**csomagot és az összes további szükséges függőséget is feltelepíti

3. Adjon hozzá egy, a System.Configuration szerelvényre mutató hivatkozást. Ez a szerelvény tartalmazza a **System.Configuration.ConfigurationManager** osztályt, amely a konfigurációs fájlok, például az App.config eléréséhez használatos.

4. Nyissa meg az App.config fájlt (ha nem lett alapértelmezés szerint hozzáadva a projekthez, akkor adja hozzá), és adjon hozzá a fájlhoz egy *appSettings* szakaszt. Adja meg az Azure Media Services-fiókjához tartozó név és fiókkulcs értékeket, a következő példában bemutatott módon. A fiókhoz tartozó név és fiókkulcs információk megtekintéséhez nyissa meg a klasszikus Azure portált, válassza a Media Services-fiókját, majd kattintson a **MANAGE KEYS** (KULCSOK KEZELÉSE) gombra.

        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
          
        </configuration>

5. Írja felül a meglévő **használati** nyilatkozatokat a Program.cs fájl elején a következő kóddal.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        

6. Hozzon létre egy új mappát a projekteket tartalmazó könyvtárban, és másoljon bele egy .mp4- vagy .wmv-fájlt, amelyet szeretne kódolni vagy fokozatosan letölteni. Ebben a példában a „C:\VideoFiles” elérési utat használjuk.

##Csatlakozás a Media Services-fiókhoz

A .NET-keretrendszerű Media Services-szolgáltatások használatakor a **CloudMediaContext** osztályt kell használnia a legtöbb Media Services-programozási feladathoz – ilyenek például a Media Services-fiókhoz való csatlakozás, továbbá az adategységek, adategységfájlok, feladatok, hozzáférési házirendek, keresők és egyebek létrehozása, frissítése, elérése és törlése.

Írja felül az alapértelmezett Program osztályt a következő kóddal. A kód bemutatja, hogyan olvashatja be a csatlakozási értékeket az App.config fájlból, és hogyan hozhatja létre a Media Services-csatlakozáshoz szükséges **CloudMediaContext** objektumot. További információk a Media Services szolgáltatásokhoz való csatlakozásról: [Csatlakozás a Media Services szolgáltatásokhoz a .NET-keretrendszerhez készült Media Services SDK használatával](http://msdn.microsoft.com/library/azure/jj129571.aspx)

A **Fő** függvény olyan módszereket hív meg, amelyek jelen szakasz során később lesznek meghatározva.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##Új adategység létrehozása és videofájl feltöltése

A Media Services szolgáltatásban a digitális fájlok feltöltése vagy kimenete egy adategységbe történik. Az **Adategység** entitás tartalmazhat videót, hangot, képeket, miniatűröket, szövegsávokat és feliratfájlokat (valamint mindezen fájlok metaadatait).  A fájlok feltöltése után a tartalom a felhőben lesz biztonságosan tárolva további feldolgozás és adatfolyam-továbbítás céljából. Az adategységben található fájlokat **adategység-fájloknak** nevezzük.

Az alábbiakban meghatározott **UploadFile** módszer a **CreateFromFile** módszert hívja meg (amely a .NET SDK-bővítmények között van meghatározva). A **CreateFromFile** létrehoz egy új adategységet, amelybe a megadott forrásfájl fel lesz töltve.

A **CreateFromFile** módszer számára az **AssetCreationOptions** alapján határozhatja meg, hogy az alábbi adategység-létrehozási lehetőségek közül melyiket használja:

- **Nincs** – Nincs titkosítás. Ez az alapértelmezett érték. Ügyeljen arra, hogy ezen lehetőség használatakor a tartalom sem átvitel, sem tárolás közben nincs védve.
Ha egy MP4-fájlt progresszív letöltés útján tervez továbbítani, használja ezt a lehetőséget.
- **StorageEncrypted** – Ezen lehetőség használatakor a tiszta tartalom helyileg, 256 bites Advanced Encryption Standard (AES) titkosítással lesz titkosítva, és így kerül feltöltésre az Azure Storage tárolóba, ahol titkosítva lesz tárolva. A Storage-titkosítással védett adategységek titkosítása a kódolás előtt automatikusan fel lesz oldva, és egy titkosított fájlrendszerbe kerülnek; az új kimeneti adategységként való újbóli feltöltés előtt pedig lehetőség van az újbóli titkosításukra. A Storage-titkosítás elsősorban akkor hasznos, ha a kiváló minőségű bemeneti médiafájljait erős titkosítással szeretné védeni a lemezen való tároláskor.
- **CommonEncryptionProtected** – Használja ezt a lehetőséget, ha olyan tartalmat tölt fel, amely már korábban titkosítva és védve lett általános titkosítás vagy a PlayReady DRM által (például egy PlayReady DRM titkosítással védett Smooth Streaming-fájlt).
- **EnvelopeEncryptionProtected** – Használja ezt a lehetőséget, ha AES által titkosított HLS tartalmakat tölt fel. Megjegyzés: ehhez a fájlokat a Transform Manager használatával kell kódolni és titkosítani.

A **CreateFromFile** módszer használatával egy visszahívást is megadhat, amely visszajelzést ad a fájl feltöltési folyamatáról.

A következő példában az adategység lehetőségei közt a **Nincs** választ adtuk meg.

Adja hozzá a Program osztályhoz a következő módszert.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


##Forrásfájl kódolása adaptív sávszélességű MP4-fájlsorozattá

Miután az adategységek bevitele a Media Services szolgáltatásba megtörtént, a médiatartalmak többek között kódolhatók, transzmultiplexálás végezhető rajtuk vagy vízjelezhetők, mielőtt továbbítva lennének az ügyfelek felé. Ezen tevékenységek több háttérbeli szerepkörpéldányhoz képest vannak ütemezve és futtatva a magas teljesítmény és rendelkezésre állás biztosítása érdekében. Ezeket a tevékenységeket feladatoknak nevezzük. Minden egyes feladat több részműveletből áll, ezek végzik el a valódi munkát az adategységfájlon.

Mint azt korábban már említettük, az Azure Media Services használatának egyik leggyakoribb forgatókönyve az adaptív sávszélességű streamelés az ügyfelek felé. A Media Services az adaptív sávszélességű MP4-fájlokat a következő formátumokba tudja dinamikusan csomagolni: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH és HDS (csak Adobe PrimeTime/Access licenctulajdonosok esetében).

A dinamikus csomagolás előnyeinek kihasználásához a következőket kell tennie:

- Kódolja a mezzanine forrásfájlt egy adaptív sávszélességű MP4- vagy Smooth Streaming-fájlsorozattá.  
- Helyezzen legalább egy adategységet arra a streamvégpontra, amelyről a tartalmat továbbítani tervezi.

A következő kód bemutatja, hogyan küldhet el egy kódolási feladatot. A feladat egyetlen műveletet tartalmaz, amely azért felel, hogy a mezzazine-fájlt egy adaptív sávszélességű MP4-fájlsorozattá kódolódjon át a **Media Encoder Standard** használatával. A kód elküldi a feladatot, és vár, amíg az befejeződik.

Ha a feladat befejeződött, lehetővé válik az adategység továbbítása vagy az átkódolás során létrejött MP4-fájlok progresszív letöltése.
Megjegyzés: az MP4-fájlok progresszív letöltéséhez nem szükséges, hogy nullánál több adatfolyam-továbbítási egységgel rendelkezzen.

Adja hozzá a Program osztályhoz a következő módszert.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {
    
        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.
    
        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);
    
        Console.WriteLine("Submitting transcoding job...");
    
    
        // Submit the job and wait until it is completed.
        job.Submit();
    
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;
    
        Console.WriteLine("Transcoding job finished.");
    
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        return outputAsset;
    }

##Adatkészlet közzététele és az adatfolyam-továbbításhoz és progresszív letöltéshez szükséges URL-címek lekérése

Egy adategység továbbításához vagy letöltéséhez először a „közzététele” szükséges, egy kereső létrehozásával. A keresők biztosítják az adategységben található fájlokhoz való hozzáférést. A Media Services kétféle keresőtípust támogat: az OnDemandOrigin keresők médiatartalmak továbbításához használatosak (például MPEG DASH, HLS vagy Smooth Streaming), a hozzáférési jogosultságkód (SAS)-keresők pedig médiafájlok letöltéséhez.

A keresők létrehozása után összeállíthatja a fájlok továbbításához vagy letöltéséhez használt URL-címeket.


Egy Smooth Streaming URL-címnek a következő formátumban kell lennie:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Egy HLS-továbbítási URL-címnek a következő formátumban kell lennie:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Egy MPEG DASH-továbbítási URL-címnek a következő formátumban kell lennie:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Egy fájlok letöltéséhez használt SAS URL-címnek a következő formátumban kell lennie:

    {blob container name}/{asset name}/{file name}/{SAS signature}

A Media Services .NET SDK-bővítmények olyan kényelmes segédmódszereket biztosítanak, amelyek formázott URL-címeket adnak meg a közzétett adategységekhez.

A következő kód .NET SDK-bővítmények használatával hoz létre keresőket és kér le adatfolyam-továbbítási és progresszív letöltési URL-címeket. A kód azt is bemutatja, hogyan tölthetők le fájlok egy helyi mappába.

Adja hozzá a Program osztályhoz a következő módszert.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##A tartalom lejátszhatóságának tesztelése  

Az előző szakaszban meghatározott program futtatásakor a konzolablakban a következőkhöz hasonló URL-címek jelennek meg:

Adaptív adatfolyam-továbbítási URL-címek:

Smooth Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

Fokozatos letöltési URL-címek (audió és videó).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


A videótovábbításhoz használja az [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) lejátszót.

A progresszív letöltés teszteléséhez másoljon egy URL-címet a böngészőjébe (például az Internet Explorerbe, Chrome-ba vagy Safariba).


##Következő lépések: Media Services képzési tervek

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Visszajelzés küldése

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### Valami mást keres?

Amennyiben ebben a témakörben nem találta meg azt, amire számított; ha a témakörből hiányzik valami; vagy bármilyen egyéb módon nem felelt meg az elvárásainak, kérjük, küldjön nekünk visszajelzést alább, egy Disqus-hozzászóláson keresztül.


<!-- Anchors. -->


<!-- URLs. -->
  [Webplatform-telepítő]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portál]: http://manage.windowsazure.com/



<!--HONumber=Jun16_HO2-->


