---
title: A kiadás újdonságai?
titleSuffix: Azure Machine Learning service
description: Ismerje meg a legújabb frissítéseket az Azure Machine Learning szolgáltatás és a machine learning és az adat-előkészítési Python SDK-k.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: d43bef902b66976c32735b6d45029f41bb5e3264
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514043"
---
# <a name="azure-machine-learning-service-release-notes"></a>Az Azure Machine Learning szolgáltatás kibocsátási megjegyzései

Ebben a cikkben megismerheti az Azure Machine Learning szolgáltatás kiadások.  Az SDK teljes leírását látogasson el a referenciadokumentumai:
+ Az Azure Machine Learning [ **fő SDK a Pythonhoz**](https://aka.ms/aml-sdk)
+ Az Azure Machine Learning [ **adat-előkészítési SDK**](https://aka.ms/data-prep-sdk)

Lásd: [kapcsolatos ismert problémák listája](resource-known-issues.md) ismert hibák és a lehetséges megoldások megismeréséhez.


## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Az Azure Machine Learning adat-előkészítési SDK v1.1.7

Azt állítja vissza a rendszer egy olyan módosítást, a jobb teljesítmény, mivel problémákat okozott az egyes ügyfelek az Azure Databricks használatával. Ha az Azure databricks szolgáltatásban hibát tapasztal, az alábbi módszerek egyikével 1.1.7 verzióra frissíthet:
1. Futtassa ezt a szkriptet frissítése: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Hozza létre újra a fürt a Data Prep SDK legújabb verzióját telepíti.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Az Azure Machine Learning SDK for Python v1.0.45

+ **Új funkciók**
  + Döntési fa helyettes modell referenciaszámítógépnek ismertető azureml azt ismertetik, modell-csomag hozzáadása
  + Adjon meg egy CUDA verziót telepíteni a következtetési lemezképek képessége. CUDA 9.0 9.1 és 10.0 támogatása.
  + Információ az Azure Machine Learning betanítási alaplemezképek már elérhetők a [Azure ML tárolók GitHub-adattár](https://github.com/Azure/AzureML-Containers) és [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + A hozzáadott CLI folyamat ütemezése támogatása. További információért futtassa a "az ml folyamat -h"
  + Egyéni Kubernetes-névtér paraméter hozzáadja az AKS webszolgáltatás telepítési konfigurációjáról és a parancssori felület.
  + Elavult hash_paths paraméter az összes folyamat lépései
  + Model.register mostantól támogatja a több egyéni fájlok regisztrálása egy adott modellt használ, a `child_paths` paraméter.
  
+ **Előzetes verziójú funkciók**
    + Pontozó explainers igény szerint most conda mentheti és pip megbízhatóbb szerializálást és deszerializálást adatait.
    + A szolgáltatás-választó automatikus hibajavítás.
    + Frissítve az új api, az új telepítés által elérhetővé tett javított hibák mlflow.azureml.build_image.

+ **Használhatatlanná tévő változásai**

+ **Hibajavítások és kapcsolatos fejlesztések**
  + Eltávolított paramiko függőség az azureml-core. A hozzáadott elavulással kapcsolatos figyelmeztetések örökölt számítási célnak csatolása módszereket.
  + Run.create_children teljesítményének növelése
  + A mimic ismertető a bináris osztályozó javítsa ki az valószínűségek sorrendjét, oktatói valószínűségi használata méretezését Alakzatadatok értékek
  + Továbbfejlesztett hiba- és automatikus gépi tanulási üzenet. 
  + Kijavítva az iteráció időtúllépési automatikus machine Learning.
  + Továbbfejlesztett automatikus gépi tanulási idősorozat-átalakítás teljesítményét.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Az Azure Machine Learning adat-előkészítési SDK v1.1.6

+ **Új funkciók**
  + Felső értékek összesítő függvények hozzáadott (`SummaryFunction.TOPVALUES`) és alsó értékek (`SummaryFunction.BOTTOMVALUES`).

+ **Hibajavítások és kapcsolatos fejlesztések**
  + Jelentősen javult a teljesítménye `read_pandas_dataframe`.
  + Kijavítva a hiba, amely miatt `get_profile()` a sikertelen bináris fájlokra mutató adatfolyamot.
  + Közzétett `set_diagnostics_collection()` a programozott engedélyezése vagy letiltása a telemetriai adatok gyűjtésének engedélyezéséhez.
  + A viselkedése megváltozott `get_profile()`. NaN értéket most figyelmen kívül hagyja, Min, átlag, Standard és a Sum, amely igazodik a Pandas viselkedését.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Az Azure Machine Learning SDK for Python v1.0.43

+ **Új funkciók**
  + Most már az Azure Machine Learning népszerű gép tanulási és elemzési keretrendszer Scikit további osztályú támogatási szolgáltatásokat biztosít. Használatával [ `SKLearn` estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py), felhasználók könnyedén betanítása és Scikit további modellek üzembe helyezése.
    + Ismerje meg, hogyan [futtassa a hiperparaméter finomhangolása a Scikit-további HyperDrive használatával](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Támogatás hozzáadva a ModuleStep létrehozása újrahasznosítható számítási egységek kezelése modul és ModuleVersion osztályok együtt folyamatokban.
  + ACI problémák megoldásához segítséget mostantól támogatják az állandó scoring_uri frissítéseken keresztül. A scoring_uri FQDN változik IP-címről. A Dns-névcímke teljes tartományneve az dns_name_label deploy_configuration beállításával konfigurálható. 
  + Automatizált machine learning-új funkciók:
    + Az előrejelzési STL featurizer
    + KMeans fürtszolgáltatás funkció kezdik engedélyezve van
  + Jóváhagyások AmlCompute kvóta most vált gyorsabb! Most a folyamat egy küszöbértéken belül a kvóta kérelmek jóváhagyása automatizálta azt. További információ a kvóták működéséről további [kvóták kezelése](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

+ **Előzetes verziójú funkciók**
    + Integráció a [MLflow](https://mlflow.org) nyomon követése az azureml-mlflow csomag 1.0.0-s ([példa notebookok](https://aka.ms/azureml-mlflow-examples)).
    + Küldje el a Jupyter notebook számít. [API dokumentációja](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Nyilvános előzetes verziója [adatok eltéréseket detector használatával](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) keresztül azureml-contrib-datadrift csomag ([példa notebookok](https://aka.ms/azureml-datadrift-example)). Adatok eltéréseket az egyik a leggyakoribb okok ahol rontja a pontosság idővel. Azt történik, ha adatok kiszolgált modellek éles környezetben eltér az adatokat, amelyek a modell tanított volt. AML adatok eltéréseket detector használatával segít vásárlóinak felfedezni figyelése adatokat eltéréseket, és riasztást küld, amikor eltéréseket észlel. 

+ **Használhatatlanná tévő változásai**

+ **Hibajavítások és kapcsolatos fejlesztések**
  + RunConfiguration betölteni, és mentse a fájl teljes elérési út megadását az előző viselkedés teljes biztonsági kompatibilitási támogatja.
  + Hozzáadja a gyorsítótárazás az ServicePrincipalAuthentication, alapértelmezés szerint ki van kapcsolva.
  + Engedélyezze az azonos metrika neve alatt található több ábra naplózását.
  + Most már megfelelően importálható a azureml.core osztály Model (`from azureml.core import Model`).
  + A folyamat lépések `hash_path` paraméter elavult. Viselkedés az ujjlenyomat-teljes forráskönyvtár, kivéve a .amlignore vagy .gitignore felsorolt fájlokat.
  + A folyamat csomagok különböző `get_all` és `get_all_*` metódusok elavultak érvénytelenítve `list` és `list_*`, illetve.
  + azureml.Core.get_run többé nem kell az eredeti futtatási típus visszaküldése előtt importálni kívánt osztályokat.
  + Javítva lett egy probléma, ahol egyes hívások webszolgáltatás Update nem aktiválódtak egy frissítést.
  + Időtúllépés kiértékelése AKS problémák megoldásához segítséget 5ms és 300000ms között kell lennie. A megengedett maximális pontozási kérelmek scoring_timeout_ms 1 perc, 5 perc bumped.
  + Most már LocalWebservice objektumok `scoring_uri` és `swagger_uri` tulajdonságait.
  + Áthelyezett kimenetek címtár létrehozása és a kimeneti könyvtár feltöltése a felhasználó folyamaton kívül. Futtatási előzmények SDK minden felhasználói folyamat futtatása engedélyezett. Ez kell feloldhatónak lennie bizonyos szinkronizálási elosztott képzési által tapasztalt problémák futtatja.
  + Az azureml naplót kell írni a felhasználói folyamat nevét a neve most folyamat nevét (csak elosztott betanítása) és PID tartalmazza.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Az Azure Machine Learning adat-előkészítési SDK v1.1.5

+ **Hibajavítások és kapcsolatos fejlesztések**
  + Értelmezett dátum/idő értékek, amelyek 2 számjegyű év formátumban frissítve lett a tartomány érvényes éves Windows előfordulhat, hogy kiadási megfelelően. A tartomány 1950-2049 a megváltozott a 1930-2029.
  + Olvasásakor a fájl- és a beállítás `handleQuotedLineBreaks=True`, `\r` , egy új sor lesznek kezelve.
  + Kijavítva a hiba kiváltó `read_pandas_dataframe` bizonyos esetekben meghiúsul.
  + Továbbfejlesztett teljesítmény az `get_profile`.
  + Továbbfejlesztett hibaüzenetek.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Az Azure Machine Learning adat-előkészítési SDK v1.1.4

+ **Új funkciók**
  + Mostantól használhatja az alábbi kifejezés nyelvi funkciókat való kibontása és elemzése a dátum/idő értékek új oszlopba.
    + `RegEx.extract_record()` kibontja a dátum és idő, elem egy új oszlopba.
    + `create_datetime()` dátum és idő objektumok külön dátum és idő elemeket hoz létre.
  + Hívásakor `get_profile()`, most már megtekintheti, hogy ki osztóérték oszlopok van jelölve (becsült) egyértelműen jelzi, hogy az értékek a következők becsült dátumokkal.
  + Mostantól használhat ** helyettesítés olvasása az Azure Blob Storage-ból.
    + Például: `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Hibajavítások**
  + Kijavítva a hiba, a Parquet-fájlokba olvasásakor távoli forrásból (Azure Blob) kapcsolatos.

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Az Azure Machine Learning SDK for Python v1.0.39
+ **Módosítások**
  + Futtatási konfigurációs auto_prepare_environment beállítás elavulttá válik, az automatikus előkészítése az alapértelmezett váljon.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Az Azure Machine Learning adat-előkészítési SDK v1.1.3

+ **Új funkciók**
  + Támogatás hozzáadva egy PostgresSQL adatbázist, vagy read_postgresql hívása, vagy egy adattárolója használatával olvasni.
    + A gyakorlati példa látható:
      + [Adatok Adatbetöltési notebook](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Adattároló-jegyzetfüzet](https://aka.ms/aml-data-prep-datastore-nb)

+ **Hibajavítások és kapcsolatos fejlesztések**
  + Oszlop típusának átalakítása fix problémái:
  + Most már megfelelően alakítja át egy olyan logikai vagy numerikus oszlop egy logikai típusú oszlop.
  + Most nem sikertelen megkísérlésekor. a dátum oszlop dátumtípus kell beállítani.
  + Továbbfejlesztett JoinType típusok valamint a hozzájuk kapcsolódó dokumentációja. Amikor két adatfolyamot, most már megadhatja ezeket különböző típusú join:
    + NINCS MEGFELELŐ, A BELSŐ, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, TELJES.
  + Továbbfejlesztett adattípus következtetési további dátumformátumok felismerni.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

Az Azure Portalon az alábbi műveleteket hajthatja végre:
+ Hozhat létre és futtathat automatizált Machine Learning-kísérletek 
+ Próbálja ki a minta Jupyter notebookok Notebook virtuális gép létrehozása vagy a saját.
+ A Machine Learning szolgáltatás munkaterületén, például a Machine Learning automatizált, vizuális felhasználói felületet és üzemeltetett Notebook virtuális gépek új szerzői műveletek szakaszának (előzetes verzió)
    + Automatikusan létrehoz egy modellt, automatikus machine learning segítségével 
    + Egy húzza használja, és dobja el a kísérleteket futtathat a vizuális felhasználói felületet
    + Adatok megismerése, modellek létrehozása és-szolgáltatások telepítését a jegyzetfüzet virtuális gép létrehozása.
+ Élő diagramot és a mérőszám a jelentések futtatása frissítése és a Futtatás részletei lapon
+ A naplók, kimenetek és a Futtatás részletei lapon pillanatképek megjelenítő frissített fájlt.
+ Új és továbbfejlesztett jelentés létrehozásának élmény, a kísérletek lapon. 
+ Töltse le a config.json fájlt az Azure Machine Learning szolgáltatás munkaterület áttekintés oldalán a hozzáadott képessége.
+ Támogatja a Machine Learning szolgáltatás munkaterület létrehozása az Azure Databricks-munkaterület 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Az Azure Machine Learning SDK for Python v1.0.33
+ **Új funkciók**
  + A _Workspace.create_ metódus már elfogad alapértelmezett fürtkonfigurációk a Processzor és GPU-fürtöket.
  + Ha a munkaterület létrehozása sikertelen, a függő erőforrások törölve lettek.
  + Alapértelmezett Azure Container Registry Termékváltozat az alapszintű nem kapcsolták.
  + Az Azure Container Registry ráérősen, jön létre a futtató- vagy képfájl létrehozásához szükség esetén.
  + A betanítási futtatások környezetek támogatása.

### <a name="notebook-virtual-machine"></a>Notebook virtuális gép 

Egy biztonságos, nagyvállalati használatra kész üzemeltetési környezet, amelyben a program machine learning-kísérletek, webes végpontként modellek üzembe helyezése, és hajtsa végre az Azure Machine Learning-SDK-ban Python által támogatott összes egyéb műveletek Jupyter notebookokhoz Notebook virtuális Gépet használjuk. Különböző képességeket biztosít:
+ [Egy előre konfigurált virtuális gép notebookot gyorsan kialakíthatja](quickstart-run-cloud-notebook.md) , amely rendelkezik az Azure Machine Learning SDK-t és a kapcsolódó csomagok legújabb verzióját.
+ Hozzáférés védelmét bevált technológiák, például a HTTPS, az Azure Active Directory-hitelesítés és engedélyezés keresztül.
+ Megbízható felhőalapú tárolását, jegyzetfüzeteket és a kódot az Azure Machine Learning-munkaterület blob storage-fiókban. A virtuális gép jegyzetfüzet nyugodtan törölheti a munkahelyi elvesztése nélkül.
+ Előtelepített mintafüzetek vizsgálata, és kísérletezzen az Azure Machine Learning-szolgáltatások.
+ Teljes testreszabási lehetőségeket az Azure virtuális gépek, virtuális gép bármilyen, a csomagokat, illesztőprogramok. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Machine Learning készült Azure SDK, amely a Python v1.0.33.

+ Az Azure Machine Learning hardver gyorsított modellek [FPGA-kban](concept-accelerate-with-fpgas.md) általánosan elérhető.
  + Mostantól [használhatja az azureml-gyorsított-modellek csomagot](how-to-deploy-fpga-web-service.md) való:
    + A súlyok (ResNet-50, ResNet 152, DenseNet-121, VGG-16 és SSD-VGG) támogatott Neurális hálózat betanítása
    + A támogatott DNN-tanulási átvitel használata
    + Regisztrálja a modellt a Modellkezelési szolgáltatás és a modell tárolóba
    + A modell üzembe helyezése egy Azure virtuális géphez az Azure Kubernetes Service (AKS)-fürtben egy FPGA
  + A tároló üzembe helyezése egy [az Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) kiszolgáló eszköz
  + A gRPC végpont ennek az adatok pontozása [minta](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizált Machine Learning

+ Ez a funkció engedélyezéséhez a teljesítmény optimalizálásához dinamikusan hozzáadásának featurizers abszolút. Új featurizers: beágyazásokat, bizonyíték, a cél kódolásokat, a cél kódování textu, a fürt távolság súly működik
+ Intelligens CV kezeléséhez train érvényes bontja belüli automatikus gépi tanulás
+ Néhány optimalizálást memóriamódosítások és modul teljesítményének fokozása
+ A teljesítmény javítása a modell ismertetése
+ ONNX modell átalakítás a helyi Futtatás
+ Subsampling támogatása
+ Intelligens leállítása, ha nincsenek megadva kilépési feltételek
+ Halmozott együttesek

+ Idősor-előrejelzési
  + Előrejelzési függvény új előrejelzése   
  + A működés közbeni eredetű keresztellenőrzési mostantól használhatja az idősorozat-adatok
  + Konfigurálja a time series késik hozzáadott új funkciók 
  + Működés közbeni ablak összesített funkciók támogatása érdekében adott hozzá új funkciók
  + Szünnap új észlelési és featurizer országkód: ha kísérletezni beállításai

+ Azure Databricks
  + Idősorozat-előrejelzés engedélyezve, és a modell explainabilty és e funkció
  + Most már visszavonhatja és a folytatási kísérletek (Folytatás) automatizált gépi tanulás
  + Támogatás hozzáadva a Többmagos feldolgozása

### <a name="mlops"></a>MLOps
+ **Helyi üzembe helyezés és a tárolók pontozó hibakeresés**<br/> Mostantól helyileg egy gépi Tanulási modell üzembe helyezése és a pontozási fájlt, és győződjön meg arról, hogy a várt módon működnek, hogy gyors iterációkat végezhetnek.

+ **Bevezetett InferenceConfig & Model.deploy()**<br/> Telepítés most már támogatja a forrásmappa megadása egy bejegyzést, parancsfájl ugyanaz, mint egy RunConfig modellje.  Modell-üzembehelyezés továbbá egyszerűsítettük egyetlen paranccsal.

+ **Követési Git-referencia**<br/> Ügyfelek rendelkezik lett kér alapvető Git-integrációs funkciók esetében egy ideig, mert segít fenntartani teljes körű naplózás. Követési implementáltuk az Azure Machine Learning Git kapcsolatos metaadatokat (tárház, véglegesítés, tiszta állapotot) a nagyobb entitás között. Ez az információ az SDK és a CLI által automatikusan gyűjtendő.

+ **A modellekre profilkészítés és az érvényesítési szolgáltatás**<br/> Ügyfeleink gyakran panaszok a méretének következtetésekhez szolgáltatásukhoz járó nehézségek. Az a profilkészítés szolgáltatási modell, az ügyfél biztosíthat minta bemeneti adatok, és azt fogja profil között különböző 16 CPU / telepítés méretezése optimális meghatározni a tárolómemória beállításai.

+ **A saját alaplemezkép használata a következtetésekhez**<br/> Egy másik gyakori panasz következtetésekhez ismételt megosztási függőségek Kísérletezési áthelyezését a nehéz volt. Az új alaplemezkép megosztása funkció, a most már felhasználhatja a Kísérletezési alaplemezképek, a függőségeket és minden, a következtetésekhez. Ez a kell felgyorsíthatja a központi telepítések, és a belső a térköz csökkentése a külső hurok.

+ **Swagger séma generációs továbbfejlesztett**<br/> Az előző swagger generációs metódus rejt magában, és nem lehet automatizálni hiba történt. Összeállítottunk egy új beágyazott módszer generálása swagger-sémák bármely Python-függvény decorator keresztül. A Microsoft rendelkezik nyílt forráskódú ezt a kódot, és a séma generációs protokoll nem összefüggő az Azure ML-platformra.

+ **Az Azure ML parancssori felületének az általánosan elérhető (GA)**<br/> Modellek mostantól egyetlen parancssori paranccsal is telepíthető. Fájt közös ügyfélvisszajelzések, senki nem helyez üzembe egy gépi Tanulási modellt, a Jupyter notebook. A [ **CLI dokumentációja** ](https://aka.ms/azmlcli) frissítve lett.


## <a name="2019-04-22"></a>2019-04-22

Machine Learning készült Azure SDK, amely a Python v1.0.30.

A [ `PipelineEndpoint` ](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) volt bevezetni hozzáadása egy közzétett folyamatot egy új verziója azonos végpont fenntartása mellett.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Az Azure Machine Learning adat-előkészítési SDK v1.1.2

Megjegyzés: Data Prep Python SDK már nem telepíti a `numpy` és `pandas` csomagokat. Lásd: [frissítése a telepítési utasításokat](https://aka.ms/aml-data-prep-installation).

+ **Új funkciók**
  + Mostantól használhatja a Pivot átalakítás.
    + Útmutató: [Pivot notebook](https://aka.ms/aml-data-prep-pivot-nb)
  + Mostantól használhatja a reguláris kifejezések a natív függvények.
    + Példák:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Most már használhatja `to_upper`  és `to_lower`  kifejezés nyelv függvényei.
  + Most láthatja az egyes oszlopok data profil egyedi értékek száma.
  + A gyakran használt olvasó lépések némelyikét, most adhasson a `infer_column_types` argumentum. Ha a változó értéke `True`, Data Prep megkísérli észlelése és automatikus átalakítás típusú oszlophoz.
    + `inference_arguments` most már elavult.
  + Most már hívása `Dataflow.shape`.

+ **Hibajavítások és kapcsolatos fejlesztések**
  + `keep_columns` most már a további választható argumentumot fogad el `validate_column_exists`, amely ellenőrzi, hogy eredményét `keep_columns` oszlopokat tartalmazza.
  + Minden olvasó lépést (amely beolvasása fájlból) mostantól elfogadják a további nem kötelező argumentum `verify_exists`.
  + Továbbfejlesztett teljesítmény pandas dataframe a beolvasása, illetve adatokat profilok beolvasása.
  + Kijavítva a hiba, ahol egyetlen indexszel rendelkező egyetlen lépésben egy adatfolyam a szeletelés sikertelen.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Egy meglévő parancsfájl futtatása egy meglévő távoli számítási fürtön most küldje el újra. 
  + A folyamatok lapon új paraméterekkel most már futtathatja egy közzétett folyamatot. 
  + Futtatás részletei mostantól támogatja az egy új fájl pillanatfelvételt. A könyvtár pillanatképet egy adott Futtatás elküldésekor tekintheti meg. A notebook, indítsa el a futtató el lett küldve is letöltheti.
  + Most már visszavonhatja szülő fut az Azure Portalról.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Az Azure Machine Learning SDK for Python v1.0.23

+ **Új funkciók**
  + Az Azure Machine Learning SDK már támogatja a Python 3.7-es verzióját.
  + Az Azure Machine Learning DNN Estimators többverziós beépített támogatást biztosít. Például `TensorFlow`  estimator már elfogad egy `framework_version` paraméter és a felhasználók adhat meg a "1.10" vagy "1.12" verzió. Az SDK jelenlegi kiadása támogatja a verziók listája, hívja `get_supported_versions()` a kívánt keretrendszer osztályról (például `TensorFlow.get_supported_versions()`).
  Az SDK legújabb kiadása által támogatott verziók listáját lásd: a [DNN Estimator dokumentáció](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Az Azure Machine Learning adat-előkészítési SDK 1.1.1-es

+ **Új funkciók**
  + Több adattárolója/DataPath/DataReference forrásból read_ * átalakítók használatával olvashatja.
  + Az oszlopok egy olyan új oszlop létrehozása a következő műveletek elvégzése: osztás, emelet, modulus power, hossza.
  + Adatelőkészítés most már az Azure ML-diagnosztika csomag része, és alapértelmezés szerint naplózza a diagnosztikai adatokat.
    + Kikapcsolja ezt a funkciót, állítsa igaz értékre a környezeti változót: DISABLE_DPREP_LOGGER

+ **Hibajavítások és kapcsolatos fejlesztések**
  + Továbbfejlesztett code dokumentáció a gyakran használt osztályok és funkciók.
  + Kijavítva a hiba, amely nem sikerült beolvasni az Excel-fájlok auto_read_file a.
  + Ez a beállítás hozzáadott read_pandas_dataframe a mappa felülírásához.
  + Továbbfejlesztett teljesítmény dotnetcore2 függőség telepítése és a Fedora 27/28- és Ubuntu 1804 támogatása.
  + Az Azure-Blobok olvasási teljesítményének növelése.
  + Adattípus észlelése oszlop típusú oszlopok hosszú támogatja.
  + Kijavítva a hiba, ahol egyes dátumértékeket is jelenik meg időbélyegeket Python datetime objektumok helyett.
  + Kijavítva a hiba, ahol bizonyos típusú számát is jelenik meg helyett egész számok Double típusú értékekkel.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Az Azure Machine Learning SDK for Python v1.0.21

+ **Új funkciók**
  + A *azureml.core.Run.create_children* módszer lehetővé teszi, hogy több gyermek közel valós idejű létrehozása egyetlen hívással futtatja.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Az Azure Machine Learning adat-előkészítési SDK v1.1.0

+ **Használhatatlanná tévő változásai**
  + Az adat-előkészítő csomag fogalma elavult, és már nem támogatott. Ahelyett, hogy egy csomag több adatfolyamot átlátni, megőrizheti a adatfolyamok külön-külön.
    + Útmutató: [Nyitó és mentése adatfolyamok notebook](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Új funkciók**
  + Data Prep mostantól képes felismerni az oszlopokat, amelyek egy adott szemantikai megfelel, és ennek megfelelően fel. A jelenleg támogatott STypes tartalmazza: e-mail-cím, földrajzi koordinátákat (földrajzi szélesség és hosszúság), az IPv4 és IPv6-címek, Egyesült Államokbeli telefonszámok és az Amerikai.
    + Útmutató: [Szemantikai típusok notebook](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Adatelőkészítés mostantól támogatja a következő műveletek használatával hozzon létre egy eredő oszlopot két numerikus oszlopok: kivonása, szorzása, osztani, és a modulus.
  + Meghívhatja `verify_has_data()` a ellenőrizze, hogy az adatfolyamot eredményezne rekordok, ha egy adatfolyamot.

+ **Hibajavítások és kapcsolatos fejlesztések**
  + Mostantól megadhatja a használandó hisztogram a numerikus oszlopok profilok dobozok száma.
  + A `read_pandas_dataframe` átalakítás mostantól csak az adathalmaz, hogy a karakterlánc - vagy bájt típusmegadású oszlopneveket.
  + Kijavítva a hiba található a `fill_nulls` átalakítás, ahol értékeket is nem megfelelően tölti ki Ha az oszlop hiányzott.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Az Azure Machine Learning SDK for Python v1.0.18

 + **Módosítások**
   + Az azureml-tensorboard csomag azureml-contrib-tensorboard váltja fel.
   + Ebben a kiadásban állíthat be egy felhasználói fiókot a felügyelt számítási fürtön (amlcompute), azt létrehozása során. Ez elvégezhető az üzembe helyezési konfiguráció adja át ezeket a tulajdonságokat. További részleteket talál a [SDK-forrásdokumentáció](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.17

+ **Új funkciók**
  + Most már két numerikus oszlopok létrehozásához a kifejezés nyelv használatával eredő oszlop hozzáadását támogatja.

+ **Hibajavítások és kapcsolatos fejlesztések**
  + Továbbfejlesztett dokumentáció és random_split paraméter.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.16

+ **Hibajavítás**
  + Egy egyszerű szolgáltatást, amely okozta hitelesítési probléma kijavítva egy API-módosítás alapján.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Az Azure Machine Learning SDK for Python v1.0.17

+ **Új funkciók**

  + Az Azure Machine Learning most már elterjedt DNN keretrendszer Chainer első osztályú támogatást nyújt. Használatával [ `Chainer` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) osztály felhasználók könnyedén betanítása és Chainer modellek üzembe helyezése.
    + Ismerje meg, hogyan [ChainerMN elosztott betanítási Futtatás](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Ismerje meg, hogyan [hiperparaméter finomhangolása a Chainer HyperDrive használatával futtassa](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Az Azure Machine Learning-folyamatokat Folyamatfuttatás adattárolója módosítások alapján lehetővé teszi az eseményindító hozzáadása. A folyamat [ütemezés notebook](https://aka.ms/pl-schedule) frissül, és ez a funkció bemutatására.

+ **Hibajavítások és kapcsolatos fejlesztések**
  + Hozzáadtuk a támogatást az Azure Machine Learning-folyamatokat a source_directory_data_store tulajdonság egy kívánt adattár (például egy blob storage) állítja a [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) , amely megadott a [ PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Alapértelmezés szerint az lépéseket az Azure fájltároló használjuk a biztonsági adattár, amely megadtuk szabályozási problémák lépések nagy számú párhuzamosan végrehajtásakor.

### <a name="azure-portal"></a>Azure Portal

+ **Új funkciók**
  + Új fogd és vidd tábla szerkesztő élmény a jelentések. Felhasználók oszlop húzhat a tábla területre, ahol a táblázat előnézetét megjelenik a jól. Az oszlopok alakítható át.
  + Új naplókat megjelenítő
  + Hivatkozások való kísérletezéshez fut, számítási, modellek, lemezképek és a tevékenységek lapról központi telepítések

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.15

+ **Új funkciók**
  + Adat-előkészítési mostantól támogatja a írása egy adatfolyam érkező adatfolyamok fájlt. Emellett a fájlnevek stream hozhat létre új fájlnevek módosítására.
    + Útmutató: [Működik az fájl Streamek notebook](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Hibajavítások és kapcsolatos fejlesztések**
  + A nagy méretű adatkészleteket a t-kivonatoló javított teljesítménye.
  + Data Prep mostantól támogatja a DataPath olvasási adatait.
  + Most már a logikai és a numerikus oszlopok egy gyakori kódolási működik.
  + Más egyéb hibajavításokat tartalmaz.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Az Azure Machine Learning SDK for Python v1.0.15

+ **Új funkciók**
  + Az Azure Machine Learning-folyamatokat hozzáadott AzureBatchStep ([notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (notebook), és az időpont alapú ütemezés funkcióit ([notebook](https://aka.ms/pl-schedule)).
  +  DataTranferStep frissítve, hogy működik az Azure SQL Server és az Azure database for postgresql-hez ([notebook](https://aka.ms/pl-data-trans)).

+ **Módosítások**
  + Elavult `PublishedPipeline.get_published_pipeline` érvénytelenítve `PublishedPipeline.get`.
  + Elavult `Schedule.get_schedule` érvénytelenítve `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.12

+ **Új funkciók**
  + Data Prep mostantól támogatja egy Azure SQL database adattárolója használatával olvasásakor.
 
+ **Módosítások**
  + Jobb bizonyos műveletek nagy mennyiségű adat a memória teljesítményét.
  + `read_pandas_dataframe()` Mostantól csak `temp_folder` adni.
  + A `name` tulajdonsága `ColumnProfile` lett használata elavult - `column_name` helyette.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Az Azure Machine Learning SDK for Python v1.0.10

+ **Módosítások**: 
  + Az Azure Machine Learning SDK már nem rendelkezik azure-cli csomag függőségként. Pontosabban az azure-cli-core és az azure-cli-profil függőségek azureml-core lettek távolítva. A felhasználót érintő változások a következők:
    + Ha "az login" végrehajtása, és ezután az azureml-sdk használatával, akkor az SDK-t az még egyszer a böngésző vagy eszköz kód napló teheti meg. "Az login" által létrehozott hitelesítő adatok munkalehetőségek nem használ.
    + Azure CLI-vel hitelesítést, például "az login"kifejezést, használja a _azureml.core.authentication.AzureCliAuthentication_ osztály. Az Azure CLI-hitelesítést, hajtsa végre _pip-telepítést az azure-cli_ az azureml-SDK-t telepítette a Python-környezetet.
    + Ha "az login" egyszerű szolgáltatás használatával az automation, azt javasoljuk, _azureml.core.authentication.ServicePrincipalAuthentication_ osztályt, azureml-sdk nem fogja használni az azure CLI által létrehozott hitelesítő adatok állapota. 

+ **Hibajavítások**: Ez a kiadás többnyire tartalmaz kisebb hibajavítások

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.8

+ **Hibajavítások**
  + Továbbfejlesztett első adatok profilok teljesítményét.
  + Hiba történt a jelentéskészítéssel kapcsolatos rögzített kisebb hibák.
  
### <a name="azure-portal-new-features"></a>Az Azure portal: új funkciók
+ Új áthúzása diagramkészítési élmény a jelentések. Felhasználók is húzza az oszlop vagy attribútum a jól a diagramterület, ahol a rendszer automatikusan kiválasztja a felhasználó az adatok típusa alapján a megfelelő diagramtípus. A felhasználók módosítsa a típusát, a többi alkalmazható vagy adjon hozzá további attribútumokat.

    Diagram típusokat támogatja:
    - Vonaldiagram
    - Hisztogram
    - Halmozott sáv diagram
    - Box-diagram
    - Pont
    - Buborék diagram
+ A portál dinamikusan hoz létre a kísérletek a jelentésekre. Amikor egy felhasználó beküld egy futtatásra annak kísérlet, egy jelentés automatikusan jön létre, a naplózott mérőszámok és gráfok különböző frissítési kísérletei összehasonlító engedélyezéséhez. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Az Azure Machine Learning SDK for Python v1.0.8

+ **Hibajavítások**: Ez a kiadás többnyire tartalmaz kisebb hibajavítások

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.7

+ **Új funkciók**
  + Adattároló fejlesztései (dokumentált [adattárolója útmutatóval-to-útmutató](https://aka.ms/aml-data-prep-datastore-nb))
    + Vertikális felskálázás az Azure-fájlmegosztás és ADLS-adattárainak írnak és onnan olvasnak új képessége.
    + Adattárolók használatakor Data Prep mostantól támogatja a egyszerű szolgáltatásnév hitelesítése interaktív hitelesítés helyett.
    + Támogatás hozzáadva a wasb és a wasbs URL-címeket.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.6

+ **Hibajavítások**
  + Nyilvános olvasható Azure Blob-tárolók Spark olvasásakor hiba javítva

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Az Azure Machine Learning SDK for Python v1.0.6
+ **Hibajavítások**: Ez a kiadás többnyire tartalmaz kisebb hibajavítások

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.4

+ **Új funkciók**
  + `to_bool` függvény mostantól lehetővé teszi, hogy a nem egyező értékek hibaértékek kell konvertálni. Ez lesz az új alapértelmezett eltérés viselkedés `to_bool` és `set_column_types`, mivel eltérő értékek konvertálása hamis volt az előző alapértelmezett viselkedést.
  + Hívásakor `to_pandas_dataframe`, egy új lehetőség a numerikus oszlopok null/hiányzó értékek értelmezése NaN.
  + Új képessége ellenőrzéséhez írja be a konzisztencia biztosítása, és sikertelen korai némely kifejezés visszatérési típusa.
  + Most már hívása `parse_json` elemezheti a JSON-objektumként egy oszlop értékét, és bontsa ki őket, több oszlopba.

+ **Hibajavítások**
  + Kijavítva a hiba, melyeken előfordult összeomlás `set_column_types` a Python 3.5.2-es verzióját.
  + Kijavítva a hiba, melyeken előfordult összeomlás, Datastore-AML-rendszerkép használatával való kapcsolódáskor.

+ **Frissítések**
  * [Példa notebookok](https://aka.ms/aml-data-prep-notebooks) az első lépések – oktatóanyagok, ügyféleset-tanulmányok és gyakorlati ismertetőket.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Általános rendelkezésre állás

Az Azure Machine Learning szolgáltatás már általánosan elérhető.

### <a name="azure-machine-learning-compute"></a>Az Azure Machine Learning Compute
Ebben a kiadásban bejelentjük, hogy egy új felügyelt számítási környezet révén a [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute). A számítási célnak az Azure Machine Learning az Azure Batch AI-számítási váltja fel. 

A számítási célnak:
+ Modell képzés és a batch következtetésekhez/pontozási használatos
+ Van egy - a több - node számítási
+ A fürt felügyeleti does és a felhasználó feladatütemezésre
+ Alapértelmezés szerint automatikusan Leskálázódik
+ Processzor-és GPU támogatása 
+ Alacsonyabb költségek mellett lehetővé teszi, hogy alacsony prioritású virtuális gépek használata

Az Azure Machine Learning Compute a Python, az Azure portal vagy a parancssori felület használatával hozható létre. A munkaterület a régióban kell létrehozni, és bármilyen egyéb munkaterülethez nem csatolható. A számítási célnak egy Docker-tárolót használ a futtatáskor, és a függőségek ugyanabban a környezetben replikálni az összes csomópont-csomagot.

> [!Warning]
> Azt javasoljuk, hogy használata az Azure Machine Learning Compute új munkaterület létrehozása. Nincs távoli előfordulhat, hogy a felhasználók próbál létrehozni egy meglévő munkaterületet az Azure Machine Learning COMPUTE számítási hiba láthatják. A munkaterületen található meglévő számítási továbbra is változatlanul működik.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Az Azure Machine Learning SDK for Python v1.0.2
+ **Használhatatlanná tévő változásai**
  + Ettől a kiadástól kezdve megszüntetjük a virtuális gép létrehozása az Azure Machine Learning támogatása. Továbbra is csatolhat a virtuális gép meglévő felhő vagy egy távoli helyszíni kiszolgáló. 
  + Emellett megszüntetjük BatchAI, mindegyike támogatnia kell az Azure Machine Learning Compute révén most támogatása.

+ **Új**
  + A machine learning-folyamatokat:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Frissítve**
  + A machine learning-folyamatokat:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) már elfogad runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) most másolja és a egy SQL-adatforrás
    + Az SDK-t létrehozni és frissíteni a közzétett folyamatok futó ütemezések funkció ütemezéséhez

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Az Azure Machine Learning adat-előkészítési SDK v0.5.2
+ **Használhatatlanná tévő változásai** 
  * `SummaryFunction.N` új nevet kapott `SummaryFunction.Count`.
  
+ **Hibajavítások**
  * Legújabb AML futtatása jogkivonat használata a távoli Futtatás adattárainak való írásakor vagy olvasásakor a. Korábban az AML futtatása Token frissül a Python, ha az Adatelőkészítés-modul nem frissül a frissített AML futtatása jogkivonat a.
  * További világosabb hibaüzenetek
  * to_spark_dataframe() már nem összeomlik, ha Spark használ `Kryo` szerializáció
  * Értékek száma vizsgáló most már 1000-nél több egyedi értékeket jeleníti meg
  * Véletlenszerű Split már nem sikertelen lesz, ha az eredeti adatfolyam nem rendelkezik egy nevet  

+ **További információ**
  * [Azure Machine Learning adat-előkészítési SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Docs és jegyzetfüzetek
+ Gépi Tanulási folyamatok
  + Ismerkedés a folyamatok, kötegelt hatókörének és stílus az új és frissített notebookok példák át: https://aka.ms/aml-pipeline-notebooks
  + Ismerje meg, hogyan [az első folyamat létrehozása](how-to-create-your-first-pipeline.md)
  + Ismerje meg, hogyan [batch előrejelzéseket folyamatok használatával futtassa](how-to-run-batch-predictions.md)
+ Az Azure Machine Learning számítási célnak
  + [Notebookok minta](https://aka.ms/aml-notebooks) most frissülnek, hogy az új felügyelt számítási használja.
  + [Ismerje meg a számítási](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Az Azure portal: új funkciók
+ Létrehozásához és kezeléséhez [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) típusok a portálon.
+ Fiókkvóta-használat figyeléséhez és [kérelmi kvótát](how-to-manage-quotas.md) az Azure Machine Learning Compute.
+ Az Azure Machine Learning COMPUTE számítási fürt állapotának megtekintése valós időben.
+ Virtuálishálózat-támogatást az Azure Machine Learning COMPUTE számítási és az Azure Kubernetes Service létrehozásakor lett hozzáadva.
+ Futtassa újra a közzétett folyamatok meglévő paraméterekkel.
+ Új [machine learning diagramok automatikus](how-to-track-experiments.md#auto) képbesorolási modellek (lift, nyereség, hitelesítési, funkció fontosság diagramot modell explainability) és regressziós modellek (például és a szolgáltatás fontosság diagram modellel explainability). 
+ A folyamatok tekinthetnek meg az Azure Portalon




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Az Azure Machine Learning SDK for Python v0.1.80

+ **Használhatatlanná tévő változásai** 
  * *azureml.train.widgets* névtér át lett helyezve *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* a következő osztályok - helyteleníti *azureml.core.compute.BatchAICompute* és *azureml.core.compute.DSVMCompute*. Az utóbbi osztály törlődni fog a következő kiadásokban. A AmlCompute osztály mostantól könnyebben definícióval rendelkezik egyszerűen egy vm_size és a max_nodes van szüksége, és automatikus méretezése a max_nodes 0-tól a fürthöz, amikor a rendszer elküld egy feladatot. A [notebookok minta](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) frissítve lett-e ezzel az információval, és adjon meg használati példák. Reméljük, mint például az egyszerűsítés és a egy későbbi kiadásban lesz több izgalmas funkciók számos!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Az Azure Machine Learning adat-előkészítési SDK v0.5.1 

További információ a Data Prep SDK olvasásával [docs hivatkozhat](https://aka.ms/data-prep-sdk).
+ **Új funkciók**
   * Létrehozott egy új Adatelőkészítés CLI hajtsa végre az Adatelőkészítés-csomagok és az adatkészlet vagy adatfolyam adatok profiljának megtekintése
   * Újratervezett SetColumnType API használhatóság javítása érdekében
   * Átnevezett smart_read_file auto_read_file
   * Mostantól tartalmazza a Adatprofil döntés és értékek
   * A rétegzett mintavételi is minta
   * CSV-fájlokat tartalmazó zip-fájlokat is olvashat
   * Is felosztása row-wise véletlenszerű Split-adatkészletek (például tesztelési – train-készlet)
   * Az összes oszlop adattípusok vagy érheti el egy adatfolyam-adatprofil meghívásával `.dtypes`
   * A sorok számát vagy érheti el egy adatfolyam-adatprofil meghívásával `.row_count`

+ **Hibajavítások**
   * Mennyi ideig dupla átalakítás rögzített 
   * Rögzített vyhodnocení után bármely oszlop hozzáadása 
   * FuzzyGrouping, ahol azt szeretné nem csoportok egyes esetekben képes észlelni kijavítva
   * Több oszlopot rendezési sorrend figyelembe veszi a rögzített rendezési funkció
   * Rögzített, illetve hogyan hasonlónak kell lennie a kifejezések `pandas` kezeli őket
   * Rögzített olvasásakor dbfs elérési útja
   * Hibaüzenetek a könnyebb érthetőség érdekében 
   * Most már nem akkor, ha az AML-jogkivonat használatával távoli számítási cél olvasó
   * Most már nem meghiúsul, a Linux-dsvm-hez
   * Most már nem összeomlik, ha nem sztring típusú értékek a következők az olyan predikátumokban karakterlánc
   * Most már kezeli a helyességi feltétel hibákat, amikor adatfolyamot megfelelően kell-e sikertelen
   * Most már támogatja az Azure databricks szolgáltatásban dbutils csatlakoztatott tárolási helyek

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal 
Az Azure Portalon az Azure Machine Learning szolgáltatás rendelkezik a következő frissítéseket:
  * Egy új **folyamatok** lapján közzétett folyamatokhoz.
  * Támogatás hozzáadva a csatolása egy meglévő HDInsight-fürt egy számítási célnak.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Az Azure Machine Learning SDK for Python v0.1.74

+ **Használhatatlanná tévő változásai** 
  * \* Workspace.compute_targets, adattárolók, kísérletek, képek, modellek és *problémák megoldásához segítséget* tulajdonságok módszer helyett. Helyettesítse be például *Workspace.compute_targets()* a *Workspace.compute_targets*.
  * *Run.get_context* helyteleníti *Run.get_submitted_run*. Az utóbbi módszer a következő kiadásokban törlődni fog.
  * *PipelineData* osztály most vár egy adattár-objektumot datastore_name egy paraméter helyett. Ehhez hasonlóan *folyamat* default_datastore_name helyett default_datastore fogad el.

+ **Új funkciók**
  * Az Azure Machine Learning-folyamatokat [minta notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) MPI lépéseket használja.
  * A Jupyter-notebookokhoz RunDetails widget megjelenítéséhez a vizualizációt, a folyamat frissül.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Az Azure Machine Learning adat-előkészítési SDK v0.4.0 
 
+ **Új funkciók**
  * Adatprofil hozzáadott száma 
  * Értékek száma és a hisztogram már elérhető
  * További. percentilisei az Adatprofil
  * A középérték érhető el összegzés
  * Mostantól támogatott Python 3.7.
  * Amikor menti egy adatfolyam, amely tartalmazza az adattárolók Adatelőkészítés csomaghoz, az adattár-információk maradnak az Adatelőkészítés-csomag részeként
  * Mostantól támogatott az adattárhoz való írás 
        
+ **Hiba kijavítva**
  * 64 bites, előjel nélküli egész túlcsordul most kezeli megfelelően Linux rendszeren
  * Egyszerű szöveges fájlok smart_read rögzített helytelen felirat
  * Karakterlánc oszloptípus most már megjelenik-e metrikák megtekintése
  * Száma már rögzített egyesével helyett egyetlen FieldType leképezve ValueKinds megjelenítése
  * Write_to_csv már nem sikertelen lesz, amikor az elérési út egy karakterláncként van megadva.
  * Cserélje le használatakor "keresése" üresen hagyja már nem meghiúsul 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Az Azure Machine Learning SDK for Python v0.1.68

+ **Új funkciók**
  * Több-bérlős támogatással új munkaterület létrehozása során.

+ **Javított hibák**
  * Már nincs szüksége a pynacl könyvtár verzió rögzítése a webszolgáltatás üzembe helyezésekor.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Az Azure Machine Learning adat-előkészítési SDK v0.3.0

+ **Új funkciók**
  * Hozzáadott metódus transform_partition_with_file(script_path), amely lehetővé teszi a felhasználók számára, hogy az elérési útját egy Python-fájlt, végrehajtására

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Az Azure Machine Learning SDK for Python v0.1.65
[Verzió 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) új funkciók, további, hibajavításokat tartalmaz, és tartalmaz további [notebookok minta](https://aka.ms/aml-notebooks).

Lásd: [kapcsolatos ismert problémák listája](resource-known-issues.md) ismert hibák és a lehetséges megoldások megismeréséhez.

+ **Használhatatlanná tévő változásai**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services visszatérési szótár, korábban visszaadott listában. Lásd: [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API dokumentációja.

  * Automatizált Machine Learning Normalizált négyzetes hiba eltávolítja az elsődleges metrikákat.

+ **HyperDrive**
  * Különböző HyperDrive hibajavítások a Bayes, a javított teljesítménye, metrikák hívások beolvasása. 
  * 1\.9 Tensorflow 1.10 verzióról 
  * Docker-rendszerkép optimalizálást a hidegindítás. 
  * Feladatok megfelelő állapota most jelentést, még akkor is, ha azok kilépési hibakód: 0 kivételével. 
  * RunConfig attribútum érvényesítése, az SDK-t. 
  * Futtatás HyperDrive objektum Futtatás szabályos hasonló Mégse támogatja: nincs szükség adja át a paramétereket. 
  * Widget fejlesztései legördülő értékeit az elosztott futtatási és HyperDrive futtatások állapotának fenntartásához. 
  * Paraméterkiszolgáló TensorBoard és a többi napló-fájloknak támogatniuk rögzíteni. 
  * Intel(R) MPI szolgáltatás oldalán támogatja. 
  * Bugfix a finomhangolással az elosztott futtatási javítás BatchAI az érvényesítés során. 
  * Környezet Manager mostantól azonosítja az elsődleges példány. 

+ **Azure portal felületének**
  * Futtatás részletei log_table() és log_row() támogatottak. 
  * 1, 2 vagy 3 numerikus oszlopok és a egy nem kötelező kategorikus oszlop hozza létre automatikusan a táblák és sorok diagramjait.

+ **Automatizált Machine Learning**
  * Továbbfejlesztett hiba- és dokumentáció 
  * Rögzített futtatási tulajdonság lekérése teljesítménybeli problémák. 
  * Rögzített futtatási a probléma továbbra is. 
  * Rögzített ensembling iteráció problémákat.
  * Rögzített méretű betanítási módosítási hiba a MAC OS.
  * Egyszerűsítés makró átlagos Pull-kérelem/ROC-görbe egyéni ellenőrzési forgatókönyvben.
  * Eltávolítja a felesleges index logikát.
  * Szűrő távolítva get_output API-t.

+ **Folyamatok**
  * A metódus egy folyamatot, közvetlenül közzé anélkül, hogy az első futtatás végrehajtási Pipeline.publish() hozzá.   
  * A folyamatfuttatások egy közzétett folyamatot létrehozott beolvasni PipelineRun.get_pipeline_runs() metódus hozzá.

+ **Project Brainwave**
  * FPGA-kban elérhető új AI modelleket frissített támogatása.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Az Azure Machine Learning adat-előkészítési SDK v0.2.0
[Verzió 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) alábbi funkcióit és hibajavításait tartalmazza:

+ **Új funkciók**
  * Egy gyakori kódolási támogatása
  * Ki osztóérték átalakító támogatása
   
+ **Hiba kijavítva:**
  * Bármely tornádó verziójával működik nincs szükség alacsonyabb szolgáltatásszintre váltásához az tornádó verzió
  * Értékek száma az összes értéket, nem csak az első három

## <a name="2018-09-public-preview-refresh"></a>2018-09 (nyilvános előzetes verzió frissítése)

Egy új, frissített verzióját az Azure Machine Learning: További információ az ebben a kiadásban: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>További lépések

Olvassa el a áttekintése [Azure Machine Learning szolgáltatás](../service/overview-what-is-azure-ml.md).
