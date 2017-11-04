---
title: "Azure Machine Learning-munkaterület használatával például átalakítással oszlop származtatni"
description: "A \"Célosztályából oszlop példa alapján\" transzformáció a referenciadokumentum"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: a02f5e827345a1d28f01d691e1b6fbccfc03ae8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="derive-column-by-example-transformation"></a>Példa átalakítással oszlop származtatni

A **Célosztályából oszlop példa alapján** átalakítás lehetővé teszi, hogy a felhasználók leszármazottja, felhasználó által megadott példák a származtatott eredményének segítségével egy vagy több meglévő oszlopok létrehozásához. A származtatott a támogatott String, a dátum és a szám átalakítások tetszőleges kombinációja lehet. 

Karakterlánc, a dátum és a szám következő átalakítások támogatottak:

**Karakterlánc átalakítások:** 

A substring számát és dátumát, összefűzése eset adatkezelési leképezési állandó értékek intelligens kivonása beleértve.

**Dátum átalakítások:** 

Dátumformátum módosításához kibontása dátum részeit, az idő Bins leképezési idő.

A dátum átalakítások viszonylag általánosak néhány fontos korlátozásokkal:
* Timezones nem támogatottak.
* Néhány gyakori formátum nem támogatott:
    * Hete ISO 8601 – év formátumban (például "2009-W53-7") 
    * UNIX epoch idő.
* Minden formátumok a következők kis-és nagybetűket (nevezetesen "hajnali 4 órakor" értéke nem értelmezhető egyszerre bár "hajnali 4 Órakor").

**Átalakítások száma:** 

Ciklikus, emelet, felső határa, Dobozolás, nullát vagy a hely, részleg vagy 1000 hatványa szorzás kitöltési.

**Összetett átalakítások:** 

Karakterlánc, szám vagy dátum átalakítások kombinációja.

## <a name="how-to-use-this-transformation"></a>Ez a transzformáció használata

A transzformáció végrehajtásához kövesse az alábbi lépéseket:
1. Válasszon legalább egy oszlopot fel az értéket. 
2. Válassza ki **Célosztályából oszlop példa alapján** a a **átalakítja** menü. Vagy kattintson jobb gombbal a kijelölt oszlopok, és válassza ki a fejlécében a **Célosztályából oszlop példa alapján** a helyi menüből. Megnyílik a átalakítási szerkesztő, és egy olyan új oszlop mellett a jobb oldali a kijelölt oszlop ad hozzá. A kijelölt oszlopok is azonosítható, ha az oszlop fejlécében jelölőnégyzet. Hozzáadása és eltávolítása a kijelölt oszlopokat az oszlopfejlécre a jelölőnégyzetek használatával elvégezhető.
3. Írja be például a *kimeneti* elleni egy sort, és nyomja le az adja meg. Ezen a ponton a munkaterületet üzemeltető elemzi a bemeneti oszlopban, valamint a megadott kimenet olyan program, amely képes a megadott bemeneti adatok átalakítása kimeneti szintetizálásához. A szintetizált program végrehajtása az adatrácson minden sora ellen. Nem egyértelmű és bonyolult esetekben több példa lehet szükség. Attól függően, hogy biztosan alapszintű vagy speciális üzemmódban több példa is különböző módon megadni.
4. Tekintse át a kimenetet, és kattintson **OK** az átalakítás fogadásához.

### <a name="transform-editor-basic-mode"></a>Átalakítás szerkesztő: alapvető mód

Egyszerű módot nyújt egy soron belüli szerkesztési funkciót a program az adatrács. Megadhatja a kimeneti példát navigáljon a cella iránt, és írja be az értéket. 

A munkaterület elemzi az adatokat, és próbálja meg kell vizsgálni a felhasználó biztonsági esetek azonosításához. Amíg az adatok elemezni, **adatok elemzése** átalakítási szerkesztő fejléc látható. Egy az elemzés befejeződik, vagy **nem javaslatok** , vagy **felülvizsgálati következő javasolt sor** a fejléc megjelenik. A peremhálózati esetekben között kattintva válthat **felülvizsgálati következő javasolt sor**. Abban az esetben, ha az érték nem megfelelő, egy olyan sor, akkor kell beírni a megfelelő értéket további példaként. 

### <a name="transform-editor-advanced-mode"></a>Átalakítás szerkesztő: speciális módban

