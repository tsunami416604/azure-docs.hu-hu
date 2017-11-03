---
title: "Az Azure CDN élcsomópontok teljesítményének elemzése |} Microsoft Docs"
description: "A Microsoft Azure CDN élcsomópontok teljesítményének elemzése. Peremhálózati teljesítmény Analytics biztosít a CDN részletes információkat forgalom és a sávszélesség-használat."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: ad285b4e2226c85859acb22ba214cc44c77c08e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Az élcsomópontok teljesítményének elemzése a Microsoft Azure CDN szolgáltatásban
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Peremhálózati teljesítmény analytics biztosít a CDN részletes információkat forgalom és a sávszélesség-használat. Ez az információ majd trendekkel statisztika, melyek lehetővé teszik a hogyan folyamatban van-e az Ön eszközeit végzett létrehozásához használható a gyorsítótárban, és az ügyfelek felé. Viszont ez lehetővé teszi, hogy a tartalom kézbesítésével optimalizálása érdekében stratégia kialakításához, és annak meghatározásához, hogy milyen problémákat kell megoldásra való jobb kihasználhatja a CDN-t. Emiatt nem csak akkor tudnak adatok kézbesítési teljesítmény javítása érdekében, de is tudnak a CDN költségek csökkentése.

> [!NOTE]
> Minden jelentés UTC/GMT notation egy dátum/idő megadásakor használhatók.
> 
> 

## <a name="reports-and-log-collection"></a>Jelentések és naplógyűjtést
CDN-tevékenységek adatait a peremhálózati teljesítmény Analytics modul kell gyűjteni előtt hozhat létre a jelentések rajta. Az adatgyűjtési folyamat akkor fordul elő, ha naponta, és ismerteti a tevékenység, amelynek az előző napra került sor. Ez azt jelenti, amelyben egy jelentésre vonatkozó statisztikák a napi statisztikák minta egy időben feldolgozott, azonban nem feltétlenül a teljes adatkészletet tartalmazza az aktuális napra. Ezek a jelentések elsődleges funkciója, teljesítmény felmérésére. Ezek nem használható számlázási célokra vagy a pontos numerikus statisztika.

> [!NOTE]
> A nyers adatokat, amelyből peremhálózati teljesítmény elemzési jelentések létrehozása legalább 90 napig érhető el.
> 
> 

## <a name="dashboard"></a>Irányítópult
A peremhálózati teljesítmény elemzések irányítópultján nyomon követi az aktuális és korábbi CDN forgalmat egy diagram és a statisztika keresztül. Ez az irányítópult használatával észleli a legutóbbi és a hosszú távú trendeket a CDN-adatforgalom a fiók a teljesítményre.

Ez az irányítópult áll:

* Interaktív diagram, amely lehetővé teszi a képi megjelenítés metrikáit és a trendeket.
* Az idősor, amely egy logika annak a hosszú távú biztosít metrikáit és a trendeket.
* Alapvető metrikákat, és hogyan statisztikai adatok a CDN hálózati növeli a webhelyek forgalmát mért összesített teljesítményt, használatát és hatékonyságát.

### <a name="accessing-the-edge-performance-dashboard"></a>A peremhálózati teljesítmény irányítópult elérése
1. A CDN-profil panelje, kattintson a **kezelése** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **Analytics** lapra, és vigye a **peremhálózati teljesítési Analytics** menü.  Kattintson a **irányítópult**.
   
    A peremhálózati csomópont elemzések irányítópultján jelenik meg.

### <a name="chart"></a>A diagram
Az irányítópult tartalmaz egy diagram, amely nyomon követi a metrika az idővonal közvetlenül alatt megjelenő kijelölt időszakra.  Győződjön meg arról, hogy diagramokat az utolsó két éves CDN tevékenység akár ütemterv közvetlenül a diagram alatt jelenik meg.

