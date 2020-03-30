---
title: Rövid útmutató – Java-tárolórendszerképek létrehozása és leküldése az Azure Container Registry szolgáltatásba a Maven és a Jib használatával
description: Hozzon létre egy tárolóba épített Java-alkalmazást, és a Maven Jib beépülő modul használatával leküldéses az Azure Container Registry alkalmazásba.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: 62d63b24baab204cb029565b109ea2de768e1d80
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78165445"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Rövid útmutató: Java-tárolórendszerképek létrehozása és leküldése az Azure Container Registry szolgáltatásba

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy tárolóba épített Java-alkalmazást, és hogyan kell leadni az Azure Container Registry-hez a Maven Jib beépülő modul használatával. A Maven és a Jib használata egy példa a fejlesztői eszközök használatával egy Azure-tároló beállításjegyzékkel való interakcióra.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial).
* Az [Azure parancssori felület (CLI)](/cli/azure/overview).
* Egy támogatott Java fejlesztői készlet (JDK). További információ az Azure-beli fejlesztéshez rendelkezésre álló JDK-król: <https://aka.ms/azure-jdks>.
* Apache's [Maven](http://maven.apache.org) épít szerszám ( változat 3 vagy újabb).
* Egy [Git](https://git-scm.com)-ügyfél.
* Egy [Docker](https://www.docker.com)-ügyfél.
* Az [ACR Docker hitelesítő adatok segítője.](https://github.com/Azure/acr-docker-credential-helper)

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>A Spring Boot on Docker – Első lépések webalkalmazás létrehozása

A következő lépések végigvezetik egy Spring Boot-webalkalmazás összeállításán és helyszíni tesztelésén.

1. A parancssorból a következő paranccsal klónozhat a [Spring Boot-ot a Docker első lépések](https://github.com/spring-guides/gs-spring-boot-docker) mintaprojektjén.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Váltsa a könyvtárat a befejezett projektre.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Használja a Mavent a mintaalkalmazás összeállításához és futtatásához.

   ```bash
   mvn package spring-boot:run
   ```

1. Tesztelje a webalkalmazást a `http://localhost:8080` cím megnyitásával vagy az alábbi `curl` paranccsal:

   ```bash
   curl http://localhost:8080
   ```

A következő üzenetnek kell láthatód: **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Azure Container Registry létrehozása az Azure CLI-vel

Ezután hozzon létre egy Azure-erőforráscsoportot és az ACR-t az alábbi lépésekkel:

1. Jelentkezzen be Azure-fiókjába a következő paranccsal:

   ```azurecli
   az login
   ```

1. Adja meg a használni kívánt Azure-előfizetést:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Hozzon létre egy erőforráscsoportot az oktatóanyagban használt Azure-erőforrások számára. A következő parancsban mindenképpen cserélje le a helyőrzőket a `eastus`saját erőforrásnevére és egy helyre, például .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Hozzon létre egy privát Azure-tároló beállításjegyzéket az erőforráscsoportban a következő paranccsal. Ügyeljen arra, hogy a helyőrzőket tényleges értékekre cserélje le. Az oktatóanyag a mintaalkalmazást Docker-lemezképként küldi le ennek a regisztrációs adatbázisnak a későbbi lépésekben.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Az alkalmazás leküldése a regisztrációs adatbázisnak a Jibbel

Végül frissíteni fogja a projekt konfigurációját, és a parancssorból létrehozza és telepíti a lemezképet.

1. Jelentkezzen be az Azure Container Registry az Azure CLI a következő parancs használatával. Ügyeljen arra, hogy a helyőrzőt a saját rendszerleíró adatbázisnevére cserélje le.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   A `az configure` parancs beállítja a `az acr` parancsokkal való alapértelmezett beállításjegyzék-nevet.

1. Lépjen a Spring Boot-alkalmazás befejezett projektkönyvtárába (például „*C:\SpringBoot\gs-spring-boot-docker\complete*” vagy „*/users/robert/SpringBoot/gs-spring-boot-docker/complete*”), és nyissa meg a *pom.xml* fájlt egy szövegszerkesztővel.

1. Frissítse `<properties>` a gyűjteményt a *pom.xml* fájlban a következő XML-fájllal. Cserélje le a helyőrzőt a `<jib-maven-plugin.version>` rendszerleíró adatbázis nevére, és frissítse az értéket a [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)legújabb verziójával.

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <jib-maven-plugin.version>1.8.0</jib-maven-plugin.version>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Frissítse `<plugins>` a gyűjteményt a *pom.xml* fájlban úgy, hogy az `<plugin>` elem tartalmazza és a bejegyzését a `jib-maven-plugin`számára, ahogy az a következő példában látható. Vegye figyelembe, hogy a Microsoft Container Registry (MCR) alaplemezképét használjuk: `mcr.microsoft.com/java/jdk:8-zulu-alpine`, amely egy hivatalosan támogatott Azure-beli JDK-t tartalmaz. A hivatalosan támogatott JDK-kkal rendelkező egyéb MCR alapképeket lásd: [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless), Java [SE JDK és Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Lépjen a Spring Boot-alkalmazás befejezett projektkönyvtárába, és futtassa a következő parancsot a lemezkép elkészítéséhez, majd a könyvtárba való leküldéséhez:

   ```bash
   mvn compile jib:build
   ```

> [!NOTE]
>
> Biztonsági okokból a hitelesítő `az acr login` adatok által létrehozott csak 1 órán keresztül érvényes. Ha *401-es jogosulatlan hibaüzenetet* kap, `az acr login -n <your registry name>` a parancs ismételt futtatásával újra hitelesítheti magát.

## <a name="verify-your-container-image"></a>A tárolókép ének ellenőrzése

Gratulálunk! Most már a konténeres Java App build az Azure-ban támogatott JDK leküldéses az ACR. Most már tesztelheti a lemezképet az Azure App Service-ben való üzembe helyezésével, vagy a helyi parancsra való húzással (a helyőrző cseréje):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker:v1
```

## <a name="next-steps"></a>További lépések

A microsoftáltal támogatott Java alapképek egyéb verzióit a következő témakörben tetszésszerint.

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE fej nélküli JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK és Maven](https://hub.docker.com/_/microsoft-java-maven)

Ha szeretne többet megtudni a Spring és az Azure szolgáltatásról, lépjen tovább a Spring on Azure dokumentációs központra.

> [!div class="nextstepaction"]
> [A Spring az Azure-on](/azure/java/spring-framework)

### <a name="additional-resources"></a>További források

További információkért lásd a következőket:

* [Azure Java fejlesztőknek](/azure/java)
* [Az Azure DevOps és a Java használata](/azure/devops/java)
* [Spring Boot on Docker – Első lépések](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Spring Boot-alkalmazás üzembe helyezése az Azure App Service-ben](/azure/java/spring-framework/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin)
* [Egyéni Docker-rendszerkép használata az Azure Web App on Linuxban](/azure/app-service-web/app-service-linux-using-custom-docker-image)