Speciális módban gazdagabb élményt nyújt a oszlopok származtatás példa. A példa egy adott helyen jelennek meg. Emellett áttekintheti az egy adott helyen peremhálózati esetekben kattintva **javasolt példák megjelenítése**. 

A speciális módot adhat hozzá minden sor egy példa sorként ehhez kattintson duplán a rács soron. Egy sor másolja a program egy példa sort, módosíthatja az adatokat egy szintetikus például a forrás az oszlopokat is. Ezzel a módszerrel esetek, amelyek nem jelenleg megtalálható a mintaadatok is hozzáadhat.

Felhasználó válthat a **alapvető mód** és a **Speciális üzemmód** átalakítási-szerkesztőben hivatkozásokra kattintva.

### <a name="editing-existing-transformation"></a>Meglévő átalakítása szerkesztése

A felhasználó már meglévő **Célosztályából oszlop szerint példa** kiválasztásával átalakítási **szerkesztése** átalakítása lépés lehetőséget. Kattintson a **szerkesztése** a átalakítási megnyitása a **Speciális üzemmód**, és minden a példa, amelyeket az átalakítás létrehozása során.

## <a name="examples-of-string-transformations-by-example"></a>Példa alapján karakterlánc átalakítások példák


>[!NOTE] 
>Az értékek **félkövér** határoz meg a példák, amelyeket az átalakítás látható adatkészlet befejezéséhez.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Fájl nevének beolvasása a fájlok elérési útja

Példák, amelyek lemezre van szükség ebben az esetben száma: 2. régiója

|Input (Bemenet)|Kimenet|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.PY|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|RGB.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.PY|2to3.PY|
|C:\Python35\Tools\Scripts\analyze_dxp.PY|**analyze_dxp.PY**|
|C:\Python35\Tools\Scripts\byext.PY|byext.PY|
|C:\Python35\Tools\Scripts\byteyears.PY|byteyears.PY|
|C:\Python35\Tools\Scripts\checkappend.PY|checkappend.PY|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Nagybetűk adatkezelési karakterlánc kibontási során

Példák, amelyek lemezre van szükség ebben az esetben száma: 3

|Input (Bemenet)|Kimenet|
|:-----|:-----|
|KI & KÉZBESÍTETLEN END; GYERTYA  ÚJ HANNOVER; Állomás 332; 2015-12-10 @ 17:10:52;|**Új Hannover**|
|BRIAR elérési út & WHITEMARSH LN;  HATFIELD TOWNSHIP; Állomás 345; 2015-12-10 @ 17:29:21.|Hatfield Township|
|HAWS AVE; NORRISTOWN; @ 14:39:21 2015-12-10-állomás: STA27;|**Norristown**|
|AIRY ST & ST káposztarepce;  NORRISTOWN; Állomás 308A; 2015 12-10-16:47:36; @|**Norristown**|
|CHERRYWOOD ki & KÉZBESÍTETLEN END;  ALACSONYABB POTTSGROVE; Állomás 329; 2015 12-10-16:56:52; @|Alacsonyabb Pottsgrove|
|FOREST AVE & W 9 ST;  LANSDALE; Állomás 345; 2015 12-10-15:39:04; @|Lansdale|
|BABÉRT AVE & OAKDALE AVE;  HORSHAM; Állomás 352; 2015 12-10-16:46:48; @|Horsham|
|A távoli asztali COLLEGEVILLE & LYWISKI távoli asztali;  SKIPPACK; Állomás 336; 2015 12-10-16:17:05; @|Skippack|
|FŐ & régi SUMNEYTOWN CSUKA;  ALACSONYABB SALFORD; Állomás 344; 2015 12-10-16:51:42; @|Alacsonyabb Salford|
|BLUEROUTE & ELSAJÁTÍTJÁK I476 NB VALÓ KÉMIAI TÁVOLI ASZTALI; PLYMOUTH; 2015-12-10 @ 17:35:41;|Plymouth|
|RT202 PKWY & KNAPP TÁVOLI ASZTALI; MONTGOMERY; 2015-12-10 @ 17:33:50.|Montgomery|
|A TÁVOLI ASZTALI FOLYAMI & COLWELL LN; PLYMOUTH; 2015 12-10-16:32:10; @|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Dátumformátum manipuláció karakterlánc kibontási során

Példák, amelyek lemezre van szükség ebben az esetben száma: 1

