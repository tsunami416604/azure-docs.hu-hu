---
title: DevOps adatfeldolgozási folyamathoz
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan alkalmazhatja a DevOps-eljárásokat egy adatfeldolgozási folyamat megvalósítására, amely a modell betanításához szükséges adatelőkészítéshez használatos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.custom: tracking-python
ms.openlocfilehash: 864c7f2fd16a935d1df740b7d64d068bf7214d18
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84552290"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps adatfeldolgozási folyamathoz

A legtöbb esetben az adatfeldolgozási megoldás a parancsfájlok, a szolgáltatások meghívásai és az összes tevékenységet összehangoló folyamat. Ebből a cikkből megtudhatja, hogyan alkalmazhat DevOps-eljárásokat a közös adatfeldolgozási folyamat fejlesztési életciklusára. A folyamat felkészíti a Machine Learning modell betanításához szükséges adattípust.

## <a name="the-solution"></a>A megoldás

Vegye figyelembe a következő adatfeldolgozási munkafolyamatot:

![adatfeldolgozás – folyamat](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

Ebben a megközelítésben a betanítási adatgyűjtést egy Azure Blob Storage tárolja. Egy Azure Data Factory folyamat beolvassa az adatokat egy bemeneti blob-tárolóból, átalakítja azt, és menti az adatokat a kimeneti blob-tárolóba. Ez a tároló [adattárolóként](concept-data.md) szolgál a Azure Machine learning szolgáltatás számára. Az adatfeldolgozást követően a Data Factory folyamat meghívja a képzési Machine Learning folyamatot a modell betanításához. Ebben az adott példában az adatátalakítást egy Azure Databricks-fürtön futó Python-jegyzetfüzet hajtja végre. 

## <a name="what-we-are-building"></a>Mi építünk

Csakúgy, mint bármilyen szoftveres megoldás esetében, van egy csapat (például az adatmérnökök). 

![vel – adatfeldolgozás](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Közösen használják az Azure-erőforrásokat, például a Azure Data Factory, Azure Databricks, az Azure Storage-fiókot és ilyeneket. Ezen erőforrások gyűjteménye fejlesztési környezet. Az adatmérnökök ugyanahhoz a forráskód-alaphoz járulnak hozzá. A folyamatos integrációs folyamat összeállítja a kódot, ellenőrzi a kód minőségi tesztelését, az egység teszteket, és olyan összetevőket állít elő, mint például a tesztelt kód és a Azure Resource Manager sablonok. A folyamatos kézbesítési folyamat az összetevőket az alsóbb rétegbeli környezetekben telepíti. Ez a cikk bemutatja, hogyan automatizálható a CI-és CD-folyamatok az [Azure](https://azure.microsoft.com/services/devops/pipelines/)-folyamatokkal.

## <a name="source-control-management"></a>Verziókövetés kezelése

A csapattagok a Python notebook forráskódján és a Azure Data Factory forráskódon való együttműködéshez némileg eltérő módon működnek. Mindkét esetben azonban a kód egy verziókövetés adattárában (például az Azure DevOps, a GitHubon, a GitLab) tárolódik, és az együttműködés általában valamilyen elágazó modellen alapul (például [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)).

### <a name="python-notebook-source-code"></a>Python notebook-forráskód

Az adatmérnökök a Python notebook forráskódját helyileg, egy IDE (például a [Visual Studio Code](https://code.visualstudio.com)) vagy közvetlenül a Databricks munkaterületen működnek. Az utóbbi lehetővé teszi a kód hibakeresését a fejlesztői környezetben. Minden esetben a rendszer egyesíti a kódot a tárházba az elágazási szabályzat után. Javasoljuk, hogy a kódot `.py` `.ipynb` Jupyter jegyzetfüzet formátuma helyett fájlokban tárolja. Javítja a kód olvashatóságát, és lehetővé teszi az automatikus kódok minőségének ellenőrzését a CI-folyamat során.

### <a name="azure-data-factory-source-code"></a>Forráskód Azure Data Factory

Azure Data Factory folyamatok forráskódja a munkaterület által generált JSON-fájlok gyűjteménye. Az adatmérnökök általában a Azure Data Factory munkaterületen lévő vizuális tervezővel működnek, nem pedig közvetlenül a forráskód fájljaival. Konfigurálja a munkaterületet egy forrásoldali vezérlő adattárral, ahogy azt a [Azure Data Factory dokumentációja](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration)tartalmazza. Ebben a konfigurációban az adatmérnökök az előnyben részesített elágazó munkafolyamatot követve képesek együttműködni a forráskódban.    

## <a name="continuous-integration-ci"></a>Folyamatos integráció (CI)

A folyamatos integrációs folyamat végső célja, hogy összegyűjtse a közös csapat munkáját a forráskódból, és előkészítse az üzembe helyezést az alsóbb rétegbeli környezetekben. A forráskód-felügyelethez hasonlóan ez a folyamat különbözik a Python-jegyzetfüzetek és a Azure Data Factory folyamatok esetében. 

### <a name="python-notebook-ci"></a>Python notebook CI

A Python-jegyzetfüzetek CI-folyamata beolvassa az együttműködési ág kódját (például ***Master*** vagy ***fejlesztése***), és a következő tevékenységeket végzi el:
* Kód
* Egységtesztelés
* A kód mentése összetevőként

A következő kódrészlet bemutatja, hogyan hajthatja végre ezeket a lépéseket egy Azure DevOps ***YAML*** -folyamatban:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

A folyamat a ***flake8*** használatával hajtja végre a Python-kód felépítését. Futtatja a forráskódban meghatározott mértékegység-teszteket, és közzéteszi a kiértékelési és tesztelési eredményeket, hogy elérhetők legyenek az Azure-folyamat végrehajtási képernyőjén:

![elszámolási egység – tesztek](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Ha a kivezetés és az egység tesztelése sikeres, a folyamat a forráskódot átmásolja az összetevő-tárházba, amelyet a következő telepítési lépések használni fognak.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Az Azure Data Factory folyamat CI-folyamata egy adatfeldolgozási folyamat teljes CI/CD-történetének szűk keresztmetszete. Nincs ***folyamatos*** integráció. A Azure Data Factory telepíthető összetevője Azure Resource Manager-sablonok gyűjteménye. A sablonok előállításának egyetlen módja, ha a Azure Data Factory munkaterület ***Közzététel*** gombjára kattint. Itt nincs automatizálás.
Az adatmérnökök egyesítik a forráskódot a szolgáltatási ágakból az együttműködési ágban, például a ***Master*** vagy a ***fejlesztés***során. Ezt követően a megadott engedélyekkel rendelkező valaki a ***Közzététel*** gombra kattintva hozza Azure Resource Manager sablonokat az együttműködési ág forráskódjában. Ha a gombra kattint, a munkaterület érvényesíti a folyamatokat (úgy gondolja, hogy a kihelyezés és az egység tesztelése miatt), Azure Resource Manager sablonokat hoz létre (például az építésből), és a létrehozott sablonokat egy technikai ***adf_publish*** ág számára menti, amely ugyanabban a tárban található (a közzétételi összetevőktől kezdve). Ezt az ágat a Azure Data Factory munkaterület automatikusan létrehozta. Ezt a folyamatot a [Azure Data Factory dokumentációjának](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)részletek című részében találja.

Fontos annak biztosítása, hogy a generált Azure Resource Manager sablonok a környezet agnosztikusok legyenek. Ez azt jelenti, hogy az összes olyan érték, amely különbözhet a környezetek között, parametrized. A Azure Data Factory elég okos ahhoz, hogy az értékek többségét elérhetővé tegye a paraméterként. A következő sablonban például a Azure Machine Learning munkaterület kapcsolódási tulajdonságai paraméterekként vannak kitéve:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Azonban előfordulhat, hogy a Azure Data Factory munkaterület által nem kezelt egyéni tulajdonságokat szeretné kitenni. Ebben a cikkben a Azure Data Factory folyamat egy Python-jegyzetfüzetet hív meg az adatfeldolgozás során. A jegyzetfüzet egy bemeneti adatfájl nevét tartalmazó paramétert fogad el.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Ez a név különbözik a ***fejlesztési***, a ***QA***-, a ***ellenőrzését***-és a ***Prod*** -környezetekben. A több tevékenységgel rendelkező összetett folyamatokban több egyéni tulajdonság is lehet. Célszerű az összes értéket összegyűjteni egy helyen, és a folyamat ***változóként***definiálni őket:

![ADF – változók](media/how-to-cicd-data-ingestion/adf-variables.png)

A folyamat tevékenységei a folyamat változóit a tényleges használat közben is hivatkozhatják:

![ADF-jegyzetfüzet – paraméterek](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

A Azure Data Factory munkaterület alapértelmezés szerint ***nem*** tesz elérhetővé Azure Resource Manager sablon paraméterként a folyamat változóit. A munkaterület az [alapértelmezett paraméterezés-sablont](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) használja, amely azt diktálja, hogy a folyamat mely tulajdonságai legyenek elérhetők Azure Resource Manager sablon paramétereinek. A folyamat változóinak a listához való hozzáadásához frissítse az [alapértelmezett paraméterezés sablon](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) "Microsoft. DataFactory/gyárak/folyamatok" szakaszát az alábbi kódrészlettel, és helyezze el az eredmény JSON-fájlját a forrás mappa gyökerébe:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Ennek hatására a Azure Data Factory munkaterület felveszi a változókat a paraméterek listára, amikor a ***Közzététel*** gombra kattint:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

A JSON-fájl értékei a folyamat definíciójában megadott alapértelmezett értékek. A rendszer a Azure Resource Manager sablon központi telepítésekor várhatóan felülbírálja a célként megadott környezeti értékeket.

## <a name="continuous-delivery-cd"></a>Folyamatos teljesítés (CD)

A folyamatos kézbesítési folyamat az összetevőket veszi át, és telepíti őket az első célként megadott környezetbe. Gondoskodik arról, hogy a megoldás a tesztek futtatásával működjön. Ha a művelet sikeres, az továbbra is a következő környezetbe kerül. A CD Azure-folyamat a környezeteket képviselő több szakaszból áll. Mindegyik szakasz a következő lépéseket végrehajtó [központi telepítéseket](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) és [feladatokat](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) tartalmazza:
* Python-jegyzetfüzet üzembe helyezése Azure Databricks munkaterületen
* Azure Data Factory folyamat üzembe helyezése 
* A folyamat futtatása
* Az adatfeldolgozás eredményének ellenőrzéséhez

A folyamat fázisai olyan [jóváhagyásokkal](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) és [kapukkal](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) konfigurálhatók, amelyek további szabályozást biztosítanak a telepítési folyamatnak a környezetek láncán való kialakulásához.

### <a name="deploy-a-python-notebook"></a>Python-jegyzetfüzet üzembe helyezése

Az alábbi kódrészlet egy olyan Azure-alapú folyamat- [telepítést](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) határoz meg, amely egy Python-jegyzetfüzetet másol egy Databricks-fürtre:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

A CI által előállított összetevők automatikusan átkerülnek a központi telepítési ügynökre, és a ***$ (pipeline. Workspace)*** mappában lesznek elérhetők. Ebben az esetben a telepítési feladat a Python-jegyzetfüzetet tartalmazó ***di-Notebooks*** összetevőre hivatkozik. Ez [az](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) üzemelő példány a [Databricks Azure DevOps bővítmény](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) használatával másolja a notebook-fájlokat a Databricks-munkaterületre.
Az ***Deploy_to_QA*** szakasz az Azure devops projektben definiált ***devops-DS-QA-VG*** változóra mutató hivatkozást tartalmaz. Az ebben a szakaszban szereplő lépések a változócsoport változóit jelentik (például $ (DATABRICKS_URL), $ (DATABRICKS_TOKEN)). Az elképzelés az, hogy a következő szakasz (például ***Deploy_to_UAT***) ugyanazokkal a változókkal fog működni, mint a saját ellenőrzését-hatókörű változó csoportjában.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Azure Data Factory folyamat üzembe helyezése

A Azure Data Factory telepíthető összetevője Azure Resource Manager sablon. Ezért az ***Azure erőforráscsoport-telepítési*** feladattal lesz üzembe helyezve, mivel az a következő kódrészletben látható:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
A Value filename paraméter értéke a QA Stage változóban definiált $ (DATA_FILE_NAME) változóból származik. Hasonlóképpen, a ***ARMTemplateForFactory. JSON*** fájlban definiált összes paraméter felülbírálható. Ha nem, akkor a rendszer az alapértelmezett értékeket használja.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>A folyamat futtatása és az adatfeldolgozás eredményének ellenőrzéséhez

A következő lépés annak ellenőrzése, hogy a telepített megoldás működik-e. A következő feladatdefiníció egy [PowerShell-parancsfájlt](https://github.com/microsoft/DataOps/tree/master/adf/utils) futtató Azure Data Factory folyamatot futtat, és egy Python-jegyzetfüzetet hajt végre egy Azure Databricks-fürtön. A jegyzetfüzet ellenőrzi, hogy az adat megfelelően lett-e betöltve, és érvényesíti a (z) $ (bin_FILE_NAME) nevű eredmény-adatfájlt.

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

A feladat utolsó feladata ellenőrzi a jegyzetfüzet végrehajtásának eredményét. Ha hibát ad vissza, akkor a folyamat végrehajtásának állapotát nem sikerült értékre állítja.

## <a name="putting-pieces-together"></a>Összeállítás

Ennek a cikknek az eredménye a CI/CD Azure-folyamat, amely a következő szakaszokból áll:
* CI
* Üzembe helyezés a QA-ben
    * Üzembe helyezés a Databricks és az ADF üzembe helyezése
    * Integrációs teszt

Számos ***üzembe helyezési*** fázist tartalmaz, amely megegyezik a megcélzott környezetek számával. Mindegyik ***üzembe helyezési*** szakasz két olyan [központi telepítést](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) tartalmaz, amelyek párhuzamosan futnak, valamint egy olyan [feladatot](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) , amely az üzembe helyezést követően fut a megoldás teszteléséhez a környezetben.

A folyamat egy minta implementációját a következő ***YAML*** -kódrészletben kell összeállítani:

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Következő lépések

* [Verziókövetés az Azure Data Factoryben](https://docs.microsoft.com/azure/data-factory/source-control)
* [Folyamatos integráció és kézbesítés Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [Azure Databricks DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
