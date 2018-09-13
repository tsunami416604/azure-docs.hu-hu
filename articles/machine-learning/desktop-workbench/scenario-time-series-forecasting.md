---
title: Energiaszükséglet időbeli előrejelzése |} A Microsoft Docs
description: Hogyan alkalmazhatja a machine learning-előrejelzést energiaszükséglet időbeli az Azure Machine Learning Workbench alkalmazásban.
services: machine-learning
documentationcenter: ''
author: anta
manager: ireiter
editor: anta
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: 3f32c9048c4b2002e7672b46a4f39e86ffa7b98e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645214"
---
# <a name="energy-demand-time-series-forecasting"></a>Energiaszükséglet időbeli előrejelzése


Egy műveletsorozatot megfigyelések a jövőbeli értékek előrejelzésére feladatának idősorozat-előrejelzés. Ez gyakori probléma és alkalmazások számos iparágban. Például a kereskedelmi cégek kell jövőbeli termék értékesítés-előrejelzést, így azok hatékony rendezheti az ellátási láncok az igényeknek. Ehhez hasonlóan csomag kézbesítési vállalatok kell megbecsülni azok a szolgáltatások iránti igény, úgy tudják tervezni munkaerő-követelmények és időben kézbesítési útvonalakat. Sok esetben a pénzügyi kockázatok pontos előrejelzéseket jelentős lehet. Előrejelzés ezért gyakran kritikus fontosságú üzleti tevékenységhez.

Ez a példa bemutatja, hogyan idősorozat-előrejelzés segítségével végezheti el alkalmazása a machine learning-módszerekkel. A modellezés minden lépés végigvezeti folyamata többek között:
- Adat-előkészítés, amelyek megtisztítják és formázza az adatokat;
- A machine learning-modellek a nyers idősorozat-adatokat; a szolgáltatások létrehozása
- Különböző gépi tanulási modellek; képzés
- A modellek kiértékelése tárolt kibővített tesztadatkészlet; a teljesítményük összehasonlításával és,
- A legoptimálisabb modellt, igény szerinti előrejelzések létrehozásához egy webes szolgáltatáson keresztül elérhetővé teszi a modellezést.

Az Azure Machine Learning Workbench célszerű a modellezési folyamat minden: 
- A minta bemutatja, hogyan teheti meg a Jupyter notebook-környezet – közvetlenül a Workbench - keresztül elérhető egyszerűbb Python-kód fejlesztéséhez. A modell fejlesztési folyamat bemutatható másoknak sokkal jobban kirajzolódik a markdown-jegyzetek és diagramok használatával. Ezeket a notebookokat tudja megtekinteni, szerkeszthetők, és közvetlenül a Workbench végre.
- Betanított modellek is tárolása és blob storage-ba való feltöltése. Ez segít a adattudós, betanított modell objektumok nyomon követheti, és győződjön meg arról, megőrzött és szükség esetén lekérhető.
- Metrikák engedélyezése a adatszakértőkről tárolja a modell teljesítményét pontszámok Python-szkript végrehajtása közben is naplózva.
- A workbench a naplózott mérőszámok, így könnyen összehasonlítható modell teljesítmény-mérőszámok az adatszakértő testre szabható táblákat hoz létre. Diagramok automatikusan megjelennek, így a legjobban teljesítő modellt könnyen azonosítható.
- Végül a minta bemutatja, hogyan betanított modell is kell üzembe helyezte azt egy valós idejű webszolgáltatás telepítésével.

## <a name="link-to-the-gallery-github-repository"></a>A katalógus GitHub-adattárra mutató hivatkozás
A nyilvános GitHub-tárházat a valós forgatókönyvekben a tartalmazza az összes anyag – Kódminták ebben a példában a szükséges:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Használati eset – áttekintés

Ebben a forgatókönyvben energia kereslet-előrejelzési, ahol a cél az, előre jelezni a jövőbeli terhelés egy energia rácson összpontosít. Ez megegyezik az energia ágazatban cégeknek egy üzleti szempontból kritikus művelet operátorok rácshoz felhasznált energia és a felhasznált energia átadott finom egyensúlyának karbantartása szükséges. Túl sok a rácshoz megadott energiagazdálkodási energia-és műszaki veszteség eredményezhet. Azonban ha túl kevés power megadott blackouts, elhagyása nélkül power ügyfelek vezethet. Rács operátorok általában rövid távú döntéseket hozhat a rácshoz energiaellátás kezeléséhez, és ne a terhelés elosztása is eltarthat. Az operátor ezen döntések magabiztosan ezért elengedhetetlen egy pontos rövid távú előrejelzést energiaszükségletét.

