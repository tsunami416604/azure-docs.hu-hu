---
title: Automatikus méretezési számítási csomópontok az Azure Batch-készlet |} Microsoft Docs
description: A felhő készlet dinamikusan úgy, hogy a készlet számítási csomópontok száma automatikus skálázás engedélyezése.
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
ms.openlocfilehash: 1114ea90ae6976a3bc3580ebae5fd853de0274a1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Hozzon létre egy méretezhetővé válik a Batch-készlet számítási csomópontjainak automatikus méretezési képlet

Az Azure Batch automatikusan át tudja méretezni készletek meghatározott paraméterei alapján. Az automatikus skálázás kötegelt csomópontok dinamikusan hozzáadja egy feladat igények növekedése erőforráskészletben, és eltávolítja a számítási csomópontok, mivel csökkentik. Az időt és pénzt takaríthat automatikusan beállítja a kötegelt alkalmazás által használt csomópontok száma. 

Automatikus skálázás a számítási csomópontok készletét társítja azt engedélyezi egy *automatikus skálázás képlet* megadhat. A Batch szolgáltatás az automatikus skálázás képlet használatával határozhatja meg a számítási feladatok végrehajtásához szükséges számítási csomópontok számát. Számítási csomópontok lehet, hogy dedikált csomópontok vagy [alacsony prioritású csomópontok](batch-low-pri-vms.md). Kötegelt válaszol-e a szolgáltatás metrikák összegyűjtött adatokon rendszeres időközönként. A metrikák adatok használatával, kötegelt módosítja a képlet alapján és konfigurálható időközönként a készlet számítási csomópontjainak számát.

A tárolókészlet létrehozásakor vagy meglévő címkészlet automatikus skálázás engedélyezéséhez. Az automatikus skálázás beállítása készletben meglévő képlet is módosíthatja. Kötegelt lehetővé teszi a képletekben kiértékelése előtt rendel készletek és automatikus méretezési futtatása állapotának figyelésére.

A cikk ismerteti a különböző entitások, amelyek az automatikus skálázás képletek, beleértve a változók, operátorok, műveletek és funkciók alkotják. Különböző számítási erőforrás és a feladat metrikák belül kötegelt beszerzése arról lesz szó. A metrikák segítségével állítsa be a készlet csomópontok száma erőforrás-használat és a feladat állapota alapján. A Microsoft majd azt ismertetik, hogyan képlet és automatikus skálázás engedélyezése a készlet a Batch REST és a .NET API-k használatával. Végül azt befejezéséhez néhány példa képletek.

