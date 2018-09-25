---
title: Oszlop felosztása példa átalakítása az Azure Machine Learning Workbench használatával
description: A referenciadokumentum az "Oszlopok felosztása példa alapján"-átalakításhoz
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 3edf49484e5bc05a297b8d8969632fb902aa1714
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953735"
---
# <a name="split-column-by-example-transformation"></a>Oszlop felosztása példa átalakítása

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Az átalakítási predictively felhasználói beavatkozást nem igénylő oszt fel egy jelentéssel bíró határokon belül oszlop tartalmát. A felosztási algoritmus a határok az oszlop tartalmát elemzése után választja ki. Ezek a hatókörök definiálni sikerült
* Egy rögzített elválasztó
* Több, tetszőleges elválasztó környezeteket, különösen megjelenő, vagy
* Adatmintákhoz szűrősablonokat vagy bizonyos entitástípusok

Felhasználók is szabályozhatja a felosztó viselkedését, ahol a szerint adja meg a kívánt megosztással példákat, vagy adja meg az elválasztó karakterek speciális módban.

Elméletileg a felosztási művelet is elvégezhető a workbenchben sorozatát *származtatása példa* alakítja át. Azonban, ha több oszlopot, ezeket külön-külön is – például megközelítéssel származtatás nagyon időigényes lehet. Prediktív split lehetővé teszi, hogy könnyen felosztás nélkül a felhasználónak meg kellene az egyes oszlopok példákat biztosítanak. 

## <a name="how-to-perform-this-transformation"></a>Az átalakítás végrehajtása

1. Válassza ki az oszlop, amelyet meg kíván osztani. 
2. Válassza ki **oszlopok felosztása példa alapján** származó a **alakítja át az** menü. Vagy kattintson a jobb gombbal a kijelölt oszlop fejlécére, és válassza ki **oszlopok felosztása példa alapján** a helyi menüből. Megnyílik a átalakítása szerkesztő, és új oszlopokat ad hozzá a kijelölt oszlop mellett. Ezen a ponton a Workbench elemzi a bemeneti oszlop, felosztási határokon határozza meg, és szintetizál egy programot az oszlop felosztása a rácson a név jelenik meg. Az oszlop a összes sorhoz végrehajtása találja a szintetizált programot. Elválasztó karakterek, ha van ilyen, ki vannak zárva a végső eredményt.
3. Kattintson a a **speciális módban** split átalakítását szabályozásához. 
4. Tekintse át a kimenetet, majd kattintson **OK** gombra az átalakítás elfogadásához. 

Az átalakítás célja, hogy eredő oszlopok összes sorának ugyanannyi előállításához. Ha bármely sor nem lehet megosztani a meghatározott határokon belül, küld *null* alapértelmezés szerint minden oszlopban. Ez a viselkedés módosítható a **speciális mód**.

### <a name="transform-editor-advanced-mode"></a>Editor átalakítása: speciális módban
**Speciális mód** oszlopok felosztása gazdagabb élményt nyújt. 

Kiválasztásával **elválasztó oszlop megtartása** tartalmazza az elválasztó karakterek a végső eredményt. Elválasztó karakterek alapértelmezés szerint ki vannak zárva.

Adjon meg **elválasztó** felülbírálja az elválasztó automatikus lemezválasztási logika. Adhat meg több elválasztó karakterek, minden egyes sorban egy **elválasztó**. Ezek a karakterek az oszlop felosztása elválasztó karakterek használják.

Egyes esetekben felosztása egy meghatározott határok észszerűek oszlopaik száma különböző, mint a legtöbb más érték. Ezekben az esetekben **irányát adja** határozható meg a sorrendet, amelyben az oszlopok ki kell tölteni.

Kattintson a **javasolt példák megjelenítése** jeleníti meg, amelyben a felhasználói a reprezentatív sorok felosztása példa kell biztosítania. Rákattinthat a felhasználó a **mentése** javasolt sort ábrázoló példa a sor előléptetése jobbra található nyílra. 

Felhasználó **oszlop törlése** vagy **új oszlop beszúrása** fejlécében kattintson a jobb gombbal a **példák tábla**.

Felhasználói másolja és illessze be értékeket egy cella egy másikra annak érdekében, hogy felosztása példa.

Felhasználói között válthat a **egyszerű módban** és a **speciális mód** átalakítása a szerkesztőben a hivatkozásokra kattintva.

### <a name="transform-editor-send-feedback"></a>Editor átalakítása: visszajelzés küldése

