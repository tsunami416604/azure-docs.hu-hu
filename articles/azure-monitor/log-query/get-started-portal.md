---
title: 'Oktatóanyag: Log Analytics lekérdezések első lépései'
description: Ebből az oktatóanyagból megtudhatja, hogyan írhat és kezelhet Azure Monitor naplózási lekérdezéseket a Azure Portal Log Analytics használatával.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 29e24166218a6757cded9d1b002321800ab0c073
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80055480"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Oktatóanyag: Log Analytics lekérdezések első lépései

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Log Analyticst Azure Monitor-naplók írásához, végrehajtásához és kezeléséhez a Azure Portal. Log Analytics lekérdezésekkel megkeresheti a kifejezéseket, azonosíthatja a trendeket, elemezheti a mintákat, és számos más elemzést is biztosíthat az adatokból. 

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Log Analytics:

> [!div class="checklist"]
> * A naplózási Adatséma ismertetése
> * Egyszerű lekérdezések írása és futtatása, valamint a lekérdezések időtartományának módosítása
> * Lekérdezés eredményeinek szűrése, rendezése és csoportosítása
> * A lekérdezési eredmények vizualizációinak megtekintése, módosítása és megosztása
> * Lekérdezések és eredmények mentése, betöltése, exportálása és másolása

További információ a naplózási lekérdezésekről: [a Azure monitorban található lekérdezések áttekintése](log-query-overview.md).<br/>
A naplók lekérdezésével kapcsolatos részletes oktatóanyagért lásd: az [első lépések a naplózási lekérdezésekkel kapcsolatban Azure monitor](get-started-queries.md).

## <a name="open-log-analytics"></a>Log Analytics megnyitása
Log Analytics használatához be kell jelentkeznie egy Azure-fiókba. Ha nem rendelkezik Azure-fiókkal, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

