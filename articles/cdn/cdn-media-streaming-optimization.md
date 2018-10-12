---
title: Online médialejátszás optimalizálása az Azure CDN-nel
description: Zökkenőmentes streamelési media fájlok optimalizálása
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: 9802296170f07bb8599058e230798f647e900d4d
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093697"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Online médialejátszás optimalizálása az Azure CDN-nel 
 
A nagy felbontású videók egyre gyakoribbá válik az interneten keresztül, amely a nagy méretű fájlok hatékony szállítási nehézségeket hoz létre. Az ügyfelek zavartalan a videó lejátszás várható igény szerinti vagy élő videó eszközöket a különböző hálózatokon és az ügyfelek a világ. Online médialejátszás fájlok gyors és hatékony kézbesítési mechanizmus fontos élvezetesebbé és a zökkenőmentes felhasználói élményt nyújt.  

Az élő adatfolyamokat a különösen nehezen, hogy a nagy méretű és egyidejű megtekintők száma miatt. Hosszú késések, hogy a felhasználóktól. Mivel élő Streamek nem gyorsítótárazható, előre, és nagy késések nem elfogadható, hogy a videó töredék időben érkeznek. 

A kérelem minták streamelési néhány új kihívásokat is biztosítanak. Amikor egy népszerű élő streamet vagy egy új adatsorozat igény szerinti videó megjelenik, a nézők millióihoz több ezer kérheti a stream egyszerre. Ebben az esetben az intelligens kérelem összevonása létfontosságú nem terhelje túl a szerverek, ha az eszközök még nincsenek gyorsítótárazva.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Online médialejátszás optimalizálása az Azure CDN a Microsofttól

**Az Azure CDN Standard a Microsoft** végpontok biztosításához streamelési médiatartalmak közvetlenül az általános webes kézbesítési optimalizálás típus használatával. 

Online médialejátszás optimalizálása a **Azure CDN Standard a Microsoft** érvényben, az élő vagy igény szerinti videó adatfolyam töredékkel önálló adathordozót használ a kézbesítési. Ez a folyamat továbbítja a progresszív letöltésen keresztül történő vagy bájttartomány-kérelmek használatával egyetlen nagy eszköz eltér. Információk az adott stílus a szállítási adathordozó: [nagy fájlok letöltési optimalizálása az Azure CDN](cdn-large-file-optimization.md).

Az általános kézbesítési vagy rögzített adathordozó kézbesítési optimalizálás-típusokra háttér-optimalizálás az Azure Content Delivery Network (CDN) használatával gyorsabban készíthetnek a médiatartalmak ellátásához. Konfigurációk a médiatartalmak ellátásához idővel megtanult ajánlott eljárások alapján is használata.

### <a name="partial-cache-sharing"></a>Részleges gyorsítótárának megosztása
Részleges gyorsítótárának megosztása lehetővé teszi, hogy a CDN-t az új kérelmek részlegesen gyorsítótárazott tartalmat szolgálnak ki. Például ha az első kérés a CDN a gyorsítótár-tévesztés eredményez, a kérelem érkezik a forrás. Bár ez hiányos a tartalom a CDN gyorsítótára tölti be, a küldött egyéb kérések számára a CDN megkezdheti az adatok beolvasása. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Online médialejátszás optimalizálása az Azure CDN a Verizontól

**Az Azure CDN Standard verizon** és **verizon Azure CDN Premium** végpontok biztosításához streamelési médiatartalmak közvetlenül az általános webes kézbesítési optimalizálás típus használatával. Néhány szolgáltatása a CDN-en közvetlenül amelyek segítenek a médiatartalmak ellátásához alapértelmezés szerint.

### <a name="partial-cache-sharing"></a>Részleges gyorsítótárának megosztása

Részleges gyorsítótárának megosztása lehetővé teszi, hogy a CDN-t az új kérelmek részlegesen gyorsítótárazott tartalmat szolgálnak ki. Például ha az első kérés a CDN a gyorsítótár-tévesztés eredményez, a kérelem érkezik a forrás. Bár ez hiányos a tartalom a CDN gyorsítótára tölti be, a küldött egyéb kérések számára a CDN megkezdheti az adatok beolvasása. 

### <a name="cache-fill-wait-time"></a>Gyorsítótár kitöltés várakozási idő

 A gyorsítótár kitöltés várakozási idő a szolgáltatás kényszeríti a peremhálózati kiszolgáló ugyanarra az erőforrásra vonatkozó kérelmei tárolásához, amíg a forráskiszolgálóról érkező HTTP-válaszfejlécek. Ha HTTP-válaszfejlécek a forrásból érkezik, az időzítő lejárata előtt, szüneteltetése is összes kérelem szolgál ki olvasásokat a növekvő gyorsítótár. Egy időben a gyorsítótárból tölti ki adatokat a forrásból. Alapértelmezés szerint a kitöltés gyorsítótár várakozási idő 3000 ezredmásodperc értéke. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Online médialejátszás optimalizálása az Azure CDN az Akamaitól
 
