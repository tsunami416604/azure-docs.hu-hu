---
title: "Azure Machine Learning API Anomáliadetektálás |} Microsoft Docs"
description: "Az anomáliadetektálási észlelési API látható egy példa adatsorozat időadatok egységesen elosztásban időben numerikus értéket tartalmazó rendellenességeket észleli a Microsoft Azure Machine Learning-val készült."
services: machine-learning
documentationcenter: 
author: alokkirpal
manager: jhubbard
editor: cgronlun
ms.assetid: 52fafe1f-e93d-47df-a8ac-9a9a53b60824
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/05/2017
ms.author: alok;rotimpe
ms.openlocfilehash: 519ac38c484b9631a3fc096a17be026e9378a178
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="machine-learning-anomaly-detection-api"></a>Számítógép-tanulási Anomáliadetektálás API
## <a name="overview"></a>Áttekintés
[Az anomáliadetektálási észlelési API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) adatsorozat időadatok egységesen elosztásban időben numerikus értéket tartalmazó rendellenességeket észlelő Azure Machine Learning-val készült példa.

Ez az API idő adatsorozat adatok képes észlelni a következő típusú rendellenes minták:

* **Pozitív és negatív trendek**: például, ha egy növekvő tendenciát számítástechnikai memóriahasználat figyelése lehet egyik fontos lehet, a memóriavesztés,
* **A dinamikus értéktartományon változásai**: például egy felhőalapú szolgáltatás által okozott kivételek figyelésekor a dinamikus értéktartományon módosításai oka lehet a szolgáltatás állapotának instabillá válásának és
* **Napra és esik**: például egy szolgáltatást a sikertelen bejelentkezések száma vagy egy elektronikus kereskedelmi webhely, a kivételek száma figyelésekor igényeiben jelentkező vagy immerzióban utalhat rendellenes viselkedés.

A machine learning érzékelők ilyen értékek változásainak követése keresztül idő és a jelentés folyamatban lévő változásai szűkebb értékeik anomáliadetektálási pontszámait mint. Ad hoc küszöbérték hangolása nincs szükségük, és eredményeiket téves pozitív arány vezérlésére használható. Az anomáliadetektálás API akkor hasznos, több forgatókönyv szerint, például a karbantartási időszak alatt KPI-k nyomon követésével figyelése használat figyelését keresztül metrikák például keresések száma, a számok kattintással teljesítményfigyelés például a memória, Processzor, számlálóin keresztül olvassa be a fájlt, stb. adott idő alatt.

Az Anomáliadetektálás elérhető az első lépésekhez hasznos eszközök tartalmaz.

