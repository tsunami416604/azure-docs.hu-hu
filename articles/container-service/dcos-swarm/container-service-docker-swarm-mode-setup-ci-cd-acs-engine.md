---
title: (ELAVULT) CI/CD – az Azure Container Service-motor és a Swarm mód
description: Az Azure Container Service-motor használja a Docker Swarm módú, egy Azure Container Registry és Azure DevOps, hogy folyamatosan többtárolós .NET Core-alkalmazásokhoz
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 4befcbb6b2297c6f80fb577b1fe22053a8c77ada
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331078"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(ELAVULT) Az ACS-motor és a Docker Swarm módú Azure DevOps használatával üzembe helyezhető egy többtárolós alkalmazást az Azure Container Service a teljes CI/CD-folyamat

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Ez a cikk alapján [teljes CI/CD-folyamat üzembe helyezéséhez egy többtárolós alkalmazást az Azure Container Service Docker Swarm használatával az Azure DevOps-](container-service-docker-swarm-setup-ci-cd.md) dokumentációja*

Napjainkban az egyik legnagyobb kihívást az jelenti a modern alkalmazások fejlesztése során folyamatban van, hogy ezek az alkalmazások folyamatosan. Ebből a cikkből megismerheti, hogyan teljes folyamatos integráció és készregyártás (CI/CD) folyamat használatával megvalósításához: 
* Az Azure Container Service-motor a Docker Swarm mód
* Azure Container Registry
* Azure DevOps

Ez a cikk egy egyszerű alkalmazást, a rendelkezésre álló alapján [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux), fejlett ASP.NET Core használatával. Az alkalmazás négy különböző szolgáltatást épül fel: három webes API-k és a egy webes előtérrendszert:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

A célja az alkalmazást egy Docker Swarm módú fürtöt az Azure DevOps folyamatosan továbbítását. Az alábbi ábra a folyamatos teljesítési folyamat részletesen:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

A következő lépések röviden:

1. A forráskód adattára kódmódosítás elkötelezettek (Itt GitHub) 
2. GitHub aktiválnak egy buildet, az Azure DevOps 
3. Az Azure DevOps lekérdezi a legújabb verzióra a források, és létrehozza a lemezképeket, amelyek az alkalmazás összeállítása 
4. Az Azure DevOps egyes rendszerképet leküldi a Docker-tárolójegyzék létrehozása az Azure Container Registry szolgáltatással 
5. Az Azure DevOps elindít egy új kiadás 
6. A kiadás fut néhány parancsot SSH-val az Azure container service fő fürtcsomópontra 
7. A fürtön a docker Swarm módú lekéri a rendszerképet a legújabb verzióra 
8. Az alkalmazás új verziójának üzembe helyezett Docker Stack használatával 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt kell végrehajtani az alábbi feladatokat:

