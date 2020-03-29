---
title: (ELAVULT) CI/CD az Azure Container Service Engine és a Swarm móddal
description: Az Azure Container Service Engine használata a Docker Swarm móddal, egy Azure Container Registry-el és az Azure DevOps használatával folyamatosan többtárolós .NET Core alkalmazást biztosíthat
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 1ec7ece6f5afd1bbd2613ae08af04b82e8a156b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277914"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(ELAVULT) Teljes CI/CD-folyamat egy többtárolós alkalmazás üzembe helyezéséhez az Azure Container Service-en Az ACS Motorral és a Docker Swarm Móddal az Azure DevOps használatával

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Ez a cikk a [Teljes CI/CD-folyamaton alapul, amely egy többtárolós alkalmazást telepít az Azure Container Service szolgáltatásban a Docker Swarm szolgáltatással az Azure DevOps dokumentációhasználatával](container-service-docker-swarm-setup-ci-cd.md)*

Napjainkban a felhőmodern alkalmazások fejlesztése során az egyik legnagyobb kihívás az, hogy ezeket az alkalmazásokat folyamatosan szállítsa. Ebben a cikkben megtudhatja, hogyan valósíthat meg teljes folyamatos integrációs és üzembe helyezési (CI/CD) folyamatot a következő használatával: 
* Azure Container Service Engine Docker Raj móddal
* Azure Container Registry
* Azure DevOps


