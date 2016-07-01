<properties
    pageTitle=" Igény szerinti tartalomtovábbítás az Azure portál használatával | Microsoft Azure"
    description="Ez az oktatóanyag végigvezeti a lépéseken, amelyek segítségével alapszintű igény szerinti videotartalom-továbbítási szolgáltatást hozhat létre az Azure portál segítségével, az Azure Media Services (AMS) alkalmazással."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/05/2016"
    ms.author="juliako"/>


# Igény szerinti tartalomtovábbítás az Azure portál használatával (előzetes)

Ez az oktatóanyag végigvezeti a lépéseken, amelyek segítségével alapszintű igény szerinti videotartalom-továbbítási szolgáltatást hozhat létre az Azure portál segítségével, az Azure Media Services (AMS) alkalmazással.

Az Azure portál Azure Media Services szolgáltatása jelenleg előzetes verziójú. 

> [AZURE.NOTE] Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 

Az oktatóanyag a következő feladatokat tartalmazza:

1.  Azure Media Services-fiók létrehozása
2.  Streamvégpont konfigurálása
1.  Videofájl feltöltése
1.  Forrásfájl kódolása adaptív sávszélességű MP4-fájlokká
1.  Az objektum közzététele, majd a streamelési és a progresszív letöltési URL-cím lekérése  
1.  Tartalom lejátszása


## Azure Media Services-fiók létrehozása

