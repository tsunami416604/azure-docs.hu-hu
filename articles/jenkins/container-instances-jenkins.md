---
title: A Jenkins tárolópéldányra épül
description: Megtudhatja, hogyan konfigurálhat egy Jenkins-kiszolgálót igény szerinti buildfeladatok futtatására az Azure Container Instances-ban
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: 8bb84895fb581053248fbad326ea7b2c8d1873a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617969"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Azure Container-példányok használata Jenkins buildügynökként

Az Azure Container Instances (ACI) egy igény szerinti, burstable és elkülönített környezetet biztosít a tárolóalapú számítási feladatok futtatásához. Ezek nek az attribútumoknak köszönhetően az ACI nagyszerű platformot biztosít a Jenkins-build feladatok széles körű futtatásához. Ez a cikk bemutatja a Jenkins-kiszolgáló üzembe helyezését és használatát, amely előre konfigurálva van az ACI buildcélként.

Az Azure Container-példányokról az [Azure Container-példányokról című témakörben](../container-instances/container-instances-overview.md)talál további információt.

## <a name="deploy-a-jenkins-server"></a>Jenkins-kiszolgáló telepítése

1. Az Azure Portalon válassza az **Erőforrás létrehozása lehetőséget,** és keresse meg a Jenkins című **gombot.** Válassza ki a Jenkins-ajánlatot a **Microsoft**egyik közzétevőjével, majd válassza a **Létrehozás lehetőséget.**

2. Írja be a következő adatokat az **Alapok** képernyőn, majd kattintson az **OK gombra.**

   - **Név**: Adja meg a Jenkins-telepítés nevét.
   - **Felhasználónév**: Adja meg a Jenkins virtuális gép rendszergazdai felhasználójának nevét.
   - **Hitelesítés típusa**: SSH nyilvános kulcsot ajánlunk a hitelesítéshez. Ha ezt a beállítást választja, illessze be a Jenkins virtuális gépre való bejelentkezéshez használt SSH nyilvános kulcsot.
   - **Előfizetés**: Válasszon egy Azure-előfizetést.
   - **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt.
   - **Hely**: Válassza ki a Jenkins-kiszolgáló helyét.

   ![A Jenkins-portál telepítésének alapbeállításai](./media/container-instances-jenkins/jenkins-portal-01.png)

