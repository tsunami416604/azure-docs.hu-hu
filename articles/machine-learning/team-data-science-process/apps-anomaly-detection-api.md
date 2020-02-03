---
title: Az Azure Machine Learning Anomáliadetektálás API – a csoportos adatelemzési folyamat
description: Anomáliadetektálás API, amelyek a észleli a rendellenességeket, amely időben egyenletesen elosztásban numerikus értékek idősorozat-adatokat a Microsoft Azure Machine Learning használatával létrehozott.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: a09094cf0d1bd3c2e299e968d7de8410dcd9c3cb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721880"
---
# <a name="machine-learning-anomaly-detection-api"></a>A Machine Learning Anomáliadetektálás API

> [!NOTE]
> Ez az elem karbantartás alatt áll. Javasoljuk, hogy az Azure Cognitive Services Machine Learning algoritmusait használó [anomália-érzékelő API-szolgáltatást](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) használja az üzleti, működési és IoT mérőszámokból származó rendellenességek észlelésére.

## <a name="overview"></a>Áttekintés
Az [anomália-észlelési API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) egy olyan Azure Machine learning, amely a Time Series-adatokban az időben egyenletesen elhelyezkedő numerikus értékekkel észlelt rendellenességeket észleli.

Az API felismeri a következő típusú rendellenes minták az időbeli:

* **Pozitív és negatív trendek**: Ha például a rendszer a memória használatának figyelése során felhasznál egy emelkedő trendet, előfordulhat, hogy a memória szivárgását jelezhetik,
* **Értékek dinamikus tartományának változásai**: például a Cloud Service által okozott kivételek figyelése esetén az értékek dinamikus tartományának változásai a szolgáltatás állapotának instabilitását jelezhetik, és
* **Tüskék és dipsok**: Ha például egy szolgáltatás bejelentkezési hibáinak számát vagy egy e-kereskedelmi helyen lévő pénztárak számát figyeli, a tüskék vagy a dips rendellenes viselkedést jelezhet.

A machine learning derítik fel ilyen értékek változásainak követése keresztül folyamatban lévő változásai idő- és azok értékeit, anomáliadetektálási pontszámok. Nincs szükségük ad hoc ad hoc küszöbérték finomhangolása és eredményeiket téves riasztási aránnyal vezérlésére használható. Az anomáliadetektálás API hasznos számos olyan szituációkra, mint a figyelést az idő múlásával nyomon követése a KPI-k révén a használat monitorozása keresztül metrikákra keresések száma, a számok kattintással, alkalmazásteljesítmény-figyelő például a memória, Processzor, a teljesítményszámlálók segítségével olvassa be a fájlt, stb. idővel.

Az Anomáliadetektálás ajánlat az első lépésekhez hasznos eszközöket tartalmaz.

