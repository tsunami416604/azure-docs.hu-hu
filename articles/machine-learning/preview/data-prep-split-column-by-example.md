---
title: "Azure Machine Learning-munkaterület használatával például átalakítással oszlop felosztásához"
description: "A \"Oszlop felosztása a példa alapján\" transzformáció a referenciadokumentum"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 5d624735a91d0828c4ac3796bde6c17acf6e131a
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="split-column-by-example-transformation"></a>Példa átalakítással osztott oszlop
A transzformáció predictively felhasználói beavatkozást nem igénylő felosztja a jelentéssel bíró határokon belül oszlop tartalmát. A felosztott algoritmus a határok kiválasztja az oszlop tartalmának elemzése után. Ezek a hatókörök sikerült határozza meg
* A rögzített elválasztó
* Több, tetszőleges határolójelek környezeteket, különösen megjelenő, vagy
* Adatok minták vagy bizonyos entitástípusok

Felhasználók is meghatározhatja, amelyen adja meg az elválasztó karaktert vagy által adja meg a kívánt megosztással példák a speciális módban felosztási viselkedését.

Elméletileg vegyes műveletek is végrehajthatók a munkaterület számos *Célosztályából oszlop példa alapján* alakítja. Azonban ha több oszlopot, egyes külön-külön is használja az-példa megközelítés való származtatás nem nagyon sok időt vesz igénybe. Prediktív vegyes lehetővé teszi, hogy könnyen felosztása a felhasználónak kell példákat biztosítanak az egyes oszlopok nélkül. 

## <a name="how-to-perform-this-transformation"></a>Ez a transzformáció végrehajtása

1. Válassza ki a felosztani kívánt oszlop. 
2. Válassza ki **osztott oszlop példa alapján** a a **átalakítja** menü. Vagy kattintson a jobb gombbal a kijelölt oszlop fejlécének, és válassza ki **osztott oszlop példa alapján** a helyi menüből. Megnyílik a átalakítási szerkesztő, és új oszlopokat ad hozzá a kijelölt oszlop mellett. Ezen a ponton a munkaterületet üzemeltető elemzi a bemeneti oszlop határozza meg a vegyes határokat és egy programot, melyet a oszlop felosztásához a rács megjelenő synthesizes. A szintetizált program végrehajtása elleni oszlopban az összes sort. Egyes elválasztó karakterek, ha bármely, a rendszer kizárja a végeredményt.
3. Kattintson a **speciális módban** a felosztás átalakítása szabályozásához. 
4. Tekintse át a kimenetet, és kattintson **OK** az átalakítás fogadásához. 

A transzformáció bemeneti célja azonos számú sorait eredő oszlopok létrehozásához. Ha bármely sor határoz meg a határokon belül nem lehet megosztani, képes *null* alapértelmezés szerint az oszlopok. Ez a viselkedés módosítható a **Speciális üzemmód**.

### <a name="transform-editor-advanced-mode"></a>Átalakítás szerkesztő: speciális módban
**Speciális módot,** gazdagabb élményt nyújt vágását meghatározó oszlop. 

Kiválasztása **elválasztó oszlopok megtartása** az elválasztó karaktert tartalmaz a végeredményt. Alapértelmezés szerint kizárt elválasztó karaktert.

Adja meg **határolójelek** felülbírálja az elválasztó automatikus lemezválasztási logika. Több egyes elválasztó karakterek, minden sor egy-egy adhat meg **határolójelek**. Ezek a karakterek vannak elválasztó karakterként felosztásához használt oszlop.

Egyes esetekben a felosztás értéket határoz meg határok előállított oszlopaik száma különböző mint mások a többsége a. Ezekben az esetekben **irányát adja** határozható meg, amelyben meg kell adni az oszlopok sorrendje.

Kattintson a **javasolt példák megjelenítése** jeleníti meg a reprezentatív sort, amelyben a felhasználói vegyes példát kell biztosítania. Felhasználói rákattinthat a **mentése** javasolt sorának előléptetése példaként sor jobbra látható nyílra. 

Felhasználó **törlése oszlop** vagy **új oszlopok beszúrása** fejlécének kattintson a jobb gombbal a **példák tábla**.

Felhasználók átmásolhatják, és a beillesztési értéket egy cella másik ahhoz, hogy adjon meg egy példát álló.

Felhasználó válthat a **alapvető mód** és a **Speciális üzemmód** átalakítási-szerkesztőben hivatkozásokra kattintva.

### <a name="transform-editor-send-feedback"></a>Átalakítás szerkesztő: visszajelzés küldése

