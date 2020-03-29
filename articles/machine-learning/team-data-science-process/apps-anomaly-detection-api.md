---
title: Azure Machine Learning anomáliaészlelési API – Csapatadat-elemzési folyamat
description: Az Anomáliadetektálás i. API egy microsoft Azure Machine Learning szolgáltatással készített példa, amely észleli az idősorozat-adatok anomáliáit, amelyek számértékeket tartalmaznak, amelyek egyenletesen vannak elosztva az időben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721880"
---
# <a name="machine-learning-anomaly-detection-api"></a>Gépi tanulási anomáliadetektálási API

> [!NOTE]
> Ez az elem karbantartás alatt áll. Azt javasoljuk, hogy használja az [Anomália-detektor API-szolgáltatás](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) által működtetett, az Azure Cognitive Services gépi tanulási algoritmusainak galériája segítségével észlelheti az üzleti, működési és IoT-metrikák anomáliákat.

## <a name="overview"></a>Áttekintés
[Az Anomáliadetektálási API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) egy olyan példa, amely az Azure Machine Learning szolgáltatással készült példa, amely észleli az idősorozat-adatok anomikus értékeit tartalmazó anomáliákat, amelyek egyenletesen vannak elosztva az időben.

Ez az API a következő típusú rendellenes mintákat képes észlelni az idősorozat-adatokban:

* **Pozitív és negatív tendenciák**: Például a memóriahasználat számítógépes használatának megfigyelése esetén egy emelkedő tendencia érdekes lehet, mivel memóriavesztésre utalhat,
* **Az értékek dinamikus tartományának változásai**: Például egy felhőszolgáltatás által okozott kivételek figyelésekénél a dinamikus értéktartomány bármilyen változása a szolgáltatás állapotának instabilitását jelezheti, és
* **Tüskék és dipek:** Ha például egy szolgáltatás bejelentkezési hibáinak vagy egy e-kereskedelmi webhelyen a kijelentkezések számának figyelése, a kiugrások vagy a dipek rendellenes viselkedést jelezhetnek.

Ezek a gépi tanulási érzékelők nyomon követik az értékek időbeli változásait, és az értékek anomáliapontszámokként jelentik az értékek folyamatos változásait. Nem igényelnek adhoc küszöbhangolást, és pontszámaik felhasználhatók a hamis pozitív arány szabályozására. Az anomáliadetektálási API számos esetben hasznos, például a szolgáltatás figyelése a KPI-k idővel történő nyomon követésével, a használat figyelése mérőszámokkal, például a keresések számával, a kattintások számával, a teljesítményfigyeléssel a számlálókon keresztül, például a memórián, a processzoron, a fájlolvasásokon stb. idővel.

Az Anomáliadetektálás imát, amely hasznos eszközöket tartalmaz a kezdéshez.

