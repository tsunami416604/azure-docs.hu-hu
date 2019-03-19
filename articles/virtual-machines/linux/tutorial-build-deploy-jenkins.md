---
title: Oktatóanyag – CI/CD a Jenkinsből az Azure-beli virtuális gépekre az Azure DevOps Services szolgáltatással | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogyan állíthatja be a Node.js-alkalmazás folyamatos integrációját (CI) és folyamatos üzembe helyezését (CD) a Azure virtuális gépekre a Jenkins használatával a Visual Studio Team Services kiadáskezeléséből vagy a Microsoft Team Foundation Serveren
author: tomarchermsft
manager: jpconnock
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: jenkins
ms.workload: infrastructure
ms.date: 07/31/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 7cd7b8f7b49915db9fcf17602429e47c1b9da95d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901423"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-azure-devops-services"></a>Oktatóanyag: Alkalmazás üzembe helyezése az Azure-beli Linux rendszerű virtuális gépek használatával a Jenkins és az Azure DevOps-szolgáltatásokkal

Folyamatos integráció (CI) és a folyamatos üzembe helyezés (CD) egy folyamatból, amellyel építheti, kiadhatja és üzembe helyezheti a kódot. Az Azure DevOps Services teljes funkcionalitású CI-/CD-automatizálási eszközöket biztosít az Azure-ban történő üzembe helyezéshez. A Jenkins egy olyan népszerű, harmadik féltől származó CI/CD szolgáltatást nyújtó, kiszolgálóalapú eszköz, amely CI/CD-automatizálást is biztosít. Az Azure DevOps Services és a Jenkins együttes használatával testre szabhatja, hogyan kézbesíti felhőalapú alkalmazását vagy szolgáltatását.

Ebben az oktatóanyagban a Jenkinst fogja használni Node.js-alapú webalkalmazás építésére. Ezután az Azure DevOps használatával üzembe helyezi azt

egy Linux rendszerű virtuális gépeket tartalmazó [üzembehelyezési csoportban](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups/index?view=vsts). Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Mintaalkalmazás letöltése.
> * A Jenkins bővítményeinek konfigurálása.
> * Jenkins Freestyle projekt konfigurálása a Node.js-hez.
> * A Jenkins konfigurálása az Azure DevOps Services-el való integrációhoz.
> * Jenkins szolgáltatási végpont létrehozása.
> * Üzembe helyezési csoport létrehozása az Azure virtuális gépekhez.
> * Hozzon létre egy Azure-folyamatok kiadási folyamatot.
> * Manuális és CI által kiváltott üzembe helyezések végrehajtása.

## <a name="before-you-begin"></a>Előkészületek

