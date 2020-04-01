---
title: Mi újság a kiadásban?
titleSuffix: Azure Machine Learning
description: Ismerje meg az Azure Machine Learning legújabb frissítéseit, valamint a gépi tanulási és adat-előkészítési Python SDK-kat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: b55c351927a56afce697d07f41bfbe668144d68d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475520"
---
# <a name="azure-machine-learning-release-notes"></a>Az Azure Machine Learning kiadási megjegyzések

Ebben a cikkben az Azure Machine Learning-kiadások.  A teljes SDK referenciatartalom, keresse fel az Azure Machine Learning [**fő SDK Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) referencialap.

Az ismert hibák és kerülő megoldások [listájában](resource-known-issues.md) olvashat.

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK python-beli 1.2.0-s-hoz

+ **Változások megbontása**
  + Drop támogatása python 2,7

+ **Hibajavítások és fejlesztések**
  + **azúr-cli-ml**
    + "--subscription-id" hozzáadása `az ml model/computetarget/service` a CLI parancsaihoz
    + Az ügyfél által felügyelt kulcs (CMK) átadásának támogatása vault_url, key_name és key_version az ACI telepítéséhez
  + **azureml-automl-core** 
    + Személyre szabott imputálás engedélyezése állandó értékkel mind az X, mind az y adat-előrejelzési feladatokhoz.
    + Kijavítottuk azt a hibát, amikor hibaüzeneteket jelenített meg a felhasználónak.    
  + **azureml-automl-futási idő**
    + Kijavítottuk a problémát az adatkészletek előrejelzésével, amely csak egy sorral rendelkező szemeket tartalmazott
    + Csökkent az előrejelzési feladatokhoz szükséges memória mennyisége.
    + Hozzáadott jobb hibaüzenetek, ha az idő oszlop helytelen formátumú.
    + Személyre szabott imputálás engedélyezése állandó értékkel mind az X, mind az y adat-előrejelzési feladatokhoz.
  + **azureml-core**
    + A ServicePrincipal környezeti változókból való betöltésének támogatása: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID és AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Új paraméter `support_multi_line` `Dataset.Tabular.from_delimited_files`bevezetése: Alapértelmezés szerint`support_multi_line=False`( ), az összes sortörés, beleértve az idézett mezőértékeket is, rekordtörésként lesz értelmezve. Az adatok így történő olvasása gyorsabb és optimalizáltabb a párhuzamos végrehajtáshoz több CPU-magot. Ez azonban azt eredményezheti, hogy csendben több rekordot hoz létre rosszul igazított mezőértékekkel. Ezt akkor kell `True` beállítani, ha a tagolt fájlokról ismert, hogy idézett sortöréseket tartalmaznak.
    + Az ADLS Gen2 regisztrálásának lehetősége az Azure Machine Learning CLI-ben
    + A TabularDataset with_timestamp_columns() metódusához az "fine_grain_timestamp" paramétert "időbélyegre" és "coarse_grain_timestamp" paramétert "partition_timestamp" nevezte át a paraméterek használatának jobb tükrözése érdekében.
    + A kísérlet maximális hosszának növelése 255-re.
  + **azureml-interpret**
    + Frissített azureml-interpret a 0.7-es közösség értelmezésére
  + **azureml-sdk**
    + Váltás a függőségek kompatibilis verzió Tilde támogatására javítás előtti és stabil kiadások.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK python-hoz 1.1.5-ös vagyon

+ **A szolgáltatás eprekation**
  + **Python 2,7**
    + Utolsó verzió a python 2.7 támogatásához

+ **Változások megbontása**
  + **Szemantikai verziószámozás 2.0.0**
    + Az 1.1-es verziótól kezdve az Azure ML Python SDK elfogadja a szemantikus verziószámozás 2.0.0. [További tudnivalók itt](https://semver.org/). Minden további verzió az új számozási sémát és szemantikai verziószámozási szerződést követi. 

+ **Hibajavítások és fejlesztések**
  + **azúr-cli-ml**
    + A konzisztencia érdekében módosítsa a CLI végpont parancsnevét "az ml végpont aks"-ról "az ml végpont valós idejűre" .
    + a CLI telepítési útmutatójának frissítése a stabil és kísérleti CLI elágazáshoz
    + Az egypéldányos profilalkotást rögzítették egy ajánlás elkészítéséhez, és a core sdk-ben tették elérhetővé.
  + **azureml-automl-core**
    + Engedélyezve a Batch mód következtetését (egyszer több sort vesz igénybe) az automl ONNX modellekhez
    + Javítottuk az adatkészletek gyakoriságának észlelését, az adatok hiányát vagy a szabálytalan adatpontokat tartalmazó adatokat
    + Hozzáadott képes eltávolítani az adatpontokat nem felel meg a domináns frekvenciát.
    + Módosította a konstruktor bemenetét, hogy listát adjon a megfelelő oszlopok imputálási beállításainak alkalmazásához.
    + A hibanaplózás tovább lett fejlesztve.
  + **azureml-automl-futási idő**
    + Javítottuk a hibát, ha a betanítási készletben nem jelenlévő gabona megjelent a tesztkészletben.
    + Eltávolította a y_query követelményt az előrejelzési szolgáltatás pontozása során
    + Kijavítottuk az előrejelzéssel kapcsolatos problémát, amikor az adatkészlet hosszú időhézagokkal rendelkező rövid szemcséket tartalmaz.
    + Kijavítottuk a problémát, amikor az automatikus max horizont be van kapcsolva, és a dátumoszlop karakterláncok formájában tartalmazza a dátumokat. Megfelelő átalakítás és hibaüzenetek kerültek hozzáadásra, amikor az átállás a mai napig nem lehetséges
    + Natív NumPy és SciPy használata a köztes adatok szerializálásához és deszerializálásához a FileCacheStore számára (helyi AutoML-futtatásokhoz)
    + Javítottunk egy hibát, amely miatt a sikertelen gyermekfuttatások futási állapotban ragadhattak meg.
    + Nagyobb sebesség a felvázolás.
    + Javítottuk a frekvenciaellenőrzést a pontozás során, most az előrejelzési feladatok nem igényelnek szigorú frekvenciaegyenértékűséget a vonat és a tesztkészlet között.
    + Módosította a konstruktor bemenetét, hogy listát adjon a megfelelő oszlopok imputálási beállításainak alkalmazásához.
    + A lag típuskiválasztásával kapcsolatos hibák.
    + Javítottuk az adatkészleteken felmerült nem minősített hibát, amelyegyetlen sorral rendelkezett szemekkel
    + Javítottuk a frekvenciaészlelés lassúságával kapcsolatos problémát.
    + Kijavít egy hibát az AutoML kivételkezelésben, amely miatt a betanítási hibát egy Attribútumhiba váltotta fel.
  + **azureml-cli-gyakori**
    + Az egypéldányos profilalkotást rögzítették egy ajánlás elkészítéséhez, és a core sdk-ben tették elérhetővé.
  + **azureml-contrib-mir**
    + A MirWebservice osztály ban a hozzáférési jogkivonat beolvasásához szükséges funkciókat adja hozzá
    + Token hitelesítés használata a MirWebservice alapértelmezett hez mirwebservice.run() hívás közben - Csak akkor frissüljön, ha a hívás sikertelen
    + A Mir webszolgáltatás telepítéséhez most már megfelelő Skus [Standard_DS2_v2, Standard_F16, Standard_A2_v2] szükséges a [Ds2v2, A2v2 és F16] helyett.
  + **azureml-contrib-pipeline-steps**
    + A ParallelRunStep szolgáltatáshoz nem kötelező side_inputs paramétert hozzáadni. Ezzel a paraméterrel csatlakoztatható a mappa a tárolón. A jelenleg támogatott típusok a DataReference és a PipelineData.
    + A ParallelRunConfig-ban átadott paraméterek felülírhatók a folyamatparamétereinek átadásával. A aml_mini_batch_size támogatott új folyamatparaméterek, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count és aml_process_count_per_node már a korábbi kiadás részét képezik).
  + **azureml-core**
    + A telepített AzureML-webszolgáltatások `INFO` alapértelmezés szerint naplózásra kerülnek. Ez a környezet változójának `AZUREML_LOG_LEVEL` beállításával szabályozható az üzembe helyezett szolgáltatásban.
    + A Python SDK felderítési szolgáltatást használ az "api" végpont használatára a "folyamatok" helyett.
    + Csere az új útvonalakra az összes SDK-hívásban
    + A ModelManagementService hívásainak útválasztásának módosítása egy új, egységes ített struktúrára
      + A munkaterület frissítési módszerének nyilvános elérhetővé tétele.
      + Hozzáadott image_build_compute paraméter munkaterület frissítési módszer, amely lehetővé teszi a felhasználó számára a számítási kép létrehozásához
    +  Elavult üzenetek hozzáadása a régi profilkészítési munkafolyamathoz. Rögzített profilkészítés cpu és a memória korlátok
    + R-feladatok futtatásához hozzáadott Rsection a környezet részeként
    +  Érvényesítéssel `Dataset.mount` növelheti a hibát, ha az adatkészlet forrása nem érhető el, vagy nem tartalmaz adatokat.
    + Az `--grant-workspace-msi-access` Azure Blob Container regisztrálásához hozzáadott datastore CLI további paraméterként, amely lehetővé teszi a virtuális hálózat mögött iblob-tároló regisztrálását
    + Az egypéldányos profilalkotást rögzítették egy ajánlás elkészítéséhez, és a core sdk-ben tették elérhetővé.
    + Javítottuk a problémát aks.py _deploy
    + Ellenőrzi a feltöltött modellek integritását a csendes tárolási hibák elkerülése érdekében.
    + A felhasználó most már megadhat egy értéket az auth kulcshoz a webszolgáltatások kulcsainak újragenerálásakor.
    + Javítottuk azt a hibát, amely szerint a nagybetűk nem használhatók adatkészlet bemeneti neveként
  + **azureml-alapértelmezett értékek**
    + `azureml-dataprep`most a program részeként `azureml-defaults`lesz telepítve. Már nem szükséges manuálisan telepíteni a dataprep[fuse] programot számítási célokra az adatkészletek csatlakoztatásához.
  + **azureml-interpret**
    + Frissített azureml-interpret a 0.6-os közösség értelmezésére
    + Frissített azureml-interpret függ értelmező-közösség 0.5.0
    + Azureml-stílusú kivételek hozzáadása az azureml-interprethoz
    + Rögzített DeepScoringExplainer szerializálás a keras modellekhez
  + **azureml-ml flow**
    + Szuverén felhők támogatása az azureml.mlflow-hoz
  + **azureml-csővezeték-mag**
    + A folyamatköteg-pontozási jegyzetfüzet mostantól parallelrunstep-t használ
    + Javítva egy hiba, amely miatt a PythonScriptStep eredményeit helytelenül lehetett felhasználni az argumentumlista módosítása ellenére
    + Hozzáadva az oszlopok típusának beállításának lehetősége a parse_* metódusok hívásakor`PipelineOutputFileDataset`
  + **azureml-folyamatlépések**
    + Áthelyeztem `AutoMLStep` `azureml-pipeline-steps` a csomagot. A belül `AutoMLStep` elavult. `azureml-train-automl-runtime`
    + Hozzáadott dokumentációs példa az adatkészlethez PythonScriptStep bemenetként
  + **azureml-tenorboard**
    + frissített azureml-tensorboard a tensorflow 2.0 támogatásához
    + Helyes portszám megjelenítése egyéni Tensorboard port használatakor számítási példányon
  + **azureml-train-automl-client**
    + Kijavítottuk azt a problémát, amely miatt bizonyos csomagok helytelen verziókban telepíthetők a távoli futtatásokon.
    + rögzített FeaturizationConfig felülbíráló kérdés, amely szűri az egyéni featurization config.
  + **azureml-train-automl-runtime**
    + Kijavítottuk a távoli futtatások gyakoriságának észlelésével kapcsolatos problémát
    + Áthelyezte `AutoMLStep` a `azureml-pipeline-steps` csomagot. A belül `AutoMLStep` elavult. `azureml-train-automl-runtime`
  + **azureml-train-core**
    + A PyTorch 1.4-es verziójának támogatása a PyTorch Estimator-ban
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK python-verzió 1.1.2rc0-es verzió (előzetes kiadás)

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Engedélyezve a Batch mód következtetését (egyszer több sort vesz igénybe) az automl ONNX modellekhez
    + Javítottuk az adatkészletek gyakoriságának észlelését, az adatok hiányát vagy a szabálytalan adatpontokat tartalmazó adatokat
    + Hozzáadott képes eltávolítani az adatpontokat nem felel meg a domináns frequrncy.
  + **azureml-automl-futási idő**
    + Javítottuk a hibát, ha a betanítási készletben nem jelenlévő gabona megjelent a tesztkészletben.
    + Eltávolította a y_query követelményt az előrejelzési szolgáltatás pontozása során
  + **azureml-contrib-mir**
    + A MirWebservice osztály ban a hozzáférési jogkivonat beolvasásához szükséges funkciókat adja hozzá
  + **azureml-core**
    + A telepített AzureML-webszolgáltatások `INFO` alapértelmezés szerint naplózásra kerülnek. Ez a környezet változójának `AZUREML_LOG_LEVEL` beállításával szabályozható az üzembe helyezett szolgáltatásban.
    + Javítsa ki az `Dataset.get_all` iterációt, hogy a munkaterülettel regisztrált összes adatkészletet visszaadja.
    + A hibaüzenet javítása, ha érvénytelen `path` típust ad át az adatkészlet-létrehozási API-k argumentumainak.
    + A Python SDK felderítési szolgáltatást használ az "api" végpont használatára a "folyamatok" helyett.
    + Csere az új útvonalakra az összes SDK-hívásban
    + A ModelManagementService hívásainak útválasztásának módosítása egy új, egységes ített struktúrára
      + A munkaterület frissítési módszerének nyilvános elérhetővé tétele.
      + Hozzáadott image_build_compute paraméter munkaterület frissítési módszer, amely lehetővé teszi a felhasználó számára a számítási kép létrehozásához
    +  Elavult üzenetek hozzáadása a régi profilkészítési munkafolyamathoz. Rögzített profilkészítés cpu és a memória korlátok
  + **azureml-interpret**
    + frissítse az azureml-interprett a 0.6-os értelmezési közösségre.*
  + **azureml-ml flow**
    + Szuverén felhők támogatása az azureml.mlflow-hoz
  + **azureml-folyamatlépések**
    + Költözött `AutoMLStep` a `azureml-pipeline-steps package`. A belül `AutoMLStep` elavult. `azureml-train-automl-runtime`
  + **azureml-train-automl-client**
    + Kijavítottuk azt a problémát, amely miatt bizonyos csomagok helytelen verziókban telepíthetők a távoli futtatásokon.
  + **azureml-train-automl-runtime**
    + Kijavítottuk a távoli futtatások gyakoriságának észlelésével kapcsolatos problémát
    + Költözött `AutoMLStep` a `azureml-pipeline-steps package`. A belül `AutoMLStep` elavult. `azureml-train-automl-runtime`
  + **azureml-train-core**
    + Költözött `AutoMLStep` a `azureml-pipeline-steps package`. A belül `AutoMLStep` elavult. `azureml-train-automl-runtime`

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK python-verzió 1.1.1rc0-es verzióhoz (előzetes kiadás)

+ **Hibajavítások és fejlesztések**
  + **azúr-cli-ml**
    + Az egypéldányos profilalkotást rögzítették egy ajánlás elkészítéséhez, és a core sdk-ben tették elérhetővé.
  + **azureml-automl-core**
    + A hibanaplózás tovább lett fejlesztve.
  + **azureml-automl-futási idő**
    + Kijavítottuk az előrejelzéssel kapcsolatos problémát, amikor az adatkészlet hosszú időhézagokkal rendelkező rövid szemcséket tartalmaz.
    + Kijavítottuk a problémát, amikor az automatikus max horizont be van kapcsolva, és a dátumoszlop karakterláncok formájában tartalmazza a dátumokat. Hozzáadtuk a megfelelő konverziót és az ésszerű hibát, ha az átalakítás eddig nem lehetséges
    + Natív NumPy és SciPy használata a köztes adatok szerializálásához és deszerializálásához a FileCacheStore számára (helyi AutoML-futtatásokhoz)
    + Javítottunk egy hibát, amely miatt a sikertelen gyermekfuttatások futási állapotban ragadhattak meg.
  + **azureml-cli-gyakori**
    + Az egypéldányos profilalkotást rögzítették egy ajánlás elkészítéséhez, és a core sdk-ben tették elérhetővé.
  + **azureml-core**
    + Az `--grant-workspace-msi-access` Azure Blob Container regisztrálásához hozzáadott datastore CLI további paraméterként, amely lehetővé teszi a virtuális hálózat mögött iblob-tároló regisztrálását
    + Az egypéldányos profilalkotást rögzítették egy ajánlás elkészítéséhez, és a core sdk-ben tették elérhetővé.
    + Javítottuk a problémát aks.py _deploy
    + Ellenőrzi a feltöltött modellek integritását a csendes tárolási hibák elkerülése érdekében.
  + **azureml-interpret**
    + azureml-stílusú kivételek hozzáadása az azureml-interprethoz
    + rögzített DeepScoringExplainer szerializálás keras modellekhez
  + **azureml-csővezeték-mag**
    + A folyamatköteg-pontozási jegyzetfüzet mostantól parallelrunstep-t használ
  + **azureml-folyamatlépések**
    + Áthelyezte `AutoMLStep` a `azureml-pipeline-steps` csomagot. A belül `AutoMLStep` elavult. `azureml-train-automl-runtime`
  + **azureml-contrib-pipeline-steps**
    + A ParallelRunStep szolgáltatáshoz nem kötelező side_inputs paramétert hozzáadni. Ezzel a paraméterrel csatlakoztatható a mappa a tárolón. A jelenleg támogatott típusok a DataReference és a PipelineData.
  + **azureml-tenorboard**
    + frissített azureml-tensorboard a tensorflow 2.0 támogatásához
  + **azureml-train-automl-client**
    + rögzített FeaturizationConfig felülbíráló kérdés, amely szűri az egyéni featurization config.
  + **azureml-train-automl-runtime**
    + Áthelyezte `AutoMLStep` a `azureml-pipeline-steps` csomagot. A belül `AutoMLStep` elavult. `azureml-train-automl-runtime`
  + **azureml-train-core**
    + A PyTorch 1.4-es verziójának támogatása a PyTorch Estimator-ban
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK python-verzió 1.1.0rc0-es verzió (előzetes kiadás)

+ **Változások megbontása**
  + **Szemantikai verziószámozás 2.0.0**
    + Az 1.1-es verziótól kezdve az Azure ML Python SDK elfogadja a szemantikus verziószámozás 2.0.0. [További tudnivalók itt](https://semver.org/). Minden további verzió az új számozási sémát és szemantikai verziószámozási szerződést követi. 
  
+ **Hibajavítások és fejlesztések**
  + **azureml-automl-futási idő**
    + Nagyobb sebesség a felvázolás.
    + Javítottuk a frekvenciaellenőrzést a pontozás során, most az előrejelzési feladatokban nem igényelünk szigorú frekvenciaegyenértékűséget a vonat és a tesztkészlet között.
  + **azureml-core**
    + A felhasználó most már megadhat egy értéket az auth kulcshoz a webszolgáltatások kulcsainak újragenerálásakor.
  + **azureml-interpret**
    + Frissített azureml-interpret függ értelmező-közösség 0.5.0
  + **azureml-csővezeték-mag**
    + Javítva egy hiba, amely miatt a PythonScriptStep eredményeit helytelenül lehetett felhasználni az argumentumlista módosítása ellenére
  + **azureml-folyamatlépések**
    + Hozzáadott dokumentációs példa az adatkészlethez PythonScriptStep bemenetként
  + **azureml-contrib-pipeline-steps**
    + A ParallelRunConfig-ban átadott paraméterek felülírhatók a folyamatparamétereinek átadásával. A aml_mini_batch_size támogatott új folyamatparaméterek, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count és aml_process_count_per_node már a korábbi kiadás részét képezik).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK python-hoz 1.0.85-ös v1.0.85

