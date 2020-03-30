---
title: DevOps adatbetöltési folyamathoz
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan alkalmazhatdevops-eljárásokat egy olyan adatbetöltési folyamat implementációján, amely az adatok előkészítéséhez szolgál egy modellbetanításhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247180"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps adatbetöltési folyamathoz

A legtöbb esetben az adatbetöltési megoldás parancsfájlok, szolgáltatás-meghívások és az összes tevékenységet vezénylő folyamat kompozíciója. Ebben a cikkben megtudhatja, hogyan alkalmazhatja a DevOps-eljárásokat egy közös adatbetöltési folyamat fejlesztési életciklusára. A folyamat előkészíti az adatokat a Machine Learning modell betanításához.

## <a name="the-solution"></a>A megoldás

Vegye figyelembe a következő adatbetöltési munkafolyamatot:

![adatbetöltési folyamat](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

Ebben a megközelítésben a betanítási adatok egy Azure blob storage tárolja. Az Azure Data Factory-folyamat lekéri az adatokat egy bemeneti blob tárolóból, átalakítja, és menti az adatokat a kimeneti blob tárolóba. Ez a tároló az Azure Machine Learning szolgáltatás [adattárolójaként](concept-data.md) szolgál. Az adatok előkészítése, a Data Factory-folyamat egy betanítási Machine Learning-folyamat egy modell betanításához. Ebben a konkrét példában az adatátalakítást egy Python-jegyzetfüzet hajtja végre, amely egy Azure Databricks-fürtön fut. 

## <a name="what-we-are-building"></a>Amit építünk

Mint minden szoftvermegoldás, van egy csapat (például adatmérnökök) dolgozik rajta. 

![cicd-adatbetöltés](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Együttműködnek és ugyanazt az Azure-erőforrást osztják meg, például az Azure Data Factoryt, az Azure Databricks-t, az Azure Storage-fiókot és hasonlókat. Ezek az erőforrások gyűjteménye egy fejlesztési környezet. Az adatmérnökök ugyanahhoz a forráskód-bázishoz járulnak hozzá. A folyamatos integrációs folyamat összeállítja a kódot, ellenőrzi azt a kód minőségtesztekkel, egységtesztekkel, és összetevőket, például tesztelt kódot és Azure Resource Manager-sablonokat hoz létre. A folyamatos kézbesítési folyamat telepíti a műtermékek az alsóbb rétegeket. Ez a cikk bemutatja, hogyan automatizálhatja a CI és cd folyamatok [at Azure Pipelines.](https://azure.microsoft.com/services/devops/pipelines/)

## <a name="source-control-management"></a>Forrásellenőrzés kezelése

A csapattagok némileg eltérő módon dolgoznak a Python-jegyzetfüzet forráskódján és az Azure Data Factory forráskódján való együttműködésben. Azonban mindkét esetben a kód egy forrásvezérlő tárházban (például Azure DevOps, GitHub, GitLab) tárolódik, és az együttműködés általában valamilyen elágazási modellen alapul (például [GitFlow).](https://datasift.github.io/gitflow/IntroducingGitFlow.html)

### <a name="python-notebook-source-code"></a>Python-jegyzetfüzet forráskódja

Az adatmérnökök a Python-jegyzetfüzet forráskódjával helyileg egy IDE-ben (például [Visual Studio-kódban)](https://code.visualstudio.com)vagy közvetlenül a Databricks-munkaterületen dolgoznak. Ez utóbbi lehetővé teszi a kód hibakeresését a fejlesztői környezetben. Mindenesetre a kód egy elágaztatási szabályzatot követően egyesül a tárházzal. Erősen ajánlott a kódot fájlokban `.py` tárolni, `.ipynb` nem pedig Jupyter notebook formátumban. Javítja a kód olvashatóságát, és lehetővé teszi az automatikus kódminőség-ellenőrzést a CI folyamatban.

### <a name="azure-data-factory-source-code"></a>Azure data factory forráskód

Az Azure Data Factory-folyamatok forráskódja egy munkaterület által létrehozott jsonfájlok gyűjteménye. Az adatmérnökök általában az Azure Data Factory munkaterületen dolgoznak egy vizuális tervezővel, nem pedig közvetlenül a forráskódfájlokkal. Konfigurálja a munkaterületet egy forrásvezérlő tárházzal, ahogy az az [Azure Data Factory dokumentációjában](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration)szerepel. Ezzel a konfigurációval az adatmérnökök egy előnyben részesített elágazási munkafolyamatot követően együttműködhetnek a forráskódon.    

## <a name="continuous-integration-ci"></a>Folyamatos integráció (CI)

A folyamatos integrációs folyamat végső célja, hogy összegyűjtse a közös csapatmunkát a forráskódból, és előkészítse azt az alsóbb rétegbeli környezetekbe való üzembe helyezéshez. A forráskód-kezeléshez, ez a folyamat is különbözik a Python-jegyzetfüzetek és az Azure Data Factory-folyamatok esetében. 

### <a name="python-notebook-ci"></a>Python Notebook CI

A Python-jegyzetfüzetek CI-folyamata lekéri a kódot az együttműködési ágból (például ***fő*** vagy ***fejlesztés),*** és a következő tevékenységeket hajtja végre:
* Kód szinting
* Egységtesztelés
* A kód mentése műtermékként

A következő kódrészlet bemutatja ezeknek a lépéseknek a megvalósítását egy Azure DevOps ***yaml*** folyamatban:

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

A folyamat ***a Flake8-ot*** használja a Python-kód szintálásához. Futtatja a forráskódban meghatározott egységteszteket, és közzéteszi a szöszleési és teszteredményeket, hogy elérhetők legyenek az Azure Pipeline végrehajtási képernyőjén:

![szösz-egység-vizsgálatok](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Ha a szöszlelő és az egység tesztelése sikeres, a folyamat másolja a forráskódot a műtermék-tárházba, amelyet a későbbi telepítési lépések nek kell használnia.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Ci folyamat egy Azure Data Factory folyamat szűk keresztmetszetet jelent a teljes CI/CD történet egy adatbetöltési folyamat. Nincs ***folyamatos*** integráció. Az Azure Data Factory üzembe helyezhető műtermék az Azure Resource Manager-sablonok gyűjteménye. Az egyetlen módja ezeknek a sablonoknak az azure data factory munkaterületen található ***közzétételi*** gombra való kattintás. Itt nincs automatizálás.
Az adatmérnökök egyesítik a forráskódot a szolgáltatáságakból az együttműködési ágba, például ***fő*** vagy ***fejlesztési .*** Ezután a megadott engedélyekkel rendelkező személy a ***közzététel*** gombra kattintva azure Resource Manager-sablonokat hozhat létre az együttműködési ág forráskódjából. Amikor a gombra kattintanak, a munkaterület ellenőrzi a folyamatokat (gondoljon rá, mint a szösz- és egységtesztelés), létrehozza az Azure Resource Manager-sablonokat (úgy gondolja, hogy az építés), és menti a létrehozott sablonokat egy technikai ***ágba, adf_publish*** ugyanabban a kódtárban (gondoljon rá úgy, mint a közzétételi összetevők). Ezt az ágat az Azure Data Factory munkaterület automatikusan létrehozza. Ezt a folyamatot az [Azure Data Factory dokumentációjában](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)ismertetik részletesen.

Fontos, hogy győződjön meg arról, hogy a létrehozott Azure Resource Manager-sablonok környezet függetlenek. Ez azt jelenti, hogy minden olyan érték, amely eltérhet a környezetek között, parametrizált. Az Azure Data Factory elég intelligens ahhoz, hogy az ilyen értékek többsége, például paraméterek. A következő sablonban például az Azure Machine Learning-munkaterülethez való kapcsolódási tulajdonságok paraméterekként jelennek meg:

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

Azonban érdemes lehet az egyéni tulajdonságok, amelyek alapértelmezés szerint nem kezelik az Azure Data Factory munkaterületet. Ebben a cikkben az Azure Data Factory-folyamat meghívja az adatokat feldolgozó Python-jegyzetfüzetet. A jegyzetfüzet elfogad egy bemeneti adatfájl nevét tartalmazó paramétert.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Ez a név ***a Fejlesztői,*** ***QA***, ***UAT***és ***PROD*** környezetekben eltérő. A több tevékenységet tartalmazó összetett folyamatokban több egyéni tulajdonság is lehet. Célszerű ezeket az értékeket egy helyen összegyűjteni, és ***folyamatváltozóként definiálni:***

![adf-változók](media/how-to-cicd-data-ingestion/adf-variables.png)

A csővezeték-tevékenységek a folyamatváltozókra hivatkozhatnak, miközben ténylegesen használják őket:

![adf-notebook-paraméterek](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Az Azure Data Factory munkaterület ***alapértelmezés szerint nem*** teszi elérhetővé a folyamatváltozókat az Azure Resource Manager-sablonok paramétereiként. A munkaterület az [alapértelmezett paraméterezési sablont](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) használja, amely diktálja, hogy milyen folyamattulajdonságokat kell elérhetővé tenni az Azure Resource Manager sablonparamétereiként. A folyamatváltozók listához való hozzáadásához frissítse az [Alapértelmezett paraméterezési sablon](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) "Microsoft.DataFactory/factories/pipelines" szakaszát a következő kódrészlettel, és helyezze az eredmény jsonfájlt a forrásmappa gyökerébe:

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

Ha így tesz, az Azure Data Factory munkaterülete a ***közzétételgombra*** való kattintáskor hozzáadja a változókat a paraméterek listájához:

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

A jsonfájlban lévő értékek a folyamatdefinícióban konfigurált alapértelmezett értékek. Az Azure Resource Manager-sablon üzembe helyezésekor várhatóan felül lesznek bírálva a célkörnyezet értékeivel.

## <a name="continuous-delivery-cd"></a>Folyamatos szállítás (CD)

A folyamatos kézbesítési folyamat veszi a műtermékek, és telepíti őket az első célkörnyezetben. Ez biztosítja, hogy a megoldás működik a tesztek futtatásával. Ha sikeres, továbbra is a következő környezetben. A CD Azure-folyamat több szakaszból áll, amelyek a környezeteket jelölik. Minden szakasz [olyan központi telepítéseket](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) és [feladatokat](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) tartalmaz, amelyek a következő lépéseket hajtják végre:
* Python-jegyzetfüzet üzembe helyezése az Azure Databricks-munkaterületre
* Azure Data Factory-folyamat üzembe helyezése 
* A folyamat futtatása
* Az adatok betöltési eredményének ellenőrzése

A folyamatfázisok [konfigurálhatók jóváhagyásokkal](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) és [kapukkal,](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) amelyek további vezérlést biztosítanak a telepítési folyamat környezetek láncolatán keresztül történő alakulására vonatkozóan.

### <a name="deploy-a-python-notebook"></a>Python-jegyzetfüzet központi telepítése

A következő kódrészlet egy Azure [Pipeline-telepítést](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) határoz meg, amely egy Python-jegyzetfüzetet egy Databricks-fürtbe másol:

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

A CI által létrehozott összetevők automatikusan átmásolódnak a központi telepítési ügynökbe, és elérhetők a ***$(Pipeline.Workspace)*** mappában. Ebben az esetben a központi telepítési feladat a ***Python-jegyzetfüzetet tartalmazó di-notebookok*** összetevőre hivatkozik. Ez a [központi telepítés](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) a [Databricks Azure DevOps-bővítmény t](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) használja a notebook-fájlok másolásához a Databricks munkaterületre.
A ***Deploy_to_QA*** szakasz az Azure DevOps-projektben definiált ***devops-ds-qa-vg*** változócsoportra mutató hivatkozást tartalmaz. Ebben a szakaszban a lépések az adott változócsoport változóira vonatkoznak (például $(DATABRICKS_URL), $(DATABRICKS_TOKEN)). Az elképzelés az, hogy a következő szakasz (például ***Deploy_to_UAT)*** a saját UAT-hatókörű változócsoportjában definiált változónevekkel fog működni.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Azure Data Factory-folyamat üzembe helyezése

Az Azure Data Factory üzembe helyezhető műtermék egy Azure Resource Manager-sablon. Ezért az ***Azure Resource Group deployment*** feladattal lesz telepítve, ahogy az a következő kódrészletben látható:

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
Az adatfájlnév paraméter értéke a QA szakaszváltozó-csoportban definiált $(DATA_FILE_NAME) változóból származik. Hasonlóképpen az ***ARMTemplateForFactory.json-ban*** definiált összes paraméter felülbírálható. Ha nem, akkor az alapértelmezett értékeket használja a rendszer.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Futtassa a folyamatot, és ellenőrizze az adatbetöltés eredményét

A következő lépés annak biztosítása, hogy az üzembe helyezett megoldás működik. A következő feladatdefiníció egy Azure Data Factory-folyamatot futtat egy [PowerShell-parancsfájllal,](https://github.com/microsoft/DataOps/tree/master/adf/utils) és egy Python-jegyzetfüzetet hajt végre egy Azure Databricks-fürtön. A jegyzetfüzet ellenőrzi, hogy az adatok megfelelően lettek-e betöltésekor, és $(bin_FILE_NAME) névvel ellenőrzi az eredményadatfájlt.

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

A feladat utolsó feladata ellenőrzi a jegyzetfüzet végrehajtásának eredményét. Ha hibát ad vissza, a folyamat végrehajtásának állapotát sikertelenre állítja.

## <a name="putting-pieces-together"></a>Összerakva a darabokat

A cikk eredménye egy CI/CD Azure Pipeline, amely a következő szakaszokból áll:
* CI
* Üzembe helyezés a minőségbiztosításba
    * Üzembe helyezés databricks + üzembe helyezés a ADF
    * Integrációs teszt

A ***telepítési*** szakaszok száma megegyezik a célkörnyezetek száma van. Minden ***üzembe helyezési*** fázis két, párhuzamosan futó [központi telepítést](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) tartalmaz, és egy [feladatot,](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) amely a környezeten a megoldás teszteléséhez a központi telepítés után fut.

A folyamat mintamegvalósítása a következő ***yaml*** kódrészletben van összeszerelve:

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

## <a name="next-steps"></a>További lépések

* [Verziókövetés az Azure Data Factoryben](https://docs.microsoft.com/azure/data-factory/source-control)
* [Folyamatos integráció és kézbesítés az Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps for Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
