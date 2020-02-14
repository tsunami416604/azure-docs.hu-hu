---
title: Számítási csomópontok automatikus méretezése egy Azure Batch készletben | Microsoft Docs
description: A készletben lévő számítási csomópontok számának dinamikus beállításához engedélyezze a Felhőbeli készlet automatikus méretezését.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 10/24/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017,fasttrack-edit
ms.openlocfilehash: 46be210ead3816356b63293b910e1c0e7ffc087b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200095"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Automatikus képlet létrehozása a számítási csomópontok méretezéséhez egy batch-készletben

A Azure Batch a definiált paraméterek alapján automatikusan méretezheti a készleteket. Az automatikus skálázással a Batch dinamikusan felveszi a csomópontokat egy készletbe, ahogy a feladat megköveteli a növekedést, és eltávolítja a számítási csomópontokat. A Batch-alkalmazás által használt számítási csomópontok számának automatikus módosításával időt és pénzt is megtakaríthat.

A számítási csomópontok készletén engedélyezheti az automatikus skálázást úgy, hogy társítja azt az Ön által meghatározott automatikus *méretezési képlettel* . A Batch szolgáltatás az autoskálázási képlet használatával határozza meg, hogy hány számítási csomópontra van szükség a munkaterhelés végrehajtásához. A számítási csomópontok dedikált csomópontok vagy [alacsony prioritású csomópontok](batch-low-pri-vms.md)lehetnek. A Batch a rendszeres időközönként gyűjtött szolgáltatás-metrikák adatait válaszolja meg. A metrikák használatával a Batch a képlet és a konfigurálható intervallum alapján módosítja a készletben lévő számítási csomópontok számát.

A készlet létrehozásakor vagy egy meglévő készleten is engedélyezheti az automatikus skálázást. Az automatikus skálázáshoz konfigurált készlet meglévő képletét is módosíthatja. A Batch segítségével kiértékelheti a képleteket, mielőtt hozzárendeli őket a készletekhez, és figyelnie kell az automatikus skálázási futtatások állapotát.

Ez a cikk az autoskálázási képleteket alkotó különböző entitásokat ismerteti, beleértve a változókat, a kezelőket, a műveleteket és a függvényeket. Megbeszéljük, hogyan szerezhet be különböző számítási erőforrások és tevékenységek mérőszámait a Batch szolgáltatáson belül. Ezekkel a metrikákkal az erőforrás-használat és a feladat állapota alapján állíthatja be a készlet csomópontjának darabszámát. Ezután leírjuk, hogyan hozhat létre egy képletet, és hogyan engedélyezheti az automatikus skálázást a készleteken a Batch REST és a .NET API-k használatával. Végül pedig néhány példa képlettel zárult.