* Egy Jenkins-kiszolgálóhoz történő hozzáférés szükséges. Ha még nem hozott létre Jenkins-kiszolgálót, lásd: [Jenkins-főkiszolgáló létrehozása Azure virtuális gépen](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Jelentkezzen be az Azure DevOps Services-szervezetbe (**https://{sajátszervezet}.visualstudio.com**). 
  Beszerezhet egy [ingyenes Azure DevOps Services-szervezetet](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > További információ: [Csatlakozás az Azure DevOps Services-hez](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Telepítési célként egy Linux rendszerű virtuális gépre van szükség.  További információért lásd: [Linux rendszerű virtuális gépek létrehozása és kezelése az Azure CLI-vel](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Nyissa meg a virtuális géphez a 80-as a bejövő portot. További információ: [Hozzon létre egy hálózati biztonsági csoportot az Azure Portal használatával](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-the-sample-app"></a>Mintaalkalmazás letöltése

A telepítéshez szükséges egy Git-adattárban lévő alkalmazás.
A jelen oktatóanyag esetében azt javasoljuk, hogy [ezt a mintaalkalmazást használja, amely a Githubon érhető el](https://github.com/azooinmyluggage/fabrikam-node). Ez az oktatóanyag tartalmaz egy szkriptet, amelyet a Node.js és egy alkalmazás telepítésére fog használni. Ha szeretne a saját adattárával dolgozni, konfigurálnia kell egy hasonló mintát.

Hozzon létre egy adattármásolatot az alkalmazáshoz, és jegyezze le a helyet (URL-címet) az oktatóanyag későbbi lépéseiben való felhasználáshoz. További információkat a [Fork a repo](https://help.github.com/articles/fork-a-repo/) (Adattármásolat készítése) témakörben talál.    

> [!NOTE]
> Az alkalmazás a [Yeoman](https://yeoman.io/learning/index.html) használatával lett létrehozva. Express-t bowert és gruntot használ. És rendelkezik néhány npm-csomaggal függőségként.
> A minta egy szkriptet is tartalmaz, amely beállítja az Nginx kiszolgálót, és üzembe helyezi az alkalmazást. A végrehajtása a virtuális gépeken történik. Pontosabban a szkript az alábbi műveleteket végzi:
> 1. Telepíti a Node-ot, az Nginx kiszolgálót és a PM2-t.
> 2. Konfigurálja az Nginx kiszolgálót és a PM2-t.
> 3. Elindítja a Node alkalmazást.

## <a name="configure-jenkins-plug-ins"></a>A Jenkins beépülő moduljainak konfigurálása

Először konfigurálnia kell két Jenkins beépülő: **NodeJS** és **VS Team Services, a folyamatos üzembe helyezés**.

1. Nyissa meg a Jenkins-fiókját, és válassza a **Manage Jenkins** (Jenkins kezelése) lehetőséget.
2. A **Manage Jenkins** (Jenkins kezelése) oldalon válassza a **Manage Plugins** (Beépülő modulok kezelése) lehetőséget.
3. A lista szűrésével keresse meg a **NodeJS** beépülő modult, majd válassza az **Install without restart** (Telepítés újraindítás nélkül) lehetőséget.
    ![A NodeJS beépülő modul hozzáadása a Jenkinshez](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. A lista szűrésével keresse meg a **VS Team Services Continuous Deployment** beépülő modult, és válassza az **Install without restart** (Telepítés újraindítás nélkül) lehetőséget.
5. Lépjen vissza a Jenkins irányítópultjára, és válassza a **Manage Jenkins** (Jenkins kezelése) lehetőséget.
6. Válassza a **Global Tool Configuration** (Globális eszköz konfigurációja) lehetőséget. Keresse meg a **NodeJS** elemet, és válassza a **NodeJS installations** (NodeJS-telepítések) lehetőséget.
7. Válassza az **Install automatically** (Automatikus telepítés) lehetőséget, majd adja meg a **Name** (Név) értékét.
8. Kattintson a **Mentés** gombra.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Jenkins Freestyle projekt konfigurálása a Node.js-hez

1. Válassza a **New Item** (Új elem) lehetőséget. Adja meg az elem nevét.
2. Válassza a **Freestyle project** lehetőséget. Kattintson az **OK** gombra.
3. A **Source Code Management** (Forráskód kezelése) lapon válassza a **Git** lehetőséget, és adja meg annak az adattárnak és elágazásnak az adatait, amely az alkalmazás forráskódját tartalmazza.    
    ![Az adattár hozzáadása a buildhez](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Válassza a **Build Triggers** (Build-aktiválók) lapon a **Poll SCM** lehetőséget, és adja meg a `H/03 * * * *` ütemtervet a Git-adattár változásainak három percenként történő ciklikus lekérdezéshez. 
5. Válassza a **Build Environment** (Build-környezet) lapon a **Provide Node &amp; npm bin/ folder PATH** (Node és npm bin/ mappa elérési útjának megadása) lehetőséget, és válassza a **NodeJS Installation** (NodeJS-telepítés) értéket. Hagyja meg az **npmrc-fájl** beállításaként a **use system default** (rendszer alapértelmezett beállításainak használata) értéket.
6. A **Build** lapon válassza az **Execute shell** (Végrehajtási felület) lehetőséget, és adja meg az `npm install` parancsot, hogy minden függőség frissítése megtörténjen.


## <a name="configure-jenkins-for-azure-devops-services-integration"></a>A Jenkins konfigurálása az Azure DevOps Services-el való integrációhoz.

> [!NOTE]
> Ellenőrizze, hogy a következő lépésben használt személyes hozzáférési jogkivonat (PAT) tartalmazza-e a *Kiadási* (olvasási, írási, végrehajtási és kezelései) engedélyeket az Azure DevOps Services-ben.
 
1.  Hozzon létre egy PAT-et az Azure DevOps Services-szervezetben, ha még nem rendelkezik ilyennel. A Jenkinsnek szüksége van erre az információra az Azure DevOps Services-szervezet eléréséhez. Ne feledje tárolni a jogkivonatadatokat a szakaszban következő lépésekhez.
  
    A jogkivonat létrehozásához olvassa el a [How do I create a personal access token for Azure DevOps Services?](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts) (Hogyan hozhatok létre személyes hozzáférési jogkivonatot az Azure DevOps Services-hez?) oldalt.
2. A **Post-build Actions** (Felépítés utáni műveletek) lapon válasza az **Add post-build action** (Felépítés utáni művelet hozzáadása) lehetőséget. Válassza az **Archive the artifacts** (Összetevők archiválása) lehetőséget.
3. A **Files to archive** (Archiválandó fájlok) beállításához adja meg a `**/*` értéket az összes fájl belefoglalásához.
4. Egy másik művelet létrehozásához válassza az **Add post-build action** (Felépítés utáni művelet hozzáadása) lehetőséget.
5. Válassza a **Trigger release in TFS/Team Services** (Kiadás aktiválása a TFS/Team Services-ben) lehetőséget. Adja meg az Azure DevOps Services-szervezet URI-ját, például a **https://{saját-szervezet-neve}.visualstudio.com** címet.
6. Adja meg a **projekt** nevét.
7. Adjon nevet a kiadási folyamatnak. (Ezt a kiadási folyamatot később fogja létrehozni az Azure DevOps Services-ben.)
8. Válassza ki a hitelesítő adatokat az Azure DevOps Services vagy a Team Foundation Server környezethez való csatlakozáshoz:
   - Hagyja a **Username** (Felhasználónév) mezőt üresen, ha az Azure DevOps Services-t használja. 
   - Ha a Team Foundation Server egy helyszíni verzióját használja, adja meg a felhasználónevet és jelszót.    
   ![A Jenkins felépítés utáni műveleteinek konfigurálása](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Mentse a Jenkins-projektet.


## <a name="create-a-jenkins-service-endpoint"></a>Jenkins szolgáltatási végpont létrehozása

A szolgáltatási végpont lehetővé teszi, hogy az Azure DevOps Services csatlakozzon a Jenkinshez.

1. Nyissa meg a **Services** (Szolgáltatások) lapot az Azure DevOps Services-ben, nyissa meg a **New Service Endpoint** (Új szolgáltatási végpont) listát, és válassza a **Jenkins** lehetőséget.
   ![Jenkins-végpont felvétele](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Adjon egy nevet a kapcsolatnak.
3. Adja meg a Jenkins-kiszolgáló URL-címét, majd válassza ki a **Nem megbízható SSL-tanúsítványok elfogadása** lehetőséget. Példa URL-címre: **http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com**.
4. Adja meg a Jenkins-fiók felhasználónevét és jelszavát.
5. Válassza a **Kapcsolat ellenőrzése** lehetőséget az adatok helyességének ellenőrzéséhez.
6. Kattintson az **OK** gombra a szolgáltatásvégpont létrehozásához.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Üzembe helyezési csoport létrehozása Azure virtuális gépekhez

Szüksége van egy [üzembe helyezési csoportra](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) az Azure DevOps Services-ügynök regisztrálásához, hogy a kiadási folyamatot üzembe lehessen helyezni a virtuális gépen. Az üzembe helyezési csoportok megkönnyítik a célgépek logikai csoportjainak megadását az üzembe helyezéshez és a kívánt ügynök egyes gépeken történő telepítéséhez.

   > [!NOTE]
   > A következő eljárásban nem mulassza el az előfeltételek telepítését, és *ne futtassa a szkriptet sudo engedélyekkel.*

1. Nyissa meg a **Build &amp; Release** (Build és kiadás) központ **Releases** (Kiadások) lapját, nyissa meg a **Deployment groups** (Üzembe helyezési csoportok) elemet, és válassza a **+ New** (+ Új) lehetőséget.
2. Adjon meg egy nevet a központi telepítési csoporthoz, és egy opcionális leírást. Ezután kattintson a **Létrehozás** elemre.
3. Válassza ki az üzembe helyezési cél virtuális gépének operációs rendszerét. Válassza például az **Ubuntu 16.04+** lehetőséget.
4. Válassza ki a **Use a personal access token in the script for authentication** (Személyes hozzáférési jogkivonat használata a szkriptben a hitelesítéséhez) elemet.
5. Válassza a **System prerequisites** (Rendszer előfeltételei) hivatkozást. Telepítse az előfeltételeket az operációs rendszeréhez.
6. Válassza a **Copy script to clipboard** (Szkript másolása a vágólapra) elemet a szkript másolásához.
7. Jelentkezzen be az üzembe helyezési cél virtuális gépére, és futtassa a szkriptet. Ne futtassa a szkriptet a sudo engedélyekkel.
8. A telepítés után a rendszer kéri az üzembe helyezési csoport címkéit. Fogadja el az alapértelmezett beállításokat.
9. Az Azure DevOps Services-ben ellenőrizze az újonnan regisztrált virtuális gépet a **Targets** (Célok) szakaszban a **Deployment Groups** (Üzembehelyezési csoportok) alatt.

## <a name="create-an-azure-pipelines-release-pipeline"></a>Hozzon létre egy Azure-folyamatok kibocsátási folyamatok

A kiadási folyamat azt a folyamatot határozza meg, amelyet az Azure Pipelines az alkalmazás üzembe helyezésére használ. Ebben a példában egy felületszkriptet fog végrehajtani.

Kiadási folyamat létrehozása az Azure Pipelinesban:

1. Nyissa meg a **Build &amp; Release** (Build és kiadás) központ **Releases** (Kiadások) lapját, és válassza a **Create release pipeline** (Kiadási folyamat létrehozása) elemet. 
2. Válassza ki az **Empty** (Üres) sablont, hogy egy **Empty process** (Üres folyamat) legyen a kezdési folyamat.
3. Az **Artifacts** (Összetevők) szakaszban jelölje ki a **+ Add Artifact** (+ Összetevő hozzáadása) lehetőséget, és válassza a **Jenkins** elemet, mint **Source type** (Erőforrástípus). Válassza ki a Jenkins szolgáltatási végpont kapcsolatát. Ezután jelölje ki a Jenkins-forrásfeladatot, és válassza az **Add** (Hozzáadás) lehetőséget.
4. Válassza a három pontot az **Environment 1** (1. környezet) mellett. Válassza az **Add deployment group phase** (Üzembe helyezési csoport fázisának hozzáadása) lehetőséget.
5. Válassza ki az üzembe helyezési csoportot.
5. Válassza a **+** lehetőséget egy feladat **Üzembe helyezési csoport fázishoz** adásához.
6. Válassza a **Shell Script** (Felületszkript) feladatot, majd az **Add** (Hozzáadás) elemet. A **Shell Script** (Felületszkript) feladat biztosítja az egyes kiszolgálókon való futtatáshoz a szkript konfigurációját a Node.js telepítéséhez és az alkalmazás elindításához.
8. A **Script Path** (Szkript elérési útja) mezőben adja meg a **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh** útvonalat.
9. Válassza az **Advanced** (Speciális) elemet, majd engedélyezze a **Specify Working Directory** (Munkakönyvtár megadása) lehetőséget.
10. A **Working Directory** (Munkakönyvtár) mezőben adja meg a **$(System.DefaultWorkingDirectory)/Fabrikam-Node** útvonalat.
11. A kiadási folyamat neveként adja meg a buildhez a Jenkinsben a **Post-build Actions** (Felépítés utáni műveletek) lapon meghatározott nevet. A Jenkinsnek erre a névre lesz szüksége új kiadás aktiválásához a forrásösszetevők frissítésekor.
12. Válassza a **Save** (Mentés), majd az **OK** gombot a kiadási folyamat mentéséhez.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Manuális és CI által kiváltott üzembe helyezések végrehajtása

1. Válassza a **+ Release** (+ Kiadás) elemet, majd a **Create Release** (Kiadás létrehozása) lehetőséget.
2. A kiemelt legördülő listában válassza ki a befejezett buildet, majd válassza a **Queue** (Várólista) lehetőséget.
3. Kattintson az előugró üzenetben a kiadási hivatkozásra. Példa: "Release **Release-1** létrejött."
4. Nyissa meg a **Logs** (Naplók) lapot a kiadási konzol kimenetének megtekintéséhez.
5. A böngészőben nyissa meg az üzembe helyezési csoporthoz hozzáadott egyik kiszolgáló URL-címét. Adja meg például a **http://{your-server-ip-address}** címet.
6. Lépjen a Git-adattárba, és módosítsa a **h1** fejléc tartalmát az app/views/index.jade fájlban egy módosított szövegre.
7. Véglegesítse a módosításokat.
8. Néhány perc elteltével megjelenik a létrehozott új kiadás az Azure DevOps **Releases** (Kiadások) lapján. Nyissa meg a kiadást az üzembe helyezési folyamat megtekintéséhez. Gratulálunk!

## <a name="troubleshooting-the-jenkins-plugin"></a>A Jenkins beépülő modul hibáinak elhárítása

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban automatizálta az alkalmazás Azure-ban történő üzembe helyezését a Jenkinst használva a buildhez, az Azure DevOps Services-t pedig a kiadáshoz. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Alkalmazás létrehozása a Jenkinsben.
> * A Jenkins konfigurálása az Azure DevOps Services-el való integrációhoz.
> * Üzembe helyezési csoport létrehozása az Azure virtuális gépekhez.
> * Olyan kiadási folyamat létrehozása, amely konfigurálja a virtuális gépeket, és üzembe helyezi az alkalmazást.

Annak elsajátításához, hogyan helyezhet üzembe LAMP (Linux, Apache, MySQL és PHP) stacket, lépjen tovább a következő oktatóanyagra.

> [!div class="nextstepaction"]
> [LAMP stack telepítése](tutorial-lamp-stack.md)
