---
author: tomarcher
ms.service: jenkins
ms.topic: include
ms.date: 03/03/2020
ms.author: tarcher
ms.openlocfilehash: 2468dc72881755a2990e8ddf8112d7fe27f64f4d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274878"
---
## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés
* SSH-hozzáférés a számítógép parancssorából (például Bash felület vagy [PuTTY](https://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Jenkins virtuális gép létrehozása a megoldássablonból
A Jenkins támogat egy olyan modellt, amelyben a Jenkins-kiszolgáló egy vagy több ügynökre delegálja a feladatokat, így egyetlen Jenkins-telepítés számos projektet tud futtatni, vagy külön környezeteket tud biztosítani a buildeléshez és teszteléshez. Az ebben a szakaszban ismertetett lépések végigvezetik a Jenkins-kiszolgáló Azure-ban való telepítésének és konfigurálásának folyamatán.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Kapcsolódás a Jenkinshez

Navigáljon a virtuális géphez (például `http://jenkins2517454.eastus.cloudapp.azure.com/`) a böngészőben. A Jenkins-konzol nem érhető el nem biztonságos HTTP-n keresztül. Az oldalon szereplő utasítások szerint, SSH-alagút használatával biztonságosan nyithatja meg a Jenkins-konzolt.

![Jenkins zárolásának feloldása](./media/jenkins-install-solution-template-steps/jenkins-ssh-instructions.png)

Állítsa be az alagutat az `ssh` paranccsal az oldal parancssorából. A `username` helyére a virtuális gép korábban, a virtuális gép megoldássablonból történő beállításakor kiválasztott rendszergazdáját írja be.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Az alagút elindítása után lépjen a `http://localhost:8080/` címre a helyi gépen. 

Kérje le a kezdeti jelszót az alábbi parancs parancssorbeli futtatásával, miközben SSH-n keresztül kapcsolódik a Jenkinst futtató virtuális géphez.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Először a kezdeti jelszóval oldja fel a Jenkins-irányítópult zárolását.

![Jenkins zárolásának feloldása](./media/jenkins-install-solution-template-steps/jenkins-unlock.png)

A következő oldalon válassza az **Install suggested plugins** (Javasolt beépülő modulok telepítése) lehetőséget, majd hozzon létre egy Jenkins-rendszergazdát a Jenkins-irányítópult eléréséhez.

![Jenkins készen áll!](./media/jenkins-install-solution-template-steps/jenkins-welcome.png)

A Jenkins-kiszolgáló mostantól készen áll a kódok előállítására.

## <a name="create-your-first-job"></a>Az első feladat létrehozása

A Jenkins-konzolon válassza a **Create new jobs** (Új feladatok létrehozása) lehetőséget, majd adja neki a **mySampleApp** nevet, és válassza a **Freestyle project** (Szabad stílusú projekt), majd az **OK** lehetőséget.

![Új feladat létrehozása](./media/jenkins-install-solution-template-steps/jenkins-new-job.png) 

A **Source Code Management** (Forráskódkezelés) lapon engedélyezze a **Git** elemet, majd írja be a következő URL-címet a **Repository URL** Adattár URL-címe mezőbe: `https://github.com/spring-guides/gs-spring-boot.git`

![A Git-tárház meghatározása](./media/jenkins-install-solution-template-steps/jenkins-job-git-configuration.png) 

A **Build** (Felépítés) lapon válassza az **Add build step** (Felépítési lépés hozzáadása), majd az **Invoke Gradle Script** (Gradle-szkript meghívása) lehetőséget. Válassza a **Use Gradle Wrapper** (Gradle-burkoló használata) lehetőséget, majd a `complete`Wrapper location **(Burkoló helye) mezőbe írja be a**, a `build`Tasks **(Feladatok) mezőbe pedig a** értéket.

![Gradle-burkoló használata a létrehozáshoz](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-config.png) 

Válassza az **Advanced** (Speciális) lehetőséget, majd adja meg a `complete` értéket a **Root Build script** (Fő felépítési szkript) mezőben. Kattintson a **Mentés** gombra.

![Speciális beállítások megadása a Gradle-burkoló létrehozása lépésben](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>A kód létrehozása

Válassza a **Build Now** (Létrehozás most) lehetőséget a kód fordításához és a mintaalkalmazás becsomagolásához. A build létrehozása után válassza a projekt **Workspace** (Munkaterület) hivatkozását.

![A munkaterület megkeresése a JAR-fájl buildből történő lekérdezéséhez](./media/jenkins-install-solution-template-steps/jenkins-access-workspace.png) 

Navigáljon a `complete/build/libs` könyvtárhoz, és a létrehozás sikerességének ellenőrzéséhez győződjön meg arról, hogy a `gs-spring-boot-0.1.0.jar` fájl megtalálható benne. A Jenkins-kiszolgáló mostantól készen áll saját Azure-projektek létrehozására.

## <a name="troubleshooting-the-jenkins-solution-template"></a>A Jenkins-megoldássablon hibaelhárítása

Ha a Jenkins-megoldássablon használata során bármilyen hibát tapasztal, jelentse be a problémát a [Jenkins GitHub-adattárában](https://github.com/azure/jenkins/issues).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek hozzáadása Jenkins-ügynökökként](/azure/jenkins-azure-vm-agents)