Ebben a részben az új AMS-fiók létrehozásához szükséges lépéseket találja.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Kattintson az **+Új** > **Media Service + CDN** > **Media Services** elemre.

    ![Media Services, létrehozás](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. A **CREATE MEDIA SERVICES ACCOUNT** (Media Services-fiók létrehozása) részben adja meg a kívánt értékeket.

    ![Media Services, létrehozás](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Az **Account Name** (Fiók neve) mezőben adja meg az új AMS-fiók nevét. A Media Services-fiók neve csak kisbetűket és számokat tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.
    2. A Subscription (Előfizetés) résznél válasszon az elérhető Azure-előfizetések közül.
    
    2. A **Resource Group** (Erőforráscsoport) résznél válasszon egy új vagy meglévő erőforrást.  Az erőforráscsoportok erőforrások olyan gyűjteményei, amelyekre azonos életciklus, engedélyek és szabályzatok vonatkoznak. További információkat [itt](resource-group-overview.md#resource-groups) talál.
    3. A **Location** (Hely) részben válassza ki azt a földrajzi régiót, amelyben tárolni fogja a Media Services-fiókhoz tartozó médiafájlokat és metaadat-bejegyzéseket. A rendszer e régió alapján fogja feldolgozni, illetve streamelni a médiafájlokat. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg. 
    
    3. A **Storage Account** (Tárfiók) résznél válasszon egy tárfiókot, amely Blob Storage tárolót fog biztosítani a Media Services-fiókhoz tartozó médiatartalmak számára. Választhat egy, a Media Services-fiókkal azonos földrajzi régióban elhelyezkedő meglévő tárfiókot, vagy új fiókot is létrehozhat. A újonnan létrehozott tárfiók is ugyanahhoz a régióhoz fog tartozni. A tárfiók nevére ugyanazok a szabályok vonatkoznak, mint a Media Services-fiókok nevére.

        További információkat a tárhelyről [itt](storage-introduction.md) talál.

    4. A fióklétrehozás előrehaladásának megtekintéséhez kattintson a **Rögzítés az irányítópulton** elemre.
    
7. Kattintson az űrlap alján található **Létrehozás** lehetőségre.

    A fiók sikeres létrehozását követően státusza **Fut** értékre változik. 

    ![Media Services, beállítások](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Az AMS-fiók kezeléséhez (például videók feltöltéséhez, objektumok kódolásához, a feladatok előrehaladásának figyeléséhez) használja a **Settings** (Beállítások) ablakot.

## Kulcsok kezelése

A Media Services-fiók programon keresztüli eléréséhez szüksége lesz a fiók nevére és az elsődleges kulcs adataira.

1. Válassza ki a fiókját az Azure portálon. 

    Jobb oldalt megjelenik a **Beállítások** ablak. 

2. A **Beállítások** ablakban válassza a **Kulcsok** lehetőséget. 

    A **Kulcsok kezelése** ablakban megtalálja a fiók nevét, valamint az elsődleges és másodlagos kulcsot. 
3. Az értékek másolásához kattintson a Másolás gombra.
    
    ![Media Services, kulcsok](./media/media-services-portal-vod-get-started/media-services-keys.png)

## Streamvégpontok konfigurálása

Az Azure Media Services egyik legnépszerűbb funkciója, amikor a portál használatával adaptív sávszélességű streamelést biztosítunk az ügyfelek számára. Adaptív sávszélességű streamelés esetén az ügyfél az elérhető hálózati sávszélesség, a processzorhasználat és más tényezők függvényében a videó megjelenítését követően magasabb vagy alacsonyabb sávszélességű streamelésre kapcsolhat. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH és HDS (amelyhez Adobe PrimeTime-/Access-licenc szükséges).

A Media Services dinamikus becsomagolást biztosít, amelynek köszönhetően adaptív sávszélességű MP4 formátumban kódolt tartalmait a Media Services által támogatott streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming, HDS) továbbíthatja igény szerint, mindezt anélkül, hogy a fájlokból az adott formátumban előcsomagolt verziót kellene tárolnia.

A dinamikus becsomagolás funkció használatához tegye a következőket:

- Kódolja adaptív sávszélességű MP4-fájlokká a forrásfájlt (a kódolás lépéseit az oktatóanyag egy későbbi részében találja meg).  
- Hozzon létre legalább egy streamelési egységet a tartalom továbbításához használni kívánt *streamvégpontra*. Az alábbi lépésekből megtudhatja, hogyan módosíthatja a streamelési egységek számát.

A dinamikus becsomagolás révén elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Streameléshez fenntartott egységek létrehozásához és számának megváltoztatásához tegye a következőket:


1. Kattintson a **Settings** (Beállítások) ablak **Streaming endpoints** (Streamvégpontok) elemére. 

2. Kattintson az alapértelmezett streamvégpontra. 

    Megjelenik a **DEFAULT STREAMING ENDPOINT DETAILS** (Alapértelmezett streamvégpont adatai) ablak.

3. Adja meg a streamelési egységek számát a **Streaming units** (Streamelési egységek) csúszka mozgatásával.

    ![Streamelési egységek](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Mentse a módosításokat a **Save** (Mentés) gombra kattintva.

    >[AZURE.NOTE]Az új egységek allokációja akár 20 percig is eltarthat.

## Fájlok feltöltése

A videóknak az Azure Media Services segítségével történő streameléséhez fel kell töltenie a forrásvideókat, kódolnia kell őket több különböző sávszélességre, majd közzé kell tennie az eredményeket. Ez a rész a folyamat első lépését írja le. 

1. A **Settings** (Beállítások) ablakban kattintson az **Assets** (Objektumok) elemre.

    ![Fájlok feltöltése](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. Kattintson az **Upload** (Feltöltés) gombra.

    Megjelenik az **Upload a video asset** (Videóobjektum feltöltése) ablak.

    >[AZURE.NOTE] Tetszőleges méretű fájlt választhat.
    
4. Keresse meg a kívánt videót a számítógépen, jelölje ki, majd kattintson az OK gombra.  

    Megkezdődik a feltöltés. A folyamat előrehaladását a fájlnév alatt követheti nyomon.  

A feltöltés befejezését követően az új objektum bekerül az **Assets** (Objektumok) ablakban található listába. 


## Objektumok kódolása

Az Azure Media Services egyik legnépszerűbb funkciója, amikor a portál használatával adaptív sávszélességű streamelést biztosítunk az ügyfelek számára. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH és HDS (amelyhez Adobe PrimeTime-/Access-licenc szükséges). A videók adaptív sávszélességű streameléséhez többszörös sávszélességű fájlokká kell kódolnia a forrásvideókat. Javasoljuk, hogy a videók kódolásához használja a **Media Encoder Standard** kódolót.  

A Media Services dinamikus becsomagolást is biztosít, aminek köszönhetően anélkül lehet MPEG DASH, HLS, Smooth Streaming illetve HDS formátumban közvetíteni többszörös sávszélességű MP4-streameket, hogy át kellene őket csomagolni ezekbe a streamformátumokba. A dinamikus becsomagolás révén elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

A dinamikus becsomagolás funkció használatához tegye a következőket:

- Kódolja többszörös sávszélességű MP4-fájlokká a forrásfájlt (a kódolás lépéseit egy későbbi részben találja meg).
- Szerezzen be legalább egy streamelési egységet a tartalom továbbításához használni kívánt streamvégpontra. További információkért lásd a [streamvégpontok konfigurálását bemutató részt](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

### Kódolás a portál használatával

Ebben a részben leírjuk, milyen lépéseket kell elvégeznie a tartalmaknak a Media Encoder Standard segítségével történő kódolásához.

1.  A **Settings** (Beállítások) ablakban válassza az **Assets** (Objektumok) lehetőséget.  
2.  Az **Assets** (Objektumok) ablakban válassza ki a kódolni kívánt objektumot.
3.  Nyomja meg az **Encode** (Kódolás) gombot.
4.  Az **Encode an asset** (Objektum kódolása) ablakban válassza a „Media Encoder Standard” feldolgozóeszközt, és egy beállításkészletet. Ha például tudja, hogy a bemeneti videó felbontása 1920 x 1080 képpont, akkor használja a „H264 Multiple Bitrate 1080p” beállításkészletet. A beállításkészletekkel kapcsolatban [ebben](https://msdn.microsoft.com/library/azure/mt269960.aspx) a cikkben talál további információkat – rendkívül fontos, hogy a bemeneti videóhoz leginkább megfelelő készletet válassza. Ha például alacsony felbontású (640 x 360) videót szeretne kódolni, ne válassza az alapértelmezett „H264 Multiple Bitrate 1080p” beállításkészletet.
    
    Az egyszerűbb kezelés érdekében lehetősége van módosítani a kimeneti objektum nevét, illetve a feladat nevét.
        
    ![Objektumok kódolása](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Nyomja meg a **Create** (Létrehozás) gombot.

### Kódolási feladatok előrehaladásának figyelése

A kódolási feladat előrehaladásának figyeléséhez kattintson az oldal tetején található **Settings** (Beállítások) elemre, majd a **Jobs** (Feladatok) lehetőségre.

![Feladatok](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## Tartalom közzététele

Ahhoz, hogy átadhassa a tartalmak streamelésére vagy letöltésére használható URL-címet a felhasználónak, először „közzé kell tennie” az objektumot. Ehhez létre kell hoznia egy lokátort. Az objektumban található fájlokhoz a lokátorok biztosítanak hozzáférést. A Media Services két lokátortípust támogat: 

- Streamelési (OnDemandOrigin) lokátorokat, amelyek adaptív streameléshez (például MPEG DASH, HLS vagy Smooth Streaming adatok streameléséhez) használhatók. A streamelési lokátorok létrehozásához az objektumnak tartalmaznia kell egy .ism-fájlt. 
- Progresszív (SAS-) lokátorokat, amelyek a videó progresszív letöltésen keresztül történő továbbítására használatosak.


A streamelési URL-cím formátuma alább látható. Ez Smooth Streaming-objektumok lejátszására használható.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS-streamelési URL-cím létrehozásához fűzze hozzá a következőt az URL-címhez: (format=m3u8-aapl).

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH-streamelési URL-cím létrehozásához fűzze hozzá a következőt az URL-címhez: (format=mpd-time-csf).

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Az SAS URL-cím formátuma a következő:

    {blob container name}/{asset name}/{file name}/{SAS signature}

>[AZURE.NOTE] 2015 márciusa előtt a portál kétéves lejárati idővel hozta létre a lokátorokat.  

A lokátor lejárati idejének módosításához használjon [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) vagy [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API-t. Ne feledje, hogy a SAS-lokátor lejárati idejének módosításával az URL-cím is megváltozik.

### Az objektum portál segítségével történő közzététele

Az objektumnak a portál segítségével történő közzétételéhez tegye a következőket:

1. Válassza a **Settgings (Beállítások)** > **Assets (Objektumok)** lehetőséget.
1. Válassza ki a közzétenni kívánt objektumot.
1. Kattintson a **Publish** (Közzététel) gombra.
1. Válassza ki a lokátor típusát.
2. Nyomja meg az **Add** (Hozzáadás) gombot.

    ![Közzététel](./media/media-services-portal-vod-get-started/media-services-publish1.png)

A rendszer hozzáadja az URL-címet a **Published URLs** (Közzétett URL-címek) listához.

## Tartalom lejátszása a portálról

Az Azure portálban talál egy tartalomlejátszót, amellyel tesztelheti a videót.

Kattintson a kívánt videóra, majd a **Lejátszás** gombra.

![Közzététel](./media/media-services-portal-vod-get-started/media-services-play.png)

Vegye figyelembe a következőket:

- Ellenőrizze, hogy közzétette-e a videót.
- A *Media Player** az alapértelmezett streamvégpontból játssza le a fájlokat. Ha egy nem alapértelmezett streamvégpontból szeretne lejátszani valamit, rákattintva másolja az URL-címet, és használjon másik lejátszót. Például az [Azure Media Services lejátszót](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

##Következő lépés: A Media Services tanulási útvonalai

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Visszajelzés küldése

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





<!--HONumber=Jun16_HO2--->


