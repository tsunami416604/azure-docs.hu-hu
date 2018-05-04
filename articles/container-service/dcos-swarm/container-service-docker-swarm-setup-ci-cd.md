---
title: CI/CD-ről az Azure Tárolószolgáltatás és a Swarm
description: Azure Tárolószolgáltatás használja a Docker Swarm, egy Azure-tároló beállításkulcs, és a Visual Studio Team Services folyamatosan egy több tároló .NET Core alkalmazás képes biztosítani
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 81a07fdfe1c862bc30fb9d567db9a393c0610990
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Teljes CI/CD adatcsatorna egy Azure tárolószolgáltatás a Docker Swarmmal Visual Studio Team Services segítségével többszörös tároló alkalmazás központi telepítése

Egyik legnagyobb kihívás a felhő modern alkalmazások fejlesztése során ezeknek az alkalmazásoknak folyamatosan küldött alatt. Ebből a cikkből megismerheti, hogyan egy teljes folyamatos integrációt és az Azure Tárolószolgáltatás használata a Docker Swarm Azure tároló beállításjegyzék és a Visual Studio Team Services build (CI/CD) telepítési folyamat bevezetése, valamint a kiadáskezelés.

Ez a cikk alapján egy egyszerű alkalmazást, a rendelkezésre álló [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), az ASP.NET Core fejlett. Az alkalmazások négy különböző szolgáltatások állnak: három webes API-k és egy webes előtér:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

A célja, hogy az alkalmazás folyamatosan biztosítanak a Docker Swarm fürtön Visual Studio Team Services használatával. Az alábbi ábra az folyamatos kézbesítési adatcsatorna részletesen:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

A lépések rövid leírása itt található:

1. A forráskódraktárban kódmódosításokat elkötelezettek (Itt GitHub) 
2. GitHub elindítja a Visual Studio Team Services build 
3. Visual Studio Team Services lekérdezi a források legújabb verzióját, és létrehozza a lemezképeket, amelyek az alkalmazás összeállítása 
4. A Visual Studio Team Services az egyes lemezképek egy Docker beállításjegyzék, az Azure-tároló beállításjegyzék szolgáltatással létrehozott leküldéses értesítések 
5. A Visual Studio Team Services új verziót váltja ki. 
6. A kiadás néhány SSH használatával az Azure-tárolót szolgáltatás fő fürtcsomóponton parancsokat futtat 
7. A docker Swarm a fürtön a képek legújabb verzióját kéri le. 
8. Az alkalmazás új verziójának használatával a Docker Compose van telepítve 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt végre kell hajtania a következő feladatokat:

