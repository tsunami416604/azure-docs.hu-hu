---
title: Peremhálózati csomópont teljesítményének elemzése az Azure CDN-ben | Microsoft dokumentumok
description: Peremhálózati csomópont teljesítményének elemzése a Microsoft Azure CDN-ben. Az Edge Performance Analytics részletes információforgalmat és sávszélesség-használatot biztosít a CDN számára.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b8a65d4ae6aaac78e642c851a66b745a940fa0ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593904"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Az élcsomópontok teljesítményének elemzése a Microsoft Azure CDN szolgáltatásban
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Az edge teljesítményelemzés részletes információforgalmat és sávszélesség-használatot biztosít a CDN számára. Ezeket az információkat ezután felkapott statisztikák készítésére használhatja, amelyek lehetővé teszik, hogy betekintést nyerjen az eszközök gyorsítótárba helyezésébe és az ügyfeleknek történő kézbesítésébe. Ez pedig lehetővé teszi, hogy stratégiát alakítson ki arra vonatkozóan, hogyan optimalizálhatja a tartalom kézbesítését, és hogyan határozhatja meg, hogy milyen kérdéseket kell kezelni a CDN jobb kihasználása érdekében. Ennek eredményeképpen nem csak javíthatja az adatkézbesítési teljesítményt, hanem csökkentheti a CDN költségeit is.

> [!NOTE]
> Minden jelentés UTC/GMT jelöléssel van elhasználva a dátum/idő megadásakor.
> 
> 

## <a name="reports-and-log-collection"></a>Jelentések és naplógyűjtés
A CDN-tevékenységi adatokat az Edge Performance Analytics modulnak kell gyűjtenie, mielőtt jelentéseket hozhatna létre róla. Ez a gyűjtési folyamat naponta egyszer történik, és az előző nap során végzett tevékenységet fedi le. Ez azt jelenti, hogy a jelentés statisztikái a napi statisztikák mintáját jelentik a feldolgozás időpontjában, és nem feltétlenül tartalmazzák az aktuális nap teljes adatkészletét. E jelentések elsődleges feladata a teljesítmény értékelése. Nem használhatók számlázási célokra vagy pontos numerikus statisztikákra.

> [!NOTE]
> A nyers adatok, amelyekből edge teljesítmény analitikus jelentések jönnek létre, legalább 90 napig elérhető.
> 
> 

## <a name="dashboard"></a>Irányítópult
Az Edge Performance Analytics irányítópultja nyomon követi az aktuális és a korábbi CDN-forgalmat egy diagramon és a statisztikákon keresztül. Ezzel az irányítópultgal észlelheti a fiók CDN-forgalmának teljesítményével kapcsolatos legújabb és hosszú távú trendeket.

Ez az irányítópult a következőkből áll:

* Interaktív diagram, amely lehetővé teszi a legfontosabb mutatók és trendek megjelenítését.
* Idővonal, amely a legfontosabb mutatók és trendek hosszú távú mintáit biztosítja.
* A cdn-hálózatunk általános teljesítmény, használat és hatékonyság alapján mért legfontosabb mérőszámok és statisztikai információk.

