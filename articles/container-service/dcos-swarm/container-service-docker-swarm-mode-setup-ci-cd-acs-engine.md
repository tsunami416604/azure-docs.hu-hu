---
title: "CI/CD-ről az Azure-tárolót szolgáltatás vezérlőprogramja és Swarm mód"
description: "Azure-tároló szolgáltatás vezérlőprogramja használja Docker Swarm-módban, egy Azure-tároló beállításkulcs, és a Visual Studio Team Services egy több tároló .NET Core alkalmazás folyamatosan továbbítására"
services: container-service
author: diegomrtnzg
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 6aa690ff7ec0689db78ff1225d36171adb30ee2c
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Azure tárolószolgáltatás egy több tároló alkalmazás telepítését az ACS-motor és a Docker Swarm mód Visual Studio Team Services használatával teljes CI/CD folyamat

*Ez a cikk alapján [teljes CI/CD adatcsatorna egy Azure tárolószolgáltatás a Docker Swarmmal Visual Studio Team Services segítségével többszörös tároló alkalmazás üzembe helyezése](container-service-docker-swarm-setup-ci-cd.md) dokumentáció*

Napjainkban egyik legnagyobb kihívás a felhő modern alkalmazások fejlesztése során az igényt ezeknek az alkalmazásoknak folyamatosan továbbítására. Ebből a cikkből megismerheti, hogyan egy teljes folyamatos integrációt és a központi telepítés (CI/CD) folyamat használatával megvalósításához: 
* Azure-tárolót szolgáltatás motorja a Docker Swarm mód
* Azure Container Registry
* Visual Studio Team Services

Ez a cikk alapján egy egyszerű alkalmazást, a rendelkezésre álló [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux), az ASP.NET Core fejlett. Az alkalmazások négy különböző szolgáltatások állnak: három webes API-k és egy webes előtér:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

A célja, hogy az alkalmazás folyamatosan biztosítanak a Docker Swarm mód fürtben, a Visual Studio Team Services használatával. Az alábbi ábra az folyamatos kézbesítési adatcsatorna részletesen:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

A lépések rövid leírása itt található:

1. A forráskódraktárban kódmódosításokat elkötelezettek (Itt GitHub) 
2. GitHub elindítja a Visual Studio Team Services build 
3. Visual Studio Team Services lekérdezi a források legújabb verzióját, és létrehozza a lemezképeket, amelyek az alkalmazás összeállítása 
4. A Visual Studio Team Services az egyes lemezképek egy Docker beállításjegyzék, az Azure-tároló beállításjegyzék szolgáltatással létrehozott leküldéses értesítések 
5. A Visual Studio Team Services új verziót váltja ki. 
6. A kiadás néhány SSH használatával az Azure-tárolót szolgáltatás fő fürtcsomóponton parancsokat futtat 
7. Docker Swarm üzemmódot a fürtön a képek legújabb verzióját kéri le. 
8. Az alkalmazás új verziójának Docker verem használatával van telepítve 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt végre kell hajtania a következő feladatokat:

