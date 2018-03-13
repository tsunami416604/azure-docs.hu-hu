---
title: "Folyamatos build és az integráció az Azure Service Fabric Linux alkalmazások Jenkins használatával |} Microsoft Docs"
description: "Folyamatos build és a Service Fabric Linux-alkalmazást Jenkins-integráció"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: saysa
ms.openlocfilehash: 00a6d04e7ec1da48ea17af4f17d3aba3cf4407d6
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Jenkins segítségével hozza létre, és a Linux-alkalmazások központi telepítése
A Jenkins egy népszerű eszköz az alkalmazások folyamatos integrációjához és üzembe helyezéséhez. Ebből a témakörből megtudhatja, hogyan helyezheti üzembe Azure Service Fabric-alkalmazásait a Jenkins használatával.

## <a name="general-prerequisites"></a>Általános előfeltételek
- A Git legyen telepítve a helyi számítógépen. A Git megfelelő verzióját [a Git letöltési oldaláról](https://git-scm.com/downloads) telepítheti a különböző operációs rendszerekhez. Ha még nem járatos a Git használatában, további információkért tekintse meg a [Git dokumentációját](https://git-scm.com/docs).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>A Jenkins beállítása egy Service Fabric-fürtben

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő szakaszok bemutatják, hogyan állítsa be a fürtben található Azure-tárfiók menteni az állapotot, a tároló-példány használata során.

### <a name="prerequisites"></a>Előfeltételek
1. Szükséges egy kész linuxos Service Fabric-fürt. Az Azure Portallal létrehozott Service Fabric-fürtökön már telepítve van a Docker. A fürt helyi futtatása esetén ellenőrizze a ``docker info`` paranccsal, hogy a Docker telepítve van-e. Ha nincs telepítve, telepítse a következő parancsokkal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ``` 

   > [!NOTE]
   > Győződjön meg arról, hogy a 8081 port van megadva egy egyéni végpont a fürtön.
   >

2. Az alkalmazás klónozása a következő lépések segítségével:
  ```sh
  git clone https://github.com/suhuruli/jenkins-container-application.git
  cd jenkins-container-application
  ```

3. A fájlmegosztás-tároló Jenkins állapot megőrzése:
  * Az Azure storage-fiók létrehozása a **ugyanabban a régióban** egy névvel, mint a fürt ``sfjenkinsstorage1``.
  * Hozzon létre egy **fájlmegosztás** alatt a tárolási fiók nevére, mint ``sfjenkins``.
  * Kattintson a **Connect** fájlmegosztási és Megjegyzés: az értékek megjeleníti a **Linux csatlakozó**, az érték az alábbihoz hasonlóan kell kinéznie:
  ```sh
  sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
  ```

  > [!NOTE]
  > Csatlakoztatási cifs megosztások meg kell rendelkeznie a cifs-utils csomag, a fürtcsomópontok telepítve.       
  >

4. Frissítse az helyőrző értékeket a ```setupentrypoint.sh``` parancsfájl az azure-tároló adatokkal a 3. lépéssel.
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
  * Cserélje le ``[REMOTE_FILE_SHARE_LOCATION]`` értékű ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins`` kimenetében a csatlakozás a fenti 3. lépés.
  * Cserélje le ``[FILE_SHARE_CONNECT_OPTIONS_STRING]`` értékű ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`` a fenti 3. lépés.

5. **Csak biztonságos fürt:** Jenkins biztonságos fürt konfigurálásához az alkalmazások telepítését, a tanúsítvány Jenkins tárolóban elérhetőnek kell lennie. Linux fürtökön a certificates(PEM) egyszerűen kerülnek át a tárolóból, a tároló X509StoreName által megadott. Az applicationmanifest jegyzékben ContainerHostPolicies alatt adja hozzá ezt a tanúsítványt hivatkozást, és frissítse az ujjlenyomat értéket. Az ujjlenyomat értékét kell lennie, hogy a csomóponton található, egy tanúsítvány.
  ```xml
  <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
  ```
  > [!NOTE]
  > Az ujjlenyomat értékét a biztonságos fürt való kapcsolódáshoz használt tanúsítványnak azonosnak kell lennie. 
  >

6. Csatlakozzon a fürthöz, és a tároló alkalmazás telepítéséhez.

  **Biztonságos fürt**
  ```sh
  sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
  bash Scripts/install.sh
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

### <a name="steps"></a>Lépések
1. Nyissa meg a ``http://PublicIPorFQDN:8081`` URL-címet a böngészőben. Így megkapja a bejelentkezéshez szükséges kezdeti rendszergazdai jelszó elérési útját. 
2. Tekintse meg a Service Fabric Explorer állapítható meg, melyik csomópontján fut a Jenkins tároló. Secure Shell (SSH) jelentkezzen be ezt a csomópontot.
```sh
ssh user@PublicIPorFQDN -p [port]
``` 
3. Kérje le a tároló példányazonosítóját a ``docker ps -a`` paranccsal.
4. Secure Shell-lel (SSH) jelentkezzen be a tárolóba, és illessze be a Jenkins portálon látott elérési utat. Például ha a portálon a `PATH_TO_INITIAL_ADMIN_PASSWORD` elérési út jelenik meg, futtassa a következőt:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  ```
  ```sh
  cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the pasword value
  ```
5. A Jenkins Gettting elindult a lapon választhatja ki a Select beépülő modulok telepítése lehetőséget, jelölje be a **nincs** jelölőnégyzetet, majd kattintson a telepítés.
6. Hozzon létre egy felhasználó vagy, rendszergazdaként a folytatáshoz válasszon

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>A Jenkins beállítása Service Fabric-fürtön kívül

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő szakaszok a fürtön kívüli beállítást mutatják be.

### <a name="prerequisites"></a>Előfeltételek
A Dockernek telepítve kell lennie. A következő parancsokkal telepítheti a Dockert a terminálról:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Így amikor a ``docker info`` fut a terminálon, a kimenetből látszódik, hogy a Docker szolgáltatás fut.

### <a name="steps"></a>Lépések
  1. A Service Fabric Jenkins tároló kép lekéréses: ``docker pull rapatchi/jenkins:v10``. Ez a rendszerkép előre telepített Service Fabric Jenkins beépülő modult tartalmaz.
  2. Futtassa a tároló rendszerképét: ``docker run -itd -p 8080:8080 rapatchi/jenkins:v10``
  3. Kérje le a tárolórendszerkép-példány azonosítóját. A ``docker ps –a`` paranccsal az összes Docker-tárolót listázhatja
  4. Jelentkezzen be a Jenkins portálra a következő lépésekkel:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Ha a tároló azonosítója 2d24a73b5964, használja a 2d24 értéket.
    * Ez a jelszó szükséges ahhoz, hogy bejelentkezzen a Jenkins-irányítópultra a ``http://<HOST-IP>:8080`` portálról.
    * Az első bejelentkezés után létrehozhatja saját felhasználói fiókját, ha a jövőben ezt kívánja használni, vagy maradhat a rendszergazdai fiók használata mellett. Ha létrehoz egy felhasználót, a továbbiakban azt kell használnia.
  5. Állítsa be a GitHubot a Jenkins használatához az [új SSH-kulcs létrehozásával és SSH-ügynökhöz adásával](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) foglalkozó cikkben található lépéseket követve.
        * A GitHub utasításait követve hozza létre az SSH-kulcsot, majd adja hozzá ahhoz a GitHub-fiókhoz, amelyen az adattár üzemel.
        * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén).
      * Ahhoz, hogy a saját gazdagépéről jelentkezhessen be a Jenkinsbe, használja a következő parancsokat:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Győződjön meg arról, hogy a fürt vagy gép, ahol a Jenkins-tároló rendszerképe fut, nyilvános IP-címmel rendelkezik. Ez lehetővé teszi, hogy a Jenkins-példány megkapja a GitHub értesítéseit.


## <a name="create-and-configure-a-jenkins-job"></a>Jenkins-feladatok létrehozása és konfigurálása

1. Hozzon létre **új elemet** az irányítópultról.
2. Adjon nevet az elemnek (pl. **MyJob**). Válassza a **free-style project** (szabad projekt) lehetőséget, majd kattintson az **OK** gombra.
3. Nyissa meg a feladat oldalát, majd kattintson a **Configure** (Konfigurálás) elemre.

   a. Általános területen jelölje be a **GitHub-projekt**, és adja meg a GitHub-projekt URL-címe. Ez az URL-cím üzemelteti azt a Service Fabric Java-alkalmazást, amelyet integrálni szeretne a Jenkins folyamatos integrációs és üzembe helyezési (CI/CD) folyamatával (például: ``https://github.com/sayantancs/SFJenkins``).

   b. A **Source Code Management** (Forráskódkezelés) szakaszban válassza a **Git** elemet. Adja meg annak az adattárnak az URL-címét, amely a Jenkins CI/CD folyamatával integrálni kívánt Service Fabric Java-alkalmazást tartalmazza (például: ``https://github.com/sayantancs/SFJenkins.git``). Itt adhatja meg azt is, hogy melyik ágat kívánja létrehozni, például: **/master**.
4. Adja meg a *GitHub* beállítását (amelyiken az adattár üzemel), így létrejöhet a kommunikáció a Jenkinsszel. Ehhez a következő lépések szükségesek:

   a. Nyissa meg a GitHub-adattár oldalát. Lépjen a **Settings** (Beállítások)  > **Integrations and Services** (Integrációk és szolgáltatások) részbe.

   b. Válassza az **Add Service** (Szolgáltatás hozzáadása) elemet, írja be a **Jenkins** kifejezést, majd válassza ki a **Jenkins-GitHub beépülő modult**.

   c. Adja meg a Jenkins-webhook URL-címét (alapértelmezés szerint ez a következő: ``http://<PublicIPorFQDN>:8081/github-webhook/``). Kattintson az **add/update service** (Szolgáltatás hozzáadása/frissítése) elemre.

   d. A Jenkins-példányra egy tesztesemény érkezik. A GitHubban egy zöld pipa jelenik meg a webhook mellett, és a projekt létrejön.

   e. A **Build Triggers** (Eseményindítók létrehozása) területen válassza ki a kívánt felépítési lehetőséget. Jelen esetben minden alkalommal szeretnénk elindítani egy felépítést, amikor valamilyen módosításra kerül sor az adattárban. Így a kiválasztandó lehetőség a következő: **GitHub hook trigger for GITScm polling** (GitHub beavatkozási pont eseményindító GITScm lekérdezés esetén). (Korábban ez **Build when a change is pushed to GitHub** (Felépítés módosítások GitHubon való közzétételekor) volt.)

   f. **Szakasz build Java-alkalmazások:** alatt a **szakasz Build**, a legördülő **Hozzáadás összeállítása lépés**, a beállításnak a **meghívása Gradle parancsfájl**. A widgetet részeként elérhető speciális menü megnyitásához, adja meg az elérési útját **legfelső szintű build script** az alkalmazáshoz. Felveszi a megadott elérési úton található build.gradle, és ennek megfelelően működik. Ha (az Eclipse beépülő modul vagy a Yeoman-generátor használatával) létrehozott projekt neve ``MyActor``, akkor a fő felépítési szkriptnek tartalmaznia kell a következőt: ``${WORKSPACE}/MyActor``. Tekintse meg erre példaként az alábbi képernyőképet:

    ![Service Fabric, Jenkins felépítési művelet][build-step]

   g. **Szakasz építése .net Core alkalmazások:** alatt a **szakasz Build**, a legördülő **Hozzáadás összeállítása lépés**, a beállításnak a **rendszerhéj hajtható végre**. A parancs mezőben, amely akkor jelenik meg a címtár először meg kell módosítani a build.sh fájl elérési útját. Ha a címtár módosult a build.sh parancsfájl futtatható, és fog létrehozni az alkalmazás.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

    Az alábbiakban van egy exmaple a parancsok, amelyek alapján állítja össze a [teljesítményszámláló szolgáltatást](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) CounterServiceApplication Jenkins feladat nevű minta.

    ![Service Fabric, Jenkins felépítési művelet][build-step-dotnet]
  
   h. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüből válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. Itt meg kell adnia annak a fürtnek az adatait, ahová a Jenkins által lefordított Service Fabric-alkalmazást üzembe kívánja helyezni. A tanúsítvány elérési útja az echo Certificates_JenkinsOnSF_Code_MyCert_PEM környezeti változó a tárolóban értékének echo találhatók. Ennek az elérési útnak az ügyfél és az ügyfél Cert mezők használható.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
    Az üzembe helyezéshez szükséges egyéb alkalmazásadatokat is megadhatja. Tekintse meg erre példaként az alábbi képernyőképet:

    ![Service Fabric, Jenkins felépítési művelet][post-build-step]

      > [!NOTE]
      > Ha a Service Fabricet használja a Jenkins-tároló rendszerképének üzembe helyezéséhez, a fürtnek meg kell egyeznie azzal, ahol a Jenkins-tárolóalkalmazás található.
      >

## <a name="next-steps"></a>További lépések
A GitHub és a Jenkins beállítása kész. Érdemes lehet elvégezni néhány mintamódosítást a ``MyActor`` projekten az adattárban (https://github.com/sayantancs/SFJenkins). A módosításokat továbbíthatja egy távoli ``master`` ágra (vagy bármilyen egyéb olyan ágra, amelyet beállított). Ez aktiválja a konfigurált ``MyJob`` Jenkins-feladatot. A feladat lekéri a módosításokat a GitHubról, érvénybe lépteti őket, és üzembe helyezi az alkalmazást a fürt azon végpontján, amelyet a felépítés utáni műveletekben adott meg.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-step.png

