---
title: "Tartalmak továbbítása igény szerint a .NET használatával | Microsoft Docs"
description: "Ez az útmutató lépésről lépésre ismerteti, hogyan valósíthat meg egy igény szerinti tartalomtovábbító alkalmazást a .NET-keretrendszert használó Azure Media Services segítségével."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f01cd8d3a68776dd12d2930def1641411e6a4994
ms.openlocfilehash: a9f77a58cdb13c357b6c3734bd9e3efa4ff5087b


---

# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Tartalmak továbbítása igény szerint a .NET SDK használatával
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
>
>

## <a name="overview"></a>Áttekintés
Ez az útmutató lépésről lépésre bemutatja, hogyan valósíthat meg egy Video-on-Demand (VoD) tartalomtovábbító alkalmazást a .NET-keretrendszerhez készült Azure Media Services (AMS) SDK segítségével.

Az útmutató bemutatja a Media Services alapvető munkafolyamatait és a Media Services-fejlesztéshez szükséges leggyakoribb programozási objektumokat és feladatokat. Az oktatóprogram elvégzése után képes lesz adatfolyamot továbbítani vagy fokozatosan letölteni egy saját maga által feltöltött, kódolt és letöltött példa médiafájlt.

### <a name="ams-model"></a>AMS-modell

A következő kép a Media Services OData-modellen alapuló VoD-alkalmazásfejlesztések során leggyakrabban használt objektumok közül mutat be néhányat.

Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="https://docs.microsoft.com/en-us/azure/media-services/media/media-services-dotnet-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png"></a> 