#### <a name="using-the-chart"></a>A diagram használatával
* Alapértelmezés szerint a gyorsítótár hatékonyságát az elmúlt 30 napban arány forrásadatok lesz.
* Ez a diagram adatok alapon naponta jön létre.
* A vonaldiagram a naponta fölé jelzi egy dátum és a metrika értékének adott napon.
* Kattintson a hétvégéket jelöljön ki egy átmeneti területre, világos szürke függőleges vonal, amelyek megfelelnek a diagram alakzatot hétvégéket váltáshoz. Átmeneti területre az ilyen típusú esetén hasznos forgalmi minták azonosítása hétvégéket keresztül.
* Kattintson a nézet egy év ezelőtt az átfedés tevékenység az előző év azonos idő alatt, a diagram váltáshoz. Az ilyen típusú összehasonlító hosszú távú CDN használati minták betekintést nyújt. A jobb felső sarokban, a diagram jelmagyarázat, amely jelzi az egyes vonaldiagram szín kódját tartalmazza.

#### <a name="updating-the-chart"></a>A diagram frissítése
* Időtartomány: Hajtsa végre a következők egyikét:
  * Válassza ki a kívánt régiót az idővonal. A diagram, amely megfelel a kiválasztott időszakra vonatkozó adatokkal frissül.
  * Kattintson duplán a diagram összes rendelkezésre álló előzményadatokat legfeljebb két évre megjelenítéséhez.
* A metrika: Kattintson a diagram ikon jelenik meg a kívánt metrika mellett. A diagram és az ütemterv frissítve lesz a megfelelő metrika adatokkal.

### <a name="key-metrics-and-statistics"></a>Alapvető metrikákat és statisztika
#### <a name="efficiency-metrics"></a>Hatékonyságát metrikák
A metrikák célja megtekintéséhez, hogy a gyorsítótár hatékonysága javítható. A gyorsítótár hatékonysága származó fő előnyei a következők:

* A forrás kiszolgálón, ami azt eredményezheti, hogy kevesebb betöltése:
  * Web server jobb teljesítményt.
  * Csökkenti a működési költségeket.
* Továbbfejlesztett adatok kézbesítési gyorsítás, mivel a CDN-ről további kérelmeket szolgáltató.

| Mező | Leírás |
| --- | --- |
| Gyorsítótár hatékonyságát |A gyorsítótárból állítása és kiszolgálása között átvitt adatok arányát jelzi. A metrika intézkedéseket, ha a kért tartalom egy gyorsítótárazott verzió állítása és kiszolgálása között a (peremhálózati kiszolgálóinak) CDN-ről történő engedélyezését (pl. webböngésző) |
| Találati arány |Azt jelzi, amely volt kiszolgálása a gyorsítótárból kérelem azon százaléka. A metrika intézkedéseket, ha a kért tartalom egy gyorsítótárazott verzió állítása és kiszolgálása között a (peremhálózati kiszolgálóinak) CDN-ről történő engedélyezését (pl. webböngésző). |
| a távoli memória - nincs gyorsítótár Config % |Azt jelzi, hogy a forgalmat, és a CDN (peremhálózati kiszolgálóinak), amelyek nem kerülnek a gyorsítótárba a Mellőzés gyorsítótár funkció (HTTP szabálymotor) miatt a forrás-kiszolgálókról kiszolgálásának aránya. |
| %-a távoli memória - gyorsítótárban lejárt |Elavult tartalom ismételt érvényesítése miatt állítása és kiszolgálása között a forrás-kiszolgálókról és a CDN (peremhálózati kiszolgálóinak) forgalom arányát jelzi. |

#### <a name="usage-metrics"></a>Használati metrikák
A metrikák célja, adja meg a következő költség-kivágáshoz intézkedések betekintést:

* A CDN a működési költségek minimalizálása.
* Gyorsítótár hatékonysága és a tömörítés révén CDN költségek csökkentését.

> [!NOTE]
> Forgalom mennyiségi számát és a százalékos arányt tesz lehetővé a számítások lett megadva, és csak lehet, hogy a teljes forgalom egy része megjelenítése nagy mennyiségű ügyfél forgalom jelölik.
> 
> 

