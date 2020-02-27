---
title: Jenkins Build a Container instance
description: Megtudhatja, hogyan konfigurálhat egy Jenkins-kiszolgálót az igény szerinti Build-feladatok futtatásához Azure Container Instances
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: 8bb84895fb581053248fbad326ea7b2c8d1873a2
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617969"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Azure Container Instances használata Jenkins Build-ügynökként

A Azure Container Instances (ACI) igény szerinti, kihasználható és elszigetelt környezetet biztosít a tároló munkaterhelések futtatásához. Ezen attribútumok miatt az ACI nagyszerű platformot biztosít a Jenkins Build-feladatok nagy léptékű futtatásához. Ez a cikk végigvezeti egy olyan Jenkins-kiszolgáló üzembe helyezésén és használatán, amely előre konfigurálva van az ACI-val Build célként.

További információ a Azure Container Instancesről: [About Azure Container instances](../container-instances/container-instances-overview.md).

## <a name="deploy-a-jenkins-server"></a>Jenkins-kiszolgáló üzembe helyezése

1. A Azure Portal válassza az **erőforrás létrehozása** és a **Jenkins**keresése lehetőséget. Válassza ki a Jenkins-ajánlatot a **Microsoft**közzétevővel, majd válassza a **Létrehozás**lehetőséget.

2. Adja meg az alábbi adatokat az **alapvető beállítások** űrlapon, majd kattintson az **OK gombra**.

   - **Név**: adja meg a Jenkins-telepítés nevét.
   - **Felhasználónév**: adja meg a Jenkins virtuális gép rendszergazda felhasználójának nevét.
   - **Hitelesítés típusa**: javasoljuk, hogy adjon meg egy nyilvános SSH-kulcsot a hitelesítéshez. Ha ezt a lehetőséget választja, illessze be a Jenkins virtuális gépre való bejelentkezéshez használandó nyilvános SSH-kulcsot.
   - **Előfizetés**: válasszon ki egy Azure-előfizetést.
   - **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt.
   - **Hely**: válassza ki a Jenkins-kiszolgáló helyét.

   ![A Jenkins-portál üzembe helyezésének alapszintű beállításai](./media/container-instances-jenkins/jenkins-portal-01.png)

