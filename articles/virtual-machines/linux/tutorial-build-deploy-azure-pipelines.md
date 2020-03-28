---
title: Oktatóanyag – CI/CD az Azure-beli virtuális gépekhez az Azure-folyamatok használatával
description: Ebben az oktatóanyagban megtudhatja, hogyan állíthatja be a Node.js alkalmazás folyamatos integrációját (CI) és folyamatos üzembe helyezését (CD) az Azure-beli virtuális gépekhez YAML-alapú Azure-folyamat használatával.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: bb7c773d02c5da5c115af79cd9e90c78e71eb6bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76988328"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Oktatóanyag: Az alkalmazás üzembe helyezése Linuxos virtuális gépekre az Azure-ban az Azure DevOps-szolgáltatások és az Azure-folyamatok használatával

A folyamatos integráció (CI) és a folyamatos üzembe helyezés (CD) egy folyamatot alkot, amellyel minden kód véglegesítése után létrehozhatja, feloldhatja és üzembe helyezheti a kódot. Ez a dokumentum tartalmazza a ci/CD-folyamat beállításához kapcsolódó lépéseket az Azure-folyamatok használatával többgépes telepítések végrehajtásához.

Az Azure Pipelines ci/CD-automatizálási eszközök teljes körű, teljes körűen kiemelt készletét biztosítja a virtuális gépekre történő központi telepítésekhez, akár a helyszíni, mind a felhőben.