- [Az Azure Tárolószolgáltatásban ACS motorral mód Swarm-fürt létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Csatlakozás a Swarm-fürthöz az Azure Container Service-ben](../container-service-connect.md)
- [Hozzon létre egy Azure-tárolót beállításjegyzék](../../container-registry/container-registry-get-started-portal.md)
- [Egy Visual Studio Team Services fiók és a team projekt létrehozása](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Elágazás a GitHub-tárházban, a GitHub-fiókba](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Az Azure Container Service Docker Swarm-vezénylője örökölt önálló Swarmot használ. Az integrált [Swarm mód](https://docs.docker.com/engine/swarm/) (a Docker 1.12-es és újabb verzióiban) jelenleg nem támogatott vezénylő az Azure Container Service-ben. Emiatt használjuk [ACS motor](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), közösségi hozzájárult [gyorsindítási sablonon](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), vagy egy Docker-megoldás a a [Azure piactér](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>1. lépés: A Visual Studio Team Services-fiók konfigurálása 

Ebben a szakaszban konfigurálhatja a Visual Studio Team Services-fiók. A VSTS Services végpontjainak, a Visual Studio Team Services projekt konfigurálásához kattintson a **beállítások** ikonra az eszköztár, és válassza ki a **szolgáltatások**.

![Nyissa meg a szolgáltatások végpont](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Csatlakozás a Visual Studio Team Services és az Azure-fiók

Állítsa be a kapcsolatot a VSTS-projektet és az Azure-fiókjával között.

1. Kattintson a bal oldali **új szolgáltatási végpont** > **Azure Resource Manager**.
2. Használható az Azure-fiókjával VSTS engedélyezésére, válassza ki a **előfizetés** kattintson **OK**.

    ![A Visual Studio Team Services - Azure engedélyezése](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Csatlakoztassa a Visual Studio Team Services, GitHub

Állítsa be a kapcsolatot a VSTS-projektet és a GitHub-fiók között.

1. Kattintson a bal oldali **új szolgáltatási végpont** > **GitHub**.
2. A GitHub-fiók használható VSTS engedélyezésére, kattintson a **engedélyezés** és kövesse a megjelenő ablakban.

    ![A Visual Studio Team Services - engedélyezik a Githubon](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>VSTS csatlakozni az Azure Tárolószolgáltatás-fürt

Az utolsó előtt a CI/CD-feldolgozási folyamat az első lépésekre külső kapcsolatot a Docker Swarm-fürt konfigurálása az Azure-ban. 

1. A Docker Swarm-fürt típusú végpont hozzáadása **SSH**. Majd adja meg az SSH csatlakozási adatait a Swarm-fürt (főcsomópont).

    ![A Visual Studio Team Services - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

A konfiguráció most történik. A következő lépésekben a CI/CD-feldolgozási folyamat alapszik, és központilag telepíti az alkalmazást a Docker Swarm-fürt létrehozása. 

## <a name="step-2-create-the-build-definition"></a>2. lépés: A build definíció létrehozása

Ebben a lépésben egy build definition beállítása a VSTS-projekthez, és a létrehozási munkafolyamat megadása a tároló lemezképek

### <a name="initial-definition-setup"></a>Kezdeti beállításának

1. Build definíció létrehozása, csatlakozzon a Visual Studio Team Services-projektet, és kattintson **Build & kiadási**. Az a **definíciók Build** kattintson **+ új**. 

    ![A Visual Studio Team Services - új definíció létrehozása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Válassza ki a **üres folyamat**.

    ![A Visual Studio Team Services - új üres Build definíció](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Kattintson a **változók** lapra, és hozzon létre két új változót: **RegistryURL** és **AgentURL**. Illessze be a beállításjegyzék és a fürt ügynökök DNS értékeit.

    ![A Visual Studio Team Services - Build változók konfigurációját](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. A a **Build definíciók** lapon nyissa meg a **eseményindítók** lapot és a build folyamatos integrációt használata az Előfeltételek című szakaszban létrehozott MyShop projekt elágazás konfigurálja. Ezt követően válassza **módosítások kötegelt**. Győződjön meg arról, hogy kiválassza *docker-linux* , a **specification fiókirodai**.

    ![Visual Studio Team Services - Build tárház konfigurációja](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Végül kattintson a **beállítások** lapra, és konfigurálja az alapértelmezett ügynök várólistához **üzemeltetett Linux előzetes**.

    ![A Visual Studio Team Services - Gazdagépügynök-konfigurálási](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Adja meg a létrehozási munkafolyamat
A következő lépéseket a build munkafolyamat határozza meg. Először a kód forrását konfigurálásához. Ehhez jelölje ki a **GitHub** és a **tárház** és **fiókirodai** (docker-linux).

![Konfigurálja a Visual Studio Team Services - kód forrása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Öt tároló lemezképeit számára részére a *MyShop* alkalmazás. Minden egyes lemezképének összeállítása a Dockerfile a projektben található használatával:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Két Docker lépéseket az egyes lemezképek, egy a lemezkép létrehozásához, és egy leküldéses a lemezkép az Azure-tárolót beállításjegyzék van szüksége. 

1. A build munkafolyamat egy lépés felvételéhez kattintson **+ Hozzáadás összeállítása lépés** válassza **Docker**.

    ![A Visual Studio Team Services - Build lépéseket adhat hozzá.](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Az egyes lemezképek konfigurálása egy lépést, amely használja a `docker build` parancsot.

    ![A Visual Studio Team Services - Docker-Build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    A létrehozási műveletet, válassza ki az Azure-tároló beállításjegyzék, a **lemezképet készít** művelet, és az egyes lemezképek definiáló Dockerfile. Állítsa be a **munkakönyvtár** , Dockerfile gyökérkönyvtáraként határozza meg a **kép neve**, és válassza ki **legújabb címke közé tartoznak**.
    
    A lemezkép neve-nak kell lennie a következő formátumban: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Cserélje le **[NAME]** kép nevű:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Az egyes lemezképek, a második lépésben használó konfigurálása a `docker push` parancsot.

    ![A Visual Studio Team Services - Docker leküldéses](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    A leküldéses művelethez válassza ki az Azure-tárolót beállításjegyzék a **lemezkép leküldéses** művelet, adja meg a **kép neve** , amely az előző lépést, és válassza ki a beépített **legújabb címkét tartalmaz**.

4. Miután konfigurálta a build és leküldéses lépéseket az egyes öt lemezképet, a build munkafolyamat a három további lépéseket adhat hozzá.

   ![A Visual Studio Team Services - parancssori feladat hozzáadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. A parancssori feladatot, amely egy bash parancsfájlt használ a *RegistryURL* a docker-compose.yml fájlt a RegistryURL változóval előfordulása. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services - frissítés Compose fájl beállításjegyzék URL-címe](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. A parancssori feladatot, amely egy bash parancsfájlt használ a *AgentURL* a docker-compose.yml fájlt a AgentURL változóval előfordulása.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. Ez a feladat elutasítja azokat a frissített Compose fájlt egy összeállítási összetevő, így használható a kiadásban. További részletek a következő képernyő jelenik meg.

         ![A Visual Studio Team Services - összetevő közzététele](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![A Visual Studio Team Services - Compose közzététele fájl](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Kattintson a **mentés és a feldolgozási sor** a build definition teszteléséhez.

   ![Visual Studio Team Services - mentés- és várólista](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![A Visual Studio Team Services - új sor](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Ha a **Build** helyes, a képernyő jelenik meg, hogy:

  ![A Visual Studio Team Services - Build sikeres volt.](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>3. lépés: A kiadási definíció létrehozása

A Visual Studio Team Services lehetővé teszi [kiadásokban kezelése környezetek között](https://www.visualstudio.com/team-services/release-management/). Folyamatos üzembe helyezés, győződjön meg arról, hogy az alkalmazás telepítve van a különböző környezetekben (például a fejlesztői, tesztelési, éles üzem előtti és éles) zökkenőmentes úgy is engedélyezheti. Létrehozhat egy olyan környezetben, az Azure tároló szolgáltatás Docker Swarm mód fürt jelöli.

![A Visual Studio Team Services - ACS verzióját](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Eredeti kiadásának beállítása

1. Egy kiadási definition létrehozásához kattintson a **kiadásokban** > **+ kiadás**

2. Az összetevő-forrás konfigurálásához kattintson **összetevők** > **hivatkozás egy összetevő forrás**. A build, amelyet az előző lépésben megadott itt, csatolja az új kiadási definíciója. Ezt követően a docker-compose.yml fájlt érhető el a kibocsátási folyamatban.

    ![A Visual Studio Team Services - kiadás összetevők](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. A kiadási eseményindító konfigurálásához kattintson **eseményindítók** válassza **folyamatos üzembe helyezés**. Az eseményindító be ugyanazon összetevő forrás. Ez a beállítás biztosítja, hogy egy új kiadási kezdődik, amikor a létrehozás sikeresen befejeződik.

    ![A Visual Studio Team Services - kiadás eseményindítók](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. A kiadási változók konfigurálásához kattintson **változók** válassza **+ változó** három új változó létrehozása a beállításjegyzék információval: **docker.username**, **docker.password**, és **docker.registry**. Illessze be a beállításjegyzék és a fürt ügynökök DNS értékeit.

    ![Visual Studio Team Services - Build tárház konfigurációja](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Amint az előző képernyőn, kattintson a **zárolási** docker.password jelölőnégyzet. A beállítás akkor fontos, hogy a jelszó korlátozása.
    >

### <a name="define-the-release-workflow"></a>Adja meg a kiadási munkafolyamat

A kiadási munkafolyamat két feladatot hozzáadott tevődik össze.

1. Egy feladat biztonságosan másolja a compose-fájlt egy *telepítése* mappa a Docker Swarm fő csomóponton korábban konfigurált SSH-kapcsolat használatával. További részletek a következő képernyő jelenik meg.
    
    Forrásmappa:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services - kiadás SCP-je](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Egy második feladat futtatásához bash parancs `docker` és `docker stack deploy` parancsok a fő csomóponton. További részletek a következő képernyő jelenik meg.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![A Visual Studio Team Services - kiadás Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    A fő végrehajtott parancs a Docker parancssori felület és a Docker Compose CLI használja a következő feladatok elvégzéséhez:

    - Jelentkezzen be az Azure-tárolót beállításjegyzék (definiált három build-változókat használ a **változók** lapon)
    - Adja meg a **DOCKER_HOST** működéséhez a Swarm végponthoz változó (: 2375)
    - Keresse meg a *telepítése* , amely az előző biztonságos másolási feladat hozta létre, és a docker-compose.yml fájlt tartalmazó mappa 
    - Végrehajtás `docker stack deploy` parancsokat, amelyek az új lemezképet lekéréses és a tárolók létrehozása.

    >[!IMPORTANT]
    > Ahogy az az előző képernyőre, hagyja a **stderr-en sikertelen** jelölőnégyzet nincs bejelölve. Ez a beállítás lehetővé teszi a kiadási befejezéséhez miatt `docker-compose` több diagnosztikai üzeneteket, jelenít meg, például a tárolók leállítása vagy törlése, a standard hibák kimenetét. Jelölje be a jelölőnégyzetet, ha a Visual Studio Team Services jelenti, hogy a kiadás során hibák jelentkeztek akkor is, ha minden megfelelően működik.
    >
3. Mentse az új kiadási definíciója.

## <a name="step-4-test-the-cicd-pipeline"></a>4. lépés: A CI/CD-feldolgozási folyamat tesztelése

Most, hogy a konfiguráció befejezése után is tesztelheti az új CI/CD adatcsatornát. A tesztek legkönnyebben frissítése a forráskódot, és véglegesítse a módosításokat a GitHub-tárházban történő. Néhány másodperccel azután leküldéses a kód jelenik meg a Visual Studio Team Services rendszert futtató új buildverziót. Sikeres befejezést követően egy új kiadási elindul, és az Azure Tárolószolgáltatás-fürthöz az alkalmazás új verziójának telepítése.

## <a name="next-steps"></a>Következő lépések

* Visual Studio Team Services CI/CD kapcsolatos további információkért tekintse meg a [VSTS összeállítása – áttekintés](https://www.visualstudio.com/docs/build/overview).
* Az ACS-motor kapcsolatos további információkért tekintse meg a [ACS motor GitHub-tárház](https://github.com/Azure/acs-engine).
* Docker Swarm móddal kapcsolatos további információkért lásd: a [Docker Swarm – áttekintés](https://docs.docker.com/engine/swarm/).