Kattintson a a **visszajelzés küldése** hivatkozás megnyílik a **visszajelzés** megadta a Megjegyzések mezőben előre feltöltve a kiválasztott paraméterek és a példák felhasználó párbeszédpanel megnyitása. Felhasználói tekintse át a Megjegyzések mezőbe tartalmát és további részleteket, és segítsen megérteni a problémát. Ha a felhasználó nem szeretné adatok megosztása a Microsofttal, felhasználói való kattintás előtt törölje a előfeltöltött példaadatokat a **visszajelzés küldése** gombra. 


### <a name="editing-an-existing-transformation"></a>Egy meglévő átalakítást szerkesztése

A felhasználó már meglévő **minta alapján oszlop felosztása** kiválasztásával átalakítási **szerkesztése** átalakítása lépés lehetőséget. Kattintson a **szerkesztése** a átalakítási megnyitása a **Speciális üzemmód**, és minden a példa, amelyeket az átalakítás létrehozása során.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>A rögzített, karaktert elválasztó felosztásával példák

Esetében gyakori, adatmezőkhöz egyetlen rögzített elválasztó például egy CSV formátumú vesszővel kell elválasztani. A felosztott átalakítása megpróbálja automatikusan következtethető ki ezeket az elválasztó karaktert. Például az alábbi példa azt automatikusan kikövetkezteti a "." elválasztó.

### <a name="splitting-ip-addresses"></a>A felosztás IP-címek

Az első oszlop értékeit predictively osztották négy oszlopot.

|IP|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>A felosztás az adott kontextusban belül több határolójelek példák

A felhasználói adatok tartalmazhatják a különböző mezők elválasztó számos különböző elválasztó karaktert. Ezenkívül egy határoló karakterláncot csak néhány előfordulásának lehet elválasztó, de nem mindegyik. Például a következő esetben szükséges halmaz van "-",","és":" a kívánt kimeneti létrehozásához. Azonban nem minden előfordulását a ":" a szétválási pont kell lennie, mivel nem szeretnénk ossza fel a idő, de legyen egy oszlopban. A felosztott átalakítása kikövetkezteti a környezet elő az elválasztó minden lehetséges előfordulásának helyett a bemeneti adatok belül elválasztó karaktert. A transzformáció bemeneti is tehát tudatában általános adattípusok, például a dátum és idő.   

### <a name="splitting-store-opening-timings"></a>A felosztás tároló megnyitása időzítés

Az alábbi értékek *időzítés* oszlop predictively beolvasása rajta a táblázatban látható kilenc oszlopok felosztása.

|Időzítés|
|:-----|
|Hétfőtől péntekig: 7:00-kor – 18:00:00, szombat: 9:00-kor – 5:00 pm vasárnap: lezárva|
|Hétfőtől péntekig: reggel 9:00 – 18:00:00, szombat: 4:00-kor - 4:00 pm vasárnap: lezárva|
|Hétfőtől péntekig: 8:30 am - 7:00 pm szombat: 3:00-kor – 2:30 pm vasárnap: lezárva|
|Hétfőtől péntekig: 8:00-kor – 18:00:00, szombat: 2:00-kor - 3:00 pm vasárnap: lezárva|
|Hétfőtől péntekig: 4:00-kor - 7:00 pm szombat: 9:00-kor - 4:00 pm vasárnap: lezárva|
|Hétfőtől péntekig: 8:30 am - 4:30 pm szombat: 9:00-kor – 5:00 pm vasárnap: lezárva|
|Hétfőtől péntekig: 5:30 am – 18:30:00, szombat: 5:00-kor - 4:00 pm vasárnap: lezárva|
|Hétfőtől péntekig: 8:30 am - 8:30 pm szombat: 6:00-kor – 5:00 pm vasárnap: lezárva|
|Hétfőtől péntekig: 8:00-kor – 9:00 pm szombat: reggel 9:00-8:00 pm vasárnap: lezárva|
|Hétfőtől péntekig: 10:00-kor – 9:30 pm szombat: 9:30 am - 3:00 pm vasárnap: lezárva|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Hétfő|Péntek|7:00-kor|18:00:00|Szombat|9:00|5:00 pm|Vasárnap|Lezárva|
|Hétfő|Péntek|9:00|18:00:00|Szombat|4:00-kor|du. 4:00|Vasárnap|Lezárva|
|Hétfő|Péntek|8:30-kor|7:00 pm|Szombat|3:00-kor|2:30 pm|Vasárnap|Lezárva|
|Hétfő|Péntek|8:00-kor|18:00:00|Szombat|2:00-kor|du. 3:00|Vasárnap|Lezárva|
|Hétfő|Péntek|4:00-kor|7:00 pm|Szombat|9:00|du. 4:00|Vasárnap|Lezárva|
|Hétfő|Péntek|8:30-kor|4:30 pm|Szombat|9:00|5:00 pm|Vasárnap|Lezárva|
|Hétfő|Péntek|5:30-kor|18:30:00|Szombat|5:00-kor|du. 4:00|Vasárnap|Lezárva|
|Hétfő|Péntek|8:30-kor|8:30 pm|Szombat|6:00-kor|5:00 pm|Vasárnap|Lezárva|
|Hétfő|Péntek|8:00-kor|9:00 pm|Szombat|9:00|8:00 pm|Vasárnap|Lezárva|
|Hétfő|Péntek|10:00-kor|9:30 pm|Szombat|9:30-kor|du. 3:00|Vasárnap|Lezárva|