3. A **További beállítások** képernyőn töltse ki a következő elemeket:

   - **Méret**: Válassza ki a megfelelő méretezési lehetőséget a Jenkins virtuális gép.
   - **Virtuálisgép-lemez típusa**: Adja meg **hdd** (merevlemez-meghajtó) vagy **SSD (SSD)** a Jenkins-kiszolgáló.
   - **Virtuális hálózat**: Jelölje ki a nyilat, ha módosítani szeretné az alapértelmezett beállításokat.
   - **Alhálózatok**: Jelölje ki a nyilat, ellenőrizze az adatokat, és válassza az **OK**gombot.
   - **Nyilvános IP-cím**: Válassza ki a nyilat, hogy a nyilvános IP-cím egyéni nevet adjon, konfigurálja a termékváltozatot, és állítsa be a hozzárendelési módszert.
   - **Tartománynév-címke:** Adjon meg egy értéket a Jenkins virtuális gép teljesen minősített URL-címének létrehozásához.
   - **Jenkins-kiadás típusa**: Válassza ki a kívánt kiadási típust a következő lehetőségek közül: **LTS**, **Weekly build**vagy **Azure Verified**.

   ![További beállítások a Jenkins-portál telepítéséhez](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Egyszerű szolgáltatásintegráció esetén válassza **az Automatikus(MSI)** lehetőséget, hogy az [Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md) automatikusan hozzanak létre egy hitelesítési identitást a Jenkins-példányhoz. Válassza **a Kézi** lehetőséget a saját egyszerű szolgáltatáshitelesítő adatok megadásához.

5. A felhőügynökök felhőalapú platformot konfigurálnak a Jenkins-feladatok létrehozásához. A cikk kedvéért válassza az **ACI**lehetőséget. Az ACI felhőalapú ügynök, minden Jenkins build feladat fut egy tárolópéldányban.

   ![Felhőintegrációs beállítások a Jenkins-portál üzembe helyezéséhez](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Ha végzett az integrációs beállításokkal, válassza az **OK**gombot, majd az érvényesítési összegzésen ismét az **OK** gombot. Válassza a **Létrehozás** lehetőséget a **Használati feltételek** összegzésén. A Jenkins-kiszolgáló üzembe helyezése néhány percet vesz igénybe.

## <a name="configure-jenkins"></a>A Jenkins konfigurálása

1. Az Azure Portalon keresse meg a Jenkins erőforráscsoportot, válassza ki a Jenkins virtuális gépet, és vegye figyelembe a DNS-nevet.

   ![DNS-név a Jenkins virtuális gép részleteiben](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Tallózással keresse meg a Jenkins virtuális gép DNS-nevét, és másolja a visszaadott SSH-karakterláncot.

   ![Jenkins bejelentkezési utasítások SSH karakterlánccal](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Nyisson meg egy terminálmunkamenetet a fejlesztői rendszeren, és illessze be az SSH-karakterláncba az utolsó lépéstől. Frissítsen `username` arra a felhasználónévre, amelyet a Jenkins-kiszolgáló telepítésekor megadott.

4. A munkamenet csatlakoztatása után futtassa a következő parancsot a kezdeti rendszergazdai jelszó beolvasásához:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Hagyja futni az SSH-munkamenetet `http://localhost:8080` és az alagutat, és lépjen a böngészőben. Illessze be a kezdeti rendszergazdai jelszót a mezőbe, majd válassza a **Folytatás**gombot.

   !["Unlock Jenkins" képernyő a rendszergazdai jelszó dobozával](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Válassza **a Javasolt bővítmények telepítése lehetőséget** az összes ajánlott Jenkins-bővítmény telepítéséhez.

   !["Customize Jenkins" képernyő a "Javasolt bővítmények telepítése" lehetőséggel](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Hozzon létre egy rendszergazdai felhasználói fiókot. Ez a fiók a Jenkins-példányba való bejelentkezéshez és a vele való munkához használatos.

   !["Első rendszergazdai felhasználó létrehozása" képernyő, kitöltött hitelesítő adatokkal](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Válassza a **Mentés és befejezés**lehetőséget, majd a Konfiguráció befejezéséhez válassza a Jenkins **használatának megkezdése** lehetőséget.

A Jenkins most már konfigurálva van, és készen áll a kód összeállítására és telepítésére. Ebben a példában egy egyszerű Java-alkalmazás az Azure Container Instances egy Jenkins-build bemutatására szolgál.

## <a name="create-a-build-job"></a>Buildfeladat létrehozása

Most egy Jenkins build feladat jön létre, hogy bemutassa Jenkins egy Azure-tároló példányra épül.

1. Válassza az **Új elem lehetőséget,** adjon a buildprojektnek egy nevet, például **aci-demo**lehetőséget, válassza **a Freestyle projekt**lehetőséget, és válassza az **OK**lehetőséget.

   ![A buildfeladat nevének és a projekttípusok listájának mezője](./media/container-instances-jenkins/jenkins-new-job.png)

2. Az **Általános**csoportban jelölje be a **Korlátozás, ahol a projekt futtatható.** Adja meg a **linux** a címke kifejezés. Ez a konfiguráció biztosítja, hogy ez a buildfeladat az ACI-felhőn fut.

   !["Általános" lap a konfiguráció részleteivel](./media/container-instances-jenkins/jenkins-job-01.png)

3. A **Build csoportban**válassza **a Build lépés hozzáadása** lehetőséget, és válassza a **Rendszerhéj végrehajtása**lehetőséget. Írja `echo "aci-demo"` be a parancsot.

   !["Build" lap a buildlépés hez való kijelöléssel](./media/container-instances-jenkins/jenkins-job-02.png)

5. Kattintson a **Mentés** gombra.

## <a name="run-the-build-job"></a>A buildelési feladat futtatása

A buildfeladat teszteléséhez és az Azure Container-példányok buildplatformként való megfigyeléséhez manuálisan indítsa el a buildet.

1. Válassza **a Build Now lehetőséget** egy buildelési feladat elindításához. A feladat elindítása néhány percet vesz igénybe. Az alábbihoz hasonló állapotnak kell lennie:

   !["Létrehozási előzmények" információ a feladat állapotával](./media/container-instances-jenkins/jenkins-job-status.png)

2. Afeladat futása közben nyissa meg az Azure Portalt, és tekintse meg a Jenkins erőforráscsoportot. Látnia kell, hogy egy tárolópéldány jött létre. A Jenkins-feladat ebben a példányban fut.

   ![Tárolópéldány az erőforráscsoportban](./media/container-instances-jenkins/jenkins-aci.png)

3. Mivel a Jenkins több feladatot futtat, mint a jenkins-végrehajtók konfigurált száma (alapértelmezett 2), több tárolópéldány jön létre.

   ![Újonnan létrehozott tárolópéldányok](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Miután az összes build feladatok befejeződött, a tároló példányok törlődnek.

   ![Erőforráscsoport eltávolított tárolópéldányokkal](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>A Jenkins beépülő modul hibáinak elhárítása

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Folyamatos integráció/üzembe helyezés az Azure App Service-be](java-deploy-webapp-tutorial.md)