---
title: A kiterjesztett Spark-előzménykiszolgáló használata alkalmazások hibakereséséhez – Apache Spark az Azure Synapse-ban
description: A kiterjesztett Spark-előzménykiszolgáló használatával hibakeresés és a Spark-alkalmazások diagnosztizálása az Azure Synapse Analytics szolgáltatásban.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429212"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Az Apache Spark előzménykiszolgálójának használata az Apache Spark-alkalmazások hibakereséséhez és diagnosztizálásához

Ez a cikk útmutatást nyújt a kiterjesztett Apache Spark előzménykiszolgáló használatával a befejezett és a Spark-alkalmazások diagnosztizálására.

A bővítmény tartalmaz egy adatlapot, grafikon lapot és **Data** a Diagnosztika lapot. A **Diagram** lapon látható a feladatgrafikon adatfolyama és visszajátszása. A **Diagnosztika** lapon **megjelenik az Adatdöntés**, **az Idődöntés**és **a Végrehajtó használati elemzése.**

## <a name="access-the-apache-spark-history-server"></a>Az Apache Spark előzménykiszolgálójának elérése

Az Apache Spark előzménykiszolgálója a Spark-alkalmazások elkészült és futó webes felhasználói felülete. Megnyithatja az Apache Spark előzménykiszolgálówebes felületét az Azure Synapse Analytics szolgáltatásból.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>A Spark History Server webes felhasználói felületének megnyitása az Apache spark-alkalmazások csomópontjáról

