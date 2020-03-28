---
title: 'Oktatóanyag: A Log Analytics-lekérdezések első lépései'
description: Ebből az oktatóanyagból megtudhatja, hogyan írhat és kezelhet Azure Monitor naplólekérdezéseket az Azure Portalon a Log Analytics használatával.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 29e24166218a6757cded9d1b002321800ab0c073
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80055480"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Oktatóanyag: A Log Analytics-lekérdezések első lépései

Ez az oktatóanyag bemutatja, hogyan használhatja a Log Analytics segítségével az Azure Monitor naplólekérdezéseinek írását, végrehajtását és kezelését az Azure Portalon. A Log Analytics-lekérdezések segítségével kifejezéseket kereshet, trendeket azonosíthat, mintákat elemezhet, és számos más elemzési adatot biztosíthat az adatokból. 

Ebben az oktatóanyagban megtudhatja, hogyan használhatja a Log Analytics szolgáltatást a következőkre:

> [!div class="checklist"]
> * A naplóadat-séma ismertetése
> * Egyszerű lekérdezések írása és futtatása, valamint a lekérdezések időtartományának módosítása
> * Lekérdezési eredmények szűrése, rendezése és csoportosítása
> * Lekérdezési eredmények vizualizációinak megtekintése, módosítása és megosztása
> * Lekérdezések és eredmények mentése, betöltése, exportálása és másolása

A naplólekérdezésekről az [Azure Monitor naplólekérdezéseinek áttekintése](log-query-overview.md)című témakörben olvashat bővebben.<br/>
A naplólekérdezések írásával kapcsolatos részletes oktatóanyagról az [Azure Monitor naplólekérdezéseinek első lépései](get-started-queries.md)című témakörben található.

