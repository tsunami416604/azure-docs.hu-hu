---
title: "Az Azure Tárolószolgáltatásban Kubernetes a rugó rendszerindító alkalmazás telepítése |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, abban az esetben, ha a Microsoft Azure fürt egy olyan Kubernetes rugó rendszerindító alkalmazás telepítésének a lépéseit."
services: container-service
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.custom: mvc
ms.openlocfilehash: 7f726436b2d459b8c16abb02e07de099abfd8974
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="deploy-a-spring-boot-application-on-a-kubernetes-cluster-in-the-azure-container-service"></a>Spring Boot-alkalmazás üzembe helyezése Kubernetes-fürtön az Azure Container Service-ben

A  **[rugó keretrendszer]**  egy népszerű nyílt forráskódú keretrendszer, amely a fejlesztőket Java webes, mobil és API-alkalmazások létrehozása. Ez az oktatóanyag használja egy mintaalkalmazást használatával létrehozott [rugó rendszerindító], rugó használatával történő gyors használatbavétel a egyezmény adatvezérelt megközelítést.

**[Kubernetes]**  és  **[Docker]**  vannak nyílt forráskódú megoldások, amelyek segítségével a fejlesztők automatizálhatja a telepítés, a méretezés és a felügyeleti a tárolókban lévő futó alkalmazások.

Ez az oktatóanyag bemutatja, hogyan abban az esetben, ha ezek két népszerű, nyílt forráskódú technológia fejlesztése és a Microsoft Azure rugó rendszerindító alkalmazást központilag kombinálásával. Pontosabban, használhat  *[rugó rendszerindító]*  alkalmazásfejlesztés,  *[Kubernetes]*  tároló központi telepítéséhez és a [Azure tároló szolgáltatás (ACS)] az alkalmazás futtatásához.

### <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés; Ha még nem rendelkezik Azure-előfizetéssel, aktiválhatja a [MSDN-előfizetői előnyeit] vagy regisztráljon egy [ingyenes Azure-fiókot].
* A [Azure parancssori felület (CLI)].
* Egy naprakész [Java fejlesztői készlet (JDK)].
* Apache tartozó [Maven] eszköz (3-as verziójához) létrehozása.
* A [Git] ügyfél.
* A [Docker] ügyfél.

> [!NOTE]
>
> Ez az oktatóanyag virtualizálási követelményeinek, mert; virtuális gépen nem kövesse a cikkben leírt lépéseket a fizikai számítógép engedélyezett virtualizációs szolgáltatások kell használnia.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>A rugó rendszerindító létrehozása webalkalmazásban Docker első lépések

A következő lépések végigvezetik a rugó rendszerindító webalkalmazás létrehozása, és helyi tesztelés keresztül.

1. Nyisson meg egy parancssort, és hozzon létre egy helyi könyvtárat az alkalmazás tárolására, és módosítsa a könyvtárhoz; Példa:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   – vagy –
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Klónozott a [rugó rendszerindító a Docker bevezetés] mintaprojektet a könyvtárba.
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Módosítsa a könyvtárat a befejezett projekthez.
   ```
   cd gs-spring-boot-docker
   cd complete
   ```

1. Maven használatával hozza létre, és futtassa a mintaalkalmazást.
   ```
   mvn package spring-boot:run
   ```

1. A webes alkalmazás tesztelése a 8080, illetve a következő tallózással `curl` parancs:
   ```
   curl http://localhost:8080
   ```

1. A következő üzenet jelenik meg: **Hello Docker World**

   ![Keresse meg a helyi mintaalkalmazás][SB01]

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Hozzon létre egy Azure-tároló beállításjegyzék, az Azure parancssori felület használatával

1. Nyisson meg egy parancssort.

1. Jelentkezzen be az Azure-fiókjával:
   ```azurecli
   az login
   ```

