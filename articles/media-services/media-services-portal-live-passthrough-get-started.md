<properties 
    pageTitle="Élő stream továbbítása helyszíni kódolókkal az Azure portál használatával | Microsoft Azure" 
    description="Ez az ismertető végigkalauzolja egy olyan csatorna létrehozásának folyamatán, amely átmenő közvetítésre van konfigurálva." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="08/30/2016" 
    ms.author="juliako"/>


#Élő stream továbbítása helyszíni kódolókkal az Azure portál használatával

Ez az ismertető végigkalauzolja egy olyan **csatorna** létrehozásának folyamatán, amely átmenő közvetítésre van konfigurálva az Azure Portalon. 


##Előfeltételek

Az ismertetett eljárás végrehajtásához a következők szükségesek:

- Egy Azure-fiók. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. A Media Services-fiók létrehozásáról a [Media Services-fiók létrehozása](media-services-create-account.md) című cikk nyújt tájékoztatást.
- Egy webkamera. Például a [Telestream Wirecast kódoló](http://www.telestream.net/wirecast/overview.htm).

Kifejezetten ajánljuk, hogy olvassa el a következő cikkeket:

- [Azure Media Services RTMP Support and Live Encoders (Az Azure Media Services RTMP-támogatása és az élő kódolók)](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Overview of Live Steaming using Azure Media Services (Az Azure Media Services segítségével történő élő streamelés áttekintése)](media-services-manage-channels-overview.md)
- [Live streaming with on-premise encoders that create multi-bitrate streams (Élő stream továbbítása többszörös sávszélességű streamet létrehozó helyszíni kódolókkal) ](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>Az élő streamelés egy gyakori alaphelyzete

A következő lépések ismertetik, hogy milyen lépésekkel lehet olyan streamelő alkalmazásokat létrehozni, amelyek átmenő közvetítésre vannak konfigurálva. Ez az oktatóprogram bemutatja, hogyan hozhat létre és kezelhet átmenő csatornát és élő eseményeket.

1. Csatlakoztasson a számítógéphez egy videokamerát. Indítson el és konfiguráljon egy élő helyszíni kódolót, amely többszörös sávszélességű RTMP- vagy fragmentált MP4-streamet állít elő. További tájékoztatást az [Azure Media Services RTMP Support and Live Encoders](http://go.microsoft.com/fwlink/?LinkId=532824) (Az Azure Media Services RTMP-támogatása és az élő kódolók) című cikk nyújt.
    
    Ezt a lépést a csatorna létrehozása után is el lehet végezni.

1. Hozzon létre és indítson el egy átmenő csatornát.
1. Kérje le a Channel ingest URL (Csatorna betöltési URL-címe) értékét. 

    Az élő kódoló a bemeneti URL-címet használva küldi el a streamet a csatornának.
1. Kérje le a csatorna előnézeti URL-címét. 

    Ezen az URL használatával ellenőrizheti, hogy a csatornája megfelelően fogadja-e az élő adatfolyamot.

3. Hozzon létre egy élő eseményt/programot. 

    Az Azure portál használata esetén az élő esemény létrehozása egy objektumot is létrehoz. 
      
    >[AZURE.NOTE]Azon a streamvégponton, amelyről a tartalmakat streamelni kívánja, legalább egy streameléshez fenntartott egységnek rendelkezésre kell állnia.
1. Amikor készen áll a streamelésre és az archiválásra, indítsa el az eseményt/programot.
2. Ha kívánja, a kódolólónak küldött jelzéssel hirdetést is elindíthat. A hirdetés bekerül a kimenő streambe.
1. Amikor le kívánja állítani az esemény streamelését és archiválását, állítsa le az eseményt/programot.
1. Törölje az eseményt/programot (és ha kívánja, törölje az objektumot).     

>[AZURE.IMPORTANT] A [Live streaming with on-premise encoders that create multi-bitrate streams](media-services-live-streaming-with-onprem-encoders.md) (Élő adatfolyam továbbítása többszörös sávszélességű streamet létrehozó helyszíni kódolókkal) című cikkből tájékozódhat helyszíni kódolókkal és átmenő csatornákkal történő élő streamelés fogalmairól és fontos szempontjairól.

##Az értesítések és a hibák megtekintése

Ha meg szeretné tekinteni az Azure portál által előállított értesítéseket és hibaüzeneteket, kattintson az Értesítés ikonra.

![Értesítések](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##Streamvégpontok konfigurálása 

A Media Services dinamikus becsomagolást biztosít, aminek köszönhetően anélkül lehet MPEG DASH, HLS, Smooth Streaming illetve HDS formátumban közvetíteni többszörös sávszélességű MP4-streameket, hogy át kellene őket csomagolni ezekbe a streamformátumokba. A dinamikus becsomagolás révén elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

A dinamikus becsomagolás előnyének kihasználásához léteznie kell legalább egy streamelési egységnek annál a streamvégpontnál, amely a tervek szerint közvetíteni fogja a tartalmat.  

Streameléshez fenntartott egységek létrehozásához és számának megváltoztatásához tegye a következőket:

1. Kattintson a **Settings** (Beállítások) ablak **Streaming endpoints** (Streamvégpontok) elemére. 

2. Kattintson az alapértelmezett streamvégpontra. 

    Megjelenik a **DEFAULT STREAMING ENDPOINT DETAILS** (Alapértelmezett streamvégpont adatai) ablak.

3. Adja meg a streamelési egységek számát a **Streaming units** (Streamelési egységek) csúszka mozgatásával.

    ![Streamelési egységek](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Mentse a módosításokat a **Save** (Mentés) gombra kattintva.

    >[AZURE.NOTE]Az új egységek allokációja akár 20 percig is eltarthat.
    
##Átmenő csatornák és események létrehozása és elindítása.

A csatornákhoz események/programok vannak társítva. Ezek lehetővé teszik az élő stream szegmenseinek közzétételét és tárolását. Az eseményeket a csatornák kezelik. 
    
Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni a program felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Az archiválási időtartam határozza meg azt is, hogy mennyi idővel ugorhatnak vissza az ügyfelek az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan eldobja azt a tartalmat, amely korábbi a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden esemény egy objektumhoz van társítva. Az esemény közzétételéhez létre kell hoznia egy OnDemand lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

Egy csatorna három egyidejűleg zajló esemény támogat, hogy több archívumot lehessen létrehozni ugyanabból a bejövő streamből. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

A meglévő élő eseményeket nem szabad újra felhasználni. Ehelyett hozzon létre egy új eseményt minden eseményhez, és indítsa el.

Amikor készen áll a streamelésre és az archiválásra, indítsa el az eseményt. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt. 

Az archivált tartalom törléséhez állítsa le és törölje az eseményt, majd törölje a hozzá társított objektumot. Olyan objektumot nem lehet törölni, amelyet használ egy esemény. Először az eseményt kell törölni. 

Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli.

Ha szeretné megtartani az archivált tartalmakat, de nem szeretné elérhetővé tenni őket streamelésre, törölje a streamelési lokátort.

###Csatorna létrehozása a portállal 

Ez a szakasz azt mutatja be, hogyan lehet átmenő csatornát létrehozni a **Gyorslétrehozás** lehetőséggel.

Az átmenő csatornákról a [Live streaming with on-premise encoders that create multi-bitrate streams](media-services-live-streaming-with-onprem-encoders.md) (Élő stream továbbítása többszörös sávszélességű streamet létrehozó helyszíni kódolókkal) című cikk tartalmaz részletes tájékoztatást.

1. Kattintson a **Settings** (Beállítások) ablak **Live streaming** (Élő stream) elemére. 

    ![Bevezetés](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
    
    Megjelenik a **Live streaming** (Élő stream) ablak.

3. A **Quick Create** (Gyorslétrehozás) gombra kattintva létrehozhat egy az RTMP betöltési protokollt használó átmenő csatornát.

    Megjelenik a **CREATE A NEW CHANNEL** (Új csatorna létrehozása) ablak.
4. Nevezze el az új csatornát, és kattintson a **Create** (Létrehozás) gombra. 

    Ennek hatására létrejön egy az RTMP betöltési protokollt használó csatorna.

    A csatorna ezenkívül hozzáad, elindít és közzétesz egy alapértelmezett élő eseményt/programot. Az esemény 8 órás archiválási időtartamra van konfigurálva. 

    További események hozzáadásához nyomja meg az **Live Event** (Élő esemény) gombot.

##A betöltési URL-címek beolvasása

A csatorna létrehozása után beolvashatja a betöltési URL-címeket. Ezeket kell megadnia az élő kódolónak. A kódoló ezekre az URL-címekre küldi a bemeneti élő streamet.

![Létrehozva](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

##Esemény megtekintése

Ha meg szeretne tekinteni egy eseményt, kattintson az Azure Portal **Watch** (Megtekintés) elemére. 
 
![Létrehozva](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Leállítása után az élő esemény automatikusan átalakul igény szerinti tartalommá.

##A fölöslegessé vált elemek eltávolítása

Az átmenő csatornákról a [Live streaming with on-premise encoders that create multi-bitrate streams](media-services-live-streaming-with-onprem-encoders.md) (Élő stream továbbítása többszörös sávszélességű streamet létrehozó helyszíni kódolókkal) című cikk tartalmaz részletes tájékoztatást.

- Egy csatornát csak akkor lehet  leállítani, ha már leállítottak minden, a csatornán közvetített eseményt/programot.  A csatorna leállítását követően nem számítunk fel további díjakat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
- Egy csatornát csak akkor lehet törölni, ha töröltek minden, a csatornán közvetített élő eseményt.

##Archivált tartalom megtekintése

Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli. Olyan objektumot nem lehet törölni, amelyet használ egy esemény. Először az eseményt kell törölni. 

Az objektumok kezeléséhez válassza a  **Setting** (Beállítás) elemet, majd kattintson az **Assets** (Objektumok) elemet.

![Objektumok](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

##A Media Services tanulási útvonalai

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Visszajelzés küldése

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!--HONumber=sep16_HO1-->


