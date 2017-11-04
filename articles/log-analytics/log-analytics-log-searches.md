---
title: "Napló keresések rendelkező adatok kereséséhez az Azure Naplóelemzés |} Microsoft Docs"
description: "A naplókeresések segítségével a környezetben lévő különféle forrásokból származó adatokat ötvözhet, és feltárhatja az összefüggéseket."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 0d7b6712-1722-423b-a60f-05389cde3625
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.openlocfilehash: d679ca7a01a96bd398b26e6a545e33674ae33390
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="find-data-using-log-searches-in-log-analytics"></a>Napló keresések használatát Naplóelemzési adatok megkeresése

>[!NOTE]
> Ez a cikk ismerteti a napló keresések Naplóelemzési az örökölt lekérdezési nyelv használatával.  Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd tekintse át [ismertetése napló keres a Log Analyticshez (új)](log-analytics-log-search-new.md).


A Naplóelemzési középpontjában van a napló keresési szolgáltatás, amely lehetővé teszi a kombinálhatja, és a számítógép adatainak a környezetben több forrásból. Megoldások is napló szerint vannak kapcsolva növelheti az metrikák keresés egy adott probléma terület körül átalakítani.

A lapon létrehozhat egy lekérdezést, és majd kereséskor szűrheti az eredményeket értékkorlátozás vezérlők használatával. Az eredmények átalakító, a szűrő és a jelentés speciális lekérdezéseket is létrehozhat.

Általános naplófájl-keresési lekérdezések a legtöbb megoldás oldalon jelennek meg. A OMS konzol különböző pontjain kattintson a csempéket, vagy a részletesebb egyéb elemek elem részleteinek megtekintéséhez a keresési napló használatával.

Ebben az oktatóanyagban végigvesszük példák a alapjait fedik le a naplófájl-keresési használatakor.

Azt a lesz egyszerű, gyakorlati példák kezdődnie, és majd kialakítható rajtuk, így is ki a kívánt elemzéseket kibontani az adatokat a szintaxis használatával kapcsolatos gyakorlati használati esetek ismeretét.

Miután megismerte a keresési technikák ismeri, tekintse át a [Naplóelemzési keresési hivatkozás jelentkezzen](log-analytics-search-reference.md).

## <a name="use-basic-filters"></a>Basic-szűrők használata
Először hozzá kell tudnia kell, hogy a Keresés az első része lekérdezéséhez minden "|}" függőleges vonal karaktert, mindig van olyan *szűrő*. Az eltolásokat tekintheti, mint a TSQL--WHERE záradék meghatározza, hogy *mi* való lekérésére kívül az OMS-adattár adatok részhalmazát. A Keresés az adattárban tárgya nagy mértékben a kívánt adatokat kinyerési, ezért ezt a természetes, hogy a lekérdezés a WHERE záradékban kellene kezdődnie jellemzőinek megadása.

Használhatja a legalapvetőbb szűrők a következők *kulcsszavak*, például az "error" vagy "timeout" vagy a számítógép nevét. Ezek a lekérdezéstípusok egyszerű általában különböző alakzatok belül az azonos eredményhalmaz adja vissza. Ennek az az oka Naplóelemzési rendelkezik különböző *típusok* az adatok a rendszerben.

