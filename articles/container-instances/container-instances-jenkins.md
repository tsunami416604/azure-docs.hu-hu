---
title: Használata Azure tároló példányok, egy Jenkins build ügynök
description: 'Útmutató: Azure tároló példányok használatára, mert egy Jenkins build ügynök.'
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: 4df230c8306a3876e94a5e9ada5e7408f134ba26
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359544"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Használata Azure tároló példányok, egy Jenkins build ügynök

A tárolóalapú munkafolyamatok futtatásához igény, burstable és elkülönített környezetet biztosít az Azure tároló példányok (ACI). Miatt ezek az attribútumok ACI teszi kiváló platform nagy léptékű Jenkins build feladatok futtatásához. Ez a cikk végigvezeti a központi telepítésével és használatával egy előre konfigurált Jenkins kiszolgáló ACI az összeállítási cél.

Azure-tároló példányokon további információkért lásd: [kapcsolatos Azure tároló példányok][about-aci].

## <a name="deploy-a-jenkins-server"></a>Jenkins kiszolgáló központi telepítése

1. Válassza ki az Azure-portálon **hozzon létre egy erőforrást** keresse meg a **Jenkins**. Válassza ki az Jenkins elérhető, a közzétevő **Microsoft**, majd válassza ki **létrehozása**.

