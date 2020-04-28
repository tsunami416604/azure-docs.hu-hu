---
title: ELAVULT CI/CD Azure Container Service motorral és Swarm móddal
description: A Azure Container Service motort a Docker Swarm Mode, egy Azure Container Registry és az Azure DevOps segítségével folyamatosan, több tárolós .NET Core-alkalmazás használatával kézbesítheti
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 1ec7ece6f5afd1bbd2613ae08af04b82e8a156b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277914"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>ELAVULT Teljes CI/CD-folyamat egy többtárolós alkalmazás üzembe helyezéséhez Azure Container Service az ACS motor és a Docker Swarm mód használatával az Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Ez a cikk [teljes körű CI/CD-folyamaton alapul, és több tárolós alkalmazást helyez üzembe Azure Container Service a Docker Swarm használatával az Azure DevOps](container-service-docker-swarm-setup-ci-cd.md) dokumentációjában*

Napjainkban a Felhőbeli modern alkalmazások fejlesztése során az egyik legnagyobb kihívás az alkalmazások folyamatos megvalósítása. Ebből a cikkből megtudhatja, hogyan valósítható meg a teljes folyamatos integrációs és üzembe helyezési (CI/CD) folyamat a következő használatával: 
* Azure Container Service-motor Docker Swarm módban
* Azure Container Registry
* Azure DevOps


