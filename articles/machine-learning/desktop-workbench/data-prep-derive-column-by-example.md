---
title: Oszlopok származtatása példa átalakítása az Azure Machine Learning Workbench használatával
description: A referenciadokumentum a "Származtatása példa" transzformáció
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
ms.openlocfilehash: 311fd39792274ef01b1b03fdf8252eb7ac93c922
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978859"
---
# <a name="derive-column-by-example-transformation"></a>Oszlopok származtatása példa átalakítása

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



A **származtatása példa** átalakítás lehetővé teszi a felhasználók leszármazottja, származtatott eredménye a felhasználó által megadott példák segítségével egy vagy több meglévő oszlop létrehozása. A származtatott a támogatott karakterlánc, dátum és számátalakítások tetszőleges kombinációját is lehet. 

Az alábbi karakterláncot, a dátum és a szám átalakítások támogatottak:

**Karakterlánc-átalakítások:** 

Például intelligens kivonása száma és a dátumok, állandó értékek összefűzésével, eset módjára, leképezés karakterláncrészletet.

**Dátumátalakítások:** 

Dátumformátum változik, kibontása dátum részei, idő Bins leképezési időt.

A dátum átalakításokra viszonylag általános néhány jelentős korlátozásokkal:
* Timezones nem támogatottak.
* Néhány gyakori formátum nem támogatott:
    * ISO 8601 – hét év formátumban (például "2009-W53-7") 
    * UNIX alapidőpont szerint.
* Az összes formátumok a következők kis-és nagybetűket ("4 am" nevezetesen nem ismerhető fel egy alkalommal bár "4 AM").

**Számátalakítások:** 

Kerekítés, emelet, felső határa, Dobozolás, nulla vagy a hely, részleg vagy egy 1000 hatékonyságát szorzás Padding.

**Összetett átalakítások:** 

Karakterlánc, szám vagy dátum átalakítások tetszőleges kombinációját.

## <a name="how-to-use-this-transformation"></a>Az átalakítás használata

Az átalakítás végrehajtásához kövesse az alábbi lépéseket:
1. Válassza ki az értékét célosztályából kívánt egy vagy több oszlop. 
2. Válassza ki **származtatása példa** származó a **alakítja át az** menü. Vagy a jobb gombbal bármelyik a kijelölt oszlopokat, és válassza a fejléc a **származtatása példa** a helyi menüből. Megnyílik a átalakítása szerkesztő, és a egy olyan új oszlop mellett a jobb oldali legtöbb kijelölt oszlop hozzá. A kijelölt oszlopok segítségével azonosítható a jelölőnégyzetek az oszlopfejlécre. Hozzáadása és eltávolítása a kijelölt oszlopokat az oszlopfejlécre a jelölőnégyzetek használatával elvégezhető.
3. Írja be például a *kimeneti* elleni egy sort, és nyomja le adja meg. Ezen a ponton a Workbench elemzi a bemeneti oszlop, valamint a megadott kimenetet egy programot, amely képes alakítsa át a megadott bemeneti adatok kimeneti szintetizálásához. Az adatrácsban összes sorhoz végrehajtása találja a szintetizált programot. Nem egyértelmű és bonyolult esetek több példát is szükséges. Attól függően, hogy biztosan megfeleljen az egyszerű vagy speciális módban különböző módon adható meg több példát.
4. Tekintse át a kimenetet, majd kattintson **OK** gombra az átalakítás elfogadásához.

### <a name="transform-editor-basic-mode"></a>Editor átalakítása: alapszintű mód

Egyszerű módban egy beágyazott szerkesztési az adatrácsban lehetőséget biztosít. Példa a kimenetre megadhatja a lényeges a cellára, és írja be az értéket. 

A workbench elemzi az adatokat, és próbálja meg, hogy a felhasználó át kell tekinteni a szélsőséges esetek azonosítását. Az adatok elemzése folyamatban van, amíg **adatok elemzése** fejlécére, az átalakítás szerkesztővel jelenik meg. Egy az elemzés befejeződik, vagy **nem javaslatok** , vagy **következő javasolt sor felülvizsgálata** jelenik meg a fejléc. Kattintson a navigációs sávja a szélsőséges eseteket **következő javasolt sor felülvizsgálata**. Abban az esetben, ha az érték helytelen sorhoz, meg kell kulcsot a megfelelő értéket a további példát. 

### <a name="transform-editor-advanced-mode"></a>Editor átalakítása: speciális módban

