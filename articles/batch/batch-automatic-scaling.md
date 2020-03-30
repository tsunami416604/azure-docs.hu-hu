---
title: Számítási csomópontok automatikus méretezése egy Azure Batch-készletben | Microsoft dokumentumok
description: Engedélyezze az automatikus méretezést egy felhőkészleten a készletben lévő számítási csomópontok számának dinamikus módosításához.
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
ms.openlocfilehash: 398b6d9c3fc05a6cf164b4003f57b94ecd6c1972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054017"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Automatikus képlet létrehozása a kötegkészlet számítási csomópontjainak méretezéséhez

Az Azure Batch automatikusan skálázhatja a készleteket a megadott paraméterek alapján. Az automatikus skálázással a Batch dinamikusan adja hozzá a csomópontokat a készlethez a feladatigények növekedésével, és eltávolítja a számítási csomópontokat, ahogy csökkennek. Időt és pénzt takaríthat meg, ha automatikusan módosítja a Batch alkalmazás által használt számítási csomópontok számát.

A számítási csomópontok készletén engedélyezi az automatikus skálázást, ha egy ön által definiált *automatikus skálázási képletet* társít hozzá. A Batch szolgáltatás az automatikus skálázási képlet et használja a számítási feladatok végrehajtásához szükséges számítási csomópontok számának meghatározásához. A számítási csomópontok lehetnek dedikált csomópontok vagy [alacsony prioritású csomópontok.](batch-low-pri-vms.md) A köteg válaszol az időszakosan gyűjtött szolgáltatásmetrikák adataira. A metrikaadatok használatával a Batch a képlet alapján és egy konfigurálható időközönként módosítja a készletben lévő számítási csomópontok számát.

Az automatikus méretezést a készlet létrehozásakor vagy egy meglévő készleten engedélyezheti. Az automatikus skálázásra konfigurált készlet meglévő képletét is módosíthatja. A Batch lehetővé teszi a képletek kiértékelését, mielőtt készletekhez rendelne, és figyelheti az automatikus skálázási futtatások állapotát.

Ez a cikk ismerteti a különböző entitások, amelyek az automatikus skálázási képletek, beleértve a változók, operátorok, műveletek és függvények. Azt tárgyalja, hogyan lehet beszerezni a különböző számítási erőforrás- és tevékenységmetrikák a Batch-en belül. Ezekkel a mérőszámokkal módosíthatja a készlet csomópontszámát az erőforrás-használat és a tevékenység állapota alapján. Ezután bemutatjuk, hogyan hozhat létre egy képletet, és hogyan engedélyezheti az automatikus skálázást egy készleten a Batch REST és a .NET API-k használatával. Végül néhány példaképlettel fejezzük be.