![MyShop mintaalkalmazás](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

A cél az, hogy ezt az alkalmazást folyamatosan egy Docker Swarm Mode fürtben, az Azure DevOps használatával. A következő ábra részletezi ezt a folyamatos szállítási folyamatot:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Itt van egy rövid magyarázat a lépéseket:

1. A kódmódosítások véglegesítésre kerülnek a forráskódtárban (itt, GitHub) 
2. A GitHub elindítja a buildet az Azure DevOps-ban 
3. Az Azure DevOps leveszi a források legújabb verzióját, és létrehozza az alkalmazást alkotó összes lemezképet 
4. Az Azure DevOps leküldéses minden egyes rendszerképet egy Docker-beállításjegyzék használatával létrehozott Azure Container Registry szolgáltatás 
5. Az Azure DevOps új kiadást indít el 
6. A kiadás futtat néhány parancsot az SSH használatával az Azure container service-fürt főcsomópontján 
7. A Docker Swarm mode a fürtön a képek legújabb verzióját kéri 
8. Az alkalmazás új verziója a Docker Stack használatával van telepítve 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt a következő feladatokat kell elvégeznie:

- [Rajmódú fürt létrehozása az Azure Container Service szolgáltatásban az ACS-motorral](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Csatlakozás a Swarm-fürthöz az Azure tárolószolgáltatásban](../container-service-connect.md)
- [Azure Container Registry létrehozása](../../container-registry/container-registry-get-started-portal.md)
- [Létrehozott egy Azure DevOps-szervezetet és projektet](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [A GitHub-tárház elágazása a GitHub-fiókhoz](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Az Azure Container Service Docker Swarm-vezénylője örökölt önálló Swarmot használ. Az integrált [Swarm mód](https://docs.docker.com/engine/swarm/) (a Docker 1.12-es és újabb verzióiban) jelenleg nem támogatott vezénylő az Azure Container Service-ben. Ezért az [ACS Engine-t](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), egy közösség által készített [gyorsindítási sablont](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)vagy egy Docker-megoldást használunk az [Azure Marketplace-en.](https://azuremarketplace.microsoft.com)
>

## <a name="step-1-configure-your-azure-devops-organization"></a>1. lépés: Az Azure DevOps-szervezet konfigurálása 

Ebben a szakaszban konfigurálja az Azure DevOps-szervezetet. Az Azure DevOps-szolgáltatások végpontjainak konfigurálásához az Azure DevOps-projektben kattintson a **Beállítások** ikonra az eszköztáron, és válassza a **Szolgáltatások**lehetőséget.

![Szolgáltatások végpontjának megnyitása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Azure DevOps- és Azure-fiók csatlakoztatása

Hozzon létre kapcsolatot az Azure DevOps-projekt és az Azure-fiók között.

1. A bal oldalon kattintson az **Új szolgáltatásvégpont** > **Azure Resource Manager**elemre.
2. Ha engedélyezni szeretné az Azure DevOps számára az Azure-fiókkal való együttműködésre, válassza ki az **előfizetést,** és kattintson az **OK**gombra.

    ![Azure DevOps – Az Azure engedélyezése](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Az Azure DevOps és a GitHub csatlakoztatása

Hozzon létre kapcsolatot az Azure DevOps-projekt és a GitHub-fiók között.

1. A bal oldalon kattintson az **Új szolgáltatásvégpont** > **GitHub**.
2. Ha engedélyezni szeretné az Azure DevOps számára a GitHub-fiókkal való munkát, kattintson az **Engedélyezés** gombra, és kövesse a megnyíló ablakban található eljárást.

    ![Azure DevOps – A GitHub engedélyezése](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Az Azure DevOps csatlakoztatása az Azure Container Service-fürthöz

A CI/CD-folyamatba való bejutás előtt az utolsó lépések az Azure-beli Docker Swarm-fürt külső kapcsolatainak konfigurálása. 

1. A Docker Swarm-fürthöz adjon hozzá egy **SSH**típusú végpontot. Ezután adja meg a Swarm-fürt (főcsomópont) SSH-kapcsolati adatait.

    ![Azure DevOps - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Minden konfiguráció befejeződött most. A következő lépésekben hozza létre a CI/CD-folyamatot, amely létrehozza és telepíti az alkalmazást a Docker Swarm fürtre. 

## <a name="step-2-create-the-build-pipeline"></a>2. lépés: A buildfolyamat létrehozása

Ebben a lépésben létrehoz egy buildelési folyamatot az Azure DevOps-projekthez, és meghatározza a tárolórendszerképek buildelési munkafolyamatát

### <a name="initial-pipeline-setup"></a>Kezdeti csővezeték beállítása

1. Build-folyamat létrehozásához csatlakozzon az Azure DevOps-projekthez, és kattintson **a Build & Release parancsra.** A **Build definitions (Definíciók buildelése)** csoportban kattintson a **+ Új**gombra. 

    ![Azure DevOps – Új buildfolyamat](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Válassza az **Üres folyamat lehetőséget**.

    ![Azure DevOps – Új üres buildfolyamat](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Ezután kattintson a **Változók** fülre, és hozzon létre két új változót: **RegistryURL** és **AgentURL**. Illessze be a rendszerleíró adatbázis és a fürtügynökök DNS-ének értékeit.

    ![Azure DevOps – Változók konfigurálásának létrehozása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. A **Definíciók összeállítása** lapon nyissa meg az **Eseményindítók** lapot, és konfigurálja úgy a buildet, hogy az előfeltételekben létrehozott MyShop-projekt elágazásával folyamatos integrációt használjon. Ezután válassza a **Kötegelt módosítások lehetőséget.** Győződjön meg arról, hogy a *docker-linuxot* választja **branch specifikációként.**

    ![Azure DevOps – Tárház konfigurációjának összeállítása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Végül kattintson a **Beállítások** fülre, és konfigurálja az Alapértelmezett ügynök várólistát **a Szolgáltatott Linux előzetes verzióra.**

    ![Azure DevOps – gazdaügynök-konfiguráció](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>A létrehozási munkafolyamat meghatározása
A következő lépések határozzák meg a buildmunkafolyamatot. Először konfigurálnia kell a kód forrását. Ehhez válassza a **GitHub** és a **tárház** és **ág** (docker-linux).

![Azure DevOps – Kódforrás konfigurálása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

A *MyShop* alkalmazáshoz öt tárolórendszerképet kell készíteni. Minden lemezkép a projektmappákban található Docker-fájl használatával épül fel:

* ProductsApi (TermékekApi)
* Proxy
* Minősítési api
* AjánlásokApi
* ShopFront

Minden lemezképhez két Docker-lépésre van szükség, egy a rendszerkép létrehozásához, és egy a rendszerkép leküldése az Azure-tároló beállításjegyzékében. 

1. A buildmunkafolyamat egy lépésének hozzáadásához kattintson a **+ Build lépés hozzáadása** gombra, és válassza a **Docker**lehetőséget.

    ![Azure DevOps – Build-lépések hozzáadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Minden lemezképhez konfiguráljon egy `docker build` lépést, amely a parancsot használja.

    ![Azure DevOps – Docker build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    A build művelethez válassza ki az Azure Container Registry, a **rendszerkép létrehozása** műveletet, és a Dockerfile, amely meghatározza az egyes rendszerképek. Állítsa be a **munkakönyvtárat** Dockerfile gyökérkönyvtárként, adja meg a **képnevét**, és válassza **a Legújabb címke felvétele**lehetőséget.
    
    A kép nevének a következő ```$(RegistryURL)/[NAME]:$(Build.BuildId)```formátumban kell lennie: . Cserélje le **a(z) [NAME]** fájlt a következő képre:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Minden lemezképhez konfiguráljon egy `docker push` második lépést, amely a parancsot használja.

    ![Azure DevOps – Docker push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    A leküldéses művelethez válassza ki az Azure-tároló beállításjegyzékét, a **Leküldéses egy lemezkép** műveletet, írja be az előző lépésben beépített **lemezképnevet,** és válassza a **Legújabb címke belefoglalása**lehetőséget.

4. Miután konfigurálta a build- és leküldéses lépéseket mind az öt lemezképhez, adjon hozzá további három lépést a buildmunkafolyamatban.

   ![Azure DevOps – Parancssori feladat hozzáadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Olyan parancssori feladat, amely bash parancsfájlt használ a docker-compose.yml fájl *RegistryURL* előfordulásának a RegistryURL változóval való lecserélésére. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps – A compose fájl frissítése a rendszerleíró adatbázis URL-címével](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Olyan parancssori feladat, amely bash parancsfájlt használ a docker-compose.yml fájl *AgentURL* előfordulásának az AgentURL változóval való lecserélésére.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Olyan feladat, amely a frissített Összeállítási fájlt buildösszetevőként dobja el, hogy a kiadásban használható legyen. A részleteket lásd a következő képernyőn.

      ![Azure DevOps – műtermék közzététele](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps – Írásfájl közzététele](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Kattintson **& várólista mentése** gombra a buildfolyamat teszteléséhez.

   ![Azure DevOps – Mentés és várólista](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps – új várólista](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Ha a **Build** helyes, akkor ezt a képernyőt kell látnia:

   ![Azure DevOps – A build elvégzett műveletek](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>3. lépés: A kiadási folyamat létrehozása

Az Azure DevOps lehetővé teszi a [kiadások kezelését a különböző környezetekben.](https://www.visualstudio.com/team-services/release-management/) Engedélyezheti a folyamatos üzembe helyezést annak érdekében, hogy az alkalmazás zökkenőmentesen legyen telepítve a különböző környezetekben (például a fejlesztés, a tesztelés, az üzem előtti és az éles környezetben). Létrehozhat egy olyan környezetet, amely az Azure Container Service Docker Swarm Mode fürtöt képviseli.

![Azure DevOps – Kiadás az ACS-nek](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Kezdeti kiadás beállítása

1. Kiadási folyamat létrehozásához kattintson a Kiadások + Kiadás **gombra.** > **+ Release**

2. A műtermék-forrás konfigurálásához kattintson **a Műtermékek** > **kapcsolódás egy műtermék forrása**elemre. Itt kapcsolja össze ezt az új kiadási folyamatot az előző lépésben megadott buildtel. Ezt követően a docker-compose.yml fájl elérhető a kiadási folyamatban.

    ![Azure DevOps – összetevők kiadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. A kiadási eseményindító konfigurálásához kattintson az **Eseményindítók gombra,** és válassza **a Folyamatos telepítés**lehetőséget. Állítsa be az eseményindítót ugyanarra a műtermék-forrásra. Ez a beállítás biztosítja, hogy egy új kiadás elindul, amikor a build sikeresen befejeződik.

    ![Azure DevOps – kibocsátási eseményindítók](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. A kiadási változók konfigurálásához kattintson a **Változók** elemre, és válassza a **+Változó** lehetőséget, ha három új változót szeretne létrehozni a rendszerleíró adatbázis adataival: **docker.username**, **docker.password**és **docker.registry**. Illessze be a rendszerleíró adatbázis és a fürtügynökök DNS-ének értékeit.

    ![Azure DevOps – Tárház konfigurációjának összeállítása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Ahogy az előző képernyőn látható, kattintson a **Lock** jelölőnégyzetre a docker.password-ben. Ez a beállítás fontos a jelszó korlátozása érdekében.
    >

### <a name="define-the-release-workflow"></a>A kiadási munkafolyamat meghatározása

A kiadási munkafolyamat két hozzáadott feladatból áll.

1. Konfiguráljon egy feladatot úgy, hogy biztonságosan másolja az írásfájlt a Docker-sraj főcsomópontegy *központi mappájába* a korábban konfigurált SSH-kapcsolat használatával. A részleteket lásd a következő képernyőn.
    
    Forrás mappa:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps – SCP kiadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Konfiguráljon egy második feladatot egy `docker` bash `docker stack deploy` parancs futtatásához és a fő csomóponton lévő parancsok végrehajtásához. A részleteket lásd a következő képernyőn.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps – Bash kiadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    A főoldalon végrehajtott parancs a Docker CLI-t és a Docker-Compose CLI-t használja a következő feladatok elvégzéséhez:

   - Jelentkezzen be az Azure container beállításjegyzékbe (három, a **Változók** lapon definiált buildváltozót használ)
   - A Raj végponttal való munka **DOCKER_HOST** meghatározása (:2375)
   - Keresse meg az előző biztonságos másolási feladat által létrehozott és a docker-compose.yml fájlt tartalmazó *központi telepítésmappát* 
   - Hajtson végre `docker stack deploy` parancsokat, amelyek lekérik az új képeket, és létrehozzák a tárolókat.

     >[!IMPORTANT]
     > Ahogy az előző képernyőn látható, hagyja bejelölve a **Fail on STDERR jelölőnégyzetet.** Ez a beállítás lehetővé teszi `docker-compose` számunkra, hogy befejezzuk a kiadási folyamatot, mivel a szabványos hibakimeneten több diagnosztikai üzenetet nyomtatunk, például a tárolók leállnak vagy törlődnek. Ha bejelöli a jelölőnégyzetet, az Azure DevOps jelenti, hogy a kiadás során hibák történtek, még akkor is, ha minden jól megy.
     >
3. Mentse ezt az új kiadási folyamatot.

## <a name="step-4-test-the-cicd-pipeline"></a>4. lépés: A CI/CD-folyamat tesztelése

Most, hogy végzett a konfigurációval, itt az ideje, hogy tesztelje ezt az új CI/CD-folyamatot. A tesztelés legegyszerűbb módja a forráskód frissítése és a módosítások véglegesítése a GitHub-tárházba. Néhány másodperccel a kód leküldése után egy új build fut az Azure DevOps-ban. Sikeres befejezése után egy új kiadás aktiválódik, és az alkalmazás új verzióját telepíti az Azure Container Service-fürtön.

## <a name="next-steps"></a>További lépések

* Az Azure DevOps-szal való CI/CD-ről az [Azure Pipelines dokumentációs](/azure/devops/pipelines/?view=azure-devops) cikkében olvashat bővebben.
* Az ACS-motorról további információt az [ACS Engine GitHub-tárházban](https://github.com/Azure/acs-engine)talál.
* A Docker Swarm módról a [Docker Swarm mód áttekintése című témakörben olvashat bővebben.](https://docs.docker.com/engine/swarm/)