Speciális mód az oszlopok származtatás példa gazdagabb élményt nyújt. Az összes a példák egy helyen jelennek meg. Emellett áttekintheti az összes a szélsőséges eseteket, egy helyen kattintva **javasolt példák megjelenítése**. 

A speciális módban is hozzáadhat minden sor egy példa sorként duplán kattint a sort a táblázatban. Egy sor egy példa sorként másolódik, szerkesztheti az adatokat a forrás-oszlopok, hogy egy szintetikus példa is. Ezzel a módszerrel adhat hozzá, amelyek nem szerepelnek jelenleg a mintaadatok esetekben.

Felhasználói között válthat a **egyszerű módban** és a **speciális mód** átalakítása a szerkesztőben a hivatkozásokra kattintva.

### <a name="transform-editor-send-feedback"></a>Editor átalakítása: visszajelzés küldése

Kattintson a a **visszajelzés küldése** hivatkozás megnyílik az **visszajelzés** párbeszédpanelen hagyja a megjegyzéseket, előre feltöltve a példák felhasználóval van megadva. Felhasználó kell a Megjegyzések mező tartalmát, és segít nekünk a probléma, további részletekkel szolgálnak. Ha a felhasználó nem szeretné az adatok megosztása a Microsofttal, felhasználói gombra kattintás előtt törölje a előfeltöltött példaadatokat a **visszajelzés küldése** gombra. 

### <a name="editing-existing-transformation"></a>Meglévő átalakítás szerkesztése

A felhasználó szerkesztheti a meglévő **származtatott oszlop szerint példa** kiválasztásával átalakítása **szerkesztése** átalakítási lépés lehetőséget. Kattintson a **szerkesztése** a átalakítása megnyitása a **speciális mód**, és a példák az átalakítás létrehozása során egy megadott látható.

## <a name="examples-of-string-transformations-by-example"></a>Példák a karakterlánc-átalakítások példa alapján


>[!NOTE] 
>Az értékek **félkövér** megjelenített adatkészlet az átalakítás végrehajtásához egy megadott példák jelölik.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Fájlelérési utakat fájlnevek kibontása

Példa, amelyek ebben az esetben szükség volt száma: 2

|Input (Bemenet)|Kimenet|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.py|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|rgb.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.py|2to3.PY|
|C:\Python35\Tools\Scripts\analyze_dxp.py|**analyze_dxp.py**|
|C:\Python35\Tools\Scripts\byext.py|byext.PY|
|C:\Python35\Tools\Scripts\byteyears.py|byteyears.PY|
|C:\Python35\Tools\Scripts\checkappend.py|checkappend.PY|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Kis adatkezelési során karakterlánc kinyerése

Példa, amelyek ebben az esetben szükség volt száma: 3

|Input (Bemenet)|Kimenet|
|:-----|:-----|
|Z & KÉZBESÍTETLEN végső; GYERTYA  ÚJ HANNOVER; Állomás 332; 10/2015-12-es \@ 17:10:52;|**Új Hannover**|
|BRIAR elérési út & WHITEMARSH LN;  HATFIELD TOWNSHIP; Állomás 345; 10/2015-12-es \@ 17:29:21.|Hatfield Township|
|HAWS AVE; NORRISTOWN; 10/2015-12-es \@ 14:39:21-állomás: STA27;|**Norristown**|
|AIRY ST & ST káposztarepce;  NORRISTOWN; Állomás 308A; 10/2015-12-es \@ 16:47:36;|**Norristown**|
|CHERRYWOOD z & KÉZBESÍTETLEN végső;  ALACSONYABB POTTSGROVE; Állomás 329; 10/2015-12-es \@ 16:56:52;|Alacsonyabb Pottsgrove|
|FOREST AVE & W-9 ST;  LANSDALE; Állomás 345; 10/2015-12-es \@ 15:39:04;|Lansdale|
|LAUREL AVE & OAKDALE AVE;  HORSHAM; Állomás 352; 10/2015-12-es \@ 16:46:48.|Horsham|
|A távoli asztali COLLEGEVILLE & LYWISKI távoli asztali;  SKIPPACK; Állomás 336; 10/2015-12-es \@ 16:17:05;|Skippack|
|Fő út & régi SUMNEYTOWN CSUKÁT;  ALACSONYABB SALFORD; Állomás 344; 10/2015-12-es \@ 16:51:42;|Alacsonyabb Salford|
|BLUEROUTE &AMP; RAMP I476 NB, A TÁVOLI ASZTALI KÉMIAI; PLYMOUTH; 10/2015-12-ES \@ 17:35:41;|Plymouth|
|RT202 PKWY &AMP; KNAPP TÁVOLI ASZTALI; MONTGOMERY; 10/2015-12-ES \@ 17:33:50.|Montgomery|
|A TÁVOLI ASZTALI FOLYAMI &AMP; COLWELL LN; PLYMOUTH; 10/2015-12-ES \@ 16:32:10.|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Dátumformátum manipuláció során karakterlánc kinyerése

