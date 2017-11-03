---
title: "Energiát igény szerint a Time Series előrejelzési |} Microsoft Docs"
description: "Gépi tanulási az Azure Machine Learning munkaterület energia igény szerint a time series előrejelzési alkalmazási módjának."
services: machine-learning
documentationcenter: 
author: anta
manager: ireiter
editor: anta
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: bd0ddfcffdb6f946f9a3786f3d0add1740be861b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="energy-demand-time-series-forecasting"></a>Energiát igény szerint a Time Series előrejelzési


Idő series előrejelzési feladata előrejelzésére megfigyelések idő rendezett sorozata jövőbeni értékeket. Gyakori probléma, és sok iparágakban rendelkezik alkalmazással. Például a kereskedelmi cégek kell előrejelzési jövőbeli értékesítés, így azok hatékonyan rendezheti az ellátási láncok igényeknek. Hasonlóképpen csomag kézbesítését vállalatok kell becsléséhez a szolgáltatásuk iránti igény, akkor megtervezheti munkaerő követelmények és időben kézbesítési útvonalakat. Sok esetben a pénzügyi kockázatok pontos előrejelzéseket jelentős lehet. Az előrejelzés ezért gyakran kritikus üzleti tevékenységhez.

Ez a példa bemutatja, hogyan idő series előrejelzési segítségével végezheti el machine learning technikák alkalmazása. Megismerheti a modellezési minden lépése folyamat többek között:
- Adatok előkészítése tisztítani és az adat;
- A gépi tanulási modellek nyers idő adatsorozat adatokból; funkciói létrehozása
- Különböző machine learning modellek betanítása
- A modellek tárolt kibővített tesztelési adatkészletre; a teljesítményük összehasonlításával kiértékelése Emellett a
- A legjobb modell, és az elérhetővé válik egy webszolgáltatás-bővítmény, az igény szerinti előrejelzések létrehozásához keresztül végrehajtott.

Az Azure Machine Learning-munkaterület minden lépésnél a modellezési folyamatban támogatások: 
- A példa bemutatja, hogyan teheti a Jupyter notebook környezet - munkaterület - keresztül közvetlenül elérhető Python kódját könnyebb fejleszt. A modell platform fejlesztési folyamata viszonylag másoknak egyértelműbben használatával markdown jegyzeteket és diagramokat. Ezek notebookok tekinthetők, szerkeszteni és végre közvetlenül a munkaterületre.
- Betanított modellek maradnak, és blobtárolóba feltöltött. Ezzel a megoldással az adatok tudósok betanított modell objektumait nyomon követheti, és biztosítsa, hogy azok megtartott és szükség esetén lekérhető.
- Metrikák bejelentkezhet a modell teljesítmény pontszámok rögzítse az adatok tudósok engedélyezése Python-parancsfájl végrehajtása közben.
- A munkaterület testre szabható táblák a naplózott mérőszámok, lehetővé téve az adatok tudósok könnyen összehasonlítható modell teljesítménymutatók eredményez. Diagramok automatikusan jelennek meg, így a legjobb teljesítményt modell könnyen azonosítható legyen.
- Végezetül a példa bemutatja, hogyan lehet-e a egy trained model operationalized egy valós idejű webszolgáltatás telepítésével.

## <a name="link-to-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárházban csatolása
A valós forgatókönyv nyilvános GitHub-tárházban összes anyagok, beleértve a mintakódok, szükséges ehhez a példához tartalmazza:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Használja az eset áttekintése

Ebben a forgatókönyvben energia igény szerinti előrejelzés, ahol a cél az, hogy egy energia rácson a jövőbeli terhelés előrejelzése összpontosít. Ez megegyezik a kritikus fontosságú üzleti műveletet a vállalatok számára a energia ágazatban operátorok kell tartania a rácson felhasznált energia és a átadott energia finom egyensúlyára. Túl sok a rács megadott power energia-és műszaki hibák pazarlás eredményezhet. Azonban ha meg van adva a túl kevés power blackouts nélkül power ügyfelek elhagyása vezethet. Rács operátorok általában a rácshoz energiaellátás kezelésére, és tartsa a terhelés elosztása rövid távú döntések vehet igénybe. A pontos rövid távú előrejelzéseket energia igényű ezért elengedhetetlen az operátort, ezek a döntések az vetett bizalmat.