Ebben a forgatókönyvben a machine learning-előrejelzési megoldás energiaszükségletét építése részletesen. A megoldás be van tanítva, nyilvános adatkészleteken a [független rendszer operátor New York-i (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), New York állapot energiagazdálkodási rács működik. Az adatkészlet óránként power igény szerint adatait tartalmazza a New York City öt éven keresztül. Egy további adatkészlet óránként New York City időjárási feltételek az azonos időszakra tartalmazó tette a [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverziók érhető el).
- Egy telepített példánya [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) következő a [rövid telepítési útmutatójában](../service/quickstart-installation.md) telepítse a programot, és hozzon létre egy munkaterületet.
- Ez a példa feltételezi, hogy az Azure ML Workbench futtatja a Windows 10-es [Docker-motor](https://www.docker.com/) helyileg telepítve. Ha macOS használ, a nagymértékben ugyanezek az utasítások érvényesek.
- Az Azure Machine Learning Operacionalizálás telepítve a helyi környezet beállítása és a egy modellkezelési fiókot ez leírtak szerint létrehozott [útmutató](./model-management-configuration.md).
- Ez a minta igényel a Pandas telepítés 0.20.3 verzióra frissíteni vagy újabb és matplotlib telepítéséhez. Kattintson a *parancssor megnyitása* származó a *fájl* menüjében a Workbench, és futtassa a következő parancsokat a függőségek telepítéséhez:

    ```
    conda install "pandas>=0.21.1"
    ```
    
## <a name="create-a-new-workbench-project"></a>Egy új Workbench-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning Workbench alkalmazásban
2.  Az a **projektek** lap, kattintson a **+** aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** panelen adja meg az információkat az új projekt
4.  Az a **projektsablonok keresése** keresőmezőbe, írja be az "Energiaellátás igény szerint Idősorozat-előrejelzés", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra


## <a name="data-description"></a>Adatok leírása

Két adatkészletet biztosítunk, ezt a mintát, és segítségével letölteni a `1-data-preparation.ipynb` notebook: `nyc_demand.csv` és `nyc_weather.csv`.

**nyc_demand.csv** óránként New York City energia igény szerint értékeit tartalmazza az évben 2012 – 2017. Az adatok egyszerű struktúrája a következő:

| Időbélyeg | igény szerint |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

Igény szerinti értékei megawatt óra (MWh). Alább energiaszükségletét 2017. július 7 napos időszakon át egy táblázatot a következő:

![Energiaszükséglet](./media/scenario-time-series-forecasting/energy_demand.png  "energiaszükségletét")

**nyc_weather.csv** az évek 2012 – 2017 óránként New York City időjárási értékeit tartalmazza:

| Időbélyeg | precip | TEMP
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* a csapadék előfordulásának szint százalékos mértéke. *temp* (hőmérsékletértékeket) rendelkezik lett átméretezése, hogy minden érték tartozik a [0, 100] intervallumban.

## <a name="scenario-structure"></a>A forgatókönyv-struktúra

Ez a projekt először az Azure Machine Learning Workbenchben való megnyitásakor lépjen a *fájlok* a bal oldali ablaktáblán. A következő kód fájlok találhatók ebben a példában:
- `1-data-preparation.ipynb` – Ez a Jupyter notebook tölti le, és feldolgozza az adatokat, hogy felkészítse az modellezési. Ez az első jegyzetfüzet fogja futtatni.
- `2-linear-regression.ipynb` – Ez a jegyzetfüzet betanítja egy lineáris regressziós modell a betanítási adatok.
- `3-ridge.ipynb` -betanítja ridge regressziós modell.
- `4-ridge-poly2.ipynb` -betanítja egy ridge regressziós modellt a 2. szintű polinom funkcióját.
- `5-mlp.ipynb` – egy többrétegű perceptron Neurális hálózat betanítja.
- `6-dtree.ipynb` -betanítja a döntési fa modell.
- `7-gbm.ipynb` -betanítja egy színátmenetes gyorsított gépi modellt.
- `8-evaluate-model.py` – Ez a szkript betölti a betanított modell, és használja a birtokolt kibővített tesztadatkészlet előrejelzéseket. Így a különböző modellek teljesítményének összehasonlíthatók modell értékelési mérőszámok küld.
- `9-forecast-output-exploration.ipynb` – Ez a jegyzetfüzet a gépi tanulási modellek által generált előrejelzések vizualizációkat hoz létre.
- `10-deploy-model.ipynb` – Ez a jegyzetfüzet bemutatja, hogyan betanított előrejelzési modell is kell üzembe helyezte azt egy valós idejű webszolgáltatás.
- `evaluate-all-models.py` – Ez a szkript kiértékeli az összes betanított modellek. Futó alternatívát nyújt `8-evaluate-model.py` minden betanított modell külön-külön.

### <a name="1-data-preparation-and-cleaning"></a>1. Adat-előkészítési és a tisztítás

A minta futtatása elindításához először kattintson az `1-data-preparation.ipynb` jelenleg előzetes módban a notebook megnyitásához. Kattintson a ***Start Notebook Server*** egy Jupyter notebook server és a Python-kernel elindításához a gépen. Ezt futtathatja a Workbench a jegyzetfüzet, vagy használhatja a böngészőjében az [ http://localhost:8888 ](http://localhost:8888). Vegye figyelembe, hogy módosítania kell a kernel *projektnév helyi*. Ehhez a a *Kernel* menü alatt a Jegyzetfüzet *módosítása kernel*. Nyomja meg ***shift + Enter billentyűkombinációt*** egyes notebook cellák futtassa a kódot, vagy kattintson *összes futtatása* a a *cella* menü a teljes jegyzetfüzet futtatásához.

A notebook először tölt az adatok egy Azure-ban üzemeltetett blob storage-tárolóba. Az adatok ott található a gépen a `AZUREML_NATIVE_SHARE_DIRECTORY`. Ezen a helyen érhető el minden olyan jegyzetfüzet vagy parancsfájlokat futtat, a Workbench így nagyszerű hely az adatok és a betanított modellek.

Az adatok letöltését követően a notebookot az adatok kitöltésével idősorozatban található hézagok, és a hiányzó értékeket beküldésekor interpolációs törli. Ezzel a módszerrel az idősorozat-adatok törlése a lehető legnagyobbra növeli a modellek betanítása rendelkezésre álló adatok mennyisége.

Több modell funkciót a megtisztított nyers adatokból jönnek létre. Ezek a funkciók két csoportra kategóriába sorolhatók:

- **Szolgáltatások driven idő** származnak az *időbélyeg* változó például *hónap*, *dayofweek* és *óra*.
- **Szolgáltatások kipufogócsöveket** időértékek áttért a tényleges igény szerint vagy hőmérsékleti értékek vannak. Ezek a funkciók célja, hogy a modellben egymást követő időszakok között a feltételes függőségek rögzítése.

Az eredményül kapott a szolgáltatás-adatkészlet előrejelzési modellek fejlesztése során ezután felhasználhatók.

### <a name="2-model-development"></a>2. Modell fejlesztése

A modellezési először megközelítést lehet egy egyszerű lineáris regressziós modellt. A `2-linear-regression.ipynb` notebook mutatja be egy lineáris regressziós jövőbeli energiaszükségletét előrejelzési modell betanításához. Különösen a modell fog tartani a előrejelzésére energiaszükségletét egy óra (*t + 1*) az aktuális időszak előtt (*t*). Azonban azt alkalmazhatja a "egylépéses" modell rekurzív módon létrehozására az előrejelzések későbbi időszakokra, teszt időpontban *t + n*.

A modell betanításához, prediktív folyamatok létrehozása magában foglalja a három különálló lépésre:

- **Egy adatátalakítást**: a bemeneti adatokat a szükséges formátumokat eltérőek lehetnek attól függően, a gépi tanulási algoritmus. Ebben az esetben a lineáris regressziós modell szükséges kódolni egy gyakori kategorikus változók.
- **A platformfüggetlen érvényesítési rutin**: gyakran egy gépi tanulási modellt, amely a Kísérletezési keresztül kell hangolni kell egy vagy több hiperparaméterek rendelkeznek. Keresse meg a paraméterértékeket optimális készletét keresztellenőrzési használható. A modell ismételten betanított és az adatkészlet különböző modellrész értékelve. A legjobb paraméterek megegyeznek, amely a legjobb modellt teljesítményt a keresztellenőrzési modellrész vetített érhető el. Ez a folyamat kifejtett részletes `2-linear-regression.ipynb`.
- **A kész modell betanítása**: A modell tanítása a teljes adatkészleten, ajánlott hiperparaméterek használatával.

6 különböző modell sorozatát szerepel a notebookok számozott 2 – 7. Minden egyes notebook betanítja egy másik modellhez, és menti a `AZUREML_NATIVE_SHARE_DIRECTORY` helyét. Ebben a forgatókönyvben fejlesztette ki minden modell van tanítva, miután azt értékeli, és hasonlítsa össze azokat a következő szakaszban leírt.

### <a name="3-model-evaluation-and-comparison"></a>3. Modell értékelése és összehasonlítása

Betanított modell használatával győződjön meg arról, előrejelzéseket, a későbbi időszakokra. Érdemes ezen a birtokolt kibővített tesztadatkészlet modelleket szeretne értékelni. A különböző modellek látott ugyanabból az adatkészletből a kiértékelésével nagyrészt a fejlesztő lehet azok teljesítményét és azonosíthatja a legoptimálisabb modellt. Ebben a forgatókönyvben a betanított modell rekurzív módon jelezheti előre a jövőre több idő lépést alkalmazunk. Különösen azt hozzon létre előrejelzések akár hat óráig, *t + 6* előre a jelenlegi órán belül *t*. Ezek az előrejelzések ellen a megfigyelt: minden időszakban tényleges igény szerint értékeli ki.

A modell értékelése, nyissa meg a `8-evaluate-model.py` a parancsfájl a *fájlok* a munkaterület ablaktáblán. Ellenőrizze, hogy *konfiguráció futtatása* értékre van állítva **helyi** és a modell nevet írja be a *argumentumok* mező. A modell neve pontosan egyeznie kell a *modell_neve* változót állítsa a jegyzetfüzet, amelyben a modell tanítása elején. Például adja meg a "linear_regression" betanított regressziós modell lineáris kiértékeléséhez. Azt is megteheti, miután minden modell rendelkezik betanítva, kiértékelheti őket egy paranccsal futtatásával `evaluate-all-models.py`. Ez a szkript futtatásához nyisson meg egy parancssort a Workbench alkalmazásból, és hajtsa végre a következő parancsot:

```
python evaluate-all-models.py
```
A `8-evaluate-model.py` szkript a következő műveleteket hajtja végre:

- Betanított modell folyamat betölti a lemezről
- A tesztelési adathalmazon előrejelzéseket tesz
- Teljesítmény-mérőszámok modell kiszámítja, és bejelentkezik a
- A teszt menti az adatkészlet előrejelzéseket `AZUREML_NATIVE_SHARE_DIRECTORY` újabb vizsgálata és elemzése
- A betanított modell folyamat menti a *kimenete* mappát.

> [!Note]
> Modell mentése a *kimenete* mappát a modellobjektum automatikusan átmásolja a Kísérletezési fiókhoz társított Azure Blob Storage-fiókba. Ez azt jelenti, hogy mindig lesz sikerült beolvasni a blobból a mentett modellobjektumot, még akkor is, ha módosítja a gépek vagy számítási környezetet.

Keresse meg a *futtatási előzmények* ablaktáblán, majd kattintson a `8-evaluate-model.py`. Több modell teljesítménymetrikák megjelenítése diagramok jelenik meg:

- **ME**: jelenti azt, hogy az előzetes hiba. Ez az átlagos eltérés az előrejelzési értelmezhető.
- **Legnagyobb megengedett**: [jelenti azt, hogy százalékos hiba](https://en.wikipedia.org/wiki/Mean_percentage_error) (ME a tényleges igény szerint százalékában kifejezve)
- **MSE**: [mean squared hiba](https://en.wikipedia.org/wiki/Mean_squared_error)
- **Gyökátlagos**: root mean-készlet négyzet (négyzetgyökét MSE) hiba
- **MAPE**: [jelenti azt, hogy abszolút százalékos hiba](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [szimmetrikus átlagos abszolút százalékos hiba](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: egy alaptervhez MAPE előrejelzés, amelyben az előrejelzés az utolsó ismert igény szerinti érték egyenlő.
- **MdRAE**: középérték relatív abszolút hiba. A középérték arány az előrejelzési hiba az alaptervhez előrejelzési hiba. Egy érték kisebb, mint 1 azt jelenti, hogy az előrejelzés jobban, mint az eredeti működik-e.

Az összes fenti metrikák tekintse meg a *t + 1* előrejelzési. A fenti metrikák mellett is megtekintheti a megfelelő metrikák készletét a *_horizon* utótag. Ez az időszak előrejelzési tartományban pontokból átlagolva metrika *t + 1* időszakot *t + 6*.

Ha a metrikák nem jelennek meg a diagramterületen, kattintson a fogaskerék ikonra a jobb felső sarokban. Győződjön meg arról, hogy a rendszer ellenőrzi az Önt érdeklő modell teljesítmény-mérőszámon. A metrikák is megjelenik a diagramok egy táblázatban. Újra, ez a tábla testre szabható a fogaskerék ikonra kattintva lehet. A táblázat rendezéséhez a teljesítmény-mérőszám azonosíthatja a legoptimálisabb modellt. Ha érdekli jelent meg, hogyan változtak az előrejelzési teljesítményének időszakba eső *t + 1* való *t + 6*, kattintson a modell a tábla bejegyzésére. A diagramok megjelenítése a MAPE, legnagyobb megengedett és MdRAE metrikák között az előzetes időszak alatt jelennek *Vizualizációk*.

Előrejelzési modellek kiértékelésekor jeleníthetik meg a kimeneti előrejelzéseket nagyon hasznos lehet. Ez segít meghatározni, hogy valósághű megjelenik-e az előállított előrejelzés az adatszakértő. Azonosíthatja a problémákat az előrejelzés, ha például az előrejelzést végez rosszul bizonyos időszakokban is segít. A `9-forecast-output-exploration.ipynb` notebook állítja elő a tesztelési adatkészletnél generált előrejelzések vizualizációkat.

### <a name="4-deployment"></a>4. Környezet

A legjobb modellt is kell üzembe helyezte azt egy valós idejű webszolgáltatásként telepítésével. A webszolgáltatás majd lehet meghívni, igény szerinti előrejelzések készítése, az új adatok válnak elérhetővé. Ebben a forgatókönyvben egy új előrejelzést kell hoz létre minden órában, előre jelezni az ezt követő órában energiaszükségletét. Ez a feladat végrehajtásához egy webszolgáltatás lehet telepítve, amely egy diagnosztikakonfigurációs tömböt foglal funkciók egy adott órában időszakban bemenetként, és ad vissza kimenetként a előre jelzett keresletet.

Ebben a példában a webszolgáltatás telepítve van a Windows 10 rendszerű gépet. Győződjön meg arról, befejezte az előfeltételként felsorolt lépéseket állítson be a helyi telepítés [– első lépések útmutató](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) az Operacionalizáláshoz CLI-hez. Ha úgy állította be a helyi környezet és a modellkezelési fiókot, futtassa a `10-deploy-model.ipynb` notebook számára, hogy a webszolgáltatás üzembe helyezéséhez.

## <a name="conclusion"></a>Összegzés

Ez a minta bemutatja, hogyan hozhat létre egy teljes körű idősor-előrejelzési megoldás energiaszükségletét előrejelzése céljából. Az ismertetett alapelvek bemutattuk az ebben a példában számos más előrejelzési esetekben és iparágakban is kiterjeszthető.

Ebből a forgatókönyvből megtudhatja, hogyan Azure Machine Learning Workbench végrehajtásában adatszakértő hasznos funkciót a Jupyter notebook-környezet és a metrika naplózási képességeket biztosító valós megoldások fejlesztésébe. A minta is végigvezeti az olvasót egy modellt is üzembe helyezte azt és telepített Azure Machine Learning Operacionalizálás parancssori felület használatával. Üzembe helyezését követően a webszolgáltatási API lehetővé teszi a fejlesztők vagy adatmérnökök való integrálására az előrejelzési modell egy szélesebb körű adatfolyamat.
