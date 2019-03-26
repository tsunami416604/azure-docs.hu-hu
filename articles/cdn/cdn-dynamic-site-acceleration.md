---
title: Azure CDN-nel dinamikuswebhely-gyorsítás
description: Az Azure CDN dinamikuswebhely-gyorsításként (DSA) optimalizálási támogatja a dinamikus tartalmat tartalmazó fájlok.
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
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: 6bd1d24cdece91265a7355678ea2bc0b0f9e3910
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439158"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Azure CDN-nel dinamikuswebhely-gyorsítás

A közösségi média, elektronikus kereskedelmi és a hyper-személyre szabott webes robbanásszerűen egyre növekvő százalékos arányát a végfelhasználók számára a tartalmak valós időben jön létre. Többek között elvárják egy gyors, megbízható és személyre szabott webes felület, a böngésző, helyről, eszközről vagy hálózati független. Azonban a rendkívül innovációkat, amelyek így vonzó is ezek a tapasztalatok lassú oldal letöltéseket, és veszélynek a felhasználói élmény minősége szempontjából. 

Standard szintű content delivery network (CDN) képesség magában foglalja a statikus fájlok kézbesítésének felgyorsítása érdekében gyorsítótárfájlok közelebb a végfelhasználók számára lehetővé teszi. Azonban a dinamikus webes alkalmazások peremhálózati helyeken tartalom gyorsítótárazása nem lehetséges, mert a kiszolgáló válaszul a felhasználók viselkedésének a tartalmat állít elő. Az ilyen tartalmak kézbesítésének felgyorsítása bonyolultabb, mint a hagyományos peremhálózati gyorsítótár, és egy teljes körű megoldás, amely a beállítás a teljes elérési útját a tervezéstől a kézbesítési mentén az egyes elemek igényel. Az Azure CDN dinamikuswebhely-gyorsításként (DSA) optimalizálása a teljesítmény, a dinamikus tartalmú weblapok kimutathatóan javult.