1. Nyissa meg [az Azure Synapse Analytics szolgáltatást.](https://web.azuresynapse.net/)

2. Kattintson **a Monitor**gombra, majd válassza **az Apache Spark-alkalmazások lehetőséget.**

    ![Kattintson a monitor, majd válassza ki a spark alkalmazás.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Jelöljön ki egy alkalmazást, majd kattintással nyissa meg a **Naplólekérdezést.**

    ![Nyissa meg a naplólekérdezés ablakát.](./media/apache-spark-history-server/open-application-window.png)

4. Válassza a **Spark előzménykiszolgálóját**, majd megjelenik a Spark History Server webes felhasználói felülete.

    ![Nyissa meg a spark előzménykiszolgálóját.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>A Spark History Server webes felhasználói felületének megnyitása az adatcsomópontból

1. Az Azure Synapse Studio-jegyzetfüzetből válassza a **Spark-előzménykiszolgálót** a feladat-végrehajtási kimeneti cellából vagy a jegyzetfüzetdokumentum alján található állapotpanelről. Válassza a **Munkamenet részletei** lehetőséget.

   ![A Spark előzménykiszolgálójának indítása](./media/apache-spark-history-server/launch-history-server2.png "A Spark előzménykiszolgálójának indítása")

2. Válassza a **Spark előzménykiszolgálóját** a kicsúsztatási panelen.

   ![A Spark előzménykiszolgálójának indítása](./media/apache-spark-history-server/launch-history-server.png "A Spark előzménykiszolgálójának indítása")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Az Adatok lap felfedezése a Spark előzménykiszolgálójában

Válassza ki a megtekinteni kívánt feladat feladatazonosítót. Ezután válassza az adatok nézet ének lefelvételéhez válassza az Eszköz menü **Adatok** parancsát. Ez a szakasz bemutatja, hogyan hajthatja végre a különböző feladatokat az Adatok lapon.

* A lapok külön-külön való kijelölésével ellenőrizze a **bemenetek,** **a kimenetek**és a **táblázatműveletek műveleteit.**

    ![A Spark alkalmazáslapjainak adatai](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Az összes sor másolása a **Másolás**lehetőség kiválasztásával.

    ![A Spark-alkalmazás másolásának adatai](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Az összes adat mentése CSV fájlként a **csv**kiválasztásával.

    ![A Spark-alkalmazások mentéséhez való adatok](./media/apache-spark-history-server/apache-spark-data-save.png)

* Keresés a keresés **mezőbe**kulcsszavak megadásával. A keresési eredmények azonnal megjelennek.

    ![A Spark-alkalmazások kereséséhez megadott adatok](./media/apache-spark-history-server/apache-spark-data-search.png)

* Jelölje ki a táblázat rendezéséhez az oszlopfejlécet, a pluszjel kiválasztásával bontsa ki a sort, hogy további részleteket jelenítsen meg, vagy válassza a mínuszjelet a sor összecsukásához.

    ![A Spark-alkalmazás táblájának adatai](./media/apache-spark-history-server/apache-spark-data-table.png)

* Egyetlen fájl letöltése a **Részleges letöltés**lehetőség kiválasztásával. A kijelölt fájl a helyi fájlba töltődik le. Ha a fájl már nem létezik, egy új lap jelenik meg egy hibaüzenettel.

    ![A Spark alkalmazás letöltési sorának adatai](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Teljes vagy relatív elérési út másolásához jelölje be a **Teljes elérési út másolása** vagy a **Relatív elérési út másolása** beállításokat a legördülő menüből. Az Azure Data Lake Storage-fájlok esetén **az Open in Azure Storage Explorer** elindítja az Azure Storage Exploreralkalmazást, és megkeresi a mappát, amikor be van jelentkezve.

    ![A Spark-alkalmazások másolási útvonalának adatai](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* A táblázat alatti oldalszámok kijelölésével navigálhat az oldalakon, ha túl sok sor van egy oldalon.

    ![A Spark alkalmazásának adatai lap](./media/apache-spark-history-server/apache-spark-data-page.png)

* Mutasson az **Adatok** melletti kérdőjelre az elemleírás megjelenítéséhez, vagy válassza ki a kérdőjelet, hogy további információkat kapjon.

    ![Adatok a Spark alkalmazáshoz további információ](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Küldjön visszajelzést a problémákkal kapcsolatban a **Visszajelzés küldése**lehetőséget választva.

    ![A Spark graph ismét visszajelzést ad nekünk](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>A Graph lap az Apache Spark előzménykiszolgálójában

Válassza ki a megtekinteni kívánt feladat feladatazonosítót. Ezután válassza a **Diagram** lehetőséget az eszköz menüjében a feladatgrafikon nézet lefelvételéhez.

### <a name="overview"></a>Áttekintés

A létrehozott feladatgrafikonon megtekintheti a feladat áttekintését. Alapértelmezés szerint a grafikon az összes feladatot megjeleníti. Ezt a nézetet **feladatazonosító**val szűrheti.

![Spark-alkalmazás- és feladatgráffeladat-azonosító](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Megjelenítés

Alapértelmezés szerint a **Folyamat** kijelző van kiválasztva. Az adatfolyamot a **Megjelenítés** legördülő listában az **Olvasás** vagy a **Írás** lehetőséget választva ellenőrizheti.

![Spark-alkalmazás és feladatgrafikon megjelenítése](./media/apache-spark-history-server/sparkui-graph-display.png)

A grafikoncsomópont a hőtérkép jelmagyarázatában látható színeket jeleníti meg.

![Spark alkalmazás és feladatgrafikon hőtérkép](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Lejátszás

A feladat lejátszásához válassza a **Lejátszás**lehetőséget. A leállításhoz bármikor kiválaszthatja a **Leállítás** lehetőséget. A feladat színei különböző állapotokat mutatnak a lejátszás során:

|Color|Jelentés|
|-|-|
|Zöld|Sikeresen: A feladat sikeresen befejeződött.|
|Narancssárga|Újrapróbált: Olyan feladatok példányai, amelyek sikertelenek, de nem befolyásolják a feladat végeredményét. Ezeka feladatok olyan duplikált vagy újrapróbálkozási példányokkal voltak meg, amelyek később sikeresek lehetnek.|
|Kék|Futás: A feladat fut.|
|Fehér|Várakozás vagy kihagyás: A feladat futásra vár, vagy a szakasz kimarad.|
|Vörös|Sikertelen: A feladat nem sikerült.|

Az alábbi képen a zöld, narancs és kék állapotszínek láthatók.

![Spark alkalmazás- és feladatgrafikonszínminta futtatása](./media/apache-spark-history-server/sparkui-graph-color-running.png)

Az alábbi képen a zöld és a fehér állapotszín látható.

![Spark alkalmazás és feladatgrafikon színminta, kihagyás](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

Az alábbi képen a piros és a zöld állapot színét látható.

![Spark-alkalmazás és feladatgrafikon színminta, sikertelen](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Az egyes feladatokkal való lejátszás engedélyezett. Hiányos feladatok esetén a lejátszás nem támogatott.

### <a name="zoom"></a>Zoom

Az egérgörgetéssel nagyíthatja és kicsinyítheti a feladatgrafikont, vagy a **Nagyítás gombra, hogy** illeszkedjen a képernyőhöz.

![Spark alkalmazás és a feladat grafikon zoom, hogy illeszkedjen](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Elemleírások

Mutasson a diagramcsomópontra az elemleírás megtekintéséhez, ha sikertelen tevékenységek vannak, és jelöljön ki egy szakaszt a színpadi lap megnyitásához.

![Spark-alkalmazás és állásgrafikon elemleírás](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

A feladatgrafikon lapon a szakaszokon megjelenik egy elemleírás és egy kis ikon, ha olyan feladatokkal rendelkeznek, amelyek megfelelnek az alábbi feltételeknek:

|Állapot|Leírás|
|-|-|
|Adatdöntés|az adatok olvasási mérete > a fázison belüli összes feladat átlagos olvasási mérete * 2 és az adatok olvasási mérete > 10 MB|
|Idődöntés|végrehajtási idő > a fázison belüli összes feladat átlagos végrehajtási ideje * 2 és a végrehajtási idő 2 perc >|
   
![A Spark alkalmazás és a feladatgrafikon döntési ikonja](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>A grafikoncsomópont leírása

A feladatgrafikon-csomópont az egyes fázisok következő adatait jeleníti meg:

  * Id.
  * Név vagy leírás.
  * Teljes feladatszám.
  * Az adatok olvasása: a bemeneti méret és a véletlen sorrendű olvasási méret összege.
  * Adatírás: a kimeneti méret és a véletlen sorrendű írási arány összege.
  * Végrehajtási idő: az első kísérlet kezdési időpontja és az utolsó kísérlet befejezési időpontja közötti idő.
  * Sorszám: bemeneti rekordok, kimeneti rekordok, véletlen sorrendű olvasási rekordok és véletlen sorrendű írási rekordok összege.
  * Haladás.

    > [!NOTE]  
    > Alapértelmezés szerint a feladatgrafikon-csomópont az egyes fázisok utolsó kísérletének adatait jeleníti meg (kivéve a fázis végrehajtási idejét). A lejátszás során azonban a gráfcsomópont az egyes próbálkozások adatait jeleníti meg.
    >  
    > Az olvasási és írási adatok mérete 1 MB = 1000 KB = 1000 * 1000 bájt.

### <a name="provide-feedback"></a>Visszajelzés küldése

Küldjön visszajelzést a problémákkal kapcsolatban a **Visszajelzés küldése**lehetőséget választva.

![Spark-alkalmazás és állásgrafikon-visszajelzés](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>A Diagnosztika lap felfedezése az Apache Spark előzménykiszolgálójában

A Diagnosztika lap eléréséhez válassza ki a feladatazonosítóját. Ezután válassza **a Diagnosztika** lehetőséget az eszköz menüben a feladatdiagnosztika nézet belátásához. A diagnosztika lapon megtalálható **az Adatdöntés**, **az Idődöntés**és **a Végrehajtó használati elemzése.**

A lapok kiválasztásával ellenőrizze az **Adatdöntés**, **az Idődöntés**és **a Végrehajtó használati elemzés** című táblázatot.

![SparkUI diagnózis adatok döntés lap újra](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Adatdöntés

Ha az **Adatdöntés lapot választja,** a megfelelő ferde feladatok a megadott paraméterek alapján jelennek meg.

* **Paraméterek megadása** – Az első szakasz megjeleníti az okat a paramétereket, amelyek az adatdöntés észlelésére szolgálnak. Az alapértelmezett szabály a következő: A tevékenységadatok olvasása meghaladja az átlagos feladatadatok háromszorosát, az olvasott tevékenységadatok pedig 10 MB-nál nagyobb. Ha saját szabályt szeretne definiálni a ferde feladatokhoz, kiválaszthatja a paramétereket, a **Ferde szakasz** és a **Döntési karakter** szakaszok ennek megfelelően frissülnek.

* **Ferde szakasz** – A második szakasz azokat a szakaszokat jeleníti meg, amelyek a fent meghatározott feltételeknek megfelelő tevékenységeket tartalmaznak. Ha egy fázisban egynél több ferde feladat van, a ferde szakasztábla csak a legferdebb tevékenységet jeleníti meg (például az adatdöntés legnagyobb adatait).

    ![sparkui diagnózis adatok döntés lap](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Döntésdiagram** – Ha a döntési szakasz tábla egy sora van kiválasztva, a döntésdiagram több tevékenységeloszlási részletet jelenít meg az adatok olvasási és végrehajtási ideje alapján. A ferde feladatok piros, a normál feladatok pedig kék színnel vannak megjelölve. A diagram legfeljebb 100 mintafeladatot jelenít meg, a tevékenység részletei pedig a jobb alsó panelen jelennek meg.

    ![sparkui skew diagram a 10.](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Idő döntés

Az **Időeltérés** lap a feladat végrehajtási ideje alapján jeleníti meg a ferde feladatokat.

* **Paraméterek megadása** – Az első szakasz megjeleníti az időeltérés észlelésére használt paramétereket. Az időeltérés észlelésének alapértelmezett feltétele: a feladat végrehajtási ideje az átlagos végrehajtási idő háromszorosa, a feladat végrehajtási ideje pedig 30 másodpercnél hosszabb. A paramétereket az igényeid nek megfelelően módosíthatja. A **Ferde szakasz** és a **Döntésdiagram** a fenti **Adatdöntés** laphoz hasonlóan megjeleníti a megfelelő szakaszokat és feladatokat.

* Válassza **az Időeltérés**lehetőséget, majd a szűrt eredmény megjelenik a **Ferde szakasz** szakaszban a Paraméterek **megadása**szakaszban beállított paramétereknek megfelelően. Jelöljön ki egy elemet a **Ferde szakasz** szakaszban, majd a megfelelő diagramot a 3.

    ![sparkui diagnózis idő döntés szakasz](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Végrehajtó használatának elemzése

A Végrehajtó használati graph vizualizálja a Spark-feladat végrehajtóinak lefoglalási és futási állapotát.  

1. Válassza **a Végrehajtó használatának elemzése lehetőséget,** majd négy típus görbét készít a végrehajtó használatáról, beleértve **a kiosztott végrehajtókat,** **a végrehajtók futtatása**, **az indle executorok**és **a Maximális végrehajtó példányok lehetőséget.** A kiosztott végrehajtók esetében minden "Végrehajtó hozzáadott" vagy "Végrehajtó eltávolítva" esemény növeli vagy csökkenti a kiosztott végrehajtókat. Az "Esemény idővonala" gombra kattintva további összehasonlítást kaphat a "Feladatok" lapon.

   ![sparkui diagnózis végrehajtók lap](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Jelölje ki a színikont a megfelelő tartalom kijelöléséhez, vagy törölje belőle a jelet az összes piszkozatban.

    ![sparkui diagnózisok válassza diagram](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Ismert problémák

A rugalmas elosztott adatkészleteket (RDD) használó bemeneti/kimeneti adatok nem jelennek meg az adatok lapon.

## <a name="next-steps"></a>További lépések

- [Azure Synapse Analytics](../overview-what-is.md)
- [Az Apache Spark dokumentációja .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