Kattintson a a **visszajelzés küldése** hivatkozás megnyílik az **visszajelzés** párbeszédpanelen hagyja a megjegyzéseket, előre feltöltve az paraméter beállításokat és a példák felhasználó van megadva. Felhasználó kell a Megjegyzések mező tartalmát, és segít nekünk a probléma, további részletekkel szolgálnak. Ha a felhasználó nem szeretné az adatok megosztása a Microsofttal, felhasználói gombra kattintás előtt törölje a előfeltöltött példaadatokat a **visszajelzés küldése** gombra. 


### <a name="editing-an-existing-transformation"></a>Egy meglévő átalakítást szerkesztése

A felhasználó szerkesztheti a meglévő **minta alapján oszlop felosztása** kiválasztásával átalakítása **szerkesztése** átalakítási lépés lehetőséget. Kattintson a **szerkesztése** a átalakítása megnyitása a **speciális mód**, és a példák az átalakítás létrehozása során egy megadott látható.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Példák a rögzített, karaktert a határolójel felosztása

Szokás adatmezőkhöz, egy egyetlen rögzített elválasztó, például egy CSV formátumú vesszővel kell elválasztani. A felosztás átalakító megkísérli automatikusan kikövetkeztetni ezek elválasztó. Ha például a következő esetben azt automatikusan kikövetkezteti a "." elválasztóként.

### <a name="splitting-ip-addresses"></a>Felosztás IP-címek

Az első oszlop értékeit predictively négy oszlopra vannak osztva.

|IP|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Példák a felosztás az adott környezetben található több elválasztó

A felhasználói adatok szétválasztása eltérő mezők számos különböző elválasztó karaktereket tartalmazhat. Ezen felül tagolhatja karakterlánc csak néhány előfordulását lehet egy elválasztó karakter, de nem minden. Például a következő esetben a olyan szükséges elválasztó, "-",","és":" a kívánt kimeneti előállításához. Azonban nem minden előfordulását a ":" kell lennie egy pont, mivel nem szeretnénk felosztani az idő, de csak egy oszlop tárolja. A felosztás átalakító kikövetkezteti belül történik a kivonni kívánt minden lehetséges előfordulásának helyett a bemeneti adatokat a környezetek elválasztó. Az átalakítás egyben általános adattípusok, például a dátumok és időpontok figyelembe.   

### <a name="splitting-store-opening-timings"></a>Tároló megnyitása időzítésüket felosztása

A következő értékeit *időzítésüket* oszlop első predictively felosztása kilenc oszlopok a tábla alatt látható.

|Időzítés|
|:-----|
|Hétfőtől péntekig: 7:00-kor – 18:00:00, szombat: 9:00-kor – 17:00-kor, vasárnap: lezárva|
|Hétfőtől péntekig: 9:00-kor – 18:00:00, szombat: 4:00 am - 4:00 pm, vasárnap: lezárva|
|Hétfőtől péntekig: 8:30-kor - 7:00 órakor, szombat: 3:00 am - 2:30 = 1997031213, vasárnap: lezárva|
|Hétfőtől péntekig: 8:00-kor – 18:00:00, szombat: 2:00-kor – 3:00 pm, vasárnap: lezárva|
|Hétfőtől péntekig: 4:00-kor - 7:00 órakor, szombat: 9:00 am - 4:00 pm, vasárnap: lezárva|
|Hétfőtől péntekig: 8:30 am - 4:30 = 1997031213, szombat: 9:00-kor – 17:00-kor, vasárnap: lezárva|
|Hétfőtől péntekig: 05:30-kor – 18:30:00, szombat: 05:00-kor - du. 4:00, vasárnap: lezárva|
|Hétfőtől péntekig: 8:30-kor – 8:30 = 1997031213, szombat: 6:00-kor – 17:00-kor, vasárnap: lezárva|
|Hétfőtől péntekig: 8:00-kor – 9:00 órakor, szombat: 9:00-kor – 8:00 pm, vasárnap: lezárva|
|Hétfőtől péntekig: 10:00-kor – 9:30-kor, szombat: 9:30-kor – 3:00 pm, vasárnap: lezárva|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Hétfő|Péntek|7:00-kor|du. 6:00|Szombat|9:00|17:00-kor|Vasárnap|Lezárva|
|Hétfő|Péntek|9:00|du. 6:00|Szombat|4:00-kor|du. 4:00|Vasárnap|Lezárva|
|Hétfő|Péntek|8:30-kor|7:00 órakor|Szombat|3:00-kor|2:30-kor|Vasárnap|Lezárva|
|Hétfő|Péntek|8:00-kor|du. 6:00|Szombat|2:00-kor|du. 3:00|Vasárnap|Lezárva|
|Hétfő|Péntek|4:00-kor|7:00 órakor|Szombat|9:00|du. 4:00|Vasárnap|Lezárva|
|Hétfő|Péntek|8:30-kor|du. 4:30|Szombat|9:00|17:00-kor|Vasárnap|Lezárva|
|Hétfő|Péntek|05:30-kor|18:30:00|Szombat|5:00-kor|du. 4:00|Vasárnap|Lezárva|
|Hétfő|Péntek|8:30-kor|8:30 = 1997031213|Szombat|6:00-kor|17:00-kor|Vasárnap|Lezárva|
|Hétfő|Péntek|8:00-kor|9:00 órakor|Szombat|9:00|du. 8:00|Vasárnap|Lezárva|
|Hétfő|Péntek|10:00-kor|9:30-kor|Szombat|9:30-kor|du. 3:00|Vasárnap|Lezárva|

