---
title: (ELAVULT) CI/CD az Azure Container Service és a Swarm szolgáltatással
description: Az Azure Container Service használata a Docker Swarm, egy Azure Container Registry és az Azure DevOps használatával folyamatosan többtárolós .NET Core alkalmazás
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549053"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(ELAVULT) Teljes CI/CD-folyamat egy többtárolós alkalmazás üzembe helyezéséhez az Azure Container Service szolgáltatásban a Docker Swarm szolgáltatással az Azure DevOps-szolgáltatások használatával

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A felhőmodern alkalmazások fejlesztése során az egyik legnagyobb kihívás az, hogy ezeket az alkalmazásokat folyamatosan képesek szállítani. Ebben a cikkben megtudhatja, hogyan valósíthatja meg a teljes folyamatos integrációés üzembe helyezés (CI/CD) folyamat az Azure Container Service docker Swarm, az Azure Container Registry és az Azure Pipelines-felügyelet használatával.


![MyShop mintaalkalmazás](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

A cél az, hogy ezt az alkalmazást folyamatosan egy Docker Swarm-fürtben, az Azure DevOps Services használatával. A következő ábra részletezi ezt a folyamatos szállítási folyamatot:

![MyShop mintaalkalmazás](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Itt van egy rövid magyarázat a lépéseket:

1. A kódmódosítások véglegesítésre kerülnek a forráskódtárban (itt, GitHub) 
1. A GitHub elindítja a buildet az Azure DevOps-szolgáltatásokban 
1. Az Azure DevOps Services leveszi a források legújabb verzióját, és létrehozza az alkalmazást alkotó összes lemezképet 
1. Az Azure DevOps Services minden lemezképet lelök egy Docker-beállításjegyzékbe, amelyet az Azure Container Registry szolgáltatás használatával hoztak létre 
1. Az Azure DevOps Services új kiadást indít el 
1. A kiadás futtat néhány parancsot az SSH használatával az Azure container service-fürt főcsomópontján 
1. A Docker Swarm a fürtön a képek legújabb verzióját kéri le 
1. Az alkalmazás új verziója a Docker Compose használatával van telepítve 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt a következő feladatokat kell elvégeznie:

- [Swarm-fürt létrehozása az Azure tárolószolgáltatásban](container-service-deployment.md)
- [Csatlakozás a Swarm-fürthöz az Azure tárolószolgáltatásban](../container-service-connect.md)
- [Azure Container Registry létrehozása](../../container-registry/container-registry-get-started-portal.md)
- [Létrehozása az Azure DevOps Services szervezetének és projektjének](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [A GitHub-tárház elágazása a GitHub-fiókhoz](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Szüksége van egy Ubuntu (14.04 vagy 16.04) gépre is, amelyen telepítve van a Docker. Ezt a gépet az Azure DevOps-szolgáltatások használják az Azure Pipelines folyamatok során. A gép létrehozásának egyik módja az Azure Marketplace-en elérhető lemezkép használata. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>1. lépés: Az Azure DevOps Services szervezetének konfigurálása 

Ebben a szakaszban konfigurálja az Azure DevOps Services-szervezetet.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Azure DevOps Services Linux buildügynök konfigurálása

Docker-rendszerképek létrehozásához, és leküldéses ezeket a rendszerképeket egy Azure-tároló beállításjegyzékbe egy Azure DevOps Services build, regisztrálnia kell egy Linux-ügynök. A következő telepítési lehetőségek állnak rendelkezésre:

* [Ügynök üzembe helyezése Linuxon](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Az Azure DevOps Services ügynök futtatásához használja a Dockert](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>A Docker-integrációs Azure DevOps-szolgáltatások bővítmény telepítése

A Microsoft egy Azure DevOps Services-bővítményt biztosít a Docker azure-folyamatokban való munkához. Ez a bővítmény az [Azure DevOps Services Marketplace-en](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker)érhető el. Kattintson a **Telepítés** gombra, ha hozzá szeretné adni ezt a bővítményt az Azure DevOps Services szervezetéhez:

![A Docker-integráció telepítése](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

A rendszer arra kéri, hogy a hitelesítő adatok használatával csatlakozzon az Azure DevOps Services szervezetéhez. 

### <a name="connect-azure-devops-services-and-github"></a>Az Azure DevOps-szolgáltatások és a GitHub összekapcsolása

Hozzon létre kapcsolatot az Azure DevOps Services projekt és a GitHub-fiók között.

1. Az Azure DevOps Services projektben kattintson a **Beállítások** ikonra az eszköztáron, és válassza a **Szolgáltatások**lehetőséget.

    ![Azure DevOps Services – Külső kapcsolat](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. A bal oldalon kattintson az **Új szolgáltatásvégpont** > **GitHub**.

    ![Azure DevOps-szolgáltatások – GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Ha engedélyezni szeretné az Azure DevOps-szolgáltatások számára a GitHub-fiókkal való munkát, kattintson az **Engedélyezés** gombra, és kövesse a megnyíló ablakban található eljárást.

    ![Azure DevOps-szolgáltatások – A GitHub engedélyezése](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Az Azure DevOps-szolgáltatások csatlakoztatása az Azure container registryhez és az Azure Container Service-fürthöz

A CI/CD-folyamatba való bejutás előtt az utolsó lépések a tároló beállításjegyzékéhez és a Docker Swarm-fürthöz való külső kapcsolatok konfigurálása az Azure-ban. 

1. Az Azure DevOps Services projekt **Szolgáltatások** beállításaiban adjon hozzá egy Docker Registry típusú **szolgáltatásvégpontot.** 

1. A megnyíló előugró ablakban adja meg az Azure-tároló beállításjegyzékének URL-címét és hitelesítő adatait.

    ![Azure DevOps-szolgáltatások – Docker-beállításjegyzék](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. A Docker Swarm-fürthöz adjon hozzá egy **SSH**típusú végpontot. Ezután adja meg a Swarm-fürt SSH-kapcsolati adatait.

    ![Azure DevOps szolgáltatások - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Minden konfiguráció befejeződött most. A következő lépésekben hozza létre a CI/CD-folyamatot, amely létrehozza és telepíti az alkalmazást a Docker Swarm fürtre. 

## <a name="step-2-create-the-build-pipeline"></a>2. lépés: A buildfolyamat létrehozása

Ebben a lépésben létrehoz egy buildelési folyamatot az Azure DevOps Services projekthez, és meghatározza a tárolórendszerképek buildelési munkafolyamatát

### <a name="initial-pipeline-setup"></a>Kezdeti csővezeték beállítása

1. Build-folyamat létrehozásához csatlakozzon az Azure DevOps Services projekthez, és kattintson **a Build & Release**elemre. 

1. A **Build definitions (Definíciók buildelése)** csoportban kattintson a **+ Új**gombra. Válassza az **Üres** sablont.

    ![Azure DevOps – Új buildfolyamat](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Konfigurálja az új buildet egy GitHub-tárházforrással, ellenőrizze a **folyamatos integrációt,** és válassza ki azt az ügynökvárólistát, ahol regisztrálta a Linux-ügynököt. A buildelési folyamat létrehozásához kattintson a **Létrehozás** gombra.

    ![Azure DevOps-szolgáltatások – Build-folyamat létrehozása](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. A **Definíciók összeállítása** lapon nyissa meg a **Tárház** lapot, és konfigurálja úgy a buildet, hogy az előfeltételekben létrehozott MyShop-projekt elágazása jelenjen meg. Győződjön meg arról, hogy *az acs-docs-ot választja* **alapértelmezett ágként.**

    ![Azure DevOps-szolgáltatások – Tárház konfigurációjának összeállítása](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Az **Eseményindítók** lapon konfigurálja a buildet, hogy minden egyes véglegesítés után aktiválódjon. Válassza a **Folyamatos integráció** és **a Kötegelt módosítások lehetőséget.**

    ![Azure DevOps-szolgáltatások – Trigger konfigurációjának létrehozása](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>A létrehozási munkafolyamat meghatározása
A következő lépések határozzák meg a buildmunkafolyamatot. A *MyShop* alkalmazáshoz öt tárolórendszerképet kell készíteni. Minden lemezkép a projektmappákban található Docker-fájl használatával épül fel:

* ProductsApi (TermékekApi)
* Proxy
* Minősítési api
* AjánlásokApi
* ShopFront

Minden lemezképhez két Docker-lépést kell hozzáadnia, egyet a rendszerkép létrehozásához, egyet pedig a rendszerkép leküldéses az Azure-tároló beállításjegyzékében. 

1. A buildmunkafolyamat egy lépésének hozzáadásához kattintson a **+ Build lépés hozzáadása** gombra, és válassza a **Docker**lehetőséget.

    ![Azure DevOps-szolgáltatások – Build-lépések hozzáadása](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Minden lemezképhez konfiguráljon egy `docker build` lépést, amely a parancsot használja.

    ![Azure DevOps-szolgáltatások – Docker build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    A buildművelethez válassza ki az Azure container beállításjegyzéket, a **Lemezkép-művelet létrehozása** és az egyes rendszerképeket meghatározó Docker-fájl. Állítsa be a **Build környezetet** Dockerfile gyökérkönyvtárként, és adja meg a **lemezkép nevét.** 
    
    Ahogy az előző képernyőn látható, indítsa el a lemezkép nevét az Azure-tároló beállításjegyzék URI-jával. (A buildváltozósegítségével paraméterezheti a kép címkéjét, például ebben a példában a buildazonosítót.)

1. Minden lemezképhez konfiguráljon egy `docker push` második lépést, amely a parancsot használja.

    ![Azure DevOps-szolgáltatások – Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    A leküldéses művelethez válassza ki az Azure container beállításjegyzéket, a **leküldéses egy rendszerkép** műveletet, és adja meg az előző lépésben beépített **lemezkép nevét.**

1. Miután konfigurálta a build- és leküldéses lépéseket mind az öt lemezképhez, adjon hozzá még két lépést a buildmunkafolyamatban.

    a. Olyan parancssori feladat, amely bash parancsfájlt használ a docker-compose.yml fájl *BuildNumber* előfordulásának az aktuális buildazonosítóval való lecserélésére. A részleteket lásd a következő képernyőn.

    ![Azure DevOps-szolgáltatások – Compose fájl frissítése](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Olyan feladat, amely a frissített Összeállítási fájlt buildösszetevőként dobja el, hogy a kiadásban használható legyen. A részleteket lásd a következő képernyőn.

    ![Azure DevOps-szolgáltatások – Írásfájl közzététele](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Kattintson **a Mentés gombra,** és nevezze el a buildfolyamatot.

## <a name="step-3-create-the-release-pipeline"></a>3. lépés: A kiadási folyamat létrehozása

Az Azure DevOps Services lehetővé teszi a [kiadások kezelését a különböző környezetekben.](https://www.visualstudio.com/team-services/release-management/) Engedélyezheti a folyamatos üzembe helyezést annak érdekében, hogy az alkalmazás zökkenőmentesen legyen telepítve a különböző környezetekben (például a fejlesztés, a tesztelés, az üzem előtti és az éles környezetben). Létrehozhat egy új környezetet, amely az Azure Container Service Docker Swarm fürtöt képviseli.

![Azure DevOps-szolgáltatások – Kiadás az ACS-nek](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Kezdeti kiadás beállítása

1. Kiadási folyamat létrehozásához kattintson a Kiadások + Kiadás **gombra.** > **+ Release**

1. A műtermék-forrás konfigurálásához kattintson **a Műtermékek** > **kapcsolódás egy műtermék forrása**elemre. Itt kapcsolja össze ezt az új kiadási folyamatot az előző lépésben megadott buildtel. Ezzel a docker-compose.yml fájl elérhető a kiadási folyamatban.

    ![Azure DevOps-szolgáltatások – kiadási összetevők](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. A kiadási eseményindító konfigurálásához kattintson az **Eseményindítók gombra,** és válassza **a Folyamatos telepítés**lehetőséget. Állítsa be az eseményindítót ugyanarra a műtermék-forrásra. Ez a beállítás biztosítja, hogy egy új kiadás elindul, amint a build sikeresen befejeződik.

    ![Azure DevOps-szolgáltatások – Kiadási eseményindítók](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>A kiadási munkafolyamat meghatározása

A kiadási munkafolyamat két hozzáadott feladatból áll.

1. Konfiguráljon egy feladatot úgy, hogy biztonságosan másolja az írásfájlt a Docker-sraj főcsomópontegy *központi mappájába* a korábban konfigurált SSH-kapcsolat használatával. A részleteket lásd a következő képernyőn.

    ![Azure DevOps-szolgáltatások – SCP kiadása](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Konfiguráljon egy második feladatot egy `docker` bash `docker-compose` parancs futtatásához és a fő csomóponton lévő parancsok végrehajtásához. A részleteket lásd a következő képernyőn.

    ![Azure DevOps-szolgáltatások – Bash kiadása](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    A főoldalon végrehajtott parancs a Docker CLI és a Docker-Compose CLI segítségével hajtsa végre a következő feladatokat:

   - Bejelentkezés az Azure container registry (három build variab'les, amelyek a **változók** lapon vannak definiálva)
   - A Raj végponttal való munka **DOCKER_HOST** meghatározása (:2375)
   - Keresse meg az előző biztonságos másolási feladat által létrehozott és a docker-compose.yml fájlt tartalmazó *központi telepítésmappát* 
   - Olyan `docker-compose` parancsokat hajtson végre, amelyek lekérik az új képeket, leállítják a szolgáltatásokat, eltávolítják a szolgáltatásokat, és létrehozzák a tárolókat.

     >[!IMPORTANT]
     > Ahogy az előző képernyőn látható, hagyja bejelölve a **Fail on STDERR jelölőnégyzetet.** Ez fontos beállítás, `docker-compose` mivel a szabványos hibakimeneten több diagnosztikai üzenetet nyomtat, például a tárolók leállnak vagy törlődnek. Ha bejelöli a jelölőnégyzetet, az Azure DevOps Services jelenti, hogy hiba történt a kiadás során, még akkor is, ha minden jól megy.
     >
1. Mentse ezt az új kiadási folyamatot.


>[!NOTE]
>Ez a központi telepítés tartalmaz némi állásidőt, mert leállítjuk a régi szolgáltatásokat, és futtatjuk az újat. Ezt el lehet kerülni egy kék-zöld telepítéssel.
>

## <a name="step-4-test-the-cicd-pipeline"></a>4. lépés A CI/CD-folyamat tesztelése

Most, hogy végzett a konfigurációval, itt az ideje, hogy tesztelje ezt az új CI/CD-folyamatot. A tesztelés legegyszerűbb módja a forráskód frissítése és a módosítások véglegesítése a GitHub-tárházba. Néhány másodperccel a kód leküldése után megjelenik egy új build fut az Azure DevOps-szolgáltatásokban. A sikeres befejeződést követően egy új kiadás aktiválódik, és az alkalmazás új verzióját telepíti az Azure Container Service-fürtön.

## <a name="next-steps"></a>Következő lépések

* A CI/CD-ről az Azure DevOps-szolgáltatásokkal kapcsolatos további információkért tekintse meg az [Azure Pipelines dokumentációs](/azure/devops/pipelines/?view=azure-devops) cikkét.
