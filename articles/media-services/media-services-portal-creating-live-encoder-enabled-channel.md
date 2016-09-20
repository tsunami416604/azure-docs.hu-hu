<properties 
    pageTitle="Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében a klasszikus Azure portálon" 
    description="Ez az oktatóanyag bemutatja, hogyan hozzon létre egy egyszeres sávszélességű élő streamet fogadó csatornát, amely többszörös sávszélességű streammé kódolja azt a klasszikus Azure portál segítségével." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako,anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="06/22/2016"
    ms.author="juliako"/>


#Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében a klasszikus Azure portálon

> [AZURE.SELECTOR]
- [Portál](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

Ez az oktatóanyag bemutatja, hogyan hozzon létre egy egyszeres sávszélességű élő streamet fogadó **csatornát**, amely többszörös sávszélességű streammé kódolja azt.

>[AZURE.NOTE]További elméleti információk a valós idejű kódolásra képes csatornákról: [Élő adatfolyam továbbítása az Azure Media Services használatával, és többféle sávszélességű adatfolyamok létrehozása](media-services-manage-live-encoder-enabled-channels.md)

##Az élő streamelés egy gyakori alaphelyzete

A leggyakrabban használt streamelési alkalmazások kialakításához általában az alábbi lépések szükségesek.

>[AZURE.NOTE] Jelenleg az élő stream maximális javasolt időtartama 8 óra. Ha hosszabb időtartamon át szeretné működtetni a csatornát, forduljon „amslived”-hez a Microsoft.com oldalon.

1. Csatlakoztasson egy videokamerát a számítógéphez. Indítson el és állítson be egy helyszíni valós idejű kódolót, amely képes egy egyféle sávszélességű kimeneti adatfolyam továbbítására a következő protokollok valamelyikével: RTMP, Smooth Streaming vagy RTP (MPEG-TS). További tudnivalók: [Azure Media Services RMTP-támogatása és valós idejű kódolók](http://go.microsoft.com/fwlink/?LinkId=532824)
    
    Ezt a lépést a csatorna létrehozása után is elvégezheti.

1. Hozzon létre és indítson el egy csatornát. 

1. Kérje le a csatorna feldolgozó URL-címét. 

    Az élő kódoló a bemeneti URL-címet használva küldi el a streamet a csatornának.
1. Kérje le a csatorna előnézeti URL-címét. 

    Ezen az URL-címen győződhet meg róla, hogy a csatorna rendben megkapja-e az élő streamet.

3. Hozzon létre egy programot (ezzel egy objektumot is létrehoz). 
1. Tegye közzé a programot (ezzel létrehozza a kapcsolódó objektumhoz tartozó OnDemand-lokátort is).  

    Biztosítsa, hogy legyen legalább egy, a folyamatos adatátvitelhez fenntartott egység a streamvégpontján, amelyről a tartalmat továbbítani kívánja.
1. Indítsa el a programot, ha készen áll az adatfolyam-továbbításra és az archiválásra.
2. További lehetőségként jelzést adhat a valós idejű kódolónak egy hirdetés elindítására. A hirdetés a kimeneti adatfolyamba lesz beszúrva.
1. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.
1. Törölje a programot (ha kívánja, törölje az objektumot is).   

##Az oktatóanyag tartalma

Ebben az oktatóanyagban a következő feladatokat fogjuk elvégezni a klasszikus Azure portál segítéségével: 

2.  Streamvégpontok konfigurálása
3.  Élő kódolásra alkalmas csatorna létrehozása
1.  Betöltési URL-cím lekérése, majd átadása az élő kódolónak Az élő kódoló erre az URL-címre tölti be a streamet a csatornának. .
1.  Program (és objektum) létrehozása
1.  Az objektum közzététele és a streamelési URL-címek lekérése  
1.  Tartalom lejátszása 
2.  Takarítás

##Előfeltételek
Az oktatóanyag elvégzésének a következők a feltételei.

- Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
- Egy Media Services-fiók szükséges. A Media Services-fiók létrehozásával kapcsolatban lásd: [Create Account](media-services-create-account.md) (Fiók létrehozása).
- Egy webkamera és egy egyszeres sávszélességű élő stream továbbítására alkalmas kódoló.

##Streamvégpont konfigurálása a portál használatával

Az Azure Media Services egyik legnépszerűbb funkciója, amikor a portál használatával adaptív sávszélességű streamelést biztosítunk az ügyfelek számára. Az adaptív sávszélességű streameléskor az ügyfél magasabb vagy alacsonyabb sávszélességű adatfolyam-továbbításra válthat a videó lejátszása közben, az aktuális hálózati sávszélességhez, CPU-használathoz és egyéb tényezőkhöz igazodva. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH, és HDS (csak Adobe PrimeTime/Access licenctulajdonosok esetében). 

Élő streameléssel való munkavégzés esetén a helyszíni élő kódoló (esetünkben a Wirecast) tölti be a csatornának a többszörös sávszélességű élő streamet. Amikor egy felhasználó lekéri a streamet, a Media Services dinamikus becsomagolás segítségével átcsomagolja a forrásstreamet a kért adaptív sávszélességű streamformátumba (ez lehet HLS, DASH vagy Smooth). 

A dinamikus becsomagolás által nyújtott előnyök kihasználásához legalább egy streamelési egységnek rendelkezésre kell állnia azon a **streamvégponton**, amely a tervek szerint közvetíteni fogja a tartalmakat.

A folyamatos átvitelhez fenntartott egységek számának módosításához tegye a következőt:

1. A [klasszikus Azure portálon](https://manage.windowsazure.com/) kattintson a **Media Services** elemre. Ezt követően kattintson az adott médiaszolgáltatás nevére.

2. Válassza ki az STREAMING ENDPOINTS (STREAMVÉGPONTOK) oldalt. Itt kattintson a módosítani kívánt streamvégpontra.

3. A streamelési egységek számának meghatározásához kattintson a SCALE (Skálázás) lapra, majd mozgassa tetszőleges irányba a **reserved capacity** (lefoglalt kapacitás) csúszkát.

    ![A Scale (Skálázás) lap](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-origin-scale.png)

4. A módosítások mentéséhez kattintson a SAVE (Mentés) gombra.

    Az új egységek allokációja akár 20 percig is eltarthat. 

     
    >[AZURE.NOTE] Ha pozitív értékről nullára csökkenti a streamelési egységek számát, azzal jelenleg akár egy órára is elérhetetlenné teheti a streamelési funkciót.
    >
    > A fizetendő összeg kiszámításához az adott 24 órás időszakban megadott legmagasabb egységszámot vesszük alapul. Az árképzésre vonatkozó további információkért lásd: [Media Services Pricing Details](http://go.microsoft.com/fwlink/?LinkId=275107) (A Media Services árképzésére vonatkozó információk).

 
##CSATORNA létrehozása

1.  A [klasszikus Azure portálon](http://manage.windowsazure.com/) kattintson a Media Services elemre, majd a kívánt Media Services-fiók nevére.
2.  Válassza a CHANNELS (Csatornák) lapot.
3.  Új csatorna hozzáadásához kattintson az (Add+) (Hozzáadás) elemre.

Válassza a **Standard** kódolási típust. Ez a típus azt jelenti, hogy élő kódolásra használható csatornát szeretne létrehozni. Ez azt jelenti, hogy a bejövő egyszeres sávszélességű streamet a rendszer elküldi a csatornára, majd az élő kódoló beállításai szerint többszörös sávszélességű streammé kódolja. További információk: [Live streaming using Azure Media Services to create multi-bitrate streams](media-services-manage-live-encoder-enabled-channels.md) (Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében).

![standard0][standard0]

A **Standard** kódolótípus esetében a következő betöltési protokollok választhatók:

- Single bitrate Fragmented MP4 (Egyszeres sávszélességű, fragmentált MP4) (Smooth Streaming)
- Single bitrate RTMP (Egyszeres sávszélességű RTMP)
- RTP (MPEG-TS): MPEG-2 Transport Stream over RTP (MPEG-2 adatátviteli stream RTP fölött)

A protokollok részletesebb leírását lásd: [Live streaming using Azure Media Services to create multi-bitrate streams](media-services-manage-live-encoder-enabled-channels.md) (Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében).

![standard1][standard1]

Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.  

Az **Advertising Configuration** (Reklámkonfiguráció) lapon adhatja meg a hirdetésjelölő jelek forrását. A portál használata esetén kizárólag az API lehetőséget választhatja, ami azt jelzi, hogy a csatorna élő kódolója egy aszinkron hirdetésjelölő API-t figyel. A portál használata esetén csak az API lehetőséget választhatja.

További információk: [Live streaming using Azure Media Services to create multi-bitrate streams](media-services-manage-live-encoder-enabled-channels.md) (Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében).

![standard2][standard2]

Az **Encoding Preset** (Kódolási beállításkészlet) lapon adhatja meg a rendszer beállításkészletét. Jelenleg kizárólag a **Default 720p** (Alapértelmezett 720p) rendszerbeállítás-készletet választhatja.

![standard3][standard3]

A **Channel Creation** (Csatorna létrehozása) lapon adhatja meg azokat az IP-címeket, amelyek jogosultak videókat közzétenni a csatornára. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk segítségével (például „10.0.0.1/22”), vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.

Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.


![standard4][standard4]

>[AZURE.NOTE] A csatorna indítása jelenleg akár 30 percet is igénybe vehet. A csatorna újraindítása legfeljebb 5 percbe telik.

A csatorna létrehozását követően válassza az **ENCODER** (Kódoló) lapot, ahol megtekintheti a csatornakonfigurációkat. Itt kezelheti a hirdetéseket és a befutókat is. 

![standard5][standard5]

További információk: [Live streaming using Azure Media Services to create multi-bitrate streams](media-services-manage-live-encoder-enabled-channels.md) (Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében).


##A betöltési URL-címek beolvasása

A csatorna létrehozása után beolvashatja a betöltési URL-címeket. Ezeket kell megadnia az élő kódolónak. A kódoló ezekre az URL-címekre küldi a bemeneti élő streamet.

![elkészült csatorna](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ready-channel.png)


![betöltési URL-címek](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##Program létrehozása és kezelése

###Áttekintés

A csatornákhoz programok vannak társítva. Ezek lehetővé teszik az élő stream szegmenseinek közzétételét és tárolását. A programokat a csatornák kezelik. A csatornák és programok viszonya hasonló a hagyományos televíziózáshoz, ahol a csatornák folyamatosan közvetítik a különböző tartalmakat, amelyek adott időtartamon át tartó részeit programoknak nevezzük.

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni a program felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Az archiválási időtartam határozza meg azt is, hogy mennyi idővel ugorhatnak vissza az ügyfelek az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program egy objektumhoz van társítva. A program közzétételéhez létre kell hoznia egy OnDemand-lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A csatornák három egyidejűleg zajló programot támogatnak, így egy bejövő streamből több archívumot is létre lehet hozni. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

A meglévő programokat nem szabad új eseményekhez ismét felhasználni. Ehelyett inkább hozzon létre új programokat az új eseményekhez.

Amikor készen áll a streamelésre és az archiválásra, indítsa el a programot. Amikor le kívánja állítani az esemény streamelését és az archiválását, állítsa le a programot. 

Az archivált tartalmak törléséhez állítsa le és törölje a programot, majd törölje a hozzá társított objektumot. Az objektum nem törölhető, ha a program még használja. Először törölje a programot. 

A program leállítását, majd törlését követően a felhasználók igény szerint lekért videóként képesek lesznek streamelni az archivált tartalmakat, egészen addig, amíg nem törli az objektumot.

Ha szeretné megtartani az archivált tartalmakat, de nem szeretné elérhetővé tenni őket streamelésre, törölje a streamelési lokátort.

###Programok létrehozása/indítása/leállítása

Ha elvégezte a stream és a csatorna összekapcsolását, elindíthatja a streamelési eseményt. Ehhez létre kell hoznia egy objektumot, egy programot és egy streamelési lokátort. Ezzel archiválja a streamet, és a streamvégponton keresztül elérhetővé teszi a nézők számára. 

Az esemény két különböző módon indítható el: 

1. Új program hozzáadásához kattintson a **CHANNEL** (Csatorna) lap  **ADD** (Hozzáadás) elemére.

    Adja meg a program nevét, az objektum nevét, az archiválás időtartamát és a titkosítás módját.
    
    ![program létrehozása](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    Ha nem törli a **Publish this program now** (Program azonnali közzététele) lehetőség bejelölését, a rendszer létrehozza a programot, és a közzétételi URL-címeket.
    
    Ha készen áll a program streamelésére, kattintson a **START** gombra.

    A program elindítását követően a PLAY (Lejátszás) gombot megnyomva elindíthatja a tartalmak lejátszását.


    ![létrehozott program](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-created-program.png)

2. Alternatív megoldásként gyorsabb utat is választhat: nyomja meg a **CHANNEL** (Csatorna) lap **START STREAMING** (Stream indítása) gombját. Ekkor létrejön az objektum, a program és a streamelési lokátor.

    A programot a rendszer alapértelmezés szerint DefaultProgram néven hozza létre, 1 órás archiválási időtartammal.

    A közzétett programot a CHANNEL (Csatorna) lapon játszhatja le. 

    ![csatorna közzététele](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-channel-play.png)


Az alapértelmezett program leállításához és törléséhez kattintson a **CHANNEL** (Csatorna) lap **STOP STREAMING** (Stream leállítása) gombjára. Az objektumot ezzel nem törli, az a **CONTENT** (Tartalom) lapon továbbra is közzétehető, illetve innen szüntethető meg közzététele.

A **CONTENT** (Tartalom) lapra lépve megtekintheti a programokhoz létrehozott objektumokat.

![tartalmak objektumai](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-content-assets.png)


##Tartalmak lejátszása

Ahhoz, hogy a felhasználók rendelkezésére bocsáthassa a tartalmak streamelésére használható URL-címet, először közzé kell tennie az objektumot (ennek folyamatát megtalálja az előzőekben) egy lokátor segítségével (amikor a portálon keresztül teszi közzé az objektumot, a rendszer létrehozza a lokátorokat). A keresők biztosítják az adategységben található fájlokhoz való hozzáférést. 

A tartalmak lejátszásához használt streamelési protokoll típusától függően előfordulhat, hogy módosítania kell a csatorna/program **PUBLISH URL** (Közzétételi URL-cím) hivatkozására kattintva lekérhető URL-címet.

Az élő streamnek a megfelelő protokollba való becsomagolását a dinamikus csomagolás funkció végzi el. 

A streamelési URL-cím alapértelmezés szerint a következő formátumot kapja. Ez az URL-cím Smooth Streaming-objektumok lejátszására használható:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS-streamelési URL-cím létrehozásához fűzze hozzá a következőt az URL-címhez: (format=m3u8-aapl).

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH-streamelési URL-cím létrehozásához fűzze hozzá a következőt az URL-címhez: (format=mpd-time-csf).

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

A tartalmak továbbításával kapcsolatos további információkért lásd: [Delivering content](media-services-deliver-content-overview.md) (Tartalmak továbbítása).

A Smooth Stream az [AMS-lejátszóval](http://amsplayer.azurewebsites.net/azuremediaplayer.html) is lejátszható. A HLS 3-as verziójának lejátszásához használjon iOS- vagy Android-eszközt.

##A fölöslegessé vált elemek eltávolítása

Ha befejezte az esemény streamelését, és törölni szeretné a korábban kiosztott erőforrásokat, kövesse az alábbi eljárást.

- Állítsa le a stream továbbítását a kódolóban.
- Állítsa le a csatornát. A csatorna leállítását követően nem számítunk fel további díjakat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
- A streamvégpontot is leállíthatja, kivéve, ha szeretné elérhetővé tenni az élő esemény archívumát igényalapú streamingre. A leállított állapotú csatornák után nem számítunk fel díjakat.
  

##Megfontolandó szempontok

- Jelenleg az élő események maximálisan ajánlott időtartama 8 óra. Ha egy ennél tovább futó csatornára van szüksége, lépjen velünk kapcsolatba az amslived@Microsoft.com e-mail címen.
- Biztosítsa, hogy legyen legalább egy, a folyamatos adatátvitelhez fenntartott egység a streamvégpontján, amelyről a tartalmat továbbítani kívánja.


##Media Services képzési tervek

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Visszajelzés küldése

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



[standard0]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard0.png
[standard1]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard1.png
[standard2]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard2.png
[standard3]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard3.png
[standard4]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard4.png
[standard5]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard_encode.png 


<!--HONumber=sep16_HO1-->