![MyShop-minta alkalmazása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

A cél az alkalmazás folyamatos továbbítása Docker Swarm módú fürtben az Azure DevOps használatával. Az alábbi ábra a folyamatos kézbesítési folyamatot részletezi:

![MyShop-minta alkalmazása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Íme a lépések rövid ismertetése:

1. A kód módosításai elkötelezettek a forráskód-tárházban (itt a GitHub) 
2. A GitHub elindítja a buildet az Azure DevOps 
3. Az Azure DevOps lekéri a források legújabb verzióját, és az alkalmazást alkotó összes lemezképet felépíti 
4. Az Azure DevOps minden rendszerképet leküld egy, a Azure Container Registry szolgáltatás használatával létrehozott Docker-beállításjegyzékbe. 
5. Az Azure DevOps új kiadást indít el 
6. A kiadás bizonyos parancsokat futtat az SSH használatával az Azure Container Service-fürt főcsomópontján 
7. A Docker Swarm mód a fürtön lekéri a lemezképek legújabb verzióját 
8. Az alkalmazás új verziója a Docker stack használatával lett telepítve. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt el kell végeznie a következő feladatokat:

- [Swarm módú fürt létrehozása Azure Container Serviceban ACS motorral](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Csatlakozás a Swarm-fürthöz az Azure tárolószolgáltatásban](../container-service-connect.md)
- [Azure Container Registry létrehozása](../../container-registry/container-registry-get-started-portal.md)
- [Létre kell hoznia egy Azure DevOps-szervezetet és-projektet](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [A GitHub-tárház elágazása a GitHub-fiókhoz](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Az Azure Container Service Docker Swarm-vezénylője örökölt önálló Swarmot használ. Az integrált [Swarm mód](https://docs.docker.com/engine/swarm/) (a Docker 1.12-es és újabb verzióiban) jelenleg nem támogatott vezénylő az Azure Container Service-ben. Ezért az [ACS motort](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), egy Közösség által biztosított gyors üzembe helyezési [sablont](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)vagy egy Docker-megoldást használunk az [Azure piactéren](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>1. lépés: az Azure DevOps-szervezet konfigurálása 

Ebben a szakaszban az Azure DevOps-szervezetet konfigurálja. Az Azure DevOps Services-végpontok konfigurálásához az Azure DevOps-projektben kattintson az eszköztár **Beállítások** ikonjára, majd válassza a **szolgáltatások**lehetőséget.

![A szolgáltatási végpont megnyitása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Az Azure DevOps és az Azure-fiók összekötése

Hozzon létre egy kapcsolatot az Azure DevOps-projekt és az Azure-fiókja között.

1. A bal oldalon kattintson az **új szolgáltatási végpont** > **Azure Resource Manager**elemre.
2. Az Azure-DevOps Azure-fiókkal való működésének engedélyezéséhez válassza ki az **előfizetését** , majd kattintson **az OK**gombra.

    ![Azure DevOps – Azure engedélyezése](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Az Azure DevOps és a GitHub összekötése

Hozzon létre egy kapcsolatot az Azure DevOps-projekt és a GitHub-fiók között.

1. A bal oldalon kattintson az **új szolgáltatási végpont** > **GitHub**elemre.
2. Ha engedélyezni szeretné, hogy az Azure DevOps működjön a GitHub-fiókkal, kattintson az **Engedélyezés** elemre, és kövesse a megnyíló ablakban található eljárást.

    ![Azure DevOps – a GitHub engedélyezése](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Az Azure DevOps összekötése a Azure Container Service-fürttel

A CI/CD-folyamatba való beolvasást megelőző lépések a külső kapcsolatok konfigurálása a Docker Swarm-fürthöz az Azure-ban. 

1. A Docker Swarm-fürthöz adjon hozzá egy **SSH**típusú végpontot. Ezután adja meg a Swarm-fürt (főcsomópont) SSH-kapcsolatok információit.

    ![Azure DevOps – SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Az összes konfiguráció most elkészült. A következő lépésekben létre kell hoznia a CI/CD folyamatot, amely létrehozza és telepíti az alkalmazást a Docker Swarm-fürtre. 

## <a name="step-2-create-the-build-pipeline"></a>2. lépés: a létrehozási folyamat létrehozása

Ebben a lépésben létrehoz egy Build-folyamatot az Azure DevOps-projekthez, és meghatározza a tároló-lemezképek létrehozási munkafolyamatát.

### <a name="initial-pipeline-setup"></a>Kezdeti folyamat beállítása

1. Build folyamat létrehozásához kapcsolódjon az Azure DevOps-projekthez, és kattintson a **build & kiadás**elemre. A **Build fogalommeghatározások** szakaszban kattintson az **+ új**elemre. 

    ![Azure DevOps – új build-folyamat](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Válassza ki az **üres folyamatot**.

    ![Azure DevOps – új üres build-folyamat](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Ezután kattintson a **változók** fülre, és hozzon létre két új változót: **RegistryURL** és **AgentURL**. Illessze be a beállításjegyzék és a fürt ügynökök DNS-értékeit.

    ![Azure DevOps – változók konfigurációjának összeállítása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. A **Build-definíciók** lapon nyissa meg az **Eseményindítók** lapot, és konfigurálja úgy a buildet, hogy a folyamatos integrációt használja az előfeltételekben létrehozott MyShop-projekt elágazásával. Ezután válassza a **Batch Changes**elemet. Ügyeljen rá, hogy a *Docker-Linux* elemet adja meg **ág-specifikációként**.

    ![Azure DevOps – adattár konfigurációjának létrehozása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Végül kattintson a **Beállítások** fülre, és konfigurálja az alapértelmezett ügynök-várólistát az **üzemeltetett Linux előzetes**verziójára.

    ![Azure DevOps – gazdagép-ügynök konfigurációja](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>A Build munkafolyamat megadása
A következő lépések a Build munkafolyamatot határozzák meg. Először konfigurálnia kell a kód forrását. Ehhez válassza a **GitHub** és az **adattár** és **ág** (Docker-Linux) lehetőséget.

![Azure DevOps – forráskód konfigurálása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

A *MyShop* alkalmazáshoz öt tároló lemezképet kell létrehozni. Minden rendszerkép a projekt mappáiban található Docker használatával készült:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Az egyes rendszerképekhez két Docker-lépést kell megadnia, egyet a rendszerkép létrehozásához, egyet pedig a rendszerkép Azure Container registryben való leküldéséhez. 

1. Ha hozzá szeretne adni egy lépést a Build munkafolyamatban, kattintson a **+ Build lépés hozzáadása** és a **Docker**elemre.

    ![Azure DevOps – Build lépések hozzáadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Minden rendszerkép esetében állítson be egy lépést, amely `docker build` a parancsot használja.

    ![Azure DevOps – Docker-Build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    A Build művelethez válassza ki a Azure Container Registry, a **rendszerkép létrehozása** műveletet, valamint az egyes képeket definiáló Docker. Állítsa be a **munkakönyvtárat** Docker gyökérkönyvtárként, adja meg a **rendszerkép nevét**, és válassza a **Belefoglalás a legújabb címkét**.
    
    A rendszerkép nevének a következő formátumúnak kell lennie ```$(RegistryURL)/[NAME]:$(Build.BuildId)```:. Cserélje le a **[name]** nevet a rendszerkép nevére:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Minden rendszerkép esetében konfigurálja a `docker push` parancsot használó második lépést.

    ![Azure DevOps – Docker-leküldés](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    A leküldéses művelethez válassza ki az Azure Container registryt, a **lemezkép leküldése** műveletet, írja be az előző lépésben létrehozott **lemezkép nevét** , és válassza a **Belefoglalás a legújabb címkét**.

4. Miután konfigurálta a létrehozási és leküldéses lépéseket az egyes öt lemezképekhez, adjon hozzá három további lépést a Build munkafolyamatban.

   ![Azure DevOps – parancssori feladat hozzáadása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Egy olyan parancssori feladat, amely bash-parancsfájlt használ a Docker-compose. YML fájl *RegistryURL* előfordulása helyett a RegistryURL változóval. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps – az összeállítási fájl frissítése a beállításjegyzék URL-címével](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Egy olyan parancssori feladat, amely bash-parancsfájlt használ a Docker-compose. YML fájl *AgentURL* előfordulása helyett a AgentURL változóval.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Egy feladat, amely a frissített összeállítási fájlt felépíti összetevőként, így a kiadásban is használható. A részletekért tekintse meg a következő képernyőt.

      ![Azure DevOps – összetevő közzététele](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps – összeállítási fájl közzététele](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Kattintson a **mentés & üzenetsor** elemre a létrehozási folyamat teszteléséhez.

   ![Azure DevOps – mentés és üzenetsor](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps – új üzenetsor](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Ha a **Build** helyes, meg kell tekintenie a képernyőt:

   ![Azure-DevOps – sikeres létrehozás](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>3. lépés: a kiadási folyamat létrehozása

Az Azure DevOps lehetővé teszi a [kiadások különböző környezetekben való kezelését](https://www.visualstudio.com/team-services/release-management/). A folyamatos üzembe helyezés lehetővé teszi, hogy az alkalmazás a különböző környezetekben (például a fejlesztési, tesztelési, üzem előtti és éles környezetben) zökkenőmentes módon legyen telepítve. Létrehozhat egy olyan környezetet, amely a Azure Container Service Docker Swarm módú fürtöt jelképezi.

![Azure DevOps – kiadás az ACS-be](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Kezdeti kiadás beállítása

1. Kiadási folyamat létrehozásához kattintson a **kiadások** > **+ kiadás** elemre.

2. Az összetevő forrásának konfigurálásához kattintson **az** > összetevők**csatolása az összetevők forrásához**elemre. Itt csatolja az új kiadási folyamatot az előző lépésben megadott buildhez. Ezt követően a Docker-compose. YML fájl elérhető a kiadási folyamatban.

    ![Azure DevOps – kiadási összetevők](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. A kiadási eseményindító konfigurálásához kattintson az **Eseményindítók** elemre, és válassza a **folyamatos üzembe helyezés**lehetőséget. Állítsa az triggert ugyanarra az összetevő-forrásra. Ez a beállítás biztosítja, hogy új kiadás induljon el, amikor a Build sikeresen befejeződik.

    ![Azure DevOps – kiadási eseményindítók](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. A kiadási változók konfigurálásához kattintson a **változók** elemre, és válassza a **+ változó** lehetőséget, hogy három új változót hozzon létre a beállításjegyzék adataival: **Docker. username**, **Docker. password**és **Docker. Registry**. Illessze be a beállításjegyzék és a fürt ügynökök DNS-értékeit.

    ![Azure DevOps – adattár konfigurációjának létrehozása](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Ahogy az előző képernyőn is látható, kattintson a **zárolás** jelölőnégyzetre a Docker. password parancsban. Ez a beállítás fontos a jelszó korlátozásához.
    >

### <a name="define-the-release-workflow"></a>A kiadási munkafolyamat megadása

A kiadási munkafolyamat két felvenni kívánt feladatból áll.

1. Konfiguráljon egy feladatot úgy, hogy a Docker Swarm főcsomópontján lévő, a korábban konfigurált SSH-kapcsolatok használatával biztonságosan másolja a levélírás fájlt egy *központi telepítés* mappájába. A részletekért tekintse meg a következő képernyőt.
    
    Forrás mappája:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps – kiadási SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Konfiguráljon egy második feladatot egy bash-parancs futtatásához `docker` , `docker stack deploy` és futtassa a parancsot a fő csomóponton. A részletekért tekintse meg a következő képernyőt.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps – kiadási bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    A főkiszolgálón végrehajtott parancs a Docker CLI-t és a Docker-levélírás CLI-t használja a következő feladatok elvégzéséhez:

   - Jelentkezzen be az Azure Container registrybe (Ez három, a **változók** lapon definiált Build változót használ)
   - Adja meg a **DOCKER_HOST** változót a Swarm-végponttal való együttműködéshez (: 2375)
   - Navigáljon az előző biztonságos másolási feladat által létrehozott, a Docker-compose. YML fájlt tartalmazó *központi telepítés* mappájához. 
   - Az `docker stack deploy` új képeket lekérő és tárolókat létrehozó parancsok végrehajtása.

     >[!IMPORTANT]
     > Az előző képernyőn látható módon hagyja bejelölve a **sikertelen stderr** jelölőnégyzetet. Ez a beállítás lehetővé teszi a kiadási folyamat elvégzését, mert `docker-compose` számos diagnosztikai üzenetet, például tárolókat állítanak le vagy törölnek, a normál hiba kimenetén. Ha bejelöli a jelölőnégyzetet, az Azure DevOps a kiadás során hibákat észlelt, még akkor is, ha minden jól megy.
     >
3. Az új kiadási folyamat mentése.

## <a name="step-4-test-the-cicd-pipeline"></a>4. lépés: a CI/CD-folyamat tesztelése

Most, hogy elkészült a konfigurációval, itt az ideje, hogy tesztelje ezt az új CI/CD-folyamatot. A legkönnyebben tesztelhető, hogy frissítse a forráskódot, és véglegesítse a módosításokat a GitHub-tárházban. Néhány másodperc elteltével a kód elküldése után egy új Build fut az Azure DevOps. A sikeres befejezést követően új kiadást indít el a rendszer, és üzembe helyezi az alkalmazás új verzióját a Azure Container Service-fürtön.

## <a name="next-steps"></a>További lépések

* Az Azure DevOps CI/CD-vel kapcsolatos további információkért tekintse meg az [Azure-folyamatok dokumentációs](/azure/devops/pipelines/?view=azure-devops) cikkét.
* További információ az ACS motorról: ACS- [motor GitHub](https://github.com/Azure/acs-engine)-tárháza.
* A Docker Swarm módjával kapcsolatos további információkért lásd a [Docker Swarm üzemmódjának áttekintését](https://docs.docker.com/engine/swarm/).
