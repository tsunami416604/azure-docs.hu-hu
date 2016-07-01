<properties
    pageTitle=" Igény szerinti tartalomtovábbítás a klasszikus Azure portál használatával | Microsoft Azure"
    description="Ez az oktatóanyag végigvezeti azokon a lépéseken, amelyek segítségével igény szerinti videotartalom-továbbítási alkalmazást hozhat létre a klasszikus Azure portálon, az Azure Media Services segítségével."
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
    ms.date="02/25/2016"
    ms.author="juliako"/>


# Igény szerinti tartalomtovábbítás a klasszikus Azure portál használatával


[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


Ez az oktatóanyag végigvezeti a lépéseken, amelyek segítségével alapszintű igény szerinti videotartalom-továbbítási alkalmazást hozhat létre a klasszikus Azure portálon.

> [AZURE.NOTE] Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](/pricing/free-trial/?WT.mc_id=A261C142F). 


Az oktatóanyag a következő feladatokat tartalmazza:

1.  Azure Media Services-fiók létrehozása
2.  Streamvégpont konfigurálása
1.  Videofájl feltöltése
1.  Forrásfájl kódolása adaptív sávszélességű MP4-fájlokká
1.  Az objektum közzététele, majd a streamelési és a progresszív letöltési URL-cím lekérése  
1.  Tartalom lejátszása


## Azure Media Services-fiók létrehozása

1. A [klasszikus Azure portálon](https://manage.windowsazure.com/) kattintson az **Új** > **Media Service** elemre, majd a **Gyorslétrehozás** lehetőségre.

    ![Media Services, Gyorslétrehozás](./media/media-services-portal-get-started/wams-QuickCreate.png)

2. A **NÉV** mezőben adja meg az új fiók nevét. A Media Services-fiók neve csak kisbetűket és számokat tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.

3. A **RÉGIÓ** részben válassza ki azt a földrajzi régiót, amelyben tárolni fogja a Media Services-fiókhoz tartozó metaadat-bejegyzéseket. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg.

4. A **TÁRFIÓK** résznél válasszon egy tárfiókot, amely Blob Storage tárolót fog biztosítani a Media Services-fiókhoz tartozó médiatartalmak számára. Választhat egy, a Media Services-fiókkal azonos földrajzi régióban elhelyezkedő meglévő tárfiókot, vagy új fiókot is létrehozhat. A újonnan létrehozott tárfiók is ugyanahhoz a régióhoz fog tartozni.

5. Az új tárfiók létrehozását követően a **NEW STORAGE ACCOUNT NAME** (ÚJ TÁRFIÓK NEVE) mezőben adjon nevet a tárfióknak. A tárfiók nevére ugyanazok a szabályok vonatkoznak, mint a Media Services-fiókok nevére.

6. Kattintson az űrlap alján található **Gyorslétrehozás** lehetőségre.

    A folyamat állapotát az ablak alsó részén található üzenetterületen követheti nyomon.

    Sikeres létrehozását követően a fiók állapota „Aktív” lesz.

    Az oldal alján megjelenik a **KULCSOK KEZELÉSE** gomb. Ha rákattint a gombra, megjelenik egy, a Media Services-fiók nevét, illetve az elsődleges és másodlagos kulcsot tartalmazó párbeszédpanel. A Media Services-fiók programon keresztüli eléréséhez szüksége lesz a fiók nevére és az elsődleges kulcs adataira.

    ![Media Services lap](./media/media-services-portal-get-started/wams-mediaservices-page.png)

    Ha duplán kattint a fiók nevére, alapértelmezés szerint a Gyors üzembe helyezés lap jelenik meg. Ezen az oldalon bizonyos gyakori felügyeleti feladatok elvégzésére van lehetőség. Ezek a portál más lapjain is megtalálhatóak. Videót például erről a lapról és a CONTENT (Tartalom) lapról egyaránt feltölthet.


## Strevégpont konfigurálása a portál használatával

Az Azure Media Services egyik legnépszerűbb funkciója, amikor a portál használatával adaptív sávszélességű streamelést biztosítunk az ügyfelek számára. Adaptív sávszélességű streamelés esetén az ügyfél az elérhető hálózati sávszélesség, a processzorhasználat és más tényezők függvényében a videó megjelenítését követően magasabb vagy alacsonyabb sávszélességű streamelésre kapcsolhat. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH és HDS (amelyhez Adobe PrimeTime-/Access-licenc szükséges).

A Media Services dinamikus becsomagolást biztosít, amelynek köszönhetően adaptív sávszélességű MP4 vagy Smooth Streaming formátumban kódolt tartalmait a Media Services által támogatott streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming, HDS) továbbíthatja, mindezt anélkül, hogy át kellene őket csomagolni ezekbe a formátumokba.

