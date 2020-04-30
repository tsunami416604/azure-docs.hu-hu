---
title: Az adathalmazok adateltolódásának elemzése és figyelése (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Hozzon létre Azure Machine Learning adatkészleteket figyelőket (előzetes verzió), figyelje az adatsorok adateltolódását, és állítson be riasztásokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: e49c621d92a8aa604b5f95291c5d80c0141f41dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682725"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Adatcsere (előzetes verzió) észlelése az adatkészleteken
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészlet-figyelőket (előzetes verzió), hogyan figyelheti az adathalmazok statisztikai változásait, és hogyan állíthatja be a riasztásokat.

Azure Machine Learning adatkészlet-figyelőkkel a következőket teheti:
* **Elemezze az adateltolódást az adataiban** , hogy megértse, hogyan változik az idő múlásával.
* A **modellre vonatkozó adatokat figyelheti** a képzés és az adatkészletek kiszolgálásához.
* Az új adatokat az alapkonfiguráció és a célként megadott adatkészlet közötti különbségekkel **figyelheti** .
* **Az adatprofil-funkciók** segítségével követheti nyomon a statisztikai tulajdonságok időbeli változását.
* **Riasztásokat állíthat be az adateltolódással** kapcsolatban a lehetséges problémákkal kapcsolatos korai figyelmeztetésekhez. 

A metrikák és az adatellenőrzések az Azure Machine Learning munkaterülethez társított [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) -erőforráson keresztül érhetők el.

> [!Important]
> Vegye figyelembe, hogy az SDK-val végzett figyelési adateltolódás minden kiadásban elérhető, míg a webes studión keresztüli adateltolódás monitorozása csak a vállalati kiadásban lehetséges.

## <a name="prerequisites"></a>Előfeltételek

Az adatkészlet-figyelők létrehozásához és működéséhez a következőkre lesz szüksége:
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.
* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md).
* A [Azure Machine learning SDK for Python telepítve](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), amely tartalmazza a azureml-adatkészletek csomagot.
* Strukturált (táblázatos) adatokat az adatokat tartalmazó fájl elérési útja, fájlneve vagy oszlopa által megadott időbélyeggel.

## <a name="what-is-data-drift"></a>Mi az az adateltolódás?

A gépi tanulás kontextusában az adateltolódás a modellben a teljesítmény romlását eredményező bemeneti adatok változása. Ez az egyik legfontosabb ok, hogy a modell pontossága az idő múlásával csökken, így az adateltolódás monitorozása segít a modell teljesítményével kapcsolatos problémák észlelésében.

Az adateltolódás okai a következők: 

- A felsőbb rétegbeli folyamatok változásai, például egy lecserélt érzékelő, amely a mérési egységeket hüvelykről centiméterre módosítja. 
- Az adatminőségi problémák, például a hibás szenzorok mindig a 0 értéket olvassák.
- Természetes eltolódás az adatpontokban, például az átlagos hőmérséklet az évszakok szerint változik.
- A szolgáltatások vagy az egyváltozós váltás közötti kapcsolat módosítása. 

A Azure Machine Learning adatkészlet figyelői olyan riasztásokat állíthatnak be, amelyek az adathalmazokban az adateltolódások észlelését segítik az idő múlásával. 

### <a name="dataset-monitors"></a>Adatkészlet-figyelők 

Létrehozhat egy adatkészlet-figyelőt az adathalmaz új adateltolódásának észleléséhez és riasztásához, a korábbi adatokat elemezheti a drift számára, és az új adatokat az idő múlásával. Az adatdrift algoritmus az adatok változásának általános mértékét nyújtja, és jelzi, hogy mely szolgáltatások felelősek a további vizsgálatért. Az adatkészlet-figyelők számos más metrikát hoznak létre az `timeseries` adatkészletben lévő új adatok profilkészítésével. Egyéni riasztások állíthatók be a figyelő által az [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)használatával létrehozott összes mérőszámon. Az adatkészlet-figyelők használatával gyorsan elvégezhető az adatvesztés, és csökkentheti a probléma hibakeresésének idejét a valószínűleg előforduló okok azonosításával.  

Elméletileg három fő forgatókönyv áll rendelkezésre az adatkészlet-figyelők beállításához Azure Machine Learningban.

