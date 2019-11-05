---
title: 'Oktatóanyag: az első ML-kísérlet az R-vel'
titleSuffix: Azure Machine Learning
description: Ebben az oktatóanyagban megismerheti a Azure Machine Learning alapszintű kialakítási mintáit, és az R-csomagok azuremlsdk és a kalap használatával betanítja a logisztikai regressziós modell modelljét, hogy megjósolja a végzetes valószínűségét egy autóbeli balesetben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 11/04/2019
ms.openlocfilehash: bcd1fff61e1612cc3361548527e5ed13affa3ba5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501371"
---
# <a name="tutorial-train-and-deploy-your-first-model-in-r-with-azure-machine-learning"></a>Oktatóanyag: az első modell betanítása és üzembe helyezése az R-ben Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben az oktatóanyagban megismerheti a Azure Machine Learning alapszintű kialakítási mintáit.  Egy olyan **kalap** -modellt fog betanítani és üzembe helyezni, amely megjósolja, hogy milyen valószínűséggel lehet végzetesnek lennie egy autóbeli balesetben. Az oktatóanyag elvégzése után az R SDK gyakorlati ismeretekkel fog rendelkezni az összetettebb kísérletek és munkafolyamatok fejlesztéséhez.

Ez az oktatóanyag a következő feladatokat ismerteti:

> [!div class="checklist"]
> * A munkaterület összekötése
> * Adatgyűjtés és felkészülés a képzésre
> * Adatok feltöltése az adattárba, hogy elérhető legyen a távoli képzéshez
> * Számítási erőforrás létrehozása
> * Egy Kalapos modell betanítása a halálozás valószínűségének előrejelzésére
> * Előrejelzési végpont üzembe helyezése
> * A modell tesztelése az R-ből

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisites"></a>Előfeltételek

1. Kövesse a [telepítési utasításokat](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) a következőre:
    + Az anaconda telepítése
    + `azuremlsdk` telepítése
    + A Pythonhoz készült Azure Machine Learning SDK telepítése

