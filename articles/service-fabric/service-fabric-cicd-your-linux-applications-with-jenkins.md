---
title: Folyamatos felépítése és integrálása a Jenkins használatával az Azure Service Fabric Linux-alkalmazások |} A Microsoft Docs
description: Folyamatos felépítése és integrálása a Jenkins használatával Linux-alapú Service Fabric-alkalmazás
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: saysa
ms.openlocfilehash: 7abc15264a44c969f57071e84ffcedca30d326fb
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766316"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>A Jenkins használata a fejleszthetők és helyezhetők üzembe Linuxos alkalmazások
A Jenkins egy népszerű eszköz az alkalmazások folyamatos integrációjához és üzembe helyezéséhez. Ebből a témakörből megtudhatja, hogyan helyezheti üzembe Azure Service Fabric-alkalmazásait a Jenkins használatával.

## <a name="topic-overview"></a>A témakör áttekintés
Ez a cikk ismerteti a Jenkins-környezet beállítása több lehetséges módjait, valamint a különböző módon helyezheti üzembe a Service Fabric-fürtön alkalmazását követően állították össze. Kövesse az alábbi általános lépéseket sikeresen a Jenkins beállítása, kérje le a módosításokat a Githubról, az alkalmazás létrehozása és üzembe helyezése a fürtön:

1. Győződjön meg arról, hogy telepítse a [Előfeltételek](#prerequisites).
1. Ezután kövesse az ezekben a szakaszokban a Jenkins beállítása egy lépéseit:
   * [A Jenkins beállítása egy Service Fabric-fürtben](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [A Jenkins beállítása Service Fabric-fürtön kívül](#set-up-jenkins-outside-a-service-fabric-cluster), vagy
   * [A Service Fabric beépülő modul telepítéséhez egy már létező környezetben Jenkins](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. A Jenkins beállítása után kövesse a [létrehozása és konfigurálása a Jenkins-feladat](#create-and-configure-a-jenkins-job) állítsa be a Githubot a Jenkins eseményindító végrehajtott módosítások az alkalmazás és a Jenkins projekt folyamatot lépést, amely lekéréses konfigurálása a a GitHub változik, és az alkalmazás létrehozása. 
1. Végül állítsa be a Jenkins feladat utáni lépést, amely az alkalmazás a Service Fabric-fürt üzembe helyezéséhez. Az üzembe helyezése egy fürtön a Jenkins konfigurálása két módja van:    
   * A fejlesztési-tesztelési környezetet használja [konfigurálhatja a telepítést, használja a fürt felügyeleti végpontja](#configure-deployment-using-cluster-management-endpoint). Ez az a legegyszerűbb módszer beállítása.
   * Használja az éles környezetekhez, [konfigurálhatja a telepítést, az Azure-hitelesítőadatokkal](#configure-deployment-using-azure-credentials). A Microsoft javasolja ezt a módszert, az éles környezetekhez, mert az Azure-beli hitelesítő korlátozhatja a hozzáférést, amely rendelkezik egy Jenkins-feladatot az Azure-erőforrások. 

## <a name="prerequisites"></a>Előfeltételek

- Ellenőrizze, hogy Git helyileg van telepítve. A Git megfelelő verzióját telepítheti [a Git letöltési oldaláról](https://git-scm.com/downloads) az operációs rendszer alapján. Ha most ismerkedik a Git, ismerje meg, további információkért a [a Git dokumentációja](https://git-scm.com/docs).
- Ez a cikk a *Service Fabric első lépései mintául szolgáló* a Githubon: [ https://github.com/Azure-Samples/service-fabric-java-getting-started ](https://github.com/Azure-Samples/service-fabric-java-getting-started) az alkalmazás létrehozása és üzembe helyezése. Hogy követni tudja a tárház elágaztatása, de néhány módosítással utasításokat használja saját GitHub-projekt.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Egy meglévő környezetben a Jenkins Service Fabric beépülő modul telepítése
A Service Fabric beépülő modul hozzáadása egy meglévő Jenkins környezetbe, a következők szükségesek:

- A [Service Fabric parancssori felület](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Ügyeljen arra, hogy rendszer a CLI telepítése, nem pedig felhasználói szinten, ezért a Jenkins is CLI-parancsokkal. 
   >

- Java-alkalmazások üzembe helyezéséhez alkalmazást is telepíteni [gradle-t és az Open JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development). 
- Telepítse a .NET Core 2.0-alkalmazások üzembe helyezéséhez a [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Ha már telepítette a saját környezetéhez szükséges előfeltételeket, keresse meg az Azure Service Fabric beépülő modul a Jenkins Marketplace-en, és telepítse.

A beépülő modul telepítése után folytassa a [létrehozása és konfigurálása a Jenkins-feladat](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>A Jenkins beállítása egy Service Fabric-fürtben

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő szakaszok bemutatják, hogyan állítsa be egy fürtben menteni az állapotot tároló-példány az Azure storage-fiók használata során.

### <a name="prerequisites"></a>Előfeltételek
- Service Fabric Linux-fürt rendelkezik a Docker telepítve van. Azure-ban már futó Service Fabric-fürtök a dockernek telepítve. Ha a fürt helyi (beépített fejlesztési környezet) futtatja, ellenőrizze, ha a Docker telepítve van-e a gépén a `docker info` parancsot. Ha nincs telepítve, telepítse a következő parancsokat:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Győződjön meg arról, hogy a fürt egyéni végpontok a 8081-es port van megadva. Ha a helyi fürtöt használ, ellenőrizze, hogy a 8081-es portot a gazdagépen nyitva-e, és arról, hogy vannak-e egy nyilvános IP-címet.
   >

### <a name="steps"></a>Lépések
1. Az alkalmazás klónozása a következő parancsokat:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. A Jenkins-tárolót egy fájlmegosztásban állapotának megőrzése:
   1. Az Azure storage-fiók létrehozása a **ugyanabban a régióban** egy névvel, mint például a fürt `sfjenkinsstorage1`.
   1. Hozzon létre egy **fájlmegosztás** alatt a storage-fiók nevére például `sfjenkins`.
   1. Kattintson a **Connect** a fájlmegosztás és megjegyzés az értékeket jeleníti meg a **csatlakozás linuxról**, az érték az alábbihoz hasonlóan kell kinéznie:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > A csatlakoztatási cifs shares szüksége lesz a cifs-utils csomag, a fürtcsomópontokon telepített.      
   >

1. A helyőrző értékeket az frissítése a `setupentrypoint.sh` szkriptet az azure-storage-adatokkal 2. lépés.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Cserélje le `[REMOTE_FILE_SHARE_LOCATION]` értékkel `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` a csatlakozás a kimenetéből származó a fenti 2. lépésben.
   * Cserélje le `[FILE_SHARE_CONNECT_OPTIONS_STRING]` értékkel `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` a fenti 2. lépésben.

1. **Csak a fürt védelme:** 
   
   Az alkalmazások telepítését a Jenkins egy biztonságos fürt konfigurálásához a fürttanúsítványnak a Jenkins-tároló elérhetőnek kell lennie. Az a *ApplicationManifest.xml* a fájlt a **ContainerHostPolicies** címke hozzáadása a tanúsítvány hivatkozást, és frissítse az ujjlenyomat értéket, amely a fürt tanúsítvány.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Ezenkívül hozzáadása a csoportban a következő sorokat a **ApplicationManifest** (root) címkét a *ApplicationManifest.xml* fájlt, és frissítse a fürt tanúsítvány, amely az ujjlenyomat értéket.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Csatlakozzon a fürthöz, és a tároló alkalmazás telepítéséhez.

   **Fürt biztonságossá tétele**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Az előző parancs a tanúsítvány PEM formátumú vesz igénybe. Ha a tanúsítvány PFX formátumban, az alábbi parancs segítségével átalakíthatja. Ha a PFX-fájl nem jelszóval védett, adja meg a **passin** paraméterrel `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **A nem biztonságos fürtökhöz**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Ezzel telepít a fürtön egy Jenkins-tárolót, amely a Service Fabric Explorerrel figyelhető meg.

   > [!NOTE]
   > Eltarthat néhány percig, le kell tölteni a fürtön a Jenkins kép.
   >

1. Nyissa meg a `http://PublicIPorFQDN:8081` URL-címet a böngészőben. Így megkapja a bejelentkezéshez szükséges kezdeti rendszergazdai jelszó elérési útját. 
1. Tekintse meg a Service Fabric Explorer állapítható meg, melyik csomópontján fut a Jenkins-tárolót. Secure Shell (SSH) jelentkezzen be ezen a csomóponton.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Kérje le a tároló példányazonosítóját a `docker ps -a` paranccsal.
1. Secure Shell (SSH) jelentkezzen be a tárolóba, és illessze be a Jenkins portálon látott elérési útja. Például, ha a portálon jelenik meg az elérési út `PATH_TO_INITIAL_ADMIN_PASSWORD`, futtassa a következő parancsokat:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. A Jenkins kezdeti lépések a lapon választhatja ki a Select beépülő modulok telepítése lehetőséget, jelölje be a **nincs** jelölőnégyzetet, majd kattintson a telepítés.
1. Hozzon létre egy felhasználót vagy rendszergazdaként. a folytatáshoz válasszon

Miután beállította a Jenkins, folytassa a [létrehozása és konfigurálása a Jenkins-feladat](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>A Jenkins beállítása Service Fabric-fürtön kívül

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő szakaszok a fürtön kívüli beállítást mutatják be.

### <a name="prerequisites"></a>Előfeltételek
- Győződjön meg arról, hogy a Docker telepítve van-e a gépén. A következő parancsokkal telepítheti a Dockert a terminálról:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Futtatásakor `docker info` billentyűparancsot a terminálon, a kimeneti jelenítsen meg, hogy a Docker szolgáltatás fut.

### <a name="steps"></a>Lépések
1. Kérje le a Service Fabric Jenkins-tárolójának rendszerképét: `docker pull rapatchi/jenkins:latest`. A rendszerképhez előre telepítve van a Service Fabric Jenkins beépülő modulja.
1. Futtassa a tároló rendszerképét: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Kérje le a tárolórendszerkép-példány azonosítóját. A `docker ps –a` paranccsal az összes Docker-tárolót listázhatja
1. Jelentkezzen be a Jenkins portálra a következő lépéseket:

   1. Jelentkezzen be a Jenkins felületére, a gazdagépről. Használja az első négy számjegy, a tároló azonosítója. Például, ha a tárolót azonosító `2d24a73b5964`, használjon `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. A Jenkins felületére, a rendszergazdai jelszó lekérése a tárolópéldány:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Jelentkezzen be a Jenkins-irányítópultra, nyissa meg a következő URL-címet egy webböngészőben: `http://<HOST-IP>:8080`. Az előző lépésben a jelszó használatával Jenkins zárolásának feloldása.
   1. (Választható.) Első bejelentkezés után létrehozhatja saját felhasználói fiókját, és használja, amely az alábbi lépéseket, vagy továbbra is rendszergazdai fiókot használja. Ha felhasználót hoz létre, az adott felhasználóhoz továbbra is szeretné.
1. Állítsa be a Githubot a Jenkins a lépéseket követve [új SSH-kulcs létrehozásával és SSH-ügynökhöz adásával](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * A GitHub utasításait követve hozza létre az SSH-kulcsot, majd adja hozzá ahhoz a GitHub-fiókhoz, amelyen az adattár üzemel.
   * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén).
   * Ahhoz, hogy a saját gazdagépéről jelentkezhessen be a Jenkinsbe, használja a következő parancsot:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Győződjön meg arról, hogy a fürt vagy gép, ahol a Jenkins-tárolójának rendszerképét üzemeltetett nyilvános IP-címmel rendelkezik. Ez lehetővé teszi, hogy a Jenkins-példány megkapja a GitHub értesítéseit.

Miután beállította a Jenkins, folytassa a következő szakaszban [létrehozása és konfigurálása a Jenkins-feladat](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins-feladatok létrehozása és konfigurálása

A jelen szakaszban ismertetett lépések bemutatják, hogyan konfigurálhatja a Jenkins-feladatot reagálni az igények változásaira, a GitHub-tárházba, beolvassa a módosításokat és hozhatóak létre. Ez a szakasz végén, van irányítva az utolsó lépéseit alapján, hogy telepít egy fejlesztési-tesztelési környezet vagy éles környezetben az alkalmazás központi telepítése a feladat konfigurálása. 

1. A Jenkins irányítópultján kattintson **új elem**.
1. Adjon nevet az elemnek (pl. **MyJob**). Válassza a **free-style project** (szabad projekt) lehetőséget, majd kattintson az **OK** gombra.
1. A feladat konfigurálása lapon nyílik meg. (A konfiguráció lekérése a Jenkins-irányítópultra, kattintson a feladat, és kattintson **konfigurálása**).

1. Az a **általános** lapra, jelölje be a **GitHub-projekt**, és adja meg a GitHub-projekt URL-CÍMÉT. Ez az URL-cím üzemelteti azt a Service Fabric Java-alkalmazást, amelyet integrálni szeretne a Jenkins folyamatos integrációs és üzembe helyezési (CI/CD) folyamatával (például: `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Az a **Source Code Management** lapon jelölje be **Git**. Adja meg annak az adattárnak az URL-címét, amely a Jenkins CI/CD folyamatával integrálni kívánt Service Fabric Java-alkalmazást tartalmazza (például: `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Melyik ágat kívánja létrehozni is megadhat (például `/master`).
1. Konfigurálja a *GitHub* kommunikáció a Jenkinsszel tárházat:

   a. A GitHub-adattár oldalát, lépjen a **beállítások** > **Integrációk és szolgáltatások**.

   b. Válassza az **Add Service** (Szolgáltatás hozzáadása) elemet, írja be a **Jenkins** kifejezést, majd válassza ki a **Jenkins-GitHub beépülő modult**.

   c. Adja meg a Jenkins-webhook URL-címét (alapértelmezés szerint ez a következő: `http://<PublicIPorFQDN>:8081/github-webhook/`). Kattintson az **add/update service** (Szolgáltatás hozzáadása/frissítése) elemre.

   d. A Jenkins-példányra egy tesztesemény érkezik. A GitHubban egy zöld pipa jelenik meg a webhook mellett, és a projekt létrejön.

1. Az a **hozhat létre eseményindítókat** Jenkins lapra, válassza ki a kívánt felépítési. Ebben a példában a buildelés kiváltása, valahányszor az adattárba egy leküldéses történik, ezért kattintson kívánt **GitHub hook trigger for GITScm lekérdezés**. (Korábban ez **Build when a change is pushed to GitHub** (Felépítés módosítások GitHubon való közzétételekor) volt.)
1. Az a **összeállítása** lapon, tegye a következők egyikét attól függően, hogy egy Java-alkalmazás vagy egy .NET Core-alkalmazást készít:

   * **A Java-alkalmazások:** Az a **felépítési lépés hozzáadása** legördülő menüben válassza **Invoke Gradle Script**. Kattintson a **speciális**. A Speciális menüben adja meg az elérési útját **fő felépítési szkript** az alkalmazáshoz. A rendszer felveszi a build.gradle elemet a megadott elérési útból, és annak megfelelően működik. Az a [ActorCounter alkalmazás](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), ez a: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Service Fabric, Jenkins felépítési művelet][build-step]

   * **A .NET Core-alkalmazásokban:** Az a **felépítési lépés hozzáadása** legördülő menüben válassza **végrehajtása rendszerhéj**. A parancs mezőben megjelenő a címtár először meg kell módosítani az elérési utat, ahol a build.sh fájlban található. Miután a címtár megváltozott, a build.sh szkript futtatható, és felépíti az alkalmazást.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     A következő képernyőképen látható egy példa a létrehozásához használt parancsok a [teljesítményszámláló szolgáltatás](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) CounterServiceApplication Jenkins feladat nevű mintát.

      ![Service Fabric, Jenkins felépítési művelet][build-step-dotnet]

1. Helyezze üzembe az alkalmazást a felépítés utáni műveletek a Service Fabric-fürtön a Jenkins konfigurálásához kell a fürt tanúsítvány helyét a Jenkins-tárolót. Válassza ki a következők egyikét attól függően, hogy a Jenkins-tárolót belül vagy kívül a fürt fut, és jegyezze fel a fürt tanúsítvány helye:

   * **A jenkins a fürtben fut:** A tanúsítvány elérési útjának értékét echo találhatók a *Certificates_JenkinsOnSF_Code_MyCert_PEM* környezeti változót, a tárolóban.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **A jenkins fut a fürtön kívül:** Kövesse az alábbi lépéseket a fürt tanúsítvány másolatára a tároló:
      1. A tanúsítvány PEM formátumban kell lennie. Ha nem rendelkezik egy PEM-fájlt, létrehozhat egyet a tanúsítvány PFX-fájlból. Ha nem jelszóval védett PFX-fájlt, futtassa a következő parancsot a gazdagépről:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
         ``` 

      Ha jelszóval védett PFX-fájl, például a jelszót a `-passin` paraméter. Példa:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
         ``` 

      1. A Jenkins-tárolót a tárolót azonosító lekéréséhez futtassa `docker ps` a gazdagépről.
      1. Másolja a PEM-fájl az a következő paranccsal Docker-tároló:
    
         ```sh
         docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
         ``` 

Majdnem kész! Ne zárja be a Jenkins-feladatot. Csak a többi feladat, hogy az alkalmazás a Service Fabric-fürt üzembe helyezéséhez a felépítés utáni lépéseket konfigurálja:

* Egy fejlesztési-tesztelési környezetben való üzembe helyezéséhez kövesse [konfigurálhatja a telepítést, használja a fürt felügyeleti végpontja](#configure-deployment-using-cluster-management-endpoint).
* Éles környezetben való üzembe helyezéséhez kövesse [konfigurálhatja a telepítést, az Azure-hitelesítőadatokkal](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurálhatja a telepítést, használja a fürt felügyeleti végpontja
Fejlesztési-tesztelési környezetet az alkalmazás üzembe helyezéséhez használhatja a fürt felügyeleti végpontját. A lehető legkevesebb beállítás konfigurálása a felépítés utáni művelet a fürt felügyeleti végpontját az alkalmazás üzembe helyezéséhez szükséges. Ha egy éles környezetbe helyezi üzembe, folytassa a [konfigurálhatja a telepítést, az Azure-hitelesítőadatokkal](#configure-deployment-using-azure-credentials) konfigurálhatja egy Azure Active Directory szolgáltatás egyszerű üzembe helyezés során használni.    

1. A Jenkins-feladatot, kattintson a **felépítés utáni műveletekben** fülre. 
1. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüből válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. 
1. A **Service Fabric-fürtkonfiguráció**, jelölje be a **töltse ki a Service Fabric felügyeleti végpontja** választógombot.
1. A **felügyeleti futtató**, adja meg a kapcsolati végpont a fürtben – például `{your-cluster}.eastus.cloudapp.azure.com`.
1. A **Ügyfélkulcsot** és **ügyféltanúsítvány**, adja meg a PEM-fájl helyét a Jenkins-tárolót; például `/var/jenkins_home/clustercert.pem`. (Utolsó lépésében a tanúsítvány helye kimásolt [létrehozása és konfigurálása a Jenkins-feladat](#create-and-configure-a-jenkins-job).)
1. Alatt **Alkalmazáskonfiguráció**, konfigurálja a **alkalmazásnév**, **alkalmazástípus**, (relatív) és **Application Manifestelérésiútja** mezőket.

   ![Service Fabric Jenkins felépítés utáni művelet felügyeleti végpont konfigurálása](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Kattintson a **konfigurációjának ellenőrzése**. Kattintson az ellenőrzés sikeres **mentése**. A Jenkins-feladat folyamat most már teljes körűen konfigurálva. Folytassa a [további lépések](#next-steps) az üzemelő példány teszteléséhez.

## <a name="configure-deployment-using-azure-credentials"></a>Az Azure-hitelesítőadatokkal üzemelő példányának konfigurálása
Éles környezetben az alkalmazások üzembe helyezése az Azure hitelesítő erősen ajánlott. Ez a szakasz bemutatja, hogyan konfigurálhatja egy Azure Active Directory-szolgáltatásnevet a felépítés utáni művelet az alkalmazás telepítéséhez. Szolgáltatásnevek rendelhet szerepköröket, a Jenkins-feladat az engedélyek korlátozására a címtárban. 

Fejlesztési és tesztelési környezetek esetében konfigurálhatja az Azure hitelesítő adatait vagy a fürt felügyeleti végpontját az alkalmazás központi telepítése. A fürt felügyeleti végpontja konfigurálásával kapcsolatos részletekért lásd: [konfigurálhatja a telepítést, használja a fürt felügyeleti végpontja](#configure-deployment-using-cluster-management-endpoint).   

1. Hozzon létre egy Azure Active Directory egyszerű szolgáltatás, és rendelje hozzá az engedélyek az Azure-előfizetésében, kövesse a [egy Azure Active Directory-alkalmazás és -szolgáltatásnév létrehozása a portál használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Figyelje meg, a következőhöz:

   * Miközben a témakörben leírt lépéseket követve, ügyeljen arra, hogy másolja ki és mentse a következő értékeket: *Alkalmazásazonosító*, *alkalmazáskulcsot*, *címtár-azonosító (bérlő azonosító)*, és *előfizetés-azonosító*. Szükség van rájuk a Jenkins az Azure hitelesítő adatainak konfigurálása.
   * Ha nem rendelkezik a [szükséges engedélyek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) a címtárban kell kérje meg egy rendszergazda adhat meg engedélyeket, vagy az Ön számára az egyszerű szolgáltatás létrehozása vagy konfigurálása a felügyeleti végpontja kell a a fürt a **felépítés utáni műveletekben** a feladatnak a Jenkinsben.
   * Az a [létrehozása az Azure Active Directory-alkalmazás](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) a szakaszban adhatja meg minden olyan megfelelően formázott URL-CÍMÉT a **bejelentkezési URL-**.
   * Az a [rendelje hozzá az alkalmazást egy szerepkörhöz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) területen rendelhet hozzá az alkalmazás a *olvasó* szerepkör az az erőforráscsoport, a fürt számára.

1. A Jenkins-feladatot, kattintson a **felépítés utáni műveletekben** fülre.
1. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüből válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. 
1. A **Service Fabric-fürtkonfiguráció**, jelölje be a **válassza ki a Service Fabric-fürt** választógombot. Kattintson a **Hozzáadás** melletti **Azure hitelesítő adatok**. Kattintson a **Jenkins** jelölje be a Jenkins-hitelesítő adatok szolgáltató.
1. Válassza ki a Jenkins-hitelesítő adatok szolgáltató **a Microsoft Azure egyszerű szolgáltatás** származó a **Kind** legördülő.
1. A mentett beállításakor mentése az egyszerű szolgáltatást az 1. lépésben a következő mezők beállítása értékeket használja:

   * **Ügyfél-azonosító**: *Alkalmazás azonosítója*
   * **Titkos Ügyfélkód**: *Alkalmazáskulcs*
   * **Bérlőazonosító**: *Címtár-azonosító*
   * **Előfizetés-azonosító**: *előfizetés-azonosító*
1. Adjon meg egy leíró **azonosító** segítségével válassza ki a hitelesítő adatokat a Jenkins és a egy rövid **leírás**. Kattintson a **egyszerű szolgáltatás ellenőrzése**. Ha az ellenőrzés sikeres, kattintson a **Hozzáadás**.

   ![A Jenkins Service Fabric adja meg Azure hitelesítő adatait](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Vissza csoportban **Service Fabric-fürtkonfiguráció**, győződjön meg arról, hogy az új hitelesítő adat van kiválasztva a **Azure hitelesítő adatok**. 
1. Az a **erőforráscsoport** legördülő menüben válassza ki az erőforráscsoportot, a fürt számára az alkalmazás számára telepíteni kívánja.
1. Az a **Service Fabric** legördülő menüben válassza ki a fürtöt, amelyre az alkalmazást telepíteni kívánja.
1. A **Ügyfélkulcsot** és **Client Cert**, írja be a Jenkins-tárolót a PEM-fájl helyét. Például: `/var/jenkins_home/clustercert.pem`. 
1. Alatt **Alkalmazáskonfiguráció**, konfigurálja a **alkalmazásnév**, **alkalmazástípus**, (relatív) és **Application Manifestelérésiútja** mezőket.
    ![Az Azure hitelesítő adatok beállítása a Service Fabric Jenkins felépítés utáni művelet](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Kattintson a **konfigurációjának ellenőrzése**. Kattintson az ellenőrzés sikeres **mentése**. A Jenkins-feladat folyamat most már teljes körűen konfigurálva. Továbbra is a [további lépések](#next-steps) az üzemelő példány teszteléséhez.

## <a name="troubleshooting-the-jenkins-plugin"></a>A Jenkins beépülő modul hibáinak elhárítása

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>További lépések
A GitHub és a Jenkins beállítása kész. Fontolja meg néhány mintamódosítást a a `reliable-services-actor-sample/Actors/ActorCounter` projekt a saját elágazásában a tárház https://github.com/Azure-Samples/service-fabric-java-getting-started. A módosításokat továbbíthatja a távoli `master` ágra (vagy bármilyen egyéb olyan használata már konfigurálta ágra). Ez aktiválja a konfigurált `MyJob` Jenkins-feladatot. Beolvassa a módosításokat a Githubról, lépteti őket, és központilag telepíti az alkalmazást a fürt megadott felépítés utáni műveletekben.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

