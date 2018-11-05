---
title: Anomáliadetektálás az Azure Stream Analytics (előzetes verzió)
description: Ez a cikk ismerteti, hogyan használhatja az Azure Stream Analytics és az Azure Machine Learning együtt észlelje a rendellenességeket.
services: stream-analytics
author: dubansal
ms.author: dubansal
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 2f35f54c7ec5ad169673aebe08602294270f470a
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364455"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Anomáliadetektálás az Azure Stream Analytics szolgáltatásban

> [!IMPORTANT]
> Ezt a funkciót, mert folyamatban van, de váltja fel az új funkciók. További információért látogasson el a [nyolc új funkciók az Azure Stream Analyticsben](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/) blogbejegyzést.

A **AnomalyDetection** operátor annak észlelésére használnak, különböző típusú anomáliákról az eseménystreameket. Például lehet, hogy a szabad memória a hosszú idővel lassú csökkenését, a memóriavesztés, vagy a webszolgáltatási kérelmeket, amelyek számos stabil száma előfordulhat, hogy jelentős mértékben növelése vagy csökkentése.  

AnomalyDetection operátort észleli a rendellenességeket háromféle: 

* **Kétirányú szint módosítása**: egy tartós növelése vagy csökkentése érdekében az értékeket, felfelé és lefelé is szintjét. Ez az érték eltér adatforgalmi csúcsokhoz és a DIP, amelyek azonnali vagy ideiglenes módosítások.  

* **Lassú pozitív Trend**: A idővel a trendek lassú növekedése.  

* **Lassú negatív trendek**: idővel a trendek lassú csökkenését.  

Amikor a AnomalyDetection operátort használja, meg kell adnia a **Limit Duration** záradékban. Ez a záradék megadja az időintervallum (milyen vissza az előzményekben az aktuális esemény) kell alkalmazni, amikor a rendellenességek észlelése. Ez az operátor dönthet úgy, hogy csak olyan események, amelyek megfelelnek egy bizonyos tulajdonság vagy feltétel használatával korlátozni az  **amikor** záradékban.   Ez az operátor is képes feldolgozni a megadott kulcs külön-külön alapján események csoportjait a **partíció által** záradékban. Betanítási és Predikciós fordulhat elő, egymástól függetlenül minden partíció esetében. 

## <a name="syntax-for-anomalydetection-operator"></a>AnomalyDetection operátort szintaxisa

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Példa a használatra**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Argumentumok

* **scalar_expression** – a skaláris kifejezés, amely fölött a rendellenességek észlelése történik. Megengedett értékek esetében ez a paraméter tartalmazza a lebegőpontos vagy Bigint adattípusok, amely visszatérési egyetlen (skaláris) értéket. A helyettesítő karakteres kifejezést **\*** nem engedélyezett. Skaláris kifejezés nem tartalmazhatnak más elemzési funkciók vagy külső funkciók. 

* **partition_by_clause** – a `PARTITION BY <partition key>` osztja fel a tanuláshoz és továbbképzéshez záradék külön partíciókon. Más szóval egy külön modell használni kívánt értéket kiszolgálónként `<partition key>` , és csak események ezt az értéket a használni kívánt tanuláshoz és továbbképzéshez a modellben. Például a következő lekérdezés vonatok és egy olvasási ellen, csak az azonos érzékelő más olvasmányok pontszámok:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **limit_duration záradék** `DURATION(<unit>, <length>)` – Itt adhatja meg az időintervallum (milyen vissza az előzményekben az aktuális esemény) kell alkalmazni, amikor a rendellenességek észlelése. Lásd: [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) támogatott egységek és rövidítésekkel részletes leírását. 

* **when_clause** – Itt adhatja meg az eseményeket is figyelembe venni a rendellenességek észlelése számítási logikai feltételt.

### <a name="return-types"></a>Návratové Typy

AnomalyDetection operátort tartalmazó összes három pontszámok kimenetként rekordot adja vissza. Az anomáliadetektálási derítik fel a különböző típusú társított tulajdonságok a következők:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Bontsa ki az egyes értékeket a rekordot, használja a **GetRecordPropertyValue** függvény. Példa:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

Anomáliadetektálási típusú észlel, amikor az anomáliadetektálási pontszámok egyik átlép egy küszöbértéket. A küszöbérték lehet bármely lebegőpontos szám > = 0. A küszöbérték érzékenységét és magabiztosan magával. Például alacsonyabb küszöbértékkel Ehhez győződjön meg a bizalmas adatokat kezelő módosítások észlelése, és hozzon létre további riasztások, mivel magasabb küszöbértéket sikerült győződjön meg arról, észlelés kevésbé érzékeny és sokkal magabiztosabb a munkában, de néhány rendellenességek maszkolja. A pontos küszöbértéket. használja a forgatókönyvtől függ. Nincs felső korlátja, de a javasolt tartomány 3,25 – 5. 