+ **Új funkciók**
  + **azureml-core**
    + Az AmlCompute-erőforrások aktuális alaphasználatának és kvótakorlátozásának beszereznie egy adott munkaterületen és előfizetésben
  
  + **azureml-contrib-pipeline-steps**
    + Annak engedélyezése, hogy a felhasználó táblázatos adatkészletet továbbítson köztes eredményként az előző lépésből a parallelrunstep-be

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-futási idő**
    + Eltávolította a követelményt y_query oszlop a kérelemben, hogy az üzembe helyezett előrejelzési szolgáltatás. 
    + A "y_query" eltávolították a Dominick Orange Juice notebook szolgáltatás kérésére részben.
    + Javítva a hiba, amely megakadályozta az előrejelzést az üzembe helyezett modelleken, és a dátumidő-oszlopokkal rendelkező adatkészleteken működött.
    + Matthews korrelációs együttható t adott hozzá osztályozási metrikaként, mind a bináris, mind a többosztályos osztályozáshoz.
  + **azureml-contrib-interpret**
    + Eltávolított szöveg magyarázók az azureml-contrib-interpret a szöveg magyarázatát átkerült a interpret-text tárhét, amely hamarosan megjelenik.
  + **azureml-core**
    + Adatkészlet: a fájladatkészlet-használat már nem függ numpy és pandák kell telepíteni a python env.
    + A LocalWebservice.wait_for_deployment() módosítása a helyi Docker-tároló állapotának ellenőrzéséhez, mielőtt megpróbálna pingelni az állapotvégpontot, jelentősen csökkentve a sikertelen központi telepítés jelentéséhez szükséges időt.
    + Javítottuk a LocalWebservice.reload() fájlban használt belső tulajdonság inicializálását, amikor a szolgáltatásobjektumot egy meglévő központi telepítésből hozták létre a LocalWebservice() konstruktor használatával.
    + Szerkesztett hibaüzenet a pontosítás érdekében.
    + Hozzáadott egy új módszert, get_access_token() az AksWebservice-hez, amely visszaadja az AksServiceAccessToken objektumot, amely hozzáférési jogkivonatot, időbélyeg utáni frissítést, időbélyeg és tokentípus lejáratát tartalmazza. 
    + Elavult meglévő get_token() metódus az AksWebservice metódusban, mivel az új metódus a metódus által visszaadott összes információt visszaadja.
    + Az ml szolgáltatás get-access-token parancs módosított kimenete. Átnevezett token accessToken és refreshBy frissíteniAfter. ExpiryOn és tokenType tulajdonságok hozzáadva.
    + Rögzített get_active_runs
  + **azureml-explain-modell**
    + frissített formát 0.33.0-ra és értelmezi a közösséget 0.4.*
  + **azureml-interpret**
    + frissített formát 0.33.0-ra és értelmezi a közösséget 0.4.*
  + **azureml-train-automl-runtime**
    + Matthews korrelációs együttható t adott hozzá osztályozási metrikaként, mind a bináris, mind a többosztályos osztályozáshoz.
    + Alapértelmezés szerint a folyamat előtti jelző elavulttá válása a kódból, és a featurization -featurization kifejezéssel helyettesíthető

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK python-hoz 1.0.83-as v1.0.83

