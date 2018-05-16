---
title: Dinamikus gyorsítás Azure CDN használatával
description: Az Azure CDN dinamikus acceleration (DSA) optimalizálási támogatja a dinamikus tartalmat tartalmazó fájlok.
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
ms.date: 03/01/2018
ms.author: rli; v-deasim
ms.openlocfilehash: 4c0a68fd7b6cdf96bb495f6b447299bdbc5772f7
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Dinamikus gyorsítás Azure CDN használatával

A közösségi média, elektronikus kereskedelmi és a hyper-személyre szabott webes felbontására a végfelhasználók számára szolgáltatott tartalom gyorsan növekszik százalékaként valós idejű jönnek létre. A felhasználók a gyors, megbízható és személyre szabott webes élményt, a böngésző, helyről, eszközről vagy hálózati független várnak el. Azonban a nagyon innovációinak, amelyek a feladatait, amelyek révén is lap letöltések lassú, és a felhasználói élmény minőségének veszélynek. 

Standard tartalomkézbesítési hálózat (CDN) funkció lehetővé teszi a gyorsítótárban levő fájlok közelebb a végfelhasználók számára a statikus fájlok kézbesítését felgyorsítása érdekében. Azonban a dinamikus webes alkalmazásokhoz, a gyorsítótár helye tartalmat nem lehet a kiszolgáló a tartalom felhasználói viselkedés válaszul állít elő, mert. A tartalom kézbesítésével felgyorsítása bonyolultabb, mint a hagyományos peremhálózati gyorsítótár, és egy végpont megoldás, amely a beállítás a minden elem mellett a teljes elérési útja kezdetektől továbbításához igényel. Azure CDN dinamikus acceleration (DSA) optimalizálásával a dinamikus tartalmat a weblapok kimutathatóan javul a teljesítmény.

**Akamai Azure CDN** és **Azure CDN Verizon** DSA optimalizálási keresztül nyújtanak a **optimalizálva** menü végpont létrehozása során.

> [!Important]
> A **Akamai Azure CDN** profilok, engedélyezettek után szeretné módosítani a optimalizálása a CDN-végpont lett létrehozva.
>   
> A **Azure CDN Verizon** profilok, után nem módosíthatja a optimalizálása a CDN-végpont lett létrehozva.

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>CDN-végpont annak érdekében, a dinamikus fájlok kézbesítési konfigurálása

A CDN-végpontok optimalizálja a dinamikus fájlok kézbesítését konfigurálásához használhatja az Azure-portálon, a REST API-k, vagy az ügyfél SDK-k szoftveres ehhez az ugyanaz. 

**A CDN-végpontok DSA energiaoptimalizálás konfigurálása az Azure portál használatával:**

