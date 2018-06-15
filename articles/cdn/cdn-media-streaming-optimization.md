---
title: Médiaadatfolyam-továbbítást Azure CDN optimalizálása
description: Adatfolyam-továbbítási médiafájlok zökkenőmentes kézbesítésre optimalizálása
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: v-deasim
ms.openlocfilehash: 8a2b69aaa601e1d00152f57841a4d67f98680181
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766172"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Médiaadatfolyam-továbbítást Azure CDN optimalizálása 
 
A nagy felbontású video egyre gyakoribbá válik az interneten, és a nagy fájlok kézbesítését nehézségek hoz létre. Az igény szerinti videó egyenletes lejátszható várt vagy a hálózatok és ügyfelek különböző video eszközök élő a világ számos országában dolgoznak. A médiaadatfolyam-továbbítást fájlok gyors és hatékony mechanizmus alapvető fontosságú élvezetesebbé és zökkenőmentes felhasználói élményt nyújtsanak.  

Élő adatfolyamokat nehézségekbe ütközhet a fájlmegosztásba a nagy méretű és száma párhuzamos megjelenítők miatt. Nagy késleltetéseket okoz, hogy a felhasználók. Élő adatfolyamok időben nem gyorsítótárazható, mert a nagy késleltetésű nem teszi a nézők számára elfogadható, videó töredék időben érkeznek. 

A kérelem mintákat keressen az adatfolyam-is nyújt néhány új kihívásokat. Amikor népszerű élő adatfolyam vagy új több megjelenik az igény szerinti videót, több ezer megjelenítők több millió kérhetnek az adatfolyam egy időben. Ebben az esetben intelligens kérelem összevonása nélkülözhetetlen annak nem ne terhelje tovább a származási kiszolgálók Ha az eszközök még nincsenek gyorsítótárazva.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Médiaadatfolyam-továbbítást a Microsoft Azure CDN optimalizálás.

**Az Azure CDN Standard Microsoft** végpontok biztosításához adatfolyam adathordozó eszközök közvetlenül az általános webes optimalizálási típusú használatával. 

Médiaadatfolyam-továbbítást optimalizálást **Azure CDN Standard Microsoft** érvényben működés közbeni vagy videotartalom kézbesítésre adathordozót töredék használó média. Ez a folyamat nem azonos a progresszív letöltésen keresztül vagy a bájttartomány kérelmek használatával át egyetlen nagy eszköz. Információ a adott stílus media szállítási: [nagy méretű fájlok letöltési optimalizálása Azure CDN](cdn-large-file-optimization.md).

Az általános media kézbesítési és videotartalom media kézbesítési optimalizálási típusú Azure Content Delivery Network (CDN) használata gyorsabb képes biztosítani a media eszközök a háttér-optimalizálást. Megtudta, időbeli ajánlott eljárásai alapján adathordozó eszközök használata konfigurációk is.

### <a name="partial-cache-sharing"></a>A részleges gyorsítótári megosztása
A részleges gyorsítótári megosztása lehetővé teszi, hogy a CDN és a részlegesen gyorsítótárazott tartalmat rendszerben tett új kérelmekre. Például ha az első kérelem és a CDN egy gyorsítótár-tévesztései eredményez, a kérelem érkezik a forrás. Bár a nem teljes tartalom a CDN-gyorsítótárból tölti be, küldött egyéb kérések számára a CDN megkezdheti az adatok beolvasása. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Médiaadatfolyam-továbbítást Azure CDN Verizon optimalizálás.

**Az Azure CDN Standard verizon** és **verizon Azure CDN Premium** végpontok biztosításához adatfolyam adathordozó eszközök közvetlenül az általános webes optimalizálási típusú használatával. A CDN néhány funkcióinak közvetlenül segítse a postai adathordozó eszközök alapértelmezés szerint.

### <a name="partial-cache-sharing"></a>A részleges gyorsítótári megosztása

A részleges gyorsítótári megosztása lehetővé teszi, hogy a CDN és a részlegesen gyorsítótárazott tartalmat rendszerben tett új kérelmekre. Például ha az első kérelem és a CDN egy gyorsítótár-tévesztései eredményez, a kérelem érkezik a forrás. Bár a nem teljes tartalom a CDN-gyorsítótárból tölti be, küldött egyéb kérések számára a CDN megkezdheti az adatok beolvasása. 

### <a name="cache-fill-wait-time"></a>Gyorsítótár kitöltés várakozási idő

 A gyorsítótár kitöltés várakozási idő a szolgáltatás kényszeríti a peremhálózati kiszolgáló erőforrást a következő kéréseit tárolásához, amíg az eredeti kiszolgálóra érkező HTTP-válaszfejlécek. Ha HTTP-válaszfejlécek a forrásból érkeznek, az időzítő lejárata előtt, a növekvő gyorsítótárból szolgáltatott összes kérelem volt helyezhető tartásba. Egy időben a gyorsítótár tölti ki a forrás adatait. Alapértelmezés szerint a gyorsítótár kitöltés várakozási idő értéke 3000 ezredmásodperc. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Médiaadatfolyam-továbbítást Akamai Azure CDN optimalizálás.
 
