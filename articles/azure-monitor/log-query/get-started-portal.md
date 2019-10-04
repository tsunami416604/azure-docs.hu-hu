---
title: Ismerkedés a Azure Monitor Log Analyticsrel | Microsoft Docs
description: Ez a cikk bemutatja, hogyan lehet lekérdezéseket írni a Azure Portal Log Analytics használatával.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: bwren
ms.openlocfilehash: 950768326228960192f48d99e5c5fa849b2c2bda
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076824"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Ismerkedés a Log Analyticsával Azure Monitor

> [!NOTE]
> Ezt a gyakorlatot a saját környezetében hajthatja végre, ha legalább egy virtuális gépről gyűjti az adatgyűjtést. Ha nem, akkor használja a [bemutató környezetét](https://portal.loganalytics.io/demo), amely rengeteg mintavételi adatmennyiséget tartalmaz.

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Portal Log Analytics a Azure Monitor-naplók lekérdezéséhez. A következőket fogja megtanítani:

- Egyszerű lekérdezés készítése a Log Analytics használatával
- Az adatai sémájának megismerése
- Eredmények szűrése, rendezése és csoportosítása
- Időtartomány alkalmazása
- Diagramok létrehozása
- Lekérdezések mentése és betöltése
- Lekérdezések exportálása és megosztása

A naplók lekérdezésével kapcsolatos oktatóanyagért tekintse meg a következő témakört: Ismerkedés [a naplózási lekérdezésekkel a Azure monitor](get-started-queries.md).<br>
A naplók lekérdezésével kapcsolatos további részletekért lásd: [a Azure monitorban található naplók áttekintése](log-query-overview.md).

## <a name="meet-log-analytics"></a>Ismerkedjen meg Log Analytics
A Log Analytics egy olyan webes eszköz, amely Azure Monitor naplók írásához és végrehajtásához használatos. Nyissa meg a Azure Monitor menüben a **naplók** elem kiválasztásával. Egy új üres lekérdezéssel kezdődik.

![Kezdőlap](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>A tűzfalra vonatkozó követelmények
A Log Analytics használatához a böngészőnek hozzá kell férnie a következő címekhez. Ha a böngésző tűzfalon keresztül fér hozzá a Azure Portalhoz, engedélyeznie kell a hozzáférést ezekhez a címekhez.

| URI | IP | Portok |
|:---|:---|:---|
| portal.loganalytics.io | Dinamikus | 80,443 |
| api.loganalytics.io | Dinamikus | 80,443 |
| docs.loganalytics.io | Dinamikus | 80,443 |

## <a name="basic-queries"></a>Alapszintű lekérdezések
A lekérdezésekkel kifejezéseket kereshet, azonosíthatja a trendeket, elemezheti a mintákat, és számos más elemzést is biztosíthat az adatai alapján. Kezdés egy alapszintű lekérdezéssel:

```Kusto
Event | search "error"
```

A lekérdezés megkeresi az _eseménynaplóban_ azokat a rekordokat, amelyek a kifejezés _hibáját_ tartalmazzák bármely tulajdonságban.

A lekérdezések egy Táblanév vagy egy [keresési](/azure/kusto/query/searchoperator) paranccsal is kezdődhetnek. A fenti példa a táblanév _eseménysel_kezdődik, amely az Event tábla összes rekordját lekéri. A pipe (|) karakter elválasztja a parancsokat, így az első kimenet a következő parancs bemenetként szolgál. Egyetlen lekérdezéshez tetszőleges számú parancsot adhat hozzá.

Ugyanez a lekérdezés más módon is írható:

```Kusto
search in (Event) "error"
```

Ebben a példában a **Keresés** az _Event_ táblára terjed ki, és a táblázatban szereplő összes rekord a kifejezés _hibáját_keresi.

## <a name="running-a-query"></a>Lekérdezés futtatása
Futtasson egy lekérdezést a **Futtatás** gombra kattintva, vagy nyomja le a **SHIFT + ENTER**billentyűkombinációt. Vegye figyelembe a következő adatokat, amelyek meghatározzák a futtatandó kódot és a visszaadott adatokat:

- Sortörések: Egyetlen töréspont esetén a lekérdezés könnyebben olvashatóvá válik. Több sortörés külön lekérdezésekre oszlik.
- Kurzor Vigye a kurzort a lekérdezésen belül a végrehajtáshoz. Az aktuális lekérdezés akkor tekinthető a kódnak, ha nem talál üres sort.
- Időtartomány – az _elmúlt 24 óra_ időtartománya alapértelmezés szerint be van állítva. Ha más tartományt szeretne használni, használja az időválasztót, vagy adjon hozzá egy explicit időtartomány-szűrőt a lekérdezéshez.


## <a name="understand-the-schema"></a>A séma bemutatása
A séma a táblázatok egy logikai kategóriába csoportosított gyűjteménye. A kategóriák közül több a figyelési megoldás. A _LogManagement_ kategória olyan gyakori adattartalmakat tartalmaz, mint a Windows és a syslog-események, a Teljesítményadatok és az ügynök szívverése.

![Séma](media/get-started-portal/schema.png)

Az egyes táblákban az adattípusok különböző adattípusú oszlopokban vannak rendezve, amelyeket az oszlopnév melletti ikonok jeleznek. Például a képernyőképen látható _esemény_ táblázat olyan oszlopokat tartalmaz, mint például a _számítógép_ , amely a szöveg, a _EventCategory_ , amely egy szám, és _TimeGenerated_ , amely dátum/idő.

## <a name="filter-the-results"></a>Az eredmények szűrése
Kezdje azzal, hogy mindent megtalál az _Event_ táblázatban.

```Kusto
Event
```

A Log Analytics automatikus hatóköre a következőket eredményezi:

- Időtartomány:  Alapértelmezés szerint a lekérdezések az elmúlt 24 órában korlátozódnak.
- Találatok száma: Az eredmények legfeljebb 10 000 rekordra korlátozódnak.

Ez a lekérdezés nagyon általános, és túl sok eredményt ad vissza hasznosnak. Az eredményeket a táblázat elemein keresztül szűrheti, vagy explicit módon szűrőt adhat hozzá a lekérdezéshez. Az eredmények a táblázat elemein keresztül történő szűrése a meglévő eredményhalmaz alapján történik, míg a lekérdezés szűrője egy új szűrt eredményhalmaz visszaadását eredményezi, így pontosabb eredményeket eredményezhet.

### <a name="add-a-filter-to-the-query"></a>Szűrő hozzáadása a lekérdezéshez
Az egyes rekordok bal oldalán egy nyíl látható. Kattintson a nyílra kattintva nyissa meg a egy adott rekord részleteit.

Vigye a kurzort a "+" és a "-" ikonokra mutató oszlop nevére a megjelenítendő érték fölé. Ha olyan szűrőt szeretne felvenni, amely csak azonos értékű rekordokat ad vissza, kattintson a "+" jelre. Kattintson a "-" gombra, ha ki szeretné zárni a rekordokat ezzel az értékkel, majd kattintson a **Futtatás** gombra a lekérdezés újbóli futtatásához.

![Szűrő hozzáadása a lekérdezéshez](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Szűrés a táblázat elemein
Most tekintsük át a hiba súlyosságát _jelző_eseményeket. Ez egy _EventLevelName_nevű oszlopban van megadva. Az oszlop megtekintéséhez görgessen jobbra.

Kattintson az oszlop címe melletti szűrő ikonra, és az előugró ablakban válassza ki a szöveges hibaüzenettel _kezdődő_ értékeket:

![Szűrés](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Eredmények rendezése és csoportosítása
Az eredmények mostantól szűkülnek, hogy csak az elmúlt 24 órában létrehozott SQL Server hibaüzeneteket tartalmazzák. Az eredményeket azonban semmilyen módon nem rendezi a rendszer. Ha az eredményeket egy adott oszlop szerint szeretné rendezni, például például az időbélyegzőt, kattintson az oszlop címére. Egy kattintással növekvő sorrendbe rendezheti a rendezést, míg a második kattintás csökkenő sorrendben jelenik meg.

![Oszlop rendezése](media/get-started-portal/sort-column.png)

Az eredmények rendezésének másik módja csoportok szerint történik. Egy adott oszlop eredményeinek csoportosításához egyszerűen húzza az oszlop fejlécét a többi oszlop fölé. Alcsoportok létrehozásához húzza a többi oszlopot is a felső sávra.

![Csoportok](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Megjelenítendő oszlopok kiválasztása
Az eredmények táblázata gyakran sok oszlopot tartalmaz. Előfordulhat, hogy a visszaadott oszlopok némelyike alapértelmezés szerint nem jelenik meg, vagy a megjelenített oszlopokat is érdemes eltávolítani. A megjelenítendő oszlopok kiválasztásához kattintson az oszlopok gombra:

![Oszlopok kiválasztása](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Időtartomány megadása
Alapértelmezés szerint a Log Analytics az _utolsó 24 órás_ időtartományt alkalmazza. Ha másik tartományt szeretne használni, válasszon egy másik értéket az időválasztón keresztül, és kattintson a **Futtatás**gombra. Az előre beállított értékek mellett az _Egyéni időtartomány_ lehetőséggel is kiválaszthatja a lekérdezés abszolút tartományát.

![Időválasztó](media/get-started-portal/time-picker.png)

Egyéni időtartomány kiválasztásakor a kiválasztott értékek UTC szerint vannak megadva, ami eltérő lehet a helyi időzónában.

Ha a lekérdezés explicit módon tartalmaz egy szűrőt a _TimeGenerated_, az időválasztó címe megjelenik a _lekérdezésben_. Az ütközések elkerülése érdekében a manuális kijelölés le lesz tiltva.


## <a name="charts"></a>Diagramok
Egy tábla eredményeinek visszaadása mellett a lekérdezési eredmények vizuális formátumban is megjeleníthetők. Példaként használja az alábbi lekérdezést:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Alapértelmezés szerint az eredmények egy táblázatban jelennek meg. Kattintson a _diagram_ elemre az eredmények grafikus nézetben való megtekintéséhez:

![Oszlopdiagram](media/get-started-portal/bar-chart.png)

Az eredmények halmozott oszlopdiagram szerint jelennek meg. Kattintson a _Halmozott oszlop_ lehetőségre, és válassza a _torta_ lehetőséget az eredmények egy másik nézetének megjelenítéséhez:

![Tortadiagram](media/get-started-portal/pie-chart.png)

A nézet különböző tulajdonságai, például az x és az y tengelyek, valamint a csoportosítási és felosztási beállítások manuálisan is módosíthatók a vezérlő menüsávján.

Saját maga is megadhatja a lekérdezés előnyben részesített nézetét a render operátor használatával.

### <a name="smart-diagnostics"></a>Intelligens diagnosztika
Ha a idődiagramját hirtelen szeg vagy lépés szerepel az adataiban, akkor a sorban egy kiemelt pont jelenik meg. Ez azt jelzi, hogy az _intelligens diagnosztika_ olyan tulajdonságok kombinációját azonosította, amely kiszűri a hirtelen változást. Kattintson a pontra a szűrő további részleteinek megtekintéséhez és a szűrt verzió megjelenítéséhez. Ez segíthet azonosítani a változást:

![Intelligens diagnosztika](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Rögzítés az irányítópulton
Ha diagramot vagy táblázatot szeretne rögzíteni egy megosztott Azure-irányítópulton, kattintson a rögzítés ikonra. Vegye figyelembe, hogy ez az ikon a Log Analytics ablak elejére került, amely eltér az alábbi képernyőképen.

![Rögzítés az irányítópulton](media/get-started-portal/pin-dashboard.png)

Bizonyos egyszerűsítéseket a rendszer a diagramon az irányítópultra való rögzítésekor alkalmaz:

- Táblázat oszlopai és sorai: Egy tábla az irányítópultra való rögzítéséhez négy vagy kevesebb oszlopot kell tartalmaznia. Csak az első hét sor jelenik meg.
- Időkorlát: A lekérdezések automatikusan az elmúlt 14 napra korlátozódnak.
- Raktárhelyek számának korlátozása: Ha olyan diagramot jelenít meg, amely sok különálló raktárhelytel rendelkezik, a kevésbé feltöltött raktárhelyek automatikusan egyetlen _másik_ raktárhelyre vannak csoportosítva.

## <a name="save-queries"></a>Lekérdezések mentése
Ha létrehozott egy hasznos lekérdezést, érdemes lehet mentenie vagy megosztani másokkal. A **Mentés** ikon a felső sávon található.

A teljes lekérdezés lapot vagy egyetlen lekérdezést is menthet a függvényként. A függvények olyan lekérdezések, amelyekre más lekérdezések is hivatkozhatnak. A lekérdezés függvényként való mentéséhez meg kell adnia egy függvény aliasát, amely a lekérdezés hívásához használt név, ha más lekérdezések hivatkoznak rá.

![Függvény mentése](media/get-started-portal/save-function.png)

>[!NOTE]
>A következő karakterek támogatottak `a–z, A–Z, 0-9, -, _, ., <space>, (, ), |` : a Name ( **név** ) mezőben a mentett lekérdezés mentésekor vagy szerkesztésekor.

Log Analytics lekérdezések mindig egy kiválasztott munkaterületre lesznek mentve, és az adott munkaterület más felhasználóival is megoszthatók.

## <a name="load-queries"></a>Lekérdezések betöltése
A lekérdezés Explorer ikonja a jobb felső sarokban található. Ez a kategória felsorolja az összes mentett lekérdezést. Emellett lehetővé teszi, hogy az adott lekérdezéseket kedvencekként megjelölve gyorsan megtalálja őket a jövőben. Kattintson duplán egy mentett lekérdezésre az aktuális ablakhoz való hozzáadásához.

![Lekérdezéskezelő](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Exportálás és megosztás hivatkozásként
Log Analytics számos exportálási módszert támogat:

- Excel Az eredmények mentése CSV-fájlként.
- Power BI: Az eredmények exportálása Power BIba. Részletekért lásd: [Azure monitor naplózási adatainak importálása Power BIba](../../azure-monitor/platform/powerbi.md) .
- Hivatkozás megosztása: A lekérdezés egy hivatkozásként is megosztható, amelyet azután más felhasználók küldhetnek és futtathatnak, akik ugyanahhoz a munkaterülethez férnek hozzá.

## <a name="next-steps"></a>További lépések

- További információ [Azure monitor log-lekérdezések írásához](get-started-queries.md).