A teljes modellt [itt](https://media.windows.net/API/$metadata?api-version=2.14) tekintheti meg.  

## <a name="what-youll-learn"></a>Ismertetett témák

Az útmutató a következő feladatok elvégzését mutatja be:

1. Media Services-fiók létrehozása (az Azure Portal használatával).
2. A streamvégpont konfigurálása (az Azure Portal használatával).
3. Egy Visual Studio-projekt létrehozása és konfigurálása.
4. A Media Services-fiókhoz való csatlakozás.
5. Egy új adategység létrehozása és egy videofájl feltöltése.
6. A forrásfájl kódolása egy adaptív sávszélességű MP4-fájlsorozattá.
7. Az adatkészlet közzététele, valamint az adatfolyam-továbbításhoz és progresszív letöltéshez szükséges URL-címek lekérése.
8. Tesztelje a tartalom lejátszhatóságát.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzésének a következők a feltételei.

* Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége.

    Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Jóváírásokat kap, amelyeket fizetős Azure-szolgáltatások kipróbálására használhat fel. Még ha a jóváírásokat el is használta, továbbra is megtarthatja a fiókot és használhatja az ingyenes szolgáltatásokat és lehetőségeket, mint például a Web Apps szolgáltatást az Azure App Service alatt.
* Operációs rendszerek: Windows 8 vagy újabb, Windows 2008 R2, Windows 7.
* A .NET-keretrendszer 4.0-s vagy újabb verziója.
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate vagy Express) vagy későbbi verzió.

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Azure Media Services-fiók létrehozása az Azure Portal használatával
A jelen szakaszban ismertetett lépések bemutatják az AMS-fiók létrehozásának módját.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson az **+Új** > **Adathordozó + CDN** > **Media Services** lehetőségre.

    ![Media Services, létrehozás](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. A **CREATE MEDIA SERVICES ACCOUNT** (Media Services-fiók létrehozása) részben adja meg a kívánt értékeket.

    ![Media Services, létrehozás](./media/media-services-portal-vod-get-started/media-services-new3.png)

   1. Az **Account Name** (Fiók neve) mezőben adja meg az új AMS-fiók nevét. A Media Services-fiók neve csak kisbetűket és számokat tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.
   2. A Subscription (Előfizetés) résznél válasszon az elérhető Azure-előfizetések közül.
   3. A **Resource Group** (Erőforráscsoport) résznél válasszon egy új vagy meglévő erőforrást.  Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. További információkat [itt](../azure-resource-manager/resource-group-overview.md#resource-groups) talál.
   4. A **Hely** részben válassza ki azt a földrajzi régiót, amelyben tárolni kívánja a Media Services-fiókhoz tartozó adathordozó- és metaadatrekordokat. A rendszer ezen régió alapján fogja feldolgozni, illetve streamelni az adathordozót. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg.
   5. A **Storage Account** (Tárfiók) résznél válasszon egy tárfiókot, amely Blob Storage tárolót fog biztosítani a Media Services-fiókhoz tartozó médiatartalmak számára. Választhat, hogy egy meglévő, a Media Services-fiókkal azonos földrajzi régióban található tárfiókot használ, vagy létrehoz egy másik tárfiókot. Az újonnan létrehozott tárfiókok ugyanabban a régióban jönnek létre. A tárfiók nevére ugyanazok a szabályok vonatkoznak, mint a Media Services-fiókok nevére.

       További információkat a tárhelyről [itt](../storage/storage-introduction.md) talál.
   6. A fióklétrehozás előrehaladásának megtekintéséhez kattintson a **Rögzítés az irányítópulton** elemre.
4. Kattintson az űrlap alján található **Létrehozás** lehetőségre.

    A fiók sikeres létrehozását követően státusza **Fut** értékre változik.

    ![Media Services, beállítások](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Az AMS-fiók kezeléséhez (például videók feltöltéséhez, objektumok kódolásához, a feladatok előrehaladásának figyeléséhez) használja a **Settings** (Beállítások) ablakot.

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>A streamvégpont konfigurálása az Azure Portal használatával
Az Azure Media Services egyik legnépszerűbb funkciója, amikor a portál használatával adaptív sávszélességű streamelést biztosítunk az ügyfelek számára. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming és MPEG DASH.

A Media Services dinamikus csomagolást biztosít, amelynek köszönhetően adaptív sávszélességű, MP4 formátumban kódolt tartalmait a Media Services által támogatott streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming) továbbíthatja igény szerint, mindezt anélkül, hogy az adott formátumban előcsomagolt verziót tárolna.

A dinamikus csomagolás előnyeinek kihasználásához a következőket kell tennie:

* Kódolja adaptív sávszélességű MP4-fájlokká a forrásfájlt (a kódolás lépéseit az oktatóanyag egy későbbi részében találja meg).  
* Hozzon létre legalább egy streamelési egységet a tartalom továbbításához használni kívánt *streamvégpontra*. Az alábbi lépésekből megtudhatja, hogyan módosíthatja a streamelési egységek számát.

A dinamikus csomagolás használatával csak egyféle formátumban kell tárolnia a fájlokat és fizetnie azok alapján, a Media Services pedig az ügyfelek igényeihez igazodva hozza létre és továbbítja számukra a megfelelő választ.

Streameléshez fenntartott egységek létrehozásához és számának megváltoztatásához tegye a következőket:

1. Kattintson a **Settings** (Beállítások) ablak **Streaming endpoints** (Streamvégpontok) elemére.
2. Kattintson az alapértelmezett streamvégpontra.

    Megjelenik a **DEFAULT STREAMING ENDPOINT DETAILS** (Alapértelmezett streamvégpont adatai) ablak.
3. Adja meg a streamelési egységek számát a **Streaming units** (Streamelési egységek) csúszka mozgatásával.

    ![Streamelési egységek](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)
4. Mentse a módosításokat a **Save** (Mentés) gombra kattintva.

   > [!NOTE]
   > Az új egységek allokációja akár 20 percig is eltarthat.
   >
   >

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Hozzon létre egy új C#-konzolalkalmazást a Visual Studio 2013, Visual Studio 2012 vagy Visual Studio 2010 SP1 használatával. Adja meg a **nevét**, a **helyét** és a **megoldás nevét**, majd kattintson az **OK** gombra.
2. A [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) NuGet-csomag használatával telepítse az **Azure Media Services .NET SDK-bővítményeket**.  A Media Services .NET SDK-bővítmények között olyan kiegészítő módszerek és segédfüggvények találhatók, amelyek egyszerűbbé teszik a kódolást és megkönnyítik a Media Services használatával történő fejlesztést. Ennek a csomagnak a telepítése a **Media Services .NET SDK**csomagot és az összes további szükséges függőséget is feltelepíti

    A hivatkozások NuGet használatával való hozzáadásához tegye a következőket: a Megoldáskezelőben kattintson a jobb gombbal a projekt nevére, és válassza a **NuGet-csomagok kezelése** lehetőséget. Ezután keressen a **windowsazure.mediaservices.extensions** elemre, és kattintson a **Telepítés** gombra.

3. Adjon hozzá egy, a System.Configuration szerelvényre mutató hivatkozást. Ez a szerelvény tartalmazza a **System.Configuration.ConfigurationManager** osztályt, amely a konfigurációs fájlok, például az App.config eléréséhez használatos.

    Hivatkozás hozzáadásához tegye a következőket: a Megoldáskezelő felületén kattintson a jobb gombbal a projekt nevére, válassza a **Hozzáadás** > **Hivatkozás...** lehetőséget, és írja be a konfigurációt a keresőmezőbe.

4. Nyissa meg az App.config fájlt (ha nem lett alapértelmezés szerint hozzáadva a projekthez, akkor adja hozzá), és adjon hozzá a fájlhoz egy *appSettings* szakaszt. Adja meg az Azure Media Services-fiókjához tartozó név és fiókkulcs értékeket, a következő példában bemutatott módon. A fiók neve és a legfontosabb információk beszerzéséhez látogasson el az [Azure-portálra](https://portal.azure.com/), és válassza ki AMS-fiókját. Ezután válassza a **Beállítások** > **Kulcsok** lehetőséget. A Kulcsok kezelése ablakban megtalálja a fiók nevét, valamint az elsődleges és másodlagos kulcsot. Másolja ki a fióknév és az elsődleges kulcs értékeit.

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
6. Hozzon létre egy új mappát (a mappa a helyi meghajtón bárhol lehet), és másoljon bele egy .mp4-fájlt, amelyet szeretne kódolni vagy fokozatosan letölteni. Ebben a példában a „C:\VideoFiles” elérési utat használjuk.

## <a name="connect-to-the-media-services-account"></a>Csatlakozás a Media Services-fiókhoz

A .NET-keretrendszerű Media Services-szolgáltatások használatakor a **CloudMediaContext** osztályt kell használnia a legtöbb Media Services-programozási feladathoz – ilyenek például a Media Services-fiókhoz való csatlakozás, továbbá az adategységek, adategységfájlok, feladatok, hozzáférési házirendek, keresők és egyebek létrehozása, frissítése, elérése és törlése.

Írja felül az alapértelmezett Program osztályt a következő kóddal. A kód bemutatja, hogyan olvashatja be a csatlakozási értékeket az App.config fájlból, és hogyan hozhatja létre a Media Services-csatlakozáshoz szükséges **CloudMediaContext** objektumot. További információk a Media Services szolgáltatásokhoz való csatlakozásról: [Csatlakozás a Media Services szolgáltatásokhoz a .NET-keretrendszerhez készült Media Services SDK használatával](http://msdn.microsoft.com/library/azure/jj129571.aspx)

Ne feledje frissíteni a fájl nevét és elérési útvonalát a médiafájl helyének megfelelően.

A **Fő** függvény olyan módszereket hív meg, amelyek jelen szakasz során később lesznek meghatározva.

> [!NOTE]
> Amíg nem veszi fel az összes függvénydefinícióit, a rendszer fordítási hibát jelez.

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
        // Make sure to update the file name and path to where you have your media file.
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
    }

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Új adategység létrehozása és videofájl feltöltése

A Media Services szolgáltatásban a digitális fájlok feltöltése vagy kimenete egy adategységbe történik. Az **Adategység** entitás tartalmazhat videót, hangot, képeket, miniatűröket, szövegsávokat és feliratfájlokat (valamint mindezen fájlok metaadatait).  A fájlok feltöltése után a tartalom a felhőben lesz biztonságosan tárolva további feldolgozás és adatfolyam-továbbítás céljából. Az adategységben található fájlokat **adategység-fájloknak** nevezzük.

Az alábbiakban meghatározott **UploadFile** módszer a **CreateFromFile** módszert hívja meg (amely a .NET SDK-bővítmények között van meghatározva). A **CreateFromFile** létrehoz egy új adategységet, amelybe a megadott forrásfájl fel lesz töltve.

A **CreateFromFile** módszer számára az **AssetCreationOptions** alapján határozhatja meg, hogy az alábbi adategység-létrehozási lehetőségek közül melyiket használja:

* **Nincs** – Nincs titkosítás. Ez az alapértelmezett érték. Ügyeljen arra, hogy ezen lehetőség használatakor a tartalom sem átvitel, sem tárolás közben nincs védve.
  Ha egy MP4-fájlt progresszív letöltés útján tervez továbbítani, használja ezt a lehetőséget.
* **StorageEncrypted** – Ezen lehetőség használatakor a tiszta tartalom helyileg, 256 bites Advanced Encryption Standard (AES) titkosítással lesz titkosítva, és így kerül feltöltésre az Azure Storage tárolóba, ahol titkosítva lesz tárolva. A Storage-titkosítással védett adategységek titkosítása a kódolás előtt automatikusan fel lesz oldva, és egy titkosított fájlrendszerbe kerülnek; az új kimeneti adategységként való újbóli feltöltés előtt pedig lehetőség van az újbóli titkosításukra. A Storage-titkosítás elsősorban akkor hasznos, ha a kiváló minőségű bemeneti médiafájljait erős titkosítással szeretné védeni a lemezen való tároláskor.
* **CommonEncryptionProtected** – Használja ezt a lehetőséget, ha olyan tartalmat tölt fel, amely már korábban titkosítva és védve lett általános titkosítás vagy a PlayReady DRM által (például egy PlayReady DRM titkosítással védett Smooth Streaming-fájlt).
* **EnvelopeEncryptionProtected** – Használja ezt a lehetőséget, ha AES által titkosított HLS tartalmakat tölt fel. Megjegyzés: ehhez a fájlokat a Transform Manager használatával kell kódolni és titkosítani.

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


## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Forrásfájl kódolása adaptív sávszélességű MP4-fájlsorozattá
Miután az adategységek bevitele a Media Services szolgáltatásba megtörtént, a médiatartalmak többek között kódolhatók, transzmultiplexálás végezhető rajtuk vagy vízjelezhetők, mielőtt továbbítva lennének az ügyfelek felé. Ezen tevékenységek több háttérbeli szerepkörpéldányhoz képest vannak ütemezve és futtatva a magas teljesítmény és rendelkezésre állás biztosítása érdekében. Ezeket a tevékenységeket feladatoknak nevezzük. Minden egyes feladat több részműveletből áll, ezek végzik el a valódi munkát az adategységfájlon.

Mint azt korábban már említettük, az Azure Media Services használatának egyik leggyakoribb forgatókönyve az adaptív sávszélességű streamelés az ügyfelek felé. A Media Services az adaptív sávszélességű MP4-fájlokat a következő formátumokba tudja dinamikusan csomagolni: HTTP Live Streaming (HLS), Smooth Streaming és MPEG DASH.

A dinamikus csomagolás előnyeinek kihasználásához a következőket kell tennie:

* Kódolja a mezzanine forrásfájlt egy adaptív sávszélességű MP4- vagy Smooth Streaming-fájlsorozattá.  
* Helyezzen legalább egy adategységet arra a streamvégpontra, amelyről a tartalmat továbbítani tervezi.

A következő kód bemutatja, hogyan küldhet el egy kódolási feladatot. A feladat egyetlen műveletet tartalmaz, amely azért felel, hogy a mezzazine-fájlt egy adaptív sávszélességű MP4-fájlsorozattá kódolódjon át a **Media Encoder Standard** használatával. A kód elküldi a feladatot, és vár, amíg az befejeződik.

Amint a kódolási feladat befejeződött, lehetővé válik az adategységek közzététele, majd az MP4-fájlok streamelése vagy fokozatos letöltése.

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

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Adatkészlet közzététele és az adatfolyam-továbbításhoz és progresszív letöltéshez szükséges URL-címek lekérése

Egy adategység továbbításához vagy letöltéséhez először a „közzététele” szükséges, egy kereső létrehozásával. Az objektumban található fájlokhoz a lokátorok biztosítanak hozzáférést. A Media Services kétféle keresőtípust támogat: az OnDemandOrigin keresők médiatartalmak továbbításához használatosak (például MPEG DASH, HLS vagy Smooth Streaming), a közös hozzáférésű jogosultságkódos (SAS) keresők pedig médiafájlok letöltéséhez (a SAS-keresőkkel kapcsolatos további információkért lásd [ezt](http://southworks.com/blog/2015/05/27/reusing-azure-media-services-locators-to-avoid-facing-the-5-shared-access-policy-limitation/) a blogot).

### <a name="some-details-about-url-formats"></a>Néhány információ az URL-formátumokról

A keresők létrehozása után összeállíthatja a fájlok továbbításához vagy letöltéséhez használandó URL-címeket. Az oktatóanyagban lévő minta kimenetei olyan URL-címek, amelyek a megfelelő böngészőkbe beilleszthetőek. Ez a szakasz csak néhány rövid példán mutatja be a különféle formátumokat.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>Egy MPEG DASH-továbbítási URL-címnek a következő formátumban kell lennie:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest**(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>Egy HLS-továbbítási URL-címnek a következő formátumban kell lennie:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest**(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>Egy Smooth Streaming URL-címnek a következő formátumban kell lennie:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>Egy fájlok letöltéséhez használt SAS URL-címnek a következő formátumban kell lennie:

{blob-tároló neve}/{adategység neve}/{fájlnév}/{SAS-aláírás}

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

## <a name="test-by-playing-your-content"></a>A tartalom lejátszhatóságának tesztelése

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


A videótovábbításhoz illessze be az URL-címet az [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) lejátszó URL szövegmezőjébe.

A progresszív letöltés teszteléséhez másoljon egy URL-címet a böngészőjébe (például az Internet Explorerbe, Chrome-ba vagy Safariba).

További információkért tekintse át a következők témaköröket:

- [A tartalom lejátszása meglévő lejátszókkal](media-services-playback-content-with-existing-players.md)
- [Videolejátszó alkalmazások fejlesztése](media-services-develop-video-players.md)
- [MPEG-DASH adaptív streamelt videók beágyazása DASH.js-sel rendelkező HTML5-alkalmazásba](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Minta letöltése
Az oktatóanyagban létrehozott kódot a következő kód tartalmazza: [minta](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps"></a>Következő lépések 

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
[Portal]: http://manage.windowsazure.com/



<!--HONumber=Jan17_HO1-->


