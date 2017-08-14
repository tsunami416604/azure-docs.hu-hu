---
title: "Igény szerinti videótartalom-továbbítás az Azure Portal használatával | Microsoft Docs"
description: "Ez az oktatóanyag végigvezeti a lépéseken, amelyek segítségével alapszintű igény szerinti videotartalom-továbbítási szolgáltatást hozhat létre az Azure Portal segítségével, az Azure Media Services (AMS) alkalmazással."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: cbb67ef92386a6288b3317bf77ebb67f15ce7fb2
ms.contentlocale: hu-hu
ms.lasthandoff: 08/08/2017

---
# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Igény szerinti tartalomtovábbítás az Azure Portal használatával
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Ez az oktatóanyag végigvezeti a lépéseken, amelyek segítségével alapszintű igény szerinti videotartalom-továbbítási szolgáltatást hozhat létre az Azure Portal segítségével, az Azure Media Services (AMS) alkalmazással.

## <a name="prerequisites"></a>Előfeltételek
Az ismertetett eljárás végrehajtásához a következők szükségesek:

* Egy Azure-fiók. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
* Egy Media Services-fiók. A Media Services-fiók létrehozásáról a [Media Services-fiók létrehozása](media-services-portal-create-account.md) című cikk nyújt tájékoztatást.

Az oktatóanyag a következő feladatokat tartalmazza:

1. Indítsa el a streamvégpontot.
2. Videofájl feltöltése
3. Forrásfájl kódolása adaptív sávszélességű MP4-fájlokká
4. Az objektum közzététele, majd a streamelési és a progresszív letöltési URL-cím lekérése  
5. Tartalom lejátszása

## <a name="start-streaming-endpoints"></a>Streamvégpontok elindítása 

Az Azure Media Services egyik leggyakrabban használt funkciója a videók továbbítása az adaptív sávszélességű streamelés használatával. A Media Services dinamikus csomagolást biztosít, amelynek köszönhetően adaptív sávszélességű, MP4 formátumban kódolt tartalmait a Media Services által támogatott streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming) továbbíthatja igény szerint, mindezt anélkül, hogy az adott formátumban előcsomagolt verziót tárolna.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

A streamvégpont elindításához tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a Settings (Beállítások) ablak Streaming endpoints (Streamvégpontok) elemére. 
3. Kattintson az alapértelmezett streamvégpontra. 

    Megjelenik a DEFAULT STREAMING ENDPOINT DETAILS (Alapértelmezett streamvégpont adatai) ablak.

4. Kattintson a Start ikonra.
5. Mentse a módosításokat a Save (Mentés) gombra kattintva.

## <a name="upload-files"></a>Fájlok feltöltése
Ha az Azure Media Services használatával kíván videókat streamelni, fel kell töltenie a forrásvideókat, különböző bitsebességekre kell kódolnia azokat, majd közzé kell tennie az eredményt. Ez a rész a folyamat első lépését írja le. 

1. A **Settings** (Beállítások) ablakban kattintson az **Assets** (Objektumok) elemre.
   
    ![Fájlok feltöltése](./media/media-services-portal-vod-get-started/media-services-upload.png)
2. Kattintson az **Upload** (Feltöltés) gombra.
   
    Megjelenik az **Upload a video asset** (Videóobjektum feltöltése) ablak.
   
   > [!NOTE]
   > Tetszőleges méretű fájlt választhat.
   > 
   > 
3. Keresse meg a kívánt videót a számítógépen, jelölje ki, majd kattintson az OK gombra.  
   
    Megkezdődik a feltöltés. A folyamat előrehaladását a fájlnév alatt követheti nyomon.  

A feltöltés befejezését követően az új objektum bekerül az **Objektumok** ablakban található listába. 

## <a name="encode-assets"></a>Objektumok kódolása

Az Azure Media Services egyik legnépszerűbb funkciója, amikor a portál használatával adaptív sávszélességű streamelést biztosítunk az ügyfelek számára. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming és MPEG DASH. A videók adaptív sávszélességű streameléséhez többszörös sávszélességű fájlokká kell kódolnia a forrásvideókat. Javasoljuk, hogy a videók kódolásához használja a **Media Encoder Standard** kódolót.  

A Media Services dinamikus csomagolást is biztosít, aminek köszönhetően anélkül lehet MPEG DASH, HLS és Smooth Streaming formátumban közvetíteni többszörös sávszélességű MP4-streameket, hogy át kellene őket csomagolni ezekbe a streamformátumokba. A dinamikus csomagolás használatával csak egyféle formátumban kell tárolnia a fájlokat és fizetnie azok alapján, a Media Services pedig az ügyfelek igényeihez igazodva hozza létre és továbbítja számukra a megfelelő választ.

Annak érdekében, hogy kihasználhassa a dinamikus csomagolást, kódolja többszörös sávszélességű MP4-fájlokká a forrásfájlt (a kódolás lépéseit egy későbbi részben találja meg).