**Az Azure CDN az Akamaitól** és **verizon Azure CDN** mindkettő lehetővé teszi a DSA-optimalizálás keresztül a **optimalizált** menü végpont létrehozása közben. A Microsoft dinamikuswebhely-gyorsítás keresztül érhető el [Azure bejárati ajtajának Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

> [!Important]
> A **Akamai Azure CDN** -profilok, átválthatnak optimalizálása CDN-végpont létrehozása után.
>   
> A **Verizon Azure CDN** típusú profilok esetén nem módosíthatja a CDN-végpont optimalizálását a végpont létrehozása után.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>A dinamikus fájlok kézbesítésének gyorsítását CDN végpont-konfiguráció

Adja meg a dinamikus fájlokat a kézbesítés optimalizálása CDN-végpont, vagy használhatja az Azure Portalon, a REST API-k, vagy az ügyfél SDK-k amely programozottan hajtja végre ugyanezt. 

**CDN-végpont, a DSA optimalizálásához konfigurálása az Azure portal használatával:**

1. Az a **CDN-profil** lapon jelölje be **végpont**.

   ![Új CDN-végpont hozzáadása](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Megjelenik a **Végpont hozzáadása** panel.

2. A **optimalizált**válassza **dinamikuswebhely-gyorsítás**.

    ![A DSA egy új CDN-végpont létrehozása](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. A **mintavétel elérési útját**, adjon meg érvényes elérési utat egy fájlba.

    Mintavétel elérési útját DSA-funkció, és a egy érvényes elérési út megadása kötelező a létrehozásához. DSA használ egy kis *mintavétel elérési útját* optimalizálható a hálózat útválasztási konfigurációi a CDN a forráskiszolgáló fájlt helyezni. A mintavétel elérési út fájl letöltése és a minta-fájl feltöltése a helyhez, vagy egy meglévő eszközt használja a forrás, amely körülbelül 10 KB-os méretig.

4. Adja meg a többi szükséges endpoint lehetőség (további információkért lásd: [hozzon létre egy új CDN-végpont](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), majd **Hozzáadás**.

   A CDN-végpont létrehozása után az összes fájl, amely megfelel bizonyos feltételeknek DSA optimalizálásokat vonatkozik. 


**Egy meglévő végpontot konfigurálása a DSA (az Azure CDN az Akamai-profilok csak):**

1. Az a **CDN-profil** lapon, válassza ki a módosítani kívánt végpontot.

2. A bal oldali panelen válassza ki a **optimalizálási**. 

   A **optimalizálási** lap jelenik meg.

3. Alatt **optimalizált**válassza **dinamikuswebhely-gyorsítás**, majd **mentése**.

> [!Note]
> DSA többletköltségek tekintetében. További információkért lásd: [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>DSA optimalizálása az Azure CDN szolgáltatás használata

Dinamikuswebhely-gyorsítási szolgáltatásával az Azure CDN felgyorsítja a kézbesítési dinamikus eszközök, az alábbi technikák segítségével:

-   [Irányíthatja az optimalizációt](#route-optimization)
-   [TCP-optimalizálást](#tcp-optimizations)
-   [(Csak Akamai Azure CDN) objektumbetöltés](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptív Képtömörítés (csak Akamai Azure CDN)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Irányíthatja az optimalizációt

Útvonal-optimalizálás fontos, mert az interneten a dinamikus hely, ahol forgalmat, és átmenetileg üzemkimaradások utáni helyreállításon folyamatosan változnak a hálózati topológiát. A Border Gateway Protocol (BGP) az útválasztási protokoll, az Internet, de előfordulhat, hogy gyorsabban útvonalak jelenléti pontok (PoP) köztes kiszolgálókon keresztül. 

Útvonal-optimalizálás úgy dönt, hogy az optimális a forrás elérési útját, hogy egy hely folyamatosan elérhetők maradnak, és a dinamikus tartalom kézbesíti a rendszer a lehető leggyorsabb és legmegbízhatóbb útvonal-n keresztül a végfelhasználók számára. 

Az Akamai hálózati módszereket használ a valós idejű adatokat gyűjthet, és hasonlítsa össze a különböző útvonalait a csomópontjai az Akamai-kiszolgáló, valamint az alapértelmezett BGP-útvonalat az a forrás és a CDN közötti leggyorsabb útvonalat a nyílt interneten keresztül. Ezek a technológiák elkerülése érdekében az Internet torlódás pontok és a hosszú útvonalakat. 

Hasonlóképpen a Verizon hálózati csomópontválasztásos DNS kombinációját használja, nagy kapacitású támogatja a POP, és állapot-ellenőrzések meghatározása az ügyfél és a forrás legjobb útvonal adatai a legjobb átjárók.
 
Ennek eredményeképpen teljesen dinamikus és tranzakciós tartalmát kézbesíti a rendszer gyorsabban és megbízhatóbb módon eljuttassa a végfelhasználók számára, akkor is, ha uncacheable. 

### <a name="tcp-optimizations"></a>TCP-optimalizálást

Transmission Control Protocol (TCP), a standard szintű, az Internet protocol Suite, hogy az IP-hálózat az alkalmazások között használt.  Alapértelmezés szerint több vissza oda-kérést szükségesek hálózati congestions, ami nagy mennyiségű hatékonysági elkerülése érdekében a TCP-kapcsolat, valamint a korlátok beállításához. **Az Azure CDN az Akamaitól** kezeli a probléma hibamegoldás a három terület: 

 - [Távolítsa el az indexkulcsból TCP lassú indítása](#eliminating-tcp-slow-start)
 - [Állandó kapcsolatot kihasználva](#leveraging-persistent-connections)
 - [TCP-csomag paraméter hangolása](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Távolítsa el az indexkulcsból TCP lassú indítása

TCP *kezdő lassú* algoritmus a TCP protokoll, amely megakadályozza, hogy a hálózati torlódás, ha a hálózaton keresztül küldött adatok mennyisége korlátozza. Akkor kezdődik, kis torlódás méretek küldő és fogadó között mindaddig, amíg eléri a maximális vagy csomagvesztés észlel.

 Mindkét **Akamai Azure CDN** és **verizon Azure CDN** profilok kiküszöbölése az alábbi három lépést TCP lassú Kezdés:

1. Állapotát és a sávszélesség-figyelés segítségével az edge PoP kiszolgálók közötti kapcsolatot a sávszélesség mérését.
    
2. Metrikák edge PoP kiszolgálók között vannak megosztva, úgy, hogy minden kiszolgáló a hálózati feltételek és a többi kapcsolódási stran kiszolgáló állapotát.  
    
3. A CDN peremhálózati kiszolgálókon győződjön feltételezéseket néhány átviteli paraméterek, például az optimális ablakméret lennie más helyezkedik CDN edge kiszolgálókkal való kommunikációhoz. Ez a lépés azt jelenti, hogy a kezdeti torlódás ablak mérete növelhető, ha a CDN edge-kiszolgáló közötti kapcsolat állapotát is képes magasabb szintű csomag adatforgalom.  

#### <a name="leveraging-persistent-connections"></a>Állandó kapcsolatot kihasználva

CDN használata kevesebb egyedi számítógépek csatlakozhat a forráskiszolgáló közvetlenül a felhasználókkal a forráshoz való közvetlen csatlakozás lehetőségét képest. Az Azure CDN-készletek együtt, kevesebb kapcsolatok a forrás a felhasználói kérések is.

Ahogy korábban említettük a TCP-kapcsolatot létesítsen a kézfogás több kérést szükség van. Állandó kapcsolatot, azok a `Keep-Alive` HTTP-fejléc, az újbóli meglévő TCP-kapcsolatok üzenetváltási idő mentéséhez és a szállítási felgyorsítása több HTTP-kérelmekre. 

**Az Azure CDN a Verizontól** is elküldi a rendszeres életben tartási csomagok, hogy a rendszer bezárja a kapcsolat nyitva a TCP-kapcsolaton keresztül.

#### <a name="tuning-tcp-packet-parameters"></a>TCP-csomag paraméter hangolása

**Az Azure CDN az Akamaitól** hangolja a paramétereket, amelyek szabályozzák a kiszolgálók közötti kapcsolatok, és csökkenti az az alábbi technikák segítségével a hely a beágyazott tartalom beolvasásához szükséges profitálni adatváltások:

- A kezdeti torlódás ablak növelése, így további csomagokat is küldhetők nyugtázásra való várakozás nélkül.
- A kezdeti újraküldési időkorlátja csökkentésével, így adatvesztést észlelt, és az újraküldési hamarabb következik be.
- A minimális és maximális újraküldési időkorlátja, feltéve, hogy a csomagok elvesztek az átvitel előtt a várakozási idő csökkentése érdekében csökkentésével.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>(Csak Akamai Azure CDN) objektumbetöltés

A legtöbb webhelyek egy HTML-oldalt, amely hivatkozik a különböző más erőforrások, például a képek és parancsfájlok állnak. Általában amikor egy ügyfél egy weblap, a böngésző először letölti és elemzi a HTML-objektum, és ezután hajt végre további kérések csatolt eszközök teljes mértékben a lap betöltéséhez szükséges. 

*Előzetes betöltési* lekérdezni a képek és parancsfájlok technika beágyazott a HTML-oldalt a HTML-kódot a böngészőbe kiszolgált közben, és mielőtt a böngésző még akkor is lehetővé teszi a kérések objektum. 

A lehívott lehetőséget időpontjában, amikor a CDN-t szolgálja ki a alap HTML-oldalt, az ügyfélböngészőnek kapcsolva, az a CDN-t elemzi a HTML-fájlt, és további kérelmeket, és a társított erőforrásokat, és a gyorsítótárban tárolhatja. Amikor az ügyfél a kérelmeket a csatolt eszközök, a CDN peremhálózati kiszolgáló már a kért objektumok, és szolgálhat őket közvetlenül a forrásnak adatváltási nélkül. Az optimalizálás számos előnyt biztosít, és a nem gyorsítótárazható, mind a gyorsítótárazható tartalom.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptív Képtömörítés (csak Akamai Azure CDN)

Bizonyos eszközök, különösen a mobil eszközök időről időre élmény lassabb hálózati átviteli sebességek esetén. Ezekben a forgatókönyvekben akkor több előnyös, a teljes felbontású képeket kell hosszú ideig tartó várakozás helyett a felhasználót, hogy azok a weblapon kisebb lemezképek gyorsabban kapja.

Ez a funkció automatikusan figyeli a hálózati minőségét, és JPEG tömörítési szabványos módszerek használ, lassabb, csökkenthetik a tartalomkézbesítési hálózati sebességek esetén.

Adaptív Képtömörítés | Fájlkiterjesztések  
--- | ---  
JPEG-tömörítés | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Gyorsítótárazás

A DSA, gyorsítótárazása ki van kapcsolva a CDN-en, alapértelmezés szerint akkor is, ha a forrás tartalmaz `Cache-Control` vagy `Expires` fejlécek a válaszban. DSA általában nem érdemes gyorsítótárazni, mivel azok az egyes ügyfelek egyedi dinamikus eszközök szolgál. Gyorsítótárazás tönkretehetik ezt a viselkedést.

Ha rendelkezik a statikus és dinamikus eszközök vegyesen webhellyel, célszerű igénybe vehet a hibrid megközelítés a legjobb teljesítmény. 

A **Azure CDN Standard verizon** és **Azure CDN Akamai Standard** profilok, bekapcsolhatja az adott DSA végpontok gyorsítótárazás [gyorsítótár-szabályokkal](cdn-caching-rules.md).

Gyorsítótárazási szabályok elérése:

1. Az a **CDN-profil** beállítások területén válassza a lap **gyorsítótár-szabályokkal**.  
    
    ![CDN-gyorsítótárszabályok gomb](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    A **gyorsítótár-szabályokkal** lap megnyitásakor.

2. Hozzon létre egy globális vagy egyéni gyorsítótárazási szabályt, a DSA-végpont gyorsítótárazás bekapcsolása. 

A **verizon Azure CDN Premium** profilok csak, kapcsolja be az adott DSA végpontok gyorsítótárazás a [szabálymotorral](cdn-rules-engine.md). Létrehozott szabályok csak azokat a végpontokat a profilhoz DSA optimalizált hatással. 

A rules engine elérése:
    
1. Az a **CDN-profil** lapon jelölje be **kezelés**.  
    
    ![CDN-profil kezelése gomb](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Megnyílik a CDN felügyeleti portálját.

2. Válassza ki a CDN felügyeleti portálját, **ADN**, majd **Szabálymotorral**. 

    ![A DSA szabálymotor](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Másik lehetőségként használhatja a két CDN-végpontok: egy végpontot optimalizált és DSA, hogy a dinamikus eszközök és a egy másik végpont optimalizált és a egy statikus optimalizálás típusa, például az általános webes kézbesítés kézbesítési gyorsítótárazható eszközökhöz. A weblap URL-címek az eszköz a CDN-végpont használatát tervezi a közvetlen hivatkozás módosítása 

Például: `mydynamic.azureedge.net/index.html` egy dinamikus lapja és betölti a DSA-végpontról.  A html-oldalt több statikus objektumokat, például a JavaScript-kódtárakat vagy a statikus CDN-végpontról, például a betöltött képek hivatkozik `mystatic.azureedge.net/banner.jpg` és `mystatic.azureedge.net/scripts.js`. 



