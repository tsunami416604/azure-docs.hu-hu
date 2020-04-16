---
title: A Spark-feladatok optimalizálása az Azure Synapse Analytics teljesítményéhez
description: Ez a cikk bemutatja az Apache Spark ot az Azure Synapse Analytics szolgáltatásban, és a különböző fogalmakat.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 6ffe7f3d9faf82c892975e9ffa03b383d3610c36
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424621"
---
# <a name="optimize-apache-spark-jobs-preview-in-azure-synapse-analytics"></a>Apache Spark-feladatok optimalizálása (előzetes verzió) az Azure Synapse Analytics szolgáltatásban

Ismerje meg, hogyan optimalizálhatja az [Apache Spark](https://spark.apache.org/) fürtkonfigurációját az adott számítási feladathoz.  A leggyakoribb kihívás a memórianyomás, a nem megfelelő konfigurációk (különösen a nem megfelelő méretű végrehajtók), a hosszú ideig futó műveletek és a descartes-i műveleteket eredményező feladatok miatt. Felgyorsíthatja a feladatokat a megfelelő gyorsítótárazással, és lehetővé teheti az [adatok elferdítését.](#optimize-joins-and-shuffles) A legjobb teljesítmény érdekében figyelje és tekintse át a hosszú ideig futó és erőforrás-igényes Spark-feladat-végrehajtásokat.

A következő szakaszok ismertetik a Spark-feladat-optimalizálásés -javaslatok közös leírását.

## <a name="choose-the-data-abstraction"></a>Az adatasztrakció kiválasztása

A korábbi Spark-verziók RDD-ket használnak az absztrakt adatokhoz, a Spark 1.3 és az 1.6 pedig a DataFrames-et és az Adathalmazokat. Tekintsük a következő relatív érdemeket:

* **Adatkeretek**
  * A legjobb választás a legtöbb helyzetben.
  * A Catalyst-on keresztül biztosítja a lekérdezés optimalizálását.
  * Teljes stádiumú kódgenerálás.
  * Közvetlen memória-hozzáférés.
  * Alacsony szemétgyűjtés (GC) rezsi.
  * Nem olyan fejlesztőbarát, mint a Adatkészletek, mivel nincsenek fordítási idejű ellenőrzések vagy tartományi objektumok programozása.
* **Adatkészletek**
  * Jó összetett ETL-folyamatokban, ahol a teljesítményhatás elfogadható.
  * Nem jó az összesítésekben, ahol a teljesítményre gyakorolt hatás jelentős lehet.
  * A Catalyst-on keresztül biztosítja a lekérdezés optimalizálását.
  * Fejlesztőbarát tartományi objektumprogramozás és fordítási idejű ellenőrzések biztosításával.
  * Szerializálási/deszerializálási többletterheléshozzáadása.
  * Magas GC felső.
  * Megtöri az egész szakaszban lévő kódgenerálást.
* **RDD-k**
  * Nem kell RDD-ket használnia, kivéve, ha új egyéni RDD-t kell készítenie.
  * Nincs lekérdezésoptimalizálás a Catalyst-on keresztül.
  * Nincs teljes stádiumú kódgenerálás.
  * Magas GC felső.
  * A Spark 1.x örökölt API-kat kell használnia.

## <a name="use-optimal-data-format"></a>Optimális adatformátum használata

A Spark számos formátumot támogat, például a csv, a json, az xml, a parketta, az ork és az avro. A Spark kiterjeszthető a külső adatforrásokkal rendelkező formátumok támogatására - további információkért lásd: [Apache Spark-csomagok.](https://spark-packages.org)

A legjobb teljesítményformátum a *lendületes tömörítéssel*ellátott parketta, amely a Spark 2.x alapértelmezett beállítása. A Parketta oszlopos formátumban tárolja az adatokat, és a Sparkban nagymértékben optimalizált. Ezen kívül, míg *a lendületes tömörítés* vezethet nagyobb fájlokat, mint mondjuk gzip tömörítés. A fájlok felosztásos jellege miatt gyorsabban kifogják bontani a kibontást]

## <a name="use-the-cache"></a>A gyorsítótár használata

A Spark saját natív gyorsítótárazási mechanizmusait biztosítja, `.cache()`amelyek `CACHE TABLE`különböző módszerekkel használhatók, például a `.persist()`, és a . Ez a natív gyorsítótárazás kis adatkészletek, valamint az ETL-folyamatok, ahol a köztes eredmények gyorsítótárazása hatékony. Azonban a Spark natív gyorsítótárazása jelenleg nem működik jól a particionálás, mivel a gyorsítótárazott tábla nem tartja a particionálási adatokat.

## <a name="use-memory-efficiently"></a>A memória hatékony használata

A Spark úgy működik, hogy adatokat helyez el a memóriában, így a memória-erőforrások kezelése a Spark-feladatok végrehajtásának optimalizálása kulcsfontosságú szempont.  A fürt memóriájának hatékony használatához számos technikát alkalmazhat.

* Előnyben részesíti a kisebb adatpartíciókat, és vegye figyelembe az adatok méretét, típusait és eloszlását a particionálási stratégiában.
* Tekintsük az újabb, hatékonyabb [Kryo adatok szerializálása,](https://github.com/EsotericSoftware/kryo)nem pedig az alapértelmezett Java szerializálás.
* A Spark konfigurációs beállításainak figyelése és finomhangolása.

A referencia, a Spark memória szerkezete és néhány kulcsfontosságú végrehajtó memória paraméterei jelennek meg a következő képen.

### <a name="spark-memory-considerations"></a>A Spark memóriával kapcsolatos szempontok

Az Apache Spark az Azure Synapse-ban a YARN [Apache Hadoop YARN-t](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)használja, a YARN szabályozza az egyes Spark-csomópontok összes tárolója által használt memória maximális összegét.  Az alábbi ábra a legfontosabb objektumokat és azok kapcsolatait mutatja be.

![YARN Spark memóriakezelés](./media/apache-spark-perf/apache-yarn-spark-memory.png)

A "kevés memóriával" kapcsolatos üzenetek címzéséhez próbálkozzon a következőkkel:

* Tekintse át a DAG Management Shuffles-t. Csökkentse a térképoldali csökkentést, az előpartíciós (vagy gyűjtődő) forrásadatokat, maximalizálja az egyes véletlen sorrendű adatlejátszásokat, és csökkentse az elküldött adatok mennyiségét.
* Inkább `ReduceByKey` a rögzített memóriakorláttal `GroupByKey`, amely összesítéseket, ablakolást és egyéb függvényeket biztosít, de nem kötött memóriakorláttal rendelkezik.
* Inkább `TreeReduce`, amely nem több munkát a végrehajtók `Reduce`vagy partíciók, a , amely nem minden munkát az illesztőprogram.
* Az alacsonyabb szintű RDD-objektumok helyett használja ki a DataFrames-objektumokat.
* Hozzon létre olyan ComplexTypes típusokat, amelyek olyan műveleteket foglalnak magukban, mint a "Top N", a különböző összesítések vagy az ablakolási műveletek.

## <a name="optimize-data-serialization"></a>Adatok szerializálásának optimalizálása

Spark-feladatok vannak elosztva, ezért a megfelelő adatszerializálás fontos a legjobb teljesítmény érdekében.  A Sparknak két szerializálási lehetősége van:

* A Java szerializálás az alapértelmezett.
* Kryo szerializálás egy újabb formátum, és azt eredményezheti, gyorsabb és kompaktabb szerializálás, mint a Java.  Kryo megköveteli, hogy regisztrálja az osztályokat a programban, és ez még nem támogatja az összes Szerializálható típusok.

## <a name="use-bucketing"></a>Gyűjtőhasználata

A gyűjtőgyűjtés hasonló az adatparticionáláshoz, de minden gyűjtő oszlopértékeket képes tárolni, nem csak egyet. A vödrök jól működik a nagy (több millió vagy több) értékszám, például a termékazonosítók particionálásához. A gyűjtőt a sor gyűjtőkulcsának kivonatolásával határozzuk meg. A gyűjtőtáblák egyedi optimalizálást kínálnak, mivel metaadatokat tárolnak a rról, hogyan vannak bevésve és rendezve.

Néhány speciális bucketing funkciók a következők:

* Lekérdezésoptimalizálás a metaadatok gyűjtőkörén alapuló módon.
* Optimalizált összesítések.
* Optimalizált illesztések.

Használhatja a particionálás és a bucketing egy időben.

## <a name="optimize-joins-and-shuffles"></a>Az illesztések és a véletlen sorrendű lejátszás optimalizálása

Ha lassú feladatok vannak egy illesztésen vagy véletlen sorrendűen, az ok valószínűleg *adatdöntés*, amely afeladatadatok aszimmetriája. Például egy térképfeladat 20 másodpercet is igénybe vehet, de egy olyan feladat futtatása, ahol az adatok összevannak kötve vagy összekeverve, órákba telik. Az adatok döntésének javításához sózni kell az egész kulcsot, vagy *elszigetelt sót* kell használnia a kulcsok nak csak egy részhalmazához. Ha elszigetelt sót használ, további szűrést kell használnia a sózott kulcsok részhalmazának elkülönítéséhez a térképillesztésekben. Egy másik lehetőség egy gyűjtőoszlop bevezetése és előösszesített a vödrökben.

Egy másik tényező, amely lassú illesztéseket okoz, az illesztés típusa lehet. Alapértelmezés szerint a `SortMerge` Spark az illesztési típust használja. Ez a fajta illesztés nagy adatkészletek esetén a legalkalmasabb, de egyébként számítási szempontból költséges, mivel először az adatok bal és jobb oldalát kell rendeznie, mielőtt egyesítenék őket.

Az `Broadcast` illesztés kisebb adatkészletek esetén a legalkalmasabb, vagy ha az illesztés egyik oldala sokkal kisebb, mint a másik oldal. Ez a fajta illesztés az egyik oldalon az összes végrehajtónak sugároz, és így általában több memóriát igényel az adások számára.

Az illesztés típusát a konfigurációban `spark.sql.autoBroadcastJoinThreshold`módosíthatja a beállítással, vagy beállíthatja`dataframe.join(broadcast(df2))`az illesztési emlékeztetőt a DataFrame API-k ( használatával ).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Ha gyűjtőtáblákat használ, akkor van egy harmadik illesztési típusa, az `Merge` illesztés. A megfelelően előre particionált és előre rendezett adatkészlet kihagyja `SortMerge` a költséges rendezési fázist az illesztésből.

Az illesztések sorrendje számít, különösen az összetettebb lekérdezéseknél. Kezdje a legszelektívebb illesztésekkel. Az illesztések áthelyezése is, amelyek lehetőség szerint növelik a sorok számát az összesítések után.

A Descartes-illesztések párhuzamosságának kezeléséhez hozzáadhat egymásba ágyazott struktúrákat, ablakolást, és esetleg kihagyhat egy vagy több lépést a Spark-feladatban.

### <a name="select-the-correct-executor-size"></a>A végrehajtó megfelelő méretének kiválasztása

A végrehajtó konfigurációjának meghatározásakor vegye figyelembe a Java szemétgyűjtés (GC) terhelését.

* A végrehajtó méretének csökkentésére játszott tényezők:
  * Csökkentse a halommemória méretét 32 GB alá, hogy a GC felső < 10%.
  * Csökkentse a magok számát, hogy a GC < 10%.

* A végrehajtó méretének növelését tényezők:
  * Csökkentse a végrehajtók közötti kommunikációs terhelést.
  * Csökkentse a végrehajtók (N2) közötti nyitott kapcsolatok számát nagyobb fürtökön (>100 végrehajtó).
  * Növelje a halommemória méretét a memóriaigényes feladatok hoz.
  * Nem kötelező: Csökkentse a végrehajtónkénti memória terhelését.
  * Nem kötelező: Növelje a kihasználtságot és az egyidejűséget a processzor túlterhelésével.

Általános ökölszabályként a végrehajtó méretének kiválasztásakor:

* Kezdje a végrehajtónkénti 30 GB-tal, és ossza el a rendelkezésre álló gépmagokat.
* Növelje a végrehajtó magok számát a nagyobb fürtök (> 100 végrehajtók).
* Módosítsa a méretet mind a próbafuttatások, mind az előző tényezők, például a globális katalógus terhelése alapján.

Az egyidejű lekérdezések futtatásakor vegye figyelembe a következőket:

* Kezdje a 30 GB/végrehajtóval és az összes gépmaggal.
* Hozzon létre több párhuzamos Spark-alkalmazást a PROCESSZOR túljelentkezésével (körülbelül 30%-os késés javítása).
* Lekérdezések elosztása párhuzamos alkalmazások között.
* Módosítsa a méretet mind a próbafuttatások, mind az előző tényezők, például a globális katalógus terhelése alapján.

Az idővonal nézet, az SQL graph, a feladatstatisztikák stb. Néha az egyik vagy néhány végrehajtó lassabb, mint a többi, és a feladatok végrehajtása sokkal hosszabb időt vesz igénybe. Ez gyakran előfordul nagyobb fürtökön (> 30 csomópont). Ebben az esetben ossza fel a munkát nagyobb számú tevékenységre, hogy az ütemező kompenzálhassa a lassú tevékenységeket. 

Például legalább kétszer annyi feladatot, mint a végrehajtó magok száma az alkalmazásban. A segítségével is engedélyezheti a `conf: spark.speculation = true`feladatok spekulatív végrehajtását.

## <a name="optimize-job-execution"></a>Feladat-végrehajtás optimalizálása

* Szükség szerint gyorsítótárazza, például ha kétszer használja az adatokat, majd gyorsítótárazza.
* Változók közvetítése az összes végrehajtónak. A változók csak egyszer szerializálódnak, ami gyorsabb keresőket eredményez.
* Használja a szálkészletet az illesztőprogramon, ami számos feladat gyorsabb működését eredményezi.

A Spark 2.x lekérdezési teljesítménykulcsa a Tungsten motor, amely a teljes fázisú kódgenerálástól függ. Bizonyos esetekben a teljes szakaszban lévő kódgenerálás letiltható. 

Ha például nem módosítható típust (`string`) használ az összesítési `SortAggregate` `HashAggregate`kifejezésben, akkor a helyett a érték jelenik meg. A jobb teljesítmény érdekében például próbálkozzon a következőkkel, majd engedélyezze újra a kódgenerálást:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>További lépések

- [Az Apache Spark hangolása](https://spark.apache.org/docs/latest/tuning.html)
- [Hogyan lehet ténylegesen tune Your Apache Spark Jobs Így működnek](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Kryo szerializálás](https://github.com/EsotericSoftware/kryo)