Példa, amelyek ebben az esetben szükség volt száma: 1

|Input (Bemenet)|Kimenet|
|:-----|:-----|
|MONTGOMERY AVE & WOODSIDE távoli asztali;  ALACSONYABB MERION; Állomás 313; 2015-12-11 \@ 04:11:35;|**12 november 2015 4 AM**|
|DREYCOTT LN & W LANCASTER AVE;  ALACSONYABB MERION; Állomás 313; 2015-12-11 \@ 01:29:52;|12 november 2015 1 AM|
|E LEVERING MILL TÁVOLI ASZTALI &AMP; CONSHOHOCKEN ÁLLAPOTA TÁVOLI ASZTALI; ALACSONYABB MERION; 2015-12-11 \@ 07:29:58;|12 november 2015 de.|
|A távoli asztali PENN VALLEY & MANOR távoli asztali;  ALACSONYABB MERION; Állomás 313; 10/2015-12-es \@ 20:53:30.|12 2015 október 8 -kor|
|GROVE AVE &AMP; OVERHILL TÁVOLI ASZTALI; ALACSONYABB MERION; 10/2015-12-ES \@ 23:02:27;|12 2015 október 23 óra|
|W MONTGOMERY AVE &AMP; PENNSWOOD TÁVOLI ASZTALI; ALACSONYABB MERION; 10/2015-12-ES \@ 19:25-ÖS: 22.|12 2015 október 7 PM|
|ROSEMONT AVE & KÉZBESÍTETLEN végső;  ALACSONYABB MERION; Állomás 313; 10/2015-12-es \@ 18:43:07;|12 2015 október 18: 00|
|AVIGNON DR & KÉZBESÍTETLEN végső; ALACSONYABB MERION; 10/2015-12-es \@ 20:01:29-állomás: STA24;|12 2015 október 8 -kor|

### <a name="s4-concatenating-strings"></a>S4. Karakterláncok kiszámításával

Példa, amelyek ebben az esetben szükség volt száma: 1

>[!NOTE] 
>Ebben a példában különleges karakter. a kimeneti oszlop tárolóhelyek jelöli.

|Utónév|Középső kezdeti|Vezetéknév|Kimenet|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda··Lohmann|
|Claudio|A|Csillapításához|**Claudio· A· Csillapításához**|
|Sarah-Jane|S|Smith|Sarah-Jane·S·Smith|
|Brandi||Blumenthal|Brandi·· Blumenthal|
|Jesusita|R|Utazás|Jesusita· R· Utazás|
|Hermina||Hults|Hermina··Hults|
|Anne-Marie|W|János|Anna-Marie· W· János|
|Rico||Ropp|Rico·· Ropp|
|Lauren – május||Fullmer|Lauren-May·· Fullmer|
|Marc|T|Maine|Marc· T· Maine|
|Angie||Adelman|Angie··Adelman|
|John-Paul||Smith|John-Paul·· János|
|Szám|W|Staller|Song· W· Staller|
|Jill||Jefferies|Jill·· Jefferies|
|Ruby-Grace|M|Simmons|Ruby-Grace·M·Simmons|

### <a name="s5-generating-initials"></a>S5. Generálása monogramja

Példa, amelyek ebben az esetben szükség volt száma: 2

|Teljes név|Kimenet|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio csillapításához|C.C.|
|Sarah-Jane Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita utazás|J.J.|
|Hermina Hults|H.H.|
|Anna-Marie Jones|A.J.|
|Rico Ropp|R.R.|
|Lauren – május Fullmer|L.F.|
|Marc Maine|M.M.|
|Angie Adelman|A.A.|
|John-Paul Smith|**J.S.**|
|Dal Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby-türelmi Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Állandó értékek leképezése

Példa, amelyek ebben az esetben szükség volt száma: 3

