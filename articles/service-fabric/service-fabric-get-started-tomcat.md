---
title: Az Apache Tomcat kiszolgálót egy Azure Service Fabric-tároló létrehozása Linux rendszeren |} A Microsoft Docs
description: Hozzon létre Linux-tárolón történő közzétételéhez az Azure Service Fabricban Apache Tomcat kiszolgálón futó alkalmazásokhoz. Az alkalmazással és az Apache Tomcat kiszolgálót egy Docker-rendszerkép létrehozásához, a rendszerkép leküldése egy tároló-beállításjegyzéket, hozhat létre, és egy Service Fabric-tárolóalkalmazás üzembe helyezése.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: v-jamebr
ms.openlocfilehash: 3e93e822c5764a23bba124152ef5dfabf2d3f94f
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223869"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Az Apache Tomcat kiszolgálót a linuxon futó Service Fabric-tároló létrehozása
Az Apache Tomcat a Java Servlet és a Java-kiszolgáló technológiák egy népszerű, nyílt forráskódú megvalósítását. Ez a cikk bemutatja, hogyan hozhat létre egy tárolót az Apache Tomcat és a egy egyszerű webalkalmazást, a tároló üzembe Linux operációs rendszert futtató Service Fabric-fürt és a webes alkalmazásba való csatlakozáshoz.  