A példában látható 3,25 értéke javasolt kiindulási pontot. Finomhangolása az értéket a végrehajtott műveleteket a saját adatok futtatásával, és figyelje meg a kimeneti érték, amíg el nem éri a megengedett küszöbértéket.

## <a name="anomaly-detection-algorithm"></a>Anomáliaészlelő

* AnomalyDetection operátort használja egy **tanítást** megközelítés, ahol azt nem feltételezi az eseményekben található terjesztési bármilyen típusú. Általában két modell karbantartása párhuzamosan egy adott időpontban, ahol az egyiket pontozási használja, és a másik be van tanítva a háttérben. A rendellenességek észlelése modellek képzett egy sávon kívüli mechanizmus használata helyett az aktuális stream származó adatok felhasználásával. Képzési felhasznált adatok mennyisége az ablak mérete d Limit Duration paraméter belül a felhasználó által megadott függ. Minden modell említi első betanított d, 2d alatt az események alapján. Azt javasoljuk, hogy a legalább 50 események minden egyes ablakban, a legjobb eredmények elérése érdekében. 

* AnomalyDetection operátort használja **ablak szemantika késleltetett** modelleket és pontszám események betanításához. Ami azt jelenti, hogy minden egyes esemény értékeli ki a anomáliadetektálás pontszámot ad vissza. A pontszám, arra utalhat, hogy adott anomáliadetektálási megbízhatósági szintjét. 

* AnomalyDetection operátort tartalmaz egy **ismételhetőség garancia** bemeneti mindig azonos a azonos pontszám, függetlenül a feladatkimenet kezdési idő eredményez. A feladat kimeneti kezdési időpont, amikor az első kimeneti esemény a feladat által előállított idejét jelzi. Azt állítja be a felhasználó az aktuális idő, egyéni értékké vagy a legutóbbi kimeneti (Ha a feladat volt korábban létrehozott kimenet). 

### <a name="training-the-models"></a>A modellek betanítása 

Idő előrehaladtával modellek képzett különböző adatokat. Ahhoz, hogy megismerje a pontszámokat, segít megérteni a mögöttes mechanizmus, amely szerint a modellek képzett. Itt **t<sub>0</sub>**  van a **feladatkimenet kezdési idő** és **d** van a **ablakméret** a korlát időtartama a paraméter. Tegyük fel, hogy az idő felosztva **mérete d útválasztók ugrásainak**kezdődően `01/01/0001 00:00:00`. A modell betanítására pontszám az események a következő lépések használhatók:

* A feladat indulásakor kezdési idő t adatokat olvas be<sub>0</sub> – 2d.  
* Megkezdődik a következő ugrás, amikor új modellt M1 létrejön, és elindítja a kezdeti betanított.  
* Egy másik Ugrás előlegfizetéseket idő, amikor új modellt M2 létrejön, és elindítja a kezdeti betanított.  
* Amikor elérkezik t<sub>0</sub>, M1 akkor aktív, és a pontszám elindítja az első használt kimeneti adattípus.  
* Jelenleg a következő Ugrás három dolgot egy időben történik:  

  * M1 már nincs szükség, és elveti azt.  
  * M2 van elég betanítva így pontozási használatos.  
  * Új modell M3 jön létre, és elindítja a kezdeti betanított a háttérben.  

* Ez a ciklus ismétlődik minden Ugrás a modell a rendszer elveti, ahol a párhuzamos modellre, és indítsa el a háttérben egy harmadik modell. 

Diagramja a lépések hasonlítania: 

