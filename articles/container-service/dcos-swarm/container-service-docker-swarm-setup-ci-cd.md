---
title: ELAVULT CI/CD Azure Container Service és Swarmtal
description: Azure Container Service a Docker Swarm, egy Azure Container Registry és az Azure DevOps használatával folyamatosan többtárolós .NET Core-alkalmazást biztosíthat
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76549053"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>ELAVULT Teljes CI/CD-folyamat egy többtárolós alkalmazás üzembe helyezéséhez Azure Container Service a Docker Swarm használatával az Azure DevOps Services segítségével

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A Felhőbeli modern alkalmazások fejlesztése során az egyik legnagyobb kihívás az alkalmazások folyamatos továbbítása. Ebből a cikkből megtudhatja, hogyan valósítható meg a teljes folyamatos integrációs és üzembe helyezési (CI/CD) folyamat a Docker Swarm, a Azure Container Registry és az Azure-folyamatok felügyelete Azure Container Service használatával.


![MyShop-minta alkalmazása](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

A cél az alkalmazás folyamatos továbbítása egy Docker Swarm-fürtben az Azure DevOps Services használatával. Az alábbi ábra a folyamatos kézbesítési folyamatot részletezi:

![MyShop-minta alkalmazása](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Íme a lépések rövid ismertetése:

1. A kód módosításai elkötelezettek a forráskód-tárházban (itt a GitHub) 
1. A GitHub elindítja a buildet az Azure DevOps Servicesben 
1. Az Azure DevOps Services lekéri a források legújabb verzióját, és az alkalmazást alkotó összes lemezképet felépíti 
1. Az Azure DevOps Services az Azure Container Registry szolgáltatás használatával leküldi az egyes rendszerképeket egy Docker-beállításjegyzékbe 
1. Az Azure DevOps Services új kiadást indít el 
1. A kiadás bizonyos parancsokat futtat az SSH használatával az Azure Container Service-fürt főcsomópontján 
1. A Docker Swarm a fürtön lekéri a lemezképek legújabb verzióját 
1. Az alkalmazás új verziója a Docker-összeállítás használatával van üzembe helyezve 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt el kell végeznie a következő feladatokat:

- [Swarm-fürt létrehozása az Azure tárolószolgáltatásban](container-service-deployment.md)
- [Csatlakozás a Swarm-fürthöz az Azure tárolószolgáltatásban](../container-service-connect.md)
- [Azure Container Registry létrehozása](../../container-registry/container-registry-get-started-portal.md)
- [Létre kell hoznia egy Azure DevOps Services-szervezetet és-projektet](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [A GitHub-tárház elágazása a GitHub-fiókhoz](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Szükség van egy Ubuntu (14,04 vagy 16,04) gépre is, amelyen telepítve van a Docker. Ezt a gépet az Azure DevOps Services használja az Azure-folyamatok folyamatai során. A gép létrehozásának egyik módja az Azure Marketplace-en elérhető rendszerkép használata. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>1. lépés: az Azure DevOps Services-szervezet konfigurálása 

Ebben a szakaszban az Azure DevOps Services-szervezetet konfigurálja.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Azure DevOps Services linuxos Build-ügynök konfigurálása

Ha Docker-rendszerképeket szeretne létrehozni, és ezeket a képeket egy Azure Container registrybe szeretné leküldeni egy Azure DevOps Services buildből, regisztrálnia kell egy Linux-ügynököt. Ezek a telepítési lehetőségek:

* [Ügynök üzembe helyezése Linux rendszeren](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Az Azure DevOps Services Agent futtatása a Docker használatával](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>A Docker Integration Azure DevOps Services bővítmény telepítése

A Microsoft Azure DevOps Services-bővítményt biztosít a Docker Azure-folyamatokban való működéséhez. Ez a bővítmény az [Azure DevOps Services piactéren](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker)érhető el. A bővítmény Azure DevOps Services-szervezethez való hozzáadásához kattintson a **telepítés** gombra:

![A Docker-integráció telepítése](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

A rendszer arra kéri, hogy az Ön hitelesítő adataival kapcsolódjon az Azure DevOps Services-szervezethez. 

### <a name="connect-azure-devops-services-and-github"></a>Az Azure DevOps Services és a GitHub összekötése

Hozzon létre egy kapcsolatot az Azure DevOps Services-projekt és a GitHub-fiók között.

1. Az Azure DevOps Services-projektben kattintson az eszköztár **Beállítások** ikonjára, majd válassza a **szolgáltatások**lehetőséget.

    ![Azure DevOps Services – külső kapcsolatok](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. A bal oldalon kattintson az **új szolgáltatási végpont** > **GitHub**elemre.

    ![Azure DevOps Services – GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Ha engedélyezni szeretné az Azure DevOps-szolgáltatások számára a GitHub-fiókkal való munkát, kattintson az **Engedélyezés** elemre, és kövesse a megnyíló ablakban található eljárást.

    ![Azure DevOps Services – GitHub engedélyezése](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Az Azure DevOps Services csatlakoztatható az Azure Container registryhez és Azure Container Service-fürthöz

A CI/CD-folyamatba való beolvasást megelőzően a külső kapcsolatok konfigurálása a tároló-beállításjegyzékhez és a Docker Swarm-fürthöz az Azure-ban. 

1. Az Azure DevOps Services-projekt **szolgáltatások** beállításainál adjon hozzá egy **Docker-beállításjegyzék**típusú szolgáltatási végpontot. 

1. A megnyíló előugró ablakban adja meg az Azure Container Registry URL-címét és hitelesítő adatait.

    ![Azure DevOps Services – Docker-beállításjegyzék](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. A Docker Swarm-fürthöz adjon hozzá egy **SSH**típusú végpontot. Ezután adja meg a Swarm-fürt SSH-kapcsolatok információit.

    ![Azure DevOps Services – SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Az összes konfiguráció most elkészült. A következő lépésekben létre kell hoznia a CI/CD folyamatot, amely létrehozza és telepíti az alkalmazást a Docker Swarm-fürtre. 

## <a name="step-2-create-the-build-pipeline"></a>2. lépés: a létrehozási folyamat létrehozása

Ebben a lépésben létrehoz egy Build-folyamatot az Azure DevOps Services-projekthez, és meghatározza a tároló lemezképek létrehozási munkafolyamatát.

### <a name="initial-pipeline-setup"></a>Kezdeti folyamat beállítása

1. Build folyamat létrehozásához kapcsolódjon az Azure DevOps Services-projekthez, és kattintson a **build & kiadás**elemre. 

1. A **Build fogalommeghatározások** szakaszban kattintson az **+ új**elemre. Válassza ki az **üres** sablont.

    ![Azure DevOps – új build-folyamat](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Konfigurálja az új buildet egy GitHub-tárház forrásával, tekintse meg a **folyamatos integrációt**, és válassza ki azt az ügynök-várólistát, amelyben regisztrálta a linuxos ügynököt. A build folyamat létrehozásához kattintson a **Létrehozás** gombra.

    ![Azure DevOps Services – build folyamat létrehozása](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. A **Build-definíciók** lapon először nyissa meg az **adattár** lapot, és konfigurálja a buildet az előfeltételek között létrehozott MyShop-projekt elágazásának használatára. Győződjön meg arról, hogy az *ACS-docs* **alapértelmezett ágként**van kiválasztva.

    ![Azure DevOps Services – adattár konfigurációjának összeállítása](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Az **Eseményindítók** lapon konfigurálja úgy a buildet, hogy az egyes véglegesítés után induljon el. Válassza a **folyamatos integráció** és a **Batch módosítása**lehetőséget.

    ![Azure DevOps Services – trigger konfigurációjának létrehozása](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>A Build munkafolyamat megadása
A következő lépések a Build munkafolyamatot határozzák meg. A *MyShop* alkalmazáshoz öt tároló lemezképet kell létrehozni. Minden rendszerkép a projekt mappáiban található Docker használatával készült:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Az egyes rendszerképekhez két Docker-lépést kell felvennie, egyet a rendszerkép létrehozásához, egyet pedig a rendszerkép Azure Container registryben való leküldéséhez. 

1. Ha hozzá szeretne adni egy lépést a Build munkafolyamatban, kattintson a **+ Build lépés hozzáadása** és a **Docker**elemre.

    ![Azure DevOps Services – Build lépések hozzáadása](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Minden rendszerkép esetében állítson be egy lépést, amely `docker build` a parancsot használja.

    ![Azure DevOps Services – Docker-Build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    A Build művelethez válassza ki az Azure Container registryt, a **rendszerkép létrehozása** műveletet, valamint az egyes képeket definiáló Docker. Állítsa a **Build környezetet** Docker gyökérkönyvtárként, és adja meg a **rendszerkép nevét**. 
    
    Ahogy az előző képernyőn is látható, indítsa el a rendszerkép nevét az Azure Container Registry URI-ja alapján. (A Build változó használatával parametrizálja a rendszerkép címkéjét, például az ebben a példában szereplő Build-azonosítót is.)

1. Minden rendszerkép esetében konfigurálja a `docker push` parancsot használó második lépést.

    ![Azure DevOps Services – Docker leküldés](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    A leküldéses művelethez válassza ki az Azure Container registryt, a **rendszerkép leküldése** műveletet, és adja meg az előző lépésben létrehozott **rendszerkép nevét** .

1. Miután konfigurálta a létrehozási és leküldéses lépéseket az egyes öt lemezképekhez, vegyen fel két további lépést a Build munkafolyamatban.

    a. Egy olyan parancssori feladat, amely bash-parancsfájlt használ a Docker-compose. YML fájl *BuildNumber* előfordulásának lecserélésére az aktuális Build-azonosítóval. A részletekért tekintse meg a következő képernyőt.

    ![Azure DevOps Services – az összeállítási fájl frissítése](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Egy feladat, amely a frissített összeállítási fájlt felépíti összetevőként, így a kiadásban is használható. A részletekért tekintse meg a következő képernyőt.

    ![Azure DevOps Services – szerkesztési fájl közzététele](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Kattintson a **Save (Mentés** ) gombra, és nevezze el a Build folyamatot.

## <a name="step-3-create-the-release-pipeline"></a>3. lépés: a kiadási folyamat létrehozása

Az Azure DevOps Services lehetővé teszi a [kiadások különböző környezetekben való kezelését](https://www.visualstudio.com/team-services/release-management/). A folyamatos üzembe helyezés lehetővé teszi, hogy az alkalmazás a különböző környezetekben (például a fejlesztési, tesztelési, üzem előtti és éles környezetben) zökkenőmentes módon legyen telepítve. Létrehozhat egy új környezetet, amely a Azure Container Service Docker Swarm-fürtöt jelképezi.

![Azure DevOps Services – kiadás az ACS-be](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Kezdeti kiadás beállítása

1. Kiadási folyamat létrehozásához kattintson a **kiadások** > **+ kiadás** elemre.

1. Az összetevő forrásának konfigurálásához kattintson **az** > összetevők**csatolása az összetevők forrásához**elemre. Itt csatolja az új kiadási folyamatot az előző lépésben megadott buildhez. Ezzel a Docker-compose. YML fájl elérhető a kiadási folyamatban.

    ![Azure DevOps Services – kiadási összetevők](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. A kiadási eseményindító konfigurálásához kattintson az **Eseményindítók** elemre, és válassza a **folyamatos üzembe helyezés**lehetőséget. Állítsa az triggert ugyanarra az összetevő-forrásra. Ezzel a beállítással biztosíthatja, hogy az új kiadás azonnal induljon el, amint a Build sikeresen befejeződik.

    ![Azure DevOps Services – kiadási eseményindítók](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>A kiadási munkafolyamat megadása

A kiadási munkafolyamat két felvenni kívánt feladatból áll.

1. Konfiguráljon egy feladatot úgy, hogy a Docker Swarm főcsomópontján lévő, a korábban konfigurált SSH-kapcsolatok használatával biztonságosan másolja a levélírás fájlt egy *központi telepítés* mappájába. A részletekért tekintse meg a következő képernyőt.

    ![Azure DevOps Services – kiadási SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Konfiguráljon egy második feladatot egy bash-parancs futtatásához `docker` , `docker-compose` és futtassa a parancsot a fő csomóponton. A részletekért tekintse meg a következő képernyőt.

    ![Azure DevOps Services – kiadási bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    A főkiszolgálón futtatott parancs a Docker CLI-t és a Docker-levélírás CLI-t használja a következő feladatok elvégzéséhez:

   - Jelentkezzen be az Azure Container registrybe (Ez három, a **változók** lapon definiált Build variab'les használ)
   - Adja meg a **DOCKER_HOST** változót a Swarm-végponttal való együttműködéshez (: 2375)
   - Navigáljon az előző biztonságos másolási feladat által létrehozott, a Docker-compose. YML fájlt tartalmazó *központi telepítés* mappájához. 
   - Hajtsa végre `docker-compose` az új képeket lekérő parancsokat, állítsa le a szolgáltatásokat, távolítsa el a szolgáltatásokat, és hozza létre a tárolókat.

     >[!IMPORTANT]
     > Az előző képernyőn látható módon hagyja bejelölve a **sikertelen stderr** jelölőnégyzetet. Ez egy fontos beállítás, mert `docker-compose` számos diagnosztikai üzenetet (például a tárolók leállítása vagy törlése) nyomtat a standard hiba kimenetén. Ha bejelöli a jelölőnégyzetet, az Azure DevOps Services jelentést küld a kiadás során felmerülő hibákról, még akkor is, ha az összes jól megy.
     >
1. Az új kiadási folyamat mentése.


>[!NOTE]
>Ez az üzembe helyezés néhány állásidőt tartalmaz, mivel a régi szolgáltatások leállítása és az új futtatása folyamatban van. Ezt elkerülheti egy kék-zöld telepítéssel.
>

## <a name="step-4-test-the-cicd-pipeline"></a>4. lépés A CI/CD-folyamat tesztelése

Most, hogy elkészült a konfigurációval, itt az ideje, hogy tesztelje ezt az új CI/CD-folyamatot. A legkönnyebben tesztelhető, hogy frissítse a forráskódot, és véglegesítse a módosításokat a GitHub-tárházban. Néhány másodperc elteltével a kód elküldése után egy új Build fut az Azure DevOps Servicesben. Ha sikeresen befejeződött, a rendszer új verziót indít el, és az alkalmazás új verzióját telepíti a Azure Container Service-fürtön.

## <a name="next-steps"></a>Következő lépések

* Az Azure DevOps Services szolgáltatással kapcsolatos CI/CD-vel kapcsolatos további információkért tekintse meg az [Azure-folyamatok dokumentációs](/azure/devops/pipelines/?view=azure-devops) cikkét.