### <a name="to-conduct-a-simple-search"></a>Az egyszerű keresés végrehajtása
1. Az OMS-portálon kattintson **naplófájl-keresési**.  
    ![keresési csempe](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. A lekérdezés mezőbe írja be a `error` majd **keresési**.  
    ![keresési hiba](./media/log-analytics-log-searches/oms-search-error.png)  
    Például a lekérdezés `error` az alábbi képen visszaadott 100 000 **esemény** (napló Management által összegyűjtött) rekordok 18 **ConfigurationAlert** (által előállított rekordokat konfiguráció Értékelés) és 12 **konfigurációváltozás** (a változások követése által rögzített) rögzíti.   
    ![keresési eredmények](./media/log-analytics-log-searches/oms-search-results01.png)  

Ezek a szűrők csak valóban típusok/objektumosztályokon. *Típus* csak egy címkét, vagy tulajdonság vagy karakterlánc/neve/kategória, az adatok csatolva van. A rendszer az egyes dokumentumok címkével rendelkeznek, **típusa: ConfigurationAlert** néhány címkével rendelkeznek, és **típusa: telj**, vagy **esemény típusa:**, és így tovább. Minden keresési eredmény, a dokumentum, a rekord vagy a belépési jeleníti meg a nyers tulajdonságok és értékeik minden egyes adatok, és segítségével Mezőnevekkel adja meg a szűrő, ha szeretné beolvasni a rekordok ahol mező rendelkezik, amelyek adott érték.

*Típus* csupán az olyan mezője, amely rendelkezik az összes rekord, eltér nem bármely más mezőre. Ez a jött létre, a mezőben megadott érték alapján. Hogy a rekord a különböző formájára lesz. Mellékesen **típus telj =**, vagy **típus = esemény** lekérdezni ügynökteljesítmény-adatokat és eseményeket a szükséges szintaxist is van.

A kettőspont (:) vagy egyenlőségjellel (=) után a mező nevét és értékét előtt is használhatja. **Esemény típusa:** és **típus = esemény** vannak egyenértékű a jelentéssel, dönthet úgy, inkább stílusát.

Igen, ha a típus = telj rekordok rendelkezik egy "CounterName" nevű mező, majd írhat egy lekérdezést színű `Type=Perf CounterName="% Processor Time"`.

Adja meg csak a teljesítményadatokat a teljesítményszámláló nevének "kihasználtsága (%) esetén.

### <a name="to-search-for-processor-time-performance-data"></a>Processzor teljesítményének időadatok keresése
* Írja be a keresőmezőbe lekérdezés`Type=Perf CounterName="% Processor Time"`

Is pontosabban, és használjon **InstanceName = _ "Összesen"** a lekérdezés, amely Windows teljesítményszámláló. Igény szerint kiválaszthatja egy dimenzió, és egy másik **mezőérték:**. A szűrő automatikusan hozzáadódik a lekérdezés sávon a szűrőnek. Az alábbi ábrán látható. Jelzi, hogy hol hozzáadásához kattintson **InstanceName: "_Total"** semmit beírása nélkül a lekérdezéshez.

![keresési dimenzió](./media/log-analytics-log-searches/oms-search-facet.png)

A lekérdezés most válik.`Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

Ebben a példában, nem kell megadnia **típus = telj** Ez az eredmény eléréséhez. Mivel a CounterName és példánynév mező csak létezik típusú rekordok = teljesítmény, a lekérdezés nem adott vissza a hosszabb, előző egy azonos:

```
CounterName="% Processor Time" InstanceName="_Total"
```

Ennek az az oka, hogy a lekérdezésben szereplő összes szűrőt értékelődnek *és* egymással. Hatékony, a további mezőket ad hozzá a feltételeket, kevesebb, kapott adott és kifinomultabb eredmények.

Például a lekérdezés `Type=Event EventLog="Windows PowerShell"` azonos `Type=Event AND EventLog="Windows PowerShell"`. Bejelentkezett és a Windows PowerShell eseménynaplójából gyűjtött összes eseményt adja vissza. Hozzáadásakor szűrő többször ismételten kiválasztásával a ugyanazon dimenzió, akkor a probléma pusztán formai –, előfordulhat, hogy megzavarhatják a keresési sávon, de azt továbbra is ad vissza ugyanaz az eredmény mivel az implicit és operátor mindig létezik.

Az implicit és operátor explicit módon a NOT operátor használatával egyszerűen visszirányú. Példa:

`Type:Event NOT(EventLog:"Windows PowerShell")`vagy azzal egyenértékű `Type=Event EventLog!="Windows PowerShell"` összes esemény visszaadásának további naplófájlokat, amelyek nem a Windows PowerShell-naplót.

Vagy más logikai operátor használhatja például a "Vagy". A következő lekérdezés adja vissza, amelynek az Eseménynapló rendszer vagy alkalmazás vagy rögzíti.

```
EventLog=Application OR EventLog=System
```

A fenti lekérdezés használatával, az azonos eredményhalmaz mindkét naplók bejegyzések fog kapni.

Ha azonban a vagy el kell távolítania, mivel a implicit és helyen, majd a következő lekérdezés fog nem termék eredmények mert mindkét naplók tartozó eseménynapló-bejegyzés nem létezik. Minden egyes eseménynapló-bejegyzés készült csak a két naplók egyikébe.

```
EventLog=Application EventLog=System
```


## <a name="use-additional-filters"></a>További szűrők használata
A következő lekérdezés 2 eseménynaplóiban keresse meg a számítógépek küldött adatok bejegyzéseit adja vissza.

```
EventLog=Application OR EventLog=System
```

![keresési eredmények](./media/log-analytics-log-searches/oms-search-results03.png)

A mező vagy a szűrők egyikének kiválasztásával szűkítheti a lekérdezést, hogy egy adott számítógép, csak egyesekből kivételével. Az eredményül kapott lekérdezés a következő hasonló lesz.

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

Ez az egyenértékű a következők miatt a implicit és művelet.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Minden egyes lekérdezés kiértékelése a következő explicit sorrendben. Vegye figyelembe a zárójel.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Csakúgy, mint az Eseménynapló mező is visszaállíthatja az adatokat csak az adott számítógépek hozzáadásával vagy. Példa:

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

Hasonlóképpen, ez a következő lekérdezés vissza **% CPU-idő** csak a kijelölt két számítógépek.

```
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```

### <a name="field-types"></a>Mezőtípusok
Szűrők létrehozásakor meg kell ismernie a különbségek különböző típusú mezők napló keresés által visszaadott használata.

**Kereshető mezők** kék keresési eredmények megjelenítése.  A mező a következő keresési feltételek adott a kereshető mezők segítségével:

```
Type: Event EventLevelName: "Error"
Type: SecurityEvent Computer:Contains("contoso.com")
Type: Event EventLevelName IN {"Error","Warning"}
```

**Szöveg kereshető mezőt szabad** jelennek meg a keresési eredmények között szürke.  A mező, például a kereshető mezők vonatkozó keresési feltételek használható.  Csak keresett keresztül minden mező a következő lekérdezés végrehajtása során.

```
"Error"
Type: Event "Exception"
```


### <a name="boolean-operators"></a>Logikai operátorok
A dátum és idő és a numerikus, kereshet értékek *nagyobb, mint*, *kisebb mint*, és *kisebb vagy egyenlő, mint*. Használhatja például a egyszerű operátorok >, <>, =, < =,! = a lekérdezés keresősávban.

Egy adott eseménynaplóból is kereshet egy adott időszakra vonatkozóan. Például az elmúlt 24 órában van megadva a következő mnemonikus kifejezésbe.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### <a name="to-search-using-a-boolean-operator"></a>Egy logikai operátorral kereséséhez
* Írja be a keresőmezőbe lekérdezés`EventLog=System TimeGenerated>NOW-24HOURS`  
    ![a logikai keresése](./media/log-analytics-log-searches/oms-search-boolean.png)

Bár grafikusan szabályozhatja az időtartam alatt, és a legtöbb alkalommal érdemes lehet megtenni, amely, számos előnnyel jár idő szűrő közvetlenül a lekérdezés is beleértve. Például a nagyszerű ahol felülbírálhatja mindegyik mozaiknál idő függetlenül attól, hogy az irányítópultok a *globális* idő választó az irányítópult-oldalon. További információkért lásd: [idő kérdések irányítópulton](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Amikor idő szerinti szűrését, vegye figyelembe, hogy az eredmény a *metszetének* a két időtartamot-: az OMS-portálon (S1) megadott és a lekérdezés (S2) megadottal.

![metszetének](./media/log-analytics-log-searches/oms-search-intersection.png)

Ez azt jelenti, ha az időszakok nem intersect, például az OMS-portállal, amelyen kiválaszthatja a **ezen a héten** és a lekérdezésben, ahol megadhatja a **múlt héten**, akkor nem metszetének, és sem nem kap eredmények.

A TimeGenerated mező összehasonlító operátorok más helyzetekben is hasznosak. Például a numerikus mezőket.

Ha például adott, konfigurációs Assessment riasztások rendelkezik-e a következő fontossági értékek:

* 0 = információk
* 1 = figyelmeztetés
* 2 = kritikus

A figyelmeztetési és a kritikus riasztások lekérdezni, és szintén kizárhatja a tájékoztató állók közül. a következő lekérdezést:

```
Type=ConfigurationAlert  Severity>=1
```


Tartomány-lekérdezéseket is használhatja. Ez azt jelenti, hogy az egymást sorrendben követő értékek elején és végén tartományán biztosíthat. Például ha azt szeretné, hogy az Operations Manager eseménynaplójában, hol található az eseményazonosító, több mint vagy egyenlő 2100, de nem nagyobb, mint 2199 származó események, majd a következő lekérdezés alakítanák vissza őket.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


> [!NOTE]
> Szintaxisa a következő tartományban kell használnia a kettőspont (:) mező: érték elválasztó és *nem* az egyenlőségjel (=). A tartomány alsó és felső végéig tegye szögletes zárójelbe, és válassza el őket két pontot (.).
>
>

## <a name="manipulate-search-results"></a>Kezelheti a keresési eredmények
Ha a keresett adatok, érdemes pontosítsa a keresést, és szabályozhatják, az eredmények megfelelő szintű. Eredmények beolvasásakor alakítják parancsok is alkalmazhat.

Log Analytics-keresések parancsok *kell* hajtsa végre a függőleges vonal (|) karakter után. A szűrő mindig kell egy lekérdezési karakterlánc első része. Határozza meg az adatkészlet dolgozunk, és ezt követően "átadja" az eredmények a parancsban. A cső segítségével majd adja hozzá a további parancsok. Ez az a Windows PowerShell kimenetátirányítási mechanizmusával lazán hasonló.

Általában a Naplóelemzési keresési nyelvi megpróbálja PowerShell stílus és útmutatókkal együtt, így az informatikai szakemberek hasonló, és megkönnyítik a tanulási görbére kövesse.

Parancsok a műveletek nevük legyen, így könnyen állapítható meg, ezek.  

### <a name="sort"></a>Rendezés
A rendezési parancs lehetővé teszi egy vagy több mező által a rendezési sorrend definiálásához. Akkor is, ha, alapértelmezés szerint nem használ, a rendszer érvényesíti egyszerre csökkenő sorrendben. A legutóbbi eredményei mindig keresési eredmények tetején. Ez azt jelenti, hogy a Keresés az futtatásakor `Type=Event EventID=1234` mi valóban végrehajtása meg van:

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

Ennek oka az, a felhasználói felület ismeri a naplókban típusú. Például az a Windows eseménynaplóban.

Rendezés használatával módosíthatja az eredmény akkor minősül. Az alábbi példák bemutatják, hogyan működik.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


A fenti egyszerű példák megjelenítése parancsok működése – a szűrő visszaadott eredmények alakját változik.

### <a name="limit-and-top"></a>Korlátozás és a felső
Egy másik kevésbé ismert parancs korlátozás. A határ egy PowerShell-szerű művelet. Korlát funkcionálisan megegyezik a TOP parancsot. A következő lekérdezés ugyanazokat az eredményeket ad vissza.

```
Type=Event EventID=600 | Limit 1
```

```
Type=Event EventID=600 | Top 1
```


#### <a name="to-search-using-top"></a>A keresés felső használatával
* Írja be a keresőmezőbe lekérdezés`Type=Event EventID=600 | Top 1`   
    ![keresés felső](./media/log-analytics-log-searches/oms-search-top.png)

A fenti ábrán vannak 358 ezer rekordokat EventID = 600. A mezőket, facets és a bal oldali szűrők mindig információkat jelenít meg az adott eredmények *által a szűrő része* a lekérdezés, mielőtt bármilyen függőleges vonal része. A **eredmények** ablaktáblán csak eredményt adja vissza a legutóbbi 1, mert a példaparancs alakú, és át legyenek-e az eredményeket.

### <a name="select"></a>Válassza ezt:
A SELECT utasítás Select-Object PowerShell viselkedik. Szűrt eredmények, amelyek nem rendelkeznek az összes azok eredeti tulajdonságait adja vissza. Ehelyett azt választja ki csak a megadott tulajdonságokat.

#### <a name="to-run-a-search-using-the-select-command"></a>A keresések a kijelölési parancs futtatásához
1. Írja be a keresésbe, `Type=Event` majd **keresési**.
2. Kattintson a **+ megjelenítése további** egy, az eredmények az eredményeket tartalmazó tulajdonságok megtekintéséhez.
3. Explicit módon válasszon olyanokat is, és a lekérdezés módosításainak `Type=Event | Select Computer,EventID,RenderedDescription`.  
    ![keresési kiválasztása](./media/log-analytics-log-searches/oms-search-select.png)

Ez a parancs akkor különösen hasznos, ha azt szeretné, keresési kimeneti ellenőrzés, és válassza ki a feltárási, amely a teljes rekord gyakran nem igazán fontos adatok csak az részének. Ez akkor hasznos, ha különböző típusú rekord *néhány* általános tulajdonságait, de nem *összes* a Tulajdonságok megegyeznek. A, kimenete, amely több természetes néz ki egy táblát, vagy jól használhatók CSV-fájlba exportálni, és majd massaged az Excel programban.

## <a name="use-the-measure-command"></a>A mérték paranccsal
MÉRTÉK a Naplóelemzési keresések legsokoldalúbb parancsok egyikét. Lehetővé teszi az alkalmazása statisztikai *funkciók* adatai és a megadott mező szerint csoportosítva aggregált eredményekben. Nincsenek több statisztikai függvények, amelyek mérték támogatja.

### <a name="measure-count"></a>Mérték count()
Az első statisztikai függvény használata, illetve az egyik legegyszerűbb megértéséhez a *count()* függvény.

Például az összes keresési lekérdezés eredménye `Type=Event`, más néven a keresési eredmények bal oldalán értékkorlátozás szűrők megjelenítése. A szűrők megjelenítése a eredmény elérése érdekében a megadott mező szerint értékek terjesztési hajtotta végre a keresést.

![keresési mérték száma](./media/log-analytics-log-searches/oms-search-measure-count01.png)

Például a fenti ábrán láthatja a **számítógép** mező, és azt mutatja, hogy az eredmények szinte 739 ezer eseményeiben nincsenek 68 egyedi és különböző értéket vár a **számítógép** mező mellett azokat a rekordok. A csempe csak az első 5, amelyek a leggyakoribb 5 értékeket írt jeleníti meg a **számítógép** mezők), ezt a mezőt a megadott értéket tartalmazó dokumentumok száma szerint rendezett. Az ábrán látható, hogy – között olyan szinte 369 ezer eseményeket – 90 ezer a OpsInsights04.contoso.com számítógép 83 ezer DB03.contoso.com számítógépről származó, és így tovább.

Mi történik, ha meg szeretné tekinteni összes érték, mivel a csempe csak jeleníti meg a csak az első 5?

Ez a mérték parancs Teendők a count() függvénnyel. Ez a funkció nem használ paramétereket. Csak adja meg a mező, amellyel – szerint csoportosítani kíván a **számítógép** mezőben ebben az esetben:

`Type=Event | Measure count() by Computer`

![keresési mérték száma](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

Azonban **számítógép** csak használt mező *a* minden adat – nincsenek érintett relációs adatbázisok, és nincs nincs külön **számítógép** bárhol objektum. Csak az értékek *a* az adatok leírhatja entitáshoz őket, és más jellemzői és szempontokat, ezért az adatok – előállított kifejezés *értékkorlátozás*. Azonban is csoportosíthatja más mezők szerint. Mivel az eredeti eredmények szinte 739 ezer események, amelyek mérték parancsa adatcsatornán is rendelkezik egy nevű mező **EventID**, alkalmazhat ugyanaz a technika, hogy a mező szerint kell csoportosítani, és az eseményazonosító események száma:

```
Type=Event | Measure count() by EventID
```

Ha nem a megadott értéket tartalmazza a tényleges rekordszám kíváncsiak vagyunk, de ehelyett Ha szeretné csak a saját magukat értékből álló lista, adhat hozzá egy *válasszon* azt végén parancsot, és az első oszlop csak kiválaszthatnak:

```
Type=Event | Measure count() by EventID | Select EventID
```

Ezután beolvasása több bonyolult, és előre rendezheti az eredményeket a lekérdezésben, vagy túl csak kattintson a oszlopai.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### <a name="to-search-using-measure-count"></a>A keresés használatával mérték száma
* Írja be a keresőmezőbe lekérdezés`Type=Event | Measure count() by EventID`
* Hozzáfűzendő `| Select EventID` a lekérdezés végéhez.
* Végezetül hozzáfűzése `| Sort EventID asc` a lekérdezés végéhez.

Van néhány fontos tényezőt figyelje meg, és emelje ki:

Először a látja eredményei nem az eredeti nyers eredmények többé. Ehelyett azok összesített eredmények – tulajdonképpen az eredmények csoportok. Ez nem hiba, de tisztában kell lennie, hogy van-e interakció eltérő adatokat az eredeti nyers alakzata menet közben a összesítési és statisztikai függvény eredményeként jön létre a nagyon különböző alakját.

Második, **számának mérésére** jelenleg csak az első 100 eltérő eredményt ad vissza. Ezt a határt a statisztikai függvények nem vonatkozik. Igen általában szüksége pontosabb szűrő használatához először mérték count() telepítését megelőzően bizonyos elemek kereséséhez.

## <a name="use-the-max-and-min-functions-with-the-measure-command"></a>A maximális és minimális funkciók használata a mérték parancs
Számos esetben ha **mérték Max()** és **mérték Min()** hasznosak. Azonban mivel minden egyes függvény ellentétes minden más, azt is bemutatják Max() és kísérletezhet Min() saját.

Biztonsági események kérdezze le, ha van egy **szint** tulajdonság, amely eltérőek lehetnek. Példa:

```
Type=SecurityEvent
```

![keresési mérték száma indítása](./media/log-analytics-log-searches/oms-search-measure-max01.png)

Ha meg szeretné tekinteni a legmagasabb érték az összes, a biztonsági események megadott közös számítógép, a Csoportosítás mező, használhatja

```
Type=ConfigurationAlert | Measure Max(Level) by Computer
```

![keresési mérték maximális számítógép](./media/log-analytics-log-searches/oms-search-measure-max02.png)

Jeleníti meg, hogy a számítógépek, amelyekre **szint** rekordok, azokat a többsége a legalább 8. szintű számos kellett egy szint 16.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![keresési mérték maximális idő számítógép jön létre.](./media/log-analytics-log-searches/oms-search-measure-max03.png)

Számok jól működik ez a funkció, de a DateTime mezők is működik. Ez akkor hasznos, az utolsó vagy a legújabb időbélyegzője bármely adat minden számítógéphez indexelt kereséséhez. Példa: Ha a legutóbbi biztonsági esemény történt az egyes gépek?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## <a name="use-the-avg-function-with-the-measure-command"></a>Az avg függvénnyel használata a mérték parancs
A mérték használt Avg() statisztikai függvény lehetővé teszi az egyes mező, és az ugyanazon vagy másik mező szerint eredmények átlagos értékének kiszámítása. Ez akkor hasznos, a különböző esetekben, például a teljesítmény.

Először foglalkozunk teljesítményadatokat. Vegye figyelembe, hogy jelenleg OMS összegyűjti a Windows és Linux rendszerű gépek teljesítményszámlálók.

Keresendő *összes* teljesítményadatokat, a lehető legegyszerűbb lekérdezés van:

```
Type=Perf
```

![keresési avg indítása](./media/log-analytics-log-searches/oms-search-avg01.png)

Láthatja a legfontosabb az, hogy a Naplóelemzési elsajátíthatja, hogy három perspektívák: lista, amely jelzi, hogy a tényleges rekordok megjelenítheti az mögött a diagramok; Táblázatot, amely bemutatja a teljesítményszámláló-adatokat; a táblázatos nézet és metrikákat, amely mutatja a teljesítményszámlálókkal diagramokat.

A fenti kép jelölt mezők, amelyek azt jelzik, a következő két csoportjára van:

* Az első beállítása Windows teljesítményszámláló neve, a objektum neve és a példány nevét határozza meg a lekérdezési szűrőt. A mezőket, valószínűleg leggyakrabban használni kívánt értékkorlátozás/szűrők
* **Ellenértéknek** a számláló a tényleges érték. Ebben a példában a értéke *75*.
* **TimeGenerated** 12:51, 24 órás formátumban van.

Ez a metrika egy grafikonon nézetét.

![keresési avg indítása](./media/log-analytics-log-searches/oms-search-avg02.png)

A teljesítményadat-rekord alakzat témakörben elolvasta, és hogy olvassa el, egyéb keresési technikák, után mérték Avg() használhatja, ha ilyen típusú numerikus adatok összesítésére.

Íme egy egyszerű példa:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![keresési avg samplevalue](./media/log-analytics-log-searches/oms-search-avg03.png)

Ebben a példában válassza ki a teljes processzoridő teljesítmény számláló és átlagos számítógépen. Ha azt szeretné, hogy csak az elmúlt 6 óra az eredmények szűkítéséhez, használja a szűrő vezérlő, vagy adja meg a lekérdezés az alábbiak szerint:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### <a name="to-search-using-the-avg-function-with-the-measure-command"></a>Az avg függvénnyel használata a mérték parancs kereséshez
* Írja be a keresőmezőbe lekérdezés `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`.

Összesítő és a adatainak *keresztül* számítógépek. Tegyük fel például, hogy rendelkezik-e a farmhoz, ahol minden csomópont bármely más egynek és azok csupán ugyanazokat írja be a munkahelyi, kell lennie nagyjából az elosztott terhelésű valamilyen állomásoknak. Az összes, egy nyissa meg a következő lekérdezési és átlagok lekérni a teljes farmban számlálók sikerült. Válassza ki a következő példa a számítógépek megkezdése:

```
Type=Perf AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Most, hogy a számítógépek, is szeretné válassza ki a két fő teljesítménymutatók (KPI): % CPU-használat és a % szabad lemezterület. Igen a lekérdezés a része lesz:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Most számítógépek és a számlálók a következő példa is hozzáadhat:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Egy olyan speciális kijelölés, mert a **Avg() mérjük** parancs térhet vissza az átlagos nem a számítógépen, de a farm egyszerűen CounterName szerint csoportosítva. Példa:

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03") | Measure Avg(CounterValue) by CounterName
```

Ez lehetővé teszi a környezet KPI-k néhány hasznos kompakt nézetét.

![keresési avg csoportosítás](./media/log-analytics-log-searches/oms-search-avg04.png)

A keresési lekérdezés irányítópult könnyen használható. Például nem sikerült a keresési lekérdezés mentéséhez, és hozzon létre egy irányítópultot, nevű *webkiszolgáló Farm KPI-k*. Irányítópultok használatával kapcsolatos további tudnivalókért lásd: [egyéni irányítópult létrehozása a Naplóelemzési](log-analytics-dashboards.md).

![keresési avg irányítópult](./media/log-analytics-log-searches/oms-search-avg05.png)

### <a name="use-the-sum-function-with-the-measure-command"></a>A sum függvénnyel használata a mérték parancs
A sum függvénnyel hasonlít a mérték parancs egyéb funkciót. Láthatja, a sum függvénnyel használatával kapcsolatos példáért [W3C IIS naplók keresése a Microsoft Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

Max() és Min() használható számok, időpontok és szöveges karakterláncot. A szöveges karakterláncok betűrend szerint rendezve jelennek és először első és utolsó.

Azonban nem használhat Sum() numerikus mezők csakis. Ez a Avg() is vonatkozik.

### <a name="use-the-percentile-function-with-the-measure-command"></a>A százalékfüggvény használata a mérték parancs
A százalékfüggvény Avg() és Sum() hasonlít abban, hogy a numerikus mezők csak használható. Egy számmező 1 és 99 között bármilyen érték használható. Is használhatja az mindkét **PERCENTILIS** és **pct** parancsok. Íme néhány példa:  

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## <a name="use-the-where-command"></a>A where használható parancs
Az adott parancs szűrő hasonlóan működik, de a feldolgozási további szűréséhez összesített eredmények, amelyek mérték parancs – által előállított is alkalmazható szemben a nyers eredmények, amelyek a rendszer lekérdezés elején.

Példa:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

Hozzáadhat egy másik cső "|}" karaktert és a Where csak megszerezni a számítógépet, amelynek átlagos CPU 80 %-át, a következő példa a fenti parancsot:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

Ha ismeri a Microsoft System Center – Operations Manager, tulajdonképpen a where parancs a felügyeleti csomag feltételeknek. Ha a példa egy szabályt, a lekérdezés első része lehet az adatforrás és a where-parancsot kell megadni a feltételészlelése.

A csempe is használhatja a lekérdezés **saját irányítópult**, rendezés, ha túl magas kihasználtságú-e számítógép processzort megegyezik egy figyelőjével. Irányítópultok kapcsolatos további információkért lásd: [egyéni irányítópult létrehozása a Naplóelemzési](log-analytics-dashboards.md). Hozhat létre, és használja a mobilalkalmazás-irányítópultot. További információkért lásd: [OMS mobilalkalmazás ](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). A a következő kép alsó két csempék, hogy a figyelő listája jelenik meg, és egy számot. Alapvetően mindig szeretne száma nulla és a lista üres. Ellenkező esetben a figyelmeztetési állapot azt jelzi. Ha szükséges, segítségével, tekintse meg, mely számítógépek vannak terhelés alatt.

![mobil irányítópult](./media/log-analytics-log-searches/oms-search-mobile.png)

## <a name="use-the-in-operator"></a>Az in operátor használata
A *IN* operátor szerinti szűrése, valamint *NOT IN* subsearches, amelyek közé tartozik, amelynek argumentuma egy újabb keresést kereséseket használhatók. Kapcsos zárójelek {} belül egy másik található *elsődleges* vagy *külső* keresési. Egy subsearch, gyakran különböző eredmények listája eredményét majd használt elsődleges keresését argumentumaként.

Subsearches segítségével fájlcsoportokat a, hogy közvetlenül egy keresési kifejezést, de a keresési generálhatók az ismerteti nem felel meg. Ha érdekli az összes esemény keresése egy keresési használatával például *gépek, amelyekről biztonsági frissítések hiányoznak*, akkor szükséges, amely először azonosítja, amely egy subsearch tervezése *hiányzó biztonsági frissítéseketszámítógépek* előtt állomásokhoz tartozó események talál.

Igen, sikerült express *számítógépek jelenleg hiányzik a szükséges biztonsági frissítések* a következő lekérdezést:

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![A keresési példa](./media/log-analytics-log-searches/oms-search-in01-revised.png)

Miután a listában, akár egy belső keresés a keresés is használhatja a hírcsatorna a számítógépek listáját egy külső (elsődleges)-keresés, amely kikeresi az események azon számítógépek esetén be. Ehhez a lehetséges értékek a külső keresés az IN operátor használata egy szűrő/mezőre vonatkozó, a belső keresési befoglaló zárójelek között és az eredményeket. A lekérdezés hasonló lesz:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![A keresési példa](./media/log-analytics-log-searches/oms-search-in02-revised.png)

Is értesítést a időszűrője szerepel a belső keresési, mert a rendszer frissítés felmérése pillanatképet az összes számítógép 24 óránként. Hogy a belső lekérdezés könnyű, és pontos naponta csak keresésével. A külső keresési helyette használja az időbeállítást a felhasználói felületen események lekérése az elmúlt 7 napban. Lásd: [logikai operátorok](#boolean-operators) idő operátorok további információt.

Mivel Ön valóban csak szűrőként belső keresési eredmények érték, a külső egy, a külső keresési parancsok továbbra is alkalmazhatja. Például továbbra is csoportosíthatja a fenti események másik mérték paranccsal:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![A keresési példa](./media/log-analytics-log-searches/oms-search-in03-revised.png)

Általában érdemes a belső lekérdezés gyorsan végrehajtása, mert Naplóelemzési Szolgáltatásoldali időtúllépések hozzá és eredmények kis mennyiségű vissza. A belső lekérdezés több eredményeket ad vissza, ha az eredménylista lekérdezi csonkolva lesz, ami miatt a külső keresés megfelelő eredményeket.

Egy másik szabály, hogy a belső keresés jelenleg kell megadni. *összesített* eredmények. Más szóval tartalmaznia kell egy *mérték* parancs; be egy külső keresés jelenleg nem hírcsatorna nyers eredmények.

Is csak egy IN operátor lehet, és a lekérdezés utolsó szűrő kell lennie. Több IN operátor nem lehet, vagy szeretné – Ez lényegében megakadályozza, hogy több subsearches fut: lényege, hogy csak egy sub/belső a keresés minden külső keresési lehetőség.

Ezek a korlátozások mellett is IN lehetővé teszi, hogy számos különböző típusú korrelált keresések, és adhatók meg az alábbihoz hasonlót csoportjának, pl. a számítógépek, felhasználók vagy a fájlok – függetlenül az adatok mezőinek tartalmazzák. Az alábbiakban további példákat:

**Minden frissítés hiányzik a számítógépeken, ahol automatikus frissítési beállítás le van tiltva**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**Összes hiba esemény (ahol SQL Assessment futott =) SQL Server rendszert futtató számítógépek**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**Minden biztonsági esemény (ahol AD Assessment futott =) tartományvezérlő számítógépekről**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**Amely más fiókokat ugyanazokra a számítógépekre, ahol BACONLAND\jochan fiók bejelentkezése jelentkeztek be?**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## <a name="use-the-distinct-command"></a>A különböző paranccsal
A nevet javasol, a parancs különböző értékből álló lista mező biztosít. Fontos, hogy érdekes egyszerű, de elég hasznos lehet. Azonos sikerült érhető el a mérték count() paranccsal, ahogy alább látható.

```
Type=Event | Measure count() by Computer
```

![KÜLÖNBÖZŐ keresési példa](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

Azonban minden továbbra is érdekli esetén csak a különböző értékeket, és nem is, hogy az értékek, majd a DISTINCT biztosíthat számát listáját tisztító és könnyebben olvasható, kimeneti és rövidebb szintaxist, az alábbi ábrán látható módon.

```
Type=Event | Distinct Computer
```
![KÜLÖNBÖZŐ keresési példa](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## <a name="use-the-countdistinct-function-with-the-measure-command"></a>A countdistinct függvény használata a mérték parancs
A countdistinct függvény különböző értékeket az egyes csoportok megszámlálása. Például akkor használható az egyes jelentett egyedi számítógépek számát:

```
* | measure countdistinct(Computer) by Type
```

![OMS-countdistinct](./media/log-analytics-log-searches/oms-countdistinct.png)

## <a name="use-the-measure-interval-command"></a>A mérték időköz paranccsal
A közel valós idejű teljesítményadat-gyűjtés, összegyűjtheti, és bármilyen teljesítményszámláló, a Naplóelemzési megjelenítése. Egyszerűen csak írja be a lekérdezés **típusa: telj** metrika diagramjait számlálók és a Naplóelemzési környezetében lévő kiszolgálókat számától függően több ezer ad vissza. Az igény szerinti metrika összesítéssel vessen egy pillantást a teljes metrikák magas szintű és részletes bemutatója részletesebb adatokká szükség van a környezetben.

Tegyük fel, hogy szeretné-e ismert átlagos CPU a számítógépek között. Átlagos CPU minden számítógép keresése nem hasznos lehet, mert az eredmények lehet, hogy első Görbített. További részletekért megismerhetők, fájlba is összevonhatja az eredmény egy kisebb idő ablak adattömböket, és keresse meg idősor között a különböző dimenziókban. Például végezheti el a CPU-használat óránkénti átlaga a számítógépek között az alábbiak szerint:

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![átlagos időköz mérték](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

Alapértelmezés szerint ezekkel az eredményekkel fog megjelenni a diagram interaktív sor több adatsort.  Ez a diagram támogatja az adatsorozat való átváltással (az y tengelyen rescaling), nagyításához, és rámutató.  A tábla megjelenítési beállítás érhető el továbbra is a nyers adatok megtekintése, ha szükséges.

Más mezők szerint is csoportosíthatja. Ebben a példában I egy adott számítógép összes % számláló, figyelve megállapítható szeretnék tudni, hogy mi az az óránkénti 70 százalékos érték minden számláló:

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
Egy dolog, ami arról értesít, hogy ezeket a lekérdezéseket teljesítményszámlálók nem korlátozódnak. Is alkalmazhatóak legyenek, hogy bármely mérték. Ebben a példában I vagyok megnézi a W3C IIS-naplókba. Szeretnék tudni, hogy mi az a maximális időt egy 5 perces időszakban az egyes kérelmek feldolgozásához:

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### <a name="use-multiple-aggregates-in-one-query"></a>Egy lekérdezést több aggregátumok használata
Megadhat egy mérték parancs több összesített záradékot.  Minden egyes egymástól függetlenül lehet aliasnevet.  Ha nincs megadva alias az eredményül kapott mező neve lesz a összesítő függvényben, de a használt (azaz "avg(CounterValue)" avg(CounterValue)) számára.

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![OMS-multiaggregates1](./media/log-analytics-log-searches/oms-multiaggregates1.png)

Egy másik példa:

 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## <a name="next-steps"></a>Következő lépések
Napló keresések kapcsolatos további információkért lásd:

* Használjon [Naplóelemzési egyéni mezők](log-analytics-custom-fields.md) napló keresések kiterjeszteni.
* Tekintse át a [Naplóelemzési jelentkezzen keresési hivatkozás](log-analytics-search-reference.md) megtekintéséhez az összes keresési mezők és facets Naplóelemzési érhető el.
