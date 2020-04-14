---
title: Dinamikus webhelygyorsítás az Azure CDN-en keresztül
description: Az Azure CDN támogatja a dinamikus tartalommal rendelkező fájlok dinamikus helygyorsítási (DSA) optimalizálását.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 26559adf183a5e008d77b87654a1bd4dabebbca0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253832"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Dinamikus webhelygyorsítás az Azure CDN-en keresztül

A robbanás a szociális média, az elektronikus kereskedelem, és a hiper-személyre szabott web, a gyorsan növekvő százaléka a tartalom szolgált a végfelhasználók valós időben. A felhasználók gyors, megbízható és személyre szabott webes élményt várnak el, függetlenül a böngészőjüktől, helyüktől, eszközüktől vagy hálózatuktól. Azonban az igazi újítások, amelyek ezeket a tapasztalatokat annyira vonzóvá teszik, szintén lassú oldalletöltéseket eredményeznek, és veszélyeztetik a fogyasztói élmény minőségét. 

A szabványos tartalomkézbesítési hálózat (CDN) funkciója lehetővé teszi a fájlok gyorsítótárazását a végfelhasználókhoz közelebb, hogy felgyorsítsa a statikus fájlok kézbesítését. A dinamikus webalkalmazások nál azonban nem lehetséges a tartalom peremhálózati helyeken való gyorsítótárazása, mert a kiszolgáló a felhasználó viselkedésére válaszul hozza létre a tartalmat. Az ilyen tartalom kézbesítésének felgyorsítása összetettebb, mint a hagyományos élgyorsítótárazás, és teljes körű megoldást igényel, amely finoman hangolja az egyes elemeket a teljes adatútvonal mentén a kezdetektől a kézbesítésig. Az Azure CDN dinamikus webhelygyorsítás (DSA) optimalizálásával a dinamikus tartalmú weboldalak teljesítménye mérhetően javul.

