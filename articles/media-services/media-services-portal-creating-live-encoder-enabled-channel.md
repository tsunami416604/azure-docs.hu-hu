---
title: "Élő adatfolyam-továbbítás az Azure Media Services segítségével, többszörös átviteli sebességű streamek Azure Portallal való létrehozásához | Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogy az Azure Portal használatával hogyan hozhat létre egy egyszeres átviteli sebességű élő streamet fogadó csatornát, amely többszörös átviteli sebességű streammé kódolja a fogadott bemenetet."
services: media-services
documentationcenter: 
author: anilmur
manager: SyntaxC4
editor: 
ms.assetid: 504f74c2-3103-42a0-897b-9ff52f279e23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: e764936afda8bd498f97a8dc3426136815c18a5a
ms.contentlocale: hu-hu
ms.lasthandoff: 01/13/2017

---
# <a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>Élő streamelés az Azure Media Services segítségével, többszörös átviteli sebességű streamek Azure Portallal való létrehozásához
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [REST API](https://docs.microsoft.com/rest/api/media/operations/channel)
> 
> 

Ez az oktatóanyag bemutatja, hogyan hozzon létre egy egyszeres sávszélességű élő streamet fogadó **csatornát**, amely többszörös sávszélességű streammé kódolja azt.

> [!NOTE]
> További elméleti információk a valós idejű kódolásra képes csatornákról: [Élő adatfolyam továbbítása az Azure Media Services használatával, és többféle sávszélességű adatfolyamok létrehozása](media-services-manage-live-encoder-enabled-channels.md)
> 
> 

## <a name="common-live-streaming-scenario"></a>Az élő streamelés egy gyakori alaphelyzete
A leggyakrabban használt streamelési alkalmazások kialakításához általában az alábbi lépések szükségesek.

> [!NOTE]
> Jelenleg az élő stream maximális javasolt időtartama 8 óra. Ha hosszabb időtartamon át szeretné működtetni a csatornát, forduljon „amslived”-hez a Microsoft.com oldalon.
> 
> 

1. Csatlakoztasson egy videokamerát a számítógéphez. Indítson el és állítson be egy helyszíni valós idejű kódolót, amely képes egy egyféle sávszélességű kimeneti adatfolyam továbbítására a következő protokollok valamelyikével: RTMP, Smooth Streaming vagy RTP (MPEG-TS). További tudnivalók: [Azure Media Services RMTP-támogatása és valós idejű kódolók](http://go.microsoft.com/fwlink/?LinkId=532824)
   
    Ezt a lépést a csatorna létrehozása után is elvégezheti.
2. Hozzon létre és indítson el egy csatornát. 
3. Kérje le a csatorna feldolgozó URL-címét. 
   
    Az élő kódoló a bemeneti URL-címet használva küldi el a streamet a csatornának.
4. Kérje le a csatorna előnézeti URL-címét. 
   
    Ezen az URL használatával ellenőrizheti, hogy a csatornája megfelelően fogadja-e az élő adatfolyamot.
5. Hozzon létre egy eseményt/programot (ezzel egy objektumot is létrehoz). 
6. Tegye közzé az eseményt (ezzel létrehozza a kapcsolódó objektumhoz tartozó OnDemand-lokátort is).    
7. Amikor készen áll a streamelésre és az archiválásra, indítsa el az eseményt.
8. Ha kívánja, a kódolólónak küldött jelzéssel hirdetést is elindíthat. A hirdetés bekerül a kimenő streambe.
9. Amikor le szeretné állítani az esemény streamelését és archiválását, állítsa le az eseményt.
10. Törölje az eseményt (és ha szeretné, törölje az objektumot is).   

## <a name="in-this-tutorial"></a>Az oktatóanyag tartalma
Ebben az oktatóanyagban az Azure Portallal a következő feladatokat végezzük el: 

1. Élő kódolásra alkalmas csatorna létrehozása
2. Betöltési URL-cím lekérése, majd átadása az élő kódolónak Az élő kódoló erre az URL-címre tölti be a streamet a csatornának.
3. Egy esemény/program (és egy objektum) létrehozása.
4. Az objektum közzététele és a streamelési URL-címek lekérése.  
5. Tartalom lejátszása
6. Tisztítás.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzésének a következők a feltételei.

* Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. 
  További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Egy Media Services-fiók szükséges. A Media Services-fiók létrehozásával kapcsolatban lásd: [Create Account](media-services-portal-create-account.md) (Fiók létrehozása).
* Egy webkamera és egy egyszeres sávszélességű élő stream továbbítására alkalmas kódoló.

## <a name="create-a-channel"></a>Csatorna létrehozása
1. Az [Azure-portálon](https://portal.azure.com/) kattintson a Media Services elemre, majd Media Services-fiókja nevére.
2. Válassza a **Live Streaming** (Élő adatfolyam) lehetőséget.
3. Válassza a **Custom create** (Egyéni létrehozás) lehetőséget. Ez a beállítás lehetővé teszi egy olyan csatorna létrehozását, amellyel használható a Live Encoding.
   
    ![Csatorna létrehozása](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
4. Kattintson a **Settings** (Beállítások) lehetőségre.
   
   1. Válassza ki a **Live Encoding**-csatorna típusát. Ez a típus azt jelenti, hogy élő kódolásra használható csatornát szeretne létrehozni. Ez azt jelenti, hogy a bejövő egyszeres sávszélességű streamet a rendszer elküldi a csatornára, majd az élő kódoló beállításai szerint többszörös sávszélességű streammé kódolja. További információk: [Live streaming using Azure Media Services to create multi-bitrate streams](media-services-manage-live-encoder-enabled-channels.md) (Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében). Kattintson az OK gombra.
   2. Adja meg a csatorna nevét.
   3. Kattintson a képernyő alján található OK gombra.
5. Kattintson az **Ingest** (Betöltés) fülre.
   
   1. Ezen a lapon választhat egy streamprotokollt. A **Live Encoding** csatornatípus esetén az érvényes protokollok a következők:
      
      * Single bitrate Fragmented MP4 (Egyszeres sávszélességű, fragmentált MP4) (Smooth Streaming)
      * Single bitrate RTMP (Egyszeres sávszélességű RTMP)
      * RTP (MPEG-TS): MPEG-2 Transport Stream over RTP (MPEG-2 adatátviteli stream RTP fölött)
        
        A protokollok részletesebb leírását lásd: [Live streaming using Azure Media Services to create multi-bitrate streams](media-services-manage-live-encoder-enabled-channels.md) (Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében).
        
        Ha a csatorna vagy a hozzá tartozó események/programok már elindultak, a protokollbeállítás nem módosítható. Ha eltérő protokollok használatára van szükség, hozzon létre külön-külön csatornákat az egyes streamprotokollokhoz.  
   2. Alkalmazhatja az IP-korlátozást a betöltésre. 
      
       Megadhatja azokat az IP-címeket, amelyek jogosultak videókat betölteni erre a csatornára. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk segítségével (például „10.0.0.1/22”), vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.
      
       Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.
6. A **Preview** (Előnézet) lapon alkalmazza az IP-korlátozást az előnézetre.
7. Az **Encoding** (Kódolás) lapon adja meg a kódolási előbeállítást. 
   
    Jelenleg kizárólag a **Default 720p** (Alapértelmezett 720p) rendszerbeállítás-készletet választhatja. Egyéni előbeállítás megadásához nyisson egy Microsoft támogatási jegyet. Ezt követően adja meg az előbeállítás Ön számára létrehozott nevét. 

> [!NOTE]
> A csatorna indítása jelenleg akár 30 percet is igénybe vehet. A csatorna újraindítása legfeljebb 5 percbe telik.
> 
> 

A csatorna létrehozását követően a csatornára kattintva, majd a **Settings** (Beállítások) lehetőséget kiválasztva megtekintheti a csatornakonfigurációkat. 

További információk: [Live streaming using Azure Media Services to create multi-bitrate streams](media-services-manage-live-encoder-enabled-channels.md) (Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében).

## <a name="get-ingest-urls"></a>A betöltési URL-címek beolvasása
A csatorna létrehozása után beolvashatja a betöltési URL-címeket. Ezeket kell megadnia az élő kódolónak. A kódoló ezekre az URL-címekre küldi a bemeneti élő streamet.

![betöltési URL-címek](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)

## <a name="create-and-manage-events"></a>Események létrehozása és kezelése
### <a name="overview"></a>Áttekintés
A csatornákhoz események/programok vannak társítva. Ezek lehetővé teszik az élő stream szegmenseinek közzétételét és tárolását. Az eseményeket/programokat a csatornák kezelik. A csatornák és programok viszonya hasonló a hagyományos televíziózáshoz, ahol a csatornák folyamatosan közvetítik a különböző tartalmakat, amelyek adott időtartamon át tartó részeit programoknak nevezzük.

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni az esemény felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Az archiválási időtartam határozza meg azt is, hogy mennyi idővel ugorhatnak vissza az ügyfelek az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan eldobja azt a tartalmat, amely korábbi a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden esemény egy objektumhoz van társítva. Az esemény közzétételéhez létre kell hoznia egy OnDemand lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

Egy csatorna három egyidejűleg zajló esemény támogat, hogy több archívumot lehessen létrehozni ugyanabból a bejövő streamből. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Tegyük fel, hogy az üzleti igény szerint például 6 órát kell archiválni egy eseményből, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló esemény létrehozásával érheti el. Állítsa be az egyik eseményt az esemény 6 órájának archiválására, de ne tegye közzé a programot. A másik eseményt 10 perc archiválására állítsa be, és tegye is közzé a programot.

A meglévő programokat nem szabad új eseményekhez ismét felhasználni. Ehelyett inkább hozzon létre új programokat az új eseményekhez.

Amikor készen áll a streamelésre és az archiválásra, indítson el egy eseményt/programot. Amikor le szeretné állítani az esemény streamelését és archiválását, állítsa le az eseményt. 

Az archivált tartalom törléséhez állítsa le és törölje az eseményt, majd törölje a hozzá társított objektumot. Az esemény által használt objektumok nem törölhetők. Először az eseményt kell törölni. 

Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli.

Ha szeretné megtartani az archivált tartalmakat, de nem szeretné elérhetővé tenni őket streamelésre, törölje a streamelési lokátort.

### <a name="createstartstop-events"></a>Események létrehozása/indítása/leállítása
Ha elvégezte a stream és a csatorna összekapcsolását, elindíthatja a streamelési eseményt. Ehhez létre kell hoznia egy objektumot, egy programot és egy streamelési lokátort. Ezzel archiválja a streamet, és a streamvégponton keresztül elérhetővé teszi a nézők számára. 

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

Az esemény két különböző módon indítható el: 

1. Új esemény hozzáadásához kattintson a **Channel** (Csatorna) oldal **Live Event** (Élő esemény) elemére.
   
    Adja meg az esemény nevét, az objektum nevét, az archiválás időtartamát és a titkosítási beállítást.
   
    ![program létrehozása](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
   
    Ha nem törli a jelet a **Publish this live event now** (Program azonnali közzététele) lehetőség mellől, a rendszer létrehozza az eseményt és a közzétételi URL-címeket.
   
    Ha készen áll az esemény streamelésére, kattintson a **Start** (Indítás) gombra.
   
    Az esemény elindítását követően a **Watch** (Megtekintés) gombra kattintva elindíthatja a tartalom lejátszását.
2. Alternatív megoldásként gyorsabb utat is választhat: kattintson a **Channel** (Csatorna) oldal **Go Live** (Élő közvetítés indítása) gombjára. Ekkor létrejön az alapértelmezett objektum, program és streamelési lokátor.
   
    Az eseményt a rendszer **default** néven hozza létre, és 8 órás archiválási időtartamot ad meg hozzá.

A közzétett eseményt a **Live event** (Élő esemény) oldalon tekintheti meg. 

Az **Off Air** (Közvetítés leállítása) gombbal az összes élő eseményt leállíthatja. 

## <a name="watch-the-event"></a>Esemény megtekintése
Ha meg szeretne tekinteni egy eseményt, kattintson az Azure Portal **Watch** (Megtekintés) elemére. 

![Létrehozva](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

A leállítása után az élő esemény automatikusan átalakítja az eseményeket igény szerinti tartalommá.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Ha befejezte az esemény streamelését, és törölni szeretné a korábban kiosztott erőforrásokat, kövesse az alábbi eljárást.

* Állítsa le a stream továbbítását a kódolóban.
* Állítsa le a csatornát. A csatorna leállítását követően nem számítunk fel további díjakat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
* A streamvégpontot is leállíthatja, kivéve, ha szeretné elérhetővé tenni az élő esemény archívumát igényalapú streamingre. A leállított állapotú csatornák után nem számítunk fel díjakat.

## <a name="view-archived-content"></a>Archivált tartalom megtekintése
Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli. Olyan objektumot nem lehet törölni, amelyet használ egy esemény. Először az eseményt kell törölni. 

Az objektumok kezeléséhez válassza a **Setting** (Beállítás) elemet, majd kattintson az **Assets** (Objektumok) elemre.

![Objektumok](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

## <a name="considerations"></a>Megfontolandó szempontok
* Jelenleg az élő események maximálisan ajánlott időtartama 8 óra. Ha egy ennél tovább futó csatornára van szüksége, lépjen velünk kapcsolatba az amslived@Microsoft.com e-mail címen.
* Győződjön meg arról, hogy a tartalomstreameléshez használt streamvégpont **Fut** állapotban van.

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


