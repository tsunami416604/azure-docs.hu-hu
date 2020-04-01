---
title: Folyamatos építés linuxos alkalmazásokhoz a Jenkins használatával
description: Folyamatos létrehozás és integráció a Service Fabric Linux-alkalmazáshoz a Jenkins használatával
keywords: jenkins, azúrkék, devops, cicd, linux
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 96e8c3721b5849b874878ea6a846271fb6760ffd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77675238"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Linux-alkalmazások létrehozása és üzembe helyezése a Jenkins segítségével

Ez az oktatóanyag a Jenkins-környezet beállításának számos lehetséges módját ismerteti, valamint az alkalmazás üzembe helyezésének különböző módjait egy Service Fabric-fürtre, miután az már megépült. Kövesse az alábbi általános lépéseket a Jenkins sikeres konfigurálásához, a módosítások lekérése a GitHubról, az alkalmazás létrehozása és a fürtre való üzembe helyezése:

1. Győződjön meg arról, hogy telepíti az [Előfeltételek](#prerequisites).
1. Ezután kövesse az alábbi szakaszok egyikének lépéseit a Jenkins beállításához:
   * [Jenkins beállítása egy Service Fabric-fürtön belül,](#set-up-jenkins-inside-a-service-fabric-cluster) 
   * [A Jenkins beállítása a Service Fabric-fürtön kívül,](#set-up-jenkins-outside-a-service-fabric-cluster)vagy
   * [Telepítse a Service Fabric bővítményt egy meglévő Jenkins-környezetben.](#install-service-fabric-plugin-in-an-existing-jenkins-environment)
1. A Jenkins beállítása után kövesse a [Jenkins-feladat létrehozása és konfigurálása a Jenkins-feladat](#create-and-configure-a-jenkins-job) beállításához a Jenkins beállításához, amikor az alkalmazás módosításai, és konfigurálja a Jenkins-feladat-folyamatot a buildlépésen keresztül a módosítások lekérése a GitHubról, és az alkalmazás létrehozása. 
1. Végül konfigurálja a Jenkins-feladat létrehozás utáni lépés az alkalmazás üzembe helyezéséhez a Service Fabric-fürtre. A Jenkins kétféleképpen konfigurálható az alkalmazás fürtre történő telepítésére:    
   * A fejlesztési és tesztelési környezetek, használja [a telepítés konfigurálása fürtkezelési végpont használatával](#configure-deployment-using-cluster-management-endpoint). Ez a legegyszerűbb telepítési módszer a beállításhoz.
   * Éles környezetben használja [a Központi telepítés konfigurálása Azure-hitelesítő adatokkal.](#configure-deployment-using-azure-credentials) A Microsoft ezt a módszert éles környezetekben ajánlja, mert az Azure-hitelesítő adatokkal korlátozhatja a Jenkins-feladat hozzáférését az Azure-erőforrásokhoz. 

## <a name="prerequisites"></a>Előfeltételek

- Ellenőrizze, hogy a Git telepítve van-e a helyileg. Telepítheti a megfelelő Git verziót [a Git letöltések lapról](https://git-scm.com/downloads) az operációs rendszer alapján. Ha most ismerkedik a Git- vel, tudjon meg többet róla a [Git](https://git-scm.com/docs)dokumentációjából.
- Ez a cikk a *Service Fabric első lépések minta a* GitHubon: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) az alkalmazás létrehozásához és üzembe helyezéséhez. Elágazhatja ezt a tárházat, hogy kövesse, vagy az utasítások valamilyen módosításával használja a saját GitHub-projektjét.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>A Service Fabric bővítmény telepítése meglévő Jenkins-környezetben

Ha hozzáadja a Service Fabric beépülő modult egy meglévő Jenkins-környezethez, az alábbi lépéseket kell megtennie:

- [Service Fabric CLI (sfctl)](../service-fabric/service-fabric-cli.md). Telepítse a CLI-t a rendszer szintjén, nem pedig a felhasználói szinten, így a Jenkins futtathatja a CLI parancsokat. 
- Java-alkalmazások telepítéséhez telepítse a [Gradle és az Open JDK 8.0](../service-fabric/service-fabric-get-started-linux.md#set-up-java-development)programot is. 
- A .NET Core 2.0 alkalmazások telepítéséhez telepítse a [.NET Core 2.0 SDK -t.](../service-fabric/service-fabric-get-started-linux.md#set-up-net-core-20-development) 

Miután telepítette a környezetéhez szükséges előfeltételeket, megkeresheti az Azure Service Fabric beépülő modult a Jenkins piactéren, és telepítheti azt.

A bővítmény telepítése után ugorjon előre [a Jenkins-feladat létrehozása és konfigurálása című projektre.](#create-and-configure-a-jenkins-job)

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>A Jenkins beállítása egy Service Fabric-fürtben

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő szakaszok bemutatják, hogyan állíthatja be egy fürtön belül, miközben egy Azure storage-fiók használatával menti a tárolópéldány állapotát.

1. Győződjön meg arról, hogy rendelkezik egy Service Fabric Linux-fürt a Docker telepítve. Az Azure-ban futó Service Fabric-fürtök már telepítették a Dockert. Ha helyileg futtatja a fürtöt (OneBox fejlesztési környezet), ellenőrizze, hogy `docker info` a Docker telepítve van-e a számítógépen a paranccsal. Ha nincs telepítve, telepítse a következő parancsokkal:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Győződjön meg arról, hogy a 8081-es port egyéni végpontként van megadva a fürtön. Ha helyi fürtöt használ, győződjön meg arról, hogy a 8081-es port meg van nyitva az állomásszámítógépen, és hogy rendelkezik-e nyilvános IP-címmel.
   >

1. Klónozza az alkalmazást a következő parancsokkal:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. A Jenkins-tároló állapotának megőrzése egy fájlmegosztásban:
   1. Hozzon létre egy Azure-tárfiókot **ugyanabban** a `sfjenkinsstorage1`régióban, mint a fürt neve, például.
   1. Hozzon létre **egy fájlmegosztást** a `sfjenkins`tárfiók alatt a névvel, például .
   1. Kattintson a **Csatlakozás** a fájlmegosztáshoz, és vegye figyelembe az általa megjelenített értékeket **a Csatlakozás Linuxról**alatt, az értéknek az alábbihoz hasonlóan kell kinéznie:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > CIF-megosztások csatlakoztatásához a CIFs-utils csomagot telepíteni kell a fürtcsomópontokba.      
   >

1. Frissítse a helyőrző `setupentrypoint.sh` értékeket a parancsfájlban az azure-storage részleteivel a 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Cserélje `[REMOTE_FILE_SHARE_LOCATION]` le `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` a csatlakozó kimenetének értékére a fenti 2.
   * Cserélje `[FILE_SHARE_CONNECT_OPTIONS_STRING]` le `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` a fenti 2.

1. **Csak biztonságos fürt:** 
   
   Az alkalmazások jenkins-i biztonságos fürtön történő telepítésének konfigurálásához a fürttanúsítványnak elérhetőnek kell lennie a Jenkins-tárolón belül. Az *ApplicationManifest.xml* fájlban a **ContainerHostPolicies** címke adja hozzá ezt a tanúsítványhivatkozást, és frissítse a hüvelykujj-érték a fürttanúsítvány értékével.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Ezenkívül adja hozzá a következő sorokat az **ApplicationManifest** (root) címke alatt az *ApplicationManifest.xml* fájlban, és frissítse a hüvelykujj-értéket a fürttanúsítvány értékével.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Csatlakozzon a fürthöz, és telepítse a tárolóalkalmazást.

   **Biztonságos fürt**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Az előző parancs pem formátumban veszi fel a tanúsítványt. Ha a tanúsítvány PFX formátumú, a következő paranccsal konvertálhatja azt. Ha a PFX-fájl nem jelszóval védett, adja `-passin pass:`meg a **passin** paramétert a ..
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
   > Eltarthat néhány percig, amíg a Jenkins-lemezkép letöltődik a fürtre.
   >

1. Nyissa meg a `http://PublicIPorFQDN:8081` URL-címet a böngészőben. Így megkapja a bejelentkezéshez szükséges kezdeti rendszergazdai jelszó elérési útját. 
1. Tekintse meg a Service Fabric Explorer határozza meg, hogy melyik csomóponton fut a Jenkins-tároló. Biztonságos rendszerhéj (SSH) jelentkezzen be ebbe a csomópontba.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Kérje le a tároló példányazonosítóját a `docker ps -a` paranccsal.
1. Secure Shell (SSH) jelentkezzen be a tárolóba, és illessze be a Jenkins-portálon látható elérési utat. Ha például a portálon az `PATH_TO_INITIAL_ADMIN_PASSWORD`elérési utat jeleníti meg, futtassa a következő parancsokat:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. A Jenkins Első lépések lapon válassza a Telepíteni kívánt bővítmények kiválasztása lehetőséget, jelölje be a **Nincs** jelölőnégyzetet, és kattintson a Telepítés gombra.
1. Hozzon létre egy felhasználót, vagy válassza ki, hogy továbbra is rendszergazdaként.

A Jenkins beállítása után ugorjon a [Jenkins-feladat létrehozása és konfigurálása](#create-and-configure-a-jenkins-job)című területre.  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>A Jenkins beállítása Service Fabric-fürtön kívül

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő szakaszok a fürtön kívüli beállítást mutatják be.

1. Győződjön meg arról, hogy a `docker info` Docker telepítve van a számítógépen a terminálon való futtatással. A kimenet azt jelzi, ha a Docker szolgáltatás fut.

1. Ha a Docker nincs telepítve, futtassa a következő parancsokat:

    ```sh
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
    ```

1. Kérje le a Service Fabric Jenkins-tárolójának rendszerképét: `docker pull rapatchi/jenkins:latest`. A rendszerképhez előre telepítve van a Service Fabric Jenkins beépülő modulja.
1. Futtassa a tároló rendszerképét: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Kérje le a tárolórendszerkép-példány azonosítóját. A `docker ps –a` paranccsal az összes Docker-tárolót listázhatja
1. Jelentkezzen be a Jenkins portálra a következő lépésekkel:

   1. Jelentkezzen be egy Jenkins-rendszerhéjba a gazdagéptől. Használja a tárolóazonosító első négy számjegyét. Ha például a tárolóazonosító `2d24a73b5964`ja, használja a használatát. `2d24`

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. A Jenkins-rendszerhéjból a tárolópéldány rendszergazdai jelszavát kéri le:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. A Jenkins-irányítópultra való bejelentkezéshez nyissa meg a `http://<HOST-IP>:8080`következő URL-címet egy webböngészőben: . Használja a jelszót az előző lépésben a Jenkins feloldásához.
   1. (Nem kötelező.) Miután első alkalommal jelentkezett be, létrehozhatja saját felhasználói fiókját, és használhatja azt a következő lépésekhez, vagy továbbra is használhatja a rendszergazdai fiókot. Ha létrehoz egy felhasználót, akkor a felhasználóval kell folytatnia.
1. Állítsa be a GitHubot a Jenkins-szel való együttműködésre az [új SSH-kulcs létrehozása és az SSH-ügynökhöz való hozzáadása](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)lépéseivel.
   * A GitHub utasításait követve hozza létre az SSH-kulcsot, majd adja hozzá ahhoz a GitHub-fiókhoz, amelyen az adattár üzemel.
   * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén).
   * Ahhoz, hogy a saját gazdagépéről jelentkezhessen be a Jenkinsbe, használja a következő parancsot:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Győződjön meg arról, hogy a fürt vagy a gép, ahol a Jenkins-tároló rendszerkép található, rendelkezik egy nyilvános ip-címet. Ez lehetővé teszi, hogy a Jenkins-példány megkapja a GitHub értesítéseit.

A Jenkins beállítása után folytassa a következő [szakaszsal, a Jenkins-feladat létrehozása és konfigurálása című szakaszban.](#create-and-configure-a-jenkins-job)

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins-feladatok létrehozása és konfigurálása

Ebben a szakaszban található lépések bemutatják, hogyan konfigurálhat egy Jenkins-feladatot a GitHub-tárházban bekövetkező változásokra, a módosítások lekéréséhez és létrehozásához. A szakasz végén a rendszer az utolsó lépésekre irányítja az alkalmazás üzembe helyezéséhez szükséges feladat konfigurálásához, függetlenül attól, hogy fejlesztési/tesztelési környezetben vagy éles környezetben telepíti-e. 

1. A Jenkins irányítópulton kattintson az **Új elem gombra.**
1. Adjon nevet az elemnek (pl. **MyJob**). Válassza a **free-style project** (szabad projekt) lehetőséget, majd kattintson az **OK** gombra.
1. Megnyílik a Feladat konfigurációja lap. (Ha a Jenkins-irányítópulton szeretné megtérülni a konfigurációval, kattintson a feladatra, majd a **Konfigurálás parancsra.**

1. Az **Általános** lapon jelölje be a **GitHub-projekt**jelölőnégyzetet, és adja meg a GitHub-projekt URL-címét. Ez az URL-cím üzemelteti azt a Service Fabric Java-alkalmazást, amelyet integrálni szeretne a Jenkins folyamatos integrációs és üzembe helyezési (CI/CD) folyamatával (például: `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. A **Forráskód kezelése** lapon válassza a **Git**lehetőséget. Adja meg annak az adattárnak az URL-címét, amely a Jenkins CI/CD folyamatával integrálni kívánt Service Fabric Java-alkalmazást tartalmazza (például: `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Azt is megadhatja, hogy melyik `/master`ágat (például ) építse meg.
1. Konfigurálja a *GitHub-tárházat* a Jenkins-szel való beszélgetéshez:

   a. A GitHub-tárház lapján nyissa meg a **Beállítások** > **integrációk és szolgáltatások lapot.**

   b. Válassza az **Add Service** (Szolgáltatás hozzáadása) elemet, írja be a **Jenkins** kifejezést, majd válassza ki a **Jenkins-GitHub beépülő modult**.

   c. Adja meg a Jenkins-webhook URL-címét (alapértelmezés szerint ez a következő: `http://<PublicIPorFQDN>:8081/github-webhook/`). Kattintson az **add/update service** (Szolgáltatás hozzáadása/frissítése) elemre.

   d. A Jenkins-példányra egy tesztesemény érkezik. A GitHubban egy zöld pipa jelenik meg a webhook mellett, és a projekt létrejön.

1. A **Jenkins buildeseményindítók** lapján válassza ki, hogy melyik buildbeállítást szeretné. Ebben a példában szeretne elindítani egy buildet, amikor leküldéses a tárház, ezért válassza a **GitHub hook trigger gitscm lekérdezési**. (Korábban ez **Build when a change is pushed to GitHub** (Felépítés módosítások GitHubon való közzétételekor) volt.)
1. A **Build** lapon hajtsa után az alábbi lehetőségek egyikét attól függően, hogy Java vagy .NET Core alkalmazást hoz létre:

   * **Java alkalmazásokhoz:** A **Build hozzáadása lépés** legördülő menüben válassza **a Gradle-parancsfájl meghívása**lehetőséget. Kattintson **a Speciális gombra.** A speciális menüben adja meg az alkalmazás **gyökérbuild-parancsfájljának** elérési útját. A rendszer felveszi a build.gradle elemet a megadott elérési útból, és annak megfelelően működik. Az [ActorCounter alkalmazás](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)esetében `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`ez a következő: .

     ![Service Fabric, Jenkins felépítési művelet](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png)

   * **.NET core alkalmazások esetén:** A **Build hozzáadása lépés** legördülő menüben válassza **a Shell végrehajtása**lehetőséget. A megjelenő parancsmezőben először a könyvtárat kell módosítani arra az elérési útra, ahol a build.sh fájl található. Miután a könyvtár megváltozott, a build.sh parancsfájl futtatható, és létrehozza az alkalmazást.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     A következő képernyőképen egy példa látható a [parancsokat,](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) amelyek létrehozásához használt a Counter Service minta egy Jenkins feladat neve CounterServiceApplication.

      ![Service Fabric, Jenkins felépítési művelet](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png)

1. Konfigurálása Jenkins az alkalmazás üzembe helyezéséhez egy Service Fabric-fürta közzététel utáni műveletek, szüksége van a helyét, hogy a fürt tanúsítványa a Jenkins-tárolóban. Válasszon az alábbi adatok közül attól függően, hogy a Jenkins-tároló a fürtön belül vagy kívül fut-e, és jegyezze fel a fürttanúsítvány helyét:

   * **A fürtön belül futó Jenkins esetén:** A tanúsítvány elérési útja a *Certificates_JenkinsOnSF_Code_MyCert_PEM* környezeti változó értékének visszhangjával érhető el a tárolón belül.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **A fürtön kívül futó Jenkins esetén:** A fürttanúsítvány tárolóba másolásához kövesse az alábbi lépéseket:
     1. A tanúsítványnak PEM formátumúnak kell lennie. Ha nem rendelkezik PEM-fájllal, létrehozhat egyet a tanúsítvány PFX-fájljából. Ha a PFX-fájl nem jelszóval védett, futtassa a következő parancsot a gazdagépről:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Ha a PFX fájl jelszóval védett, `-passin` adja meg a jelszót a paraméterben. Példa:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. A Jenkins-tároló tárolóazonosítójának lekérnie, futtassa `docker ps` a gazdagépről.
     1. Másolja a PEM-fájlt a tárolóba a következő Docker paranccsal:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Már majdnem kész vagy! Tartsa nyitva a Jenkins-munkát. Az egyetlen fennmaradó feladat az alkalmazás Service Fabric-fürtre való üzembe helyezéséhez szükséges build utáni lépések konfigurálása:

* Fejlesztési vagy tesztelési környezetben való üzembe helyezéshez kövesse a [Központi telepítés konfigurálása fürtkezelési végpont használatával című lépéseit.](#configure-deployment-using-cluster-management-endpoint)
* Éles környezetben való üzembe helyezéshez kövesse a [Központi telepítés konfigurálása Azure-hitelesítő adatokkal című lépéseit.](#configure-deployment-using-azure-credentials)

## <a name="configure-deployment-using-cluster-management-endpoint"></a>A telepítés konfigurálása fürtkezelési végpont használatával

Fejlesztési és tesztelési környezetek, használhatja a fürtfelügyeleti végpont az alkalmazás üzembe helyezéséhez. A létrehozás utáni művelet konfigurálása a fürtfelügyeleti végpontot az alkalmazás üzembe helyezéséhez a legkevesebb beállításszükséges. Ha éles környezetben telepíti, ugorjon előre a [Központi telepítés konfigurálása Azure-hitelesítő adatokhasználatával](#configure-deployment-using-azure-credentials) konfigurálásához egy Azure Active Directory szolgáltatás egyszerű használatba helyezésa a központi telepítés során.    

1. A Jenkins-feladatban kattintson a **Létrehozás utáni műveletek** fülre. 
1. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüből válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. 
1. A **Service Fabric fürtkonfiguráció csoportjában**válassza **a Service Fabric Management végpont kitöltése** választógombot.
1. A **Felügyeleti állomás csoportban**adja meg a fürt kapcsolati végpontját; például `{your-cluster}.eastus.cloudapp.azure.com`.
1. Az **ügyfélkulcs** és **az ügyféltanúsítvány**esetén adja meg a PEM-fájl helyét a Jenkins-tárolóban; például `/var/jenkins_home/clustercert.pem`. (A tanúsítvány helyét a [Jenkins-feladat létrehozása és konfigurálása](#create-and-configure-a-jenkins-job)utolsó lépéséhez másolta.)
1. Az **Alkalmazáskonfiguráció**csoportban adja meg az **Alkalmazás nevét**, az **Alkalmazástípusát**és az Alkalmazásjegyzék (relatív) **elérési útját.**

   ![Service Fabric Jenkins buildelés utáni művelet konfigurálása felügyeleti végpont](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Kattintson **a Konfiguráció ellenőrzése gombra.** A sikeres ellenőrzéskor kattintson a **Mentés gombra.** A Jenkins-feladat-folyamat most már teljesen konfigurálva van. Ugorjon előre a [következő lépésekre](#next-steps) az üzembe helyezés teszteléséhez.

## <a name="configure-deployment-using-azure-credentials"></a>Telepítés konfigurálása Azure-hitelesítő adatokkal

Éles környezetekben az Azure-hitelesítő adatok konfigurálása az alkalmazás üzembe helyezéséhez erősen ajánlott. Ez a szakasz bemutatja, hogyan konfigurálhatja az Azure Active Directory egyszerű szolgáltatás az alkalmazás üzembe helyezéséhez a build utáni műveletben. A szolgáltatásnévi tagokat a címtárszerepkörökhöz rendelheti hozzá a Jenkins-feladat engedélyeinek korlátozása érdekében. 

Fejlesztési és tesztelési környezetek, konfigurálhatja az Azure hitelesítő adatok at vagy a fürtfelügyeleti végpont az alkalmazás üzembe helyezéséhez. A fürtkezelési végpontok konfigurálásáról a [Központi telepítés konfigurálása fürtkezelési végponthasználatával](#configure-deployment-using-cluster-management-endpoint)című témakörben talál további információt.   

1. Ha egyszerű Azure Active Directory-szolgáltatást szeretne létrehozni, és engedélyeket szeretne hozzárendelni az Azure-előfizetéséhez, kövesse [a Portál használata azure Active Directory-alkalmazás és egyszerű szolgáltatás létrehozásácímű lépései című](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)részt. Ügyeljen a következőkre:

   * A témakör lépéseit követve mindenképpen másolja és mentse a következő értékeket: *Alkalmazásazonosító*, *alkalmazáskulcs*, *címtárazonosító (bérlői azonosító)* és *előfizetés-azonosító*. Szüksége van rájuk az Azure-hitelesítő adatok konfigurálásához a Jenkinsben.
   * Ha nem rendelkezik a [szükséges engedélyekkel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) a címtárban, meg kell kérnie a rendszergazdát, hogy adja meg az engedélyeket, vagy hozza létre az egyszerű szolgáltatást, vagy konfigurálnia kell a fürt felügyeleti végpontját a Jenkins-beli feladathoz a **buildelés utáni műveletekhez.**
   * Az [Azure Active Directory-alkalmazás létrehozása](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) szakaszban megadhatja a **bejelentkezési URL-cím**hez jól formázott URL-címet.
   * Az [alkalmazás hozzárendelése szerepkörhöz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) szakaszban az alkalmazáshoz *rendelheti* az Olvasó szerepkört a fürt erőforráscsoportjában.

1. A Jenkins-feladatban kattintson a **Létrehozás utáni műveletek** fülre.
1. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüből válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. 
1. A **Service Fabric cluster configuration (Szolgáltatásháló-fürt konfigurációja)** csoportban kattintson a Service Fabric Cluster kiválasztása **gombra.** Kattintson a **Hozzáadás** gombra az **Azure-hitelesítő adatok**elem mellett. Kattintson a **Jenkins** elemre a Jenkins hitelesítő adatok szolgáltatójának kiválasztásához.
1. A Jenkins hitelesítő adatok szolgáltató, válassza ki a **Microsoft Azure Service Principal** a **Kind** legördülő menüből.
1. Az 1.

   * **Ügyfélazonosító**: *Alkalmazásazonosító*
   * **Ügyféltitok**: *Alkalmazáskulcs*
   * **Bérlőazonosítója**: *Címtárazonosító*
   * **Előfizetés azonosítója**: *Előfizetés-azonosító*
1. Adja meg a hitelesítő adatok Jenkins ben történő kiválasztásához használt leíró **azonosítót** és egy rövid **leírást.** Ezután kattintson **az Egyszerű szolgáltatás ellenőrzése gombra.** Ha az ellenőrzés sikeres, kattintson a **Hozzáadás**gombra.

   ![A Service Fabric Jenkins adja meg az Azure hitelesítő adatait](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. A **Service Fabric-fürt konfigurációja**területen győződjön meg arról, hogy az új hitelesítő adatok az **Azure-hitelesítő adatokhoz**vannak kiválasztva. 
1. Az **Erőforráscsoport** legördülő menüből válassza ki annak a fürtnek az erőforráscsoportját, amelybe az alkalmazást telepíteni szeretné.
1. A **Service Fabric** legördülő menüből válassza ki azt a fürtöt, amelybe az alkalmazást telepíteni szeretné.
1. Az **ügyfélkulcs** és **az ügyféltanúsítvány**esetén adja meg a PEM-fájl helyét a Jenkins-tárolóban. Például: `/var/jenkins_home/clustercert.pem`. 
1. Az **Alkalmazáskonfiguráció**csoportban adja meg az **Alkalmazás nevét**, az **Alkalmazástípusát**és az Alkalmazásjegyzék (relatív) **elérési útját.**
    ![Service Fabric Jenkins buildelés utáni művelet – Azure-hitelesítő adatok konfigurálása](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Kattintson **a Konfiguráció ellenőrzése gombra.** A sikeres ellenőrzéskor kattintson a **Mentés gombra.** A Jenkins-feladat-folyamat most már teljesen konfigurálva van. Folytassa a [következő lépésekkel](#next-steps) az üzembe helyezés teszteléséhez.

## <a name="troubleshooting-the-jenkins-plugin"></a>A Jenkins beépülő modul hibáinak elhárítása

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="ideas-to-try"></a>Ötletek, hogy megpróbálja

A GitHub és a Jenkins beállítása kész. Fontolja meg, hogy `reliable-services-actor-sample/Actors/ActorCounter` néhány minta változás a projekt https://github.com/Azure-Samples/service-fabric-java-getting-starteda villában a tárház, . A módosítások leküldése `master` a távoli ágra (vagy bármely olyan ágra, amelyhez konfigurált a munka). Ez aktiválja a konfigurált `MyJob` Jenkins-feladatot. Lekéri a módosításokat a GitHubról, létrehozza őket, és telepíti az alkalmazást a build utáni műveletekben megadott fürtre.  

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Jenkins az Azure-on](/azure/Jenkins/)