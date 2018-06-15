---
title: Folyamatos build és az integráció az Azure Service Fabric Linux alkalmazások Jenkins használatával |} Microsoft Docs
description: Folyamatos build és a Service Fabric Linux-alkalmazást Jenkins-integráció
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: ''
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: saysa
ms.openlocfilehash: 047b3d00da4f192febeeab79c9c87b67a8a0489b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207961"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Jenkins segítségével hozza létre, és a Linux-alkalmazások központi telepítése
A Jenkins egy népszerű eszköz az alkalmazások folyamatos integrációjához és üzembe helyezéséhez. Ebből a témakörből megtudhatja, hogyan helyezheti üzembe Azure Service Fabric-alkalmazásait a Jenkins használatával.

## <a name="topic-overview"></a>A témakör áttekintése
Ez a cikk ismerteti a Jenkins környezet beállítása több lehetséges módjait, valamint a különböző módokon az alkalmazás számára a Service Fabric-fürt telepítése után be lett építve. Sikeresen telepítő Jenkins, húzza módosítások a Githubból, építenie az alkalmazást, és központilag telepítenie kell a fürt általános lépések végrehajtásával:

1. Győződjön meg arról, hogy telepítse a [Előfeltételek](#prerequisites).
2. Kövesse a lépéseket egy ezekben a szakaszokban Jenkins beállítása:
   * [A Service Fabric-fürt belül Jenkins beállítása](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [A Service Fabric-fürt kívül Jenkins beállítása](#set-up-jenkins-outside-a-service-fabric-cluster), vagy
   * [A Service Fabric beépülő modul telepítése egy meglévő környezetben Jenkins](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
3. Miután beállította a Jenkins, kövesse a [létrehozása Jenkins feladat konfigurálása és](#create-and-configure-a-jenkins-job) beállítani GitHub eseményindító Jenkins Ha módosításai az alkalmazás és a Jenkins feladat folyamat-a build lépéseket való lekérésére konfigurálni a módosítja a Githubról és építenie az alkalmazást. 
4. Végezetül az alkalmazás számára a Service Fabric-fürt telepítése Jenkins feladat utáni lépés konfigurálható. A fürthöz az alkalmazás közzétételéhez Jenkins konfigurálása két módja van:    
   * A fejlesztési és tesztkörnyezetek, használjon [konfigurálása a fürt felügyeleti végpontja állításra](#configure-deployment-using-cluster-management-endpoint). Ez az a legegyszerűbb módszer beállítása.
   * Éles környezetben használjon [Azure hitelesítő adataival központi telepítésének konfigurálása](#configure-deployment-using-azure-credentials). A Microsoft azt javasolja ezt a módszert, éles környezetben, mert az Azure hitelesítő adatokkal rendelkező korlátozhatja a hozzáférést, amely a Jenkins feladat rendelkezik az Azure-erőforrások. 

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy helyi telepítésű az Git. Telepítheti a megfelelő Git verziót [a Git letölti lap](https://git-scm.com/downloads) az operációs rendszer alapján. Ha most ismerkedik a Git, további információ a a [Git dokumentáció](https://git-scm.com/docs).
- Ebben a cikkben az a *Service Fabric lépések minta első* a Githubon: [ https://github.com/Azure-Samples/service-fabric-java-getting-started ](https://github.com/Azure-Samples/service-fabric-java-getting-started) az alkalmazás létrehozásához és telepítéséhez. Ebben a tárházban követéséhez forkolhatja, de néhány módosítással utasításokat, használja a saját GitHub-projektet.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>A Service Fabric beépülő modul telepítése meglévő Jenkins környezetben
A Service Fabric beépülő modul hozzáadása a meglévő Jenkins környezethez, a következők szükségesek:

- A [Service Fabric CLI](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Győződjön meg arról, a rendszer szintjén CLI telepítése helyett felhasználói szinten, ezért Jenkins futtathat parancssori felület parancsait. 
   >

- Java-alkalmazások központi telepítéséhez telepítse két [Gradle és a nyitott JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development). 
- Telepítse a .NET Core 2.0 alkalmazások központi telepítéséhez a [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Ha már telepítette a környezetében szükséges előfeltételeket, keresse meg az Azure Service Fabric beépülő modul Jenkins piactéren, és telepítse.

Ha már telepítette a beépülő modul, ugorjon előre [létrehozása és Jenkins feladatot](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>A Jenkins beállítása egy Service Fabric-fürtben

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő szakaszok bemutatják, hogyan állítsa be a fürtben található Azure-tárfiók menteni az állapotot, a tároló-példány használata során.

### <a name="prerequisites"></a>Előfeltételek
- A Service Fabric Linux-fürt Docker telepítve rendelkezik. Már fut az Azure Service Fabric-fürtök Docker telepítve van. Ha a fürt helyben (OneBox fejlesztői környezetben) fut, Docker meglétének ellenőrzése a számítógépen a `docker info` parancsot. Ha nincs telepítve, telepítse a következő parancsokkal:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Győződjön meg arról, hogy a 8081 port van megadva egy egyéni végpont a fürtön. Használatakor egy helyi fürtöt, győződjön meg arról, hogy a port 8081 nyissa meg a gazdaszámítógépen-e, és arról, hogy rendelkezik-e nyilvánosan elérhető IP-címnek.
   >

### <a name="steps"></a>Lépések
1. Az alkalmazás klónozása a következő parancsokkal:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

3. A fájlmegosztás-tároló Jenkins állapot megőrzése:
   1. Az Azure storage-fiók létrehozása a **ugyanabban a régióban** egy névvel, mint a fürt `sfjenkinsstorage1`.
   2. Hozzon létre egy **fájlmegosztás** alatt a tárolási fiók nevére, mint `sfjenkins`.
   3. Kattintson a **Connect** fájlmegosztási és Megjegyzés: az értékek megjeleníti a **Linux csatlakozó**, az érték az alábbihoz hasonlóan kell kinéznie:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Csatlakoztatási cifs megosztások meg kell rendelkeznie a cifs-utils csomag, a fürtcsomópontok telepítve.      
   >

4. Frissítse az helyőrző értékeket a `setupentrypoint.sh` parancsfájl a 2. lépésben az azure-tároló adatokkal.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Cserélje le `[REMOTE_FILE_SHARE_LOCATION]` értékű `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` kimenetében a csatlakozás a fenti 2. lépés.
   * Cserélje le `[FILE_SHARE_CONNECT_OPTIONS_STRING]` értékű `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` a fenti 2. lépés.

5. **Csak biztonságos fürt esetén:** 
   
   Alkalmazások központi telepítésének konfigurálásához a Jenkins biztonságos fürt, a fürt tanúsítvány Jenkins tárolóban elérhetőnek kell lennie. Az a *ApplicationManifest.xml* a fájlt a **ContainerHostPolicies** címke hozzáadása az adott tanúsítvány esetében, és frissítse az ujjlenyomat értékét, amely a fürt tanúsítvány.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Emellett adja hozzá a következő sorokat a a **applicationmanifest jegyzékben** (root) címkét a *ApplicationManifest.xml* fájlt, és frissítse a fürt tanúsítvány, amely az ujjlenyomat értékét.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

6. Csatlakozzon a fürthöz, és a tároló alkalmazás telepítéséhez.

   **Biztonságos fürt**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Az előző parancs a tanúsítványt a PEM-formátumba vesz igénybe. Ha a tanúsítvány PFX formátumban van, a következő paranccsal konvertálja. Ha a PFX-fájl nem jelszóval védett, adja meg a **passin** paraméterként `-passin pass:`.
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
   > Eltarthat néhány percig Jenkins kép le kell tölteni a fürtön.
   >

7. Nyissa meg a `http://PublicIPorFQDN:8081` URL-címet a böngészőben. Így megkapja a bejelentkezéshez szükséges kezdeti rendszergazdai jelszó elérési útját. 
2. Tekintse meg a Service Fabric Explorer állapítható meg, melyik csomópontján fut a Jenkins tároló. Secure Shell (SSH) jelentkezzen be ezt a csomópontot.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
3. Kérje le a tároló példányazonosítóját a `docker ps -a` paranccsal.
4. Secure Shell (SSH) jelentkezzen be a tárolóhoz, és illessze be az elérési út volt látható a Jenkins portálon. Például, ha a portál megjeleníti elérési `PATH_TO_INITIAL_ADMIN_PASSWORD`, a következő parancsokat:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
5. A bevezetés Jenkins a lapon választhatja ki a Select beépülő modulok telepítése lehetőséget, jelölje be a **nincs** jelölőnégyzetet, majd kattintson a telepítés.
6. Hozzon létre egy felhasználó vagy, rendszergazdaként a folytatáshoz válasszon

Miután beállította a Jenkins, ugorjon előre [létrehozása és Jenkins feladatot](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>A Jenkins beállítása Service Fabric-fürtön kívül

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő szakaszok a fürtön kívüli beállítást mutatják be.

### <a name="prerequisites"></a>Előfeltételek
- Győződjön meg arról, hogy a Docker telepítve van a számítógépen. A következő parancsokkal telepítheti a Dockert a terminálról:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Amikor futtatja `docker info` a terminálban a kimeneti jelenítsen meg, hogy a Docker-szolgáltatás fut-e.

### <a name="steps"></a>Lépések
1. Kérje le a Service Fabric Jenkins-tárolójának rendszerképét: `docker pull rapatchi/jenkins:latest`. A rendszerképhez előre telepítve van a Service Fabric Jenkins beépülő modulja.
2. Futtassa a tároló rendszerképét: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
3. Kérje le a tárolórendszerkép-példány azonosítóját. A `docker ps –a` paranccsal az összes Docker-tárolót listázhatja
4. Jelentkezzen be a Jenkins portálra a következő lépéseket:

   1. Jelentkezzen be egy Jenkins rendszerhéj a gazdagépről. Az első négy számjegy a tárolót azonosító használata Például, ha a tárolót azonosító `2d24a73b5964`, használjon `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   2. A tároló példányát a rendszergazdai jelszó lekérése az Jenkins rendszerhéj:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   3. Jelentkezzen be a Jenkins irányítópult, nyissa meg a következő URL-címet egy webböngészőben: `http://<HOST-IP>:8080`. Az előző lépésben jelszó használata Jenkins feloldásához.
   4. (Választható.) Az első alkalommal való bejelentkezés után hozzon létre egy saját felhasználói fiókot, és használja a következő lépéseket, vagy továbbra is rendszergazdai fiókot használja. Ha egy felhasználó hoz létre, továbbra is kell az adott felhasználóhoz.
5. Állítsa be a GitHub Jenkins dolgozni a lépések segítségével [új SSH-kulcs létrehozása, majd az SSH-Agent hozzáadásával](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * A GitHub utasításait követve hozza létre az SSH-kulcsot, majd adja hozzá ahhoz a GitHub-fiókhoz, amelyen az adattár üzemel.
   * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén).
   * Ahhoz, hogy a saját gazdagépéről jelentkezhessen be a Jenkinsbe, használja a következő parancsot:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Győződjön meg arról, hogy a fürt vagy a számítógép, ahol a Jenkins tároló lemezképének tárolása egy nyilvánosan elérhető IP-címet. Ez lehetővé teszi, hogy a Jenkins-példány megkapja a GitHub értesítéseit.

Miután beállította a Jenkins, folytassa a következő szakaszban [létrehozása és Jenkins feladatot](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins-feladatok létrehozása és konfigurálása

Ebben a szakaszban a lépések bemutatják a GitHub-tárházban változásait, lehívni a módosításokat, és állítsa be őket a Jenkins feladatot. Ez a szakasz végén beállítása alapján, hogy telepít egy fejlesztési és tesztelési célú környezet vagy egy éles környezetben az alkalmazás közzétételéhez a feladat utolsó lépéseit a van átirányítva. 

1. A Jenkins irányítópultján kattintson **új elem**.
2. Adjon nevet az elemnek (pl. **MyJob**). Válassza a **free-style project** (szabad projekt) lehetőséget, majd kattintson az **OK** gombra.
3. A feladat konfigurálása lapon nyílik meg. (A konfiguráció lekérése a Jenkins irányítópultot, kattintson a feladat, majd **konfigurálása**).

4. Az a **általános** lapra, jelölje be a **GitHub-projekt**, és adja meg a GitHub-projekt URL-címe. Ez az URL-cím üzemelteti azt a Service Fabric Java-alkalmazást, amelyet integrálni szeretne a Jenkins folyamatos integrációs és üzembe helyezési (CI/CD) folyamatával (például: `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

5. Az a **forrás kód felügyeleti** lapon jelölje be **Git**. Adja meg annak az adattárnak az URL-címét, amely a Jenkins CI/CD folyamatával integrálni kívánt Service Fabric Java-alkalmazást tartalmazza (például: `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Azt is megadhatja, mely fiókirodai összeállításához (például `/master`).
6. Konfigurálja a *GitHub* felvegye a Jenkins tárházat:

   a. Nyissa meg a GitHub-tárház oldalon **beállítások** > **integrációja és a szolgáltatások**.

   b. Válassza az **Add Service** (Szolgáltatás hozzáadása) elemet, írja be a **Jenkins** kifejezést, majd válassza ki a **Jenkins-GitHub beépülő modult**.

   c. Adja meg a Jenkins-webhook URL-címét (alapértelmezés szerint ez a következő: `http://<PublicIPorFQDN>:8081/github-webhook/`). Kattintson az **add/update service** (Szolgáltatás hozzáadása/frissítése) elemre.

   d. A Jenkins-példányra egy tesztesemény érkezik. A GitHubban egy zöld pipa jelenik meg a webhook mellett, és a projekt létrejön.

7. Az a **Build eseményindítók** Jenkins lapján válassza ki, amely összeállítása a kívánt beállítást. Ebben a példában keresi a build indítás, amikor a tárházba leküldéses történik, így válasszon **GitHub hook eseményindítója a következőnek: GITScm lekérdezési**. (Korábban ez **Build when a change is pushed to GitHub** (Felépítés módosítások GitHubon való közzétételekor) volt.)
8. Az a **Build** lapra, attól függően, hogy Java-alkalmazások vagy a .NET Core alkalmazás felépítése még a következőket:

   * **Java-alkalmazások:** a a **Hozzáadás összeállítása lépés** legördülő listából válassza **meghívása Gradle parancsfájl**. Kattintson a **speciális**. A Speciális menüben adja meg az elérési útját **legfelső szintű build script** az alkalmazáshoz. A rendszer felveszi a build.gradle elemet a megadott elérési útból, és annak megfelelően működik. Az a [ActorCounter alkalmazás](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), ez: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Service Fabric, Jenkins felépítési művelet][build-step]

   * **A .NET Core-alkalmazásokban:** a a **Hozzáadás összeállítása lépés** legördülő listából válassza **hajtható végre rendszerhéj**. A parancs mezőben, amely akkor jelenik meg a címtár először meg kell módosítani a build.sh fájl elérési útját. A címtár módosult, miután a build.sh parancsfájl futtathatja, és az alkalmazást fog létrehozni.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Az alábbi képernyőfelvételen látható egy példa a parancsok, amelyek alapján állítja össze a [teljesítményszámláló szolgáltatást](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) CounterServiceApplication Jenkins feladat nevű minta.

      ![Service Fabric, Jenkins felépítési művelet][build-step-dotnet]

9. Jenkins szeretné telepíteni az alkalmazást a utáni műveletek a Service Fabric-fürt konfigurálásához szükséges a Jenkins tároló adott fürt tanúsítvány helyét. Válassza ki az egyiket a következők attól függően, hogy a Jenkins tároló fut-e belül vagy kívül a fürt, és jegyezze fel a fürt tanúsítvány helyét:

   * **A a fürtön belül futó Jenkins:** a tanúsítvány elérési útja találhatók értékének megjelenítése a *Certificates_JenkinsOnSF_Code_MyCert_PEM* környezeti változó a tárolóban.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **A fut a fürtön kívüli Jenkins:** kövesse az alábbi lépéseket a fürt tanúsítvány átmásolása a tároló:
      1. A tanúsítvány PEM formátumúnak kell lennie. Ha nem rendelkezik a PEM-fájl, létrehozhat egy, a tanúsítvány PFX-fájlból. Ha a PFX-fájl nem jelszóval védett, a következő parancsot a az állomás:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
         ``` 

      Ha a PFX-fájl jelszóval védett, a jelszót tartalmazza a `-passin` paraméter. Példa:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
         ``` 

      2. A tárolót azonosító beszerzése a Jenkins tároló, futtassa a `docker ps` a gazdagépről.
      3. A PEM-fájl másolása a tároló Docker a következő paranccsal:
    
         ```sh
         docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
         ``` 

Majdnem végzett! Tartsa nyitva a Jenkins feladat. Az egyetlen fennmaradó feladat be nem konfigurálhatja az alkalmazás a Service Fabric-fürt telepítése utáni lépéseit:

* Az fejlesztési vagy tesztelési környezetben való telepítéséhez kövesse a lépéseket [konfigurálása a fürt felügyeleti végpontja állításra](#configure-deployment-using-cluster-management-endpoint).
* Éles környezetben való telepítéséhez kövesse a [Azure hitelesítő adataival központi telepítésének konfigurálása](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>A fürt felügyeleti végpontja használó telepítés konfigurálása
A fürt felügyeleti végpont fejlesztési és tesztkörnyezetek, használhatja az alkalmazás közzétételéhez. A lehető legkevesebb beállításról a létrehozás után végrehajtandó művelet konfigurálása a fürt felügyeleti végponttal az alkalmazás központi telepítése szükséges. Ha éles környezetben telepít, ugorjon előre [Azure hitelesítő adataival központi telepítésének konfigurálása](#configure-deployment-using-azure-credentials) konfigurálása az Azure Active Directory szolgáltatás egyszerű telepítéskor használni.    

1. A Jenkins feladat, kattintson a **utáni műveletek** fülre. 
2. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüből válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. 
3. A **Service Fabric fürtkonfiguráció**, jelölje be a **töltse ki a Service Fabric felügyeleti végpont** választógombot.
4. A **felügyeleti állomás**, adja meg a csatlakozási végpont a fürt; például `{your-cluster}.eastus.cloudapp.azure.com`.
5. A **Ügyfélkulcsot** és **ügyféltanúsítványt**, adja meg a PEM-fájl helyét a Jenkins tárolóban; például `/var/jenkins_home/clustercert.pem`. (Az utolsó lépése annak a helyet a tanúsítvány másolt [létrehozása és Jenkins feladatot](#create-and-configure-a-jenkins-job).)
6. A **alkalmazás konfigurációja**, konfigurálja a **alkalmazásnév**, **alkalmazástípus**, (relatív) és **elérési útját Application Manifest** mezőket.

   ![Service Fabric Jenkins utáni művelet felügyeleti végpont konfigurálása](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

7. Kattintson a **konfigurációjának ellenőrzése**. Kattintson az ellenőrzés sikeres **mentése**. A Jenkins feladat folyamat mostantól teljesen konfigurálva. Ugorjon előre [további lépések](#next-steps) az üzemelő példány.

## <a name="configure-deployment-using-azure-credentials"></a>Az Azure hitelesítő adataival telepítés konfigurálása
Éles környezetben az alkalmazás központi telepítése egy Azure hitelesítő adatok konfigurálása erősen ajánlott. Ez a szakasz bemutatja, hogyan konfigurálhatja az Azure Active Directory szolgáltatás egyszerű szeretné telepíteni az alkalmazást a létrehozás után végrehajtandó művelet. Szerepkörök szolgáltatásnevekről rendelhet a Jenkins feladat engedélyek korlátozása a könyvtárban. 

Fejlesztési és tesztelési környezetben vagy Azure hitelesítő adatait, vagy az alkalmazás központi telepítése a fürt felügyeleti végpont konfigurálhatja. A fürt felügyeleti végpont konfigurálásával kapcsolatos részletekért lásd: [konfigurálása a fürt felügyeleti végpontja állításra](#configure-deployment-using-cluster-management-endpoint).   

1. Hozzon létre egy Azure Active Directory szolgáltatás egyszerű, és rendelje hozzá az Azure-előfizetéshez tartozó engedélyek, kövesse a lépéseket a [a portál használatával hozzon létre egy Azure Active Directory alkalmazás és szolgáltatás egyszerű](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal). Nagy figyelmet fordítani az a következőket:

   * A témakörben ismertetett lépések követése, során ügyeljen arra, hogy másolhat és menthet a következő értékek: *Alkalmazásazonosító*, *alkalmazáskulcsot*, *könyvtár-azonosítója (bérlő azonosító)*, és *Előfizetés-azonosító*. Azokat az Azure hitelesítő adatok konfigurálása a Jenkins van szüksége.
   * Ha nem rendelkezik a [szükséges engedélyek](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) a könyvtárban kell kérje meg egy rendszergazda adja meg azt az engedélyeket, vagy az Ön a szolgáltatásnevet létrehozni, vagy a felügyeleti végpontja konfigurálni kell a a fürt a **utáni műveletek** a Jenkins a feladathoz.
   * Az a [hozzon létre egy Azure Active Directory-alkalmazás](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) a szakaszban adhatja meg a megfelelően formázott URL-címe a **bejelentkezési URL-cím**.
   * Az a [alkalmazást egy szerepkörhöz rendelhető](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) szakaszban az alkalmazás rendelhet a *olvasó* az erőforráscsoport a fürt szerepkört.

2. A Jenkins feladat, kattintson a **utáni műveletek** fülre.
3. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüből válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. 
4. A **Service Fabric fürtkonfiguráció**, jelölje be a **válassza ki a Service Fabric-fürt** választógombot. Kattintson a **Hozzáadás** melletti **Azure hitelesítő adatok**. Kattintson a **Jenkins** a Jenkins hitelesítő adatokat szolgáltató kiválasztásához.
5. Válassza ki a Jenkins hitelesítő adatokat szolgáltató **Microsoft Azure szolgáltatás egyszerű** a a **jellegű** legördülő.
6. A mentett beállításakor be az 1. lépésben a következő mezők beállítása egyszerű értékeket használja:

   * **Ügyfél-azonosító**: *Alkalmazásazonosító*
   * **Titkos Ügyfélkulcsot**: *alkalmazás kulcs*
   * **A bérlői azonosító**: *könyvtár-azonosítója*
   * **Előfizetés-azonosító**: *előfizetés-azonosító*
6. Adjon meg egy leíró **azonosító** Jenkins és rövid jelölje be a hitelesítő adatok használata **leírás**. Kattintson a **ellenőrizze a szolgáltatás egyszerű**. Ha az ellenőrzés sikeres, kattintson a **Hozzáadás**.

   ![Service Fabric Jenkins, írja be Azure hitelesítő adatait](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
7. Vissza a **Service Fabric fürtkonfiguráció**, győződjön meg arról, hogy az új hitelesítő adatok az van kiválasztva **Azure hitelesítő adatok**. 
8. Az a **erőforráscsoport** legördülő listából válassza ki az erőforráscsoportot, a fürt az alkalmazást telepíteni szeretné.
9. Az a **Service Fabric** legördülő listából válassza ki a fürtöt, az alkalmazást telepíteni szeretné.
10. A **Ügyfélkulcsot** és **ügyféltanúsítványt**, adja meg a PEM-fájl helyét a Jenkins tárolóban. Például: `/var/jenkins_home/clustercert.pem`. 
11. A **alkalmazás konfigurációja**, konfigurálja a **alkalmazásnév**, **alkalmazástípus**, (relatív) és **elérési útját Application Manifest** mezőket.
    ![Service Fabric Jenkins utáni művelet Azure hitelesítő adatok beállítása](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
12. Kattintson a **konfigurációjának ellenőrzése**. Kattintson az ellenőrzés sikeres **mentése**. A Jenkins feladat folyamat mostantól teljesen konfigurálva. Továbbra is a [további lépések](#next-steps) az üzemelő példány.

## <a name="next-steps"></a>További lépések
A GitHub és a Jenkins beállítása kész. Néhány változás a minta tervezhet a `reliable-services-actor-sample/Actors/ActorCounter` az elágazáshoz a tárház a projekt https://github.com/Azure-Samples/service-fabric-java-getting-started. Továbbítsa a módosításokat a távoli `master` ág (vagy bármely olyan fiókja, amelyek történő együttműködésre konfigurálva). Ez aktiválja a konfigurált `MyJob` Jenkins-feladatot. Lekéri a módosításokat a Githubból, épít fel őket, és központilag telepíti az alkalmazást a fürt megadott utáni műveleteket.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