2. Adja meg az alábbi adatokat a **alapjai** alkotnak, és válassza ki **OK**.

   - **Név**: Adjon meg egy nevet a Jenkins központi telepítéshez.
   - **Felhasználónév**: Adja meg a felhasználót a rendszergazda a Jenkins virtuális gép nevét.
   - **Hitelesítés típusa**: azt javasoljuk, hogy egy nyilvános SSH-kulcsot a hitelesítéshez. Ha ezt a beállítást, illessze be a nyilvános SSH-kulcs jelentkezik be a Jenkins virtuális gépek portbesorolása.
   - **Előfizetés**: válasszon ki egy Azure-előfizetést.
   - **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt.
   - **Hely**: Jelölje ki a Jenkins kiszolgáló helyét.

   ![Alapvető Jenkins portál telepítési beállításait](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Az a **további beállításokat** alkotnak, végezze el a következő elemek:

   - **Méret**: válassza ki a Jenkins virtuális gép megfelelő méretezési beállítását.
   - **Virtuális gép lemeztípus**: Adja meg **HDD** (merevlemez-meghajtó) vagy **SSD** (SSD-meghajtóra) a Jenkins kiszolgáló.
   - **Virtuális hálózati**: válassza a nyílra, ha azt szeretné, az alapértelmezett beállítások módosítása.
   - **Alhálózatok**: mutató nyílra, ellenőrizze az adatokat, és válassza ki **OK**.
   - **Nyilvános IP-cím**: mutató nyílra a nyilvános IP-cím egy egyéni nevet adjon, konfigurálja a Termékváltozat és hozzárendelési módszert állítja be.
   - **Tartománynév-címke**: Adjon meg egy értéket, abszolút URL-CÍMÉT a Jenkins virtuális gép létrehozásához.
   - **Jenkins kiadás típusa**: válassza ki a kívánt kiadási típusainak a lehetőségek közül: **LTS**, **hetente build**, vagy **Azure ellenőrzése**.

   ![További beállítások megadása Jenkins portál telepítése](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Válassza a szolgáltatás egyszerű integráció, **Auto(MSI)** rendelkeznie [Azure felügyelt Szolgáltatásidentitás] [ managed-service-identity] automatikusan létrehozza a hitelesítési identitást a Jenkins a példány. Válassza ki **manuális** saját szolgáltatás egyszerű hitelesítő adatait.

5. Felhő ügynökök konfigurálása egy felhőalapú platform Jenkins build feladatok. Válassza ki az ebben a cikkben **ACI**. ACI felhő ügynök minden egyes Jenkins összeállítási feladat fut egy tároló-példányban.

   ![Integráció felhőbeállítások Jenkins portál telepítése](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Amikor elkészült, az integráció beállításokkal, válassza ki a **OK**, majd válassza ki **OK** az érvényesítési összegzés meg újra. Válassza ki **létrehozása** a a **használati feltételek** összegzése. A Jenkins kiszolgáló központi telepítése néhány percet vesz igénybe.

## <a name="configure-jenkins"></a>A Jenkins konfigurálása

1. Az Azure portálon keresse meg a Jenkins erőforráscsoportot, válassza ki a Jenkins virtuális gépet, és jegyezze fel a DNS-nevét.

   ![A Jenkins virtuális gép adatait a DNS-név](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Keresse meg a virtuális gép Jenkins DNS-nevét, és másolja a visszaadott SSH-karakterláncot.

   ![Jenkins bejelentkezési utasítások SSH karakterlánccal](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Nyisson meg egy terminál-munkamenetet a fejlesztői rendszeren, és illessze be az utolsó lépésben SSH-karakterláncban. Frissítés `username` a felhasználónév, amikor központilag telepítette a Jenkins kiszolgáló megadott.

4. A munkamenet végeztével csatlakozik, a kezdő adminisztrációs jelszó lekérése a következő parancsot:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Hagyja meg az SSH-munkamenetet, és alagút fut, és navigáljon a http://localhost:8080 a böngészőben. Illessze be a kezdeti rendszergazdai jelszavát, majd válassza ki **Folytatás**.

   ![Hagyja a rendszergazdai jelszó a "Zárolásának feloldásához Jenkins" képernyő](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Válassza ki **javasolt beépülő modulok telepítése** telepítheti az összes ajánlott Jenkins beépülő modulok.

   ![Képernyő "Testreszabása Jenkins" a "Javasolt beépülő modulok telepítése a" kijelölt](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Rendszergazdai fiók létrehozása. Ez a fiók jelentkezik be, és a Jenkins példányt szolgál.

   !["Az első rendszergazdai jogú felhasználó létrehozása" képernyőn lévő hitelesítő adatokkal](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Válassza ki **mentés és Befejezés**, majd válassza ki **Jenkins indíthatja** a konfigurálás befejezéséhez.

Jenkins konfigurálva lett, és hozza létre és kód telepítése kész. Ebben a példában egy egyszerű Java-alkalmazások bemutatása az Azure-tároló példányokon Jenkins build használjuk.

## <a name="create-a-build-job"></a>Összeállítási feladat létrehozása

Használatakor a tároló lemezkép, mint egy Jenkins build target, meg kell adnia egy olyanra, amely tartalmazza az összes tooling eszköz sikeres build szükséges. A kép megadása:

1. Válassza ki **Jenkins kezelése** > **rendszer konfigurálása** és görgessen le a **felhő** szakasz. Ebben a példában a frissítés a Docker kép értéket **microsoft/java-a-azure-jenkins-alárendelt**.

   Amikor elkészült, válassza ki a **mentése** a Jenkins irányítópulton való visszatéréshez.

   ![Jenkins felhőkonfiguráció](./media/container-instances-jenkins/jenkins-aci-image.png)

2. Most hozzon létre egy Jenkins build feladatot. Válassza ki **új elem**, nevezze el a build projekt például **aci-java-bemutató**, jelölje be **Freestyle projekt**, és válassza ki **OK**.

   ![A neve mezőben az összeállítási feladat, és a projekt típusok listája](./media/container-instances-jenkins/jenkins-new-job.png)

3. A **általános**, ügyeljen arra, hogy **korlátozása, amelyben ez a projekt futtathatók** van kiválasztva. Adja meg **linux** a címke kifejezést. Ez a konfiguráció biztosítja, hogy a létrehozási feladat fut-e a ACI felhő.

   !["Általános" lapot a konfiguráció részletei](./media/container-instances-jenkins/jenkins-job-01.png)

4. A **forrás kód felügyeleti**, jelölje be **Git** , és írja be **https://github.com/spring-projects/spring-petclinic.git** a tárház URL-címhez. A GitHub-tárház minta alkalmazás kódját tartalmazza.

   ![Forrás információkat tartalmazó "Forrás-kód felügyeleti" lap](./media/container-instances-jenkins/jenkins-job-02.png)

5. A **Build**, jelölje be **Hozzáadás összeállítása lépés** válassza ki **meghívása a legfelső szintű Maven célok**. Adja meg **csomag** a build lépés célként.

   ![A build lépés választása esetén a "Szerkesztés" lapon](./media/container-instances-jenkins/jenkins-job-03.png)

6. Kattintson a **Mentés** gombra.

## <a name="run-the-build-job"></a>Az összeállítási feladat futtatása

A build tesztfeladat, és tekintse meg az Azure-tároló példányok a build platformként, manuálisan indítsa el a build.

1. Válassza ki **Build most** a build feladat elindításához. A feladat indítása néhány percet vesz igénybe. Az alábbi képen hasonló állapota kell:

   ![Adatok "Összeállítása előzmények" feladat állapota](./media/container-instances-jenkins/jenkins-job-status.png)

2. A feladat futása közben, nyissa meg az Azure-portálon, és tekintse meg a Jenkins erőforráscsoportot. Megtekintheti, hogy létrejött-e a tároló példánya. Ez a példány belül fut a Jenkins feladat.

   ![Az erőforráscsoport tároló-példányra](./media/container-instances-jenkins/jenkins-aci.png)

3. Jenkins Jenkins végrehajtója (alapértelmezett érték 2) konfigurált számánál több feladat fut, mivel több tárolót példány jönnek létre.

   ![Az újonnan létrehozott tároló példányok](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Minden build feladatok után, a rendszer eltávolítja a tároló példányok.

   ![Erőforráscsoport tároló osztályt eltávolítása](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>További lépések

Az Azure-on Jenkins kapcsolatos további információkért lásd: [Azure és Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md