---
title: Automatikusan méretezni a számítási csomópontok az Azure Batch-készletben |} A Microsoft Docs
description: Engedélyezze az automatikus skálázást egy felhőalapú készlet dinamikusan állítsa be a készletben lévő számítási csomópontok számát.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 06/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab41211fb0b0b6360bdbc255e367d0492c2438ed
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330886"
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Hozzon létre egy automatikus méretezési képlet egy Batch-készletben lévő számítási csomópontok méretezése

Az Azure Batch automatikusan méretezheti az Ön által meghatározott paraméterek alapján készletek. Automatikus méretezés, a Batch dinamikusan kerülnek be a csomópontok egy készlethez, a feladat igények növekedése, és eltávolítja a számítási csomópontok, mivel csökkentik. Időt és pénzt takarít mentheti a Batch-alkalmazás által használt számítási csomópontok automatikusan módosításával. 

Automatikus skálázáshoz a számítási csomópontok készletét társít hozzá egy *automatikus skálázási képletet* definiálásához. A Batch szolgáltatás az automatikus méretezési képlet segítségével a számítási feladat végrehajtásához szükséges számítási csomópontok számának meghatározásához. Számítási csomópontok lehet dedikált csomópontok vagy [alacsony prioritású csomópontok](batch-low-pri-vms.md). A Batch szolgáltatás rendszeres időközönként gyűjtött metrikák adatok válaszol. A metrikák adatait használja, a Batch állítja be a képlet alapján, és a egy konfigurálható időközönként a készletben lévő számítási csomópontok számát.

Engedélyezheti az automatikus méretezés, vagy egy készlet létrehozásakor, vagy egy meglévő készlet. Egy készlet automatikus skálázáshoz konfigurált meglévő képlet is módosíthatja. Batch lehetővé teszi, hogy a képletek kiértékelése előtt őket hozzárendelni a készletek és az automatikus skálázási futtatások állapotának figyelése.

Ez a cikk ismerteti a különböző entitások, amelyek az automatikus skálázási képletet, többek között a változókat, operátorok, műveleteket és funkciók alkotják. Bemutatjuk, hogyan lehet, a különböző számítási erőforrás és a feladat metrikáinak beszerzése Batch belül. Ezek a metrikák használatával állítsa be a készletben a csomópontok száma alapján az erőforrás-használat és a feladat állapota. Azt majd ismertetjük, hogyan hozhat létre egy képletet, és engedélyezze az automatikus skálázást egy készletet a Batch REST és .NET API-k segítségével. Végül azt végül néhány példa képletekkel.

