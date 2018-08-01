---
title: A Jenkins használata Azure Container Instances szolgáltatásban az ügynök létrehozása
description: Megtudhatja, hogyan használhatja az Azure Container Instances egy Jenkins felépítési ügynök.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 08/31/2018
ms.author: marsma
ms.openlocfilehash: 6419753a48e1356b2d94592fca72cee1f848dcdb
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390653"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>A Jenkins használata Azure Container Instances szolgáltatásban az ügynök létrehozása

Az Azure Container Instances (ACI) tárolóalapú számítási feladatot futtat egy igény szerinti, – adatlöket-kezelés és elkülönített környezetet biztosít. Ezek az attribútumok miatt ACI lehetővé teszi a futó Jenkins-létrehozási feladatok nagy méretű nagyszerű platformot. Ez a cikk végigvezeti a telepítésével és használatával a Jenkins-kiszolgáló, amely előre konfigurálva van az ACI build célként.

További információ az Azure Container Instances: [kapcsolatos Azure Container Instances][about-aci].

## <a name="deploy-a-jenkins-server"></a>Jenkins-kiszolgáló üzembe helyezése

1. Az Azure Portalon válassza ki a **erőforrás létrehozása** és keressen rá a **Jenkins**. Jelölje be a Jenkins ajánlat az kiadóként a **Microsoft**, majd válassza ki **létrehozás**.

