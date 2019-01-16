---
title: Az Azure CDN-ben az élcsomópontok teljesítményének elemzése |} A Microsoft Docs
description: A Microsoft Azure CDN Szolgáltatásban az élcsomópontok teljesítményének elemzése. Peremhálózati Teljesítményelemzés a CDN-t biztosít részletes információkat és sávszélesség-kihasználtságáról.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7dfa252c29121adca2ecc77c08b2fca81d56e575
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320074"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Az élcsomópontok teljesítményének elemzése a Microsoft Azure CDN szolgáltatásban
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Peremhálózati Teljesítményelemzés a CDN-t biztosít részletes információkat és sávszélesség-kihasználtságáról. Ez az információ majd segítségével trendstatisztikákat, amelyek lehetővé teszik, hogy betekintést nyerhet az eszközök éppen hogyan gyorsítótárazásáról és kézbesítéséről, az ügyfelek számára. Ez lehetővé teszi a tartalmak kézbesítésének optimalizálása érdekében a stratégia kialakításához, és meghatározni, milyen problémák kell megoldásra való jobban kihasználhatja a CDN-t. Ennek eredményeképpen nem csak akkor fog tudni adatokat kézbesítési teljesítményének javítása, de akkor is elérhetik a CDN-költségek csökkentése érdekében.

> [!NOTE]
> Minden jelentés UTC/GMT jelöléssel dátum/idő megadásakor használhatók.
> 
> 

## <a name="reports-and-log-collection"></a>Jelentések és naplók összegyűjtése
A CDN-tevékenységi adatait ahhoz, hogy jelentéseket hozhatnak létre a peremhálózati Teljesítményelemzés modul kell gyűjteni. Az adatgyűjtési folyamat akkor fordul elő, ha naponta, és ismerteti a tevékenység az előző nap során bekövetkezett. Ez azt jelenti, hogy egy jelentés statisztika képviselik a napi statisztikák minta időpontjában lett feldolgozva, és nem feltétlenül tartalmazzák a teljes adatkészletet az aktuális napra. Ezek a jelentések elsődleges funkciója, hogy a teljesítmény értékeléséhez. Azok a számlázás tekintetében vagy pontos numerikus statisztikák nem használható.

> [!NOTE]
> A nyers adatokat, amelyről Edge teljesítményét elemző jelentések létrehozása legalább 90 napig érhető el.
> 
> 

## <a name="dashboard"></a>Irányítópult
A peremhálózati Teljesítményelemzés irányítópult aktuális és előzménynézeteket CDN forgalmát egy diagram és statisztikai követi nyomon. Ez az irányítópult használatával megfigyelheti a szabályszerűségeket legutóbbi és a hosszú távú fiókja CDN-adatforgalom teljesítményét.

Ez az irányítópult áll:

* Interaktív diagram, amely lehetővé teszi a Vizualizáció alapvető metrikákat és trendeket.
* Egy ütemterv, hogy megismerje a hosszú távú mintázatok nyújt alapvető metrikákat és trendeket.
* Alapvető metrikákat és statisztikai információkat a CDN-hálózatban javítja webhelyforgalom mért teljes teljesítmény, a használat és a hatékonyság.

### <a name="accessing-the-edge-performance-dashboard"></a>Az edge-teljesítmény irányítópulthoz hozzáférő
1. A CDN-profil panelje, kattintson a **kezelés** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portálját.
2. A kurzort a **Analytics** lapfülre, majd mutasson a **peremhálózati Teljesítményelemzés** úszó menü.  Kattintson a **irányítópult**.
   
    A peremhálózati csomópont analytics irányítópult jelenik meg.

### <a name="chart"></a>Diagram
Az irányítópult tartalmaz egy diagram, amely nyomon követi a metrika az idővonalon megjelenő közvetlenül alá kijelölt időszakra.  Győződjön meg arról, hogy gráfok az utolsó két év CDN tevékenység beállítása ütemterv közvetlenül a diagram alatt jelenik meg.

