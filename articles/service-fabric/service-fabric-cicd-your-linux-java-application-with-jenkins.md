---
title: "Az Azure Service Fabric linuxos Java-alkalmazás folyamatos felépítése és integrálása a Jenkins használatával | Microsoft Docs"
description: "A linuxos Java-alkalmazás folyamatos felépítése és integrálása a Jenkins használatával"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 335f41e2e4a40e64e87382ea338673de3ab79c27
ms.lasthandoff: 03/14/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>A linuxos Java-alkalmazás felépítése és üzembe helyezése a Jenkins használatával
A Jenkins népszerű eszköz a folyamatos integrációhoz és üzembe helyezéshez. Ez a dokumentum ismerteti azokat a lépéseket, amelyek a Service Fabric-alkalmazások felépítéséhez és üzembe helyezéséhez szükségesek a Jenkins használatával.

## <a name="general-prerequisites"></a>Általános előfeltételek
1. A Git legyen telepítve a helyi számítógépen. A Git megfelelő verziója [itt](https://git-scm.com/downloads) érhető el a különböző operációs rendszerekhez. Ha még nem ismeri a Git működését, a [dokumentumok](https://git-scm.com/docs) között minden szükséges információt megtalál.
2. Szüksége lesz a Jenkins Service Fabric beépülő moduljára is. A Jenkins Service Fabric beépülő modulját [itt](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi) lehet letölteni.

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>A Jenkins beállítása egy Service Fabric-fürtben

### <a name="prerequisites"></a>Előfeltételek
1. Szükséges egy kész linuxos Service Fabric-fürt. Az Azure Portallal létrehozott Service Fabric-fürtökön már telepítve van a Docker. A fürt helyi futtatása esetén ellenőrizze a ``docker info`` paranccsal, hogy a Docker telepítve van-e. Ha nincs telepítve, telepítse a következő parancsokkal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Helyezze üzembe a Service Fabric tárolóalkalmazását a fürtön a következő lépésekkel:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
Ezzel telepíti a fürtön a Jenkins-tárolót, amely a Service Fabric Explorerrel figyelhető.

### <a name="steps"></a>Lépések
1. Nyissa meg a ``http://PublicIPorFQDN:8081`` URL-címet a böngészőben. Így megkapja a bejelentkezéshez szükséges kezdeti rendszergazdai jelszó elérési útját. Miután bejelentkezett a kezdeti rendszergazdai fiókkal, továbbra is használhatja rendszergazdaként a Jenkinst, de létrehozhat új felhasználót is.

  > [!NOTE]
  > Ügyeljen rá, hogy a fürt létrehozásakor 8081-es portot adja meg az alkalmazás végponti portjaként.
  >

2. Kérje le a tároló példányazonosítóját a ``docker ps -a`` használatával.
3. SSH-val jelentkezzen be a tárolóba a Jenkins portálon látott elérési út beillesztésével. Például ha a portálon a `PATH_TO_INITIAL_ADMIN_PASSWORD` elérési út jelenik meg, Ön a következőképpen járhat el:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Állítsa be a GitHubot a Jenkins használatához a [hivatkozásban](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) említett lépéseket követve.
    * A GitHub utasításait követve hozza létre az SSH-kulcsot, majd adja hozzá ahhoz a GitHub-fiókhoz, amelyen az adattár üzemel.
    * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén)
    * Ahhoz, hogy a saját gazdagépéről jelentkezhessen be a Jenkins felületére, használja a következő parancsot:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>A Jenkins beállítása Service Fabric-fürtön kívül

### <a name="prerequisites"></a>Előfeltételek
A Dockernek telepítve kell lennie. A következő parancsokkal telepítheti a Dockert a terminálról:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Így amikor a ``docker info`` fut a terminálon, a kimenetből látszódik, hogy a Docker szolgáltatás fut.

### <a name="steps"></a>Lépések
  1. Kérje le a Service Fabric Jenkins-tárolójának rendszerképét: ``docker pull servicefabric-microsoft.azurecr.io/jenkins:v1``
  2. Futtassa a tároló rendszerképét: ``docker run -itd -p 8080:8080 servicefabric-microsoft.azurecr.io/jenkins:v1``
  3. Kérje le a tárolórendszerkép-példány azonosítóját. A ``docker ps –a`` paranccsal az összes Docker-tárolót listázhatja
  4. Jelentkezzen be a Jenkins portálra a következő lépésekkel:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Ha a tároló azonosítója 2d24a73b5964, használja a 2d24 értéket.
    * Ez a jelszó szükséges ahhoz, hogy bejelentkezzen a Jenkins-irányítópultra a portálról, amely a következő: ``http://<HOST-IP>:8080``
    * Az első bejelentkezéskor létrehozhatja saját felhasználói fiókját, ha a jövőben ezt kívánja használni, vagy maradhat a rendszergazdai fiók használata mellett. Ha felhasználót hoz létre, a továbbiakban azt kell használnia.
  5. Állítsa be a GitHubot a Jenkins használatához a [hivatkozásban](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) említett lépéseket követve.
      * A GitHub utasításait követve hozza létre az SSH-kulcsot, majd adja hozzá ahhoz a GitHub-fiókhoz, amelyiken az adattár üzemel (majd).
      * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén)
      * Ahhoz, hogy a saját gazdagépéről bejelentkezhessen a Jenkins felületére, használja a következő parancsokat:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

    > [!NOTE]
    > Győződjön meg róla, hogy a fürt/gép, ahol a Jenkins-tároló rendszerképe fut, nyilvános IP-címmel rendelkezik, és így a Jenkins-példány megkapja a GitHub értesítéseit.
    >

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>A Jenkins Service Fabric beépülő moduljának telepítése a portálon keresztül

