---
title: Használati statisztikák elemzése az Azure CDN speciális HTTP-jelentésekkel | Microsoft dokumentumok
description: Megtudhatja, hogy miként hozhat létre speciális HTTP-jelentéseket a Microsoft Azure CDN-ben. Ezek a jelentések részletes információkat nyújtanak a CDN-tevékenységről.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 0b0eec2425f8a1663eb7a09c83a6bad037d1d79c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594109"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Használati statisztikák elemzése az Azure CDN speciális HTTP-jelentésekkel
## <a name="overview"></a>Áttekintés
Ez a dokumentum a Microsoft Azure CDN speciális HTTP-jelentéskészítési funkcióit ismerteti. Ezek a jelentések részletes információkat nyújtanak a CDN-tevékenységről.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Speciális HTTP-jelentések elérése
1. A CDN-profilpanelen kattintson a **Kezelés** gombra.
   
    ![CDN profilpanel kezelőgombja](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Mutasson az **Analytics** fülre, majd mutasson a **Speciális HTTP-jelentések** úszó panelre.  Kattintson a **HTTP Nagy Platform**.
   
    ![CDN felügyeleti portál - Speciális jelentések menü](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Megjelennek a jelentésbeállításai.

## <a name="geography-reports-map-based"></a>Földrajzi jelentések (térképalapú)
Öt jelentés létezik, amelyek kihasználják a térképet, amely jelzi azon régiókat, amelyektől a tartalmat kérik. Ezek a jelentések a Világtérkép, az Egyesült Államok térképe, a Kanada térképe, az Európa térkép és az Ázsia-csendes-óceáni térkép.

Minden térképalapú jelentés földrajzi entitásokat rangsorol (azaz országokat/régiókat, egy térképet, amely segít megvizelni azokat a helyeket, ahonnan a tartalmat kérik. Ezt úgy tudja megtenni, hogy az egyes régiókat az adott régióban tapasztalt kereslet nek megfelelően színkódolja. A világosabb árnyékolt területek alacsonyabb igényt jeleznek a tartalom iránt, míg a sötétebb területek a tartalom iránti nagyobb keresletet.

Az egyes régiók részletes forgalmi és sávszélesség-információi közvetlenül a térkép alatt találhatók. Ez lehetővé teszi a találatok teljes számának, a lekérések százalékos arányának, az átvitt adatok teljes mennyiségének (gigabájtban) és az egyes régiókra átvitt adatok százalékos arányának megtekintését. Tekintse meg az egyes mutatók leírását. Végül, ha az egérmutatót egy régió (azaz ország/régió, állam vagy tartomány) fölé viszi, a régióban történt találatok neve és százalékos aránya elemleírásként jelenik meg.

Az alábbiakban rövid leírást talál a térképalapú földrajzi jelentések egyes típusairól.

| Jelentés neve | Leírás |
| --- | --- |
| Világtérkép |Ez a jelentés lehetővé teszi a CDN-tartalom iránti globális igény megtekintését. Minden ország/régió színkóddal van eltüntetve a világtérképen, amely az adott régióból származó találatok százalékos arányát jelzi. |
| Egyesült Államok Térkép |Ez a jelentés lehetővé teszi a CDN-tartalom iránti igény megtekintését az Egyesült Államokban. Minden állapot színkóddal van eltüntetve ezen a térképen, amely az adott régióból származó találatok százalékos arányát jelzi. |
| Kanada Térkép |Ez a jelentés lehetővé teszi, hogy megtekinthesse a CDN-tartalom iránti keresletet Kanadában. Minden tartomány színkóddal van eltüntetve ezen a térképen, amely az adott régióból származó találatok százalékos arányát jelzi. |
| Európa Térkép |Ez a jelentés lehetővé teszi, hogy megtekinthesse a CDN-tartalom iránti igényt Európában. Minden ország/régió színkóddal van eltüntetve ezen a térképen, amely az adott régióból származó találatok százalékos arányát jelzi. |
| Ázsia és a csendes-óceáni térség térképe |Ez a jelentés lehetővé teszi, hogy megtekinthesse a CDN-tartalom iránti igényt Ázsiában. Minden ország/régió színkóddal van eltüntetve ezen a térképen, amely az adott régióból származó találatok százalékos arányát jelzi. |

## <a name="geography-reports-bar-charts"></a>Földrajzi jelentések (sávdiagramok)
Két további jelentés létezik, amelyek földrajzi hely szerint statisztikai adatokat szolgáltatnak, amelyek a legnépszerűbb városok és a legnépszerűbb országok. Ezek a jelentések a városokat, illetve az országokat/régiókat az adott országokból/régiókból származó találatok száma alapján rangsorolják. Az ilyen típusú jelentések létrehozásakor egy sávdiagram jelzi azt a 10 várost vagy országot/régiót, amely egy adott platformon kért tartalmat. Ez a sávdiagram lehetővé teszi, hogy gyorsan felmérje azokat a régiókat, amelyek a legtöbb kérelmet generálják a tartalomhoz.

A diagram bal oldala (y tengely) azt jelzi, hogy hány találat történt a megadott régióban. Közvetlenül a grafikon (x tengely) alatt található egy címke a 10 legnépszerűbb terület mindegyikéhez.

### <a name="using-the-bar-charts"></a>Sávdiagramok használata
* Ha az egérmutatót egy sáv fölé viszi, a régióban történt találatok neve és teljes száma elemleírásként jelenik meg.
* A Legnépszerűbb városok jelentés elemleírása a város nevét, állapotát/tartományát és ország/régió rövidítése alapján azonosítja a várost.
* Ha az a város vagy régió (azaz állam/megye), ahonnan a kérelem származik, nem határozható meg, akkor azt jelzi, hogy ismeretlenek. Ha az ország/régió ismeretlen, akkor két kérdőjel (azaz ??) jelenik meg.
* A jelentés "Európa" vagy "Ázsia/csendes-óceáni régió" mutatóit is tartalmazhatja. Ezek a tételek nem célja, hogy statisztikai adatokat az összes IP-címek ezekben a régiókban. Ehelyett csak olyan IP-címekről származó kérelmekre vonatkoznak, amelyek Európában vagy Ázsiában/csendes-óceáni térségben vannak elosztva, nem pedig egy adott városra vagy országra/régióra.

A sávdiagram létrehozásához használt adatok alatta tekinthetők meg. Itt megtalálja a találatok teljes számát, a találatok százalékos arányát, az átvitt adatok mennyiségét (gigabájtban), valamint az első 250 régióra átvitt adatok százalékos arányát. Tekintse meg az egyes mutatók leírását.

Az alábbiakban mindkét típusú jelentéshez rövid leírást talál.

| Jelentés neve | Leírás |
| --- | --- |
| Legnépszerűbb városok |Ez a jelentés a városokat az adott régióból származó találatok száma alapján rangsorolja. |
| Top országok |Ez a jelentés az országokat/régiókat az adott országból/régióból származó találatok száma szerint rangsorolja. |

## <a name="daily-summary"></a>Napi összegzés
A Napi összegzés jelentés lehetővé teszi, hogy naponta megtekinthesse az adott platformon továbbított találatok és adatok teljes számát. Ez az információ a CDN-tevékenységi minták gyors felismerésére használható. Ez a jelentés például segíthet annak észlelésén, hogy mely napokon tapasztalt a vártnál magasabb vagy alacsonyabb forgalom.

Az ilyen típusú jelentés létrehozásakor egy sávdiagram vizuálisan jelzi a jelentés által lefedett időszakban napi szinten tapasztalt platformspecifikus kereslet mennyiségét. Ezt úgy fogja megtenni, hogy a jelentés minden egyes napjára vonatkozóan megjelenít egy sávot. Ha például a "Múlt hét" nevű időszakot választja, akkor egy hét sávból álló sávdiagramjön létre. Minden sáv jelzi az adott napon tapasztalt találatok teljes számát.

A diagram bal oldala (y tengely) azt jelzi, hogy hány találat történt a megadott napon. Közvetlenül a grafikon (x tengely) alatt talál egy címkét, amely a jelentésben szereplő minden nap dátumát (Formátum: ÉÉÉÉ-HH-DD) jelzi.

> [!TIP]
> Ha az egérmutatót egy sáv fölé viszi, az adott napon történt találatok teljes száma elemleírásként jelenik meg.
> 
> 

A sávdiagram létrehozásához használt adatok alatta tekinthetők meg. Itt megtalálja a találatok teljes számát és az átvitt adatok mennyiségét (gigabájtban) a jelentés által lefedett minden egyes napra vonatkozóan.

## <a name="by-hour"></a>Óra szerint
A By Hour jelentés lehetővé teszi, hogy óránként tekintse meg az adott platformon továbbított találatok és adatok teljes számát. Ez az információ a CDN-tevékenységi minták gyors felismerésére használható. Ez a jelentés például segíthet a nap során a vártnál magasabb vagy alacsonyabb forgalmat tapasztaló időszakok észlelésén.

Az ilyen típusú jelentés létrehozásakor a sávdiagram vizuálisan jelzi, hogy a jelentés által lefedett időszakban óránként mekkora platformspecifikus keresletet tapasztalt. Ezt úgy fogja megtenni, hogy a jelentés által lefedett minden egyes órára vonatkozó sávot jelenít meg. Ha például egy 24 órás időszakot választ, akkor egy 24 sávból származó sávdiagramjön létre. Minden sáv jelzi az adott óra során tapasztalt találatok teljes számát.

A diagram bal oldala (y tengely) azt jelzi, hogy hány találat történt a megadott órában. Közvetlenül a grafikon (x tengely) alatt talál egy címkét, amely a jelentésben szereplő minden egyes órára vonatkozódátumot/időpontot (Formátum: ÉÉÉÉ-HH-Hh:mm) jelzi. Az idő 24 órás formátumban van jelentve, és az UTC/GMT időzónával van megadva.

> [!TIP]
> Ha az egérmutatót egy sáv fölé viszi, az adott órában történt találatok teljes száma elemleírásként jelenik meg.
> 
> 

A sávdiagram létrehozásához használt adatok alatta tekinthetők meg. Itt megtalálja a találatok teljes számát és az átvitt adatok mennyiségét (gigabájtban) a jelentés által lefedett minden egyes órára vonatkozóan.

## <a name="by-file"></a>Fájl szerint
A By File jelentés lehetővé teszi, hogy az igény és a forgalom egy adott platformon a legkeresettebb eszközök mennyisége. Az ilyen típusú jelentések létrehozásakor a rendszer sávdiagramot hoz létre a 10 legkeresettebb eszközön a megadott időszakban.

> [!NOTE]
> A jelentés alkalmazásában a szegélyes CNAME URL-ek egyenértékű CDN URL-címekké alakulnak át. Ez lehetővé teszi az eszközhöz társított találatok teljes számának pontos megfeleltetését, függetlenül a ttól, hogy a CDN vagy a széléli CNAME URL-cím a kérelemhez használt.This allows a accurate tally for the total number of hits associated with an asset regardless of the CDN or edge CNAME URL used to request it.
> 
> 

A grafikon bal oldala (y tengely) azt jelzi, hogy a megadott időszakban hány kérelem érkezett az egyes eszközökre. Közvetlenül a grafikon (x-tengely) alatt talál egy címkét, amely a 10 legkeresettebb eszköz mindegyikének fájlnevét jelzi.

A sávdiagram létrehozásához használt adatok alatta tekinthetők meg. Itt megtalálja a következő információkat az első 250 kért eszköz mindegyikéről: relatív elérési út, a találatok teljes száma, a találatok százalékos aránya, az átvitt adatok mennyisége (gigabájtban) és az átvitt adatok százalékos aránya.

## <a name="by-file-detail"></a>Fájl részletei szerint
A Fájl részletei jelentés lehetővé teszi, hogy megtekinthesse az igény összegét és az adott platformon egy adott eszközön felmerülő forgalmat. A jelentés legtetején található a Fájl részletei beállítás. Ez a beállítás a kiválasztott platformon a legkeresettebb eszközök listáját tartalmazza. A Fájlrészletek jelentés létrehozásához ki kell választania a kívánt eszközt a Fájl részletei beállításból. Ezt követően a sávdiagram jelzi a megadott időszakban generált napi igény mennyiségét.

A diagram bal oldala (y-tengely) azt jelzi, hogy egy eszköz egy adott napon összesen hány kérelmet tapasztalt. Közvetlenül a grafikon (x tengely) alatt talál egy címkét, amely jelzi azt a dátumot (Formátum: YYYY-MM-DD), amelyre az eszköz CDN-igényét jelentették.

A sávdiagram létrehozásához használt adatok alatta tekinthetők meg. Itt megtalálja a találatok teljes számát és az átvitt adatok mennyiségét (gigabájtban) a jelentés által lefedett minden egyes napra vonatkozóan.

## <a name="by-file-type"></a>Fájltípus szerint
A Fájltípus szerint jelentés lehetővé teszi az igény és a fájltípus által érintett forgalom megtekintését. Az ilyen típusú jelentések létrehozásakor egy fánkdiagram jelzi a találatok százalékos arányát, amelyet a top 10 fájltípus generál.

> [!TIP]
> Ha az egérmutatót a fánkdiagram egy szelete fölé viszi, az adott fájltípus internetes médiatípusa elemleírásként jelenik meg.
> 
> 

A fánkdiagram létrehozásához használt adatok alatta tekinthetők meg. Itt megtalálja a fájlnév kiterjesztést/ internetes médiatípust, a találatok teljes számát, a találatok százalékos arányát, az átvitt adatok mennyiségét (gigabájtban), valamint az átvitt adatok százalékos arányát az első 250 fájltípus esetében.

## <a name="by-directory"></a>Könyvtár szerint
A Címtár jelentés lehetővé teszi, hogy megtekinthesse az igény mennyiségét és a forgalom egy adott platformon keresztül egy adott könyvtár tartalmát. Az ilyen típusú jelentések létrehozásakor egy sávdiagram jelzi a top 10 könyvtárak ban lévő tartalom által generált találatok teljes számát.

### <a name="using-the-bar-chart"></a>Sávdiagram használata
* Mutasson egy sávra a megfelelő könyvtár relatív elérési útjának megtekintéséhez.
* A könyvtár almappájában tárolt tartalom nem számít a címtár szerinti igény számításakor. Ez a számítás kizárólag a tényleges könyvtárban tárolt tartalomra vonatkozóan generált kérelmek számán alapul.
* A jelentés alkalmazásában a szegélyes CNAME URL-ek egyenértékű CDN URL-címekké alakulnak át. Ez lehetővé teszi az eszközhöz társított összes statisztiká pontos megfeleltetését, függetlenül a ttól, hogy a CDN vagy a peremhálózati CNAME URL-cím a kérelemhez használt.This allows a accurate tally for all statistics associated with an asset regardless of the CDN or edge CNAME URL used to request it.

A diagram bal oldala (y-tengely) a 10 legfontosabb könyvtárakban tárolt tartalomra vonatkozó kérelmek teljes számát jelzi. A diagram minden sávja egy könyvtárat jelöl. A színkódolási séma segítségével egy sávot egyezhet a Top 250 teljes könyvtárak részben felsorolt könyvtárral.

A sávdiagram létrehozásához használt adatok alatta tekinthetők meg. Itt megtalálja a következő információkat az első 250 könyvtár mindegyikéhez: relatív elérési út, a találatok teljes száma, a találatok százalékos aránya, az átvitt adatok mennyisége (gigabájtban) és az átvitt adatok százalékos aránya.

## <a name="by-browser"></a>Böngésző szerint
A Böngésző szerint jelentés lehetővé teszi annak megtekintését, hogy mely böngészőket használták a tartalom kéréséhez. Az ilyen típusú jelentések létrehozásakor egy kördiagram jelzi a 10 legnépszerűbb böngésző által kezelt kérelmek százalékos arányát.

### <a name="using-the-pie-chart"></a>A kördiagram használata
* Mutasson egy szeletre a tortadiagramon a böngésző nevének és verziójának megtekintéséhez.
* A jelentés alkalmazásában minden egyedi böngésző/verzió kombináció más böngészőnek minősül.
* Az "Egyéb" nevű szelet az összes többi böngésző és verzió által kezelt kérelmek százalékos arányát jelzi.

A kördiagram létrehozásához használt adatok alatta tekinthetők meg. Itt megtalálja a böngésző típusát / verziószámát, a találatok teljes számát és a találatok százalékos arányát az egyes top 250 böngészőkben.

## <a name="by-referrer"></a>Ajánló szerint
A By Referrer jelentés lehetővé teszi, hogy megtekinthesse a kiválasztott platform on a tartalomra vonatkozó legjobb hivatkozókat. A hivatkozó azt az állomásnevet jelzi, amelyből a kérelem létrejött. Az ilyen típusú jelentés létrehozásakor egy sávdiagram jelzi a top 10 hivatkozó által generált kereslet (azaz találatok) mennyiségét.

A grafikon bal oldala (y-tengely) azt jelzi, hogy az adott eszköz összesen hány kérést tapasztalt az egyes ajánlók esetében. A diagram minden sávja egy hivatkozót jelöl. A színkódolási séma segítségével egy sávot egy ezz meg a Top 250 Hivatkozó szakaszban felsorolt hivatkozóval.

A sávdiagram létrehozásához használt adatok alatta tekinthetők meg. Itt megtalálja az URL-t, a találatok teljes számát és a találatok százalékos arányát, amelyeket az első 250 hivatkozó ból generált.

## <a name="by-download"></a>Letöltés szerint
A Letöltés sel jelentés lehetővé teszi, hogy elemezze a letöltési mintákat a leggyakrabban kért tartalomhoz. A jelentés tetején egy sávdiagram található, amely összehasonlítja a megkísérelt letöltéseket a 10 legkeresettebb eszköz befejezett letöltéseivel. Minden sáv színkódolt szerint, hogy ez egy megkísérelt letöltés (kék) vagy egy befejezett letöltés (zöld).

> [!NOTE]
> A jelentés alkalmazásában a szegélyes CNAME URL-ek egyenértékű CDN URL-címekké alakulnak át. Ez lehetővé teszi az eszközhöz társított összes statisztiká pontos megfeleltetését, függetlenül a ttól, hogy a CDN vagy a peremhálózati CNAME URL-cím a kérelemhez használt.This allows a accurate tally for all statistics associated with an asset regardless of the CDN or edge CNAME URL used to request it.
> 
> 

A diagram bal oldala (y-tengely) a 10 legkeresettebb eszköz mindegyikének fájlnevét jelzi. Közvetlenül a grafikon (x-tengely) alatt olyan címkéket talál, amelyek a megkísérelt/befejezett letöltések teljes számát jelzik.

Közvetlenül a sávdiagram alatt a következő információk jelennek meg a legkeresettebb 250 eszközhöz: relatív elérési út (beleértve a fájlnevet is), a letöltés letöltéseinek száma, a kért alkalmak száma és a teljes letöltést eredményező kérések.

> [!TIP]
> A CDN-t nem tájékoztatja egy HTTP kliens (azaz böngésző), ha egy eszköz teljesen le lett töltve. Ennek eredményeképpen ki kell számolnunk, hogy egy eszköz teljes mértékben le lett-e töltve az állapotkódok és a bájttartomány-kérelmek szerint. Az első dolog, amit keresünk, amikor ezt a számítást, hogy a kérelem eredménye egy 200 OK állapotkódot. Ha igen, akkor megnézzük a bájttartományú kérelmeket, hogy megbizonyosodjunk arról, hogy azok a teljes eszközre vonatkoznak. Végül összehasonlítjuk az átvitt adatok mennyiségét a kért eszköz méretével. Ha az átvitt adatok egyenlő vagy nagyobbak, mint a fájlméret, és a bájttartomány-kérelmek megfelelőek az adott eszközhöz, akkor a lekérés teljes letöltésnek számít.
> 
> A jelentés értelmező jellege miatt a következő pontokat kell szem előtt tartania, amelyek megváltoztathatják a jelentés konzisztenciáját és pontosságát.
> 
> * A forgalmi minták nem jósolhatók meg pontosan, ha a felhasználói ügynökök eltérően viselkednek. Ez 100%-nál nagyobb letöltési eredményeket eredményezhet.
> * Előfordulhat, hogy a HTTP progresszív letöltés előnyeit kihasználó eszközöket ez a jelentés nem képviseli pontosan. Ez annak köszönhető, hogy a felhasználók keresnek a különböző pozíciókat a videó.
> 
> 

## <a name="by-404-errors"></a>404-es hibák kal
A By 404 Hibák jelentés lehetővé teszi, hogy azonosítsa a tartalom típusát, amely a legtöbb 404 nem található állapotkódokat hozza létre. A jelentés tetején található egy sávdiagram a top 10 eszközhöz, amelyhez egy 404-es nem található állapotkódot adott vissza. Ez a sávdiagram összehasonlítja a kérelmek teljes számát azokkal a kérelmekkel, amelyek az eszközök 404-es nem található állapotkódját eredményezték. Minden sáv színkóddal van elhatárolt. A sárga sáv jelzi, hogy a kérelem 404-es nem található állapotkódot eredményezett. A piros sáv az eszközre vonatkozó kérelmek teljes számát jelzi.

> [!NOTE]
> E jelentés alkalmazásában vegye figyelembe a következőket:
> 
> * A lekérés az eszközre vonatkozó minden kérelmet jelent, függetlenül az állapotkódtól.
> * A szegélyes CNAME URL-ek egyenértékű CDN URL-ekké alakulnak át. Ez lehetővé teszi az eszközhöz társított összes statisztiká pontos megfeleltetését, függetlenül a ttól, hogy a CDN vagy a peremhálózati CNAME URL-cím a kérelemhez használt.This allows a accurate tally for all statistics associated with an asset regardless of the CDN or edge CNAME URL used to request it.
> 
> 

A diagram bal oldala (y-tengely) a 404-es állapotkódot eredményező 10 legkeresettebb eszköz mindegyikének fájlnevét jelzi. Közvetlenül a grafikon (x tengely) alatt olyan címkéket talál, amelyek a kérelmek teljes számát és a 404-es nem található állapotkódot eredményező kérelmek számát jelzik.

Közvetlenül a sávdiagram alatt a következő információk jelennek meg a legkeresettebb 250 eszközhöz: relatív elérési út (beleértve a fájlnevet is), a 404-es nem található állapotkódot eredményező kérelmek száma, az eszköz kérésének teljes száma, és a 404-es nem található állapotkódot eredményező kérelmek százalékos aránya.

## <a name="see-also"></a>Lásd még
* [Azure tartalomkézbesítési hálózat (CDN) – áttekintés](cdn-overview.md)
* [Valós idejű statisztikák a Microsoft Azure CDN-ben](cdn-real-time-stats.md)
* [Az alapértelmezett HTTP-viselkedés felülbírálása a szabálymotor használatával](cdn-rules-engine.md)
* [Élteljesítmény elemzése](cdn-edge-performance.md)