#### <a name="using-the-chart"></a>A diagram használatával
* Alapértelmezés szerint a gyorsítótár hatékonyságát arány az elmúlt 30 napban lesz látható diagramon.
* Ez a diagram napi alapon adataiból jön létre.
* Egy nap, a vonaldiagram az egérmutatót fogja jelezni egy dátum és a mérőszám értéke azon a napon.
* Kattintson a hétvégeken jelöljön ki egy átmeneti területre, világos szürke függőleges sávok alakzatot a diagram szabadnapok képviselő váltás. Az ilyen típusú területre hasznos forgalmi minták azonosítása a hétvégék keresztül.
* Kattintson a nézet egy év ezelőtt válthat egy átmeneti területre a tevékenység az előző év azonos időszakra alakzatot a diagram. Az ilyen típusú összehasonlító hosszú távú CDN használati mintáinak betekintést nyújt. A diagram jobb felső sarkában a jelmagyarázatot, amely azt jelzi, hogy minden egyes vonaldiagram a szín kódját tartalmazza.

#### <a name="updating-the-chart"></a>A diagram frissítése
* Időtartomány: Hajtsa végre az alábbi lehetőségek közül:
  * Válassza ki a kívánt régiót az idővonalon. A diagram, amely megfelel a kiválasztott időszakban adatokkal frissülnek.
  * Kattintson duplán a diagram pedig akár két évig minden elérhető előzményadatok megjelenítése.
* Metrika: Kattintson a diagram ikonra, amely a metrikát mellett jelenik meg. A diagram és az idővonalon a megfelelő metrika adatokkal frissülnek.

### <a name="key-metrics-and-statistics"></a>Alapvető metrikákkal és statisztika
#### <a name="efficiency-metrics"></a>Hatékonyság metrikák
A metrikák célja, hogy talál-e a gyorsítótár-hatékonyság javítása érdekében. A gyorsítótárazás hatékonysága származó fő előnyei a következők:

* Csökkentett terhelését az eredeti kiszolgálóra, amely lehet:
  * Jobb webkiszolgáló teljesítményét.
  * Csökkenti a működési költségeket.
* Továbbfejlesztett adatok kézbesítési gyorsítás, mivel további kérelmeket a rendszer közvetlenül a CDN-ből szolgálja ki.

| Mező | Leírás |
| --- | --- |
| Gyorsítótárazás hatékonysága |Azt jelzi, hogy a gyorsítótárból való kiszolgálása között átvitt adatok aránya. A metrika mértékeket, ha egy gyorsítótárazott verziója a lekért tartalom kiszolgálása a CDN-t (edge kiszolgálók) közvetlenül a igénylők (pl. webböngésző) |
| Találati aránya |Azt jelzi, hogy a gyorsítótárból is kiszolgált kérések aránya. A metrika mértékeket, ha egy gyorsítótárazott verziója a lekért tartalom kiszolgálása a CDN-t (edge kiszolgálók) közvetlenül a igénylők (pl. webböngésző). |
| %-a távoli memória - nincs gyorsítótár-Config |Azt jelzi, hogy a forgalmat, és a CDN (edge kiszolgálók), amely nem a Mellőzés gyorsítótárának (HTTP Szabálymotorral) eredményeként gyorsítótárazza a szerverek kiszolgálása aránya. |
| %-a távoli memória - gyorsítótárban lejárt |Elavult tartalom újraérvényesítésre eredményeként kiszolgálása szerverek és a CDN (edge kiszolgálók) forgalom arányát jelzi. |

#### <a name="usage-metrics"></a>Használati metrikák
Ezek a metrikák célja nyújt betekintést az alábbi általános költségek mértékeket:

* Minimalizálja a működési költségeket, a CDN-en keresztül.
* Gyorsítótárazás hatékonysága és tömörítés révén CDN kiadások csökkentése.

> [!NOTE]
> Forgalom mennyiségi számát arányok és százalékértékek számítások használták, és előfordulhat, hogy csak a megjelenítése a teljes forgalom egy része az ügyfelek nagy mennyiségű forgalom képviseli.
> 
> 