1. Szerezze be a három oktatóanyag-fájlt a [githubról](https://github.com/Azure/azureml-sdk-for-r/tree/master/vignettes/train-and-deploy-with-caret).  Mentse őket egy **oktatóanyagok** könyvtárába.

2. Hozzon létre egy Azure Machine Learning munkaterületet, és töltse le a konfigurációs fájlját az alábbi lépésekkel.


### <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning munkaterület a felhőben található alapvető erőforrás, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használható. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz fűzi az SDK-ban. Ha már rendelkezik Azure Machine Learning munkaterülettel, [ugorjon a következő szakaszra](#config). Ellenkező esetben hozzon létre egyet most.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="config"></a>A config. JSON fájl letöltése

1. A munkaterület lap tetején válassza a **config. JSON letöltése**lehetőséget.

    ![A config. JSON fájl letöltése](media/tutorial-1st-r-experiment/download-config.png)  

1. Adja hozzá ezt a fájlt az **oktatóanyagok** könyvtárához.

Most már készen áll az oktatóanyag futtatására.

Javasoljuk, hogy a RStudio használatával futtassa ezt az oktatóanyagot. A RStudio területen válassza a fájl > új projekt > meglévő könyvtár lehetőséget, majd válassza ki a fent létrehozott **oktatóanyagok** könyvtárat.

> [!Important]
> A cikk többi része ugyanazokat a tartalmakat tartalmazza, mint amit a **Train-and-Deploy-ACI-ban lát. RMD** -fájl.  
> Ha a RMarkdown-t használja, nyugodtan használhatja az adott fájl kódját.  Vagy másolhatja vagy beillesztheti a kódrészleteket onnan, vagy ebből a cikkből egy R-parancsfájlba vagy a parancssorba.


## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása
Az oktatóanyagban a fejlesztési munka beállítása a következő műveleteket tartalmazza:

* Szükséges csomagok telepítése
* Kapcsolódás munkaterülethez, hogy a helyi számítógép képes legyen kommunikálni a távoli erőforrásokkal
* Kísérlet létrehozása a futtatások nyomon követéséhez
* A képzéshez használandó távoli számítási cél létrehozása

### <a name="install-required-packages"></a>Szükséges csomagok telepítése
Ez az oktatóanyag feltételezi, hogy már telepítette az Azure ML SDK-t. Lépjen tovább, és importálja a **azuremlsdk** csomagot.

```R
library(azuremlsdk)
```

Az oktatóanyag a [ **DAAG** -csomag](https://cran.r-project.org/package=DAAG)adatait használja. Ha nem rendelkezik ezzel a csomaggal, telepítse a csomagot.

```R
install.packages("DAAG")
```

A betanítási és pontozási parancsfájlok (`accidents.R` és `accident_predict.R`) további függőségekkel rendelkeznek. Ha a parancsfájlok helyi futtatását tervezi, győződjön meg arról, hogy rendelkezik a szükséges csomagokkal is.

### <a name="load-your-workspace"></a>Munkaterület betöltése
Munkaterület-objektum példányának létrehozása a meglévő munkaterületről. A következő kód betölti a munkaterület részleteit a **config. JSON** fájlból. [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html)használatával is lekérheti a munkaterületet.

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása
Az Azure ML-kísérlet a futtatások csoportosítását követi nyomon, jellemzően ugyanabból a betanítási szkriptből. Hozzon létre egy kísérletet a futtatások nyomon követéséhez, hogy betanítsa a kalap modelljét a balesetek adattípusán.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Számítási cél létrehozása
Azure Machine Learning számítás (AmlCompute) használatával a felügyelt szolgáltatás, az adatszakértők pedig gépi tanulási modelleket hozhatnak létre az Azure-beli virtuális gépek fürtjén. Ilyenek például a GPU-támogatással rendelkező virtuális gépek. Ebben az oktatóanyagban egy egycsomópontos AmlCompute-fürtöt hoz létre oktatási környezetként. Az alábbi kód létrehozza a számítási fürtöt, ha még nem létezik a munkaterületen.

Előfordulhat, hogy néhány percet várnia kell, amíg a számítási fürt kiépíthető, ha még nem létezik.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>Az adatképzés előkészítése
Ez az oktatóanyag a **DAAG** -csomag adatait használja. Ez az adatkészlet több mint 25 000 autóbalesetben tartalmaz adatokat az Egyesült Államokban, és a változókat a végzetes valószínűségének előrejelzésére használhatja. Először importálja az adatfájlokat az R-be, és alakítsa át egy új dataframe-`accidents` elemzésre, és exportálja egy `Rdata` fájlba.

```R
library(DAAG)
data(nassCDS)

accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Adatok feltöltése az adattárba
Töltse fel az adatait a felhőbe, hogy elérhető legyen a távoli képzési környezetében. Minden egyes Azure ML-munkaterület tartalmaz egy alapértelmezett adattárt, amely a kapcsolódási adatokat a munkaterülethez csatolt Storage-fiókban kiépített Azure Blob-tárolóba tárolja. A következő kód feltölti a fent létrehozott baleseti adatok számát az adattárba.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Modell betanítása

Ebben az oktatóanyagban egy logisztikai regressziós modellt kell kitölteni a feltöltött adatokon a távoli számítási fürt használatával. A feladatok elküldéséhez a következőket kell tennie:

* A betanítási szkript előkészítése
* Becslő létrehozása
* Feladat küldése

### <a name="prepare-the-training-script"></a>A betanítási szkript előkészítése
Egy `accidents.R` nevű betanítási szkriptet adott meg az oktatóanyagban található címtárban. Figyelje meg az Azure ML szolgáltatás betanításra való kihasználása érdekében az **oktatóanyagban** szereplő alábbi részleteket:

* A betanítási szkript argumentuma `-d`, hogy megkeresse a betanítási adatkészletet tartalmazó könyvtárat. Amikor később definiálja és elküldi a feladatot, erre az argumentumra az adattárra mutat. Az Azure ML a betanítási feladatokhoz csatlakoztatja a tárolási mappát a távoli fürthöz.
* A betanítási parancsfájl a végső pontosságot az Azure ML-ben lévő futtatási rekordhoz `log_metric_to_run()`használatával naplózza. Az Azure ML SDK számos naplózási API-készletet biztosít a különböző metrikák naplózásához a betanítási futtatások során. A rendszer rögzíti a metrikákat, és megőrzi a kísérlet futtatási rekordját. A metrikák ezután bármikor elérhetők, vagy megtekinthetők a [Azure Machine learning Studio](http://ml.azure.com)Futtatás részletei lapján. Tekintse meg a naplózási módszerek teljes készletére vonatkozó [referenciát](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) `log_*()`.
* A betanítási szkript egy **kimenet**nevű könyvtárba menti a modellt. A `./outputs` mappa speciális kezelést kap az Azure ML-vel. A betanítás során a rendszer a `./outputs`ba írt fájlokat automatikusan feltölti a futtatási rekordba az Azure ML-ben, és összetevőkként megőrzi őket. Ha a betanított modellt `./outputs`ra menti, akkor a Futtatás után is elérheti és lekérheti a modell fájlját, és már nem férhet hozzá a távoli képzési környezethez.

### <a name="create-an-estimator"></a>Becslő létrehozása

Az Azure ML-kalkulátor egy képzési parancsfájlnak a számítási célra való végrehajtásához szükséges futtatási konfigurációs adatokat ágyazza be. Az Azure ML-futtatások tároló-feladatokként futnak a megadott számítási célra. Alapértelmezés szerint a betanítási feladatokhoz készült Docker-rendszerkép tartalmazza az R-t, az Azure ML SDK-t és a gyakran használt R-csomagok készletét. Tekintse meg az itt található alapértelmezett csomagok teljes listáját.

A kalkulátor létrehozásához adja meg a következőt:

* A betanításhoz szükséges parancsfájlokat tartalmazó könyvtár (`source_directory`). A rendszer az ebben a könyvtárban lévő összes fájlt feltölti a fürt csomópontjaira. A címtárban szerepelnie kell a betanítási parancsfájlnak és a szükséges további parancsfájloknak.
* A végrehajtandó betanítási szkript (`entry_script`).
* A számítási cél (`compute_target`), ebben az esetben a korábban létrehozott AmlCompute-fürt.
* A betanítási parancsfájlból (`script_params`) szükséges paraméterek. Az Azure ML a betanítási szkriptet parancssori parancsfájlként futtatja `Rscript`. Ebben az oktatóanyagban egy argumentumot kell megadnia a parancsfájlhoz, az adatkönyvtár-csatlakoztatási ponthoz, amelyet a `ds$path(target_path)`hoz érhet el.
* A betanításhoz szükséges környezeti függőségek. A képzéshez készült alapértelmezett Docker-rendszerkép már tartalmazza a betanítási parancsfájlban szükséges három csomagot (`caret`, `e1071`és `optparse`).  Így nem kell további adatokat megadnia. Ha olyan R-csomagokat használ, amelyek nem szerepelnek alapértelmezés szerint, használja a kalkulátor `cran_packages` paraméterét további CRAN-csomagok hozzáadásához. Tekintse meg a konfigurálható beállítások teljes készletének [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) referenciáját.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>A feladatot a távoli fürtön küldje el

Végül küldje el a feladatot a fürtön való futtatáshoz. `submit_experiment()` egy futtatási objektumot ad vissza, amelyet aztán a futtatással való kapcsolódáshoz használ. Összességében az első futtatás **körülbelül 10 percet**vesz igénybe. A későbbi futtatások esetében azonban ugyanazt a Docker-rendszerképet használja a rendszer, amíg a parancsfájl függőségei nem változnak.  Ebben az esetben a rendszer gyorsítótárazza a rendszerképet, és a tároló indítási ideje sokkal gyorsabb.

```R
run <- submit_experiment(exp, est)
```

A Futtatás részleteit a RStudio megjelenítőben tekintheti meg. Ha a "Web View" hivatkozásra kattint, megjelenik a Azure Machine Learning Studio, ahol nyomon követheti a futtatást a felhasználói felületen.

```R
view_run_details(run)
```

A modell betanítása a háttérben történik. Várjon, amíg a modell befejezte a képzést, mielőtt további kódokat futtasson.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Ön – és a munkaterülethez hozzáféréssel rendelkező munkatársak – több kísérletet is küldhet párhuzamosan, és az Azure ML a számítási fürtön lévő feladatok ütemezését is elvégzi. Akár úgy is beállíthatja a fürtöt, hogy az automatikusan több csomópontra is felskálázást hajtson végre, és ha a várólista nem rendelkezik több számítási feladattal, akkor a méretezés vissza is állítható. Ez a konfiguráció költséghatékony módszer a csapatok számára a számítási erőforrások megosztására.

## <a name="retrieve-training-results"></a>Betanítási eredmények beolvasása
Miután a modell befejezte a képzést, elérheti a feladathoz tartozó, a futtatási rekordban megőrzött összetevőket, beleértve a naplózott metrikákat és a végső betanított modellt is.

### <a name="get-the-logged-metrics"></a>Naplózott mérőszámok beolvasása
A betanítási szkriptben `accidents.R`a modellből egy mérőszámot naplózott: a betanítási adatokban szereplő előrejelzések pontossága. A mérőszámokat megtekintheti a [Studióban](https://ml.azure.com), vagy kicsomagolhatja őket a helyi munkamenetbe R-listaként a következőképpen:

```R
metrics <- get_run_metrics(run)
metrics
```

Ha több kísérletet is futtat (azaz eltérő változókat, algoritmusokat vagy hyperparamers használ), az egyes futtatások metrikáit felhasználva összehasonlíthatja és kiválaszthatja az éles környezetben használni kívánt modellt.

### <a name="get-the-trained-model"></a>A betanított modell beszerzése
Lekérheti a betanított modellt, és megtekintheti az eredményeket a helyi R-munkamenetben. A következő kód letölti a `./outputs` könyvtár tartalmát, amely tartalmazza a modell fájlját is.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Bizonyos tényezőket láthat, amelyek hozzájárulnak a halálozás várható valószínűségének növeléséhez:

* nagyobb hatás sebessége 
* férfi illesztőprogram
* régebbi utas
* privát

A halálozások alacsonyabb valószínűségét láthatja a következővel:

* légzsák jelenléte
* jelenléti biztonsági övek
* frontális ütközés 

A gyártás jármű éve nem rendelkezik jelentős hatással.

A modell használatával új előrejelzéseket készíthet:

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Üzembe helyezés webszolgáltatásként

A modell segítségével előre megjósolhatja, hogy az ütközésből származó halál veszélye. Használja az Azure ML-t a modell előrejelzési szolgáltatásként való üzembe helyezéséhez. Ebben az oktatóanyagban üzembe helyezi a webszolgáltatást [Azure Container Instancesban](https://docs.microsoft.com/en-us/azure/container-instances/) (ACI).

### <a name="register-the-model"></a>A modell regisztrálása

Először regisztrálja a munkaterületre letöltött modellt [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html)használatával. A regisztrált modell lehet fájlok gyűjteménye, de ebben az esetben az R Model objektum elegendő. Az Azure ML a regisztrált modellt fogja használni az üzembe helyezéshez.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>A következtetési függőségek meghatározása
Ahhoz, hogy webszolgáltatást hozzon létre a modellhez, először létre kell hoznia egy pontozási szkriptet (`entry_script`), egy R-szkriptet, amely bemeneti változó értékként (JSON formátumban) fog megjelenni, és a modellből előrejelzést küld. Ebben az oktatóanyagban használja a megadott pontozási fájlt `accident_predict.R`. A pontozási parancsfájlnak tartalmaznia kell egy `init()` metódust, amely betölti a modellt, és egy olyan függvényt ad vissza, amely a modell használatával előrejelzést készít a bemeneti adatok alapján. További részletekért tekintse meg a [dokumentációt](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) .

Ezután adjon meg egy Azure ML- **környezetet** a parancsfájlhoz tartozó csomagok függőségeihez. A környezettel a szkript futtatásához szükséges R-csomagokat (a CRAN-ból vagy máshonnan) kell megadnia. Megadhatja azon környezeti változók értékeit is, amelyekkel a parancsfájl hivatkozhat a működésének módosítására. Az Azure ML alapértelmezésben ugyanazt az alapértelmezett Docker-rendszerképet hozza létre, amelyet a kalkulátor a képzéshez használ. Mivel az oktatóanyag nem rendelkezik speciális követelményekkel, hozzon létre egy olyan környezetet, amely nem tartalmaz speciális attribútumokat.

```R
r_env <- r_environment(name = "basic_env")
```

Ha ehelyett saját Docker-rendszerképet szeretne használni a központi telepítéshez, adja meg a `custom_docker_image` paramétert. Tekintse meg a környezet definiálására szolgáló konfigurálható lehetőségek teljes készletére [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) referenciát.

Most már mindent megtalál, amire szüksége lehet egy **következtetési konfiguráció** létrehozásához a pontozási parancsfájl és a környezeti függőségek beágyazásához.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Üzembe helyezés az ACI-ban
Ebben az oktatóanyagban üzembe helyezi a szolgáltatást az ACI-ban. Ez a kód egyetlen tárolót helyez üzembe a bejövő kérelmekre való reagáláshoz, amely tesztelési és könnyű betöltésre alkalmas. További konfigurálható beállítások: [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) . (Éles környezetben történő üzembe helyezéshez az [Azure Kubernetes szolgáltatásban is üzembe](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html)helyezhető.)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Most üzembe helyezi a modellt webszolgáltatásként. Az üzembe helyezés **több percet is igénybe**vehet. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>A telepített szolgáltatás tesztelése

Most, hogy a modellt szolgáltatásként telepítette, tesztelheti az R szolgáltatást az [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html)használatával.  Adjon meg egy új adatkészletet az előrejelzéshez, alakítsa át a JSON formátumba, és küldje el a szolgáltatásnak.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

A webszolgáltatás HTTP-végpontját is beolvashatja, amely fogadja a REST-ügyfelek hívásait. Ezt a végpontot bárki megoszthatja, aki szeretné tesztelni a webszolgáltatást, vagy integrálni azt egy alkalmazásba.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, törölje az erőforrásokat. Ne töröljön olyan erőforrást, amelyet még használni szeretne. 

Webszolgáltatás törlése:
```{r delete_service, eval=FALSE}
delete_webservice(aci_service)
```

A regisztrált modell törlése:
```{r delete_model, eval=FALSE}
delete_model(model)
```

A számítási fürt törlése:
```{r delete_compute, eval=FALSE}
delete_compute(compute)
```

### <a name="delete-everything"></a>Mindent törölni

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Most, hogy elvégezte az első Azure Machine Learning-kísérletet az R-ben, ismerkedjen meg az [r Azure Machine learning SDK](https://azure.github.io/azureml-sdk-for-r/index.html)-val.

