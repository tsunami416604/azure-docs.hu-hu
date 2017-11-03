---
title: "Az Azure CDN használatával dinamikus gyorsulás"
description: "Dinamikus gyorsítás részletes bemutatója"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: v-semcev
ms.openlocfilehash: be2719e0e02c8bc69800ef4a3e7da3c3164cb9dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Az Azure CDN használatával dinamikus gyorsulás

A közösségi média, elektronikus kereskedelmi és a hyper-személyre szabott webes felbontására a végfelhasználók számára szolgáltatott tartalom gyorsan növekszik százalékaként valós idejű jönnek létre. A felhasználók gyors, megbízható és személyre szabott webes feladatait, a böngésző, helyről, eszközről vagy hálózati független várnak el. Azonban a nagyon innovációinak, amelyek a feladatait, amelyek révén is lap letöltések lassú, és a felhasználói élmény minőségének veszélynek. 

Standard szintű CDN-funkció lehetővé teszi a gyorsítótárban levő fájlok közelebb a végfelhasználók számára a statikus fájlok kézbesítését felgyorsítása érdekében. Azonban a dinamikus webes alkalmazásokhoz, a gyorsítótár helye tartalmat nem lehet a kiszolgáló a tartalom felhasználói viselkedés válaszul állít elő, mert. A tartalom kézbesítésével felgyorsítása bonyolultabb, mint a hagyományos peremhálózati gyorsítótár, és egy végpont megoldás, amely a beállítás a minden elem mellett a teljes elérési útja kezdetektől továbbításához igényel. Az Azure CDN dinamikus hely Acceleration (DSA), a dinamikus tartalmat a weblapok kimutathatóan javul a teljesítmény.

Akamai és Verizon Azure CDN DSA optimalizálási keresztül kínál a **optimalizálva** menü végpont létrehozása során.

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>CDN-végpont annak érdekében, a dinamikus fájlok kézbesítési konfigurálása

Beállíthatja, hogy a CDN-végpontot kiválasztásával az Azure-portálon dinamikus fájlok kézbesítését optimalizálása érdekében a **dinamikus acceleration** lehetőség szerint a **optimalizálva** tulajdonság kiválasztása a végpont létrehozása során. A REST API-k vagy bármely, az ügyfél SDK-k használatával is hajtsa végre ugyanezt programozott módon. 

### <a name="probe-path"></a>Mintavételi elérési útja
Mintavételi elérési út használatával dinamikus hely gyorsítás, és érvénytelen a létrehozásához szükséges. DSA használja kis *mintavételi elérési* fájl a forrás hálózati útválasztási konfigurációi a CDN optimalizálása helyezett. Töltse le és a minta-fájl feltöltése a helyen, vagy egy meglévő eszközt használja a forrás, amely körülbelül 10 KB-os mintavételi elérési helyette Ha az eszköz már létezik.

> [!Note]
> DSA költségeket terhel extra. További információkért lásd: a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cdn/) további információt.

Az alábbi képek ábrázolni a folyamatot, Azure-portálon.
 
![Új CDN-végpont hozzáadása](./media/cdn-dynamic-site-acceleration/01_Endpoint_Profile.png) 

*1. ábra: Új CDN-végpont hozzáadása a CDN-profil*
 
![DSA új CDN-végpont létrehozása](./media/cdn-dynamic-site-acceleration/02_Optimized_DSA.png)  

*2. ábra: A CDN-végpont létrehozása dinamikus gyorsítás kijelölt optimalizálása*

A CDN-végpont létrehozása után a feltételeknek megfelelő összes fájlt a DSA optimalizálásokat vonatkozik. Az alábbi szakasz ismerteti részletesen DSA-optimalizálás.

## <a name="dsa-optimization-using-azure-cdn"></a>Azure CDN használatával DSA optimalizálása

Az Azure CDN dinamikus hely gyorsítás felgyorsítja a kézbesítési dinamikus eszközök az alábbi technikák segítségével:

-   Útvonal optimalizálása
-   TCP optimalizálás.
-   Objektum előzetes betöltési (csak Akamai)
-   Mobil kép tömörítési (csak Akamai)

### <a name="route-optimization"></a>Útvonal optimalizálása

Útvonal optimalizálási fontos, mert az interneten a dinamikus hely, ahol forgalmat, és átmenetileg leállások folyamatosan változnak a hálózati topológia. A Border Gateway Protocol (BGP) az útválasztási protokoll, az Internet, de előfordulhat, hogy gyorsabb útvonalak keresztül közbülső jelenléti pontra (PoP) kiszolgálók. 