* A [webalkalmazás](https://anomalydetection-aml.azurewebsites.net/) segítségével kiértékelheti és megjelenítheti az adatrendellenesség-észlelési API-kat az adatain.

> [!NOTE]
> Próbálja ki [az API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) által működtetett **anomália** -alapú megoldást
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API üzembe helyezés
Annak érdekében, hogy az API-t használ, azt telepítenie kell az Azure-előfizetéshez ahol tárolható az Azure Machine Learning-webszolgáltatásként.  Ezt a [Azure AI Gallery](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)teheti meg.  Ez két Azure Machine Learning Studio (klasszikus) webszolgáltatást (és az azokhoz kapcsolódó erőforrásokat) helyez üzembe az Azure-előfizetésében – az egyiket a szezonális észlelés és a szezonális észlelés nélkül.  Az üzembe helyezés befejezése után az API-kat a [Azure Machine learning Studio (klasszikus) webszolgáltatások](https://services.azureml.net/webservices/) oldaláról kezelheti.  Ezen az oldalon lesz a végpontok helyére, API-kulcsokat, valamint mintakód találhat az API-t hívná.  Részletesebb utasítások [itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice)érhetők el.

## <a name="scaling-the-api"></a>Az API-t méretezése
Alapértelmezés szerint az üzembe helyezés ingyenes fejlesztési/tesztelési számlázási csomaggal rendelkezik, amely 1 000 tranzakciót, hónapot és 2 számítási órát/hónapot tartalmaz.  Az igényeinek megfelelően frissítheti egy másik csomagra.  A különböző csomagok díjszabásáról a "Production web API díjszabása" [című szakaszban olvashat](https://azure.microsoft.com/pricing/details/machine-learning/) bővebben.

## <a name="managing-aml-plans"></a>Csomagok AML kezelése
A számlázási tervet [itt](https://services.azureml.net/plans/)kezelheti.  A csomagnév döntött az API-t üzembe helyezésekor az erőforráscsoport nevét, valamint egy karakterlánc, amely egyedi az előfizetés alapján.  A terv frissítésével kapcsolatos utasítások a "számlázási csomagok kezelése" [szakaszban találhatók.](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice)

## <a name="api-definition"></a>API-definíció
A webszolgáltatás egy REST-alapú API-t biztosít a HTTPS-en keresztül, amely különböző módokon használható, például webes vagy mobil alkalmazások, R, Python, Excel stb. használatával.  Az idősoros adatait REST API híváson keresztül küldi el a szolgáltatásnak, és az alább leírt három rendellenesség-típus kombinációját futtatja.

## <a name="calling-the-api"></a>Az API meghívása
Az API meghívása kell tudni, hogy a végpont helye és API-kulcsot.  Ez a két követelmény, valamint az API meghívásához használható mintakód a [Azure Machine learning Studio (klasszikus) webszolgáltatások](https://services.azureml.net/webservices/) oldaláról érhető el.  Keresse meg a kívánt API-t, majd kattintson a "Felhasználás" lapon találja meg azokat.  Meghívhatja az API-t hencegő API-ként (azaz az URL-paraméterrel `format=swagger`), vagy nem hencegő API-ként (azaz a `format` URL-cím paraméter nélkül).  A mintakód a Swagger-formátumot használja.  Az alábbiakban egy példa kérések és válaszok a a Swagger formátumban van.  Ezekben a példákban vannak a szezonalitás végpontnak.  A nem szezonalitás végpont hasonlít.

### <a name="sample-request-body"></a>Minta-kérelem törzse
A kérelem két objektumot tartalmaz: `Inputs` és `GlobalParameters`.  Az alábbi példa kérelem néhány paraméter kell küldeni explicit módon nem vannak (görgessen le a végpontok paraméterek teljes listája).  A kérelem nem küldött explicit módon paraméterek az alább megadott alapértelmezett értékeket fogja használni.

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
A `ColumnNames` mező megjelenítéséhez a kérelemben URL-ként kell megadni `details=true`.  Tekintse meg az alábbi táblázatokban ezek a mezők mindegyike mögött jelentését.

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
Az alábbi ábra a rendellenességeket, amelyek a pontszám API észleli egy példát mutat be. Ebben az idősorozatban két különböző szintű módosítás és három tüske látható. A piros pötty megjelenítése az idő, amelyen a szint észlelt, miközben a fekete pontok megjelenítése az észlelt adatforgalmi csúcsokhoz.
![pontszám API][1]

### <a name="detectors"></a>Érzékelők
Az anomáliák észlelése API három széles kategóriában támogatja az érzékelők használatát. A megadott bemeneti paramétereket és kimeneteket az egyes detector használatával részletei a következő táblázatban található.

| Kategória detector használatával | Detector használatával | Leírás | Bemeneti paraméterek | Kimenetek |
| --- | --- | --- | --- | --- |
| Megnövekedett érzékelők |TSpike detector használatával |Csúcsok és a DIP sokkal értékei alapján is az első és harmadik quartiles észlelése |*tspikedetector. érzékenység:* egész értéket vesz igénybe a 1-10 tartományban, alapértelmezett érték: 3; A magasabb értékek több szélsőséges értéket kapnak, így kevésbé érzékenyek |TSpike: bináris értékek – "1", ha a megnövekedett/dip észlel, különben "0" |
| Megnövekedett érzékelők | ZSpike detector használatával |Csúcsok és alapján illesztésnek a esetén a rendszer a középérték a DIP észlelése |*zspikedetector. érzékenység:* egész értéket kell megtennie a 1-10 tartományban, alapértelmezett érték: 3; A magasabb értékek több szélsőséges értéket kapnak, ami kevésbé érzékeny |ZSpike: bináris értékek – "1", ha a megnövekedett/dip észlel, különben "0" |
| Lassú Trend detector használatával |Lassú Trend detector használatával |A set-érzékenysége alapján lassú pozitív trend észlelése |*trenddetector. érzékenység:* a detektor pontszámának küszöbértéke (alapértelmezett: 3,25, 3,25 – 5) egy elfogadható tartomány, amely közül választhat. Minél nagyobb a kevésbé érzékeny) |tscore: a trend anomáliadetektálás pontszámot jelölő lebegőpontos szám |
| Szint módosításának érzékelők | Kétirányú szint módosítása detector használatával |Felfelé és lefelé is megfelelően a beállított érzékenységi szint módosításának észlelése |*bileveldetector. érzékenység:* a detektor pontszámának küszöbértéke (alapértelmezett: 3,25, 3,25 – 5) egy elfogadható tartomány, amely közül választhat. Minél nagyobb a kevésbé érzékeny) |rpscore: anomáliadetektálási pontszám a felfelé és lefelé szint módosításának jelölő lebegőpontos szám |

### <a name="parameters"></a>Paraméterek
Ezek a bemeneti paraméterek részletesebb információkat az alábbi táblázatban szerepel:

| Bemeneti paraméterek | Leírás | Alapértelmezett beállítás | Típus | Érvényes értékek | Javasolt tartomány |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Anomáliadetektálás pontszámot törölje a számításhoz használt előzmények (a adatpontok száma) |500 |egész szám |10-2000 |Idősorozat-függő |
| detectors.spikesdips | -E csak hirtelen megugró kihasználtság, csak DIP vagy mindkettő |Mindkettő |a felsorolt |Mindkét, adatforgalmi csúcsokhoz, DIP |Mindkettő |
| bileveldetector.sensitivity |Bizalmassági szint kétirányú detector használatával módosíthatja. |3.25 |double |Nincs |3,25 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| trenddetector.sensitivity |Pozitív trend detector használatával különbségtételt. |3.25 |double |Nincs |3,25 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| tspikedetector.sensitivity |Különbségtételt TSpike detector használatával |3 |egész szám |1-10 |3 – 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| zspikedetector.sensitivity |Különbségtételt ZSpike detector használatával |3 |egész szám |1-10 |3 – 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| postprocess.tailRows |A kimeneti eredmények megőrizni a legújabb adatpontok száma |0 |egész szám |a 0 (az összes adatpont tartsa), vagy adja meg, hogy az eredményeket pontszám |N.A. |

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
Az alábbi ábra egy szezonális idősor észlelt rendellenességek egy példát mutat be. Az idősorozat egy tüske (az első fekete pont), két dips (a második fekete pont és egy végén), valamint egy szint változás (piros pont). Az idősorozat közepén mind a dip, mind a szint változása csak az időszakos összetevőknek az adatsorozatból való eltávolítása után felismerhető.
![szezonális API][2]

### <a name="detectors"></a>Érzékelők
A szezonalitás végpontját derítik fel azokat, de nem szezonalitás végpontját (lásd lent) kissé eltérő paraméterek nevei hasonlóak.

### <a name="parameters"></a>Paraméterek

Ezek a bemeneti paraméterek részletesebb információkat az alábbi táblázatban szerepel:

| Bemeneti paraméterek | Leírás | Alapértelmezett beállítás | Típus | Érvényes értékek | Javasolt tartomány |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Összesítési időköz (másodperc) összesítéséhez szükséges bemeneti idősorozat |0 (nincs összesítés történik) |egész szám |0: ellenkező esetben hagyja ki az összesítést, > 0 |1 nap, az idősorozat-függő 5 perc |
| preprocess.aggregationFunc |A függvény a megadott AggregationInterval az adatok összesítéséhez szükséges tartományt |középérték |a felsorolt |mean, sum, hossza |N.A. |
| preprocess.replaceMissing |Hiányzó adatok imputálására értékek |LKV (utolsó ismert érték) |a felsorolt |nulla, lkv, középérték |N.A. |
| detectors.historywindow |Anomáliadetektálás pontszámot törölje a számításhoz használt előzmények (a adatpontok száma) |500 |egész szám |10-2000 |Idősorozat-függő |
| detectors.spikesdips | -E csak hirtelen megugró kihasználtság, csak DIP vagy mindkettő |Mindkettő |a felsorolt |Mindkét, adatforgalmi csúcsokhoz, DIP |Mindkettő |
| bileveldetector.sensitivity |Bizalmassági szint kétirányú detector használatával módosíthatja. |3.25 |double |Nincs |3,25 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| postrenddetector.sensitivity |Pozitív trend detector használatával különbségtételt. |3.25 |double |Nincs |3,25 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| negtrenddetector.sensitivity |Negatív trendek detector használatával különbségtételt. |3.25 |double |Nincs |3,25 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| tspikedetector.sensitivity |Különbségtételt TSpike detector használatával |3 |egész szám |1-10 |3 – 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| zspikedetector.sensitivity |Különbségtételt ZSpike detector használatával |3 |egész szám |1-10 |3 – 5 (kisebb értékek jelenti azt, hogy a bizalmas adatokat kezelő) |
| seasonality.enable |Szezonalitás elemzési végrehajtandó-e |true |logikai |IGAZ, hamis |Idősorozat-függő |
| seasonality.numSeasonality |Rendszeres ciklusok észleltnek maximális száma |1 |egész szám |1, 2 |1-2 |
| seasonality.transform |E szezonális (és) anomáliadetektálás alkalmazása előtt el kell távolítani a trend összetevők |deseason |a felsorolt |nincs, deseason, deseasontrend |N.A. |
| postprocess.tailRows |A kimeneti eredmények megőrizni a legújabb adatpontok száma |0 |egész szám |a 0 (az összes adatpont tartsa), vagy adja meg, hogy az eredményeket pontszám |N.A. |

### <a name="output"></a>Kimenet
Az API-t futtatja az összes derítik fel az idősoros adatokat, és anomáliadetektálási pontszámok és az egyes bináris kiugrás mutatók időt adja vissza. Az alábbi táblázat felsorolja az API-ból kimenetek.

| Kimenetek | Leírás |
| --- | --- |
| Time |A nyers adatokat, vagy összesített (és/vagy) imputált adatok időbélyegeket Ha összesítés (és/vagy) hiányzik adatok imputálási alkalmazása |
| OriginalData |Értékek a nyers adatokat, vagy összesített (és/vagy) imputált adatokat, ha összesítés (és/vagy) hiányzó adatok imputálási alkalmazása |
| ProcessedData |A következő lehetőségek egyike: <ul><li>A time series szezonálisan igazított, ha a jelentős szezonalitás észlelt, és lehetőség van kijelölve; deseason</li><li>szezonálisan igazított és trendmentes jelentős szezonalitás észlelésekor a time series és deseasontrend lehetőség van kijelölve</li><li>Ellenkező esetben ez a beállítás megegyezik a OriginalData</li> |
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