- [Swarm-fürt létrehozása az Azure Container Service-ben](container-service-deployment.md)
- [Csatlakozás a Swarm-fürthöz az Azure Container Service-ben](../container-service-connect.md)
- [Hozzon létre egy Azure-tárolót beállításjegyzék](../../container-registry/container-registry-get-started-portal.md)
- [Egy Visual Studio Team Services fiók és a team projekt létrehozása](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Elágazás a GitHub-tárházban, a GitHub-fiókba](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Egy Ubuntu (14.04 vagy 16.04) gépet is telepítve Docker kell. Ezen a számítógépen a build és kiadás folyamatok során használja a Visual Studio Team Services. Ez gépen létrehozásának egyik módja, hogy a lemezképpel találhatók a [Azure piactér](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>1. lépés: A Visual Studio Team Services-fiók konfigurálása 

Ebben a szakaszban konfigurálhatja a Visual Studio Team Services-fiók.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>A Visual Studio Team Services Linux build ügynök konfigurálása

Docker-képek létrehozására és az Azure-tárolót beállításjegyzékbe, a Visual Studio Team Services build leküldéses ezeket a lemezképeket, akkor regisztrálnia kell egy Linux-ügynök. Ezen telepítési lehetőség közül választhat:

* [Egy Linux-ügynök telepítése](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [A VSTS-ügynök futtatásához használt Docker](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>A Docker-integráció VSTS-kiterjesztés telepítése

A Microsoft biztosít egy VSTS bővítménye build a Docker használni, és felszabadíthatja a folyamatok. A bővítmény érhető el a [VSTS piactér](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Kattintson a **telepítése** VSTS-fiókját a bővítmény hozzáadása:

![A Docker-integráció telepítése](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

A rendszer felkéri a hitelesítő adatok használatával VSTS-fiókjához. 

### <a name="connect-visual-studio-team-services-and-github"></a>Csatlakoztassa a Visual Studio Team Services, GitHub

Állítsa be a kapcsolatot a VSTS-projektet és a GitHub-fiók között.

1. A Visual Studio Team Services projektben kattintson a **beállítások** ikonra az eszköztár, és válassza ki a **szolgáltatások**.

    ![A Visual Studio Team Services - külső kapcsolatot](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. Kattintson a bal oldali **új szolgáltatási végpont** > **GitHub**.

    ![A Visual Studio Team Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. A GitHub-fiók használható VSTS engedélyezésére, kattintson a **engedélyezés** és kövesse a megjelenő ablakban.

    ![A Visual Studio Team Services - engedélyezik a Githubon](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>VSTS csatlakozni az Azure-tárolót beállításjegyzék és Azure Tárolószolgáltatás-fürt

Az utolsó lépéseket azokat a CI/CD adatcsatorna elérése előtt kell a tároló beállításjegyzék és a Docker Swarm-fürt külső kapcsolatok konfigurálása az Azure-ban. 

1. Az a **szolgáltatások** a Visual Studio Team Services projekt beállítások hozzáadása típusú szolgáltatásvégpont **Docker beállításjegyzék**. 

2. A felugró ablakban nyílik meg adja meg az URL-címet, és az Azure-tárolót beállításjegyzék hitelesítő adatai.

    ![A Visual Studio Team Services - Docker beállításjegyzék](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. A Docker Swarm-fürt típusú végpont hozzáadása **SSH**. Majd adja meg az SSH csatlakozási adatait a Swarm-fürt.

    ![A Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

A konfiguráció most történik. A következő lépésekben a CI/CD-feldolgozási folyamat alapszik, és központilag telepíti az alkalmazást a Docker Swarm-fürt létrehozása. 

## <a name="step-2-create-the-build-definition"></a>2. lépés: A build definíció létrehozása

Ebben a lépésben állítson be egy build definitionfor a VSTS-projektet, és a létrehozási munkafolyamat megadása a tároló lemezképek

### <a name="initial-definition-setup"></a>Kezdeti beállításának

1. Build definíció létrehozása, csatlakozzon a Visual Studio Team Services-projektet, és kattintson **Build & kiadási**. 

2. Az a **definíciók Build** kattintson **+ új**. Válassza ki a **üres** sablont.

    ![A Visual Studio Team Services - új definíció létrehozása](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. A GitHub tárház forrását, ellenőrizze az új buildverziót konfigurálása **folyamatos integrációt**, és válassza ki az ügynök várólista, ahol regisztrálták-e a Linux-ügynök. Kattintson a **létrehozása** hozza létre a build definícióit.

    ![A Visual Studio Team Services - Build-definíció létrehozása](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. Az a **Build definíciók** lapon, először megnyitja a **tárház** lapot és a build az Előfeltételek című szakaszban létrehozott MyShop projekt elágazás használatára konfigurálja. Győződjön meg arról, hogy kiválassza *acs-dokumentumok* , a **alapértelmezett ágat**.

    ![Visual Studio Team Services - Build tárház konfigurációja](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. Az a **eseményindítók** lapra, konfigurálja a build minden véglegesítés után aktiválására. Válassza ki **folyamatos integrációt** és **módosítások kötegelt**.

    ![Visual Studio Team Services - Build eseményindító konfigurációja](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Adja meg a létrehozási munkafolyamat
A következő lépéseket a build munkafolyamat határozza meg. Öt tároló lemezképeit számára részére a *MyShop* alkalmazás. Minden egyes lemezképének összeállítása a Dockerfile a projektben található használatával:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Adjon hozzá két Docker lépéseket az egyes lemezképek, egy a lemezkép létrehozásához, és egy leküldéses a lemezkép az Azure-tárolót beállításjegyzékben kell. 

1. A build munkafolyamat egy lépés felvételéhez kattintson **+ Hozzáadás összeállítása lépés** válassza **Docker**.

    ![A Visual Studio Team Services - Build lépéseket adhat hozzá.](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. Az egyes lemezképek konfigurálása egy lépést, amely használja a `docker build` parancsot.

    ![A Visual Studio Team Services - Docker-Build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    A létrehozási műveletet, válassza ki az Azure-tárolót beállításjegyzék, a **lemezképet készít** művelet, és az egyes lemezképek definiáló Dockerfile. Állítsa be a **összeállítási környezet** , a Dockerfile gyökérkönyvtár, és adja meg a **Lemezképnév**. 
    
    Amint az előző képernyőn, a lemezkép neve kezdődhet URI-azonosítója az Azure-tárolót beállításjegyzék. (Is segítségével build változó parametrizálja a címke a kép, például az ebben a példában a build azonosítója.)

3. Az egyes lemezképek, a második lépésben használó konfigurálása a `docker push` parancsot.

    ![A Visual Studio Team Services - Docker leküldéses](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Leküldéses művelethez válassza ki az Azure-tárolót beállításjegyzék a **lemezkép leküldéses** művelet, és írja be a **kép neve** , amely az előző lépésben épül.

4. Miután konfigurálta a build és leküldéses lépéseket az egyes öt lemezképet, adja hozzá a két további lépés a build munkafolyamatban.

    a. A parancssori feladatot, amely egy bash parancsfájlt használ a *BuildNumber* a docker-compose.yml fájlt, és a jelenlegi számainak létrehozása azonosítóját. További részletek a következő képernyő jelenik meg.

    ![A Visual Studio Team Services - frissítés Compose fájl](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Ez a feladat elutasítja azokat a frissített Compose fájlt egy összeállítási összetevő, így használható a kiadásban. További részletek a következő képernyő jelenik meg.

    ![A Visual Studio Team Services - Compose közzététele fájl](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. Kattintson a **mentése** és a build definition neve.

## <a name="step-3-create-the-release-definition"></a>3. lépés: A kiadási definíció létrehozása

A Visual Studio Team Services lehetővé teszi [kiadásokban kezelése környezetek között](https://www.visualstudio.com/team-services/release-management/). Folyamatos üzembe helyezés, győződjön meg arról, hogy az alkalmazás telepítve van a különböző környezetekben (például a fejlesztői, tesztelési, éles üzem előtti és éles) zökkenőmentes úgy is engedélyezheti. Létrehozhat egy új környezetben, amely az Azure tároló szolgáltatás Docker Swarm-fürt jelöli.

![A Visual Studio Team Services - ACS verzióját](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Eredeti kiadásának beállítása

1. Egy kiadási definition létrehozásához kattintson a **kiadásokban** > **+ kiadás**

2. Az összetevő-forrás konfigurálásához kattintson **összetevők** > **hivatkozás egy összetevő forrás**. A build, amelyet az előző lépésben megadott itt, csatolja az új kiadási definíciója. Ezzel a docker-compose.yml fájlt érhető el a kibocsátási folyamatban.

    ![A Visual Studio Team Services - kiadás összetevők](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. A kiadási eseményindító konfigurálásához kattintson **eseményindítók** válassza **folyamatos üzembe helyezés**. Az eseményindító be ugyanazon összetevő forrás. Ez a beállítás biztosítja, hogy az új kiadási elindul, amint a létrehozás sikeresen befejeződik.

    ![A Visual Studio Team Services - kiadás eseményindítók](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Adja meg a kiadási munkafolyamat

A kiadási munkafolyamat két feladatot hozzáadott tevődik össze.

1. Egy feladat biztonságosan másolja a compose-fájlt egy *telepítése* mappa a Docker Swarm fő csomóponton korábban konfigurált SSH-kapcsolat használatával. További részletek a következő képernyő jelenik meg.

    ![Visual Studio Team Services - kiadás SCP-je](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. Egy második feladat futtatásához bash parancs `docker` és `docker-compose` parancsok a fő csomóponton. További részletek a következő képernyő jelenik meg.

    ![A Visual Studio Team Services - kiadás Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    A parancs végrehajtása meg a fő használja, a Docker parancssori felületén és a Docker Compose CLI-t a következő feladatok elvégzéséhez:

    - Bejelentkezés az Azure-tárolót beállításjegyzék (három build variab'les definiált használ a **változók** lapon)
    - Adja meg a **DOCKER_HOST** működéséhez a Swarm végponthoz változó (: 2375)
    - Keresse meg a *telepítése* , amely az előző biztonságos másolási feladat hozta létre, és a docker-compose.yml fájlt tartalmazó mappa 
    - Végrehajtás `docker-compose` parancsokat, amelyek az új lemezképet lekéréses leállítania a szolgáltatásokat, távolítsa el a szolgáltatásokat, és a tárolók létrehozása.

    >[!IMPORTANT]
    > Amint az előző képernyőn, hagyja a **stderr-en sikertelen** jelölőnégyzet nincs bejelölve. Ez az egy fontos beállítása, mert `docker-compose` több diagnosztikai üzeneteket, jelenít meg, például a tárolók leállítása vagy törlése, a standard hibák kimenetét. Jelölje be a jelölőnégyzetet, ha a Visual Studio Team Services jelenti, hogy a kiadás során hibák jelentkeztek akkor is, ha minden megfelelően működik.
    >
3. Mentse az új kiadási definíciója.


>[!NOTE]
>A központi telepítés tartalmazza a némi állásidővel, mivel azt a régi szolgáltatások leállítása és futtatja az újjal. Ennek elkerüléséhez a kék-zöld központi telepítés végrehajtásával lehetőség.
>

## <a name="step-4-test-the-cicd-pipeline"></a>4. lépés A CI/CD-feldolgozási folyamat tesztelése

Most, hogy a konfiguráció befejezése után is tesztelheti az új CI/CD adatcsatornát. A tesztek legkönnyebben frissítése a forráskódot, és véglegesítse a módosításokat a GitHub-tárházban történő. Néhány másodperccel azután leküldéses a kód jelenik meg a Visual Studio Team Services rendszert futtató új buildverziót. Sikeres befejezést követően az új kiadási indul, és az Azure Tárolószolgáltatás-fürthöz az alkalmazás új verzióját telepíti.

## <a name="next-steps"></a>További lépések

* Visual Studio Team Services CI/CD kapcsolatos további információkért tekintse meg a [VSTS összeállítása – áttekintés](https://www.visualstudio.com/docs/build/overview).