Forgatókönyv | Leírás
---|---
A modellnek a modell betanítási adatokból való eltolódására szolgáló adatok figyelése | Ennek a forgatókönyvnek az eredményei a modell pontosságára szolgáló proxy figyelésére használhatók, mivel a modell pontossága csökkenti, ha a kiszolgált adatok a betanítási adatokból származnak.
Idősorozat-adatkészlet figyelése egy korábbi időszakból származó eltolódáshoz. | Ez a forgatókönyv általánosabb, és a modell kiépítése során a felsőbb rétegbeli vagy az ahhoz kapcsolódó adatkészletek figyelésére használható.  A célként megadott adatkészletnek időbélyeg-oszloptal kell rendelkeznie, míg az alapkonfiguráció adatkészlete bármely táblázatos adatkészlet lehet, amely közösen tartalmazza a célként megadott adatkészletet.
A múltbeli adatok elemzésének végrehajtása. | Ez a forgatókönyv felhasználható a korábbi adathalmazok megismerésére és a döntések meghozatalára az adatkészlet-figyelők beállításaiban.

## <a name="how-dataset-can-monitor-data"></a>Az adatkészlet figyelése az adatkészletben

A Azure Machine Learning használatával az adateltolódást az adatkészletek figyelik. Az adateltolódás figyeléséhez egy alapkonfigurációt – általában a modell betanítási adatkészletét – kell megadni. A célként megadott adatkészletek – általában a bemeneti adatok modellezése – összehasonlítjuk az alapkonfigurációval. Ez az összehasonlítás azt jelenti, hogy a célként megadott adatkészlet esetében meg kell adni egy időbélyeg-oszlopot.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>A `timeseries` tulajdonság beállítása a cél adatkészletben

A célként megadott adatkészletnek a `timeseries` tulajdonságot úgy kell beállítania, hogy az időbélyegző oszlopot az adatok egyik oszlopával vagy egy, a fájlok elérési útjából származtatott virtuális oszloppal adja meg. Ezt a Python SDK vagy a Azure Machine Learning Studio használatával teheti meg. Az adatkészlethez a tulajdonságok hozzáadásához `timeseries` meg kell adni egy "részletes gabona" időbélyeget jelképező oszlopot. Ha az adatok particionálva vannak az időadatokkal (például "{éééé/hh/nn}"), létrehozhat egy virtuális oszlopot az elérésiút-minta beállítással, és beállíthatja a "durva gabona" időbélyeget az idősorozat-funkciók fontosságának növelése érdekében. 

#### <a name="python-sdk"></a>Python SDK

