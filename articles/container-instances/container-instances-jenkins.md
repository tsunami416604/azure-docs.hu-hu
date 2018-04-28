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
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Használata Azure tároló példányok, egy Jenkins build ügynök

Azure-tárolót példányok indexelése munkaterhelést futtató igény, burstable és elkülönített környezetet biztosíthat. Miatt ezek az attribútumok Azure tároló példányok ellenőrizze a futó Jenkins build feladatok nagy léptékű kiváló platform. Ez a dokumentum végigvezeti a központi telepítésével és használatával olyan Jenkins kiszolgálóra, amely az összeállítási cél ACI előre konfigurálva van.

Azure-tároló példányokon további információkért lásd: [kapcsolatos Azure tároló példányok][about-aci].

## <a name="deploy-jenkins-server"></a>Jenkins kiszolgáló központi telepítése

Válassza ki az Azure-portálon **hozzon létre egy erőforrást** keresse meg a **Jenkins**. Válassza ki az Jenkins elérhető, a közzétevő **Microsoft** válassza **létrehozása**.

Adja meg a következő információkat az alapvető űrlapon, és kattintson a **OK** végzett.

- **Név** - az Jenkins üzemelő példány neve.
- **Felhasználónév** -ezt a felhasználónevet a rendszergazdai jogú felhasználó szolgál a Jenkins virtuális géphez.
- **Hitelesítés típusa** - SSH nyilvános kulcs használata ajánlott. Ha választotta, másolja át egy nyilvános SSH-kulcsot, amikor bejelentkezik a Jenkins virtuális géphez használandó.
- **Előfizetés** -válassza ki az Azure-előfizetéssel.
- **Erőforráscsoport** – hozzon létre egy új, vagy válasszon ki egy meglévő erőforráscsoportot.
- **Hely** -jelöljön ki egy helyet a Jenkins kiszolgáló.

![Jenkins portál telepítési alapbeállítások](./media/container-instances-jenkins/jenkins-portal-01.png)

A további beállítások képernyőn hajtsa végre a következő elemek:

- **Méret** -válassza ki a Jenkins virtuális gép megfelelő méretezési beállítását.
- **Virtuális gép lemeztípus** -adja meg a HDD (merevlemez-meghajtó) vagy SSD (SSD-meghajtóra) a Jenkins kiszolgáló.
- **Virtuális hálózati** -(nem kötelező) jelölje ki virtuális hálózatot az alapértelmezett beállítások módosításával.
- **Alhálózatok** - alhálózatok, ellenőrizze az adatokat válassza ki és **OK**.
- **Nyilvános IP-cím** -kiválasztása a nyilvános IP-cím lehetővé teszi egyéni nevet, SKU, és a hozzárendelés módszer konfigurálása.
- **Tartománynév-címke** -adjon meg egy értéket, abszolút URL-CÍMÉT a Jenkins virtuális gép létrehozásához.
- **Jenkins kiadás típusa** -válassza ki a kívánt kiadási típusainak a lehetőségek közül: LTS, heti build vagy Azure ellenőrzése.

![További beállítások Jenkins-portál telepítése](./media/container-instances-jenkins/jenkins-portal-02.png)

Válassza a szolgáltatás egyszerű integráció, **Auto(MSI)** kell rendelkeznie [Azure felügyelt Szolgáltatásidentitás] [ managed-service-identity] automatikus létrehozása egy hitelesítési identitás Jenkins példány. Válassza a kézi szolgáltatóhoz saját szolgáltatás egyszerű hitelesítő adatait.

Felhő ügynökök konfigurálása egy felhőalapú platform Jenkins build feladatok. Az ebben a dokumentumban ACI kiválasztása ACI felhő ügynök minden egyes Jenkins létrehozási feladat fut egy Azure-tároló példány.

![Jenkins portál telepítési felhő integrációs beállítások](./media/container-instances-jenkins/jenkins-portal-03.png)

Ha az integrációs beállításokat befejezése után kattintson **OK**, majd **OK** az érvényesítési összegzés meg újra. Kattintson a **létrehozása** használata összegzés feltételeinek. A Jenkins kiszolgáló központi telepítése néhány percet vesz igénybe.

## <a name="configure-jenkins"></a>A Jenkins konfigurálása

Az Azure portálon keresse meg a Jenkins erőforráscsoportot, válassza ki a Jenkins virtuális gépet, és jegyezze fel a DNS-nevét.