1. Hozzon létre egy erőforráscsoportot ebben az oktatóanyagban használt Azure-erőforrások.
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=eastus
   ```

1. Hozzon létre egy saját Azure-tárolót beállításjegyzék erőforráscsoportban. Az oktatóanyag leküldi mintaalkalmazás Docker képként a későbbi lépésekben a beállításjegyzékben. Cserélje le `wingtiptoysregistry` a beállításjegyzék egyedi nevére.
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes--location eastus \
    --name wingtiptoysregistry --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry"></a>Az alkalmazás leküldése a tároló beállításjegyzék

1. Keresse meg a Maven telepítés konfigurációs könyvtára (alapértelmezett ~/.m2/ vagy C:\Users\username\.m2), majd nyissa meg a *settings.xml* fájlt egy szövegszerkesztőben.

1. A tároló beállításjegyzék jelszavát lekérése az Azure parancssori felület.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   ```json
   {
  "name": "password",
  "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. Adja hozzá az Azure-tároló beállításjegyzék-azonosítót és jelszót egy új `<server>` gyűjtemény a *settings.xml* fájlt.
A `id` és `username` a rendszerleíró adatbázis neve. Használja a `password` érték az előző parancsot (idézőjelek nélkül).

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Keresse meg a befejezett projekt könyvtárát rugó rendszerindító alkalmazás (például "*C:\SpringBoot\gs-spring-boot-docker\complete*"vagy"*/users/robert/SpringBoot/gs-spring-boot-docker/complete*"), és nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben.

1. Frissítés a `<properties>` gyűjtemény a *pom.xml* a bejelentkezési kiszolgáló értéke az Azure-tároló rendszerleíró fájlt.

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Frissítés a `<plugins>` gyűjtemény a *pom.xml* fájlt úgy, hogy a `<plugin>` bejelentkezési kiszolgáló címét és a beállításjegyzék nevét tartalmazza az Azure-tároló beállításjegyzék.

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. Keresse meg a befejezett projekt könyvtárát rugó rendszerindító alkalmazás, és a Docker-tároló létrehozásához, és küldje le a lemezképet a beállításjegyzékben a következő parancsot:

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
>  Maven leküldi a kép Azure esetén a következőhöz hasonló hibaüzenet jelenhet meg:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> Ha a következő hibaüzenet jelenik meg, jelentkezzen be az Azure a Docker parancssorból.
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> Majd küldje le a tároló:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`

## <a name="create-a-kubernetes-cluster-on-acs-using-the-azure-cli"></a>Hozzon létre egy Kubernetes fürtöt ACS az Azure parancssori felület használatával

1. Az Azure Tárolószolgáltatásban Kubernetes fürt létrehozása. A következő parancs létrehoz egy *kubernetes* a fürt a *tartománynév-kubernetes* erőforrás csoport és *tartománynév-tárolószolgáltatás* neveként a fürt, és *tartománynév-kubernetes* előtag, a DNS-ben:
   ```azurecli
   az acs create --orchestrator-type=kubernetes --resource-group=wingtiptoys-kubernetes \ 
    --name=wingtiptoys-containerservice --dns-prefix=wingtiptoys-kubernetes
   ```
   Ez a parancs is igénybe vehet igénybe.

1. Telepítés `kubectl` az Azure parancssori felület használatával. Linux-felhasználók úgy lehet, hogy ez a parancs az előtag `sudo` óta telepíti a Kubernetes CLI való `/usr/local/bin`.
   ```azurecli
   az acs kubernetes install-cli
   ```

1. Töltse le a fürt konfigurációs adatokat, kezelheti a fürt a Kubernetes webes felület, és `kubectl`. 
   ```azurecli
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes  \ 
    --name=wingtiptoys-containerservice
   ```

## <a name="deploy-the-image-to-your-kubernetes-cluster"></a>A lemezkép telepítéséhez a Kubernetes fürt

Ez az oktatóanyag telepíti az alkalmazást használó `kubectl`, majd engedélyezze segítségével megismerheti a telepítés a Kubernetes webes felületen keresztül.

### <a name="deploy-with-the-kubernetes-web-interface"></a>A Kubernetes webes felület telepítése

1. Nyisson meg egy parancssort.

1. Az alapértelmezett böngészőben nyissa meg a konfigurációs webhely a Kubernetes fürt:
   ```
   az acs kubernetes browse --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-containerservice
   ```

1. Ha a Kubernetes konfigurációs webhely megnyitása a böngészőben, kattintson a hivatkozásra kattintva **indexelése alkalmazás telepítése**:

   ![Kubernetes konfigurációs webhely][KB01]

1. Ha a **indexelése alkalmazás telepítése** lap is megjelenik, adja meg a következő beállításokat:

   a. Válassza ki **adja meg az alkalmazás adatait az alábbi**.

   b. A rugó rendszerindító alkalmazás nevét adja meg a **alkalmazásnév**; például: "*gs-rugó-rendszerindítás – docker*".

   c. Adja meg a bejelentkezési kiszolgáló és a tároló lemezképét a korábban a **tároló kép**; például: "*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*".

   d. Válasszon **külső** a a **szolgáltatás**.

   e. Adja meg a külső és belső portjainak a **Port** és **céloz port** szövegmezőket.

   ![Kubernetes konfigurációs webhely][KB02]


1. Kattintson a **telepítés** a tároló üzembe.

   ![Tároló üzembe][KB05]

1. Miután az alkalmazás telepítve van, megjelenik-e a felsorolt rugó rendszerindító alkalmazás **szolgáltatások**.

   ![Kubernetes szolgáltatások][KB06]

1. Ha a hivatkozásra kattintva **külső végpontok száma**, az Azure-on futó rugó rendszerindító alkalmazás látható.

   ![Kubernetes szolgáltatások][KB07]

   ![Keresse meg a mintaalkalmazás az Azure-on][SB02]


### <a name="deploy-with-kubectl"></a>Kubectl üzembe helyezéséhez

1. Nyisson meg egy parancssort.

1. A tároló a Kubernetes fürt használatával futtassa a `kubectl run` parancsot. Adja meg az alkalmazást a Kubernetes szolgáltatás nevét és a teljes kép neve. Példa:
   ```
   kubectl run gs-spring-boot-docker --image=wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest
   ```
   Ebben a parancsban:

   * A tároló neve `gs-spring-boot-docker` közvetlenül után van megadva a `run` parancs

   * A `--image` paraméter határozza meg a kombinált bejelentkezési kiszolgáló- és képfájlok név szerint`wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest`

1. Külsőleg használatával teszi közzé a Kubernetes fürt a `kubectl expose` parancsot. Adja meg a szolgáltatás, az alkalmazás eléréséhez használt nyilvánosan elérhető TCP-portot, és figyeli az alkalmazás belső cél portot. Példa:
   ```
   kubectl expose deployment gs-spring-boot-docker --type=LoadBalancer --port=80 --target-port=8080
   ```
   Ebben a parancsban:

   * A tároló neve `gs-spring-boot-docker` közvetlenül után van megadva a `expose deployment` parancs

   * A `--type` paraméter határozza meg, hogy a fürt használja-e a terheléselosztó

   * A `--port` paraméter határozza meg a nyilvánosan elérhető TCP-port a 80-as. Az alkalmazás ezen a porton érhető el.

   * A `--target-port` paraméter határozza meg a belső, a 8080-as TCP-portot. A load balancer továbbítja a kérelmeket az alkalmazáshoz ezen a porton.

1. Miután az alkalmazás telepítve van a fürt, lekérdezni a külső IP-cím, és nyissa meg a böngészőben:

   ```
   kubectl get services -o jsonpath={.items[*].status.loadBalancer.ingress[0].ip} --namespace=${namespace}
   ```

   ![Keresse meg a mintaalkalmazás az Azure-on][SB02]


## <a name="next-steps"></a>Következő lépések

Azure rugó rendszerindítással kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Az Azure App Service egy rugó rendszerindító alkalmazás központi telepítése](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [A rugó rendszerindító alkalmazás az Azure Tárolószolgáltatásban Linux központi telepítése](container-service-deploy-spring-boot-app-on-linux.md)

Az Azure Javával való használatáról további információ: [Azure Java fejlesztői központ] és [Java-eszközök a Visual Studio Team Serviceshez].

A Docker mintaprojektet a rugó rendszerindító kapcsolatos további információkért lásd: [rugó rendszerindító a Docker bevezetés].

A következő hivatkozásokra kattintva rugó rendszerindító alkalmazások létrehozásáról további információt:

* Egy egyszerű rugó rendszerindító alkalmazás létrehozásával kapcsolatos további információkért tekintse meg a következő https://start.spring.io/ rugó Initializr.

A következő hivatkozásokra kattintva Kubernetes az Azure-ral való használatáról további információkat:

* [A Tárolószolgáltatás Kubernetes fürt első lépések](https://docs.microsoft.com/azure/container-service/container-service-kubernetes-walkthrough)
* [Azure Tárolószolgáltatás a Kubernetes webes felhasználói felület használatával](https://docs.microsoft.com/azure/container-service/container-service-kubernetes-ui)

Kubernetes parancssori felület használatával kapcsolatos további információkat a **kubectl** felhasználói útmutatóban talál: <https://kubernetes.io/docs/user-guide/kubectl/>.

A Kubernetes webhelyhez van több olyan cikket TITKOS nyilvántartó képek használatát ismertetik, amelyek:

* [Három munkaállomás-csoporttal fiókjainak szolgáltatás konfigurálása]
* [Névterek]
* [Húzza a lemezkép egy titkos beállításjegyzékből]

Az egyéni Docker lemezképek használata Azure további példákért lásd [Azure webalkalmazás Linux rendszeren egyéni Docker-lemezkép használatával].

<!-- URL List -->

[Azure parancssori felület (CLI)]: /cli/azure/overview
[Azure tároló szolgáltatás (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java fejlesztői központ]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Azure webalkalmazás Linux rendszeren egyéni Docker-lemezkép használatával]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[ingyenes Azure-fiókot]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java fejlesztői készlet (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java-eszközök a Visual Studio Team Serviceshez]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Kubernetes Command-Line Interface (kubectl)]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[Maven]: http://maven.apache.org/
[MSDN-előfizetői előnyeit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[rugó rendszerindító]: http://projects.spring.io/spring-boot/
[rugó rendszerindító a Docker bevezetés]: https://github.com/spring-guides/gs-spring-boot-docker
[rugó keretrendszer]: https://spring.io/
[Három munkaállomás-csoporttal fiókjainak szolgáltatás konfigurálása]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/
[Névterek]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[Húzza a lemezkép egy titkos beállításjegyzékből]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR04.png

[KB01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB01.png
[KB02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB02.png
[KB03]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB03.png
[KB04]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB04.png
[KB05]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB05.png
[KB06]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB06.png
[KB07]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB07.png
