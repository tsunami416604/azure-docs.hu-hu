---
title: 'Oktatóanyag: Gépi tanulási modell létrehozásához használja az R-t'
titleSuffix: Azure Machine Learning
description: Ebben az oktatóanyagban az Azure Machine Learning R SDK használatával hozzon létre egy logisztikai regressziós modellt, amely előre jelzi egy autóbalesetben a halálos kimenetelű baleset valószínűségét.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 72488ba339399c526e882ffd11c41410a0b011ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80159088"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model"></a>Oktatóanyag: Gépi tanulási modell létrehozásához használja az R-t
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben az oktatóanyagban az Azure Machine Learning R SDK használatával hozzon létre egy logisztikai regressziós modellt, amely előre jelzi egy autóbalesetben a halálos kimenetelű baleset valószínűségét. Látni fogja, hogy az Azure Machine Learning felhőbeli erőforrások r-rel együttműködve méretezhető környezetet biztosít a modell betanításához és üzembe helyezéséhez.  

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:
> [!div class="checklist"]
> * Azure Machine Learning-munkaterület létrehozása
> * Jegyzetfüzetmappa klónozása az oktatóanyag munkaterületre való futtatásához szükséges fájlokkal
> * Nyissa meg az RStudio-t a munkaterületről
> * Adatok betöltése és felkészülés a betanításra
> * Adatok feltöltése egy adattárba, hogy azok elérhetők legyenek a távoli betanításhoz
> * Számítási erőforrás létrehozása a modell távoli betanításához
> * Modell `caret` betanítása a halálos kimenetelű balesetek valószínűségének előrejelzéséhez
> * Előrejelzési végpont telepítése
> * Tesztelje a modellt R-ből

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)


## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning-munkaterület egy alapvető erőforrás a felhőben, amely et a kísérletezés, a betanítás és a gépi tanulási modellek üzembe helyezése. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz köti a szolgáltatásban. 

Hozzon létre egy munkaterületet az Azure Portalon keresztül, egy webalapú konzol az Azure-erőforrások kezelésére. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Vegye figyelembe **munkaterületét** és **előfizetését.** Ezekre szüksége lesz, hogy a kísérletet a megfelelő helyen hozza létre. 


## <a name="clone-a-notebook-folder"></a><a name="azure"></a>Jegyzetfüzetmappa klónozása

Ez a példa a felhőalapú jegyzetfüzet-kiszolgálót használja a munkaterületen egy telepítés nélküli és előre konfigurált élményhez. Használja [a saját környezetét,](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) ha szeretné, hogy a környezet, a csomagok és a függőségek felett.

A következő kísérletbeállítási és -futtatási lépéseket az Azure Machine Learning-stúdióban, egy összevont felületen futtathatja, amely gépi tanulási eszközöket tartalmaz az adatelemzési forgatókönyvek végrehajtásához az adatelemzési szakemberek számára, minden képzettségi szinten.

