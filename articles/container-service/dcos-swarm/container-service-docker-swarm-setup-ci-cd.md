---
title: (ELAVULT) CI/CD – az Azure Container Service és a Swarm
description: Az Azure Container Service Docker Swarm, egy Azure Container Registry és Azure DevOps, hogy folyamatosan többtárolós .NET Core-alkalmazásokhoz
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 93046fa8225d8c85172d113d3c7f9e979c336770
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331435"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(ELAVULT) A teljes CI/CD-folyamat egy többtárolós alkalmazást az Azure Container Service Docker Swarm használatával az Azure DevOps-szolgáltatásokkal való üzembe helyezéséhez

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Az egyik legnagyobb kihívást az jelenti a modern alkalmazások fejlesztése során folyamatban van, hogy ezek az alkalmazások folyamatosan. Ebből a cikkből megismerheti, hogyan teljes folyamatos integráció és készregyártás (CI/CD) folyamat használatával az Azure Container Service a Docker Swarm, az Azure Container Registry és Azure folyamatok felügyeleti megvalósításához.

Ez a cikk egy egyszerű alkalmazást, a rendelkezésre álló alapján [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), fejlett ASP.NET Core használatával. Az alkalmazás négy különböző szolgáltatást épül fel: három webes API-k és a egy webes előtérrendszert:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

A cél, hogy folyamatosan továbbítását ezt az alkalmazást egy Docker Swarm-fürtöt, az Azure DevOps-szolgáltatásokkal. Az alábbi ábra a folyamatos teljesítési folyamat részletesen:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

A következő lépések röviden:

1. A forráskód adattára kódmódosítás elkötelezettek (Itt GitHub) 
1. GitHub aktiválnak egy buildet, az Azure DevOps-szolgáltatásokkal 
1. Az Azure DevOps-szolgáltatásokkal lekérdezi a legújabb verzióra a források, és létrehozza a lemezképeket, amelyek az alkalmazás összeállítása 
1. Azure DevOps-szolgáltatások minden rendszerképet leküldi a Docker-tárolójegyzék létrehozása az Azure Container Registry szolgáltatással 
1. Az Azure DevOps-szolgáltatásokkal aktiválja az új kiadás 
1. A kiadás fut néhány parancsot SSH-val az Azure container service fő fürtcsomópontra 
1. A fürtön a docker Swarm lekéri a rendszerképet a legújabb verzióra 
1. Az alkalmazás új verziója telepítve van a Docker Compose használatával 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt kell végrehajtani az alábbi feladatokat:

- [Swarm-fürt létrehozása az Azure Container Service-ben](container-service-deployment.md)
- [Csatlakozás a Swarm-fürthöz az Azure Container Service-ben](../container-service-connect.md)
- [Az Azure container registry létrehozása](../../container-registry/container-registry-get-started-portal.md)
- [Az Azure DevOps-szolgáltatásokkal szervezet és a létrehozott projekt rendelkezik](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [A GitHub-fiókjába a GitHub tárház elágaztatása](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

A Docker telepítve van egy (14.04-es vagy 16.04) Ubuntu-gép is szükséges. Ezt a gépet az Azure-folyamatok folyamatok során használja az Azure DevOps-szolgáltatásokkal. Ez a gép létrehozásának egyik módja az, hogy az elérhető rendszerképet használja a [Azure Marketplace-en](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>1. lépés: Az Azure DevOps-szolgáltatásokkal szervezet konfigurálása 

Ebben a szakaszban az Azure DevOps-szolgáltatásokkal szervezet fog konfigurálni.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Egy Azure-beli fejlesztési és üzemeltetési szolgáltatások Linuxos fordító-ügynökhöz konfigurálása

Docker-rendszerképek létrehozásához, és ezek a lemezképek leküldése az Azure container registry egy Azure-fejlesztési és üzemeltetési szolgáltatásokat hozhat létre a, akkor regisztrálnia kell egy Linux-ügynök. Ezen telepítési lehetőségek állnak rendelkezésére:

* [Egy Linux-ügynök telepítése](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Futtassa az Azure DevOps-szolgáltatásokkal ügynököt, a Docker használatával](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Az integráció az Azure DevOps-szolgáltatásokkal Docker-bővítményének telepítése

A Microsoft Azure DevOps-szolgáltatásokkal kiterjesztést működik együtt a Docker Azure folyamatok folyamatokat tartalmaz. Ez a bővítmény érhető el a [Azure DevOps-szolgáltatások Piactéri](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Kattintson a **telepítése** Ez a bővítmény hozzáadása az Azure DevOps-szolgáltatásokkal szervezet:

![Telepítse a Docker-integrációval](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

A rendszer felkéri az Azure DevOps-szolgáltatásokkal szervezeti hitelesítő adataival csatlakozhat. 

### <a name="connect-azure-devops-services-and-github"></a>Csatlakozás az Azure fejlesztési és üzemeltetési szolgáltatásokat és a GitHub

Az Azure DevOps-szolgáltatásokkal projekt és a GitHub-fiók közötti kapcsolat beállítása.

1. Az Azure DevOps-Services-projektben kattintson a **beállítások** ikonra az eszköztárban, majd válassza a **szolgáltatások**.

    ![Az Azure DevOps-szolgáltatásokkal – külső kapcsolatok](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Kattintson a bal oldali **új szolgáltatásvégpont** > **GitHub**.

    ![Az Azure DevOps-szolgáltatásokkal – GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Engedélyezze az Azure DevOps-szolgáltatásokkal való együttműködéshez a GitHub-fiókot, kattintson a **engedélyezés** , és kövesse a megjelenő ablakban.

    ![Az Azure DevOps-szolgáltatásokkal – GitHub engedélyezése](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Az Azure fejlesztési és üzemeltetési szolgáltatások csatlakoztatása az Azure container registry és Azure Container Service-fürt

Az utolsó előtt a CI/CD-folyamat az első lépésekre külső kapcsolatok a tárolóregisztrációs adatbázis és a Docker Swarm-fürt beállítása az Azure-ban. 

1. Az a **szolgáltatások** beállításait az Azure DevOps-Services-projektben adja hozzá egy végpontot típusú **Docker-beállításjegyzék**. 

1. Az előugró ablak írja be az URL-cím és az Azure container registry hitelesítő adataival.

    ![Az Azure DevOps-szolgáltatásokkal – Docker-beállításjegyzék](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. A Docker Swarm-fürt esetében típusú végpont hozzáadása **SSH**. Ezután írja be a SSH-kapcsolati adatok a Swarm-fürt.

    ![Az Azure DevOps szolgáltatások - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Minden konfiguráció a most történik. Az alábbi lépésekkel hoz létre a CI/CD folyamatot, amely az alkalmazás a Docker Swarm-fürt létrehozása és telepítése. 

## <a name="step-2-create-the-build-pipeline"></a>2. lépés: A létrehozási folyamat létrehozása

Ebben a lépésben egy buildelési folyamat beállítása az Azure DevOps-szolgáltatásokkal projekt és a build munkafolyamat definiálása a tárolólemezképek

### <a name="initial-pipeline-setup"></a>Kezdeti folyamat beállítása

1. Felépítési folyamat létrehozása az Azure DevOps-szolgáltatásokkal projekt csatlakozhat, és kattintson a **Build & Release**. 

1. Az a **Builddefinícióiról** területén kattintson **+ új**. Válassza ki a **üres** sablont.

    ![Az Azure DevOps - új folyamat létrehozása](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. A GitHub adattár forrással, ellenőrizze az új build konfigurálása **folyamatos integráció**, és válassza ki az ügynök üzenetsorba, ahol regisztrálta a Linux-ügynök. Kattintson a **létrehozás** az összeállítási folyamat létrehozásához.

    ![Az Azure DevOps-szolgáltatásokkal – Buildelési folyamat létrehozása](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Az a **definíciókat hozhat létre** lapon, először nyissa meg a **tárház** fülre, és a build, amelyet az Előfeltételek MyShop projekt elágazás használatára konfigurálja. Győződjön meg arról, hogy kiválasztotta *acs-docs* , a **alapértelmezett ággal**.

    ![Az Azure DevOps-Services - Build adattár-konfiguráció](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Az a **eseményindítók** lapra, konfigurálja a build minden véglegesítés után aktiválását. Válassza ki **folyamatos integráció** és **Batch-módosítások**.

    ![Az Azure DevOps-Services - Build Trigger konfigurációja](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>A build munkafolyamatokat
A következő lépéseket a build munkafolyamat határozza meg. Nincsenek hozhat létre öt tárolórendszerképeket a *MyShop* alkalmazás. Egyes rendszerképek a docker-fájlban található meg a projekt használatával lett összeállítva:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Adjon hozzá két Docker lépéseket minden egyes képe, egyet a rendszerkép létrehozásához és a egy-a rendszerkép leküldése az Azure container registry a kell. 

1. A build-munkafolyamat egy lépés hozzáadásához kattintson **+ Add build lépés** válassza **Docker**.

    ![Az Azure DevOps-Services - létrehozási lépések hozzáadása](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Az egyes lemezképek konfigurálása egy lépést, amely használja a `docker build` parancsot.

    ![Az Azure DevOps-szolgáltatásokkal – Docker-Build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    A létrehozási műveletet, válassza ki az Azure container registry a **állítson össze egy rendszerképet** művelet, és a docker-fájl, amely meghatározza az egyes rendszerképek. Állítsa be a **összeállítási környezet** gyökér, a docker-fájl a könyvtárban, és definiálja a **Lemezképnév**. 
    
    Ahogyan az előző képernyőn, indítsa el a rendszerkép nevének URI-ját az Azure container registrybe. (Is használhatja egy build változót a címke a rendszerkép, például a build azonosító ebben a példában paraméterezni.)

1. Az egyes lemezképek, a második lépésben használó konfigurálása a `docker push` parancsot.

    ![Az Azure DevOps-szolgáltatásokkal – Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    A leküldéses művelethez válassza ki az Azure container registry a **rendszerkép leküldése** műveletet, és adja meg a **Lemezképnév** , amely az előző lépésben épül.

1. Miután konfigurálta a build és a leküldéses lépéseket az egyes öt rendszerképet, két további lépések hozzáadása a build-munkafolyamatot.

    a. Egy parancssori feladatot, amely egy bash-szkript használ a *BuildNumber* a docker-compose.yml fájl és a jelenlegi eseményt hozhat létre azonosítót. Részleteket a következő képernyő jelenik meg.

    ![Az Azure DevOps-szolgáltatásokkal – frissítés Compose-fájl](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Ez a feladat csökken, a buildösszetevőt a frissített Compose-fájlt, így használhatók a kiadásban. Részleteket a következő képernyő jelenik meg.

    ![Azure DevOps Services - Publish Compose file](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Kattintson a **mentése** és nevezze el a buildelési folyamat.

## <a name="step-3-create-the-release-pipeline"></a>3. lépés: A kibocsátási folyamat létrehozása

Azure DevOps-szolgáltatás lehetővé teszi, hogy [kiadások kezelheti a környezeteket](https://www.visualstudio.com/team-services/release-management/). Győződjön meg arról, hogy az alkalmazás üzemel, a különböző környezetekben (például fejlesztési, tesztelési, éles üzem előtti vagy éles) zökkenőmentes módon való folyamatos üzembe helyezés is engedélyezheti. Létrehozhat egy új környezetet, amely az Azure Container Service Docker Swarm-fürt jelöli.

![Az Azure DevOps-szolgáltatásokkal – az ACS-kiadás](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Kezdeti kiadás beállítása

1. Kiadási folyamatok létrehozásához kattintson a **kiadásokban** > **+ kiadás**

1. Az összetevő-forrás konfigurálásához kattintson **összetevők** > **hivatkozás egy összetevő forrás**. Ez a kiadás új folyamat itt összekapcsolása a build, amelyet az előző lépésben megadott. Ha így tesz, a docker-compose.yml fájl érhető el a kibocsátási folyamat.

    ![Az Azure DevOps-szolgáltatásokkal – kiadási összetevők](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. A kiadási trigger konfigurálásához kattintson **eseményindítók** válassza **folyamatos üzembe helyezés**. Állítsa be az eseményindító összetevő azonos forrásból. Ez a beállítás biztosítja, hogy az új kiadás elindul, amint a létrehozás sikeresen befejeződik.

    ![Az Azure DevOps-szolgáltatásokkal – kiadási eseményindítók](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>A kiadási munkafolyamatokat

A kiadási munkafolyamat két feladatot a hozzáadott tevődik össze.

1. Egy feladat használatával is biztonságosan átmásolhatja a compose-fájlt egy *üzembe helyezése* mappájába, a Docker Swarm fő csomóponttal, az előzőekben konfigurált SSH-kapcsolat használatával. Részleteket a következő képernyő jelenik meg.

    ![Az Azure DevOps-szolgáltatásokkal – kiadási szolgáltatáskapcsolódási pont](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Egy második tevékenység futtatásához egy bash-parancs végrehajtása konfigurálása `docker` és `docker-compose` parancsok a fő csomópont. Részleteket a következő képernyő jelenik meg.

    ![Az Azure DevOps-szolgáltatásokkal – kiadási Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    A parancs végrehajtása a fő használatát a Docker parancssori felületén és a Docker-Compose CLI a következő feladatokat végezheti el:

    - Jelentkezzen be az Azure container registrybe (három build variab'les meghatározott használ a **változók** lap)
    - Adja meg a **DOCKER_HOST** változó dolgozhat a Swarm-végponthoz (: 2375-ös)
    - Keresse meg a *üzembe helyezése* , amely az előző biztonságos másolási tevékenység által létrehozott és a docker-compose.yml fájlt tartalmazó mappa 
    - Hajtsa végre `docker-compose` parancsok, amelyek az új rendszerképeket, állítsa le a szolgáltatásokat, távolítsa el a szolgáltatásokat, és a tárolók létrehozásához.

    >[!IMPORTANT]
    > Ahogyan az előző képernyőn, hagyja a **STDERR-en sikertelen** jelölőnégyzet nincs bejelölve. Ez az egy fontos beállítást, mert `docker-compose` több diagnosztikai üzeneteket jelenít meg például tárolók leállítása vagy törlése, a standard hibakimenet. Jelölje be a jelölőnégyzetet, ha az Azure DevOps-szolgáltatásokkal jelenti, hogy a kiadás során hibák jelentkeztek akkor is, ha minden megfelelően működik.
    >
1. Mentse az új kiadási folyamatot.


>[!NOTE]
>A központi telepítés tartalmazza a némi állásidőt jelent, mivel azt a régi szolgáltatások leállítása és futtatja az újat. Ennek elkerülése a kék-zöld üzembe helyezés végrehajtásával lehetőség.
>

## <a name="step-4-test-the-cicd-pipeline"></a>4. lépés A CI/CD-folyamat tesztelése

Most, hogy végzett a konfiguráció, legyen az új CI/CD-folyamat teszteléséhez. A legegyszerűbben úgy, hogy tesztelje, hogy frissítse a forráskódot, és véglegesítse a módosításokat a GitHub-tárházba. Néhány másodperccel azután, küldje le a kódot, látni fogja a DevOps-szolgáltatásokkal az Azure-ban futó új build. Sikeres befejezést követően új kiadás indul, és az Azure Container Service-fürtön az alkalmazás új verzióját telepíti.

## <a name="next-steps"></a>További lépések

* CI/CD Azure DevOps-szolgáltatásokkal kapcsolatos további információkért lásd: a [áttekintése az Azure fejlesztési és üzemeltetési szolgáltatásokat hozhat létre](https://www.visualstudio.com/docs/build/overview).
