---
title: "A linuxos Java-alkalmazás folyamatos felépítése és üzembe helyezése a Jenkins használatával | Microsoft Docs"
description: "A linuxos Java-alkalmazás folyamatos felépítése és üzembe helyezése a Jenkins használatával"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: raunakp
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: sayantancs
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: d3c64e3ed78a5379592b5e4276c83d26f04d2e1c
ms.lasthandoff: 03/08/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>A linuxos Java-alkalmazás felépítése és üzembe helyezése a Jenkins használatával
A Jenkins népszerű eszköz a folyamatos integrációhoz és üzembe helyezéshez. Ez a dokumentum ismerteti azokat a lépéseket, amelyek a Service Fabric-alkalmazások felépítéséhez és üzembe helyezéséhez szükségesek a Jenkins használatával.

## <a name="general-prerequisites"></a>Általános előfeltételek
1. A Gitnek telepítve kell lennie a helyi számítógépen. A Git megfelelő verziója [itt](https://git-scm.com/downloads) érhető el a különböző operációs rendszerekhez. Ha még nem ismeri a Git működését, a [dokumentumok](https://git-scm.com/docs) között minden szükséges információt megtalál.
2. Szüksége lesz a Jenkins Service Fabric beépülő moduljára is. A Jenkins Service Fabric beépülő modulját [itt](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi) lehet letölteni.

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>A Jenkins beállítása egy Service Fabric-fürtben

### <a name="prerequisites"></a>Előfeltételek
1. Szükséges egy kész linuxos Service Fabric-fürt. Az Azure Portallal létrehozott Service Fabric-fürtökön már telepítve van a Docker. A helyi fürt futtatásakor ellenőriznie kell, hogy a Docker telepítve van-e, amit a ``docker info`` paranccsal tehet meg. Ha nincs, telepítse a következővel:
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
2. Helyezze üzembe a Service Fabric tárolóalkalmazását a következő fürtön: ``http://PublicIPorFQDN:19080``. Ha szükséges, kövesse az alábbi lépéseket:
```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker && cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080 # Azure CLI cluster connect command
bash Scripts/install.sh
```
  Ezzel telepítheti a Jenkins-tárolót a csatlakoztatott fürthöz. A tárolóalkalmazás állapotát a Service Fabric Explorer használatával követheti nyomon.

### <a name="steps"></a>Lépések
1. Nyissa meg a ``http://PublicIPorFQDN:8081`` URL-címet a böngészőben. Így megkapja a bejelentkezéshez szükséges kezdeti rendszergazdai jelszó elérési útját. Miután bejelentkezett a kezdeti rendszergazdai fiókkal, továbbra is használhatja rendszergazdaként a Jenkinst, de létrehozhat új felhasználót is.
> [!NOTE]
> Ügyeljen rá, hogy a fürt létrehozásakor 8081-es portot adja meg az alkalmazás végponti portjaként.
>

2. Kérje le a tároló példányazonosítóját a ``docker ps -a`` használatával.
3. SSH-val jelentkezzen be a tárolóba a Jenkins portálon látott elérési út használatával. Például ha a portálon a `PATH_TO_INITIAL_ADMIN_PASSWORD` elérési út jelenik meg, Ön a következőképpen járhat el:  
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash # This takes you inside docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Állítsa be a GitHubot a Jenkins használatához [ezzel](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * A GitHub utasításait követve hozza létre az SSH-kulcsot, majd adja hozzá ahhoz a GitHub-fiókhoz, amelyiken az adattár üzemel (majd).
    * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén)
    * Ahhoz, hogy a saját gazdagépéről bejelentkezhessen a Jenkins felületére, a következőt kell tennie:
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>A Jenkins beállítása Service Fabric-fürtön kívül

### <a name="prerequisites"></a>Előfeltételek
1. A Dockernek telepítve kell lennie. Ha ez a feltétel nem teljesül, írja be a következő parancsokat a terminálba a Docker telepítéséhez.
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
Így amikor a ``docker info`` fut a terminálon, a kimenetből látszódik, hogy a Docker szolgáltatás fut.

### <a name="steps"></a>Lépések
  1. Kérje le a Service Fabric Jenkins-tárolóját: ``docker pull IMAGE_NAME ``
  2. Futtassa a tároló rendszerképét: ``docker run -itd -p 8080:8080 IMAGE_NAME``
  3. Kérje le annak a Docker-tárolónak az azonosítóját, amely a Jenkins-rendszerképet tartalmazza (azt, amelyet épp most telepített). A ``docker ps –a`` paranccsal az összes Docker-tárolót listázhatja
  4. Kérje le a Jenkins-fiók rendszergazdai jelszavát. Ehhez a következőt teheti:
    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Ha tehát a tároló azonosítója 2d24a73b5964, csak a 2d24 értéket kell beszúrnia
    * Ez a jelszó szükséges ahhoz, hogy bejelentkezzen a Jenkins-irányítópultra a portálról, amely a következő: ``http://<HOST-IP>:8080``
    * Az első bejelentkezéskor létrehozhatja saját felhasználói fiókját, ha a jövőben ezt kívánja használni, vagy maradhat a rendszergazdai fiók használata mellett. Ha új felhasználót hoz létre, a továbbiakban azt kell használnia.
  5. Állítsa be a GitHubot a Jenkins használatához [ezzel](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
      * A GitHub utasításait követve hozza létre az SSH-kulcsot, majd adja hozzá ahhoz a GitHub-fiókhoz, amelyiken az adattár üzemel (majd).
      * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén)
      * Ahhoz, hogy a saját gazdagépéről bejelentkezhessen a Jenkins felületére, a következőt kell tennie:
    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