|Input (Bemenet)|Kimenet|
|:-----|:-----|
|MONTGOMERY AVE & WOODSIDE távoli asztali;  ALACSONYABB MERION; Állomás 313; 2015-12-11 @ 04:11:35;|**12 november 2015 hajnali 4 Órakor**|
|DREYCOTT LN & W LANCASTER AVE;  ALACSONYABB MERION; Állomás 313; 2015-12-11 @ 01:29:52;|12 november 2015 13: 00|
|E LEVERING MILL TÁVOLI ASZTALI & CONSHOHOCKEN ÁLLAPOT TÁVOLI ASZTALI; ALACSONYABB MERION; 2015-12-11 @ 07:29:58;|12 november 2015 de.|
|A távoli asztali PENN VALLEY & MANOR távoli asztali;  ALACSONYABB MERION; Állomás 313; 2015-12-10 @ 20:53:30.|12 Oct 2015 8 du|
|JOVANOVICH AVE & OVERHILL TÁVOLI ASZTALI; ALACSONYABB MERION; 2015-12-10 @ 23:02:27;|12 Oct 2015 23 óra|
|W MONTGOMERY AVE & PENNSWOOD TÁVOLI ASZTALI; ALACSONYABB MERION; 2015-12-10 @ 19:25:22.|12 Oct 2015 7 PM|
|ROSEMONT AVE & KÉZBESÍTETLEN END;  ALACSONYABB MERION; Állomás 313; 2015-12-10 @ 18:43:07;|12 Oct 2015 18: 00|
|A vész-Helyreállítási AVIGNON & KÉZBESÍTETLEN END; ALACSONYABB MERION; 2015 12-10-20:01:29 @-állomás: STA24;|12 Oct 2015 8 du|

### <a name="s4-concatenating-strings"></a>S4. Karakterláncok hozzáfűzésével

Példák, amelyek lemezre van szükség ebben az esetben száma: 1

>[!NOTE] 
>Ebben a példában, speciális karakter. a kimeneti oszlop szóközöket jelöli.

|Utónév|Középső kezdeti|Vezetéknév|Kimenet|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda·· Lohmann|
|Claudio|A|Csillapításához|**Claudio· A· Csillapításához**|
|Sarah-Jane|S|Smith|Sarah-Jane· S· Smith|
|Brandi||Blumenthal|Brandi·· Blumenthal|
|Jesusita|R|Út|Jesusita· R· Út|
|Hermina||Hults|Hermina·· Hults|
|Anna-Marie|W|János|Anna-Marie· W· János|
|Rico||Ropp|Rico·· Ropp|
|Lauren-május||Fullmer|Lauren-May·· Fullmer|
|Marc|T|Maine|Marc· T· Maine|
|Angie||Adelman|Angie·· Adelman|
|John-Paul||Smith|John-Paul·· Smith|
|Szám|W|Staller|Song· W· Staller|
|Jill||Jefferies|Jill·· Jefferies|
|Ruby-türelmi idő|M|Simmons|Ruby-Grace· M · Simmons|

### <a name="s5-generating-initials"></a>S5. Előállítása monogramja

Példák, amelyek lemezre van szükség ebben az esetben száma: 2. régiója

|Teljes név|Kimenet|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio csillapításához|C.C.|
|Sarah-Jane Smith|S.S.|
|Brandi Blumenthal|B.B.1|
|Jesusita út|J.J.|
|Hermina Hults|H. H.|
|Anna-Marie János|A.J.|
|Rico Ropp|R.R.|
|Lauren-előfordulhat, hogy Fullmer|L. F.|
|Marc Maine|M. M.|
|Angie Adelman|A.A.|
|John-Paul Smith|**J.S.**|
|Dal Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby-türelmi Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Állandó értékek leképezése

Példák, amelyek lemezre van szükség ebben az esetben száma: 3

|Felügyeleti nemét|Kimenet|
|:-----|:-----:|
|csatlakozó|**0**|
|nő|**1**|
|Ismeretlen|**2**|
|nő|1|
|nő|1|
|csatlakozó|0|
|Ismeretlen|2|
|csatlakozó|0|
|nő|1|

## <a name="examples-of-number-transformations-by-example"></a>Példa alapján számú átalakítások példák

>[!NOTE] 
>Az értékek **félkövér** határoz meg a példák, amelyeket az átalakítás látható adatkészlet befejezéséhez.


### <a name="n1-rounding-to-nearest-10"></a>N1. Kerekítése a legközelebbi 10-re