1. Jelentkezzen be az [Azure Machine Learning stúdióba.](https://ml.azure.com/)

1. Válassza ki az előfizetést és a létrehozott munkaterületet.

1. Válassza a bal oldalon a **Jegyzetfüzetek** lehetőséget.

1. Nyissa **meg** a Minták mappát.

1. Nyissa meg az **R** mappát.

1. Nyissa meg azt a mappát, amelyen verziószám van.  Ez a szám az R SDK aktuális kiadását jelöli.

1. Válassza ki a **vignettálási** mappa jobb oldalán található **"..."** elemet, majd válassza a **Klónozás**lehetőséget.

    ![Klónozott mappa](media/tutorial-1st-r-experiment/clone-folder.png)

1. A munkaterületet elérő felhasználók listáját jeleníti meg a mappák listája.  Jelölje ki a mappát a **vignettamappa klónozásához.**

## <a name="a-nameopenopen-rstudio"></a><a name="open">Tárja meg az RStudio-t

Használja az RStudio egy számítási példány vagy notebook virtuális gép az oktatóanyag futtatásához.  

1. Válassza a bal oldalon a **Számítási** lehetőséget.

1. Számítási erőforrás hozzáadása, ha még nem létezik.

1. Miután a számítás fut, használja az **RStudio** linket az RStudio megnyitásához.

1. Az RStudio-ban a *vignettálási* mappa néhány szinttel lejjebb van a jobb alsó sarokban található **Fájlok** szakasz *Ban.*  A *matricák*alatt válassza ki a *betanítási és üzembe helyezési* mappát az oktatóanyagban szükséges fájlok megkereséséhez.

> [!Important]
> A cikk többi része ugyanazt a tartalmat tartalmazza, mint a *vonat-és-deploy-to-aci. Rmd* fájl. Ha tapasztalt RMarkdown, nyugodtan használja a kódot, hogy a fájlt.  Vagy másolhatja/beillesztheti a kódrészleteket onnan, vagy ebből a cikkből egy R parancsfájlba vagy a parancssorba.  


## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása
Az oktatóanyagban a fejlesztési munka beállítása a következő műveleteket tartalmazza:

* Szükséges csomagok telepítése
* Csatlakozás munkaterülethez, hogy a számítási példány kommunikáljon a távoli erőforrásokkal
* Hozzon létre egy kísérletet, hogy nyomon kövesse a fut
* Távoli számítási cél létrehozása a betanításhoz

### <a name="install-required-packages"></a>Szükséges csomagok telepítése
Ez az oktatóanyag feltételezi, hogy már telepítve van az Azure ML SDK. Folytassa az **azuremlsdk** csomag importálásával.

```R
library(azuremlsdk)
```

A betanítási és`accidents.R` pontozási `accident_predict.R`parancsfájlok ( és ) néhány további függőségek. Ha azt tervezi, hogy ezeket a parancsfájlokat helyileg futtatja, győződjön meg arról, hogy rendelkezik a szükséges csomagokkal is.

### <a name="load-your-workspace"></a>A munkaterület betöltése
Munkaterület-objektum példányosítson a meglévő munkaterületről. A következő kód betölti a munkaterület részleteit a **config.json** fájlból. Munkaterületet a használatával [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html)is bekérhet.

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása
Egy Azure ML-kísérlet nyomon követi a futtatások csoportosítását, általában ugyanabból a betanítási parancsfájlból. Hozzon létre egy kísérletet a futtatások nyomon követéséhez a caret modell betanítása a baleseti adatok.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Számítási cél létrehozása
Az Azure Machine Learning Compute (AmlCompute) felügyelt szolgáltatás használatával az adatszakértők betaníthatják a gépi tanulási modelleket az Azure virtuális gépek fürtjein. Ilyenek például a GPU-támogatással rendelkező virtuális gépek. Ebben az oktatóanyagban egy egycsomópontos AmlCompute-fürtöt hoz létre betanítási környezetként. Az alábbi kód létrehozza a számítási fürtöt, ha még nem létezik a munkaterületen.

Előfordulhat, hogy várnia kell néhány percet a számítási fürt kiépítése, ha még nem létezik.

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

## <a name="prepare-data-for-training"></a>Adatok előkészítése a betanításhoz
Ez az oktatóanyag az Egyesült Államok [Nemzeti Közúti Közlekedésbiztonsági Hivatalának](https://cdan.nhtsa.gov/tsftables/tsfar.htm) adatait használja [(Mary C. Meyernek és Tremika Finney-nek](https://www.stat.colostate.edu/~meyer/airbags.htm)köszönhetően).
Ez az adatkészlet több mint 25 000 egyesült államokbeli autóbaleset adatait tartalmazza, és változókkal előre jelezheti a halálos kimenetelű balesetek valószínűségét. Először importálja az adatokat az R-be, és alakítsa át őket egy új adatkeretté `accidents` elemzésre, és exportálja őket egy `Rdata` fájlba.

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Adatok feltöltése az adattárba
Töltsön fel adatokat a felhőbe, hogy azok a távoli betanítási környezetben is elérhetők legyenek. Minden Egyes Azure Machine Learning-munkaterület egy alapértelmezett adattárral érkezik, amely tárolja a kapcsolatadatait az Azure blob tárolóhoz, amely a munkaterülethez csatlakoztatott tárfiókban van kiépítve. A következő kód feltölti a fent létrehozott baleseti adatokat az adott adattárba.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Modell betanítása

Ebben az oktatóanyagban helyezzen el egy logisztikai regressziós modellt a távoli számítási fürt használatával feltöltött adatokra. Egy állás elküldéséhez a következőket kell tenni:

* Az edzésszkript előkészítése
* Becslő létrehozása
* Feladat küldése

### <a name="prepare-the-training-script"></a>Az edzésszkript előkészítése
A betanítási szkript hívott `accidents.R` már biztosított az Ön számára ugyanabban a könyvtárban, mint ez az oktatóanyag. Figyelje meg az alábbi részleteket **a betanítási parancsfájlon belül,** amelyek az Azure Machine Learning betanítási célokra való kihasználására lettek lekényszerítve:

* A betanítási `-d` parancsfájl egy argumentumot vesz igénybe a betanítási adatokat tartalmazó könyvtár megkereséséhez. Amikor később definiálja és elküldi a feladatot, az argumentum adattárára mutat. Az Azure ML csatlakoztatja a tárolómappát a távoli fürthöz a betanítási feladathoz.
* A betanítási parancsfájl naplózza a végső pontosságot, mint `log_metric_to_run()`egy metrika a futtatási rekord az Azure ML használatával. Az Azure ML SDK naplózási API-k készletét biztosítja a különböző metrikák naplózásához a betanítási futtatások során. Ezek a mérőszámok rögzítésre kerülnek, és megmaradnak a kísérlet futtatási rekordjában. A mérőszámok ezután bármikor elérhetők, vagy megtekinthetők a futtatás részletei oldalon a [stúdióban.](https://ml.azure.com) Lásd a [naplózási](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) módszerek `log_*()`teljes készletét.
* A betanítási parancsfájl a modellt egy **kimenetnevű**könyvtárba menti. A `./outputs` mappa az Azure ML különleges elbánásban részesül. A betanítás során `./outputs` a rendszer automatikusan feltölti a futtatási rekordba az Azure ML által írt fájlokat, és összetevőkként marad meg. A betanított modell `./outputs`mentésével a programa rendszerbe a futtatás után is elérheti és lekérheti a modellfájlt, és már nem fér hozzá a távoli betanítási környezethez.

### <a name="create-an-estimator"></a>Becslő létrehozása

Az Azure ML-becslő beágyazza a számítási cél betanítási parancsfájljának végrehajtásához szükséges futtatási konfigurációs adatokat. Az Azure ML-futtatások a megadott számítási célon tárolóba kötött feladatokként futnak. Alapértelmezés szerint a betanítási feladathoz készített Docker-rendszerkép tartalmazza az R, az Azure ML SDK és a gyakran használt R-csomagok készletét. Tekintse meg az itt található alapértelmezett csomagok teljes listáját.

A becslés létrehozásához adja meg a következőket:

* A betanításhoz szükséges parancsfájlokat`source_directory`tartalmazó könyvtár ( ). A könyvtárban lévő összes fájl feltöltésre kerülnek a fürtcsomópont(ok)ba végrehajtásra. A könyvtárnak tartalmaznia kell a betanítási parancsfájlt és a szükséges további parancsfájlokat.
* A végrehajtandó betanítási`entry_script`parancsfájl ( ).
* A számítási cél`compute_target`( ), ebben az esetben a korábban létrehozott AmlCompute fürt.
* A betanítási parancsfájlból`script_params`( ). Az Azure ML parancssori parancsfájlként futtatja a betanítási parancsfájlt a. `Rscript` Ebben az oktatóanyagban egy argumentumot ad meg a parancsfájlhoz, az adatkönyvtár csatlakoztatási pontjához, amelyet a segítségével `ds$path(target_path)`érhet el.
* A képzéshez szükséges környezeti függőségek. A betanításhoz készített alapértelmezett Docker-rendszerkép`caret` `e1071`már `optparse`tartalmazza a betanítási parancsfájlban szükséges három csomagot ( , és ).  Így nem kell további információkat megadnia. Ha olyan R csomagokat használ, amelyek alapértelmezés szerint nem `cran_packages` szerepelnek benne, a becslő paraméterével adjon hozzá további CRAN-csomagokat. A [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) konfigurálható beállítások teljes készletét lásd a hivatkozásban.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>A feladat elküldése a távoli fürtön

Végül küldje el a feladatot a fürtön való futtatáshoz. `submit_experiment()`egy Futtatás objektumot ad vissza, amelyet ezután a futtatással való kapcsolódáshoz használ. Összességében az első futás **körülbelül 10 percet**vesz igénybe. De a későbbi futtatások, ugyanazt a Docker-rendszerkép újra felhasználható, amíg a parancsfájl-függőségek nem változnak.  Ebben az esetben a rendszerkép gyorsítótárazott, és a tároló indítási ideje sokkal gyorsabb.

```R
run <- submit_experiment(exp, est)
```

Megtekintheti a futtatás részleteit az RStudio Viewer alkalmazásban. A "Web View" hivatkozásra kattintva az Azure Machine Learning stúdióba kerül, ahol figyelheti a futtatást a felhasználói felületen.

```R
view_run_details(run)
```

Modell képzés történik a háttérben. Várjon, amíg a modell befejezi a betanítást, mielőtt további kódot futtatna.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Ön – és a munkaterülethez hozzáféréssel rendelkező munkatársak – párhuzamosan több kísérletet is beküldhetnek, és az Azure ML a számítási fürt feladatainak ütemezését veszi igénybe. Akár azt is beállíthatja, hogy a fürt automatikusan több csomópontra skálázható, és akkor is méretezhető, ha nincs több számítási feladat a várólistában. Ez a konfiguráció költséghatékony módja annak, hogy a csapatok megosszák a számítási erőforrásokat.

## <a name="retrieve-training-results"></a>Betanítási eredmények beolvasása
Miután a modell befejezte a betanítást, hozzáférhet a feladat, amely a futtatási rekord, beleértve a naplózott metrikák és a végleges betanított modell összetevői.

### <a name="get-the-logged-metrics"></a>A naplózott metrikák begyűjtése
A betanítási parancsfájlban `accidents.R`naplózott egy metrikát a modellből: az előrejelzések pontosságát a betanítási adatokban. A metrikákat a [stúdióban](https://ml.azure.com)láthatja, vagy r-listaként kinyerheti őket a helyi munkamenetbe az alábbiak szerint:

```R
metrics <- get_run_metrics(run)
metrics
```

Ha több kísérletet futtatott (például különböző változókat, algoritmusokat vagy hiperparamereket használva), az egyes futtatások metrikáival összehasonlíthatja és kiválaszthatja az éles környezetben használni kívánt modellt.

### <a name="get-the-trained-model"></a>A betanított modell beszerezni
A betanított modell beolvasása, és tekintse meg az eredményeket a helyi R-munkamenetben. A következő kód letölti `./outputs` a könyvtár tartalmát, amely tartalmazza a modellfájlt.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Látsz néhány tényezőt, amelyek hozzájárulnak a becsült halálvalószínűség növekedéséhez:

* nagyobb ütközési sebesség 
* férfi vezető
* idősebb utas
* Utas

Alacsonyabb a halál valószínűsége a következőkkel:

* légzsákok jelenléte
* jelenlét biztonsági övek
* frontális ütközés 

A gyártás járműévének nincs jelentős hatása.

Ezzel a modellel új előrejelzéseket készíthet:

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

A modell, meg tudja jósolni a halál veszélyét egy ütközés. Az Azure ML használatával a modell t előrejelzési szolgáltatásként való üzembe helyezéséhez. Ebben az oktatóanyagban a webszolgáltatás az [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI) üzembe helyezése.

### <a name="register-the-model"></a>Regisztrálja a modellt

Először regisztrálja a munkaterületre letöltött [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html)modellt a segítségével. A regisztrált modell bármilyen fájlgyűjtemény lehet, de ebben az esetben az R modell objektum elegendő. Az Azure ML a regisztrált modellt fogja használni a központi telepítéshez.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>A következtetési függőségek meghatározása
Webszolgáltatás létrehozásához a modell, először létre kell hoznia egy pontozási parancsfájl (`entry_script`), egy R-parancsfájlt, amely bemeneti változó értékek (JSON formátumban) és a kimeneti előrejelzést a modellből. Ebben az oktatóanyagban használja `accident_predict.R`a megadott pontozási fájlt. A pontozási parancsfájlnak `init()` tartalmaznia kell egy metódust, amely betölti a modellt, és egy függvényt ad vissza, amely a modellt használja a bemeneti adatok on alapuló előrejelzéshez. További részleteket a [dokumentációban](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) talál.

Ezután definiáljon egy Azure **ML-környezetet** a parancsfájl csomagfüggőségeihez. Környezetben megadhatja az R-csomagokat (cran-ból vagy máshonnan), amelyek szükségesek a parancsfájl futtatásához. A környezeti változók értékeit is megadhatja, amelyekre a parancsfájl hivatkozhat a viselkedésének módosításához. Alapértelmezés szerint az Azure ML ugyanazt az alapértelmezett Docker-lemezképet hozza létre, amelyet a betanítási estimator használ. Mivel a bemutató nem rendelkezik különleges követelményekkel, hozzon létre egy olyan környezetet, amely nem rendelkezik speciális attribútumokkal.

```R
r_env <- r_environment(name = "basic_env")
```

Ha azt szeretné, hogy a saját Docker-rendszerképet a központi telepítés helyett, adja meg a `custom_docker_image` paramétert. [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) A környezet definiálására szolgáló konfigurálható beállítások teljes készletét lásd.

Most már mindent meg kell hoznia egy **következtetés config** a pontozási parancsfájl és a környezeti függőségek beágyazása.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Üzembe helyezés az ACI-ban
Ebben az oktatóanyagban a szolgáltatást az ACI-nak fogja telepíteni. Ez a kód egyetlen tárolót tartalmaz a bejövő kérések megválaszolására, amely alkalmas a tesztelésre és a könnyű terhelésekre. További [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) konfigurálható lehetőségek. (Éles környezetben történő telepítés esetén [az Azure Kubernetes-szolgáltatásban](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html)is üzembe helyezheti.)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Most már webszolgáltatásként telepíti a modellt. A telepítés **több percet is igénybe vehet.** 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Az üzembe helyezett szolgáltatás tesztelése

Most, hogy a modell szolgáltatásként van telepítve, tesztelheti [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html)a szolgáltatást az R használatával.  Adjon meg egy új adathalmazt, amelyből előre jelezheti, átalakíthatja JSON-ra, és elküldheti a szolgáltatásnak.

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

A webszolgáltatás HTTP-végpontját is lejuthat, amely fogadja a REST-ügyfélhívásokat. Ezt a végpontot megoszthatja bárkivel, aki tesztelni szeretné a webszolgáltatást, vagy integrálni szeretné egy alkalmazásba.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje az erőforrásokat, ha már nincs rájuk szüksége. Ne törölje azokat az erőforrásokat, amelyeket továbbra is használni szeretne. 

A webszolgáltatás törlése:
```R
delete_webservice(aci_service)
```

Törölje a regisztrált modellt:
```R
delete_model(model)
```

A számítási fürt törlése:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Mindent töröljön

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

* Most, hogy befejezte az első Azure Machine Learning-kísérletet az R-ben, tudjon meg többet az [Azure Machine Learning R-hez.](https://azure.github.io/azureml-sdk-for-r/index.html)

* További információ az R-rel rendelkező Azure Machine Learningről a többi *vignettálási* mappában található példákból.