Az [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) osztály [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) metódusa határozza meg az adatkészlethez tartozó időbélyegző oszlopot. 

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Az adatkészletek `timeseries` tulajdonságaival kapcsolatos teljes példaként tekintse meg a [Jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) vagy az [adatkészletek SDK dokumentációját](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Ha Azure Machine Learning Studióval hozza létre az adatkészletet, ügyeljen arra, hogy az adatok elérési útja tartalmazza az adatokhoz tartozó összes almappát, és állítsa be a partíció formátumát. 

A következő példában a *NoaaIsdFlorida/2019* almappában található összes adat létrejön, a partíció formátuma pedig a timestamp év, hónap és nap oszlopát adja meg. 

[![Partíció formátuma](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

A **séma** beállításainál adja meg a megadott adatkészlet virtuális vagy valódi oszlopának timestamp oszlopát:

![Időbélyeg](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Adatkészlet-figyelő beállításai

Miután létrehozta az adatkészletet a megadott időbélyeg-beállításokkal, készen áll az adatkészlet-figyelő konfigurálására.

A különböző adatkészlet-figyelő beállítások három csoportra oszlanak: **alapszintű információ, a beállítások figyelése** és a **backfill beállításai**.

### <a name="basic-info"></a>Alapszintű információ

Ez a táblázat az adatkészlet-figyelő alapszintű beállításait tartalmazza.

| Beállítás | Leírás | Tippek | Változtatható | 
| ------- | ----------- | ---- | ------- | 
| Name (Név) | Az adatkészlet figyelő neve. | | Nem |
| Alapterv-adatkészlet | Táblázatos adatkészlet, amely a célként megadott adatkészlet időbeli összehasonlításához használt alapkonfigurációként fog megjelenni. | Az alapadatkészletnek a célként megadott adatkészlettel közös szolgáltatásokkal kell rendelkeznie. Az alaptervet általában a modell betanítási adatkészletére vagy a célként megadott adatkészlet egy szeletére kell beállítani. | Nem |
| Cél adatkészlet | A táblázatos adatkészlet, amely az adateltolódás elemzésére szolgáló időbélyeg-oszloppal van megadva. | A célként megadott adatkészletnek közös szolgáltatásokkal kell rendelkeznie az alapkonfigurációhoz, és `timeseries` olyan adatkészletnek kell lennie, amelybe a rendszer hozzáfűzi az új adatokat. A célként megadott adatkészletben lévő korábbi adatokat elemezni lehet, vagy az új adatokat lehet figyelni. | Nem | 
| Frequency | A folyamat feladatainak beütemezett és a korábbi adatok elemzéséhez használt gyakoriság, ha backfill fut. A beállítások a következők: naponta, hetente vagy havonta. | Módosítsa ezt a beállítást úgy, hogy az az alaptervhez hasonló adatméretet tartalmazzon. | Nem | 
| Szolgáltatások | Azoknak a szolgáltatásoknak a listája, amelyeket az adateltolódás az idő múlásával elemezni fog. | A modell kimeneti funkciójának beállítása a koncepciók eltolódásának mérésére. Ne tartalmazzon olyan funkciókat, amelyek természetesen az idő függvényében sodródnak (hónap, év, index stb.). A szolgáltatások listájának módosítása után a backfill és a meglévő adatdrift-figyelő is megadható. | Igen | 
| Számítási cél | Azure Machine Learning számítási célt az adatkészlet-figyelő feladatok futtatásához. | | Igen | 

### <a name="monitor-settings"></a>Beállítások figyelése

Ezek a beállítások az ütemezett adatkészlet-figyelő folyamathoz tartoznak, amelyet a rendszer létrehoz. 

| Beállítás | Leírás | Tippek | Változtatható | 
| ------- | ----------- | ---- | ------- |
| Bekapcsolás | Az adatkészlet-figyelő folyamatának ütemezett engedélyezése vagy letiltása | Tiltsa le az ütemtervet, hogy elemezze a korábbi adatértékeket a backfill beállítással. Az adatkészlet-figyelő létrehozása után is engedélyezhető. | Igen | 
| Késés | Az adatkészletbe való beérkezéshez szükséges idő (óra). Ha például három napot vesz igénybe az adatkészletet tároló SQL-ADATBÁZISba való beérkezési adat, a késést állítsa 72-re. | Az adatkészlet-figyelő létrehozása után nem módosítható | Nem | 
| E-mail cím | A riasztások e-mail-címei az adateltolódás százalékos küszöbértékének megszegése alapján. | Az e-maileket Azure Monitor küldi el a rendszer. | Igen | 
| Küszöbérték | Az e-mail riasztások százalékos küszöbértéke az e-mailek küldéséhez. | További riasztások és események a munkaterület társított Application Insights erőforrásának számos más metrikáján is megadhatók. | Igen | 

### <a name="backfill-settings"></a>Backfill-beállítások

Ezek a beállítások a múltbeli adatok backfill futtatására szolgálnak az adateltolódás metrikái esetében.

| Beállítás | Leírás | Tippek |
| ------- | ----------- | ---- |
| Kezdő dátum | A backfill-feladathoz tartozó kezdési dátum. | | 
| Záródátum | A backfill-feladathoz tartozó befejezési dátum. | A befejezési dátum nem lehet több, mint 31 * gyakorisági egység a kezdő dátumtól. Egy meglévő adatkészlet-figyelőn a metrikák visszatöltötte a korábbi adatok elemzéséhez, illetve a metrikák a frissített beállításokkal való lecseréléséhez. |

## <a name="create-dataset-monitors"></a>Adatkészlet-figyelők létrehozása 

Hozzon létre adatkészlet-figyelőket a Azure Machine Learning Studióval vagy a Python SDK-val egy új adatkészlet adateltolódásának észleléséhez és riasztásához. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Ha riasztásokat szeretne beállítani az adatkészlet figyelője számára, akkor az adatkészletet tartalmazó munkaterületnek, amely a figyelőt szeretné létrehozni, vállalati kiadási képességekkel kell rendelkeznie. 

A munkaterület funkciójának megerősítése után navigáljon a Studio kezdőlapjára, és kattintson a bal oldali adatkészletek fülre. Válassza az adatkészlet-figyelők elemet.

![Figyelési lista](./media/how-to-monitor-datasets/monitor-list.png)

Kattintson a **+ figyelő létrehozása** gombra, és folytassa a varázslót **a Tovább gombra kattintva.**

![Varázsló](./media/how-to-monitor-datasets/wizard.png)

Az eredményül kapott adatkészlet figyelője megjelenik a listában. Válassza ki, hogy megnyissa a figyelő részleteket tartalmazó lapját.

### <a name="from-python-sdk"></a>Python SDK-ból

A teljes részletekért tekintse [meg a PYTHON SDK dokumentációját az adateltolódásról](/python/api/azureml-datadrift/azureml.datadrift) . 

Az alábbi példa bemutatja, hogyan hozhat létre egy adatkészlet-figyelőt a Python SDK használatával

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

Az `timeseries` adatkészletek és adatdrift-detektorok beállításának teljes példáját a [példaként szolgáló jegyzetfüzetben](https://aka.ms/datadrift-notebook)tekintheti meg.

## <a name="understanding-data-drift-results"></a>Az adateltolódás eredményeinek ismertetése

Az adatfigyelő két eredményt hoz létre: a drift áttekintése és a szolgáltatás részletei. A következő animáció az elérhető drift figyelő diagramokat mutatja be a kiválasztott szolgáltatás és metrika alapján. 

![Bemutató videó](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>A drift áttekintése

Az **Átsodródás áttekintése** szakasz az adateltolódások nagyságával kapcsolatos legfelső szintű elemzéseket tartalmazza, és hogy mely funkciókat kell megvizsgálni. 

| Metrika | Leírás | Tippek | 
| ------ | ----------- | ---- | 
| Adateltolódás nagysága | Az alapkonfiguráció és a célként megadott adatkészlet időbeli eloszlása. 0 és 100 között, ahol a 0 azt jelzi, hogy az azonos adatkészletek és a 100 azt jelzi, hogy a Azure Machine Learning adateltolódási képesség teljes mértékben tudja megállapítani a két adathalmazt. | A rendszer a megfelelő mérési arányban mért zaj miatt várhatóan a gépi tanulási technikákat használja a magnitúdó létrehozásához. | 
| Drift-közreműködés a funkció alapján | A cél adatkészlet egyes funkcióinak a mért eltolódási magnitúdóhoz való hozzájárulása. |  Az egyváltozós váltás miatt a szolgáltatások mögöttes eloszlása nem feltétlenül szükséges ahhoz, hogy a szolgáltatás viszonylag magas szintű legyen. | 

Az alábbi ábrán egy példa látható a **drift áttekintés** eredményei Azure Machine learning Studióban, a [NOAA integrált Surface-adatok](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)backfill eredményeként. Az adatokat a rendszer a `stationName contains 'FLORIDA'`következőként használta, és január 2019-én használták alapadatkészletként, valamint a célként használt összes 2019-adat.
 
![A drift áttekintése](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Szolgáltatás részletei

A **szolgáltatás részletei** szakasz a kiválasztott szolgáltatás terjesztésének változását, valamint az egyéb statisztikákat az idő múlásával mutatja be. 

A célként megadott adatkészlet az idő múlásával is bekerül. Az egyes szolgáltatások alapkonfiguráció-eloszlása közötti statisztikai távolságot a rendszer összehasonlítja a célként megadott adatkészlettel, amely koncepcionálisan hasonló az adateltolódási mértékhez, azzal a kivétellel, hogy ez a statisztikai távolság egy adott szolgáltatáshoz tartozik. A min, a Max és a Mean is elérhető. 

Ha a gráf egyik adatpontjára kattint, akkor a Azure Machine Learning Studióban a megjelenő funkció eloszlása ennek megfelelően lesz módosítva. Alapértelmezés szerint megjeleníti az alapadatkészlet eloszlását és a legutóbbi futtatási eloszlását. 

Ezek a metrikák a Python SDK-ban is beolvashatók egy `get_metrics()` `DataDriftDetector` objektum metódusával. 

#### <a name="numeric-features"></a>Numerikus funkciók 

A numerikus funkciók minden egyes adatkészlet-figyelő futtatásakor szerepelnek. A következők jelennek meg a Azure Machine Learning Studióban. A eloszlás valószínűségi sűrűsége látható.

| Metrika | Leírás |  
| ------ | ----------- |  
| Wasserstein távolság | Az alapkonfiguráció terjesztésének a célként megadott eloszlásba való átalakításához szükséges minimális mennyiség. |
| Középérték | A szolgáltatás átlagos értéke. |
| Minimális érték | A szolgáltatás minimális értéke. |
| Maximális érték | A szolgáltatás maximális értéke. |

![Szolgáltatás részleteinek numerikus értéke](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Kategorikus funkciók 

A numerikus funkciók minden egyes adatkészlet-figyelő futtatásakor szerepelnek. A következők jelennek meg a Azure Machine Learning Studióban. A terjesztéshez egy hisztogram jelenik meg.

| Metrika | Leírás |  
| ------ | ----------- |  
| Euklidészi távolság | Az alapkonfiguráció és a célként megadott eloszlások közötti geometriai távolság. |
| Egyedi értékek | A szolgáltatás egyedi értékeinek (kardinális) száma. |


![Szolgáltatás részletei kategorikus](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Metrikák, riasztások és események

A metrikák a Machine learning-munkaterülethez társított [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) -erőforrásban kérhetők le. Amely hozzáférést biztosít a Application Insights összes szolgáltatásához, beleértve az egyéni riasztási szabályok és a műveleti csoportok beállítását is, így például egy e-mail-, SMS-/leküldéses/hang-vagy Azure-függvényt. A részletekért tekintse meg a teljes Application Insights dokumentációját. 

Első lépésként navigáljon a Azure Portal, és válassza ki a munkaterület **Áttekintés** lapját.  A kapcsolódó Application Insights erőforrás a jobb szélen van:

[![Az Azure Portal áttekintése](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Válassza a naplók (elemzés) lehetőséget a bal oldali ablaktábla figyelés területén:

![Az Application ininsight áttekintése](./media/how-to-monitor-datasets/ai-overview.png)

Az adatkészlet-figyelő metrikái a következőképpen `customMetrics`tárolódnak:. Az adatkészlet-figyelő beállítása után írhat és futtathat egy lekérdezést a megtekintéséhez:

[![Log Analytics-lekérdezés](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Miután azonosított mérőszámokat a riasztási szabályok beállításához, hozzon létre egy új riasztási szabályt:

![Új riasztási szabály](./media/how-to-monitor-datasets/alert-rule.png)

Meglévő műveleti csoportot is használhat, vagy létrehozhat egy újat, hogy meghatározza a beállított feltételek teljesülése esetén végrehajtandó műveletet:

![Új műveleti csoport](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Korlátozások és ismert problémák:

* A backfill-feladatok időtartománya a figyelő gyakorisági beállításának 31 intervallumára van korlátozva. 
* A 200 funkcióinak korlátozása, kivéve, ha nincs megadva szolgáltatási lista (az összes funkció használatban van).
* A számítás méretének elég nagynak kell lennie az adatok kezeléséhez. 
* Győződjön meg arról, hogy az adatkészlet egy adott figyelő futtatásának kezdési és befejezési dátumán belüli adatokat tartalmaz.
* Az adatkészlet-figyelők csak a 50 vagy több sort tartalmazó adatkészleteken működnek. 

Az adatkészletben lévő oszlopok vagy szolgáltatások a következő táblázatban szereplő feltételek alapján kategorikusnak vagy numerikusnak minősülnek. Ha a szolgáltatás nem felel meg az alábbi feltételeknek – például egy karakterlánc típusú, >100 egyedi értékekkel rendelkező oszlop – a szolgáltatás el lesz dobva az adateltolódási algoritmusból, de a rendszer még mindig felhasználható. 

| Szolgáltatás típusa | Adattípus | Állapot | Korlátozások | 
| ------------ | --------- | --------- | ----------- |
| Kategorikus | karakterlánc, bool, int, float | A szolgáltatásban található egyedi értékek száma kevesebb, mint 100, és a sorok száma kevesebb, mint 5%. | A Null érték a saját kategóriája. | 
| Numerikus | int, float | A szolgáltatás értékei numerikus adattípussal rendelkeznek, és nem felelnek meg a kategorikus funkció feltételének. | A szolgáltatás el lett dobva, ha az értékek 15%-a null értékű >. | 

## <a name="next-steps"></a>További lépések

* Hozzon létre egy adatkészlet-figyelőt a [Azure Machine learning Studióban](https://ml.azure.com) vagy a [Python-jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) .
* Ismerje meg, hogyan állíthatja be az adateltolódást az [Azure Kubernetes szolgáltatásban üzembe helyezett modelleken](how-to-monitor-data-drift.md).
* Adatkészlet-drift figyelők beállítása az [Event Grid](how-to-use-event-grid.md)használatával. 
