---
title: CI/CD – az Azure Container Service-motor és a Swarm mód
description: Az Azure Container Service-motor használja a Docker Swarm módú, egy Azure Container Registrybe, és a Visual Studio Team Services, hogy folyamatosan többtárolós .NET Core-alkalmazásokhoz
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 6c41156383791fb7d72ac02dae919a25a0d15c84
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621062"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Az ACS-motor és a Docker Swarm módú Visual Studio Team Services használatával üzembe helyezhető egy többtárolós alkalmazást az Azure Container Service a teljes CI/CD-folyamat

*Ez a cikk alapján [teljes CI/CD folyamatot egy többtárolós alkalmazást az Azure Container Service Docker Swarm, a Visual Studio Team Services használatával való üzembe helyezéséhez](container-service-docker-swarm-setup-ci-cd.md) dokumentációja*

Napjainkban az egyik legnagyobb kihívást az jelenti a modern alkalmazások fejlesztése során folyamatban van, hogy ezek az alkalmazások folyamatosan. Ebből a cikkből megismerheti, hogyan teljes folyamatos integráció és készregyártás (CI/CD) folyamat használatával megvalósításához: 
* Az Azure Container Service-motor a Docker Swarm mód
* Azure Container Registry
* Visual Studio Team Services

Ez a cikk egy egyszerű alkalmazást, a rendelkezésre álló alapján [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux), fejlett ASP.NET Core használatával. Az alkalmazás négy különböző szolgáltatást épül fel: három webes API-k és a egy webes előtérrendszert:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

A cél, hogy ezt az alkalmazást egy Docker Swarm módú fürtöt, a Visual Studio Team Services használatával folyamatos továbbítását. Az alábbi ábra a folyamatos teljesítési folyamat részletesen:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

A következő lépések röviden:

1. A forráskód adattára kódmódosítás elkötelezettek (Itt GitHub) 
2. GitHub aktiválnak egy buildet, a Visual Studio Team Servicesben 
3. Visual Studio Team Services lekérdezi a legújabb verzióra a forrásból, és létrehozza a lemezképeket, amelyek az alkalmazás összeállítása 
4. A Visual Studio Team Services minden rendszerképet leküldi a Docker-tárolójegyzék létrehozása az Azure Container Registry szolgáltatással 
5. A Visual Studio Team Services elindít egy új kiadás 
6. A kiadás fut néhány parancsot SSH-val az Azure container service fő fürtcsomópontra 
7. A fürtön a docker Swarm módú lekéri a rendszerképet a legújabb verzióra 
8. Az alkalmazás új verziójának üzembe helyezett Docker Stack használatával 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt kell végrehajtani az alábbi feladatokat:

- [Hozzon létre egy Swarm módú fürtöt az Azure Container Service-ACS Engine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Csatlakozás a Swarm-fürthöz az Azure Container Service-ben](../container-service-connect.md)
- [Az Azure container registry létrehozása](../../container-registry/container-registry-get-started-portal.md)
- [Rendelkezik egy Visual Studio Team Services-fiók és a team projekt létrehozása](https://docs.microsoft.com/vsts/organizations/accounts/create-organization-msa-or-work-student)
- [A GitHub-fiókjába a GitHub tárház elágaztatása](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Az Azure Container Service Docker Swarm-vezénylője örökölt önálló Swarmot használ. Az integrált [Swarm mód](https://docs.docker.com/engine/swarm/) (a Docker 1.12-es és újabb verzióiban) jelenleg nem támogatott vezénylő az Azure Container Service-ben. Ezért használjuk a [ACS Engine](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), Közösség által biztosított [gyorsindítási sablon](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), vagy egy Docker-megoldást a [Azure Marketplace-en](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>1. lépés: A Visual Studio Team Services-fiók konfigurálása 

Ebben a szakaszban konfigurálja a Visual Studio Team Services-fiók. Vsts-ben szolgáltatások végpontjait, a Visual Studio Team Services-projektben kattintson a **beállítások** ikonra az eszköztárban, majd válassza a **szolgáltatások**.

![Nyissa meg a szolgáltatások végpontja](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Visual Studio Team Services és az Azure-fiók csatlakoztatásával

Állítsa be a VSTS-projekt és az Azure-fiókjával.

1. Kattintson a bal oldali **új szolgáltatásvégpont** > **Azure Resource Manager**.
2. Engedélyezze a vsts-ben az Azure-fiókkal működik, válassza a **előfizetés** kattintson **OK**.

    ![A Visual Studio Team Services – Azure engedélyezése](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Csatlakozás a Visual Studio Team Services és a GitHub

Állítsa be a VSTS-projektet és a GitHub-fiók közötti kapcsolat.

1. Kattintson a bal oldali **új szolgáltatásvégpont** > **GitHub**.
2. Engedélyezze a GitHub-fiók használata a vsts-ben, kattintson a **engedélyezés** , és kövesse a megjelenő ablakban.

    ![A Visual Studio Team Services - GitHub engedélyezése](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>VSTS csatlakozhat az Azure Container Service-fürt

Az utolsó előtt a CI/CD-folyamat az első lépésekre külső kapcsolatok a Docker Swarm-fürt beállítása az Azure-ban. 

1. A Docker Swarm-fürt esetében típusú végpont hozzáadása **SSH**. Ezután írja be a SSH-kapcsolati adatok a Swarm-fürt (fő csomópontot).

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Minden konfiguráció a most történik. Az alábbi lépésekkel hoz létre a CI/CD folyamatot, amely az alkalmazás a Docker Swarm-fürt létrehozása és telepítése. 

## <a name="step-2-create-the-build-definition"></a>2. lépés: A builddefiníció létrehozása

Ebben a lépésben builddefiníció beállítása a VSTS-projekthez, és a build munkafolyamat definiálása a tárolólemezképek

### <a name="initial-definition-setup"></a>Kezdeti definíció beállítása

1. Builddefiníció létrehozása a Visual Studio Team Services-projekthez csatlakozzon, és kattintson a **Build & Release**. Az a **Builddefinícióiról** területén kattintson **+ új**. 

    ![A Visual Studio Team Services – új Build definíciója](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Válassza ki a **üres folyamat**.

    ![A Visual Studio Team Services – új üres Builddefiníció](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Kattintson a **változók** lapra, és hozzon létre két új változót: **RegistryURL** és **AgentURL**. Illessze be a beállításjegyzék és a fürt ügynökök DNS értékeit.

    ![A Visual Studio Team Services - Build változók konfigurálása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Az a **definíciókat hozhat létre** lapon nyissa meg a **eseményindítók** fülre, és konfigurálja a folyamatos integráció használata az elágazást, amelyet az Előfeltételek MyShop projekt build. Ezután válassza ki **Batch-módosítások**. Győződjön meg arról, hogy kiválasztotta *docker-linux* , a **specifikáció ágban**.

    ![A Visual Studio Team Services - Build adattár-konfiguráció](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Végül kattintson a **beállítások** fülre, és konfigurálja az alapértelmezett ügynök üzenetsor, melyben **üzemeltetett Linux előzetes**.

    ![A Visual Studio Team Services - Gazdagépügynök-konfigurálási](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>A build munkafolyamatokat
A következő lépéseket a build munkafolyamat határozza meg. Először a forrás a szabályzat konfigurálása. Ehhez válassza ki a **GitHub** és a **tárház** és **ág** (docker-linux).

![A Visual Studio Team Services - konfigurálása kód forrása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Nincsenek hozhat létre öt tárolórendszerképeket a *MyShop* alkalmazás. Egyes rendszerképek a docker-fájlban található meg a projekt használatával lett összeállítva:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Szüksége lesz az egyes rendszerképek, egy a rendszerkép létrehozásához, és egy, a rendszerkép leküldése az Azure container registry a két Docker lépést. 

1. A build-munkafolyamat egy lépés hozzáadásához kattintson **+ Add build lépés** válassza **Docker**.

    ![A Visual Studio Team Services - létrehozási lépések hozzáadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Az egyes lemezképek konfigurálása egy lépést, amely használja a `docker build` parancsot.

    ![A Visual Studio Team Services - Docker Build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    A létrehozási műveletet, válassza ki az Azure Container Registrybe a **állítson össze egy rendszerképet** művelet, és a docker-fájl, amely meghatározza az egyes rendszerképek. Állítsa be a **Working Directory** , a docker-fájlban gyökérkönyvtár meghatározása a **rendszerkép neve**, válassza ki **közé tartozik a legújabb címke**.
    
    A rendszerkép nevének azt kell a következő formátumban: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Cserélje le **[NAME]** rendszerkép nevét:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Az egyes lemezképek, a második lépésben használó konfigurálása a `docker push` parancsot.

    ![A Visual Studio Team Services - Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    A leküldéses művelethez válassza ki az Azure container registry a **rendszerkép leküldése** művelet, adja meg a **Lemezképnév** , amely az előző lépést, és válassza ki a beépített **közé tartozik a legújabb címke**.

4. Miután konfigurálta a build és a leküldéses lépéseket az egyes öt rendszerképet, a build-munkafolyamat a három további lépéseket adhat hozzá.

   ![A Visual Studio Team Services - parancssori feladat hozzáadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. Egy parancssori feladatot, amely egy bash-szkript használ a *RegistryURL* a docker-compose.yml fájl RegistryURL változóval előfordulás. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![A Visual Studio Team Services - frissítés összeállítás fájlt a beállításjegyzék URL-címe](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. Egy parancssori feladatot, amely egy bash-szkript használ a *AgentURL* a docker-compose.yml fájl AgentURL változóval előfordulás.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. Ez a feladat csökken, a buildösszetevőt a frissített Compose-fájlt, így használhatók a kiadásban. Részleteket a következő képernyő jelenik meg.

         ![A Visual Studio Team Services - összetevők közzététele](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![A Visual Studio Team Services - összeállítás közzététele fájl](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Kattintson a **várólistára & mentése** a builddefiníció teszteléséhez.

   ![A Visual Studio Team Services - mentés és üzenetsorba](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![A Visual Studio Team Services – új várólista](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Ha a **összeállítása** megfelelő, tekintse meg ezt a képernyőt kell:

  ![A Visual Studio Team Services - Build sikeres volt](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>3. lépés: A kiadási definíció létrehozása

A Visual Studio Team Services lehetővé teszi, hogy [kiadások kezelheti a környezeteket](https://www.visualstudio.com/team-services/release-management/). Győződjön meg arról, hogy az alkalmazás üzemel, a különböző környezetekben (például fejlesztési, tesztelési, éles üzem előtti vagy éles) zökkenőmentes módon való folyamatos üzembe helyezés is engedélyezheti. Létrehozhat olyan környezetet, amely az Azure Container Service Docker Swarm módú fürtöt jelöli.

![A Visual Studio Team Services - az ACS-kiadás](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Kezdeti kiadás beállítása

1. Kiadási definíció létrehozása, kattintson a **kiadásokban** > **+ kiadás**

2. Az összetevő-forrás konfigurálásához kattintson **összetevők** > **hivatkozás egy összetevő forrás**. Az új kiadási definíció itt összekapcsolása a build, amelyet az előző lépésben megadott. Ezt követően a docker-compose.yml fájl érhető el a kibocsátási folyamat során.

    ![A Visual Studio Team Services - kiadás összetevők](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. A kiadási trigger konfigurálásához kattintson **eseményindítók** válassza **folyamatos üzembe helyezés**. Állítsa be az eseményindító összetevő azonos forrásból. Ez a beállítás biztosítja, hogy új kiadás elindul, amikor a létrehozás sikeresen befejeződik.

    ![A Visual Studio Team Services - kiadás eseményindítók](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. A kiadási változók konfigurálásához kattintson **változók** válassza **+ változó** három új változó létrehozása a beállításjegyzék információval: **docker.username**, **docker.password**, és **docker.registry**. Illessze be a beállításjegyzék és a fürt ügynökök DNS értékeit.

    ![A Visual Studio Team Services - Build adattár-konfiguráció](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Ahogyan az előző oldalon, kattintson a **zárolási** docker.password jelölőnégyzetet. A beállítás akkor fontos, hogy az új jelszó.
    >

### <a name="define-the-release-workflow"></a>A kiadási munkafolyamatokat

A kiadási munkafolyamat két feladatot a hozzáadott tevődik össze.

1. Egy feladat használatával is biztonságosan átmásolhatja a compose-fájlt egy *üzembe helyezése* mappájába, a Docker Swarm fő csomóponttal, az előzőekben konfigurált SSH-kapcsolat használatával. Részleteket a következő képernyő jelenik meg.
    
    Forrásmappa: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![A Visual Studio Team Services - kiadás SCP-je](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Egy második tevékenység futtatásához egy bash-parancs végrehajtása konfigurálása `docker` és `docker stack deploy` parancsok a fő csomópont. Részleteket a következő képernyő jelenik meg.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![A Visual Studio Team Services - kiadás Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    A parancsot a fő végrehajtása használja a Docker parancssori felületén és a Docker-Compose CLI a következő feladatokat végezheti el:

    - Jelentkezzen be az Azure container registrybe (a definiált három hozhat létre változókat használ a **változók** lap)
    - Adja meg a **DOCKER_HOST** változó dolgozhat a Swarm-végponthoz (: 2375-ös)
    - Keresse meg a *üzembe helyezése* , amely az előző biztonságos másolási tevékenység által létrehozott és a docker-compose.yml fájlt tartalmazó mappa 
    - Hajtsa végre `docker stack deploy` parancsokat, amelyek az új rendszerképeket és a tárolók létrehozásához.

    >[!IMPORTANT]
    > Ahogyan az az előző képernyőre, hagyja a **STDERR-en sikertelen** jelölőnégyzet nincs bejelölve. Ez a beállítás lehetővé teszi számunkra, hogy végezze el a kibocsátási folyamat miatt `docker-compose` több diagnosztikai üzeneteket jelenít meg például tárolók leállítása vagy törlése, a standard hibakimenet. Jelölje be a jelölőnégyzetet, ha Visual Studio Team Services-jelentések, hogy a kiadás során hibák jelentkeztek akkor is, ha minden megfelelően működik.
    >
3. Az új kiadási definíció mentéséhez.

## <a name="step-4-test-the-cicd-pipeline"></a>4. lépés: A CI/CD-folyamat tesztelése

Most, hogy végzett a konfiguráció, legyen az új CI/CD-folyamat teszteléséhez. A legegyszerűbben úgy, hogy tesztelje, hogy frissítse a forráskódot, és véglegesítse a módosításokat a GitHub-tárházba. Néhány másodperccel azután, küldje le a kódot, megjelenik egy új buildet a Visual Studio Team Servicesben futó. Sikeres befejezést követően új kiadás akkor aktiválódik, és az Azure Container Service-fürtön az alkalmazás új verziójának telepítése.

## <a name="next-steps"></a>További lépések

* CI/CD a Visual Studio Team Services szolgáltatással kapcsolatos további információkért lásd: a [hozhat létre a vsts-ben – áttekintés](https://www.visualstudio.com/docs/build/overview).
* ACS Engine kapcsolatos további információkért lásd: a [ACS Engine GitHub-adattárat](https://github.com/Azure/acs-engine).
* Docker Swarm módú kapcsolatos további információkért lásd: a [Docker Swarm üzemmód áttekintése](https://docs.docker.com/engine/swarm/).