### <a name="to-use-the-portal-to-encode"></a>Kódolás a portál használatával
Ebben a részben leírjuk, milyen lépéseket kell elvégeznie a tartalmaknak a Media Encoder Standard segítségével történő kódolásához.

1. A **Settings** (Beállítások) ablakban válassza az **Assets** (Objektumok) lehetőséget.  
2. Az **Assets** (Objektumok) ablakban válassza ki a kódolni kívánt objektumot.
3. Nyomja meg az **Encode** (Kódolás) gombot.
4. Az **Encode an asset** (Objektum kódolása) ablakban válassza a „Media Encoder Standard” feldolgozóeszközt, és egy beállításkészletet. A beállításkészletekkel kapcsolatos információkért lásd [a sávszélességi skála automatikus létrehozását](media-services-autogen-bitrate-ladder-with-mes.md) és a [MES előre beállított feladatait](media-services-mes-presets-overview.md) ismertető részeket. Ha szeretné szabályozni, hogy a rendszer mely kódolási beállításkészletet használja, ne feledje: rendkívül fontos, hogy a bemeneti videóhoz leginkább megfelelő készletet válassza. Ha például tudja, hogy a bemeneti videó felbontása 1920 x 1080 képpont, akkor használja a „H264 Multiple Bitrate 1080p” beállításkészletet. Ha például alacsony felbontású (640 x 360) videót szeretne kódolni, ne válassza a „H264 Multiple Bitrate 1080p” beállításkészletet.
   
   Az egyszerűbb kezelés érdekében lehetősége van módosítani a kimeneti objektum nevét, illetve a feladat nevét.
   
   ![Objektumok kódolása](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Nyomja meg a **Create** (Létrehozás) gombot.

### <a name="monitor-encoding-job-progress"></a>Kódolási feladatok előrehaladásának figyelése
A kódolási feladat előrehaladásának figyeléséhez kattintson az oldal tetején található **Settings** (Beállítások), majd a **Jobs** (Feladatok) elemre.

![Feladatok](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Tartalom közzététele
Ahhoz, hogy átadhassa a tartalmak streamelésére vagy letöltésére használható URL-címet a felhasználónak, először „közzé kell tennie” az objektumot. Ehhez létre kell hoznia egy lokátort. Az objektumban található fájlokhoz a lokátorok biztosítanak hozzáférést. A Media Services két lokátortípust támogat: 

* Streamelési (OnDemandOrigin) lokátorokat, amelyek adaptív streameléshez (például MPEG DASH, HLS vagy Smooth Streaming adatok streameléséhez) használhatók. A streamelési lokátorok létrehozásához az objektumnak tartalmaznia kell egy .ism-fájlt. 
* Progresszív (SAS-) lokátorokat, amelyek a videó progresszív letöltésen keresztül történő továbbítására használatosak.

A streamelési URL-cím formátuma alább látható. Ez Smooth Streaming-objektumok lejátszására használható.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS-streamelési URL-cím létrehozásához fűzze hozzá a következőt az URL-címhez: (format=m3u8-aapl).

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH-streamelési URL-cím létrehozásához fűzze hozzá a következőt az URL-címhez: (format=mpd-time-csf).

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Az SAS URL-cím formátuma a következő:

    {blob container name}/{asset name}/{file name}/{SAS signature}

> [!NOTE]
> 2015 márciusa előtt a portál kétéves lejárati idővel hozta létre a lokátorokat.  
> 
> 

A lokátor lejárati idejének módosításához használjon [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) vagy [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API-t. Az SAS-keresők lejárati dátumának frissítésekor az URL-cím is módosul.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Az objektum portál segítségével történő közzététele
Az objektumnak a portál segítségével történő közzétételéhez tegye a következőket:

1. Válassza a **Settgings (Beállítások)** > **Assets (Objektumok)** lehetőséget.
2. Válassza ki a közzétenni kívánt objektumot.
3. Kattintson a **Publish** (Közzététel) gombra.
4. Válassza ki a lokátor típusát.
5. Nyomja meg az **Add** (Hozzáadás) gombot.
   
    ![Közzététel](./media/media-services-portal-vod-get-started/media-services-publish1.png)

Az URL-cím bekerül a **Közzétett URL-címek** listájába.

## <a name="play-content-from-the-portal"></a>Tartalom lejátszása a portálról
Az Azure Portalon talál egy tartalomlejátszót, amellyel tesztelheti a videót.

Kattintson a kívánt videóra, majd a **Lejátszás** gombra.

![Közzététel](./media/media-services-portal-vod-get-started/media-services-play.png)

Vegye figyelembe a következőket:

* A streamelés megkezdéséhez futtassa az **alapértelmezett** streamvégpontot.
* Ellenőrizze, hogy közzétette-e a videót.
* A **Media Player** az alapértelmezett streamvégpontból játssza le a fájlokat. Ha egy nem alapértelmezett streamvégpontból szeretne lejátszani valamit, rákattintva másolja az URL-címet, és használjon másik lejátszót. Például az [Azure Media Services lejátszót](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="next-steps"></a>Következő lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