> [!IMPORTANT]
> Batch-fiók létrehozásakor megadhatja a [fiókkonfiguráció](batch-api-basics.md#account), amely meghatározza, hogy készletek foglal le a Batch szolgáltatás az előfizetéshez (alapértelmezett), vagy a felhasználó az előfizetéshez. A Batch-fiók a kötegelt szolgáltatáskonfiguráció hozta létre, ha a fiók korlátozva magok használható a feldolgozás maximális számát. A Batch szolgáltatás méretezi a számítási csomópontok csak alapvető korlátnak. Emiatt a Batch szolgáltatás nem jut el az automatikus skálázás képlet által meghatározott számítási csomópontok cél száma. Lásd: [kvótái és korlátai az Azure Batch szolgáltatás](batch-quota-limit.md) információk megtekintéséhez és a fiók kvótákat növelését.
>
>Ha a felhasználó előfizetéshez konfigurációjával létrehozta a fiókot, a fiók az előfizetés magkvótája megosztja. További információkért lásd [az Azure-előfizetésekre és -szolgáltatásokra vonatkozó korlátozásokat, kvótákat és megkötéseket](../azure-subscription-service-limits.md) ismertető témakör [a virtuális gépek korlátaira](../azure-subscription-service-limits.md#virtual-machines-limits) vonatkozó részét.
>
>

## <a name="automatic-scaling-formulas"></a>Automatikus méretezési képletek
Az automatikus méretezési képlete karakterlánc-értéke megadhat egy vagy több utasítást tartalmaz. Az automatikus skálázás képlet hozzá van rendelve egy alkalmazáskészlet [autoScaleFormula] [ rest_autoscaleformula] elem (Batch REST) vagy [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] a tulajdonság (Batch .NET). A Batch szolgáltatás a képlet használatával a következő időszakú feldolgozási a készlet számítási csomópontjai cél számának meghatározásához. A képlet karakterlánc nem haladhatja meg a 8 KB-os, pontosvesszővel elválasztva, amely tartalmazhat sortörést és megjegyzéseket legfeljebb 100 utasításokat tartalmazhatnak.

Az eltolásokat tekintheti automatikus méretezési képletek, egy kötegelt automatikus skálázás "nyelv". Képletadat kimutatásai, szabad formátumú kifejezések szolgáltatás által definiált változókat (a változók határozzák meg a Batch szolgáltatás) és a felhasználói változók (Ön által meghatározott változók) is tartalmazhatja. Azok a feladatra, beépített típusok, a kezelők és a funkciók használatával, ezeket az értékeket különféle műveleteket végezhet. Például egy utasítás is igénybe vehet a következő formában:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Képletek általában több utasítás műveleteket értékek, amelyek akkor kapja meg az előző utasítások tartalmaznak. Például azt szereznie értéket `variable1`, majd átadása egy függvény adatokkal való feltöltéséhez `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Vegye fel a számítási csomópontok cél számos elérésének automatikus skálázás képlet ezekről az utasításokról. Dedikált csomópontok és alacsony prioritású csomópont is saját tárolóbeállítások, így megadhatja az egyes csomópont target. Az automatikus skálázás képlet tartalmazhat egy célértéke dedikált csomópontok, alacsony prioritású csomópont target értéket vagy mindkettőt.

Lehet, hogy a tároló csomópontok száma nagyobb, alsó, illetve ugyanaz, mint a csomópontok a készlet típusú aktuális száma. Kötegelt a készlet automatikus skálázás képlet kiértékelése adott időközönként (lásd: [automatikus skálázás intervallumok](#automatic-scaling-interval)). Kötegelt beállítása minden típusú csomópont szám, amely az automatikus skálázás képlet megadja a kiértékelési idején a tárolókészlet cél száma.

### <a name="sample-autoscale-formula"></a>Minta automatikus skálázás képlet

Íme egy példa az automatikus skálázás úgy, hogy működik a legtöbb esetben lehet beállítani. A változók `startingNumberOfVMs` és `maxNumberofVMs` példában képlet módosítható az igényeinek megfelelően. A képlet dedikált csomópontok méretezi, de méretezési alacsony prioritású, valamint csomópontra érvényes kell módosítani. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Az automatikus skálázás képlettel a készlet először hozza létre egyetlen virtuális gépen. A `$PendingTasks` metrika határozza meg, amelyek fut, vagy az aszinkron feladatok száma. A képlet talál átlagos száma függőben lévő feladatok az elmúlt 180 másodperc és beállítása a `$TargetDedicatedNodes` változó ennek megfelelően. A képlet biztosítja, hogy a cél dedikált csomópontok száma soha nem meghaladja 25 virtuális gépek. Új feladatok be, mivel a készlet automatikusan nő. A feladat befejeződött virtuális gépek szabad egyenként válnak, valamint az automatikus skálázás képlet zsugorítja a készlet.

## <a name="variables"></a>Változók
Mindkét használhatja **szolgáltatás által definiált** és **felhasználói** az automatikus skálázás képletekben változók. A szolgáltatás által definiált változókat a Batch szolgáltatás beépített. Néhány szolgáltatás által definiált változókat írható-olvasható, és csak olvasható. Felhasználói változók az alábbiak változókat, amelyek adhat meg. A példa az előző részben található képletben `$TargetDedicatedNodes` és `$PendingTasks` szolgáltatás által definiált változók. Változók `startingNumberOfVMs` és `maxNumberofVMs` felhasználói változók.

> [!NOTE]
> Szolgáltatás által definiált változókat mindig előzi dollárjelet ($). Felhasználói változók az dollárjel nem kötelező megadni.
>
>

Az alábbi táblázatok tartalmazzák, amelyeket a Batch szolgáltatás írási és olvasási és az írásvédett változók.

Get, és adja meg a készlet számítási csomópontjai számú kezelését a szolgáltatás által definiált változók értékeit:

| Olvasási és írási szolgáltatás által definiált változó | Leírás |
| --- | --- |
| $TargetDedicatedNodes |A dedikált cél száma számítási csomópontok a készlet. Dedikált csomópontok száma cél van megadva, mert a készlet mindig nem érhetik el a kívánt csomópontok számát. Például ha a cél dedikált csomópontok száma módosul a automatikus skálázás próbaverzióként előtt a készlet elérte a kezdeti célként, majd a készlet előfordulhat, hogy nem érte el a cél. <br /><br /> A Batch szolgáltatás beállításokkal létre egy fiókot a készlet nem érhetik el a célértéket Ha a cél meghaladja a kötegelt fiók csomópont vagy core kvóta. A felhasználó előfizetéshez beállításokkal létre egy fiókot a készlet nem érhetik el a célértéket, ha a cél meghaladja az előfizetés megosztott magkvótája.|
| $TargetLowPriorityNodes |Alacsony prioritású cél száma számítási csomópontok a készlet. Alacsony prioritású csomópontok száma cél van megadva, mert a készlet mindig nem érhetik el a kívánt csomópontok számát. Például ha a cél alacsony prioritású csomópontok száma módosul a automatikus skálázás próbaverzióként előtt a készlet elérte a kezdeti célként, majd a készlet előfordulhat, hogy nem érte el a cél. Egy készlet is nem érhetik el a célértéket Ha a cél meghaladja a kötegelt fiók csomópont vagy core kvóta. <br /><br /> Alacsony prioritású számítási csomóponton további információkért lásd: [alacsony prioritású virtuális gépek használata a kötegelt (előzetes verzió)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |A művelet, amikor a számítási csomópontok el lesznek távolítva a készletben. Lehetséges értékek:<ul><li>**requeue**--feladatok azonnal leáll, és, hogy azok újraütemezte helyezi őket újra a feladat-várólistába.<li>**Állítsa le**--feladatok azonnal leáll, és eltávolítja azokat a feladat-várólistán.<li>**taskcompletion**--vár a jelenleg futó feladatok befejezéséhez, majd eltávolítja a csomópontot a készletből.<li>**retaineddata**– az összes helyi feladat őrzi meg a csomóponton, hogy a csomópont eltávolítása a készletből előtt tisztítani vár.</ul> |

A Batch szolgáltatás metrikák alapuló módosításokra szolgáltatás által definiált változókhoz értékének kaphat:

| Csak olvasható szolgáltatás által definiált változó | Leírás |
| --- | --- |
| $CPUPercent |Az átlagos CPU-használat százalékos aránya |
| $WallClockSeconds |A felhasznált másodpercek számát. |
| $MemoryBytes |A használt mérete (MB) átlagos száma. |
| $DiskBytes |A helyi lemezeken használt gigabájt átlagos száma. |
| $DiskReadBytes |A beolvasott bájtok száma. |
| $DiskWriteBytes |Az írt bájtok száma. |
| $DiskReadOps |A végrehajtott lemezolvasási műveletek száma. |
| $DiskWriteOps |A végrehajtott írási lemez műveletek száma. |
| $NetworkInBytes |Bejövő bájtok száma. |
| $NetworkOutBytes |Küldött bájtok száma. |
| $SampleNodeCount |A számítási csomópontok száma. |
| $ActiveTasks |A végrehajtásra kész, de még nem végrehajtása feladatok száma. A $ActiveTasks száma minden feladatokat, amelyek az aktív állapotú, és amelynek függőségek teljesülnek tartalmazza. Minden feladatot, amely az aktív állapotban van, de amelynek függőségek nem teljesülnek a $ActiveTasks száma nem tartoznak.|
| $RunningTasks |Futó állapotban feladatok száma. |
| $PendingTasks |$ActiveTasks és $RunningTasks összege. |
| $SucceededTasks |Sikeresen befejeződött feladatok száma. |
| $FailedTasks |A sikertelen feladatok száma. |
| $CurrentDedicatedNodes |A számítási csomópontok dedikált aktuális száma. |
| $CurrentLowPriorityNodes |Alacsony prioritású aktuális száma a számítási csomópontok, beleértve azoknak a fürtöknek, pre-empted volt. |
| $PreemptedNodeCount | A készlet pre-empted állapotban lévő csomópontok száma. |

> [!TIP]
> A csak olvasható, a szolgáltatás által meghatározott változók az előző táblázatban látható *objektumok* , adja meg a különböző módszereket a hozzájuk kapcsolódó adatok eléréséhez. További információkért lásd: [szerezze be a mintaadatokat](#getsampledata) című cikkben.
>
>

## <a name="types"></a>Típusok
Ezek a típusok támogatottak a képlet:

* Dupla
* doubleVec
* doubleVecList
* karakterlánc
* Timestamp típusú--időbélyeg olyan összetett struktúra, amely a következő tagot tartalmaz:

  * év
  * a hónap (1-12)
  * a nap (1-31)
  * milyen napra esik (a kell száma; például hétfő 1)
  * óra (24 órás számformátumú; például 13 azt jelenti, hogy 1 óra)
  * a perc (00 és 59 közötti)
  * második (00 és 59 közötti)
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
Ezeket a műveleteket az előző szakaszban felsorolt típusok engedélyezettek.

| Művelet | Támogatott operátorok | Eredménytípus |
| --- | --- | --- |
| kettős *operátor* dupla |+, -, *, / |Dupla |
| kettős *operátor* timeinterval |* |TimeInterval |
| doubleVec *operátor* dupla |+, -, *, / |doubleVec |
| doubleVec *operátor* doubleVec |+, -, *, / |doubleVec |
| TimeInterval *operátor* dupla |*, / |TimeInterval |
| TimeInterval *operátor* timeinterval |+, - |TimeInterval |
| TimeInterval *operátor* időbélyeg |+ |időbélyeg |
| Timestamp típusú *operátor* timeinterval |+ |időbélyeg |
| Timestamp típusú *operátor* időbélyeg |- |TimeInterval |
| *operátor*dupla |-, ! |Dupla |
| *operator*timeinterval |- |TimeInterval |
| kettős *operátor* dupla |<, <=, ==, >=, >, != |Dupla |
| karakterlánc *operátor* karakterlánc |<, <=, ==, >=, >, != |Dupla |
| Timestamp típusú *operátor* időbélyeg |<, <=, ==, >=, >, != |Dupla |
| TimeInterval *operátor* timeinterval |<, <=, ==, >=, >, != |Dupla |
| kettős *operátor* dupla |&&, &#124;&#124; |Dupla |

Dupla típusú értékként Ternáris operátorral tesztelése során (`double ? statement1 : statement2`), nem nulla van **igaz**, és nulla **hamis**.

## <a name="functions"></a>Functions
Ezek előre definiált **funkciók** érhetők el, hogy az automatikus méretezési képlet definiálásakor használja.

| Függvény | Visszatérési típusa | Leírás |
| --- | --- | --- |
| avg(doubleVecList) |Dupla |A doubleVecList átlagértékét összes értékét adja vissza. |
| len(doubleVecList) |Dupla |A a doubleVecList létrehozott vektor hosszának beolvasása. |
| LG(Double) |Dupla |A napló alap 2. a dupla adja vissza. |
| lg(doubleVecList) |doubleVec |A component-wise napló alap 2. a doubleVecList adja vissza. Egy vec(double) paraméter explicit módon kell átadni. Ellenkező esetben a dupla lg(double) verzió feltételezi. |
| ln(Double) |Dupla |A kettős természetes naplójának adja vissza. |
| ln(doubleVecList) |doubleVec |A component-wise napló alap 2. a doubleVecList adja vissza. Egy vec(double) paraméter explicit módon kell átadni. Ellenkező esetben a dupla lg(double) verzió feltételezi. |
| log(Double) |Dupla |A napló a kettős 10 alap adja vissza. |
| log(doubleVecList) |doubleVec |A component-wise napló a doubleVecList alap 10 adja vissza. Egy vec(double) explicit módon kell átadni a egyetlen dupla paramétert. Ellenkező esetben a dupla log(double) verzió feltételezi. |
| max(doubleVecList) |Dupla |A doubleVecList maximális értékét adja vissza. |
| min(doubleVecList) |Dupla |A doubleVecList minimális értékét adja vissza. |
| norm(doubleVecList) |Dupla |A két-alapértelmezetté a doubleVecList létrehozott vektor adja vissza. |
| a PERCENTILIS (doubleVec v, dupla p) |Dupla |A PERCENTILIS elem a v vektor beolvasása. |
| rand() |Dupla |Egy véletlenszerű értéke 0,0 és 1,0 között. |
| range(doubleVecList) |Dupla |A doubleVecList a minimális és maximális értékek különbségét adja vissza. |
| std(doubleVecList) |Dupla |A doubleVecList értékek minta szórását adja vissza. |
| stop() | |Leállítja az automatikus skálázás kifejezés kiértékelése. |
| sum(doubleVecList) |Dupla |A doubleVecList összetevőit összegét adja vissza. |
| idő (dateTime karakterlánc = "") |időbélyeg |Ha átadva adja vissza a jelenlegi időpontnál, ha nincs átadott paraméter van, vagy a dátum/idő karakterlánc időbélyegzőjét. Támogatott dátum és idő formátumok a következők: W3C-DTF és RFC 1123. |
| val (doubleVec v, dupla i) |Dupla |Az elem, helyén i vektoros v, a nulla kezdődő indexű értékét adja vissza. |

A függvények, az előző táblázatban ismertetett fogadhatnak egy listát, amelynek argumentuma. A vesszővel elválasztott lista bármilyen kombinációját *dupla* és *doubleVec*. Példa:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

A *doubleVecList* érték alakítja át egyetlen *doubleVec* kiértékelése előtt. Például ha `v = [1,2,3]`, majd hívja `avg(v)` hívása egyenértékű `avg(1,2,3)`. Hívása `avg(v, 7)` hívása egyenértékű `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Szerezze be a mintaadatokat
A Batch szolgáltatás által biztosított metrikákat adatok (minták) automatikus skálázási hajtanak végre műveleteket. A képlet növekszik, vagy zsugorítja megszerzi a szolgáltatásból értékek alapján a készlet méretét. A szolgáltatás által definiált változókat leírt korábban olyan objektumok, adja meg a különböző módszereket, hogy az objektum társított adatok eléréséhez. Például az alábbi kifejezés látható, a CPU-használat az elmúlt 5 percben beolvasása kérelem:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Módszer | Leírás |
| --- | --- |
| GetSample() |A `GetSample()` metódus egy adatok minták-vektorát adja vissza.<br/><br/>Egy minta érték metrikák adatok érdemes 30 másodperc. Más szóval minták akkor kapja meg, 30 másodperces. Azonban az alábbi esetekben, amikor egy minta gyűjt, és elérhető képlet késleltetés van. Mint ilyen nem minden mintákat egy adott időszakra vonatkozóan egy képlettel értékelésre érhetők el.<ul><li>`doubleVec GetSample(double count)`<br/>Adja meg a legutóbbi minták összegyűjtött beszerezni minták számát.<br/><br/>`GetSample(1)` a legutóbbi elérhető minta adja vissza. A metrikák, például `$CPUPercent`, azonban ez nem használható, mert nem lehet tudni, hogy *amikor* gyűjtötte a program a minta. Előfordulhat, hogy friss, vagy a rendszer problémák miatt előfordulhat, hogy sokkal régebbi. Érdemes ebben az esetben egy adott időintervallumban alább látható módon használatára.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Adja meg vagy időszakon minta adatgyűjtést. Szükség esetén azt is a százalékos értékét határozza meg, hogy a kért időkereten belül el kell érnie minták.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` 20 minták alakítanák vissza, ha az elmúlt 10 perc minden minták léteznek CPUPercent előzményeit. Ha az elmúlt percben az előzményeket nem volt elérhető, azonban csak 18 minták akkor adja vissza. Ebben az esetben:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` fognak működni, mert a minták csak 90 %-át érhetők el.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` járnak.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Adatgyűjtés a kezdési idő és a befejezési idő időkeretet határozza meg.<br/><br/>Fent említett nincs amikor minta gyűjt, és elérhető képlet késleltetés. Ez a késés használatakor vegye figyelembe a `GetSample` metódust. Lásd: `GetSamplePercent` alatt. |
| GetSamplePeriod() |Egy korábbi minta adathalmaz végrehajtott minták a dátumtartományt ad vissza. |
| Count() |A metrika előzmények minták teljes számát adja vissza. |
| HistoryBeginTime() |A metrika a legrégebbi rendelkezésre álló adatok minta időbélyegzőjét adja vissza. |
| GetSamplePercent() |Egy adott időintervallumban a rendelkezésre álló minták aránya adja vissza. Példa:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Mivel a `GetSample` módszer nem jár sikerrel, ha minták százalékos visszaadott kisebb, mint a `samplePercent` megadott, használhatja a `GetSamplePercent` először ellenőrzési módszert. Majd egy másik műveletet hajthatnak végre, ha elegendő minták léteznek, Leállítás, az automatikus méretezési értékelése nélkül. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Minták, a minta százalék, és a *GetSample()* módszer
Az automatikus skálázás képlet alapvető működéséhez szerezze be a tevékenység- és metrika értékét, és majd módosítsa a készlet mérete az adatok alapján. Fontos, hogy az automatikus skálázás képletek hogyan működnek együtt a metrikai adatok (minták).

**Minták**

A Batch szolgáltatás rendszeres időközönként tevékenység- és metrikákat mintát vesz igénybe, és elérhetővé válnak az automatikus skálázás képletek. Ezeket a mintákat a Batch szolgáltatás 30 másodpercenként rögzítését. Van azonban általában a késleltetés között, ha ezek a minták rögzítve, és amikor a rendelkezésre álló (és tudja olvasni.) az automatikus skálázás formulákat. Továbbá például hálózati vagy egyéb infrastrukturális problémára különböző tényezők miatt minták előfordulhat, hogy nem rögzíti egy adott időszakban.

**A minta százalékos aránya**

Ha `samplePercent` számára a `GetSample()` metódus vagy a `GetSamplePercent()` metódus lehívásra kerül, _százalék_ hivatkozik a teljes lehetséges minták a Batch szolgáltatás által rögzített és száma összehasonlítása az automatikus skálázás képlet elérhető mintát.

Nézzük timespan érték 10 perc példaként. Minták tárolja, 30 másodperces timespan érték 10 perc belül, mert a köteg által rögzített minták maximális száma lenne 20 minták (2 / perc). Azonban a jelentéskészítési mechanizmus és más olyan problémák Azure-ban rejlő késését, mert előfordulhat, csak az automatikus skálázás képlet olvasásra rendelkezésre álló 15 minták. Tehát például az, hogy 10 perc alatt 75 %-a rögzített minták száma érhetők el a képlet.

**GetSample() és minta tartományok**

Az automatikus skálázás képletek fog növekszik és a készletek zsugorítását &mdash; csomópontok hozzáadása vagy eltávolítása a csomópontok. Csomópontok pénz költségeket, mert szeretne biztosítani, hogy a képletekben módszerhez egy intelligens elemzés, amely elegendő adatokon alapul. Ezért azt javasoljuk, hogy használja-e a trendek-elemzés a képletekben. Ez a típus növekszik, és az összegyűjtött minták alapján készletek zsugorítja.

Ehhez használja `GetSample(interval look-back start, interval look-back end)` minták vektor vissza:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

A fenti sor kötegelt kiértékelésekor értékek vektor vissza minták számos. Példa:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Miután minták vektoros összegyűjtött használhatók funkciók, például a `min()`, `max()`, és `avg()` jelentéssel bíró értékek kapcsolattípusokból származhatnak az összegyűjtött tartományon.

A fokozott biztonság érdekében kényszerítheti a kiértékelési sikertelen lesz, ha kisebb, mint egy bizonyos minta százalék érhető el egy adott időszakra vonatkozóan. Ha kényszeríti a kiértékelési sikertelen lesz, kötegelt, hogy további a képlet értékelése megszűnik, ha nem érhető el a megadott százalék minták utasította. Ebben az esetben nem módosítja a készlet méretét. Minták sikeres értékeléséhez szükséges százalékos megadásához adja meg azt a harmadik paraméter `GetSample()`. Itt 75 százalékával minták követelményt van megadva:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

A minta rendelkezésre állási késés előfordulhat, mert fontos mindig adjon meg egy időtartományt megjelenését visszaírt kezdő időpontja korábbi, mint egy perc. Az minták propagálásához a rendszer körülbelül egy percet vesz igénybe, így minták a tartomány `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` nem érhető el. Ebben az esetben használhatja a százalékos paramétere `GetSample()` adott minta százalékos követelmény kényszerítése.

> [!IMPORTANT]
> A Microsoft **erősen ajánlott** , amikor **elkerülése érdekében a megbízható függő *csak* a `GetSample(1)` az automatikus skálázás képletekben**. Ennek az az oka `GetSample(1)` lényegében szerint a Batch szolgáltatás "Adja meg az utolsó mintát, rendelkezik, függetlenül attól, hogy mennyivel beolvasni." Csak egy egyszeres, és lehet, hogy egy régebbi mintát, mert nem lehet jellemző a legutóbbi tevékenység vagy erőforrás állapotának nagyobb képe. Ha a `GetSample(1)`, győződjön meg arról, hogy része egy nagyobb utasítás, és nem az egyetlen adatpont, amely a képlet támaszkodik.
>
>

## <a name="metrics"></a>Mérőszámok
Erőforrás és a feladat metrikák képlet definiálásakor még használhatja. Módosíthatja a dedikált csomópontok a készlet az beszerzése és kiértékelése metrikák adatokon alapulnak cél száma. Tekintse meg a [változók](#variables) szakasz fölött mindegyik metrikát olvashat.

<table>
  <tr>
    <th>Metrika</th>
    <th>Leírás</th>
  </tr>
  <tr>
    <td><b>Erőforrás</b></td>
    <td><p>Erőforrás metrikáit a CPU, a sávszélesség, a memória használata, a számítási csomópontok és a csomópontok száma alapulnak.</p>
        <p> A szolgáltatás által definiált változókat hasznosak korrekciók csomópontok száma alapján:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>A szolgáltatás által definiált változókat hasznosak korrekciók csomópont erőforrás-használat alapján:</p>
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
    <td><p>Feladat metrikák függőben, például az aktív, a feladatok állapotának alapulnak, és befejeződött. A következő szolgáltatás által definiált változókat hasznosak feladat mérőszámok alapján készletméretet korrekciók:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Az automatikus skálázás képlet írása
Kibővített kialakítást hozhat létre az automatikus skálázás képlet képező utasításokat, amelyek a fenti összetevőket használnak, majd egyesítése egy teljes képlet ezen utasításokat. Ebben a szakaszban egy példa automatikus skálázás képlet által végrehajtható műveleteket az egyes valós méretezési döntések tudjuk létrehozni.

Először is határozza meg az új automatikus skálázás képlet követelményei. A képlet a következőket:

1. Dedikált számítási csomópontok a készlet cél számának növelése, ha a CPU-használata túl magas.
2. Dedikált számítási csomópontok a készlet cél számának csökkentéséhez, amikor a CPU-használat alacsony.
3. Mindig korlátozása 400 dedikált csomópontok maximális száma.

A csomópontok számának növeléséhez során magas CPU-használatot, definiálni kell az utasítást, amely tölti fel a felhasználó által definiált változó (`$totalDedicatedNodes`) értékkel, amely 110 százalékos aránya a cél száma dedikált csomópontok, de csak akkor, ha a minimális átlagos CPU-használat során a elmúlt 10 perc 70 százalék felett volt. Ellenkező esetben használja az értéket a kijelölt csomópontok száma.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

A *csökkentése* során alacsony processzorhasználat, a következő utasítás a képletben dedikált csomópontok száma beállítja azonos `$totalDedicatedNodes` változó – 90 százalékára az aktuális célként kijelölt csomópontok száma, ha a átlagos processzorhasználat százalékos mértéke az elmúlt 60 perc alatt 20 % volt. Ellenkező esetben használja az aktuális értéke `$totalDedicatedNodes` , amely jelenleg a fenti utasításban feltöltve.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Most dedikált számítási csomópontok legfeljebb 400 cél számának korlátozása:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

A teljes képlet a következő:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>A .NET az automatikus skálázás engedélyezve készlet létrehozása

Az automatikus skálázást engedélyezve van a .NET-készlet létrehozása, kövesse az alábbi lépéseket:

1. A készlet létrehozásához [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Állítsa be a [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) tulajdonságot `true`.
3. Állítsa be a [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) az automatikus skálázás képlettel tulajdonság.
4. (Választható) Állítsa be a [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) tulajdonság (alapértelmezett érték 15 perc).
5. A címkészlet, amely véglegesíti [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) vagy [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

A következő kódrészletet az automatikus skálázás engedélyezve készlet .NET hoz létre. A készlet automatikus skálázás képlet állítja be a cél több hétfőn 5 dedikált csomópontot, és minden egyéb a hét napja, 1. A [automatikus méretezési időköz](#automatic-scaling-interval) 30 percre van beállítva. Ebben és más C# kódtöredékek ebben a cikkben `myBatchClient` megfelelően inicializálva példánya a [BatchClient] [ net_batchclient] osztály.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "small", // single-core, 1.75 GB memory, 225 GB disk
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Az automatikus skálázás engedélyezve készletet hoz létre, ha nem adja meg a _targetDedicatedComputeNodes_ paraméter vagy a _targetLowPriorityComputeNodes_ hívásakor paraméter **CreatePool** . Ehelyett adja meg a **AutoScaleEnabled** és **AutoScaleFormula** a készlet tulajdonságai. Ezek a tulajdonságok értékeit minden típusú csomópont target számának meghatározásához. Manuálisan méretezze át az automatikus skálázás-kompatibilis készlet is, a (például a [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), első **letiltása** automatikus méretezésének a tárolókészlet, majd méretezze át.
>
>

Mellett Batch .NET, használhatja a másik [kötegelt SDK-k](batch-apis-tools.md#azure-accounts-for-batch-development), [Batch REST](https://docs.microsoft.com/rest/api/batchservice/), [kötegelt PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md), és a [kötegelt CLI](batch-cli-get-started.md) számára automatikus skálázás konfigurálása.


### <a name="automatic-scaling-interval"></a>Automatikus méretezési időköz
Alapértelmezés szerint a Batch szolgáltatás 15 percenként állítja be a készlet méretét az automatikus skálázási képletnek megfelelően. Ez az időtartam alatt a következő alkalmazáskészlet tulajdonságaiban használatával konfigurálható:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

A minimális időköze öt perc, a maximális 168 óra. Ha az időközönkénti ezen a tartományon kívül van megadva, a Batch szolgáltatás egy hibás kérés (400) hibaüzenetet adja vissza.

> [!NOTE]
> Automatikus skálázás nem célja jelenleg igazodjanak kisebb, mint egy percen belül, de ahelyett, hogy olyan fokozatosan futtatása a munkaterhelés, a készlet méretének beállítása.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Egy meglévő készlet automatikus skálázás engedélyezéséhez

Minden egyes kötegelt SDK biztosítja az automatikus skálázás engedélyezéséhez. Példa:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .NET)
* [A készlet automatikus skálázás engedélyezése] [ rest_enableautoscale] (REST API-t)

Ha engedélyezi az automatikus skálázás meglévő címkészlet, vegye figyelembe a következő szempontokat:

* Ha az automatikus skálázás jelenleg le van tiltva a készletet az automatikus skálázás engedélyezéséhez a kérelem elküldésekor, adjon meg érvényes automatikus skálázás képlet a kérelem elküldésekor. Opcionálisan megadhat az automatikus skálázás újraértékelési időközét. Ha nem adja meg a időközt, az alapértelmezett érték 15 perc szolgál.
* Ha automatikus skálázás jelenleg engedélyezve van a készletet, megadhatja az automatikus skálázás képlet, egy újraértékelési időközét, vagy mindkettőt. Ezek a tulajdonságok közül legalább egy meg kell adnia.

  * Ha egy új automatikus skálázás újraértékelési időközét adja meg, majd a meglévő értékelésének ütemezése le van állítva, és új ütemezés szerint elindult. Az új ütemezés kezdési ideje az az idő, amelynél a kérelem automatikus skálázás engedélyezéséhez ki.
  * Ha nincs megadva vagy az automatikus skálázás képlet vagy újraértékelési időközét, a Batch szolgáltatás továbbra is az aktuális értéket használja.

> [!NOTE]
> Ha a megadott értékek a *targetDedicatedComputeNodes* vagy *targetLowPriorityComputeNodes* paraméterei a **CreatePool** módszer a tárolókészlet létrehozásakor .NET, vagy egy másik nyelven összehasonlítható paraméterekhez, majd ezeket az értékeket figyelmen kívül hagyja az automatikus méretezési képlet kiértékelésekor.
>
>

A C# kódrészletet használja a [Batch .NET] [ net_api] szalagtár meglévő címkészlet automatikus skálázás engedélyezéséhez:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Az automatikus skálázás képlet frissítése

A képlet meglévő automatikus skálázás engedélyezve címkészlet frissítéséhez hívható meg a művelet újra az új képletet az automatikus skálázás engedélyezéséhez. Például, ha az automatikus skálázás már engedélyezve van a `myexistingpool` eljárás végrehajtásakor a következő .NET-kódot, az automatikus skálázás képlet helyére tartalmát `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Frissítés az automatikus skálázás időköz

Az automatikus skálázás újraértékelési időközét egy meglévő automatikus skálázás engedélyezve készlet frissítéséhez hívható meg a művelet újra az új időköz az automatikus skálázás engedélyezéséhez. Ha például az automatikus skálázás újraértékelési időközét beállítása és 60 perc már automatikus skálázás engedélyezve a .NET-készletre vonatkozó:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Az automatikus skálázás képlet kiértékelése

Mielőtt telepítené azt a készlethez kiértékelheti az képletet. Ezzel a módszerrel tesztelheti a képletet, hogy hogyan kimutatásait értékelje ki a képletet üzembe helyezésre előtt.

Az automatikus skálázás képlet kiértékeléséhez, először engedélyeznie kell a készlet egy érvényes képletet az automatikus skálázást. Egy készlet, amely még nem rendelkezik engedélyezett automatikus skálázás képlet teszteléséhez használja a egysoros `$TargetDedicatedNodes = 0` Ha először engedélyezi az automatikus skálázást. Ezután segítségével a következők egyikét értékelje ki a vizsgálni kívánt képlet:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) or [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    A Batch .NET módszerekhez kiértékelése egy meglévő készlet és az automatikus skálázás képletet tartalmazó karakterlánc Azonosítóját.

* [Az automatikus méretezési képlet kiértékelése](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    A REST API-kérelem, adja meg az URI azonosító a készlet Azonosítóját, és az automatikus skálázás képlet a *autoScaleFormula* eleme a kérés törzsében. A válasz a művelet bármilyen melyikhez kapcsolódhat a képlet hibát adatokat tartalmaz.

Ezen [Batch .NET] [ net_api] kódrészletet, azt az automatikus skálázás képlet kiértékeléséhez. Ha a készlet nem rendelkezik automatikus skálázás engedélyezve van, engedélyezzük az első.

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
        autoscaleFormula: "$TargetDedicatedNodes = {pool.CurrentDedicatedNodes};",
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

A következő kódrészletet látható képlet sikeres kiértékelési eredménye hasonló:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Automatikus skálázás futtatása adatainak beolvasása

Győződjön meg arról, hogy a várt módon működik-e a képletet, azt javasoljuk, hogy rendszeres időközönként ellenőrizze az automatikus skálázás fut, a készlet végző kötegelt eredményeit. A tehát (vagy frissítse) egy hivatkozást, a készletbe és az utolsó automatikus skálázás futtatása tulajdonságainak vizsgálata.

A Batch .NET a [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) tulajdonság rendelkezik, amelyek információval szolgálnak a legújabb automatikus skálázás futtatnak majd elvégezni a készlet több tulajdonságok:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

A REST API-ban a [készlet adatainak beolvasása](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) kérelmet a készletbe, amely tartalmazza a legújabb automatikus skálázás információk futtatni információt ad vissza a [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool#bk_autrun) tulajdonság.

A következő C# kódrészletet használja a Batch .NET kódtár adatokat a legutóbbi automatikus skálázás futtassa a készlet nyomtathat _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Az előző részlet Példakimenet:

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

## <a name="example-autoscale-formulas"></a>Példa automatikus skálázás képletek
Vizsgáljuk meg néhány képletek, amelyek megjelenítik a különböző módon úgy, hogy a készlet számítási erőforrások mennyisége.

### <a name="example-1-time-based-adjustment"></a>1. példa: Időalapú módosítása
Tegyük fel, hogy úgy, hogy a készlet méretét, és a hét napja, nap alapul. Ez a példa bemutatja, hogyan növelhető vagy csökkenthető ennek megfelelően a készletben található csomópontok számát.

A képlet először beolvassa az aktuális idővel. Ha a hét napja (1-5) és munkaidőn (8 AM a 18: 00) belül, a cél készletméretet értéke 20 csomópontot. Ellenkező esetben az értéke 10 csomópontok.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>2. példa: Feladatalapú módosítása
Ebben a példában a készlet méretét módosul a várólistán lévő feladatok száma alapján. Megjegyzések és a sortöréseket a képlet karakterláncok elfogadhatók.

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

### <a name="example-3-accounting-for-parallel-tasks"></a>3. példa: Párhuzamos tevékenységek nyilvántartás
Ebben a példában a készlet méretét, a feladatok száma alapján állítja be. A képlet is figyelembe veszi a [MaxTasksPerComputeNode] [ net_maxtasks] érték, amely be van állítva a készletet. Ez a módszer akkor hasznos, ahol [párhuzamos tevékenység végrehajtása](batch-parallel-node-tasks.md) a készlet engedélyezve lett.

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

### <a name="example-4-setting-an-initial-pool-size"></a>4. példa: Egy kezdeti mérete beállítása
Ez a példa bemutatja egy C# kódrészletet az automatikus skálázás képlettel, amely egy megadott számára korlátozva a csomópontok a készlet méretét határoz meg egy kezdeti időszak. Ezután azt állítja be a készlet mérete alapján fut számát és az aktív feladatok a kezdeti időszak eltelte után.

A képlet a következő kódrészletet:

* Négy csomópont állítja be a kezdeti mérete.
* A készlet életciklus első 10 percen belül nem igazítja a készlet méretét.
* 10 perc elteltével beolvassa a maximális értékének száma az elmúlt 60 percen belül futó és az aktív feladatok.
  * Ha mindkét értékek 0 (Ez azt jelzi, hogy a feladat nem fut, vagy az elmúlt 60 perc aktív volt-e), a készlet méretét értéke 0.
  * Ha bármelyik érték nullánál nem kisebb, nem történik változás.

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
* [Azure Batch számítási erőforrás-használat csomópont egyidejű feladatok maximális](batch-parallel-node-tasks.md) részletesen ismerteti, hogyan hajthat végre több feladat egyidejű a készlet számítási csomópontjain. Mellett automatikus skálázást Ez a szolgáltatás segíthet feladat időtartama bizonyos munkaterhelések esetén, így pénzt takaríthat csökkenthető.
* Egy másik hatékonyságát gyorsító győződjön meg arról, hogy a kötegelt kérelem a Batch szolgáltatás lekérdezi a legoptimálisabb módon. Lásd: [hatékonyan lekérdezése az Azure Batch szolgáltatás](batch-efficient-list-queries.md) megtudhatja, hogyan átmenő a hálózaton, amikor több ezer számítási csomópontok és a feladatok állapotának lekérdezése adatok korlátozásához.

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