3. A **További beállítások** űrlapon hajtsa végre a következő elemeket:

   - **Méret**: válassza ki a Jenkins virtuális gép megfelelő méretezési beállítását.
   - Virtuálisgép- **lemez típusa**: adja meg a Jenkins-kiszolgáló **HDD** -lemezét (merevlemez-meghajtó) vagy **SSD** -meghajtóját.
   - **Virtuális hálózat**: kattintson a nyílra, ha módosítani szeretné az alapértelmezett beállításokat.
   - **Alhálózatok**: válassza ki a nyilat, ellenőrizze az adatokat, majd kattintson az **OK gombra**.
   - **Nyilvános IP-cím**: válassza a nyilat, hogy a nyilvános IP-címet egyéni névvel adja meg, KONFIGURÁLJA az SKU-t, és állítsa be a hozzárendelési módszert.
   - **Tartománynév címkéje**: adjon meg egy értéket, amely teljesen minősített URL-címet hoz létre a Jenkins virtuális géphez.
   - **Jenkins-Kiadás típusa**: válassza ki a kívánt kiadás típusát a következő lehetőségek közül: **LTS**, **heti Build**vagy **Azure ellenőrizve**.

   ![A Jenkins-portál üzembe helyezésének további beállításai](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Az egyszerű szolgáltatásnév integrálásához válassza az **automatikus (MSI)** lehetőséget, hogy az [Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md) automatikusan hozzanak létre hitelesítési identitást a Jenkins-példányhoz. Válassza a **manuális** lehetőséget a saját egyszerű szolgáltatásnév hitelesítő adatainak megadásához.

5. A felhőalapú ügynökök egy felhőalapú platformot állítanak be a Jenkins-Build feladatokhoz. Ennek a cikknek a kedvéért válassza az **ACI**lehetőséget. Az ACI Cloud Agent esetében minden Jenkins-Build-feladatot egy tároló-példányban futtatnak.

   ![A Jenkins-portál üzembe helyezésének felhő-integrációs beállításai](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Ha elkészült az integrációs beállításokkal, kattintson az **OK gombra**, majd az érvényesítési összegzésben kattintson ismét **az OK gombra** . Válassza a **Létrehozás** lehetőséget a **használati feltételek** összegzésben. A Jenkins-kiszolgáló üzembe helyezése néhány percet vesz igénybe.

## <a name="configure-jenkins"></a>A Jenkins konfigurálása

1. A Azure Portal keresse meg a Jenkins erőforráscsoportot, válassza ki a Jenkins virtuális gépet, és jegyezze fel a DNS-nevet.

   ![A Jenkins virtuális géphez tartozó DNS-név](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Keresse meg a Jenkins virtuális gép DNS-nevét, és másolja a visszaadott SSH-karakterláncot.

   ![Jenkins bejelentkezési utasítások SSH-karakterlánccal](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Nyisson meg egy terminál-munkamenetet a fejlesztői rendszeren, és illessze be az SSH-karakterláncot az utolsó lépésből. Frissítse `username` a Jenkins-kiszolgáló üzembe helyezésekor megadott felhasználónévre.

4. A munkamenet csatlakoztatása után futtassa a következő parancsot a kezdeti rendszergazdai jelszó lekéréséhez:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Hagyja meg az SSH-munkamenetet és a bújtatást, és lépjen a `http://localhost:8080` egy böngészőben. Illessze be a kezdeti rendszergazdai jelszót a mezőbe, majd kattintson a **Continue (folytatás**) gombra.

   !["Jenkins zárolásának feloldása" képernyő a rendszergazdai jelszóval](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Válassza a **javasolt beépülő modulok telepítése** lehetőséget az összes ajánlott Jenkins beépülő modul telepítéséhez.

   !["Jenkins testreszabása" képernyő "a javasolt beépülő modulok telepítése" lehetőség kijelölve](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Hozzon létre egy rendszergazdai felhasználói fiókot. Ez a fiók a Jenkins-példányának beléptetéséhez és használatához való bejelentkezéshez használható.

   !["Az első rendszergazda felhasználó létrehozása" képernyő, a hitelesítő adatokkal kitöltve](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Válassza a **Mentés és Befejezés**lehetőséget, majd válassza a **Start a Jenkins használatával** lehetőséget a konfiguráció befejezéséhez.

A Jenkins mostantól konfigurálva van, és készen áll a kód kiépítésére és üzembe helyezésére. Ebben a példában egy egyszerű Java-alkalmazás használatával lehet bemutatni egy Jenkins-buildet Azure Container Instanceson.

## <a name="create-a-build-job"></a>Build-feladatok létrehozása

Most létrejön egy Jenkins-Build-feladatokkal, amely bemutatja a Jenkins buildet egy Azure Container instance-példányon.

1. Válassza az **új elem**lehetőséget, adja meg a Build projekt nevét, például **ACI-demo**, válassza a **Freestyle projekt**lehetőséget, majd kattintson **az OK gombra**.

   ![A felépítési feladatokhoz és a projekttípus listájához tartozó mező](./media/container-instances-jenkins/jenkins-new-job.png)

2. Az **általános**területen ellenőrizze, hogy be van-e jelölve **a projekt futtatási helyének korlátozása** . Adja meg a **Linux** kifejezést a Label kifejezéshez. Ez a konfiguráció biztosítja, hogy ez a felépítési feladatok fussanak az ACI-felhőben.

   !["Általános" lap konfigurációs részletekkel](./media/container-instances-jenkins/jenkins-job-01.png)

3. A **Létrehozás**területen válassza a **létrehozási lépés hozzáadása** elemet, és válassza a **rendszerhéj végrehajtása**lehetőséget. Adja meg a `echo "aci-demo"` parancsot.

   !["Build" (létrehozás) lap a Build lépés kiválasztásával](./media/container-instances-jenkins/jenkins-job-02.png)

5. Kattintson a **Mentés** gombra.

## <a name="run-the-build-job"></a>A felépítési feladatok futtatása

Ha tesztelni szeretné a felépítési feladatot, és megfigyelheti Azure Container Instances a Build platformként, manuálisan indítson el egy buildet.

1. Válassza a **Build Now** (létrehozás) lehetőséget a létrehozási feladatok elindításához. Eltarthat néhány percig, amíg a feladatok elindulnak. Az alábbi képhez hasonló állapotnak kell megjelennie:

   !["Előzmények" információ a feladatok állapotáról](./media/container-instances-jenkins/jenkins-job-status.png)

2. Amíg a művelet fut, nyissa meg a Azure Portal, és tekintse meg a Jenkins erőforráscsoportot. Látnia kell, hogy létrejött egy tároló példánya. A Jenkins-feladatok ezen a példányon belül futnak.

   ![Az erőforráscsoport tároló-példánya](./media/container-instances-jenkins/jenkins-aci.png)

3. Mivel a Jenkins több feladatot futtat, mint a Jenkins-végrehajtók beállított száma (alapértelmezés szerint 2), a rendszer több tároló példányt hoz létre.

   ![Újonnan létrehozott Container instances](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Az összes felépítési feladat befejezése után a rendszer eltávolítja a tároló példányait.

   ![A Container instances erőforráscsoport eltávolítva](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>A Jenkins beépülő modul hibáinak elhárítása

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Folyamatos integráció/üzembe helyezés az Azure App Service-be](java-deploy-webapp-tutorial.md)