![Képzési modellek](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Modell** | **Képzési kezdő időpontja** | **A pontszám használatának megkezdéséhez idő** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* Modell M1 elindítja a képzés: 00:20 óra, azaz a következő ugrás a feladat olvasása, 11:13 órakor elindulása után. Az első kimeneti M1 előállított-betanítás a 13 percnyi adat után 11:33 kor. 

* Új modell M2 is elindul kapcsolatos képzésére 11:30-kor, de a pontszám nem beolvasása használja, csak 11:40 óra, amely után betanítva 10 percnyi adat az. 

* M3 M2, ugyanezt a mintát követi. 

### <a name="scoring-with-the-models"></a>A modellek pontozása 

A Machine Learning szintjén a anomáliaészlelő kiszámítja az egyes bejövő események strangeness értéket hasonlítsa össze az eseményeket egy előzmények ablak. A strangeness számítási eltér az egyes anomáliadetektálási.  

Tekintsük át részletesen a strangeness számítás (feltételezik, hogy a korábbi windows-események létezik): 

1. **Kétirányú szint módosításának:** az Előzmények ablak alapján, a szokásos üzemi tartomány számítja ki, hogy [10th PERCENTILIS, 90. százalékértékre], a 10th százalékértékről, mint a felső határérték alsó határérték, és 90 percentile értékéhez. Az aktuális esemény strangeness értékét számítja ki, hogy:  

   - 0, ha event_value normál működési hatótávolságon belül van  
   - event_value/90th_percentile, ha event_value > 90th_percentile  
   - 10th_percentile/event_value, ha a event_value < 10th_percentile  

2. **Lassú pozitív trend:** trendvonal keresztül az Előzmények ablak esemény értékeinek kiszámítása, és a művelet megkeresi a soron belül pozitív tendenciát. Strangeness értékét számítja ki, hogy:  

   - Görbét, ha meredekség pozitív  
   - 0, egyéb 

3. **Lassú negatív trendek:** trendvonal keresztül az Előzmények ablak esemény értékeinek kiszámítása, és negatív trendek a soron belül keres a műveletet. Strangeness értékét számítja ki, hogy: 

   - Görbét, ha a görbét negatív  
   - 0, egyéb  

A beérkező eseményben strangeness értékét számítja ki, ha egy martingale érték kiszámítása a strangeness érték alapján (lásd a [Machine Learning blog](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) hogyan az martingale érték kiszámítása a részletekért). Ez az érték martingale szerint az anomáliadetektálási pontszám van retuned. A martingale érték lassan válaszul furcsa értékek, amely lehetővé teszi, hogy az érzékelő szórványos változások hatékony maradjon, és csökkenti a téves riasztások növekszik. A hasznos tulajdonsággal is rendelkezik: 

Mekkora valószínűséggel [t ilyen létezik, M<sub>t</sub> > λ] < 1/λ, ahol M<sub>t</sub> azonnali t martingale értékét, és λ valós értékű. Például, ha egy riasztás amikor M<sub>t</sub>> 100, majd a valószínűsége annak, téves 1/100-nál kisebb.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Útmutató a kétirányú szint használatával módosítsa detector használatával 

A kétirányú szint módosításának detector használatával power szolgáltatáskimaradás és helyreállítási vagy csúcsforgalom forgalmat, és így tovább – egyéb felhasználási helyzetek is használható. Azonban működést úgy, hogy a modell tanítása bizonyos adatokat, ha egy másik szint módosításának rendellenes csak, ha új értéke nagyobb, mint az előző felső korlátja (felfelé szint módosítása eset) vagy kisebb, mint az előző alacsonyabb korlátot (lefelé szint eset módosítása). Ezért egy modell nem szabadna megjelennie (felfelé vagy lefelé méreteznie) az új szint a tartományon lévő rendellenes figyelembe venni, hogy a képzés ablakban. 

Az érzékelő használatával a következő szempontokat érdemes figyelembe venni: 

1. Amikor a time series hirtelen látja növelését, vagy dobja el az érték, AnomalyDetection operátort észleli ezt. Azonban további tervezést igényel a vissza a normál észlelése. Ha az idősor lépésközi előtt, amely AnomalyDetection operátort duplikátumészlelési időtartam legfeljebb hosszának felét az anomáliadetektálási értékre állításával biztosíthatja az anomáliadetektálási stabil állapotban volt. Ebben a forgatókönyvben azt feltételezi, hogy az anomáliadetektálási minimális időtartama becsülhető időben, és nincsenek megfelelő események, hogy a modell betanításához elég időkereten belül (legalább 50 események). 

   Ez az 1 és a egy felső korlátot módosítása (a ugyanez a logika vonatkozik a alacsonyabb korlátot módosítása) az alábbi 2. ábra jelenik meg. Mindkét-adatok a hullámformák egy rendellenes szint módosításának. Narancssárga függőleges vonalak jelölik Ugrás határok Ugrás mérete pedig ugyanaz, mint a megadott AnomalyDetection operátort duplikátumészlelési időtartam. A zöld sorok azt jelzik, hogy a képzés ablak méretének. Az 1. ábrán az Ugrás mérete megegyezik a melyik anomáliadetektálási aktiválást időpontját. A 2. ábrán az Ugrás mérete, amelynek az anomáliadetektálási tart feleannyi idő. Minden esetben egy felfelé változás észlelésekor, mert a modell pontozása használt volt tanított normális adatokká. Kizárása alapján a kétirányú szint módosításának detector használatával működését, azt kell a normál értéket használja, lépjen vissza a normál pontszámmodell modell betanítási ablakban. Az 1. ábrán a pontozási modell betanítása néhány szokásos esemény tartalmaz, így vissza a normál nem észlelhető. De a 2. ábrán a képzés csak tartalmazza a rendellenes rész, amely biztosítja, hogy lépjen vissza a normál észlel. Fele-nél is működik ugyanezen okból, mivel semmit nagyobb lesz végül többek között a normál események kicsit. 

   ![AD-ablak méretének egyenlő anomáliadetektálási hossza](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![AD-ablakméret egyenlő fele anomáliadetektálási hossza](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. Azokban az esetekben, ahol az anomáliadetektálási hossza nem becsülhető ez detector használatával működtet, legjobb erőfeszítés. Azonban kiválasztása, hogy egy szűkebb időtartomány korlátozza a betanítási adatok, amelyek növelné a valószínűsége, hogy a normál visszaállításához. 

3. A következő esetben a hosszabb anomáliadetektálási nem észlelhető, a képzési ablak már eleve tartalmazza egy anomáliadetektálási ugyanolyan magas értékű. 

   ![Az azonos méretű anomáliák](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>Rendellenességek észlelése. példalekérdezés 

A következő lekérdezést a kimenetben riasztást, ha észleli egy anomáliadetektálási használható.
Ha a bemeneti stream nem egységes, az összesítés lépés segít átalakítja a nyersanyagokat egy egységes idősorozat. A példában AVG, de a felhasználói forgatókönyv függ az adott típusú összesítést. Továbbá ha az idősor lépésközi nagyobb, mint az összesítési időszak hézagok, nincsenek más eseményeket az idősor eseményindító anomáliadetektálás gyakorisághoz (késleltetett ablak szemantika). Ennek eredményeképpen az egységesség feltételezzük, megszakadt, amikor a következő esemény érkezik. Az ilyen helyzetekben az idősorozatban található hézagok ki kell tölteni. Egy lehetséges módszert kerül az utolsó esemény minden Ugrás ablakában alább látható módon.

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

## <a name="performance-guidance"></a>Teljesítmény-útmutató

* Hat a folyamatos átviteli egységek használata feladatokhoz. 
* Események küldése legalább egy második egymástól.
* Egy nem particionált lekérdezést, amely AnomalyDetection operátort használja is eredményt, 25 MS-os számítási késéssel átlagosan.
* A particionált lekérdezések által tapasztalt késés kis mértékű a partíciók számát, mivel a számítások száma magasabb. A várakozási azonban kapcsolatos ugyanaz, mint egy összehasonlítható események száma összesen nem particionált esetében van, minden partíciók között.
* Nem valós idejű adatkiolvasás során nagy mennyiségű adat betöltött gyorsan. Ezek az adatok feldolgozása jelenleg lassabb lesz. Ilyen esetekben a késés növekedése lineárisan az ablak mérete vagy esemény intervallum helyett az ablakban szereplő adatpontok számát található. Nem valós idejű esetekben a késés csökkentése érdekében fontolja meg egy kisebb ablakméret. Másik lehetőségként fontolja meg a feladat indítása folyamatban van az aktuális időponthoz képest. Néhány példa a nem particionált lekérdezést az késleltetések: 
    - az észlelési időszak 60 adatpontok eredményezhet a késés 10 másodperces egy 3 MB/s átviteli sebességgel. 
    - 600 adatpontok, a késés egy 0,4 MB/s átviteli sebességgel körülbelül 80 másodpercig is elérheti.

## <a name="limitations-of-the-anomalydetection-operator"></a>AnomalyDetection operátort korlátozásai 

* Ez az operátor jelenleg nem támogatja forgalomnövekedést és dedikált IP-címmel. Csúcsok és a DIP olyan spontán vagy ideiglenes módosítások idősorozatban található. 

* Ez az operátor jelenleg nem kezeli a szezonalitás mintákat. Szezonalitás minták olyan ismétlődő mintákat az adatok, például egy eltérő méretű webes forgalom működés hétvégi vagy különböző vásárlási trendeket fekete péntek, során, amelyek nincsenek a rendellenességeket, de az adott várt mintának működésében. 

* Ez az operátor a bemeneti idősorozat egységes vár. Az eseménystream egy átfedésmentes keresztül összesítés vagy ablak meg egységes lehet tenni. Olyan forgatókönyvekben, ahol az események közötti résnek mindig kisebb, mint az összesítési időszak átfedésmentes ablak is, hogy az idősorozat egységes elegendő. A térköz nagyobb is lehet, ha a hiányosságok pótlásában töltheti újraindításához ismételje meg az utolsó olyan értéket egy ugróablak használatával. 

## <a name="references"></a>Referencia

* [Rendellenességek észlelése – a gépi tanulás segítségével észlelheti a rendellenességeket idősorozat-adatok](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [A Machine learning anomáliadetektálás API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Time series anomáliadetektálás](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

