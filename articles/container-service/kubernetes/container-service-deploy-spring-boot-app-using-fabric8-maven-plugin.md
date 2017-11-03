---
title: "A Fabric8 Maven beépülő modul használatával rugó rendszerindító alkalmazás telepítése"
description: "Ez az oktatóanyag bemutatja, hogyan, ha a Microsoft Azure-ban a Fabric8 beépülő modul az Apache Maven rugó rendszerindító alkalmazás telepítésének a lépéseit."
services: container-service
documentationcenter: java
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 09/15/2017
ms.author: yuwzho;robmcm
ms.custom: 
ms.openlocfilehash: 6ec48caef0eebeacf260b0dbc91b175368faea69
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="deploy-a-spring-boot-app-using-the-fabric8-maven-plugin"></a>A Fabric8 Maven beépülő modul használatával rugó rendszerindító alkalmazás telepítése

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

**[Fabric8]**  egy nyílt forráskódú megoldás, amely a épül  **[Kubernetes]**, amely segít a fejlesztők alkalmazások létrehozása Linux-tárolókban lévő.

Ez az oktatóanyag bemutatja, hogyan fejlesztéséhez egy Linux gazdagépre, az alkalmazás központi telepítése a Fabric8 beépülő modul a Maven használata a [Azure tároló szolgáltatás (ACS)].

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban szereplő lépések végrehajtásához kell rendelkeznie a következő előfeltételek teljesülését:

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
   ```shell
   md /home/GenaSoto/SpringBoot
   cd /home/GenaSoto/SpringBoot
   ```
   – vagy –
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```

1. Klónozott a [rugó rendszerindító a Docker bevezetés] mintaprojektet a könyvtárba.
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Módosítsa a könyvtárat a befejezett projekthez; Példa:
   ```shell
   cd gs-spring-boot-docker/complete
   ```
   – vagy –
   ```shell
   cd gs-spring-boot-docker\complete
   ```

1. Maven használatával hozza létre, és futtassa a mintaalkalmazást.
   ```shell
   mvn clean package spring-boot:run
   ```

1. A webes alkalmazás tesztelése a 8080, illetve a következő tallózással `curl` parancs:
   ```shell
   curl http://localhost:8080
   ```

   Megjelenik egy **Hello Docker World** üzenet jelenik meg.

   ![Keresse meg a helyi mintaalkalmazás][SB01]


## <a name="install-the-kubernetes-command-line-interface-and-create-an-azure-resource-group-using-the-azure-cli"></a>A Kubernetes parancssori felület telepítése és az Azure parancssori felület használatával Azure erőforráscsoport-csoport létrehozása

1. Nyisson meg egy parancssort.

1. Írja be az Azure-fiókjával jelentkezzen be a következő parancsot:
   ```azurecli
   az login
   ```
   Kövesse az utasításokat a bejelentkezési folyamat elvégzése
   
   Az Azure parancssori felület megjeleníti a fiókok; Példa:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "00000000-0000-0000-0000-000000000000",
       "isDefault": false,
       "name": "Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "00000000-0000-0000-0000-000000000000",
       "user": {
         "name": "Gena.Soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. Ha még nem rendelkezik a Kubernetes parancssori felület (`kubectl`) telepítve, telepítheti az Azure parancssori felülettel; például:
   ```azurecli
   az acs kubernetes install-cli
   ```

   > [!NOTE]
   >
   > Linux-felhasználók úgy lehet, hogy ez a parancs az előtag `sudo` óta telepíti a Kubernetes CLI való `/usr/local/bin`.
   >
   > Ha már rendelkezik `kubectl`) telepítve és verziójának `kubectl` van túl régi, előfordulhat, hogy egy hibaüzenetet lát a következőhöz hasonló el az ebben a cikkben ismertetett lépéseket tett kísérlet során:
   >
   > ```
   > error: group map[autoscaling:0x0000000000 batch:0x0000000000 certificates.k8s.io
   > :0x0000000000 extensions:0x0000000000 storage.k8s.io:0x0000000000 apps:0x0000000
   > 000 authentication.k8s.io:0x0000000000 policy:0x0000000000 rbac.authorization.k8
   > s.io:0x0000000000 federation:0x0000000000 authorization.k8s.io:0x0000000000 comp
   > onentconfig:0x0000000000] is already registered
   > ```
   >
   > Ha ez történik, akkor telepítse újra a `kubectl` a verzióra frissíteni.
   >

1. Hozzon létre egy erőforráscsoportot az Azure-erőforrások, amelyeket ebben az oktatóanyagban; fog használni Példa:
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=westeurope
   ```
   Az elemek magyarázata:  
      * *tartománynév-kubernetes* egyedi név az erőforráscsoport számára  
      * *westeurope* egy megfelelő földrajzi hely, az alkalmazás  

   Az Azure parancssori felület megjeleníti az erőforrás-csoport létrehozása; eredményei Példa:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes",
     "location": "westeurope",
     "managedBy": null,
     "name": "wingtiptoys-kubernetes",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```


## <a name="create-a-kubernetes-cluster-using-the-azure-cli"></a>Az Azure parancssori felület használatával Kubernetes fürt létrehozása

1. Az új erőforráscsoportban; Kubernetes fürt létrehozása Példa:  
   ```azurecli 
   az acs create --orchestrator-type kubernetes --resource-group wingtiptoys-kubernetes --name wingtiptoys-cluster --generate-ssh-keys --dns-prefix=wingtiptoys
   ```
   Az elemek magyarázata:  
      * *tartománynév-kubernetes* az ebben a cikkben az erőforrás-csoport neve  
      * *tartománynév-fürt* a Kubernetes fürt egyedi neve
      * *tartománynév* egy egyedi nevet DNS-neve az alkalmazáshoz

   Az Azure parancssori felület megjeleníti a fürt létrehozása; eredményei Példa:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.Resources/deployments/azurecli0000000000.00000000000",
     "name": "azurecli0000000000.00000000000",
     "properties": {
       "correlationId": "00000000-0000-0000-0000-000000000000",
       "debugSetting": null,
       "dependencies": [],
       "mode": "Incremental",
       "outputs": {
         "masterFQDN": {
           "type": "String",
           "value": "wingtiptoysmgmt.westeurope.cloudapp.azure.com"
         },
         "sshMaster0": {
           "type": "String",
           "value": "ssh azureuser@wingtiptoysmgmt.westeurope.cloudapp.azure.com -A -p 22"
         }
       },
       "parameters": {
         "clientSecret": {
           "type": "SecureString"
         }
       },
       "parametersLink": null,
       "providers": [
         {
           "id": null,
           "namespace": "Microsoft.ContainerService",
           "registrationState": null,
           "resourceTypes": [
             {
               "aliases": null,
               "apiVersions": null,
               "locations": [
                 "westeurope"
               ],
               "properties": null,
               "resourceType": "containerServices"
             }
           ]
         }
       ],
       "provisioningState": "Succeeded",
       "template": null,
       "templateLink": null,
       "timestamp": "2017-09-15T01:00:00.000000+00:00"
     },
     "resourceGroup": "wingtiptoys-kubernetes"
   }
   ```