| Mező | Leírás |
| --- | --- |
| Elküldött kimenő bájtok |Azt jelzi, hogy az egyes kérések a kérelmező (pl. webböngésző), a CDN (edge kiszolgálók) által szolgáltatott átvitt bájtok átlagos száma. |
| Nincs gyorsítótár Config bájt sebessége |Azt jelzi, hogy a forgalom a kérelmezőnek (pl. webböngésző), amely nem miatt a Mellőzés gyorsítótár-szolgáltatás gyorsítótárazza a CDN (edge kiszolgálók) által szolgáltatott aránya. |
| Tömörített bájt arány |Azt jelzi, hogy a igénylők (pl. webböngésző) a CDN-t (edge kiszolgálók) által küldött forgalom százalékaránya tömörített formátumban. |
| Kimenő bájtok |Az adatok (bájt), amely a CDN-t (edge kiszolgálók) származó, a kérelmező (pl. webböngésző) kézbesítése jelöli. |
| A bájtok |Azt jelzi, hogy igénylők (pl. webböngésző) által küldött adatok (bájt) mennyisége és a CDN (edge kiszolgálók). |
| Távoli bájt |Azt jelzi, hogy a CDN-t (peremhálózati kiszolgálókon) a CDN-t és az ügyfelek szerverek küldött bájtok, az adatok mennyisége. |

#### <a name="performance-metrics"></a>Teljesítmény-mérőszámok
Ezek a metrikák célja a forgalom általános CDN teljesítményének nyomon követéséhez.

| Mező | Leírás |
| --- | --- |
| Átviteli sebesség |Azt jelzi, hogy az átlagos tartalom volt továbbításának sebessége a CDN-ből egy kérelmezőnek. |
| Időtartam |Azt jelzi, hogy az átlagos idő (MS), egy eszköz továbbítására a kérelmező (pl. webböngésző) időtartamát. |
| Tömörített kérelmek gyakorisága |A kérelmező (pl. webböngésző), a CDN-t (peremhálózati kiszolgálókon) a kapott találatok arányát jelzi a tömörített formátumban. |
| 4xx Hibaarány |Által generált egy 4xx típusú állapotkódot találatok arányát jelzi. |
| 5XX Hibaarány |Által generált 5xx állapotkódot találatok arányát jelzi. |
| Találatok |Azt jelzi, hogy a CDN-tartalom vonatkozó kérelmek száma. |

#### <a name="secure-traffic-metrics"></a>Biztonságos forgalom metrikák
Ezek a metrikák célja a HTTPS-forgalmat a CDN teljesítményének nyomon követéséhez.

| Mező | Leírás |
| --- | --- |
| Biztonságos gyorsítótárazás hatékonysága |Azt jelzi, hogy a HTTPS-kéréseket, amelyek a kiszolgált voltak a gyorsítótárból átvitt adatok aránya. A metrika mértékeket, ha egy gyorsítótárazott verziója a lekért tartalom kiszolgálása a CDN-t (edge kiszolgálók) közvetlenül a igénylők (pl. webböngésző) HTTPS-kapcsolaton keresztül. |
| Biztonságos átviteli sebessége |Azt jelzi, hogy tartalom volt való továbbításának a CDN-t (edge kiszolgálók) igénylők (pl. webkiszolgálók) átlagos sebességét HTTPS-kapcsolaton keresztül. |
| Biztonságos átlagos időtartama |Azt jelzi, hogy az átlagos idő a kérelmező (pl. webböngésző) HTTPS-kapcsolaton keresztül egy eszköz továbbítására tartott, ezredmásodpercben. |
| Biztonságos találatok |Azt jelzi, hogy a CDN-tartalom HTTPS-kérelmek száma. |
| Biztonságos kimenő bájtok |Azt jelzi, hogy a HTTPS-forgalom (bájt), amely a CDN-t (edge kiszolgálók) származó, a kérelmező (pl. webböngésző) kézbesítése mennyiségét. |

## <a name="reports"></a>Jelentések
Ez a modul minden egyes jelentést tartalmaz egy diagramot és a különböző típusú metrikák sávszélességet és a forgalom használati statisztikák (pl. HTTP-állapotkódok, gyorsítótár állapotkódokat alállapotkódok kérés URL-címe, stb.). Ezek az információk használhatók, mélyebb elmélyedhet hogyan tartalom szolgál ki a rendszer az ügyfelek számára, és finomhangolása a CDN viselkedésének szállítási adatok a teljesítmény javítása.

