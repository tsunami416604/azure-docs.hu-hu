---
title: 'Rövid útmutató: Java-alkalmazás létrehozása az Azure Service Fabric szolgáltatásban'
description: Ez a rövid útmutató azt ismerteti, hogyan lehet Java-alkalmazást létrehozni az Azure-ban a Service Fabric Reliable Services mintaalkalmazásának használatával.
author: suhuruli
ms.topic: quickstart
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: fc615149b092aebfdde767fb3b716fb897bfd551
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121508"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Rövid útmutató: Java-alkalmazás üzembe helyezése linuxos Azure Service Fabric szolgáltatásba

Ebben a rövid útmutatóban üzembe helyez egy Java-alkalmazást az Azure Service Fabric-re az Eclipse IDE használatával egy Linux fejlesztői gépen. Az útmutató elvégzése után rendelkezni fog egy Java webes kezelőfelületes szavazóalkalmazással, amely egy a fürtben található állapotalapú háttérszolgáltatásba menti a szavazati adatokat.

Az Azure Service Fabric egy elosztott rendszerplatform, amely mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál.

## <a name="prerequisites"></a>Előfeltételek

- [Java környezet](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) és [Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclipse Neon (4.6)+](https://www.eclipse.org/downloads/packages/) és [Eclipse plug-in a Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#install-the-eclipse-plug-in-optional)
- [Service Fabric SDK és parancssori felület (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
- [Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>A minta letöltése

Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

1. A helyi fürt elindításához futtassa a következő parancsot:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    A helyi fürt elindítása eltarthat egy ideig. A fürt üzembe állásának ellenőrzéséhez nyissa meg a Service Fabric Explorert a `http://localhost:19080` címen. Ha öt kifogástalan csomópontot lát, a fürt megfelelően üzemel.

    ![Az Azure Service Fabric Explorer kifogástalan állapotú csomópontokat jelenít meg](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Nyissa meg az Eclipse környezetet.
3. Válassza **a Meglévő színátmenet-importálási** > **Gradle** > **projekt** **et,** > és kövesse a varázslót.
4. Válassza a **Címtárat,** és válassza ki a **szavazási** könyvtárat a GitHubról klónozott **service-fabric-java-quickstart** mappából. Válassza a **Finish** (Befejezés) elemet.

    ![Gradle projekt importálása az Eclipse programba](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Ezzel létrehozta a `Voting` projektet az Eclipse-hez készült Package Explorerben.
6. Kattintson a jobb gombbal a projektre, és válassza az **Alkalmazás közzététele parancsot** a **Service Fabric** legördülő menüalatt. Válassza **a PublishProfiles/Local.json parancsot** célprofilként, majd a **Közzététel**lehetőséget.

    ![Az Azure Service Fabric közzéteszi a helyi JSON-t](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Nyissa meg a kedvenc webböngészőjét, és nyissa meg az alkalmazást a `http://localhost:8080` címen.

    ![Az Azure Service Fabric helyi gazdagépe](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Most hozzáadhat szavazási lehetőségeket az alkalmazáshoz, és megkezdheti a szavazatok gyűjtését. Az alkalmazás különálló adatbázis nélkül futtatja és tárolja az összes adatot a Service Fabric-fürtben.

![Az Azure Service Fabric szavazási minta](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Alkalmazások és szolgáltatások méretezése a fürtökben

A szolgáltatások skálázhatók egy adott fürtben, hogy kövessék a szolgáltatások terhelésének változásait. A szolgáltatások méretezése a fürtben futó példányok számának módosításával történik. A szolgáltatások méretezésének számos módja van. Használhatja például a Service Fabric CLI (`sfctl`parancsfájljait vagy parancsait. A következő lépések során a Service Fabric Explorert használjuk.

A Service Fabric Explorer az összes Service Fabric-fürtön fut, és a fürt HTTP-felügyeleti portjára (19080) való böngészéssel érhető el egy böngészőből. Például: `http://localhost:19080`.

A webes előtér-szolgáltatás skálázásához tegye a következőket:

1. Nyissa meg a Service Fabric Explorert a fürtben. Például: `https://localhost:19080`.
2. Válassza ki a három pontot (**...**) a **fabric:/Voting/VotingWeb** csomópont mellett a treeview-ban, és válassza a **Méretezésszolgáltatás lehetőséget.**

    ![Szolgáltatás méretezése az Azure Service Fabricben](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Most már méretezheti a webes előtér-szolgáltatás példányainak számát.

3. Módosítsa a **2** számot 2-re, és válassza **a Szolgáltatás méretezése**lehetőséget.
4. Válassza ki a **fabric:/Voting/VotingWeb** csomópontot a fanézetben, és bontsa ki a partíciócsomópontot (amelyet GUID képvisel).

    ![Skálázott szolgáltatás az Azure Service Fabricben](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Ekkor láthatja, hogy a szolgáltatás két példányban jelenik meg, a fanézetben pedig megtekintheti, hogy a példányok melyik csomópontokon futnak.

Ezzel az egyszerű felügyeleti eljárással megduplázta az előtér-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos tudni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány elinduljon a fürtben.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Az Eclipse használata a Service Fabricben üzemeltetett Java-alkalmazásokhoz
* Java-alkalmazások központi telepítése a helyi fürtre
* Az alkalmazás horizontális felskálázása több csomópontra

Ha bővebb információra van szüksége a Java-alkalmazások Service Fabricben való használatával kapcsolatban, lépjen tovább a Java-alkalmazásokról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Java-alkalmazás üzembe helyezése](./service-fabric-tutorial-create-java-app.md)
