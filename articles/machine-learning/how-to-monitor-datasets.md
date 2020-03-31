---
title: Adateltolódás elemzése és figyelése adatkészleteken (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Hozzon létre Azure Machine Learning-adatkészleteket figyelők (előzetes verzió), figyelheti az adateltolódást az adatkészletekben, és riasztásokat állíthat be.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 401019c537cb0eb51fa6002637e170a79210f7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617636"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Adateltolódás (előzetes verzió) észlelése adatkészleteken
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan hozhat létre Azure Machine Learning-adatkészlet-figyelők (előzetes verzió), figyelheti az adatok eltolódását és az adatkészletek statisztikai változásait, és riasztásokat állíthat be.

Az Azure Machine Learning adatkészlet-figyelőivel a következőket teheti:
* **Elemezze az adatok eltolódását,** hogy megértse, hogyan változik az idő múlásával.
* **A modelladatok figyelése** a betanítási és a kiszolgáló adatkészletek közötti különbségek.
* **Az új adatok figyelése** az alapterv és a céladatkészlet közötti különbségekre.
* **Az adatok ban lévő jellemzők profilfunkciói** nyomon követhetik, hogyan változnak a statisztikai tulajdonságok az idő múlásával.
* **Állítsa be az adateltolódásra vonatkozó riasztásokat** a korai figyelmeztetésekhez a lehetséges problémákra. 

Metrikák és elemzések érhetők el az [Azure Application Insights-erőforrás](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) az Azure Machine Learning-munkaterülethez társított.

> [!Important]
> Kérjük, vegye figyelembe, hogy az SDK-val történő adateltolódás figyelése minden kiadásban elérhető, míg az adatok átsodródása a stúdión keresztül a weben csak Enterprise edition.

## <a name="prerequisites"></a>Előfeltételek

Adatkészlet-figyelők létrehozásához és az azokhoz való munkához a következőkre van szükség:
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt elkezdené. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)
* Egy [Azure Machine Learning-munkaterület.](how-to-manage-workspace.md)
* Az [Azure Machine Learning SDK python telepítve,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)amely tartalmazza az azureml-adatkészletek csomag.
* Strukturált (táblázatos) adatok a fájl elérési útján, fájlnevében vagy oszlopában megadott időbélyegzővel.

## <a name="what-is-data-drift"></a>Mi az adateltolódás?

A gépi tanulás környezetében az adateltolódás a modell bemeneti adatainak változása, amely a modell teljesítményének csökkenéséhez vezet. Ez az egyik legfontosabb oka annak, hogy a modell pontossága idővel romlik, így az adatok eltolódásának figyelése segít a modell teljesítményproblémáinak észlelésében.

Az adateltolódás okai a következők: 

- Az upstream folyamat megváltozik, például egy olyan érzékelő cseréje, amely a mértékegységeket hüvelykről centiméterre változtatja. 
- Az adatminőséggel kapcsolatos problémák, például egy törött érzékelő mindig 0-t olvas.
- Természetes sodródás az adatokban, például az átlagos hőmérséklet-változás az évszakokkal.
- A jellemzők közötti váltás vagy a kovariáns eltolódása. 

Az Azure Machine Learning-adatkészlet-figyelők segítségével riasztásokat állíthat be, amelyek segítik az adateltolódás észlelését az adatkészletekben az idő múlásával. 

### <a name="dataset-monitors"></a>Adatkészlet-figyelők 

Létrehozhat egy adatkészlet-figyelőt, amely észleli és figyelmezteti az adateltolódást az adatkészlet új adataira, elemzi az előzményadatokat az eltolódáshoz, és az új adatokat idővel profilozza. Az adateltolódási algoritmus az adatok változásának általános mérésére szolgál, és jelzi, hogy mely jellemzők felelősek a további vizsgálatokért. Az adatkészlet-figyelők számos más metrikát hoznak `timeseries` létre az adatkészlet új adatainak profilalkotásával. Egyéni riasztási lehet beállítani az összes metrikák által generált a figyelő az [Azure Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) Az adatkészlet-figyelők segítségével gyorsan felfigyelheti az adatproblémákat, és a valószínű okok azonosításával csökkentheti a probléma hibakereséséhez használt időt.  

Fogalmilag három elsődleges forgatókönyv ek adatkészlet-figyelők az Azure Machine Learning beállítása.