**Az Azure CDN Akamai Standard** kínál olyan szolgáltatás, amely kézbesíti a streamelési médiatartalmak hatékonyan felhasználóknak szerte a világon, ipari méretekben. A funkció csökkenti a késéseket, mivel ez csökkenti a szerverek terhelését. Ez a funkció az Akamai standard tarifacsomagban érhető el. 

Online médialejátszás optimalizálása a **Azure CDN Akamai Standard** érvényben, az élő vagy igény szerinti videó adatfolyam töredékkel önálló adathordozót használ a kézbesítési. Ez a folyamat továbbítja a progresszív letöltésen keresztül történő vagy bájttartomány-kérelmek használatával egyetlen nagy eszköz eltér. Információk az adott stílus a szállítási adathordozó: [nagyméretű fájlok optimalizálása](cdn-large-file-optimization.md).

Az általános kézbesítési vagy rögzített adathordozó kézbesítési optimalizálás-típusokra háttér-optimalizálás a CDN segítségével gyorsabban készíthetnek a médiatartalmak ellátásához. Konfigurációk a médiatartalmak ellátásához idővel megtanult ajánlott eljárások alapján is használata.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Online médialejátszás optimalizálása az Akamai CDN-végpont konfigurálása
 
Beállíthatja, hogy a content delivery network (CDN) végponthoz készregyártás az Azure Portalon nagyméretű fájlok optimalizálása érdekében. Ehhez a REST API-k vagy az ügyfél SDK-k valamelyikét is használhatja. A következő lépések bemutatják az Azure Portalon, az a folyamat egy **Azure CDN Akamai Standard** profil:

1. Egy új végponton, hozzáadásához az Akamai **CDN-profil** lapon jelölje be **végpont**.
  
    ![Új végpont](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. Az a **optimalizált** legördülő listában válassza **videó igényalapú videóstreaming** video-on-demand eszközök. Ha mégis kombinációja élő és igény szerinti videó streamelési, válassza ki a **általános médiatartalmak streamelése**.

    ![Streamelési kiválasztva](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Miután létrehozta a végpontot, bizonyos feltételeknek eleget tevő fájlok optimalizálása vonatkozik. Az alábbi szakasz ismerteti a folyamatot. 

### <a name="caching"></a>Gyorsítótárazás

Ha **Azure CDN Akamai Standard** észleli, hogy az eszköz egy streamelési jegyzékfájlt vagy töredékrész, különböző gyorsítótárazás lejárati idővel az általános webes kézbesítés használ. (A teljes listát az alábbi táblázatban tájékozódhat.) Mint mindig cache-control vagy Expires fejlécek a forrás által küldött összes régió megfelel. Ha az eszköz nem egy médiaobjektum, gyorsítótárazza a lejárati idővel az általános webes kézbesítés használatával.

A rövid negatív gyorsítótárazási időt hasznos forrás-alapú kiszervezéshez, ha sok felhasználó kérés egy kódrészletet, amely még nem létezik. Ilyen például, ha a csomagok nem érhetők el a forrásból a második élő stream. A már gyorsítótárazási időköz is segít, kiszervezheti a kérelmeket a forrásból, mert videótartalmak általában nem módosul.
 

|   | Általános webes kézbesítés | Általános médiastreaming | Igény szerinti videó online médialejátszás  
--- | --- | --- | ---
Gyorsítótárazás: pozitív <br> HTTP 200, 203, 300, <br> 301, 302 és 410 | 7 nap |365 nap | 365 nap   
Gyorsítótárazás: negatív <br> HTTP 204, 305, 404-ES, <br> és a 405-ös | None | 1 másodperc | 1 másodperc
 
### <a name="deal-with-origin-failure"></a>Foglalkoznak, forrása  

Általános kézbesítési és rögzített adathordozó kézbesítési is forrás időtúllépések és a egy újrapróbálkozási napló kérelem jellemző minták ajánlott eljárásai alapján. Ha például mert általános kézbesítési élő és rögzített adathordozó kézbesítési, használ egy rövidebb időkorlátot időérzékeny jellege miatt élő adások online közvetítése.

Ha a kapcsolat időkorlátja lejár, a CDN újrapróbálkozik többször a "504 – átjáró időtúllépése" hiba történt az ügyfél küldése előtt. 

Ha egy fájl megfelel a fájlok típusát és méretét feltételek listáját, a CDN médiatartalmak streamelése viselkedését használja. Ellenkező esetben általános webes kézbesítés használ.
   
### <a name="conditions-for-media-streaming-optimization"></a>Online médialejátszás optimalizálása feltételei 

A következő táblázatban megtalálható, az online médialejátszás optimalizálása teljesítendő feltételek: 
 
Támogatott az adatfolyam-típusok | Fájlkiterjesztések  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, kulcsot ts, aac
Az Adobe HDS | f4m, f4x, drmmeta, rendszerindítási, f4f,<br>Seg-illetheti URL-cím szerkezete <br> (reguláris kifejezéssel egyező: ^(/.*)Seq(\d+)-Frag(\d+)
KÖTŐJEL | mpd, kötőjelet, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Smooth streaming | / jegyzékfájl//QualityLevels/töredékek száma /
  
 
