---
title: A kiadás újdonságai
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg a Azure Machine Learning legújabb frissítéseivel, valamint a gépi tanulással és az adat-előkészítéssel kapcsolatos Python SDK-val.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: 40ea1bfe65c963dbbe78c8aca8f5e9e96fd986cb
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82928890"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning kibocsátási megjegyzések

Ebben a cikkben megismerheti Azure Machine Learning kiadásait.  A teljes SDK-hivatkozási tartalomért keresse fel a Azure Machine Learning [**fő SDK for Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -referenciát tartalmazó oldalt.

Az ismert hibák és a megkerülő megoldások megismeréséhez tekintse meg [az ismert problémák listáját](resource-known-issues.md) .

## <a name="2020-05-04"></a>2020-05-04
**Új jegyzetfüzet-élmény**

Mostantól közvetlenül a Azure Machine Learning Studio webes felületén belül hozhat létre, szerkeszthet és oszthat meg gépi tanulási jegyzetfüzeteket és fájlokat. A [Azure Machine learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -ban elérhető összes osztályt és módszert felhasználhatja ezekből a jegyzetfüzetekről az [első lépések](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks) elkezdéséhez

**Új szolgáltatások bevezetése:**

+ A VS Code által használt továbbfejlesztett szerkesztő (Monaco Editor) 
+ Felhasználói felület/UX-fejlesztés
+ Cella eszköztár
+ Új jegyzetfüzet eszköztár és számítási vezérlők
+ Jegyzetfüzet állapotsora 
+ Beágyazott kernel-váltás
+ R-támogatás
+ A kisegítő lehetőségek és a honosítás fejlesztése
+ Parancs paletta
+ További billentyűparancsok
+ Autosave
+ Jobb teljesítmény és megbízhatóság

A Studio alkalmazásban a következő webalapú szerzői eszközöket érheti el:
    
| Webalapú eszköz  |     Leírás  | Kiadás | 
|---|---|---|
| Azure ML Studio notebookok   |     A notebook-fájlok első, osztályon belüli létrehozása és az Azure ML Python SDK-ban elérhető összes művelet támogatása. | Alapszintű & Enterprise  |   

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK a Python v 1.4.0

+ **Új funkciók**
  + A AmlCompute-fürtök mostantól támogatják a felügyelt identitás beállítását a fürtön a kiépítés időpontjában. Csak adja meg, hogy a rendszer által hozzárendelt identitást vagy felhasználó által hozzárendelt identitást szeretne-e használni, és az utóbbi esetében egy Identitásazonosítója ad át. Ezután beállíthatja, hogy a permissiosn a különböző erőforrásokhoz, például a Storage-hoz vagy az ACR-hez férjen hozzá úgy, hogy a számítási adatok biztonságosan hozzáférhessenek az adatokhoz, nem pedig a AmlCompute által használt jogkivonat-alapú megközelítés helyett. A paraméterekkel kapcsolatos további információkért tekintse meg az SDK-referenciát.
  

+ **Kompatibilitástörő változások**
  + A AmlCompute-fürtök a futtatáson alapuló létrehozást támogató előzetes szolgáltatást támogatnak, amely két hét alatt elavulttá válik. A Amlcompute osztály használatával továbbra is állandó számítási célokat hozhat létre, de a "Amlcompute" azonosító megadásának konkrét megközelítését a közeljövőben nem fogja támogatni a rendszer a futtatási konfigurációban. 

+ **Hibajavítások és javítások**
  + **azureml-automl-runtime**
    + A nem kivonatoló típus támogatásának engedélyezése az egyes oszlopokban lévő egyedi értékek számának kiszámításakor.
  + **azureml-core**
    + Jobb stabilitás az Azure-Blob Storage TabularDataset használatával történő olvasásakor.
    + Továbbfejlesztett dokumentáció a `grant_workspace_msi` paraméterhez `Datastore.register_azure_blob_store`.
    + Kijavítva a `datastore.upload` hiba, ha `src_dir` az argumentum a `/` vagy `\`a értékkel végződött, a feltöltés sikertelen lesz.
    + A rendszer felveszi a művelettel kapcsolatos hibaüzenetet, amikor olyan Azure Blob Storage adattárba próbál feltölteni, amely nem rendelkezik hozzáférési kulccsal vagy SAS-jogkivonattal.
  + **azureml – értelmezés**
    + A rendszer felső korlátot adott hozzá a fájl méretéhez a feltöltött magyarázatokon a vizualizációs adathoz.
  + **azureml-train-automl-client**
    + A label_column_name & weight_column_name paramétereinek explicit ellenőrzése a AutoMLConfig karakterlánc típusúra.
  + **azureml – egytörzsű folyamatok – lépések**
    + A ParallelRunStep mostantól támogatja az adatkészletet a pipeline paraméterként. A felhasználó létrehozhatja a folyamatokat a minta adatkészlettel, és az új folyamat futtatásához megváltoztathatja ugyanazt a típusú (fájl vagy táblázatos) bemeneti adatkészletet.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK a Python v 1.3.0

+ **Hibajavítások és javítások**
  + **azureml-automl-core**
    + További telemetria bővült a betanítás utáni műveletek köré.
    + Felgyorsítja az automatikus ARIMA képzést a 100-nál nagyobb hosszúságú négyzetek (CSS) betanításának feltételes összegével. Vegye figyelembe, hogy a használt hossz állandó ARIMA_TRIGGER_CSS_TRAINING_LENGTH w/a TimeSeriesInternal osztályban, a következő helyen:/src/azureml-automl-Core/azureml/automl/Core/Shared/constants.py
    + Az előrejelzési futtatások felhasználói naplózása javult, és most már több információ jelenik meg arról, hogy milyen fázis fut a naplóban.
    + A nem engedélyezett target_rolling_window_size értéke kisebb, mint 2
  + **azureml-automl-runtime**
    + Javítottuk a duplikált időbélyegek megtalálása során megjelenő hibaüzenetet.
    + A nem engedélyezett target_rolling_window_size értéke kisebb, mint 2.
    + Kijavítva a késés imputálási hibáját. A probléma oka az, hogy nem áll rendelkezésre elegendő számú észrevétel a sorozat szezonális felállításához. A "de-idényjellegű" adatok egy részleges autokorrelációs funkció (PACF) kiszámítására szolgálnak a késés hosszának megállapításához.
    + Engedélyezve van az oszlop céljának featurization testreszabása az előrejelzési feladatokhoz a featurization config alapján. Az előrejelzési feladatokhoz a numerikus és a kategorikus, az oszlop célja már támogatott.
    + A drop Column featurization testreszabása az előrejelzési feladatokhoz featurization-konfiguráció alapján.
    + Engedélyezve van a imputálási testreszabása az előrejelzési feladatokhoz a featurization config alapján. Mostantól támogatott a imputálási és a középérték, a medián, a most_frequent és az állandó érték imputálási.
  + **azureml-contrib-pipeline-steps**
    + A ParallelRunConfig átadandó karakterlánc-számítási nevek elfogadása
  + **azureml-core**
    +  A Environment. Clone (new_name) API hozzáadása a környezeti objektum másolatának létrehozásához
    +  A Environment. Docker. base_dockerfile elfogadja a filepath. Ha képes egy fájl feloldására, a rendszer beolvassa a tartalmat base_dockerfile Environment tulajdonságba
    + A base_image és base_dockerfile kölcsönösen kizáró értékének automatikus visszaállítása, ha a felhasználó manuálisan állít be egy értéket a környezetben. Docker
    + User_managed jelzőt adott hozzá a RSection-ben, amely azt jelzi, hogy a környezetet a felhasználó vagy a AzureML kezeli-e.
    + Adatkészlet: a rögzített adatkészlet letöltése sikertelen, ha az adatelérési út Unicode-karaktereket tartalmaz.
    + Adatkészlet: továbbfejlesztett adatkészlet-csatlakoztatási gyorsítótárazási mechanizmus, amely figyelembe veszi a minimális lemezterület-követelményt Azure Machine Learning számításban, ami megakadályozza a csomópont használhatatlanná tételét, és a feladat megszakítását okozza.
    + Adatkészlet: hozzáadunk egy indexet a idősor oszlophoz, amikor a idősor-adatkészletet Panda-dataframes éri el, amely a idősor-alapú adathozzáféréshez való hozzáférés felgyorsítására szolgál.  Korábban az index ugyanazt a nevet kapta, mint az időbélyeg oszlop, amely zavaros felhasználókat mutat, amely a tényleges timestamp oszlop, és amely az index. Most nem adunk meg konkrét nevet az indexnek, mert nem használható oszlopként. 
    + Adatkészlet: rögzített adatkészlet-hitelesítési probléma a szuverén felhőben.
    + Adatkészlet: az `Dataset.to_spark_dataframe` Azure PostgreSQL-adattárokból létrehozott adatkészletek rögzített hibája.
  + **azureml-interpret**
    + Globális pontszámok hozzáadása a vizualizációhoz, ha a helyi fontossági értékek ritkák
    + Frissített azureml – a következő értelmezési értelmezést használja: Community 0,9. *
    + Kijavítva a probléma a ritka kiértékelési adattal rendelkező magyarázat letöltésével
    + A magyarázat objektum ritka formátumának támogatása a AutoML-ben
  + **azureml-pipeline-core**
    + A ComputeInstance támogatása a folyamatok számítási céljaként
  + **azureml-train-automl-client**
    + További telemetria bővült a betanítás utáni műveletek köré.
    + A regresszió kijavítása a korai leállítás során
    + Az elavult azureml. dprep. adatfolyam érvényes típusú a bemeneti adatokhoz.
    +  Az alapértelmezett AutoML-kísérlet időkorlátja 6 napra módosul.
  + **azureml-train-automl-runtime**
    + További telemetria bővült a betanítás utáni műveletek köré.
    + ritka automl E2E-támogatás hozzáadva
  + **azureml-opendatasets**
    + További telemetria hozzáadva a szolgáltatás-figyelőhöz.
    + A frontdoor engedélyezése a blob számára a stabilitás növeléséhez 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK a Python v 1.2.0

+ **Kompatibilitástörő változások**
  + A Python 2,7 támogatásának eldobása

+ **Hibajavítások és javítások**
  + **Azure-CLI-ml**
    + Hozzáadja az "--előfizetés-azonosító" `az ml model/computetarget/service` parancsot a parancssori felület parancsaihoz
    + Ügyfél által felügyelt kulcs (CMK) vault_url, key_name és key_version bevezetésének támogatása az ACI üzembe helyezéséhez
  + **azureml-automl-core** 
    + Engedélyezett, testreszabott imputálási az X és y adatelőrejelzési feladatokhoz is állandó értékkel.
    + Kijavítottuk a hibával kapcsolatos hibaüzeneteket a felhasználó számára.    
  + **azureml-automl-runtime**
    + Rögzítette az előrejelzéssel kapcsolatos problémát az adathalmazokon, amelyek csak egy sorral rendelkező gabonákat tartalmaznak
    + Csökkenthető az előrejelzési feladatokhoz szükséges memória mennyisége.
    + Jobb hibaüzenetek lettek hozzáadva, ha a Time oszlop formátuma helytelen.
    + Engedélyezett, testreszabott imputálási az X és y adatelőrejelzési feladatokhoz is állandó értékkel.
  + **azureml-core**
    + A ServicePrincipal betöltésének támogatása a környezeti változóktól: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID és AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Új paramétert `support_multi_line` vezetett be `Dataset.Tabular.from_delimited_files`a következőre:`support_multi_line=False`alapértelmezés szerint (), az összes sortörést, beleértve az idézőjelben lévő mezőértékeket is, a rendszer a rekordok tördelését fogja értelmezni. Az adatok ily módon történő olvasása gyorsabban és jobbá teszi a párhuzamos végrehajtáshoz több CPU-magot. Azonban előfordulhat, hogy a további rekordokat a helytelenül igazított mezőértékek miatt csendesen is létrehozta. Ezt akkor kell beállítani, `True` ha a tagolt fájlok idézőjelek közé vannak állítva.
    + A ADLS Gen2 regisztrációjának lehetősége a Azure Machine Learning parancssori felületén
    + Átnevezte a (z) "fine_grain_timestamp" paramétert a "Timestamp" értékre, a "coarse_grain_timestamp" paramétert pedig "partition_timestamp" értékre a TabularDataset with_timestamp_columns () metódusához, hogy jobban tükrözze a paraméterek használatát.
    + A kísérletek maximális hossza a 255 értékre nő.
  + **azureml – értelmezés**
    + Frissítve azureml – értelmezés a Közösség 0,7. *
  + **azureml – SDK**
    + A kompatibilitási és a kompatibilis verziókkal való váltás a javítások támogatásához a kiadás előtti és a stabil kiadásokban.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK a Python v 1.1.5

+ **Szolgáltatás elavulása**
  + **Python 2,7**
    + A Python 2,7 támogatásának utolsó verziója

+ **Kompatibilitástörő változások**
  + **Szemantikai verziószámozás 2.0.0**
    + Az 1,1-es verziótól kezdődően az Azure ML Python SDK a szemantikai verziószámozási 2.0.0 alkalmazza. [További tudnivalók itt](https://semver.org/). Minden további verzió új számozási sémát és szemantikai verziószámozási szerződést fog követni. 

+ **Hibajavítások és javítások**
  + **Azure-CLI-ml**
    + Módosítsa a végpont CLI-parancsának nevét az "az ml Endpoint AK" értékről az "az ml Endpoint Realtime" értékre a konzisztencia érdekében.
    + a CLI-telepítési utasítások frissítése a stabil és a kísérleti ág parancssori felületéhez
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
  + **azureml-automl-core**
    + Engedélyezve van a Batch Mode következtetés (több sor többszöri elkészítése) a automl ONNX-modellekhez
    + Javult az adathalmazok gyakoriságának észlelése, az adat hiánya vagy a szabálytalan adatpontokat tartalmazó információk
    + Hozzáadta az olyan adatpontok eltávolításának lehetőségét, amelyek nem felelnek meg a domináns gyakoriságnak.
    + Módosította a konstruktor bemenetét, hogy a megfelelő oszlopok imputálási beállításait alkalmazza.
    + A hiba naplózása javult.
  + **azureml-automl-runtime**
    + Kijavítottuk a hibát, amely akkor fordul elő, ha a betanítási készletben nem szereplő gabona szerepel a tesztelési készletben.
    + Az előrejelzési szolgáltatásra vonatkozó pontozás során eltávolította a y_query követelményt
    + Kijavítottuk az előrejelzéssel kapcsolatos problémát, ha az adathalmaz rövid, hosszú idejű hézagokat tartalmazó gabonát tartalmaz.
    + Kijavítottuk a problémát, ha az automatikus maximális horizont be van kapcsolva, és a Date oszlop karakterláncok formájában tartalmaz dátumokat. A rendszer a megfelelő átalakítást és hibaüzeneteket adta hozzá, amikor a dátumra váltás nem lehetséges
    + Natív NumPy és SciPy használata a FileCacheStore köztes adatközpont szerializálásához és deszerializálásához (a helyi AutoML futtatásához használatos)
    + Kijavítva a hiba, ahol a gyermek futása meghiúsult a futó állapotban.
    + Nagyobb sebesség a featurization.
    + A gyakoriság ellenőrzésének kijavítása a pontozás során – mostantól az előrejelzési feladatok nem igénylik a vonat és a tesztelési készlet szigorú gyakorisági egyenértékűségét.
    + Módosította a konstruktor bemenetét, hogy a megfelelő oszlopok imputálási beállításait alkalmazza.
    + A lag típusának kiválasztásával kapcsolatos rögzített hibák.
    + Az adatkészleteken kiváltott, nem besorolt hiba kijavítva, az egyetlen sorral rendelkező gabonákkal
    + Javítva lett a probléma a gyakoriság észlelésének lassúságával.
    + Kijavít egy hibát a AutoML kivételek kezelésére vonatkozóan, ami azt eredményezte, hogy a AttributeError nem helyettesíthetik a betanítási hibák valós okát.
  + **azureml – CLI – gyakori**
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
  + **azureml – megterhelés – Mir**
    + Funkciók beolvasása a MirWebservice osztályban a hozzáférési jogkivonat lekéréséhez
    + A MirWebservice jogkivonat-hitelesítés használata alapértelmezés szerint a MirWebservice. Run () hívása esetén, ha a hívás sikertelen
    + A Mir webszolgáltatások üzembe helyezéséhez a [Ds2v2, a A2v2 és a F16] helyett megfelelő SKU-ket [Standard_DS2_v2, Standard_F16, Standard_A2_v2] szükséges.
  + **azureml-contrib-pipeline-steps**
    + A nem kötelező paraméter side_inputs hozzáadva a ParallelRunStep-hez. Ezzel a paraméterrel lehet csatlakoztatni a mappát a tárolón. A jelenleg támogatott típusok a következők: DataReference és PipelineData.
    + A ParallelRunConfig átadott paraméterek felülírása a folyamat paramétereinek átadásával lehetséges. Az új folyamat paraméterei támogatottak aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count és aml_process_count_per_node már a korábbi kiadás részét képezik).
  + **azureml-core**
    + A AzureML webszolgáltatások üzembe helyezése mostantól `INFO` alapértelmezés szerint a naplózást végzi. Ezt a `AZUREML_LOG_LEVEL` környezeti változónak a központilag telepített szolgáltatásban való beállításával lehet szabályozni.
    + A Python SDK a felderítési szolgáltatás használatával "API" végpontot használ a "folyamatok" helyett.
    + Cserélje le az új útvonalakra az összes SDK-hívásban.
    + Módosult a ModelManagementService irányuló hívások útválasztása egy új egységesített struktúrára.
      + A munkaterület frissítési metódusa nyilvánosan elérhető.
      + Image_build_compute paraméter hozzáadva a munkaterület-frissítési metódusban, amely lehetővé teszi a felhasználó számára a rendszerkép-létrehozási számítások frissítését.
    + Elavult üzeneteket adott hozzá a régi profilkészítési munkafolyamathoz. Rögzített profilkészítési CPU-és memória-korlátok.
    + A környezet részeként hozzáadott RSection az R-feladatok futtatásához.
    + A rendszer az `Dataset.mount` ellenőrzéshez hozzáadta a hibát, ha az adatkészlet forrása nem érhető el, vagy nem tartalmaz adatokat.
    + Hozzáadva `--grant-workspace-msi-access` további paraméterként az adattár CLI-hez az Azure Blob-tároló regisztrálásához, amely lehetővé teszi a VNet mögötti blob-tároló regisztrálását.
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
    + Kijavítva a problémát a aks.py _deploy.
    + Ellenőrzi a feltöltés alatt álló modellek integritását a csendes tárolási hibák elkerülése érdekében.
    + A felhasználók mostantól meghatározhatnak egy értéket az Auth kulcshoz a webszolgáltatások kulcsainak újragenerálása során.
    + Kijavítva a hiba, ahol a nagybetűk nem használhatók adatkészlet bemeneti neveként.
  + **azureml – alapértékek**
    + `azureml-dataprep`Mostantól a `azureml-defaults`részeként települ. Az adatkészletek csatlakoztatásához már nem szükséges a adatelőkészítés [Fuse] manuális telepítése a számítási célokon.
  + **azureml – értelmezés**
    + Frissítve azureml – értelmezés a Közösség 0,6. *
    + Frissített azureml – az értelmezéstől függ a közösségi 0.5.0
    + Azureml-stílusú kivételek hozzáadva a azureml-értelmezéshez
    + Rögzített DeepScoringExplainer szerializálás kerasz-modellekhez
  + **azureml-mlflow**
    + Adja hozzá a szuverén felhők támogatását a azureml. mlflow
  + **azureml-pipeline-core**
    + A pipeline batch pontozási jegyzetfüzet mostantól a ParallelRunStep-t használja
    + Kijavítva egy hiba, ahol a PythonScriptStep-eredmények helytelenül újra felhasználhatók az argumentumok listájának módosításakor
    + Az oszlopok típusának beállítása a parse_ * metódusok meghívásakor`PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Áthelyezte `AutoMLStep` a `azureml-pipeline-steps` csomagot a csomagba. A `AutoMLStep` belül `azureml-train-automl-runtime`elavult.
    + Dokumentáció hozzáadva példa az adatkészlethez PythonScriptStep-bemenetként
  + **azureml-tensorboard**
    + Frissítve azureml-tensorboard a tensorflow 2,0 támogatásához
    + Helyes portszám megjelenítése, ha egy számítási példányon egyéni Tensorboard-portot használ
  + **azureml-train-automl-client**
    + Kijavított egy hibát, amelyben bizonyos csomagok a távoli futtatások helytelen verzióiban telepíthetők.
    + rögzített FeaturizationConfig-felülbírálási probléma, amely az egyéni featurization-konfigurációt szűri.
  + **azureml-train-automl-runtime**
    + Kijavítottuk a gyakoriság észlelésével kapcsolatos problémát a távoli futtatásokban.
    + `AutoMLStep` Áthelyezte a `azureml-pipeline-steps` csomagot a csomagba. A `AutoMLStep` belül `azureml-train-automl-runtime`elavult.
  + **azureml-train-core**
    + A PyTorch 1,4-es verziójának támogatása a PyTorch-Kalkulátorban
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.2 RC0 (előzetes verzió)

+ **Hibajavítások és javítások**
  + **azureml-automl-core**
    + Engedélyezve van a Batch Mode következtetés (több sor többszöri elkészítése) a automl ONNX-modellekhez
    + Javult az adathalmazok gyakoriságának észlelése, az adat hiánya vagy a szabálytalan adatpontokat tartalmazó információk
    + Hozzáadta az olyan adatpontok eltávolításának lehetőségét, amelyek nem felelnek meg a domináns frequrncy.
  + **azureml-automl-runtime**
    + Kijavítottuk a hibát, amely akkor fordul elő, ha a betanítási készletben nem szereplő gabona szerepel a tesztelési készletben.
    + Az előrejelzési szolgáltatásra vonatkozó pontozás során eltávolította a y_query követelményt
  + **azureml – megterhelés – Mir**
    + Funkciók beolvasása a MirWebservice osztályban a hozzáférési jogkivonat lekéréséhez
  + **azureml-core**
    + A AzureML webszolgáltatások üzembe helyezése mostantól `INFO` alapértelmezés szerint a naplózást végzi. Ezt a `AZUREML_LOG_LEVEL` környezeti változónak a központilag telepített szolgáltatásban való beállításával lehet szabályozni.
    + A munkaterületen `Dataset.get_all` regisztrált összes adatkészlet visszaküldéséhez javítsa az iterációt.
    + Javítsa a hibaüzenetet, ha a rendszer érvénytelen `path` típust ad át az adatkészlet-létrehozási API-k argumentumának.
    + A Python SDK a felderítési szolgáltatás használatával "API" végpontot használ a "folyamatok" helyett.
    + Csere az új útvonalakra az összes SDK-hívásban
    + A ModelManagementService irányuló hívások átirányításának módosítása egy új egyesített struktúrára
      + A munkaterület frissítési metódusa nyilvánosan elérhető.
      + Image_build_compute paraméter hozzáadva a munkaterület-frissítési metódusban, amely lehetővé teszi a felhasználó számára a rendszerkép-létrehozási számítások frissítését.
    +  Elavult üzeneteket adott hozzá a régi profilkészítési munkafolyamathoz. Rögzített profilkészítési CPU-és memória-korlátok
  + **azureml-interpret**
    + azureml frissítése – értelmezés az értelmezéshez – Community 0,6. *
  + **azureml-mlflow**
    + Adja hozzá a szuverén felhők támogatását a azureml. mlflow
  + **azureml-pipeline-steps**
    + `AutoMLStep` Áthelyezte a `azureml-pipeline-steps package`elemet a következőre:. A `AutoMLStep` belül `azureml-train-automl-runtime`elavult.
  + **azureml-train-automl-client**
    + Kijavított egy hibát, amelyben bizonyos csomagok a távoli futtatások helytelen verzióiban telepíthetők.
  + **azureml-train-automl-runtime**
    + Kijavítottuk a gyakoriság észlelésével kapcsolatos problémát a távoli futtatásokban.
    + `AutoMLStep` Áthelyezte a `azureml-pipeline-steps package`elemet a következőre:. A `AutoMLStep` belül `azureml-train-automl-runtime`elavult.
  + **azureml-train-core**
    + `AutoMLStep` Áthelyezte a `azureml-pipeline-steps package`elemet a következőre:. A `AutoMLStep` belül `azureml-train-automl-runtime`elavult.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.1 RC0 (előzetes verzió)

+ **Hibajavítások és javítások**
  + **Azure-CLI-ml**
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
  + **azureml-automl-core**
    + A hiba naplózása javult.
  + **azureml-automl-runtime**
    + Kijavítottuk az előrejelzéssel kapcsolatos problémát, ha az adathalmaz rövid, hosszú idejű hézagokat tartalmazó gabonát tartalmaz.
    + Kijavítottuk a problémát, ha az automatikus maximális horizont be van kapcsolva, és a Date oszlop karakterláncok formájában tartalmaz dátumokat. A megfelelő átalakítást és az értelmes hibát adta hozzá, ha a dátumra történő átalakítás nem lehetséges
    + Natív NumPy és SciPy használata a FileCacheStore köztes adatközpont szerializálásához és deszerializálásához (a helyi AutoML futtatásához használatos)
    + Kijavítva a hiba, ahol a gyermek futása meghiúsult a futó állapotban.
  + **azureml – CLI – gyakori**
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
  + **azureml-core**
    + További `--grant-workspace-msi-access` paraméterként hozzáadva az adattár CLI-hez az Azure Blob-tároló regisztrálásához, amely lehetővé teszi a VNet mögötti blob-tároló regisztrálását.
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
    + Kijavítva a problémát a aks.py _deploy
    + Ellenőrzi a feltöltés alatt álló modellek integritását a csendes tárolási hibák elkerülése érdekében.
  + **azureml – értelmezés**
    + azureml-stílusú kivételek hozzáadva a azureml-értelmezéshez
    + rögzített DeepScoringExplainer szerializálás kerasz-modellekhez
  + **azureml-pipeline-core**
    + A pipeline batch pontozási jegyzetfüzet mostantól a ParallelRunStep-t használja
  + **azureml-pipeline-steps**
    + `AutoMLStep` Áthelyezte a `azureml-pipeline-steps` csomagot a csomagba. A `AutoMLStep` belül `azureml-train-automl-runtime`elavult.
  + **azureml-contrib-pipeline-steps**
    + A nem kötelező paraméter side_inputs hozzáadva a ParallelRunStep-hez. Ezzel a paraméterrel lehet csatlakoztatni a mappát a tárolón. A jelenleg támogatott típusok a következők: DataReference és PipelineData.
  + **azureml-tensorboard**
    + Frissítve azureml-tensorboard a tensorflow 2,0 támogatásához
  + **azureml-train-automl-client**
    + rögzített FeaturizationConfig-felülbírálási probléma, amely az egyéni featurization-konfigurációt szűri.
  + **azureml-train-automl-runtime**
    + `AutoMLStep` Áthelyezte a `azureml-pipeline-steps` csomagot a csomagba. A `AutoMLStep` belül `azureml-train-automl-runtime`elavult.
  + **azureml-train-core**
    + A PyTorch 1,4-es verziójának támogatása a PyTorch-Kalkulátorban
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.0 RC0 (előzetes verzió)

+ **Kompatibilitástörő változások**
  + **Szemantikai verziószámozás 2.0.0**
    + Az 1,1-es verziótól kezdődően az Azure ML Python SDK a szemantikai verziószámozási 2.0.0 alkalmazza. [További tudnivalók itt](https://semver.org/). Minden további verzió új számozási sémát és szemantikai verziószámozási szerződést fog követni. 
  
+ **Hibajavítások és javítások**
  + **azureml-automl-runtime**
    + Nagyobb sebesség a featurization.
    + A gyakoriság ellenőrzésének kijavítása a pontozás során – mostantól az előrejelzési feladatokban nem szükséges szigorú gyakorisági egyenértékűséget megadni a vonat és a tesztelési készlet között.
  + **azureml-core**
    + A felhasználók mostantól meghatározhatnak egy értéket az Auth kulcshoz a webszolgáltatások kulcsainak újragenerálása során.
  + **azureml – értelmezés**
    + Frissített azureml – az értelmezéstől függ a közösségi 0.5.0
  + **azureml – folyamat – mag**
    + Kijavítva egy hiba, ahol a PythonScriptStep-eredmények helytelenül újra felhasználhatók az argumentumok listájának módosításakor
  + **azureml-pipeline-steps**
    + Dokumentáció hozzáadva példa az adatkészlethez PythonScriptStep-bemenetként
  + **azureml – egytörzsű folyamatok – lépések**
    + A ParallelRunConfig átadott paraméterek felülírása a folyamat paramétereinek átadásával lehetséges. Az új folyamat paraméterei támogatottak aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count és aml_process_count_per_node már a korábbi kiadás részét képezik).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK a Python v 1.0.85

+ **Új funkciók**
  + **azureml-core**
    + Egy adott munkaterülethez és előfizetéshez tartozó AmlCompute-erőforrások aktuális alapvető használati és kvóta-korlátozásának beolvasása
  
  + **azureml-contrib-pipeline-steps**
    + Lehetővé teheti, hogy a felhasználó átadja a táblázatos adatkészletet közbenső eredményként az előző lépésből a parallelrunstep

+ **Hibajavítások és javítások**
  + **azureml-automl-runtime**
    + A rendszer eltávolította a kérelemben szereplő y_query oszlop követelményét a telepített előrejelző szolgáltatáshoz. 
    + A "y_query" el lett távolítva a Dominic Orange Juice notebook Service Request szakaszból.
    + Javítva lett a hiba, amely megakadályozza a telepített modellekre vonatkozó előrejelzéseket, és a dátum-és időoszlopokat tartalmazó adatkészleteken működik.
    + Az Matthews korrelációs együtthatója egy besorolási metrikaként lett hozzáadva mind a bináris, mind a többosztályos besoroláshoz.
  + **azureml-contrib-interpret**
    + Eltávolított szöveges magyarázatok a azureml – a szöveg magyarázata átkerült a tolmácsolási szöveges tárházba, amely hamarosan elérhető lesz.
  + **azureml-core**
    + Adatkészlet: a fájl adatkészlethez való használata már nem függ a Python env-be telepítendő NumPy és pandáktől.
    + Módosította a LocalWebservice. wait_for_deployment () parancsot a helyi Docker-tároló állapotának ellenőrzésére, mielőtt megpróbálja az állapot-végpont pingelését, nagy mértékben csökkenti a sikertelen központi telepítés jelentésének időtartamát.
    + A LocalWebservice. reload () metódusban használt belső tulajdonság inicializálásának kijavítása, ha a szolgáltatási objektum egy meglévő telepítésből jön létre a LocalWebservice () konstruktor használatával.
    + A pontosításhoz szerkesztett hibaüzenet.
    + Egy get_access_token () nevű új metódust adott hozzá a (z) AksWebservice, amely a hozzáférési tokent tartalmazó AksServiceAccessToken objektumot adja vissza, az időbélyeg és a token típusának lejárta után. 
    + A AksWebservice meglévő get_token () metódusa elavult, mivel az új metódus visszaadja a metódus által visszaadott összes információt.
    + Az az ml Service Get-Access-Token parancs módosított kimenete. Átnevezte a tokent a accessToken és a refreshBy értékre a refreshAfter. A expiryOn és a tokenType tulajdonság hozzáadva.
    + Rögzített get_active_runs
  + **azureml – magyarázat – modell**
    + frissítve a 0.33.0 és a tolmácsolás – Közösség 0,4. *
  + **azureml-interpret**
    + frissítve a 0.33.0 és a tolmácsolás – Közösség 0,4. *
  + **azureml-train-automl-runtime**
    + Az Matthews korrelációs együtthatója egy besorolási metrikaként lett hozzáadva mind a bináris, mind a többosztályos besoroláshoz.
    + Az előfeldolgozási jelző elavult a kódban, és lecserélve a következőre: featurization-featurization alapértelmezés szerint be van kapcsolva

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK a Python v 1.0.83

+ **Új funkciók**
  + Adatkészlet: vegyen fel `on_error` két `out_of_range_datetime` lehetőséget `to_pandas_dataframe` , és ha a (z `None`) értéke sikertelen, akkor a rendszer nem tölti be őket a következővel
  + Munkaterület: hozzáadta `hbi_workspace` a jelölőt a bizalmas adatokkal rendelkező munkaterületekhez, amelyek lehetővé teszik a további titkosítást, és letiltja a speciális diagnosztikát a munkaterületeken. Emellett a saját kulcsok létrehozásához is támogatást biztosítunk a társított Cosmos DB példányhoz, ha a és `cmk_keyvault` `resource_cmk_uri` a paramétereket megadja a munkaterület létrehozásakor, amely létrehoz egy Cosmos db példányt az előfizetésben a munkaterület kiépítése során. [Itt olvashat bővebben.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Hibajavítások és javítások**
  + **azureml-automl-runtime**
    + Javítva lett egy olyan regresszió, amely TypeError okozta a AutoML a 3.5.4 alatti Python-verziókban való futtatásakor.
  + **azureml-core**
    + Kijavítva a `datastore.upload_files` hiba abban az esetben, ha a `./` relatív elérési út, amely nem indult el, nem használható.
    + Elavult üzenetek hozzáadása az összes rendszerkép osztály codepaths
    + Rögzített modellkezelés URL-felépítés a mooncake régióhoz.
    + Kijavítva a probléma, hogy az source_dir-t használó modellek nem tölthetők Azure Functionshoz.    
    + Lehetőség a [környezethez. build_local ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) a rendszerkép leküldése a AzureML Workspace-tároló beállításjegyzékbe
    + Frissítette az SDK-t, hogy az új jogkivonat-függvénytárat használja az Azure szinapszisban egy visszamenőleges kompatibilis módon.
  + **azureml – értelmezés**
    + Kijavítva a hiba, ha a rendszer nem adott vissza semmilyen magyarázatot a letöltéshez. A mostantól kivételt vet fel.
  + **azureml-pipeline-steps**
    + A rendszer nem engedélyezte `DatasetConsumptionConfig`a `Estimator`( `inputs` z `Estimator` `EstimatorStep`) paramétert, ha az a-ben lesz használva.
  + **azureml – SDK**
    + AutoML-ügyfél hozzáadva a azureml-SDK csomaghoz, amely lehetővé teszi a távoli AutoML futtatását, hogy a teljes AutoML csomag telepítése nélkül legyen elküldve.
  + **azureml-train-automl-client**
    + A konzol kimenetének javított igazítása a automl futtatásához
    + Kijavítva a hiba, ahol a Panda helytelen verziója lehet a távoli amlcompute telepítve.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK a Python v 1.0.81

+ **Hibajavítások és javítások**
  + **azureml-contrib-interpret**
    + a Shap-függőség elhalasztása a azureml értelmezéséhez
  + **azureml-core**
    + A számítási cél mostantól paraméterként is megadható a megfelelő telepítési konfigurációs objektumokhoz. Ez kifejezetten annak a számítási célnak a neve, amelyet telepíteni kell, nem az SDK-objektumra.
    + CreatedBy-információ hozzáadva a modell-és szolgáltatás-objektumokhoz. A-on keresztül <var>érhető el. created_by
    + Rögzített ContainerImage. Run (), amely nem volt megfelelően beállítva a Docker-tároló HTTP-portjának beállítása.
    + A `azureml-dataprep` `az ml dataset register` CLI parancs használata nem kötelező
    + Kijavítva egy olyan `TabularDataset.to_pandas_dataframe` hibát, amely helytelenül visszaesik egy másik olvasóba, és kinyomtat egy figyelmeztetést.
  + **azureml – magyarázat – modell**
    + a Shap-függőség elhalasztása a azureml értelmezéséhez
  + **azureml-pipeline-core**
    + Új folyamat lépésének `NotebookRunnerStep`hozzáadása a helyi jegyzetfüzetnek a folyamat lépéseiként való futtatásához.
    + Elavult get_all függvények lettek eltávolítva a PublishedPipelines, az ütemtervekhez és a PipelineEndpoints
  + **azureml-train-automl-client**
    + A rendszer elindította a data_script elavulttá tételét a AutoML bemenetként.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK a Python v 1.0.79

+ **Hibajavítások és javítások**
  + **azureml-automl-core**
    + A naplózott featurizationConfig eltávolítva
      + Frissült a naplózás az "Auto"/"off"/"Customized" kifejezésre.
  + **azureml-automl-runtime**
    + A Panda-támogatás hozzáadva. Adatsorozat és pandák. Kategorikus az oszlop adattípusának észleléséhez. Korábban csak a NumPy. ndarray támogatott.
      + A kapcsolódó kód módosításainak hozzáadása a kategorikus dtype megfelelő kezeléséhez.
    + Az előrejelzési funkció felülete javult: a y_pred paraméter nem kötelező. – A docstrings javult.
  + **azureml-contrib-dataset**
    + Kijavítva egy hiba, amelyben a címkézett adatkészletek nem csatlakoztathatók.
  + **azureml-core**
    + Hibajavítás a következőhöz: `Environment.from_existing_conda_environment(name, conda_environment_name)`. A felhasználó létrehozhat egy olyan környezet-példányt, amely a helyi környezet pontos replikája
    + A rendszer alapértelmezés szerint módosította az `include_boundary=True` idősorozathoz kapcsolódó adatkészletek metódusait.
  + **azureml-train-automl-client**
    + Kijavítva a hiba, ha az érvényesítési eredmények nem lesznek kinyomtatva, ha a kimenet értéke hamis.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK a Python v 1.0.76

+ **Kompatibilitástörő változások**
  + Azureml – AutoML frissítési problémák
    + Frissítés a azureml-Train-automl>= 1.0.76 from azureml-automl<1.0.76 részleges telepítéseket okozhat, ami miatt egyes automl-importálások sikertelenek lesznek. Ennek megoldásához futtassa a következő helyen található telepítési parancsfájlt: https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd. Vagy ha közvetlenül a pip-et használja, a következőket teheti:
      + "pip install--upgrade azureml-Train-automl"
      + "pip install--Mellőzés – telepített azureml-Train-automl-Client"
    + vagy eltávolíthatja a régi verziót a frissítés előtt
      + "pip eltávolítása azureml-Train-automl"
      + "pip install azureml-Train-automl"

+ **Hibajavítások és javítások**
  + **azureml-automl-runtime**
    + A AutoML mostantól az igaz és a hamis osztályokat is figyelembe veszik a bináris besorolási feladatok átlagos skaláris metrikáinak kiszámításakor.
    + Áthelyezte a Machine learning-és betanítási kódot a AzureML-AutoML-Core-ban egy új, AzureML-AutoML-Futtatókörnyezetű csomagba.
  + **azureml-contrib-dataset**
    + Ha címkével ellátott adatkészletet hív `to_pandas_dataframe` meg a letöltési lehetőséggel, akkor megadhatja, hogy felülírja-e a meglévő fájlokat.
    + `keep_columns` Egy idősor, felirat vagy képoszlop eldobásakor a rendszer a megfelelő képességeket is elveti az adatkészlethez `drop_columns` .
    + Az objektum észlelése feladat pytorch betöltő hibája javítva.
  + **azureml-contrib-interpret**
    + A magyarázat-irányítópult widget eltávolítása a azureml-untribal-értelmezze, changed Package, hogy az újat hivatkozzon a interpret_community
    + A tolmácsolási Közösség frissített verziója 0.2.0
  + **azureml-core**
    + Javítsa a `workspace.datasets`teljesítményét.
    + Az Azure SQL Database adattár regisztrálása felhasználónévvel és jelszóval történő hitelesítéssel
    + Javítsa a RunConfigurations betöltését a relatív elérési utakról.
    + Ha a `keep_columns` meghívásakor vagy `drop_columns` a idősor oszlop eldobása történik, akkor a megfelelő képességek el lesznek dobva az adatkészlethez is.
  + **azureml – értelmezés**
    + a tolmácsolási Közösség frissített verziója 0.2.0
  + **azureml-pipeline-steps**
    + `runconfig_pipeline_params` Az Azure Machine learning-folyamat lépéseinek dokumentált támogatott értékei.
  + **azureml-pipeline-core**
    + CLI-beállítás hozzáadva a kimenet JSON formátumban való letöltéséhez a folyamat parancsaihoz.
  + **azureml-Train-automl**
    + Split AzureML – AutoML 2 csomagba, egy AzureML-Train-AutoML-Client és egy ML-es képzési csomag AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Egy vékony ügyfelet vett fel a AutoML-kísérletek elküldéséhez anélkül, hogy a gépi tanulási függőségeket helyileg kellene telepítenie.
    + Az automatikusan észlelt késések, a gördülő ablakok méretei és a távoli futtatások maximális távlatai rögzített naplózása.
  + **azureml-train-automl-runtime**
    + Új AutoML-csomag hozzáadva a Machine learning és a futásidejű összetevők elkülönítéséhez az ügyfélről.
  + **azureml-a-a-Train-RL**
    + Megerősítő tanulási támogatás az SDK-ban.
    + AmlWindowsCompute-támogatás hozzáadva az RL SDK-ban.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK a Python v 1.0.74

  + **Előzetes verziójú funkciók**
    + **azureml-contrib-dataset**
      + A azureml-a `Dataset.Labeled.from_json_lines` `._Labeled` --adatkészletek importálása után meghívhatja a címkével ellátott adatkészlet létrehozása helyett.
      + Ha címkével ellátott adatkészletet hív `to_pandas_dataframe` meg a letöltési lehetőséggel, akkor megadhatja, hogy felülírja-e a meglévő fájlokat.
      + Ha egy `keep_columns` idősorozat, címke vagy képoszlop eldobásakor meghívja a vagy `drop_columns` az eredményt, a rendszer a megfelelő képességeket is elveti az adatkészlethez.
      + Rögzített hibák a PyTorch betöltő hívásakor `dataset.to_torchvision()`.

+ **Hibajavítások és javítások**
  + **Azure-CLI-ml**
    + Modell-profilkészítés hozzáadva az előnézeti CLI-hez.
    + Javítja az Azure Storage-ban fellépő változások változását, ami miatt a CLI AzureML sikertelen lesz.
    + Load Balancer típus hozzáadva az MLC-hoz az AK-típusoknál
  + **azureml-automl-core**
    + Kijavítottuk a problémát az idősorozatok maximális horizontjának észlelésével, hiányzó értékekkel és több gabonával.
    + Kijavítottuk a hibák számát a kereszt-ellenőrzési felosztások létrehozásakor.
    + A szakasz helyébe egy Markdown formátumú üzenet jelenik meg, amely a kibocsátási megjegyzésekben szerepel: – az előrejelzési adatkészletekben a rövid szemek jobb kezelését.
    + Kijavítottuk a problémát néhány felhasználói információ maszkolásakor a naplózás során. – A hibák továbbfejlesztett naplózása az előrejelzési futtatások során.
    + A psutil hozzáadása Conda-függőségként az automatikusan létrehozott YML-telepítési fájlhoz.
  + **azureml – megterhelés – Mir**
    + Javítja az Azure Storage-ban fellépő változások változását, ami miatt a CLI AzureML sikertelen lesz.
  + **azureml-core**
    + Javít egy olyan hibát, amely a Azure Functionson üzembe helyezett modelleket okozta a 500 létrehozásához.
    + Kijavított egy hibát, amelyben a amlignore-fájl nem lett alkalmazva a pillanatképekre.
    + Hozzáadott egy új API-amlcompute. get_active_runs, amely visszaad egy generátort a futó és a várólistán lévő futtatásokhoz egy adott amlcompute.
    + Load Balancer típust adtak hozzá az MLC-hoz az AK-típusoknál.
    + Append_prefix bool paramétert adtak hozzá a run.py-ben, és a artifacts_client-ben download_artifacts_from_prefix a download_files. Ez a jelző a forrás filepath szelektív összevonására szolgál, így csak a fájl-vagy mappanév kerül a output_directoryba
    + A deszerializálási probléma kijavítása az adatkészlet használata esetén `run_config.yml` .
    + Amikor egy `keep_columns` idősorozat-oszlop eldobásakor meghívja a vagy `drop_columns` az eredményt, a rendszer a megfelelő képességeket is elveti az adatkészlethez.
  + **azureml – értelmezés**
    + Frissített értelmezés – közösségi verzió a 0.1.0.3
  + **azureml-Train-automl**
    + Kijavított egy problémát, amelyben a automl_step nem lehet kinyomtatni az érvényesítési problémákat.
    + A rögzített register_model sikeres lesz, még akkor is, ha a modell környezete hiányzik a függőségek helyileg.
    + Kijavított egy hibát, amelyben egyes távoli futtatások nem engedélyezettek a Docker-ben.
    + Adja hozzá a kivétel naplózását, amely a helyi futtatást okozza idő előtt.
  + **azureml-train-core**
    + Gondolja át, hogy resume_from fut-e az automatikus hiperparaméter hangolásának legjobb alárendelt futtatásainak kiszámításakor.
  + **azureml – folyamat – mag**
    + Rögzített paraméterek feldolgozása a folyamat argumentumának kialakításakor.
    + Hozzáadott folyamat leírása és a lépés típusa YAML paraméter.
    + Új YAML-formátum a folyamat lépéseihez és a régi formátumra vonatkozó elavult figyelmeztetés hozzáadása.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webes felület

Az együttműködési munkaterület kezdőlapja a következő [https://ml.azure.com](https://ml.azure.com) helyen lett kialakítva, és a Azure Machine learning Studio (előzetes verzió) néven lett feldolgozva.

A studióból betaníthatja, tesztelheti, üzembe helyezheti és kezelheti Azure Machine Learning adategységeket, például adatkészleteket, folyamatokat, modelleket, végpontokat és sok más eszközt.

A Studio alkalmazásban a következő webalapú szerzői eszközöket érheti el:

| Webalapú eszköz | Leírás | Kiadás |
|-|-|-|
| Notebook VM (előzetes verzió) | Teljes körűen felügyelt felhőalapú munkaállomás | Alapszintű & Enterprise |
| [Automatikus gépi tanulás](tutorial-first-experiment-automated-ml.md) (előzetes verzió) | Nincs programkód a gépi tanulási modellek fejlesztésének automatizálásához | Enterprise |
| [Designer](concept-designer.md) (előzetes verzió) | A korábban a tervezőnek nevezett gépi tanulási modellezési eszköz húzása és eltávolítása | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning Designer fejlesztései

+ Korábbi nevén vizuális felület 
+    11 új [modul](algorithm-module-reference/module-reference.md) , beleértve az ajánlókat, az osztályozók és az oktatási segédeszközöket, beleértve a szolgáltatások mérnöki, több ellenőrzési és Adatátalakítási funkcióját.

### <a name="r-sdk"></a>R SDK 
 
Az adatszakértők és a mesterséges intelligencia-fejlesztők a [Azure Machine learning SDK for R](tutorial-1st-r-experiment.md) használatával hozhatnak létre és futtathatnak gépi tanulási munkafolyamatokat Azure Machine learning.

Az R Azure Machine Learning SDK a `reticulate` csomagot használja a Python SDK-hoz való kötéshez. A Pythonhoz való közvetlen kötéssel az R SDK lehetővé teszi, hogy a Python SDK-ban megvalósított alapvető objektumokhoz és metódusokhoz hozzáférjen a kiválasztott R-környezetből.

Az SDK főbb képességei a következők:

+    Felhőbeli erőforrások kezelése a gépi tanulási kísérletek monitorozásához, naplózásához és rendszerezéséhez.
+    Modellek betanítása felhőalapú erőforrásokkal, beleértve a GPU-gyorsított Modelles képzést.
+    A modelleket webszolgáltatásként helyezheti üzembe Azure Container Instances (ACI) és az Azure Kubernetes Service (ak) szolgáltatásban.

A teljes dokumentációért tekintse meg a [csomag webhelyét](https://azure.github.io/azureml-sdk-for-r) .

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integráció a Event Grid 

A Azure Machine Learning mostantól a Event Grid erőforrás-szolgáltatója, a Azure Portal vagy az Azure CLI használatával konfigurálhatja a gépi tanulási eseményeket. A felhasználók létrehozhatnak eseményeket a Futtatás befejezésére, a modell regisztrálására, a modell üzembe helyezésére és az adateltolódás észlelésére. Ezeket az eseményeket a Event Grid által támogatott eseménykezelők számára lehet átirányítani. További részletekért tekintse meg a Machine learning-esemény [sémáját](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning) és az [oktatóanyagokat](how-to-use-event-grid.md) ismertető cikket.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK a Python v 1.0.72

+ **Új funkciók**
  + A [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift) csomag segítségével hozzáadta az adatkészlet-figyelőket, ami lehetővé teszi, hogy az idősorozat-adatkészleteket az adateltolódásra vagy más statisztikai változásokra időben figyelje. A riasztások és események akkor indíthatók el, ha a rendszer a drift észlelését vagy az adatfeldolgozás egyéb feltételeit is kielégíti. A részletekért tekintse meg [a dokumentációt](https://aka.ms/datadrift) .
  + Két új kiadás bejelentése (más néven SKU szinonimaként) a Azure Machine Learningban. Ezzel a kiadással már létrehozhat egy alapszintű vagy egy vállalati Azure Machine Learning munkaterületet is. Minden meglévő munkaterület Alapértelmezésben az alapszintű kiadás lesz, és a munkaterületet bármikor frissítheti a Azure Portal vagy a studióba. Létrehozhat egy alapszintű vagy egy vállalati munkaterületet is a Azure Portal. További információért olvassa el [a dokumentációt](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) . Az SDK-ból a munkaterület kiadása a munkaterület objektum "SKU" tulajdonságának használatával határozható meg.
  + Továbbfejlesztettük a Azure Machine Learning számítást is – mostantól megtekintheti a fürtök metrikáit (például az összes csomópontot, a csomópontokat, a teljes kvótát) a Azure Monitorban, a diagnosztikai naplók megtekintése mellett a hibakereséshez. Emellett az aktuálisan futó vagy várólistán lévő futtatásokat is megtekintheti a fürtön, valamint a fürt különböző csomópontjainak IP-címeit. Ezeket a portálon vagy az SDK-ban vagy a CLI-ben a megfelelő függvények használatával tekintheti meg.

  + **Előzetes verziójú funkciók**
    + Az előzetes verzió támogatja a helyi SSD lemezek titkosítását Azure Machine Learning számításban. Ha ezt a funkciót szeretné használni, vegyen fel egy technikai támogatási jegyet az előfizetése engedélyezési listára.
    + Azure Machine Learning batch-következtetés nyilvános előzetes verziója. Azure Machine Learning batch-következtetések olyan nagy következtetési feladatokat céloznak meg, amelyek nem időérzékenyek. A Batch-következtetések költséghatékony következtetéseket biztosítanak, és az aszinkron alkalmazások esetében páratlan átviteli sebességgel rendelkeznek. Nagy adatátviteli sebességre van optimalizálva, és nagy adatgyűjtési feladatokhoz is felejtsd el.
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + A címkézett adatkészlet funkcióinak engedélyezése
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Hibajavítások és javítások**
  + **Azure-CLI-ml**
    + A CLI mostantól támogatja a modell csomagolását.
    + Adatkészlet hozzáadva a CLI-hez. További információ:`az ml dataset --help`
    + Támogatott modellek (ONNX, scikit-Learn és TensorFlow) üzembe helyezésének és csomagolásának támogatása InferenceConfig-példány nélkül.
    + A szolgáltatás üzembe helyezésének (ACI és ak) felülírási jelzője hozzáadva az SDK-ban és a CLI-ben. Ha meg van jelölve, a felülírja a meglévő szolgáltatást, ha már létezik ilyen nevű szolgáltatás. Ha a szolgáltatás nem létezik, új szolgáltatást fog létrehozni.
    + A modellek regisztrálása két új keretrendszerrel, Onnx és Tensorflow lehetséges. -A modell regisztrációja fogadja a minta bemeneti adatokat, minta kimeneti adatokat és erőforrás-konfigurációt a modellhez.
  + **azureml-automl-core**
    + Az iterációk betanítása csak akkor fut egy alárendelt folyamatban, ha a futásidejű megkötések be vannak állítva.
    + Az előrejelzési feladatokhoz hozzáadott egy Guardrail, amellyel ellenőrizhető, hogy egy adott max_horizon okozhat-e memóriát a megadott gépen. Ha ez megtörténik, egy Guardrail üzenet jelenik meg.
    + Többek között az összetett gyakoriságok támogatása, például 2 év és 1 hónap. – Új, érthető hibaüzenet, ha a gyakoriság nem határozható meg.
    + Azureml hozzáadása – alapértelmezés szerint automatikusan generált Conda env a modell telepítési hibájának megoldása érdekében
    + Azure Machine Learning folyamat közbenső adatfolyamatainak átalakítása táblázatos adatkészletbe és használatba `AutoMLStep`vehető.
    + Megvalósított oszlop céljának frissítése a folyamatos átvitelhez.
    + Megvalósított transzformátor-paraméterek frissítése az imputált és a HashOneHotEncoder a streaminghez.
    + Az aktuális adatméretet és a minimálisan szükséges adatméretet adta hozzá az érvényesítési hibaüzenetekhez.
    + A több ellenőrzés minimálisan szükséges adatméretének frissítése az egyes ellenőrzési sorokban legalább két minta biztosításához.
  + **azureml – CLI – gyakori**
    + A CLI mostantól támogatja a modell csomagolását.
    + A modellek regisztrálása két új keretrendszerrel, Onnx és Tensorflow lehetséges.
    + A modell regisztrációja fogadja a minta bemeneti adatokat, a modell kimeneti adatait és erőforrás-konfigurációját.
  + **azureml – gbdt**
    + a notebook kiadási csatornájának kijavítása
    + Figyelmeztetés hozzáadva a nem AmlCompute számítási célhoz, amelyet nem támogatunk
    + LightGMB-kalkulátor hozzáadva a azureml-gbdt csomaghoz
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + A CLI mostantól támogatja a modell csomagolását.
    + Elavult figyelmeztetés hozzáadása az elavult adatkészlet API-khoz. Lásd: https://aka.ms/tabular-datasetadatkészlet API-változási nyilatkozata.
    + Ha [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) az adatkészlet regisztrálva van, váltson vissza a regisztrációs névre és a verzióra.
    + Javítsa ki azt a hibát, amelyet a ScriptRunConfig az adatkészletben argumentummal nem lehet ismételten használni a kísérlet futtatásának elküldéséhez.
    + A Futtatás során lekért adatkészleteket a rendszer nyomon követi, és megtekintheti a Futtatás részletei lapon vagy [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) a Futtatás befejezését követően.
    + Azure Machine Learning folyamat közbenső adatfolyamatainak átalakítása táblázatos adatkészletbe és használatba [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)vehető.
    + Támogatott modellek (ONNX, scikit-Learn és TensorFlow) üzembe helyezésének és csomagolásának támogatása InferenceConfig-példány nélkül.
    + A szolgáltatás üzembe helyezésének (ACI és ak) felülírási jelzője hozzáadva az SDK-ban és a CLI-ben. Ha meg van jelölve, a felülírja a meglévő szolgáltatást, ha már létezik ilyen nevű szolgáltatás. Ha a szolgáltatás nem létezik, új szolgáltatást fog létrehozni.
    +  A modellek regisztrálása két új keretrendszerrel, Onnx és Tensorflow lehetséges. A modell regisztrációja fogadja a minta bemeneti adatokat, a modell kimeneti adatait és erőforrás-konfigurációját.
    + Új adattár lett hozzáadva a Azure Database for MySQLhoz. Új példa a Azure Database for MySQL DataTransferStep való használatára Azure Machine Learning folyamatokban.
    + Hozzáadott funkciók a címkék hozzáadásához és eltávolításához a-kísérletek hozzáadott funkcióival a címkék a futtatásokból való eltávolításához
    + A szolgáltatás üzembe helyezésének (ACI és ak) felülírási jelzője hozzáadva az SDK-ban és a CLI-ben. Ha meg van jelölve, a felülírja a meglévő szolgáltatást, ha már létezik ilyen nevű szolgáltatás. Ha a szolgáltatás nem létezik, új szolgáltatást fog létrehozni.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Áthelyezve a következőbe `azureml-contrib-datadrift` :`azureml-datadrift`
    + Az idősorozat-adatkészletek figyelésének támogatása a drift és más statisztikai intézkedések esetében
    + Új `create_from_model()` metódusok `create_from_dataset()` és a [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) osztály. A `create()` metódus elavulttá válik.
    + A Pythonban és a felhasználói felületen lévő vizualizációk módosításai a Azure Machine Learning Studióban.
    + A heti és havi monitorozási ütemezés támogatása a napi adatkészlet-figyelők mellett.
    + Az adatfigyelő metrikáinak backfill támogatása az adatkészlet-figyelők korábbi adatainak elemzéséhez.
    + Különböző hibajavítások
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + a azureml-adatelőkészítés már nem szükséges egy Azure Machine Learning-folyamatnak a folyamat `yaml` fájljából való beküldéséhez.
  + [**azureml-Train-automl**](/python/api/azureml-train-automl-runtime/)
    + Azureml hozzáadása – alapértelmezés szerint automatikusan generált Conda env a modell telepítési hibájának megoldása érdekében
    + A AutoML Remote Training mostantól tartalmazza a azureml-alapértékeket, amelyek lehetővé teszik a betanítási env használatának újbóli használatát.
  + **azureml-train-core**
    + PyTorch 1,3-támogatás hozzáadva a [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) kalkulátorban

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Vizuális felület (előzetes verzió)

+ A Azure Machine Learning Visual Interface (előzetes verzió) [Azure Machine learning folyamatokon](concept-ml-pipelines.md)való futtatásra lett kidolgozva. A vizuális felületen létrehozott folyamatok (korábbi néven kísérletek) mostantól teljes mértékben integrálva vannak az alapvető Azure Machine Learning felülettel.
  + Egységes felügyeleti élmény az SDK-eszközökkel
  + A Visual Interface-modellek,-folyamatok és-végpontok verziószámozása és nyomon követése
  + Újratervezett felhasználói felület
  + A Batch következtetési központi telepítése hozzáadva
  + Az Azure Kubernetes Service (ak) támogatása a következtetésekhez kapcsolódó számítási célokhoz
  + Új Python-Step folyamat-létrehozási munkafolyamat
  + A Visual authoring Tools új [kezdőlapja](https://ml.azure.com)

+ **Új modulok**
  + Matematikai művelet alkalmazása
  + SQL-transzformáció alkalmazása
  + Klip értékei
  + Az adatösszesítés
  + Importálás az SQL Database-ből

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK a Python v 1.0.69

+ **Hibajavítások és javítások**
  + **azureml-automl-core**
    + A modell magyarázatait a legjobb futtatásra korlátozza, és nem kell minden futtatáskor számítási magyarázatot felvennie. Ez a viselkedés a helyi, a távoli és az ADB esetében is változhat.
    + Igény szerinti modellhez kapcsolódó magyarázatok támogatása a felhasználói felületen
    + A rendszer a psutil-függőségként `automl` hozzáadta a psutil, amely Conda-függőségként szerepel a amlcompute-ben.
    + Az előrejelzési adatkészletek esetében a heurisztikus késéssel és a gördülő ablak méretével kapcsolatos probléma kijavítva egy sor, ami lineáris algebra-hibákhoz vezethet
      + Kinyomtatva az előrejelzési futtatások heurisztikus meghatározású paramétereinek kinyomtatása.
  + **azureml – datadrift**
    + A védelem a kimeneti metrikák létrehozásakor lett hozzáadva, ha az adatkészletek szintjének eltolódása nem az első szakaszban található.
  + **azureml-contrib-interpret**
    + azureml – összevont – magyarázat – a modell csomagja át lett nevezve a azureml-retribal-értelmezze
  + **azureml-core**
    + API hozzáadása az adatkészletek regisztrációjának megszüntetéséhez. `dataset.unregister_all_versions()`
    + azureml – a (z)-detörzsi-magyarázat-Model csomag át lett nevezve a azureml-retribal-értelmezze névre.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + API hozzáadása az adatkészletek regisztrációjának megszüntetéséhez. adatkészlet. [unregister_all_versions ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Az adatkészlet API-ját hozzáadta az adatváltozások időpontjához. `dataset.data_changed_time`.
    + `FileDataset` `PythonScriptStep` `EstimatorStep` `HyperDriveStep` A, a, a és a Azure Machine learning adatcsatornán való bevitele `TabularDataset`
    + `FileDataset.mount` A nagy mennyiségű fájllal rendelkező mappák esetében javult a teljesítmény.
    + A [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) és a [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) bemenetként való felhasználása a [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), a [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)és a [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) számára a Azure Machine learning folyamat során.
    + A FileDataset teljesítménye. a [Mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) szolgáltatás a nagy mennyiségű fájllal rendelkező mappák esetében javult
    + A Futtatás részleteiben szereplő ismert hibajelentések URL-címe hozzáadva.
    + Hiba történt a Futtatás közben. get_metrics, ahol a kérelmek sikertelenek lesznek, ha egy Futtatás túl sok gyermeket tartalmaz
    + Hiba történt a [Futtatás közben. get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) , ahol a kérelmek sikertelenek lesznek, ha egy Futtatás túl sok gyermeket tartalmaz
    + Támogatás hozzáadva az Arcadia-fürtön történő hitelesítéshez.
    + A kísérlet objektum létrehozása vagy létrehozása a kísérletet a Azure Machine Learning munkaterületen a futtatási előzmények nyomon követéséhez. A kísérlet azonosítója és az archivált idő a létrehozáskor a kísérlet objektumban van feltöltve. Példa: Experiment = Experiment (munkaterület, "New Experiment") experiment_id = experiment.id Archive () és reactivate () függvények, amelyek meghívhatók egy kísérletre, hogy elrejtsék és visszaállítsák a kísérletet az UX-ben, vagy alapértelmezés szerint visszaadja azokat a kísérleteket tartalmazó hívásban. Ha egy új kísérlet ugyanazzal a névvel lett létrehozva, mint az archivált kísérlet, az újraaktiváláskor átnevezheti az archivált kísérletet egy új név átadásával. Egy adott névvel csak egy aktív kísérlet lehet. Például: experiment1 = Experiment (munkaterület, "Active Experiment") experiment1. Archive () # hozzon létre új aktív kísérletet ugyanazzal a névvel, mint az archivált. experiment2. = Kísérlet (munkaterület, "aktív kísérlet") experiment1. reactivate (new_name = "előző aktív kísérlet") a kísérlet során a statikus metódusok listája () is megteheti a nevet és a nézet típusa szűrőt. A nézet típusa értékek: "ACTIVE_ONLY", "ARCHIVED_ONLY" és "ALL" Példa: archived_experiments = Experiment. list (munkaterület, view_type = "ARCHIVED_ONLY") all_first_experiments = kísérlet. list (munkaterület, név = "első kísérlet", view_type = "ALL")
    + A környezet támogatása a modell üzembe helyezéséhez és a szolgáltatás frissítéséhez
  + **azureml-datadrift**
    + A DataDriftDector osztály show attribútuma nem támogatja többé a következő opcionális argumentumot: "with_details". A show attribútum csak az adateltolódási együtthatót és a szolgáltatás oszlopainak adateltolódási hozzájárulását mutatja.
    + A (z) "get_output" DataDriftDetector attribútum változásai:
      + A (z) start_time bemeneti paraméter nem kötelező, hanem end_time opcionális.
      + Ha a megadott start_time és/vagy end_time egy adott meghívásban meghatározott run_id adott meg, akkor az érték hiba miatt kivételt eredményez, mivel kölcsönösen kizárják egymást
      + A megadott start_time és/vagy end_time alapján csak az ütemezett futtatások eredményei lesznek visszaadva.
      + A következő paraméter elavult: "daily_latest_only".
    + Támogatja az adatkészlet-alapú adateltolódások kimenetének beolvasását.
  + **azureml – magyarázat – modell**
    + Átnevezi a AzureML-magyarázza-Model csomagot a AzureML-értelmezésre, így a régi csomagot a visszamenőleges kompatibilitás érdekében megtarthatja
    + kijavítva `automl` a hiba, amely a ExplanationClient-re való letöltéskor nem a regressziós, hanem a besorolási feladatra van beállítva.
    + Támogatás hozzáadása közvetlenül `ScoringExplainer` a következő használatával:`MimicWrapper`
  + **azureml – folyamat – mag**
    + Nagyobb teljesítmény a nagy adatcsatornák létrehozásakor
  + **azureml-train-core**
    + TensorFlow 2,0-támogatás hozzáadva a TensorFlow kalkulátorhoz
  + **azureml-Train-automl**
    + A kísérlet [objektum létrehozása](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) vagy létrehozása a kísérletet a Azure Machine learning munkaterületen a futtatási előzmények nyomon követéséhez. A kísérlet azonosítója és az archivált idő a létrehozáskor a kísérlet objektumban van feltöltve. Példa:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        az [archiválás ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) és az [újraaktiválás ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) olyan függvények, amelyek meghívhatók egy kísérletre, hogy elrejtsék és visszaállítsák a kísérletet az UX-ben, vagy alapértelmezés szerint visszaadja azokat a kísérletek listájának hívásakor. Ha egy új kísérlet ugyanazzal a névvel lett létrehozva, mint az archivált kísérlet, az újraaktiváláskor átnevezheti az archivált kísérletet egy új név átadásával. Egy adott névvel csak egy aktív kísérlet lehet. Példa:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        A kísérlet során a statikus metódusok [listája ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) a név szűrőjét és a nézet típusa szűrőt is elvégezheti. A nézet típusa értékei a következők: "ACTIVE_ONLY", "ARCHIVED_ONLY" és "ALL". Példa:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + A környezet támogatása a modell üzembe helyezéséhez és a szolgáltatás frissítéséhez.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + A [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) osztály show attribútuma nem támogatja többé a következő opcionális argumentumot: "with_details". A show attribútum csak az adateltolódási együtthatót és a szolgáltatás oszlopainak adateltolódási hozzájárulását mutatja.
    + A DataDriftDetector függvény [get_outputhttps://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) ] viselkedésének változásai:
      + A (z) start_time bemeneti paraméter nem kötelező, hanem end_time opcionális.
      + A megadott start_time és/vagy end_time egy adott meghíváshoz tartozó konkrét run_id megadása esetén a rendszer hibát eredményez, mivel ezek kölcsönösen kizárják egymást.
      + A megadott start_time és/vagy end_time alapján csak az ütemezett futtatások eredményei lesznek visszaadva.
      + A következő paraméter elavult: "daily_latest_only".
    + Támogatja az adatkészlet-alapú adateltolódások kimenetének beolvasását.
  + **azureml – magyarázat – modell**
    + Átnevezi a AzureML-magyarázza-Model csomagot a AzureML-értelmezésre, így a régi csomag visszafelé kompatibilis marad.
    + rögzített AutoML hiba a RAW-magyarázatokkal a besorolási feladathoz a ExplanationClient-ből való letöltéskor alapértelmezetten nem regressziós.
    + A MimicWrapper használatával közvetlenül létrehozandó [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py) -támogatás hozzáadása
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Nagyobb teljesítmény a nagy adatcsatornák létrehozásához.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + TensorFlow 2,0 támogatás hozzáadva a [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) kalkulátorhoz.
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + A szülő futtatása nem fog sikerülni, ha a telepítés iterációja meghiúsult, mivel a folyamat már gondoskodik róla.
    + Helyi Docker-és helyi Conda-támogatás hozzáadva a AutoML-kísérletekhez
    + Helyi Docker-és helyi Conda támogatása a AutoML-kísérleteknél.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Új webes felület (előzetes verzió) Azure Machine Learning munkaterületekhez

Frissült az [Új munkaterület-portál](https://ml.azure.com) Experiment (kísérlet) lapja, így az adatszakértők több módon is megfigyelheti a kísérleteket. A következő funkciók közül választhat:
+ Kísérletezzen a metaadatokkal, így egyszerűen szűrheti és rendezheti a kísérletek listáját
+ Egyszerűsített és elvégezhető kísérlet részleteinek oldalai, amelyek lehetővé teszik a futtatások megjelenítését és összehasonlítását
+ Új kialakítás a részletek oldalain a képzések megismeréséhez és figyeléséhez

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK a Python v 1.0.65

  + **Új funkciók**
    + Válogatott környezetek lettek hozzáadva. Ezek a környezetek előre konfigurálva vannak könyvtárakkal a gyakori gépi tanulási feladatokhoz, és a gyorsabb végrehajtás érdekében Docker-rendszerképekként előre felépítve és gyorsítótárazva lettek. Alapértelmezés szerint a munkaterület "AzureML" előtagú környezetének listájában jelennek meg.
    + Válogatott környezetek lettek hozzáadva. Ezek a környezetek előre konfigurálva vannak könyvtárakkal a gyakori gépi tanulási feladatokhoz, és a gyorsabb végrehajtás érdekében Docker-rendszerképekként előre felépítve és gyorsítótárazva lettek. Alapértelmezés szerint a [munkaterület](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)"AzureML" előtagú környezetének listájában jelennek meg.

  + **azureml-Train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + A ONNX-átalakítás támogatása az ADB és a HDI esetében

+ **Előzetes verziójú funkciók**
  + **azureml-Train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Támogatott BERT és BiLSTM as text Képtulajdonság (csak előzetes verzió)
    + Az oszlop céljának és a transzformátor paramétereinek támogatott featurization testreszabása (csak előzetes verzió)
    + Támogatott nyers magyarázatok, ha a felhasználó a betanítás során engedélyezi a modell magyarázatát (csak előzetes verzió)
    + Próféta hozzáadva `timeseries` az előrejelzéshez egy betanítható folyamatként (csak előzetes verzió)

  + **azureml – datadrift**
    + Csomagok áthelyezve a azureml-datadrift-azureml-datadrift; a `contrib` csomag el lesz távolítva egy későbbi kiadásban

+ **Hibajavítások és javítások**
  + **azureml-automl-core**
    + Bevezette a FeaturizationConfig a AutoMLConfig és a AutoMLBaseSettings
    + Bevezette a FeaturizationConfig a [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) és a AutoMLBaseSettings
      + A Featurization oszlopának felülbírálása a megadott oszlop és szolgáltatás típusával
      + A transzformátor paramétereinek felülbírálása
    + Elavult üzenet hozzáadva a explain_model () és a retrieve_model_explanations () rendszerhez
    + Próféta hozzáadva betanítható folyamatként (csak előzetes verzió)
    + Elavult üzenet lett hozzáadva a explain_model () és a retrieve_model_explanations () rendszerhez.
    + Próféta hozzáadva betanítható folyamatként (csak előzetes verzió).
    + A cél késések automatikus észlelésének támogatása, a gördülő ablak mérete és a maximális horizont. Ha target_lags, target_rolling_window_size vagy max_horizon az "Auto" értékre van állítva, a rendszer a heurisztikus értékeket a betanítási adat alapján a megfelelő paraméter értékének becslésére fogja alkalmazni.
    + Rögzített előrejelzés abban az esetben, ha az adathalmaz egy gabona oszlopot tartalmaz, ez a gabona numerikus típusú, és a vonat és a tesztelési készlet közötti hézag van.
    + Kijavítva a duplikált indextel kapcsolatos hibaüzenet a távoli Futtatás előrejelzési feladatokban
    + Rögzített előrejelzés abban az esetben, ha az adathalmaz egy gabona oszlopot tartalmaz, a gabona numerikus típusú, és a vonat és a tesztelési készlet között hiányzik a különbség.
    + Kijavítva a duplikált indexre vonatkozó hibaüzenetet a távoli Futtatás előrejelzési feladatokban.
    + Egy Guardrail hozzáadva annak ellenőrzését, hogy az adatkészlet kiegyensúlyozatlan vagy sem. Ha igen, a rendszer egy Guardrail üzenetet ír a konzolra.
  + **azureml-core**
    + Lehetőség van az SAS URL-címének lekérésére a Storage-ban a Model objektumon keresztül. Pl.: Model. get_sas_url ()
    + Bemutatjuk `run.get_details()['datasets']` az elküldött futtatáshoz társított adatkészletek beolvasását
    + Adja hozzá `Dataset.Tabular.from_json_lines_files` az API-t, hogy TabularDataset hozzon létre a JSON-sorok fájljaiból. A TabularDataset https://aka.ms/azureml-data JSON-sorok fájljaiban található táblázatos adatokat a dokumentációban találhatja meg.
    + További virtuálisgép-méret mezők (operációsrendszer-lemez, GPU-k száma) lettek hozzáadva a supported_vmsizes () függvényhez
    + További mezők lettek hozzáadva a list_nodes () függvényhez a Futtatás, a privát és a nyilvános IP-cím, a port stb. megjelenítéséhez.
    + Új mező megadásának lehetősége a fürt üzembe helyezése során – remotelogin_port_public_access amely engedélyezhető vagy letiltható attól függően, hogy a fürt létrehozásakor nyitva vagy le szeretné-e hagyni az SSH-portot. Ha nem adja meg, a szolgáltatás intelligensen megnyitja vagy lezárta a portot attól függően, hogy egy VNet belül telepíti a fürtöt.
  + **azureml – magyarázat – modell**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Lehetőség van az SAS URL-címének lekérésére a Storage-ban a Model objektumon keresztül. Pl.: modell. [get_sas_url ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Futtassa a parancsot. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)["adatkészletek"] az elküldött futtatáshoz társított adatkészletek lekéréséhez
    + API `Dataset.Tabular`hozzáadása. [from_json_lines_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) TabularDataset létrehozásához JSON-sorok fájljaiból. A TabularDataset https://aka.ms/azureml-data JSON-sorok fájljaiban található táblázatos adatokat a dokumentációban találhatja meg.
    + További virtuálisgép-méret mezők (operációsrendszer-lemez, GPU-k száma) lettek hozzáadva a [supported_vmsizes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) függvényhez
    + További mezők lettek hozzáadva a [list_nodes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) függvényhez a Futtatás, a privát és a nyilvános IP-cím, a port stb. megjelenítéséhez.
    + Új mező megadásának lehetősége a fürt [üzembe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` helyezése során, amely beállítható úgy, hogy engedélyezve vagy Letiltva legyen attól függően, hogy a fürt létrehozásakor nyitva vagy lezártként szeretné-e hagyni az SSH-portot. Ha nem adja meg, a szolgáltatás intelligensen megnyitja vagy lezárta a portot attól függően, hogy egy VNet belül telepíti a fürtöt.
  + **azureml – magyarázat – modell**
    + Továbbfejlesztett dokumentáció a besorolási forgatókönyv magyarázatának eredményeiről.
    + A kiértékelési példákban az előre jelzett y értékek feltöltésére is lehetőség van. További hasznos vizualizációk feloldása.
    + A magyarázatot megadó tulajdonság hozzáadva a MimicWrapper-hez, amely lehetővé teszi az alapul szolgáló MimicExplainer beszerzését.
  + **azureml-pipeline-core**
    + Hozzáadott jegyzetfüzet a modul, a ModuleVersion és a ModuleStep leírásához
  + **azureml-pipeline-steps**
    + A RScriptStep hozzáadva a pénzmosás-folyamaton keresztüli R-szkriptek támogatásához.
    + A AzureBatchStep olyan rögzített metaadat-paramétereket elemez, amely a következő hibaüzenetet okozta: "a SubscriptionId paraméterhez nincs megadva a hozzárendelés."
  + **azureml-Train-automl**
    + Támogatott training_data, validation_data, label_column_name, weight_column_name adatbevitel formátuma
    + Elavult üzenet hozzáadva a explain_model () és a retrieve_model_explanations () rendszerhez
  + **[azureml – folyamat – mag](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Hozzáadott egy [jegyzetfüzetet](https://aka.ms/pl-modulestep) a [modul](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), a [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) és a [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)leírásához.
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + A [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) hozzáadva a pénzmosás-folyamaton keresztüli R-szkriptek támogatásához.
    + A [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) -elemzésben rögzített metaadat-paraméterek a következő hibaüzenetet okozzák: "a SubscriptionId paraméter nem lett megadva.
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Támogatott training_data, validation_data, label_column_name weight_column_name adatbevitel formátuma.
    + Elavult üzenet lett hozzáadva a [explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) és a [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)rendszerhez.


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK a Python v 1.0.62

+ **Új funkciók**
  + Bevezette a `timeseries` tulajdonságot a TabularDataset. Ez a funkció lehetővé teszi, hogy a TabularDataset egyszerű időbélyeg-szűrést biztosítson, például egy adott időtartam vagy a legfrissebb adatmennyiség között. Ha többet szeretne megtudni a `timeseries` TabularDataset található tulajdonságról, tekintse meg https://aka.ms/azureml-data a https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb dokumentációt vagy egy jegyzetfüzetet.
  + TabularDataset-és FileDataset-alapú betanítás. Nyisson meg https://aka.ms/dataset-tutorial egy példát a jegyzetfüzetre.

  + **azureml-train-core**
      + Hozzáadás `Nccl` és `Gloo` támogatás a PyTorch kalkulátorban

+ **Hibajavítások és javítások**
  + **azureml-automl-core**
    + A (z) "lag_length" AutoML-beállítás és a LaggingTransformer elavult.
    + A bemeneti adatok helyes érvényesítésének javítása, ha adatfolyam formátumban vannak megadva
    + Módosította a fit_pipeline. a Graph-ot a gráf JSON létrehozásához és az összetevőkre való feltöltéséhez.
    + A gráf a `userrun` használatával `Cytoscape`lett megjelenítve.
  + **azureml-core**
    + Tekintse át a kivételt az ADB-kódban, és végezze el a módosítást új hibakezelés esetén
    + Automatikus MSI-hitelesítés hozzáadva a notebook virtuális gépekhez.
    + Kijavítja a hibát, ha a hibás újrapróbálkozások miatt a sérült vagy üres modelleket fel lehet tölteni.
    + Kijavítva a hiba `DataReference` , ahol a név `DataReference` megváltozik, amikor a mód megváltozik ( `as_upload`pl `as_download`. hívás `as_mount`,, vagy).
    + `mount_point` A és `target_path` `FileDataset.download`a `FileDataset.mount` használata nem kötelező.
    + Kivétel az időbélyegző-oszlop nem található, ha az idősoros kapcsolódó API-t a rendszer az időbélyegző oszlop kiosztása nélkül hívja meg, vagy a hozzárendelt időbélyeg-oszlopok el lesznek dobva.
    + Az idősoros oszlopokat olyan oszlophoz kell rendelni, amelynek a típusa dátum, ellenkező esetben a rendszer kivételt vár.
    + A (z) "with_timestamp_columns" API-t hozzárendelő idő-sorozatszámok egyike sem lehet egy értékre vonatkozó apró/durva időbélyeg-oszlop neve, amely törli a korábban hozzárendelt időbélyeg-oszlopokat
    + Kivétel keletkezik, ha a durva gabona vagy a részletes időbélyeg-oszlop el van dobva, és jelzi, hogy az eldobásra kerülő felhasználó megszakította az időbélyegző with_time_stamp oszlop kizárása
    + Kivétel keletkezik, ha a durva vagy a részletes időbélyeg-oszlop nem szerepel az oszlopok megtartása listán, és a felhasználók megtartása után megadható, hogy a megőrzési időbélyegzőt tartalmazó with_time_stamp oszlopot is beleértve
    + Egy regisztrált modell méretének naplózása hozzáadva.
  + **azureml – magyarázat – modell**
    + Rögzített figyelmeztetés kinyomtatva a konzolra, ha a "csomagolás" Python-csomag nincs telepítve: "a lightgbm támogatott verziójának régebbi verziója esetén frissítsen a 2.2.1-nél nagyobb verzióra"
    + Rögzített letöltési modell magyarázata a globális magyarázatok számos funkcióval
    + A rögzített utánozó-magyarázat hiányzó inicializálási példákat tartalmaz a kimeneti magyarázaton
    + Rögzített megváltoztathatatlan hiba történt a tulajdonságok beállításakor, ha két különböző típusú modellt használ a magyarázó ügyféllel való feltöltéshez
    + Egy get_raw paramétert adott hozzá a magyarázathoz. magyarázza el (), hogy az egyik pontozási elmagyarázó a mesterséges és a nyers értékeket is visszaadja.
  + **azureml-Train-automl**
    + Nyilvános API-kat vezetett be a AutoML-ből `automl` , hogy támogassa a magyarázatot az SDK-ból – újabb módszer a AutoML-magyarázatok támogatásához a AutoML-featurization leválasztásával, valamint az SDK-val integrált, a AZUREML magyarázó SDK-k AutoML-modellekkel kapcsolatos,
    + Azureml eltávolítása – Alapértelmezések a távoli képzési környezetekben.
    + A FileCacheStore alapján módosította az alapértelmezett gyorsítótár-tároló helyét, hogy a AzureFileCacheStore egy AutoML Azure Databricks a kód elérési útján.
    + A bemeneti adatok helyes érvényesítésének javítása, ha adatfolyam formátumban vannak megadva
  + **azureml-train-core**
    + Source_directory_data_store elavulttá vált.
    + A azureml telepített csomag verzióinak felülbírálásának lehetősége.
    + Docker-támogatás hozzáadva a `environment_definition` paraméterben a becslések-ben.
    + Egyszerűsített elosztott tanítási paraméterek a becslések-ben.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Új webes felület (előzetes verzió) Azure Machine Learning munkaterületekhez
Az új webes felület lehetővé teszi az adatszakértők és adatmérnökök számára, hogy teljes körű gépi tanulási életciklust prepping, és megjelenítsék az adatok megjelenítését a modellek betanításához és üzembe helyezéséhez egyetlen helyen.

![Azure Machine Learning munkaterület felhasználói felülete (előzetes verzió)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Főbb funkciók:**

Az új Azure Machine Learning felület használatával mostantól a következőket teheti:
+ Jegyzetfüzetek kezelése vagy Jupyter
+ [Automatizált ML-kísérletek futtatása](tutorial-first-experiment-automated-ml.md)
+ [Adatkészletek létrehozása helyi fájlokból, adattárokból, & webfájlokból](how-to-create-register-datasets.md)
+ Ismerkedjen meg & adatkészletek előkészítésével a modell létrehozásához
+ A modellek adateltolódásának monitorozása
+ Legutóbbi erőforrások megtekintése az irányítópultról

A jelen kiadás időpontjában a következő böngészők támogatottak: Chrome, Firefox, Safari és Microsoft Edge preview.

**Ismert problémák:**

1. Frissítse a böngészőt, ha "hiba történt". Hiba történt az adathalmaz fájljainak betöltésekor, ha a központi telepítés folyamatban van.

1. Nem lehet törölni vagy átnevezni a fájlt jegyzetfüzetekben és fájlokban. A nyilvános előzetes verzió során a Jupyter felhasználói felületét vagy a terminált használhatja a notebook virtuális gépen a frissítési fájl műveleteinek elvégzéséhez. Mivel ez egy csatlakoztatott hálózati fájlrendszer, a notebook virtuális gépen végzett módosítások azonnal megjelennek a notebook munkaterületen.

1. SSH-t a notebook VM-be:
   1. Keresse meg a virtuális gép beállítása során létrehozott SSH-kulcsokat. Vagy keresse meg a kulcsokat a Azure Machine Learning munkaterületen > nyissa meg a számítási lapot, > keresse meg a jegyzetfüzet virtuális gépet a listában > nyissa meg a tulajdonságokat: másolja a kulcsokat a párbeszédpanelről.
   1. Importálja ezeket a nyilvános és titkos SSH-kulcsokat a helyi gépre.
   1. Az SSH-t a notebook VM-be használhatja.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK a Python v 1.0.60

+ **Új funkciók**
  + Bevezetett FileDataset, amely egy vagy több fájlra hivatkozik az adattárban vagy a nyilvános URL-címeken. A fájlok formátuma bármilyen lehet. A FileDataset lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számítási feladatokhoz. A FileDataset kapcsolatos további információkért tekintse https://aka.ms/file-datasetmeg a következőt:.
  + Hozzáadott folyamat YAML-támogatás a PythonScript lépéshez, ADla lépés, Databricks lépés, DataTransferStep és AzureBatch lépés

+ **Hibajavítások és javítások**
  + **azureml-automl-core**
    + A AutoArima mostantól az előzetes verzióra vonatkozó, javasolt folyamat.
    + Továbbfejlesztett hibajelentés az előrejelzéshez.
    + Az előrejelzési feladatokban az általános helyett egyéni kivételek használatával javította a naplózást.
    + Az ellenőrzési max_concurrent_iterations nem lehet kisebb, mint az ismétlések teljes száma.
    + A AutoML modellek mostantól AutoMLExceptions adnak vissza
    + Ez a kiadás javítja az automatizált gépi tanulás helyi futtatásának végrehajtási teljesítményét.
  + **azureml-core**
    + Adja meg az adatkészlet. get_all (munkaterület) bevezetését, `TabularDataset` amely `FileDataset` a regisztrációs nevük alapján a szótárt és az objektumokat adja vissza.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + A `parition_format` és `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files`a argumentumként be kell jelentkeznie. Az egyes adatútvonalak partíciós adatait a rendszer a megadott formátum alapján oszlopokra bontja ki. a (z) "{column_name}" karakterlánc-oszlopot hoz létre, és a (z) {column_name: ÉÉÉÉ/HH/NN/HH/PP/SS} létrehoz datetime oszlopot, ahol az "éééé", a "hh", a "HH", a "mm" és az "SS A partition_format a fájl elérési útjának végéig kell kezdenie az első partíciós kulcs pozíciójában. Például a következő elérési út miatt: ".. /USA/2019/01/01/data.csv ", ahol a partíció ország és idő szerint van, partition_format ="/{Country}/{PartitionDate: éééé/hh/nn}/Value. csv "karakterláncot hoz létre az" ország "értékkel, amelynek értéke" USA ", a DateTime oszlop pedig" PartitionDate ", értéke" 2019-01-01 ".
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + A `partition_format` és `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files`a argumentumként be kell jelentkeznie. Az egyes adatútvonalak partíciós adatait a rendszer a megadott formátum alapján oszlopokra bontja ki. a (z) "{column_name}" karakterlánc-oszlopot hoz létre, és a (z) {column_name: ÉÉÉÉ/HH/NN/HH/PP/SS} létrehoz datetime oszlopot, ahol az "éééé", a "hh", a "HH", a "mm" és az "SS A partition_format a fájl elérési útjának végéig kell kezdenie az első partíciós kulcs pozíciójában. Például a következő elérési út miatt: ".. /USA/2019/01/01/data.csv ", ahol a partíció ország és idő szerint van, partition_format ="/{Country}/{PartitionDate: éééé/hh/nn}/Value. csv "karakterláncot hoz létre az" ország "értékkel, amelynek értéke" USA ", a DateTime oszlop pedig" PartitionDate ", értéke" 2019-01-01 ".
    + `to_csv_files`és `to_parquet_files` metódusok lettek hozzáadva a `TabularDataset`következőhöz:. Ezek a módszerek lehetővé teszik az `TabularDataset` a és `FileDataset` a a közötti konverziót, ha az adatfájlokat a megadott formátumú fájlokra konvertálja.
    + Automatikusan bejelentkezik az alaprendszerkép beállításjegyzékbe a Model. package () által generált Docker mentésekor.
    + a (z) "gpu_support" már nem szükséges; A pénzmosás mostantól automatikusan észleli és használja az NVIDIA Docker-bővítményt, ha elérhető. Egy későbbi kiadásban el lesz távolítva.
    + Támogatás hozzáadva a PipelineDrafts létrehozásához, frissítéséhez és használatához.
    + Ez a kiadás javítja az automatizált gépi tanulás helyi futtatásának végrehajtási teljesítményét.
    + A felhasználók a futtatási előzmények alapján tudják lekérdezni a metrikákat.
    + Az előrejelzési feladatokban az általános helyett egyéni kivételek használatával javította a naplózást.
  + **azureml – magyarázat – modell**
    + Feature_maps paraméter hozzáadva az új MimicWrapper, amely lehetővé teszi a felhasználók számára a nyers funkciók magyarázatának beolvasását.
    + Az adatkészlet-feltöltések mostantól alapértelmezés szerint ki vannak kapcsolva a magyarázat feltöltéséhez, és újra engedélyezhetők upload_datasets = true értékkel
    + A rendszer hozzáadta a "is_law" paramétert a magyarázatok listájához és a letöltési függvényekhez.
    + Metódust `get_raw_explanation(feature_maps)` ad a globális és a helyi magyarázó objektumokhoz is.
    + Hozzáadott verzió-ellenőrzési lightgbm a nyomtatott figyelmeztetéssel, ha az alábbi támogatott verzió
    + Optimalizált memória használata
    + A AutoML modellek mostantól AutoMLExceptions adnak vissza
  + **azureml – folyamat – mag**
    + A PipelineDrafts létrehozásához, frissítéséhez és használatához hozzáadva támogatott a megváltoztathatatlan folyamat-definíciók fenntartása és interaktív módon történő futtatása
  + **azureml-Train-automl**
    + A szolgáltatás a GPU-kompatibilis pytorch v 1.1.0, :::no-loc text="cuda"::: Toolkit 9,0, pytorch-Transformers speciális verzióinak telepítésére szolgál, amely a Bert/XLNet a távoli Python futtatókörnyezeti környezetben való engedélyezéséhez szükséges.
  + **azureml-train-core**
    + Néhány hiperparaméter-hely definíciós hibájának korai meghibásodása közvetlenül az SDK-ban a kiszolgálóoldali helyett.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.14
+ **Hibajavítások és javítások**
  + A ADLS/ADLSGen2 írásának engedélyezése a nyers elérési út és a hitelesítő adatok használatával.
  + Kijavított egy hibát, `include_path=True` amely miatt nem működött `read_parquet`.
  + A `to_pandas_dataframe()` (z) "Érvénytelen tulajdonságérték: hostSecret" kivétel okozta rögzített hiba.
  + Kijavítva egy hiba, ahol a DBFS Spark módban nem olvashatók a fájlok.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK a Python v 1.0.57
+ **Új funkciók**
  + Engedélyezve `TabularDataset` , hogy az AutomatedML felhasználható legyen. Ha többet szeretne megtudni `TabularDataset`a szolgáltatásról https://aka.ms/azureml/howto/createdatasets, látogasson el ide.

+ **Hibajavítások és javítások**
  + **automl-Client-Core-nativeclient**
    + Kijavítva a hiba, amely akkor következik be, amikor a betanítási és/vagy ellenőrzési címkéket (y és y_valid) a pandák dataframe, de nem NumPy tömb formájában adták meg.
    + Frissített illesztőfelület a létrehozásához `RawDataContext` , hogy csak az adatértéket `AutoMLBaseSettings` és az objektumot kelljen megadni.
    +  Annak engedélyezése, hogy a AutoML-felhasználók eldobják a betanítási sorozatot, amely nem elég hosszú az előrejelzés során. – Lehetővé teszi, hogy a AutoML-felhasználók eldobják azokat a teszt-készletből, amelyek nem szerepelnek az előrejelzés során a betanítási készletben.
  + **Azure-CLI-ml**
    + Most már frissítheti a TLS/SSL-tanúsítványt az AK-fürtön üzembe helyezett pontozási végponthoz a Microsoft által generált és az ügyfél-tanúsítvány esetében is.
  + **azureml-automl-core**
    + Javítva lett egy probléma a AutoML, ahol a hiányzó címkékkel rendelkező sorok nem lettek megfelelően eltávolítva.
    + Javított hibák naplózása a AutoML-ben; a rendszer mindig a naplófájlba írja a teljes hibaüzeneteket.
    + A AutoML frissítette a csomagjának rögzítését `azureml-explain-model`, amely `azureml-dataprep`tartalmazza `azureml-defaults`a, a és a. A AutoML nem fog figyelmeztetni a csomagok eltérésére (kivéve a `azureml-train-automl` csomagot).
    + Kijavítva egy olyan `timeseries` problémát, amelyben a KtgE felosztások nem egyenlőek, mert a Raktárhelyek kiszámítása nem sikerül.
    + Ha a többszörös ellenőrzési betanítási típushoz a Ensemble-iterációt futtatja, akkor a teljes adatkészletre betanított modellek letöltése nem sikerül, a modell súlyozása és a szavazási Ensemble-ba betáplált modellek között inkonzisztens volt.
    + Kijavítva a hiba, amely akkor következik be, amikor a betanítási és/vagy ellenőrzési címkéket (y és y_valid) a pandák dataframe, de nem NumPy tömb formájában adták meg.
    + Kijavítva az előrejelzési feladatokkal kapcsolatos problémát, ha a bemeneti táblák logikai oszlopaiban sem történt a hiba.
    + Annak engedélyezése, hogy a AutoML-felhasználók eldobják a betanítási sorozatot, amely nem elég hosszú az előrejelzés során. – Lehetővé teszi, hogy a AutoML-felhasználók eldobják azokat a teszt-készletből, amelyek nem szerepelnek az előrejelzés során a betanítási készletben.
  + **azureml-core**
    + A blob_cache_timeout paraméter megrendelésével kapcsolatos probléma kijavítva.
    + A külső illesztési és átalakítási kivételek rendszerhibákhoz lettek adva.
    + Key Vault-titkok támogatása távoli futtatásokhoz. Vegyen fel egy azureml. Core. kulcstartó. kulcstartó osztályt a munkaterülethez tartozó kulcstartó hozzáadásához, beolvasásához és listázásához. A támogatott műveletek a következők:
      + azureml. Core. Workspace. Workspace. get_default_keyvault ()
      + azureml. Core. kulcstartó. kulcstartó. set_secret (név, érték)
      + azureml. Core. kulcstartó. kulcstartó. set_secrets (secrets_dict)
      + azureml. Core. kulcstartó. kulcstartó. get_secret (név)
      + azureml. Core. kulcstartó. kulcstartó. get_secrets (secrets_list)
      + azureml. Core. kulcstartó. kulcstartó. list_secrets ()
    + További módszerek az alapértelmezett kulcstartó beszerzéséhez és a titkos kulcsok lekéréséhez a távoli futtatás során:
      + azureml. Core. Workspace. Workspace. get_default_keyvault ()
      + azureml. Core. Run. Run. get_secret (név)
      + azureml. Core. Run. Run. get_secrets (secrets_list)
    + További felülbírálási paraméterek lettek hozzáadva a Submit-HyperDrive CLI-parancshoz.
    + Az API-hívások megbízhatóságának növelése a gyakori kérések könyvtárbeli kivételekre való ismételt próbálkozások kiterjesztése.
    + A futtatások küldésének támogatása egy elküldött futtatásból.
    + A FileWatcher-ben rögzített lejáró SAS-token probléma, amely miatt a fájlok a kezdeti jogkivonat lejárta után le lettek töltve.
    + Támogatott a HTTP CSV/TSV fájlok importálása a DataSet Python SDK-ban.
    + A munkaterület. Setup () metódus elavult. A felhasználók számára megjelenített figyelmeztető üzenet a Create () vagy a Get ()/from_config () függvényt javasolja.
    + Hozzáadott környezet. add_private_pip_wheel (), amely lehetővé teszi privát egyéni Python- `whl`csomagok feltöltését a munkaterületre, és biztonságosan használhatja őket a környezet létrehozásához és megvalósulása érdekében.
    + Most már frissítheti a TLS/SSL-tanúsítványt az AK-fürtön üzembe helyezett pontozási végponthoz a Microsoft által generált és az ügyfél-tanúsítvány esetében is.
  + **azureml – magyarázat – modell**
    + Paraméter hozzáadva a modell AZONOSÍTÓjának a feltöltéshez való hozzáadásához.
    + A `is_raw` címkézés a memóriában és a feltöltésben szereplő magyarázatokhoz lett hozzáadva.
    + Pytorch-támogatás és-tesztek hozzáadva a azureml-magyarázza-Model csomaghoz.
  + **azureml-opendatasets**
    + Az automatikus tesztelési környezet észlelése és naplózása.
    + Osztályok hozzáadásával az Egyesült államokbeli lakosságot a megye és a zip alapján szerezheti be.
  + **azureml – folyamat – mag**
    + A Label tulajdonság hozzáadva a bemeneti és a kimeneti port definícióhoz.
  + **azureml-telemetry**
    + Helytelen telemetria-konfiguráció lett kijavítva.
  + **azureml-Train-automl**
    + Kijavítva a hiba, ahol a telepítési hiba miatt nem sikerült bejelentkezni a "hibák" mezőbe a telepítési futtatáshoz, ezért a rendszer nem a szülő "errors" (hibák) alatt tárolja a hibát.
    + Javítva lett egy probléma a AutoML, ahol a hiányzó címkékkel rendelkező sorok nem lettek megfelelően eltávolítva.
    + Annak engedélyezése, hogy a AutoML-felhasználók eldobják a betanítási sorozatot, amely nem elég hosszú az előrejelzés során.
    + Annak engedélyezése, hogy a AutoML-felhasználók eldobják azokat a tesztelési készletből, amelyek nem szerepelnek az előrejelzés során a betanítási készletben.
    + A AutoMLStep mostantól `automl` áthalad a konfiguráción keresztül, hogy elkerülje az új konfigurációs paraméterek módosításainak vagy kiegészítéseinek esetleges problémáit.
    + A AutoML-adatGuardrail már nyilvános előzetes verzióban érhető el. A felhasználó az Guardrail jelentést (besorolási/regressziós feladatok esetén) fogja látni a képzés után, és az SDK API-n keresztül is elérhetővé teheti.
  + **azureml-train-core**
    + A Torch 1,2-támogatás hozzáadva a PyTorch kalkulátorhoz.
  + **azureml-widgets**
    + Továbbfejlesztett összetéveszthető mátrix-diagramok a besorolási képzéshez.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.12
+ **Új funkciók**
  + A sztringek listája mostantól a `read_*` metódusok bemenetként adható át.

+ **Hibajavítások és javítások**
  + A teljesítménye `read_parquet` jelentősen javult a Sparkban való futás során.
  + Kijavítva egy olyan `column_type_builder` hibát, amely nem egyértelmű dátumformátum esetén egyetlen oszlop esetében meghiúsult.

### <a name="azure-portal"></a>Azure Portal
+ **Előzetes verzió funkció**
  + A log és a output file streaming mostantól elérhető a futtatási részletek oldalain. A fájlok valós időben továbbítják a frissítéseket, amikor bekapcsolják az előnézeti kapcsolót.
  + A kvóta megadásának lehetősége a munkaterület szintjén előzetes verzióban érhető el. A AmlCompute-kvóták az előfizetés szintjén vannak lefoglalva, de mostantól lehetővé tesszük, hogy az adott kvótát a munkaterületek között, és a méltányos megosztáshoz és irányításhoz ossza ki. A munkaterület bal oldali navigációs sávján kattintson a **használat + kvóták** panelre, és válassza a **kvóták konfigurálása** fület. vegye figyelembe, hogy a kvóták a munkaterület szintjén való beállítása, mivel ez egy több-munkaterület művelet.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK a Python v 1.0.55

+ **Új funkciók**
  + A jogkivonat-alapú hitelesítés mostantól támogatott az AK-on üzembe helyezett pontozási végponton kezdeményezett hívásokban. Továbbra is támogatni fogjuk az aktuális kulcs alapú hitelesítést, és a felhasználók egyszerre használhatják ezeket a hitelesítési mechanizmusokat.
  + Lehetőség a virtuális hálózat (VNet) mögött lévő blob Storage adattárként való regisztrálására.

+ **Hibajavítások és javítások**
  + **azureml-automl-core**
    + Kijavít egy hibát, ahol a CV-felosztások érvényesítési mérete kicsi, és a regressziós és az előrejelzési eredményhez rossz előre jelzett és igaz diagramokat eredményez.
    + A távoli futtatások előrejelzési feladatainak naplózása továbbfejlesztett, mostantól a felhasználó átfogó hibaüzenetet kap, ha a Futtatás sikertelen volt.
    + Rögzített hibák, `Timeseries` ha az előfeldolgozás jelzője igaz.
    + Az előrejelzési adatellenőrzéssel kapcsolatos egyes hibaüzenetek további műveleteket végeznek.
    + Az adatkészletek ledobásával és/vagy lusta betöltésével a AutoML kevesebb memóriát használ, különösen a folyamatok között
  + **azureml – feltörzs – magyarázat – modell**
    + Model_task jelző hozzáadása a magyarázatokhoz, amely lehetővé teszi, hogy a felhasználó felülbírálja a modell típusának alapértelmezett automatikus következtetési logikáját
    + Widget-változások: automatikusan települ `contrib`a-val `nbextension` , és nincs több telepítés/engedélyezés – támogatás magyarázata csupán globális funkció fontosságával (pl. Permutative)
    + Irányítópult-változások: Box-mintaterületek és hegedű-mintaterületek az `beeswarm` összefoglalás oldalra való felvételen felül – sokkal gyorsabb a `beeswarm` "Top-k" csúszka módosítása – hasznos üzenet, amely leírja, hogy a Top-k milyen hasznos testreszabható üzeneteket tartalmaz a diagramok helyett, ha az adatok nincsenek megadva
  + **azureml-core**
    + A Model. package () metódussal olyan Docker-lemezképeket és Dockerfiles hozhat létre, amelyek a modelleket és a függőségeiket beágyazzák.
    + Frissített helyi webszolgáltatások a környezeti objektumokat tartalmazó InferenceConfigs fogadásához.
    + Fixed Model. Register () érvénytelen modelleket állít elő, ha "." (az aktuális könyvtár esetében) a model_path paraméterként lesz átadva.
    + Adja hozzá a Run. submit_child, a funkció-tükrözések kísérlet. Submit parancsot az elküldött gyermek futtatásának megadásakor.
    + A Model. register konfigurációs beállításainak támogatása a Run. register_modelban.
    + JAR-feladatok futtatása meglévő fürtön.
    + Most instance_pool_id és cluster_log_dbfs_path paraméterek támogatása.
    + Egy környezeti objektum használatának támogatása a modell webszolgáltatásba történő telepítésekor. A környezeti objektum most már megadható a InferenceConfig objektum részeként.
    + Új régiókhoz tartozó appinsifht-leképezés hozzáadása – CentralUS-westus-northcentralus
    + Dokumentáció hozzáadva az összes adattár osztály összes attribútumához.
    + Blob_cache_timeout paraméter hozzáadva `Datastore.register_azure_blob_container`a következőhöz:.
    + Save_to_directory és load_from_directory metódusok lettek hozzáadva a következőhöz: azureml. Core. environment. environment.
    + Hozzáadta az "az ml-környezet letöltése" és az "az ml-környezet regisztrálása" parancsokat a CLI-hez.
    + Hozzáadott környezet. add_private_pip_wheel metódus.
  + **azureml – magyarázat – modell**
    + Az adatkészlet nyomon követése az adatkészlet szolgáltatás (előzetes verzió) használatával magyarázatokkal bővült.
    + Csökkent az alapértelmezett köteg mérete, amikor a globális magyarázatot a 10k-ról 100-re.
    + Model_task jelzőt adott hozzá a magyarázatokhoz, hogy a felhasználó felülbírálja a modell típusának alapértelmezett automatikus következtetési logikáját.
  + **azureml-mlflow**
    + Kijavítva a hiba a mlflow. azureml. build_imageban, ahol a rendszer figyelmen kívül hagyja a beágyazott könyvtárakat.
  + **azureml-pipeline-steps**
    + A JAR-feladatok meglévő Azure Databricks-fürtön való futtatásának lehetősége hozzáadva.
    + A támogatási instance_pool_id és cluster_log_dbfs_path paraméterek hozzáadása a DatabricksStep lépéshez.
    + A DatabricksStep lépésben hozzáadta a folyamat paramétereinek támogatását.
  + **azureml-Train-automl**
    + Hozzáadva `docstrings` az Ensemble-hez kapcsolódó fájlokhoz.
    + A dokumentumok frissítése a megfelelő nyelvre `max_cores_per_iteration` és a`max_concurrent_iterations`
    + A távoli futtatások előrejelzési feladatainak naplózása továbbfejlesztett, mostantól a felhasználó átfogó hibaüzenetet kap, ha a Futtatás sikertelen volt.
    + Get_data eltávolítva a `automlstep` folyamat jegyzetfüzetből.
    + Megkezdődött `dataprep` a `automlstep`támogatása a alkalmazásban.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.10

+ **Új funkciók**
  + Most már kérheti az adott oszlopokra vonatkozó bizonyos ellenőrök (például hisztogram, pontdiagram stb.) futtatását is.
  + Egy integrálással argumentum hozzáadva `append_columns`a következőhöz:. Ha az értéke igaz, a rendszer a memóriába tölti be az adatmennyiséget, a végrehajtás pedig párhuzamosan fut. Ha a False (hamis), a végrehajtás streaming, de egyszálas lesz.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK a Python v 1.0.53

+ **Új funkciók**
  + Az automatizált Machine Learning mostantól támogatja a távoli számítási cél ONNX modelljeit
  + A Azure Machine Learning mostantól lehetővé teszi az előző futtatási, ellenőrzőpont-vagy modell-fájlok betanításának folytatását.
    + Megtudhatja, hogyan [használhatja a becslések-t egy korábbi futtatásból származó képzés folytatásához](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/training/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Hibajavítások és javítások**
  + **automl-Client-Core-nativeclient**
    + Javítsa ki a hibát az oszlopok típusának elvesztése után az átalakítás után (hiba összekapcsolva);
    + Annak engedélyezése, hogy y_query olyan objektumtípus legyen, amely a No (s) értéket tartalmazza a BEGIN (#459519) értéknél.
  + **Azure-CLI-ml**
    + A CLI-parancsok "modell telepítése" és "szolgáltatás frissítése" mostantól elfogadják a paramétereket, a konfigurációs fájlokat vagy a kettő kombinációját. A paraméterek elsőbbséget élveznek a fájlok attribútumaival szemben.
    + A modell leírása mostantól a regisztráció után is frissíthető
  + **azureml-automl-core**
    + NimbusML-függőség frissítése a 1.2.0 verziójára (aktuális legújabb).
    + Támogatás hozzáadása a NimbusML becslések & a AutoML-becslések belüli folyamatokhoz.
    + Az Ensemble kiválasztási eljárásában felmerült hiba, amely szükségtelenül bővült az eredményül kapott együttesnél, még akkor is, ha a pontszámok változatlanok maradnak.
    + Az előrejelzési feladatok elvégzése érdekében az egyes featurizations ismételt használatának engedélyezése. Ez felgyorsítja a telepítés futtatási idejét, nagyjából a költséges featurizations, például a késések és a folyamatos Windows-költségek n_cross_validationsának egy tényezője.
    + Probléma megoldása, ha az idő a Panda által támogatott időtartományon kívül esik. Most DataException, ha az idő kevesebb, mint PD. Timestamp. min vagy nagyobb, mint a PD. Timestamp. max
    + Az előrejelzés mostantól lehetővé teszi, hogy a vonatok és a tesztek különböző gyakorisággal legyenek igazítva. Például a "negyedéves kezdés januárban" és a "negyedévente kezdődően október" értékkel igazítható.
    + A "parameters" tulajdonság hozzá lett adva a TimeSeriesTransformer.
    + Távolítsa el a régi kivételi osztályokat.
    + Az előrejelzési feladatokban `target_lags` a paraméter mostantól egyetlen egész értéket, vagy egész számok listáját fogadja el. Ha az egész számot adta meg, csak egy késés lesz létrehozva. Ha egy lista van megadva, a rendszer a lemaradás egyedi értékeit veszi figyelembe. target_lags = [1, 2, 2, 4] egy, 2 és 4 időszakból álló késéseket fog létrehozni.
    + Javítsa ki a hibát az oszlopok típusának elvesztése után az átalakítás után (hiba összekapcsolva);
    + A `model.forecast(X, y_query)`(z) esetében engedélyezze, hogy y_query olyan objektumtípus legyen, amely a none (s) értéket tartalmazza a begin (#459519) elemnél.
    + Várt értékek hozzáadása a `automl` kimenethez
  + **azureml – datadrift**
    +  Többek között a jegyzetfüzetek fejlesztése, beleértve a azureml-opendatasets való váltást, a azureml-opendatasets és a teljesítmény javítását az adatgazdagítás során
  + **azureml – feltörzs – magyarázat – modell**
    + Rögzített átalakítások argumentum a LIME-magyarázathoz a nyers funkció fontosságának megállapításához a azureml-rel-detribal-magyarázza-Model csomag
    + Szegmentálások hozzáadása a képmagyarázatban a AzureML-a-detribal-magyarázza-Model csomaghoz
    + SciPy ritka támogatásának hozzáadása a LimeExplainer
    + Felvéve `batch_size` a magyarázó `include_local=False`elemre, amely a DecisionTreeExplainableModel végrehajtásának javításához szükséges kötegekben a globális magyarázatok továbbítására szolgál.
  + **azureml – featureengineering**
    + A (z) set_featurizer_timeseries_params hívásának javítása (): dict-értéktípus módosítása és null- `timeseries` beadás – jegyzetfüzet hozzáadása a Képtulajdonság
    + NimbusML-függőség frissítése a 1.2.0 verziójára (aktuális legújabb).
  + **azureml-core**
    + A DBFS-adattárolók csatlakoztatásának lehetősége a AzureML CLI-ben
    + Kijavítva a hiba az adattár feltöltésekor, ahol a rendszer üres `target_path` mappát hoz létre, ha az elindult`/`
    + Kijavítva `deepcopy` a probléma a ServicePrincipalAuthentication.
    + Hozzáadta az "az ml-környezet megjelenítése" és az "az ml környezet listázása" parancsot a parancssori felülethez.
    + A környezetek mostantól támogatják a base_dockerfile megadását egy már létrehozott base_image alternatívájaként.
    + A nem használt RunConfiguration-beállítás auto_prepare_environment elavultként van megjelölve.
    + A modell leírása mostantól a regisztráció után is frissíthető
    + Hibajavítás: a modell-és rendszerkép-törlés mostantól további információkat nyújt a felsőbb rétegbeli objektumok lekéréséről, amelyek attól függenek, hogy a törlés a felsőbb rétegbeli függőség miatt meghiúsul-e.
    + Kijavítva a hiba, amely üres időtartamot nyomtat a környezetekhez tartozó munkaterületek létrehozásakor előforduló központi telepítések esetén.
    + A továbbfejlesztett munkaterület a hibák kivételeit hozza létre. A felhasználók nem látják a munkaterület létrehozását. Nem található... " az üzenet helyett a tényleges létrehozási hiba jelenik meg.
    + Adja hozzá a jogkivonat-hitelesítés támogatását az AK webszolgáltatásokban.
    + Metódus `get_token()` hozzáadása `Webservice` objektumokhoz.
    + A CLI-támogatás hozzáadva a Machine learning-adatkészletek kezeléséhez.
    + `Datastore.register_azure_blob_container`most opcionálisan igénybe `blob_cache_timeout` vesz egy értéket (másodpercben), amely a blobfuse csatlakoztatási paramétereit konfigurálja a gyorsítótár lejáratának engedélyezéséhez az adattár számára. Az alapértelmezett érték nem időtúllépés, azaz a Blobok beolvasása esetén a helyi gyorsítótárban marad, amíg a művelet be nem fejeződik. A legtöbb feladat inkább ezt a beállítást fogja előnyben részesíteni, de egyes feladatokhoz több adatra van szükség egy nagy adatkészletből, mint amennyi a csomópontjaihoz fér. Ezekhez a feladatokhoz a paraméter finomhangolása segít a sikeres műveletben. Ügyeljen a paraméter finomhangolására: a túl alacsony érték beállítása gyenge teljesítményt eredményezhet, mivel a korszakban használt adat lejár, mielőtt ismét használni lehetne. Ez azt jelenti, hogy az összes olvasás a blob Storage-ból (azaz a hálózatból) történik, nem pedig a helyi gyorsítótárból, amely negatív hatással van a betanítási időpontokra.
    + A modell leírása mostantól a regisztráció után is megfelelően frissíthető
    + A modell-és képtörlés mostantól további információkat tartalmaz a rajtuk függő felsőbb rétegbeli objektumokról, ami miatt a törlés meghiúsul
    + A távoli futtatások erőforrás-kihasználtságának javítása a azureml. mlflow használatával.
  + **azureml – magyarázat – modell**
    + Rögzített átalakítások argumentum a LIME-magyarázathoz a nyers funkció fontosságának megállapításához a azureml-rel-detribal-magyarázza-Model csomag
    + SciPy ritka támogatásának hozzáadása a LimeExplainer
    + hozzáadott alakzat lineáris bemutató burkoló, valamint egy másik szint a táblázatos magyarázathoz a lineáris modellek elmagyarázása érdekében
    + az elmagyarázza a modell függvénytárában, rögzített hiba, ha include_local = false for ritka adatbevitel
    + várt értékek hozzáadása a `automl` kimenethez
    + a rögzített permutációs funkció fontossága, ha az átalakítások argumentuma a nyers funkció fontosságának beolvasására van megadva
    + felvéve `batch_size` a magyarázó `include_local=False`elemre, amely a DecisionTreeExplainableModel végrehajtásának javításához szükséges kötegekben a globális magyarázatok továbbítására szolgál.
    + a modell-magyarázó könyvtár, a rögzített tábla-magyarázatok, ahol a Panda dataframe input szükséges az előrejelzéshez
    + Kijavítva egy olyan `explanation.expected_values` hibát, amely néha lebegőpontos értéket ad vissza, nem pedig egy lebegőpontos listát.
  + **azureml-mlflow**
    + A mlflow teljesítményének javítása. set_experiment (experiment_name)
    + Javítsa ki a InteractiveLoginAuthentication for mlflow tracking_uri
    + A távoli futtatások erőforrás-kihasználtságának javítása a azureml. mlflow használatával.
    + Javítsa a azureml-mlflow csomag dokumentációját
    + Javítási hiba, ahol a mlflow. log_artifacts ("my_dir") az összetevők mentését a "my_dir/<ereklyét-paths>" helyett "<összetevő-elérési utak>"
  + **azureml-opendatasets**
    + A `pyarrow` régi `opendatasets` verziók (<0.14.0) rögzítése az újonnan bevezetett memória-probléma miatt.
    + Helyezze át a azureml-opendatasets-azureml-opendatasets.
    + Lehetővé teszi, hogy a nyitott adatkészlet-osztályok regisztrálva legyenek Azure Machine Learning munkaterületre, és zökkenőmentesen hasznosítsa a pénzmosás-adatkészlet képességeit.
    + Jelentősen javíthatja a NoaaIsdWeather-bővítési teljesítményt a nem SPARK verzióban.
  + **azureml-pipeline-steps**
    + A DBFS adattár mostantól támogatja a DatabricksStep bemeneteit és kimeneteit.
    + Frissített dokumentáció a Azure Batch lépéshez a bemenetek/kimenetek tekintetében.
    + A AzureBatchStep-ben módosult *delete_batch_job_after_finish* alapértelmezett értéke *true (igaz*).
  + **azureml-telemetry**
    +  Helyezze át a azureml-opendatasets-azureml-opendatasets.
    + Lehetővé teszi, hogy a nyitott adatkészlet-osztályok regisztrálva legyenek Azure Machine Learning munkaterületre, és zökkenőmentesen hasznosítsa a pénzmosás-adatkészlet képességeit.
    + Jelentősen javíthatja a NoaaIsdWeather-bővítési teljesítményt a nem SPARK verzióban.
  + **azureml-Train-automl**
    + A get_output frissített dokumentációja, amely a tényleges visszatérési típust tükrözi, és további megjegyzéseket biztosít a kulcs tulajdonságainak lekéréséhez.
    + NimbusML-függőség frissítése a 1.2.0 verziójára (aktuális legújabb).
    + várt értékek hozzáadása a `automl` kimenethez
  + **azureml-train-core**
    + A karakterláncok mostantól számítási célként vannak elfogadva az automatikus Hiperparaméter-hangoláshoz
    + A nem használt RunConfiguration-beállítás auto_prepare_environment elavultként van megjelölve.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.9

+ **Új funkciók**
  + A fájlok közvetlenül egy http-vagy HTTPS-URL-címről való olvasásának támogatása.

+ **Hibajavítások és javítások**
  + Továbbfejlesztett hibaüzenet, amikor egy távoli forrásból (amely jelenleg nem támogatott) származó parketta-adatkészletet próbál beolvasni.
  + Hiba javítva a parketta-fájlformátumba való íráskor a ADLS Gen 2 fájlban, és a ADLS Gen 2 tároló nevének frissítése az elérési úton.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Vizuális felület
+ **Előzetes verziójú funkciók**
  + Az "R-szkript végrehajtása" modul hozzáadva a Visual Interface-ben.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK a Python v 1.0.48

+ **Új funkciók**
  + **azureml-opendatasets**
    + **azureml – a opendatasets** már elérhető **azureml-opendatasets**néven. A régi csomag továbbra is működhet, de javasoljuk, hogy a **azureml-opendatasets** a szélesebb körű képességek és tökéletesítések érdekében használja.
    + Ez az új csomag lehetővé teszi, hogy a megnyitott adatkészleteket adatkészletként regisztrálja Azure Machine Learning munkaterületen, és kihasználja az adatkészlet által kínált funkciókat.
    + Emellett olyan meglévő képességeket is tartalmaz, mint például az Open-adatkészletek Panda/SPARK dataframes való használata, valamint egyes adathalmazok, például időjárási viszonyok elérésének helye.

+ **Előzetes verziójú funkciók**
    + A HyperDriveConfig mostantól elfogadhatja a folyamat objektumát paraméterként a hiperparaméter hangolásának támogatásához egy folyamat használatával.

+ **Hibajavítások és javítások**
  + **azureml-Train-automl**
    + Kijavítva a hiba az oszlopok típusának elvesztése után az átalakítás után.
    + Kijavítva a hiba, hogy a y_query olyan objektumtípus legyen, amely a none (ok) értéket tartalmazza az elején.
    + Kijavította a problémát a Ensemble kiválasztási eljárásában, amely szükségtelenül megnőtte az eredményül kapott Ensemble-t, még akkor is, ha a pontszámok állandó
    + Kijavítottuk a problémát a AutoMLStep whitelist_models és blacklist_models beállításaival.
    + Kijavítottuk a problémát, amely megakadályozta az előfeldolgozás használatát, ha az AutoML az Azure ML-folyamatok kontextusában használták volna.
  + **azureml-opendatasets**
    + Áthelyezett azureml-opendatasets-azureml-opendatasets.
    + Az engedélyezett nyitott adatkészlet-osztályok regisztrálva lesznek Azure Machine Learning munkaterületen, és zökkenőmentesen használhatják a pénzmosás-adatkészletek képességeit.
    + A NoaaIsdWeather továbbfejlesztett teljesítményének növelése a nem SPARK verzióban jelentős mértékben.
  + **azureml – magyarázat – modell**
    + Frissített online dokumentáció az értelmező objektumokhoz.
    + Hozzáadva `batch_size` a magyarázathoz, `include_local=False`ha a kötegekben a globális magyarázatok továbbítása érdekében a modell magyarázó DecisionTreeExplainableModel végrehajtásának javítását kívánja javítani.
    + Kijavítottuk a `explanation.expected_values` problémát, amely néha lebegőpontos értéket ad vissza, nem pedig egy lebegőpontos lista.
    + A rendszer az elvárt értékeket `automl` adta hozzá a modellezési magyarázathoz a modell könyvtárában.
    + A rögzített permutációs funkció fontossága, ha az átalakítások argumentuma a nyers funkció fontosságának beolvasására van megadva.
  + **azureml-core**
    + Hozzá lett adva a DBFS-adattárolók csatolása az AzureML CLI-ben.
    + Kijavítva az adattár feltöltésével kapcsolatos problémát, ha `target_path` a-vel indult, `/`üres mappát hoz létre.
    + Két adatkészlet engedélyezett összehasonlítása.
    + A modell és a rendszerkép törlése mostantól további információkat nyújt a felsőbb rétegbeli objektumok lekéréséről, amelyek attól függenek, hogy a törlés a felsőbb rétegbeli függőség miatt meghiúsul-e.
    + A auto_prepare_environment nem használt RunConfiguration-beállítás elavult.
  + **azureml-mlflow**
    + A azureml. mlflow-t használó távoli futtatások továbbfejlesztett Erőforrás-kihasználtsága.
    + Javítottuk a azureml-mlflow csomag dokumentációját.
    + Kijavítva a probléma, amelyben a mlflow. log_artifacts ("my_dir") a "my_dir/artifact-paths" alatt az "összetevő-útvonalak" helyett az összetevők mentését tenné.
  + **azureml-pipeline-core**
    + Az összes folyamat lépéseihez hash_paths paraméter elavult, és a jövőben el lesz távolítva. A source_directory alapértelmezett tartalma kivonattal van elkészítve (kivéve a. amlignore vagy. gitignore fájlban felsorolt fájlokat)
    + Folyamatosan fejlesztheti a modul-és ModuleStep a számítási típusokra vonatkozó speciális modulok támogatásához, hogy felkészüljön a RunConfiguration-integrációra és egyéb változásokra a számítási típus meghatározott modul-használatának feloldásához a folyamatokban.
  + **azureml-pipeline-steps**
    + AzureBatchStep: továbbfejlesztett dokumentáció a bemenetek/kimenetek tekintetében.
    + AzureBatchStep: módosult delete_batch_job_after_finish alapértelmezett értéke TRUE (igaz).
  + **azureml-train-core**
    + A karakterláncok mostantól számítási célként vannak elfogadva az automatikus Hiperparaméter-hangoláshoz.
    + A auto_prepare_environment nem használt RunConfiguration-beállítás elavult.
    + Az elavult paraméterek `conda_dependencies_file_path` és `pip_requirements_file_path` a `conda_dependencies_file` és `pip_requirements_file` a mellett.
  + **azureml-opendatasets**
    + Jelentősen javíthatja a NoaaIsdWeather-bővítési teljesítményt a nem SPARK verzióban.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.8

+ **Új funkciók**
 + A adatfolyam objektumok mostantól megadhatók, és rekordokat hozhatnak létre. Tekintse meg `Dataflow.to_record_iterator`a dokumentációját.
  + A adatfolyam objektumok mostantól megadhatók, és rekordokat hozhatnak létre. Tekintse meg `Dataflow.to_record_iterator`a dokumentációját.

+ **Hibajavítások és javítások**
 + Növelte a Adatelőkészítés SDK megbízhatóságát.
 + A pandák DataFrames jobb kezelését a nem karakterlánc típusú oszlop indexekkel.
 + Javította az adatkészletek teljesítményét `to_pandas_dataframe` .
 + Kijavítva egy olyan hibát, amelyben az adathalmazok Spark-végrehajtása nem sikerült, ha több csomópontos környezetben futnak.
  + Növelte a Adatelőkészítés SDK megbízhatóságát.
  + A pandák DataFrames jobb kezelését a nem karakterlánc típusú oszlop indexekkel.
  + Javította az adatkészletek teljesítményét `to_pandas_dataframe` .
  + Kijavítva egy olyan hibát, amelyben az adathalmazok Spark-végrehajtása nem sikerült, ha több csomópontos környezetben futnak.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.7

Javítottunk egy olyan változást, amely javítja a teljesítményt, mivel a Azure Databrickst használó ügyfeleknél problémát okozott. Ha Azure Databricks problémába ütközött, az alábbi módszerek egyikével frissíthet a verzió 1.1.7:
1. Futtassa ezt a parancsfájlt a frissítéshez:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Hozza létre újra a fürtöt, amely telepíti a legújabb adat-előkészítési SDK-verziót.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK a Python v 1.0.45

+ **Új funkciók**
  + Döntési fa helyettesítő modell hozzáadása az azureml-magyarázza-Model csomagban található magyarázatok megjelenítéséhez
  + Lehetőség arra, hogy megadhatja a rendszerképekre telepítendő CUDA-verziót. A CUDA 9,0, 9,1 és 10,0 támogatása.
  + Az Azure ML betanítási alaplemezképekkel kapcsolatos információk mostantól elérhetők az [Azure ml containers GitHub-tárházban](https://github.com/Azure/AzureML-Containers) és a [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + CLI-támogatás hozzáadva a folyamat-ütemtervhez. További információért futtassa az "az ml pipeline-h" parancsot.
  + Egyéni Kubernetes névtér-paraméter lett hozzáadva az AK webszolgáltatások üzembe helyezési konfigurációjához és a CLI-hez.
  + Az összes folyamat lépéseinél elavult hash_paths paraméter
  + A Model. register mostantól támogatja a több egyedi fájl regisztrálását egyetlen modellként a `child_paths` (z) paraméter használatával.

+ **Előzetes verziójú funkciók**
    + A pontozási magyarázatok mostantól opcionálisan menthetik a Conda és a pip-adatokat a megbízhatóbb szerializálás és deszerializálás céljából.
    + Hibajavítás az automatikus funkció-választóhoz.
    + A mlflow. azureml. build_image frissítve az új API-val, az új implementáció által közzétett javított hibákkal.

+ **Hibajavítások és javítások**
  + Eltávolított `paramiko` függőség a azureml-Core-ból. Elavult figyelmeztetések jelennek meg az örökölt számítási cél csatolási módszereihez.
  + A futtatási teljesítmény javítása. create_children
  + A megmagyarázó bináris osztályozó használatával a rendszer kijavítja a valószínűségi sorrendet, ha a tanár valószínűsége az alakzat értékének méretezésére szolgál.
  + Továbbfejlesztett hibakezelés és üzenet az automatizált gépi tanuláshoz.
  + Kijavítottuk az automatikus gépi tanulásra vonatkozó iterációs időtúllépési problémát.
  + Továbbfejlesztettük az automatikus gépi tanulás idősorozat-átalakítási teljesítményét.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.6

+ **Új funkciók**
  + Az összegző függvények a Top Values`SummaryFunction.TOPVALUES`() és a bottom`SummaryFunction.BOTTOMVALUES`Values () függvényhez lettek hozzáadva.

+ **Hibajavítások és javítások**
  + Jelentősen javult a teljesítménye `read_pandas_dataframe`.
  + Kijavítva egy olyan hibát, `get_profile()` amely egy bináris fájlokra mutató adatfolyam okoz.
  + `set_diagnostics_collection()` Lehetővé teszi a telemetria-gyűjtemény programozott engedélyezését vagy letiltását.
  + Módosította a viselkedését `get_profile()`. A NaN értékeit a rendszer figyelmen kívül hagyja a min, a Mean, az STD és az Sum függvénynél, amely a pandák viselkedéséhez igazodik.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK a Python v 1.0.43

+ **Új funkciók**
  + A Azure Machine Learning mostantól biztosítja az első osztályú támogatást a népszerű gépi tanulási és adatelemzési keretrendszer Scikit – Learn. A [ `SKLearn` kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)használatával a felhasználók könnyedén betanítják és üzembe helyezhetik a Scikit-tanulási modelleket.
    + Ismerje meg, hogyan [futtathatja a hiperparaméter-hangolást a Scikit használatával – Ismerkedjen meg a HyperDrive segítségével](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + További támogatás a ModuleStep létrehozásához a folyamatokban, valamint a modul-és ModuleVersion osztályok használatával az újrafelhasználható számítási egységek kezeléséhez.
  + Az ACI-webszolgáltatások mostantól támogatják az állandó scoring_uri a frissítéseken keresztül. A scoring_uri IP-ről FQDN-re változik. A teljes tartománynévhez tartozó DNS-név címkéje konfigurálható úgy, hogy beállítja a dns_name_label deploy_configuration.
  + Automatikus gépi tanulás – új funkciók:
    + STL-Képtulajdonság az előrejelzéshez
    + A KMeans-fürtszolgáltatás engedélyezve van a szolgáltatások elsöprő számára
  + A AmlCompute-kvóták jóváhagyása csak gyorsabb volt! Most automatizáljuk a kvóta-kérelmek küszöbértéken belüli jóváhagyásának folyamatát. A kvóták működésével kapcsolatos további információkért tekintse meg a [kvóták kezelését](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)ismertető témakört.

+ **Előzetes verziójú funkciók**
    + Integráció a [MLflow](https://mlflow.org) 1.0.0 követésével azureml-MLflow-csomagon keresztül ([például jegyzetfüzetek](https://aka.ms/azureml-mlflow-examples)).
    + Jupyter-jegyzetfüzet küldése futtatásként. [API-referenciák dokumentációja](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Az [adatdrift-detektor](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) nyilvános előzetes verziója azureml-datadrift csomagon keresztül ([például jegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/monitor-models/data-drift)). Az adateltolódás az egyik legfontosabb ok, ahol a modell pontossága az idő múlásával csökken. Ez akkor történik meg, ha az éles modellbe való kiszolgált adatok eltérnek a modell által betanított adatoktól. A pénzmosás-adatdrift detektor segítségével az ügyfél figyelheti az adateltolódást, és riasztást küld, ha a rendszer a drift észlelését észleli

+ **Kompatibilitástörő változások**

+ **Hibajavítások és javítások**
  + A RunConfiguration betöltési és a mentési funkció támogatja a teljes elérési út megadását a korábbi működéshez szükséges teljes biztonsági mentéssel.
  + A ServicePrincipalAuthentication-ben hozzáadott gyorsítótárazási funkció alapértelmezés szerint ki van kapcsolva.
  + Több mintaterület naplózásának engedélyezése ugyanazzal a metrikai névvel.
  + A Model osztály mostantól megfelelően importálható a azureml. Core`from azureml.core import Model`() webhelyről.
  + A folyamat lépéseiben `hash_path` a paraméter már elavult. Az új viselkedés a teljes source_directory kivonatolása, kivéve a. amlignore vagy. gitignore fájlban felsorolt fájlokat.
  + A folyamat csomagjaiban a `get_all` különböző `get_all_*` és a metódusok a `list` és `list_*`a mellett elavulttá váltanak.
  + a azureml. Core. get_run már nem igénylik az osztályok importálását az eredeti futtatási típus visszaküldése előtt.
  + Kijavított egy hibát, amelyben a webszolgáltatások frissítéseinek meghívása nem váltott ki frissítést.
  + Az AK-webszolgáltatások pontozási időtúllépésének 5ms és 300000ms között kell lennie. A pontozási kérelmek maximális megengedett scoring_timeout_msa 1 perc és 5 perc között van.
  + A LocalWebservice objektumok már `scoring_uri` rendelkeznek `swagger_uri` és tulajdonságokkal rendelkeznek.
  + Áthelyezett kimenetek könyvtár létrehozása és kimenetek könyvtár feltöltése a felhasználói folyamatból. A futtatási előzmények SDK-t minden felhasználói folyamaton futtathatja. Ennek során meg kell oldania az elosztott képzések futtatásával kapcsolatos egyes szinkronizálási problémákat.
  + A felhasználói folyamat nevéből írt azureml-napló neve mostantól tartalmazza a folyamat nevét (csak elosztott képzés esetén) és a PID-t.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.5

+ **Hibajavítások és javítások**
  + A kétjegyű éves formátummal rendelkező, az érvényes évek tartománya frissült a Windows májusi kiadásának megfelelő értékekre. A tartomány 1930-2029 és 1950-2049 között lett módosítva.
  + Egy fájl és beállítás `handleQuotedLineBreaks=True`beolvasásakor a `\r` rendszer új sorként kezeli őket.
  + Kijavított egy hibát, `read_pandas_dataframe` amely bizonyos esetekben sikertelen volt.
  + Javult a `get_profile`teljesítménye.
  + Javított hibaüzenetek.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning adat PREP SDK v 1.1.4

+ **Új funkciók**
  + Mostantól a következő kifejezés nyelvi függvények használatával kinyerheti és elemezheti a DateTime értékeket új oszlopokra.
    + `RegEx.extract_record()`a DateTime elemek új oszlopba való kibontása.
    + `create_datetime()`datetime objektumokat hoz létre külön datetime elemből.
  + A hívásakor `get_profile()`láthatja, hogy a quantile oszlopai (EST.) szerint vannak megjelölve, hogy egyértelműen jelezze az értékek közelítését.
  + Mostantól a * * globbing is használhatja az Azure Blob Storage való olvasáskor.
    + emelkedés pl`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Hibajavítások**
  + Kijavított egy olyan hibát, amely a Parquet-fájlok távoli forrásból (Azure Blob) való olvasásával kapcsolatos.

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK a Python v 1.0.39
+ **Változások**
  + A konfigurációs auto_prepare_environment futtatása beállítás elavult, és az automatikus előkészítés válik az alapértelmezett értékre.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.3

+ **Új funkciók**
  + A PostgresSQL-adatbázisból való olvasás támogatása a read_postgresql meghívásával vagy adattár használatával.
    + Példák a útmutatókra:
      + [Adatfeldolgozási jegyzetfüzet](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Adattár-jegyzetfüzet](https://aka.ms/aml-data-prep-datastore-nb)

+ **Hibajavítások és javítások**
  + Az oszlop típusú konverzióval kapcsolatos hibák Javítva:
  + A most helyesen konvertál egy logikai vagy numerikus oszlopot egy logikai oszlopra.
  + Most nem sikerül, ha egy Date (dátum) típusú oszlopot kíván beállítani dátum típusként.
  + Továbbfejlesztett JoinType-típusok és kísérő dokumentáció. Két adatfolyamok való csatlakozáskor mostantól megadhatja az alábbi típusú csatlakozások egyikét:
    + NINCS, EGYEZÉS, BELSŐ, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Továbbfejlesztett adattípus, amely több dátumformátum felismerésére használható.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

A Azure Portal mostantól a következőket teheti:
+ Automatizált ML-kísérletek létrehozása és futtatása
+ Hozzon létre egy notebook virtuális gépet, hogy kipróbálja a minta Jupyter notebookokat vagy a sajátját.
+ Új szerzői műveletek szakasz (előzetes verzió) a Azure Machine Learning munkaterületen, amely magában foglalja az automatizált Machine Learning, a vizuális felületet és a szolgáltatott notebookos virtuális gépeket
    + Modell automatikus létrehozása automatizált gépi tanulás használatával
    + Drag and drop vizuális felület használata kísérletek futtatásához
    + Hozzon létre egy notebook virtuális gépet az adatelemzéshez, modellek létrehozásához és szolgáltatások telepítéséhez.
+ Élő diagram és metrika frissítése a jelentések futtatása és a részletek lapok futtatása
+ A naplófájlok, a kimenetek és a pillanatképek frissített fájlkezelője a futtatási részletek oldalain.
+ Új és továbbfejlesztett jelentés-létrehozási élmény a kísérletek lapon.
+ Lehetőség van a config. JSON fájl letöltésére a Azure Machine Learning munkaterület áttekintés lapján.
+ Azure Machine Learning munkaterület létrehozását a Azure Databricks munkaterületről.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK a Python v 1.0.33
+ **Új funkciók**
  + A _munkaterület. Create_ metódus most elfogadja az alapértelmezett FÜRTKONFIGURÁCIÓ a processzor-és GPU-fürtökhöz.
  + Ha a munkaterület létrehozása sikertelen, a függő erőforrások megtisztítása megtörténik.
  + Az alapértelmezett Azure Container Registry SKU alapértékre lett állítva.
  + A Azure Container Registry lustán jön létre, ha szükséges a futtatáshoz vagy a rendszerkép létrehozásához.
  + A betanítási környezetek támogatása.

### <a name="notebook-virtual-machine"></a>Notebook virtuális gép 

A notebookos virtuális gépek biztonságos, nagyvállalati használatra kész üzemeltetési környezetként használhatók Jupyter jegyzetfüzetekhez, ahol gépi tanulási kísérleteket végezhet, webvégpontként telepíthet modelleket, és elvégezheti az Azure Machine Learning SDK által támogatott összes egyéb műveletet a Python használatával.Számos képességet biztosít:
+ [Gyorsan elindíthat egy előre konfigurált jegyzetfüzet virtuális gépet](tutorial-1st-experiment-sdk-setup.md) , amely az Azure Machine learning SDK és a kapcsolódó csomagok legújabb verzióját tartalmazza.
+ A hozzáférést bizonyított technológiák, például HTTPS, Azure Active Directory hitelesítés és engedélyezés biztosítja.
+ A notebookok és kódok megbízható Felhőbeli tárolása a Azure Machine Learning-munkaterület blob Storage-fiókban.A notebook virtuális gépet biztonságosan törölheti a munka elvesztése nélkül.
+ Előtelepített minta-jegyzetfüzetek Azure Machine Learning funkciók megismeréséhez és kipróbálásához.
+ Az Azure-beli virtuális gépek teljes körű testreszabási képességei, bármilyen virtuálisgép-típus, bármely csomag és bármely illesztőprogram. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>A Azure Machine Learning SDK for Python v 1.0.33 megjelent.

+ Általánosan elérhető az Azure ML Hardware Accelerated Models a [FPGA](how-to-deploy-fpga-web-service.md) .
  + Mostantól [a azureml-extra gyorsulás-models csomagot is használhatja](how-to-deploy-fpga-web-service.md) a következőhöz:
    + Egy támogatott Deep neurális hálózat (ResNet 50, ResNet 152, DenseNet-121, VGG-16 és SSD-VGG) súlyozásának betanítása
    + Az adatátviteli tanulás használata a támogatott DNN
    + A modell regisztrálása modellkezelés szolgáltatással és a modell tárolóba helyezése
    + A modell üzembe helyezése Azure-beli virtuális gépen egy Azure Kubernetes Service-(ak-) fürt FPGA
  + A tároló üzembe helyezése egy [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) kiszolgáló eszközön
  + Adja meg az adatait a gRPC-végponttal ezzel a [mintával](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizált gépi tanulás

+ A funkciók dinamikus hozzáadásának :::no-loc text="featurizers"::: engedélyezése a teljesítmény optimalizálása érdekében. Új :::no-loc text="featurizers":::: a munkahelyi beágyazások, a bizonyítékok súlya, a célzott kódolások, a szöveges cél kódolása, a fürt távolsága
+ Intelligens önéletrajz a betanítási/érvényes osztások automatikus ML-ben való kezeléséhez
+ Néhány memória-optimalizálási módosítás és futásidejű teljesítmény javítása
+ Teljesítmény javítása a modellben – magyarázat
+ ONNX-modell átalakítása helyi futtatáshoz
+ Almintavételezési támogatás hozzáadva
+ Intelligens Leállítás, ha nincs megadva kilépési feltétel
+ Halmozott együttesek

+ Idősoros előrejelzés
  + Új prediktív előrejelzési függvény
  + Mostantól használhatja az idősorozat-adatelemzési időszakon belüli többit is
  + Az idősorozat-elmaradás konfigurálásához hozzáadott új funkciók
  + Új funkciókkal bővült a működés közbeni ablak összesített funkcióinak támogatása
  + Új üdülés észlelése és Képtulajdonság, ha az országkód definiálva van a kísérleti beállításokban

+ Azure Databricks
  + Az idősorozat-előrejelzés és a modell explainabilty/értelmező képességének engedélyezése
  + Mostantól megszakíthatja és folytathatja a (folytatás) automatizált ML-kísérletek elvégzését
  + Többprocesszoros feldolgozás támogatása

### <a name="mlops"></a>MLOps
+ **Helyi üzembe helyezési & hibakeresése pontozási tárolók esetén**<br/> Most már helyileg is üzembe helyezhet egy ML-modellt, és gyorsan megismételheti a pontozási fájlt és a függőségeket, hogy a várt módon viselkedjenek.

+ **Bevezetett InferenceConfig & Model. Deploy ()**<br/> A modell üzembe helyezése mostantól támogatja a forrás mappa megadását egy bejegyzési parancsfájllal, ugyanazokat a RunConfig.  Emellett a modell üzembe helyezését egyetlen parancsra is egyszerűsítettük.

+ **Git-hivatkozások követése**<br/> Az ügyfelek egy ideig alapszintű git-integrációs képességeket igényelnek, mivel ez segít a teljes körű naplózás fenntartásában. Az Azure ML-ben a git-hez kapcsolódó metaadatok (tárház, véglegesítés, tiszta állapot) tekintetében a főbb entitások nyomon követését valósították meg. Az SDK és a CLI automatikusan összegyűjti ezeket az információkat.

+ **Modell profilkészítés & érvényesítési szolgáltatás**<br/> Az ügyfelek gyakran kifogásolják a következtetési szolgáltatáshoz kapcsolódó számítási kapacitás mértékét. A Model profilkészítési szolgáltatás segítségével az ügyfél minta bemeneteket biztosíthat, és 16 különböző CPU-/memória-konfigurációban dolgozunk fel, hogy a telepítés optimális méretezése megtörténjen.

+ **Saját alaprendszerkép használata a következtetéshez**<br/> Egy másik gyakori panasz volt a nehézség a kísérletezéstől az ismételt megosztási függőségekre való áttérés során. Az új alaprendszerkép-megosztási képességgel most már felhasználhatja a kísérletezési alapképeket, a függőségeket és az összes, a következtetést. Ennek fel kell gyorsítania az üzemelő példányokat, és csökkentenie kell a belső és a külső hurok közötti hézagot.

+ **Továbbfejlesztett hencegő séma-létrehozási élmény**<br/> A korábbi hencegés-létrehozási módszer volt a hiba, és nem lehet automatizálni. Új, beágyazott módon hozhat létre a kisegítő sémákat bármely Python-függvényből a dekoratőrs használatával. Nyílt forráskódú ez a kód, és a séma generálási protokollja nem párosul az Azure ML platformmal.

+ **A Azure ML CLI általánosan elérhető (GA)**<br/> A modellek mostantól egyetlen CLI-paranccsal is üzembe helyezhetők. Általános vásárlói visszajelzésünk szerint a Jupyter-jegyzetfüzetből senki sem telepít ML modellt. A [**CLI-hivatkozás dokumentációja**](https://aka.ms/azmlcli) frissítve lett.


## <a name="2019-04-22"></a>2019-04-22

A Azure Machine Learning SDK for Python v 1.0.30 megjelent.

A [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) was bevezette a közzétett folyamat új verziójának hozzáadását, miközben megtartja ugyanazt a végpontot.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.2

Megjegyzés: az adatprep Python SDK többé nem lesz `numpy` telepítve `pandas` és csomagokat. Lásd: a [telepítési utasítások frissítése](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Új funkciók**
  + Mostantól a pivot átalakítót is használhatja.
    + Útmutató: [pivot notebook](https://aka.ms/aml-data-prep-pivot-nb)
  + Mostantól reguláris kifejezéseket is használhat natív függvényekben.
    + Példák:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Mostantól használhatja `to_upper`  a kifejezést `to_lower`  és a függvényeket a kifejezés nyelvén.
  + Most már megtekintheti az egyes oszlopok egyedi értékeinek számát egy adatprofilban.
  + A gyakran használt olvasói lépések némelyike az `infer_column_types` argumentumot is átadhatja. Ha a `True`értékre van állítva, az adat-előkészítő megpróbálja felderíteni és automatikusan átalakítani az oszlopok típusait.
    + `inference_arguments`már elavult.
  + Most már hívható `Dataflow.shape`.

+ **Hibajavítások és javítások**
  + `keep_columns` a mostantól egy további opcionális `validate_column_exists`argumentumot `keep_columns` is elfogad, amely ellenőrzi, hogy az eredmény tartalmaz-e oszlopokat.
  + Az összes olvasó lépés (amely egy fájlból olvas) mostantól egy további opcionális `verify_exists`argumentumot is elfogad.
  + A Panda dataframe és az adatprofilok beolvasásának jobb teljesítménye.
  + Kijavítva egy olyan hibát, amelyben egy adatfolyam egyetlen lépésének szeletelése egyetlen indexszel meghiúsult.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Most már újra elküldheti egy meglévő parancsfájl futtatását egy meglévő távoli számítási fürtön.
  + Mostantól egy közzétett folyamatot is futtathat új paraméterekkel a folyamatok lapon.
  + A Run details mostantól támogatja az új pillanatkép-fájl megjelenítőjét. Egy adott Futtatás elküldésekor megtekintheti a könyvtár pillanatképét. A Futtatás elindításához le is töltheti a beküldött jegyzetfüzetet.
  + Mostantól megszakíthatja a szülő futtatását a Azure Portalból.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK a Python v 1.0.23

+ **Új funkciók**
  + A Azure Machine Learning SDK mostantól támogatja a Python 3,7-es verzióit.
  + Azure Machine Learning DNN becslések már beépített, többverziós támogatást biztosít. Például `TensorFlow`  a kalkulátor mostantól elfogad egy `framework_version` paramétert, és a felhasználók megadhatják a következő verziót: "1,10" vagy "1,12". Az aktuális SDK-kiadás által támogatott verziók listáját a kívánt keretrendszer osztály ( `get_supported_versions()` például: `TensorFlow.get_supported_versions()`) alapján teheti meg.
  A legújabb SDK-kiadás által támogatott verziók listáját a [DNN kalkulátor dokumentációjában](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)találja.

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.1

+ **Új funkciók**
  + Read_ * átalakítók használatával több adattár-/DataPath-vagy DataReference-forrást is olvashat.
  + A következő műveleteket végezheti el az oszlopokon egy új oszlop létrehozásához: osztás, padló, adatmegosztás, teljesítmény, hossz.
  + Az adat-előkészítés mostantól az Azure ML diagnosztikai csomag része, és alapértelmezés szerint naplózza a diagnosztikai adatokat.
    + A kikapcsoláshoz állítsa a környezeti változót True (igaz) értékre: DISABLE_DPREP_LOGGER

+ **Hibajavítások és javítások**
  + Továbbfejlesztett kód dokumentáció a gyakran használt osztályokhoz és függvényekhez.
  + Kijavított egy olyan hibát auto_read_file, amely nem tudta beolvasni az Excel-fájlokat.
  + A mappa felülírásának lehetősége a read_pandas_dataframeban.
  + A dotnetcore2 függőségek telepítésének jobb teljesítménye, valamint a Fedora 27/28 és az Ubuntu 1804 támogatása.
  + Továbbfejlesztettük az Azure-Blobok olvasásának teljesítményét.
  + Az oszlop típusú észlelés mostantól támogatja a Long típusú oszlopokat.
  + Kijavítva egy hiba, amelyben néhány dátumérték időbélyegként jelenik meg a Python datetime-objektumok helyett.
  + Kijavítva egy olyan hibát, amelyben egyes típusok száma nem egész számként, hanem párosként jelenik meg.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK a Python v 1.0.21

+ **Új funkciók**
  + A *azureml. Core. Run. create_children* metódus lehetővé teszi több gyermek kis késleltetésű létrehozását egyetlen hívással.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.0

+ **Kompatibilitástörő változások**
  + Az adat-előkészítő csomag fogalma elavult, és már nem támogatott. Ahelyett, hogy egy csomagban több adatfolyamok is megmaradjon, külön adatfolyamok maradhat.
    + Útmutató: [adatfolyamok-jegyzetfüzet megnyitása és mentése](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Új funkciók**
  + Az adat-előkészítő mostantól felismerheti az adott szemantikai típusnak megfelelő oszlopokat, és ennek megfelelően feloszthatja őket. A jelenleg támogatott STypes a következők: e-mail-cím, földrajzi koordináták (szélesség & hosszúság), IPv4-és IPv6-címek, USA-beli telefonszámok és US zip-kódok.
    + Útmutató: [szemantikai típusok jegyzetfüzet](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Az adat-előkészítő mostantól a következő műveleteket támogatja két numerikus oszlopból származó eredő oszlop létrehozásához: kivonás, szorzás, osztás és maradék.
  + A adatfolyam meghívásával `verify_has_data()` ellenőrizhető, hogy a adatfolyam végrehajtja-e a rekordokat.

+ **Hibajavítások és javítások**
  + Mostantól megadhatja, hogy hány raktárhelyet használjon a hisztogram a numerikus oszlopok profiljaihoz.
  + Az `read_pandas_dataframe` átalakítás most megköveteli, hogy a DataFrame karakterlánc vagy bájt típusú oszlopnevek legyenek.
  + Javítva lett egy hiba az `fill_nulls` átalakításban, ahol az értékek nem voltak megfelelően kitöltve, ha az oszlop hiányzik.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK a Python v 1.0.18

 + **Változások**
   + Az azureml-tensorboard csomag helyettesíti a azureml-tensorboard.
   + Ebben a kiadásban beállíthat egy felhasználói fiókot a felügyelt számítási fürtön (amlcompute), miközben létrehozta. Ezt a tulajdonságok a létesítési konfigurációban való átadásával teheti meg. További részleteket az [SDK-dokumentációban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)talál.

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.17

+ **Új funkciók**
  + A mostantól támogatja két numerikus oszlop hozzáadását egy eredményül kapott oszlop létrehozásához a kifejezés nyelvének használatával.

+ **Hibajavítások és javítások**
  + Javítottuk a random_split dokumentációját és paraméterének ellenőrzését.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.16

+ **Hibajavítás**
  + Kijavított egy egyszerű szolgáltatás hitelesítési problémáját, amelyet egy API-változás okozott.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK a Python v 1.0.17

+ **Új funkciók**

  + A Azure Machine Learning mostantól első osztályú támogatást biztosít a népszerű DNN-keretrendszer láncolásához. Az [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) osztály felhasználóinak használatával könnyedén betaníthatja és üzembe helyezheti a láncolt modelleket.
    + Ismerje meg, hogyan [futtathatja az elosztott képzést a ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/training/distributed-chainer/distributed-chainer.ipynb)
    + Ismerje meg, hogyan [futtathat hiperparaméter-hangolást a chainer használatával a HyperDrive segítségével](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning folyamatokkal bővült az adattár-módosítások alapján a folyamat futtatásának lehetősége. A folyamat [ütemezett jegyzetfüzete](https://aka.ms/pl-schedule) frissült a szolgáltatás megjelenítéséhez.

+ **Hibajavítások és javítások**
  + Azure Machine Learning-folyamatokat is támogatunk, amelyekkel beállíthatja a source_directory_data_store tulajdonságot egy kívánt adattárra (például blob Storage-ra) a [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)megadott [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) . Alapértelmezés szerint az Azure file Store-t használja a háttérként szolgáló adattárként, amely szabályozási problémákba ütközik, amikor nagy számú lépést hajt végre egyszerre.

### <a name="azure-portal"></a>Azure Portal

+ **Új funkciók**
  + Új fogd és vidd táblázat szerkesztői élmény a jelentésekhez. A felhasználók a megfelelő oszlopokat a tábla azon területére húzhatja, ahol megjelenik a táblázat előnézete. Az oszlopok átrendezhető.
  + Új naplófájlok megjelenítője
  + A műveletek lapon található kísérletekre, számításokra, modellekre, lemezképekre és központi telepítésekre mutató hivatkozások

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.15

+ **Új funkciók**
  + Az adat-előkészítő mostantól támogatja a adatfolyam származó fájlok adatfolyamként való írását. A Emellett lehetővé teszi a fájlok stream-neveinek módosítását új fájlnevek létrehozásához.
    + Útmutató: [a file Streams notebook használata](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Hibajavítások és javítások**
  + Nagyobb teljesítmény a t-Digest nagy adatkészleteken.
  + Az adat-előkészítő mostantól támogatja az adatok DataPath való olvasását.
  + Egy gyors kódolás a logikai és a numerikus oszlopokon is működik.
  + Egyéb egyéb hibajavítások.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK a Python v 1.0.15

+ **Új funkciók**
  + Azure Machine Learning folyamatokat hozzáadott AzureBatchStep ([notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (notebook) és időalapú ütemezési funkció ([notebook](https://aka.ms/pl-schedule)).
  +  A DataTranferStep frissítve lett az Azure SQL Server és az Azure Database for PostgreSQL ([notebook](https://aka.ms/pl-data-trans)) használatához.

+ **Változások**
  + `PublishedPipeline.get_published_pipeline` A mellett elavult `PublishedPipeline.get`.
  + `Schedule.get_schedule` A mellett elavult `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.12

+ **Új funkciók**
  + Az adat-előkészítő mostantól támogatja az Azure SQL Database-adatbázisokból való olvasást az adattár használatával.

+ **Változások**
  + Javítottuk a nagy mennyiségű adathoz tartozó bizonyos műveletek memóriájának teljesítményét.
  + `read_pandas_dataframe()`most meg `temp_folder` kell adni a szükséges értéket.
  + A `name` (z `ColumnProfile` ) tulajdonsága elavult – használja `column_name` helyette.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK a Python v 1.0.10

+ **Módosítások**:
  + Az Azure ML SDK-nak már nincs Azure-CLI-csomagja függőségként. Pontosabban, az Azure-CLI-Core és az Azure-CLI-profil függőségei el lettek távolítva a azureml-Core-ból. A felhasználók a következő változásokat érintik:
      + Ha az "az login", majd az azureml-SDK használatával végzi az SDK-t, a böngésző vagy az eszköz kódjának naplózása még egyszer megtörténik. Nem használja az "az login" által létrehozott hitelesítő adatok állapotát.
    + Az Azure CLI-hitelesítéshez, például az "az login" használata esetén használja az _azureml. Core. Authentication. AzureCliAuthentication_ osztályt. Azure CLI-hitelesítés esetén a _pip az Azure-CLI telepítését_ a Python-környezetben, ahol a azureml-SDK telepítve van.
    + Ha az automatizáláshoz az "az login" szolgáltatást használja, javasoljuk, hogy az _azureml. Core. Authentication. ServicePrincipalAuthentication_ osztályt használja, mivel a azureml-SDK nem fogja használni az Azure CLI által létrehozott hitelesítő adatokat.

+ **Hibajavítások**: Ez a kiadás főleg kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.8

+ **Hibajavítások**
  + Javítottuk az adatprofilok beolvasásának teljesítményét.
  + A hibajelentéssel kapcsolatos rögzített kisebb hibák.

### <a name="azure-portal-new-features"></a>Azure Portal: új funkciók
+ A jelentések új, húzással kapcsolatos felhasználói felülete. A felhasználók a megfelelő típusú oszlopokat vagy attribútumokat a diagram területére húzhatja, ahol a rendszer automatikusan kiválasztja a megfelelő diagramtípust a felhasználó számára az adatok típusától függően. A felhasználók módosíthatják a diagram típusát más alkalmazható típusokra, illetve további attribútumokat adhatnak hozzá.

    Támogatott diagramok típusai:
    - Vonalas diagram
    - Hisztogram
    - Halmozott sávdiagram
    - Box-ábra
    - Pontdiagram
    - Buborék ábrázolása
+ A portál mostantól dinamikusan generál jelentéseket a kísérletekhez. Amikor egy felhasználó elküld egy kísérletet, a rendszer automatikusan létrehoz egy jelentést a naplózott metrikákkal és gráfokkal, hogy az összehasonlítás lehetővé váljon a különböző futtatások között.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK a Python v 1.0.8

+ **Hibajavítások**: Ez a kiadás főleg kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.7

+ **Új funkciók**
  + Az adattár fejlesztése (dokumentálva az [adattár](https://aka.ms/aml-data-prep-datastore-nb)útmutatójában)
    + Lehetőség van az Azure-fájlmegosztás és a ADLS-adattárolók beolvasására és írására.
    + Az adattárolók használatakor az adatelőkészítés mostantól támogatja az egyszerű szolgáltatásnév hitelesítés használatát az interaktív hitelesítés helyett.
    + A wasb és a wasbs URL-címek támogatása.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.6

+ **Hibajavítások**
  + Rögzített hiba a nyilvánosan olvasható Azure Blob-tárolók beolvasásával a Sparkban

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK a Python v 1.0.6
+ **Hibajavítások**: Ez a kiadás főleg kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.4

+ **Új funkciók**
  + `to_bool`a függvény mostantól lehetővé teszi a nem egyező értékek értékre konvertálását. Ez az új alapértelmezett eltérés a `to_bool` és `set_column_types`a esetében, míg az előző alapértelmezett viselkedés a nem egyező értékek hamis értékre konvertálása.
  + A hívásakor `to_pandas_dataframe`új lehetőség van a null/hiányzó értékek értelmezésére a numerikus oszlopokban, mint a Nan.
  + Bizonyos kifejezések visszatérési típusának további ellenőrzése, hogy a konzisztencia és a hibák korának legyenek.
  + Mostantól `parse_json` JSON-objektumokként értelmezheti az oszlopokban szereplő értékeket, és kibonthatja őket több oszlopba.

+ **Hibajavítások**
  + Kijavított egy hibát, amely `set_column_types` összeomlott a Python 3.5.2-ben.
  + Kijavítva egy hiba, amely összeomlott az adattárhoz való csatlakozáskor egy pénzmosás-rendszerkép használatával.

+ **Frissítések**
  * [Például jegyzetfüzetek](https://aka.ms/aml-data-prep-notebooks) az első lépések oktatóanyagok, esettanulmányok és útmutatók.

## <a name="2018-12-04-general-availability"></a>2018-12-04: általánosan elérhető

A Azure Machine Learning már általánosan elérhető.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
Ebben a kiadásban bejelentjük az új felügyelt számítási élményt a [Azure Machine learning számítási](how-to-set-up-training-targets.md#amlcompute)feladatokon keresztül. Ez a számítási cél a Azure Machine Learning Azure Batch AI számítását váltja fel.

Ez a számítási cél:
+ A modell betanítására és a Batch-következtetésre/pontozásra szolgál.
+ Egy-a több csomópontos számítás
+ A fürt kezelése és a feladatütemezés a felhasználó számára
+ Alapértelmezés szerint az autoskálázás
+ A processzor-és a GPU-erőforrások támogatása
+ Alacsony prioritású virtuális gépek használatát teszi lehetővé csökkentett áron

Azure Machine Learning a számításokat a Pythonban, Azure Portal vagy a parancssori felület használatával lehet létrehozni. Ezt a munkaterület régiójában kell létrehozni, és nem csatolhatók más munkaterületekhez. Ez a számítási cél egy Docker-tárolót használ a futtatásához, és a függőségek csomagjaival replikálja ugyanazt a környezetet az összes csomóponton.

> [!Warning]
> Javasoljuk, hogy hozzon létre egy új munkaterületet Azure Machine Learning számítás használatához. Távoli esély van arra, hogy a felhasználók egy meglévő munkaterületről próbálnak létrehozni Azure Machine Learning számítást, és ez hibát jelez. A munkaterületen lévő meglévő számítások továbbra is nem érintik a munkát.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK a Python v 1.0.2-hez
+ **Kompatibilitástörő változások**
  + Ezzel a kiadással eltávolítjuk a virtuális gépek Azure Machine Learningból való létrehozásának támogatását. Továbbra is csatlakoztathat meglévő Felhőbeli virtuális gépet vagy távoli helyszíni kiszolgálót.
  + Emellett a BatchAI támogatását is eltávolítja, amelyek mindegyikét Azure Machine Learning számításon keresztül kell támogatni.

+ **Új**
  + Gépi tanulási folyamatokhoz:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Frissítve**
  + Gépi tanulási folyamatokhoz:
    + A [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) most elfogadja a runconfig
    + A [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) mostantól egy SQL-adatforrásból másolható
    + Az SDK-ban a közzétett folyamatok futtatásához szükséges ütemtervek létrehozásához és frissítéséhez tartozó funkciók ütemezhetők.

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning adat-előkészítési SDK v 0.5.2
+ **Kompatibilitástörő változások**
  * `SummaryFunction.N`átnevezve: `SummaryFunction.Count`.

+ **Hibajavítások**
  * A legújabb pénzmosás-jogkivonatot használja az adattárolók távoli futtatáskor történő beolvasása és írása során. Korábban, ha a pénzmosás-futtatási jogkivonat frissült a Pythonban, az adatelőkészítési futtatókörnyezet nem frissül a frissített pénzmosás-futtatási jogkivonattal.
  * További világosabb hibaüzenetek
  * a to_spark_dataframe () nem fog összeomlani, ha `Kryo` a Spark szerializálást használ
  * Az értékek száma felügyelő mostantól több mint 1000 egyedi értéket jeleníthet meg
  * A véletlenszerű felosztás már nem sikerül, ha az eredeti adatfolyam nem rendelkezik névvel

+ **További információ**
  * [Azure Machine Learning adat-előkészítési SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumentumok és jegyzetfüzetek
+ ML-folyamatok
  + Új és frissített jegyzetfüzetek a folyamatok, a kötegek hatóköre és a stílus-átadási példákkal való ismerkedéshez:https://aka.ms/aml-pipeline-notebooks
  + Ismerje meg, hogyan [hozhatja létre első folyamatát](how-to-create-your-first-pipeline.md)
  + Útmutató a [Batch-előrejelzések folyamatokkal történő futtatásához](how-to-use-parallel-run-step.md)
+ Számítási cél Azure Machine Learning
  + A [minta jegyzetfüzetek](https://aka.ms/aml-notebooks) mostantól frissülnek az új felügyelt számítás használatára.
  + [További tudnivalók a számítási feladatokról](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure Portal: új funkciók
+ [Azure Machine learning számítási](how-to-set-up-training-targets.md#amlcompute) típusok létrehozása és kezelése a portálon.
+ A kvóta használatának figyelése és a Azure Machine Learning számításhoz szükséges [kvóta igénylése](how-to-manage-quotas.md) .
+ Azure Machine Learning számítási fürt állapotának valós idejű megtekintése.
+ A virtuális hálózatok támogatása Azure Machine Learning számítási és Azure Kubernetes-szolgáltatás létrehozásakor lett hozzáadva.
+ Futtassa újra a közzétett folyamatokat a meglévő paraméterekkel.
+ Új [automatizált gépi tanulási diagramok](how-to-understand-automated-ml.md) a besorolási modellekhez (lift, nyereség, kalibrálás, funkció fontossági diagramja modell-magyarázattal) és regressziós modellekkel (maradványok és funkciók fontossági diagramja a modell magyarázatával).
+ A folyamatok megtekinthetők Azure Portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK a Python v 0.1.80

+ **Kompatibilitástörő változások**
  * a *azureml. Train. Widgets* névtér át lett helyezve a *azureml. Widgets*-re.
  * *azureml. Core. számítás. a AmlCompute* a következő osztályokat elavult: *azureml. Core. számítás. BatchAICompute* és *azureml. Core. számítás. DSVMCompute*. A későbbi kiadásokban az utóbbi osztály el lesz távolítva. A AmlCompute osztálynak egyszerűbb definíciója van, és egyszerűen csak egy vm_sizere és a max_nodesra van szüksége, és a fürt automatikusan a 0 értékről a max_nodesre méretezi, amikor egy feladatot elküldenek. A [mintául szolgáló jegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training) frissítve lettek ezzel az információval, és használati példákat kell megadni. Reméljük, hogy ezt az egyszerűsítést és a sok izgalmas funkciót egy későbbi kiadásban szeretné megtekinteni!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning adat-előkészítési SDK v 0.5.1

További információ az adat-előkészítő SDK-ról a [dokumentációs dokumentumok](https://aka.ms/data-prep-sdk)beolvasásával.
+ **Új funkciók**
   * Létrehozott egy új Adatelőkészítés CLI-t a Adatelőkészítés-csomagok végrehajtásához és egy adatkészlet vagy adatfolyam adatprofiljának megtekintéséhez.
   * Újratervezett SetColumnType API a használhatóság javítása érdekében
   * Átnevezve smart_read_file auto_read_file
   * Most már tartalmazza az adatprofilban található ferde és csúcsosságát
   * Minta rétegzett mintavételezéssel
   * A CSV-fájlokat tartalmazó Zip-fájlokból is olvasható
   * Feloszthatja az adathalmazokat a Wise véletlenszerű felosztással (például test-Train készletekbe)
   * Lekérheti az összes oszlop adattípust egy adatfolyam vagy egy adatprofilból a következő hívásával:`.dtypes`
   * Lekérheti a sorok darabszámát egy adatfolyam vagy egy adatprofilból a következő meghívásával:`.row_count`

+ **Hibajavítások**
   * Rögzített hosszú – dupla átalakítás
   * Rögzített érvényesítés az oszlop hozzáadása után
   * Kijavított egy problémát a FuzzyGrouping, amelyben bizonyos esetekben nem észlelhetők a csoportok
   * Rögzített rendezési függvény a többoszlopos rendezési sorrend figyelembevételéhez
   * A rögzített és/vagy kifejezések hasonlóak a `pandas` kezeléséhez.
   * Rögzített olvasás a dbfs útvonalról
   * A hibaüzenetek jobban megérthetők
   * Most már nem sikerül a távoli számítási cél beolvasása egy pénzmosás-token használatával
   * A Linux DSVM már nem sikerül
   * Most már nem összeomlik, ha a nem sztring típusú értékek karakterlánc-predikátumokban vannak
   * A most már kezeli az állítási hibákat, ha a adatfolyam megfelelően működnek
   * A mostantól támogatja a dbutils csatlakoztatott tárolási tárolóhelyeket Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal
A Azure Machine Learning Azure Portal a következő frissítésekkel rendelkezik:
  * Új **folyamatok** lap a közzétett folyamatokhoz.
  * Meglévő HDInsight-fürt számítási célként való csatolásának támogatása.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK a Python v 0.1.74

+ **Kompatibilitástörő változások**
  * * A munkaterület. compute_targets, az adattárolók, a kísérletek, a képek, a modellek és a *webszolgáltatások* a metódusok helyett tulajdonságok. Például cserélje le a *munkaterület. compute_targets ()* *munkaterületre. compute_targets*.
  * A *Run. get_context* a *Run. get_submitted_run*elavult. Az utóbbi metódus el lesz távolítva a következő kiadásokban.
  * A *PipelineData* osztály most datastore_name helyett paraméterként egy adattár-objektumot vár. Hasonlóképpen, a *folyamat* default_datastore_name helyett a default_datastore is elfogadja.

+ **Új funkciók**
  * A Azure Machine Learning-folyamatok [minta notebookja](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines) már MPI-lépéseket használ.
  * A Jupyter-jegyzetfüzetek RunDetails widgetje frissül a folyamat vizualizációjának megjelenítéséhez.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning adat-előkészítési SDK v 0.4.0

+ **Új funkciók**
  * Az adatprofilba felvett típusok száma
  * Az értékek száma és a hisztogram mostantól elérhető
  * További százalékos érték az adatprofilban
  * A medián az összegzésben érhető el
  * A Python 3,7 mostantól támogatott
  * Ha olyan adatfolyam ment, amely adattárolókat tartalmaz egy Adatelőkészítés-csomagba, az adattár adatai a Adatelőkészítés-csomag részeként maradnak meg
  * Az adattárba való írás mostantól támogatott

+ **Hiba javítva**
  * a 64 bites, előjel nélküli egész túlcsordulások mostantól megfelelően kezelhetők Linux rendszeren
  * A smart_readban található egyszerű szövegfájlok helytelen szöveges címkével rendelkeznek
  * A karakterlánc-oszlop típusa most megjelenik a metrikák nézetben
  * A types Count most úgy van rögzítve, hogy megjelenítse az egyes FieldType leképezett ValueKinds az egyes felhasználók helyett
  * Write_to_csv már nem sikerül, ha az elérési út karakterláncként van megadva
  * A Replace utasítás használata esetén a "keresés" üres érték nem fog működni.

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK a Python v 0.1.68

+ **Új funkciók**
  * Több-bérlős támogatás új munkaterület létrehozásakor.

+ **Kijavított hibák**
  * A webszolgáltatás telepítésekor már nem kell rögzítenie a pynacl-függvénytár verzióját.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning adat-előkészítési SDK v 0.3.0

+ **Új funkciók**
  * Hozzáadott metódus transform_partition_with_file (script_path), amely lehetővé teszi a felhasználók számára a végrehajtandó Python-fájl elérési útjának továbbítását

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK a Python v 0.1.65
A [0.1.65 verzió](https://pypi.org/project/azureml-sdk/0.1.65) új funkciókat, több dokumentációt, hibajavítást és több [minta jegyzetfüzetet](https://aka.ms/aml-notebooks)tartalmaz.

Az ismert hibák és a megkerülő megoldások megismeréséhez tekintse meg [az ismert problémák listáját](resource-known-issues.md) .

+ **Kompatibilitástörő változások**
  * Munkaterület. kísérletek, munkaterület. modellek, munkaterület. compute_targets, munkaterület. images, munkaterület. web_services visszatérési szótár, korábban visszaadott lista. Lásd: [azureml. Core. Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API-dokumentáció.

  * Az automatizált Machine Learning az elsődleges mérőszámokból származó normalizált középértékes hibát eltávolította.

+ **HyperDrive**
  * Különböző HyperDrive-hibajavítások a Bayes-as verzióhoz, teljesítménybeli javítások a metrikák beszerzése érdekében.
  * Tensorflow 1,10 verziófrissítés 1,9
  * Docker-rendszerkép optimalizálása a hideg indításhoz.
  * A feladatok mostantól akkor is helyes állapotot jelentenek, ha a nullától eltérő hibakóddal kilépnek.
  * RunConfig az SDK-ban.
  * A HyperDrive futtatási objektuma támogatja a megszakítást a normál futtatáshoz hasonlóan: nem kell paramétereket átadni.
  * A widgetek az elosztott futtatások és a HyperDrive futtatására szolgáló legördülő értékek állapotának fenntartására szolgáló funkciókat biztosítanak.
  * A TensorBoard és más naplófájlok támogatják a paraméter-kiszolgálót.
  * Az Intel (R) MPI támogatása a szolgáltatás oldalán.
  * Hibajavítás az elosztott Futtatás javításához a BatchAI-ben történő ellenőrzés során.
  * A Context Manager most azonosítja az elsődleges példányt.

+ **Azure Portali élmény**
  * a log_table () és a log_row () támogatottak a Futtatás részleteiben.
  * Diagramok automatikus létrehozása a táblázatok és sorok számára 1, 2 vagy 3 numerikus oszlop és egy nem kötelezően megadandó oszlop esetében.

+ **Automatizált Machine Learning**
  * Javított hibakezelés és dokumentáció
  * A futtatási tulajdonságok lekérési teljesítményével kapcsolatos hibák elhárítása.
  * Rögzített folytatási hiba történt.
  * Rögzített :::no-loc text="ensembling"::: iterációs problémák.
  * Rögzített betanítási hiba a MAC operációs rendszeren.
  * Az egyéni ellenőrzési forgatókönyvben lévő PR/ROC görbe leegyszerűsítése a makróban.
  * Eltávolított további index-logikát.
  * Eltávolított szűrő a get_output API-ból.

+ **Folyamatok**
  * A () metódust hozzáadta egy folyamathoz, amely közvetlenül a végrehajtás megkövetelése nélkül teszi közzé a folyamatot.
  * A rendszer hozzáadta a PipelineRun. get_pipeline_runs () metódust a közzétett folyamatból generált folyamat-futtatások beolvasásához.

+ **Projekt agyhullám**
  * Frissített támogatás a FPGA-on elérhető új AI-modellekhez.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning adat-előkészítési SDK v 0.2.0
A [0.2.0 verziója](https://pypi.org/project/azureml-dataprep/0.2.0/) a következő funkciókat és hibajavításokat tartalmazza:

+ **Új funkciók**
  * Egy gyors kódolás támogatása
  * A quantile átalakítás támogatása

+ **Hiba javítva:**
  * Bármely Tornado-verzióval működik, nincs szükség a tornádó verziójának visszalépésére
  * Az értékek száma az összes értéknél, nem csak az első három

## <a name="2018-09-public-preview-refresh"></a>2018-09 (nyilvános előzetes verzió frissítése)

Azure Machine Learning új, frissített kiadása: További információ erről a kiadásról:https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>További lépések

Olvassa el az [Azure Machine Learning](overview-what-is-azure-ml.md) áttekintését.