|Felügyeleti nemek|Kimenet|
|:-----|:-----:|
|Férfi|**0**|
|Nő|**1**|
|Ismeretlen|**2**|
|Nő|1|
|Nő|1|
|Férfi|0|
|Ismeretlen|2|
|Férfi|0|
|Nő|1|

## <a name="examples-of-number-transformations-by-example"></a>Példák a számátalakítások példa alapján

>[!NOTE] 
>Az értékek **félkövér** megjelenített adatkészlet az átalakítás végrehajtásához egy megadott példák jelölik.


### <a name="n1-rounding-to-nearest-10"></a>N1. Kerekítése a legközelebbi 10-re

Példa, amelyek ebben az esetben szükség volt száma: 1

|Input (Bemenet)|Kimenet|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Kerekítés és a legközelebbi 10

Példa, amelyek ebben az esetben szükség volt száma: 2

|Input (Bemenet)|Kimenet|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Kerekítése a legközelebbi 0,05

Példa, amelyek ebben az esetben szükség volt száma: 2

|Input (Bemenet)|Kimenet|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. A dobozolás

Példa, amelyek ebben az esetben szükség volt száma: 1

|Input (Bemenet)|Kimenet|
|-----:|:-----:|
|20.16|**20-25**|
|14.32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. 1000 skálázása

Példa, amelyek ebben az esetben szükség volt száma: 1

|Input (Bemenet)|Kimenet|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. Kitöltés

Példa, amelyek ebben az esetben szükség volt száma: 1

|Kód|Kimenet|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>Példák a Dátumátalakítások példa alapján

>[!NOTE] 
>Az értékek **félkövér** megjelenített adatkészlet az átalakítás végrehajtásához egy megadott példák jelölik.


### <a name="d1-extracting-date-parts"></a>D1. Dátum részei kibontása

Ezek a részek különböző szerint – példa átalakítások használata az adatok ugyanezek könyvtárban találhatók. Félkövér karakterláncok, amelyek a megfelelő átalakításában szerepel a kapott példák képviseli.

|DateTime|Hét napja|Dátum|Hónap|Év|Óra|Perc|Másodperc|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31 – Jan-2031 05:54:18|**Fri**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|13:32:01 17-Jan-1990|Em engedélyezett|17|Jan|1990|13|32|01|
|05:36:07 14-február-2034|Kedd|14|Feb|2034|5|36|07|
|Március 14-2002 13:16:16|Thu|14|Március|2002|13|16|16|
|21-Jan-1985 05:44:43|Hétfő|21|Jan|1985|5|44|**43**|
|01:11:56 16-Aug-1985.|Fri|16|Aug|1985|1|11|56|
|20-Dec-2033 18:36:29|Kedd|20|Dec|2033|18|36|29|
|10:21:59 16-júliusban-1984|Hétfő|16|Július|1984|10|21|59|
|10:59:36 a 2038 Jan 13|Em engedélyezett|13|Jan|2038|10|59|36|
|15:13:54 14-Aug-1982|Rövidítéssel|14|Aug|1982|15|13|54|
|22-Nov-2030 08:18:08|Fri|22|Nov|2030|8|18|08|
|21-Oct-1997 08:42:58|Kedd|21|Okt|1997|8|42|58|
|28-Nov-2006 14:19:15|Kedd|28|Nov|2006|14|19|15|
|29-Apr-2031 04:59:45|Kedd|29|Képest|2031|4|59|45|
|02:38:36 29-Jan-2032|Thu|29|Jan|2032|2|38|36|
|11 – május-2028 15:31:52|Thu|11|Május|2028|15|31|52|
|12:45:39 15-júliusban-1977|Fri|15|Július|1977|12|45|39|
|27-Jan-2029 05:55:41|Rövidítéssel|27|Jan|2029|5|55|41|
|03-Mar-2024 10:17:49|Sun|3|Március|2024|10|17|49|
|00:23:13 14-Diagramhalmazban – 2010|Em engedélyezett|14|Képest|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. A dátumok formázásához

A dátum formattings elkészült különböző szerint – példa átalakítások használata az adatok ugyanahhoz az adatkészlethez. Félkövér karakterláncok, amelyek a megfelelő átalakításában szerepel a kapott példák képviseli.