![Jenkins bejelentkezési utasítások](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

A DNS-nevét a Jenkins VM, másolja a visszaadott SSH karakterláncot böngészőben.

![Jenkins bejelentkezési utasítások](./media/container-instances-jenkins/jenkins-portal-04.png)

Nyissa meg a fejlesztői rendszeren egy terminál-munkamenetet, és illessze be az utolsó lépésben SSH-karakterláncban. Frissítse a Jenkins kiszolgáló telepítésekor megadott felhasználónévnek "username adat".

A csatlakozás után a következő parancsot a kezdő adminisztrációs jelszó lekérése.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Hagyja meg az SSH-munkamenetet, és alagút fut, és navigáljon a http://localhost:8080 a böngészőben. A mező az alábbi képen látható módon illessze be a kezdeti rendszergazdai jelszavát. Válassza ki **Folytatás** végzett.

![Jenkins feloldása](./media/container-instances-jenkins/jenkins-portal-05.png)

Válassza ki **javasolt beépülő modulok telepítése** telepítheti az összes ajánlott Jenkins beépülő modulok.

![Jenkins beépülő modul telepítése](./media/container-instances-jenkins/jenkins-portal-06.png)

Hozzon létre egy új rendszergazdai felhasználói fiókot. Ez a fiók való bejelentkezés, és a Jenkins példányt szolgál.

![Jenkins felhasználó létrehozása](./media/container-instances-jenkins/jenkins-portal-07.png)

Válassza ki **mentés és Befejezés** végzett, majd **Jenkins indíthatja** a konfigurálás befejezéséhez.

Jenkins konfigurálva lett, és hozza létre és kód telepítése kész. Ebben a példában egy egyszerű Java-alkalmazások bemutatása az Azure-tároló példányokon Jenkins build használjuk.

## <a name="create-build-job"></a>Összeállítási feladat létrehozása

Ha a tároló-lemezkép használata a Jenkins build target, meg kell adnia egy olyanra, amely tartalmazza az összes tooling eszköz sikeres build szükséges. A képet, adja meg **kezelése Jenkins** > **rendszer konfigurálása** és görgessen le a **felhő** szakasz. Ebben a példában a frissítés a Docker kép értéket `microsoft/java-on-azure-jenkins-slave`.

Ha végzett, kattintson a **mentése** a Jenkins irányítópulton való visszatéréshez.

![Jenkins felhőkonfiguráció](./media/container-instances-jenkins/jenkins-aci-image.png)

Most hozzon létre egy Jenkins build feladatot. Válassza ki **új elem**, nevezze el a build projekt például `aci-java-demo`, jelölje be **Freestyle projekt**, és kattintson a **OK**.

![Jenkins-feladat létrehozása](./media/container-instances-jenkins/jenkins-new-job.png)

A **általános**, ügyeljen arra, hogy **korlátozása, amelyben ez a projekt futtathatók** van kiválasztva. Adja meg `linux` a címke kifejezést. Ez a konfiguráció biztosítja, hogy a létrehozási feladat fut-e a ACI felhő.

![Jenkins-feladat létrehozása](./media/container-instances-jenkins/jenkins-job-01.png)

A kód forráskezelés területen válassza a `Git` , és írja be `https://github.com/spring-projects/spring-petclinic.git` a tárház URL-címhez. A GitHub-tárház minta alkalmazás kódját tartalmazza.

![Forráskód Jenkins feladat hozzáadása](./media/container-instances-jenkins/jenkins-job-02.png)

A Build, válassza a **összeállítása lépés hozzáadása** válassza `Invoke top-level Maven targets`. Adja meg `package` a build lépés célként.

![Adja hozzá a Jenkins összeállítása lépés](./media/container-instances-jenkins/jenkins-job-03.png)

Válassza ki **mentése** végzett.

## <a name="run-the-build-job"></a>Az összeállítási feladat futtatása

A build tesztfeladat, és tekintse meg az Azure-tároló példányok a build platformként, manuálisan indítsa el a build.

Válassza ki **Build most** a build feladat elindításához. A feladat futtatásakor elindításához néhány percet vesz igénybe, megtekintheti az állapot a következő lemezképek hasonló.

![Adja hozzá a Jenkins összeállítása lépés](./media/container-instances-jenkins/jenkins-job-status.png)

A feladat futása közben, nyissa meg az Azure-portálon, és tekintse meg a Jenkins erőforráscsoportot. Megtekintheti, hogy létrejött-e egy Azure-tároló példányát. Ez a példány, hogy fut-e a Jenkins feladat belül van.

![A ACI Jenkins buildek](./media/container-instances-jenkins/jenkins-aci.png)

Jenkins Jenkins végrehajtója (alapértelmezett érték 2) konfigurált számánál több feladat fut, mert több Azure-tároló példány jönnek létre.

![A ACI Jenkins buildek](./media/container-instances-jenkins/jenkins-aci-multi.png)

Minden build feladatok elvégzése után, a rendszer eltávolítja az Azure-tárolót példányok.

![A ACI Jenkins buildek](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>További lépések

További információt Jenkins Azure lásd a [Azure és Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md