Ebben a forgatókönyvben a machine learning-megoldás az előrejelzés energia igény szerinti építése részletezi. A megoldás be van tanítva, egy nyilvános adatkészlet a a [New York független rendszer operátor (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), a New York állapothoz működik a power rács. A dataset tartalmazza az óránkénti power igény szerinti adatokat New York Város, öt évet egy adott időszakban. A kerül egy további adatkészlet óránkénti időjárási feltételek Budapesten azonos időszakra tartalmazó [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el).
- Egy telepített példánya [Azure Machine Learning-munkaterület](./overview-what-is-azure-ml.md) következő a [– első lépések a telepítési útmutató](./quickstart-installation.md) telepíteni a programot, és hozzon létre egy munkaterület.
- A példa feltételezi, hogy futtatja a Windows 10 Azure ML munkaterület [Docker-motorhoz](https://www.docker.com/) helyileg telepített. MacOS használatakor utasításokat ugyanazok a nagy mértékben.
- Az Azure Machine Learning Operationalization telepítve a helyi környezet beállítása és a modell felügyeleti fiókja ez leírtak szerint létrehozott [útmutató](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md).
- Ez a minta igényel a Pandas telepítés 0.20.3 verzióra frissíteni vagy magasabb és matplotlib telepítse. Kattintson a *nyissa meg a parancssort* a a *fájl* menü a munkaterületet üzemeltető futtatja a következő parancsokat a függőségek telepítése:

    ```
    conda install "pandas>=0.20.3"

    conda install matplotlib
    ```
    
## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning-munkaterület
2.  Az a **projektek** lapján kattintson a  **+**  aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** ablaktáblán, töltse ki az adatokat az új projekt
4.  Az a **keresési Projektsablonjai** keresési mezőbe, írja be a "Energia igény szerinti idő Series előrejelzési", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra


## <a name="data-description"></a>Adatok leírása

Két adatkészletet van: `nyc_demand.csv` és `nyc_weather.csv`:

**nyc_demand.csv** 2012-2017 évig óránkénti New York Város energia igény szerinti értéket tartalmaz. Az adatok egyszerű struktúrája a következő:

| Időbélyeg | igény szerint |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

Igény szerinti értékei megawatt óra (MWh). Alább energia igény szerint a 2017. július 7 napos időszakon át egy táblázatot a következő:

![Energiát igény szerinti](./media/scenario-time-series-forecasting/energy_demand.png  "energia igény szerint")

**nyc_weather.csv** az évek 2012-2017 óránkénti New York Város időjárási értéket tartalmaz:

| Időbélyeg | precip | TEMP
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* utaló szintjének százalékos mértéke. *temp* (hőmérséklet) értékek rendelkezik lett rescaled, úgy, hogy az összes érték tartozik, a [0, 100] intervallumban.

## <a name="scenario-structure"></a>A forgatókönyv struktúra

Ez a projekt először az Azure Machine Learning-munkaterület megnyitásakor navigáljon a *fájlok* a bal oldali ablaktáblán. Ez a minta a következő kód fájlok kapnak:
- `1-data-preparation.ipynb`-a Jupyter notebook tölt le és dolgozza fel az adatokat, hogy felkészítse az modellezési. Ez az az első notebook fogja futtatni.
- `2-linear-regression.ipynb`-a notebook betanítja egy lineáris regressziós modellt a betanítási adatokon.
- `3-ridge.ipynb`-betanítja peremmel regressziós modell.
- `4-ridge-poly2.ipynb`-a peremmel regressziós modell 2 mértéke a polinom szolgáltatások betanítja.
- `5-mlp.ipynb`-a többrétegű perceptron Neurális hálózat betanítja.
- `6-dtree.ipynb`-a döntési fa modell betanítja.
- `7-gbm.ipynb`-betanítja átmenetes súlyozott gép modell.
- `8-evaluate-model.py`– Ezt a parancsfájlt betölti a modell betanítását, és használja a birtokolt kibővített tesztelési adatkészletre előrejelzéseket készítsen. Így különböző modell teljesítményétől hasonlítható képes modell kiértékelése metrikákat.
- `9-forecast-output-exploration.ipynb`-a notebook hozza létre a gépi tanulási modellek által generált előrejelzések képi megjelenítések.
- `10-deploy-model.ipynb`-a notebook bemutatja, hogyan lehet-e a előrejelzési modell betanítását operationalized egy valós idejű webszolgáltatás.
- `evaluate-all-models.py`– Ez a parancsfájl kiértékeli az összes betanított modellek. Ahelyett, hogy fut biztosít `8-evaluate-model.py` minden betanítása minta külön-külön.

### <a name="1-data-preparation-and-cleaning"></a>1. Adatok előkészítése és a tisztítás

A minta futtató elindításához először kattintson az `1-data-preparation.ipynb` megnyitásához a notebook csak előzetes módban működik. Kattintson a ***Start Notebook Server*** a Jupyter notebook kiszolgáló és a Python kernel elindítani a számítógépen. A munkaterületen a jegyzetfüzet futtatásával, vagy használhatja a böngészőben navigáljon [http://localhost:8888](http://localhost:8888). Vegye figyelembe, hogy módosítania kell a kernel *projektnév helyi*. Ehhez a a *Kernel* menü alatti Jegyzetfüzet *módosítás kernel*. Nyomja le az ***shift + Enter*** egyedi notebook celláiban a kódra, vagy kattintson *minden* a a *cella* menü a teljes jegyzetfüzet futtatásához.

A notebook először letölti az adatokat az Azure-platformon futó blob storage tárolója. A majd tárolja a gép a `AZUREML_NATIVE_SHARE_DIRECTORY`. Ezen a helyen bármely notebookok elérhető-e, vagy futtatja a munkaterület parancsfájlok így egy érdemes tárolni az adatokat és betanított modellek.

Miután letöltötte az adatokat, a notebook törli az adatok kitöltésével idősorozatban hiányosságait, és a köztes által kitöltése a hiányzó értékeket. Ezzel a módszerrel a idő adatsorozat adatainak törlése a lehető legnagyobbra növeli a modellek betanítása adatok mennyiségét.

Több modell funkciót a megtisztított nyers adatokból jönnek létre. Ezek a funkciók csoportosíthatók két csoportra bontják:

- **Idő áll a szolgáltatások** képzi a rendszer a *időbélyeg* változó pl. *hónap*, *dayofweek* és *óra*.
- **Szolgáltatások kipufogócsöveket** időértékek vette a tényleges igény szerint vagy hőmérséklet értékek vannak. Ezek a szolgáltatások célja, hogy a modell egymást követő időszakok feltételes függőségeinek rögzítése.

Az eredményül kapott szolgáltatás adatkészlet majd előrejelzési modellek fejlesztése során használható.

### <a name="2-model-development"></a>2. Modell fejlesztési

Egy első modellezéshez hatékony módszer lehet egy egyszerű lineáris regressziós modellt. A `2-linear-regression.ipynb` notebook mutatja be egy lineáris regressziós jövőbeli energia igény szerinti előrejelzési modell betanításához. Különösen lesz-e a a modell betanítása előre jelezni az energiafogyasztás, igény szerint egy óra (*t + 1*) az aktuális időszakot előre (*t*). Azonban azt is érvényesek a "egylépéses" modell rekurzív módon tesztidejének létrehozására az előrejelzések jövőbeli időszakokat, *t + n*.

A modell betanításához egy prediktív folyamat jön létre, három különböző lépést foglal magában, amelyek:

- **A data transformation**: a bemeneti adatok szükséges formátumokat eltérőek lehetnek attól függően, hogy a gépi tanulási algoritmus. Ebben az esetben a lineáris regressziós modell szükséges egy közbeni kódolni kategorikus változók.
- **A közötti érvényesítési rutin**: gyakran a gépi tanulási modell egy vagy több hiperparamétereket, kísérletezhet keresztül kell hangolni kell rendelkeznek. Az optimális paraméter értékhalmazt található keresztellenőrzési használható. A modell ismételten betanítása és különböző modellrészt a DataSet adatkészlet alapján értékeli ki. A legjobb paraméterei azokat, ha átlagosan volt a keresztellenőrzési modellrészt legjobb modell teljesítmény érdekében. Ez a folyamat kifejtett részletes `2-linear-regression.ipynb`.
- **A végső modell betanítása**: A modell betanítása a teljes adatkészlethez, a legjobb hiperparamétereket használatával.

Több különböző modell 6 notebookok számozott 2 – 7 vannak megadva. Minden egyes notebook betanítja egy másik modellhez, és tárolja azokat a a `AZUREML_NATIVE_SHARE_DIRECTORY` helyét. Ebben a forgatókönyvben fejlesztett összes modell rendelkezik betanítása, amennyiben azt értékelje ki, és hasonlítsa össze azokat a következő szakaszban leírt.

### <a name="3-model-evaluation-and-comparison"></a>3. Modell kiértékelése és összehasonlítása

A Microsoft segítségével egy trained model előrejelzések jövőbeli időpont időszakokra. Célszerű, ezek a modellek a birtokolt kibővített tesztelési adatkészletre kiértékeléséhez. A különböző modellek számára nem látható DataSet adatkészlethez az kiértékelésével azt is viszonylag vetik össze a, és azonosítsa a legjobb modell. Ebben a forgatókönyvben a betanított modell rekurzív módon több idő lépés a jövőben előrejelzési érvénybe lépni. Különösen előrejelzések azt létrehozni legfeljebb hat órán keresztül *t + 6* előre a jelenlegi órán *t*. Ezek előrejelzéseket rendszer összehasonlítja a megfigyelt minden időszak tényleges igény szerint.

A modell kiértékelése, nyissa meg a `8-evaluate-model.py` parancsfájl a *fájlok* a munkaterület ablaktáblán. Ellenőrizze, hogy *futtatása konfigurációs* értékre van állítva **helyi** és a modell nevét írja be a *argumentumok* mező. A modell neve pontosan egyezniük kell a *modell_neve* változó beállítása a notebook, amelyben a modell betanítása elején. Például írja be a "linear_regression", a betanított lineáris regressziós modell kiértékelése. Azt is megteheti, amennyiben az összes modell rendelkezik betanítva, kiértékelheti az őket egy paranccsal való futtatásával `evaluate-all-models.py`. Futtassa ezt a parancsfájlt, a parancssor megnyitása a munkaterületre, és hajtsa végre a következő parancsot:

```
python evaluate-all-models.py
```
A `8-evaluate-model.py` parancsfájl a következő műveleteket hajtja végre:

- A betanított modell csővezeték betölti a lemezről
- A tesztelési adatkészletnél előrejelzéseket lesz
- Kiszámítja a modell teljesítménymutatók, és bejelentkezik a
- Menti a vizsgálat a dataset előrejelzéseket `AZUREML_NATIVE_SHARE_DIRECTORY` újabb vizsgálati és elemzési
- Menti a betanított modell kimenetátirányítási a *kimenete* mappát.

> [!Note]
> A modell mentése a *kimenete* mappa automatikusan átmásolja a modellobjektumot be a kísérleti fiókjához társított Azure Blob Storage-fiókba. Ez azt jelenti, hogy mindig lesz a mentett modell objektum lekérése a blob, még akkor is, ha módosítja a számítógép vagy számítási környezetben.

Keresse meg a *futtatása előzmények* ablaktáblán, majd kattintson a `8-evaluate-model.py`. Több modell teljesítménymutatók megjelenítő diagramok jelenik meg:

- **A NEKEM**: Hiba történt az előzetes jelenti. Ez az előzetes átlagos időeltérés értelmezhető.
- **Legnagyobb megengedett**: [százalékos hiba jelenti](https://en.wikipedia.org/wiki/Mean_percentage_error) (ME a tényleges igény szerinti százalékában kifejezve)
- **MSE**: [mean squared hiba](https://en.wikipedia.org/wiki/Mean_squared_error)
- **Gyökátlagos**: legfelső szintű közepét négyzet hiba (négyzetgyökét MSE)
- **MAPE**: [jelenti abszolút százalékos hiba](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [szimmetrikus átlagos abszolút százalékos hiba](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: Alapterv MAPE előrejelzési, amelyben az előrejelzés az utolsó ismert igény szerinti érték egyenlő.
- **MdRAE**: medián relatív abszolút hiba. Az előrejelzési hiba az alaptervhez közepes aránya előrejelzési hiba. Egy érték kisebb, mint 1 azt jelenti, hogy az előrejelzés jobb, mint az eredeti hajt végre.

Az összes fenti metrikák tekintse meg a *t + 1* előrejelzési. A fenti metrikák mellett is látni fogja a megfelelő mértékek készletét a *_horizon* utótag. Ez az a mérték csak pontokból, az előrejelzési közé időszak átlagolva *t + 1* időszak *t + 6*.

Ha a metrikák jelennek meg a diagramterületen, kattintson a jobb felső sarkában található fogaskerék szimbólum. Győződjön meg arról, hogy a modell metrikák érdekli ellenőrzi. A metrikák egy, a diagramok az alábbi táblázatban is megjelennek. Ebben az esetben ezt a táblázatot testre szabható-e a fogaskerék szimbólummal kell kattintva. Rendezi a táblát a legjobb modell azonosításához teljesítmény metrika szerint. Ha érdekli jelent meg, hogyan az előrejelzési a teljesítmény változhat az időszakba eső *t + 1* való *t + 6*, kattintson a bejegyzésre a táblázat a modellben. A MAPE megjelenítő diagramok, legnagyobb megengedett és MdRAE metrikák között az előrejelzési időszak alatt jelennek *képi megjelenítések*.

Előrejelzési modellek kiértékelésekor megjelenítéséhez a kimeneti előrejelzéseket nagyon hasznos lehet. Ezzel a megoldással az adatok tudósok annak meghatározásához, hogy reális megjelenjen-e az előzetes hozott létre. Azt is segítenek az előrejelzés a problémák azonosításához, ha, például az előrejelzés hajt végre a rosszul bizonyos időszakokra. A `9-forecast-output-exploration.ipynb` notebook létre a tesztelési adathalmazon előrejelzések képi megjelenítések eredményez.

### <a name="4-deployment"></a>4. Környezet

A legjobb modell is operationalized, valós idejű webszolgáltatásként telepítésével. Ez a webszolgáltatás majd előrejelzések az igény szerinti létrehozásához, amint az elérhetővé válik az új adatok hívható meg. Ebben a forgatókönyvben az új előrejelzés kell óránként hozható létre előre jelezni az energia igény szerint a következő órában. Ez a feladat végrehajtásához egy webszolgáltatás-bővítmény lehet telepítve, amely tömb szolgáltatások esetében egy adott időszakban bemenetként, és az előre jelzett igény szerinti kimeneteként adja vissza.

Ez a minta egy webszolgáltatás-bővítmény a Windows 10-es gépre van telepítve. Ellenőrizze, hogy végrehajtotta az Előfeltételek szakaszában meghatározott helyi telepítéshez [– első lépések útmutató](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) a Operationalization CLI számára. Miután állította be a helyi környezet és a modell-felügyeleti fiókja, futtassa a `10-deploy-model.ipynb` notebook használatához a webszolgáltatás üzembe helyezése.

## <a name="conclusion"></a>Összegzés

Ez a minta bemutatja, hogyan hozhat létre egy végpontok közötti idősor megoldás előrejelzés energia igény szerint az előrejelzés céljából. Ez a példa felfedezte ezeket az alapelveket számos más előrejelzési forgatókönyveit és iparágakban bővíthető.

Ebből a forgatókönyvből megtudhatja, hogyan Azure Machine Learning-munkaterület segít a adatok tudósok hasznos szolgáltatásait, például a Jupyter notebook környezet és a metrika naplózási képességeket valós megoldások kidolgozásában. A minta az olvasó is útmutató egy modell is lehet operationalized és az Azure Machine Learning Operationalization parancssori felület használatával telepíthetők. Amennyiben telepített, a webszolgáltatási API lehetővé teszi a fejlesztők vagy adatok mérnökök az előrejelzési modell integrálja szélesebb adatok folyamat.