A dinamikus becsomagolás funkció használatához tegye a következőket:

- Kódolja adaptív sávszélességű MP4- vagy Smooth Streaming-fájlokká a forrásfájlt (a kódolás lépéseit az oktatóanyag egy későbbi részében találja meg).  
- Szerezzen be legalább egy streamelési egységet a tartalom továbbításához használni kívánt *streamvégpontra*.

A dinamikus becsomagolás révén elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Streameléshez fenntartott egységek számának megváltoztatásához tegye a következőket:

1. A [klasszikus Azure portálon](https://manage.windowsazure.com/) kattintson a **Media Services** elemre. Ezt követően kattintson az adott médiaszolgáltatás nevére.

2. Válassza a STREAMING ENDPOINTS (Streamvégpontok) lapot. Itt kattintson a módosítani kívánt streamvégpontra.

3. A streamelési egységek számának meghatározásához kattintson a **SCALE** (Skálázás) lapra, majd mozgassa tetszőleges irányba a **reserved capacity** (lefoglalt kapacitás) csúszkát.

    ![A Scale (Skálázás) lap](./media/media-services-portal-get-started/media-services-origin-scale.png)

4. A módosítások mentéséhez kattintson a **SAVE** (Mentés) gombra.

    Az új egységek allokációja akár 20 percig is eltarthat.

    >[AZURE.NOTE] Ha pozitív értékről nullára csökkenti a streamelési egységek számát, azzal jelenleg akár egy órára is elérhetetlenné teheti a streamelési funkciót.
    >
    > A fizetendő összeg kiszámításához az adott 24 órás időszakban megadott legmagasabb egységszámot vesszük alapul. Az árképzésre vonatkozó további információkért lásd: [Media Services Pricing Details](http://go.microsoft.com/fwlink/?LinkId=275107) (A Media Services árképzésére vonatkozó információk).

## Tartalom feltöltése


1. A [klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409) kattintson a **Media Services** elemre, majd a kívánt Media Services-fiók nevére.
2. Válassza a CONTENT (Tartalom) lapot.
3. Kattintson a **Feltöltés** gombra a lapon vagy a portál alján.
4. A **Tartalom feltöltése** párbeszédpanelen keresse meg a kívánt objektumfájlt. Kattintson a fájlra, majd kattintson a **Megnyitás** gombra, vagy nyomja le az Enter billentyűt.

    ![Tartalom feltöltése párbeszédpanel][uploadcontent]

5. A **Tartalom feltöltése** párbeszédpanelen a **Fájl** és a **Tartalomnév** értékének elfogadásához kattintson a pipa gombra.
6. Megkezdődik a feltöltés, amelynek előrehaladását a portál alján követheti nyomon.  

    ![Feladat állapota][status]

A feltöltés befejezését követően az új objektum bekerül a Tartalom listába. A konvenciók szerint a rendszer a névhez hozzáfűzi a „**-Source**” szót, ezzel segít nyomon követni a kódolási feladatokhoz feltöltött új forrástartalmakat.

![Tartalom lap][contentpage]

Ha a feltöltési folyamat befejeződését követően nem módosul a fájlméret, kattintson a **Sync Metadata** (Metaadatok szinkronizálása) gombra. Ekkor a rendszer szinkronizálja az objektum fájlméretét a tárhelyen lévő fájl tényleges méretével, és frissíti az értéket a Content (Tartalom) lapon.


## Tartalom kódolása

### Áttekintés

Ahhoz, hogy digitális videót továbbíthasson az interneten keresztül, először el kell végezni a tartalom tömörítését. A Media Services szolgáltatásnak egy kódoló is részét képezi, amelyben részletesen beállíthatja, hogy hogyan szeretné kódolni tartalmait (megadhatja például a használni kívánt kodeket, fájlformátumot, felbontást és sávszélességet).

Az Azure Media Services egyik legnépszerűbb funkciója, amikor a portál használatával adaptív sávszélességű streamelést biztosítunk az ügyfelek számára. Adaptív sávszélességű streamelés esetén az ügyfél az elérhető hálózati sávszélesség, a processzorhasználat és más tényezők függvényében a videó megjelenítését követően magasabb vagy alacsonyabb sávszélességű streamelésre kapcsolhat. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH és HDS (amelyhez Adobe PrimeTime-/Access-licenc szükséges).

A Media Services dinamikus becsomagolást biztosít, amelynek köszönhetően adaptív sávszélességű MP4 vagy Smooth Streaming formátumban kódolt tartalmait a Media Services által támogatott streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming vagy HDS) továbbíthatja, mindezt anélkül, hogy át kellene őket csomagolni ezekbe a formátumokba.

A dinamikus becsomagolás funkció használatához tegye a következőket:

- Kódolja adaptív sávszélességű MP4- vagy Smooth Streaming-fájlokká a forrásfájlt (a kódolás lépéseit az oktatóanyag egy későbbi részében találja meg).
- Szerezzen be legalább egy igényalapú streamelési egységet a tartalom továbbításához használni kívánt streamvégpontra. További információk: [How to scale On-Demand Streaming reserved units](media-services-manage-origins.md#scale_streaming_endpoints/) (Igényalapú streameléshez fenntartott egységek méretezése).

A dinamikus becsomagolás révén elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Ne feledje, hogy a dinamikus becsomagolási funkciók használata mellett az igényalapú streameléshez fenntartott egységek dedikált kimeneti funkciót is biztosítanak, amely 200 Mb/s-os lépésekben vásárolható meg. Alapértelmezés szerint az igényalapú streamelés megosztott példányos módon működik, ami azt jelenti, hogy Ön más felhasználókkal osztozik a kiszolgáló-erőforrásokon (például a számítási erőforrásokon vagy a kilépési kapacitáson). Az igényalapú streamelés sebességének javítása érdekében javasoljuk, hogy vásároljon igényalapú streameléshez fenntartott egységeket.

### Kódolás

Ebben a részben leírjuk, milyen lépéseket kell elvégeznie a tartalmaknak a Media Encoder Standard és a klasszikus Azure portál segítségével történő kódolásához.

1.  Válassza ki a kódolni kívánt fájlt.
    Ha a rendszer támogatja az adott fájltípus kódolását, a Content (Tartalom) lap alján elérhetővé válik a **PROCESS** (Feldolgozás) gomb.
4. A **Process** (Feldolgozás) párbeszédpanelen válassza a **Media Encoder Standard** feldolgozóeszközt.
5. Válasszon a **kódolási konfigurációk** közül.

    ![Process2][process2]

    A [Task Preset Strings for Media Encoder Standard](https://msdn.microsoft.com/en-US/library/mt269960) (Feladatokhoz beállított karakterláncok a Media Encoder Standard feldolgozóeszközhöz) című témakörből megtudhatja, hogy mit jelentenek az egyes beállításkészletek.  

5. Ezt követően adja meg a kimenő tartalom felhasználóbarát nevét, vagy fogadja el az alapértelmezést. A kódolási művelet elkezdéséhez kattintson a pipa gombra. A folyamat előrehaladását a portál alsó részén követheti nyomon.
6. Kattintson az **OK** gombra.

    A kódolás befejezését követően a kódolt fájl bekerül a CONTENT (Tartalom) lapra.

    A kódolási feladat előrehaladásának megtekintéséhez váltson a **JOBS** (Feladatok) lapra.  

    Ha a kódolás befejezését követően nem módosul a fájlméret, kattintson a **Sync Metadata** (Metaadatok szinkronizálása) gombra. Ekkor a rendszer szinkronizálja a kimeneti objektum fájlméretét a tárhelyen lévő fájl tényleges méretével, és frissíti az értéket a Content (Tartalom) lapon.


## Tartalom közzététele

### Áttekintés

Ahhoz, hogy átadhassa a tartalmak streamelésére vagy letöltésére használható URL-címet a felhasználónak, először „közzé kell tennie” az objektumot. Ehhez létre kell hoznia egy lokátort. Az objektumban található fájlokhoz a lokátorok biztosítanak hozzáférést. A Media Services két lokátortípust támogat: az egyik az OnDemandOrigin lokátortípus, amely médiafájlok (például MPEG DASH, HLS vagy Smooth Streaming) streamelésére használható, a másik pedig a hozzáférési aláírás (SAS) lokátortípus, amely médiafájlok letöltésére alkalmas.

Ha a klasszikus Azure portál segítségével teszi közzé tartalmait, a rendszer létrehozza Ön helyett a lokátorokat, és átadja Önnek az OnDemand-alapú URL-címet (ha az objektum .ism-fájlt tartalmaz) vagy az SAS URL-címet.

Az SAS URL-cím formátuma a következő:

    {blob container name}/{asset name}/{file name}/{SAS signature}

A streamelési URL-cím formátuma alább látható. Ez Smooth Streaming-objektumok lejátszására használható.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS-streamelési URL-cím létrehozásához fűzze hozzá a következőt az URL-címhez: (format=m3u8-aapl).

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH-streamelési URL-cím létrehozásához fűzze hozzá a következőt az URL-címhez: (format=mpd-time-csf).

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


A lokátoroknak lejárati idejük van. Ha a portál segítségével teszi közzé az objektumot, a rendszer 100 éves lejárati idővel hozza létre a lokátorokat.

>[AZURE.NOTE] 2015 márciusa előtt a portál kétéves lejárati idővel hozta létre a lokátorokat.  

A lokátor lejárati idejének módosításához használjon [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) vagy [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API-t. Ne feledje, hogy a SAS-lokátor lejárati idejének módosításával az URL-cím is megváltozik.

### Közzététel

Az objektumnak a portál segítségével történő közzétételéhez tegye a következőket:

1. Válassza ki az objektumot.
2. Kattintson a Közzététel gombra.

 ![Közzétett objektum][publishedcontent]


## Tartalom lejátszása a portálról

A klasszikus Azure portálon talál egy tartalomlejátszót, amellyel tesztelheti a videót.

Kattintson a kívánt videóra, majd a portál alsó részén található **Lejátszás** gombra.

Vegye figyelembe a következőket:

- Ellenőrizze, hogy közzétette-e a videót.
- A **MEDIA SERVICES CONTENT PLAYER** (Media Services tartalomlejátszó) az alapértelmezett streamvégpontból játssza le a fájlokat. Ha egy nem alapértelmezett streamvégpontból szeretne lejátszani valamit, használjon másik lejátszót. Például az [Azure Media Services lejátszót](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]



##Következő lépés: A Media Services tanulási útvonalai

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Visszajelzés küldése

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## Valami mást keres?

Ha ebben a témakörben nem azt találta, amire számított, valami hiányzik, vagy bármilyen más módon hiányérzete maradt, mondja el nekünk véleményét az alábbi Disqus-beszélgetés segítségével.

### További források
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - Get your video online now! (Videó feltöltése az internetre)</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - Dynamic Packaging and Mobile Devices (Dinamikus becsomagolás és mobileszközök)</a>


<!-- Anchors. -->


<!-- URLs. -->
[Klasszikus Azure portál]: http://manage.windowsazure.com/


<!-- Images -->
[portaloverview]: ./media/media-services-portal-get-started/media-services-content-page.png
[publishedcontent]: ./media/media-services-portal-get-started/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-portal-get-started/UploadContent.png
[status]: ./media/media-services-portal-get-started/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-portal-get-started/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-portal-get-started/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-portal-get-started/media-services-portal-player.png



<!--HONumber=Jun16_HO2--->