> [!IMPORTANT]
> Batch-fiók létrehozásakor megadhatja a [fiók konfigurációját,](batch-api-basics.md#account)amely meghatározza, hogy a készletek egy Batch szolgáltatás-előfizetésben (alapértelmezett) vagy a felhasználói előfizetésben vannak-e lefoglalva. Ha a Batch-fiókot az alapértelmezett Batch-szolgáltatás konfigurációval hozta létre, akkor a fiók a feldolgozáshoz használható magok maximális számára korlátozódik. A Batch szolgáltatás csak az adott alapkorlátig méretezi a számítási csomópontokat. Emiatt előfordulhat, hogy a Batch szolgáltatás nem éri el az automatikus skálázási képlet által megadott számítási csomópontok célszámát. A fiókkvóták megtekintésével és növelésével kapcsolatos információkért tekintse meg [az Azure Batch-szolgáltatás kvótáinak és](batch-quota-limit.md) korlátozásai.
>
>Ha a felhasználói előfizetés konfigurációjával hozta létre a fiókját, akkor a fiók az előfizetés alapkvótájában osztozik. További információkért lásd [az Azure-előfizetésekre és -szolgáltatásokra vonatkozó korlátozásokat, kvótákat és megkötéseket](../azure-resource-manager/management/azure-subscription-service-limits.md) ismertető témakör [a virtuális gépek korlátaira](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) vonatkozó részét.
>
>

## <a name="automatic-scaling-formulas"></a>Automatikus méretezési képletek

Az automatikus méretezési képlet egy karakterlánc-érték, amelyet egy vagy több utasítást tartalmazó karakterlánc-értékként határoz meg. Az automatikus skálázási képlet egy készlet [autoScaleFormula][rest_autoscaleformula] eleméhez (Batch REST) vagy [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] tulajdonsághoz (Batch .NET) van rendelve. A Batch szolgáltatás a képlet segítségével határozza meg a számítási csomópontok célszámát a készletben a következő feldolgozási időszakhoz. A képletkarakterlánc nem haladhatja meg a 8 KB-ot, legfeljebb 100 pontosvesszőt tartalmazhat, és sortöréseket és megjegyzéseket is tartalmazhat.

Az automatikus skálázási képleteket kötegelt automatikus skálázási "nyelvként" is felhasználhatja. A receptúrautasítások olyan szabad formáns kifejezések, amelyek a szolgáltatás által definiált változókat (a Batch szolgáltatás által meghatározott változókat) és a felhasználó által definiált változókat (a megadott változókat) is tartalmazhatnak. Beépített típusok, operátorok és függvények használatával különböző műveleteket hajthatnak végre ezeken az értékeken. Egy utasítás például a következő formában jelenhet meg:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

A képletek általában több utasítást tartalmaznak, amelyek a korábbi utasításokban kapott értékeken műveleteket hajtanak végre. Például először megkapjuk `variable1`az értékét, majd adjuk át `variable2`egy függvénynek, hogy feltöltsük:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Ezeket az állításokat az automatikus skálázási képlet, hogy eljusson a számítási csomópontok célszáma. Dedikált csomópontok és alacsony prioritású csomópontok mindegyike saját célbeállításokkal rendelkezik, így minden csomóponttípushoz megadhat egy célt. Az automatikus skálázási képlet tartalmazhat célértéket dedikált csomópontokhoz, egy célértéket az alacsony prioritású csomópontokhoz, vagy mindkettőt.

A csomópontok célszáma lehet magasabb, alacsonyabb vagy megegyező, mint az adott típusú csomópontok aktuális száma a készletben. A Batch egy készlet automatikus skálázási képletét egy adott időközönként értékeli ki (lásd az [automatikus méretezési időközöket).](#automatic-scaling-interval) A Batch a készletben lévő egyes csomóponttípusok célszámát az automatikus skálázási képlet által a kiértékelés időpontjában megadott számhoz igazítja.

### <a name="sample-autoscale-formulas"></a>Automatikus skálázási képletek mintája

Az alábbiakban két automatikus skálázási képletet mutatbe, amelyek a legtöbb esetben is módosíthatók. A `startingNumberOfVMs` változók `maxNumberofVMs` és a példa képletek lehet igazítani az Ön igényeinek.

#### <a name="pending-tasks"></a>Függőben lévő feladatok

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

Ezzel az automatikus skálázási képlet, a készlet kezdetben egyetlen virtuális gép jön létre. A `$PendingTasks` metrika határozza meg a futó vagy várólistára helyezett feladatok számát. A képlet megkeresi a függőben lévő tevékenységek átlagos számát `$TargetDedicatedNodes` az elmúlt 180 másodpercben, és ennek megfelelően állítja be a változót. A képlet biztosítja, hogy a dedikált csomópontok célszáma soha ne haladja meg a 25 virtuális gépet. Az új feladatok elküldésekor a készlet automatikusan növekszik. A feladatok befejeződésével a virtuális gépek egyenként válnak szabaddá, és az automatikus skálázási képlet összezsugorítja a készletet.

Ez a képlet skálázza a dedikált csomópontokat, de módosítható az alacsony prioritású csomópontok méretezéséhez is.

#### <a name="preempted-nodes"></a>Preempted csomópontok 

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Ebben a példában egy 25 alacsony prioritású csomóponttal kezdődő készletet hoz létre. Minden alkalommal, amikor egy alacsony prioritású csomópont preempted, akkor helyébe egy dedikált csomópont. Az első példához, `maxNumberofVMs` a változó megakadályozza, hogy a készlet meghaladja a 25 virtuális gépek. Ez a példa akkor hasznos, ha kihasználják az alacsony prioritású virtuális gépek, miközben azt is biztosítja, hogy csak egy meghatározott számú megelőző műveletek fog bekövetkezni a készlet élettartama alatt.

## <a name="variables"></a>Változók

Az automatikus skálázási képletekben **szolgáltatás és** felhasználó **által definiált** változók is használhatók. A szolgáltatás által definiált változók be vannak építve a Batch szolgáltatásba. Egyes szolgáltatásdefiniált változók írási és olvasási, és néhány csak olvasható. A felhasználó által definiált változók a megadott változók. Az előző szakaszban látható példaképletben szolgáltatás `$TargetDedicatedNodes` `$PendingTasks` által definiált változók. Változók `startingNumberOfVMs` `maxNumberofVMs` és a felhasználó által definiált változók.

> [!NOTE]
> A szolgáltatás által definiált változókat mindig dollárjel ($) előzi meg. A felhasználó által definiált változók esetében a dollárjel megadása nem kötelező.
>
>

Az alábbi táblázatok a Batch szolgáltatás által meghatározott írási és írásvédett változókat is mutatják.

Ezeknek a szolgáltatásáltal definiált változóknak az értékeit beszerezheti és beállíthatja a készletben lévő számítási csomópontok számának kezeléséhez:

| Írás-írás szolgáltatás által definiált változók | Leírás |
| --- | --- |
| $TargetDedicatedNodes |A készlet dedikált számítási csomópontjainak célszáma. A dedikált csomópontok száma célként van megadva, mert előfordulhat, hogy a készlet nem mindig éri el a kívánt számú csomópontot. Például ha a dedikált csomópontok célszámát egy automatikus skálázási kiértékelés módosítja, mielőtt a készlet elérte volna a kezdeti célt, akkor előfordulhat, hogy a készlet nem éri el a célt. <br /><br /> Előfordulhat, hogy a Batch-szolgáltatás konfigurációjával létrehozott fiók készlete nem éri el a célt, ha a cél meghaladja a Batch-fiók csomópontját vagy az alapkvótát. Előfordulhat, hogy a Felhasználói előfizetés konfigurációjával létrehozott fiók készlete nem éri el a célt, ha a cél meghaladja az előfizetés megosztott alapkvótáját.|
| $TargetLowPriorityNodes |A készlet alacsony prioritású számítási csomópontjainak célszáma. Az alacsony prioritású csomópontok száma célként van megadva, mert előfordulhat, hogy egy készlet nem mindig éri el a kívánt számú csomópontot. Ha például az alacsony prioritású csomópontok célszámát egy automatikus skálázási kiértékelés módosítja, mielőtt a készlet elérte volna a kezdeti célt, akkor előfordulhat, hogy a készlet nem éri el a célt. Előfordulhat, hogy a készlet nem éri el a célt, ha a cél meghaladja a Batch-fiók csomópontját vagy alapkvótáját. <br /><br /> Az alacsony prioritású számítási csomópontokról további információt a [Kis prioritású virtuális gépek használata a Batch-el című témakörben talál.](batch-low-pri-vms.md) |
| $NodeDeallocationOption |A művelet, amely akkor következik be, amikor a számítási csomópontokat eltávolítják a készletből. Lehetséges értékek:<ul><li>**requeue**- Az alapértelmezett érték. Azonnal leállítja a feladatokat, és visszahelyezi őket a feladatvárólistába, hogy átütemezzék őket. Ez a művelet biztosítja, hogy a csomópontok célszáma a lehető leggyorsabban elérhető legyen, de kevésbé hatékony lehet, mivel minden futó feladat megszakad, és újra kell indítani, elpazarolva a már elvégzett munkát. <li>**--A**feladatok azonnali leállítása és a feladatvárólistából való eltávolítása.<li>**taskcompletion**--Megvárja, amíg a jelenleg futó feladatok befejeződnek, majd eltávolítja a csomópontot a készletből. Ezzel a beállítással elkerülheti a feladatok megszakítását és újbóli várólistára hirdetését, és elpazarolhatja a feladat által végzett munkát. <li>**megtartott adatok**- Várakozás a csomóponton lévő összes helyi feladat által megőrzött adat törlésére, mielőtt eltávolítaná a csomópontot a készletből.</ul> |

> [!NOTE]
> A `$TargetDedicatedNodes` változó a használatával is `$TargetDedicated`megadható. Hasonlóképpen a `$TargetLowPriorityNodes` változó is megadható `$TargetLowPriority`az alias használatával. Ha a képlet mind a teljes mértékben elnevezett változót, mind az aliast beállítja, a teljesen elnevezett változóhoz rendelt érték lesz az elsőbbség.
>
>

Ezeknek a szolgáltatásáltal definiált változóknak az értékét a Batch szolgáltatás metrikáin alapuló korrekcióihoz kaphatja meg:

| Írásvédett szolgáltatás által definiált változók | Leírás |
| --- | --- |
| $CPUPercent |A CPU-használat átlagos százaléka. |
| $WallClockSeconds |A felhasznált másodpercek száma. |
| $MemoryBytes |A felhasznált megabájtok átlagos száma. |
| $DiskBytes |A helyi lemezeken használt gigabájtok átlagos száma. |
| $DiskReadBytes |Az olvasott bájtok száma. |
| $DiskWriteBytes |A megírt bájtok száma. |
| $DiskReadOps |Az olvasási lemez en végrehajtott műveletek száma. |
| $DiskWriteOps |Az írási lemez végrehajtott műveleteinek száma. |
| $NetworkInBytes |A bejövő bájtok száma. |
| $NetworkOutBytes |A kimenő bájtok száma. |
| $SampleNodeCount |A számítási csomópontok száma. |
| $ActiveTasks |A végrehajtandó, de még végrehajtásra kész feladatok száma. A $ActiveTasks száma tartalmazza az aktív állapotban lévő és a függőségeket teljesített tevékenységeket. Minden olyan tevékenység, amely aktív állapotban van, de amelynek függőségei nem teljesültek, ki van zárva a $ActiveTasks száma alól. Többpéldányos feladat esetén $ActiveTasks a feladatra beállított példányok számát tartalmazza.|
| $RunningTasks |A futó állapotban lévő feladatok száma. |
| $PendingTasks |A $ActiveTasks és a $RunningTasks összege. |
| $SucceededTasks |A sikeresen befejezett feladatok száma. |
| $FailedTasks |A sikertelen tevékenységek száma. |
| $CurrentDedicatedNodes |A dedikált számítási csomópontok aktuális száma. |
| $CurrentLowPriorityNodes |Az alacsony prioritású számítási csomópontok aktuális száma, beleértve a preempted csomópontokat is. |
| $PreemptedNodeCount | A készletben lévő, preempted állapotban lévő csomópontok száma. |

> [!TIP]
> Az előző táblában megjelenő írásvédett, szolgáltatásáltal definiált változók olyan *objektumok,* amelyek különböző módszereket biztosítanak az egyes adatok eléréséhez. További információ: [A mintaadatok beszerzése](#getsampledata) a cikk későbbi részében.
>
>

## <a name="types"></a>Típusok

Ezeket a típusokat egy képlet támogatja:

* double
* duplaVec
* doubleVecList között
* sztring
* időbélyeg – az időbélyeg egy összetett struktúra, amely a következő tagokat tartalmazza:

  * év
  * hónap (1-12)
  * nap (1-31)
  * hétköznap (szám formátumban; például 1 hétfőesetén)
  * óra (24 órás számformátumban; például 13 13 óra)
  * perc (00-59)
  * második (00-59)
* időintervallum

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

Ezek a műveletek az előző szakaszban felsorolt típusokon engedélyezettek.

| Művelet | Támogatott operátorok | Eredmény típusa |
| --- | --- | --- |
| dupla *kezelő* |+, -, *, / |double |
| kettős *operátori* időintervallum |* |időintervallum |
| doubleVec *kezelő* dupla |+, -, *, / |duplaVec |
| doubleVec *kezelő* doubleVec |+, -, *, / |duplaVec |
| időintervallum *operátor* kettős |*, / |időintervallum |
| időintervallum *operátori* időintervallum |+, - |időintervallum |
| időintervallum *operátor* időbélyege |+ |időbélyeg |
| időbélyeg *operator* operátoridő-intervallum |+ |időbélyeg |
| időbélyeg *operátor* időbélyeg |- |időintervallum |
| *kezelő*kettős |-, ! |double |
| *operátori*időintervallum |- |időintervallum |
| dupla *kezelő* |<, <=, ==, >=, >, != |double |
| *karakterlánc-operátori* karakterlánc |<, <=, ==, >=, >, != |double |
| időbélyeg *operátor* időbélyeg |<, <=, ==, >=, >, != |double |
| időintervallum *operátori* időintervallum |<, <=, ==, >=, >, != |double |
| dupla *kezelő* |&&, &#124;&#124; |double |

Ha egy harmadkezelővel (`double ? statement1 : statement2`) tesztel, a nem nulla **igaz**, a nulla pedig **hamis**.

## <a name="functions"></a>Functions
Ezek az előre definiált **függvények** az automatikus méretezési képletek definiálásához használhatók.

| Függvény | Visszatérés típusa | Leírás |
| --- | --- | --- |
| átlag(doubleVecList) |double |A doubleVecList összes értékének átlagértékét adja eredményül. |
| len(doubleVecList) |double |A doubleVecList listából létrehozott vektor hosszát adja eredményül. |
| lg(dupla) |double |A dupla 2. |
| lg(doubleVecList) |duplaVec |A doubleVecList 2 összetevő-bölcs naplóalapját adja eredményül. A paraméterhez explicit módon át kell adni egy vec(double) paramétert. Ellenkező esetben a kettős lg(dupla) verziót feltételezi. |
| Ln(dupla) |double |A kettős természetes naplóját adja eredményül. |
| ln(doubleVecList) |duplaVec |A kettős természetes naplóját adja eredményül. |
| log(dupla) |double |A dupla 10-es naplóbázisát adja eredményül. |
| log(doubleVecList) |duplaVec |A doubleVecList 10 összetevő-bölcs naplóalapját adja eredményül. Az egykettős paraméterhez explicit módon át kell adni egy vec(double) paramétert. Ellenkező esetben a kettős log(double) verziót kell feltételezni. |
| max(doubleVecList) |double |A doubleVecList maximális értékét adja eredményül. |
| min(doubleVecList) |double |A doubleVecList minimális értékét adja eredményül. |
| norm(doubleVecList) |double |A doubleVecList-ből létrehozott vektor kétnormát adja eredményül. |
| percentilis(doubleVec v, dupla p) |double |A vektor v percentilis elemét adja eredményül. |
| rand() |double |0,0 és 1,0 közötti véletlenszerű értéket ad eredményül. |
| tartomány(doubleVecList) |double |A doubleVecList min és max értéke közötti különbséget adja eredményül. |
| std(doubleVecList) |double |A doubleVecList értékeinek mintasztinációját számítja ki. |
| megállás() | |Leállítja az automatikus skálázási kifejezés kiértékelését. |
| összeg(doubleVecList) |double |A doubleVecList összes összetevőjének összegét adja eredményül. |
| time(string dateTime="") |időbélyeg |Az aktuális idő időbélyegzőjét adja eredményül, ha nem adnak át paramétereket, vagy a dateTime karakterlánc időbélyegzőjét, ha az átvan adva. A támogatott dateTime formátumok a W3C-DTF és az RFC 1123. |
| val(doubleVec v, dupla i) |double |Az i. helyen lévő elem értékét adja eredményül az v vektorban, nulla kezdő indexszel. |

Az előző táblázatban ismertetett függvények némelyike argumentumként elfogadhatja a listát. A vesszővel tagolt lista a *kettős* és a *kettősVec*bármilyen kombinációja. Példa:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

A *doubleVecList* érték értékelés előtt egyetlen *doubleVec-re* alakul át. Ha például `v = [1,2,3]`a `avg(v)` , akkor `avg(1,2,3)`a hívás egyenértékű a hívásnak. A `avg(v, 7)` hívás egyenértékű `avg(1,2,3,7)`a hívásokkal.

## <a name="obtain-sample-data"></a><a name="getsampledata"></a>Mintaadatok beszerzése

Az automatikus skálázási képletek a Batch szolgáltatás által biztosított metrikaadatokra (mintákra) hatnak. A képlet a szolgáltatástól kapott értékek alapján növeli vagy csökkenti a készlet méretét. A korábban leírt szolgáltatásáltal definiált változók olyan objektumok, amelyek különböző módszereket biztosítanak az objektumhoz társított adatok eléréséhez. A következő kifejezés például a PROCESSZOR-használat utolsó öt percének lekérésére vonatkozó kérést jeleníti meg:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Módszer | Leírás |
| --- | --- |
| Beszerezniaminta() |A `GetSample()` metódus adatminták vektot ad vissza.<br/><br/>A minta 30 másodpercnyi metrikaadat. Más szóval a mintákat 30 másodpercenként kell beszerezni. De amint az alábbiakban megjegyeztük, a minta gyűjtése és a képlet rendelkezésre állása között késés van. Mint ilyen, egy adott időszakra vonatkozóan nem minden minta érhető el egy képlettel való értékelésre.<ul><li>`doubleVec GetSample(double count)`<br/>Megadja a legutóbbi összegyűjtött mintákból beszerző minták számát.<br/><br/>`GetSample(1)`az utolsó rendelkezésre álló mintát adja vissza. Az olyan `$CPUPercent`mérőszámok esetében azonban, mint a , ezt nem szabad használni, mert nem lehet tudni, hogy *mikor* gyűjtötték a mintát. Lehet, hogy a közelmúltban, vagy azért, mert a rendszer problémák, lehet, hogy sokkal régebbi. Ilyen esetekben jobb az alábbi időintervallumot használni.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>A mintaadatok gyűjtésének időkeretét adja meg. Opcionálisan azt is meghatározza, hogy a minták százalékos arányának kell rendelkezésre állnia a kért időkereten belül.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`20 mintát adna vissza, ha az utolsó 10 perc összes minta jelen van a CPUPercent előzményeiközött. Ha azonban a történelem utolsó perce nem áll rendelkezésre, csak 18 mintát ad vissza. Ebben az esetben:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`sikertelen lenne, mert a mintáknak csak 90 százaléka áll rendelkezésre.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`sikerrel járna.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Megadja az adatgyűjtés időkeretét, amely nek mind a kezdési, mind a befejezési időpontja van.<br/><br/>A minta gyűjtése és a képlet rendelkezésre állása között késés van. Vegye figyelembe ezt a `GetSample` késleltetést a módszer használatakor. Lásd `GetSamplePercent` alább. |
| Bekési mintaidőszak() |A korábbi mintaadatkészletben vett minták időszakát adja eredményül. |
| Darabszám() |A metrikaelőzményekben lévő minták teljes számát adja eredményül. |
| Előzményekkezdete() |A metrika legrégebbi rendelkezésre álló adatmintájának időbélyegzőjét adja eredményül. |
| GetSamplePercent() |Az adott időintervallumban rendelkezésre álló minták százalékos arányát adja eredményül. Példa:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Mivel `GetSample` a metódus sikertelen, ha a visszaadott minták százalékos aránya kisebb, mint a `samplePercent` megadott, a `GetSamplePercent` módszer segítségével először ellenőrizze. Ezután egy alternatív műveletet hajthat végre, ha nincs elegendő minta, az automatikus skálázás kiértékelése leállítása nélkül. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Minták, mintaszázalék és a *GetSample()* metódus
Az automatikus skálázási képlet alapvető művelete a tevékenység- és erőforrás-metrikaadatok beszerzése, majd a készlet méretének módosítása az adatok alapján. Ezért fontos, hogy világosan megértsük, hogyan hatnak az automatikus skálázási képletek a metrikaadatokkal (mintákkal).

**Minták**

A Batch szolgáltatás rendszeres időközönként mintát vesz a tevékenység- és erőforrás-metrikákból, és elérhetővé teszi azokat az automatikus skálázási képletek számára. Ezeket a mintákat a Batch szolgáltatás 30 másodpercenként rögzíti. Azonban általában van egy késleltetés között, amikor ezeket a mintákat rögzítették, és amikor elérhetővé tették (és lehet olvasni) az automatikus skálázási képletek. Emellett különböző tényezők, például hálózati vagy egyéb infrastrukturális problémák miatt a mintákat nem lehet egy adott időszakban rögzíteni.

**Minta százaléka**

Amikor `samplePercent` a `GetSample()` metódusnak adják át, vagy a `GetSamplePercent()` metódus neve, a _százalék_ a Batch szolgáltatás által rögzített minták teljes lehetséges száma és az automatikus skálázási képlet számára rendelkezésre álló minták száma közötti összehasonlításra utal.

Tekintsünk példaként egy 10 perces időtartamot. Mivel a mintákat 30 másodpercenként, 10 perces időtartamon belül rögzítik, a Batch által rögzített minták maximális száma 20 minta (percenként 2). Azonban a jelentéskészítési mechanizmus és az Azure-on belüli egyéb problémák belső késése miatt előfordulhat, hogy csak 15 minta érhető el az automatikus skálázási képlet olvasásához. Így például, hogy a 10 perces időszak, csak 75%-a rögzített minták száma lehet elérhető a képlet.

**GetSample() és mintatartományok**

Az automatikus skálázási képletek növekedni fognak, &mdash; és a készletek csomópontok hozzáadása vagy csomópontok eltávolítása lesznek. Mivel a csomópontok pénzbe kerülnek, biztosítani szeretné, hogy a képletek olyan intelligens elemzési módszert használjanak, amely elegendő adaton alapul. Ezért azt javasoljuk, hogy a képletekben használjon felkapott típusú elemzést. Ez a típus növekszik, és összezsugorodik a medencék alapján számos összegyűjtött mintákat.

Ehhez használja `GetSample(interval look-back start, interval look-back end)` a minták vektora:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Ha a fenti sort kötegtel értékeli ki, a minták egy tartományát adja vissza értékvektorként. Példa:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Miután összegyűjtötte a minták vektorát, `min()` `max()`használhatja `avg()` az olyan függvényeket, mint a , és értelmes értékeket nyerhet az összegyűjtött tartományból.

A nagyobb biztonság érdekében kényszerítheti a képlet kiértékelését, ha egy adott időszakban egy bizonyos mintaszázaléknál kevesebb áll rendelkezésre. Ha egy képlet kiértékeléssikertelensé kényszerít, utasítja a Batch-et, hogy szüntesse meg a képlet további kiértékelését, ha a minták megadott százaléka nem érhető el. Ebben az esetben a készlet mérete nem változik. A minták szükséges százalékának megadásához adja meg a próbaidőszak `GetSample()`ot, adja meg a következőhöz szükséges harmadik paramétert. Itt a minták 75 százalékának követelménye van megadva:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Mivel a minta rendelkezésre állása késhet, fontos, hogy mindig adjon meg egy időtartományt egy percnél régebbi visszatekintési kezdési idővel. Körülbelül egy percet vesz igénybe, amíg a minták terjednek a rendszeren keresztül, így előfordulhat, hogy a tartományban `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` lévő minták nem állnak rendelkezésre. Ismét használhatja `GetSample()` a százalékos paramétert egy adott mintaszázalékos követelmény kényszerítésére.

> [!IMPORTANT]
> **Javasoljuk,** hogy **ne csak *only* az `GetSample(1)` automatikus skálázási képletekben támaszkodjon.** Ez azért `GetSample(1)` van, mert lényegében azt mondja, hogy a Batch szolgáltatás, "Add nekem az utolsó minta van, nem számít, milyen régen letölteni." Mivel ez csak egy minta, és lehet, hogy egy régebbi minta, lehet, hogy nem reprezentatív a nagyobb képet a legutóbbi tevékenység vagy erőforrás állapota. Ha a `GetSample(1)`programot használja, győződjön meg arról, hogy az egy nagyobb utasítás része, és nem az egyetlen adatpont, amelyen a képlet támaszkodik.
>
>

## <a name="metrics"></a>Mérőszámok

Képlet definiálásakor erőforrás- és tevékenységmutatókat is használhat. A készletben lévő dedikált csomópontok célszámát a beszerzett és kiértékelt metrikák adatai alapján módosíthatja. Az egyes mutatókkal kapcsolatos további információkért tekintse meg a fenti [Változók](#variables) című szakaszt.

<table>
  <tr>
    <th>Metrika</th>
    <th>Leírás</th>
  </tr>
  <tr>
    <td><b>Erőforrás</b></td>
    <td><p>Az erőforrás-metrikák a processzoron, a sávszélességen, a számítási csomópontok memóriahasználatán és a csomópontok számán alapulnak.</p>
        <p> Ezek a szolgáltatás által definiált változók a csomópontok száma alapján történő korrekciók elvégzéséhez hasznosak:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Ezek a szolgáltatás által definiált változók a csomóponterőforrás-használaton alapuló korrekciók elvégzéséhez hasznosak:</p>
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
    <td><p>A feladatmetrikák a feladatok (például aktív, függőben és befejezett) állapotán alapulnak. A következő szolgáltatásáltal definiált változók hasznosak a készletméret-módosítások elvégzéséhez a feladatmetrikák alapján:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Automatikus skálázási képlet írása

Automatikus skálázási képletet hozhat létre a fenti összetevőket használó utasítások kialakításával, majd ezeket az állításokat egy teljes képletbe egyesítheti. Ebben a szakaszban hozzunk létre egy példa automatikus skálázási képlet, amely képes végrehajtani néhány valós skálázási döntéseket.

Először határozd meg az új automatikus skálázási képlet követelményeit. A képletnek:

1. Növelje a dedikált számítási csomópontok célszámát egy készletben, ha a CPU-használat magas.
1. Csökkentse a dedikált számítási csomópontok célszámát egy készletben, ha a PROCESSZOR-használat alacsony.
1. Mindig korlátozza a dedikált csomópontok maximális számát 400-ra.
1. A csomópontok számának csökkentésekor ne távolítsa el a feladatokat futtató csomópontokat; ha szükséges, várjon, amíg a feladatok befejeződnek a csomópontok eltávolításához.

A csomópontok számának növeléséhez a magas CPU-használat során adja meg azt az`$totalDedicatedNodes`utasítást, amely feltöltegy felhasználó által definiált változót ( ) a dedikált csomópontok aktuális célszámának 110 százalékával, de csak akkor, ha az elmúlt 10 perc minimális átlagos CPU-használata 70 százalék felett volt. Ellenkező esetben használja a dedikált csomópontok aktuális számának értékét.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

A dedikált csomópontok számának *csökkentése* alacsony CPU-használat során, a képletünk következő utasítása ugyanazt a változót `$totalDedicatedNodes` állítja a dedikált csomópontok aktuális célszámának 90 százalékára, ha az elmúlt 60 perc átlagos CPU-használata 20 százalék alatt volt. Ellenkező esetben használja a `$totalDedicatedNodes` fenti kimutatásban szereplő aktuális értéket.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Most korlátozza a dedikált számítási csomópontok célszámát legfeljebb 400-ra:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Itt a teljes képlet:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Automatikus skálázási készlet létrehozása kötegelt SDK-kkal

A készlet automatikus skálázása konfigurálható bármelyik [Batch SDK-val,](batch-apis-tools.md#azure-accounts-for-batch-development)a [Batch REST API Batch](https://docs.microsoft.com/rest/api/batchservice/) [PowerShell-parancsmagokkal](batch-powershell-cmdlets-get-started.md)és a [Batch CLI-vel.](batch-cli-get-started.md) Ebben a szakaszban a .NET és a Python példákat is láthat.

### <a name="net"></a>.NET

Ha olyan készletet szeretne létrehozni, amelyben a .NET-ben engedélyezve van az automatikus skálázás, kövesse az alábbi lépéseket:

1. Hozza létre a készletet a [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool)segítségével.
1. Állítsa a [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) tulajdonságot a értékre. `true`
1. Állítsa be a [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) tulajdonságot az automatikus skálázási képlettel.
1. (Nem kötelező) Állítsa be a [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) tulajdonságot (az alapértelmezett érték 15 perc).
1. A készlet véglegesítése a [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) vagy [commitasync segítségével.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync)

A következő kódrészlet automatikus skálázási kompatibilis készletet hoz létre a .NET-ben. A készlet automatikus skálázási képlete a dedikált csomópontok célszámát hétfőnként 5-re, a hét minden második napján pedig 1-re állítja. Az [automatikus méretezési időköz](#automatic-scaling-interval) 30 percre van állítva. Ebben és a cikkben `myBatchClient` szereplő többi C# kódrészletben a [BatchClient][net_batchclient] osztály megfelelően inicializált példánya.

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
> Automatikus skálázás-kompatibilis készlet létrehozásakor ne adja meg a _targetDedicatedNodes_ vagy a _targetLowPriorityNodes_ paramétert a **CreatePool**hívásához. Ehelyett adja meg az **AutoScaleEnabled** és az **AutoScaleFormula** tulajdonságokat a készletben. Ezeknek a tulajdonságoknak az értékei határozzák meg az egyes csomóponttípusok célszámát. Emellett manuálisan átméretezheti az automatikus skálázást támogató készletet (például [a BatchClient.PoolOperations.ResizePoolAsync segítségével),][net_poolops_resizepoolasync]először **tiltsa le** az automatikus méretezést a készleten, majd méretezze át.
>
>

#### <a name="automatic-scaling-interval"></a>Automatikus méretezési időköz

Alapértelmezés szerint a Batch szolgáltatás 15 percenként módosítja a készlet méretét az automatikus skálázási képletnek megfelelően. Ez az időköz a következő készlettulajdonságok használatával konfigurálható:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (köteg .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

A minimális időköz öt perc, a maximális pedig 168 óra. Ha a tartományon kívül esik egy időköz, a Batch szolgáltatás hibás kérés (400) hibát ad vissza.

> [!NOTE]
> Automatikus skálázás jelenleg nem célja, hogy reagáljon a változásokra kevesebb, mint egy perc alatt, hanem célja, hogy a készlet méretét fokozatosan, a számítási feladatok futtatásakor.
>
>

### <a name="python"></a>Python

Hasonlóképpen, a Python SDK-val automatikus skálázás-kompatibilis készletet hozhat el:

1. Hozzon létre egy készletet, és adja meg annak konfigurációját.
1. Adja hozzá a készletet a szolgáltatásügyfélhez.
1. Engedélyezze az automatikus skálázást a készleten egy írott képlettel.

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
> További példák a Python SDK használatával a [GitHub Batch Python gyorsindítási tárházában](https://github.com/Azure-Samples/batch-python-quickstart) található.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Automatikus skálázás engedélyezése meglévő készleten

Minden kötegelt SDK lehetővé teszi az automatikus skálázást. Példa:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (köteg .NET)
* [Automatikus méretezés engedélyezése készleten][rest_enableautoscale] (REST API)

Ha engedélyezi az automatikus skálázást egy meglévő készleten, tartsa szem előtt a következő pontokat:

* Ha az automatikus skálázás jelenleg le van tiltva a készleten, amikor kiadja az automatikus skálázás engedélyezésére vonatkozó kérelmet, meg kell adnia egy érvényes automatikus skálázási képletet a kérelem kiadásakor. Megadhat egy automatikus skálázási kiértékelési időközt. Ha nem ad meg intervallumot, a rendszer az alapértelmezett 15 perces értéket használja.
* Ha az automatikus skálázás jelenleg engedélyezve van a készletben, megadhat egy automatikus skálázási képletet, egy kiértékelési időközt vagy mindkettőt. Ezen tulajdonságok közül legalább egyet meg kell adnia.

  * Ha új automatikus skálázási kiértékelési időközt ad meg, akkor a meglévő értékelési ütemezés leáll, és új ütemezés indul el. Az új ütemezés kezdési időpontja az az időpont, amikor az automatikus skálázás engedélyezésére irányuló kérés kiadásra került.
  * Ha kihagyja az automatikus skálázási képletet vagy a kiértékelési időközt, a Batch szolgáltatás továbbra is a beállítás aktuális értékét használja.

> [!NOTE]
> Ha a **createPool** metódus *targetDedicatedNodes* vagy *targetLowPriorityNodes* paramétereinek értékeit adta meg a készlet .NET-ben történő létrehozásakor, vagy más nyelven lévő összehasonlítható paraméterekhez, akkor ezeket az értékeket a rendszer figyelmen kívül hagyja az automatikus skálázási képlet kiértékelésekor.
>
>

Ez a C# kódrészlet a [Batch .NET][net_api] függvénytár segítségével engedélyezi az automatikus skálázást egy meglévő készleten:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Automatikus skálázási képlet frissítése

Ha egy meglévő automatikus skálázást támogató készleten szeretné frissíteni a képletet, hívja meg a műveletet, hogy az új képlettel újra engedélyezze az automatikus skálázást. Ha például az automatikus skálázás már be van kapcsolva `myexistingpool` a következő .NET kód `myNewFormula`végrehajtásakor, az automatikus skálázási képlet et a program lecseréli a tartalmára.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Az automatikus skálázási időköz frissítése

Egy meglévő automatikus skálázás-kompatibilis készlet automatikus skálázási kiértékelési időközének frissítéséhez hívja meg a műveletet, hogy az új időközvel újra engedélyezze az automatikus skálázást. Ha például az automatikus skálázás kiértékelési időközét 60 percre szeretné állítani egy olyan készlet esetében, amely már automatikus skálázást engedélyezve van a .NET-ben:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Automatikus skálázási képlet kiértékelése

A képletet kiértékelheti, mielőtt egy készletre alkalmazna. Ily módon tesztelheti a képletet, hogy lássa, hogyan értékelik az utasítások, mielőtt a képletet éles környezetbe helyezne.

Automatikus skálázási képlet kiértékeléséhez először engedélyeznie kell az automatikus skálázást a készleten érvényes képlettel. Ha olyan készleten szeretne tesztelni egy képletet, amelyen még `$TargetDedicatedNodes = 0` nincs engedélyezve az automatikus skálázás, használja az egysoros képletet, amikor először engedélyezi az automatikus skálázást. Ezután az alábbiak egyikével kiértékelheti a tesztelni kívánt képletet:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) vagy [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Ezek a Batch .NET módszerek kiértékeléséhez egy meglévő készlet és egy automatikus skálázási képletet tartalmazó karakterlánc azonosítójára van szükség.

* [Automatikus méretezési képlet kiértékelése](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Ebben a REST API-kérelemben adja meg a készletazonosítót az URI-ban, és az automatikus skálázási képletet a kérelemtörzs *autoScaleFormula* elemében. A művelet válasza tartalmazza a képlettel kapcsolatos hibainformációkat.

Ebben [a Batch .NET][net_api] kódrészletben kiértékelünk egy automatikus skálázási képletet. Ha a készlet nem rendelkezik automatikus skálázás engedélyezve van, először engedélyezzük.

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

## <a name="get-information-about-autoscale-runs"></a>Információ az automatikus skálázási futtatásokról

Annak érdekében, hogy a képlet a várt módon működjön, azt javasoljuk, hogy rendszeresen ellenőrizze az automatikus skálázási futtatások eredményeit, amelyeket a Batch hajt végre a készleten. Ehhez szerezzen (vagy frissítse) a készletre mutató hivatkozást, és vizsgálja meg az utolsó automatikus skálázási futtatás tulajdonságait.

A Batch .NET-ben a [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) tulajdonság számos tulajdonsággal rendelkezik, amelyek információt nyújtanak a készleten végrehajtott legutóbbi automatikus méretezési futtatásról:

* [AutoScaleRun.Időbélyeg](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [Automatikus scalerun.Eredmények](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [Automatikus scalerun.Hiba](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

A REST API-ban a [get információt egy készlet](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) kérelem akészlet, amely tartalmazza a legújabb automatikus méretezés futtatási információkat az [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) tulajdonság.

A következő C# kódrészlet a Batch .NET könyvtár segítségével nyomtatja ki a _myPool_készleten futó utolsó automatikus skálázásadatait:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Az előző kódrészlet mintakimenete:

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

## <a name="example-autoscale-formulas"></a>Példa automatikus skálázási képletekre

Nézzünk meg néhány képletet, amelyek különböző módokon jelenítik meg a készletben lévő számítási erőforrások mennyiségét.

### <a name="example-1-time-based-adjustment"></a>1. példa: Időalapú korrekció

Tegyük fel, hogy a hét napja és a napszak alapján szeretné módosítani a készlet méretét. Ez a példa bemutatja, hogyan növelheti vagy csökkentheti a csomópontok számát a készletben ennek megfelelően.

A képlet először az aktuális időt kapja meg. Ha hétköznap van (1-5) és munkaidőn belül (8:00 és 18:00 óra között), a célkészlet mérete 20 csomópontra van állítva. Ellenkező esetben 10 csomópontra van állítva.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```
`$curTime`beállítható úgy, hogy tükrözze a `time()` helyi időzónát, ha hozzáadja a termékhez `TimeZoneInterval_Hour` és az UTC eltoláshoz. Például a `$curTime = time() + (-6 * TimeInterval_Hour);` hegyi nyári időszámítás (MDT) használata. Ne feledje, hogy az eltolást a nyári időszámítás kezdetén és végén kell módosítani (ha van ilyen).

### <a name="example-2-task-based-adjustment"></a>2. példa: Feladatalapú korrekció

Ebben a példában a készlet mérete a várólistában lévő feladatok száma alapján módosul. A megjegyzések és a sortörések egyaránt elfogadhatók a képletkarakterláncokban.

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

### <a name="example-3-accounting-for-parallel-tasks"></a>3. példa: Párhuzamos feladatok számlázása

Ez a példa a tevékenységek száma alapján módosítja a készlet méretét. Ez a képlet figyelembe veszi a készlethez beállított [MaxTasksPerComputeNode][net_maxtasks] értéket is. Ez a megközelítés olyan helyzetekben hasznos, ahol [a párhuzamos feladat-végrehajtás](batch-parallel-node-tasks.md) engedélyezve van a készleten.

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

### <a name="example-4-setting-an-initial-pool-size"></a>4. példa: Kezdeti készletméret beállítása

Ebben a példában egy C# kódrészlet egy automatikus skálázási képlet, amely beállítja a készlet méretét egy adott számú csomópont egy kezdeti időszakban. Ezután a kezdeti időszak letelte után módosítja a készlet méretét a futó és aktív feladatok száma alapján.

A képlet a következő kódrészletben:

* A kezdeti készletméretet négy csomópontra állítja.
* Nem módosítja a készlet méretét a készlet életciklusának első 10 percében.
* 10 perc elteltével megszerzi a futó és aktív feladatok maximális értékét az elmúlt 60 percben.
  * Ha mindkét érték 0 (ami azt jelzi, hogy az elmúlt 60 percben egyetlen feladat sem futott vagy volt aktív), a készlet mérete 0-ra van állítva.
  * Ha bármelyik érték nagyobb nullánál, a függvény nem történik változás.

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

* [Maximalizálja az Azure Batch számítási erőforrás-használatát az egyidejű csomópontfeladatokkal,](batch-parallel-node-tasks.md) amelyek részletesen ismertetik, hogyan hajthat végre egyszerre több feladatot a készlet számítási csomópontjain. Az automatikus skálázás mellett ez a funkció segíthet csökkenteni a feladatok időtartamát egyes számítási feladatok, pénzt takarít meg.
* Egy másik hatékonyságnövelő, győződjön meg arról, hogy a Batch alkalmazás lekérdezi a Batch szolgáltatás a legoptimálisabb módon. Tekintse [meg az Azure Batch-szolgáltatás hatékony lekérdezése című témakört,](batch-efficient-list-queries.md) amelyből megtudhatja, hogyan korlátozhatja a vezetéket átlépő adatok mennyiségét, amikor potenciálisan több ezer számítási csomópont vagy feladat állapotát kérdezi le.

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
