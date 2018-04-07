---
title: Anomáliadetektálás Azure Stream Analytics (előzetes verzió)
description: A cikkből megtudhatja, hogyan használható az Azure Stream Analytics és az Azure Machine Learning együtt rendellenességek észlelését.
services: stream-analytics
author: dubansal
ms.author: dubansal
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: cda5c26d4256720a8cf9af0e9abd604c979422a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Az Azure Stream Analytics Anomáliadetektálás

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető, nem a termelési számítási feladatokhoz használatát javasoljuk.

A **AnomalyDetection** operátor használata eseményfolyamokat különböző típusú rendellenességek észlelését. Például lehet, hogy hosszú ideig keresztül lassú csökkenése szabad memória, a memóriavesztés, vagy a webszolgáltatási kérelmeket, amelyek egy stabil száma előfordulhat, hogy jelentős mértékben növeli vagy csökkenti.  

A AnomalyDetection operátor háromféle rendellenességeket észlel: 

* **Kétirányú szint módosítása**: egy tartós növeléséhez vagy csökkentéséhez értékek, felfelé és lefelé is szintjén. Ezt az értéket egy eltérő igényeiben jelentkező és immerzióban, amelyek azonnali vagy rövid élettartamú módosul.  

* **Lassú pozitív Trend**: A időbeli trendjét lassú növekedése.  

* **Negatív Trend lassú**: időbeli trendjét lassú csökken.  

A AnomalyDetection operátor használata esetén meg kell adnia a **Limit Duration** záradékban. Ehhez a záradékhoz megadja során rendellenességeinek észlelésekor érdemes figyelembe venni az időtartam (milyen távolságban vissza az aktuális esemény előzmények). Ez az operátor megadható az, hogy csak olyan események, amelyek megfelelnek egy adott tulajdonság vagy feltétel használatával korlátozni a **amikor** záradékban. Ez az operátor is képes a csoportok a megadott kulcs külön alapján események a **által partícióazonosító** záradékban. Képzés és az előrejelzés történik egymástól függetlenül minden partíció esetében. 

## <a name="syntax-for-anomalydetection-operator"></a>AnomalyDetection operátor szintaxisa

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Példa használati**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Argumentumok

* **scalar_expression** -a skaláris kifejezés, amely alatt az anomáliadetektálás történik. Megengedett értékek, ez a paraméter tartalmazza a lebegőpontos vagy Bigint adattípus, amely visszatérési egyetlen (skaláris) értéket. A helyettesítő karakteres kifejezést **\*** nem engedélyezett. Skaláris kifejezés nem tartalmazhat más analitikai függvények vagy külső funkciók. 

* **partition_by_clause** – a `PARTITION BY <partition key>` záradék osztja a tanulási és a képzési külön partíciók között. Más szóval egy külön modell használni / értékének `<partition key>` és tanulási és, hogy a modell a tanítási használják ezt az értéket csak eseményeket. Például a következő lekérdezés szerelvények és egy olvasási szemben a más értékek csak az azonos érzékelő pontszámok:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **limit_duration záradék** `DURATION(<unit>, <length>)` -megadja a során rendellenességeinek észlelésekor érdemes figyelembe venni az időtartam (milyen távolságban vissza az aktuális esemény előzmények). Lásd: [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) támogatott egységek és azok rövidítések részletes leírását. 

* **when_clause** -ad meg az események számítanak bele az anomáliadetektálási észlelési számítási logikai feltételt.

### <a name="return-types"></a>Visszatérési típusokat

A AnomalyDetection üzemeltető összes három pontszámok kimenetként tartalmazó rekordot ad vissza. A különböző típusú anomáliadetektálási érzékelők társított tulajdonságok a következők:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Az egyedi értékek kívül a rekord kibontásához használja a **GetRecordPropertyValue** függvény. Példa:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

Anomáliadetektálási típusú észlelt egy anomáliadetektálási pontszámok ebbe a küszöbértéket. A küszöbérték lehet bármely lebegőpontos szám > = 0. Küszöbértéke egy érzékenységi és az vetett bizalmat közötti kompromisszumot. Például alacsonyabb küszöbértékkel Ehhez ellenőrizze észlelési érzékenyebb a módosításokat, és további riasztást állítson elő, mivel a magasabb küszöbértéket sikerült észlelés kevésbé érzékeny és több biztosnak, azonban néhány rendellenességeket maszk. A pontos küszöbérték használni a forgatókönyv függ. Nincs felső korlátja, de a javasolt tartomány 3,25 – 5. 