Az Apache Tomcat kapcsolatos további információkért tekintse meg a [Apache Tomcat kezdőlap](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Előfeltételek
* Egy fejlesztői számítógép, amelyen a következők futnak:
  * [Service Fabric SDK és -eszközök](service-fabric-get-started-linux.md).
  * [Linuxhoz készült Docker CE](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric parancssori felület](service-fabric-cli.md)

* Az Azure Container Registry tárolóregisztrációs. Az Azure-előfizetés használatával létrehozhat egy tároló-beállításjegyzéket [az Azure Portalon](../container-registry/container-registry-get-started-portal.md) vagy [az Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>A Tomcat-rendszerkép összeállítása és Futtatás helyben
Kövesse az ebben a szakaszban egy Docker-rendszerkép alapján az Apache Tomcat kép és a egy egyszerű webalkalmazást hozhat létre, és futtassa a tárolóban a helyi számítógépen. 
 
1. Klónozás a [Service Fabric első lépései Java](https://github.com/Azure-Samples/service-fabric-java-getting-started) mintaadattárban a fejlesztési számítógépen.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Módosítsa a könyvtárakat az Apache Tomcat kiszolgálót minta könyvtár (*service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Hozzon létre egy Docker-fájlt a hivatalos alapján [Tomcat kép](https://hub.docker.com/_/tomcat/) Docker Hub és a Tomcat kiszolgálón minta található. Az a *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* könyvtárban hozzon létre egy fájlt *Dockerfile* (fájlkiterjesztés nélkül). Adja hozzá a következőket a *Docker-fájlhoz*, és mentse a módosításokat:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Tekintse meg a [Dockerfile referencia](https://docs.docker.com/engine/reference/builder/) további információt.


4. Futtassa a `docker build` parancsot a webalkalmazást futtató rendszerkép létrehozásához:

   ```bash
   docker build . -t tomcattest
   ```

   Ez a parancs létrehozza az új rendszerképet a docker-fájlban szereplő utasítások segítségével elnevezési (-t címkézés) a rendszerkép `tomcattest`. Egy tároló-rendszerkép létrehozásához az alaprendszerképet az először letöltött le a Docker Hubból, és az alkalmazás adnak hozzá. 

   Miután az összeállító parancs lefutott, futtassa a `docker images` parancsot az új rendszerkép információinak megtekintéséhez:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Győződjön meg arról, hogy fut-e a tárolóba helyezett alkalmazást helyileg, mielőtt leküldené azt a tároló-beállításjegyzékbe:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` a tároló-neveket, olvassa el ezt az azonosító helyett egy rövid nevet
   * `-p` Megadja a portleképezést a tároló és a gazda operációs rendszer között. 

   > [!Note]
   > A port, nyissa meg a `-p` paraméternek kell lennie a port, a kérelmek figyeli a Tomcat alkalmazás. Ebben a példában, van egy konfigurált összekötőt a *ApacheTomcat/conf/server.xml* fájlt a 8080-as porton figyeli a HTTP-kéréseket figyelik. Ezt a portot a gazdagépen a 8080-as porton van leképezve. 

   Más paraméterekkel kapcsolatos további információkért tekintse meg a [dokumentáció: Docker run](https://docs.docker.com/engine/reference/commandline/run/).

1. A tároló tesztelése, nyisson meg egy böngészőt, és adja meg a következő URL-címek valamelyikét. Megjelenik egy "Hello World!" változata üdvözlőképernyőn minden egyes URL-CÍMÉT.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hello world /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Állítsa le a tároló, és törölje azt a fejlesztői számítógépről:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>A Tomcat rendszerkép leküldése a tárolójegyzékbe
Most, hogy már ellenőrizte, hogy a Tomcat kép tárolóban fut, a fejlesztői számítógépen való, leküldése egy tároló-beállításjegyzék-tárház. Ez a cikk az Azure Container Registryt használjuk a lemezkép tárolására, de néhány módosítással lépéseket használhatja bármely tárolóregisztrációs adatbázis választott. Ebben a cikkben a beállításjegyzék neve azt feltételezi, hogy *myregistry* myregistry.azurecr.io pedig a teljes adatbázis nevét. Ezek a forgatókönyvnek megfelelően a módosítása 

1. Futtassa a(z) `docker login` parancsot a tároló-beállításjegyzékbe való bejelentkezéshez a [beállításjegyzékhez tartozó hitelesítő adataival](../container-registry/container-registry-authentication.md).

   Az alábbi példában a rendszer egy Azure Active Directory [egyszerű szolgáltatás](../active-directory/develop/app-objects-and-service-principals.md) azonosítóját és jelszavát adja át. Például lehet, hogy hozzárendelt egy egyszerű szolgáltatást a beállításjegyzékhez egy automatizálási forgatókönyvhöz. Vagy bejelentkezhet a beállításjegyzékhez tartozó felhasználónevével és jelszavával.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. A következő parancs létrehoz egy címkét vagy aliast a rendszerképről a beállításjegyzékre mutató teljes elérési úttal. Az alábbi példa a rendszerképet a `samples` névtérben helyezi el, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Küldje le a rendszerképet tároló-beállításjegyzékbe:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Hozhat létre, és a Service Fabric-tárolóalkalmazás üzembe helyezése
Most, hogy a Tomcat rendszerkép már leküldött továbbíthat egy tárolóregisztrációs adatbázisba, hozhat létre, és üzembe helyezése egy Service Fabric-tárolóalkalmazás, amely lekéri a Tomcat rendszerképet a regisztrációs adatbázisból, és futtatja a tárolóalapú szolgáltatás a fürtben. 

1. Hozzon létre egy új könyvtárat a helyi klón kívül (alkalmazáson kívül a *service-fabric-java-getting-started* könyvtárfában). Váltson át, és a egy scaffold tároló alkalmazás létrehozása a Yeoman használatával: 

   ```bash
   yo azuresfcontainer 
   ```
   Adja meg, amikor a rendszer kéri a következő értékeket:

   * Adjon nevet az alkalmazásnak: ServiceFabricTomcat
   * Az alkalmazás-szolgáltatás neve: TomcatService
   * Adjon meg a lemezkép neve: Adja meg az URL-címet a tárolórendszerképet a tárolóregisztrációs adatbázis található Ha például myregistry.azurecr.io/samples/tomcattest.
   * Parancsok: Hagyja üresen. Mivel ez a rendszerkép meghatározott számításifeladat-belépési ponttal rendelkezik, így nem kell explicit módon megadni a bemeneti parancsokat (tárolón belül futó parancsok, amelyek az indítás után biztosítják a tároló futtatását).
   * Vendég-tárolóalkalmazás példányainak számát: 1

   ![Tárolókhoz készült Service Fabric Yeoman-generátor](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. A szolgáltatásjegyzékben (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*), adja hozzá a következő XML formátumú, területen a legfelső szintű **ServiceManfest** címke, a port megnyitásához a alkalmazás figyel a kérésekre. A **végpont** címke deklarálja a protokoll és port a végponthoz. Ebben a cikkben a tárolóalapú szolgáltatás a 8080-as portot figyeli: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. Az alkalmazásjegyzékben (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) alatt a **ServiceManifestImport** címke, adja hozzá a következő XML-kódot. Cserélje le a **AccountName** és **jelszó** a a **RepositoryCredentials** címke nevét a tárolóregisztrációs adatbázisba, és jelentkezzen be, szükséges jelszó.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   A **ContainerHostPolicies** kód házirendek tárológazdagép aktiválásához adja meg.
    
   * A **PortBinding** címkét konfigurál a tárolóport – gazdagépport leképezést házirendet. A **ContainerPort** attribútum 8080-as értékre van állítva, mert a tároló elérhetővé teszi a 8080-as porton, a docker-fájlban megadott. A **EndpointRef** attribútum értéke "endpointTest", az előző lépésben a szolgáltatásjegyzékben definiált végpont. Így a szolgáltatás a 8080-as porton beérkező kérések a tárolón a 8080-as porton képeződik. 
   * A **RepositoryCredentials** címke hitelesítő adatokat adja meg, hogy a tároló a (személyes) tárház, ahol kér le a lemezképet hitelesíteni kell. Ez a házirend nincs szükség, ha a képet fogja le kell kérnie a nyilvános tárházhoz.
    

12. Az a *ServiceFabricTomcat* mappában, a service fabric-fürthöz való csatlakozáshoz. 

   * Szeretne csatlakozni a helyi Service Fabric-fürthöz, futtassa:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Szeretne csatlakozni egy biztonságos Azure-fürtben, ellenőrizze, hogy az ügyféltanúsítvány nem található a PEM-fájlként a *ServiceFabricTomcat* könyvtárat, és futtassa: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     Az előző parancsban cserélje le a `your-certificate.pem` az ügyféltanúsítvány-fájl nevét. A fejlesztési-tesztelési környezetet a fürttanúsítvány gyakran használják az ügyfél-tanúsítványt. Ha a tanúsítvány nem önaláírt, hagyja el a `-no-verify` paraméter. 
       
     Fürttanúsítványok általában letöltődnek helyileg .pfx fájlként. Ha még nem rendelkezik a tanúsítvány PEM formátumú, hozzon létre egy .pem fájlt egy .pfx-fájlból a következő parancsot futtathatja:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Ha a .pfx-fájl nem jelszóval védett, akkor `-passin pass:` utolsó paraméterhez.


13. Futtassa az alkalmazást a fürtön üzembe helyezéséhez a sablonban megadott telepítési szkriptet. A parancsfájl az alkalmazáscsomag a fürt lemezképtárolójába másolja, regisztrálja az alkalmazás típusát, és létrehozza az alkalmazás egy példányát.

     ```bash
     ./install.sh
     ```

   A telepítési szkript futtatása után nyisson meg egy böngészőt, és keresse meg a Service Fabric Explorer:
    
   * Egy helyi fürtön használata `http://localhost:19080/Explorer` (cserélje le *localhost* magánhálózati IP-címét a virtuális gép használata a Vagrant Mac OS x).
   * Egy biztonságos Azure-fürtön, használjon `https://PublicIPorFQDN:19080/Explorer`. 
    
   Bontsa ki a **alkalmazások** csomópontot, és vegye figyelembe, hogy most már rendelkezésre áll egy bejegyzés az alkalmazás típusához **ServiceFabricTomcatType**, és a egy másik a típus első példányához. Néhány percet vehet igénybe az alkalmazás teljes körű központi telepítését, ezért legyen kis türelmet.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Az alkalmazáshoz való hozzáférés a Tomcat kiszolgálón, nyisson meg egy böngészőablakot, és adja meg a következő URL-címek valamelyikét. Ha a helyi fürt központi telepítése, használata *localhost* a *PublicIPorFQDN*. Megjelenik egy "Hello World!" változata üdvözlőképernyőn minden egyes URL-CÍMÉT.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Használja a sablonban megadott eltávolítási szkriptet az alkalmazáspéldány törléséhez a fürtből, és törölje az alkalmazástípus regisztrációját.

```bash
./uninstall.sh
```

Miután leküldte a rendszerképet a tároló-beállításjegyzékbe, törölheti a helyi rendszerképet a fejlesztői számítógépről:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>További lépések
* Gyors lépések a Linux-tároló további funkciók, olvassa el a [az első Service Fabric-tárolóalkalmazás létrehozása linuxon](service-fabric-get-started-containers-linux.md).
* Részletes lépések a Linux-tárolók, olvassa el a [hozzon létre egy Linux-tároló alkalmazás oktatóanyag](service-fabric-tutorial-create-container-images.md) oktatóanyag.
* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-containers-overview.md).