### <a name="accessing-the-edge-performance-dashboard"></a>A peremhálózati teljesítményirányítópult elérése
1. A CDN-profilpanelen kattintson a **Kezelés** gombra.
   
    ![CDN profilpanel kezelőgombja](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Mutasson az **Analytics** fülre, majd mutasson az **Edge Performance Analytics** úszó panelre.  Kattintson az **Irányítópult gombra.**
   
    Megjelenik a peremcsomópont-elemzési irányítópult.

### <a name="chart"></a>Diagram
Az irányítópult egy olyan diagramot tartalmaz, amely nyomon követi a közvetlenül alatta megjelenő idővonalon kiválasztott időszak metrikáját.  A CDN-tevékenység utolsó két évének grafikonjai közvetlenül a diagram alatt jelennek meg.

#### <a name="using-the-chart"></a>A diagram használata
* Alapértelmezés szerint az elmúlt 30 nap gyorsítótár-hatékonysági aránya lesz ábrázolva.
* Ez a diagram napi szinten összeállított adatokból jön létre.
* Ha a vonaldiagramon egy nap fölé viszi az egérmutatót, akkor a dátum és a mutató értéke az adott napon jelenik meg.
* Kattintson a Hétvégék kiemelése gombra a hétvégéket jelképező világosszürke függőleges sávok átfedésének a diagramra való bekapcsolásához. Ez a fajta átfedés hasznos a forgalmi minták azonosításához a hétvégén.
* Kattintson az Egy évvel ezelőtti nézet elemre, ha az előző év tevékenységének átfedését ugyanebben az időszakban a diagramra szeretné váltani. Ez a fajta összehasonlítás betekintést nyújt a hosszú távú CDN-használati minták. A diagram jobb felső sarokja egy jelmagyarázatot tartalmaz, amely az egyes vonaldiagramok színkódját jelzi.

#### <a name="updating-the-chart"></a>A diagram frissítése
* Időtartomány: Hajtsa végre az alábbi műveletek egyikét:
  * Válassza ki a kívánt területet az idővonalon. A diagram a kiválasztott időszaknak megfelelő adatokkal frissül.
  * Kattintson duplán a diagramra az összes rendelkezésre álló előzményadat megjelenítéséhez legfeljebb két évig.
* Mérőszám: Kattintson a diagram ikonjára, amely a kívánt mutató mellett jelenik meg. A diagram és az ütemterv frissül a megfelelő metrika adataival.

### <a name="key-metrics-and-statistics"></a>Főbb mutatók és statisztikák
#### <a name="efficiency-metrics"></a>Hatékonysági mutatók
Ezek a metrikák célja, hogy lássa, hogy a gyorsítótár hatékonyságát lehet-e javítani. A gyorsítótár hatékonyságából származó fő előnyök a következők:

* Csökkentett terhelés az eredeti kiszolgálón, ami a következőkhöz vezethet:
  * Jobb webszerver teljesítmény.
  * Csökkentett működési költségek.
* Továbbfejlesztett adatkézbesítési gyorsítás, mivel több kérés lesz közvetlenül a CDN-ből kézbesítve.

| Mező | Leírás |
| --- | --- |
| Gyorsítótár hatékonysága |A gyorsítótárból kiszolgált adatok százalékos arányát jelzi. Ez a mérőszám azt méri, hogy a kért tartalom gyorsítótárazott verziója közvetlenül a CDN-ből (peremhálózati kiszolgálókról) került-e ki a kérelmezőknek (pl. webböngésző) |
| Találati arány |A gyorsítótárból kiszolgált kérelmek százalékos arányát jelzi. Ez a mérőszám azt méri, hogy a kért tartalom gyorsítótárazott verziója közvetlenül a CDN-ből (peremhálózati kiszolgálókról) került-e kia késedelmi kiszolgálókról a kérelmezőknek (pl. webböngésző). |
| A távoli bájtok %-a – nincs gyorsítótár-konfiguráció |Azt jelzi, hogy az eredeti kiszolgálókról a CDN-kiszolgálókra (peremhálózati kiszolgálókra) kiszolgált forgalom hány százalékát nem gyorsítótárazható a gyorsítótár megkerülése szolgáltatás (HTTP rules engine). |
| A távoli bájtok %-a – lejárt gyorsítótár |Azt jelzi, hogy az elavult tartalom-újraérvényesítés következtében az eredeti kiszolgálókról a CDN-kiszolgálókra (peremhálózati kiszolgálókra) kiszolgált forgalom hány százalékát szolgálták ki. |

#### <a name="usage-metrics"></a>Használati metrikák
Ezeknek a mutatóknak az a célja, hogy betekintést nyújtsanak a következő költségcsökkentő intézkedésekbe:

* A működési költségek minimalizálása a CDN-en keresztül.
* A CDN-kiadások csökkentése a gyorsítótár hatékonysága és tömörítése révén.

> [!NOTE]
> A forgalomvolumen-számok olyan forgalmat jelentenek, amelyet az arányok és százalékok számításában használtak, és előfordulhat, hogy a nagy volumenű ügyfelek teljes forgalmának csak egy részét mutatják.
> 
> 

| Mező | Leírás |
| --- | --- |
| Ave byteout |A CDN-ről (peremhálózati kiszolgálókról) a kérelmezőnek (pl. webböngésző) kiszolgált kérelmekhez átvitt bájtok átlagos számát jelzi. |
| Nincs gyorsítótár-konfigurációs bájtsebesség |Azt jelzi, hogy a CDN (peremhálózati kiszolgálók) által a kérelmezőnek (pl. webböngésző) kiszolgált forgalom hány százaléka lesz gyorsítótárazva a Gyorsítótár megkerülése szolgáltatás miatt. |
| Tömörített bájtsebesség |A CDN (peremhálózati kiszolgálók) által a kérelmezőknek (pl. webböngésző) tömörített formátumban küldött forgalom százalékos arányát jelzi. |
| Kikötött bájtok |A CDN-ről (peremhálózati kiszolgálókról) a kérelmezőnek (pl. webböngésző) szállított adatok bájtban megadott mennyiségét jelzi. |
| Bájt |A kérelmezőktől (pl. webböngésző) a CDN-kiszolgálóknak küldött adatok bájtban megadott mennyiségét jelzi bájtban. |
| Távoli bájtok |A CDN-kiszolgálókról és az ügyfél eredeti kiszolgálóiról a CDN-kiszolgálókra küldött adatok bájtban megadott mennyiségét jelzi bájtban. |

#### <a name="performance-metrics"></a>Teljesítmény-metrikák
Ezek a metrikák célja, hogy nyomon kövesse a teljes CDN teljesítményét a forgalom.

| Mező | Leírás |
| --- | --- |
| Átviteli árfolyam |Azt az átlagos sebességet jelzi, amellyel a tartalom átkerült a CDN-ről a kérelmezőhöz. |
| Időtartam |Azt az átlagos időt jelzi, hogy az eszköz átlagosan ezredmásodpercben (például webböngésző) szállított a kérelmezőnek. |
| Tömörített kérelmek aránya |A CDN -ből (peremhálózati kiszolgálókról) a kérelmezőnek (pl. webböngésző) tömörített formátumban szállított találatok százalékos arányát jelzi. |
| 4xx hibaarány |A 4xx állapotkódot létrehozó találatok százalékos arányát jelzi. |
| 5xx hibaarány |Az 5xx állapotkódot létrehozó találatok százalékos arányát jelzi. |
| Találatok |A CDN-tartalomra vonatkozó kérelmek számát jelzi. |

#### <a name="secure-traffic-metrics"></a>Biztonságos forgalmi mutatók
Ezek a metrikák célja a CDN-teljesítmény https-forgalom nyomon követése.

| Mező | Leírás |
| --- | --- |
| Biztonságos gyorsítótár-hatékonyság |A gyorsítótárból kiszolgált HTTPS-kérelmek átvitelének százalékos arányát jelzi. Ez a mérőszám azt méri, hogy a kért tartalom gyorsítótárazott verziója közvetlenül a CDN-ről (peremhálózati kiszolgálókról) került-e ki a kérelmezőknek (pl. webböngésző) HTTPS-kapcsolaton keresztül. |
| Biztonságos átviteli sebesség |Azt jelzi, hogy a CDN-kiszolgálókról (peremhálózati kiszolgálókról) átlagosan milyen sebességgel került át a tartalom a kérelmezőknek (pl. webkiszolgálóknak) HTTPS-kapcsolaton keresztül. |
| Átlagos biztonságos időtartam |Azt az átlagos időt jelzi, hogy az átlagos idő ezredmásodpercben az eszköz HTTPS-en keresztül történő kézbesítéséhez került. |
| Biztonságos találatok |A CDN-tartalomhttps-kérelmeinek számát jelzi. |
| Biztonságos bájtok ki |A CDN-ről (peremhálózati kiszolgálókról) a kérelmezőnek (például webböngészőnek) kézbesített HTTPS-forgalom bájtban történő mennyiségét jelzi. |

## <a name="reports"></a>Jelentések
Ebben a modulban minden jelentés tartalmaz egy diagramot és statisztikákat a különböző típusú metrikák (pl. HTTP állapotkódok, gyorsítótár állapotkódok, kérelem URL-je stb.) sávszélességére és forgalomhasználatára vonatkozóan. Ezeket az információkat arra használhatják fel, hogy mélyebben átmélyedjenek a tartalom ügyfelek nek történő kiszolgálásában, és finomítsák a CDN viselkedését az adatkézbesítési teljesítmény javítása érdekében.

### <a name="accessing-the-edge-performance-reports"></a>A peremhálózati teljesítményjelentések elérése
1. A CDN-profilpanelen kattintson a **Kezelés** gombra.
   
    ![CDN profilpanel kezelőgombja](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Mutasson az **Analytics** fülre, majd mutasson az **Edge Performance Analytics** úszó panelre.  Kattintson a **HTTP Large Object elemre.**
   
    Megjelenik a peremcsomópont-elemzési jelentések képernyője.

| Jelentés | Leírás |
| --- | --- |
| Napi összegzés |Lehetővé teszi a napi forgalmi trendek megtekintését egy adott időszakban. A diagram minden sávja egy adott dátumot jelöl. A sáv mérete az adott napon történt találatok teljes számát jelzi. |
| Óránkénti összegzés |Lehetővé teszi az óránkénti forgalmi trendek megtekintését egy adott időszakban. A diagram minden sávja egy adott dátumon egy órát jelöl. A sáv mérete az adott órában történt találatok teljes számát jelzi. |
| Protokollok |A HTTP és HTTPS protokollok közötti forgalom bontását jeleníti meg. A fánkdiagram azt jelzi, hogy az egyes protokolltípusok esetében hány találat történt. |
| HTTP-metódusok |Lehetővé teszi, hogy gyorsan megtudja, hogy mely HTTP-módszereket használják az adatok kéréséhez. A leggyakoribb HTTP-kérelemmódszerek a GET, a HEAD és a POST. A fánkdiagram azt jelzi, hogy az egyes HTTP-kérelemmetódusok esetében hány találat történt. |
|  URL-címek |Egy grafikont tartalmaz, amely a 10 legkeresettebb URL-t jeleníti meg. Minden URL-címhez megjelenik egy sáv. A sáv magassága azt jelzi, hogy az adott URL hány találatot generált a jelentés által lefedett időtartam alatt. A 100 legkeresettebb URL statisztikája közvetlenül a grafikon alatt jelenik meg. |
| CNAMES között |Egy grafikont tartalmaz, amely a jelentés időtartama alatt az eszközök igényléséhez használt 10 legfontosabb cname-t jeleníti meg. A 100 legkeresettebb CNAME statisztikája közvetlenül a grafikon alatt jelenik meg. |
| Eredete |Egy grafikont tartalmaz, amely azt a 10 legfontosabb CDN- vagy ügyfél-eredetű kiszolgálót jeleníti meg, amelyektől az eszközöket egy adott időszakban kérték. A 100 legkeresettebb CDN- vagy ügyfél-forráskiszolgáló statisztikája közvetlenül a grafikon alatt jelenik meg. Az ügyfél eredetkiszolgálóit a Címtárnév beállításban megadott név azonosítja. |
| Geo PO-k |Megmutatja, hogy a forgalom mekkora része kerül egy adott jelenléti ponton (POP) keresztül. A hárombetűs rövidítés egy POP-ot jelöl a CDN-hálózatunkban. |
| Ügyfelek |Egy grafikont tartalmaz, amely a 10 legfontosabb ügyfelet jeleníti meg, amelyek egy adott időszakban eszközöket kértek. A jelentés alkalmazásában minden, ugyanattól az IP-címről származó kérelem ugyanattól az ügyféltől származik. A top 100 ügyfél statisztikái közvetlenül a grafikon alatt jelennek meg. Ez a jelentés hasznos a letöltési tevékenység minták meghatározásához a legjobb ügyfelek számára. |
| Gyorsítótár állapotai |Részletes bontást ad a gyorsítótár viselkedéséről, amely felfedheti a teljes végfelhasználói élmény javításának megközelítéseit. Mivel a leggyorsabb teljesítmény a gyorsítótár-találatokból származik, optimalizálhatja az adatátviteli sebességet a gyorsítótár-tévesztések és a lejárt gyorsítótár-találatok minimalizálásával. |
| NONE Részletek |Egy grafikont tartalmaz, amely az eszközök 10 legfontosabb URL-jét jeleníti meg, amelyek gyorsítótárazási tartalmának frissességét egy adott időszakban nem ellenőrizték. Az ilyen típusú eszközök első 100 URL-jének statisztikái közvetlenül a grafikon alatt jelennek meg. |
| CONFIG_NOCACHE részletek |Egy grafikont tartalmaz, amely az ügyfél CDN-konfigurációja miatt nem gyorsítótárazott eszközök 10 legfontosabb URL-jét jeleníti meg. Az ilyen típusú eszközöket közvetlenül az eredeti kiszolgálóról szolgálták ki. Az ilyen típusú eszközök első 100 URL-jének statisztikái közvetlenül a grafikon alatt jelennek meg. |
| Nem gyorsítótárazható részletek |Egy grafikont tartalmaz, amely a fejlécadatok miatt nem gyorsítótárazható eszközök 10 legfontosabb URL-címét jeleníti meg. Az ilyen típusú eszközök első 100 URL-jének statisztikái közvetlenül a grafikon alatt jelennek meg. |
| TCP_HIT Részletek |Egy grafikont tartalmaz, amely a gyorsítótárból azonnal kiszolgált eszközök 10 legfontosabb URL-jeit jeleníti meg. Az ilyen típusú eszközök első 100 URL-jének statisztikái közvetlenül a grafikon alatt jelennek meg. |
| TCP_MISS részletek |Egy grafikont tartalmaz, amely a gyorsítótár TCP_MISS állapotú eszközök első 10 URL-t jeleníti meg. Az ilyen típusú eszközök első 100 URL-jének statisztikái közvetlenül a grafikon alatt jelennek meg. |
| TCP_EXPIRED_HIT Részletek |Egy grafikont tartalmaz, amely a közvetlenül a POP-ból kiszolgált elavult eszközök első 10 URL-jét jeleníti meg. Az ilyen típusú eszközök első 100 URL-jének statisztikái közvetlenül a grafikon alatt jelennek meg. |
| TCP_EXPIRED_MISS Részletek |Egy grafikont tartalmaz, amely az elavult eszközök első 10 URL-jét jeleníti meg, amelyekhez új verziót kellett beolvasni az eredeti kiszolgálóról. Az ilyen típusú eszközök első 100 URL-jének statisztikái közvetlenül a grafikon alatt jelennek meg. |
| TCP_CLIENT_REFRESH_MISS részletek |Egy sávdiagramot tartalmaz, amely az ügyfél gyorsítótárnélküli kérése miatt az eredeti kiszolgálóról lekért eszközök első 10 URL-címét jeleníti meg. Az ilyen típusú kérelmek első 100 URL-jének statisztikái közvetlenül a diagram alatt jelennek meg. |
| Ügyfélkérelem-típusok |A HTTP-ügyfelek (például böngészők) által benyújtott kérelmek típusát jelzi. Ez a jelentés egy fánkdiagramot tartalmaz, amely képet ad a kérések kezelésének módjáról. Az egyes kérelemtípusok sávszélesség- és forgalmi információi a diagram alatt jelennek meg. |
| Felhasználói ügynök |Tartalmaz egy sávos grafikont, amely a 10 legjobb felhasználói ügynököt jeleníti meg, hogy a CDN-en keresztül kérje a tartalmat. A felhasználói ügynök általában webböngésző, médialejátszó vagy mobiltelefon-böngésző. A top 100 felhasználói ügynök statisztikája közvetlenül a diagram alatt jelenik meg. |
| Utalt |Egy sávdiagramot tartalmaz, amely a CDN-en keresztül elért tartalom első 10 hivatkozóját jeleníti meg. A hivatkozó általában annak a weboldalnak vagy erőforrásnak az URL-címe, amely a tartalomra hivatkozik. Részletes információk találhatók a top 100 hivatkozó grafikonja alatt. |
| Tömörítési típusok |Egy fánkdiagramot tartalmaz, amely lebontja a kért eszközöket, függetlenül attól, hogy azokat a peremhálózati kiszolgálóink tömörítették-e. A tömörített eszközök százalékos aránya a használt tömörítés típusa szerint van lebontva. Részletes információk találhatók az egyes tömörítési típusokés állapotok grafikonja alatt. |
| Fájltípusok |Tartalmaz egy sávdiagramot, amely megjeleníti a 10 legfontosabb fájltípust, amelyet a CDN-en keresztül kért a fiókjához. A jelentés alkalmazásában a fájltípust az eszköz fájlnévkiterjesztése és internetes adathordozó-típusa határozza meg \[(pl., .html \[text/html\]\], .htm\]text/html , .aspx \[text/html stb.). Részletes információ a top 100 fájltípus grafikonja alatt található. |
| Egyedi fájlok |Egy grafikont tartalmaz, amely egy adott napon egy adott napon egy adott időszakban kért egyedi eszközök teljes számát tartalmazza. |
| Token hitelesítésének összegzése |Kördiagramot tartalmaz, amely gyors áttekintést nyújt arról, hogy a kért eszközöket jogkivonatalapú hitelesítés védi-e. A védett eszközök a hitelesítési kísérlet eredményei nek megfelelően jelennek meg a diagramon. |
| Token hitelesítési adatok |Egy sávdiagramot tartalmaz, amely lehetővé teszi a token alapú hitelesítés miatt elutasított 10 kérelem megtekintését. |
| HTTP-válaszkódok |Lebontja azokat a HTTP-állapotkódokat (pl. 200 OK, 403 Tiltott, 404 nem található stb.), amelyeket a peremhálózati kiszolgálóink szállítottak a HTTP-ügyfeleinek. A kördiagram segítségével gyorsan felmérheti az eszközök kiszolgálását. A részletes statisztikai adatok a grafikon alatti válaszkódokra vonatkozóan találhatók. |
| 404 hibák |Sávdiagramot tartalmaz, amely lehetővé teszi a 404-es válaszkód nak a megtekintését a 10 legfontosabb kérelem megtekintésére. |
| 403 hibák |Egy sávdiagramot tartalmaz, amely lehetővé teszi a 403-as tiltott válaszkódot eredményező első 10 kérelem megtekintését. A 403 Tiltott válaszkód akkor fordul elő, amikor egy ügyfél származási kiszolgálója vagy a POP-kiszolgáló peremhálózati kiszolgálója visszautasít egy kérést. |
| 4xx hibák |Sávdiagramot tartalmaz, amely lehetővé teszi a 400-as tartomány válaszkódjának megtekintését eredményező első 10 kérelem megtekintését. A jelentésből ki van zárva a 403 nem található és a 404 Tiltott válaszkód. A 4xx válaszkód általában akkor fordul elő, ha egy kérést ügyfélhiba miatt megtagadnak. |
| 504 hibák |Sávdiagramot tartalmaz, amely lehetővé teszi az 504-es átjáró időmegfordítási válaszkódjának megtekintését. Az 504-es átjáró időkimenő válaszkódja akkor fordul elő, amikor időbeli meghosszabbítás történik, amikor egy HTTP-proxy egy másik kiszolgálóval próbál kommunikálni. A CDN esetében az 504-es átjáró időkitöltési válaszkódja általában akkor fordul elő, ha egy peremhálózati kiszolgáló nem tud kapcsolatot létesíteni az ügyfél származási kiszolgálójával. |
| 502 hibák |Sávdiagramot tartalmaz, amely lehetővé teszi az 502-es rossz átjáró válaszkódjának megtekintését a 10 legfontosabb kérelem megtekintésére. Az 502-es rossz átjáró válaszkódja akkor fordul elő, ha egy HTTP protokoll hiba lép fel egy kiszolgáló és egy HTTP-proxy között. A CDN esetében az 502-es hibás átjáró válaszkódáltalában akkor fordul elő, amikor egy ügyfél származási kiszolgálója érvénytelen választ ad vissza egy peremhálózati kiszolgálónak. A válasz érvénytelen, ha nem lehet elemezni, vagy ha hiányos. |
| 5xx hibák |Sávdiagramot tartalmaz, amely lehetővé teszi a 10 legfontosabb kérelem megtekintését, amelyek az 500-as tartomány válaszkódját eredményezték.  A jelentésből nem tartozik az 502 Bad Gateway és az 504 Gateway időout-válaszkód. |

## <a name="see-also"></a>Lásd még
* [Azure tartalomkézbesítési hálózat (CDN) – áttekintés](cdn-overview.md)
* [Valós idejű statisztikák a Microsoft Azure CDN-ben](cdn-real-time-stats.md)
* [Az alapértelmezett HTTP-viselkedés felülbírálása a szabálymotor használatával](cdn-rules-engine.md)
* [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)