## <a name="anomaly-detection-algorithm"></a>Anomáliaészlelő

* AnomalyDetection operátort használja egy **felügyelt tanítást** megközelítést, ha azt nem feltételezi azt az eseményekben található terjesztési bármilyen típusú. Általában két modell karbantartása párhuzamosan egy adott időpontban, ahol az egyik legyen pontozási használja, és a másik a háttérben be van tanítva. Az anomáliadetektálási észlelési modellek képzett adatokkal az aktuális adatfolyamból egy sávon kívüli mechanizmus használata helyett. A használt betanítási adatok mennyisége az ablak mérete d a Limit Duration paraméter belül a felhasználó által megadott függ. Egyes modellek említi első betanítása d 2d alatt érkezett események alapján. Javasoljuk, hogy van legalább 50 események minden egyes ablakban a legjobb eredmények elérése érdekében. 

* AnomalyDetection operátor **ablak szemantikáját késleltetett** modellek és pontszám események képzése érdekében. Ami azt jelenti, hogy minden esemény értékeli ki a anomáliadetektálási pontszámot adja vissza. A pontszám utalhat, hogy az adott anomáliadetektálási a megbízhatósági szint. 

* AnomalyDetection operátor biztosít egy **ismételhetőség garancia** bemeneti mindig azonos függetlenül a feladat kimenetére azonos pontszám kezdési időpont eredményez. A feladat kimenete kezdési ideje a, ahol az első kimeneti esemény a feladat által előállított időt jelenti. (Ha a feladat volt korábban létrehozott egy kimeneti) érték a felhasználó a jelenlegi időpontnál, egy egyéni érték vagy kimeneti legutóbbi. 

### <a name="training-the-models"></a>A modellek betanítása 

Idő előrehaladtával modellek képzett különböző adatokkal. Pontszámok értelme, áttekinteni tudni, hogy az alapul szolgáló mechanizmus, amellyel a modellek betanítása vannak. Itt **t<sub>0</sub>**  van a **feladatkiemenetét kezdési időpont** és **d** van a **ablakméret** a korlát időtartama a paraméter. Tegyük fel, amely idő felosztva **mérete d ugrások**kezdődően a `01/01/0001 00:00:00`. Az alábbi lépéseket a modell és a pontszám az események betanításához használandó:

* A feladat indításakor idő t el az adatok olvassa be<sub>0</sub> – 2d.  
* Amikor elérkezik a következő ugrás, új modell M1 jön létre, és elindítja a kezdeti képezni.  
* Idő kiadásokban egy másik Ugrás által, ha új modellt M2 jön létre, és elindítja a kezdeti képezni.  
* Amikor elérkezik t<sub>0</sub>, M1 aktív legyen, és a pontszám elindítja a kezdeti outputted.  
* A következő ugrás a három dolgokat okozhat, egyszerre:  

  * M1 már nem szükséges, és ez a program elveti.  
  * M2 még megfelelően betanítva, pontozási használható.  
  * Új modell M3 jön létre, és elindítja a kezdeti képezni a háttérben.  

* Ez a ciklus minden hop, ahol az aktív modellt a rendszer törli, az ismétlések váltson át a párhuzamos modell, és indítsa el a háttérben egy harmadik modell betanítása. 

Diagramja a lépések keresse meg az alábbiak szerint: 

