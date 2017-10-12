---
title: "Jenkins-kiszolgáló létrehozása az Azure-on"
description: "Jenkins-kiszolgáló telepítése egy Azure-beli linuxos virtuális gépen a Jenkins-megoldássablonból és egy Java-mintaalkalmazás létrehozása."
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: hero-article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 7bb74f297d52fb25171817175cce64187b397c38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Jenkins-kiszolgáló létrehozása Azure-beli linuxos virtuális gépen az Azure Portalról

Ez a rövid útmutató bemutatja, hogyan telepítheti a [Jenkins](https://jenkins.io)-kiszolgálót Ubuntu Linux rendszerű virtuális gépre az Azure-ral való együttműködésre konfigurált eszközökkel és beépülő modulokkal együtt. Ha elkészült, rendelkezni fog egy Azure-ban futó Jenkins-kiszolgálóval, amely egy Java-mintaalkalmazást hoz létre a [GitHubról](https://github.com).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés
* SSH-hozzáférés a számítógép parancssorából (például Bash felület vagy [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Jenkins virtuális gép létrehozása a megoldássablonból

Nyissa meg a [Jenkins áruházbeli rendszerképét](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview) a webböngészőben, és az oldal bal oldalán válassza a **LETÖLTÉS MOST** lehetőséget. Tekintse át a díjszabás részleteit, és válassza a **Folytatás**, majd a **Létrehozás** lehetőséget a Jenkins-kiszolgáló Azure Portalon történő konfigurálásához. 
   
![Azure Portal párbeszédpanel](./media/install-jenkins-solution-template/ap-create.png)

**Az alapszintű beállítások konfigurálása** lapon töltse ki a következő mezőket:

![Az alapvető beállítások konfigurálása](./media/install-jenkins-solution-template/ap-basic.png)

* Írja be a **Jenkins** értéket a **Név** mezőbe.
* Írjon be egy **felhasználónevet**. A felhasználónévnek [egyedi követelményeknek](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) kell megfelelnie.
* A **Hitelesítés típusa** mezőben válassza a **Jelszó** lehetőséget, majd adjon meg egy jelszót. A jelszónak legalább egy nagybetűs karaktert, egy számot és egy speciális karaktert is tartalmaznia kell.
* Az **Erőforráscsoport** mezőben adja meg a **myJenkinsResourceGroup** kifejezést.
* A **Hely** legördülő menüből válassza az **USA keleti régiója** [Azure-régiót](https://azure.microsoft.com/regions/).

Az **OK** lehetőség kiválasztásával lépjen tovább a **További beállítások konfigurálása** lapra. Adjon meg egy egyedi tartománynevet a Jenkins-kiszolgáló azonosításához, majd válassza az **OK** lehetőséget.

![További beállítások megadása](./media/install-jenkins-solution-template/ap-addtional.png)  

 Az ellenőrzés után válassza ismét az **OK** lehetőséget az **Összefoglalás** lapon. Végül válassza a **Vásárlás** lehetőséget a Jenkins virtuális gép létrehozásához. Ha a kiszolgáló készen áll, értesítést fog kapni az Azure Portalon:   

![„A Jenkins készen áll” értesítés](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## <a name="connect-to-jenkins"></a>Kapcsolódás a Jenkinshez

A webböngészőjében nyissa meg a virtuális gépet (például: http://jenkins2517454.eastus.cloudapp.azure.com/). A Jenkins-konzol nem érhető el nem biztonságos HTTP-n keresztül. Az oldalon szereplő utasítások szerint, SSH-alagút használatával biztonságosan nyithatja meg a Jenkins-konzolt.

![Jenkins zárolásának feloldása](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Állítsa be az alagutat az `ssh` paranccsal az oldal parancssorából. A `username` helyére a virtuális gép korábban, a virtuális gép megoldássablonból történő beállításakor kiválasztott rendszergazdáját írja be.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Az alagút elindítása után lépjen a http://localhost:8080/ címre a helyi gépen. 

Kérje le a kezdeti jelszót az alábbi parancs parancssorbeli futtatásával, miközben SSH-n keresztül kapcsolódik a Jenkinst futtató virtuális géphez.

```bash
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
```

Először a kezdeti jelszóval oldja fel a Jenkins-irányítópult zárolását.

![Jenkins zárolásának feloldása](./media/install-jenkins-solution-template/jenkins-unlock.png)

A következő oldalon válassza az **Install suggested plugins** (Javasolt beépülő modulok telepítése) lehetőséget, majd hozzon létre egy Jenkins-rendszergazdát a Jenkins-irányítópult eléréséhez.

![Jenkins készen áll!](./media/install-jenkins-solution-template/jenkins-welcome.png)

A Jenkins-kiszolgáló mostantól készen áll a kódok előállítására.

## <a name="create-your-first-job"></a>Az első feladat létrehozása

A Jenkins-konzolon válassza a **Create new jobs** (Új feladatok létrehozása) lehetőséget, majd adja neki a **mySampleApp** nevet, és válassza a **Freestyle project** (Szabad stílusú projekt), majd az **OK** lehetőséget.

![Új feladat létrehozása](./media/install-jenkins-solution-template/jenkins-new-job.png) 

A **Source Code Management** (Forráskódkezelés) lapon engedélyezze a **Git** elemet, majd írja be a következő URL-címet a **Repository URL** Adattár URL-címe mezőbe: `https://github.com/spring-guides/gs-spring-boot.git`

![A Git-tárház meghatározása](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

A **Build** (Felépítés) lapon válassza az **Add build step** (Felépítési lépés hozzáadása), majd az **Invoke Gradle Script** (Gradle-szkript meghívása) lehetőséget. Válassza a **Use Gradle Wrapper** (Gradle-burkoló használata) lehetőséget, majd a **Wrapper location** (Burkoló helye) mezőbe írja be a `complete`, a **Tasks** (Feladatok) mezőbe pedig a `build` értéket.

![Gradle-burkoló használata a létrehozáshoz](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Válassza az **Advanced...** (Speciális) lehetőséget, majd adja meg a `complete` értéket a **Root Build script** (Fő felépítési szkript) mezőben. Kattintson a **Mentés** gombra.

![Speciális beállítások megadása a Gradle-burkoló létrehozása lépésben](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>A kód létrehozása

Válassza a **Build Now** (Létrehozás most) lehetőséget a kód fordításához és a mintaalkalmazás becsomagolásához. A build létrehozása után válassza a projekt **Workspace** (Munkaterület) hivatkozását.

![A munkaterület megkeresése a JAR-fájl buildből történő lekérdezéséhez](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Navigáljon a `complete/build/libs` könyvtárhoz, és a létrehozás sikerességének ellenőrzéséhez győződjön meg arról, hogy a `gs-spring-boot-0.1.0.jar` fájl megtalálható benne. A Jenkins-kiszolgáló mostantól készen áll saját Azure-projektek létrehozására.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek hozzáadása Jenkins-ügynökökként](jenkins-azure-vm-agents.md)