Példák, amelyek lemezre van szükség ebben az esetben száma: 1

|Input (Bemenet)|Kimenet|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Kerekítési legközelebbi 10

Példák, amelyek lemezre van szükség ebben az esetben száma: 2. régiója

|Input (Bemenet)|Kimenet|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Kerekítése a legközelebbi 0,05

Példák, amelyek lemezre van szükség ebben az esetben száma: 2. régiója

|Input (Bemenet)|Kimenet|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. A dobozolás

Példák, amelyek lemezre van szükség ebben az esetben száma: 1

|Input (Bemenet)|Kimenet|
|-----:|:-----:|
|20.16|**20-25**|
|14.32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. 1000 skálázása

Példák, amelyek lemezre van szükség ebben az esetben száma: 1

|Input (Bemenet)|Kimenet|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. Kitöltés

Példák, amelyek lemezre van szükség ebben az esetben száma: 1

|Kód|Kimenet|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>Példa alapján dátum átalakítások példák

>[!NOTE] 
>Az értékek **félkövér** határoz meg a példák, amelyeket az átalakítás látható adatkészlet befejezéséhez.


### <a name="d1-extracting-date-parts"></a>D1. Dátum részek kibontása

Ezek a részek különböző által-példa átalakítások használata adatok ugyanazokat a könyvtárban találhatók. A félkövér betűvel írott karakterláncok képviselnek a példák, amelyek a saját megfelelő átalakításában lett megadva.

|Dátum és idő|milyen napra esik|Dátum|Hónap|Év|Óra|Perc|Második|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|05:54:18 31-Jan-2031|**Fri**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|13:32:01 17-Jan-1990|WED|17|Jan|1990|13|32|01|
|05:36:07 14-Feb-2034|K|14|Feb|2034|5|36|07|
|13:16:16 14-gyel-2002|Csüt.|14|Gyel|2002|13|16|16|
|05:44:43 21-Jan-1985.|F|21|Jan|1985|5|44|**43**|
|01:11:56 16-gyel-1985.|Fri|16|Gyel|1985|1|11|56|
|18:36:29 20-Dec-2033|K|20|DEC|2033|18|36|29|
|10:21:59 16-július-1984|F|16|Július|1984|10|21|59|
|A 2038 január 13 10:59:36|WED|13|Jan|2038|10|59|36|
|15:13:54 14-gyel-1982|Kép|14|Gyel|1982|15|13|54|
|08:18:08 22-november-2030|Fri|22|November|2030|8|18|08|
|08:42:58 21-Oct-1997|K|21|Oct|1997|8|42|58|
|14:19:15 28-november-2006|K|28|November|2006|14|19|15|
|04:59:45 29-6%/4!a-2031|K|29|6%/4!a|2031|4|59|45|
|29-Jan-2032 02:38:36|Csüt.|29|Jan|2032|2|38|36|
|15:31:52 11-előfordulhat, hogy-2028|Csüt.|11|Előfordulhat, hogy|2028|15|31|52|
|12:45:39 15-július-1977.|Fri|15|Július|1977|12|45|39|
|05:55:41 27-Jan-2029|Kép|27|Jan|2029|5|55|41|
|10:17:49 03-gyel-2024|Sun|3|Gyel|2024|10|17|49|
|00:23:13 14-6%/4!a-2010|WED|14|6%/4!a|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. A dátumok formázásához

Ezek dátum formattings volt kész adatokat ugyanazokat a különböző által-példa átalakítások használata. A félkövér betűvel írott karakterláncok képviselnek a példák, amelyek a saját megfelelő átalakításában lett megadva.

