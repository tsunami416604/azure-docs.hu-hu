---
title: A Linux-alkalmazások folyamatos létrehozása a Jenkins használatával
description: A Service Fabric Linux-alkalmazás folyamatos felépítése és integrálása a Jenkins használatával
author: sayantancs
ms.topic: conceptual
ms.date: 07/31/2018
ms.openlocfilehash: 175338fef600f6e726fd02eee6b0f416181bd9dd
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610216"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>A Linux-alkalmazások létrehozása és üzembe helyezése a Jenkins használatával
A Jenkins egy népszerű eszköz az alkalmazások folyamatos integrációjához és üzembe helyezéséhez. Ebből a témakörből megtudhatja, hogyan helyezheti üzembe Azure Service Fabric-alkalmazásait a Jenkins használatával.

## <a name="topic-overview"></a>Témakör – áttekintés
Ez a cikk a Jenkins-környezet beállításának számos lehetséges módját ismerteti, valamint az alkalmazások Service Fabric-fürtön való üzembe helyezésének különböző módjait. A Jenkins sikeres beállításához, a GitHubról történő lekéréshez, az alkalmazás létrehozásához és a fürtön való üzembe helyezéséhez kövesse az alábbi általános lépéseket:

1. Győződjön meg arról, hogy telepíti az [előfeltételeket](#prerequisites).
1. Ezután kövesse az alábbi szakaszokban ismertetett lépéseket a Jenkins beállításához:
   * [A Jenkins beállítása egy Service Fabric-fürtön belül](#set-up-jenkins-inside-a-service-fabric-cluster) 
   * [Jenkins beállítása Service Fabric fürtön kívül](#set-up-jenkins-outside-a-service-fabric-cluster), vagy
   * [Telepítse a Service Fabric beépülő modult egy meglévő Jenkins-környezetben](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. A Jenkins beállítása után kövesse a [Jenkins-feladat létrehozása és konfigurálása](#create-and-configure-a-jenkins-job) című témakör lépéseit a GitHub kiváltásához a Jenkins aktiválásához, amikor az alkalmazáson végzett változtatások történnek, és a Jenkins-feladatok folyamatát a Build lépés segítségével szeretné lekérni, hogy lekérje a módosításokat a githubról, és létrehozza az alkalmazást. 
1. Végül konfigurálja a Jenkins-feladatot a létrehozás utáni lépéssel, és telepítse az alkalmazást a Service Fabric-fürtön. A Jenkins-t kétféleképpen állíthatja be az alkalmazás fürtön való üzembe helyezéséhez:    
   * Fejlesztési és tesztelési környezetekhez használja a [telepítés konfigurálása a fürt felügyeleti végpontja segítségével](#configure-deployment-using-cluster-management-endpoint). Ez a legegyszerűbb telepítési módszer a beállításhoz.
   * Éles környezetekben használja az [Azure-beli hitelesítő adatok használatával történő üzembe helyezés konfigurálását](#configure-deployment-using-azure-credentials). A Microsoft ezt a módszert javasolja éles környezetekhez, mivel az Azure-beli hitelesítő adatokkal korlátozható a Jenkins-feladatok Azure-erőforrásokhoz való hozzáférése. 

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a git helyileg van telepítve. Az operációs rendszer alapján a git- [letöltések lapról](https://git-scm.com/downloads) telepítheti a megfelelő git-verziót. Ha most ismerkedik a git szolgáltatással, további információt a [git dokumentációjában](https://git-scm.com/docs)talál.
- Ez a cikk a GitHubon a *Service Fabric első lépések mintát* használja: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) az alkalmazás létrehozásához és üzembe helyezéséhez. A tárházat leképezve követheti a lépéseket, vagy az utasítások módosításával a saját GitHub-projektjét is használhatja.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Service Fabric beépülő modul telepítése meglévő Jenkins-környezetben
Ha a Service Fabric beépülő modult egy meglévő Jenkins-környezethez adja hozzá, a következőkre lesz szüksége:

- A [Service FABRIC CLI](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Ügyeljen arra, hogy a parancssori felületet a felhasználói szint helyett a rendszerszinten telepítse, így a Jenkins futtatható CLI-parancsokat is. 
   >

- Java-alkalmazások telepítéséhez telepítse mindkét [Gradle, és nyissa meg a JDK 8,0-](service-fabric-get-started-linux.md#set-up-java-development)et. 
- A .NET Core 2,0-alkalmazások telepítéséhez telepítse a [.net core 2,0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development)-t. 

Miután telepítette a környezetéhez szükséges előfeltételeket, megkeresheti az Azure Service Fabric beépülő modult a Jenkins piactéren, és telepítheti.

A beépülő modul telepítése után ugorjon előre a [Jenkins-feladatok létrehozásához és konfigurálásához](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>A Jenkins beállítása egy Service Fabric-fürtben

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő részekben bemutatjuk, hogyan állíthatja be egy fürtön belül egy Azure Storage-fiók használatával, hogy mentse a tároló példányának állapotát.

### <a name="prerequisites"></a>Előfeltételek
- Rendelkeznie kell egy Service Fabric Linux-fürttel, amelyen a Docker telepítve van. Az Azure-ban futó Service Fabric fürtökön már telepítve van a Docker. Ha helyileg futtatja a fürtöt (beépített fejlesztői környezet), ellenőrizze, hogy a Docker telepítve van-e a gépen a `docker info` paranccsal. Ha nincs telepítve, telepítse a következő parancsokkal:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Győződjön meg arról, hogy az 8081-es port egyéni végpontként van megadva a fürtön. Ha helyi fürtöt használ, győződjön meg arról, hogy az 8081-es port nyitva van a gazdagépen, és hogy nyilvános IP-címmel rendelkezik.
   >

### <a name="steps"></a>Lépések
1. Az alkalmazás klónozásához használja az alábbi parancsokat:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Megőrzi a Jenkins-tároló állapotát egy fájlmegosztás esetében:
   1. Hozzon létre egy Azure Storage-fiókot **ugyanabban a régióban** , mint a fürt neve, például `sfjenkinsstorage1`.
   1. Hozzon létre egy **fájlmegosztást** a Storage-fiókban egy olyan névvel, mint például a `sfjenkins`.
   1. Kattintson a **Csatlakozás** a fájlhoz-megosztás elemre, és jegyezze fel a **Linux-csatlakozás**alatt megjelenített értékeket, az értéknek az alábbihoz hasonlóan kell kinéznie:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > A CIFS-megosztások csatlakoztatásához a fürtcsomópontokon telepíteni kell a CIFS-utils csomagot.      
   >

1. Frissítse a `setupentrypoint.sh` parancsfájlban található helyőrző értékeket az Azure-Storage részleteivel a 2. lépésben.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Cserélje le a `[REMOTE_FILE_SHARE_LOCATION]` értéket a fenti 2. lépésben a kapcsolat kimenetében `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` értékre.
   * Cserélje le a `[FILE_SHARE_CONNECT_OPTIONS_STRING]` értéket a fenti 2. lépésben `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` értékre.

1. **Csak biztonságos fürt:** 
   
   Az alkalmazások a Jenkins-ből származó biztonságos fürtön való üzembe helyezésének konfigurálásához a fürt tanúsítványának elérhetőnek kell lennie a Jenkins-tárolóban. A *ApplicationManifest. XML* fájlban a **ContainerHostPolicies** címke alatt adja hozzá ezt a tanúsítvány-referenciát, és frissítse az ujjlenyomat értékét a fürt tanúsítványával.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Emellett adja hozzá a következő sorokat a **ApplicationManifest** (root) címkéhez a *ApplicationManifest. XML* fájlban, és frissítse az ujjlenyomat értékét a fürt tanúsítványával.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Kapcsolódjon a fürthöz, és telepítse a Container alkalmazást.

   **Biztonságos fürt**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Az előző parancs PEM formátumban veszi át a tanúsítványt. Ha a tanúsítvány PFX formátumú, a következő parancs használatával alakíthatja át. Ha a PFX-fájl jelszava nem védett, adja meg a **passin** paramétert `-passin pass:`ként.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Nem biztonságos fürt**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Ezzel telepít a fürtön egy Jenkins-tárolót, amely a Service Fabric Explorerrel figyelhető meg.

   > [!NOTE]
   > Eltarthat néhány percig, amíg a Jenkins-rendszerkép le lesz töltve a fürtön.
   >

1. Nyissa meg a `http://PublicIPorFQDN:8081` URL-címet a böngészőben. Így megkapja a bejelentkezéshez szükséges kezdeti rendszergazdai jelszó elérési útját. 
1. Tekintse meg a Service Fabric Explorer annak meghatározásához, hogy a Jenkins-tároló melyik csomóponton fut. A Secure Shell (SSH) be van jelentkezni ebbe a csomópontba.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Kérje le a tároló példányazonosítóját a `docker ps -a` paranccsal.
1. A Secure Shell (SSH) használatával jelentkezzen be a tárolóba, és illessze be a Jenkins-portálon megjelenő elérési utat. Ha például a portálon a `PATH_TO_INITIAL_ADMIN_PASSWORD`elérési út látható, futtassa a következő parancsokat:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. A Jenkins Első lépések lapon válassza a telepítendő bővítmények lehetőséget, jelölje be a **nincs** jelölőnégyzetet, majd kattintson a telepítés gombra.
1. Hozzon létre egy felhasználót, vagy válassza a folytatás rendszergazdaként lehetőséget.

A Jenkins beállítása után ugorjon előre a Jenkins- [feladatok létrehozásához és konfigurálásához](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>A Jenkins beállítása Service Fabric-fürtön kívül

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő szakaszok a fürtön kívüli beállítást mutatják be.

### <a name="prerequisites"></a>Előfeltételek
- Győződjön meg arról, hogy a Docker telepítve van a gépen. A következő parancsokkal telepítheti a Dockert a terminálról:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Ha `docker info` futtat a terminálon, a kimenetnek azt kell megjelennie, hogy a Docker szolgáltatás fut.

### <a name="steps"></a>Lépések
1. Kérje le a Service Fabric Jenkins-tárolójának rendszerképét: `docker pull rapatchi/jenkins:latest`. A rendszerképhez előre telepítve van a Service Fabric Jenkins beépülő modulja.
1. Futtassa a tároló rendszerképét: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Kérje le a tárolórendszerkép-példány azonosítóját. A `docker ps –a` paranccsal az összes Docker-tárolót listázhatja
1. Jelentkezzen be a Jenkins-portálra a következő lépésekkel:

   1. Jelentkezzen be egy Jenkins-rendszerhéjba a gazdagépről. Használja a tároló AZONOSÍTÓjának első négy számjegyét. Ha például a tároló azonosítója `2d24a73b5964`, használja a `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. A Jenkins rendszerhéjból szerezze be a tároló példányának rendszergazdai jelszavát:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. A Jenkins-irányítópultra való bejelentkezéshez nyissa meg a következő URL-címet egy böngészőben: `http://<HOST-IP>:8080`. A Jenkins zárolásának feloldásához használja az előző lépésben használt jelszót.
   1. (Nem kötelező.) Az első bejelentkezés után létrehozhatja saját felhasználói fiókját, és használhatja azt a következő lépésekhez, vagy továbbra is használhatja a rendszergazdai fiókot. Ha létrehoz egy felhasználót, ennek a felhasználónak kell megfelelnie.
1. Állítsa be a GitHubot a Jenkins használatához az [új SSH-kulcs létrehozásához és az SSH-ügynökhöz való hozzáadásához](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)szükséges lépéseket követve.
   * A GitHub utasításait követve hozza létre az SSH-kulcsot, majd adja hozzá ahhoz a GitHub-fiókhoz, amelyen az adattár üzemel.
   * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén).
   * Ahhoz, hogy a saját gazdagépéről jelentkezhessen be a Jenkinsbe, használja a következő parancsot:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Győződjön meg arról, hogy a fürt vagy a gép, ahol a Jenkins-tároló rendszerképe fut, nyilvános IP-címmel rendelkezik. Ez lehetővé teszi, hogy a Jenkins-példány megkapja a GitHub értesítéseit.

A Jenkins beállítása után folytassa a következő szakasszal, [hozzon létre és konfiguráljon egy Jenkins-feladatot](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins-feladatok létrehozása és konfigurálása

Az ebben a szakaszban ismertetett lépések bemutatják, hogyan konfigurálhat egy Jenkins-feladatot a GitHub-tárház változásaira reagálva, beolvashatja a módosításokat, és felépítheti azokat. Ennek a szakasznak a végén arra a végső lépésre lesz átirányítva, hogy az alkalmazás üzembe helyezése a fejlesztési/tesztelési környezetben vagy éles környezetben történő üzembe helyezése alapján történjen. 

1. A Jenkins-irányítópulton kattintson az **új elem**lehetőségre.
1. Adjon nevet az elemnek (pl. **MyJob**). Válassza a **free-style project** (szabad projekt) lehetőséget, majd kattintson az **OK** gombra.
1. Megnyílik a feladatok konfigurációja lap. (A Jenkins-irányítópult konfigurációjának beszerzéséhez kattintson a feladatokra, majd a **Konfigurálás**elemre.

1. Az **általános** lapon jelölje be a **GitHub-projekthez**tartozó jelölőnégyzetet, és adja meg a GitHub-projekt URL-címét. Ez az URL-cím üzemelteti azt a Service Fabric Java-alkalmazást, amelyet integrálni szeretne a Jenkins folyamatos integrációs és üzembe helyezési (CI/CD) folyamatával (például: `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. A **forráskód-kezelés** lapon válassza a **git**lehetőséget. Adja meg annak az adattárnak az URL-címét, amely a Jenkins CI/CD folyamatával integrálni kívánt Service Fabric Java-alkalmazást tartalmazza (például: `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Megadhatja azt is, hogy melyik ágat kívánja létrehozni (például `/master`).
1. A *GitHub* -tárház konfigurálása a Jenkins-vel való kommunikációhoz:

   a. A GitHub-adattár lapon lépjen a **beállítások** > **integrációk és szolgáltatások**elemre.

   b. Válassza az **Add Service** (Szolgáltatás hozzáadása) elemet, írja be a **Jenkins** kifejezést, majd válassza ki a **Jenkins-GitHub beépülő modult**.

   c. Adja meg a Jenkins-webhook URL-címét (alapértelmezés szerint ez a következő: `http://<PublicIPorFQDN>:8081/github-webhook/`). Kattintson az **add/update service** (Szolgáltatás hozzáadása/frissítése) elemre.

   d. A Jenkins-példányra egy tesztesemény érkezik. A GitHubban egy zöld pipa jelenik meg a webhook mellett, és a projekt létrejön.

1. A Jenkins **Build triggerek** lapján válassza ki a kívánt Build-beállítást. Ebben a példában szeretné elindítani a buildet, amikor leküldi az adattárat, ezért válassza a **GitHub Hook-trigger lehetőséget a gitscm Polling lekérdezéséhez**. (Korábban ez **Build when a change is pushed to GitHub** (Felépítés módosítások GitHubon való közzétételekor) volt.)
1. A **build (összeállítás** ) lapon tegye a következők egyikét attól függően, hogy Java-alkalmazást vagy .net Core-alkalmazást épít:

   * **Java-alkalmazások esetén:** A **létrehozási lépés hozzáadása** legördülő menüből válassza a **Gradle-parancsfájl meghívása**lehetőséget. Kattintson a **Speciális** elemre. A Speciális menüben adja meg az alkalmazáshoz tartozó **gyökérszintű felépítési parancsfájl** elérési útját. A rendszer felveszi a build.gradle elemet a megadott elérési útból, és annak megfelelően működik. A [ActorCounter alkalmazás](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)esetében ez a következő: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Service Fabric, Jenkins felépítési művelet][build-step]

   * **.Net Core-alkalmazások esetén:** A **létrehozási lépés hozzáadása** legördülő menüben válassza a **rendszerhéj végrehajtása**elemet. A megjelenő parancsban először a könyvtárat kell módosítani ahhoz az elérési útra, ahol a build.sh fájl található. A címtár megváltozása után a build.sh parancsfájl futtatható, és létrehozza az alkalmazást.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Az alábbi képernyőképen egy példa látható a [Counter Service](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) -minta létrehozásához használt parancsokra a CounterServiceApplication-beli Jenkins-feladatokkal.

      ![Service Fabric, Jenkins felépítési művelet][build-step-dotnet]

1. Ha úgy konfigurálja a Jenkins-t, hogy az alkalmazást egy Service Fabric-fürtre telepítse a létrehozás utáni műveletekben, szüksége lesz a fürt tanúsítványának helyére a Jenkins-tárolóban. Válasszon egyet az alábbiak közül attól függően, hogy a Jenkins-tároló a fürtön belül vagy kívül fut-e, és jegyezze fel a fürt tanúsítványának helyét:

   * **A fürtön belül futó Jenkins esetén:** A tanúsítvány elérési útja a *Certificates_JenkinsOnSF_Code_MyCert_PEM* környezeti változó értékének a tárolóból való megírásával érhető el.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **A fürtön kívül futó Jenkins esetén:** Kövesse az alábbi lépéseket a fürt tanúsítványának a tárolóba való másolásához:
     1. A tanúsítványnak PEM formátumúnak kell lennie. Ha nem rendelkezik PEM-fájllal, létrehozhat egyet a tanúsítvány PFX-fájljából. Ha a PFX-fájl jelszava nem védett, futtassa a következő parancsot a gazdagépről:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Ha a PFX-fájl jelszavas védelemmel van ellátva, adja meg a jelszót a `-passin` paraméterben. Példa:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. A Jenkins-tárolóhoz tartozó tároló-azonosító lekéréséhez futtassa a `docker ps`t a gazdagépről.
     1. Másolja a PEM-fájlt a tárolóba a következő Docker-paranccsal:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Már majdnem elkészült! Tartsa nyitva a Jenkins-feladatot. Az egyetlen fennmaradó feladat az alkalmazás üzembe helyezése a Service Fabric-fürtön a létrehozás utáni lépések konfigurálásával:

* A fejlesztési vagy tesztelési környezetben történő üzembe helyezéshez kövesse a [központi telepítés konfigurálása a fürt felügyeleti végpontja segítségével](#configure-deployment-using-cluster-management-endpoint)című témakör lépéseit.
* Éles környezetben történő üzembe helyezéshez kövesse az [üzembe helyezés konfigurálása Azure-beli hitelesítő adatok használatával](#configure-deployment-using-azure-credentials)című témakör lépéseit.

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Központi telepítés konfigurálása a fürt felügyeleti végpontjának használatával
Fejlesztési és tesztelési környezetekhez használhatja a fürt felügyeleti végpontját az alkalmazás üzembe helyezéséhez. Az üzembe helyezés utáni művelet konfigurálásával az alkalmazás telepítéséhez a fürt felügyeleti végpontja szükséges a lehető legkevesebb beállításhoz. Ha éles környezetben végzi az üzembe helyezést, ugorjon az [üzembe helyezés az Azure-beli hitelesítő adatokkal való konfigurálásához](#configure-deployment-using-azure-credentials) az üzembe helyezés során használandó Azure Active Directory egyszerű szolgáltatásnév konfigurálásához.    

1. A Jenkins-feladatokban kattintson a **létrehozás utáni műveletek** fülre. 
1. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüből válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. 
1. A **fürtkonfiguráció Service Fabric**alatt jelölje be a **Service Fabric felügyeleti végpont választógombjának kitöltése** választógombot.
1. A **felügyeleti gazdagép**esetében adja meg a fürthöz tartozó kapcsolatok végpontját; például `{your-cluster}.eastus.cloudapp.azure.com`.
1. Az **ügyfél kulcsa** és az **ügyfél tanúsítványa**mezőben adja meg a PEM-tárolóban található PEM-fájl helyét; például `/var/jenkins_home/clustercert.pem`. (Lemásolta a tanúsítvány helyét a [Jenkins-feladatok létrehozásának és konfigurálásának](#create-and-configure-a-jenkins-job)utolsó lépéseként.)
1. Az **alkalmazás konfigurációja**területen adja meg az alkalmazás **neve**, az **alkalmazás típusa**és az alkalmazás jegyzékfájl mezőinek (relatív) **elérési útját** .

   ![Service Fabric Jenkins-Build utáni művelet – felügyeleti végpont konfigurálása](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Kattintson a **Konfiguráció ellenőrzése**elemre. Sikeres ellenőrzés esetén kattintson a **Mentés**gombra. A Jenkins-feladatok folyamata mostantól teljesen konfigurálva van. Ugorjon [Tovább a következő lépésekre](#next-steps) az üzemelő példány teszteléséhez.

## <a name="configure-deployment-using-azure-credentials"></a>Üzembe helyezés konfigurálása az Azure hitelesítő adataival
Éles környezetekben az alkalmazás üzembe helyezéséhez szükséges Azure-beli hitelesítő adatok konfigurálása erősen ajánlott. Ebből a szakaszból megtudhatja, hogyan konfigurálhat egy Azure Active Directory egyszerű szolgáltatást, amellyel az alkalmazást üzembe helyezheti a létrehozás utáni művelet során. A Jenkins-feladatok engedélyeinek korlátozásához hozzárendelhet egy egyszerű szolgáltatást a címtár szerepköreihez. 

Fejlesztési és tesztelési környezetekben az alkalmazás üzembe helyezéséhez az Azure-beli hitelesítő adatokat vagy a fürtszolgáltatás végpontját is konfigurálhatja. A fürt felügyeleti végpontjának konfigurálásával kapcsolatos további információkért lásd: [a központi telepítés konfigurálása a fürt felügyeleti végpontjának használatával](#configure-deployment-using-cluster-management-endpoint).   

1. Azure Active Directory egyszerű szolgáltatásnév létrehozásához és az Azure-előfizetéshez való hozzárendeléséhez kövesse a [portál használata Azure Active Directory alkalmazás és egyszerű szolgáltatás létrehozásához](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)című témakör lépéseit. Ügyeljen a következőkre:

   * A témakör lépéseit követve másolja és mentse a következő értékeket: *Application ID*, *Application Key*, *Directory ID (bérlői azonosító)* és *előfizetés-azonosító*. Szüksége lesz rájuk az Azure-beli hitelesítő adatok konfigurálásához a Jenkins-ben.
   * Ha nem rendelkezik a [szükséges engedélyekkel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) a címtárában, meg kell kérnie a rendszergazdát, hogy engedélyezze Önnek az engedélyeket, vagy hozza létre az egyszerű szolgáltatásnevet, vagy a fürt felügyeleti végpontját is konfigurálnia kell a feladathoz tartozó, a Jenkins-ben végzett **művelet után** .
   * Az [Azure Active Directory alkalmazás létrehozása](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) szakaszban megadhatja a **bejelentkezési URL-cím**bármilyen jól formázott URL-címét.
   * Az [alkalmazás társítása szerepkörhöz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) szakaszban hozzárendelheti az alkalmazáshoz az *olvasó* szerepkört a fürt erőforráscsoporthoz.

1. A Jenkins-feladatokban kattintson a **Build utáni műveletek** fülre.
1. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüből válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. 
1. A **fürtkonfiguráció Service Fabric**alatt jelölje be a **Service Fabric fürt kiválasztása** választógombot. Az Azure-beli **hitelesítő adatok**mellett kattintson a **Hozzáadás** elemre. A Jenkins hitelesítő adatok szolgáltatójának kiválasztásához kattintson a **Jenkins** elemre.
1. A Jenkins hitelesítő adatai szolgáltatónál válassza ki **Microsoft Azure egyszerű szolgáltatásnév** elemet a **típus** legördülő listából.
1. Az 1. lépésben az egyszerű szolgáltatásnév beállításakor mentett értékeket az alábbi mezők beállításához használhatja:

   * **Ügyfél-azonosító**: *alkalmazás azonosítója*
   * **Ügyfél titka**: *alkalmazás kulcsa*
   * **Bérlő azonosítója**: *könyvtár azonosítója*
   * **Előfizetés azonosítója**: *előfizetés azonosítója*
1. Adjon meg egy leíró **azonosítót** , amellyel kiválaszthatja a hitelesítő adatokat a Jenkins szolgáltatásban és egy rövid **leírást**. Ezután kattintson az **egyszerű szolgáltatásnév ellenőrzése**elemre. Ha az ellenőrzés sikeres, kattintson a **Hozzáadás**gombra.

   ![Service Fabric Jenkins Azure-beli hitelesítő adatokat ad meg](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. **Service Fabric fürtkonfiguráció**alatt ellenőrizze, hogy az új hitelesítő adatok ki vannak-e választva az Azure-beli **hitelesítő adatokhoz**. 
1. Az **erőforráscsoport** legördülő menüben válassza ki annak a fürtnek az erőforrását, amelyre telepíteni szeretné az alkalmazást.
1. A **Service Fabric** legördülő listából válassza ki azt a fürtöt, amelyre telepíteni kívánja az alkalmazást.
1. Az **ügyfél kulcsa** és az **ügyfél tanúsítványa**mezőben adja meg a PEM-tároló helyét a Jenkins-tárolóban. Például: `/var/jenkins_home/clustercert.pem`. 
1. Az **alkalmazás konfigurációja**területen adja meg az alkalmazás **neve**, az **alkalmazás típusa**és az alkalmazás jegyzékfájl mezőinek (relatív) **elérési útját** .
    ![Service Fabric Jenkins-létrehozás utáni művelet az Azure-beli hitelesítő adatok konfigurálása](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Kattintson a **Konfiguráció ellenőrzése**elemre. Sikeres ellenőrzés esetén kattintson a **Mentés**gombra. A Jenkins-feladatok folyamata mostantól teljesen konfigurálva van. A telepítés teszteléséhez folytassa a [következő lépésekkel](#next-steps) .

## <a name="troubleshooting-the-jenkins-plugin"></a>A Jenkins beépülő modul hibáinak elhárítása

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>Következő lépések
A GitHub és a Jenkins beállítása kész. Vegye figyelembe, hogy a `reliable-services-actor-sample/Actors/ActorCounter`-projektben a tárházban lévő saját villában módosul a változás, https://github.com/Azure-Samples/service-fabric-java-getting-started. Küldje el a módosításokat a távoli `master` ág (vagy bármely olyan ág esetében, amelyet a szolgáltatással való együttműködésre konfigurált). Ez aktiválja a konfigurált `MyJob` Jenkins-feladatot. Beolvassa a GitHubról származó módosításokat, létrehozza őket, és üzembe helyezi az alkalmazást a létrehozás utáni műveletekben megadott fürtön.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