| Mező | Leírás |
| --- | --- |
| Kimenő Ave bájtok |Azt jelzi, hogy az egyes kérelmek a CDN (peremhálózati kiszolgálóinak) számára a kérelmező (pl. webböngésző) szolgáltatott átvitt bájtok átlagos számát. |
| Egyetlen gyorsítótár Config bájt sebessége |A kérelmező (pl. webböngésző), amely nem miatt a Mellőzés gyorsítótár funkció gyorsítótárazza a CDN (peremhálózati kiszolgálóinak) szolgáltatott forgalom arányát jelzi. |
| Tömörített bájt arány |Tömörített formátumban (pl. webböngésző) engedélyezését a CDN (peremhálózati kiszolgálóinak) küldött forgalmat arányát jelzi. |
| Kimenő bájtok |Azt jelzi, hogy a adatmennyiség bájtban megadva, a kérelmezőnek (pl. webböngésző) kapott a CDN (peremhálózati kiszolgálóinak). |
| A bájtok |Azt jelzi, az adatmennyiség bájtban (pl. webböngésző) engedélyezését által küldött és a CDN (peremhálózati kiszolgálóinak). |
| Bájtok távoli |Azt jelzi, hogy a CDN-t (peremhálózati kiszolgálóinak) a CDN és a felhasználói származási kiszolgálókról küldött bájtok az adatok mennyiségét. |

#### <a name="performance-metrics"></a>Teljesítménymértékeket
A metrikák célja az forgalom általános CDN teljesítményének nyomon követéséhez.

| Mező | Leírás |
| --- | --- |
| Átviteli sebesség |Azt jelzi, ahol tartalom áthelyezték a CDN egy kérelmező átlagos. |
| Időtartam |Azt jelzi, az átlagos idő, az eszköz kézbesíthet a kérelmező (pl. webböngésző) tartott, ezredmásodpercben. |
| Tömörített kérelmek gyakorisága |A CDN (peremhálózati kiszolgálóinak) a kérelmezőnek (pl. webböngésző) kapott találatok arányát jelzi a tömörített formátumban. |
| 4xx Hibaarány |Által generált 4xx állapotkódot találatok arányát jelzi. |
| 5XX Hibaarány |Által generált 5xx állapotkódot találatok arányát jelzi. |
| A találatok |Azt jelzi, hogy a CDN-tartalom kérelmek száma. |

#### <a name="secure-traffic-metrics"></a>Biztonságos forgalom metrikák
A metrikák célja a HTTPS-forgalmat a CDN teljesítményének nyomon követéséhez.

| Mező | Leírás |
| --- | --- |
| Biztonságos gyorsítótár hatékonyságát |A HTTPS-kéréseket volt kiszolgálása a gyorsítótárból átvitt adatok arányát jelzi. A metrika intézkedéseket, ha a kért tartalom egy gyorsítótárazott verzió állítása és kiszolgálása között a (peremhálózati kiszolgálóinak) CDN-ről (pl. webböngésző) engedélyezését a HTTPS-KAPCSOLATON keresztül. |
| Biztonságos átviteli sebessége |Azt jelzi, ahol tartalom áthelyezték a CDN (peremhálózati kiszolgálóinak) engedélyezését (például a webkiszolgálók) átlagos sebességét HTTPS-KAPCSOLATON keresztül. |
| Biztonságos végrehajtásának átlagos időtartama |Azt jelzi, az átlagos idő, telepíthet egy eszköz a kérelmező (pl. webböngésző) HTTPS-KAPCSOLATON keresztül tartott, ezredmásodpercben. |
| Biztonságos találatok |Azt jelzi, hogy a CDN-tartalom HTTPS-kérelmek számát jelenti. |
| Kimenő biztonságos bájtok |Azt a HTTPS-forgalmat a kérelmezőnek (pl. webböngésző) kapott a CDN (peremhálózati kiszolgálóinak) bájtokban. |

## <a name="reports"></a>Jelentések
Ez a modul az egyes jelentések tartalmaz egy diagram és a sávszélesség és a forgalom használati metrikák különböző típusú statisztikák (pl. HTTP-állapotkódok, gyorsítótár állapotkódokat alállapotkódok kérés URL-címe, stb.). Ezeket az információkat felhasználhatja mélyebb elmélyedhet hogyan tartalom kiszolgált az ügyfelek számára, és annak finomhangolásához CDN viselkedésének adatok kézbesítési teljesítmény javítása érdekében.