> [!IMPORTANT]
> Batch-fiók létrehozásakor megadhatja a [fiók konfigurációját](batch-api-basics.md#account), amely meghatározza, hogy a készleteket a Batch szolgáltatás előfizetésében (az alapértelmezett) vagy a felhasználói előfizetésben kell-e lefoglalni. Ha létrehozta a Batch-fiókot az alapértelmezett batch szolgáltatás konfigurációjában, akkor a fiókja legfeljebb a feldolgozásra felhasználható magok maximális számára korlátozódik. A Batch szolgáltatás csak az adott alapkorlátig méretezi a számítási csomópontokat. Ezért előfordulhat, hogy a Batch szolgáltatás nem éri el az autoscale Formula által meghatározott számítási csomópontok céljának számát. A fiók kvótáinak megtekintésével és növelésével kapcsolatos információkért tekintse meg [a Azure batch szolgáltatás kvótáit és korlátait](batch-quota-limit.md) .
>
>Ha a felhasználói előfizetés konfigurációjával hozta létre a fiókját, a fiók az előfizetés fő kvótájában osztozik. További információkért lásd [az Azure-előfizetésekre és -szolgáltatásokra vonatkozó korlátozásokat, kvótákat és megkötéseket](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) ismertető témakör [a virtuális gépek korlátaira](../azure-resource-manager/management/azure-subscription-service-limits.md) vonatkozó részét.
>
>

## <a name="automatic-scaling-formulas"></a>Automatikus méretezési képletek

Az automatikus skálázási képlet egy definiált karakterlánc-érték, amely egy vagy több utasítást tartalmaz. Az autoskálázási képlet a készlet [autoScaleFormula][rest_autoscaleformula] eleméhez (batch REST) vagy a [CloudPool. autoScaleFormula][net_cloudpool_autoscaleformula] tulajdonsághoz (Batch .net) van rendelve. A Batch szolgáltatás a képlet segítségével határozza meg a készletben lévő számítási csomópontok megcélzott számát a feldolgozás következő időszakában. A képlet karakterlánca nem haladhatja meg a 8 KB-ot, legfeljebb 100, pontosvesszővel elválasztott utasítást tartalmazhat, és tartalmazhat sortöréseket és megjegyzéseket.

A képletek automatikus méretezése kötegelt automatikus méretezési "nyelv" lehet. A képlet-utasítások olyan szabadon formázott kifejezések, amelyek a szolgáltatás által definiált változókat (a Batch szolgáltatás által definiált változókat) és a felhasználó által definiált változókat (a definiált változókat) is tartalmazhatják. A beépített típusok, operátorok és függvények használatával különböző műveleteket végezhetnek ezeken az értékeken. Egy utasítás például a következő formát veheti fel:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

A képletek általában több olyan utasítást tartalmaznak, amelyek műveleteket hajtanak végre a korábbi utasításokban beszerzett értékeken. Például először a `variable1`értéket kapjuk meg, majd átadjuk egy függvénynek a `variable2`feltöltéséhez:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Vegye fel ezeket az utasításokat az autoskálázási képletbe, hogy a számítási csomópontok megérkezése esetén megtörténjen. A dedikált csomópontok és az alacsony prioritású csomópontok rendelkeznek saját célként megadott beállításokkal, így definiálni lehet egy célt az egyes csomópontokhoz. Az autoskálázási képletek a dedikált csomópontok, az alacsony prioritású csomópontok vagy mindkettő céljának értékét is tartalmazhatják.

A csomópontok célként megadott száma lehet magasabb, alacsonyabb vagy ugyanaz, mint a készletben lévő csomópontok aktuális száma. A Batch adott időközönként kiértékeli a készlet automatikus méretezési képletét (lásd: [automatikus skálázási időközök](#automatic-scaling-interval)). A Batch úgy állítja be a készletben lévő egyes csomópontok számát, hogy az a szám, amelyet az automatikusan Felskálázási képlet a kiértékeléskor megad.

### <a name="sample-autoscale-formulas"></a>Minta autoscale-képletek

Az alábbiakban két, a legtöbb forgatókönyv esetén a működéshez igazítható képleteket találhat. A példában szereplő képletekben `startingNumberOfVMs` és `maxNumberofVMs` változók az igényei szerint módosíthatók.

#### <a name="pending-tasks"></a>Függőben lévő feladatok

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

Ezzel az autoskálázási képlettel a készletet kezdetben egyetlen virtuális géppel hozza létre a rendszer. A `$PendingTasks` metrika meghatározza a futtatott vagy a várólistára helyezett feladatok számát. A képlet megkeresi a függőben lévő feladatok átlagos számát az utolsó 180 másodpercben, és ennek megfelelően beállítja a `$TargetDedicatedNodes` változót. A képlet biztosítja, hogy a dedikált csomópontok megcélzott száma soha ne haladja meg a 25 virtuális gépet. Az új feladatok elküldésekor a készlet automatikusan növekszik. A feladatok elvégzése után a virtuális gépek egyenként válnak szabaddá, és az automatikus skálázási képlet csökkenti a készletet.

Ez a képlet a dedikált csomópontokat méretezi, de úgy módosítható, hogy az alacsony prioritású csomópontok méretezésére is érvényes legyen.

#### <a name="preempted-nodes"></a>Előzik-csomópontok 

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Ez a példa egy olyan készletet hoz létre, amely 25 alacsony prioritású csomóponttal kezdődik. Minden alkalommal, amikor egy alacsony prioritású csomópont előzik, a rendszer egy dedikált csomóponttal cseréli le. Az első példához hasonlóan a `maxNumberofVMs` változó megakadályozza, hogy a készlet meghaladja a 25 virtuális gépet. Ez a példa hasznos lehet az alacsony prioritású virtuális gépek kihasználása mellett, ugyanakkor gondoskodni kell arról, hogy a készlet élettartama csak rögzített számú preemptions legyen.

## <a name="variables"></a>Változók

A **szolgáltatás által definiált** és a **felhasználó által definiált** változókat is használhatja az autoscale-képletekben. A szolgáltatás által definiált változók a Batch szolgáltatásba vannak beépítve. Néhány szolgáltatás által definiált változó írható és olvasható, néhány pedig csak olvasható. A felhasználó által definiált változók a definiált változók. Az előző szakaszban bemutatott példa képletben `$TargetDedicatedNodes` és `$PendingTasks` a szolgáltatás által definiált változók. A `startingNumberOfVMs` és `maxNumberofVMs` változók felhasználó által definiált változók.

> [!NOTE]
> A szolgáltatás által definiált változókat mindig a dollár ($) jel előzi meg. Felhasználó által definiált változók esetén a dollár aláírása nem kötelező.
>
>

Az alábbi táblázatokban a Batch szolgáltatás által definiált írható és olvasható változók is láthatók.

A szolgáltatás által definiált változók értékeit lekérheti és beállíthatja a készletben lévő számítási csomópontok számának kezeléséhez:

| Olvasási és írási szolgáltatás által definiált változók | Leírás |
| --- | --- |
| $TargetDedicatedNodes |A készlet dedikált számítási csomópontjainak megcélzott száma. A dedikált csomópontok száma célként van megadva, mert előfordulhat, hogy a készlet nem mindig éri el a kívánt számú csomópontot. Ha például a dedikált csomópontok megcélzott száma módosítva van egy autoskálázási kiértékeléssel, mielőtt a készlet elérte a kezdeti célt, akkor előfordulhat, hogy a készlet nem éri el a célt. <br /><br /> Előfordulhat, hogy a Batch szolgáltatás konfigurációjával létrehozott fiók nem éri el a célját, ha a cél meghaladja a Batch-fiók csomópontját vagy a fő kvótát. Előfordulhat, hogy a felhasználói előfizetés-konfigurációval létrehozott fiókban lévő készlet nem éri el a célját, ha a cél meghaladja az előfizetés megosztott alapszintű kvótáját.|
| $TargetLowPriorityNodes |A készlet alacsony prioritású számítási csomópontjainak megcélzott száma. Az alacsony prioritású csomópontok száma célként van megadva, mert előfordulhat, hogy a készlet nem mindig éri el a kívánt számú csomópontot. Ha például az alacsony prioritású csomópontok megcélzott száma módosítva van egy autoskálázási kiértékeléssel, mielőtt a készlet elérte a kezdeti célt, akkor előfordulhat, hogy a készlet nem éri el a célt. Előfordulhat, hogy a készlet nem éri el a célját, ha a cél meghaladja a Batch-fiók csomópontját vagy a fő kvótát. <br /><br /> Az alacsony prioritású számítási csomópontokkal kapcsolatos további információkért lásd: [alacsony prioritású virtuális gépek használata a Batch használatával](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Az a művelet, amely akkor fordul elő, amikor a számítási csomópontok el lesznek távolítva a készletből. Lehetséges értékek:<ul><li>**újravárólista**– az alapértelmezett érték. Azonnal leállítja a feladatokat, és visszahelyezi őket a feladat-várólistába, hogy azok újra legyenek ütemezve. Ez a művelet biztosítja, hogy a csomópontok megcélzott száma a lehető leggyorsabban elérhető legyen, de kevésbé hatékony, mivel a futó feladatok megszakadnak, és újra kell indítani a már elvégzett munkát. <li>**megszakítás**– leállítja a feladatokat azonnal, és eltávolítja azokat a feladat-várólistából.<li>**taskcompletion**– megvárja, amíg a jelenleg futó feladatok befejeződik, majd eltávolítja a csomópontot a készletből. Ezzel a beállítással elkerülhető a feladatok megszakítása és újravárólistába helyezése, ami a feladat által végzett munka pazarlását okozhatja. <li>**retaineddata**– megvárja a csomóponton a helyi feladat által megőrzött összes adat törlését, mielőtt eltávolítja a csomópontot a készletből.</ul> |

> [!NOTE]
> A `$TargetDedicatedNodes` változó a `$TargetDedicated`alias használatával is megadható. Hasonlóképpen a `$TargetLowPriorityNodes` változó is megadható a `$TargetLowPriority`alias használatával. Ha a képlet a teljes névvel ellátott változót és annak aliasát is beállítja, akkor a teljes névvel ellátott változóhoz rendelt érték elsőbbséget élvez.
>
>

A szolgáltatás által definiált változók értékének beszerzésével a Batch szolgáltatás mérőszámai alapján végezheti el a módosításokat:

| Csak olvasható, szolgáltatás által definiált változók | Leírás |
| --- | --- |
| $CPUPercent |A CPU-használat átlagos százalékos aránya. |
| $WallClockSeconds |A felhasznált másodpercek száma. |
| $MemoryBytes |A felhasznált megabájtok átlagos száma. |
| $DiskBytes |A helyi lemezeken használt gigabájtok átlagos száma. |
| $DiskReadBytes |A beolvasott bájtok száma. |
| $DiskWriteBytes |A megírt bájtok száma. |
| $DiskReadOps |A végrehajtott olvasási lemez műveleteinek száma. |
| $DiskWriteOps |A végrehajtott írási lemez műveleteinek száma. |
| $NetworkInBytes |A bejövő bájtok száma. |
| $NetworkOutBytes |A kimenő bájtok száma. |
| $SampleNodeCount |A számítási csomópontok száma. |
| $ActiveTasks |A végrehajtásra kész, de még nem végrehajtó feladatok száma. A $ActiveTasks száma magában foglalja az aktív állapotban lévő összes feladatot, és amelynek függőségei teljesültek. Az aktív állapotú, de a függőségeket nem kielégítő feladatok ki vannak zárva a $ActiveTasks darabszámból.|
| $RunningTasks |A futó állapotú feladatok száma. |
| $PendingTasks |$ActiveTasks és $RunningTasks összege. |
| $SucceededTasks |A sikeresen befejeződött feladatok száma. |
| $FailedTasks |A sikertelen feladatok száma. |
| $CurrentDedicatedNodes |A dedikált számítási csomópontok aktuális száma. |
| $CurrentLowPriorityNodes |Az alacsony prioritású számítási csomópontok aktuális száma, beleértve az előre kiszolgálása kimarad összes csomópontot. |
| $PreemptedNodeCount | A készletben lévő, előre kiszolgálása kimarad állapotú csomópontok száma. |

> [!TIP]
> Az előző táblázatban szereplő írásvédett, szolgáltatás által definiált változók olyan *objektumok* , amelyek különböző módszereket biztosítanak az egyes adatbázisokhoz kapcsolódó adateléréshez. További információ: [Mintaadatok beszerzése](#getsampledata) a cikk későbbi részében.
>
>

## <a name="types"></a>Típusok

Ezek a típusok a következő képletekben támogatottak:

* double
* doubleVec
* doubleVecList
* sztring
* időbélyeg – a timestamp egy összetett struktúra, amely a következő tagokat tartalmazza:

  * év
  * hónap (1-12)
  * nap (1-31)
  * hétköznap (szám formátumban, például 1 – hétfő)
  * óra (24 órás számformátum; például: 13, 1 óra)
  * perc (00-59)
  * második (00-59)
* timeinterval

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

Ezek a műveletek az előző szakaszban felsorolt típusoknál engedélyezettek.

| Művelet | Támogatott operátorok | Eredmény típusa |
| --- | --- | --- |
| dupla *operátor* dupla |+, -, *, / |double |
| kettős *operátor* TimeInterval |* |timeinterval |
| doubleVec- *kezelő* dupla |+, -, *, / |doubleVec |
| doubleVec *operátor* doubleVec |+, -, *, / |doubleVec |
| TimeInterval- *kezelő* dupla |*, / |timeinterval |
| TimeInterval *operátor* TimeInterval |+, - |timeinterval |
| TimeInterval *operátor* időbélyege |+ |időbélyeg |
| időbélyeg- *kezelő* TimeInterval |+ |időbélyeg |
| időbélyeg- *operátor* időbélyege |- |timeinterval |
| *operátor*dupla |-, ! |double |
| *operátor*TimeInterval |- |timeinterval |
| dupla *operátor* dupla |<, <=, ==, >=, >, != |double |
| karakterlánc- *operátor* karakterlánca |<, <=, ==, >=, >, != |double |
| időbélyeg- *operátor* időbélyege |<, <=, ==, >=, >, != |double |
| TimeInterval *operátor* TimeInterval |<, <=, ==, >=, >, != |double |
| dupla *operátor* dupla |&&, &#124;&#124; |double |

Ternáris operátorral (`double ? statement1 : statement2`) való dupla teszteléskor a nem nulla érték **igaz**, és a nulla **hamis**.

## <a name="functions"></a>Functions
Ezek az előre definiált **függvények** használhatók az automatikus skálázási képlet definiálásához.

| Függvény | Visszatérési típus | Leírás |
| --- | --- | --- |
| avg(doubleVecList) |double |A doubleVecList lévő összes érték átlagos értékét adja vissza. |
| Len (doubleVecList) |double |A doubleVecList létrehozott vektor hosszát adja vissza. |
| LG (dupla) |double |A dupla log-alapot adja vissza. |
| LG (doubleVecList) |doubleVec |Az összetevő – a doubleVecList 2. alapszintű log-alapját adja vissza. A paraméternek explicit módon át kell adni egy VEC (Double). Ellenkező esetben a rendszer a kettős LG (Double) verziót feltételezi. |
| ln (dupla) |double |A Double típusú természetes naplót adja vissza. |
| ln (doubleVecList) |doubleVec |A Double típusú természetes naplót adja vissza. |
| napló (dupla) |double |A Double napló alapját adja vissza. |
| napló (doubleVecList) |doubleVec |A doubleVecList összetevő-Wise log Base 10-es értékét adja vissza. Az egyszeres dupla paraméterhez explicit módon át kell adni egy VEC (Double). Ellenkező esetben a rendszer a kettős napló (Double) verzióját feltételezi. |
| max(doubleVecList) |double |A doubleVecList maximális értékét adja vissza. |
| min (doubleVecList) |double |A doubleVecList minimális értékét adja vissza. |
| norm(doubleVecList) |double |A doubleVecList létrehozott vektor két normáját adja vissza. |
| percentilis (doubleVec v, Double p) |double |A Vector v percentilis elemét adja vissza. |
| rand() |double |0,0 és 1,0 közötti véletlenszerű értéket ad vissza. |
| range(doubleVecList) |double |A doubleVecList minimális és maximális értékei közötti különbséget adja vissza. |
| std(doubleVecList) |double |A doubleVecList lévő értékek mintájának szórását adja vissza. |
| stop() | |Leállítja az automatikus skálázási kifejezés kiértékelését. |
| Sum (doubleVecList) |double |A doubleVecList összes összetevőjének összegét adja vissza. |
| time(string dateTime="") |időbélyeg |Az aktuális idő időbélyegzőjét adja vissza, ha a rendszer nem ad át paramétereket, vagy ha a dateTime karakterlánc időbélyegzője átadásra kerül. A támogatott dateTime formátumok a W3C-DTF és az RFC 1123. |
| val (doubleVec v, dupla i) |double |A Vector v helyen található elem értékének visszaadása a nulla kezdő indexével. |

Az előző táblázatban leírt függvények némelyike argumentumként is elfogadhatja a listát. A vesszővel tagolt lista a *kettős* és a *doubleVec*bármely kombinációját tartalmazza. Például:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

A *doubleVecList* érték a kiértékelés előtt egyetlen *doubleVec* lesz konvertálva. Ha például `v = [1,2,3]`, akkor a `avg(v)` meghívása egyenértékű a `avg(1,2,3)`meghívásával. A `avg(v, 7)` meghívása egyenértékű a `avg(1,2,3,7)`meghívásával.

## <a name="getsampledata"></a>Mintaadatok beszerzése

Az autoskálázási képletek a Batch szolgáltatás által biztosított mérőszámok adatain (mintákon) működnek. A képletek a szolgáltatástól kapott értékek alapján növekednek vagy zsugorodnak a készlet méretétől függően. A korábban leírt szolgáltatás által definiált változók olyan objektumok, amelyek különböző módszereket biztosítanak az adott objektumhoz társított adateléréshez. A következő kifejezés például egy kérést mutat be a CPU-használat utolsó öt percének beszerzéséhez:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Módszer | Leírás |
| --- | --- |
| GetSample() |A `GetSample()` metódus adatmintákból álló vektort ad vissza.<br/><br/>A minta a metrikák adataihoz tartozó 30 másodperc. Más szóval a mintákat 30 másodpercenként szerzi be a rendszer. De ahogy az alábbiakban is látható, a rendszer a mintavétel begyűjtésének és a képletek számára elérhetővé tételének késleltetését jelzi. Így az adott időszakra vonatkozóan nem minden minta lehet egy képlet alapján kiértékelésre.<ul><li>`doubleVec GetSample(double count)`<br/>Meghatározza, hogy a rendszer hány mintát kapjon a legutóbbi összegyűjtött mintákból.<br/><br/>`GetSample(1)` az utolsó elérhető mintát adja vissza. A metrikák, például a `$CPUPercent`esetében azonban ez nem használható, mert nem lehet tudni, hogy *Mikor* gyűjtötték be a mintát. Előfordulhat, hogy a legutóbbi vagy a rendszerproblémák miatt sokkal régebbi. Ilyen esetekben jobb, ha az alább látható időintervallumot használja.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Meghatározza a mintaadatok gyűjtésének időkeretét. Azt is meghatározza, hogy a minták hány százalékát kell elérhetőnek lennie a kért időkeretben.<br/><br/>a `$CPUPercent.GetSample(TimeInterval_Minute * 10)` 20 mintát ad vissza, ha az utolsó 10 perc összes mintája megtalálható a CPUPercent előzményeiben. Ha a korábbi előzmények nem voltak elérhetők, de csak 18 mintát ad vissza. Ebben az esetben:<br/><br/>a `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` sikertelen, mert csak a minták 90 százaléka érhető el.<br/><br/>a `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` sikeres.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Megadja az adatgyűjtés időkeretét, a kezdési és befejezési időpontot is beleértve.<br/><br/>A fentiekben leírtaknak megfelelően a rendszer a mintavétel begyűjtését és a képletek számára elérhetővé tételét késlelteti. A `GetSample` metódus használatakor vegye figyelembe ezt a késleltetést. Lásd alább `GetSamplePercent`. |
| GetSamplePeriod() |Egy korábbi mintaadatok-készletben szereplő minták időszakát adja vissza. |
| Count() |A metrikus előzményekben szereplő minták teljes számát adja vissza. |
| HistoryBeginTime() |A metrika legrégebbi rendelkezésre állási mintájának időbélyegét adja vissza. |
| GetSamplePercent() |Egy adott időintervallumhoz elérhető minták százalékos arányát adja vissza. Például:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Mivel a `GetSample` metódus meghiúsul, ha a visszaadott minták százalékos aránya kisebb, mint a megadott `samplePercent`, az `GetSamplePercent` metódussal lehet először megtekinteni. Ezt követően másik műveletet is végrehajthat, ha nincs elegendő minta, az automatikus skálázás kiértékelésének megállítása nélkül. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Minták, minta százalék és a *GetSample ()* metódus
Az automatikusan méretezhető képletek alapvető működése a feladat-és erőforrás-metrikai adatok beszerzése, majd a készlet méretének módosítása az adatok alapján. Ezért fontos, hogy tisztában legyen azzal, hogy az autoskálázási képletek hogyan hatnak a metrikák adataira (minták).

**Minták**

A Batch szolgáltatás rendszeres időközönként mintákat vesz fel a feladat-és erőforrás-mérőszámokból, és elérhetővé teszi azokat az autoscale-képletek számára. Ezeket a mintákat a Batch szolgáltatás 30 másodpercenként rögzíti. A minták rögzítése és a (és az) az autoscale-képletek esetében azonban általában egy késleltetéssel jár. Emellett a különböző tényezők, például a hálózati vagy más infrastrukturális problémák miatt előfordulhat, hogy a mintákat nem rögzíti egy adott intervallum.

**Minta százaléka**

Ha a rendszer átadja a `samplePercent` a `GetSample()` metódusnak, vagy a `GetSamplePercent()` metódust hívja meg, a _százalék_ a Batch szolgáltatás által rögzített összes lehetséges számú minta és az autoskálázási képlet számára elérhető minták számának összehasonlítására utal.

Tekintsük át például egy 10 perces TimeSpan. Mivel a mintákat 30 másodpercenként rögzítik egy 10 perces TimeSpan belül, a Batch által rögzített minták maximális száma 20 minta (2 percenként). A jelentéskészítési mechanizmus és az Azure-beli egyéb problémák miatt azonban csak 15 olyan minta lehet, amely az autoskálázási képlet számára elérhető az olvasáshoz. Tehát például az adott 10 perces időszakra vonatkozóan a képletnek csak a rögzített minták teljes számának 75%-a lehet elérhető.

**GetSample () és mintavételezési tartományok**

Az autoscale-képletek egyre növekednek és csökkennek a készletek &mdash; csomópontok hozzáadásával vagy csomópontok eltávolításával. Mivel a csomópontok pénzbe kerülnek, gondoskodni szeretne arról, hogy a képletek olyan intelligens elemzési módszert használjanak, amely elegendő adatmennyiségen alapul. Ezért javasoljuk, hogy a képletekben egy trend típusú elemzést használjon. Ez a típus egyre növekszik, és az összegyűjtött minták alapján csökkenti a készleteket.

Ehhez használja a `GetSample(interval look-back start, interval look-back end)` a minták vektorának visszaküldéséhez:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Ha a fenti sort kiértékeli a Batch, a több mintát ad vissza az értékek vektora. Például:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

A minták vektorának begyűjtése után a függvények, például a `min()`, a `max()`és a `avg()` függvényt használhatja a begyűjtött tartományból származó hasznos értékek kinyeréséhez.

A további biztonság érdekében kényszerítheti a képletek kiértékelését, ha az adott időszakra vonatkozóan kevesebb mint egy bizonyos minta százalékaránya érhető el. Ha a képlet kiértékelését kényszeríti, a Batch a képlet további kiértékelését utasítja, ha a minták megadott százaléka nem érhető el. Ebben az esetben a készlet méretének módosítása nem történik meg. Ha meg szeretné adni, hogy a kiértékelés sikeres legyen-e a minták százalékában, akkor azt a `GetSample()`harmadik paramétereként kell megadni. Itt meg kell adni a minták 75 százalékának követelményét:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Mivel előfordulhat, hogy a minta rendelkezésre állása késésben van, fontos, hogy mindig olyan időtartományt határozzon meg, amely egy percnél régebbi kezdési időpontot használ. Körülbelül egy percet vesz igénybe, hogy a minták a rendszeren keresztül propagálva legyenek, ezért előfordulhat, hogy a tartomány `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` nem érhető el. Újra használhatja a `GetSample()` százalékos paraméterét egy adott minta százalékos követelményének kényszerítéséhez.

> [!IMPORTANT]
> Azt **javasoljuk** , hogy **ne *csak* az autoskálázási képletekben lévő `GetSample(1)` ne kelljen a rendszerre támaszkodni**. Ennek az az oka, hogy `GetSample(1)` lényegében a Batch szolgáltatásnak szól, "adj nekem az utolsó mintát, függetlenül attól, hogy milyen régen lekérte." Mivel ez csak egyetlen minta, és lehet egy régebbi minta is, nem lehet reprezentatív a legutóbbi feladat vagy erőforrás állapotának nagyobb képére. Ha `GetSample(1)`használ, győződjön meg róla, hogy az egy nagyobb utasítás része, és nem az egyetlen adatpont, amelyről a képlet támaszkodik.
>
>

## <a name="metrics"></a>Mérőszámok

Képletek definiálásakor az erőforrás és a tevékenység mérőszámait is használhatja. A készletben lévő dedikált csomópontok számát a beszerzett és kiértékelt metrikai adatok alapján állíthatja be. Az egyes metrikákkal kapcsolatos további információkért tekintse meg a fenti [változók](#variables) szakaszt.

<table>
  <tr>
    <th>Metrika</th>
    <th>Leírás</th>
  </tr>
  <tr>
    <td><b>Erőforrás</b></td>
    <td><p>Az erőforrás-metrikák a PROCESSZORon, a sávszélességen, a számítási csomópontok memóriahasználat és a csomópontok számán alapulnak.</p>
        <p> Ezek a szolgáltatás által definiált változók a csomópontok számán alapuló módosítások elvégzéséhez hasznosak:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Ezek a szolgáltatás által definiált változók a csomópont-erőforrás-használaton alapuló módosítások elvégzéséhez hasznosak:</p>
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
    <td><p>A tevékenységek mérőszámai a feladatok állapotán alapulnak, például az aktív, a függőben lévő és a befejezett műveletekkel. A következő, szolgáltatás által definiált változók hasznosak lehetnek a készletre vonatkozó méretek végrehajtásához a tevékenységek metrikái alapján:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Autoscale-képlet írása

A fenti összetevőket használó utasítások alapján hozhat létre egy autoskálázási képletet, majd egy teljes képletbe egyesítheti ezeket az utasításokat. Ebben a szakaszban egy olyan automatikus méretezési képletet hozunk létre, amely valamilyen valós skálázási döntést tud végezni.

Először is adjuk meg az új, az új autoskálázási képletre vonatkozó követelményeket. A képletnek a következőket kell tennie:

1. Növelje meg a készlet dedikált számítási csomópontjainak megcélzott számát, ha a CPU-használat magas.
1. Csökkentse a készlet dedikált számítási csomópontjainak megcélzott számát, ha a CPU-használat alacsony.
1. Mindig korlátozza a dedikált csomópontok maximális számát 400-re.
1. A csomópontok számának csökkentésekor ne távolítsa el a feladatokat futtató csomópontokat. Ha szükséges, várjon, amíg a tevékenységek el nem végezték a csomópontok eltávolítását.

A magas CPU-használat során a csomópontok számának növeléséhez határozza meg azt az utasítást, amely egy felhasználó által definiált változót (`$totalDedicatedNodes`) tölt be egy olyan értékkel, amely a dedikált csomópontok aktuális megcélzott számának 110 százalékát adja meg, de csak akkor, ha az utolsó 10 percben az átlagos CPU-használat a 70 százalék felett volt. Ellenkező esetben használja a dedikált csomópontok aktuális számának értékét.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Ha *csökkenteni* szeretné a dedikált csomópontok számát az alacsony CPU-használat során, a képlet következő utasítása ugyanazt a `$totalDedicatedNodes` változót állítja be a dedikált csomópontok aktuálisan megcélzott számának 90 százalékára, ha az elmúlt 60 percben az átlagos CPU-használat 20%-os volt. Ellenkező esetben használja az `$totalDedicatedNodes` aktuális értékét, amelyet a fenti utasításban töltöttünk fel.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Most korlátozza a dedikált számítási csomópontok megcélzott számát legfeljebb 400-re:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

A teljes képlet:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>A Batch SDK-val rendelkező, automéretezést támogató készlet létrehozása

A készlet automatikus skálázása a Batch [SDK](batch-apis-tools.md#azure-accounts-for-batch-development)-k, a Batch [REST API](https://docs.microsoft.com/rest/api/batchservice/) a Batch [PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md)és a [Batch CLI](batch-cli-get-started.md)használatával konfigurálható. Ebben a szakaszban a .NET és a Python esetében is láthat példákat.

### <a name="net"></a>.NET

A .NET-ben engedélyezett automatikus skálázással rendelkező készlet létrehozásához kövesse az alábbi lépéseket:

1. Hozza létre a készletet a [BatchClient. PoolOperations. CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Állítsa a [CloudPool. AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) tulajdonságot `true`értékre.
1. Állítsa be az [CloudPool. AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) tulajdonságot az autoscale képlettel.
1. Választható A [CloudPool. AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) tulajdonság beállítása (az alapértelmezett érték 15 perc).
1. Véglegesítse a készletet a [CloudPool. commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) vagy a [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Az alábbi kódrészlet egy automatikusan méretezhető, .NET-alapú készletet hoz létre. A készlet autoskálázási képlete a dedikált csomópontok megcélzott számát 5 – hétfő értékre állítja, a hét minden más napján pedig 1 értéket. Az [automatikus skálázási időköz](#automatic-scaling-interval) 30 percre van beállítva. A jelen cikkben szereplő többi C# kódrészletben `myBatchClient` a [BatchClient][net_batchclient] osztály megfelelően inicializált példánya.

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
> Amikor létrehoz egy autoscale-kompatibilis készletet, ne a _targetDedicatedNodes_ paramétert vagy a _targetLowPriorityNodes_ paramétert a **CreatePool**hívásához. Ehelyett a **AutoScaleEnabled** és a **AutoScaleFormula** tulajdonságokat kell megadnia a készleten. Ezen tulajdonságok értékei határozzák meg az egyes csomópontok céljának számát. Emellett az automatikus méretezést engedélyező készlet (például a [BatchClient. PoolOperations. ResizePoolAsync][net_poolops_resizepoolasync]) manuális átméretezéséhez először **Tiltsa le** a készlet automatikus skálázását, majd méretezze át.
>
>

#### <a name="automatic-scaling-interval"></a>Automatikus skálázási időköz

Alapértelmezés szerint a Batch szolgáltatás 15 percenként úgy állítja be a készlet méretét, hogy az automatikusan méretezi a képletet. Ez az intervallum a következő készlet tulajdonságainak használatával konfigurálható:

* [CloudPool. AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .net)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

A minimális időköz öt perc, a maximális érték 168 óra. Ha a tartományon kívüli intervallum van megadva, a Batch szolgáltatás hibás kérést ad vissza (400).

> [!NOTE]
> Az automatikus skálázás jelenleg nem arra szolgál, hogy egy percnél rövidebb ideig válaszoljon a változásokra, ehelyett a számítási feladatok futtatásakor fokozatosan módosítania kell a készlet méretét.
>
>

### <a name="python"></a>Python

Ehhez hasonlóan a Python SDK-val is létrehozhat egy autoskálázási képességgel rendelkező készletet:

1. Hozzon létre egy készletet, és határozza meg annak konfigurációját.
1. Adja hozzá a készletet a szolgáltatás ügyfeléhez.
1. Az autoskálázás engedélyezése a készleten egy írásos képlettel.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18; 
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5; 
             $isWorkingWeekdayHour = $workHours && $isWeekday; 
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10), 
                                            pool_enable_auto_scale_options=None, 
                                            custom_headers=None, raw=False)
```

> [!TIP]
> A Python SDK használatával kapcsolatban további példákat talál a [Batch Python](https://github.com/Azure-Samples/batch-python-quickstart) rövid útmutatójában a githubon.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Automatikus skálázás engedélyezése meglévő készleten

Minden batch SDK lehetővé teszi az automatikus skálázást. Például:

* [BatchClient. PoolOperations. EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .net)
* [Automatikus skálázás engedélyezése egy készleten][rest_enableautoscale] (REST API)

Ha egy meglévő készleten engedélyezi az automatikus skálázást, vegye figyelembe a következő szempontokat:

* Ha az automatikus skálázás jelenleg le van tiltva a készleten, amikor az automatikus skálázás engedélyezésére vonatkozó kérést ad ki, meg kell adnia egy érvényes automatikus méretezési képletet a kérelem kiadásakor. Opcionálisan megadhat egy autoscale kiértékelési időközt is. Ha nem ad meg intervallumot, a rendszer az alapértelmezett 15 perces értéket használja.
* Ha az autoskálázás jelenleg engedélyezve van a készleten, megadhat egy autoscale-képletet, egy kiértékelési időközt vagy mindkettőt. Legalább egy ilyen tulajdonságot meg kell adnia.

  * Ha új autoskálázási értékelési időközt ad meg, a rendszer leállítja a meglévő értékelési ütemtervet, és új ütemtervet indít el. Az új ütemezett kezdési idő az az idő, amikor az automatikus skálázás engedélyezésére vonatkozó kérést kiállították.
  * Ha kihagyja az autoskálázási képletet vagy a kiértékelési időközt, a Batch szolgáltatás továbbra is a beállítás aktuális értékét használja.

> [!NOTE]
> Ha az **CreatePool** metódus *targetDedicatedNodes* vagy *targetLowPriorityNodes* paraméterei számára megadott értékeket a készlet .net-ben történő létrehozásakor vagy egy másik nyelvben lévő hasonló paraméterek esetében, akkor a rendszer figyelmen kívül hagyja ezeket az értékeket az automatikus skálázási képlet kiértékelése során.
>
>

Ez C# a kódrészlet a [Batch .net][net_api] -kódtár használatával engedélyezi az automatikus skálázást egy meglévő készleten:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Az autoscale-képlet frissítése

Ha egy meglévő automatikus méretezést használó készleten szeretné frissíteni a képletet, hívja meg a műveletet, és engedélyezze újra az automatikus skálázást az új képlettel. Ha például az automatikus skálázás már engedélyezve van `myexistingpool` a következő .NET-kód végrehajtásakor, az automatikus méretezési képletet a rendszer a `myNewFormula`tartalmára cseréli le.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Az autoskálázási időköz frissítése

Egy meglévő Automatikus méretezéssel rendelkező készlet automatikus méretezési értékelési intervallumának frissítéséhez hívja meg a műveletet, és engedélyezze újra az automatikus skálázást az új intervallummal. Ha például az autoscale kiértékelési intervallumát 60 percre szeretné beállítani egy olyan készlethez, amely már engedélyezve van a .NET-ben:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Az autoscale képlet kiértékelése

Kiértékelheti a képletet, mielőtt alkalmazná egy készletre. Ily módon tesztelheti a képletet, hogy megtudja, hogyan értékeli ki a képleteket, mielőtt a képletet éles környezetben helyezi el.

Egy automatikus méretezési képlet kiértékeléséhez először engedélyeznie kell az automatikus skálázást a készleten érvényes képlettel. Ha olyan készlethez szeretne tesztelni egy képletet, amelyen még nincs engedélyezve az automatikus skálázás, használja az egysoros képletet `$TargetDedicatedNodes = 0`, amikor először engedélyezi az automatikus skálázást. Ezután a következők egyikével értékelje ki a tesztelni kívánt képletet:

* [BatchClient. PoolOperations. EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) vagy [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Ezek a Batch .NET-metódusok megkövetelik egy meglévő készlet AZONOSÍTÓját, valamint egy olyan karakterláncot, amely a kiértékeléshez az autoskálázási képletet tartalmazza.

* [Automatikus skálázási képlet kiértékelése](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Ebben az REST API-kérelemben adja meg a készlet AZONOSÍTÓját az URI-ban, valamint az autoskálázási képletet a *autoScaleFormula* elemben. A művelet válasza tartalmaz minden olyan hibaüzenetet, amely lehet a képlethez köthető.

Ebben a [Batch .net][net_api] -kódrészletben kiértékelünk egy autoskálázási képletet. Ha a készletben nincs engedélyezve az automatikus skálázás, először engedélyezzük.

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

Az ebben a kódrészletben látható képlet sikeres kiértékelése a következőhöz hasonló eredményeket eredményez:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Az autoscale-futtatásokkal kapcsolatos információk beolvasása

Annak érdekében, hogy a képlet a várt módon legyen végrehajtva, javasoljuk, hogy rendszeresen ellenőrizze az automatikus skálázás futtatásának eredményét, hogy a Batch végrehajtsa a készletet. Ehhez szerezze be (vagy frissítse) a készletre mutató hivatkozást, és vizsgálja meg az utolsó méretezési Futtatás tulajdonságait.

A Batch .NET-ben a [CloudPool. AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) tulajdonság több olyan tulajdonsággal rendelkezik, amelyek információt nyújtanak a készleten végrehajtott legújabb automatikus skálázási futtatásról:

* [AutoScaleRun. timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun. Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun. error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

A REST API a [készletre vonatkozó kérés információi](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) a készletre vonatkozó információkat adnak vissza, amely magában foglalja a legújabb automatikus skálázási futtatási információkat a [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) tulajdonságban.

A következő C# kódrészlet a Batch .net-függvénytárat használja az utolsó automatikus skálázási futtatással kapcsolatos információk nyomtatásához a _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Az előző kódrészlet mintájának kimenete:

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

## <a name="example-autoscale-formulas"></a>Példa az autoskálázási képletekre

Nézzük meg néhány képletet, amelyek különböző módszereket mutatnak be a készletben lévő számítási erőforrások mennyiségének módosítására.

### <a name="example-1-time-based-adjustment"></a>1\. példa: időalapú beállítás

Tegyük fel, hogy a készlet méretét a hét napja és a nap időpontja alapján szeretné beállítani. Ebből a példából megtudhatja, hogyan növelheti vagy csökkentheti a készlet csomópontjainak számát.

A képlet először az aktuális időt kapja meg. Ha hétköznap (1-5) és munkaidőn (8 – 6 ÓRAKOR) belül van megadva, a célként megadott készlet mérete 20 csomópont. Ellenkező esetben 10 csomópontra van beállítva.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-2-task-based-adjustment"></a>2\. példa: feladat-alapú beállítás

Ebben a példában a készlet méretét a várólista feladatai száma alapján korrigáljuk. Mind a megjegyzések, mind a sortörések elfogadhatók a képletek karakterláncában.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>3\. példa: párhuzamos feladatok könyvelése

A példa a feladatok száma alapján állítja be a készlet méretét. Ez a képlet a készlethez beállított [MaxTasksPerComputeNode][net_maxtasks] értéket is figyelembe veszi. Ez a megközelítés olyan helyzetekben hasznos, amikor engedélyezve van a [párhuzamos feladatok végrehajtása](batch-parallel-node-tasks.md) a készleten.

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

### <a name="example-4-setting-an-initial-pool-size"></a>4\. példa: a készlet kezdeti méretének beállítása

Ez a példa egy C# olyan kódrészletet mutat be, amely egy olyan autoskálázási képletet mutat be, amely egy kezdeti időszakra vonatkozóan megadott számú csomópontra állítja be a készlet méretét. Ezután a kezdeti időtartam lejárta után a futó és az aktív feladatok száma alapján módosítja a készlet méretét.

A következő kódrészletben szereplő képlet:

* A készlet kezdeti méretének beállítása négy csomópontra.
* A készlet életciklusának első 10 percén belül nem módosítja a készlet méretét.
* 10 perc elteltével az elmúlt 60 percben megszerezheti a futó és az aktív feladatok maximális értékét.
  * Ha mindkét érték 0 (ami azt jelzi, hogy az utolsó 60 percben nem futnak vagy nem voltak aktívak), a készlet mérete 0.
  * Ha bármelyik érték nagyobb nullánál, a rendszer nem végez módosítást.

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

## <a name="next-steps"></a>Következő lépések

* A [Azure batch számítási erőforrások használatának maximalizálása egyidejű csomópont-feladatokkal](batch-parallel-node-tasks.md) : részletes információ arról, hogy miként hajtható végre egyszerre több feladat a készlet számítási csomópontjain. Az automatikus skálázás mellett ez a funkció bizonyos munkaterhelések esetében csökkentheti a feladatok időtartamát, így pénzt takaríthat meg.
* Egy másik hatékonysági emlékeztető esetén győződjön meg arról, hogy a Batch-alkalmazás a legoptimálisabb módon kérdezi le a Batch szolgáltatást. Lásd: [a Azure batch szolgáltatás hatékony lekérdezése](batch-efficient-list-queries.md) , amelyből megtudhatja, hogy miként lehet korlátozni a drótot keresztező adatok mennyiségét, ha egy akár több ezer számítási csomópont vagy feladat állapotát kérdezi le.

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
