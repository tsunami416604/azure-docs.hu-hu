---
title: CI/CD – az Azure Container Service és a Swarm
description: Az Azure Container Service Docker Swarm, egy Azure Container Registry és a Visual Studio Team Services, hogy folyamatosan többtárolós .NET Core-alkalmazásokhoz
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: b88fba4e04adb56742edf8023fde34e8ff6519c2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437916"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>A teljes CI/CD-folyamat üzembe helyezéséhez egy többtárolós alkalmazást az Azure Container Service a Docker Swarmmal Visual Studio Team Services használatával

Az egyik legnagyobb kihívást az jelenti a modern alkalmazások fejlesztése során folyamatban van, hogy ezek az alkalmazások folyamatosan. Ebből a cikkből megismerheti, hogyan teljes folyamatos integráció és készregyártás (CI/CD) folyamat használatával az Azure Container Service Docker Swarm, az Azure Container Registry és a Visual Studio Team Services buildelési megvalósítása, és a kiadáskezelés.

Ez a cikk egy egyszerű alkalmazást, a rendelkezésre álló alapján [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), fejlett ASP.NET Core használatával. Az alkalmazás négy különböző szolgáltatást épül fel: három webes API-k és a egy webes előtérrendszert:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

A célja, hogy az alkalmazás folyamatosan, a Docker Swarm-fürt, a Visual Studio Team Services használatával. Az alábbi ábra a folyamatos teljesítési folyamat részletesen:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

A következő lépések röviden:

1. A forráskód adattára kódmódosítás elkötelezettek (Itt GitHub) 
1. GitHub aktiválnak egy buildet, a Visual Studio Team Servicesben 
1. Visual Studio Team Services lekérdezi a legújabb verzióra a forrásból, és létrehozza a lemezképeket, amelyek az alkalmazás összeállítása 
1. A Visual Studio Team Services minden rendszerképet leküldi a Docker-tárolójegyzék létrehozása az Azure Container Registry szolgáltatással 
1. A Visual Studio Team Services elindít egy új kiadás 
1. A kiadás fut néhány parancsot SSH-val az Azure container service fő fürtcsomópontra 
1. A fürtön a docker Swarm lekéri a rendszerképet a legújabb verzióra 
1. Az alkalmazás új verziója telepítve van a Docker Compose használatával 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt kell végrehajtani az alábbi feladatokat:

- [Swarm-fürt létrehozása az Azure Container Service-ben](container-service-deployment.md)
- [Csatlakozás a Swarm-fürthöz az Azure Container Service-ben](../container-service-connect.md)
- [Az Azure container registry létrehozása](../../container-registry/container-registry-get-started-portal.md)
- [Rendelkezik egy Visual Studio Team Services-fiók és a team projekt létrehozása](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [A GitHub-fiókjába a GitHub tárház elágaztatása](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

A Docker telepítve van egy (14.04-es vagy 16.04) Ubuntu-gép is szükséges. Ezen a számítógépen használja a Visual Studio Team Services a buildelési és kiadási folyamatok során. Ez a gép létrehozásának egyik módja az, hogy az elérhető rendszerképet használja a [Azure Marketplace-en](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>1. lépés: A Visual Studio Team Services-fiók konfigurálása 

Ebben a szakaszban konfigurálja a Visual Studio Team Services-fiók.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>A Visual Studio Team Services Linux fordító-ügynökhöz konfigurálása

Docker-rendszerképek létrehozásához, és ezek a lemezképek leküldése az Azure container registry a Visual Studio Team Services build, akkor regisztrálnia kell egy Linux-ügynök. Ezen telepítési lehetőségek állnak rendelkezésére:

* [Egy Linux-ügynök telepítése](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Futtassa a VSTS-ügynököt a Docker használatával](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>A Docker-integráció VSTS-bővítmény telepítése

A Microsoft biztosít egy VSTS-bővítmény, amely a docker használatával a build és a kiadási folyamatok. Ez a bővítmény érhető el a [VSTS Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Kattintson a **telepítése** Ez a bővítmény hozzáadása a VSTS-fiók:

![Telepítse a Docker-integrációval](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

A rendszer felkéri a VSTS-fiók hitelesítő adataival csatlakozhat. 

### <a name="connect-visual-studio-team-services-and-github"></a>Csatlakozás a Visual Studio Team Services és a GitHub

Állítsa be a VSTS-projektet és a GitHub-fiók közötti kapcsolat.

1. A Visual Studio Team Services-projektben kattintson a **beállítások** ikonra az eszköztárban, majd válassza a **szolgáltatások**.

    ![A Visual Studio Team Services - külső kapcsolatok](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Kattintson a bal oldali **új szolgáltatásvégpont** > **GitHub**.

    ![A Visual Studio Team Services – GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Engedélyezze a GitHub-fiók használata a vsts-ben, kattintson a **engedélyezés** , és kövesse a megjelenő ablakban.

    ![A Visual Studio Team Services - GitHub engedélyezése](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Csatlakoztathatja a VSTS-a az Azure container registry és Azure Container Service-fürt

Az utolsó előtt a CI/CD-folyamat az első lépésekre külső kapcsolatok a tárolóregisztrációs adatbázis és a Docker Swarm-fürt beállítása az Azure-ban. 

1. Az a **szolgáltatások** beállításait a Visual Studio Team Services projekt hozzáadása egy szolgáltatásvégpont típusú **Docker-beállításjegyzék**. 

1. Az előugró ablak írja be az URL-cím és az Azure container registry hitelesítő adataival.

    ![A Visual Studio Team Services – Docker-beállításjegyzék](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. A Docker Swarm-fürt esetében típusú végpont hozzáadása **SSH**. Ezután írja be a SSH-kapcsolati adatok a Swarm-fürt.

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Minden konfiguráció a most történik. Az alábbi lépésekkel hoz létre a CI/CD folyamatot, amely az alkalmazás a Docker Swarm-fürt létrehozása és telepítése. 

## <a name="step-2-create-the-build-definition"></a>2. lépés: A builddefiníció létrehozása

Ebben a lépésben egy build definitionfor beállítása a VSTS-projekthez, és a build munkafolyamat definiálása a tárolólemezképek

### <a name="initial-definition-setup"></a>Kezdeti definíció beállítása

1. Builddefiníció létrehozása a Visual Studio Team Services-projekthez csatlakozzon, és kattintson a **Build & Release**. 

1. Az a **Builddefinícióiról** területén kattintson **+ új**. Válassza ki a **üres** sablont.

    ![A Visual Studio Team Services – új Build definíciója](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. A GitHub adattár forrással, ellenőrizze az új build konfigurálása **folyamatos integráció**, és válassza ki az ügynök üzenetsorba, ahol regisztrálta a Linux-ügynök. Kattintson a **létrehozás** a builddefiníció létrehozása.

    ![A Visual Studio Team Services - Build definíció létrehozása](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Az a **definíciókat hozhat létre** lapon, először nyissa meg a **tárház** fülre, és a build, amelyet az Előfeltételek MyShop projekt elágazás használatára konfigurálja. Győződjön meg arról, hogy kiválasztotta *acs-docs* , a **alapértelmezett ággal**.

    ![A Visual Studio Team Services - Build adattár-konfiguráció](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Az a **eseményindítók** lapra, konfigurálja a build minden véglegesítés után aktiválását. Válassza ki **folyamatos integráció** és **Batch-módosítások**.

    ![A Visual Studio Team Services - Build-Trigger konfigurációja](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>A build munkafolyamatokat
A következő lépéseket a build munkafolyamat határozza meg. Nincsenek hozhat létre öt tárolórendszerképeket a *MyShop* alkalmazás. Egyes rendszerképek a docker-fájlban található meg a projekt használatával lett összeállítva:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Adjon hozzá két Docker lépéseket minden egyes képe, egyet a rendszerkép létrehozásához és a egy-a rendszerkép leküldése az Azure container registry a kell. 

1. A build-munkafolyamat egy lépés hozzáadásához kattintson **+ Add build lépés** válassza **Docker**.

    ![A Visual Studio Team Services - létrehozási lépések hozzáadása](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Az egyes lemezképek konfigurálása egy lépést, amely használja a `docker build` parancsot.

    ![A Visual Studio Team Services - Docker Build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    A létrehozási műveletet, válassza ki az Azure container registry a **állítson össze egy rendszerképet** művelet, és a docker-fájl, amely meghatározza az egyes rendszerképek. Állítsa be a **összeállítási környezet** gyökér, a docker-fájl a könyvtárban, és definiálja a **Lemezképnév**. 
    
    Ahogyan az előző képernyőn, indítsa el a rendszerkép nevének URI-ját az Azure container registrybe. (Is használhatja egy build változót a címke a rendszerkép, például a build azonosító ebben a példában paraméterezni.)

1. Az egyes lemezképek, a második lépésben használó konfigurálása a `docker push` parancsot.

    ![A Visual Studio Team Services - Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    A leküldéses művelethez válassza ki az Azure container registry a **rendszerkép leküldése** műveletet, és adja meg a **Lemezképnév** , amely az előző lépésben épül.

1. Miután konfigurálta a build és a leküldéses lépéseket az egyes öt rendszerképet, két további lépések hozzáadása a build-munkafolyamatot.

    a. Egy parancssori feladatot, amely egy bash-szkript használ a *BuildNumber* a docker-compose.yml fájl és a jelenlegi eseményt hozhat létre azonosítót. Részleteket a következő képernyő jelenik meg.

    ![A Visual Studio Team Services - frissítés Compose-fájl](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Ez a feladat csökken, a buildösszetevőt a frissített Compose-fájlt, így használhatók a kiadásban. Részleteket a következő képernyő jelenik meg.

    ![A Visual Studio Team Services - összeállítás közzététele fájl](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Kattintson a **mentése** és nevezze el a builddefiníció.

## <a name="step-3-create-the-release-definition"></a>3. lépés: A kiadási definíció létrehozása

A Visual Studio Team Services lehetővé teszi, hogy [kiadások kezelheti a környezeteket](https://www.visualstudio.com/team-services/release-management/). Győződjön meg arról, hogy az alkalmazás üzemel, a különböző környezetekben (például fejlesztési, tesztelési, éles üzem előtti vagy éles) zökkenőmentes módon való folyamatos üzembe helyezés is engedélyezheti. Létrehozhat egy új környezetet, amely az Azure Container Service Docker Swarm-fürt jelöli.

![A Visual Studio Team Services - az ACS-kiadás](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Kezdeti kiadás beállítása

1. Kiadási definíció létrehozása, kattintson a **kiadásokban** > **+ kiadás**

1. Az összetevő-forrás konfigurálásához kattintson **összetevők** > **hivatkozás egy összetevő forrás**. Az új kiadási definíció itt összekapcsolása a build, amelyet az előző lépésben megadott. Ha így tesz, a docker-compose.yml fájl érhető el a kibocsátási folyamat.

    ![A Visual Studio Team Services - kiadás összetevők](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. A kiadási trigger konfigurálásához kattintson **eseményindítók** válassza **folyamatos üzembe helyezés**. Állítsa be az eseményindító összetevő azonos forrásból. Ez a beállítás biztosítja, hogy az új kiadás elindul, amint a létrehozás sikeresen befejeződik.

    ![A Visual Studio Team Services - kiadás eseményindítók](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>A kiadási munkafolyamatokat

A kiadási munkafolyamat két feladatot a hozzáadott tevődik össze.

1. Egy feladat használatával is biztonságosan átmásolhatja a compose-fájlt egy *üzembe helyezése* mappájába, a Docker Swarm fő csomóponttal, az előzőekben konfigurált SSH-kapcsolat használatával. Részleteket a következő képernyő jelenik meg.

    ![A Visual Studio Team Services - kiadás SCP-je](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Egy második tevékenység futtatásához egy bash-parancs végrehajtása konfigurálása `docker` és `docker-compose` parancsok a fő csomópont. Részleteket a következő képernyő jelenik meg.

    ![A Visual Studio Team Services - kiadás Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    A parancs végrehajtása a fő használatát a Docker parancssori felületén és a Docker-Compose CLI a következő feladatokat végezheti el:

    - Jelentkezzen be az Azure container registrybe (három build variab'les meghatározott használ a **változók** lap)
    - Adja meg a **DOCKER_HOST** változó dolgozhat a Swarm-végponthoz (: 2375-ös)
    - Keresse meg a *üzembe helyezése* , amely az előző biztonságos másolási tevékenység által létrehozott és a docker-compose.yml fájlt tartalmazó mappa 
    - Hajtsa végre `docker-compose` parancsok, amelyek az új rendszerképeket, állítsa le a szolgáltatásokat, távolítsa el a szolgáltatásokat, és a tárolók létrehozásához.

    >[!IMPORTANT]
    > Ahogyan az előző képernyőn, hagyja a **STDERR-en sikertelen** jelölőnégyzet nincs bejelölve. Ez az egy fontos beállítást, mert `docker-compose` több diagnosztikai üzeneteket jelenít meg például tárolók leállítása vagy törlése, a standard hibakimenet. Jelölje be a jelölőnégyzetet, ha Visual Studio Team Services-jelentések, hogy a kiadás során hibák jelentkeztek akkor is, ha minden megfelelően működik.
    >
1. Az új kiadási definíció mentéséhez.


>[!NOTE]
>A központi telepítés tartalmazza a némi állásidőt jelent, mivel azt a régi szolgáltatások leállítása és futtatja az újat. Ennek elkerülése a kék-zöld üzembe helyezés végrehajtásával lehetőség.
>

## <a name="step-4-test-the-cicd-pipeline"></a>4. lépés A CI/CD-folyamat tesztelése

Most, hogy végzett a konfiguráció, legyen az új CI/CD-folyamat teszteléséhez. A legegyszerűbben úgy, hogy tesztelje, hogy frissítse a forráskódot, és véglegesítse a módosításokat a GitHub-tárházba. Néhány másodperccel azután, küldje le a kódot, megjelenik egy új buildet a Visual Studio Team Servicesben futó. Sikeres befejezést követően új kiadás indul, és az Azure Container Service-fürtön az alkalmazás új verzióját telepíti.

## <a name="next-steps"></a>További lépések

* CI/CD a Visual Studio Team Services szolgáltatással kapcsolatos további információkért lásd: a [hozhat létre a vsts-ben – áttekintés](https://www.visualstudio.com/docs/build/overview).