### <a name="accessing-the-edge-performance-reports"></a>Az edge-teljesítmény jelentések elérése
1. A CDN-profil panelje, kattintson a **kezelés** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portálját.
2. A kurzort a **Analytics** lapfülre, majd mutasson a **peremhálózati Teljesítményelemzés** úszó menü.  Kattintson a **HTTP nagy objektum**.
   
    A peremhálózati csomópont elemzési jelentések képernyő jelenik meg.

| Jelentés | Leírás |
| --- | --- |
| Napi összesítés |Lehetővé teszi a napi adatforgalom trendek megtekintheti a megadott időtartam alatt. Minden Ez a diagram sávjára egy adott dátumot jelöli. A sávon mérete jelzi, hogy mikor történt a dátumnak a találatok teljes száma. |
| Óránkénti összefoglaló |A megadott időszakra óránként forgalom trendek megtekintését teszi lehetővé. Minden Ez a diagram sávjára jelöli egy adott dátumon egy egyetlen óra. A sáv mérete jelzi órában fellépő találatok teljes száma. |
| Protokollok |Megjeleníti a HTTP és HTTPS protokollok közötti forgalom elosztása. Egy fánkdiagramon által az egyes protokoll történt találatok arányát jelzi. |
| HTTP-metódusok |Lehetővé teszi a HTTP-metódusok használ az adatok kérelmezése képet kaphat. A leggyakrabban használt HTTP-kérelem metódusok általában GET, HEAD és POST. Egy fánkdiagramon jelzi, amely minden HTTP-kérési metódust típusú történt a találatok arányát. |
| URLs |Tartalmazza az első 10 kért URL megjelenítő grafikon. Minden egyes URL-cím egy sáv jelenik meg. A sáv magassága jelzi, hogy a jelentés által lefedett által adott URL-cím generált alatt az időtartam hány találatok. Az első 100 statisztikáit a kért URL-címek közvetlenül ehhez a diagramhoz alább látható. |
| CNAMEs |Tartalmazza az első 10 CNAME-rekordokat eszközök az idő múlásával a szabadságigények span jelentést megjelenítő grafikon. Az első 100 statisztikája kért, CNAME-rekordokat közvetlenül ez a diagram alatt jelenik meg. |
| Források |A felső 10 CDN megjelenítő grafikon vagy eszközök, amelyről a megadott időszakon belül kért ügyfél szerverek tartalmazza. Az első 100 statisztikája kért CDN vagy ügyfél szerverek közvetlenül ehhez a diagramhoz alább látható. A neve, a könyvtárnév lehetőség meghatározott ügyfél szerverek azonosítja. |
| GEO-kre |Látható, hogy mekkora a forgalom folyamatban áthalad egy adott –-jelenléti pontok (POP). A hárombetűs rövidítést jelöli a jelenléti pontra Irányíthatja a CDN hálózatban. |
| Ügyfelek |Tartalmazza a felső 10 ügyfél által kért objektumok egy adott időszakon belül megjelenítő grafikon. Ez a jelentés az alkalmazásában az azonos IP-címről érkező összes kérelmekkel tekintendők az ugyanazon ügyféltől érkező. Az első 100 ügyféllel statisztikája közvetlenül ehhez a diagramhoz alatt jelennek meg. Ez a jelentés letöltése tevékenység minták meghatározására a felső ügyfelei számára hasznos. |
| Gyorsítótárak Allapota |Gyorsítótár-viselkedés felfedheti az megközelítések a teljes végfelhasználói élmény javítására vonatkozó részletes áttekintését biztosítja. Mivel az a legjobb teljesítmény találatot eredményező gyorsítótárbeli kereséseinek származik, optimalizálhatja adatok kézbesítési megbízhatóbbak gyorsítótárbeli minimalizálása és a lejárt gyorsítótárbeli találatok. |
| Nincs részletei |Tartalmazza az eszközök, amelynek gyorsítótár tartalom frissessége nem lett ellenőrizve a megadott időszakban 10 URL megjelenítő grafikon. Az első 100 URL-címeket az ilyen típusú eszközök statisztikája közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| CONFIG_NOCACHE részletei |Tartalmazza az első 10 URL-címeket az eszközök, amelyek az ügyfél a CDN-konfiguráció miatt nem lettek gyorsítótárazva megjelenítő grafikon. Az ilyen típusú eszközök közvetlenül a forráskiszolgálóról is szolgált. Az első 100 URL-címeket az ilyen típusú eszközök statisztikája közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| UNCACHEABLE részletei |Tartalmazza a felső 10 URL-címek, amelyek miatt a kérelem fejlécében adatai nem gyorsítótárazható eszközök megjelenítő grafikon. Az első 100 URL-címeket az ilyen típusú eszközök statisztikája közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| TCP_HIT részletei |Tartalmazza az eszközök közvetlenül a gyorsítótárból fájlnévkiterjesztései 10 URL megjelenítő grafikon. Az első 100 URL-címeket az ilyen típusú eszközök statisztikája közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| TCP_MISS részletei |Tartalmazza a felső 10 URL TCP_MISS gyorsítótár állapottal rendelkező eszközök megjelenítő grafikon. Az első 100 URL-címeket az ilyen típusú eszközök statisztikája közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| TCP_EXPIRED_HIT részletei |Tartalmazza az elavult eszköz, amely közvetlenül a jelenléti pont is kiszolgált 10 URL megjelenítő grafikon. Az első 100 URL-címeket az ilyen típusú eszközök statisztikája közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| TCP_EXPIRED_MISS részletei |Tartalmazza az elavult eszközöket, amelyhez új verzió kellett kérhető le a forráskiszolgáló URL felső 10-megjelenítő grafikon. Az első 100 URL-címeket az ilyen típusú eszközök statisztikája közvetlenül ehhez a diagramhoz alatt jelennek meg. |
| TCP_CLIENT_REFRESH_MISS részletei |Tartalmazza a sávdiagramot oszlopdiagramra cseréli, amely megjeleníti az első 10 URL-címeket az eszközök lekérése egy forráskiszolgálóról, az ügyfél kérése no-cache miatt. Az első 100 URL-címeket az ilyen típusú kérelmeket statisztikája közvetlenül a diagram alatt jelennek meg. |
| Ügyfél kéréstípusok |HTTP-ügyfél (például böngészők) által végrehajtott kérelmek típusát jelzi. Ez a jelentés egy fánkdiagramon feltárhatja, hogy hogyan kérések kezelésének bizonyos értelemben biztosító tartalmaz. Minden egyes kérelemtípus kapcsolatos sávszélesség és a forgalom a diagram alatt jelenik meg. |
| Böngészőazonosító |Tartalmaz egy olyan oszlopdiagramon, megjelenítés, a felső 10 felhasználói ügynökök lekérni a tartalmat a CDN-en keresztül. A felhasználói ügynök általában egy webböngészőben, a media player vagy a mobiltelefon böngészőben. Az első 100 felhasználói ügynökök statisztikáját közvetlenül a diagram alatt jelennek meg. |
| Hivatkozók |A felső 10 hivatkozók megjelenítése a CDN-en keresztül elérhető tartalmakhoz oszlopdiagramon tartalmazza. A hivatkozó általában a weblapon vagy a tartalom összekapcsoló erőforrás URL-CÍMÉT. Részletes információkat lejjebb a diagramon az első 100 hivatkozóknak. |
| A tömörítési típusok |E tömörített, a peremhálózati kiszolgáló által kért eszközök felszámolja gyűrűdiagramra tartalmazza. A tömörített eszközök aránya használt tömörítési típust oszlanak meg. Részletes információkat lejjebb a gráf minden tömörítési típus és állapotát. |
| Fájltípusok |Egy oszlopdiagramot jelenít meg a felső 10 fájltípusokat, a fiókhoz tartozó CDN-en keresztül kérő tartalmazza. Ez a jelentés az alkalmazásában, a fájl típusa határozza meg az objektum fájlnév-kiterjesztésű és internetes média típusa (pl. .html \[text/html\], .htm \[text/html\], .aspx \[text/html\]stb.). Részletes információkat lejjebb a gráf az első 100 fájltípusok esetén. |
| Egyedi fájlok |Tartalmaz egy diagram, amely egy megadott időszakban egy adott napon kért egyedi eszközök száma látható. |
| Jogkivonat-hitelesítés összefoglalása |A kördiagram, amely a kért objektumok jogkivonat-alapú hitelesítés által védett e rövid áttekintését tartalmazza. Védett adatok megkísérelt hitelesítésüket eredményei alapján a diagramon jelennek meg. |
| Jogkivonat-hitelesítés megtagadása részletei |Tartalmaz egy oszlopdiagram, amely lehetővé teszi, hogy a felső 10 kérést, amely miatt a jogkivonat-alapú hitelesítés el lett utasítva megtekintéséhez. |
| HTTP-válaszkódot |A HTTP-állapotkódok szerinti bontásban (például 200 OK, 403 Tiltott, 404 nem található, stb.), amely volt a HTTP-ügyfelek által szállított a peremhálózati kiszolgálókról. Tortadiagram segítségével mérheti fel gyorsan a hogyan lettek szolgálja ki az eszközök. A grafikon alatt minden válaszkód részletes statisztikai adatok biztosítunk. |
| 404 Errors |Tartalmaz egy oszlopdiagram, amely lehetővé teszi, hogy a felső 10 kérések 404-es nem található válaszkódot eredményezett megtekintését. |
| 403-as hibák |Tartalmaz egy oszlopdiagram, amely lehetővé teszi, hogy a felső 10 kérések tiltott 403-as válaszkódot eredményezett megtekintését. Egy 403 Tiltott válasz kódja akkor fordul elő, amikor egy ügyfél eredeti kiszolgálóra vagy egy biztonsági kiszolgálót, a jelenléti pontra Irányíthatja a kérelem megtagadva. |
| 4xx hibakódok |Tartalmaz egy oszlopdiagram, amely lehetővé teszi, hogy a felső 10 kérést, amely a 400 tartomány válaszkódot eredményezett megtekintéséhez. Ez a jelentés nem tartoznak azok a 403-as és a 404-es tiltott válaszkódot nem található. Általában 4xx válaszkód fordul elő, amikor egy kérelem egy ügyfélhiba miatt megtagadva. |
| 504 hibák |Tartalmaz egy oszlopdiagram, amely lehetővé teszi, hogy a felső 10 kérések átjáró időtúllépése 504 válaszkódot eredményezett megtekintését. Átjáró időtúllépése 504 válaszkód akkor fordul elő, ha időtúllépés történik, egy HTTP-proxy egy másik Serverrel való kommunikáció közben. CDN-en, esetén egy átjáró időtúllépése 504 válaszkód általában akkor fordul elő, ha egy biztonsági kiszolgálót nem tudja a kommunikációt egy ügyfél forráskiszolgáló. |
| 502 hibák |Tartalmaz egy oszlopdiagram, amely lehetővé teszi, hogy a felső 10 kérések 502 Hibás átjáró válaszkódot eredményezett megtekintését. 502 Hibás átjáró válaszkód következik be, amikor HTTP-protokoll hibát akkor fordul elő, egy kiszolgáló és a egy HTTP-proxyt. CDN-en, esetén 502 Hibás átjáró válaszkód általában akkor fordul elő, amikor egy ügyfél forráskiszolgáló egy érvénytelen választ adja vissza. Válasz nem érvényes, ha nem lehetett elemezni, vagy nem teljes. |
| 5XX kódú hiba |Tartalmaz egy oszlopdiagram, amely lehetővé teszi, hogy a felső tartományba 500 válaszkódot eredményezett 10 kérések megtekintését.  Ez a jelentés nem 502 Hibás átjáró és 504 átjáró időtúllépése válaszkódot. |

## <a name="see-also"></a>Lásd még
* [Az Azure CDN áttekintése](cdn-overview.md)
* [A Microsoft Azure CDN Szolgáltatásban valós idejű statisztikák](cdn-real-time-stats.md)
* [A rules engine használatával a HTTP alapértelmezés felülbírálása](cdn-rules-engine.md)
* [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)