> [!IMPORTANT]
> Batch-fiók létrehozásakor megadhatja a [fiókkonfiguráció](batch-api-basics.md#account), amely meghatározza, hogy a készletek lefoglalásának, a Batch szolgáltatás előfizetésében (alapértelmezett), illetve a felhasználói előfizetés. Ha a Batch-fiók alapértelmezett Batch szolgáltatás konfigurációjával létrehozott, a fiók a feldolgozáshoz használt magok maximális száma korlátozva. A Batch szolgáltatás méretezi a számítási csomópontok csak adott core korlátig. Ebből kifolyólag a Batch szolgáltatás nem jut el az automatikus skálázás képletét által meghatározott számítási csomópontok kitűzött számát. Lásd: [kvóták és korlátozások az Azure Batch szolgáltatás](batch-quota-limit.md) megtekintése és a fiókra vonatkozó kvóták növelésével kapcsolatos információkat.
>
>Ha létrehozta a fiókot a felhasználói előfizetés konfigurációval, a fiók az előfizetés magkvótáján fájlmegosztások. További információkért lásd [az Azure-előfizetésekre és -szolgáltatásokra vonatkozó korlátozásokat, kvótákat és megkötéseket](../azure-subscription-service-limits.md) ismertető témakör [a virtuális gépek korlátaira](../azure-subscription-service-limits.md#virtual-machines-limits) vonatkozó részét.
>
>

## <a name="automatic-scaling-formulas"></a>Az automatikus méretezési képletek
Az automatikus méretezési képlet egy karakterláncérték, amely azt határozza meg, amely tartalmaz egy vagy több utasítás. Az automatikus méretezési képlet hozzá van rendelve egy készlet [autoScaleFormula] [ rest_autoscaleformula] elem (Batch REST) vagy [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] tulajdonság (Batch .NET). A Batch szolgáltatás határozza meg a készletben lévő számítási csomópontok célszámát a következő esedékes feldolgozása a képletet használja. A képlet karakterlánc nem haladhatja meg a 8 KB-os, pontosvesszővel elválasztva, és tartalmazhat sortörést és megjegyzéseket legfeljebb 100 utasításokat is tartalmazhat.

Felfogható automatikus méretezési képletek a Batch automatikus skálázási "nyelv". Képlet kimutatásai szabad formátumú kifejezések, amelyek a szolgáltatás által definiált változókat (a Batch szolgáltatás által definiált változókat) és a felhasználó által definiált változókat (Ön által meghatározott változókat) is tartalmazhatnak. Beépített típusok, operátorokból és függvényekből segítségével végrehajtható különféle műveletekhez ezeket az értékeket. Például egy utasítást is igénybe vehet a következő formátumot követi:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Képletek általában tartalmaznak, amelyek műveleteket végeznek, melyek értékeket az előző utasítások több utasítás. Például először célpontot értéket `variable1`, majd adja át azt egy függvényt, amely feltölti `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Ezek az utasítások tartalmazzák ahhoz, hogy eljusson a számítási csomópontok célszámát egy automatikus skálázási képletet. Dedikált csomópontok és alacsony prioritású csomópontok is saját célbeállítások, így meghatározhatja, hogy minden egyes csomópont típusa céljának. Az automatikus skálázás képletét a célérték dedikált csomópontok, alacsony prioritású csomópontok célértéket vagy mindkettő tartalmazhat.

Csomópontok célszáma lehet nagyobb, kisebb vagy megegyezik az adott típusú a készletben lévő csomópontok aktuális száma. A Batch adott időközönként egy készlet automatikus skálázási képletet értékeli ki (lásd: [automatikus skálázási időközök](#automatic-scaling-interval)). Batch módosítja a csomópont a készletben, a szám, az automatikus skálázási képletet adja meg a kiértékelés idején különböző típusú kitűzött számát.

### <a name="sample-autoscale-formula"></a>Automatikus skálázási képletet minta

Íme egy példa az automatikus skálázás képletét, amely lehet beállítani a legtöbb esetben működik. A változók `startingNumberOfVMs` és `maxNumberofVMs` a példában a képlet az igényeinek megfelelően módosítható. Ez a képlet méretezi a dedikált csomópontok, de a alkalmazni a méretezési csoport alacsony prioritású csomópontok is módosíthatók. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

A következő automatikus skálázási képletet a készlet először hozza létre egyetlen virtuális gépen. A `$PendingTasks` metrika megadja a futó vagy az aszinkron feladatok számát. A képlet megkeresi átlagos száma függőben lévő feladatok az elmúlt 180 másodperc és csoportok a `$TargetDedicatedNodes` változó ennek megfelelően. A képlet gondoskodik arról, hogy a dedikált csomópontok célszáma soha nem meghaladja-e a 25 virtuális gépeket. Új feladatok elküldése, ahogy a készlet automatikusan nő. A feladat befejeződött, mint virtuális gépek ingyenes egyenként legyen, és az automatikus skálázási képletet csökken a készlet.

## <a name="variables"></a>Változók
Egyszerre is használhatja **szolgáltatás által definiált** és **felhasználó által definiált** az automatikus skálázási képletet a változókat. A szolgáltatás által definiált változókat a Batch szolgáltatás beépített. Szolgáltatás által definiált változókat írható-olvasható, és csak olvasható. Felhasználói változók olyan változók, határoz meg. A példa az előző szakaszban látható képletben `$TargetDedicatedNodes` és `$PendingTasks` szolgáltatás által definiált változókat is. Változók `startingNumberOfVMs` és `maxNumberofVMs` felhasználói változók.

> [!NOTE]
> Szolgáltatás által definiált változókat mindig előzi meg egy dollárjelet ($). A felhasználó által definiált változókat dollárjel nem kötelező.
>
>

Az alábbi táblázatokban a Batch szolgáltatás által definiált írási-olvasási és az írásvédett változók.

Első és a egy készletben lévő számítási csomópontok számának kezelését szolgáltatás által definiált változók értékét:

| Olvasási és írási szolgáltatás által definiált változó | Leírás |
| --- | --- |
| $TargetDedicatedNodes |A célszáma dedikált számítási csomópontok a készlethez. Dedikált csomópontok száma cél van megadva, mert a készlet minden esetben nem érhetik el a kívánt számú csomópontot. Például ha az automatikus skálázás kiértékelési ezt a dedikált csomópontok célszáma módosítanak, mielőtt a készlet elérte a kezdeti cél, majd a készlet nem jut el a cél. <br /><br /> Egy készletet a Batch szolgáltatás konfigurációjával létrehozott fiókban nem érhetik el a célértéket, ha a cél meghaladja a Batch-fiók csomópont- vagy core kvóták. Felhasználói előfizetés-konfigurációval létrehozott fiók, a készlet nem érhetik el a célértéket, ha a célként megadott előfizetés magkvótáján megosztott meghaladja.|
| $TargetLowPriorityNodes |A kitűzött számát alacsony prioritású számítási csomópontok a készlethez. Alacsony prioritású csomópontok száma cél van megadva, mert a készlet minden esetben nem érhetik el a kívánt számú csomópontot. Például ha alacsony prioritású csomópontok célszáma van módosította az automatikus skálázás kiértékelési, mielőtt a készlet elérte a kezdeti cél, majd a készlet lehet, hogy nem érhető el a cél. Egy készlet is nem érhetik el a célértéket, ha a cél meghaladja a Batch-fiók csomópont- vagy core kvóták. <br /><br /> Alacsony prioritású számítási csomópontok további információkért lásd: [alacsony prioritású virtuális gépek használata a Batch (előzetes verzió)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |A művelet, amikor a számítási csomópontot a készletből törlődnek. Lehetséges értékek:<ul><li>**újbóli várólistázás**– leállítja a feladatok azonnal, és a replikaügynöknek vissza a feladat-várólistában lévő, így azok időpontra van ütemezve.<li>**leállítja**– leállítja a feladatok azonnal, és eltávolítja azokat a feladat-várólistában.<li>**taskcompletion**--vár a jelenleg futó feladat befejeződik, és majd eltávolítja a csomópontot a készletből.<li>**retaineddata**– minden helyi feladat megőrzött adat megvárja, amíg a csomóponton, mielőtt megpróbálja eltávolítani a csomópontot a készletből törlődnek.</ul> |

Ezeket a módosításokat a Batch szolgáltatás mérőszámainak alapuló szolgáltatás által definiált változókat értékét kaphat:

| Csak olvasható szolgáltatás által definiált változó | Leírás |
| --- | --- |
| $CPUPercent |Átlagos CPU-használat százalékos. |
| $WallClockSeconds |A felhasznált másodpercek számát. |
| $MemoryBytes |A hely MB-ban használt átlagos száma. |
| $DiskBytes |A helyi lemezeken használt gigabájt átlagos száma. |
| $DiskReadBytes |A beolvasott bájtok száma. |
| $DiskWriteBytes |Írt bájtok száma. |
| $DiskReadOps |Lemezolvasási végrehajtott műveletek száma. |
| $DiskWriteOps |Végrehajtott írási lemezes műveletek száma. |
| $NetworkInBytes |Bejövő bájtok száma. |
| $NetworkOutBytes |Kimenő bájtok száma. |
| $SampleNodeCount |Számítási csomópontok száma. |
| $ActiveTasks |Feladatok végrehajtására kész, de még nem executing száma. $ActiveTasks száma tartalmazza az összes feladatot, amely az aktív állapotban vannak, és amelynek függőségek teljesülnek. Minden feladatot, amely az aktív állapotban vannak, de amelynek függőségek nem teljesülnek a $ActiveTasks száma nem tartoznak.|
| $RunningTasks |Futó állapotú feladatok száma. |
| $PendingTasks |$ActiveTasks és $RunningTasks összege. |
| $SucceededTasks |Sikeresen befejeződött feladatok száma. |
| $FailedTasks |A sikertelen feladatok száma. |
| $CurrentDedicatedNodes |Az aktuális száma dedikált számítási csomópontok. |
| $CurrentLowPriorityNodes |Az aktuális száma alacsony prioritású számítási csomópontok, többek között olyan csomópontokat, a felfüggesztett lett. |
| $PreemptedNodeCount | A felfüggesztett állapotban van, a készletben lévő csomópontok száma. |

> [!TIP]
> A csak olvasható, a szolgáltatás által definiált előző táblázatban látható értékek *objektumok* , adja meg a különböző módszereket az egyes társított adatok eléréséhez. További információkért lásd: [szerezze be a mintaadatokat](#getsampledata) a cikk későbbi részében.
>
>

## <a name="types"></a>Típusok
Ezek a típusok támogatottak a képletet:

* double
* doubleVec
* doubleVecList
* sztring
* időbélyeg – időbélyeg: egy összetett struktúra, amely tartalmazza a következő tagok:

  * év
  * (1-12) hónapban
  * nap (1-31)
  * hét napja (szám; formátumban például hétfő 1)
  * óra (24 órás számformátumú; például 13 azt jelenti, hogy 1 óra)
  * perc (00-59)
  * a másodpercenként (00-59)
* TimeInterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Műveletek
Ezek a műveletek az előző szakaszban felsorolt típusok engedélyezettek.

| Művelet | Támogatott operátorok | Eredmény típusa |
| --- | --- | --- |
| dupla *operátor* dupla |+, -, *, / |double |
| dupla *operátor* timeinterval |* |TimeInterval |
| doubleVec *operátor* dupla |+, -, *, / |doubleVec |
| doubleVec *operátor* doubleVec |+, -, *, / |doubleVec |
| TimeInterval *operátor* dupla |*, / |TimeInterval |
| TimeInterval *operátor* timeinterval |+, - |TimeInterval |
| TimeInterval *operátor* időbélyeg |+ |időbélyeg |
| időbélyeg *operátor* timeinterval |+ |időbélyeg |
| időbélyeg *operátor* időbélyeg |- |TimeInterval |
| *operátor*dupla |-, ! |double |
| *operátor*timeinterval |- |TimeInterval |
| dupla *operátor* dupla |<, <=, ==, >=, >, != |double |
| karakterlánc *operátor* karakterlánc |<, <=, ==, >=, >, != |double |
| időbélyeg *operátor* időbélyeg |<, <=, ==, >=, >, != |double |
| TimeInterval *operátor* timeinterval |<, <=, ==, >=, >, != |double |
| dupla *operátor* dupla |&&, &#124;&#124; |double |

Egy dupla Ternáris operátor tesztelésekor (`double ? statement1 : statement2`), nem nulla van **igaz**, és nulla érték **hamis**.

## <a name="functions"></a>Functions
Ezek előre meghatározott **funkciók** elérhető definiálása az automatikus skálázási képletet használhat.

| Függvény | Visszatérési típusa | Leírás |
| --- | --- | --- |
| avg(doubleVecList) |double |A doubleVecList átlagértékét összes értékét adja vissza. |
| len(doubleVecList) |double |A vektor, amely jön létre a doubleVecList hosszát adja vissza. |
| LG(Double) |double |A napló alap 2. a kettős adja vissza. |
| LG(doubleVecList) |doubleVec |A component-wise napló alap 2. a doubleVecList adja vissza. Egy vec(double) explicit módon kell átadni a paraméterhez. Ellenkező esetben a dupla lg(double) verziója veszi alapul. |
| ln(Double) |double |A kettős, természetes logaritmusát adja vissza. |
| ln(doubleVecList) |doubleVec |A component-wise napló alap 2. a doubleVecList adja vissza. Egy vec(double) explicit módon kell átadni a paraméterhez. Ellenkező esetben a dupla lg(double) verziója veszi alapul. |
| log(Double) |double |A napló a kettős 10 alapszintű adja vissza. |
| log(doubleVecList) |doubleVec |A component-wise napló adja vissza a doubleVecList alap 10. Egy vec(double) egyetlen dupla paraméter explicit módon kell átadni. Ellenkező esetben a dupla log(double) verziója veszi alapul. |
| max(doubleVecList) |double |A doubleVecList a legnagyobb értéket adja vissza. |
| Min(doubleVecList) |double |A doubleVecList a legkisebb értéket adja vissza. |
| norm(doubleVecList) |double |A vektor, amely a doubleVecList jön létre a két inkább normát adja vissza. |
| PERCENTILIS (doubleVec v, dupla p) |double |A vektor v PERCENTILIS elemét adja vissza. |
| rand() |double |0,0 és 1,0 közötti véletlenszerű értéket ad vissza. |
| range(doubleVecList) |double |A minimális és maximális értékek közötti különbség a doubleVecList adja vissza. |
| std(doubleVecList) |double |A doubleVecList értékek minta szórásának visszaadása. |
| Stop() | |Leállítja az automatikus méretezés kifejezés kiértékelése. |
| Sum(doubleVecList) |double |A doubleVecList összetevőit összegét adja vissza. |
| idő (dátum/idő karakterlánc = "") |időbélyeg |Ha átadva adja vissza az aktuális idő, ha nincsenek átadott paraméterek időbélyegzőjét, vagy a dátum/idő karakterlánc időbélyegzőjét. Támogatott dátum és idő formátumok a következők: W3C-DTF és RFC 1123. |
| érték (doubleVec v, dupla i) |double |Az elem helyén i vektor v, a nulla kiindulási indexszel rendelkező értékét adja vissza. |

A függvények az előző táblázatban ismertetett elfogadhatja a lista argumentumként. A vesszővel tagolt lista tetszőleges kombinációját *dupla* és *doubleVec*. Példa:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

A *doubleVecList* érték egyetlen alakítja át *doubleVec* kiértékelése előtt. Például ha `v = [1,2,3]`, majd hívása `avg(v)` hívása egyenértékű `avg(1,2,3)`. Hívó `avg(v, 7)` hívása egyenértékű `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Szerezze be a mintaadatokat
Automatikus skálázási képletet a metrikák adatait (minták), a Batch szolgáltatás által biztosított cselekedhet. A képlet növekszik vagy csökken a készlet méretét, amely azt a szolgáltatás lekéri az értékek alapján. A szolgáltatás által definiált változókat leírt korábban olyan objektumok, adja meg a különböző módszerek az adott objektumhoz tartozó adatok eléréséhez. A következő kifejezést például CPU-használat az elmúlt öt percben beolvasására irányuló kérelem látható:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Módszer | Leírás |
| --- | --- |
| GetSample() |A `GetSample()` metódus visszaadja a vektoros adatok minták.<br/><br/>Egy minta mérőszámadatokat érdemes 30 másodpercen. Más szóval minták felhőszolgáltatásából, ez lehet 30 másodperc. Azonban az alábbi esetekben, amikor egy minta gyűjtött és elérhető képlet késleltetés van. Mint ilyen egy adott időszakban nem az összes minta értékelés képlet alapján elérhető lehet.<ul><li>`doubleVec GetSample(double count)`<br/>Adja meg a legutóbbi minták összegyűjtött lekérését minták számát.<br/><br/>`GetSample(1)` a legutóbbi elérhető minta adja vissza. Metrikák hasonló `$CPUPercent`, azonban ez nem használható, mert nem lehet tudni, hogy *amikor* gyűjtötte a program a mintát. Elképzelhető, hogy a legutóbbi, vagy rendszerproblémák, miatt jóval régebbi lehet. Érdemes használni az adott idő alatt, ahogy az alábbi ezekben az esetekben.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Adja meg a minta az adatgyűjtés időkeretet. Másik lehetőségként azt is, százalékos aránya a minták azt érhető el a kért időkereten belül kell lennie.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` 20 minták adna vissza, ha az összes minta az utolsó 10 percet a CPUPercent előzmények szerepelnek. Ha az elmúlt percben előzmények nem volt elérhető, azonban csak 18 minták kellene visszaadnia. Ebben az esetben:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` mert érhetők el a minták csak 90 százalékát sikertelen lesz.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` járnak.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Megadja az adatgyűjtés, a kezdési időt és a záró időpontot az időkeretet.<br/><br/>Mint már említettük, van egy minta gyűjtött mikor, és elérhető képlet késleltetés. Fontolja meg ezt a késést, használatakor a `GetSample` metódust. Lásd: `GetSamplePercent` alatt. |
| GetSamplePeriod() |Egy korábbi minta adatkészlet a időtartama minták végrehajtott adja vissza. |
| Count() |A metrika előzmények minták teljes számát adja vissza. |
| HistoryBeginTime() |A metrika a legrégebbi rendelkezésre álló adatok minta időbélyegzőjét adja vissza. |
| GetSamplePercent() |Egy adott időtartam alatt rendelkezésre álló minták aránya adja vissza. Példa:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Mivel a `GetSample` metódus sikertelen lesz, ha a visszaadott minták aránya kisebb, mint a `samplePercent` megadott, használhatja a `GetSamplePercent` először ellenőrzési módszert. Majd egy másik művelet hajtható végre, ha elegendő minták jelen, az automatikus skálázási értékelés leállítása nélkül. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Minták, mintául szolgáló százalékos aránya, és a *GetSample()* metódus
Az automatikus skálázás képletét alapvető működéséhez, hogy szerezze be a tevékenység- és metrikaadatokat, majd módosítsa a készlet mérete az adatok alapján. Ezért fontos egyértelművé, hogyan kommunikáljanak az automatikus skálázási képletet mérőszámadatokat (minták).

**Minták**

A Batch szolgáltatás rendszeres időközönként tevékenység- és erőforrás-metrikák mintát vesz igénybe, és elérhetővé teszi azokat az automatikus méretezési képletek. Ezek a minták a Batch szolgáltatás 30 másodpercenként rögzíti. Van azonban általában a késleltetés között, ha ezek a minták rögzítve, és mikor érhetők el (és által olvasható) az automatikus skálázási képletet. Ezenkívül számos tényezőtől, például hálózati vagy egyéb infrastrukturális problémára, a minták előfordulhat, hogy nem rögzíti az egy adott időszakban.

**Minta százalékos aránya**

Amikor `samplePercent` átadott a `GetSample()` metódus vagy a `GetSamplePercent()` metódust meghívják, és _%-os_ minták, a Batch szolgáltatás által rögzített lehetséges száma és száma összehasonlítását hivatkozik a minták az automatikus skálázási képletet elérhető.

Lássunk erre egy 10 perces időtartam példaként. Minták belül egy 10 perces időtartam 30 másodpercenként vannak rögzítve, mert a Batch által rögzített minták maximális száma 20 minták (2 / perc) lenne. Előfordulhat azonban, a jelentéskészítési mechanizmus és más Azure-on belüli problémák eredendő késést, mert nincs csak 15 minták az automatikus skálázási képletet az olvasáshoz elérhető. Így például az adott 10 perces időszak 75 %-a rögzített minták száma összesen elérhető lehet a képletet.

**GetSample() és minta tartományok**

Az automatikus skálázási képletet fog növekvő és a készletekben zsugorítását &mdash; csomópontok hozzáadásával vagy eltávolításával a csomópontokat. Csomópontok költséget takaríthat meg költséget, mert szeretne biztosítani, hogy a képletek módszerhez egy intelligens elemzési megfelelő adatok alapján. Ezért javasoljuk, hogy a képletekben egy népszerű típusú elemző használjon. Ez a típus növekszik és csökken a készletek, a széles körű összegyűjtött minták alapján.

Ehhez használja `GetSample(interval look-back start, interval look-back end)` minták vektor vissza:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Ha a fenti sort kiértékelése Batch, értékek vektor visszaadja minták számos. Példa:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

A minták vektor adatgyűjtés után használhatja például a functions `min()`, `max()`, és `avg()` jelentéssel bíró értékek származtassa. az összegyűjtött tartomány.

A fokozott biztonság érdekében kényszerítheti a kiértékelési a meghiúsuljon, ha kisebb, mint egy bizonyos minta százalék érhető el egy adott időszakban. Ha kényszeríti a kiértékelési sikertelen lesz, további a képlet kiértékelése megszűnik, ha nem érhető el a megadott százalék minták a Batch kérje meg. Ebben az esetben nem módosítja a készlet méretét. Az értékelés sikeres minták szükséges százalékos, adja meg azt, harmadik paraméterként `GetSample()`. Itt 75 %-os minták követelmény van megadva:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Minta rendelkezésre állási késleltetés lehet, mert fontos mindig adjon meg egy időtartományt a régebbi, mint egy perc meg visszaírt kezdési időpontot. IT minták propagálásához a rendszer körülbelül egy percig tart, így a tartomány minták `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` nem érhető el. A százalékos paraméterében használja ismét `GetSample()` kényszerítése egy adott minta százalékos követelmény.

> [!IMPORTANT]
> Hogy **erősen ajánlott** , amikor **elkerülése érdekében a megbízható függő entitásokat *csak* a `GetSample(1)` az automatikus méretezési képletek**. Ennek az az oka `GetSample(1)` lényegében szerint a Batch szolgáltatásban, a "Velem a legutóbbi minta, adjon van, függetlenül attól, hogy mennyivel lekért." Mivel ez csak egyetlen mintát, és elképzelhető, hogy egy korábbi minta, nem lehet képviselője a közelmúltbeli tevékenység vagy erőforrás állapotának nagyobb méretű kép. Ha használja a `GetSample(1)`, győződjön meg arról, hogy része egy nagyobb utasítás és nem az egyetlen adatpont, amely a képlet támaszkodik.
>
>

## <a name="metrics"></a>Mérőszámok
Egy képlet definiálásakor erőforrás és a feladat mérőszámok használatát. A készlet, amelyet beszerzése és kiértékelheti a mérőszámadatokat alapján dedikált csomópontok célszáma módosíthatja. Tekintse meg a [változók](#variables) szakasz fölött mindegyik metrikát további tájékoztatást.

<table>
  <tr>
    <th>Metrika</th>
    <th>Leírás</th>
  </tr>
  <tr>
    <td><b>Erőforrás</b></td>
    <td><p>Erőforrás-mérőszámok a Processzor, a sávszélesség, a memóriahasználat, a számítási csomópontok és a csomópontok számát alapulnak.</p>
        <p> A szolgáltatás által definiált változókat a csomópontok száma alapján, a korrekciók hasznosak lehetnek:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>A szolgáltatás által definiált változókat a csomóponton erőforrás-használat alapján, a korrekciók hasznosak lehetnek:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tevékenység</b></td>
    <td><p>Tevékenység-mérőszámok, függőben lévő aktív, például a feladatok állapotát alapulnak, és befejeződött. A következő szolgáltatás által definiált változókat a feladat-metrikák alapján készletméret korrekciók hasznosak lehetnek:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Az automatikus skálázás képletét írása
Az automatikus skálázás képletét készítését képező utasítások, amelyek a fenti összetevők, majd kombinálhatja az utasításokat a teljes képletbe. Ebben a szakaszban az például automatikus skálázás képletét által elvégezhető egy való életből vett méretezési döntéseket hozunk létre.

Első lépésként határozzon meg az új automatikus skálázási képletet követelményeinek. A képlet a következőket kell tennie:

1. Növelje a készlet dedikált számítási csomópontok célszáma, ha a CPU-használata túl magas.
2. Dedikált számítási csomópontok célszáma csökkentéséhez, amikor a CPU-használat alacsony.
3. Mindig korlátozzák a dedikált csomópontok maximális számát 400-as.

Csomópontok számának növelése magas CPU-használat során, az utasítás, amely feltölti egy felhasználó által definiált változó megadása (`$totalDedicatedNodes`) az értéke 110 százaléka az aktuális száma a dedikált csomópontok, de csak akkor, ha a minimális átlagos CPU-használat során a elmúlt 10 percben meghaladta a 70 %-os. Ellenkező esetben használja az értéket a dedikált csomópontok jelenlegi száma.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

A *csökkentése* dedikált csomópontok alacsony CPU-használat, a következő utasítás képletekben hivatkozhatunk során beállítja azonos `$totalDedicatedNodes` változó – 90 százalékára az aktuális dedikált csomópontok célszáma, ha az átlagos CPU-használat az elmúlt 60 perc a 20 %-os volt. Ellenkező esetben használja az aktuális értékét `$totalDedicatedNodes` , hogy kitölti az utasításban.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Most már korlátja 400 maximális dedikált számítási csomópontok célszáma:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Itt látható a teljes képlet:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Az automatikus méretezés felkészített készlet létrehozása a .NET használatával

Készlet létrehozása és az automatikus skálázás engedélyezve van a .NET-ben, kövesse az alábbi lépéseket:

1. Az a készlet létrehozásához [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Állítsa be a [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) tulajdonságot `true`.
3. Állítsa be a [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) tulajdonságot, amelyben az automatikus skálázási képletet.
4. (Nem kötelező) Állítsa be a [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) tulajdonság (az alapértelmezett érték 15 perc).
5. Véglegesítse a készlet [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) vagy [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Az alábbi kódrészlet egy automatikus méretezés felkészített készletet hoz létre a .NET-ben. A készlet automatikus skálázási képletet hétfőn 5 dedikált csomópontok célszáma, és minden más a hét napjának 1 állítja be. A [automatikus méretezési időközhöz](#automatic-scaling-interval) 30 percre van beállítva. Ebben és a többi C# kódrészletek ebben a cikkben `myBatchClient` megfelelően inicializálva példánya a [BatchClient] [ net_batchclient] osztály.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Az automatikus méretezés felkészített készletet hoz létre, ha nem adja meg a _targetDedicatedComputeNodes_ paraméter vagy a _targetLowPriorityComputeNodes_ hívása paraméterrel **CreatePool** . Ehelyett adja meg a **AutoScaleEnabled** és **AutoScaleFormula** a készlet tulajdonságait. Ezek a tulajdonságok értékeit a száma minden egyes csomópont típusa határozza meg. Is, a manuális átméretezése az automatikus méretezés engedélyezve van a készlet (például [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), első **letiltása** az automatikus méretezés a tárolókészlet, majd méretezze át.
>
>

Mellett a Batch .NET-es, használhatja a másik [Batch SDK-k](batch-apis-tools.md#azure-accounts-for-batch-development), [Batch REST](https://docs.microsoft.com/rest/api/batchservice/), [Batch PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md), és a [Batch CLI](batch-cli-get-started.md) , automatikus skálázás konfigurálása.


### <a name="automatic-scaling-interval"></a>Az automatikus méretezési időközhöz.
Alapértelmezés szerint a Batch szolgáltatás 15 percenként állítja be a készlet méretének megfelelően az automatikus skálázási képletet. Ez az időtartam alatt a következő alkalmazáskészlet-tulajdonságok használatával konfigurálható:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (REST API)

A minimális időköz öt perc alatt, és a maximális 168 óra. Ha egy intervallumot, ezen a tartományon kívül van megadva, a Batch szolgáltatás egy hibás kérés (400) hibával tér vissza.

> [!NOTE]
> Az automatikus skálázás nem célja jelenleg a reagálni az igények változásaira kevesebb mint egy perc alatt, de nem célja, hogy a készlet számítási feladatok futtatása másként fokozatosan méretének beállítása.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Egy meglévő készlet automatikus skálázás engedélyezése

Minden egyes Batch SDK biztosítja az automatikus skálázás engedélyezéséhez. Példa:

* [BatchClient.PoolOperations.EnableAutoScaleAsync] [ net_enableautoscaleasync] (Batch .NET)
* [Egy készlet automatikus skálázás engedélyezése] [ rest_enableautoscale] (REST API)

Ha egy meglévő készlet automatikus skálázás engedélyezéséhez vegye figyelembe a következőket:

* Ha az automatikus skálázás jelenleg le van tiltva a készlet automatikus skálázás engedélyezéséhez a kérelem elküldésekor, meg kell adnia egy érvényes automatikus skálázási képletet, a kérelem elküldésekor. Igény szerint megadhatja az automatikus skálázás kiértékelési időköze. Ha nem ad meg az időközt, az alapértelmezett érték 15 perc szolgál.
* Ha az automatikus méretezés engedélyezve van a készlet, megadhatja az automatikus skálázás képletét, egy kiértékelési időköze vagy mindkettő. Ezek a tulajdonságok egyikét meg kell adnia.

  * Ha megad egy új automatikus skálázás kiértékelési időköze, majd a meglévő értékelési ütemezés le van állítva, és új ütemezés szerint elindult. Az új ütemezés kezdési ideje az az idő, milyen automatikus skálázás engedélyezéséhez a kérelem ki lett adva.
  * Ha nincs megadva az automatikus méretezési képlet vagy a kiértékelési időköze, a a Batch szolgáltatás továbbra is az aktuális értéket használja.

> [!NOTE]
> Ha a megadott értékeket a *targetDedicatedComputeNodes* vagy *targetLowPriorityComputeNodes* paramétereit a **CreatePool** módszer a készlet létrehozásakor .NET-, vagy az összehasonlítható paraméterek egy másik nyelven, majd ezeket az értékeket nem veszi figyelembe az automatikus skálázási képletet értékeli ki.
>
>

Ez a C# kódrészletet használja a [Batch .NET] [ net_api] erőforrástár egy meglévő készlet automatikus skálázás engedélyezéséhez:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Az automatikus skálázás képletét frissítése

A képlet az automatikus méretezés engedélyezve van a létező készletekhez frissítéséhez hívja meg a műveletet az új képlet újra az automatikus skálázás engedélyezéséhez. Például, ha az automatikus skálázás már engedélyezve van a `myexistingpool` hajtja végre a következő .NET-kódot, ha az automatikus skálázási képletet tartalma helyére `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Frissítés az automatikus skálázás időköze

Meglévő készlet automatikus méretezés engedélyezve van az automatikus skálázás kiértékelési időköze frissítéséhez hívja meg a műveletet az új intervallum újra az automatikus skálázás engedélyezéséhez. Például, ha szeretné 60 perc alatt egy készletet, amely már a .NET-ben az automatikus méretezés engedélyezve az automatikus skálázás kiértékelési időköze:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Az automatikus skálázás képletét kiértékelése

A képlet értékelheti a készlethez alkalmazása előtt. Ezzel a módszerrel a képletet, tekintse meg, hogyan kimutatásait kiértékelése előtt a képlet üzembe éles környezetben tesztelheti.

Az automatikus skálázás képletét kiértékelése, először engedélyeznie kell egy érvényes képlettel a készlet automatikus skálázást. Egy készlet, amely még nem rendelkezik az automatikus skálázás engedélyezése mellett egy képlet teszteléséhez használja a egysoros képlet `$TargetDedicatedNodes = 0` amikor első alkalommal kapcsolja be az automatikus skálázás. Kiértékeli a képletet, tesztelni szeretné, ezt követően használja a következők egyikét:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) vagy [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    A Batch .NET módszerhez kiértékelheti, hogy egy meglévő készlet és az automatikus skálázási képletet tartalmazó karakterlánc azonosítója.

* [Az automatikus méretezési képlet kiértékelése](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    A REST API-kérés esetén adja meg a készlet azonosítója, az URI-ban, és az automatikus skálázási képletet a *autoScaleFormula* eleme a kérelem törzsében. A művelet a válasz lehet, hogy a képlet kapcsolatos hiba információit tartalmazza.

A jelen [Batch .NET] [ net_api] kódrészletet, az automatikus skálázás képletét kiértékeljük. Ha a készlet nem rendelkezik automatikus skálázás engedélyezése mellett, hogy aktiválták azt.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Ez a kódrészlet látható a képlet sikeres kiértékelés eredménye hasonló:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Automatikus skálázási futtatások adatainak lekérése

Győződjön meg arról, hogy a várt módon működik-e a képletet, azt javasoljuk, hogy rendszeres időközönként ellenőrizze a eredményét az automatikus skálázás fut, amely a Batch a készlet hajt végre. Tehát (vagy frissítse) egy hivatkozást a készlethez, és vizsgálja meg a legutóbbi automatikus skálázási Futtatás tulajdonságait.

A Batch .NET-ben a [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) tulajdonsággal rendelkezik, amelyek információkkal a legújabb automatikus skálázást elvégezni futtat a készlet több tulajdonságát:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

A REST API-ban a [készlet adatainak lekérése](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) kérelem a készletet, amely tartalmazza az információk futtatása a legújabb automatikus skálázást kapcsolatos információkat ad vissza a [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool#bk_autrun) tulajdonság.

Az alábbi C# kódrészletet használja a Batch .NET-kódtár az utolsó automatikus skálázási készlet futtassa kapcsolatos információkat _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Az előző kódrészletben mintakimenete:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>A példában az automatikus méretezés képletek
Tekintsünk meg néhány olyan képletek, amelyek a készletben működő számítási erőforrások nagyságának különböző módszereket mutatnak.

### <a name="example-1-time-based-adjustment"></a>1. példa: Időalapú beállítása
Tegyük fel, hogy átméretezi a készletet a hét napját és idejét alapján. Ez a példa bemutatja, hogyan növelheti vagy csökkentheti a készletben lévő csomópontok számát ennek megfelelően.

A képlet először lekéri az aktuális idő. Ha a weekday (1-5) és munkaidőn (8 6 18: 00) belül, a cél készletméret 20 csomóponttal van beállítva. Ellenkező esetben a 10 csomópont értéke azt.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>2. példa: A feladat-alapú beállítása
Ebben a példában a készlet méretét módosul az üzenetsorban szereplő feladatok száma alapján. Megjegyzések és a sortöréseket a képlet karakterláncok elfogadhatók.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>3. példa: Nyilvántartási párhuzamos feladatok
Ebben a példában a készlet méretét, a feladatok száma alapján állítja be. Ez a képlet is figyelembe veszi a [MaxTasksPerComputeNode] [ net_maxtasks] a készlethez beállított értéket. Ez a megközelítés olyan esetekben hasznos, ahol [párhuzamos feladat a végrehajtás](batch-parallel-node-tasks.md) engedélyezve van a készleten.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>4. példa: Egy kezdeti készlet méretének beállítása
Ez a példa bemutatja egy C# kódrészlet az egy automatikus skálázási képletet, amely beállítja a készlet méretét a megadott számú csomópontra egy kezdeti adott időszakban. Ezután azt állítja be a készlet méretét futó számát és az aktív feladatok a kezdeti időszak eltelte után.

A képlet megjelenik a következő kódrészletet:

* A készlet kezdeti mérete négy csomóponton állítja be.
* A készlet életciklus az első 10 percen belül nem igazítja a készlet méretét.
* 10 perc múlva kap számának maximális értékét az elmúlt 60 percen belül futó és az aktív feladatok.
  * Ha mindkét értékek 0 (Ez azt jelzi, hogy a feladat nem volt futó vagy aktív az elmúlt 60 perc), a készlet méretét értéke 0.
  * Ha bármelyik érték csak nullánál nagyobb, nem történik változás.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>További lépések
* [Maximalizálhatja az Azure Batch számítási erőforrás-használat a párhuzamosan futó csomóponti feladatok](batch-parallel-node-tasks.md) részletezi, hogyan hajthat végre több feladat egyidejű a készlet számítási csomópontjain. Automatikus skálázás mellett ez a funkció segíthet csökkentheti a számítási feladatok esetén, pénzt feladat időtartama.
* Egy másik hatékonyság gyorsító győződjön meg arról, hogy a Batch-alkalmazás a Batch szolgáltatás lekérdezi a legoptimálisabb módja. Lásd: [az Azure Batch szolgáltatás hatékony lekérdezése](batch-efficient-list-queries.md) hogyan továbbítva, ha több ezer számítási csomópontot vagy a feladatok állapotának lekérdezése adatok mennyisége korlátozza.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