### <a name="splitting-iis-log"></a>Felosztási IIS-napló

Íme egy másik példa több tetszőleges elválasztó. Ebben a példában is tartalmaz egy környezetfüggő elválasztó "/", amely nem kell osztani az URL-címeket belül vagy a fájlok elérési útjainak. Célszerű a fárasztó feladat végrehajtására, ez a felosztás használatával számos *származtatása példa* átalakítások, és így a példák az egyes mezőkhöz. A felosztás átalakító használatával is végrehajthatjuk anélkül tudnák példákkal szemléltetni prediktív osztásával.

|logtext|
|:-----|
|192.128.138.20 – [16/Oct/2016 16:22:33-0200] "GET /images/picture.gif HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0-s verzióját (kompatibilis. MSIE 4)"" – "|
|10.128.72.213 – [október 17/2016 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 6233 www.aol.com "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "–"|
|192.165.71.165 – [2016/12/november 14:22:44-0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U. Windows NT 5.1; RV:1.7.3) "" – "|
|10.166.64.165 – [november 23/2016 01:52:45-0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "–"|
|192.167.1.193 – [2017/16/január 22-es: 34:56 + 0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "–"|
|192.147.76.193 – [28/Jan/2017 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0-s verzióját (kompatibilis. MSIE 6.0; Windows NT 5.1-es)"" – "|
|192.166.64.165 – [március 23/2017-01:55:25-kor-0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "–"|
|11.167.1.193 – [16/Diagramhalmazban/2017-11:34:36 + 0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

Lekérdezi felosztva:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|Október 16/2016|16:22:33|-0200|GET|Images/Picture.gif|HTTP|1.1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4.0|kompatibilis. MSIE 4|
|10.128.72.213|Október 17/2016|12:43:12|+0300|GET|news/stuff.html|HTTP|1.1|200|6233|www.aol.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|November 12/2016|14:22:44|-0500|GET|sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U. Windows NT 5.1; RV:1.7.3|
|10.166.64.165|November 23/2016|01:52:45|-0800|GET|Style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|16/Jan/2017|22:34:56|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|28/Jan/2017|26:36:16|+0800|GET|Search.php|HTTP|1.1|400|1777|www.bing.com|-|Mozilla|4.0|kompatibilis. MSIE 6.0; Windows NT 5.1|
|192.166.64.165|Március 23/2017|01:55:25|-0800|GET|Style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|2017/16/képest|11:34:36|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Példák a felosztás nélkül elválasztó karaktereket
Bizonyos esetekben vannak nem tényleges elválasztó karakterek, és datová Pole ábrázolására fordulhat egymás mellé. Ebben az esetben a felosztás átalakítás automatikusan észleli minták célszámítógéppel valószínűleg szétválasztási pontokat az adatok. Például a következő esetben az összeg elkülönítése a pénznem típusa szeretnénk, és a felosztott automatikusan kikövetkezteti a határt, a pont a numerikus és a nem numerikus adatok között.

### <a name="splitting-amount-with-currency-symbol"></a>Felosztás a pénznem szimbólumaként összeg

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

A következő példában a súlyértékeket elkülönítése a mértékegységek szeretnénk. Újra a felosztás következtetésekhez automatikusan észleli a jelentéssel bíró határ és részesíti előnyben, keresztül más lehetséges elválasztó karakterek például a "." karaktert. 

### <a name="splitting-weights-with-units"></a>Felosztás súlyok egységgel

|Tömeg|Weight_1|Weight_2|
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

A felosztás átalakítási szolgáltatás alapul az **prediktív Program összefoglaló** technika. Ezzel a technikával az átalakítási programokat a bemeneti adatok alapján automatikusan küszöbértéket. A programok vannak synthesized tartomány-specifikus nyelven. A DSL elválasztó karakterek és a mezőket, amelyek különösen reguláris kifejezés környezetek alapul. Ez a technológia további információ található a [Ez a témakör legutóbbi közzétételét](https://www.microsoft.com/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