A jelen oktatóanyagban ismertetett lépések többségének végrehajtásához használhatja [ezt a bemutató környezetet](https://portal.loganalytics.io/demo), amely számos mintaadatok beszámítását is magában foglalja. A bemutató környezettel nem mentheti a lekérdezéseket, és nem rögzítheti az eredményeket az irányítópulton.

Használhatja a saját környezetét is, ha a Azure Monitor használatával gyűjti be a naplózási adatokat legalább egy Azure-erőforráson. Log Analytics munkaterület megnyitásához a Azure Monitor bal oldali navigációs sávján válassza a **naplók**lehetőséget. 

## <a name="understand-the-schema"></a>A séma bemutatása
A *sémák* a logikai kategóriák alatt csoportosított táblák gyűjteményei. A bemutató séma több kategóriát is tartalmaz a figyelési megoldásokból. A **LogManagement** kategória például a Windows-és syslog-eseményeket, a teljesítményadatokat és az ügynök szívveréseit tartalmazza.

A séma táblái a Log Analytics munkaterület **táblák** lapján jelennek meg. A táblák oszlopokat tartalmaznak, amelyek mindegyike az oszlop neve melletti ikon által megjelenített adattípussal rendelkezik. Az **Event** tábla például olyan szöveges oszlopokat tartalmaz, mint a **számítógép** és a numerikus oszlopok, például a **EventCategory**.

![Séma](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Alapszintű lekérdezések írása és futtatása

A Log Analytics megnyílik egy új, üres lekérdezéssel a **lekérdezés-szerkesztőben**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Lekérdezés írása
Azure Monitor a naplózási lekérdezések a Kusto lekérdezési nyelvének egy verzióját használják. A lekérdezések egy Táblanév vagy egy [keresési](/azure/kusto/query/searchoperator) paranccsal kezdődhetnek. 

A következő lekérdezés az **Event** tábla összes rekordját lekérdezi:

```Kusto
Event
```

A pipe (|) karakter elválasztja a parancsokat, így az első parancs kimenete a következő parancs bemenete. Egyetlen lekérdezéshez tetszőleges számú parancsot adhat hozzá. Az alábbi lekérdezés lekérdezi a rekordokat az **esemény** táblából, majd megkeresi a kifejezési **hibát** bármely tulajdonságban:

```Kusto
Event 
| search "error"
```

Az egysoros töréspontok könnyebben olvashatóvá teszik a lekérdezéseket. A lekérdezés több sortöréssel külön lekérdezésekre oszlik.

Ugyanezt a lekérdezést egy másik módon is megírhatja:

```Kusto
search in (Event) "error"
```

A második példában a **Search** parancs csak az **Events (események** ) táblában lévő rekordokat keresi meg a kifejezés **hibája**miatt.

Alapértelmezés szerint a Log Analytics az elmúlt 24 órában megadott időtartományra korlátozza a lekérdezéseket. Egy másik időtartomány beállításához hozzáadhat egy explicit **TimeGenerated** szűrőt a lekérdezéshez, vagy használhatja az **időtartomány** vezérlőelemet is.

### <a name="use-the-time-range-control"></a>Az időtartomány vezérlőelem használata
Az **időtartomány** vezérlőelem használatához válassza ki azt a felső sávon, majd válasszon ki egy értéket a legördülő listából, vagy válassza az **Egyéni** lehetőséget az egyéni időtartomány létrehozásához.

![Időválasztó](media/get-started-portal/time-picker.png)

- Az időtartomány értékei UTC szerint vannak, ami eltér a helyi időzónától.
- Ha a lekérdezés explicit módon beállít egy szűrőt a **TimeGenerated**, akkor az időválasztó vezérlőelem a **lekérdezésben jelenik meg**, és le van tiltva, hogy megakadályozza az ütközést.

### <a name="run-a-query"></a>Lekérdezés futtatása
Lekérdezés futtatásához vigye a kurzort a lekérdezésbe, és válassza a **Futtatás** gombot a felső sávban, **vagy nyomja le**+az**ENTER**billentyűt. A lekérdezés addig fut, amíg nem talál üres sort.

## <a name="filter-results"></a>Szűrés eredményei
A Log Analytics legfeljebb 10 000 rekordra korlátozza az eredményeket. Egy általános lekérdezés, `Event` például túl sok eredményt ad vissza, hasznosnak bizonyulhat. A lekérdezési eredményeket szűrheti a lekérdezésben szereplő táblák elemeinek korlátozásával, vagy explicit módon hozzáadhat egy szűrőt az eredményekhez. A táblázat elemeinek szűrése új eredményhalmazt ad vissza, míg egy explicit szűrő a meglévő eredményhalmaz esetében érvényes.

### <a name="filter-by-restricting-table-elements"></a>Szűrés a táblázat elemeinek korlátozásával
A lekérdezés `Event` eredményeinek szűrése a **hibák** eseményeire a lekérdezésben szereplő táblák elemeinek korlátozásával:

1. A lekérdezés eredményei között válassza ki a legördülő nyilat minden olyan rekord mellett, amely a **EventLevelName** oszlopban **hibát** tartalmaz. 
   
1. A kibontott részletek között vigye a kurzort a **EventLevelName**, majd válassza a **...** elemet, majd válassza a **"hiba"** lehetőséget. 
   
   ![Szűrő hozzáadása a lekérdezéshez](media/get-started-portal/add-filter.png)
   
1. Figyelje meg, hogy a **lekérdezés szerkesztője** mostantól a következőre változott:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Válassza a **Futtatás** lehetőséget az új lekérdezés futtatásához.

### <a name="filter-by-explicitly-filtering-results"></a>Szűrés az eredmények explicit szűrésével
A `Event` lekérdezés eredményeinek szűrése a **hibák** eseményeire a lekérdezés eredményeinek szűrésével:

1. A lekérdezés eredményei között válassza a **szűrő** ikont a **EventLevelName**oszlopfejléc mellett. 
   
1. Az előugró ablak első mezőjében válassza a **egyenlő**elemet, majd a következő mezőben adja meg a *hiba*értéket. 
   
1. Válassza a **szűrő**lehetőséget.
   
   ![Szűrés](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Oszlopok rendezése, csoportosítása és kiválasztása
Ha a lekérdezési eredményeket egy adott oszlop szerint szeretné rendezni (például **TimeGenerated [UTC]**), válassza az oszlop fejlécét. A növekvő és a csökkenő sorrend közötti váltáshoz válassza újra a fejlécet.

![Oszlop rendezése](media/get-started-portal/sort-column.png)

Az eredmények rendezésének másik módja csoportok szerint történik. Ha egy adott oszlop alapján kívánja csoportosítani az eredményeket, húzza az oszlopfejléc fölé az oszlop fejlécét az **oszlop áthúzása feliratú oszlop fölé, és dobja el ide az oszlop csoportosításához**. Alcsoportok létrehozásához húzzon más oszlopokat a felső sávra. Átrendezheti a hierarchiát és a csoportok és alcsoportok rendezését a sávon.

![Csoportok](media/get-started-portal/groups.png)

Az eredmények oszlopainak elrejtéséhez vagy megjelenítéséhez válassza a tábla fölötti **oszlopok** lehetőséget, majd válassza ki vagy törölje a kívánt oszlopokat a legördülő listából.

![Oszlopok kiválasztása](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Diagramok megtekintése és módosítása
A lekérdezési eredményeket vizuális formátumokban is megtekintheti. Példaként adja meg a következő lekérdezést:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Alapértelmezés szerint az eredmények egy táblázatban jelennek meg. A táblázat fölötti **diagram** kiválasztásával megjelenítheti az eredményeket grafikus nézetben.

![Sávdiagram](media/get-started-portal/bar-chart.png)

Az eredmények egy halmozott oszlopdiagram-diagramon jelennek meg. Válasszon más beállításokat, például a **halmozott oszlopot** vagy a **tortadiagramot** az eredmények egyéb nézeteinek megjelenítéséhez.

![Tortadiagram](media/get-started-portal/pie-chart.png)

Megváltoztathatja a nézet tulajdonságait, például az x és az y tengelyt, vagy csoportosíthatja és feloszthatja a beállításokat manuálisan a vezérlő sávon.

Saját maga is megadhatja a lekérdezés előnyben részesített nézetét a [Render](/azure/kusto/query/renderoperator) operátor használatával.

## <a name="pin-results-to-a-dashboard"></a>Eredmények rögzítése egy irányítópulton
Egy eredmény táblázat vagy diagram Log Analyticsból egy megosztott Azure-irányítópultra való rögzítéséhez válassza a **rögzítés az irányítópulton** lehetőséget a felső sávon. 

![Rögzítés az irányítópulton](media/get-started-portal/pin-dashboard.png)

A **PIN-kód másik irányítópult** ablaktábláján válasszon ki vagy hozzon létre egy megosztott irányítópultot a rögzítéshez, majd kattintson az **alkalmaz**gombra. A táblázat vagy a diagram megjelenik a kiválasztott Azure-irányítópulton.

![Az irányítópultra rögzített diagram](media/get-started-portal/pin-dashboard2.png)

A megosztott irányítópulthoz rögzített táblázat vagy diagram a következő egyszerűsítésekkel rendelkezik: 

- Az adatmennyiség az elmúlt 14 napra korlátozódik.
- A tábla legfeljebb négy oszlopot és az első hét sort jeleníti meg.
- A sok különálló kategóriával rendelkező diagramok automatikusan csoportosítják a kevésbé feltöltött kategóriákat egyetlen **más** raktárhelyre.

## <a name="save-load-or-export-queries"></a>Lekérdezések mentése, betöltése vagy exportálása
A lekérdezés létrehozása után mentheti vagy megoszthatja a lekérdezést vagy az eredményeket másokkal. 

### <a name="save-queries"></a>Lekérdezések mentése
Lekérdezés mentése:

1. Válassza a **Mentés** lehetőséget a felső sávon.
   
1. A **Save (Mentés** ) párbeszédpanelen adja meg a lekérdezés **nevét**az A – Z, a – z, 0-9, szóköz, kötőjel, aláhúzás, időtartam, zárójel vagy cső karakterek használatával. 
   
1. Válassza ki, hogy **lekérdezésként** vagy **függvényként**kívánja-e menteni a lekérdezést. A függvények olyan lekérdezések, amelyekre más lekérdezések hivatkozhatnak. 
   
   A lekérdezés függvényként való mentéséhez adjon meg egy **függvény aliast**, amely egy rövid név a lekérdezés meghívásához használni kívánt egyéb lekérdezések számára.
   
1. Adja meg a lekérdezéshez használni kívánt **lekérdezési tallózó** **kategóriáját** .
   
1. Kattintson a **Mentés** gombra.
   
   ![Függvény mentése](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Lekérdezések betöltése
Mentett lekérdezés betöltéséhez válassza a jobb felső sarokban található **query Explorer** lehetőséget. Megnyílik a **query Explorer** panel, amely felsorolja az összes lekérdezést kategóriánként. Bontsa ki a kategóriákat, vagy adja meg a lekérdezés nevét a keresősávban, majd válasszon ki egy lekérdezést a **lekérdezés-szerkesztőbe**való betöltéshez. A lekérdezéseket megjelölheti **kedvencként** , ha kiválasztja a lekérdezés neve melletti csillagot.

![Lekérdezési tallózó](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Lekérdezések exportálása és megosztása
Egy lekérdezés exportálásához válassza az **Exportálás** lehetőséget a felső sávon, majd válassza az **Exportálás CSV-ként – minden oszlop**, **Exportálás CSV-ben megjelenített oszlopokra**lehetőséget, vagy **exportálja Power bi (M lekérdezés)** értékre a legördülő listából.

Ha egy lekérdezésre mutató hivatkozást szeretne megosztani, válassza a felső sávon a **hivatkozás másolása** lehetőséget, majd válassza a **hivatkozás másolása lekérdezéshez**, a **lekérdezési szöveg másolása**vagy a **lekérdezési eredmények** másolása lehetőséget a vágólapra másoláshoz. A lekérdezési hivatkozást elküldheti másoknak is, akik ugyanahhoz a munkaterülethez férnek hozzá.

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan lehet Azure Monitor napló lekérdezéseit írni.
> [!div class="nextstepaction"]
> [Írási Azure Monitor log-lekérdezések](get-started-queries.md)