* A [webes alkalmazás](http://anomalydetection-aml.azurewebsites.net/) segítségével értékelje ki és jelenítheti az eredményeket az anomáliadetektálás API-k az adatokon.

> [!NOTE]
> Próbálja **informatikai Anomáliadetektálási Insights-megoldást** technológiával [az API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
> 
> A teljes körű megoldást az Azure-előfizetéshez telepített <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **az első lépések >**</a>
> 
>

## <a name="api-deployment"></a>API-telepítés
Ahhoz, hogy az API-t, telepítenie kell azt az Azure-előfizetéshez ahol tárolható egy Azure Machine Learning webszolgáltatásként.  Az ehhez a [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Ezzel telepít két AzureML-webszolgáltatásokat (és a kapcsolódó erőforrások) az Azure-előfizetés - egy, a szezonalitás értékének észlelési közüli és egy szezonalitás értékének észlelési nélkül.  A telepítés befejezése után lesz az API-kat kezelheti a [AzureML webszolgáltatások](https://services.azureml.net/webservices/) lap.  Ezen az oldalon lesz találhatók a végpontok helyére, API-kulcsokat, valamint mintakódot az API felület meghívásakor.  Részletes útmutatás érhető el [Itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Az API-t skálázás
Alapértelmezés szerint a központi telepítés lesz egy ingyenes fejlesztési és tesztelési célú számlázási tervet, amely 1000 tranzakciók/és 2 számítási órák/hónap.  Frissíthet egy másik terv igényeinek megfelelően.  A különböző tervek az árakkal kapcsolatos információk [Itt](https://azure.microsoft.com/en-us/pricing/details/machine-learning/) "Éles webes API-k árképzési" alatt.

## <a name="managing-aml-plans"></a>Tervek AML kezelése 
Kezelheti a számlázási csomag [Itt](https://services.azureml.net/plans/).  A séma neve úgy döntött, hogy az API-t központi telepítésekor az erőforráscsoport neve, valamint egy karakterlánc, amely egyedi az előfizetéséhez alapul.  Útmutatás a terv frissítésével érhető el [Itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) a "Számlázási tervek kezelése" szakaszban.

## <a name="api-definition"></a>API-definíció
A webes szolgáltatás egy REST-alapú API alkalmazást biztosít, amely képes használni a különböző módokon, például a web- vagy mobilalkalmazás R, Python, Excel, HTTPS-KAPCSOLATON keresztül stb.  Az idő adatsorok küldött a szolgáltatás egy REST API-híváson keresztül, és fusson az alább ismertetett három anomáliadetektálási típus kombinációját.

## <a name="calling-the-api"></a>Az API felület meghívásakor
Ahhoz, hogy hívja az API-t, akkor tudniuk kell, hogy a végpont helye és az API-kulcs.  Mindkét esetben, hívja az API-t, a mintakódot együtt érhetők el a [AzureML webszolgáltatások](https://services.azureml.net/webservices/) lap.  Keresse meg a kívánt API, és kattintson a "Felhasználás" lapon találja meg azokat.  Vegye figyelembe, hogy az API-t, mint a Swagger API meghívása (azaz az URL-cím paraméterrel `format=swagger`) vagy mint egy nem - Swagger API-t (azaz nélkül a `format` URL-paramétert).  A mintakód a Swagger-formátumot használja.  Az alábbiakban egy példa egy kérelem-válasz a a Swagger formátumban van.  Ezek többek között a szezonalitás értékének végpontnak.  A szezonalitás értékének nem végpont esetében hasonló.

### <a name="sample-request-body"></a>A minta kérelem törzsében
A kérelem tartalmazza a két objektum: `Inputs` és `GlobalParameters`.  Az alábbi példa kérelem, az egyes paraméterek küldött explicit módon nem vannak (görgessen lefelé a végpontok paraméterek teljes listáját).  Explicit módon nem küldött a kérelemben szereplő paraméterek alatt megadott alapértelmezett értéket fogja használni.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Mintaválasz
Vegye figyelembe, hogy a láthatók a `ColumnNames` mezőben meg kell adni `details=true` a kérés URL-cím paraméterként.  Tekintse meg az alábbi táblázatokban mögött egyes mezők szerint.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>Pontszám API
A pontszám API anomáliadetektálás futó nem határozza idő adatsor szolgál. Az API-t az adatokon anomáliadetektálási érzékelők számos fut, és visszaadja az anomáliadetektálási eredményeiket. Az alábbi ábra a rendellenességek észlelését, amely a pontszám API észlelni tudja példáját mutatja be. Ez a time series 2 különböző megváltozik, és 3 igényeiben jelentkező rendelkezik. A piros pont megjelenítése, amelyen a szint észlelt, amíg a fekete pontok megjelenítése észlelt ágainak idő.
![Pontszám API][1]

### <a name="detectors"></a>Érzékelők
Az anomáliadetektálás API támogatja az érzékelők 3 kategóriába sorolhatók. Meghatározott bemeneti paraméterek és minden egyes érzékelő kimeneti a következő táblázatban található.

| Érzékelő kategória | Érzékelő | Leírás | A bemeneti paraméterek | Kimenetek |
| --- | --- | --- | --- | --- |
| Csúcs érzékelők |TSpike érzékelő |Észleli a teljesítményt és immerzióban sokkal értékek alapján a rendszer az első és harmadik quartiles |*tspikedetector.Sensitivity:* egész értéket a tartomány 1 – 10., alapértelmezett vesz: 3; Így így kevésbé érzékeny további rendkívüli értékek fog dolgozza fel a magasabb értékkel |TSpike: bináris értékek – "1", ha a rendszer észlelt egy csúcs/dip, egyébként pedig "0" |
| Csúcs érzékelők | ZSpike érzékelő |Igényeiben jelentkező és milyen távolságban esetén a datapoints tartoznak, amely a középérték alapján immerzióban észlelése |*zspikedetector.Sensitivity:* érvénybe egész szám, a tartomány 1 – 10., alapértelmezett: 3; Így kevésbé érzékeny további rendkívüli értékek fog dolgozza fel a magasabb értékkel |ZSpike: bináris értékek – "1", ha a rendszer észlelt egy csúcs/dip, egyébként pedig "0" | |
| Lassú Trend érzékelő |Lassú Trend érzékelő |Lassú pozitív trend a beállított érzékenységi szerinti észlelése |*trenddetector.Sensitivity:* érzékelő pontszám küszöbértékét (alapértelmezett: 3,25, 3,25 – 5 az egy ésszerű tartomány, jelölje be ezt a; Minél nagyobb a kevesebb érzékeny) |tscore: a trend anomáliadetektálási pontszám jelentő lebegőpontos szám |
| Szint módosításának érzékelők | Kétirányú szint módosítása érzékelő |A készlet érzékenységi szerint felfelé és lefelé is szint módosításának észlelése |*bileveldetector.Sensitivity:* érzékelő pontszám küszöbértékét (alapértelmezett: 3,25, 3,25 – 5 az egy ésszerű tartomány, jelölje be ezt a; Minél nagyobb a kevesebb érzékeny) |rpscore: jelentő anomáliadetektálási pontszám felfelé és lefelé szint módosítása a lebegőpontos szám | |

### <a name="parameters"></a>Paraméterek
Részletesebb információ ezen bemeneti paraméterek, az alábbi táblázatban szerepel:

| A bemeneti paraméterek | Leírás | Alapértelmezett beállítás | Típus | Érvényes értékek | Javasolt tartomány |
| --- | --- | --- | --- | --- | --- |
| detectors.historyWindow |Anomáliadetektálási pontszám számításhoz használt előzményeket (az adatpontok száma) |500 |egész szám |10-2000 |Idősorozat függő |
| detectors.spikesdips | Hogy-e csak napra, csak immerzióban vagy mindkettő |Mindkettő |számbavétele |Mindkét, teljesítményt, immerzióban |Mindkettő |
| bileveldetector.Sensitivity |Érzékenységi kétirányú szint módosítása érzékelő. |3.25 |Dupla |None |(A kisebb értékek jelenti érzékenyebb) 3,25 5 |
| trenddetector.Sensitivity |Érzékenysége a pozitív trend érzékelő. |3.25 |Dupla |None |(A kisebb értékek jelenti érzékenyebb) 3,25 5 |
| tspikedetector.Sensitivity |Érzékenysége a TSpike érzékelő |3 |egész szám |1-10 |3-5 (a kisebb értékek jelenti érzékenyebb) |
| zspikedetector.Sensitivity |Érzékenysége a ZSpike érzékelő |3 |egész szám |1-10 |3-5 (a kisebb értékek jelenti érzékenyebb) |
| postprocess.tailRows |Meg kell őrizni, a kimeneti eredmények a legfrissebb adatpontok száma |0 |egész szám |a 0 (tartani minden adatpontok), vagy adja meg, hány pontot kell eredmények megőrzése |N/A |

### <a name="output"></a>Kimenet
Az API-t az az idő adatsorok összes érzékelők fut, és anomáliadetektálási pontszámokat és az egyes bináris csúcs mutatók időt adja vissza. Az alábbi táblázat felsorolja az API-t kimeneteinek. 

| Kimenetek | Leírás |
| --- | --- |
| Time |A nyers adatokat, vagy összesített (és/vagy) imputált adatok időbélyegeket Ha összesítési (és/vagy) hiányzik az adatok imputálási vonatkozik |
| Adatok |Nyers vagy összesített (és/vagy) imputált adatok közötti értéket, ha összesítő (és/vagy) hiányzó adatok imputálási vonatkozik |
| TSpike |Bináris jelölő jelzi, hogy egy csúcs TSpike érzékelő észlelhető |
| ZSpike |Bináris jelölő jelzi, hogy egy csúcs ZSpike érzékelő észlelhető |
| rpscore |Egy lebegőpontos szám képviselő anomáliadetektálási pontozása a kétirányú szint módosítása |
| rpalert |1 vagy 0 érték azt jelzi, kétirányú szintű bemeneti érzékenysége alapján anomáliadetektálási módosítása |
| tscore |Egy lebegőpontos szám képviselő anomáliadetektálási pontozása a pozitív trend |
| talert |1 vagy 0 értéket, amely jelzi, hogy egy bemeneti érzékenysége alapján pozitív trend anomáliadetektálási |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
A ScoreWithSeasonality API anomáliadetektálás futó határozza minták rendelkező a time series szolgál. Ez az API akkor hasznos, eltérések határozza mintázatok észlelését.  
Az alábbi ábra határozza idősor észlelhető rendellenességeket példáját mutatja be. Az idősor rendelkezik egy csúcs (az 1. fekete pont), a két immerzióban (a 2. fekete pont és egy végén), és egy szint módosítása (piros pont). Ne feledje, hogy mindkét a dip közepén idősorozatban és a szint módosítása csak discernable után határozza összetevők el lesznek távolítva az adatsorozat.
![Szezonalitás értékének API][2]

### <a name="detectors"></a>Érzékelők
Az érzékelők a szezonalitás értékének végpont hasonlóak a szezonalitás értékének nem végpont, de a (lenti) kis mértékben eltérő paraméternevei megfelelően.

### <a name="parameters"></a>Paraméterek

Részletesebb információ ezen bemeneti paraméterek, az alábbi táblázatban szerepel:

| A bemeneti paraméterek | Leírás | Alapértelmezett beállítás | Típus | Érvényes értékek | Javasolt tartomány |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Az aggregációs időköznek másodpercben összesítéséhez szükséges tartományt adjon meg a time series |0 (nincs összesítési történik) |egész szám |0: ellenkező esetben hagyja ki az összesítés, amely > 0 |1 nap, idősorozat függő 5 perc |
| preprocess.aggregationFunc |A megadott AggregationInterval az adatok összesítéséhez szükséges tartományt függvény |témakörök |számbavétele |átlagos, sum, hossza |N/A |
| preprocess.replaceMissing |Hiányzó adatok imputálására értékek |LKV (utolsó ismert érték) |számbavétele |nulla, lkv, középérték |N/A |
| detectors.historyWindow |Anomáliadetektálási pontszám számításhoz használt előzményeket (az adatpontok száma) |500 |egész szám |10-2000 |Idősorozat függő |
| detectors.spikesdips | Hogy-e csak napra, csak immerzióban vagy mindkettő |Mindkettő |számbavétele |Mindkét, teljesítményt, immerzióban |Mindkettő |
| bileveldetector.Sensitivity |Érzékenységi kétirányú szint módosítása érzékelő. |3.25 |Dupla |None |(A kisebb értékek jelenti érzékenyebb) 3,25 5 |
| postrenddetector.Sensitivity |Érzékenysége a pozitív trend érzékelő. |3.25 |Dupla |None |(A kisebb értékek jelenti érzékenyebb) 3,25 5 |
| negtrenddetector.Sensitivity |A negatív trend érzékelő érzékenységi. |3.25 |Dupla |None |(A kisebb értékek jelenti érzékenyebb) 3,25 5 |
| tspikedetector.Sensitivity |Érzékenysége a TSpike érzékelő |3 |egész szám |1-10 |3-5 (a kisebb értékek jelenti érzékenyebb) |
| zspikedetector.Sensitivity |Érzékenysége a ZSpike érzékelő |3 |egész szám |1-10 |3-5 (a kisebb értékek jelenti érzékenyebb) |
| seasonality.enable |Szezonalitás értékének elemzés hajtható végre, hogy van-e |igaz |Logikai érték |IGAZ, hamis |Idősorozat függő |
| seasonality.numSeasonality |Észleltnek rendszeres ciklusok maximális száma |1 |egész szám |1, 2 |1-2 |
| seasonality.Transform |E határozza (és) anomáliadetektálás alkalmazása előtt el kell távolítani a trend összetevők |deseason |számbavétele |nincs, deseason, deseasontrend |N/A |
| postprocess.tailRows |Meg kell őrizni, a kimeneti eredmények a legfrissebb adatpontok száma |0 |egész szám |a 0 (tartani minden adatpontok), vagy adja meg, hány pontot kell eredmények megőrzése |N/A |

### <a name="output"></a>Kimenet
Az API-t az az idő adatsorok összes érzékelők fut, és anomáliadetektálási pontszámokat és az egyes bináris csúcs mutatók időt adja vissza. Az alábbi táblázat felsorolja az API-t kimeneteinek. 

| Kimenetek | Leírás |
| --- | --- |
| Time |A nyers adatokat, vagy összesített (és/vagy) imputált adatok időbélyegeket Ha összesítési (és/vagy) hiányzik az adatok imputálási vonatkozik |
| OriginalData |Nyers vagy összesített (és/vagy) imputált adatok közötti értéket, ha összesítő (és/vagy) hiányzó adatok imputálási vonatkozik |
| ProcessedData |Az alábbiak valamelyikét: <ul><li>A time series szezonálisan módosul, ha jelentős szezonalitás értékének már telepítve van, és deseason beállításnak;</li><li>szezonálisan módosul, és ha jelentős szezonalitás értékének már telepítve van a time series és deseasontrend választógomb detrended</li><li>Ellenkező esetben ez megegyezik a OriginalData</li> |
| TSpike |Bináris jelölő jelzi, hogy egy csúcs TSpike érzékelő észlelhető |
| ZSpike |Bináris jelölő jelzi, hogy egy csúcs ZSpike érzékelő észlelhető |
| BiLevelChangeScore |Egy lebegőpontos szám képviselő anomáliadetektálási pontozása a szint módosítása |
| BiLevelChangeAlert |1 vagy 0 értéket, amely jelzi, hogy egy bemeneti érzékenysége alapján szint módosításának anomáliadetektálási |
| PosTrendScore |Egy lebegőpontos szám képviselő anomáliadetektálási pontozása a pozitív trend |
| PosTrendAlert |1 vagy 0 értéket, amely jelzi, hogy egy bemeneti érzékenysége alapján pozitív trend anomáliadetektálási |
| NegTrendScore |Egy lebegőpontos szám képviselő anomáliadetektálási pontozása a negatív trend |
| NegTrendAlert |1 vagy 0 értéket, amely jelzi, hogy egy negatív trend anomáliadetektálási bemeneti érzékenysége alapján |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