**Az Akamai azure CDN-je** és a **Verizon Azure CDN-je** egyaránt kínál DSA-optimalizálást a végpontlétrehozása során a **Menu optimalizált** menün keresztül. A Microsoft dinamikus webhelygyorsítását az [Azure Front Door Service kínálja.](https://docs.microsoft.com/azure/frontdoor/front-door-overview)

> [!Important]
> **Az Akamai-profilokból származó Azure CDN** esetén módosíthatja a CDN-végpont optimalizálását a létrehozása után.
>   
> A **Verizon Azure CDN** típusú profilok esetén nem módosíthatja a CDN-végpont optimalizálását a végpont létrehozása után.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>A CDN-végpont konfigurációja a dinamikus fájlok kézbesítésének felgyorsításához

CdN-végpont konfigurálásához a dinamikus fájlok kézbesítésének optimalizálásához használhatja az Azure Portalt, a REST API-kat vagy az ügyfél SDK-kat, hogy ugyanazt a dolgot programozott módon végezze el. 

**CdN-végpont konfigurálása a DSA-optimalizáláshoz az Azure Portal használatával:**

1. A **CDN-profillapon** válassza a **Végpont**lehetőséget.

   ![Új CDN-végpont hozzáadása](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Megjelenik a **Végpont hozzáadása** panel.

2. Az **Optimalizált csoportban**válassza a **Dinamikus helygyorsítás**lehetőséget.

    ![Új CDN-végpont létrehozása a DSA-val](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. A **Mintavétel elérési útja**mezőbe írja be a fájl érvényes elérési útját.

    A mintavételi útvonal a DSA-ra jellemző szolgáltatás, és a létrehozáshoz érvényes elérési út szükséges. A DSA egy kis *mintavételi elérési utat* használ, amelyet az eredeti kiszolgálón helyeznek el a CDN hálózati útválasztási konfigurációinak optimalizálásához. A mintavételi elérési út fájl, letöltheti és feltöltheti a mintafájlt a webhelyre, vagy egy meglévő eszköz a származási, amely körülbelül 10 KB méretű.

4. Adja meg a többi szükséges végponti beállítást (további információt az [Új CDN-végpont létrehozása) című](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)témakörben talál, majd válassza **a Hozzáadás lehetőséget.**

   A CDN-végpont létrehozása után alkalmazza a DSA-optimalizálást az összes olyan fájlra, amely megfelel bizonyos feltételeknek. 


**Meglévő végpont konfigurálása a DSA-hoz (csak Akamai-profilokból származó Azure CDN):**

1. A **CDN-profillapon** jelölje ki a módosítani kívánt végpontot.

2. A bal oldali ablaktáblában válassza az **Optimalizálás**lehetőséget. 

   Megjelenik **az Optimalizálás** lap.

3. Az **Optimalizált csoportban**válassza a **Dinamikus helygyorsítás**lehetőséget, majd a **Mentés**lehetőséget.

> [!Note]
> DSA felár ellenében. További információt a [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/)című témakörben talál.

## <a name="dsa-optimization-using-azure-cdn"></a>DSA-optimalizálás az Azure CDN használatával

Az Azure CDN dinamikus webhelygyorsítása a következő technikák alkalmazásával felgyorsítja a dinamikus eszközök kézbesítését:

-   [Útvonaloptimalizálás](#route-optimization)
-   [TCP-optimalizálások](#tcp-optimizations)
-   [Objektum előzetes betöltése (csak az Akamai-ból származó Azure CDN)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptív képtömörítés (csak az Akamai-ból származó Azure CDN)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Útvonaloptimalizálás

Az útvonaloptimalizálás azért fontos, mert az internet dinamikus hely, ahol a forgalom és az ideiglenes kimaradások folyamatosan módosítják a hálózati topológiát. A Border Gateway Protocol (BGP) az internet útválasztási protokollja, de előfordulhat, hogy gyorsabb útvonalak vannak a közvetítő jelenléti pont (PoP) kiszolgálóin keresztül. 

Az útvonaloptimalizálás az eredeti ség legoptimálisabb útvonalát választja ki, így a webhely folyamatosan elérhető, és a dinamikus tartalom a lehető leggyorsabb és legmegbízhatóbb útvonalon kerül a végfelhasználókhoz. 

Az Akamai hálózat technikákat használ a valós idejű adatok gyűjtésére és a különböző útvonalak összehasonlítására az Akamai kiszolgáló különböző csomópontjain keresztül, valamint az alapértelmezett BGP útvonalat a nyílt interneten keresztül az origó és a CDN él közötti leggyorsabb útvonal meghatározásához. Ezek a technikák elkerülik az internetes torlódási pontokat és a hosszú útvonalakat. 

Hasonlóképpen a Verizon hálózat az Anycast DNS, a nagy kapacitású támogató POP-ok és az állapotellenőrzések kombinációját használja a legjobb átjárók meghatározásához, hogy az ügyféltől az eredetihöz irányítsa az adatokat.
 
Ennek eredményeképpen a teljesen dinamikus és tranzakciós tartalom gyorsabban és megbízhatóbban kerül a végfelhasználókhoz, még akkor is, ha nem gyorsítótárazható. 

### <a name="tcp-optimizations"></a>TCP-optimalizálás

A TCP (Transmission Control Protocol) az IP-hálózaton lévő alkalmazások közötti információtovábbításra használt ip-protokollcsomag szabványa.  Alapértelmezés szerint több oda-vissza kérelemre van szükség a TCP-kapcsolat beállításához, valamint a hálózati torlódások elkerülésére vonatkozó korlátokra, ami nagy méretekben a hatékonyság csökkenését eredményezi. **Az Akamai Azure CDN-je** három területen optimalizálja ezt a problémát: 

 - [A TCP lassú indításának kiküszöbölése](#eliminating-tcp-slow-start)
 - [A tartós csatlakozások kihasználása](#leveraging-persistent-connections)
 - [TCP-csomagparamétereinek finomhangolása](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>A TCP lassú indításának kiküszöbölése

A TCP *lassú indítása* a TCP protokoll olyan algoritmusa, amely a hálózaton keresztül küldött adatok mennyiségének korlátozásával megakadályozza a hálózati torlódást. A küldő és a fogadó közötti kis torlódási ablakméretekkel kezdődik, amíg el nem éri a maximális értéket, vagy nem észleli a csomagvesztést.

 Mind **az Akamai azure-beli CDN,** mind a **Verizon-profilokból származó Azure CDN** kiküszöböli a TCP lassú kezdését a következő három lépéssel:

1. Az állapot- és sávszélesség-figyelés a peremhálózati PoP-kiszolgálók közötti kapcsolatok sávszélességének mérésére szolgál.
    
2. Metrikák vannak megosztva a peremhálózati PoP-kiszolgálók között, így minden kiszolgáló tisztában van a körülötte lévő többi POP hálózati feltételeivel és a kiszolgáló állapotával.  
    
3. A CDN peremhálózati kiszolgálók feltételezéseket tesznek bizonyos átviteli paraméterekről, például arról, hogy milyen optimális ablakméretnek kell lennie, amikor a közelben lévő más CDN peremhálózati kiszolgálókkal kommunikál. Ez a lépés azt jelenti, hogy a kezdeti torlódási ablak mérete növelhető, ha a CDN peremhálózati kiszolgálók közötti kapcsolat állapota nagyobb csomagadatátvitelre képes.  

#### <a name="leveraging-persistent-connections"></a>A tartós csatlakozások kihasználása

CdN használatával kevesebb egyedi gép csatlakozik közvetlenül az eredeti kiszolgálóhoz, mint a közvetlenül az ön forrásához csatlakozó felhasználók. Az Azure CDN is készletek felhasználói kérelmek együtt kevesebb kapcsolatot létesíteni az eredeti.

Ahogy korábban említettük, a TCP-kapcsolat létrehozásához több kézfogási kérelemre van szükség. A HTTP-fejléc által `Keep-Alive` megvalósított állandó kapcsolatok a meglévő TCP-kapcsolatokat több HTTP-kérelemhez is újra felhasználják az oda-vissza út mentéséhez és a kézbesítés felgyorsításához. 

**A Verizon Azure CDN-je** rendszeresen életre kelti a csomagokat a TCP-kapcsolaton keresztül, hogy megakadályozza a nyitott kapcsolat bezárását.

#### <a name="tuning-tcp-packet-parameters"></a>TCP-csomagparamétereinek finomhangolása

**Az Akamai Azure CDN-je** a kiszolgálók közötti kapcsolatokat szabályozó paramétereket hangolja, és a következő technikák alkalmazásával csökkenti a webhelybe ágyazott tartalom lekéréséhez szükséges hosszú távú adatváltások mennyiségét:

- A kezdeti torlódási ablak növelése, hogy több csomag küldhető el anélkül, hogy megkellene várnia a nyugtázást.
- A kezdeti újraküldési időtúltöltés csökkentése a veszteség észlelése és az újraküldés gyorsabb annektizálása érdekében.
- A minimális és maximális újraküldési időszám csökkentése a várakozási idő csökkentése érdekében, feltételezve, hogy a csomagok elvesztek az átvitelben.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Objektum előzetes betöltése (csak az Akamai-ból származó Azure CDN)

A legtöbb webhely egy HTML-lapból áll, amely különböző egyéb forrásokra, például képekre és parancsfájlokra hivatkozik. Amikor egy ügyfél weblapot kér, a böngésző először letölti és elemzi a HTML-objektumot, majd további kéréseket küld a csatolt eszközökhöz, amelyek a lap teljes betöltéséhez szükségesek. 

*Prefetch* egy olyan technika, hogy letölteni a képeket és parancsfájlokat ágyazott HTML oldalon, miközben a HTML szolgál a böngésző, és mielőtt a böngésző is teszi ezeket az objektum kérelmeket. 

Ha az előzetes betöltési lehetőség be van kapcsolva, amikor a CDN a HTML-alaplapot szolgálja ki az ügyfél böngészőjében, a CDN elemzi a HTML-fájlt, és további kéréseket küld a csatolt erőforrásokra vonatkozóan, és tárolja azt a gyorsítótárában. Amikor az ügyfél kéri a csatolt eszközök, a CDN peremhálózati kiszolgáló már rendelkezik a kért objektumokat, és azonnal kiszolgálhatja őket anélkül, hogy oda-vissza az eredeti. Ez az optimalizálás a gyorsítótárazható és a nem gyorsítótárazható tartalom számára is előnyös.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptív képtömörítés (csak az Akamai-ból származó Azure CDN)

Egyes eszközök, különösen a mobileszközök, időről időre lassabb hálózati sebességet tapasztalnak. Ezekben a helyzetekben előnyösebb a felhasználó számára, ha gyorsabban fogadja a kisebb képeket a weboldalán, ahelyett, hogy hosszú ideig várna a teljes felbontású képekre.

Ez a funkció automatikusan figyeli a hálózat minőségét, és szabványos JPEG tömörítési módszereket alkalmaz, ha a hálózati sebesség lassabb a szállítási idő növelése érdekében.

Adaptív képtömörítés | Fájlkiterjesztések  
--- | ---  
JPEG tömörítés | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Gyorsítótárazás

A DSA esetén a gyorsítótárazás alapértelmezés szerint ki van kapcsolva a CDN-en, még akkor is, ha az origó tartalmazza `Cache-Control` vagy `Expires` fejlécek a válaszban. A DSA általában olyan dinamikus eszközökhöz használatos, amelyeket nem szabad gyorsítótárazni, mert az egyes ügyfelek egyediek. A gyorsítótárazás megtörheti ezt a viselkedést.

Ha van egy weboldal vegyesen statikus és dinamikus eszközök, a legjobb, hogy egy hibrid megközelítést, hogy a legjobb teljesítményt. 

A **Verizon Azure CDN Standard** és az **Akamai-profilokból származó Azure CDN Standard** esetén gyorsítótárazást kapcsolhat be adott DSA-végpontokhoz [gyorsítótárazási szabályok](cdn-caching-rules.md)használatával.

A gyorsítótárazási szabályok elérése:

1. A **CDN-profil** lapon a Beállítások csoportban válassza a **Gyorsítótárazási szabályok lehetőséget.**  
    
    ![CDN-gyorsítótárszabályok gomb](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Megnyílik **a Gyorsítótárazási szabályok** lap.

2. Hozzon létre egy globális vagy egyéni gyorsítótárazási szabályt a DSA-végpont gyorsítótárazásának bekapcsolására. 

Csak **a Verizon-profilokból származó Azure CDN Premium** esetén a [szabálymotor](cdn-rules-engine.md)használatával kapcsolja be az adott DSA-végpontok gyorsítótárazását. A létrehozott szabályok csak a profil DSA-ra optimalizált végpontjait érintik. 

A szabálymotor elérése:
    
1. A **CDN-profil** lapon válassza a **Kezelés lehetőséget.**  
    
    ![CDN-profilkezelés gomb](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Megnyílik a CDN felügyeleti portál.

2. A CDN felügyeleti portálon válassza az **ADN**lehetőséget, majd válassza **a Szabálymotor lehetőséget.** 

    ![Szabályok motor DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Másik lehetőségként két CDN-végpontot is használhat: az egyik végpontot dsa-val optimalizálva dinamikus eszközök, a másik végpont statikus optimalizálási típussal optimalizálva, például általános webes kézbesítéssel, gyorsítótárazható eszközök kézbesítéséhez. Módosítsa a weblap URL-címeit úgy, hogy közvetlenül a használni kívánt CDN-végponton lévő eszközhöz kapcsolódjon. 

Például: `mydynamic.azureedge.net/index.html` egy dinamikus oldal, amely a DSA-végpontból töltődik be.A html-lap több statikus elemre hivatkozik, például JavaScript-függvénytárakra vagy `mystatic.azureedge.net/banner.jpg` `mystatic.azureedge.net/scripts.js`a statikus CDN-végpontról betöltött képekre, például a és a parancsra. 