|Dátum és idő|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|05:54:18 31-Jan-2031|**1/31/2031**|**2031. január 31., péntek**|**01312031 5:54**|**31/1/2031 REGGEL 5:54**|**1. KÉRDÉS 2031**|
|13:32:01 17-Jan-1990|1/17/1990|1990. január 17., szerda|01171990 13:32|17/1/1990 1:32 PM|1. KÉRDÉS 1990.|
|05:36:07 14-Feb-2034|2/14/2034|2034. február 14., kedd|02142034 5:36|14/2/2034 REGGEL 5:36|1. KÉRDÉS 2034
|13:16:16 14-gyel-2002|3/14/2002|2002. március 14., csütörtök|03142002 13:16|14/3/2002 1:16 PM|1. KÉRDÉS 2002
|05:44:43 21-Jan-1985.|1/21/1985|1985. január 21., hétfő|01211985 5:44|21/1/1985 REGGEL 5:44|1. KÉRDÉS 1985.
|01:11:56 16-gyel-1985.|8/16/1985|1985. augusztus 16., péntek|08161985 1:11|16/8/1985 13:11:00|3. NEGYEDÉVÉBEN 1985.
|18:36:29 20-Dec-2033|12/20/2033|2033. December 20., kedd|12202033 18:36|20/12/2033 6:36 DU|4. KÉRDÉS 2033
|10:21:59 16-július-1984|7/16/1984|Hétfőtől, 16, 1984. július|07161984 10:21|16/7/1984 10:21 DE|3. NEGYEDÉVÉBEN 1984.
|A 2038 január 13 10:59:36|1/13/2038|2038. január 13., szerda|01132038 10:59|13/1/2038 10:59 ÓRA|1. KÉRDÉS 2038
|15:13:54 14-gyel-1982|8/14/1982|1982. augusztus 14., szombat|08141982 15:13|14/8/1982 DU. 3:13|3. NEGYEDÉVÉBEN 1982.
|08:18:08 22-november-2030|11/22/2030|2030. November 22., péntek|11222030 8:18|22/11/2030 8:18 ÓRA|4. KÉRDÉS 2030
|08:42:58 21-Oct-1997|10/21/1997|1997. október 21., kedd|10211997 8:42|21/10/1997 REGGEL 8:42|4. KÉRDÉS 1997.
|14:19:15 28-november-2006|11/28/2006|2006. November 28., kedd|11282006 14:19|2006/28/11 2:19 PM|4. KÉRDÉS 2006
|04:59:45 29-6%/4!a-2031|4/29/2031|2031. április 29., kedd|04292031 4:59|29/4/2031 4:59 ÓRA|2. KÉRDÉS 2031
|29-Jan-2032 02:38:36|1/29/2032|2032. január 29., csütörtök|01292032 2:38|29/1/2032 REGGEL 2:38|1. KÉRDÉS 2032
|15:31:52 11-előfordulhat, hogy-2028|5/11/2028|2028. május 11., csütörtök|05112028 15:31|11/5/2028 DU. 3:31|2. KÉRDÉS 2028
|12:45:39 15-július-1977.|7/15/1977|1977. július 15., péntek|07151977 12:45|15/7/1977 12:45:00|3. NEGYEDÉVÉBEN 1977.
|05:55:41 27-Jan-2029|1/27/2029|2029. január 27., szombat|01272029 5:55|27/1/2029 REGGEL 5:55|1. KÉRDÉS 2029
|10:17:49 03-gyel-2024|3/3/2024|2024. március 3., vasárnap|03032024 10:17|3/3/2024 10:17-KOR|2024 1.
|00:23:13 14-6%/4!a-2010|4/14/2010|2010. április 14., szerda|04142010 0:23|14/4/2010 12:23 ÓRA|2. KÉRDÉS 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Az időszakok idő leképezése

Ezen időszak rendelése időpontok volt kész adatokat ugyanazokat a különböző által-példa átalakítások használata. A félkövér betűvel írott karakterláncok képviselnek a példák, amelyek a saját megfelelő átalakításában lett megadva.