1. Nyissa meg a következőt: ``http://PublicIPorFQDN:8081``
2. A Jenkins irányítópultján válassza ki a következőt: **Manage Jenkins** (Jenkins kezelése)  -> **Manage Plugins** (Beépülő modulok kezelése)  -> **Advanced** (Speciális).
Itt feltöltheti a beépülő modult. Válassza a **Choose file** (Fájl kiválasztása) lehetőséget, majd a serviceFabric.hpi fájlt, amelyet az előfeltételek szakaszban már letöltött. Ha a feltöltésre kattint, a Jenkins automatikusan telepíti a beépülő modult. Ha a rendszer kéri, engedélyezze az újraindítást.

## <a name="creating-and-configuring-a-jenkins-job"></a>Jenkins-feladat létrehozása és konfigurálása

1. **Új elem** létrehozása az irányítópultról
2. Adjon nevet az elemnek – például: **MyJob**, válassza a „free-style project” (szabad projekt) lehetőséget, majd kattintson az OK gombra
3. Ezután nyissa meg a feladat oldalát, majd kattintson a **Configure** - (Konfigurálás) elemre
  * Az általános beállítások **Github project** (GitHub-projekt) területén adja meg azon GitHub-projekt URL-címét, amely a Jenkins CI/CD folyamatával integrálni kívánt Service Fabric Java-alkalmazást tartalmazza (például: ``https://github.com/sayantancs/SFJenkins``).
  * A **Source Code Management** (Forráskódkezelés) szakaszban válassza a **Git** elemet. Adja annak az adattárnak az URL-címét, amely a Jenkins CI/CD folyamatával integrálni kívánt Service Fabric Java-alkalmazást tartalmazza (például: ``https://github.com/sayantancs/SFJenkins.git``). Itt adhatja meg azt is, hogy melyik ágat kívánja létrehozni, például: ***/master**.
4. Adja meg a *Github* beállítását (vagyis hogy melyiken üzemeljen az adattár), így létrejöhet a kommunikáció a Jenkinsszel. Ehhez a következő lépések szükségesek:
  1. Nyissa meg a GitHub-adattár oldalát. Lépjen a **Settings** (Beállítások)  -> **Integrations and Services** (Integrációk és szolgáltatások) részbe.
  2. Válassza az **Add Service** (Szolgáltatás hozzáadása) elemet, írja be a Jenkins kifejezést, majd válassza ki a **Jenkins-GitHub beépülő modult**.
  3. Adja meg a Jenkins-webhook URL-címét (alapértelmezés szerint ez a következő: ``http://<PublicIPorFQDN>:8081/github-webhook/``). Kattintson az Add/update service (Szolgáltatás hozzáadása/frissítése) elemre.
  4. A Jenkins-példányra egy tesztesemény érkezik. A GitHubban egy zöld pipa jelenik meg a webhook mellett, és a projekt létrejön.
  5. A **Build Triggers** (Eseményindítók létrehozása) területen válassza ki a kívánt felépítési lehetőséget – jelen esetben minden olyan alkalommal szeretnénk elindítani a felépítést, amikor valamilyen módosítás történik az adattárban. Így a megfelelő lehetőség a következő: **GitHub hook trigger for GITScm polling** (GitHub beavatkozási pont eseményindító GITScm lekérdezés esetén) (korábban ez Build when a change is pushed to GitHub (Felépítés módosítások a GitHubon való közzétételekor) volt)
  6. A **Build** (Felépítés) szakaszban az **Add build step** (Felépítési lépés hozzáadása) legördülő listából válassza az **Invoke Gradle Script** (Gradle szkript meghívása) lehetőséget. A megjelenő widgeten adja meg a **fő felépítési szkript** elérési útját az alkalmazásához. A rendszer felveszi a build.gradle elemet a megadott elérési útból, és annak megfelelően működik. Ha (az Eclipse beépülő modul vagy a Yeoman-generátor használatával) létrehozott projekt neve ``MyActor``, akkor a fő felépítési szkriptnek tartalmaznia kell a következőt: ``${WORKSPACE}/MyActor``. Íme egy példa arra, hogy ez a szakasz általában hogyan néz ki:

    ![Service Fabric, Jenkins felépítési művelet][build-step]
  7. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüben válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. Itt meg kell adnia annak a fürtnek az adatait, ahová a Jenkins által lefordított Service Fabric-alkalmazást üzembe kívánja helyezni, illetve az üzembe helyezéshez szükséges egyéb alkalmazásadatokat. Az alábbi képernyőkép referenciaként használható:

    ![Service Fabric, Jenkins felépítési művelet][post-build-step]

 > [!NOTE]
 > Ha a Service Fabricet használja a Jenkins-tároló rendszerképének üzembe helyezéséhez, a fürtnek meg kell egyeznie azzal, ahol a Jenkins-tárolóalkalmazás található.
 >

### <a name="end-to-end-scenario"></a>Végpontok közötti forgatókönyv
A GitHub és a Jenkins beállítása sikeresen megtörtént, és most már elvégezhet néhány mintamódosítást a ``MyActor`` projekten az adattárban – pl. https://github.com/sayantancs/SFJenkins – és a módosításokat továbbíthatja egy távoli ``master`` ágra (vagy bármilyen egyéb olyan ágra, amelyet beállított). Ez aktiválja a konfigurált ``MyJob`` Jenkins-feladatot. A feladat lekéri a módosításokat a GitHubról, alkalmazza őket, és üzembe helyezi az alkalmazást a fürt azon végpontján, amelyet Ön a felépítés utáni műveletekben adott meg.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