1. Az a **CDN-profil** lapon jelölje be **végpont**.

   ![Új CDN-végpont hozzáadása](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Megjelenik a **Végpont hozzáadása** panel.

2. A **optimalizálva**, jelölje be **dinamikus acceleration**.

    ![Hozzon létre egy új CDN-végpont DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. A **mintavételi elérési**, adjon meg érvényes elérési utat egy fájlba.

    Mintavételi elérési út használatával DSA, és egy érvényes elérési utat kell megadni a létrehozásához. DSA használja kis *mintavételi elérési* fájl az eredeti kiszolgálóra, hálózati útválasztási konfigurációi a CDN optimalizálása helyezett. A mintavétel elérési út fájl letöltése és a minta-fájl feltöltése a helyen, vagy egy meglévő eszközt használja a forrás, amely körülbelül 10 KB-nál.

4. Adja meg a szükséges végpont beállításokat (további információkért lásd: [hozzon létre egy új CDN-végpont](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)) elemre, majd válassza **Hozzáadás**.

   A CDN-végpont létrehozása után a feltételeknek megfelelő összes fájlt a DSA optimalizálásokat vonatkozik. 


**Egy meglévő végpontot konfigurálása DSA (csak a profilok Akamai Azure CDN):**

1. Az a **CDN-profil** lapon, válassza ki a módosítani kívánt végpont.

2. A bal oldali ablaktáblán válassza ki a **optimalizálási**. 

   A **optimalizálási** lap jelenik meg.

3. A **optimalizálva**, jelölje be **dinamikus acceleration**, majd jelölje be **mentése**.

> [!Note]
> DSA költségeket terhel extra. További információkért lásd: [Content Delivery Network árképzési](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Azure CDN használatával DSA optimalizálása

Azure CDN szolgáltatás használata a dinamikus hely gyorsítás felgyorsítja a kézbesítési dinamikus eszközök, az alábbi technikák segítségével:

-   [Útvonal optimalizálása](#route-optimization)
-   [TCP optimalizálás.](#tcp-optimizations)
-   [Objektum előzetes betöltési (csak Akamai Azure CDN)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptív kép tömörítési (csak Akamai Azure CDN)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Útvonal optimalizálása

Útvonal optimalizálási fontos, mert az interneten a dinamikus hely, ahol forgalmat, és átmenetileg leállások folyamatosan változnak a hálózati topológia. A Border Gateway Protocol (BGP) az útválasztási protokoll, az Internet, de előfordulhat, hogy gyorsabb útvonalak keresztül közbülső jelenléti pontra (PoP) kiszolgálók. 

Útvonal optimalizálási úgy dönt, a legoptimálisabb a forrás elérési útja, hogy a hely folyamatosan elérhető és a dinamikus tartalmat a rendszer a végfelhasználók számára a lehető leggyorsabb és legmegbízhatóbb útvonal keresztül. 

A Akamai hálózat technikák segítségével valós idejű adatokat gyűjthet, és hasonlítsa össze a Akamai kiszolgáló, valamint az alapértelmezett BGP-útvonal csomópontjai különböző útvonalai a forrás és a CDN közötti leggyorsabb útvonal meghatározásához nyissa meg interneten keresztül. Ezek a technológiák elkerülése Internet torlódás pontok és a hosszú útvonalakat. 

Hasonlóképpen a Verizon hálózat által használt DNS nem egyedi kombinációját, nagy kapacitású támogatja a POP, és állapot-ellenőrzést, az ügyfél és a forrás legjobb útvonal adatai a legjobb átjárók meghatározásához.
 
Ennek eredményeképpen teljesen dinamikus és tranzakciós tartalom céleszközökre gyorsabb és megbízhatóbb a végfelhasználók számára, akkor is, amikor az uncacheable. 

### <a name="tcp-optimizations"></a>TCP optimalizálás.

Transmission Control Protocol (TCP) a normál a Internet protokollkészlet továbbítják az IP-hálózat az alkalmazások között.  Alapértelmezés szerint több vissza oda-kérelmek szükségesek hálózati congestions, a hatékonyság hiánya léptékű eredmények elkerülése érdekében a TCP-kapcsolatot, valamint korlátok beállításához. **Akamai Azure CDN** kezeli a probléma három területeken optimalizálásával: 

 - [Távolítsa el az indexkulcsból TCP lassú indítása](#eliminating-tcp-slow-start)
 - [Állandó kapcsolatot kihasználva](#leveraging-persistent-connections)
 - [TCP-csomag paraméter hangolása](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Távolítsa el az indexkulcsból TCP lassú indítása

TCP *start lassú* algoritmus a TCP protokoll, amely megakadályozza, hogy a hálózati torlódás, ha a hálózaton keresztül küldött adatok mennyisége korlátozza. Kezdődik ki a küldő és fogadó között kis torlódás méretek amíg eléri a maximális vagy csomagvesztés észlel.

 Mindkét **Akamai Azure CDN** és **Azure CDN Verizon** profilok kiküszöbölése TCP lassú útmutató az alábbi három lépést:

1. A peremhálózati PoP kiszolgálók közötti kapcsolatok a sávszélesség mérését állapotának és sávszélesség figyelésére szolgál.
    
2. Metrikák peremhálózati PoP kiszolgálók között vannak megosztva, úgy, hogy minden kiszolgáló tisztában legyen a hálózati feltételek mellett, valamint a felhasználókat ezekbe a csoportokba a kapcsolódási kiszolgáló állapotát.  
    
3. A CDN peremhálózati kiszolgálóinak ellenőrizze feltételezéseket néhány átviteli paraméterek, például milyen az optimális ablakméret kell más CDN peremhálózati kiszolgálóinak a közelében való kommunikáció során. Ez a lépés azt jelenti, hogy a kezdeti torlódás ablakméret növelhető, ha a CDN peremhálózati kiszolgáló közötti kapcsolat állapotát magasabb packet adatátvitel képes.  

#### <a name="leveraging-persistent-connections"></a>Állandó kapcsolatot kihasználva

CDN használatával, kevesebb egyedi számítógépek kapcsolódni az eredeti kiszolgálóra közvetlenül az eredeti közvetlenül csatlakozó felhasználók képest. Az Azure CDN is a felhasználói kérelmek együtt kevesebb a forrás-kapcsolatok létesítéséhez a készletbe.

Mint korábban már említettük több kézfogás kérést a TCP-kapcsolat létrehozásához szükséges. Állandó kapcsolat, amely által megvalósított a `Keep-Alive` HTTP-fejléc, az TCP-kapcsolatok a több HTTP-kérelmekre körbejárási alkalommal mentéséhez és a szállítási felgyorsítása meglévő újbóli. 

**Verizon Azure CDN** is elküldi a rendszeres életben tartási csomagok nyitott kapcsolat a lezárási folyamatban érdekében a TCP-kapcsolaton keresztül.

#### <a name="tuning-tcp-packet-parameters"></a>TCP-csomag paraméter hangolása

**Akamai Azure CDN** beállítja a kiszolgálók kapcsolatok paraméterek, és csökkenti a távolsági kiszolgálókkal való adatváltások számát az alábbi technikák segítségével a hely beágyazott tartalom beolvasásához szükséges:

- A kezdeti torlódás ablak növelését, így további csomagokat is küldhetők nyugtázást várakozás nélkül.
- A kezdeti újraküldési időtúllépés csökkentése, hogy a rendszer észlelt egy adatvesztés, és újraküldési gyorsabban következik be.
- A várakozási idő, feltéve, hogy a csomagok elvesztek a továbbítás előtt csökkentse a minimális és maximális újraküldési időkorlát csökkentése.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Objektum előzetes betöltési (csak Akamai Azure CDN)

A legtöbb webhelyek tartalmazzák a HTML-lapot, amely különböző más erőforrások, például képek és parancsfájlok hivatkozik. Általában, amikor egy ügyfél egy weblap, a böngésző először letölti és elemzi a HTML-objektumot, és majd hajt végre további kérelmeket, amelyek szükségesek az oldal teljes betöltése csatolt eszközök. 

*Előzetes betöltés* képek és parancsfájlok beolvasásához technika beágyazott HTML-weblap közben a böngésző kiszolgált a HTML-KÓDBAN, és előtt a böngésző még akkor is lehetővé teszi a objektum kérelmeket. 

A lehívott beállítással időpontjában, amikor a CDN szolgál a HTML-kiinduló lap az ügyfél böngészője kapcsolva a CDN elemzi a HTML-fájl, és további erőforrásokra vonatkozó kérelmeket bármely csatolt, és a gyorsítótárban tárolja. Amikor az ügyfél a kérelmeket a csatolt eszközök, a CDN biztonsági kiszolgáló már rendelkezik a kért objektumok, és képes kiszolgálni azokat azonnal nélkül és a forrás oda-vissza. Az optimalizálás előnyökkel jár a tartalmat kérelmeznék és a nem gyorsítótárazható.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptív kép tömörítési (csak Akamai Azure CDN)

Egyes eszközök, különösen a mobil is, felhasználói élmény időről időre lassabb hálózati átviteli sebességek esetén. Ezekben az esetekben célszerű több kelljen sokat várnia a teljes megoldás lemezképek helyett a felhasználó számára a weblap képek gyorsabban kapja.

Ez a szolgáltatás automatikusan figyeli a hálózatban, és alkalmazta a szabványos JPEG tömörítési módszerek kézbesítési időhöz javítására lassabb hálózati sebességek esetén.

Adaptív kép tömörítés | Fájlkiterjesztések  
--- | ---  
JPEG-tömörítés | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Gyorsítótárazás

A DSA, gyorsítótárazás ki van kapcsolva a CDN-t, a szolgáltatást alapértelmezés szerint akkor is, ha a forrás tartalmazza `Cache-Control` vagy `Expires` fejlécek a válaszban. DSA általában kell nem gyorsítótárazható, mert azokat az egyes ügyfelek egyedi dinamikus eszközök esetében használatos. Gyorsítótárazás Ez a viselkedés meghibásodásához vezethet.

Ha rendelkezik statikus és dinamikus eszközök vegyesen webhellyel, célszerű egy hibrid megoldást a legjobb teljesítmény eléréséhez. 

A **Azure CDN Standard verizon** és **Azure CDN Standard Akamai** profilok, bekapcsolása a megadott DSA-végpontok gyorsítótárazás [szabályok gyorsítótárazás](cdn-caching-rules.md).

Gyorsítótárazás szabályok eléréséhez:

1. Az a **CDN-profil** lapon beállításaiban, válassza a **szabályok gyorsítótárazás**.  
    
    ![CDN-gyorsítótárszabályok gomb](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    A **szabályok gyorsítótárazás** lap megnyitásakor.

2. Szabály létrehozása a globális vagy egyéni gyorsítótárazási kapcsolja be a DSA-végpontot gyorsítótárazását. 

A **verizon Azure CDN Premium** csak profilok, kapcsolja be a megadott DSA-végpontok gyorsítótárazása a [szabálymotor](cdn-rules-engine.md). Minden létrehozott szabályok csak mindegyik végpont a profil DSA optimalizált hatással. 

A szabályok motor eléréséhez:
    
1. Az a **CDN-profil** lapon jelölje be **kezelése**.  
    
    ![CDN-profil kezelésére gomb](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Megnyitja a CDN-felügyeleti portálon.

2. A CDN-kezelési portálon, válassza ki a **ADN**, majd jelölje be **szabálymotor**. 

    ![A DSA szabálymotor](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Másik lehetőségként használhatja a két CDN-végpontok: az eszközök dinamikus és statikus optimalizálási típussal, például az általános optimalizált egy másik végpontjával DSA optimalizált egy végpont webes kézbesítése kézbesítési kérelmeznék eszközök. A weblap URL-címek közvetlenül kapcsolódik a CDN-végpont használatát tervezi meg az eszköz módosításához. 

Például: `mydynamic.azureedge.net/index.html` egy dinamikus oldalra, és be töltve, a DSA végpontról.  A html-weblap hivatkozik, például JavaScript-tárak vagy lemezképeket, amelyek be vannak töltve a statikus CDN-végpont a például a statikus adategységek `mystatic.azureedge.net/banner.jpg` és `mystatic.azureedge.net/scripts.js`. 