|Dátum és idő|Period(seconds)|Period(Minutes)|Időtartam (két óra)|Időtartam (30 perc)|
|-----:|-----:|-----:|-----:|-----:|
|05:54:18 31-Jan-2031|**0-20**|**45-60**|**DE 5 AM.**|**5:30-6:00**|
|13:32:01 17-Jan-1990|**0-20**|30-45|DU. 1-3 PM|13:30-14:00|
|05:36:07 14-Feb-2034|0-20|30-45|DE 5 AM.|5:30-6:00|
|13:16:16 14-gyel-2002|0-20|15-30|DU. 1-3 PM|13:00-13:30|
|05:44:43 21-Jan-1985.|40-60|30-45|DE 5 AM.|5:30-6:00|
|01:11:56 16-gyel-1985.|40-60|0-15|13: 00 - 3 DE|1:00-1:30|
|18:36:29 20-Dec-2033|20-40|30-45|DÉLUTÁN 5 ÓRA - 7 PM|18:30-19:00|
|10:21:59 16-július-1984|40-60|15-30|REGGEL 9 – DE|10:00-10:30|
|A 2038 január 13 10:59:36|20-40|45-60|REGGEL 9 – DE|10:30-11:00|
|15:13:54 14-gyel-1982|40-60|0-15|DU. 3-5 ÓRA|15:00-15:30|
|08:18:08 22-november-2030|0-20|15-30|DE. – 9 ÓRA|8:00-8:30|
|08:42:58 21-Oct-1997|40-60|30-45|DE. – 9 ÓRA|8:30-9:00|
|14:19:15 28-november-2006|0-20|15-30|DU. 1-3 PM|14:00-14:30|
|04:59:45 29-6%/4!a-2031|40-60|45-60|DE 3-5 ÓRA|4:30-5:00|
|29-Jan-2032 02:38:36|20-40|30-45|13: 00 - 3 DE|2:30-3:00|
|15:31:52 11-előfordulhat, hogy-2028|40-60|30-45|DU. 3-5 ÓRA|15:30-16:00|
|12:45:39 15-július-1977.|20-40|45-60|DE - 1 ÓRA|12:30-13:00|
|05:55:41 27-Jan-2029|40-60|45-60|DE 5 AM.|5:30-6:00|
|10:17:49 03-gyel-2024|40-60|15-30|REGGEL 9 – DE|10:00-10:30|
|00:23:13 14-6%/4!a-2010|0-20|15-30|DU 11 – 13: 00|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Példa alapján összetett átalakítások példák

|tripduration|Kezdő időpont|Indítsa el az állomás azonosítója|Indítsa el az állomás szélesség|Indítsa el az állomás hosszúság|UserType|Oszlop|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|Előfizető|**Az előfizető kerékpárt kivételezett állomásról 107, lat/hosszú (42.363,-71.088), 2016. január 08. körülbelül 4 du. A út időtartama óta 61 perc**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|Ügyfél|Az ügyfél kerékpárt kivételezett állomásról 74, lat/hosszú (42.373,-71.119), 2016. január 17., körülbelül Reggel 9. A út időtartama óta 61 perc|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|Előfizető|Az előfizető kerékpárt kivételezett állomásról 176, lat/hosszú (42.387,-71.119), 2016. január 25. körülbelül 8 órakor. A út időtartama óta 62 perc|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|Előfizető|Az előfizető kerékpárt kivételezett állomásról 107, lat/hosszú (42.363,-71.088), 2016. január 08. körülbelül 10 órakor. A út időtartama óta 63 perc|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|Előfizető|Az előfizető kerékpárt kivételezett állomásról 68-as, lat/hosszú (42.365,-71.103), 2016. január 15. körülbelül 7 du. A út időtartama óta 64 perc|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|Előfizető|Az előfizető kerékpárt kivételezett állomásról 115, lat/hosszú (42.388,-71.119), 2016. január 22. körülbelül 6 du. A út időtartama óta 64 perc|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|Előfizető|Az előfizető kerékpárt kivételezett állomásról 178, lat/hosszú (42.360,-71.101), 2016. január 18. körülbelül 9 órakor. A út időtartama óta 68 perc|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|Előfizető|Az előfizető kerékpárt kivételezett állomásról 176, lat/hosszú (42.387,-71.119), 2016. január 14. körülbelül 8 órakor. A út időtartama óta 69 perc|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|Előfizető|Az előfizető kerékpárt kivételezett állomásról 141, lat/hosszú (42.364,-71.082), 2016. január 13. körülbelül 10 du. A út időtartama óta 69 perc|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|Előfizető|Az előfizető kerékpárt kivételezett állomásról 176, lat/hosszú (42.387,-71.119), 2016. január 15. körülbelül 8 órakor. A út időtartama óta 69 perc|


## <a name="technical-notes"></a>Technikai megjegyzések

### <a name="conditional-transformations"></a>Feltételes átalakítások
Bizonyos esetekben egyetlen átalakítás nem található, amely megfelel a megadott példákat. Ebben az esetben származnia oszlop példa átalakítási megkísérli a bemenetek néhány szabály alapján csoportnak, és ismerje meg az egyes csoportok külön átalakítása. Ezt nevezzük **feltételes átalakítása**. **Feltételes átalakítása** csak egyetlen bemeneti oszloppal átalakításokhoz kísérlet történik. 

### <a name="reference"></a>Referencia
További információk a karakterlánc átalakításáról példa Technology megtalálhatók [a kiadvány](https://www.microsoft.com/en-us/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/).