- [Hozzon létre egy Swarm módú fürtöt az Azure Container Service-ACS Engine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Csatlakozás a Swarm-fürthöz az Azure Container Service-ben](../container-service-connect.md)
- [Az Azure container registry létrehozása](../../container-registry/container-registry-get-started-portal.md)
- [Az Azure DevOps-szervezet és a létrehozott projekt rendelkezik](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [A GitHub-fiókjába a GitHub tárház elágaztatása](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Az Azure Container Service Docker Swarm-vezénylője örökölt önálló Swarmot használ. Az integrált [Swarm mód](https://docs.docker.com/engine/swarm/) (a Docker 1.12-es és újabb verzióiban) jelenleg nem támogatott vezénylő az Azure Container Service-ben. Ezért használjuk a [ACS Engine](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), Közösség által biztosított [gyorsindítási sablon](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), vagy egy Docker-megoldást a [Azure Marketplace-en](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>1. lépés: Az Azure DevOps-szervezet konfigurálása 

Ebben a szakaszban az Azure DevOps-szervezet konfigurálása. Az Azure fejlesztési és üzemeltetési szolgáltatások végpontjait, az Azure DevOps-projekt konfigurálásához kattintson a **beállítások** ikonra az eszköztárban, majd válassza a **szolgáltatások**.

![Nyissa meg a szolgáltatások végpontja](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Az Azure DevOps és az Azure-fiók csatlakoztatása

Állítsa be az Azure DevOps-projekt és az Azure-fiókjával.

1. Kattintson a bal oldali **új szolgáltatásvégpont** > **Azure Resource Manager**.
2. Azure DevOps-használata az Azure-fiókja hitelesítéséhez, válassza ki a **előfizetés** kattintson **OK**.

    ![Az Azure DevOps - Azure engedélyezése](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Csatlakozás az Azure DevOps- és GitHub

Az Azure DevOps-projekt és a GitHub-fiók közötti kapcsolat beállítása.

1. Kattintson a bal oldali **új szolgáltatásvégpont** > **GitHub**.
2. A GitHub-fiók használata az Azure DevOps-engedélyezésére, kattintson a **engedélyezés** , és kövesse a megjelenő ablakban.

    ![Az Azure DevOps - GitHub engedélyezése](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Az Azure DevOps csatlakozhat az Azure Container Service-fürt

Az utolsó előtt a CI/CD-folyamat az első lépésekre külső kapcsolatok a Docker Swarm-fürt beállítása az Azure-ban. 

1. A Docker Swarm-fürt esetében típusú végpont hozzáadása **SSH**. Ezután írja be a SSH-kapcsolati adatok a Swarm-fürt (fő csomópontot).

    ![Azure DevOps - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Minden konfiguráció a most történik. Az alábbi lépésekkel hoz létre a CI/CD folyamatot, amely az alkalmazás a Docker Swarm-fürt létrehozása és telepítése. 

## <a name="step-2-create-the-build-pipeline"></a>2. lépés: A létrehozási folyamat létrehozása

Ebben a lépésben egy buildelési folyamat beállítása az Azure DevOps-projekt és a build munkafolyamat definiálása a tárolólemezképek

### <a name="initial-pipeline-setup"></a>Kezdeti folyamat beállítása

1. Felépítési folyamat létrehozása az Azure DevOps-projekt csatlakozhat, és kattintson a **Build & Release**. Az a **Builddefinícióiról** területén kattintson **+ új**. 

    ![Az Azure DevOps - új folyamat létrehozása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Válassza ki a **üres folyamat**.

    ![Az Azure DevOps - új üres folyamat létrehozása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Kattintson a **változók** lapra, és hozzon létre két új változót: **RegistryURL** és **AgentURL**. Illessze be a beállításjegyzék és a fürt ügynökök DNS értékeit.

    ![Az Azure DevOps - Buildkonfigurációhoz változók](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Az a **definíciókat hozhat létre** lapon nyissa meg a **eseményindítók** fülre, és konfigurálja a folyamatos integráció használata az elágazást, amelyet az Előfeltételek MyShop projekt build. Ezután válassza ki **Batch-módosítások**. Győződjön meg arról, hogy kiválasztotta *docker-linux* , a **specifikáció ágban**.

    ![Az Azure DevOps - Build adattár-konfiguráció](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Végül kattintson a **beállítások** fülre, és konfigurálja az alapértelmezett ügynök üzenetsor, melyben **üzemeltetett Linux előzetes**.

    ![Az Azure DevOps - Gazdagépügynök-konfigurálási](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>A build munkafolyamatokat
A következő lépéseket a build munkafolyamat határozza meg. Először a forrás a szabályzat konfigurálása. Ehhez válassza ki a **GitHub** és a **tárház** és **ág** (docker-linux).

![Az Azure DevOps - konfigurálása kód forrása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Nincsenek hozhat létre öt tárolórendszerképeket a *MyShop* alkalmazás. Egyes rendszerképek a docker-fájlban található meg a projekt használatával lett összeállítva:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Szüksége lesz az egyes rendszerképek, egy a rendszerkép létrehozásához, és egy, a rendszerkép leküldése az Azure container registry a két Docker lépést. 

1. A build-munkafolyamat egy lépés hozzáadásához kattintson **+ Add build lépés** válassza **Docker**.

    ![Az Azure DevOps - létrehozási lépések hozzáadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Az egyes lemezképek konfigurálása egy lépést, amely használja a `docker build` parancsot.

    ![Az Azure DevOps - Docker Build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    A létrehozási műveletet, válassza ki az Azure Container Registrybe a **állítson össze egy rendszerképet** művelet, és a docker-fájl, amely meghatározza az egyes rendszerképek. Állítsa be a **Working Directory** , a docker-fájlban gyökérkönyvtár meghatározása a **rendszerkép neve**, válassza ki **közé tartozik a legújabb címke**.
    
    A rendszerkép nevének azt kell a következő formátumban: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Cserélje le **[NAME]** rendszerkép nevét:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Az egyes lemezképek, a második lépésben használó konfigurálása a `docker push` parancsot.

    ![Az Azure DevOps - Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    A leküldéses művelethez válassza ki az Azure container registry a **rendszerkép leküldése** művelet, adja meg a **Lemezképnév** , amely az előző lépést, és válassza ki a beépített **közé tartozik a legújabb címke**.

4. Miután konfigurálta a build és a leküldéses lépéseket az egyes öt rendszerképet, a build-munkafolyamat a három további lépéseket adhat hozzá.

   ![Az Azure DevOps - parancssori feladat hozzáadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. Egy parancssori feladatot, amely egy bash-szkript használ a *RegistryURL* a docker-compose.yml fájl RegistryURL változóval előfordulás. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Az Azure DevOps - frissítés összeállítás fájlt a beállításjegyzék URL-címe](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. Egy parancssori feladatot, amely egy bash-szkript használ a *AgentURL* a docker-compose.yml fájl AgentURL változóval előfordulás.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. Ez a feladat csökken, a buildösszetevőt a frissített Compose-fájlt, így használhatók a kiadásban. Részleteket a következő képernyő jelenik meg.

         ![Azure DevOps - Publish Artifact](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Azure DevOps - Publish Compose file](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Kattintson a **várólistára & mentése** a buildelési folyamat teszteléséhez.

   ![Az Azure DevOps - mentés és üzenetsorba](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Az Azure DevOps - új várólista](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Ha a **összeállítása** megfelelő, tekintse meg ezt a képernyőt kell:

  ![Az Azure DevOps - Build sikeres volt](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>3. lépés: A kibocsátási folyamat létrehozása

Az Azure DevOps lehetővé teszi, hogy [kiadások kezelheti a környezeteket](https://www.visualstudio.com/team-services/release-management/). Győződjön meg arról, hogy az alkalmazás üzemel, a különböző környezetekben (például fejlesztési, tesztelési, éles üzem előtti vagy éles) zökkenőmentes módon való folyamatos üzembe helyezés is engedélyezheti. Létrehozhat olyan környezetet, amely az Azure Container Service Docker Swarm módú fürtöt jelöli.

![Az Azure DevOps - az ACS-kiadás](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Kezdeti kiadás beállítása

1. Kiadási folyamatok létrehozásához kattintson a **kiadásokban** > **+ kiadás**

2. Az összetevő-forrás konfigurálásához kattintson **összetevők** > **hivatkozás egy összetevő forrás**. Ez a kiadás új folyamat itt összekapcsolása a build, amelyet az előző lépésben megadott. Ezt követően a docker-compose.yml fájl érhető el a kibocsátási folyamat során.

    ![Az Azure DevOps - Release-összetevők](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. A kiadási trigger konfigurálásához kattintson **eseményindítók** válassza **folyamatos üzembe helyezés**. Állítsa be az eseményindító összetevő azonos forrásból. Ez a beállítás biztosítja, hogy új kiadás elindul, amikor a létrehozás sikeresen befejeződik.

    ![Az Azure DevOps - kiadás eseményindítók](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. A kiadási változók konfigurálásához kattintson **változók** válassza **+ változó** három új változó létrehozása a beállításjegyzék információval: **docker.username**, **docker.password**, és **docker.registry**. Illessze be a beállításjegyzék és a fürt ügynökök DNS értékeit.

    ![Az Azure DevOps - Build adattár-konfiguráció](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Ahogyan az előző oldalon, kattintson a **zárolási** docker.password jelölőnégyzetet. A beállítás akkor fontos, hogy az új jelszó.
    >

### <a name="define-the-release-workflow"></a>A kiadási munkafolyamatokat

A kiadási munkafolyamat két feladatot a hozzáadott tevődik össze.

1. Egy feladat használatával is biztonságosan átmásolhatja a compose-fájlt egy *üzembe helyezése* mappájába, a Docker Swarm fő csomóponttal, az előzőekben konfigurált SSH-kapcsolat használatával. Részleteket a következő képernyő jelenik meg.
    
    Forrásmappa: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Az Azure DevOps - kiadás SCP-je](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Egy második tevékenység futtatásához egy bash-parancs végrehajtása konfigurálása `docker` és `docker stack deploy` parancsok a fő csomópont. Részleteket a következő képernyő jelenik meg.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Az Azure DevOps - kiadás Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    A parancsot a fő végrehajtása használja a Docker parancssori felületén és a Docker-Compose CLI a következő feladatokat végezheti el:

    - Jelentkezzen be az Azure container registrybe (a definiált három hozhat létre változókat használ a **változók** lap)
    - Adja meg a **DOCKER_HOST** változó dolgozhat a Swarm-végponthoz (: 2375-ös)
    - Keresse meg a *üzembe helyezése* , amely az előző biztonságos másolási tevékenység által létrehozott és a docker-compose.yml fájlt tartalmazó mappa 
    - Hajtsa végre `docker stack deploy` parancsokat, amelyek az új rendszerképeket és a tárolók létrehozásához.

    >[!IMPORTANT]
    > Ahogyan az az előző képernyőre, hagyja a **STDERR-en sikertelen** jelölőnégyzet nincs bejelölve. Ez a beállítás lehetővé teszi számunkra, hogy végezze el a kibocsátási folyamat miatt `docker-compose` több diagnosztikai üzeneteket jelenít meg például tárolók leállítása vagy törlése, a standard hibakimenet. Jelölje be a jelölőnégyzetet, ha az Azure DevOps-jelentések, hogy a kiadás során hibák jelentkeztek akkor is, ha minden megfelelően működik.
    >
3. Mentse az új kiadási folyamatot.

## <a name="step-4-test-the-cicd-pipeline"></a>4. lépés: A CI/CD-folyamat tesztelése

Most, hogy végzett a konfiguráció, legyen az új CI/CD-folyamat teszteléséhez. A legegyszerűbben úgy, hogy tesztelje, hogy frissítse a forráskódot, és véglegesítse a módosításokat a GitHub-tárházba. Néhány másodperccel azután, küldje le a kódot, látni fogja az Azure DevOps-ban futó új build. Sikeres befejezést követően új kiadás akkor aktiválódik, és az Azure Container Service-fürtön az alkalmazás új verziójának telepítése.

## <a name="next-steps"></a>További lépések

* CI/CD – Azure DevOps kapcsolatos további információkért lásd: a [áttekintése az Azure DevOps-Build](https://www.visualstudio.com/docs/build/overview).
* ACS Engine kapcsolatos további információkért lásd: a [ACS Engine GitHub-adattárat](https://github.com/Azure/acs-engine).
* Docker Swarm módú kapcsolatos további információkért lásd: a [Docker Swarm üzemmód áttekintése](https://docs.docker.com/engine/swarm/).
