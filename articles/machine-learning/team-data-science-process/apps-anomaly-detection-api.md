---
title: Az Azure Machine Learning Anomáliadetektálás API – a csoportos adatelemzési folyamat
description: Anomáliadetektálás API, amelyek a észleli a rendellenességeket, amely időben egyenletesen elosztásban numerikus értékek idősorozat-adatokat a Microsoft Azure Machine Learning használatával létrehozott.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 06/05/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: b67028562a2c377e1dd99635bdf04cad14782341
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793135"
---
# <a name="machine-learning-anomaly-detection-api"></a>A Machine Learning Anomáliadetektálás API

> [!NOTE]
> Ez az elem karbantartás alatt áll. Javasoljuk, hogy használja a [Anomáliadetektálási detector használatával API szolgáltatás](https://azure.microsoft.com/en-us/services/cognitive-services/anomaly-detector/) működteti, a katalógusban, gépi tanulási algoritmusok Azure Cognitive Services segítségével észlelheti a rendellenességeket, az üzleti, üzemeltetési, és IoT-metrikák alapján.

## <a name="overview"></a>Áttekintés
[Anomáliadetektálás API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) a, amely észleli a rendellenességeket, amely időben egyenletesen elosztásban numerikus értékek idősorozat-adatokat az Azure Machine Learning használatával létrehozott példa.

Az API felismeri a következő típusú rendellenes minták az időbeli:

* **Pozitív és negatív trendek**: Például ha memóriahasználatát figyeli egy növekvő tendenciát számítástechnika lehet fontos lehet, hogy egy memóriavesztés behatolási kísérletre utaló
* **Az értékek dinamikus tartományának változásai**: Például egy felhőalapú szolgáltatás által okozott kivételek figyelésekor minden az értékek dinamikus tartományának változásai utalhat a szolgáltatás állapotának licencszolgáltatás instabillá válásának és
* **Csúcsok és Süllyedések**: Ha például a figyelő egy szolgáltatásban sikertelen bejelentkezések száma, vagy egy e-kereskedelmi webhely a véglegesítések számát, csúcsok és jelezheti rendellenes viselkedés.

A machine learning derítik fel ilyen értékek változásainak követése keresztül folyamatban lévő változásai idő- és azok értékeit, anomáliadetektálási pontszámok. Nincs szükségük ad hoc ad hoc küszöbérték finomhangolása és eredményeiket téves riasztási aránnyal vezérlésére használható. Az anomáliadetektálás API hasznos számos olyan szituációkra, mint a figyelést az idő múlásával nyomon követése a KPI-k révén a használat monitorozása keresztül metrikákra keresések száma, a számok kattintással, alkalmazásteljesítmény-figyelő például a memória, Processzor, a teljesítményszámlálók segítségével olvassa be a fájlt, stb. idővel.

Az Anomáliadetektálás ajánlat az első lépésekhez hasznos eszközöket tartalmaz.

* A [webes alkalmazás](https://anomalydetection-aml.azurewebsites.net/) segítségével értékelje ki és jelenítheti meg az eredményeket az anomáliadetektálás API-k az adatokon.

> [!NOTE]
> Próbálja ki **informatikai Anomáliaelemző megoldás** működteti [az API-t](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API üzembe helyezés
Annak érdekében, hogy az API-t használ, azt telepítenie kell az Azure-előfizetéshez ahol tárolható az Azure Machine Learning-webszolgáltatásként.  Az ehhez a [Azure AI-katalógusban](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Két Azure Machine Learning studio-webszolgáltatások (és az azokhoz kapcsolódó erőforrásokat) az Azure-előfizetéshez – ez telepíti, egy a szezonalitás észlelési anomáliadetektálás, és egy szezonalitás észlelési nélkül.  Az üzembe helyezés befejeztével lesz az API-k kezelése az [Azure Machine Learning studio-webszolgáltatások](https://services.azureml.net/webservices/) lapot.  Ezen az oldalon lesz a végpontok helyére, API-kulcsokat, valamint mintakód találhat az API-t hívná.  Részletes utasítások [Itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Az API-t méretezése
Alapértelmezés szerint a központi telepítés kap egy ingyenes fejlesztési-tesztelési számlázási csomagot 1000 tranzakció havonta, és 2 számítási óra/hó.  Az igényeinek megfelelően frissítheti egy másik csomagra.  A különböző csomagokról díjszabásáról érhetők el [Itt](https://azure.microsoft.com/pricing/details/machine-learning/) "Élő webes API díjszabása" alatt.

## <a name="managing-aml-plans"></a>Csomagok AML kezelése
Kezelheti a számlázási csomag [Itt](https://services.azureml.net/plans/).  A csomagnév döntött az API-t üzembe helyezésekor az erőforráscsoport nevét, valamint egy karakterlánc, amely egyedi az előfizetés alapján.  Váltson magasabb szintű csomagra való érhetők el [Itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) a "Számlázási csomagot kezelése" szakaszban.

## <a name="api-definition"></a>API-definíció
A webszolgáltatás REST-alapú API-t biztosít, amely képes használni a különféle módokon, például a webes vagy mobilalkalmazás, R, Python, az Excel, HTTPS-kapcsolaton keresztül stb.  Az idősoros adatokat küldeni a szolgáltatás REST API-hívás keresztül, és azt futtatja az alább ismertetett három anomáliadetektálási típusok kombinációját.

## <a name="calling-the-api"></a>Az API meghívása
Az API meghívása kell tudni, hogy a végpont helye és API-kulcsot.  Mindkét, az API hívásakor a mintakód együtt érhetők el a [Azure Machine Learning studio-webszolgáltatások](https://services.azureml.net/webservices/) lapot.  Keresse meg a kívánt API-t, majd kattintson a "Felhasználás" lapon találja meg azokat.  Vegye figyelembe, hogy meghívhatja az API-t egy Swagger API-ként (azaz az URL-cím paraméterrel `format=swagger`), vagy mint egy nem Swagger API-(azaz nélkül a `format` URL paraméter).  A mintakód a Swagger-formátumot használja.  Az alábbiakban egy példa kérések és válaszok a a Swagger formátumban van.  Ezekben a példákban vannak a szezonalitás végpontnak.  A nem szezonalitás végpont hasonlít.

### <a name="sample-request-body"></a>Minta-kérelem törzse
A kérés tartalmaz két objektum: `Inputs` és `GlobalParameters`.  Az alábbi példa kérelem néhány paraméter kell küldeni explicit módon nem vannak (görgessen le a végpontok paraméterek teljes listája).  A kérelem nem küldött explicit módon paraméterek az alább megadott alapértelmezett értékeket fogja használni.

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
Vegye figyelembe, hogy, annak érdekében, hogy tekintse meg a `ColumnNames` mezőben meg kell adni `details=true` a kérés URL-cím paraméterként.  Tekintse meg az alábbi táblázatokban ezek a mezők mindegyike mögött jelentését.

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
A pontszám API nem szezonális idősorozat-adatok a rendellenességek észlelése futtatásához használható. Az API anomáliadetektálási érzékelők számos futtat az adatokon, és azok anomáliadetektálási pontszámokat ad vissza.
Az alábbi ábra a rendellenességeket, amelyek a pontszám API észleli egy példát mutat be. A time series rendelkezik a 2 különböző megváltozik, és 3 adatforgalmi csúcsokhoz. A piros pötty megjelenítése az idő, amelyen a szint észlelt, miközben a fekete pontok megjelenítése az észlelt adatforgalmi csúcsokhoz.
![Score API][1]

### <a name="detectors"></a>Érzékelők
Az anomáliadetektálás API támogatja a derítik fel a 3 tág kategóriába. A megadott bemeneti paramétereket és kimeneteket az egyes detector használatával részletei a következő táblázatban található.

| Kategória detector használatával | Detector használatával | Leírás | Bemeneti paraméterek | Kimenetek |
| --- | --- | --- | --- | --- |
| Megnövekedett érzékelők |TSpike detector használatával |Csúcsok és a DIP sokkal értékei alapján is az első és harmadik quartiles észlelése |*tspikedetector.Sensitivity:* egész értéket a tartomány 1 – 10., alapértelmezett vesz fel: 3. A magasabb értékek lesznek catch tehát így kevésbé érzékeny további rendkívüli értékek |TSpike: bináris értékek – "1", ha a megnövekedett/dip észlel, különben "0" |
| Megnövekedett érzékelők | ZSpike detector használatával |Csúcsok és alapján illesztésnek a esetén a rendszer a középérték a DIP észlelése |*zspikedetector.Sensitivity:* egész szám, a tartomány 1 – 10., alapértelmezett igénybe: 3. A magasabb értékek lesznek catch így kevésbé érzékeny további rendkívüli értékek |ZSpike: bináris értékek – "1", ha a megnövekedett/dip észlel, különben "0" |
| Lassú Trend detector használatával |Lassú Trend detector használatával |A set-érzékenysége alapján lassú pozitív trend észlelése |*trenddetector.Sensitivity:* detector használatával pontszám a küszöbérték (alapértelmezett: 3,25, 3,25 – 5, és válassza ki ezt a; elfogadható tartományban Minél nagyobb a less-és nagybetűkre) |tscore: a trend anomáliadetektálás pontszámot jelölő lebegőpontos szám |
| Szint módosításának érzékelők | Kétirányú szint módosítása detector használatával |Felfelé és lefelé is megfelelően a beállított érzékenységi szint módosításának észlelése |*bileveldetector.Sensitivity:* detector használatával pontszám a küszöbérték (alapértelmezett: 3,25, 3,25 – 5, és válassza ki ezt a; elfogadható tartományban Minél nagyobb a less-és nagybetűkre) |rpscore: anomáliadetektálási pontszám a felfelé és lefelé szint módosításának jelölő lebegőpontos szám |

### <a name="parameters"></a>Paraméterek
Ezek a bemeneti paraméterek részletesebb információkat az alábbi táblázatban szerepel:

| Bemeneti paraméterek | Leírás | Alapértelmezett beállítás | Típus | Érvényes értékek | Javasolt tartomány |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Anomáliadetektálás pontszámot törölje a számításhoz használt előzmények (a adatpontok száma) |500 |egész szám |10-2000 |Idősorozat-függő |
| detectors.spikesdips | -E csak hirtelen megugró kihasználtság, csak DIP vagy mindkettő |Mindkettő |a felsorolt |Mindkét, adatforgalmi csúcsokhoz, DIP |Mindkettő |
| bileveldetector.sensitivity |Bizalmassági szint kétirányú detector használatával módosíthatja. |3.25 |double |None |3,25 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| trenddetector.sensitivity |Pozitív trend detector használatával különbségtételt. |3.25 |double |None |3,25 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| tspikedetector.sensitivity |Különbségtételt TSpike detector használatával |3 |egész szám |1-10 |3 – 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| zspikedetector.sensitivity |Különbségtételt ZSpike detector használatával |3 |egész szám |1-10 |3 – 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| postprocess.tailRows |A kimeneti eredmények megőrizni a legújabb adatpontok száma |0 |egész szám |a 0 (az összes adatpont tartsa), vagy adja meg, hogy az eredményeket pontszám |– |

### <a name="output"></a>Kimenet
Az API-t futtatja az összes derítik fel az idősoros adatokat, és anomáliadetektálási pontszámok és az egyes bináris kiugrás mutatók időt adja vissza. Az alábbi táblázat felsorolja az API-ból kimenetek.

| Kimenetek | Leírás |
| --- | --- |
| Time |A nyers adatokat, vagy összesített (és/vagy) imputált adatok időbélyegeket Ha összesítés (és/vagy) hiányzik adatok imputálási alkalmazása |
| Adatok |Értékek a nyers adatokat, vagy összesített (és/vagy) imputált adatokat, ha összesítés (és/vagy) hiányzó adatok imputálási alkalmazása |
| TSpike |Bináris jelölő jelzi, hogy ugrásszerű észlel TSpike detector használatával |
| ZSpike |Bináris jelölő jelzi, hogy ugrásszerű észlel ZSpike detector használatával |
| rpscore |Egy lebegőpontos számot jelölő anomáliadetektálási pontszám a kétirányú szint módosítása |
| rpalert |1/0 érték jelzi a kétirányú szintű módosítsa a bemeneti érzékenység szerint anomáliadetektálási |
| tscore |Egy lebegőpontos számot jelölő anomáliadetektálás pontszámot rendelni az a pozitív trend |
| talert |1/0 érték, amely jelzi a hiba egy pozitív trend anomáliadetektálási bemeneti érzékenysége alapján |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
Időbeli adatsorok szezonális minták rendelkező rendellenesség-észlelést futtat a ScoreWithSeasonality API használható. Ez az API hasznos szezonális minták eltérések észlelését.
Az alábbi ábra egy szezonális idősor észlelt rendellenességek egy példát mutat be. A time series rendelkezik egy megnövekedett (az 1-től fekete pont), a két DIP (a 2. fekete pont és a végén egy) és a egy szint módosításának (piros pont). Vegye figyelembe, hogy mindkét a dip közepén az idősor és a szint módosítása csak lekérdezésteljesítmény után szezonális összetevők el lesznek távolítva az adatsorozathoz.
![Szezonalitás API][2]

### <a name="detectors"></a>Érzékelők
A szezonalitás végpontját derítik fel azokat, de nem szezonalitás végpontját (lásd lent) kissé eltérő paraméterek nevei hasonlóak.

### <a name="parameters"></a>Paraméterek

Ezek a bemeneti paraméterek részletesebb információkat az alábbi táblázatban szerepel:

| Bemeneti paraméterek | Leírás | Alapértelmezett beállítás | Típus | Érvényes értékek | Javasolt tartomány |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Összesítési időköz (másodperc) összesítéséhez szükséges bemeneti idősorozat |0 (nincs összesítés történik) |egész szám |0: ellenkező esetben hagyja ki az összesítést, > 0 |1 nap, az idősorozat-függő 5 perc |
| preprocess.aggregationFunc |A függvény a megadott AggregationInterval az adatok összesítéséhez szükséges tartományt |középérték |a felsorolt |mean, sum, hossza |– |
| preprocess.replaceMissing |Hiányzó adatok imputálására értékek |LKV (utolsó ismert érték) |a felsorolt |nulla, lkv, középérték |– |
| detectors.historywindow |Anomáliadetektálás pontszámot törölje a számításhoz használt előzmények (a adatpontok száma) |500 |egész szám |10-2000 |Idősorozat-függő |
| detectors.spikesdips | -E csak hirtelen megugró kihasználtság, csak DIP vagy mindkettő |Mindkettő |a felsorolt |Mindkét, adatforgalmi csúcsokhoz, DIP |Mindkettő |
| bileveldetector.sensitivity |Bizalmassági szint kétirányú detector használatával módosíthatja. |3.25 |double |None |3,25 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| postrenddetector.sensitivity |Pozitív trend detector használatával különbségtételt. |3.25 |double |None |3,25 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| negtrenddetector.sensitivity |Negatív trendek detector használatával különbségtételt. |3.25 |double |None |3,25 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| tspikedetector.sensitivity |Különbségtételt TSpike detector használatával |3 |egész szám |1-10 |3 – 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| zspikedetector.sensitivity |Különbségtételt ZSpike detector használatával |3 |egész szám |1-10 |3 – 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| seasonality.enable |Szezonalitás elemzési végrehajtandó-e |true |logikai |IGAZ, hamis |Idősorozat-függő |
| seasonality.numSeasonality |Rendszeres ciklusok észleltnek maximális száma |1 |egész szám |1, 2 |1-2 |
| seasonality.transform |E szezonális (és) anomáliadetektálás alkalmazása előtt el kell távolítani a trend összetevők |deseason |a felsorolt |nincs, deseason, deseasontrend |– |
| postprocess.tailRows |A kimeneti eredmények megőrizni a legújabb adatpontok száma |0 |egész szám |a 0 (az összes adatpont tartsa), vagy adja meg, hogy az eredményeket pontszám |– |

### <a name="output"></a>Kimenet
Az API-t futtatja az összes derítik fel az idősoros adatokat, és anomáliadetektálási pontszámok és az egyes bináris kiugrás mutatók időt adja vissza. Az alábbi táblázat felsorolja az API-ból kimenetek.

| Kimenetek | Leírás |
| --- | --- |
| Time |A nyers adatokat, vagy összesített (és/vagy) imputált adatok időbélyegeket Ha összesítés (és/vagy) hiányzik adatok imputálási alkalmazása |
| OriginalData |Értékek a nyers adatokat, vagy összesített (és/vagy) imputált adatokat, ha összesítés (és/vagy) hiányzó adatok imputálási alkalmazása |
| ProcessedData |A következők egyikét: <ul><li>A time series szezonálisan igazított, ha a jelentős szezonalitás észlelt, és lehetőség van kijelölve; deseason</li><li>szezonálisan igazított és trendmentes jelentős szezonalitás észlelésekor a time series és deseasontrend lehetőség van kijelölve</li><li>Ellenkező esetben ez megegyezik a OriginalData</li> |
| TSpike |Bináris jelölő jelzi, hogy ugrásszerű észlel TSpike detector használatával |
| ZSpike |Bináris jelölő jelzi, hogy ugrásszerű észlel ZSpike detector használatával |
| BiLevelChangeScore |Egy lebegőpontos számot jelölő anomáliadetektálás pontszámot rendelni az a szint módosítása |
| BiLevelChangeAlert |1/0 értéket jelezve van egy szint módosításának anomáliadetektálási bemeneti érzékenysége alapján |
| PosTrendScore |Egy lebegőpontos számot jelölő anomáliadetektálás pontszámot rendelni az a pozitív trend |
| PosTrendAlert |1/0 érték, amely jelzi a hiba egy pozitív trend anomáliadetektálási bemeneti érzékenysége alapján |
| NegTrendScore |Egy lebegőpontos számot jelölő anomáliadetektálási pontszám a negatív trendek |
| NegTrendAlert |1/0 érték, amely jelzi a hiba egy negatív trendek anomáliadetektálási bemeneti érzékenysége alapján |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