1. Töltse le a hitelesítő adatokat az új Kubernetes fürthöz; Példa:  
   ```azurecli 
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes --name wingtiptoys-cluster
   ```

1. A kapcsolat ellenőrzése a következő paranccsal:
   ```shell 
   kubectl get nodes
   ```

   A csomópontok és az alábbi példához hasonló állapotok listáját kell megjelennie:

   ```shell
   NAME                    STATUS                     AGE       VERSION
   k8s-agent-00000000-0    Ready                      5h        v1.6.6
   k8s-agent-00000000-1    Ready                      5h        v1.6.6
   k8s-agent-00000000-2    Ready                      5h        v1.6.6
   k8s-master-00000000-0   Ready,SchedulingDisabled   5h        v1.6.6
   ```


## <a name="create-a-private-azure-container-registry-using-the-azure-cli"></a>Hozza létre a saját Azure-tárolót beállításkulcs, az Azure parancssori felület használatával

1. Hozzon létre egy saját Azure-tárolót beállításjegyzék üzemeltetéséhez a Docker-lemezkép; az erőforráscsoportban Példa:
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes --location westeurope --name wingtiptoysregistry --sku Basic
   ```
   Az elemek magyarázata:  
      * *tartománynév-kubernetes* az ebben a cikkben az erőforrás-csoport neve  
      * *wingtiptoysregistry* a személyes beállításjegyzék egyedi neve
      * *westeurope* egy megfelelő földrajzi hely, az alkalmazás  

   Az Azure parancssori felület megjeleníti a beállításjegyzék létrehozásának; eredményei Példa:  

   ```json
   {
     "adminUserEnabled": true,
     "creationDate": "2017-09-15T01:00:00.000000+00:00",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.ContainerRegistry/registries/wingtiptoysregistry",
     "location": "westeurope",
     "loginServer": "wingtiptoysregistry.azurecr.io",
     "name": "wingtiptoysregistry",
     "provisioningState": "Succeeded",
     "resourceGroup": "wingtiptoys-kubernetes",
     "sku": {
       "name": "Basic",
       "tier": "Basic"
     },
     "storageAccount": {
       "name": "wingtiptoysregistr000000"
     },
     "tags": {},
     "type": "Microsoft.ContainerRegistry/registries"
   }
   ```

1. A tároló beállításjegyzék jelszavát lekérése az Azure parancssori felület.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   Az Azure parancssori felület megjeleníti a beállításjegyzék; jelszavát Példa:  

   ```json
   {
     "name": "password",
     "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. Keresse meg a Maven telepítés konfigurációs könyvtára (alapértelmezett ~/.m2/ vagy C:\Users\username\.m2), majd nyissa meg a *settings.xml* fájlt egy szövegszerkesztőben.

1. Az Azure tároló beállításjegyzék URL-cím, a felhasználónév és a jelszó hozzáadása egy új `<server>` gyűjtemény a *settings.xml* fájlt.
A `id` és `username` a rendszerleíró adatbázis neve. Használja a `password` érték az előző parancsot (idézőjelek nélkül).

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry.azurecr.io</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Keresse meg a befejezett projekt könyvtárát rugó rendszerindító alkalmazás (például "*C:\SpringBoot\gs-spring-boot-docker\complete*"vagy"*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete* "), és nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben.

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
     <artifactId>dockerfile-maven-plugin</artifactId>
     <version>1.3.4</version>
     <configuration>
       <repository>${docker.image.prefix}/${project.artifactId}</repository>
       <serverId>${docker.image.prefix}</serverId>
       <registryUrl>https://${docker.image.prefix}</registryUrl>
     </configuration>
   </plugin>
   ```

1. Keresse meg a befejezett projektkönyvtárban rugó rendszerindító alkalmazás, és futtassa a következő Maven-parancsot a Docker-tároló létrehozásához, és küldje le a lemezképet a beállításjegyzékben:

   ```shell
   mvn package dockerfile:build -DpushImage
   ```

   Maven megjeleníti az eredményeket a build; Példa:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 38.113 s
   [INFO] Finished at: 2017-09-15T02:00:00-07:00
   [INFO] Final Memory: 47M/338M
   [INFO] ----------------------------------------------------
   ```


## <a name="configure-your-spring-boot-app-to-use-the-fabric8-maven-plugin"></a>A rugó rendszerindító alkalmazás használata a Fabric8 Maven beépülő modul konfigurálása

1. Keresse meg a befejezett projekt könyvtárát a rugó rendszerindító alkalmazás (például: "*C:\SpringBoot\gs-spring-boot-docker\complete*"vagy"*/home/GenaSoto/SpringBoot/gs-spring-boot-docker / teljes*"), és nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben.

1. Frissítés a `<plugins>` gyűjtemény a *pom.xml* fájl a Fabric8 Maven beépülő modul hozzáadása:

   ```xml
   <plugin>
     <groupId>io.fabric8</groupId>
     <artifactId>fabric8-maven-plugin</artifactId>
     <version>3.5.30</version>
     <configuration>
       <ignoreServices>false</ignoreServices>
       <registry>${docker.image.prefix}</registry>
     </configuration>
   </plugin>
   ```

1. A rugó rendszerindító alkalmazás fő forráskönyvtár navigáljon (például: "*C:\SpringBoot\gs-spring-boot-docker\complete\src\main*"vagy"*/home/GenaSoto/SpringBoot/gs-spring-boot-docker / Fejezze be/src/main*"), és hozzon létre egy új mappát"*fabric8*".

1. Hozzon létre három YAM töredék fájlokat az új *fabric8* mappába:

   a. Hozzon létre egy fájlt **deployment.yml** a következő tartalommal:
      ```yaml
      apiVersion: extensions/v1beta1
      kind: Deployment
      metadata:
        name: ${project.artifactId}
        labels:
          run: gs-spring-boot-docker
      spec:
        replicas: 1
        selector:
          matchLabels:
            run: gs-spring-boot-docker
        strategy:
          rollingUpdate:
            maxSurge: 1
            maxUnavailable: 1
          type: RollingUpdate
        template:
          metadata:
            labels:
              run: gs-spring-boot-docker
          spec:
            containers:
            - image: ${docker.image.prefix}/${project.artifactId}:latest
              name: gs-spring-boot-docker
              imagePullPolicy: Always
              ports:
              - containerPort: 8080
            imagePullSecrets:
              - name: mysecrets
      ```

   b. Hozzon létre egy fájlt **secrets.yml** a következő tartalommal:
      ```yaml
      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecrets
        namespace: default
        annotations:
          maven.fabric8.io/dockerServerId:  ${docker.image.prefix}
      type: kubernetes.io/dockercfg
      ```

   c. Hozzon létre egy fájlt **service.yml** a következő tartalommal:
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: ${project.artifactId}
        labels:
          expose: "true"
      spec:
        ports:
        - port: 80
          targetPort: 8080
        type: LoadBalancer
      ```

1. Futtassa a következő Maven-parancsot hozhat létre az Kubernetes lista fájlhoz:

   ```shell
   mvn fabric8:resource
   ```

   Ez a parancs minden Kubernetes yam Erőforrásfájlok alatt egyesíti a *src/main/fabric8* mappa, amely tartalmazza a Kubernetes erőforrás listáját, és közvetlenül a fürt Kubernetes vagy helm diagram exportálás alkalmazható YAM fájlba.

   Maven megjeleníti az eredményeket a build; Példa:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 16.744 s
   [INFO] Finished at: 2017-09-15T02:35:00-07:00
   [INFO] Final Memory: 30M/290M
   [INFO] ----------------------------------------------------
   ```

1. Futtassa a következő Maven-parancsot az erőforrásfájl lista alkalmazása a Kubernetes fürt:

   ```shell
   mvn fabric8:apply
   ```

   Maven megjeleníti az eredményeket a build; Példa:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 14.814 s
   [INFO] Finished at: 2017-09-15T02:41:00-07:00
   [INFO] Final Memory: 35M/288M
   [INFO] ----------------------------------------------------
   ```

1. Ha az alkalmazás telepítve van a fürt, lekérdezés a külső IP cím használatával a `kubectl` alkalmazás, például:
   ```shell
   kubectl get svc -w
   ```

   `kubectl`Megjeleníti a belső és külső IP-címek; Példa:
   
   ```shell
   NAME                    CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
   kubernetes              10.0.0.1     <none>        443/TCP        19h
   gs-spring-boot-docker   10.0.242.8   13.65.196.3   80:31215/TCP   3m
   ```
   
   A külső IP-cím segítségével nyissa meg az alkalmazást egy webböngészőben.

   ![Keresse meg a külsőleg mintaalkalmazás][SB02]

## <a name="delete-your-kubernetes-cluster"></a>A Kubernetes fürt törlése

Ha már nincs szükség a Kubernetes fürt, használhatja a `az group delete` parancs beírásával távolítsa el az erőforráscsoportot, amely eltávolítja a kapcsolódó erőforrásokat; például:

   ```azurecli
   az group delete --name wingtiptoys-kubernetes --yes --no-wait
   ```

## <a name="next-steps"></a>Következő lépések

Azure rugó rendszerindító alkalmazások használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Az Azure App Service egy rugó rendszerindító alkalmazás központi telepítése](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [A rugó rendszerindító alkalmazás az Azure Tárolószolgáltatásban Linux központi telepítése](container-service-deploy-spring-boot-app-on-linux.md)
* [A rugó rendszerindító alkalmazás az az Azure Tárolószolgáltatásban Kubernetes fürt központi telepítése](container-service-deploy-spring-boot-app-on-kubernetes.md)

Az Azure Javával való használatáról további információ: [Azure Java fejlesztői központ] és [Java-eszközök a Visual Studio Team Serviceshez].

A rugó rendszerindító Docker mintaprojektet a további részleteiért lásd: [rugó rendszerindító a Docker bevezetés].

Első lépések a saját rugó rendszerindító alkalmazásokkal kapcsolatban lásd: a **rugó Initializr** : <https://start.spring.io/>.

Első lépések egy egyszerű rugó rendszerindító alkalmazás létrehozásával kapcsolatos további információkért tekintse meg a következő rugó Initializr <https://start.spring.io/>.

Az egyéni Docker lemezképek használata Azure további példákért lásd [Azure webalkalmazás Linux rendszeren egyéni Docker-lemezkép használatával].

<!-- URL List -->

[Azure parancssori felület (CLI)]: /cli/azure/overview
[Azure tároló szolgáltatás (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java fejlesztői központ]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Azure webalkalmazás Linux rendszeren egyéni Docker-lemezkép használatával]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Fabric8]: https://fabric8.io/
[ingyenes Azure-fiókot]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java fejlesztői készlet (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java-eszközök a Visual Studio Team Serviceshez]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Maven]: http://maven.apache.org/
[MSDN-előfizetői előnyeit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[rugó rendszerindító a Docker bevezetés]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB02.png