2. Adja meg az alábbi adatokat a **alapjai** alkotnak, és válassza ki **OK**.

   - **Név**: Adjon meg egy nevet a Jenkins-telepítéshez.
   - **Felhasználónév**: Adja meg a rendszergazdai felhasználót a Jenkins virtuális gép nevét.
   - **Hitelesítési típus**: azt javasoljuk, hogy a nyilvános SSH-kulcsot a hitelesítéshez. Ha ezt a lehetőséget választja, illessze be a nyilvános SSH-kulccsal jelentkezik be a Jenkins virtuális gép használható.
   - **Előfizetés**: válasszon ki egy Azure-előfizetést.
   - **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt.
   - **Hely**: válassza ki a Jenkins-kiszolgáló helyét.

   ![A Jenkins portálon keresztüli üzembe helyezési alapbeállítások](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Az a **további beállítás** alkotnak, hajtsa végre a következő elemek:

   - **Méret**: válassza ki a megfelelő méretezési lehetőséget a Jenkins virtuális gép.
   - **Virtuális merevlemez típusa**: Adja meg **HDD** (merevlemez-meghajtó) vagy **SSD** (SSD-meghajtóra) a Jenkins-kiszolgáló számára.
   - **Virtuális hálózat**: a nyílra, ha azt szeretné, módosíthatja az alapértelmezett beállításokat.
   - **Alhálózatok**: a nyílra, ellenőrizze az adatokat, és válassza ki **OK**.
   - **Nyilvános IP-cím**: a nyílra kattintva nevezze el a nyilvános IP-cím egy egyéni, konfigurálja a Termékváltozat és hozzárendelési módszert állítja be.
   - **Tartománynévcímke**: Adjon meg egy értéket egy abszolút URL-címet a Jenkins virtuális gép létrehozásához.
   - **A Jenkins kiadási típusa**: válassza ki a kívánt kiadási típusát a lehetőségek közül: **LTS**, **hetente hozhat létre**, vagy **Azure ellenőrzött**.

   ![A Jenkins portálon keresztüli üzembe helyezési további beállításai](./media/container-instances-jenkins/jenkins-portal-02.png)

4. A szolgáltatás egyszerű integráció, válassza ki a **Auto(MSI)** rendelkeznie [Azure Felügyeltszolgáltatás-identitás] [ managed-service-identity] automatikusan létrehozza a hitelesítési identitást a Jenkins a példány. Válassza ki **manuális** saját szolgáltatásnév hitelesítő adatainak megadását.

5. Felhő-ügynökök konfigurálása egy felhőalapú platform, Jenkins felépítési feladatok. Válassza ki az ebben a cikkben **ACI**. Az ACI felhőalapú ügynököt minden egyes Jenkins létrehozási feladatot futtatja egy tárolópéldányt.

   ![A Jenkins portálon keresztüli üzembe helyezési felhő-integrációs beállításai](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Ha elkészült, az integrációs beállításokkal, válassza ki a **OK**, majd válassza ki **OK** újra az érvényesítés összegzése a. Válassza ki **létrehozás** a a **használati feltételeket tartalmazó fájl** összegzése. A Jenkins-kiszolgáló üzembe helyezése néhány percet vesz igénybe.

## <a name="configure-jenkins"></a>A Jenkins konfigurálása

1. Az Azure Portalon keresse meg a Jenkins-erőforráscsoportot, válassza ki a Jenkins virtuális gép és jegyezze fel a DNS-nevét.

   ![A Jenkins virtuális gép adatait a DNS-név](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Keresse meg a Jenkins virtuális gép DNS-nevét, és másolja a visszaadott SSH-karakterláncot.

   ![A Jenkins bejelentkezési utasítások az SSH-karakterlánc](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Nyisson meg egy terminál-munkamenetben a fejlesztői rendszeren, és illessze be az SSH-karakterlánc, az utolsó lépésben. Frissítés `username` , amikor központilag telepítette a Jenkins-kiszolgáló megadott felhasználónév.

4. A munkamenet után van csatlakoztatva, a kezdeti rendszergazdai jelszó lekérése a következő parancsot:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Hagyja meg az SSH-munkamenetet, és alagút fut, és nyissa meg http://localhost:8080 egy böngészőben. A kezdeti rendszergazdai jelszó illessze be a mezőbe, és válassza ki **Folytatás**.

   !["A Jenkins zárolásának feloldása" képernyő és a box, a rendszergazdai jelszó](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Válassza ki **javasolt beépülő modulok telepítése** telepítheti az összes ajánlott a Jenkins beépülő modulokat.

   ![Képernyő "Testre szabhatja a Jenkins" és "Javasolt beépülő modulok telepítése" kijelölve](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Hozzon létre egy rendszergazdai felhasználói fiókját. Ez a fiók bejelentkezett, és a Jenkins-példány használata szolgál.

   !["Első rendszergazdai felhasználót létrehozása" képernyőt kitölti a rendszer hitelesítő adatokkal](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Válassza ki **mentés és Befejezés**, majd válassza ki **Jenkins használatának megkezdéséhez** konfigurálásának befejezéséhez.

A Jenkins konfigurálva van, és készen áll létrehozása és kód üzembe helyezése. Ebben a példában egy egyszerű Java-alkalmazás segítségével mutatja be a Jenkins-buildelés, az Azure Container Instances szolgáltatásban.

## <a name="create-a-build-job"></a>Hozzon létre a fordítási feladatot

Bemutatják a Jenkins épül, amely egy Azure Container Instances szolgáltatáshoz, egy Jenkins létrehozási feladatot kell létrehozni.

1. Válassza ki **új elem**, nevezze el a build project például **aci-demo**válassza **Freestyle project**, és válassza ki **OK**.

   ![A fordítási feladatot, és a project-típusok listája a nevének mezőjében](./media/container-instances-jenkins/jenkins-new-job.png)

2. A **általános**, ügyeljen arra, hogy **korlátozása, amelyben a projekt futtathatók** van kiválasztva. Adja meg **linux** a címke kifejezéshez. Ez a konfiguráció biztosítja, hogy fut-e a fordítási feladatot az ACI-felhőben.

   ![Konfiguráció részletei az "Általános" lap](./media/container-instances-jenkins/jenkins-job-01.png)

3. Alatt **hozhat létre**, jelölje be **felépítési lépés hozzáadása** válassza **rendszerhéj végrehajtása**. Adja meg `echo "aci-demo"` parancsot.

   ![A létrehozási lépés választása esetén "Build" lap](./media/container-instances-jenkins/jenkins-job-02.png)

5. Kattintson a **Mentés** gombra.

## <a name="run-the-build-job"></a>A build-feladat futtatása

Tesztelje a fordítási feladatot, és tekintse meg az Azure Container Instances a build-platformként, a build manuális elindításához.

1. Válassza ki **hozhat létre most** , indítsa el a fordítási feladatot. Indítsa el a feladat néhány percet vesz igénybe. Az alábbi képhez hasonló állapotnak kell megjelennie:

   !["Build előzmények" az adatokat a feladat állapota](./media/container-instances-jenkins/jenkins-job-status.png)

2. A feladat futása közben nyissa meg az Azure Portalt, és tekintse meg a Jenkins erőforráscsoportot. Megtekintheti, hogy létrejött-e egy tárolópéldányt. A Jenkins-feladat fut ezen a példányon belül.

   ![Az erőforráscsoportban lévő tárolópéldányon](./media/container-instances-jenkins/jenkins-aci.png)

3. A Jenkins Jenkins végrehajtóval (alapértelmezett érték 2) a beállított számúnál több feladat futása több tárolópéldányok jönnek létre.

   ![Az újonnan létrehozott tárolópéldányok](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Után minden build feladat befejeződött, a rendszer eltávolítja a container Instances szolgáltatásban.

   ![Az eltávolított container instances szolgáltatással erőforráscsoport](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Hibaelhárítás a Jenkins beépülő modul

Ha bármely hibái a Jenkins beépülő modulok, a fájl be a problémát a [Jenkins JIRA](https://issues.jenkins-ci.org/) az adott összetevő.

## <a name="next-steps"></a>További lépések

Az Azure-ban a Jenkins kapcsolatos további információkért lásd: [Azure és a Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md