## <a name="open-log-analytics"></a>A Log Analytics megnyitása
A Log Analytics használatához be kell jelentkeznie egy Azure-fiókba. Ha nem rendelkezik Azure-fiókkal, [hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

Az oktatóanyag legtöbb lépésének végrehajtásához használhatja [ezt a bemutatókörnyezetet,](https://portal.loganalytics.io/demo)amely számos mintaadatot tartalmaz. A bemutató környezetben nem mentheti a lekérdezéseket, és nem rögzítheti az eredményeket az irányítópulton.

Saját környezetét is használhatja, ha az Azure Monitor használatával legalább egy Azure-erőforrásnapló-adatokat gyűjt. A Log Analytics-munkaterület megnyitásához az Azure Monitor bal oldali navigációs sávján válassza a **Naplók**lehetőséget. 

## <a name="understand-the-schema"></a>A séma bemutatása
A *séma* logikai kategóriák ba csoportosított táblák gyűjteménye. A Demo séma több kategóriában figyelési megoldások. A **LogManagement** kategória például Windows- és Syslog-eseményeket, teljesítményadatokat és ügynökszívveréseket tartalmaz.

A sématáblák a Log Analytics munkaterület **Táblák** lapján jelennek meg. A táblázatok oszlopokat tartalmaznak, amelyek adattípusát az oszlop neve melletti ikon jelzi. **Az Esemény** tábla például olyan szövegoszlopokat tartalmaz, mint **a Számítógép** és a numerikus oszlopok, például **az EventCategory**.

![Séma](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Alaplekérdezések írása és futtatása

A Log Analytics egy új üres lekérdezéssel nyílik meg a **Lekérdezés szerkesztőben.**

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Lekérdezés írása
Az Azure Monitor naplólekérdezések a Kusto lekérdezési nyelv egy verzióját használják. A lekérdezések táblanévvel vagy [keresési](/azure/kusto/query/searchoperator) paranccsal kezdődhetnek. 

A következő lekérdezés az Összes rekordot beolvassa az **Esemény** táblából:

```Kusto
Event
```

A cső (|) karakter elválasztja a parancsokat, így az első parancs kimenete a következő parancs bemenete. Egyetlen lekérdezéshez tetszőleges számú parancsot adhat hozzá. A következő lekérdezés lekéri a rekordokat az **Esemény** táblából, majd megkeresi őket a **kifejezéshiba** szempontjából bármely tulajdonságban:

```Kusto
Event 
| search "error"
```

Az egyetlen sortörés megkönnyíti a lekérdezések olvasását. Egynél több sortörés osztja fel a lekérdezést külön lekérdezésekre.

Ugyanazt a lekérdezést a következő módon lehet megírni:

```Kusto
search in (Event) "error"
```

A második példában a **keresési** parancs csak az **Események** táblában lévő rekordokat keresi meg a **hiba**kifejezésre.

Alapértelmezés szerint a Log Analytics a lekérdezéseket az elmúlt 24 óra időtartományára korlátozza. Ha másik időtartományt szeretne beállítani, explicit **timeGenerated szűrőt** adhat a lekérdezéshez, vagy használhatja az **Időtartomány** vezérlőt.

### <a name="use-the-time-range-control"></a>Az Időtartomány vezérlő használata
Az **Időtartomány** vezérlő elem ének használatához jelölje ki a felső sávon, majd jelöljön ki egy értéket a legördülő listából, vagy egyéni időtartomány létrehozásához válassza az **Egyéni** lehetőséget.

![Időválasztó](media/get-started-portal/time-picker.png)

- Az időtartomány-értékek UTC-ben vannak, ami eltérhet a helyi időzónától.
- Ha a lekérdezés explicit módon beállít egy szűrőt a **TimeGenerated**számára, az időválasztó vezérlőben megjelenik **a Készlet a lekérdezésben**, és az ütközés megelőzése érdekében le van tiltva.

### <a name="run-a-query"></a>Lekérdezés futtatása
Lekérdezés futtatásához helyezze a kurzort a lekérdezésen belülre, és válassza a **Futtatás** gombot a felső sávon, vagy nyomja le a **Shift**+**Enter billentyűt.** A lekérdezés addig fut, amíg üres sort nem talál.

## <a name="filter-results"></a>Szűrés eredményei
A Log Analytics legfeljebb 10 000 rekordra korlátozza az eredményeket. Egy általános `Event` lekérdezés, mint túl sok eredményt ad vissza ahhoz, hogy hasznos legyen. A lekérdezés eredményeit szűrheti a lekérdezés táblaelemeinek korlátozásával, vagy az eredményekhez való kifejezett hozzáadásával. A táblaelemek szűrése új eredményhalmazt ad vissza, míg egy explicit szűrő a meglévő eredményhalmazra vonatkozik.

### <a name="filter-by-restricting-table-elements"></a>Szűrés táblaelemek korlátozásával
A `Event` lekérdezés eredményének **hibaeseményekre** történő szűrése a lekérdezés táblaelemeinek korlátozásával:

1. A lekérdezés eredményében jelölje ki a hiba oszlopban **hibával** beírt rekordok melletti legördülő **nyilat.** 
   
1. A kibontott részletekben mutasson az **EventLevelName**( Eseménynév ) melletti **...** elemre, majd válassza **a "Hiba" belefoglalása**lehetőséget. 
   
   ![Szűrő hozzáadása a lekérdezéshez](media/get-started-portal/add-filter.png)
   
1. Figyelje meg, hogy a **Lekérdezésszerkesztő** lekérdezése a következőre változott:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Az új lekérdezés futtatásához válassza a **Futtatás** lehetőséget.

### <a name="filter-by-explicitly-filtering-results"></a>Szűrés az eredmények explicit szűrésével
A `Event` lekérdezés eredményeinek **hibaeseményekre** történő szűrése a lekérdezés eredményének szűrésével:

1. A lekérdezés eredményében válassza a **Szűrő ikont** az **EventLevelName**oszlop fejléce mellett. 
   
1. Az előugró ablak első mezőjében válassza az **Egyenlő a**lehetőséget, és a következő mezőbe írja be a *hibát.* 
   
1. Válassza **a Szűrő**lehetőséget.
   
   ![Szűrés](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Oszlopok rendezése, csoportosítása és kijelölése
Ha a lekérdezés eredményeit egy adott oszlop , például **a TimeGenerated [UTC]** szerint szeretné rendezni, jelölje ki az oszlopfejlécet. Jelölje ki ismét a címsort a növekvő és a csökkenő sorrend közötti váltáshoz.

![Oszlop rendezése](media/get-started-portal/sort-column.png)

Az eredmények rendszerezésének másik módja a csoportok. Ha az eredményeket egy adott oszlop szerint szeretné csoportosítani, húzza az oszlopfejlécet az eredménytáblázat feletti **sávra: Húzza ide az oszlopfejlécet, és ide dobja az oszlop szerint csoportosítva**. Alcsoportok létrehozásához húzza a többi oszlopot a felső sávra. Átrendezheti a sávban lévő csoportok és alcsoportok hierarchiáját és rendezését.

![Csoportok](media/get-started-portal/groups.png)

Ha el szeretné rejteni vagy meg szeretné jelenik az oszlopok at az eredmények között, jelölje be a táblázat feletti **Oszlopok jelölőnégyzetet,** majd jelölje ki vagy törölje a kívánt oszlopok kijelölését a legördülő listából.

![Oszlopok kiválasztása](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Diagramok megtekintése és módosítása
A lekérdezési eredmények vizuális formátumban is láthatók. Példaként írja be a következő lekérdezést:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Alapértelmezés szerint az eredmények egy táblázatban jelennek meg. A táblázat feletti **diagram** elemre a grafikus nézetben való eredmény megtekintéséhez válassza a diagramot.

![Sávdiagram](media/get-started-portal/bar-chart.png)

Az eredmények halmozott sávdiagramon jelennek meg. Az eredmények egyéb nézeteinek megjelenítéséhez adja meg az egyéb beállításokat, **például** a Halmozott oszlop ot vagy a **Körpitét.**

![Tortadiagram](media/get-started-portal/pie-chart.png)

A nézet tulajdonságait (például x és y tengelyeket, vagy csoportosítási és felosztási beállításokat) manuálisan módosíthatja a vezérlősávról.

Az előnyben részesített nézetet magát a lekérdezést is beállíthatja a [leképezési](/azure/kusto/query/renderoperator) operátor használatával.

## <a name="pin-results-to-a-dashboard"></a>Eredmények rögzítése irányítópulton
Ha egy eredménytáblázatot vagy diagramot szeretne rögzíteni a Log Analytics szolgáltatásból egy megosztott Azure-irányítópultra, válassza a **Rögzítés az irányítópulthoz** lehetőséget a felső sávon. 

![Rögzítés az irányítópulton](media/get-started-portal/pin-dashboard.png)

A **Pin egy másik irányítópultra** ablaktáblában jelölje ki vagy hozzon létre egy megosztott irányítópultot, amelyet rögzíteni szeretne, majd válassza **az Alkalmaz**lehetőséget. A táblázat vagy diagram megjelenik a kiválasztott Azure-irányítópulton.

![Irányítópultra rögzített diagram](media/get-started-portal/pin-dashboard2.png)

A megosztott irányítópultra kitűzött táblázat vagy diagramok a következő egyszerűsítéseket követik: 

- Az adatok az elmúlt 14 napra korlátozódnak.
- Egy táblázatban legfeljebb négy oszlop és az első hét sor látható.
- A sok különálló kategóriát tartalmazó diagramok automatikusan csoportosítják a kevésbé lakott kategóriákat egyetlen **másik** raktárhelybe.

## <a name="save-load-or-export-queries"></a>Lekérdezések mentése, betöltése és exportálása
A lekérdezés létrehozása után mentheti vagy megoszthatja a lekérdezést vagy az eredményeket másokkal. 

### <a name="save-queries"></a>Lekérdezések mentése
Lekérdezés mentése:

1. Válassza a **Mentés** gombot a felső sávon.
   
1. A **Mentés** párbeszédpanelen adjon nevet a **lekérdezésnek**az a–z, A–Z, 0-9, szóköz, kötőjel, aláhúzás, pont, zárójel vagy cső használatával. 
   
1. Adja meg, hogy a lekérdezést **lekérdezésként** vagy **függvényként**szeretné-e menteni. A függvények olyan lekérdezések, amelyekre más lekérdezések hivatkozhatnak. 
   
   Ha függvényként szeretne menteni egy lekérdezést, adjon meg egy **függvényaliast**, amely a lekérdezés hívásához használható többi lekérdezés rövid neve.
   
1. Adjon meg egy **kategóriát** a lekérdezéshez használandó **Lekérdezéskezelőszámára.**
   
1. Kattintson a **Mentés** gombra.
   
   ![Mentés funkció](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Lekérdezések betöltése
Mentett lekérdezés betöltéséhez válassza a jobb felső sarokban a **Lekérdezéskezelő lehetőséget.** Megnyílik **a Lekérdezéskezelő** ablaktábla, amely az összes lekérdezést kategória szerint listázza. Bontsa ki a kategóriákat, vagy írjon be egy lekérdezésnevet a keresősávba, majd válasszon egy lekérdezést a **Lekérdezésszerkesztőbe**való betöltéséhez. A lekérdezést **kedvencként** is megjelölheti, ha kiválasztja a lekérdezés neve melletti csillagot.

![Lekérdezéskezelő](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Lekérdezések exportálása és megosztása
Lekérdezés exportálásához válassza az **Exportálás** elemet a felső sávon, majd válassza az **Exportálás CSV-re – az összes oszlop**, a **CSV -, a megjelenített oszlopok ,** vagy a Power BI **(M lekérdezés) exportálása** lehetőséget a legördülő listából.

Ha lekérdezésre mutató hivatkozást szeretne megosztani, válassza a felső **sávhivatkozás másolása,** majd a **Hivatkozás másolása**a lekérdezéshez lehetőséget, **a Lekérdezés szövegének másolása**vagy a vágólapra másolandó **lekérdezéseredmények másolása** lehetőséget. A lekérdezési hivatkozást elküldheti másoknak, akik hozzáférnek ugyanahhoz a munkaterülethez.

## <a name="next-steps"></a>További lépések

Továbbad a következő oktatóanyaghoz, ha többet szeretne megtudni az Azure Monitor naplólekérdezéseinek írásáról.
> [!div class="nextstepaction"]
> [Azure-figyelő naplólekérdezéseinek írása](get-started-queries.md)