### <a name="accessing-the-edge-performance-reports"></a>A peremhálózati teljesítmény jelentések használata
1. A CDN-profil panelje, kattintson a **kezelése** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **Analytics** lapra, és vigye a **peremhálózati teljesítési Analytics** menü.  Kattintson a **HTTP nagy objektum**.
   
    A peremhálózati csomópont elemzési jelentések képernyő jelenik meg.

| Jelentés | Leírás |
| --- | --- |
| Napi összegzése |Lehetővé teszi megadott idő alatt napi forgalom trendeket. Ez a diagram minden sávján egy adott dátumot jelöli. A sáv mérete jelzi, hogy mikor történt a dátumnak a találatok száma. |
| Óránkénti összegzése |Lehetővé teszi óránkénti forgalmi megadott idő alatt. Ez a diagram minden sávján egy adott napon egy órát jelöli. A sáv mérete jelzi, hogy az óra során bekövetkezett találatok száma. |
| Protokollok |Megjeleníti a HTTP és HTTPS protokollok közötti forgalom bontásban tartalmazza. Fánk diagram, amelyek az egyes protokoll történt találatok arányát jelzi. |
| HTTP-metódus |Ismerkedjen meg a HTTP-módszereket használ az adatok kérése teszi lehetővé. A leggyakrabban használt HTTP-metódus általában GET, HEAD és POST. Fánk diagram, amelyek az egyes HTTP-kérési metódust történt találatok arányát jelzi. |
| URL-címek |Tartalmazza a felső 10 kért URL-címek megjelenítő grafikon. A sáv jelenik meg minden egyes URL-CÍMÉT. A sáv magasságának azt jelzi, hogy a jelentés által lefedett által adott URL generált alatt az időtartam hány találatok. Az első 100 statisztikája kért URL-címek közvetlenül a ehhez a diagramhoz alább láthatók. |
| CNAME |Tartalmazza az első 10 CNAME eszközök kérhet az idő múlásával span jelentés megjelenítő grafikon. Az első 100 statisztikája kért CNAME közvetlenül a ehhez a diagramhoz alább láthatók. |
| Források |A felső 10 CDN megjelenítő grafikon vagy ügyfél eredeti kiszolgálók, amelyből eszközök kért egy meghatározott időtartamra vonatkozóan tartalmazza. Az első 100 statisztikája kért CDN vagy ügyfél eredeti kiszolgálók közvetlenül a ehhez a diagramhoz alább láthatók. Ügyfél eredeti kiszolgálókat a neve, a könyvtárnév beállításban megadott azonosítja. |
| Földrajzi POP |Mutatja, hogy mekkora a forgalom alatt keresztül történik egy adott pont nyújtó jelenléti (POP). A rövidítését jelöli a CDN hálózat POP. |
| Ügyfelek |Tartalmaz egy grafikonon, amely a felső 10 ügyfél által kért eszközök egy meghatározott időtartamra vonatkozóan jeleníti meg. Ez a jelentés céljából az azonos IP-címről érkező összes kérelmekkel lévőknek tekintendők az azonos ügyféltől. A top 100 ügyfelek statisztika közvetlenül ehhez a diagramhoz alatt jelennek meg. Ez a jelentés az letöltési tevékenység minták a felső ügyfelek meghatározására szolgál. |
| Gyorsítótár-állapotok |Részletes információkat a gyorsítótár-viselkedést, amely a teljes végfelhasználói élmény javításához megközelítések felfedheti biztosít. Mivel a leggyorsabb teljesítmény találatot eredményező gyorsítótárbeli kereséseinek származik, adatok kézbesítési sebességű minimalizálja a gyorsítótárbeli és lejárt találatok optimalizálható. |
| Nincs részletek |Tartalmaz egy grafikonon, amely a felső 10 URL-címek eszközök, amelynek gyorsítótár tartalom frissessége nincs bejelölve egy meghatározott időtartamra vonatkozóan jeleníti meg. A top 100 URL-címek esetén az ilyen típusú eszközök statisztika közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| CONFIG_NOCACHE részletei |Tartalmazza az első 10 URL-címeket, amelyek az ügyfél CDN konfigurációja miatt nem lettek gyorsítótárazva eszközök megjelenítő grafikon. Az ilyen típusú eszközök volt kiszolgált közvetlenül a forráskiszolgálóról. A top 100 URL-címek esetén az ilyen típusú eszközök statisztika közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| UNCACHEABLE részletei |Tartalmazza a felső 10 URL-címek, amelyek miatt a kérelem fejlécében adatai nem gyorsítótárazható eszközök megjelenítő grafikon. A top 100 URL-címek esetén az ilyen típusú eszközök statisztika közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| TCP_HIT részletei |Tartalmazza a felső 10 URL-címek eszközök gyorsítótárból azonnal szolgáltatott megjelenítő grafikon. A top 100 URL-címek esetén az ilyen típusú eszközök statisztika közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| TCP_MISS részletei |Tartalmazza a felső 10 URL-címek TCP_MISS gyorsítótár állapottal rendelkező eszközök megjelenítő grafikon. A top 100 URL-címek esetén az ilyen típusú eszközök statisztika közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| TCP_EXPIRED_HIT részletei |Tartalmazza a felső 10 URL-címeket a POP-ről volt szolgáltatott elavult eszközök megjelenítő grafikon. A top 100 URL-címek esetén az ilyen típusú eszközök statisztika közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| TCP_EXPIRED_MISS részletei |Tartalmazza a felső 10 URL-címeket, amelynek új verzió kellett kérhető le a forráskiszolgáló elavult eszközök megjelenítő grafikon. A top 100 URL-címek esetén az ilyen típusú eszközök statisztika közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| TCP_CLIENT_REFRESH_MISS részletei |A sávdiagram, amely megjeleníti az első 10 URL-címeket az eszközök lekérése egy forráskiszolgálóról, az ügyfél no-cache kérelem miatt tartalmazza. Az első 100 URL-kérelmeket ilyen típusú statisztikák közvetlenül a diagram alatt jelennek meg. |
| Ügyfél kéréstípusok |A HTTP-ügyfelek (például böngészők) által végrehajtott kérelmek típusát jelzi. Ez a jelentés tartalmaz egy fánk a diagram, amely egy logika, hogy hogyan alatt álló kéréseket kezeli. Az egyes kérelem sávszélesség és a forgalmi információk jelennek meg a diagram alá. |
| Felhasználói ügynök |A felső 10 felhasználói ügynökök keresztül a CDN a tartalmat igénylő megjelenítése oszlopdiagramon tartalmazza. A felhasználói ügynök általában egy webes böngésző, a media player vagy a mobiltelefon böngészővel. A top 100 felhasználói ügynökök statisztikája közvetlenül a diagram alatt jelennek meg. |
| Hivatkozó kérelmei |A felső 10 hivatkozó kérelmei megjelenítése a CDN keresztül érhető el tartalom oszlopdiagramon tartalmazza. A hivatkozó általában a weblapok és a tartalomhoz kapcsolódó erőforrás URL-CÍMÉT. Részletes információk az első 100 hivatkozó kérelmei előírt a grafikon alatt. |
| A tömörítési típusok |E tömörített azokat a peremhálózati kiszolgáló által kért eszközök megszakad fánk diagram tartalmazza. A tömörített eszközök aránya használt tömörítési típust oszlanak meg. Részletes információkat a graph alább az egyes tömörítési típus és állapotát. |
| Fájltípus |A felső 10 fájltípusokat keresztül a CDN a fiókjához kért megjelenítő oszlopdiagramon tartalmazza. Ez a jelentés céljából, a fájl típusa határozza meg az eszköz kiterjesztésű és internetes adathordozó típusát (pl. .html \[text/html\], .htm \[text/html\], .aspx \[text/html\]stb.). Részletes információk a top 100 fájltípusokat előírt a grafikon alatt. |
| Egyedi fájlok |Diagramját, amelyek egy adott napon egy adott időtartamra vonatkozóan kért egyedi eszközök teljes száma tevékenységtérkép tartalmazza. |
| Jogkivonat hitelesítési összegzése |A tortadiagram, amely gyors áttekintést nyújt a kért eszközök jogkivonat-alapú hitelesítés által védett e tartalmazza. A megkísérelt hitelesítés eredménye alapján a diagramban védett eszközök jelennek meg. |
| Jogkivonat hitelesítési megtagadása részletei |Tartalmaz egy oszlopdiagramot, amely lehetővé teszi, hogy a jogkivonat-alapú hitelesítés miatt megtagadva volt felső 10 kérések megtekintéséhez. |
| A HTTP válaszkódot. |Bontásban megmutatja az a HTTP-állapotkódok (pl. 200 OK, 403 Tiltott, 404 nem található, stb.), amely volt a HTTP-ügyfelek által szállított peremhálózati kiszolgálókról. A tortadiagram lehetővé teszi, hogy gyorsan felmérheti a módját a eszközök kiszolgált volt. Részletes statisztikai adatok biztosított minden válaszkód a grafikon alatt. |
| 404-es hibák |Tartalmaz egy oszlopdiagramot, amely lehetővé teszi, hogy a 404-es nem található válaszkódot eredményezett felső 10 kérések megtekintéséhez. |
| 403-as hiba |Tartalmaz egy oszlopdiagramot, amely lehetővé teszi a felső 10 kérelmek egy tiltott 403-as válaszkódot eredményezett. A tiltott 403-as válaszkódot megtagadta a kérelmet egy ügyfél forrás vagy a POP-ra a peremhálózati kiszolgáló következik be. |
| 4xx hibák |Tartalmaz egy oszlopdiagramot, amely lehetővé teszi a 400 közé válaszkódot eredményező felső 10 kérések megtekintéséhez. Ez a jelentés nem tartoznak azok a 403-as nem található, vagy tiltott 404-es válaszkódot. Általában 4xx válaszkód következik be, amikor egy kérelem egy ügyfél hiba miatt megtagadva. |
| 504-es számú hiba |Tartalmaz egy oszlopdiagramot, amely lehetővé teszi az átjáró időtúllépése 504-es számú válaszkódot eredményező felső 10 kérések megtekintéséhez. Átjáró időtúllépése 504-es számú válaszkódot időtúllépés történik egy másik kiszolgálóval való kommunikációhoz HTTP-proxy közben következik be. A CDN esetén 504-es számú átjáró időtúllépése válaszkód rendszerint azért fordul elő, ha egy biztonsági kiszolgálót nem tudja létrehozni a felhasználói eredetű-kiszolgálóval folytatott kommunikációhoz. |
| 502 hibák |Tartalmaz egy oszlopdiagramot, amely lehetővé teszi, hogy a hibás átjáró 502 válaszkódot eredményezett felső 10 kérések megtekintéséhez. 502 Hibás átjáró válaszkód egy kiszolgáló és a HTTP-proxy egy HTTP protokoll hiba esetén következik be. Esetén a CDN 502 Hibás átjáró válaszkód általában akkor fordul elő, amikor egy ügyfél eredeti kiszolgálóra adja vissza egy biztonsági kiszolgálót érvénytelen válasz. A válasz érvénytelen, ha nem lehetett elemezni, vagy nem teljes. |
| 5XX hibák |Tartalmaz egy oszlopdiagramot, amely lehetővé teszi a 500 közé válaszkódot eredményező felső 10 kérések megtekintéséhez.  Ez a jelentés nem 502 Hibás átjáró és az átjáró időtúllépése 504-es számú válaszkódot. |

## <a name="see-also"></a>Lásd még:
* [Az Azure CDN áttekintése](cdn-overview.md)
* [A Microsoft Azure CDN valós idejű statisztikák](cdn-real-time-stats.md)
* [A szabályok használata alapértelmezett HTTP működés felülbírálata](cdn-rules-engine.md)
* [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)