Útvonal optimalizálási úgy dönt, a legoptimálisabb a forrás elérési útja, hogy a hely folyamatosan elérhető és a dinamikus tartalmat a rendszer a végfelhasználók számára a lehető leggyorsabb és legmegbízhatóbb útvonal keresztül. 

A Akamai hálózat technikák segítségével valós idejű adatokat gyűjthet, és hasonlítsa össze a Akamai kiszolgáló, valamint az alapértelmezett BGP-útvonal csomópontjai különböző útvonalai a forrás és a CDN közötti leggyorsabb útvonal meghatározásához nyissa meg interneten keresztül. Ezek a technológiák elkerülése Internet torlódás pontok és a hosszú útvonalakat. 

Hasonlóképpen a Verizon hálózat által használt DNS nem egyedi kombinációját, nagy kapacitású támogatja a POP, és állapot-ellenőrzést, az ügyfél és a forrás legjobb útvonal adatai a legjobb átjárók meghatározásához.
 
Ennek eredményeképpen teljesen dinamikus és tranzakciós tartalom céleszközökre gyorsabb és megbízhatóbb a végfelhasználók számára, akkor is, amikor az uncacheable. 

### <a name="tcp-optimizations"></a>TCP optimalizálás.

Transmission Control Protocol (TCP) a normál a Internet protokollkészlet továbbítják az IP-hálózat az alkalmazások között.  Alapértelmezés szerint nincsenek több oda-vissza kérelmek hálózati congestions, a hatékonyság hiánya léptékű eredmények elkerülése érdekében a TCP-kapcsolatot, valamint korlátok beállításához szükséges. Ez a probléma három területeken optimalizálásával Akamai Azure CDN foglalkozik: 

 - Távolítsa el az indexkulcsból lassú indítása
 - Állandó kapcsolatot kihasználva
 - TCP-csomag paraméter (csak Akamai) hangolása

#### <a name="eliminating-slow-start"></a>Távolítsa el az indexkulcsból lassú indítása

*Kezdő lassú* tartozik a TCP protokoll, amely megakadályozza, hogy a hálózati torlódás, ha a hálózaton keresztül küldött adatok mennyisége korlátozza. Kezdődik ki a küldő és fogadó között kis torlódás méretek amíg eléri a maximális vagy csomagvesztés észlel.

Akamai és Verizon Azure CDN három lépésben lassú start megoldással:

1.  Akamai és a Verizon tartozó hálózati állapot és a sávszélesség-figyelés segítségével peremhálózati PoP kiszolgálók közötti kapcsolatok a sávszélesség mérését.
2. A metrikák peremhálózati PoP kiszolgálók között vannak megosztva, úgy, hogy minden kiszolgáló tisztában legyen a hálózati feltételek mellett, valamint a felhasználókat ezekbe a csoportokba a kapcsolódási kiszolgáló állapotát.  
3. A CDN peremhálózati kiszolgálóinak is tud végezni néhány átviteli paraméterek, például milyen az optimális ablakméret kell más CDN peremhálózati kiszolgálóinak a közelében való kommunikáció során feltételezéseket. Ez a lépés azt jelenti, hogy a kezdeti torlódás ablakméret növelhető, ha a CDN peremhálózati kiszolgáló közötti kapcsolat állapotát magasabb packet adatátvitel képes.  

#### <a name="leveraging-persistent-connections"></a>Állandó kapcsolatot kihasználva

CDN használatával, kevesebb egyedi számítógépek kapcsolódni az eredeti kiszolgálóra közvetlenül az eredeti közvetlenül csatlakozó felhasználók képest. Akamai és Verizon Azure CDN is a felhasználói kérelmek együtt kevesebb a forrás-kapcsolatok létesítéséhez a készletbe.

A korábban említett TCP-kapcsolatok a hálózatról több kérést oda-vissza a kézfogás új kapcsolatot létesíteni. Állandó kapcsolat, más néven "HTTP életben tartási," használja fel meglévő TCP-kapcsolatok a körbejárási alkalommal mentéséhez és a szállítási felgyorsítása több HTTP-kérelmekre. 

A Verizon hálózati rendszeres életben tartási csomagok is küld a kapcsolat a lezárási folyamatban érdekében TCP-kapcsolaton keresztül.

#### <a name="tuning-tcp-packet-parameters"></a>TCP-csomag paraméter hangolása

Is a paraméterek, a kiszolgálók kapcsolatok szabályozására, és csökkenti a távolsági kerekíteni tartalmának lekéréséhez szükséges utazgatással zenéjét beágyazott a hely a következő eljárások használatával Akamai Azure CDN:

1.  A kezdeti torlódás ablak növelését, így további csomagokat is küldhetők nyugtázást várakozás nélkül.
2.  A kezdeti újraküldési időtúllépés csökkentése, hogy a rendszer észlelt egy adatvesztés, és újraküldési gyorsabban következik be.
3.  A várakozási idő, feltéve, hogy a csomagok elvesztek a továbbítás előtt csökkentse a minimális és maximális újraküldési időkorlát csökkentése.

### <a name="object-prefetch-akamai-only"></a>Objektum előzetes betöltési (csak Akamai)

A legtöbb webhelyek tartalmazzák a HTML-lapot, amely különböző más erőforrások, például képek és parancsfájlok hivatkozik. Általában, amikor egy ügyfél egy weblap, a böngésző először letölti és elemzi a HTML-objektumot, és majd hajt végre további kérelmeket, amelyek szükségesek az oldal teljes betöltése csatolt eszközök. 

*Előzetes betöltés* képek és parancsfájlok beolvasásához technika beágyazott HTML-weblap közben a böngésző kiszolgált a HTML-KÓDBAN, és előtt a böngésző még akkor is lehetővé teszi a objektum kérelmeket. 

Az a **előzetesen lehívott lapok** kapcsolva időpontjában, amikor a CDN szolgál a HTML-KÓDBAN kiinduló lap az ügyfél böngészője, a CDN elemzi a HTML-fájlt, és további kérelmeket ellenőrizze az összes kapcsolódó erőforrásokat, és tárolja a gyorsítótárban. Amikor az ügyfél a kérelmeket a csatolt eszközök, a CDN biztonsági kiszolgáló már rendelkezik a kért objektumok, és képes kiszolgálni azokat azonnal nélkül és a forrás oda-vissza. Az optimalizálás előnyökkel jár a tartalmat kérelmeznék és a nem gyorsítótárazható.

### <a name="adaptive-image-compression-akamai-only"></a>Adaptív kép tömörítési (csak Akamai)

Egyes eszközök, különösen a mobil is, felhasználói élmény időről időre lassabb hálózati átviteli sebességek esetén. Ezekben az esetekben célszerű több kelljen sokat várnia a teljes megoldás lemezképek helyett a felhasználó számára a weblap képek gyorsabban kapja.

Ez a szolgáltatás automatikusan figyeli a hálózatban, és alkalmazta a szabványos JPEG tömörítési módszerek kézbesítési időhöz javítására lassabb hálózati sebességek esetén.

Adaptív kép tömörítés | Fájlkiterjesztések  
--- | ---  
JPEG-tömörítés | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Gyorsítótárazás

DSA, a gyorsítótárazás ki van kapcsolva a CDN-t, a szolgáltatást alapértelmezés szerint akkor is, ha a forrás fejlécek cache-control/lejár a válaszban. Ez az alapértelmezett ki van kapcsolva, mert DSA jellemzően a dinamikus eszközök, mert azok az egyes ügyfelek egyedi, nem kell gyorsítótárazva, és bekapcsolja a gyorsítótárazás alapértelmezés szerint ez a viselkedés érvénytelenné.

Ha rendelkezik statikus és dinamikus eszközök vegyesen webhellyel, célszerű egy hibrid megoldást a legjobb teljesítmény eléréséhez. 

ADN használatakor az Verizon Premium bekapcsolhatja a gyorsítótárazást vissza az adott esetben a szabályok használata.  

Alternatív, hogy két CDN-végpontok használja. Egy statikus optimalizálási típussal, például az általános webes kézbesítése kézbesítési kérelmeznék eszközök dinamikus eszközök, és egy másik végpontjával továbbítására DSA. Ahhoz, hogy ez a megoldás megvalósítása érdekében módosítani fogja a weblap URL-címek közvetlenül kapcsolódik a CDN-végpont is használni kívánja az eszközt. 

Például: `mydynamic.azureedge.net/index.html` egy dinamikus oldalra, és be töltve, a DSA végpontról.  A html-weblap hivatkozik, például JavaScript-tárak vagy lemezképeket, amelyek be vannak töltve a statikus CDN-végpont a például a statikus adategységek `mystatic.azureedge.net/banner.jpg` és `mystatic.azureedge.net/scripts.js`. 

Egy példa található [Itt](https://docs.microsoft.com/azure/cdn/cdn-cloud-service-with-cdn#controller) az ASP.NET webalkalmazások vezérlők segítségével tartalmat adott CDN URL-a.