### <a name="splitting-iis-log"></a>Felosztási IIS-napló

Itt látható egy másik példa több tetszőleges elválasztó karaktert. Ebben a példában is magában foglalja a környezetfüggő elválasztó "/", amely nem kell osztani az URL-címek belül, vagy a fájl elérési utak. Ez a felosztás használatával számos végrehajtásához fárasztó *Célosztályából oszlop példa alapján* átalakítások és példák adjon az egyes mezők. A felosztott átalakítása azt is végezze el a prediktív felosztásával olyan kiadása nélkül.

|logtext|
|:-----|
|192.128.138.20--[16 Oct/2016. december 16:22:33-0200] "GET /images/picture.gif HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla vagy 4.0-s verzióját (kompatibilis; MSIE 4)""-"|
|10.128.72.213--[17 Oct/2016. december 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 "http://www.sample.com/" "Mozilla/5.0 (MSIE)" 6233 www.aol.com "-"|
|192.165.71.165--[november 12/2016 14:22:44-0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; RV:1.7.3) ""-"|
|10.166.64.165 - - [23/Nov/2016 01:52:45 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.167.1.193--[16/Jan/2017 22:34:56 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193--[28/Jan/2017 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla vagy 4.0-s verzióját (kompatibilis; MSIE 6.0; Windows NT 5.1)""-"|
|192.166.64.165 - - [23/Mar/2017 01:55:25 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|11.167.1.193--[16/6%/4!a/2017 11:34:36 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

Lekérdezi ossza fel:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|16 Oct/2016. december|16:22:33|-0200|GET|Images/Picture.gif|HTTP|1.1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4.0|kompatibilis; MSIE 4|
|10.128.72.213|17 Oct/2016. december|12:43:12|+0300|GET|news/stuff.html|HTTP|1.1|200|6233|www.aol.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|12 november/2016. december|14:22:44|-0500|GET|sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; RV:1.7.3|
|10.166.64.165|23 november/2016. december|01:52:45|-0800|GET|Style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|16/Jan/2017|22:34:56|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|28/Jan/2017|26:36:16|+0800|GET|search.php|HTTP|1.1|400|1777|www.bing.com|-|Mozilla|4.0|kompatibilis; MSIE 6.0; Windows NT 5.1|
|192.166.64.165|23/gyel/2017|01:55:25|-0800|GET|Style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|16/6%/4!a/2017|11:34:36|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>A felosztás nélkül határolójelek példák
Bizonyos esetekben nincsenek nem tényleges elválasztó karaktert, és adatmezők fordulhat elő, folyamatosan egymás mellé. Ebben az esetben a felosztás átalakítása automatikusan észleli minták célszámítógéppel valószínűleg szétválasztási pontokat az adatokat. Például abban az esetben az összeg külön a Pénznem típusból szeretnénk, és a felosztott automatikusan kikövetkezteti a numerikus és a nem numerikus adatokat a szétválási pont közötti határokat.

### <a name="splitting-amount-with-currency-symbol"></a>A felosztás pénznemszimbólum mennyiségének

|Mennyiség|Amount_1|Amount_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

A következő példában szeretnénk mértékegységének a súlyozási értékeket elkülönítéséhez. Újra a felosztás megállapítás a jelentéssel bíró határ automatikusan észleli és például inkább keresztül más lehetséges elválasztó karaktert a "." karaktert. 

### <a name="splitting-weights-with-units"></a>A felosztás súlyok egységek

|Súlyozás|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2.27KG|2.27|KG|
|1L|1|L|
|2.5 KG|2.5|KG|
|2KG|2|KG|
|1.7KGA|1.7|KGA|
|3KG|3|KG|
|2KG|2|KG|
|125G|125|G|
|500G|500|G|
|1.5KGA|1.5|KGA|

## <a name="technical-notes"></a>Technikai megjegyzések

A felosztott átalakító szolgáltatás alapul az **prediktív Program összefoglaló** módszer. Ezzel a módszerrel a data transformation programok megjegyzett a bemeneti adatok alapján automatikusan. A programok vannak synthesized tartományspecifikus nyelven. A DSL határolójel és előforduló különösen reguláris kifejezés környezetek mezők alapul. A technológiával kapcsolatos további információk találhatók a [legutóbbi közzétételét ebben a témakörben a](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