![Képzési modellek](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Modell** | **Képzési kezdési ideje** | **A pontszám használatának ideje** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* Modell M1 elindítja a képzési: 00:20 óra, vagyis a következő ugrás a feladat indítása: 00 óra 13 olvasási után. Az első kimeneti előállított M1 után képzési 13 percek száma, az adatok a 11:33 órakor. 

* Új modell M2 is indítja el a 11:30 am oktatóanyagokat, de a pontszám nem get használt csak 11:40 óra, amely után az adatok 10 percig még betanítva. 

* M3 M2, azonos mintát követi. 

### <a name="scoring-with-the-models"></a>A modellek pontozási 

A Machine Learning szinten az anomáliadetektálási algoritmus összehasonlítva azt az előzmények ablakban események minden bejövő esemény strangeness értékét számítja ki. A strangeness számítási eltér az egyes anomáliadetektálási.  

Tekintsük át részletesen a strangeness számítási (feltételezik, hogy a korábbi windows események létezik): 

1. **Kétirányú szint módosítása:** az Előzmények ablak alapján, a szokásos üzemi tartomány számítja ki, hogy [10 PERCENTILIS, 90. százalékos érték] Ez azt jelenti, hogy 10 PERCENTILIS, mint a felsőé alsó határérték és 90th percentile értékéhez. Az aktuális esemény strangeness értékét számítja ki, hogy:  

   - 0, ha event_value normál működési hatótávolságon belül van  
   - event_value/90th_percentile, ha event_value > 90th_percentile  
   - 10th_percentile/event_value, ha a event_value < 10th_percentile  

2. **Lassú pozitív trend:** egy trendvonal keresztül az Előzmények ablak esemény értékének kiszámítása, és azt a soron belül pozitív tendenciát keresi. A strangeness értékét számítja ki, hogy:  

   - Görbét, ha görbét pozitív  
   - 0, egyébként pedig 

1. **Lassú negatív trend:** egy trendvonal keresztül az Előzmények ablak esemény értékének kiszámítása, és azt meg negatív trend a soron belül. A strangeness értékét számítja ki, hogy: 

   - Görbét, ha negatív görbét  
   - 0, egyébként pedig  

A beérkező eseményhez strangeness értékét számítja ki, ha egy martingale érték kiszámítása alapján történik a strangeness érték (lásd a [Machine Learning blog](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) talál részletes információt a martingale érték számított hogyan). Ez az érték martingale, az anomáliadetektálási pontszám van retuned. A martingale érték lassan válaszul furcsa értékek, amely lehetővé teszi, hogy az érzékelő maradjon robusztus szórványos változásokat, és csökkenti a téves riasztások növekszik. Hasznos tulajdonsággal is rendelkezik: 

Valószínűség [t ilyen létezik adott M<sub>t</sub> > λ] < 1/λ, ahol M<sub>t</sub> azonnali t martingale értékét, és λ legyen valós szám. Például, ha jelenleg a riasztás feltétele M<sub>t</sub>> 100, majd a vakriasztások valószínűségét 1/100-nál kisebb.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Útmutató a kétirányú szintű érzékelő módosítása 

A kétirányú szint módosításának jelentő használható forgatókönyvek például power kimaradás és helyreállítási vagy sürgős óra forgalom stb. Azonban működést úgy, hogy a modell betanítása bizonyos adatokat, ha egy másik szint módosításának rendellenes csak, ha új értéke nagyobb, mint a korábbi felső korlátja (felfelé szint módosítása esetében) vagy alacsonyabb, mint a korábbi alsó határérték (lefelé szint eset módosítása). Emiatt egy modell nem szabadna megjelennie az új szint (felfelé vagy lefelé) tartományán adatértékekkel rendellenes veszi figyelembe, hogy a képzés ablakában. 

A következő szempontokat kell figyelembe a rendszer az érzékelő használatakor: 

1. Amikor a idősorozat hirtelen látja növelését vagy dobja el az értéket, a AnomalyDetection operátor felismeri. De észlelése normál visszaállításához további tervezést igényel. Ha idősor előtt az anomáliadetektálási, amely a AnomalyDetection operátor észlelési időszak nagyobb, mint fele eltelt a anomáliadetektálási értékre állításával érheti el a stabil állapotban volt. Ez a forgatókönyv azt feltételezi, hogy az anomáliadetektálási minimális időtartama becsülhető időben, és nincs elég események a megadott idő alatt a modell betanításához elég (legalább 50 esemény). 

   Ezt az 1 és kisebb egy felső korlát módosítása (ugyanez a logika vonatkozik a alsó korlát módosítása) 2. ábra mutatja be. Mindkét számokkal a hullámformák egy rendellenes szint módosítása. Narancssárga függőleges vonalak jelölik Ugrás határokat és ugrásának mérete megegyezik a AnomalyDetection operátorban megadott észlelési ablak. A zöld sorok a képzés ablak méretét jelzik. 1. ábráján ugrásának mérete megegyezik a időt, hogy mely anomáliadetektálási tart. A 2. ábrán az Ugrás mérete, amelynek az anomáliadetektálási tart idő fele. Minden esetben egy felfelé változást észlel, mert a modell pontozása használatos a megszokott adatforgalmi lett képezni. De alapján a kétirányú szint módosításának jelentő működése, igazolnia kell zárnia a normál értéket a modell, amely pontszámaihoz normál visszaállításához használt betanítási ablakból. 1. ábrán a a pontozási modell betanítási néhány szokásos eseményeket tartalmazza a, térjen vissza a normál nem észlelhető. De a 2. ábrán, a képzési csak tartalmazza a rendellenes része, amely biztosítja, hogy a normál visszatérési észlelt. Bármi kisebb, mint felét is működik, ugyanezen okból, mivel minden nagyobb kat, beleértve a normál események bit. 

   ![Az ablak mérete nagyobb az anomáliadetektálási hossza AD](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![Az ablak mérete AD egyenlő fele anomáliadetektálási hossza](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. Azokban az esetekben, ahol az anomáliadetektálási hossza nem meghatározható az érzékelő működik legjobb elérhető. Azonban választ, egy szűkebb időkerete korlátozza a betanítási adatok, amelyek volna növelése normál visszaállításához észlelésének valószínűsége. 

3. Abban az esetben a hosszabb anomáliadetektálási, a képzési ablakban már található azonos legnagyobb értékének egy anomáliadetektálási nem észlelhető. 

   ![Az azonos méretűnek rendellenességeket](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>Példalekérdezés rendellenességek észlelését 

A következő lekérdezés kimeneti riasztást, ha a rendszer észlelt egy anomáliadetektálási használható.
Ha a bemeneti adatfolyam nem egységes, az összesítő lépés segíthet irányítópulttá, egységes idősor. A példában AVG, de az összesítési típusát a felhasználói forgatókönyvnek függ. Továbbá amikor idősor nagyobb, mint az összesítési ablak hézagok, hogy nincs eseményeket eseményindító anomáliadetektálás (visszamenőleges késleltetett ablak szemantikáját) idősorozatban található. Ennek eredményeképpen egységességének feltételezve megszakad, amikor a következő esemény érkezik. Ilyen helyzetekben a idősorozat hézagok ki kell tölteni. Egy lehetséges módszer kerül az utolsó esemény minden Ugrás ablakban alább látható módon.

```sql
    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25
```

## <a name="performance-guidance"></a>Teljesítmény útmutató

* Hat streamelési egységek használata feladatok. 
* Küldés események legalább egy második egymástól.
* A AnomalyDetection operátorral nem particionált lekérdezés is eredményt körülbelül 25 ms számítási késéssel átlagosan.
* A particionált lekérdezések által tapasztalt késést számától függ kissé partíciók, mivel a számítások száma magasabb. Azonban a késleltetés arra készül, ugyanaz, mint a nem particionált eset a hasonló események teljes száma a összes partíciójára.
* Nem valós idejű adatok olvasása közben nagy mennyiségű adatot okozhatnak gyorsan. Ezek az adatok feldolgozására jelenleg lassabb. Ilyen esetekben a késés növekedése a az ablak mérete vagy esemény időköz helyett az ablak adatpontok száma lineárisan található. Nem valós idejű esetekben a késés csökkentése érdekében érdemes lehet egy kisebb ablak mérete. Azt is megteheti fontolja meg a feladat indítása az aktuális időponthoz képest. Néhány példa a nem particionált lekérdezésben késések fordulnak elő: 
    - az észlelési időszak 60 adatpontok eredményezhet a 10 másodperces késleltetése 3 MB/s átviteli sebességgel. 
    - 600 adatok pontokat a késés körülbelül 80 másodperc 0,4 MB/s átviteli sebességgel képes elérni.

## <a name="limitations-of-the-anomalydetection-operator"></a>Korlátozások a AnomalyDetection operátor 

* Ez az operátor nem ismer csúcs és DIP-t. Igényeiben jelentkező és immerzióban módosulnak önkéntes vagy rövid élettartamú idősorozatban található. 

* Ez a művelet jelenleg nem kezeli a szezonalitás értékének kombinációját. Szezonalitás értékének mintákat az adatok, például egy másik webes forgalom működés során hétvégéket vagy különböző bevásárlási trendek során fekete péntek, amelyek nem rendellenességek észlelését, de egy viselkedés várható mintát ismételt minták. 

* Ez a művelet a bemeneti idősor egységes vár. Az eseménystream egy átfedésmentes keresztül összesítése vagy ablak hopping egységes is végezhető. Forgatókönyvekben, ahol események közötti résnek mindig kisebb, mint az összesítési ablakban a átfedésmentes ablak is elegendő az idősorozatban egységes ellenőrizze. Résnek nagyobb is lehet, ha hézagok is ki kell tölteni az utolsó értékét ugróablakok többszöri használatával. 

## <a name="references"></a>Referencia

* [Anomáliadetektálás – a gépi tanulási rendellenességeket észlelheti az adatsorozat időadatok](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Gépi tanulási anomáliadetektálás API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Idő adatsorozat anomáliadetektálás](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