|DateTime|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31 – Jan-2031 05:54:18|**1/31/2031**|**2031. január 31., péntek**|**01312031 5:54**|**31/1/2031 5:54 KOR**|**Q1 2031**|
|13:32:01 17-Jan-1990|1/17/1990|1990. január 17., szerda|01171990 13:32|17/1/1990 1:32 PM|Q1 1990|
|05:36:07 14-február-2034|2/14/2034|2034. február 14., "frissítő kedd"|02142034 5:36|14/2/2034 5:36 KOR|Q1 2034
|Március 14-2002 13:16:16|3/14/2002|Csütörtöki napot, március 14, 2002-es|03142002 13:16|14/3/2002 13:16-KOR|Q1 2002
|21-Jan-1985 05:44:43|1/21/1985|Hétfő, 21, 1985. január|01211985 5:44|21/1/1985 5:44 KOR|Q1 1985
|01:11:56 16-Aug-1985.|8/16/1985|1985. augusztus 16., péntek|08161985 1:11|16/8/1985 1:11:00|Q3 1985
|20-Dec-2033 18:36:29|12/20/2033|2033. December 20., "frissítő kedd"|12202033 18:36|20/12/2033 18:36:00|Q4 2033
|10:21:59 16-júliusban-1984|7/16/1984|1984. július 16., hétfő|07161984 10:21|16/7/1984 10:21-KOR|Q3 1984
|10:59:36 a 2038 Jan 13|1/13/2038|2038. január 13., szerda|01132038 10:59|13/1/2038 10:59-KOR|Q1 2038
|15:13:54 14-Aug-1982|8/14/1982|1982. augusztus 14., szombat|08141982 15:13|14/8/1982 DU. 3:13|Q3 1982
|22-Nov-2030 08:18:08|11/22/2030|2030. November 22., péntek|11222030 8:18|22-ES/11/2030 8:18 ÓRAKOR|Q4 2030
|21-Oct-1997 08:42:58|10/21/1997|1997. október 21., "frissítő kedd"|10211997 8:42|21/10/1997 8:42-KOR|Q4 1997
|28-Nov-2006 14:19:15|11/28/2006|2006. November 28., "frissítő kedd"|11282006 14:19|28/11/2006 2:19 PM|Q4 2006
|29-Apr-2031 04:59:45|4/29/2031|2031. április 29., "frissítő kedd"|04292031 4:59|29/4/2031 4:59-KOR|2. NEGYEDÉVI 2031
|02:38:36 29-Jan-2032|1/29/2032|2032. január 29., csütörtök|01292032 2:38|29/1/2032 2:38 AM|Q1 2032
|11 – május-2028 15:31:52|5/11/2028|2028. május 11., csütörtök|05112028 15:31|11/5/2028 DU. 3:31|Q2 2028
|12:45:39 15-júliusban-1977|7/15/1977|1977. július 15., péntek|07151977 12:45|15/7/1977. 12:45-KOR|Q3 1977
|27-Jan-2029 05:55:41|1/27/2029|2029. január 27., szombat|01272029 5:55|27/1/2029 05:55-KOR|Q1 2029
|03-Mar-2024 10:17:49|3/3/2024|2024. márciusi 3., vasárnap|03032024 10:17|3/3/2024 10:17 ÓRA|Q1 2024
|00:23:13 14-Diagramhalmazban – 2010|4/14/2010|2010. április 14., szerda|04142010 0:23|14/4/2010-12:23-KOR|Q2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Idő az időszakok leképezése

Ezek az időszak leképezések időpontok elkészült különböző szerint – példa átalakítások használata az adatok ugyanahhoz az adatkészlethez. Félkövér karakterláncok, amelyek a megfelelő átalakításában szerepel a kapott példák képviseli.