Forgatókönyv | Leírás
---|---
A modell kiszolgálóadatainak figyelése a modell betanítási adataitól való eltolódáshoz | Az ebből a forgatókönyvből származó eredmények úgy értelmezhetők, mint a modell pontosságának proxyja figyelése, mivel a modell pontossága romlik, ha a kiszolgáló adatok eltérnek a betanítási adatoktól.
Idősorozat-adatkészlet figyelése az előző időszakból való eltolódáshoz. | Ez a forgatókönyv általánosabb, és a modellépület előtt vagy után szereplő adatkészletek figyelésére használható.  A céladatkészletnek időbélyeg-oszloppal kell rendelkeznie, míg az alaptervi adatkészlet bármilyen táblázatos adatkészlet lehet, amely a céladatkészlettel közös funkciókkal rendelkezik.
Korábbi adatok elemzésének végrehajtása. | Ez a forgatókönyv az előzményadatok megértéséhez és az adatkészlet-figyelők beállításaiban hozott döntések tájékoztatásához használható.

## <a name="how-dataset-can-monitor-data"></a>Hogyan figyelheti az adatkészlet az adatokat?

Az Azure Machine Learning használatával az adateltolódás tetszése adatkészleteken keresztül történik. Az adatok eltolódásának figyeléséhez egy alapszintű adatkészlet – általában egy modell betanítási adatkészlete – van megadva. A céladatkészletet – általában a modellbemeneti adatokat – az idő múlásával összehasonlítja az alapkonfigurációs adatkészlettel. Ez az összehasonlítás azt jelenti, hogy a cél adatkészlet nek rendelkeznie kell egy időbélyeg oszlop megadva.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>A `timeseries` tulajdonság beállítása a céladatkészletben

A céladatkészletnek be `timeseries` kell állítania a tulajdonságot az időbélyeg oszlopból vagy az adatok egyik oszlopából, vagy a fájlok elérési útmintájából származó virtuális oszlopból. Ez a Python SDK vagy az Azure Machine Learning stúdió keresztül végezhető el. Meg kell adni egy "finomszemcsés" időbélyeget jelző oszlopot, hogy tulajdonságot adjon `timeseries` az adatkészlethez. Ha az adatok mappastruktúrára vannak felosztva az időadatokkal, például "{yyyy/MM/dd}", virtuális oszlopot hozhat létre az elérési út mintabeállításán keresztül, és beállíthatja azt "durva szemcse" időbélyegként az idősorozat-funkciók fontosságának javítása érdekében. 

#### <a name="python-sdk"></a>Python SDK

Az [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) osztály [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) metódusa határozza meg az adatkészlet időbélyegző oszlopát. 

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

