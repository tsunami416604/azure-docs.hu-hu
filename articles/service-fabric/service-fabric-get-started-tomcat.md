---
title: Hozzon létre egy tárolót apache Tomcat Linuxon
description: Linux-tároló létrehozása az Apache Tomcat-kiszolgálón futó alkalmazás azure Service Fabric. Hozzon létre egy Docker-rendszerképet az alkalmazással és az Apache Tomcat-kiszolgálóval, küldje le a lemezképet egy tároló beállításjegyzékébe, hozzon létre és telepítsen egy Service Fabric-tárolóalkalmazást.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 1a699f3b35970270a9800162a6d8717682a168ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614417"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Apache Tomcat-kiszolgálót futtató Service Fabric-tároló létrehozása Linuxon
Az Apache Tomcat a Java Servlet és a Java Server technológiák népszerű, nyílt forráskódú implementációja. Ez a cikk bemutatja, hogyan hozhat létre egy tárolót az Apache Tomcat és egy egyszerű webalkalmazással, hogyan telepítheti a tárolót egy Linux ot futtató Service Fabric-fürtre, és hogyan csatlakozhat a webalkalmazáshoz.  

Ha többet szeretne megtudni az Apache Tomcat-ról, tekintse meg az [Apache Tomcat honlapját.](https://tomcat.apache.org/) 

## <a name="prerequisites"></a>Előfeltételek
* Egy fejlesztői számítógép, amelyen a következők futnak:
  * [Service Fabric SDK és eszközök](service-fabric-get-started-linux.md).
  * [Linuxhoz készült Docker CE](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric parancssori felület](service-fabric-cli.md)

* Egy tároló beállításjegyzék az Azure Container Registry. Az Azure Portalon vagy [az Azure CLI-n](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry)keresztül létrehozhat egy tároló-beállításjegyzéket az Azure-előfizetésben. [the Azure portal](../container-registry/container-registry-get-started-portal.md) 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Tomcat-lemezkép létrehozása és helyi futtatása
Ebben a szakaszban leírt lépéseket követve hozzon létre egy Apache Tomcat-lemezkép és egy egyszerű webalkalmazás alapján docker-lemezképet, majd futtassa azt egy tárolóban a helyi rendszeren. 
 
1. Klónozza a [Service Fabric első lépések a Java](https://github.com/Azure-Samples/service-fabric-java-getting-started) mintatárház a fejlesztési számítógépen.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Könyvtárak módosítása az Apache Tomcat kiszolgáló mintakönyvtárára *(service-fabric-java-getting-started/container-apache-tomcat-web-server-sample):*

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Hozzon létre egy Docker-fájlt a Docker Hubon és a Tomcat-kiszolgálómintán található hivatalos [Tomcat-lemezkép](https://hub.docker.com/_/tomcat/) alapján. A *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* könyvtárban hozzon létre egy *Dockerfile* nevű fájlt (fájlkiterjesztés nélkül). Adja hozzá a következőket a *Docker-fájlhoz*, és mentse a módosításokat:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   További információkért tekintse meg a [Docker-fájl hivatkozását.](https://docs.docker.com/engine/reference/builder/)


4. Futtassa a `docker build` parancsot a webalkalmazást futtató lemezkép létrehozásához:

   ```bash
   docker build . -t tomcattest
   ```

   Ez a parancs az új lemezképet a Docker-fájl utasításai nak megfelelően építi fel, és a rendszerképet `tomcattest`(-tagging) nevezi el . Egy tárolórendszerkép létrehozásához az alaprendszerkép először letöltődik a Docker Hubról, és az alkalmazás hozzáadódik hozzá. 

   Miután az összeállító parancs lefutott, futtassa a `docker images` parancsot az új rendszerkép információinak megtekintéséhez:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Ellenőrizze, hogy a tárolós alkalmazás helyileg fut-e, mielőtt lenyomja a tároló beállításjegyzékét:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name`megnevezi a tárolót, így nem az azonosítója, hanem egy rövid név használatával hivatkozhat rá.
   * `-p`megadja a tároló és a gazdaoperációs rendszer közötti portleképezést. 

   > [!Note]
   > A `-p` paraméterrel megnyitott portnak kell lennie annak a portnak, amelyen a Tomcat alkalmazás meghallgatja a kéréseket. Az aktuális példában van egy összekötő konfigurálva az *ApacheTomcat/conf/server.xml* fájlban, amely a 8080-as porton http-kérelmeket figyel. Ez a port az állomás 8080-as portjára van leképezve. 

   További információ a [Docker futtatási dokumentációjában.](https://docs.docker.com/engine/reference/commandline/run/)

1. A tároló teszteléséhez nyisson meg egy böngészőt, és adja meg az alábbi URL-címek egyikét. Látni fogja egy változata a "Hello World!" üdvözlőképernyőt az egyes URL-címekhez.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hello világ / sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Állítsa le a tárolót, és törölje a fejlesztői számítógépről:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>A Tomcat-lemezkép leküldése a tároló beállításjegyzékébe
Most, hogy meggyőződött arról, hogy a Tomcat-lemezkép egy tárolóban fut a fejlesztői számítógépen, leküldéses egy tároló beállításjegyzékben lévő tárházba. Ez a cikk az Azure Container Registry segítségével tárolja a lemezképet, de néhány lépés módosításával használhatja bármelyik tároló beállításjegyzék et választhat. Ebben a cikkben a rendszerleíró adatbázis neve a *rendszerleíró adatbázis* neve, a teljes rendszerleíró név pedig myregistry.azurecr.io. Módosítsa ezeket a forgatókönyvnek megfelelően. 

1. Futtassa `docker login` a rendszerleíró adatbázisba való bejelentkezéshez a [rendszerleíró adatbázis hitelesítő adataival.](../container-registry/container-registry-authentication.md)

   Az alábbi példában a rendszer egy Azure Active Directory [egyszerű szolgáltatás](../active-directory/develop/app-objects-and-service-principals.md) azonosítóját és jelszavát adja át. Például lehet, hogy hozzárendelt egy egyszerű szolgáltatást a beállításjegyzékhez egy automatizálási forgatókönyvhöz. Vagy a rendszerleíró adatbázis felhasználónevével és jelszavával is bejelentkezhet.

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

## <a name="build-and-deploy-the-service-fabric-container-application"></a>A Service Fabric-tárolóalkalmazás létrehozása és üzembe helyezése
Most, hogy leküldte a Tomcat-lemezképet egy tároló beállításjegyzékébe, létrehozhat és üzembe helyezhet egy Service Fabric-tárolóalkalmazást, amely lekéri a Tomcat-lemezképet a rendszerleíró adatbázisból, és a fürtben tárolószolgáltatásként futtatja. 

1. Hozzon létre egy új könyvtárat a helyi klónon kívül (a *szolgáltatás-fabric-java-getting-started* könyvtárfán kívül). Váltson rá, és használja yeoman segítségével hozzon létre egy állványt egy konténer alkalmazás: 

   ```bash
   yo azuresfcontainer 
   ```
   Amikor a rendszer kéri, adja meg a következő értékeket:

   * Az alkalmazás neve: ServiceFabricTomcat
   * Az alkalmazásszolgáltatás neve: TomcatService
   * Adja meg a lemezkép nevét: Adja meg a tárolórendszerkép URL-címét a tároló beállításjegyzékében; például myregistry.azurecr.io/samples/tomcattest.
   * Parancsok: Hagyja üresen ezt a lehetőséget. Mivel ez a rendszerkép meghatározott számításifeladat-belépési ponttal rendelkezik, így nem kell explicit módon megadni a bemeneti parancsokat (tárolón belül futó parancsok, amelyek az indítás után biztosítják a tároló futtatását).
   * Vendégtároló-alkalmazás példányainak száma: 1

   ![Tárolókhoz készült Service Fabric Yeoman-generátor](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. A szolgáltatásjegyzékben (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*) adja hozzá a következő XML-t a root **ServiceManfest** címkében, hogy megnyissa azt a portot, amelyen az alkalmazás kéréseket figyel. Az **Endpoint** címke deklarálja a végpont protokollját és portját. Ebben a cikkben a tárolós szolgáltatás a 8080-as porton hallgatja: 

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

11. Az alkalmazásjegyzékben (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*), a **ServiceManifestImport** címkénél adja hozzá a következő XML-t. Cserélje le a RepositoryCredentials címkét a **Tárházhitelesítőcímkét** tartalmazó **fióknév** és **jelszó** helyére a tároló beállításjegyzékének nevére és a bejelentkezéshez szükséges jelszóra.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   A **ContainerHostPolicies** címke a tárolóállomások aktiválására vonatkozó házirendeket határozza meg.
    
   * A **PortBinding** címke konfigurálja a tárolóport-állomás port hozzárendelési házirendet. A **ContainerPort** attribútum 8080-ra van állítva, mert a tároló a Docker-fájlban megadott 8080-as portot teszi elérhetővé. Az **EndpointRef** attribútum "endpointTest" értékre van állítva, amely az előző lépésben a szolgáltatásjegyzékben definiált végpont. Így a 8080-as porton lévő szolgáltatáshoz érkező kérelmek a tárolón lévő 8080-as portra vannak leképezve. 
   * A **RepositoryCredentials** címke megadja azokat a hitelesítő adatokat, amelyeket a tárolónak hitelesítenie kell a (magán) tárházzal, ahonnan lekéri a lemezképet. Nincs szüksége erre a házirendre, ha a rendszerkép egy nyilvános tárházból lesz lekéri.
    

12. A *ServiceFabricTomcat* mappában csatlakozzon a szolgáltatásháló-fürthöz. 

   * A helyi Service Fabric-fürthöz való csatlakozáshoz futtassa a következőt:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Biztonságos Azure-fürthöz való csatlakozáshoz győződjön meg arról, hogy az ügyféltanúsítvány .pem fájlként van jelen a *ServiceFabricTomcat* könyvtárban, és futtassa a következőket: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     Az előző parancsban `your-certificate.pem` cserélje le az ügyféltanúsítvány-fájl nevét. Fejlesztési és tesztelési környezetekben a fürttanúsítványt gyakran használják ügyféltanúsítványként. Ha a tanúsítvány nincs önaláírt, `-no-verify` hagyja ki a paramétert. 
       
     A fürttanúsítványokat általában helyileg .pfx fájlként töltik le. Ha még nem rendelkezik a tanúsítvánnyal PEM formátumban, a következő paranccsal .pfx fájlból hozhat létre .pem fájlt:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Ha a .pfx fájl nem jelszóval védett, használja `-passin pass:` az utolsó paramétert.


13. Futtassa a sablonban megadott telepítési parancsfájlt az alkalmazás fürtre való központi telepítéséhez. A parancsfájl átmásolja az alkalmazáscsomagot a fürt lemezképtárolójára, regisztrálja az alkalmazás típusát, és létrehozza az alkalmazás egy példányát.

     ```bash
     ./install.sh
     ```

   A telepítési parancsfájl futtatása után nyisson meg egy böngészőt, és keresse meg a Service Fabric Intézőt:
    
   * Egy helyi fürtön `http://localhost:19080/Explorer` használja a *helyi állomást* a virtuális gép privát IP-címével, ha AMac OS X rendszeren használja a Vagrant-et).
   * Egy biztonságos Azure-fürtön használja a használatát. `https://PublicIPorFQDN:19080/Explorer` 
    
   Bontsa ki az **Alkalmazások** csomópontot, és vegye figyelembe, hogy most már van egy bejegyzés az alkalmazás típusához, **ServiceFabricTomcatType**, és egy másik az adott típusú első példányhoz. Eltarthat néhány percig, amíg az alkalmazás teljes mértékben üzembe helyezhető, ezért legyen türelmes.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Az alkalmazás Tomcat kiszolgálón való eléréséhez nyisson meg egy böngészőablakot, és adja meg az alábbi URL-címek bármelyikét. Ha a helyi fürtre telepítette, használja a *localhost-ot* a *PublicIPorFQDN számára.* Látni fogja egy változata a "Hello World!" üdvözlőképernyőt az egyes URL-címekhez.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
A sablonban található eltávolítási parancsfájl segítségével törölheti az alkalmazáspéldányt a fürtből, és törölheti az alkalmazástípus regisztrációját.

```bash
./uninstall.sh
```

Miután leküldte a rendszerképet a tároló-beállításjegyzékbe, törölheti a helyi rendszerképet a fejlesztői számítógépről:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>További lépések
* A további Linux-tárolófunkciók gyors lépéseiért olvassa [el az Első Service Fabric-tárolóalkalmazás létrehozása Linuxon](service-fabric-get-started-containers-linux.md)című olvasnivalót.
* A Linux-tárolók részletesebb lépéseiért olvassa el a [Linux-tárolóalkalmazás létrehozása oktatóanyag](service-fabric-tutorial-create-container-images.md) című oktatóanyagát.
* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-containers-overview.md).