**Az Azure CDN Standard Akamai** egy szolgáltatás, amely továbbítja a folyamatos átviteli adathordozó eszközök hatékony felhasználók léptékű világszerte kínál. A szolgáltatással csökken késések fordulnak elő, mivel csökkenti a terhelést a forrás-kiszolgálókon. A szolgáltatás az IP-címek Akamai standard érhető el. 

Médiaadatfolyam-továbbítást optimalizálást **Azure CDN Standard Akamai** érvényben működés közbeni vagy videotartalom kézbesítésre adathordozót töredék használó média. Ez a folyamat nem azonos a progresszív letöltésen keresztül vagy a bájttartomány kérelmek használatával át egyetlen nagy eszköz. Információ a adott stílus media szállítási: [nagy méretű fájlok optimalizálási](cdn-large-file-optimization.md).

Az általános media kézbesítési és videotartalom media kézbesítési optimalizálási típusú CDN használata gyorsabb képes biztosítani a media eszközök a háttér-optimalizálást. Megtudta, időbeli ajánlott eljárásai alapján adathordozó eszközök használata konfigurációk is.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Optimalizálja a médiaadatfolyam-továbbítást Akamai CDN végpont konfigurálása
 
Konfigurálhatja a tartalomkézbesítési hálózat (CDN) végpont nagy fájlok az Azure-portálon kézbesítési optimalizálása érdekében. Ehhez a REST API-k vagy az ügyfél SDK-k bármelyikét is használja. A következő lépések bemutatják az Azure-portálon a folyamat egy **Azure CDN Standard Akamai** profil:

1. Egy új végpont hozzáadásához egy Akamai **CDN-profil** lapon jelölje be **végpont**.
  
    ![Új végpont](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. Az a **optimalizálva** legördülő listában válassza **videó igény szerinti médiaadatfolyam** videotartalom eszközök. Ha egy élő kombinációja és a videotartalom adatfolyamként történő továbbítását, válassza ki a **általános médiaadatfolyam**.

    ![Adatfolyam-kiválasztva](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Miután létrehozta a végpontot, a feltételeknek megfelelő összes fájl optimalizálása vonatkozik. Az alábbi szakasz ismerteti a folyamatot. 

### <a name="caching"></a>Gyorsítótárazás

Ha **Azure CDN Standard Akamai** észleli, hogy az eszköz egy adatfolyam-továbbítási jegyzékfájl vagy töredék, a különböző gyorsítótár lejárati időpontjait a általános webes kézbesítési használ. (Lásd az alábbi táblázatban a teljes listát.) Ennek mindig a cache-control vagy Expires fejléc a forrásnak küldött is figyelembe véve. Ha az eszköz nem egy adathordozó eszköz, a lejárati időpontjait használatával általános webes kézbesítésre gyorsítótárazza.

A rövid negatív gyorsítótárazási idő az eredeti kiszervezési hasznos, sok felhasználó kérése egy kódrészletet, amely még nem létezik. Példa: Ha a csomagok nem érhetők el a forrásból, hogy a második élő adatfolyam. A gyorsítótárazási már intervallum segít kiszervezése a forrás érkező kéréseket, mert videotartalom általában nem módosította.
 

|   | Általános webes kézbesítés | Általános médiastreaming | Videotartalom médiaadatfolyam-továbbítást  
--- | --- | --- | ---
Gyorsítótárazás: pozitív <br> 200-AS, 203, 300, HTTP <br> 301, 302, és 410 | 7 nap |365 nap | 365 nap   
Gyorsítótárazás: negatív. <br> HTTP 204, 305, 404, <br> és 405 | None | 1 másodperc | 1 másodperc
 
### <a name="deal-with-origin-failure"></a>Az eredeti hiba kezelésére  

Általános media kézbesítési és videotartalom media kézbesítési is származási időtúllépések és szokásos kérelem minták ajánlott eljárásai alapján újrapróbálkozási naplót. Például mert általános media kézbesítési a működés közbeni és időérzékeny jellemzői miatt rövidebb kapcsolódási időtúllépés videotartalom media kézbesítési, használja az élő adatfolyam.

Ha a kapcsolat időtúllépés miatt megszakadt, a CDN újrapróbálja többször az ügyfél egy "504 - átjáró időtúllépése" hibaüzenet küldése előtt. 

Ha egy fájl megfelel a fájl típusát és méretét feltételek listája, a CDN viselkedésének médiaadatfolyam használja. Ellenkező esetben általános webes kézbesítési használ.
   
### <a name="conditions-for-media-streaming-optimization"></a>Médiaadatfolyam-továbbítást optimalizálási feltételei 

Az alábbi táblázat a médiaadatfolyam-továbbítást optimalizálási teljesítését feltételek csoportja: 
 
Támogatott adatfolyam-továbbítási típusok | Fájlkiterjesztések  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, kulcsot ts, aac
Az Adobe HDS | f4m, f4x, drmmeta, a rendszerindítás, f4f,<br>Seg-illetheti URL-cím szerkezete <br> (reguláris kifejezéssel egyező: ^(/.*)Seq(\d+)-Frag(\d+)
KÖTŐJEL | mpd, kötőjelet, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Smooth streaming | / jegyzékfájl//QualityLevels/töredék /
  
 