Az adatkészletek tulajdonságának `timeseries` teljes példáját a [példajegyzetfüzet](https://aka.ms/azureml-tsd-notebook) ben vagy az [SDK-dokumentációban](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)találja.

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Ha az azure Machine Learning studio használatával hozza létre az adatkészletet, győződjön meg arról, hogy az adatok elérési útja időbélyeg-adatokat tartalmaz, az összes adattal rendelkező almappát tartalmazza, és állítsa be a partícióformátumot. 

A következő példában a *NoaaIsdFlorida/2019* almappában lévő összes adat elkészül, és a partícióformátum az időbélyeg évét, hónapját és napját adja meg. 

[![Partíció formátuma](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

A **Sémabeállításaiban** adja meg a megadott adatkészlet virtuális vagy valós oszlopának időbélyegoszlopát:

![Időbélyeg](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Adatkészlet-figyelő beállításai

Miután létrehozta az adatkészletet a megadott időbélyeg-beállításokkal, készen áll az adatkészlet figyelőjének konfigurálására.

A különböző adatkészlet-monitorbeállítások három csoportra oszlanak: **Alapszintű információk, Monitor-beállítások** és **Háttérkitöltés-beállítások**.

### <a name="basic-info"></a>Alapvető információk

Ez a tábla az adatkészlet-figyelő höz használt alapvető beállításokat tartalmazza.

| Beállítás | Leírás | Tippek | Változtatható | 
| ------- | ----------- | ---- | ------- | 
| Név | Az adatkészlet-figyelő neve. | | Nem |
| Alaptervi adatkészlet | Táblázatos adatkészlet, amely a céladatkészlet idővel történő összehasonlításához alapként fog használni. | Az alapterv-adatkészletnek a céladatkészlettel közös jellemzőkkel kell rendelkeznie. Általában az alaptervet a modell betanítási adatkészletére vagy a céladatkészlet egy szeletére kell beállítani. | Nem |
| Céladatkészlet | Táblázatos adatkészlet időbélyeg oszlopgal megadva, amely az adatok eltolódása lesz elemezve. | A céladatkészletnek az alaptervadatkészlettel közös jellemzőkkel kell `timeseries` rendelkeznie, és olyan adatkészletnek kell lennie, amelyhez új adatokat fűz hozzá a rendszer. A céladatkészlet előzményadatai elemezhetők, vagy új adatok figyelhetők meg. | Nem | 
| Frequency | A folyamatfeladat ütemezéséhez és a korábbi adatok elemzéséhez használt gyakoriság, ha háttérkitöltést futtat. A lehetőségek közé tartozik a napi, heti vagy havi. | Módosítsa ezt a beállítást úgy, hogy az adatok nak az alapvonalhoz hasonló méretű ek. | Nem | 
| Szolgáltatások | Az adatok idővel való eltolódása szempontjából elemzett szolgáltatások listája. | Állítsa be a modell kimeneti jellemző(k) mérésére koncepció eltolódását. Nem tartalmazzák azolyan funkciókat, amelyek természetesen idővel elsodródnak (hónap, év, index stb.). A funkciók listájának módosítása után kitöltheti és a meglévő adateltolódás-figyelőt is kitöltheti. | Igen | 
| Számítási cél | Az Azure Machine Learning számítási cél az adatkészlet-figyelő feladatok futtatásához. | | Igen | 

### <a name="monitor-settings"></a>Monitor beállításai

Ezek a beállítások az ütemezett adatkészlet-figyelő folyamathoz tartoznak, amely létrehozva lesz. 

| Beállítás | Leírás | Tippek | Változtatható | 
| ------- | ----------- | ---- | ------- |
| Bekapcsolás | Az ütemezés engedélyezése vagy letiltása az adatkészletfigyelő-folyamaton | Tiltsa le az ütemtervet a korábbi adatok elemzéséhez a háttérkitöltés beállítással. Az adatkészlet-figyelő létrehozása után engedélyezhető. | Igen | 
| Késés | Az adatok órákban megadott ideje alatt az adatkészletbe való megérkezéshez szükséges idő. Ha például három napig tart, amíg az adatok megérkeznek az SQL DB-be, az adatkészlet beágyazódik, állítsa a késést 72-re. | Az adatkészlet-figyelő létrehozása után nem módosítható | Nem | 
| E-mail cím | E-mail címek az adateltolódási százalékküszöbértéknek megsértése alapján riasztáshoz. | Az e-maileket az Azure Monitoron keresztül küldik. | Igen | 
| Küszöbérték | Az e-mailek riasztásának adateltolódási százalékos küszöbértéke. | További riasztások és események is beállítható számos más metrikák a munkaterület társított Application Insights erőforrás. | Igen | 

### <a name="backfill-settings"></a>Háttérkitöltési beállítások

Ezek a beállítások az adatátlatási mérőszámok korábbi adatainak háttérkitöltésére szolgálnak.

| Beállítás | Leírás | Tippek |
| ------- | ----------- | ---- |
| Kezdő dátum | A háttérkitöltési feladat kezdő dátuma. | | 
| Záródátum | A háttérkitöltési feladat záró dátuma. | A záró dátum nem lehet több, mint 31*gyakorisági időegység a kezdő dátumtól. Egy meglévő adatkészlet-figyelő, metrikák lehet visszatölteni a korábbi adatok elemzéséhez, vagy cserélje le a metrikák at frissített beállításokkal. |

## <a name="create-dataset-monitors"></a>Adatkészlet-figyelők létrehozása 

Adatkészlet-figyelők létrehozása az azure Machine Learning-stúdióval vagy a Python SDK-val egy új adatkészleten az adateltolódás észleléséhez és riasztásához. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Ha riasztásokat szeretne beállítani az adatkészlet figyelőjében, a figyelőt tartalmazó munkaterületnek Enterprise Edition képességekkel kell rendelkeznie. 

A munkaterület iménti funkció megerősítést követően keresse meg a stúdió kezdőlapját, és válassza az Adatkészletek lapot a bal oldalon. Válassza az Adatkészlet-figyelők lehetőséget.

![Figyelőlista](./media/how-to-monitor-datasets/monitor-list.png)

Kattintson a **+Monitor létrehozása** gombra, és folytassa a varázslón keresztül a **Tovább**gombra kattintva.

![Varázsló](./media/how-to-monitor-datasets/wizard.png)

Az eredményül kapott adatkészlet-figyelő megjelenik a listában. Jelölje be, ha meg szeretné jelenik a figyelő részletes oldalára.

### <a name="from-python-sdk"></a>Python SDK-ból

Tekintse meg a [Python SDK referencia dokumentációaz adatok eltolódása](/python/api/azureml-datadrift/azureml.datadrift) a részletekért. 

A következő példa bemutatja, hogyan hozhat létre egy adatkészlet-figyelőt a Python SDK használatával

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

Az `timeseries` adatkészlet és az adateltolódás-detektor beállításának teljes példáját a [példajegyzetfüzetben láthatja.](https://aka.ms/datadrift-notebook)

## <a name="understanding-data-drift-results"></a>Az adateltolódás eredményeinek ismertetése

Az adatfigyelő két eredménycsoportot hoz létre: Drift áttekintése és A szolgáltatás részletei. A következő animáció a kiválasztott funkció és mérőszám alapján jeleníti meg a rendelkezésre álló drift monitordiagramokat. 

![Bemutató videó](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Drift – áttekintés

A **Drift áttekintése** szakasz felső szintű betekintést nyújt az adateltolódás nagysága, és mely funkciókat kell tovább vizsgálni. 

| Metrika | Leírás | Tippek | 
| ------ | ----------- | ---- | 
| Adateltolódás nagysága | Az alapterv és a céladatkészlet közötti százalékos érték. 0-tól 100-ig, ahol 0 azonos adatkészleteket jelöl, és 100 azt jelzi, hogy az Azure Machine Learning-adatátlatási képesség teljesen megkülönbözteti a két adatkészletet. | A mért pontos százalékos zaj várható, mivel gépi tanulási technikákat alkalmaznak ennek a nagyságzataságnak a létrehozásához. | 
| Drift hozzájárulás funkció szerint | A céladatkészlet egyes jellemzőinek hozzájárulása a mért drift nagyságához. |  A kovariánseltolódás miatt a jellemző mögöttes eloszlását nem feltétlenül kell módosítani ahhoz, hogy viszonylag nagy funkciós jelentőséggel bírjon. | 

Az alábbi képen látható diagramok látható a **Drift áttekintést** eredmények az Azure Machine Learning stúdióban, eredő backfill a [NOAA integrált felületi adatok.](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) Az adatokból `stationName contains 'FLORIDA'`mintát vettek, 2019 januárját használták kiindulási adatkészletként, és minden 2019-es adatot használtak célként.
 
![Drift – áttekintés](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>A szolgáltatás részletei

A **Funkció részletei** szakasz a kiválasztott funkció eloszlásának változására, valamint az idő múlásával más statisztikákra vonatkozó jellemzőszintű elemzéseket tartalmaz. 

A céladatkészlet is profilozott idővel. Az egyes funkciók kiindulási eloszlása közötti statisztikai távolságot összehasonlítja a céladatkészlet időbeli értékével, amely fogalmilag hasonló az adateltolódás nagyságához, azzal a kivétellel, hogy ez a statisztikai távolság egy adott jellemzőhöz kapcsolódik. Min, max, és az átlagos is rendelkezésre állnak. 

Az Azure Machine Learning-stúdióban, ha rákattint egy adatpontra a grafikonon a megjelenített funkció eloszlása ennek megfelelően módosul. Alapértelmezés szerint az alapkonfigurációs adatkészlet eloszlását és ugyanannak a szolgáltatásnak a legutóbbi futtatási eloszlását jeleníti meg. 

Ezek a metrikák is lehívhatók a `get_metrics()` Python `DataDriftDetector` SDK egy objektum metóduson keresztül. 

#### <a name="numeric-features"></a>Numerikus jellemzők 

Numerikus funkciók profilozott minden adatkészlet monitor fut. Az alábbiak az Azure Machine Learning stúdióban találhatók. A valószínűségi sűrűség az eloszláshoz látható.

| Metrika | Leírás |  
| ------ | ----------- |  
| Wasserstein távolság | Minimális munkamennyiség az alaptervi eloszlás céleloszlássá alakításához. |
| Átlagérték | A szolgáltatás átlagos értéke. |
| Minimális érték | A szolgáltatás minimális értéke. |
| Maximális érték | A szolgáltatás maximális értéke. |

![A szolgáltatás részletei numerikusak](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Kategorikus funkciók 

Numerikus funkciók profilozott minden adatkészlet monitor fut. Az alábbiak az Azure Machine Learning stúdióban találhatók. Egy hisztogram jelenik meg az eloszláshoz.

| Metrika | Leírás |  
| ------ | ----------- |  
| Euklidési távolság | Geometriai távolság az alapvonal és a céleloszlás között. |
| Egyedi értékek | A szolgáltatás egyedi értékeinek száma (számossága). |


![A szolgáltatás részletei kategorikusak](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Mérőszámok, riasztások és események

Metrikák lekérdezhető kérhető az [Azure Application Insights-erőforrás](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) a gépi tanulási munkaterülethez társított. Amely hozzáférést biztosít az Application Insights összes funkciójához, beleértve az egyéni riasztási szabályok és műveletcsoportok beállítását egy olyan művelet elindításához, mint például egy e-mail/SMS/Push/Voice vagy az Azure-függvény. Kérjük, olvassa el a teljes Application Insights dokumentációt a részletekért. 

Első lépésekhez keresse meg az Azure Portalt, és válassza ki a munkaterület **áttekintése** lapot.  A társított Application Insights-erőforrás a jobb szélen található:

[![Az Azure Portal áttekintése](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

A bal oldali ablaktábla Figyelés parancsában válassza a Naplók (Analytics) lehetőséget:

![Alkalmazáselemzések – áttekintés](./media/how-to-monitor-datasets/ai-overview.png)

Az adatkészlet-figyelő metrikái `customMetrics`a rendszer tárolódnak. Az adatkészlet-figyelő beállítása után lekérdezést írhat és futtathat a következők megtekintéséhez:

[![Naplóelemzési lekérdezés](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Miután azonosította a riasztási szabályok beállításához szolgáló mutatókat, hozzon létre egy új riasztási szabályt:

![Új riasztási szabály](./media/how-to-monitor-datasets/alert-rule.png)

Használhatja a meglévő műveletcsoportot, vagy létrehozhat egy újat a beállított feltételek teljesülése esetén végrehajtandó művelet meghatározásához:

![Új műveletcsoport](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Korlátozások és ismert problémák:

* A háttérkitöltési feladatok időtartománya a monitor frekvenciabeállításának 31 intervallumára korlátozódik. 
* 200 szolgáltatás korlátozása, kivéve, ha nincs megadva szolgáltatáslista (az összes használt szolgáltatás).
* A számítási méretnek elég nagynak kell lennie az adatok kezeléséhez. 
* Győződjön meg arról, hogy az adatkészlet adatokat a kezdő és záró dátumon belül egy adott monitor futtatása.
* Az adatkészlet-figyelők csak 50 vagy több sort tartalmazó adatkészleteken működnek. 

Az adatkészlet oszlopai vagy jellemzői a következő táblázatban szereplő feltételek alapján kategorikusnak vagy numerikusnak minősülnek. Ha a szolgáltatás nem felel meg ezeknek a feltételeknek - például egy karakterlánc-oszlop, amely >100 egyedi értéket tartalmaz -, a funkció elesik az adateltolódási algoritmusunkból, de továbbra is profilozott. 

| Jellemző típusa | Adattípus | Állapot | Korlátozások | 
| ------------ | --------- | --------- | ----------- |
| Kategorikus | string, bool, int, float | A szolgáltatás egyedi értékeinek száma kevesebb, mint 100, és kevesebb, mint a sorok számának 5%-a. | A null a saját kategóriájának minősül. | 
| Numerikus | int, lebegés | A jellemző értékei numerikus adattípusúak, és nem felelnek meg a kategorikus jellemző kondíciójának. | A szolgáltatás ellett, ha >értékek 15%-a null értékű. | 

## <a name="next-steps"></a>További lépések

* Az [Azure Machine Learning-stúdióban](https://ml.azure.com) vagy a [Python-jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) beállíthat egy adatkészlet-figyelőt.
* Megtudhatja, hogyan állíthatja be az adatok eltolódását az [Azure Kubernetes szolgáltatásban telepített modelleken.](how-to-monitor-data-drift.md)
* Állítsa be az adatkészlet-eltolódásfigyelőit [eseményrácsgal.](how-to-use-event-grid.md) 