> [!NOTE]
> Győződjön meg róla, hogy a fürt/gép, ahol a Jenkins-tároló rendszerképe fut, nyilvános IP-címmel rendelkezik, és így a Jenkins-példány megkapja a GitHub értesítéseit.
>

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>A Jenkins Service Fabric beépülő moduljának telepítése a portálon keresztül

1. Nyissa meg a következőt: ``http://PublicIPorFQDN:8081``
2. A Jenkins irányítópultján válassza ki a következőt: ``Manage Jenkins`` -> ``Manage Plugins`` -> ``Advanced``.
Itt feltöltheti a beépülő modult. Válassza a ``Choose file`` lehetőséget, majd a serviceFabric.hpi fájlt, amelyet az Előfeltételek szakaszban már letöltött. Ha a feltöltésre kattint, a Jenkins automatikusan telepíti a beépülő modult. Ha a rendszer újraindítást kér, engedélyezze.

## <a name="creating-and-configuring-a-jenkins-job"></a>Jenkins-feladat létrehozása és konfigurálása

1. Hozzon létre egy ``new item`` elemet az irányítópultból
2. Adjon nevet az új elemnek – például: ``MyJob``, válassza a „free-style project” (szabad projekt) lehetőséget, majd kattintson az OK gombra
3. Ezután nyissa meg a feladat oldalát, majd kattintson a következőre: ``Configure`` -
  * Az általános beállítások ``Github project`` területén adja meg a GitHub-projekt URL-címét, amely a Jenkins CI/CD folyamatával integrálni kívánt Service Fabric Java-alkalmazást tartalmazza (például: ``https://github.com/sayantancs/SFJenkins``).
  * A ``Source Code Management`` területen válassza a ``Git`` lehetőséget. Adja annak az adattárnak az URL-címét, amely a Jenkins CI/CD folyamatával integrálni kívánt Service Fabric Java-alkalmazást tartalmazza (például: ``https://github.com/sayantancs/SFJenkins.git``). Itt adhatja meg azt is, hogy melyik ágat kívánja létrehozni, például: ``*/master``.
4. Adja meg a *Github* beállítását (vagyis hogy melyiken üzemeljen az adattár), így létrejöhet a kommunikáció a Jenkinsszel. Ehhez a következő lépések szükségesek:
  - Nyissa meg a GitHub-adattár oldalát. Nyissa meg a következőt: ``Settings`` -> ``Integrations and Services``.
  - Válassza ki az ``Add Service`` elemet, írja be a Jenkins kifejezést, majd válassza ki a ``Jenkins-Github plugin`` elemet.
  - Adja meg a Jenkins-webhook URL-címét (alapértelmezés szerint ez a következő: ``http://<PublicIPorFQDN>:8081/github-webhook/``). Kattintson a Szolgáltatás hozzáadása/frissítése elemre.
  - A Jenkins-példányra egy tesztesemény érkezik. A GitHubban egy zöld pipa jelenik meg a webhook mellett, és a projekt létrejön.
  - A ``Build Triggers`` területen válassza ki a kívánt felépítési lehetőséget – jelen esetben minden olyan alkalommal szeretnénk elindítani a felépítést, amikor valamilyen módosítás történik az adattárban. Így a megfelelő lehetőség a következő: ``GitHub hook trigger for GITScm polling`` (korábban ez „Build when a change is pushed to GitHub” volt)
  - A ``Build section`` területen válassza ki az ``Invoke Gradle Script`` lehetőséget az ``Add build step`` legördülő menüből. A megjelenő widgeten adja meg a ``Root build script`` elérési utat az alkalmazásához. A rendszer felveszi a build.gradle elemet a megadott elérési útból, és annak megfelelően működik. Vegye figyelembe, hogy ha (az Eclipse beépülő modul vagy a Yeoman generátor használatával) létrehozott projekt neve ``MyActor``, akkor a fő felépítési szkriptnek tartalmaznia kell a következőt: ``${WORKSPACE}/MyActor``. Íme egy példa arra, hogy ez a szakasz általában hogyan néz ki:

    ![Service Fabric, Jenkins felépítési művelet][build-step]
  - A ``Post-Build Actions`` legördülő menüből válassza ki a ``Deploy Service Fabric Project`` lehetőséget. Itt meg kell adnia annak a fürtnek az adatait, ahová a Jenkins által lefordított Service Fabric-alkalmazást üzembe kívánja helyezni, illetve az üzembe helyezéshez szükséges egyéb alkalmazásadatokat. Az alábbi képernyőkép referenciaként használható:

    ![Service Fabric, Jenkins felépítési művelet][post-build-step]

 >[!Note]
 > Ha a Service Fabricet használja a Jenkins-tároló rendszerképének üzembe helyezéséhez, a fürtnek meg kell egyeznie azzal, ahol a Jenkins-tárolóalkalmazás található.
 >

### <a name="end-to-end-scenario"></a>Végpontok közötti forgatókönyv
A GitHub és a Jenkins beállítása sikeresen megtörtént, és most már elvégezhet néhány mintamódosítást a ``MyActor`` projekten az adattárban – pl. https://github.com/sayantancs/SFJenkins – és a módosításokat továbbíthatja egy távoli ``master`` ágra (vagy bármilyen egyéb olyan ágra, amelyet beállított). Ez aktiválja a konfigurált ``MyJob`` Jenkins-feladatot. A feladat lekéri a módosításokat a GitHubról, alkalmazza őket, és üzembe helyezi az alkalmazást a fürt azon végpontján, amelyet Ön a felépítés utáni műveletekben adott meg.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