|DateTime|Period(Seconds)|Period(Minutes)|Időszak (két óra)|Period(30 Minutes)|
|-----:|-----:|-----:|-----:|-----:|
|31 – Jan-2031 05:54:18|**0-20**|**45-60**|**5AM-7AM**|**5:30-6:00**|
|13:32:01 17-Jan-1990|**0-20**|30-45|1PM-3PM|13:30-14:00|
|05:36:07 14-február-2034|0-20|30-45|5AM-7AM|5:30-6:00|
|Március 14-2002 13:16:16|0-20|15-30|1PM-3PM|13:00-13:30|
|21-Jan-1985 05:44:43|40-60|30-45|5AM-7AM|5:30-6:00|
|01:11:56 16-Aug-1985.|40-60|0-15|1AM-3AM|1:00-1:30|
|20-Dec-2033 18:36:29|20-40|30-45|5PM-7PM|18:30-19:00|
|10:21:59 16-júliusban-1984|40-60|15-30|9: 00 – 11: 00|10:00-10:30|
|10:59:36 a 2038 Jan 13|20-40|45-60|9: 00 – 11: 00|10:30-11:00|
|15:13:54 14-Aug-1982|40-60|0-15|3PM-5PM|15:00-15:30|
|22-Nov-2030 08:18:08|0-20|15-30|7AM-9AM|8:00-8:30|
|21-Oct-1997 08:42:58|40-60|30-45|7AM-9AM|8:30-9:00|
|28-Nov-2006 14:19:15|0-20|15-30|1PM-3PM|14:00-14:30|
|29-Apr-2031 04:59:45|40-60|45-60|3AM-5AM|4:30-5:00|
|02:38:36 29-Jan-2032|20-40|30-45|1AM-3AM|2:30-3:00|
|11 – május-2028 15:31:52|40-60|30-45|3PM-5PM|15:30-16:00|
|12:45:39 15-júliusban-1977|20-40|45-60|11: 00 – 1 ÓRA|12:30-13:00|
|27-Jan-2029 05:55:41|40-60|45-60|5AM-7AM|5:30-6:00|
|03-Mar-2024 10:17:49|40-60|15-30|9: 00 – 11: 00|10:00-10:30|
|00:23:13 14-Diagramhalmazban – 2010|0-20|15-30|23 ÓRA – 1 ÓRA|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Példák a összetett átalakítások példa alapján

|tripduration|Kezdés időpontja|Indítsa el az állomás azonosítója|Indítsa el az állomás szélesség|Indítsa el az állomás hosszúság|UserType|Oszlop|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|Előfizető|**Az előfizető kerékpárt kivételezett állomásról 107, lat/hosszú (42.363,-71.088), 2016. január 08. körülbelül 4 du. Út időtartama óta 61 perc**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|Ügyfél|Egy ügyfél állomásról 74, szél/nagy (42.373,-71.119), a 2016. január 17., körülbelül reggel 9 Órakor egy kerékpárutat jelöl ki. Út időtartama óta 61 perc|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|Előfizető|Előfizető állomásról 176, szél/nagy (42.387,-71.119), a 2016. január 25. körülbelül 8 órakor egy kerékpárutat jelöl ki. Út időtartama óta 62 perc|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|Előfizető|Az előfizető állomásról 107, szél/hosszú (42.363,-71.088), a 2016. január 08. körülbelül 10 órakor egy kerékpárt követi. Út időtartama óta 63 perc|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|Előfizető|Az előfizető kerékpárt kivételezett állomásról 68-as, lat/hosszú (42.365,-71.103), 2016. január 15. körülbelül 7 du. Út időtartama óta 64 perc|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|Előfizető|Az előfizető kerékpárt kivételezett állomásról 115, lat/hosszú (42.388,-71.119), 2016. január 22. körülbelül 6 du. Út időtartama óta 64 perc|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|Előfizető|Előfizető állomásról 178, szél/nagy (42.360,-71.101), a 2016. január 18., körülbelül reggel 9 Órakor egy kerékpárutat jelöl ki. Út időtartama óta 68 perc|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|Előfizető|Előfizető állomásról 176, szél/hosszú (42.387,-71.119), a 14, 2016. január körülbelül 8 órakor egy kerékpárutat jelöl ki. Út időtartama óta 69 perc|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|Előfizető|Az előfizető kerékpárt kivételezett állomásról 141, lat/hosszú (42.364,-71.082), 2016. január 13. körülbelül 10 du. Út időtartama óta 69 perc|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|Előfizető|Előfizető állomásról 176, szél/nagy (42.387,-71.119), a 2016. január 15., körülbelül 8 Órakor egy kerékpárutat jelöl ki. Út időtartama óta 69 perc|


## <a name="technical-notes"></a>Technikai megjegyzések

### <a name="conditional-transformations"></a>Feltételes átalakítások
Bizonyos esetekben egyetlen átalakítás nem található, amely megfelelne a megadott példák. Ezekben az esetekben származtatása példa átalakítása megkísérli a bemeneti adatok alapján néhány minta csoportot, és ismerje meg, minden csoport külön átalakításában. Erre **feltételes átalakítási**. **Feltételes átalakítási** csak egyetlen bemeneti oszlop átalakítások kísérlet történik. 

### <a name="reference"></a>Leírások
További információ a karakterlánc átalakítási példa Technology található [ennek a kiadványnak](https://www.microsoft.com/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/).