* A [webalkalmazás](https://anomalydetection-aml.azurewebsites.net/) segítségével kiértékelheti és vizualizálhatja az anomáliadetektálási API-k eredményeit az adatokon.

> [!NOTE]
> Próbálja ki **az IT Anomaly Insights megoldást,** amely [et ez az API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) működteti
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API-telepítés
Az API használatához telepítenie kell az Azure-előfizetésében, ahol az Azure Machine Learning webszolgáltatásként lesz üzemeltetve.  Ezt az Azure [AI-galériából](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)teheti meg.  Ez két Azure Machine Learning Studio (klasszikus) webszolgáltatást (és a hozzájuk kapcsolódó erőforrásokat) telepít az Azure-előfizetésbe – egyet a szezonalitás-észleléssel rendelkező anomáliafelismeréshez, egyet pedig szezonalitásészlelés nélkül.  Miután a központi telepítés befejeződött, az Api-k at az [Azure Machine Learning Studio (klasszikus) webszolgáltatások](https://services.azureml.net/webservices/) lapjáról kezelheti.  Ezen az oldalon megtalálhatja a végponti helyeket, az API-kulcsokat, valamint az API-híváshoz használható mintakódot.  Részletesebb utasítások [itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice)találhatók.

## <a name="scaling-the-api"></a>Az API méretezése
Alapértelmezés szerint a központi telepítés lesz egy ingyenes fejlesztési/tesztelési számlázási csomag, amely tartalmazza a 1000 tranzakciók / hó és 2 számítási óra/hó.  Frissíthet egy másik tervre az igényeinek megfelelően.  A különböző csomagok díjszabásának részletei [itt,](https://azure.microsoft.com/pricing/details/machine-learning/) az "Éles webes API-díjszabás" alatt találhatók.

## <a name="managing-aml-plans"></a>AML-csomagok kezelése
A számlázási csomagot [itt](https://services.azureml.net/plans/)kezelheti.  A terv neve az API üzembe helyezésekor kiválasztott erőforráscsoport-név, valamint az előfizetésegyedi karakterlánca alapján történik.  A csomag frissítésével kapcsolatos utasítások [itt,](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) a "Számlázási csomagok kezelése" című részben találhatók.

## <a name="api-definition"></a>API-definíció
A webszolgáltatás egy REST-alapú API-t biztosít HTTPS-en keresztül, amely különböző módokon használható fel, beleértve egy webes vagy mobilalkalmazást, R- t, Pythont, Excelt stb.  Az idősorozat-adatokat egy REST API-híváson keresztül küldi el a szolgáltatásnak, és az az alábbiakban ismertetett három anomáliatípus kombinációját futtatja.

## <a name="calling-the-api"></a>Az API hívása
Az API-híváshoz ismernie kell a végpont helyét és az API-kulcsot.  Ez a két követelmény, valamint az API-hívás mintakódja az [Azure Machine Learning Studio (klasszikus) webszolgáltatások](https://services.azureml.net/webservices/) lapjáról érhető el.  Keresse meg a kívánt API-t, majd kattintson a "Felhasználás" fülre, hogy megtalálja őket.  Az API-t Swagger API-ként (azaz az `format=swagger`URL-paraméterrel) vagy nem Swagger API-ként (azaz az `format` URL-paraméter nélkül) hívhatja meg.  A mintakód a Swagger formátumot használja.  Az alábbiakban egy példa kérés és válasz nem Swagger formátumban.  Ezek a példák a szezonalitásvégpont.  A nem szezonalitásvégpont hasonló.

### <a name="sample-request-body"></a>Mintakérelem törzse
A kérelem két `Inputs` objektumot tartalmaz: és `GlobalParameters`.  Az alábbi példakérelemben egyes paraméterek et a rendszer explicit módon küldi el, míg másokat nem (görgessen le az egyes végpontok paramétereinek teljes listájához).  A kérelemben kifejezetten nem küldött paraméterek az alábbi alapértelmezett értékeket fogják használni.

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
A `ColumnNames` mező megtekintéséhez url-paraméterként kell szerepelnie `details=true` a kérelemben.  Az alábbi táblázatokban az egyes mezők mögötti jelentésről olvashat.

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
A Score API nem szezonális idősorozat-adatokon való anomáliadetektálásfuttatására szolgál. Az API számos anomáliadetektort futtat az adatokon, és visszaadja az anomáliapontszámaikat.
Az alábbi ábra egy példát mutat be a Score API által észlelhető anomáliákra. Ez az idősorozat két különböző szintváltozást és három csúcsot. A piros pont azt az időt mutatja, amikor a rendszer észleli a szintváltozást, míg a fekete pont az észlelt tüskéket.
![Pontszám API][1]

### <a name="detectors"></a>Érzékelők
Az anomáliadetektálási API három nagy kategóriába tartozó érzékelőket támogat. Az egyes detektorok adott bemeneti paramétereire és kimeneteire vonatkozó részletek az alábbi táblázatban találhatók.

| Detektor kategória | Detektor | Leírás | Bemeneti paraméterek | Kimenetek |
| --- | --- | --- | --- | --- |
| Tüske detektorok |Tspike detektor |Észlelése tüskék és dips alapján messze az értékek az első és a harmadik kvartilisek |*tspikedetector.sensitivity:* egész értéket vesz fel az 1-10 tartományban, alapértelmezett: 3; A magasabb értékek szélsőségesebb értékeket fognak elkapni, így kevésbé érzékenyek |TSpike: bináris értékek – '1', ha a tüske / dip észlelése, "0" egyébként |
| Tüske detektorok | ZSpike detektor |A kiugrások és a dipek észlelése annak alapján, hogy az adatpontok milyen messze vannak az átlagos |*zspikedetector.sensitivity:* vegye egész érték a tartományban 1-10, alapértelmezett: 3; A magasabb értékek szélsőségesebb értékeket fognak elkapni, így kevésbé érzékenyek |ZSpike: bináris értékek – '1', ha tüske/dip észlelhető, "0" egyébként |
| Lassú trendérzékelő |Lassú trendérzékelő |Lassú pozitív trend észlelése a beállított érzékenység szerint |*trenddetector.sensitivity:* küszöbérték detektor pontszám (alapértelmezett: 3,25, 3,25 - 5 egy ésszerű tartomány közül lehet választani; Minél magasabb, annál kevésbé érzékeny) |tscore: lebegő szám, amely az anomália pontszámot képviseli a trenden |
| Szintváltozás-érzékelők | Kétirányú szintváltozás-érzékelő |Érzékeli mind a felfelé, mind a lefelé irányuló szintváltozást a beállított érzékenységnek |*bileveldetector.sensitivity:* küszöbérték az érzékelő pontszámán (alapértelmezett: 3,25, 3,25 – 5 ésszerű tartomány a kiválasztáshoz; Minél magasabb, annál kevésbé érzékeny) |rpscore: lebegő szám, amely az anomáliapontszámmal jelöl a felfelé és lefelé irányuló szintváltozáson |

### <a name="parameters"></a>Paraméterek
Ezekről a bemeneti paraméterekről részletesebb információkat az alábbi táblázat sorol fel:

| Bemeneti paraméterek | Leírás | Alapértelmezett beállítás | Típus | Érvényes tartomány | Javasolt tartomány |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Előzmények (adatpontok számában) az anomáliapontszám-számításhoz |500 |egész szám |10-2000 |Idősoroktól függő |
| detectors.spikesdips | Azt jelzi, hogy csak a tüskéket, csak a dips- et vagy mindkettőt |Mindkettő |Felsorolt |Mindkettő, Tüskék, Mártások |Mindkettő |
| bileveldetector.sensitivity |Érzékenység kétirányú szintváltozás-érzékelőhöz. |3,25 |double |None |3.25-5 (A kisebb értékek érzékenyebbek) |
| trenddetektor.érzékenység |Érzékenység a pozitív trenddetektorhoz. |3,25 |double |None |3.25-5 (A kisebb értékek érzékenyebbek) |
| tspikedetector.sensitivity |Érzékenység A TSpike detektor |3 |egész szám |1-10 |3-5 (A kisebb értékek érzékenyebbek) |
| zspikedetector.sensitivity |Érzékenység ZSpike detektorhoz |3 |egész szám |1-10 |3-5 (A kisebb értékek érzékenyebbek) |
| postprocess.tailSorok |A legfrissebb adatpontok száma, amelyeket meg kell őrizni a kimeneti eredményekben |0 |egész szám |0 (az összes adatpont megtartása), vagy adja meg az eredményekben tartandó pontok számát |N/A |

### <a name="output"></a>Kimenet
Az API az összes érzékelőt futtatja az idősorozat-adatokon, és anomáliapontszámokat és bináris csúcsjelzőket ad vissza az egyes időpontokhoz. Az alábbi táblázat az API-ból származó kimeneteket sorolja fel.

| Kimenetek | Leírás |
| --- | --- |
| Time |A nyers adatokból származó időbélyegek, vagy összesített (és/vagy) imputált adatok, ha aggregáció (és/vagy) hiányzó adatimputálás tanusít(vagy) van alkalmazva |
| Adatok |Nyers adatokból származó értékek, vagy összesített (és/vagy) imputált adatok, ha aggregáció (és/vagy) hiányzó adatimputálás tanusít(vagy) kerül alkalmazásra |
| TSpike között |Bináris kijelző, amely jelzi, hogy a TSpike detektor észlel-e egy tüskét |
| ZSpike között |Bináris kijelző, amely jelzi, hogy a ZSpike detektor észlel-e egy tüskét |
| rppontszám |Akétirányításos szintváltozáson az anomáliapontszámot ábrázoló lebegő szám |
| rpalert |1/0 érték, amely kétirányú szintváltozási anomáliát jelez a bemeneti érzékenység alapján |
| tscore (tscore) |Az anomáliamutatót képviselő lebegő szám pozitív trendre |
| talert |1/0 érték, amely pozitív trendanomáliát jelez a bemeneti érzékenység alapján |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
A ScoreWithSeasonality API-t az anomáliadetektálás szezonális mintákkal rendelkező idősorozatokon való futtatására használják. Ez az API a szezonális minták eltéréseinek észleléséhez hasznos.
Az alábbi ábra egy szezonális idősorozatban észlelt anomáliák példáját mutatja be. Az idősorozatnak egy tüskéje van (az első fekete pont), két dipje (a második fekete pont és egy a végén), és egy szintváltozás (piros pont). Mind a dip közepén az idősorozat és a szintváltozás csak akkor érzékelhető, miután szezonális alkatrészek eltávolítása a sorozatból.
![Szezonalitás API][2]

### <a name="detectors"></a>Érzékelők
A szezonalitási végpontban lévő érzékelők hasonlóak a nem szezonalitási végpontban lévőkhöz, de kissé eltérő paraméternevekkel (lásd alább).

### <a name="parameters"></a>Paraméterek

Ezekről a bemeneti paraméterekről részletesebb információkat az alábbi táblázat sorol fel:

| Bemeneti paraméterek | Leírás | Alapértelmezett beállítás | Típus | Érvényes tartomány | Javasolt tartomány |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |A bemeneti idősorok összesítésének összesítési időköze másodpercben |0 (nincs összesítés) |egész szám |0: az aggregáció kihagyása, > 0 egyébként |5 perc 1 nap, idősorozat függő |
| preprocess.aggregationFunc |Adatok összesítéséhez használt függvény a megadott AggregationInterval értékbe |középérték |Felsorolt |átlag, összeg, hossz |N/A |
| preprocess.replaceMissing |A hiányzó adatok imputálására használt értékek |lkv (utolsó ismert érték) |Felsorolt |nulla, lkv, átlagos |N/A |
| detectors.historywindow |Előzmények (adatpontok számában) az anomáliapontszám-számításhoz |500 |egész szám |10-2000 |Idősoroktól függő |
| detectors.spikesdips | Azt jelzi, hogy csak a tüskéket, csak a dips- et vagy mindkettőt |Mindkettő |Felsorolt |Mindkettő, Tüskék, Mártások |Mindkettő |
| bileveldetector.sensitivity |Érzékenység kétirányú szintváltozás-érzékelőhöz. |3,25 |double |None |3.25-5 (A kisebb értékek érzékenyebbek) |
| postrenddetector.sensitivity |Érzékenység a pozitív trenddetektorhoz. |3,25 |double |None |3.25-5 (A kisebb értékek érzékenyebbek) |
| negtrenddetector.sensitivity |Érzékenység a negatív trenddetektorhoz. |3,25 |double |None |3.25-5 (A kisebb értékek érzékenyebbek) |
| tspikedetector.sensitivity |Érzékenység A TSpike detektor |3 |egész szám |1-10 |3-5 (A kisebb értékek érzékenyebbek) |
| zspikedetector.sensitivity |Érzékenység ZSpike detektorhoz |3 |egész szám |1-10 |3-5 (A kisebb értékek érzékenyebbek) |
| szezonalitás.enable |A szezonalitási elemzést kell-e végezni |igaz |logikai |igaz, hamis |Idősoroktól függő |
| szezonalitás.numSzezoniitás |Az észlelendő időszakos ciklusok maximális száma |1 |egész szám |1, 2 |1-2 |
| szezonalitás.transform |Az anomáliadetektálás alkalmazása előtt el kell-e távolítani a szezonális (és) trendösszetevőket |deseason |Felsorolt |nincs, deseason, deseasontrend |N/A |
| postprocess.tailSorok |A legfrissebb adatpontok száma, amelyeket meg kell őrizni a kimeneti eredményekben |0 |egész szám |0 (az összes adatpont megtartása), vagy adja meg az eredményekben tartandó pontok számát |N/A |

### <a name="output"></a>Kimenet
Az API az összes érzékelőt futtatja az idősorozat-adatokon, és anomáliapontszámokat és bináris csúcsjelzőket ad vissza az egyes időpontokhoz. Az alábbi táblázat az API-ból származó kimeneteket sorolja fel.

| Kimenetek | Leírás |
| --- | --- |
| Time |A nyers adatokból származó időbélyegek, vagy összesített (és/vagy) imputált adatok, ha aggregáció (és/vagy) hiányzó adatimputálás tanusít(vagy) van alkalmazva |
| Eredeti adatok |Nyers adatokból származó értékek, vagy összesített (és/vagy) imputált adatok, ha aggregáció (és/vagy) hiányzó adatimputálás tanusít(vagy) kerül alkalmazásra |
| Feldolgozott adatok |Az alábbi lehetőségek bármelyike: <ul><li>Szezonálisan kiigazított idősorok, ha jelentős szezonalitást észleltek, és a szezonelmi opciót választották;</li><li>szezonálisan kiigazított és detrended idősorok, ha jelentős szezonalitást észleltek, és deseasontrend opciót választottak</li><li>ellenkező esetben ez a beállítás megegyezik az OriginalData</li> |
| TSpike között |Bináris kijelző, amely jelzi, hogy a TSpike detektor észlel-e egy tüskét |
| ZSpike között |Bináris kijelző, amely jelzi, hogy a ZSpike detektor észlel-e egy tüskét |
| BiLevelChangeScore |Aszintváltozáson alapuló anomáliapontszámot ábrázoló lebegő szám |
| BiLevelChangeAlert |1/0 érték, amely azt jelzi, hogy a bemeneti érzékenység alapján szintváltozási anomália van |
| PosTrendScore |Az anomáliamutatót képviselő lebegő szám pozitív trendre |
| PosTrendAlert |1/0 érték, amely pozitív trendanomáliát jelez a bemeneti érzékenység alapján |
| NegTrendScore |Az anomáliapontszámot jelző lebegő szám negatív trendre |
| NegTrendAlert |1/0 érték, amely negatív trendanomáliát jelez a bemeneti érzékenység alapján |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