Ebben az oktatóanyagban egy YAML-alapú CI/CD-folyamatot fog beállítani az alkalmazás üzembe helyezéséhez egy Azure [Pipelines-környezetben,](https://docs.microsoft.com/azure/devops/pipelines/process/environments?view=azure-devops) linuxos virtuális gépekkel erőforrásokkal, amelyek mindegyike webkiszolgálóként szolgál az alkalmazás futtatásához.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Szerezzen be egy mintaalkalmazást.
> * Hozzon létre egy YAML-alapú Azure Pipelines CI-folyamatot a mintaalkalmazás létrehozásához.
> * Hozzon létre egy Azure-folyamatkörnyezetet az Azure virtuális gépekhez
> * Hozzon létre egy Azure Pipelines CD-folyamatot.
> * Manuális és CI által kiváltott üzembe helyezések végrehajtása.

## <a name="before-you-begin"></a>Előkészületek

* Jelentkezzen be az Azure DevOps**https://dev.azure.com/** Services szervezetébe ( ). 
  Beszerezhet egy [ingyenes Azure DevOps Services-szervezetet](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > További információ: [Csatlakozás az Azure DevOps Services-hez](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Telepítési célként egy Linux rendszerű virtuális gépre van szükség.  További információért lásd: [Linux rendszerű virtuális gépek létrehozása és kezelése az Azure CLI-vel](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Nyissa meg a virtuális géphez a 80-as a bejövő portot. További információ: [Hozzon létre egy hálózati biztonsági csoportot az Azure Portal használatával](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-your-sample-app-code"></a>A mintaalkalmazás kódjának beszereznie

Ha már rendelkezik egy alkalmazást a GitHubon, amelyet telepíteni szeretne, megpróbálhat létrehozni egy folyamatot az adott kódhoz.

Azonban, ha egy új felhasználó, akkor lehet, hogy jobban kezdeni segítségével a minta kódot. Ebben az esetben elvillázatot a GitHubon:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic egy [Java Spring Boot](https://spring.io/guides/gs/spring-boot) alkalmazás segítségével épült [Maven](https://spring.io/guides/gs/maven/).

#### <a name="javascript"></a>[Javascript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Ez a Node.js alkalmazás [a Yeoman-en](https://yeoman.io/learning/index.html)keresztül készült. Express-t bowert és gruntot használ. És rendelkezik néhány npm-csomaggal függőségként.
> A minta egy szkriptet is tartalmaz, amely beállítja az Nginx kiszolgálót, és üzembe helyezi az alkalmazást. A végrehajtása a virtuális gépeken történik. Pontosabban a szkript az alábbi műveleteket végzi:
> 1. Telepíti a Node-ot, az Nginx kiszolgálót és a PM2-t.
> 2. Konfigurálja az Nginx kiszolgálót és a PM2-t.
> 3. Elindítja a Node alkalmazást.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>A Linux virtuális gép előfeltételei

A fent említett mintaalkalmazásokat tesztelték az Ubuntu 16.04-en, és javasoljuk, hogy a Linux VM ugyanazon verzióját használja ehhez a rövid útmutatóhoz.
Kövesse az alábbiakban ismertetett további lépéseket az alkalmazáshoz használt futásidejű verem alapján.

#### <a name="java"></a>[Java](#tab/java)

- Java spring boot és spring cloud alapú alkalmazások üzembe helyezéséhez hozzon létre egy Linux virtuális gép az Azure-ban [ezzel a](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) sablonnal, amely teljes mértékben támogatott OpenJDK-alapú futásidejű.
- Java servlets tomcat kiszolgálón történő telepítéséhez hozzon létre egy Java 8-as Linux virtuális gépet [ezzel az](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) Azure-sablonnal, és [konfigurálja a Tomcat 9.x szolgáltatást.](https://tomcat.apache.org/tomcat-9.0-doc/setup.html)
- Java EE alapú alkalmazás üzembe helyezéséhez használjon Azure-sablont [Linux VM + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) vagy [Linux VM + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) vagy [Linux VM +Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) + WildFly/JBoss 14 létrehozásához 

#### <a name="javascript"></a>[Javascript](#tab/java-script)

Javascript-alkalmazás vagy Node.js alkalmazás telepítéséhez az alkalmazás üzembe helyezéséhez szüksége lesz egy Nginx webkiszolgálóval rendelkező Linux virtuális gépre.
Ha még nem rendelkezik egy Linux virtuális gép nginx, hozzon létre egyet most az Azure-ban a lépések ebben a [példában.](/azure/virtual-machines/linux/quick-create-cli)

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Hozzon létre egy Azure Pipelines környezetet az Azure virtuális gépeivel

Virtuális gépek környezeten belüli erőforrásokként [adhatók](https://docs.microsoft.com/azure/devops/pipelines/process/environments) hozzá, és többgépes telepítések számára is megcélozhatók. A környezeten belüli üzembe helyezési előzmények et követő nézetek nyomon követhetőséget biztosítanak a virtuális géptől a folyamatig, majd a véglegesítésig.

Létrehozhat egy környezetet a "**Környezetek**" hub a "**Folyamatok**" szakaszban.
1.  Jelentkezzen be az Azure DevOps-szervezetbe, és keresse meg a projektet.
2.  A projektben keresse meg a **Folyamatok** lapot. Ezután válassza **a Környezet lehetőséget,** és kattintson **a Környezet létrehozása**gombra. Adja meg a környezet **nevét** (kötelező) és **a leírást.**
3.  Válassza a **virtuális gépek** lehetőséget a környezethez hozzáadni kívánt **erőforrásként,** majd kattintson a **Tovább**gombra.
4.  Válassza az Operációs rendszer (Windows/Linux) lehetőséget, és **másolja a PS regisztrációs parancsfájlt.** 
5.  Most futtassa a másolt parancsfájlt egy rendszergazdai PowerShell parancssorból az egyes, ebben a környezetben regisztrálandó célvirtuális gépeken.
    > [!NOTE]
    > - Személyes hozzáférési jogkivonata a bejelentkezett felhasználó előre beillesztett a szkript, amely lejár ugyanazon a napon, hogy a másolt script használhatatlan rajta.
    > - Ha a virtuális gép már rendelkezik olyan ügynök fut rajta, adjon meg egy egyedi nevet az "ügynök" a környezetben való regisztrációhoz.
6.  A virtuális gép regisztrálása után a környezet "erőforrások" lapján környezeti erőforrásként jelenik meg.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  További virtuális gépek hozzáadásához megtekintheti és másolhatja újra a parancsfájlt az "Erőforrás hozzáadása" gombra kattintva, és a "Virtuális gépek" erőforrásként lehetőséget választva. Ez a parancsfájl ugyanaz marad az összes virtuális gép hozzáadása ebben a környezetben. 
8.  Minden gép együttműködik az Azure-folyamatok az alkalmazás üzembe helyezésének koordinálása érdekében.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Hozzáadhat címkéket a virtuális géphez az interaktív PS regisztrációs parancsfájl (vagy) az erőforrásnézetből is hozzáadhatja/eltávolíthatja ugyanazt az erőforrásnézetből, ha az erőforrások nézetében az egyes virtuális gép-erőforrások végén lévő hárompontra kattint.

   A címkék et hozzá, amelyek lehetővé teszik, hogy korlátozza a központi telepítés adott virtuális gépek, ha a környezet et használják a központi telepítési feladat. A címkék legfeljebb 256 karakterből állhatnak, de a használható címkék száma nincs korlátozva.

   ![Virtuális gépjelzők](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>A CI-buildfolyamat definiálása

Szüksége lesz egy folyamatos integrációs (CI) build folyamat, amely közzéteszi a webalkalmazást, valamint egy központi telepítési parancsfájlt, amely helyileg futtatható az Ubuntu kiszolgálón. Ci build-folyamat beállítása a használni kívánt futásidő alapján. 

1. Jelentkezzen be az Azure DevOps-szervezetbe, és keresse meg a projektet.

1. A projektben keresse meg a **Folyamatok** lapot. Ezután válassza ki a műveletet egy új folyamat létrehozásához.

1. A varázsló lépéseit úgy, hogy először a **GitHubot** választja a forráskód helyeként.

1. Előfordulhat, hogy a bejelentkezéshez át irányítja a GitHubra. Ha igen, adja meg a GitHub-hitelesítő adatait.

1. Amikor megjelenik az adattárak listája, válassza ki a kívánt mintaalkalmazás-tárházat.

1. Az Azure Pipelines elemzi a tárházat, és megfelelő folyamatsablont javasol.

#### <a name="java"></a>[Java](#tab/java)

Válassza **starter** ki az indítósablont, és másolja az alábbi YAML-kódrészletet, amely létrehozza a Java-projektet, és teszteket futtat az Apache Mavennel:

```YAML
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

További útmutatásért kövesse a [Java alkalmazás létrehozása a Mavennel](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/java)című részben leírt lépéseket.

#### <a name="javascript"></a>[Javascript](#tab/java-script)

Válassza **starter** ki az indítósablont, és másolja az alábbi YAML-kódrészletet, amely egy általános Node.js projektet hoz létre npm-mel.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

További útmutatásért kövesse a [Node.js alkalmazás létrehozása kortysegítségével című lépéseit.](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/javascript)

- Vessen egy pillantást a csővezeték, hogy mit csinál. Győződjön meg arról, hogy az összes alapértelmezett bemenet megfelel a kódnak.

- Válassza a **Mentés és futtatás**lehetőséget, majd válassza a **Véglegesítés közvetlenül a főágnak**lehetőséget, majd válassza a **Mentés és újrafuttatás** lehetőséget.

- Új futtatás indul. Várd meg, amíg a futás befejeződik.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>A Linux virtuális gépre való üzembe helyezéshez szükséges CD-lépések meghatározása

1. A fenti folyamat szerkesztése és egy [telepítési feladat](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs) belefoglalása a környezetre és a virtuális gép erőforrásaira való hivatkozással, amelyet korábban az alábbi YAML szintaxis használatával használt:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web1
     strategy:
   ```
2. Kiválaszthatja a virtuális gépek adott készleteit a környezetből a központi telepítés fogadásához a környezetben lévő egyes virtuális gépekhez megadott **címkék** megadásával.
[Itt](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#deployment-job) van a teljes YAML-séma a központi telepítési feladathoz.

3. Megadhatja eithor `runOnce` `rolling` vagy telepítési stratégiaként. 

   `runOnce`a legegyszerűbb telepítési stratégia, amelyben az összes életciklus-horgok, `preDeploy` `deploy`azaz , `routeTraffic`és `postRouteTraffic`, végre egyszer. Ezután `on:` `success` vagy `on:` `failure` végrehajtják, vagy végrehajtják.

   Az alábbiakban a példa YAML `runOnce` kódrészlet:
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Az alábbiakban egy példa a YAML-kódrészlet, amely segítségével definiálhatja a gördülő stratégia virtuális gépek frissítések akár 5 célok minden iteráció. `maxParallel`meghatározza a felvethethető célok számát, párhuzamosan. A kijelölés a célok abszolút számát vagy százalékos arányát számlálja, amelyeknek bármikor rendelkezésre kell állniuk, kivéve azokat a célokat, amelyekre telepítve van. Azt is használják, hogy meghatározza a sikeres és a hiba feltételek üzembe helyezés során.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   A feladat minden egyes futtatásakor a `<environment name>` központi telepítési előzmények rögzítésre kerülnek a virtuális gépek által létrehozott és regisztrált környezetben.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Futtassa a folyamatot, és kövessen követhetőségi nézeteket kapjon környezetben
A környezet központi telepítései nézete biztosítja a véglegesítések és a munkaelemek teljes nyomon követhetőségét, valamint a folyamatok közötti telepítési előzményeket környezetenként/erőforrásonként.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>További lépések
- Folytathatja az imént létrehozott [folyamat testreszabását.](https://docs.microsoft.com/azure/devops/pipelines/customize-pipeline)
- A YAML-folyamatokban további tudnivalókat a [YAML-sémahivatkozás .](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema)
- Annak elsajátításához, hogyan helyezhet üzembe LAMP (Linux, Apache, MySQL és PHP) stacket, lépjen tovább a következő oktatóanyagra.

> [!div class="nextstepaction"]
> [LAMP verem telepítése](tutorial-lamp-stack.md)