+ **Új funkciók**
  + Adatkészlet: Adjon hozzá `on_error` `out_of_range_datetime` két `to_pandas_dataframe` lehetőséget, és sikertelen, ha az `None`adatok hibaértékeket tartalmaznak, ahelyett, hogy kitöltenék őket.
  + Munkaterület: Hozzáadta `hbi_workspace` a bizalmas adatokat tartalmazó munkaterületek jelzőjét, amely lehetővé teszi a további titkosítást, és letiltja a speciális diagnosztikát a munkaterületeken. Azt is hozzáadtuk, hogy a saját kulcsokat a `cmk_keyvault` társított `resource_cmk_uri` Cosmos DB-példány, megadva a és a paraméterek et egy munkaterület létrehozásakor, amely létrehoz egy Cosmos DB-példányt az előfizetésben, miközben a munkaterület kiépítése. [Bővebben itt olvashattok róluk.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-futási idő**
    + Javítottunk egy regressziót, amely miatt egy TypeError előkerült, amikor az AutoML-t a Python 3.5.4 alatti verzióin futtatta.
  + **azureml-core**
    + Javítva, `datastore.upload_files` hogy a relatív elérési `./` út, amely nem indult el, nem használható.
    + Eprecációs üzenetek hozzáadása az összes image osztálykódpatohoz
    + Rögzített modell menedzsment URL építése Mooncake régióban.
    + Kijavítottuk azt a problémát, amely miatt a source_dir használó modelleket nem lehetett csomagolni az Azure Functions szolgáltatáshoz.    
    + Hozzáadott egy lehetőséget [az Environment.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) szolgáltatással az AzureML-munkaterületi tároló beállításjegyzékébe való leküldéseérdekében
    + Frissítette az SDK-t, hogy az új jogkivonat-kódtárat az azure synapse-on egy vissza-kompatibilis módon használja.
  + **azureml-interpret**
    + Javítva az a hiba, amely miatt a Nincs nem került vissza, amikor nem volt elérhető magyarázat letölthető. Most kivételt vet fel, máshol egyező viselkedést.
  + **azureml-folyamatlépések**
    + Nem engedélyezett `DatasetConsumptionConfig`az `Estimator`s `inputs` átadása `Estimator` a paraméterre, amikor a- t egy `EstimatorStep`ban használják.
  + **azureml-sdk**
    + AutoML-ügyfél hozzáadása az azureml-sdk csomaghoz, lehetővé téve a távoli AutoML-futtatások elküldését a teljes AutoML-csomag telepítése nélkül.
  + **azureml-train-automl-client**
    + Javított igazítás a konzol kimenetén az automatikus ml-es futtatásokhoz
    + Javítva egy hiba, ahol helytelen változata pandák lehet telepíteni a távoli amlcompute.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK python-hoz 1.0.81-es v1.0.81

+ **Hibajavítások és fejlesztések**
  + **azureml-contrib-interpret**
    + elhalasztja a shap függőséget az azureml-interpret-community értelmezésére
  + **azureml-core**
    + Számítási cél most már megadható paraméterként a megfelelő üzembe helyezési konfigurációs objektumok. Ez kifejezetten a számítási cél neve üzembe helyezés, nem az SDK-objektum.
    + CreatedBy információ hozzáadása modell- és szolgáltatásobjektumokhoz. A .created_by-on keresztül <var>érhető el
    + Rögzített ContainerImage.run(), amely nem megfelelően beállítása a Docker-tároló HTTP-port.
    + Választhatóvá `azureml-dataprep` `az ml dataset register` a cli parancshoz
    + Javítva egy `TabularDataset.to_pandas_dataframe` hiba, amely helytelenül esik vissza egy másik olvasó, és nyomtassa ki a figyelmeztetést.
  + **azureml-explain-modell**
    + elhalasztja a shap függőséget az azureml-interpret-community értelmezésére
  + **azureml-csővezeték-mag**
    + Új folyamatlépés `NotebookRunnerStep`hozzáadva, a helyi jegyzetfüzet futtatásához a folyamat lépéseként.
    + Elavult get_all függvények eltávolítva a Közzétett folyamatok, ütemezések és PipelineEndpoints pontokhoz
  + **azureml-train-automl-client**
    + A data_script automatikus ml-bemenetként történő eprecációjának megkezdése.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK python-hoz 1.0.79-es v1.0.79

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Eltávolítva featurizationConfig kell naplózni
      + Frissítve a naplózás az "auto"/"off"/"customize" naplózáshoz.
  + **azureml-automl-futási idő**
    + Hozzáadott támogatása pandák. Sorozat és pandák. Az oszlopadattípus észlelésének kategorikus esete. Korábban csak a numpy.ndarray programot támogatta
      + Kapcsolódó kódmódosítások hozzáadva a kategorikus dtype helyes kezeléséhez.
    + Továbbfejlesztettük az előrejelzési függvény felületét: a y_pred paraméter tévá lett. - A docstringek javultak.
  + **azureml-contrib-adatkészlet**
    + Javítva egy hiba, amely nem csatlakoztatható a címkézett adatkészleteken.
  + **azureml-core**
    + Hibajavítás `Environment.from_existing_conda_environment(name, conda_environment_name)`a számára. A felhasználó létrehozhat egy olyan környezetpéldányt, amely a helyi környezet pontos másolata
    + Az idősorozatokkal kapcsolatos adatkészletek `include_boundary=True` metódusait alapértelmezés szerint módosította.
  + **azureml-train-automl-client**
    + Kijavítottuk azt a hibát, amely miatt az érvényesítési eredmények nem lesznek kinyomtatva, ha a megjelenítési kimenet értéke hamis.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK python-hoz 1.0.76-os

+ **Változások megbontása**
  + Az Azureml-Train-AutoML frissítési problémái
    + Az azureml-train-automl>=1.0.76-ra való frissítés az azureml-train-automl<1.0.76 részleges telepítést okozhat, ami egyes automl-importálások sikertelensítését okozhatja. A probléma megoldásához futtassa a https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmdtelepítőparancsfájlt, amely a alkalmazásban található. Vagy ha közvetlenül pip-et használ, akkor:
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + vagy eltávolíthatja a régi verziót a frissítés előtt
      + "pip uninstall azureml-train-automl"
      + "pip install azureml-train-automl"

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-futási idő**
    + Az AutoML mostantól figyelembe veszi a valódi és a hamis osztályokat is a bináris osztályozási feladatok átlagos skaláris metrikáinak kiszámításakor.
    + Az AzureML-AutoML-Core gépi tanulási és betanítási kódját áthelyezték egy új azureML-AutoML-Runtime csomagba.
  + **azureml-contrib-adatkészlet**
    + Ha `to_pandas_dataframe` egy címkézett adatkészletet hív meg a letöltési beállítással, most megadhatja, hogy felülírja-e a meglévő fájlokat.
    + `keep_columns` Híváskor, `drop_columns` vagy olyan eseménysorozatok, címke vagy képoszlop eldobásához vezet, hogy a megfelelő képességek is ellesznek dobva az adatkészlethez.
    + Kijavítottuk a pytorch betöltővel kapcsolatos problémát az objektumészlelési feladathoz.
  + **azureml-contrib-interpret**
    + Eltávolított a magyarázat irányítópult widget az azureml-contrib-interpret, megváltozott csomagot, hogy hivatkozzon az új at interpret_community
    + Az interpret-community frissített verziója 0.2.0-ra
  + **azureml-core**
    + A teljesítményének `workspace.datasets`javítása.
    + Az Azure SQL Database Datastore regisztrálásának lehetősége felhasználónév- és jelszó-hitelesítéssel
    + Javítsa ki a RunConfigurations relatív elérési utakról való betöltését.
    + `keep_columns` Híváskor, `drop_columns` vagy ami egy idősorozat-oszlop eldobását eredményezi, a megfelelő képességek is ellesznek dobva az adatkészlethez.
  + **azureml-interpret**
    + frissített változata interpret-közösség 0.2.0
  + **azureml-folyamatlépések**
    + Dokumentált támogatott értékek `runconfig_pipeline_params` az azure machine learning folyamat lépéseihez.
  + **azureml-csővezeték-mag**
    + Cli opcióval töltötte le a kimenetet json formátumban a Pipeline parancsokhoz.
  + **azureml-train-automl**
    + Az AzureML-Train-AutoML felosztása 2 csomagra, egy ügyfélcsomagra, az AzureML-Train-AutoML-Clientre és egy ML-betanítási csomagra AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Hozzáadott egy vékony kliens autoML-kísérletek beküldése nélkül telepíteni a gépi tanulási függőségek helyileg.
    + Rögzített fakitermelés automatikusan észlelt késések, gördülő ablakméretek és maximális horizontok a távoli fut.
  + **azureml-train-automl-runtime**
    + Hozzáadott egy új AutoML-csomagot a gépi tanulási és futásidejű összetevők ügyféltől való elkülönítéséhez.
  + **azureml-contrib-vonat-rl**
    + Megerősítési támogatás hozzáadva az SDK-ban.
    + Hozzáadva az AmlWindowsCompute támogatás az RL SDK-ban.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK python-hoz 1.0.74-es v1.0.74

  + **Előzetes verzió funkciói**
    + **azureml-contrib-adatkészlet**
      + Az azureml-contrib-adatkészlet importálása után `Dataset.Labeled.from_json_lines` hívhatja `._Labeled` ahelyett, hogy egy címkézett adatkészletet hozna létre.
      + Ha `to_pandas_dataframe` egy címkézett adatkészletet hív meg a letöltési beállítással, most megadhatja, hogy felülírja-e a meglévő fájlokat.
      + Amikor `keep_columns` a `drop_columns` hívás, vagy ami egy idősorozat, címke vagy kép oszlop eldobását eredményezi, a megfelelő képességek az adatkészlethez is ellesznek.
      + Javítottuk a PyTorch betöltővel kapcsolatos problémákat a hívás kor. `dataset.to_torchvision()`

+ **Hibajavítások és fejlesztések**
  + **azúr-cli-ml**
    + Modell profilalkotás hozzáadása az előnézeti CLI-hez.
    + Javítja az Azure Storage-ban az AzureML CLI sikertelensítését okozó módosítási javításokat.
    + Hozzáadott terheléselosztó típusa az MLC-hez Az AKS-típusokhoz
  + **azureml-automl-core**
    + Javítottuk a maximális horizont észlelésével kapcsolatos problémát az idősorokban, hiányzó értékekkel és több szemcsével.
    + Kijavítottuk a hibákkal kapcsolatos problémát a keresztérvényesítési felosztások létrehozása során.
    + Cserélje le ezt a szakaszt egy markdown formátumú üzenetre, amely megjelenik a kibocsátási megjegyzésekben: -A rövid szemcsék jobb kezelése az előrejelzési adatkészletekben.
    + Kijavítottuk a felhasználói adatok naplózás közbeni maszkolásával kapcsolatos problémát. -Továbbfejlesztett fakitermelés a hibák során előrejelzés fut.
    + Psutil hozzáadása conda-függőségként az automatikusan létrehozott yml központi telepítési fájlhoz.
  + **azureml-contrib-mir**
    + Javítja az Azure Storage-ban az AzureML CLI sikertelensítését okozó módosítási javításokat.
  + **azureml-core**
    + Kijavít egy hibát, amely miatt az Azure Functions ben telepített modellek 500-as éveket eredményeznek.
    + Kijavítottunk egy problémát, amely miatt az Amlignore fájl nem lett alkalmazva a pillanatképeken.
    + Hozzáadott egy új API amlcompute.get_active_runs, amely visszaadja a generátor fut, és a várólistára helyezett fut egy adott amlcompute.
    + Hozzáadva a terheléselosztó típusa az MLC-hez az AKS-típusokhoz.
    + Hozzáadva append_prefix bool paraméter a run.py és download_artifacts_from_prefix download_artifacts_from_prefix artifacts_client download_files. Ez a jelző az eredeti fájlelérési út szelektív összeolvasztására szolgál, így csak a fájl vagy mappa neve kerül a output_directory
    + Javítsa ki az `run_config.yml` adatkészlet-használattal kapcsolatos deszerializálási problémát.
    + Amikor `keep_columns` a `drop_columns` hívás, vagy ami egy idősorozat oszlop eldobását eredményezi, a megfelelő képességek az adatkészlethez is el lesznek dobva.
  + **azureml-interpret**
    + Frissített értelmezőközösségi verzió a 0.1.0.3-ra
  + **azureml-train-automl**
    + Kijavítottunk egy hibát, amely miatt automl_step nem nyomtatható ki érvényesítési problémák.
    + Rögzített register_model a sikeres akkor is, ha a modell környezetében hiányzik a függőségek helyileg.
    + Kijavítottuk azt a problémát, amely miatt néhány távoli futtatás nem volt engedélyezve a docker-ben.
    + Adja hozzá a kivétel naplózását, amely miatt a helyi futtatás idő előtt sikertelen lesz.
  + **azureml-train-core**
    + Vegye figyelembe, resume_from fut a számítás automatikus hiperparaméter tuning legjobb gyermek fut.
  + **azureml-csővezeték-mag**
    + Rögzített paraméterkezelés a csővezeték argumentumának felépítésében.
    + Hozzáadva a folyamat leírása és a lépés típusa yaml paraméter.
    + Új yaml formátum a folyamatlépéshez, és a régi formátum elavultsági figyelmeztetése hozzáadva.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webes élmény

Az együttműködésen alapuló [https://ml.azure.com](https://ml.azure.com) munkaterület céloldal a továbbfejlesztett és átnevezett az Azure Machine Learning stúdió (előzetes verzió).

A stúdióból betaníthatja, tesztelheti, üzembe helyezheti és kezelheti az Azure Machine Learning-eszközöket, például adatkészleteket, folyamatokat, modelleket, végpontokat és egyebeket.

A következő webalapú szerzői eszközök a stúdióból:

| Webalapú eszköz | Leírás | Kiadás |
|-|-|-|
| Notebook virtuális gép(előzetes verzió) | Teljes körűen felügyelt felhőalapú munkaállomás | Alapszintű & Nagyvállalati |
| [Automatizált gépi tanulás](tutorial-first-experiment-automated-ml.md) (előzetes verzió) | Nincs kódélmény a gépi tanulási modell fejlesztésének automatizálásához | Enterprise |
| [Tervező](concept-designer.md) (előzetes verzió) | Drag-and-drop gépi tanulási modellező eszköz korábbi nevén a tervező | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Az Azure Machine Learning tervezőinek fejlesztései

+ Korábbi nevén a vizuális felület 
+    11 új [modul,](algorithm-module-reference/module-reference.md) beleértve az ajánlókat, az osztályozókat és a betanítási segédprogramokat, beleértve a szolgáltatástervezést, a keresztérvényesítést és az adatátalakítást.

### <a name="r-sdk"></a>R SDK 
 
Az adatszakértők és az AI-fejlesztők az [Azure Machine Learning R-hez készült SDK-t](tutorial-1st-r-experiment.md) használják gépi tanulási munkafolyamatok létrehozásához és futtatásához az Azure Machine Learning segítségével.

Az Azure Machine Learning SDK `reticulate` R használja a csomagot a Python SDK-hoz való kötéshez. Közvetlenül a Pythonhoz való kötéssel az SDK R lehetővé teszi a python SDK-ban bármely R-környezetben megvalósított alapvető objektumok és metódusok elérését.

Az SDK fő képességei a következők:

+    Felhőbeli erőforrások kezelése a gépi tanulási kísérletek monitorozásához, naplózásához és rendszerezéséhez.
+    A modellek et felhőalapú erőforrások használatával taníthatja be, beleértve a GPU-gyorsított modellbetanítást is.
+    A modellek webszolgáltatásokként az Azure Container Instances (ACI) és az Azure Kubernetes Service (AKS) webszolgáltatásokként való üzembe helyezése.

A teljes dokumentációt a [csomag webhelyén](https://azure.github.io/azureml-sdk-for-r) találja.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning-integráció az Event Griddel 

Az Azure Machine Learning mostantól az Event Grid erőforrás-szolgáltatója, az Azure Portalon vagy az Azure CLI-n keresztül konfigurálhatja a gépi tanulási eseményeket. A felhasználók létrehozhatnak eseményeket a futtatás befejezéséhez, a modellregisztrációhoz, a modell telepítéséhez és az észlelt adateltolódáshoz. Ezek az események az Event Grid által támogatott eseménykezelőkhöz irányíthatók. További részletekért lásd a gépi tanulási [eseményséma](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning), [fogalmak](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) és [oktatóanyag](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid) cikkek.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK python-hoz 1.0.72-es v1.0.72

+ **Új funkciók**
  + Adatkészlet-figyelők hozzáadása az [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift) csomagon keresztül, amely lehetővé teszi az idősorozat-adatkészletek figyelését az adatok eltolódása vagy más statisztikai változások idővel. Riasztások és események akkor is kiválthatók, ha elsodródást észlel, vagy az adatokon más feltételek teljesülnek. A részleteket a [dokumentációban](https://aka.ms/datadrift) találja.
  + Két új kiadás bejelentése (más néven termékváltozat szinonimaként) az Azure Machine Learningben. Ezzel a kiadással most már létrehozhat egy basic vagy Enterprise Azure Machine Learning munkaterületet. Az összes meglévő munkaterület alapértelmezés szerint az alapszintű kiadás, és az Azure Portalon vagy a stúdióban bármikor frissítheti a munkaterületet. Létrehozhat egy alapszintű vagy nagyvállalati munkaterületet az Azure Portalon. Kérjük, olvassa el [dokumentációnkat,](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) hogy többet tudjon meg. Az SDK-ból a munkaterület kiadása a munkaterület-objektum "sku" tulajdonságával határozható meg.
  + Az Azure Machine Learning Compute továbbfejlesztését is elkészítettük – mostantól megtekintheti a fürtök metrikáit (például a teljes csomópontokat, a futó csomópontokat, a teljes alapkvótát) az Azure Monitorban, a diagnosztikai naplók hibakereséshez való megtekintése mellett. Emellett megtekintheti a fürtön jelenleg futó vagy várólistára helyezett futtatásokat is, valamint a fürt különböző csomópontjainak IP-kiszolgálóit. Ezeket megtekintheti a portálon, vagy az SDK vagy a CLI megfelelő függvényei használatával.

  + **Előzetes verzió funkciói**
    + Az Azure Machine Learning Compute-ban a helyi SSD lemeztitkosításának előzetes verziójú támogatását nyújtjuk. Kérjük, emelje fel a technikai támogatási jegyet, hogy az előfizetés engedélyezési listán, hogy használja ezt a funkciót.
    + Az Azure Machine Learning batch-következtetések nyilvános előzetes verziója. Az Azure Machine Learning Batch-következtetés nagy, nem időérzékeny következtetéseket céloz meg. A Batch Inference költséghatékony következtetésszámítási skálázást biztosít, páratlan átviteli függással az aszinkron alkalmazásokhoz. Nagy átviteli sebességű, tűzzel és felejtésre optimalizált következtetéseket a nagy adatgyűjtemények felett.
    + [**azureml-contrib-adatkészlet**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Engedélyezett funkciók a címkézett adatkészlethez
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

+ **Hibajavítások és fejlesztések**
  + **azúr-cli-ml**
    + A CLI mostantól támogatja a modellcsomagolást.
    + Hozzáadott adatkészlet CLI. További információk:`az ml dataset --help`
    + A támogatott modellek (ONNX, scikit-learn és TensorFlow) telepítése és csomagolása inferenceConfig példány nélkül történő üzembe helyezésének és csomagolásának támogatása.
    + Felülírásjelző hozzáadása a szolgáltatás telepítéséhez (ACI és AKS) az SDK-ban és a CLI-ben. Ha rendelkezésre áll, felülírja a meglévő szolgáltatást, ha már létezik névvel rendelkező szolgáltatás. Ha a szolgáltatás nem létezik, új szolgáltatást hoz létre.
    + A modellek két új keretrendszerrel, az Onnx-szal és a Tensorflow-val regisztrálhatók. - A modellregisztráció mintabemeneti adatokat, mintakimeneti adatokat és erőforrás-konfigurációt fogad el a modellhez.
  + **azureml-automl-core**
    + Iteráció betanítása csak akkor futna egy alárendelt folyamatban, ha futásidejű megkötések vannak beállítva.
    + Hozzáadtunk egy védőkorlátot az előrejelzési feladatokhoz, hogy ellenőrizze, hogy egy adott max_horizon memóriaproblémát okoz-e az adott gépen. Ha így lesz, egy korlát üzenet jelenik meg.
    + Az olyan összetett frekvenciák támogatása, mint a 2 év és az 1 hónap. -Hozzáadott érthető hibaüzenet, ha a frekvencia nem lehet meghatározni.
    + Add azureml-defaults az automatikusan generált conda env megoldani a modell telepítési hiba
    + Az Azure Machine Learning-folyamat köztes adatainak táblázatos adatkészletgé történő konvertálásának engedélyezése és a. `AutoMLStep`
    + Oszlopcél-frissítés átvitelre.
    + Transzformátor paraméterfrissítés megvalósítása az Imputer és a HashOneHotEncoder számára a streameléshez.
    + Az aktuális adatméretet és a minimálisan szükséges adatméretet hozzáadta az érvényesítési hibaüzenetekhez.
    + A keresztellenőrzéshez szükséges minimális adatméret frissítése, hogy minden egyes ellenőrzési hajtásban legalább két minta biztosított.
  + **azureml-cli-gyakori**
    + A CLI mostantól támogatja a modellcsomagolást.
    + A modellek két új keretrendszerrel, az Onnx-szal és a Tensorflow-val regisztrálhatók.
    + A modellregisztráció mintabemeneti adatokat, mintakimeneti adatokat és erőforrás-konfigurációt fogad el a modellhez.
  + **azureml-contrib-gbdt**
    + rögzítette a notebook kiadási csatornáját
    + Figyelmeztetést adott hozzá a nem AmlCompute számítási célhoz, amelyet nem támogatunk
    + Hozzáadott LightGMB Estimator az azureml-contrib-gbdt csomaghoz
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + A CLI mostantól támogatja a modellcsomagolást.
    + Elavult figyelmeztetés hozzáadása elavult adatkészlet-API-khoz. Lásd: Dataset API https://aka.ms/tabular-datasetváltozása értesítés a.
    + Módosítsa [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) a regisztrációs név és a verzió visszaadásához, ha az adatkészlet regisztrálva van.
    + Javítsa ki azt a hibát, amelyet a ScriptRunConfig adatkészlettel argumentumként nem használható többször a kísérletfuttatásának elküldéséhez.
    + A futtatás során beolvasott adatkészletek nyomon lesznek követve, és [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) a futtatás részleteit tartalmazó lapon vagy a futtatás befejezése után hívással láthatók.
    + Az Azure Machine Learning-folyamat köztes adatainak táblázatos adatkészletgé történő konvertálásának engedélyezése és a. [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)
    + A támogatott modellek (ONNX, scikit-learn és TensorFlow) telepítése és csomagolása inferenceConfig példány nélkül történő üzembe helyezésének és csomagolásának támogatása.
    + Felülírásjelző hozzáadása a szolgáltatás telepítéséhez (ACI és AKS) az SDK-ban és a CLI-ben. Ha rendelkezésre áll, felülírja a meglévő szolgáltatást, ha már létezik névvel rendelkező szolgáltatás. Ha a szolgáltatás nem létezik, új szolgáltatást hoz létre.
    +  A modellek két új keretrendszerrel, az Onnx-szal és a Tensorflow-val regisztrálhatók. A modellregisztráció mintabemeneti adatokat, mintakimeneti adatokat és erőforrás-konfigurációt fogad el a modellhez.
    + Új adattár hozzáadva az Azure Database for MySQL-hez. Hozzáadott példa az Azure Database for MySQL használatával a DataTransferStep az Azure Machine Learning folyamatokban.
    + Hozzáadott funkciók hozzáadása és eltávolítása címkék kísérletek Hozzáadott funkciók eltávolítása címkék fut
    + Felülírásjelző hozzáadása a szolgáltatás telepítéséhez (ACI és AKS) az SDK-ban és a CLI-ben. Ha rendelkezésre áll, felülírja a meglévő szolgáltatást, ha már létezik névvel rendelkező szolgáltatás. Ha a szolgáltatás nem létezik, új szolgáltatást hoz létre.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Költözött `azureml-contrib-datadrift` a`azureml-datadrift`
    + Az idősorok adatkészletei nek az eltolódáshoz és egyéb statisztikai intézkedésekhez való figyeléséhez nyújtott támogatás
    + Új `create_from_model()` módszerek `create_from_dataset()` és [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) az osztály. A `create()` metódus elavult.
    + A vizualizációk módosítása a Python ban és a felhasználói felületen az Azure Machine Learning stúdióban.
    + Támogatja a heti és havi monitor ütemezése mellett a napi adatkészlet-figyelők.
    + Támogatja az adatfigyelő mérőszámainak háttérkitöltését az adatkészlet-figyelők előzményadatainak elemzéséhez.
    + Különböző hibajavítások
  + [**azureml-csővezeték-mag**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + az azureml-dataprep már nincs szükség az Azure Machine `yaml` Learning-folyamat a folyamatfájlból való futtatásához.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Add azureml-defaults az automatikusan generált conda env megoldani a modell telepítési hiba
    + AutoML távoli betanítás most már tartalmazza az azureml-defaults, hogy újra felhasználja a betanítási env a következtetés.
  + **azureml-train-core**
    + Hozzáadott PyTorch 1.3 [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) támogatás a becslésben

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Vizuális felület (előzetes verzió)

+ Az Azure Machine Learning vizuális felületét (előzetes verzió) átdolgozták az [Azure Machine Learning-folyamatokon](concept-ml-pipelines.md)való futtatáshoz. A vizuális felületen készített folyamatok (korábbi nevén kísérletek) most már teljes mértékben integrálva vannak az alapvető Azure Machine Learning-környezettel.
  + Egységes felügyeleti élmény az SDK-eszközökkel
  + Vizuális kapcsolati modellek, folyamatok és végpontok verziószámozása és nyomon követése
  + Újratervezett felhasználói felület
  + Hozzáadott kötegkövetkeztetés központi telepítése
  + Hozzáadott Azure Kubernetes szolgáltatás (AKS) támogatása a következtetés számítási célok
  + Új Python-lépés folyamat szerzői munkafolyamat
  + Új [céloldal](https://ml.azure.com) a vizuális szerzői eszközökhöz

+ **Új modulok**
  + Matematikai művelet alkalmazása
  + SQL-átalakítás alkalmazása
  + Klip értékek
  + Adatok összegzése
  + Importálás SQL-adatbázisból

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK python-hoz 1.0.69-es v1.0.69

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + A modellmagyarázatok futtatási célokra való korlátozása az egyes futtatások hozadékolása helyett. A viselkedés módosítása a helyi, a távoli és az ADB esetében.
    + A felhasználói felület igény szerinti modellmagyarázatai támogatással bővültek
    + Hozzáadott psutil függősége, `automl` és tartalmazza psutil, mint egy conda függőség amlcompute.
    + Javítottuk a heurisztikus lemaradások és a gördülő ablakméretek okozta problémát az előrejelzési adatkészleteken, amelyek közül néhány lineáris algebrahibát okozhat
      + Az előrejelzési futtatások heurisztikusan meghatározott paramétereinek nyomtatása hozzáadva.
  + **azureml-contrib-datadrift**
    + Hozzáadott védelem létrehozása közben kimeneti metrikák, ha az adatkészlet szinteltolódás nem az első szakaszban.
  + **azureml-contrib-interpret**
    + az azureml-contrib-explain-model csomag átnevezve azureml-contrib-interpret-ra
  + **azureml-core**
    + HOZZÁADOTT API az adatkészletek regisztrációjának megszüntetése érdekében. `dataset.unregister_all_versions()`
    + az azureml-contrib-explain-model csomag átnevezése azureml-contrib-interpret-ra lett átnevezve.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + HOZZÁADOTT API az adatkészletek regisztrációjának megszüntetése érdekében. Adatkészlet. [unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Adatkészlet API-val ellenőrizve az adatok módosított idejét. `dataset.data_changed_time`.
    + A `FileDataset` , és `TabularDataset` `PythonScriptStep` `HyperDriveStep` az Azure `EstimatorStep`Machine Learning Pipeline-ban használható és bemenetként
    + `FileDataset.mount` A nagy számú fájlt tartalmazó mappák teljesítménye javult
    + [A FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) és [a TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) a [PythonScriptStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) [estimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)és [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) bemenetként használható az Azure Machine Learning Pipeline-ban.
    + A FileDataset teljesítménye. [mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) javult a nagy számú fájlt tartalmazó mappák esetében
    + URL-címet adott hozzá az ismert hibajavaslatokhoz a futtatás részleteiben.
    + Javítottunk egy hibát a run.get_metrics-ban, ahol a kérelmek sikertelenek lennének, ha egy futtatástúl sok gyermeket kapna
    + Javítottunk egy hibát a [run.get_metrics-ban,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) ahol a kérelmek sikertelenek lennének, ha egy futtatástúl sok gyermeket kapna
    + Az Arcadia fürt hitelesítésének támogatása hozzáadva.
    + Egy kísérlet objektum létrehozása lekéri vagy létrehozza a kísérletet az Azure Machine Learning munkaterületen az előzmények nyomon követéséhez. A kísérletazonosító és az archivált idő a létrehozáskori kísérlet objektumban van feltöltve. Példa: kísérlet = Kísérlet(munkaterület, "Új kísérlet") experiment_id = experiment.id archív() és újraaktiválás() olyan függvények, amelyek egy kísérletben meghívhatók, hogy elrejtsék és visszaállítsák a kísérletet a felhasználói környezetben való megjelenítésből vagy alapértelmezés szerint a listás kísérletek hívásából. Ha egy új kísérlet az archivált kísérlet nevével azonos névvel jön létre, új név átadásával átnevezheti az archivált kísérletet. Csak egy aktív kísérlet lehet egy adott névvel. Példa: experiment1 = Experiment(workspace, "Active Experiment") experiment1.archive() # Hozzon létre új aktív kísérletet az archivált névvel. kísérlet2. = Kísérlet(munkaterület, "Aktív kísérlet") kísérlet1.reactivate(new_name="Előző aktív kísérlet") A Kísérlet statikus metóduslistája() névszűrőt és ViewType szűrőt vehet fel. ViewType értékek "ACTIVE_ONLY", "ARCHIVED_ONLY" és "ALL" Példa: archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + Támogatás a modelltelepítés és a szolgáltatásfrissítés környezetének használatával
  + **azureml-datadrift**
    + A DataDriftDector osztály show attribútuma többé nem támogatja a választható "with_details" argumentumot. A show attribútum csak az adateltolódási együtthatót és a jellemzőoszlopok adateltolódási hozzájárulását mutatja be.
    + A DataDriftDetector attribútum "get_output" viselkedésének változásai:
      + A bemeneti paraméter start_time, end_time nem kötelező;
      + Adott start_time és/vagy end_time bevitele egy adott run_id ugyanabban a meghívással értékhiba-kivételt eredményez, mivel kölcsönösen kizárják egymást
      + A megadott start_time és/vagy end_time bemeneti adatok szerint csak az ütemezett futtatások eredményei lesznek visszaadva;
      + Az "daily_latest_only" paraméter elavult.
    + Az adatkészlet-alapú adattúllókimenetek lekérésének támogatása.
  + **azureml-explain-modell**
    + Átnevezi az AzureML-explain-model csomagot AzureML-interpret-ra, és a régi csomagot a visszamenőleges kompatibilitás érdekében egyelőre megtartja
    + javított `automl` hiba a nyers magyarázatokkal, amelyek besorolási feladatra vannak állítva, ahelyett, hogy alapértelmezés szerint regressziós lenne a ExplanationClient-ból való letöltéskor
    + Közvetlenül `ScoringExplainer` a létrehozandó támogatás hozzáadása`MimicWrapper`
  + **azureml-csővezeték-mag**
    + Jobb teljesítmény a nagy csővezetékek létrehozásához
  + **azureml-train-core**
    + TensorFlow 2.0 támogatás hozzáadva a TensorFlow Estimator-hoz
  + **azureml-train-automl**
    + Egy [kísérlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) objektum létrehozása lekéri vagy létrehozza a kísérletet az Azure Machine Learning munkaterületen az előzmények nyomon követéséhez. A kísérletazonosító és az archivált idő a létrehozáskori kísérlet objektumban van feltöltve. Példa:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [Az archive()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) és [az újraaktiválás()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) olyan függvények, amelyek egy kísérletben meghívhatók, hogy elrejtsék és visszaállítsák a kísérletet a felhasználói környezetben való megjelenítéstől, vagy alapértelmezés szerint visszaadják a listás kísérletek hívásában. Ha egy új kísérlet az archivált kísérlet nevével azonos névvel jön létre, új név átadásával átnevezheti az archivált kísérletet. Csak egy aktív kísérlet lehet egy adott névvel. Példa:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        A Kísérlet statikus [metóduslistája()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) névszűrőt és ViewType szűrőt vehet fel. A ViewType értékek a következők: "ACTIVE_ONLY", "ARCHIVED_ONLY" és "ALL". Példa:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Támogatás a modellüzembe helyezés környezetének és a szolgáltatásfrissítésnek a használatával.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + A Show attribútum [A DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) osztály nem támogatja a választható érv "with_details" többé. A show attribútum csak az adateltolódási együtthatót és a jellemzőoszlopok adateltolódási hozzájárulását mutatja be.
    + A DataDriftDetector függvényhttps://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) [get_output] viselkedése megváltozik:
      + A bemeneti paraméter start_time, end_time nem kötelező;
      + Adott start_time és/vagy end_time, amelyek egy adott run_id ugyanabban a meghívással rendelkeznek, értékhiba-kivételt eredményeznek, mivel kölcsönösen kizárják egymást;
      + A megadott start_time és/vagy end_time bemeneti adatok szerint csak az ütemezett futtatások eredményei lesznek visszaadva;
      + Az "daily_latest_only" paraméter elavult.
    + Az adatkészlet-alapú adattúllókimenetek lekérésének támogatása.
  + **[azureml-explain-modell](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Átnevezi az AzureML-explain-model csomagot AzureML-interpret-ra, és a régi csomagot a visszamenőleges kompatibilitás érdekében egyelőre megtartja.
    + javított AutoML hiba nyers magyarázatokkal, amelyek besorolási feladatra vannak állítva, ahelyett, hogy alapértelmezés szerint regressziót töltöttek volna le a ExplanationClient programból.
    + Támogatás hozzáadása a Közvetlenül a [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper) használatával létrehozandó [ScoringExplainer-hez](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py)
  + **[azureml-csővezeték-mag](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Jobb teljesítmény a nagy folyamatlétrehozásához.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + TensorFlow 2.0 támogatás hozzáadva a [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator-hoz.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + A szülőfuttatás a továbbiakban nem lesz sikertelen, ha a beállítási iteráció sikertelen, mivel a vezénylésmár gondoskodik róla.
    + Hozzáadott local-docker és local-conda támogatás AutoML kísérletekhez
    + Hozzáadott helyi-docker és a helyi-conda támogatása AutoML kísérletek.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Új webes élmény (előzetes verzió) az Azure Machine Learning-munkaterületekhez

Az [új munkaterületi portál](https://ml.azure.com) Kísérlet lapja frissült, így az adatszakértők hatékonyabban figyelhetik a kísérleteket. A következő funkciókat fedezheti fel:
+ Metaadatok kísérletezése a kísérletek listájának egyszerű szűréséhez és rendezéséhez
+ Egyszerűsített és performant kísérlet részleteit oldalak, amelyek lehetővé teszik, hogy vizualizálja és hasonlítsa össze a fut
+ Új kialakítás a részletes oldalak futtatásához, hogy megértse és figyelje a betanítási futtatásokat

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK python-hoz 1.0.65-ös v1.0.65

  + **Új funkciók**
    + Válogatott környezetek hozzáadása. Ezeket a környezeteket előre konfigurálták a gyakori gépi tanulási feladatok kódtáraival, és előre felépítésük és Docker-rendszerképekként gyorsítótárazták őket a gyorsabb végrehajtás érdekében. Alapértelmezés szerint a Workspace környezetlistájában jelennek meg az "AzureML" előtaggal.
    + Válogatott környezetek hozzáadása. Ezeket a környezeteket előre konfigurálták a gyakori gépi tanulási feladatok kódtáraival, és előre felépítésük és Docker-rendszerképekként gyorsítótárazták őket a gyorsabb végrehajtás érdekében. Alapértelmezés szerint a [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)környezetlistájában jelennek meg az "AzureML" előtaggal.

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Hozzáadva az ONNX konverziós támogatása az ADB-hez és a HDI-hez

+ **Előzetes verzió funkciói**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Támogatott BERT és BiLSTM szövegként featurizer (csak előnézet)
    + Támogatott jellemző testreszabása oszlop célra és transzformátor paraméterek (csak előnézet)
    + Támogatott nyers magyarázatok, ha a felhasználó engedélyezi a modell magyarázatát a betanítás során (csak előzetes verzió)
    + Hozzáadott próféta `timeseries` előrejelzéshez, mint a vonattalhető csővezeték (csak előzetes verzió)

  + **azureml-contrib-datadrift**
    + Az azureml-contrib-datadriftről az azureml-datadrift-re áthelyezett csomagok; a `contrib` csomag egy későbbi kiadásban eltávolításra kerül

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Bevezetett FeaturizationConfig az AutoMLConfig és AutoMLBaseSettings
    + Bevezetett FeaturizationConfig az [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) és AutoMLBaseSettings
      + Oszlopcélja a felvázolás felülbírálása adott oszlop- és szolgáltatástípussal
      + A transzformátor paramétereinek felülbírálása
    + Hozzáadott eprecation üzenet explain_model() és retrieve_model_explanations()
    + Próféta hozzáadva vonattal kiegészíthető csővezetékként (csak előzetes verzió)
    + Hozzáadott edeprecation üzenet explain_model() és retrieve_model_explanations().
    + Próféta hozzáadva vonattal kiegészíthető csővezetékként (csak előzetes verzió).
    + Hozzáadott támogatása automatikus észlelése cél elmarad, gördülő ablak mérete és maximális horizonton. Ha target_lags, target_rolling_window_size vagy max_horizon valamelyike "auto"-ra van állítva, a heurisztika lesz alkalmazva a megfelelő paraméter értékének becslésére a betanítási adatok alapján.
    + Rögzített előrejelzés abban az esetben, ha az adatkészlet egy szemcseoszlopot tartalmaz, ez a szemcsézettség numerikus típusú, és hézag van a vonat és a tesztkészlet között.
    + Javítottuk a hibaüzenetet a távoli futtatásban lévő duplikált indexről az előrejelzési feladatokban
    + Rögzített előrejelzés abban az esetben, ha az adatkészlet egy szemcseoszlopot tartalmaz, ez a szemcse numerikus típusú, és hézag van a vonat és a tesztkészlet között.
    + Javítottuk a hibakérő üzenetet a távoli futtatásban az előrejelzési feladatokban.
    + Hozzáadott egy korlát, hogy ellenőrizze, hogy egy adatkészlet kiegyensúlyozatlan-e vagy sem. Ha ez így van, akkor egy korlát üzenet lenne írva a konzolra.
  + **azureml-core**
    + Hozzáadott képes letölteni a SAS URL-t a modell a tárolóban a modell objektumon keresztül. Pl.: model.get_sas_url()
    + Bevezetés `run.get_details()['datasets']` a beküldött futtatáshoz társított adatkészletek betöltéséhez
    + Add `Dataset.Tabular.from_json_lines_files` API-t, hogy hozzon létre egy táblázatosDataset a JSON Lines fájlokat. Ha többet szeretne megtudni erről a táblázatos adatokról a JSON Lines fájlokban a TabularDataset-en, kérjük, látogasson el https://aka.ms/azureml-data a dokumentációra.
    + További virtuálisgép-méretmezők (operációsrendszer-lemez, GPU-k száma) hozzáadása a supported_vmsizes () függvényhez
    + További mezőket adott hozzá a list_nodes () függvényhez a futtatás, a privát és a nyilvános IP, a port stb.
    + Új mező megadása a fürt kiépítéssorán --remotelogin_port_public_access amely afürt létrehozásakor nyitva vagy zárva szeretné hagyni az SSH-portot. Ha nem adja meg, a szolgáltatás intelligensen megnyitja vagy bezárja a portot attól függően, hogy a fürtöt virtuális hálózaton belül telepíti-e.
  + **azureml-explain-modell**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Hozzáadott képes letölteni a SAS URL-t a modell a tárolóban a modell objektumon keresztül. Pl.: modell. [get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Mutasd be a futást. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] a beküldött futtatáshoz társított adatkészletek lekérte
    + API `Dataset.Tabular`hozzáadása . [from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) a JSON Lines fájlokból származó táblázatos adatkészlet létrehozásához. Ha többet szeretne megtudni erről a táblázatos adatokról a JSON Lines fájlokban a TabularDataset-en, kérjük, látogasson el https://aka.ms/azureml-data a dokumentációra.
    + További virtuálisgép-méretmezők (operációsrendszer-lemez, GPU-k száma) hozzáadása a [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) függvényhez
    + További mezőket adott hozzá a [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) függvényhez a futtatás, a privát és a nyilvános IP, a port stb.
    + Új mező tãbiliálása során adható `--remotelogin_port_public_access` [meg,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) amely engedélyezve vagy letiltva állítható be attól függően, hogy az SSH-portot nyitva vagy zárva szeretné-e hagyni a fürt létrehozásakor. Ha nem adja meg, a szolgáltatás intelligensen megnyitja vagy bezárja a portot attól függően, hogy a fürtöt virtuális hálózaton belül telepíti-e.
  + **[azureml-explain-modell](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Továbbfejlesztett dokumentáció a magyarázat kimenetek a besorolási forgatókönyvben.
    + Hozzáadva az előre jelzett y értékek feltöltésének lehetősége a kiértékelési példák magyarázatához. Hasznosabb vizualizációkat old meg.
    + Hozzáadta a Magyarázó tulajdonságot a MimicWrapper-hez, hogy lehetővé tegye az alapul szolgáló MimicExplainer beszerzését.
  + **azureml-csővezeték-mag**
    + Hozzáadva a modul, a ModuleVersion és a ModuleStep leírásához hozzáadott jegyzetfüzet
  + **azureml-folyamatlépések**
    + RScriptStep hozzáadása az R parancsfájl AML-folyamaton keresztül történő futtatásának támogatásához.
    + Javítottuk az AzureBatchStep szolgáltatásban a "assignment for parameter SubscriptionId nincs megadva" hibaüzenetet okozó metaadat-paraméterek elemzésével.
  + **azureml-train-automl**
    + Támogatott training_data, validation_data, label_column_name, weight_column_name adatbeviteli formátumként
    + Hozzáadott eprecation üzenet explain_model() és retrieve_model_explanations()
  + **[azureml-csővezeték-mag](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Hozzáadtunk egy [jegyzetfüzetet](https://aka.ms/pl-modulestep) a [Module](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) és [A ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)leírásához.
  + **[azureml-folyamatlépések](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + [RScriptStep hozzáadása](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) az R parancsfájl AML-folyamaton keresztül történő futtatásának támogatásához.
    + Javítottuk az [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) szolgáltatásban a "assignment for parameter SubscriptionId nincs megadva" hibaüzenetet okozó metaadat-paraméterek elemzésével.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Támogatott training_data, validation_data, label_column_name, weight_column_name adatbeviteli formátumként.
    + Hozzáadott edeprecation üzenet [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) és [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK python-hoz 1.0.62-es v1.0.62

+ **Új funkciók**
  + Bemutatta a `timeseries` tabularDataset tulajdonságát. Ez a tulajdonság lehetővé teszi a tabularDataset adatok egyszerű időbélyeg-szűrését, például az összes adat időtartomány vagy a legfrissebb adatok közötti levételét. Ha többet szeretne `timeseries` megtudni erről a TabularDataset tulajdonságáról, kérjük, látogasson el https://aka.ms/azureml-data a dokumentációra vagy https://aka.ms/azureml-tsd-notebook egy példa jegyzetfüzetre.
  + Engedélyezve az edzést a Táblázatos adatkészlettel és a FileDataset beállítással. Kérjük, látogasson el https://aka.ms/dataset-tutorial egy példa notebook.

  + **azureml-train-core**
      + `Nccl` Hozzáadva `Gloo` és támogatva a PyTorch becslésben

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + A "lag_length" autoML-beállítás és a LaggingTransformer elavulttá vált.
    + A bemeneti adatok helyes érvényesítése, ha adatfolyam-formátumban vannak megadva
    + Módosította a fit_pipeline.py-t a json grafikon létrehozásához és az összetevőkre való feltöltéshez.
    + Renderelte a `userrun` `Cytoscape`grafikon alatt a használatával.
  + **azureml-core**
    + Az ADB-kódban újra áttekintette a kivételkezelést, és módosította az új hibakezelést
    + Automatikus MSI-hitelesítés hozzáadva a notebook virtuális gépekhez.
    + Javítja a hibát, ahol sérült vagy üres modelleket lehet feltölteni a sikertelen újrapróbálkozások miatt.
    + Javítva a `DataReference` hiba, `DataReference` ahol a név megváltozik, `as_upload` `as_download`amikor `as_mount`az üzemmód megváltozik (pl. híváskor , vagy ).
    + Gyártmány `mount_point` és `target_path` választható a és `FileDataset.mount` `FileDataset.download`a számára.
    + Kivétel, hogy az időbélyeg oszlop nem található lesz kidobva, ha az idő sorozatok kapcsolódó API-t nem finom időbélyeg oszlop hozzárendelt, vagy a hozzárendelt időbélyeg oszlopok eldobása.
    + A sorozatok időoszlopait dátum típusú oszlophoz kell rendelni, ellenkező esetben kivételre van lehetőség.
    + Az API "with_timestamp_columns" hozzárendeléséhez rendelt sorozatoszlopok nem tartalmazhatnak Nincs értéket finom/durva időbélyeg oszlopnévvel, amely törli a korábban hozzárendelt időbélyeg-oszlopokat.
    + Kivétel lesz kidobva, ha vagy durva szemcse vagy finom szemcsés időbélyeg oszlop eldobódik a felhasználó jelzésével, hogy a csepegés történhet, miután vagy kizárva időbélyeg oszlop csepegtető lista vagy hívás with_time_stamp nincs érték kiadásához időbélyeg oszlopok
    + Kivétel lesz kidobva, ha vagy durva szemcse vagy finom szemcsés időbélyeg oszlop nem szerepel a keep oszlopok listáját, jelezve a felhasználó számára, hogy a tartás lehet tenni, miután vagy beleértve az időbélyeg oszlop lista megtartása oszlop lista vagy hívja with_time_stamp nincs érték kiadásidőbélyeg oszlopok.
    + A regisztrált modell méretével kapcsolatos naplózás hozzáadva.
  + **azureml-explain-modell**
    + Rögzített figyelmeztetés nyomtatott konzol, ha a "csomagolás" python csomag nincs telepítve: "A régebbi, mint a támogatott változata lightgbm, kérjük, frissítsen a verzió nagyobb, mint 2.2.1"
    + Javítottuk a letöltési modell magyarázatát a globális magyarázatokkal, számos funkcióval
    + Javítottuk, hogy a magyarázónem tartalmaz inicializálási példákat a kimeneti magyarázaton
    + Javítottunk egy nem módosítható hibát a beállított tulajdonságokon, amikor két különböző típusú modellt használó magyarázó ügyféllel töltött fel
    + Hozzáadott egy get_raw param pontozási explainer .explain(), így egy pontozási magyarázó vissza mind a mesterséges és a nyers értékeket.
  + **azureml-train-automl**
    + Az AutoML nyilvános API-kat vezetett `automl` be az SDK magyarázatából származó magyarázat-magyarázatok támogatásához – az AutoML-magyarázatok újabb támogatásának módja az AutoML-featurization leválasztásával és az SDK - Integrated raw explain support from azureml explain SDK for AutoML models.
    + Az azureml-defaults eltávolítása a távoli betanítási környezetekből.
    + Módosította az alapértelmezett gyorsítótár-tároló helyét a FileCacheStore-ról az Azure Databricks kódútvonalán az AutoML-re vonatkozóan az Azure Databricks-kód elérési útjára.
    + A bemeneti adatok helyes érvényesítése, ha adatfolyam-formátumban vannak megadva
  + **azureml-train-core**
    + Visszaállítva source_directory_data_store eprecáció.
    + Az azureml telepített csomagverzióinak felülbírálásának lehetősége.
    + Dockerfile támogatás `environment_definition` hozzáadva a paraméterben a becslésekben.
    + Egyszerűsített elosztott betanítási paraméterek a becslőkben.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Új webes élmény (előzetes verzió) az Azure Machine Learning-munkaterületekhez
Az új webes felület lehetővé teszi az adatszakértők és adatmérnökök számára, hogy teljes körű gépi tanulási életciklusukat az adatok előkészítésétől és megjelenítésétől a modellek egyetlen helyen történő betanítását és üzembe helyezéséig hajtsák végre.

![Azure Machine Learning-munkaterületi felhasználói felület (előzetes verzió)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Főbb jellemzők:**

Ezzel az új Azure Machine Learning-felülettel most a következőket teheti:
+ Jegyzetfüzetek kezelése vagy hivatkozás a Jupyterhez
+ [Automatizált gépelési kísérletek futtatása](tutorial-first-experiment-automated-ml.md)
+ [Adatkészletek létrehozása helyi fájlokból, adattárakból & webfájlokból](how-to-create-register-datasets.md)
+ Fedezze fel & készítsen elő adatkészleteket a modell létrehozásához
+ A modellek adateltolódásának figyelése
+ Legutóbbi erőforrások megtekintése irányítópultról

Ebben a kiadásban a következő böngészők támogatottak: Chrome, Firefox, Safari és Microsoft Edge Preview.

**Ismert problémák:**

1. Frissítse a böngészőt, ha azt látja, "Valami elromlott! Hiba az adattömbfájlok betöltésekor" a telepítés folyamatban van.

1. A jegyzetfüzetekben és fájlokban nem lehet fájlokat törölni vagy átnevezni. A Nyilvános előzetes verzió során a Jupyter felhasználói felületvagy a terminál a notebook virtuális gépben frissítési fájlműveletek végrehajtásához használható. Mivel ez egy csatlakoztatott hálózati fájlrendszer, a notebook virtuális gépen végzett összes módosítás azonnal megjelenik a jegyzetfüzet-munkaterületen.

1. Az SSH-hoz a notebook virtuális gépbe:
   1. Keresse meg a virtuális gép telepítése során létrehozott SSH-kulcsokat. Vagy keresse meg a kulcsokat az Azure Machine Learning-munkaterületen, > nyissa meg a Számítási lapot, > keresse meg a notebook virtuális gépet a listában, > nyissa meg a tulajdonságait: másolja a kulcsokat a párbeszédpanelről.
   1. Importálja ezeket a nyilvános és privát SSH-kulcsokat a helyi számítógépre.
   1. Használja őket az SSH-hoz a notebook virtuális gép.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK python-hoz 1.0.60-as v1.0.60

+ **Új funkciók**
  + Bevezetett FileDataset, amely hivatkozik egy vagy több fájlt az adattárak vagy nyilvános URL-eket. A fájlok bármilyen formátumúak lehetnek. FileDataset lehetővé teszi, hogy töltse le vagy csatolja a fájlokat a számításhoz. Ha többet szeretne megtudni a https://aka.ms/file-datasetFileDataset-ről, látogasson el a látogatásra.
  + Hozzáadott folyamat Yaml támogatása PythonScript lépés, Adla Lépés, Databricks lépés, DataTransferStep és AzureBatch lépés

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Az AutoArima mostantól csak előzetes verzióra javasolható folyamat.
    + Továbbfejlesztett hibajelentés az előrejelzéshez.
    + Javította a naplózást az előrejelzési feladatokban általános helyett egyéni kivételekkel.
    + Eltávolította az ellenőrzést max_concurrent_iterations, hogy kevesebb, mint az összes ismétlések száma.
    + Az AutoML-modellek mostantól autoML-kivételeket adnak vissza
    + Ez a kiadás javítja az automatizált gépi tanulási helyi futtatások végrehajtási teljesítményét.
  + **azureml-core**
    + A Dataset.get_all(workspace) bemutatkozása, amely a regisztrációs nevükkel megadott objektumok szótárát `TabularDataset` adja `FileDataset` vissza.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Mutassa `parition_format` be `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files`argumentumként a és a . Az egyes adatelérési utak partícióinformációi a megadott formátum alapján oszlopokba lesznek kibontva. A(z) "{column_name}" karakterláncoszlopot hoz létre, a "{column_name:yyyy/HH/dd/hh/mm/ss}" pedig datetime oszlopot, ahol a "yyyy", "MM", "dd", "HH", "mm" és "ss" az év, hónap, nap, óra, perc és második szöveg kibontására szolgál a datetime típushoz. A partition_format az első partíciókulcs pozíciójától a fájlelérési út végéig kell indulnia. Például, mivel az utat ".. /USA/2019/01/01/data.csv", ahol a partíció ország és idő szerint van, partition_format="/{Country}/{PartitionDate:yyyy/HH/ndz}/data.csv" karakterláncoszlopot hoz létre "Ország" oszlop "USA" értékkel és "PartitionDate" date column "2019-01-01" értékkel.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Mutassa `partition_format` be `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files`argumentumként a és a . Az egyes adatelérési utak partícióinformációi a megadott formátum alapján oszlopokba lesznek kibontva. A(z) "{column_name}" karakterláncoszlopot hoz létre, a "{column_name:yyyy/HH/dd/hh/mm/ss}" pedig datetime oszlopot, ahol a "yyyy", "MM", "dd", "HH", "mm" és "ss" az év, hónap, nap, óra, perc és második szöveg kibontására szolgál a datetime típushoz. A partition_format az első partíciókulcs pozíciójától a fájlelérési út végéig kell indulnia. Például, mivel az utat ".. /USA/2019/01/01/data.csv", ahol a partíció ország és idő szerint van, partition_format="/{Country}/{PartitionDate:yyyy/HH/ndz}/data.csv" karakterláncoszlopot hoz létre "Ország" oszlop "USA" értékkel és "PartitionDate" date column "2019-01-01" értékkel.
    + `to_csv_files`és `to_parquet_files` metódusokkal egészült ki. `TabularDataset` Ezek a módszerek `TabularDataset` lehetővé `FileDataset` teszik az a és a közötti átalakítást az adatok megadott formátumú fájlokká alakításával.
    + Automatikusan jelentkezzen be az alaprendszerkép-beállításjegyzékbe a Model.package() által létrehozott Docker-fájl mentésekor.
    + "gpu_support" már nincs szükség; Az AML mostantól automatikusan észleli és használja az nvidia docker bővítményt, ha az elérhetővé válik. Ez lesz távoli -ban egy jövő mentesít.
    + A PipelineDrafts létrehozásához, frissítéséhez és használatához nyújtott támogatás.
    + Ez a kiadás javítja az automatizált gépi tanulási helyi futtatások végrehajtási teljesítményét.
    + A felhasználók név szerint lekérdezhetik a futtatási előzmények metrikákat.
    + Javította a naplózást az előrejelzési feladatokban általános helyett egyéni kivételekkel.
  + **azureml-explain-modell**
    + Hozzáadott feature_maps paraméter az új MimicWrapper, amely lehetővé teszi a felhasználók számára, hogy nyers funkció magyarázatokat.
    + Az adatkészlet-feltöltések alapértelmezés szerint ki vannak kapcsolva a magyarázatfeltöltéshez, és újra engedélyezhetők a upload_datasets=True funkcióval
    + Hozzáadott "is_law" szűrési paraméterek magyarázat lista és letöltési funkciókat.
    + Módszert `get_raw_explanation(feature_maps)` ad a globális és a helyi magyarázó objektumokhoz is.
    + Hozzáadott verzió ellenőrzése lightgbm nyomtatott figyelmeztetést, ha az alábbi támogatott verzió
    + Optimalizált memóriahasználat a magyarázatok kötegelésekén
    + Az AutoML-modellek mostantól autoML-kivételeket adnak vissza
  + **azureml-csővezeték-mag**
    + A PipelineDrafts létrehozásához, frissítéséhez és használatához nyújtott támogatás sal használható a módosítható folyamatdefiníciók karbantartásához és interaktív futtatásához.
  + **azureml-train-automl**
    + Létrehozott funkció a GPU-képes pytorch v1.1.0, :::no-loc text="cuda"::: toolkit 9.0, pytorch-transzformátorok speciális verzióinak telepítéséhez, amely szükséges a BERT/ XLNet távoli python futásidejű környezetben való engedélyezéséhez.
  + **azureml-train-core**
    + Néhány hiperparaméter-térdefiníciós hiba korai meghibásodása közvetlenül az SDK-ban, nem pedig a kiszolgálóoldalon.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK 1.1.14-es v1.14
+ **Hibajavítások és fejlesztések**
  + Engedélyezve van az ADLS/ADLSGen2 íráshoz nyers elérési úttal és hitelesítő adatokkal.
  + Javítva egy `include_path=True` hiba, amely `read_parquet`miatt nem működik a .
  + Javítottuk `to_pandas_dataframe()` az "Érvénytelen tulajdonságérték: hostSecret" kivétel által okozott hibát.
  + Javítva egy hiba, amely szerint a fájlok at nem lehetett olvasni a DBFS-en Spark módban.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK python-hoz 1.0.57-es v1.0.57
+ **Új funkciók**
  + Az `TabularDataset` AutomatedML általi felhasználásengedélyezve van. Ha többet `TabularDataset`szeretne megtudni https://aka.ms/azureml/howto/createdatasetsa , kérjük, látogasson el.

+ **Hibajavítások és fejlesztések**
  + **automl-client-core-nativeclient**
    + Javítva a hiba, akkor merült fel, amikor a képzési és / vagy érvényesítési címkék (y és y_valid) formájában pandák dataframe, de nem numpy tömb.
    + Frissítve a `RawDataContext` felület, hogy hozzon `AutoMLBaseSettings` létre egy, hogy csak az adatok és az objektum.
    +  Lehetővé teszi az AutoML-felhasználók számára, hogy eldobják az előrejelzéshez nem elég hosszú betanítási sorozatot. - Lehetővé teszi az AutoML-felhasználók számára, hogy olyan szemcséket dobjanak ki a tesztkészletből, amelyek előrejelzéskor nem szerepelnek a betanítási készletben.
  + **azúr-cli-ml**
    + Most már frissítheti a TLS/SSL tanúsítványt az AKS-fürtön telepített pontozási végponthoz mind a Microsoft által létrehozott, mind az ügyfél tanúsítványához.
  + **azureml-automl-core**
    + Kijavítottuk az AutoML azon problémája miatt, amely miatt a hiányzó címkékkel rendelkező sorok nem lettenek megfelelően eltávolítva.
    + Továbbfejlesztett hibanaplózás az AutoML-ben; a teljes hibaüzenetek mindig a naplófájlba kerülnek.
    + Az AutoML frissítette a `azureml-defaults`csomag `azureml-explain-model`rögzítési céljából , hogy tartalmazza a , és `azureml-dataprep`. Az AutoML a továbbiakban nem figyelmeztet `azureml-train-automl` a csomageltérésekre (kivéve a csomagot).
    + Kijavítottuk `timeseries` azt a hibát, amely miatt a cv felosztások nem egyenlő méretűek, ami a raktárhely számításának sikertelenét okozta.
    + Amikor a Keresztérvényesítés képzési típushoz futó együttes iterációt futtatunk, ha végül nem tudtuk letölteni a teljes adatkészletre betanított modelleket, ellentmondás volt a modell súlyaés a szavazóegyüttesbe táplált modellek között.
    + Javítva a hiba, akkor merült fel, amikor a képzési és / vagy érvényesítési címkék (y és y_valid) formájában pandák dataframe, de nem numpy tömb.
    + Kijavítottuk az előrejelzési feladatokkal kapcsolatos problémát, amikor a bemeneti táblák logikai oszlopaiban a Nincs érték volt.
    + Lehetővé teszi az AutoML-felhasználók számára, hogy eldobják az előrejelzéshez nem elég hosszú betanítási sorozatot. - Lehetővé teszi az AutoML-felhasználók számára, hogy olyan szemcséket dobjanak ki a tesztkészletből, amelyek előrejelzéskor nem szerepelnek a betanítási készletben.
  + **azureml-core**
    + Kijavítottuk a blob_cache_timeout paraméterrendeléssel kapcsolatos problémát.
    + Külső illesztési és átalakítási kivételtípusokat adott hozzá a rendszerhibákhoz.
    + A key vault-i titkos kulcsok támogatása hozzáadva a távoli futtatásokhoz. Adjon hozzá egy azureml.core.keyvault.Keyvault osztályt a munkaterülethez társított keyvault-ból származó titkok hozzáadásához, leéséhez és listázásához. A támogatott műveletek a következők:
      + azureml.core.workspace.workspace.get_default_keyvault()
      + azureml.core.keyvault.keyvault.set_secret(név, érték)
      + azureml.core.keyvault.keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.keyvault.get_secret(név)
      + azureml.core.keyvault.keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.keyvault.list_secrets()
    + További módszerek az alapértelmezett keyvault és titkos kulcsok beszerzéséhez a távoli futtatás során:
      + azureml.core.workspace.workspace.get_default_keyvault()
      + azureml.core.run.run.get_secret(név)
      + azureml.core.run.run.get_secrets(secrets_list)
    + További felülbírálási paraméterek et adott hozzá a submit-hyperdrive CLI parancshoz.
    + Javítsa az API-hívások megbízhatóságát az újrapróbálkozások kiterjesztése a gyakori kérelmek könyvtárkivételekre.
    + A beküldött futtatásból történő futtatások elküldése támogatásának hozzáadása.
    + Javítva a Fájlfigyelő SAS tokenjének lejáró problémája, amely miatt a fájlok feltöltése a kezdeti token lejárta után leáll.
    + Http csv/tsv fájlok importálása támogatott a python SDK adatkészletben.
    + A Workspace.setup() metódus elavult. A felhasználók számára megjelenített figyelmeztető üzenet a create() vagy a get()/from_config() használatát javasolja.
    + Hozzáadott Environment.add_private_pip_wheel(), amely lehetővé teszi `whl`a privát egyéni python-csomagok feltöltését a munkaterületre, és biztonságosan használja őket a környezet felépítéséhez/megvalósulására.
    + Most már frissítheti a TLS/SSL tanúsítványt az AKS-fürtön telepített pontozási végponthoz mind a Microsoft által létrehozott, mind az ügyfél tanúsítványához.
  + **azureml-explain-modell**
    + Hozzáadott paraméter, hogy adjunk egy modell azonosítót magyarázatok feltöltéskor.
    + Hozzáadott `is_raw` címkézés magyarázatok a memóriában, és feltölteni.
    + Hozzáadott pytorch támogatás és tesztek azureml-explain-modell csomag.
  + **azureml-opendatasets**
    + Támogatja az automatikus tesztkörnyezet észlelését és naplózását.
    + Hozzáadott osztályok, hogy az amerikai lakosság megyei és zip.
  + **azureml-csővezeték-mag**
    + Címketulajdonság hozzáadva a bemeneti és kimeneti portdefiníciókhoz.
  + **azureml-telemetry**
    + Helytelen telemetriai konfigurációt javítottunk.
  + **azureml-train-automl**
    + Javítva a hiba, ahol a beállítási hiba, hiba nem volt bejelentkezve a "hibák" mezőbe a telepítő fut, és így nem volt tárolva a szülő fut "hiba".
    + Kijavítottuk az AutoML azon problémája miatt, amely miatt a hiányzó címkékkel rendelkező sorok nem lettenek megfelelően eltávolítva.
    + Lehetővé teszi az AutoML-felhasználók számára, hogy eldobják az előrejelzéshez nem elég hosszú betanítási sorozatot.
    + Engedélyezze az AutoML-felhasználóknak, hogy olyan szemcséket dobjanak ki a tesztkészletből, amelyek előrejelzéskor nem szerepelnek a betanítási készletben.
    + Most AutoMLStep áthalad `automl` config a háttér- elkerülése érdekében bármilyen probléma a változások vagy kiegészítések az új konfigurációs paramétereket.
    + Az AutoML Data Guardrail mostantól nyilvános előzetes verzióban érhető el. A felhasználó a betanítás után megjelenik egy Adatvédő-jelentés (besorolási/regressziós feladatokesetén), és az SDK API-n keresztül is hozzáférhet.
  + **azureml-train-core**
    + Hozzáadott fáklya 1.2 támogatás PyTorch Estimator.
  + **azureml-widgets**
    + Továbbfejlesztett zűrzavarmátrix-diagramok a besorolási képzéshez.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **Új funkciók**
  + A karakterláncok listái most már `read_*` átadhatók a metódusok bemeneteként.

+ **Hibajavítások és fejlesztések**
  + A Sparkban való futtatás során jelentősen javult a teljesítménye. `read_parquet`
  + Kijavítottunk `column_type_builder` egy problémát, amely miatt nem sikerült egyetlen, nem egyértelmű dátumformátumú oszlop.

### <a name="azure-portal"></a>Azure Portal
+ **Előnézeti funkció**
  + A napló- és kimeneti fájlok streamelése mostantól elérhető a részletes oldalak futtatásához. A fájlok valós időben streamelik a frissítéseket, amikor az előnézeti kapcsoló be van kapcsolva.
  + A kvóta munkaterületi szinten történő beállításának lehetősége előzetes verzióban érhető el. Az AmlCompute kvóták az előfizetés szintjén vannak lefoglalva, de most már lehetővé tesszük, hogy ezt a kvótát elossza a munkaterületek között, és méltányos megosztásés irányítás érdekében ossza ki. Csak kattintson a **Használat + Kvóták** panelre a munkaterület bal oldali navigációs sávján, és válassza a **Kvóták konfigurálása** lapot. Ne feledje, hogy előfizetési rendszergazdának kell lennie ahhoz, hogy kvótákat állíthat be a munkaterület szintjén, mivel ez egy munkaterület-közi művelet.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK python-hoz 1.0.55-ös v1.0.55

+ **Új funkciók**
  + A token alapú hitelesítés mostantól támogatott az AKS-en telepített pontozási végpontra irányuló hívások hoz. Továbbra is támogatni fogjuk a jelenlegi kulcsalapú hitelesítést, és a felhasználók egyszerre használhatják a hitelesítési mechanizmusok egyikét.
  + A virtuális hálózat (VNet) mögött álló blobtároló regisztrálása adattárként.

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Kijavít egy hibát, amely szerint az önéletrajzfelosztások érvényesítési mérete kicsi, és rossz előre jelzett és igaz diagramokat eredményez a regresszióhoz és az előrejelzéshez.
    + A távoli futtatások előrejelzési feladatainak naplózása javult, mostantól a felhasználó átfogó hibaüzenetet kap, ha a futtatás sikertelen volt.
    + Javítottuk, `Timeseries` hogy az előfolyamat jelzője igaz-e.
    + Néhány előrejelzési adatérvényesítési hibaüzenetet még végrehajthatóbbá tett.
    + Az AutoML csökkentett memóriafelhasználása az adatkészletek eldobásával és/vagy lusta betöltésével fut, különösen a folyamatívek között
  + **azureml-contrib-explain-modell**
    + Hozzáadott model_task jelző a magyarázókhoz, hogy a felhasználó felülbírálhassa a modelltípus alapértelmezett automatikus következtetési logikáját
    + Widget változások: Automatikusan `contrib`telepíti `nbextension` a , nincs több telepítés / engedélyezése - támogatás magyarázata csak a globális funkció fontosságát (pl. Permutative)
    + Műszerfal változások: - Box telkek és `beeswarm` hegedű telkek mellett telek összefoglaló `beeswarm` oldalon - Sokkal gyorsabb újrarenderelése telek a "Top-k" csúszka változás - hasznos üzenet elmagyarázza, hogyan top-k számítják - Hasznos testreszabható üzenetek helyett diagramok, ha az adatok nem biztosított
  + **azureml-core**
    + Model.package() metódus hozzáadása Docker-rendszerképek és Docker-fájlok létrehozásához, amelyek magukba foglalják a modelleket és azok függőségeit.
    + Frissítve a helyi webszolgáltatások a Környezeti objektumokat tartalmazó inferenceConfigs fogadásához.
    + Rögzített Model.register() termelő érvénytelen modellek, ha "." (az aktuális könyvtárhoz) model_path paraméterként kerül átadásra.
    + Add Run.submit_child, a funkció tükrözi Experiment.submit, miközben megadja a futtatás, mint a szülő a beküldött gyermek fut.
    + A Model.register segédprogram konfigurációs beállításainak támogatása a Run.register_model területen.
    + Jar-feladatok futtatásának lehetősége meglévő fürtön.
    + Most támogatja instance_pool_id és cluster_log_dbfs_path paramétereket.
    + A modell webszolgáltatásra történő üzembe helyezésekor támogatja a környezeti objektumok használatát. A Környezeti objektum most már megadható az InferenceConfig objektum részeként.
    + Appinsifht leképezés hozzáadása új régiókhoz - centralus - westus - northcentralus
    + Az adattár összes osztályának összes attribútumának dokumentációja hozzáadva.
    + Hozzáadva blob_cache_timeout `Datastore.register_azure_blob_container`paraméter .
    + save_to_directory és load_from_directory módszerek hozzáadása az azureml.core.environment.Environment hez.
    + Az "az ml környezet letöltése" és az "az ml környezetregiszter" parancsok at hozzáadva a CLI-hez.
    + Hozzáadva Environment.add_private_pip_wheel metódus.
  + **azureml-explain-modell**
    + Adatkészlet-követés hozzáadása a magyarázatokhoz az Adatkészlet szolgáltatás használatával (előzetes verzió).
    + Az alapértelmezett kötegméret 10 ezerről 100-ra csökkent.
    + Hozzáadott model_task jelzőt a magyarázókhoz, hogy a felhasználó felülbírálhassa a modelltípus alapértelmezett automatikus következtetési logikáját.
  + **azureml-ml flow**
    + Javítottuk a mlflow.azureml.build_image hibát, ahol a beágyazott könyvtárakat a rendszer figyelmen kívül hagyja.
  + **azureml-folyamatlépések**
    + A JAR-feladatok meglévő Azure Databricks-fürtön való futtatásának lehetősége.
    + A DatabricksStep lépés támogatási instance_pool_id és cluster_log_dbfs_path paramétereit adta hozzá.
    + A DatabricksStep lépésben támogatja a folyamatparamétereket.
  + **azureml-train-automl**
    + Hozzáadva `docstrings` az együttessel kapcsolatos fájlokhoz.
    + Frissített dokumentumok a megfelelőbb `max_cores_per_iteration` nyelvre a`max_concurrent_iterations`
    + A távoli futtatások előrejelzési feladatainak naplózása javult, mostantól a felhasználó átfogó hibaüzenetet kap, ha a futtatás sikertelen volt.
    + Eltávolította `automlstep` get_data a folyamatjegyzetfüzetből.
    + A `dataprep` támogatás `automlstep`indítása a ban

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Új funkciók**
  + Most kérheti, hogy bizonyos ellenőröket (pl. hisztogram, szórásos telek stb.) hajtson végre bizonyos oszlopokon.
  + Párhuzamosítási argumentum `append_columns`hozzáadva a hoz. Ha Igaz, az adatok betöltődnek a memóriába, de a végrehajtás párhuzamosan fog futni; ha Hamis, a végrehajtás streamelésre kerül, de egyszálas lesz.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK python-hoz 1.0.53-as v

+ **Új funkciók**
  + Az Automated Machine Learning mostantól támogatja az ONNX-modellek betanítását a távoli számítási célon
  + Az Azure Machine Learning most már lehetővé teszi, hogy folytassa a betanítást egy korábbi futtatási, ellenőrzőpont- vagy modellfájlokból.
    + További információ [arról, hogyan használhatja a becslőket az edzés folytatásához egy korábbi futtatásból](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Hibajavítások és fejlesztések**
  + **automl-client-core-nativeclient**
    + Javítsa ki az oszlopok típusainak elvesztésével kapcsolatos hibát az átalakítás után (hibacsatolva);
    + Engedélyezze, hogy y_query a Kezdő (#459519) egyik (Nincs) objektumtípust is tartalmazzon.
  + **azúr-cli-ml**
    + A CLI parancsok "modelltelepítés" és "szolgáltatásfrissítés" mostantól paramétereket, konfigurációs fájlokat vagy a kettő kombinációját fogadnak el. A paraméterek elsőbbséget élveznek a fájlok attribútumaival szemben.
    + A modell leírása most már frissíthető a regisztráció után
  + **azureml-automl-core**
    + Frissítse a NimbusML-függőséget az 1.2.0-s verzióra (aktuális verzió).
    + A NimbusML-becslések támogatásának hozzáadása & AutoML-becslésekben használandó folyamatokhoz.
    + Egy hiba kijavítása az Ensemble kiválasztási eljárásban, amely szükségtelenül növésben volt a kapott együttesben, még akkor is, ha a pontszámok állandóak maradtak.
    + Engedélyezze néhány jellemző további felhasználását az önéletrajzfelosztások között az előrejelzési feladatokhoz. Ez felgyorsítja a futási idő a beállítás által működtetett nagyjából egy tényező a n_cross_validations a drága featurizations, mint a késések és gördülő ablakok.
    + Kifejt egy kérdés ha idő van kívül pandák támogatott idő sor. Most egy DataException-et emelünk fel, ha az idő kisebb, mint a pd. Időbélyeg.min vagy nagyobb, mint a pd. Időbélyeg.max
    + Az előrejelzés mostantól lehetővé teszi a különböző frekvenciákat a vonat- és tesztsorozatokban, ha azok összehangolhatók. Például a "januárban kezdődő negyedéves" és az októberben kezdődő "negyedéves" összehangolható.
    + A "paraméterek" tulajdonság hozzá lett adva a TimeSeriesTransformerhez.
    + Távolítsa el a régi kivételosztályokat.
    + Az előrejelzési feladatokban a `target_lags` paraméter mostantól egyetlen egész értéket vagy egész számok listáját fogad el. Ha az egész szám lett megadva, csak egy késés jön létre. Ha van megadva egy lista, a rendszer a késések egyedi értékeit veszi figyelembe. target_lags=[1, 2, 2, 4] egy, 2 és 4 pont lemaradást eredményez.
    + Javítsa ki az oszlopok típusainak az átalakítás utáni elvesztésével kapcsolatos hibát (hibacsatolva);
    + A `model.forecast(X, y_query)`alkalmazásban engedélyezze, hogy y_query olyan objektumtípus legyen, amely a kezdő (#459519) elején nincs(ek)et tartalmaz.
    + Várt értékek `automl` hozzáadása a kimenethez
  + **azureml-contrib-datadrift**
    +  A példajegyzetfüzet fejlesztései, beleértve az azureml-contrib-opendatasets helyett az azureml-contrib-opendatasets helyett az azureml-opendatasets-re való váltást és az adatok bővítésének teljesítménybeli javítását
  + **azureml-contrib-explain-modell**
    + Rögzített átalakítások argumentum a LIME magyarázóhoz az azureml-contrib-explain-model csomag nyers funkciófontosságához
    + Az AzureML-contrib-explain-model csomag képmagyarázójában szegmensek hozzáadása a képmagyarázathoz
    + Scipy sparse támogatás hozzáadása a LimeExplainer-hez
    + Hozzáadva `batch_size` a mimikai magyarázóhoz, amikor `include_local=False`a globális magyarázatok streameléséhez kötegekben, hogy javítsa a DecisionTreeExplainableModel végrehajtási idejét
  + **azureml-contrib-featureengineering**
    + Javítás a hívó set_featurizer_timeseries_params(): diktált érték változása `timeseries` és null ellenőrzés - Add notebook featurizer
    + Frissítse a NimbusML-függőséget az 1.2.0-s verzióra (aktuális verzió).
  + **azureml-core**
    + Hozzáadta a DBFS-adattárak csatlakoztatásának lehetőségét az AzureML CLI-ben
    + Javítva a hiba adattár feltöltés, `target_path` ahol egy üres mappát hoz létre, ha indult`/`
    + Kijavítottuk `deepcopy` a ServicePrincipalAuthentication problémát.
    + Az "az ml environment show" és az "az ml environment list" parancsok at hozzáadva a CLI-hez.
    + A környezetek mostantól támogatják a base_dockerfile megadását a már meglévő base_image alternatívájaként.
    + A nem használt RunConfiguration beállítás auto_prepare_environment elavultként van megjelölve.
    + A modell leírása most már frissíthető a regisztráció után
    + Hibajavítás: A modell és a lemezkép törlése mostantól további információt nyújt a tőlük függő upstream objektumok beolvasásáról, ha a törlés sikertelen egy felsőbb sugárúti függőség miatt.
    + Javítottuk azt a hibát, amely üres időtartamot nyomtatott az egyes környezetekben lévő munkaterület ek létrehozásakor bekövetkező központi telepítéseknél.
    + A továbbfejlesztett munkaterület hibakivételeket hoz létre. Olyan például, hogy a felhasználók nem látják a "Nem lehet létrehozni munkaterületet. Nem található..." mint az üzenetet, és ehelyett a tényleges létrehozási hibát látja.
    + Az AKS-webszolgáltatások ban támogatja a tokenhitelesítést.
    + Metódus `get_token()` hozzáadása `Webservice` az objektumokhoz.
    + Cli-támogatás hozzáadása a gépi tanulási adatkészletek kezeléséhez.
    + `Datastore.register_azure_blob_container`most opcionálisan `blob_cache_timeout` vesz egy értéket (másodpercben), amely konfigurálja a blobfuse csatlakoztatási paramétereit, hogy engedélyezze a gyorsítótár lejáratát az adattárban. Az alapértelmezett beállítás nem időtúllépés, azaz ha egy blob olvasása, akkor marad a helyi gyorsítótárban, amíg a feladat befejeződött. A legtöbb feladat inkább ezt a beállítást, de egyes feladatok kell olvasni több adatot egy nagy adatkészlet, mint elfér a csomópontokon. Ezeknél a feladatokban a paraméter finomhangolása segít nekik a sikerben. Óvatosan hangolásakor ezt a paramétert: az érték túl alacsony beállítása gyenge teljesítményt eredményezhet, mivel a korszakban használt adatok lejárhatnak, mielőtt újra használnák. Ez azt jelenti, hogy az összes olvasás a blob storage (azaz a hálózat) helyett a helyi gyorsítótárból történik, ami negatívan befolyásolja a betanítási időket.
    + A modell leírása most már megfelelően frissíthető a regisztráció után
    + A modell- és képtörlés mostantól további információt nyújt a tőlük függő felsőbb objektumokról, amelyek a törlés sikertelensülését okozzák
    + Az azureml.mlflow használatával javíthatja a távoli futtatások erőforrás-kihasználtságát.
  + **azureml-explain-modell**
    + Rögzített átalakítások argumentum a LIME magyarázóhoz az azureml-contrib-explain-model csomag nyers funkciófontosságához
    + add scipy ritka támogatása LimeExplainer
    + hozzáadott forma lineáris magyarázó wrapper, valamint egy másik szinten táblázatos magyarázó magyarázata lineáris modellek
    + a mimikai magyarázó a magyarázó modelltárban, rögzített hiba, ha include_local=Hamis a ritka adatbevitelhez
    + várt értékek `automl` hozzáadása a kimenethez
    + rögzített permutációs funkció fontossága, amikor az átalakítások argumentuma a nyers funkció fontosságának lehívására
    + hozzá, `batch_size` hogy utánozza magyarázó, ha `include_local=False`, a streaming globális magyarázatok tételekben, hogy javítsa végrehajtási idő DecisionTreeExplainableModel
    + a modell explainability könyvtár, rögzített blackbox magyarázók, ahol pandák dataframe bemenet szükséges előrejelzés
    + Javítva egy `explanation.expected_values` hiba, amely néha egy úszót adott vissza, nem pedig egy olyan listát, amelyben úszó van.
  + **azureml-ml flow**
    + A mlflow teljesítményének javítása.set_experiment(experiment_name)
    + Az InteractiveLoginAuthentication használatával az mlflow tracking_uri használt hiba kijavítása
    + Az azureml.mlflow használatával javíthatja a távoli futtatások erőforrás-kihasználtságát.
    + Az azureml-ml flow csomag dokumentációjának javítása
    + Patch hiba, ahol mlflow.log_artifacts("my_dir") mentené leletek alatt "my_dir / <műtárgy-utak>" helyett "<műtárgyak-útvonalak>"
  + **azureml-opendatasets**
    + Pin `pyarrow` `opendatasets` a régi verziók (<0.14.0), mert a memória probléma újonnan bevezetett ott.
    + Az azureml-contrib-opendatasets áthelyezése azureml-opendatasets-be.
    + Lehetővé teszi a nyílt adatkészlet-osztályok regisztrálását az Azure Machine Learning-munkaterületre, és zökkenőmentesen használhatja ki az AML-adatkészlet-képességeket.
    + A NoaaIsdWeather javítása jelentősen gazdagítja a teljesítményt a nem SPARK verzióban.
  + **azureml-folyamatlépések**
    + A DBFS Datastore mostantól támogatott a DatabricksStep bemenetei és kimenetei számára.
    + Az Azure Batch Step dokumentációja a bemenetek/kimenetek tekintetében.
    + Az AzureBatchStep alkalmazásban *delete_batch_job_after_finish* alapértelmezett értéket *true*értékre módosította.
  + **azureml-telemetry**
    +  Az azureml-contrib-opendatasets áthelyezése azureml-opendatasets-be.
    + Lehetővé teszi a nyílt adatkészlet-osztályok regisztrálását az Azure Machine Learning-munkaterületre, és zökkenőmentesen használhatja ki az AML-adatkészlet-képességeket.
    + A NoaaIsdWeather javítása jelentősen gazdagítja a teljesítményt a nem SPARK verzióban.
  + **azureml-train-automl**
    + A get_output frissített dokumentációja, hogy tükrözze a tényleges visszatérési típust, és további megjegyzéseket nyújtson a kulcstulajdonságok beolvasásához.
    + Frissítse a NimbusML-függőséget az 1.2.0-s verzióra (aktuális verzió).
    + várt értékek `automl` hozzáadása a kimenethez
  + **azureml-train-core**
    + A karakterláncok mostantól az automatikus hiperparaméter-hangolás számítási célként vannak elfogadva
    + A nem használt RunConfiguration beállítás auto_prepare_environment elavultként van megjelölve.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK 1.1.9-es v1.1.9

+ **Új funkciók**
  + Hozzáadott támogatása olvasó egy fájlt közvetlenül a http vagy https url.Added support for reading a file directly from a http or https url.

+ **Hibajavítások és fejlesztések**
  + Továbbfejlesztett hibaüzenet, amikor a Parketta adatkészletet távoli forrásból próbálja olvasni (amely jelenleg nem támogatott).
  + Javítva egy hiba, amikor az ADLS Gen 2-ben parquet fájlformátumba írtunk, és frissítette az ADLS Gen 2 tároló nevét az elérési úton.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Vizuális felület
+ **Előzetes verzió funkciói**
  + Hozzáadott "Végrehajtási R script" modul vizuális felületen.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK python-hoz 1.0.48-as v1.0.48

+ **Új funkciók**
  + **azureml-opendatasets**
    + **Az azureml-contrib-opendatasets** már elérhető **azureml-opendatasets néven.** A régi csomag továbbra is működhet, de azt javasoljuk, hogy **azureml-opendatasets** halad előre a gazdagabb képességek és fejlesztések használata.
    + Ez az új csomag lehetővé teszi a nyitott adatkészletként történő regisztrálást az Azure Machine Learning-munkaterületen, és kihasználhatja az Adatkészlet által kínált funkciókat.
    + Ez is tartalmaz a meglévő képességek, például a pandák/SPARK-adatkeretek nyílt adatkészletek fogyasztása, és a helyillesztés egyes adatkészletek, például az időjárás.

+ **Előzetes verzió funkciói**
    + A HyperDriveConfig most már elfogadhatja a folyamatobjektumot paraméterként a folyamat használatával történő hiperparaméter-hangolás támogatásához.

+ **Hibajavítások és fejlesztések**
  + **azureml-train-automl**
    + Javítottuk az oszlopok típusainak elvesztésével kapcsolatos hibát az átalakítás után.
    + Javítva a hiba, hogy y_query, hogy egy objektum típus, amely nincs (ek) az elején.
    + Javítva a probléma az Ensemble kiválasztási eljárás, amely szükségtelenül növekszik a kapott együttes akkor is, ha a pontszámok állandóak maradtak.
    + Kijavítottuk az AutoMLStep whitelist_models és blacklist_models beállításaival kapcsolatos problémát.
    + Kijavítottuk azt a problémát, amely megakadályozta az előfeldolgozás használatát, amikor az AutoML-t az Azure ML-folyamatok környezetében használták volna.
  + **azureml-opendatasets**
    + Az azureml-contrib-opendatasets áthelyezése azureml-opendatasets-be.
    + Lehetővé tette, hogy a nyitott adatkészlet-osztályok regisztrálva legyenek az Azure Machine Learning-munkaterületre, és zökkenőmentesen használják ki az AML-adatkészlet-képességeket.
    + Továbbfejlesztett NoaaIsdWeather gazdagítja a teljesítményt nem SPARK verzió jelentősen.
  + **azureml-explain-modell**
    + Frissített online dokumentáció értelmezhetőségi objektumokhoz.
    + Hozzáadva `batch_size` a mimikai magyarázóhoz, amikor `include_local=False`a globális magyarázatok kötegekben való streameléséhez javítsa a DecisionTreeExplainableModel modellmagyarázó függvénytárvégrehajtási idejét.
    + Javítva a `explanation.expected_values` probléma, amely miatt néha egy úszót adott vissza, nem pedig egy olyan listát, amelyben úszó volt.
    + A magyarázómodelltárban a mimikai magyarázó `automl` kimenethez szükséges értékek et adta hozzá.
    + Rögzített permutációs funkció fontossága, amikor az átalakítások argumentumot a nyers funkció fontosságának lehetővé teszi.
  + **azureml-core**
    + Hozzáadott a DBFS-adattárak csatolása az AzureML CLI-ben.
    + Kijavítottuk az adattár feltöltésével kapcsolatos `target_path` problémát, ahol üres mappa jött létre, ha a programmal `/`indult.
    + Két adatkészlet összehasonlításának engedélyezése.
    + A Modell és a Kép törlése mostantól további információt nyújt a tőlük függő felsőbb sugárúti objektumok beolvasásáról, ha a törlés egy felsőbb sugárúti függőség miatt sikertelen.
    + A auto_prepare_environment elavulta kontinami(k) nem használt RunConfiguration-beállítást.
  + **azureml-ml flow**
    + Az azureml.mlflow-t használó távoli futtatások továbbfejlesztett erőforrás-kihasználtsága.
    + Javította az azureml-ml flow csomag dokumentációját.
    + Kijavítottuk azt a problémát, amely miatt a mlflow.log_artifacts("my_dir") a "műtárgyutak" helyett a "my_dir/műtermék-elérési utak" alá mentette a műtermékeket.
  + **azureml-csővezeték-mag**
    + Az összes folyamatlépés paraméteres hash_paths elavult, és a jövőben el lesz távolítva. Alapértelmezés szerint a source_directory tartalma kivonatolt (kivéve az .amlignore vagy .gitignore fájlban felsorolt fájlokat)
    + A Modul és a ModuleStep továbbfejlesztése a számítási típusspecifikus modulok támogatásához, a RunConfiguration integrációra való felkészüléshez és a folyamatok ban a számítási típusspecifikus modulhasználat feloldásához szükséges egyéb módosításokhoz.
  + **azureml-folyamatlépések**
    + AzureBatchStep: Továbbfejlesztett dokumentáció a bemenetek/kimenetek tekintetében.
    + AzureBatchStep: az alapértelmezett érték delete_batch_job_after_finish true értékre módosítható.
  + **azureml-train-core**
    + A karakterláncok mostantól az automatikus hiperparaméter-hangolás számítási célként vannak elfogadva.
    + A auto_prepare_environment elavulta kontinami(k) nem használt RunConfiguration-beállítást.
    + Elavult `conda_dependencies_file_path` paraméterek, `pip_requirements_file_path` valamint a mellett, `pip_requirements_file` `conda_dependencies_file` illetve.
  + **azureml-opendatasets**
    + A NoaaIsdWeather javítása jelentősen gazdagítja a teljesítményt a nem SPARK verzióban.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning Data Prep SDK 1.1.8-as v1.1.8

+ **Új funkciók**
 + Az adatfolyam-objektumok mostmár átvihetők, és rekordsorozatot hozhatnak létre. Lásd a `Dataflow.to_record_iterator`dokumentációt.
  + Az adatfolyam-objektumok mostmár átvihetők, és rekordsorozatot hozhatnak létre. Lásd a `Dataflow.to_record_iterator`dokumentációt.

+ **Hibajavítások és fejlesztések**
 + Megnövelte a DataPrep SDK robusztusságát.
 + Továbbfejlesztett kezelése pandák DataFrames nem string oszlop indexek.
 + Az adatkészletek `to_pandas_dataframe` teljesítményének javítása.
 + Kijavítottunk egy hibát, amely miatt az adatkészletek Spark-végrehajtása nem sikerült, amikor többcsomópontos környezetben futtatták.
  + Megnövelte a DataPrep SDK robusztusságát.
  + Továbbfejlesztett kezelése pandák DataFrames nem string oszlop indexek.
  + Az adatkészletek `to_pandas_dataframe` teljesítményének javítása.
  + Kijavítottunk egy hibát, amely miatt az adatkészletek Spark-végrehajtása nem sikerült, amikor többcsomópontos környezetben futtatták.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning Data Prep SDK 1.1.7-es v1.1.7

Visszaállítottuk a jobb teljesítményt eredményező változást, mivel az azure Databricks-t használó egyes ügyfelek számára problémákat okozott. Ha problémát tapasztalt az Azure Databricks-en, az alábbi módszerek egyikével frissíthet az 1.1.7-es verzióra:
1. Futtassa a parancsfájlt a frissítéshez:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Hozza létre újra a fürtöt, amely telepíti a legújabb Data Prep SDK-verziót.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK python-hoz 1.0.45-ös v1.0.45

+ **Új funkciók**
  + Döntési fa helyettesítő modelljének hozzáadása az azureml-explain-model csomag magyarázatának utánzására
  + A lemezképekre telepítendő CUDA-verzió megadása. A CUDA 9.0, 9.1 és 10.0 támogatása.
  + Az Azure ML betanítási alaprendszerképeivel kapcsolatos információk már elérhetők az [Azure ML-tárolók GitHub-tárházán](https://github.com/Azure/AzureML-Containers) és [a DockerHub-on](https://hub.docker.com/_/microsoft-azureml)
  + Cli-támogatás hozzáadva a csővezeték-ütemezéshez. Futtassa az "az ml pipeline -h" futtatást, hogy többet tudjon meg
  + Egyéni Kubernetes névtér paraméter t adott hozzá az AKS webszolgáltatás telepítési konfigurációjához és a CLI-hez.
  + Az összes folyamatlépés elavult hash_paths paramétere
  + Model.register most már támogatja a regisztráció több egyedi fájlokat, mint egy modell a `child_paths` paraméter használatával.

+ **Előzetes verzió funkciói**
    + Pontozási magyarázók most már opcionálisan menteni conda és pip információkat megbízhatóbb szerializálás és deszerializálás.
    + Hibajavítás az automatikus funkcióválasztóhoz.
    + Frissített mlflow.azureml.build_image az új api, folt hibák kitéve az új végrehajtás.

+ **Hibajavítások és fejlesztések**
  + Függőség `paramiko` eltávolítása az azureml-core-ból. Hozzáadott eprekation figyelmeztetések örökölt számítási cél csatolási módszerek.
  + A futtatás teljesítményének javítása.create_children
  + A bináris osztályozóval rendelkező mimikai magyarázóban rögzítse a valószínűségek sorrendjét, amikor a tanár imátlevan használva az alakzatértékek méretezéséhez.
  + Továbbfejlesztett hibakezelés és üzenet az automatikus gépi tanuláshoz.
  + Javítottuk az automatikus gépi tanulás iterációs időtúltöltési problémája.
  + Javította az automatizált gépi tanulás idősorozat-átalakítási teljesítményét.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning Data Prep SDK 1.1.6-os v1.1.6

+ **Új funkciók**
  + A legfelső értékek (`SummaryFunction.TOPVALUES`) és`SummaryFunction.BOTTOMVALUES`az alsó értékek ( ) összegző függvényeinek hozzáadása

+ **Hibajavítások és fejlesztések**
  + Jelentősen javította a `read_pandas_dataframe`teljesítményét.
  + Javítva egy hiba, amely a `get_profile()` bináris fájlokra mutató adatfolyamon sikertelen.
  + Elérhetőak, `set_diagnostics_collection()` hogy lehetővé tegye a telemetriai gyűjtemény programozott engedélyezése/letiltása.
  + Megváltoztatta a `get_profile()`viselkedését. NaN értékek most figyelmen kívül hagyja a Min, Mean, Std, és sum, amely igazodik a viselkedését Pandák.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK python-hoz 1.0.43-as v1.0.43

+ **Új funkciók**
  + Az Azure Machine Learning mostantól első osztályú támogatást nyújt a népszerű gépi tanulási és adatelemzési keretrendszerscikit-learn számára. A [ `SKLearn` becslés](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)használatával a felhasználók könnyedén betaníthatják és telepíthetik a Scikit-learn modelleket.
    + Ismerje meg, hogyan [futtatható a hyperparameter tuning a Scikit-learn segítségével HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Az újrafelhasználható számítási egységek kezeléséhez a ModuleStep létrehozása a folyamatokban a modul- és a ModuleVersion-osztályokkal együtt.
  + Az ACI webservices mostantól támogatja az állandó scoring_uri a frissítéseken keresztül. A scoring_uri IP-címről teljes tartománytartomány-n-re változik. Az FQDN DNS-névcímkéje a dns_name_label deploy_configuration beállításával konfigurálható.
  + Automatizált gépi tanulás új funkciók:
    + STL featurizer előrejelzéshez
    + KMeans fürtözés engedélyezve van a szolgáltatás elsöprő
  + AmlCompute Kvóta jóváhagyások csak lett gyorsabb! Most már automatizált a folyamat, hogy jóváhagyja a kvótakérelmek egy küszöbértéken belül. A kvóták működéséről a kvóták kezeléséről szóló további információkért olvassa el a [kvóták kezelését.](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)

+ **Előzetes verzió funkciói**
    + Integráció [az MLflow](https://mlflow.org) 1.0.0-val az azureml-ml flow csomagon keresztül ([példa notebookok](https://aka.ms/azureml-mlflow-examples)).
    + Küldje jupyter notebook futtatásként. [API referenciadokumentációja](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + [Adatsodródás-érzékelő](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) nyilvános előzetes verziója az azureml-contrib-datadrift csomagon keresztül ([példa jegyzetfüzetek](https://aka.ms/azureml-datadrift-example)). Az adateltolódás az egyik legfontosabb oka annak, hogy a modell pontossága az idő múlásával romlik. Ez akkor fordul elő, ha az éles környezetben a modellnek kiszolgált adatok eltérnek a modell betanított adatoktól. Az AML-adateltolódás-érzékelő segít az ügyfélnek az adateltolódás figyelésében, és riasztást küld, ha eltolódást észlel.

+ **Változások megbontása**

+ **Hibajavítások és fejlesztések**
  + RunConfiguration terhelés és mentés támogatja a teljes fájl elérési útját teljes back-compat a korábbi viselkedés.
  + Gyorsítótárazás hozzáadva a ServicePrincipalAuthentication szolgáltatásban, alapértelmezés szerint ki van kapcsolva.
  + Több telek naplózásának engedélyezése ugyanazon metrikanév alatt.
  + A modellosztály mostantól megfelelően importálható az azureml.core (`from azureml.core import Model`fájlból).
  + A folyamat `hash_path` lépésekben a paraméter elavult. Az új viselkedés az ,amlignore vagy .gitignore fájlban felsorolt fájlok kivételével a source_directory kitöltésének.
  + A csővezeték-csomagokban `get_all` `get_all_*` a különböző és módszerek elavultak a `list` javára, illetve `list_*`a.
  + az azureml.core.get_run már nem szükséges osztályokimportálást igényel az eredeti futtatási típus visszaadása előtt.
  + Kijavítottuk azt a problémát, amely miatt a WebService Update egyes hívásai nem indítottak frissítést.
  + Az AKS-webszolgáltatások pontozási időhaszolgáltatásának 5 ms és 300000ms között kell lennie. Max engedélyezett scoring_timeout_ms pontozási kérelmek már ütközött 1 perc 5 perc.
  + A LocalWebservice `scoring_uri` objektumok `swagger_uri` mostmár rendelkeznek tulajdonságokkal.
  + Áthelyezték kimenetek könyvtár létrehozása és kimenetek könyvtár feltöltése ki a felhasználói folyamat. Engedélyezve van az SDK futtatásának engedélyezése minden felhasználói folyamatban. Ez megoldja az elosztott képzési futtatások által tapasztalt szinkronizálási problémákat.
  + A felhasználófolyamat nevéből írt azureml napló neve mostantól tartalmazza a folyamat nevét (csak elosztott betanításesetén) és a PID-t.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning Data Prep SDK 1.1.5-ös v1.1.5

+ **Hibajavítások és fejlesztések**
  + A kétjegyű évszámmal rendelkező értelmezett datetime értékek esetében az érvényes évek tartománya frissült a Windows májusi kiadásának megfelelően. A tartomány 1930-2029-ről 1950-2049-re változott.
  + Fájlban és beállításkor `handleQuotedLineBreaks=True` `\r` a program új sorként kezeli a fájlt.
  + Javítva egy `read_pandas_dataframe` hiba, amely bizonyos esetekben sikertelen volt.
  + A teljesítmény `get_profile`ének javítása.
  + Továbbfejlesztett hibaüzenetek.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning Data Prep SDK 1.1.4-es v1.1.4

+ **Új funkciók**
  + Most már használhatja a következő kifejezés nyelvi függvények kibontása és elemzésdatetime értékek et új oszlopokba.
    + `RegEx.extract_record()`a datetime elemeket egy új oszlopba bontja ki.
    + `create_datetime()`datetime objektumokat hoz létre különböző datetime elemekből.
  + Híváskor `get_profile()`most már láthatja, hogy a kvantilis oszlopok (est.) feliratúak, hogy egyértelműen jelezze, hogy az értékek közelítések.
  + Most már használhatja ** globbing olvasásakor az Azure Blob Storage.
    + Pl.`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Hibajavítások**
  + Kijavítottuk a parettatfájl távoli forrásból (Azure Blob) történő olvasásával kapcsolatos hibát.

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK python-hoz 1.0.39-es v1.0.39
+ **Változások**
  + A konfiguráció futtatása auto_prepare_environment beállítás elavulttá válik, és az automatikus előkészítés lesz az alapértelmezett.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning Data Prep SDK 1.1.3-as v1.1.3

+ **Új funkciók**
  + Hozzáadott támogatás olvasni egy PostgresSQL adatbázis, akár hívja read_postgresql, vagy egy Datastore.
    + Lásd a példákat az útmutatókban:
      + [Adatbetöltési jegyzetfüzet](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Adattár-jegyzetfüzet](https://aka.ms/aml-data-prep-datastore-nb)

+ **Hibajavítások és fejlesztések**
  + Kijavítottuk az oszloptípus-átalakítással kapcsolatos problémákat:
  + Most már helyesen konvertálja a logikai vagy numerikus oszlopot logikai oszloptá.
  + Most nem sikertelen, ha egy dátumoszlopot dátumtípusra próbál beállítani.
  + Továbbfejlesztett JoinType típusok és kísérő referenciadokumentáció. Két adatfolyam egyesítésekén most az alábbi típusú illesztések egyikét adhatja meg:
    + NINCS, MECCS, BELSŐ, BALBAL, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULLANTI, FULL.
  + Továbbfejlesztett adattípus-bevitel több dátumformátum felismeréséhez.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

Az Azure Portalon most már:
+ Automatikus pénzmosási kísérletek létrehozása és futtatása
+ Hozzon létre egy notebook virtuális gép, hogy próbálja ki a minta Jupyter notebookok vagy a saját.
+ Vadonatúj szerzői szakasz (előzetes verzió) az Azure Machine Learning-munkaterületen, amely magában foglalja az automatizált gépi tanulást, a vizuális felületet és a tárolt jegyzetfüzet virtuális gépeit
    + Modell automatikus létrehozása automatikus gépi tanulás sal
    + Kísérletek futtatása húzással vizuális felülettel
    + Hozzon létre egy notebook virtuális gépet az adatok feltárásához, modellek létrehozásához és szolgáltatások üzembe helyezéséhez.
+ Élő diagram és mérőszám frissítése a futtatási jelentésekben és a részletes oldalak futtatása
+ Frissített fájlmegjelenítő a naplókhoz, kimenetekhez és pillanatképekhez a Futtatás részleteit megjelenítő lapokban.
+ Új és továbbfejlesztett jelentéskészítési élmény a Kísérletek lapon.
+ A config.json fájl letöltésének lehetősége az Azure Machine Learning munkaterület áttekintése lapjáról.
+ Az Azure Machine Learning-munkaterület létrehozását az Azure Databricks-munkaterületről támogathato.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK python-hoz 1.0.33-as v1.0.33
+ **Új funkciók**
  + A _Workspace.create_ metódus mostantól elfogadja a CPU- és GPU-fürtök alapértelmezett fürtkonfigurációit.
  + Ha a munkaterület létrehozása sikertelen, a rendszer törli a függő erőforrásokat.
  + Az alapértelmezett Azure Container Registry Termékváltozat alapszintűre lett átkapcsolva.
  + Az Azure Container Registry lustán jön létre, ha futtatáshoz vagy lemezkép létrehozásához szükséges.
  + Környezetek támogatása a képzési futtatások.

### <a name="notebook-virtual-machine"></a>Jegyzetfüzet virtuális gépe 

A Jupyter-jegyzetfüzetek biztonságos, nagyvállalati használatra kész üzemeltetési környezeteként használjon jegyzetfüzet-virtuális gépet, amelyben gépi tanulási kísérleteket programozhat, modelleket helyezhet üzembe webes végpontként, és végrehajthatja az Azure Machine Learning SDK által a Python használatával támogatott összes többi műveletet.Számos lehetőséget kínál:
+ [Gyorsan felpörgethet egy előre konfigurált notebook virtuális gépet,](tutorial-1st-experiment-sdk-setup.md) amely az Azure Machine Learning SDK legújabb verzióját és a kapcsolódó csomagokat használja.
+ Az access bizonyított technológiákon keresztül védett, például HTTPS, Azure Active Directory-hitelesítés és engedélyezés.
+ A jegyzetfüzetek és a kódok megbízható felhőalapú tárolása az Azure Machine Learning Workspace blobstorage-fiókjában.Biztonságosan törölheti a notebook virtuális gép anélkül, hogy elveszítené a munkáját.
+ Előre telepített mintajegyzetfüzetek az Azure Machine Learning-funkciók felfedezéséhez és kísérletezéséhez.
+ Az Azure virtuális gépek teljes testreszabási lehetőségei, bármilyen virtuális gép típus, bármilyen csomag, illesztőprogramok. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Megjelent az Azure Machine Learning SDK python-v1.0.33-as.

+ Az Azure ML hardveres gyorsított modellek [fpga-kon](how-to-deploy-fpga-web-service.md) általában elérhető.
  + Most már [használhatja az azureml-accel-models csomagot:](how-to-deploy-fpga-web-service.md)
    + A támogatott mély neurális hálózat súlyainak betanítása (ResNet 50, ResNet 152, DenseNet-121, VGG-16 és SSD-VGG)
    + Átviteli tanulás használata a támogatott DNN-nel
    + Regisztrálja a modellt a Modellkezelő szolgáltatással, és konténereztesse a modellt
    + A modell üzembe helyezése egy FPGA-val rendelkező Azure-virtuális gépre egy Azure Kubernetes-szolgáltatás (AKS) fürtben
  + A tároló üzembe helyezése egy Azure Data Box Edge-kiszolgálóeszközre [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Az adatok pontozása a gRPC-végponttal ezzel a [mintával](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizált gépi tanulás

+ Funkcióelsöprő, amely dinamikusan teszi lehetővé :::no-loc text="featurizers"::: a teljesítményoptimalizálást. Új :::no-loc text="featurizers":::: munkabeágyazások, bizonyítékok súlya, célkódolás, szöveges célkódolás, fürttávolság
+ Intelligens CV az automatizált ml-en belüli vonat/érvényes felosztások kezelésére
+ Kevés memóriaoptimalizálási változás és futásidejű teljesítményjavulás
+ Teljesítményjavulás a modell magyarázatában
+ ONNX modell átalakítása helyi futtatáshoz
+ Hozzáadott szubmintavételezési támogatás
+ Intelligens megállás, ha nincs megadva kilépési feltétel
+ Halmozott együttesek

+ Idősoros előrejelzés
  + Új előrejelzési függvény
  + Most már használhatja a gördülő-eredetű keresztérvényesítést az idősorozat-adatokon
  + Az idősorozatok késésének konfigurálásához hozzáadott új funkciók
  + Új funkciók kal bővült a gördülő ablak összesítő funkcióinak támogatásához
  + Új ünnepnap észlelése és featurizer, ha az országkód definiálva van a kísérlet beállításaiban

+ Azure Databricks
  + Engedélyezve az idősorozat-előrejelzés és modellmagyarázat/értelmezhetőségi képesség
  + Most már törölheti és folytathatja (folytathatja) az automatikus gépi tanulási kísérleteket
  + A többmagos feldolgozás támogatása

### <a name="mlops"></a>MLOps
+ **Helyi üzembe helyezés & hibakeresés a pontozási tárolókhoz**<br/> Most már telepítheti a rendszerinformatikai modell helyileg, és gyorsan iterálhatja a pontozási fájl és a függőségek annak érdekében, hogy a várt módon viselkednek.

+ **Bevezetett inferenceConfig & Model.deploy()**<br/> A modell központi telepítése mostantól támogatja egy bejegyzési parancsfájllal rendelkező forrásmappa megadását, amely megegyezik a RunConfig-éval.  Emellett a modell üzembe helyezése egyetlen parancsra lett egyszerűsítve.

+ **Git hivatkozáskövetés**<br/> Az ügyfelek már egy ideje alapvető Git-integrációs képességeket kérnek, mivel ez segít a végpontok közötti naplózási nyomvonal fenntartásában. Az Azure ML-ben a Git-hez kapcsolódó metaadatok (tártár, véglegesítés, tiszta állapot) a főbb entitások között végrehajtottuk a nyomon követést. Ezeket az információkat az SDK és a CLI automatikusan gyűjti.

+ **Modellprofil-profilkészítési &-érvényesítési szolgáltatás**<br/> Az ügyfelek gyakran panaszkodnak arra, hogy nehéz megfelelően méretezni a következtetési szolgáltatásukhoz társított számítást. Modell profilalkotási szolgáltatásunkkal az ügyfél mintabemeneteket biztosíthat, és 16 különböző CPU/ memória konfiguráción keresztül profilozunk, hogy meghatározzuk az üzembe helyezés optimális méretezését.

+ **Hozza magával a saját alapképét a következtetéshez**<br/> Egy másik gyakori panasz volt a kísérletből a re megosztási függőségek következtetésére való áttérés nehézsége. Az új alap képmegosztási képességünkkel most már újra felhasználhatja a kísérletezési alapképeket, függőségeket és mindent, hogy következtetéseket vonjon le. Ez felgyorsítja a központi telepítéseket, és csökkenti a belső és a külső hurok közötti rést.

+ **Továbbfejlesztett Swagger séma-generálási élmény**<br/> A korábbi swagger generációs módszer hibahajlamos volt, és lehetetlen automatizálni. Van egy új in-line módon generáló swagger sémák bármely Python funkció keresztül lakberendezők. Nyílt forráskódú ezt a kódot, és a séma generációs protokoll nem kapcsolódik az Azure ML platformhoz.

+ **Az Azure ML CLI általánosan elérhető (GA)**<br/> A modellek mostantól egyetlen CLI-paranccsal telepíthetők. Általános ügyfél-visszajelzést kaptunk arról, hogy senki sem telepít im-modellt egy Jupyter-jegyzetfüzetből. A [**CLI referenciadokumentációja**](https://aka.ms/azmlcli) frissült.


## <a name="2019-04-22"></a>2019-04-22

Megjelent az Azure Machine Learning SDK python-v1.0.30-hoz.

A [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) bevezetésegy közzétett folyamat új verziójának hozzáadására került, miközben ugyanazt a végpontot fenntartotta.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning Data Prep SDK 1.1.2-es v1.1.2

Megjegyzés: A Data Prep Python SDK már nem telepíti `numpy` és `pandas` a csomagokat. Lásd a [frissített telepítési utasításokat](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Új funkciók**
  + Most már használhatja a Pivot transzformációt.
    + Útmutató: Kimutatás [i](https://aka.ms/aml-data-prep-pivot-nb)
  + Mostantól használhat reguláris kifejezéseket a natív függvényekben.
    + Példák:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Most már `to_upper`  használhatja és `to_lower`  használhatja a kifejezésnyelvben.
  + Most már láthatja az adatprofil egyes oszlopainak egyedi értékeit.
  + Néhány általánosan használt olvasó lépéseket, akkor most `infer_column_types` adja át az argumentumot. Ha `True`a beállítás a , a Data Prep megpróbálja észlelni és automatikusan konvertálni az oszloptípusokat.
    + `inference_arguments`most elavult.
  + Most már `Dataflow.shape`hívhatja.

+ **Hibajavítások és fejlesztések**
  + `keep_columns` most elfogad egy további `validate_column_exists`választható argumentumot `keep_columns` , amely ellenőrzi, hogy az eredmény tartalmaz-e oszlopokat.
  + Az olvasó összes lépése (amely fájlból olvasható) `verify_exists`most már elfogad egy további választható argumentumot.
  + A pandák adatkeretéből történő olvasás és az adatprofilok beolvasásának javítása.
  + Javítottunk egy hibát, amely miatt egyetlen lépés egyetlen lépés sel egyetlen indexeléssel nem sikerült.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Most már újra elküldheti egy meglévő parancsfájl futtatását egy meglévő távoli számítási fürtön.
  + Most már futtathat egy közzétett folyamatot új paraméterekkel a Folyamatok lapon.
  + A Futtatás részletei mostantól támogatják az új Snapshot fájlnézegetőt. Megtekintheti a könyvtár pillanatképét egy adott futtatás beküldésekor. A futtatás elindításához elküldött jegyzetfüzetet is letöltheti.
  + Most már megszakíthatja a szülőfuttatásokat az Azure Portalról.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK python-hoz 1.0.23-as v1.0.23

+ **Új funkciók**
  + Az Azure Machine Learning SDK mostantól támogatja a Python 3.7-es.
  + Az Azure Machine Learning DNN estimators mostantól többverziós beépített támogatást nyújt. Például `TensorFlow`  a estimator most `framework_version` elfogad egy paramétert, és a felhasználók megadhatják az "1.10" vagy "1.12" verziót. Az aktuális SDK-kiadás által támogatott verziók `get_supported_versions()` listájának megnevezése `TensorFlow.get_supported_versions()`a kívánt keretrendszerosztályra (például ).
  A legújabb SDK-kiadás által támogatott verziók listáját a [DNN Estimator dokumentációjában](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)találja.

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning Data Prep SDK 1.1.1-es v1.1.1

+ **Új funkciók**
  + Több Datastore/DataPath/DataReference forrás t is elolvashat read_* átalakítások használatával.
  + Az oszlopokon a következő műveleteket hajthatja végre, hogy új oszlopot hozzon létre: osztás, padló, modulo, teljesítmény, hossz.
  + A Data Prep most már az Azure ML diagnosztikai csomag része, és alapértelmezés szerint naplózza a diagnosztikai információkat.
    + A kikapcsoláshoz állítsa a környezeti változót true értékre: DISABLE_DPREP_LOGGER

+ **Hibajavítások és fejlesztések**
  + Továbbfejlesztett kóddokumentáció az általánosan használt osztályokhoz és függvényekhez.
  + Javítottunk egy hibát a auto_read_file, amely nem tudta olvasni az Excel-fájlokat.
  + Hozzáadott lehetőség, hogy felülírja a mappát read_pandas_dataframe.
  + A dotnetcore2 függőségi telepítésének jobb teljesítménye, és a Fedora 27/28 és az Ubuntu 1804 támogatása.
  + Az Azure Blobs olvasási teljesítményének javítása.
  + Az oszloptípus-észlelés mostantól támogatja a Hosszú típusú oszlopokat.
  + Javítva egy hiba, ahol néhány dátumértékek voltak jelennek meg időbélyegként helyett Python datetime objektumok.
  + Javítottunk egy hibát, amely szerint egyes típusok száma itáliai számok helyett párosként jelent meg.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK python-hoz 1.0.21-es v1.0.21

+ **Új funkciók**
  + Az *azureml.core.Run.create_children* metódus lehetővé teszi, hogy egy hívással több gyermek futtatása alacsony késésű legyen.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning Data Prep SDK 1.1.0-s v1.1.0

+ **Változások megbontása**
  + Az adat-előkészítési csomag koncepciója elavult, és már nem támogatott. Ahelyett, hogy több adatfolyamot őrizne meg egy csomagban, az adatfolyamokat külön-külön is megőrizheti.
    + Útmutató: [Adatfolyamok jegyzetfüzetmegnyitása és mentése](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Új funkciók**
  + A Data Prep mostantól felismeri az adott szemantikai típusnak megfelelő oszlopokat, és ennek megfelelően felosztható. A jelenleg támogatott STypes típusok a következők: e-mail cím, földrajzi koordináták (szélességi & hosszúság), IPv4- és IPv6-címek, amerikai telefonszám és amerikai irányítószám.
    + Útmutató: [Szemantikai típusú jegyzetfüzet](https://aka.ms/aml-data-prep-semantic-types-nb)
  + A Data Prep mostantól a következő műveleteket támogatja, hogy két numerikus oszlopból hozzon létre egy eredményoszlopot: kivonás, szorzás, osztás és modulo.
  + Az adatfolyamot felhívhatja `verify_has_data()` annak ellenőrzésére, hogy az adatfolyam rekordokat hozna-e létre, ha végrehajtják.

+ **Hibajavítások és fejlesztések**
  + Most már megadhatja a numerikus oszlopprofilok hisztogramjában használandó raktárhelyek számát.
  + Az `read_pandas_dataframe` átalakításhoz most a DataFrame-nek karakterlánc- vagy bájtbeírt oszlopnevekkel kell rendelkeznie.
  + Javítottunk egy `fill_nulls` hibát az átalakításban, ahol az értékek nem voltak megfelelően kitöltve, ha az oszlop hiányzott.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK python-hoz 1.0.18-as

 + **Változások**
   + Az azureml-tensorboard csomag az azureml-contrib-tensorboard helyébe lép.
   + Ezzel a kiadással felhasználói fiókot állíthat be a felügyelt számítási fürtön (amlcompute), miközben létrehozza azt. Ez úgy érhető el, hogy átadja ezeket a tulajdonságokat a létesítési konfigurációban. További részleteket az [SDK referenciadokumentációjában](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)talál.

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Új funkciók**
  + Mostantól támogatja két numerikus oszlop hozzáadását, hogy a kifejezés nyelvével létrejtesd az eredményoszlopot.

+ **Hibajavítások és fejlesztések**
  + Javítottuk a dokumentációt és a paraméterek ellenőrzését a random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Hibajavítás**
  + Kijavítottuk az első szolgáltatás hitelesítési problémát, amelyet egy API-módosítás okozott.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK python-hoz 1.0.17-es v1.0.17

+ **Új funkciók**

  + Az Azure Machine Learning mostantól első osztályú támogatást nyújt a népszerű DNN-keretrendszerláncolóhoz. Az [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) osztályfelhasználók használata könnyedén betaníthatja és üzembe helyezheti a Chainer modelleket.
    + Ismerje meg, hogyan futtathatja az [elosztott képzést a ChainerMN-nel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + A [hyperparameter tuning hyperdrive használatával történő futtatása](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Az Azure Machine Learning-folyamatok az adattár módosításai alapján aktiválhatók. A [folyamatütemezési jegyzetfüzet](https://aka.ms/pl-schedule) frissül, hogy bemutassa ezt a funkciót.

+ **Hibajavítások és fejlesztések**
  + Az Azure Machine Learning-folyamatok ban támogatást adtunk hozzá a source_directory_data_store tulajdonság kívánt adattárba (például egy blobstorage)-ra állításához a [PythonScriptStep-hez](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)megadott [RunConfigurations-on.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) Alapértelmezés szerint lépések használata Azure File Store, mint a biztonsági adattár, amely előfordulhat, hogy szabályozási problémák, ha nagy számú lépést egyidejűleg hajt végre.

### <a name="azure-portal"></a>Azure Portal

+ **Új funkciók**
  + Új húzási táblázatszerkesztő-élmény a jelentésekhez. A felhasználók áthúzhatnak egy oszlopot a kútból arra a táblázatterületre, ahol megjelenik a táblázat előnézete. Az oszlopok átrendezhetők.
  + Új naplók fájlmegjelenítője
  + A tevékenységek lapról származó kísérletfuttatásokra, számítási műveletekre, modellekre, lemezképekre és központi telepítésekre mutató hivatkozások

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning Data Prep SDK v1.0.15

+ **Új funkciók**
  + A Data Prep mostantól támogatja az adatfolyamokból származó fájladatfolyamok írását. Azt is lehetővé teszi, hogy a fájlfolyam-neveket új fájlnevek létrehozásához manipulálja.
    + Útmutató: A [fájladatfolyamok jegyzetfüzetének megsegítése](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Hibajavítások és fejlesztések**
  + A t-Digest jobb teljesítménye nagy adatkészleteken.
  + A Data Prep mostantól támogatja a DataPath-ból származó adatok olvasását.
  + Egy gyorskódolás mostantól logikai és numerikus oszlopokon is működik.
  + Egyéb egyéb hibajavítások.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK python-hoz 1.0.15-ös v1.0.15

+ **Új funkciók**
  + Az Azure Machine Learning Pipelines hozzáadta az AzureBatchStep[(notebook),](https://aka.ms/pl-azbatch)a HyperDriveStep (notebook) és az időalapú ütemezési funkciót ([jegyzetfüzet).](https://aka.ms/pl-schedule)
  +  A DataTranferStep frissítve az Azure SQL Server és a PostgreSQL ([notebook)](https://aka.ms/pl-data-trans)Azure SQL Server és Azure adatbázisa számára.

+ **Változások**
  + Elavult `PublishedPipeline.get_published_pipeline` javára `PublishedPipeline.get`.
  + Elavult `Schedule.get_schedule` javára `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **Új funkciók**
  + A Data Prep mostantól támogatja az Azure SQL-adatbázisból történő olvasást a Datastore használatával.

+ **Változások**
  + Javította bizonyos műveletek memóriateljesítményét nagy méretű adatokon.
  + `read_pandas_dataframe()`most `temp_folder` meg kell határozni.
  + A `name` beírt `ColumnProfile` tulajdonság elavult - `column_name` helyette használja.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK python-hoz 1.0.10-es v1.0.10

+ **Változások**:
  + Az Azure ML SDK már nem rendelkezik azure-cli csomagok függőségként. Pontosabban az azure-cli-core és az azure-cli-profile függőségek lettek eltávolítva az azureml-core-ból. Ezek a felhasználók által befolyásoló változások:
      + Ha az "az login" kifejezést hajtja végre, majd az azureml-sdk-ot használja, az SDK még egyszer elvégzi a böngésző vagy az eszköz kódbejelentkezését. Nem használja az "az login" által létrehozott hitelesítő adatok állapotát.
    + Az Azure CLI-hitelesítéshez, például az "az login" használatához használja _az azureml.core.authentication.AzureCliAuthentication_ osztályt. Az Azure CLI-hitelesítéshez _a pip telepítse az azure-cli-t_ abban a Python-környezetben, ahol telepítette az azureml-sdk-t.
    + Ha az "az login" szolgáltatást használ az automatizáláshoz, javasoljuk, _hogy azureml.core.authentication.ServicePrincipalAuthentication_ osztály használata, az azureml-sdk nem használja az azure CLI által létrehozott hitelesítő adatok állapotát.

+ **Hibajavítások**: Ez a kiadás többnyire kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK 1.0.8-as v1.0.8

+ **Hibajavítások**
  + Javította az adatprofilok beszerzésének teljesítményét.
  + Javítottuk a hibajelentéssel kapcsolatos kisebb hibákat.

### <a name="azure-portal-new-features"></a>Azure portal: új funkciók
+ Új húzási diagramkészítési élmény a jelentésekhez. A felhasználók áthúzhatnak egy oszlopot vagy attribútumot a kútból arra a diagramterületre, ahol a rendszer automatikusan kiválasztja a felhasználó nak megfelelő diagramtípust az adatok típusa alapján. A felhasználók módosíthatják a diagram típusát más megfelelő típusokra, vagy további attribútumokat adhatnak hozzá.

    Támogatott diagramtípusok:
    - Vonaldiagram
    - Hisztogram
    - Halmozott sávdiagram
    - Dobozos telek
    - Ponttelek
    - Buborékdiagram
+ A portál most dinamikusan jelentéseket készít a kísérletekhez. Amikor egy felhasználó beküld egy futtatást egy kísérletbe, a rendszer automatikusan jelentést hoz létre a naplózott mérőszámokkal és grafikonokkal, hogy lehetővé tegye a különböző futtatások összehasonlítását.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK python-hoz 1.0.8-as

+ **Hibajavítások**: Ez a kiadás többnyire kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK 1.0.7-es v1.0.7

+ **Új funkciók**
  + Adattár fejlesztései (dokumentálva az [adattár útmutatójában)](https://aka.ms/aml-data-prep-datastore-nb)
    + Az Azure File Share és az ADLS Datastores-ba történő olvasás és írás lehetősége a felskálázásban hozzáadott.
    + Datastores használata esetén a Data Prep mostantól támogatja az egyszerű szolgáltatáshitelesítés használatát az interaktív hitelesítés helyett.
    + Hozzáadott támogatása wasb és wasbs urls.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK 1.0.6-os v1.0.6

+ **Hibajavítások**
  + Javítottuk a hibát a nyilvánosan olvasható Azure Blob-tárolókból való olvasással a Sparkon

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK python-hoz 1.0.6-os
+ **Hibajavítások**: Ez a kiadás többnyire kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK 1.0.4-es v1.0.4

+ **Új funkciók**
  + `to_bool`a függvény mostantól lehetővé teszi, hogy a nem egyező értékeket hibaértékekké konvertálja. Ez az új alapértelmezett eltérési `to_bool` `set_column_types`viselkedés a és a esetében, míg az előző alapértelmezett viselkedés az volt, hogy az egyező értékeket hamisértékre konvertálta.
  + A `to_pandas_dataframe`híváskor új lehetőség van a null/hiányzó értékek numerikus oszlopokban NaN-ként értelmezhető.
  + Hozzáadott képes ellenőrizni a visszatérési típusa egyes kifejezések típuskonzisztencia és nem korai.
  + Most már `parse_json` json-objektumokként elemezheti az oszlopok értékeit, és több oszlopra bonthatja őket.

+ **Hibajavítások**
  + Javítva egy hiba, amely lezuhant `set_column_types` a Python 3.5.2-ben.
  + Javítva egy hiba, amely összeomlott, amikor AML-lemezképpel csatlakozott az Adattárhoz.

+ **Frissítések**
  * [Példa jegyzetfüzetek](https://aka.ms/aml-data-prep-notebooks) az első lépésekhez oktatóanyagok, esettanulmányok és útmutatók.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Általános elérhetőség

Az Azure Machine Learning már általánosan elérhető.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
Ezzel a kiadással egy új felügyelt számítási élményt jelentünk be az [Azure Machine Learning Compute segítségével.](how-to-set-up-training-targets.md#amlcompute) Ez a számítási cél felváltja az Azure Batch AI számítási Azure Machine Learning.

Ez a számítási cél:
+ Modellbetanításhoz és kötegelt következtetéshez/pontozáshoz használatos
+ Egy- és többcsomópontos számítás
+ A fürtkezelés és a feladatütemezés a felhasználó számára
+ Automatikus skálázás alapértelmezés szerint
+ A CPU- és A GPU-erőforrások támogatása
+ Alacsony prioritású virtuális gépek használatát teszi lehetővé csökkentett költségek esetén

Az Azure Machine Learning Compute pythonban, az Azure Portalon vagy a CLI használatával hozható létre. A munkaterület régiójában kell létrehozni, és nem csatolható más munkaterülethez. Ez a számítási cél egy Docker-tárolót használ a futtatáshoz, és a függőségek csomagok replikálja ugyanazt a környezetet az összes csomóponton.

> [!Warning]
> Azt javasoljuk, hogy hozzon létre egy új munkaterületet az Azure Machine Learning Compute használatához. Van egy távoli esély, hogy a felhasználók próbálnak létrehozni az Azure Machine Learning Compute egy meglévő munkaterületről előfordulhat, hogy egy hiba. A munkaterület meglévő számítási területén továbbra is működnie kell.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK python-hoz 1.0.2-es
+ **Változások megbontása**
  + Ezzel a kiadással eltávolítjuk a virtuális gép létrehozásának támogatását az Azure Machine Learningből. Továbbra is csatolhat egy meglévő felhőbeli virtuális gép vagy egy távoli helyszíni kiszolgáló.
  + Eltávolítjuk a BatchAI támogatását is, amelyek mindegyikét az Azure Machine Learning Compute-on keresztül kell támogatni.

+ **Új**
  + Gépi tanulási folyamatokhoz:
    + [EstimatorStep (EstimatorStep)](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Frissítve**
  + Gépi tanulási folyamatokhoz:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) most elfogadja runconfig
    + [A DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) mostantól SQL adatforrásba és sql adatforrásból is átmásol
    + Az SDK-ban lévő funkciók ütemezése a közzétett folyamatok futtatásához szükséges ütemezések létrehozásához és frissítéséhez

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **Változások megbontása**
  * `SummaryFunction.N`a nevet `SummaryFunction.Count`átnevezte .

+ **Hibajavítások**
  * Használja a legújabb AML-alapú szennivaló-alapú jogkivonatot, amikor távoli futtatásokon olvassa az adattárakból, és azokat írja. Korábban, ha az AML-beli futtatási jogkivonat frissül a Pythonban, a Data Prep futásidejű nem frissül a frissített AML-futtatási jogkivonattal.
  * További egyértelműbb hibaüzenetek
  * a(z) to_spark_dataframe() nem `Kryo` omol össze, ha a Spark szerializálást használ
  * Az Értékszámlálási felügyelő mostantól több mint 1000 egyedi értéket tud megjeleníteni
  * A véletlen felosztás már nem sikertelen, ha az eredeti adatfolyamnak nincs neve

+ **További információk**
  * [Azure Machine Learning adat-előkészítési SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumentumok és jegyzetfüzetek
+ ML-folyamatok
  + Új és frissített jegyzetfüzetek a folyamatok, a kötegek hatókörének és a stílusátvitelhez:https://aka.ms/aml-pipeline-notebooks
  + Az [első folyamat létrehozásának megismerése](how-to-create-your-first-pipeline.md)
  + A [kötegelt előrejelzések folyamatokkal történő futtatása](how-to-use-parallel-run-step.md)
+ Az Azure Machine Learning számítási célja
  + [A mintajegyzetfüzetek](https://aka.ms/aml-notebooks) most frissülnek az új felügyelt számítás használatához.
  + [További információ erről a számításról](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure portal: új funkciók
+ Azure [Machine Learning compute-típusok](how-to-set-up-training-targets.md#amlcompute) létrehozása és kezelése a portálon.
+ Figyelheti a kvótahasználatot, és [kérheti](how-to-manage-quotas.md) az Azure Machine Learning Compute-t.
+ Tekintse meg az Azure Machine Learning Compute fürt állapotát valós időben.
+ Virtuális hálózati támogatás lett hozzáadva az Azure Machine Learning Compute és az Azure Kubernetes szolgáltatás létrehozásához.
+ Futtassa újra a közzétett folyamatokat a meglévő paraméterekkel.
+ Új [automatizált gépi tanulási diagramok](how-to-understand-automated-ml.md) besorolási modellekhez (lift, nyereség, kalibráció, jellemzőfontossági diagram modellmagyarázással) és regressziós modellekhez (maradékok és jellemzőfontossági diagram modellmegmagyarázhatósággal).
+ A folyamatok megtekinthetők az Azure Portalon




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK python-hoz 0.1.80-as v0.1.80

+ **Változások megbontása**
  * *Az azureml.train.widgets* névtér az *azureml.widgets*oldalra került.
  * *az azureml.core.compute.AmlCompute* a következő osztályokat mentesíti - *azureml.core.compute.BatchAICompute* és *azureml.core.compute.DSVMCompute*. Az utóbbi osztály a későbbi kiadásokban eltávolításra kerül. Az AmlCompute osztály most már könnyebb definícióval rendelkezik, és egyszerűen csak egy vm_size és a max_nodes, és automatikusan méretezi a fürtöt 0-ról a max_nodes feladat beküldésekor. [Mintajegyzetfüzeteink frissültek](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) ezekkel az információkkal, és használati példákat kell adniuk. Reméljük, tetszik ez az egyszerűsítés és még sok más izgalmas funkciók, hogy jöjjön egy későbbi kiadás!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1

A Data Prep SDK-ról a referenciadokumentumok olvasásával olvashat [bővebben.](https://aka.ms/data-prep-sdk)
+ **Új funkciók**
   * Új DataPrep CLI létrehozása a DataPrep-csomagok végrehajtásához és egy adatkészlet vagy adatfolyam adatprofiljának megtekintéséhez
   * A SetColumnType API újratervezett e-kulcsa a használhatóság javítása érdekében
   * Átnevezve smart_read_file auto_read_file
   * Most már tartalmazza a ferde és kurtosis az adatprofil
   * Lehet mintát rétegzett mintavétel
   * Képes olvasni a ZIP fájlokat tartalmazó CSV fájlokat
   * Az adatkészletek et sorba oszthatja a Random Split segítségével (például tesztvonat-készletekre)
   * Az összes oszlopadattípust beszerezheti egy adatfolyamból vagy egy adatprofilból, ha meghívja`.dtypes`
   * A sorszámot adatfolyamból vagy adatprofilból lehet lekéri, ha meghívja`.row_count`

+ **Hibajavítások**
   * Rögzített hosszú és kettős konverzió
   * Rögzített megerősítés bármely add oszlop után
   * Kijavítottuk a FuzzyGrouping hibáját, ahol bizonyos esetekben nem észleltcsoportokat
   * Rögzített rendezési funkció a többoszlopos rendezési sorrend tiszteletben tartása érdekében
   * Rögzített és /vagy kifejezések, hogy `pandas` hasonló legyen, ahogyan kezeli őket
   * Rögzített olvasás a dbfs útvonalról
   * A hibaüzenetek érthetőbbé tették
   * Most már nem sikerül, ha olvasás távoli számítási cél segítségével AML token
   * Most már nem sikerül a Linux DSVM
   * Most már nem összeomlik, ha a nem karakterlánc-értékek karakterlánc-predikátumokban vannak
   * Most antól kezeli a helyességi helyességi hibákat, ha az adatfolyam nak megfelelően meg kell hibásullennie
   * Mostantól támogatja a dbutils csatlakoztatott tárolási helyeket az Azure Databricks-en

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal
Az Azure Machine Learning Azure Portalon a következő frissítéseket tartalmaz:
  * A közzétett folyamatok új **folyamatok** lapja.
  * Egy meglévő HDInsight-fürt számítási célként való csatolásának támogatása.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK python-hoz 0.1.74-es v0.1.74

+ **Változások megbontása**
  * *A Workspace.compute_targets, az adattárak, a kísérletek, a képek, a modellek és *a webszolgáltatások* metódusok helyett tulajdonságok. Cserélje le például *a Munkaterület.compute_targets() programot* *a Workspace.compute_targets.*
  * *A Run.get_context* elavultak *a Futtatás.get_submitted_run*. Az utóbbi módszer a későbbi kiadásokban eltávolításra kerül.
  * *A PipelineData* osztály mostantól nem datastore_name, hanem paraméterként számít egy adattárobjektumra. Hasonlóképpen *a Pipeline* default_datastore fogad el, nem pedig default_datastore_name.

+ **Új funkciók**
  * Az Azure Machine Learning-folyamatok [mintanotebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) most mpi lépéseket használ.
  * A Jupyter-jegyzetfüzetek RunDetails widgetje frissül, hogy a folyamat vizualizációját jelenítse meg.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0

+ **Új funkciók**
  * Az adatprofilhoz hozzáadott típusszám
  * Az Értékszám és a Hisztogram már elérhető
  * További százalékok az adatprofilban
  * A medián elérhető összegző
  * A Python 3.7 már támogatott
  * Amikor adatfolyamokat tartalmazó adatfolyamot ment egy DataPrep-csomagba, az adattár adatai a DataPrep csomag részeként megmaradnak
  * Az adattárba való írás mostantól támogatott

+ **Hiba javítva**
  * A 64 bites, aláíratlan egész túlcsordulás oka a Linux on
  * Rögzített helytelen szöveges címke az egyszerű szöveges fájlok smart_read
  * A karakterláncoszlop-típus mostantól megjelenik a metrikák nézetben
  * A típusszám most úgy van rögzítve, hogy az egyes mezőtípusok helyett egyetlen FieldType-hoz leképezett ValueKinds-t jelenítse meg
  * Write_to_csv már nem sikertelen, ha az elérési út karakterláncként van megadva
  * A Csere használata esetén a "keresés" üresen hagyása többé nem fog sikertelen

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK python-hoz 0.1.68-as v0.1.68

+ **Új funkciók**
  * Több-bérlős támogatás új munkaterület létrehozásakor.

+ **Hibák javítva**
  * A webszolgáltatás üzembe helyezésekor már nem kell rögzítenie a pynacl könyvtár verzióját.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning Data Prep SDK v0.3.0

+ **Új funkciók**
  * Hozzáadott módszer transform_partition_with_file(script_path), amely lehetővé teszi a felhasználók számára, hogy adja át az elérési úton egy Python fájl végrehajtása

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK python-hoz 0.1.65-ös v0.1.65
[A 0.1.65-ös verzió](https://pypi.org/project/azureml-sdk/0.1.65) új funkciókat, további dokumentációt, hibajavításokat és több [mintajegyzetfüzetet](https://aka.ms/aml-notebooks)tartalmaz.

Az ismert hibák és kerülő megoldások [listájában](resource-known-issues.md) olvashat.

+ **Változások megbontása**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services visszaad szótár, korábban visszaadott lista. Lásd: [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API dokumentáció.

  * Az automatikus gépi tanulás eltávolította a normalizált négyzetes hibát az elsődleges metrikákból.

+ **HyperDrive (Hipermeghajtó)**
  * Különböző HyperDrive hibajavítások a Bayes-i, teljesítménybeli fejlesztések a metrikák hívásainak fogadásához.
  * Tensorflow 1.10 frissítés 1,9-ről
  * Docker-képoptimalizálás hidegindításhoz.
  * A feladatok mostantól akkor is jelentik a helyes állapotot, ha 0-tól eltérő hibakóddal lépnek ki.
  * RunConfig attribútum érvényesítése az SDK-ban.
  * A HyperDrive futtatási objektum támogatja a normál futtatáshoz hasonló megszakítást: nem kell paramétereket átadnia.
  * Widget fejlesztések az elosztott futtatások és a HyperDrive legördülő értékeinek fenntartásához.
  * TensorBoard és más naplófájlok támogatása fix paraméter szerver.
  * Intel(R) MPI támogatás a szerviz oldalon.
  * Bugfix a paraméter hangoláselosztott run fix érvényesítése során BatchAI.
  * A Context Manager mostantól azonosítja az elsődleges példányt.

+ **Az Azure Portal élménye**
  * a log_table() és a log_row() a Futtatás részletei ben támogatottak.
  * Diagramok automatikus létrehozása 1, 2 vagy 3 numerikus oszlopot és választható kategorikus oszlopot tartalmazó táblázatokhoz és sorokhoz.

+ **Automatizált gépi tanulás**
  * Továbbfejlesztett hibakezelés és dokumentáció
  * Javítottuk a futtatási tulajdonságlekérési teljesítményproblémákat.
  * Javítva a futtatás folytatásának kérdése.
  * Javítottuk :::no-loc text="ensembling"::: az iterációs problémákat.
  * Rögzített képzés lógó hiba-ra MAC OS.
  * A makróátlag-érték PR/ROC-görbét az egyéni érvényesítési forgatókönyvben.
  * Eltávolított extra index logika.
  * Szűrő eltávolítása get_output API-ból.

+ **Folyamatok**
  * Hozzáadott egy metóduspipeline.publish() közzé egy folyamatot közvetlenül, anélkül, hogy egy végrehajtási futtatás először.
  * Hozzáadott egy metódust PipelineRun.get_pipeline_runs() a közzétett folyamatból létrehozott folyamatfuttatások beolvasásához.

+ **Projekt Brainwave**
  * Az FPGA-kon elérhető új AI modellek frissített támogatása.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning Data Prep SDK v0.2.0
[A 0.2.0-s verzió](https://pypi.org/project/azureml-dataprep/0.2.0/) a következő funkciókat és hibajavításokat tartalmazza:

+ **Új funkciók**
  * Az egy melegen felmelegített kódolás támogatása
  * Quantile transzformáció támogatása

+ **Hiba javítva:**
  * Működik minden Tornado verzió, nem kell visszaminősíteni a Tornado verzió
  * Az érték az összes értéket számolja, nem csak az első háromértéket

## <a name="2018-09-public-preview-refresh"></a>2018-09 (Nyilvános előzetes verzió frissítése)

Az Azure Machine Learning új, frissített kiadása: További információ erről a kiadásról:https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>További lépések

Olvassa el az [Azure Machine Learning](overview-what-is-azure-ml.md) áttekintését.
