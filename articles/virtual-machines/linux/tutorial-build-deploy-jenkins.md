---
title: "CI/CD Jenkins az Azure virtuális gépek VSTS |} Microsoft Docs"
description: "Folyamatos integrációt (CI) és a folyamatos üzembe helyezés (CD) Jenkins segítségével Azure virtuális gépeken futó szolgáltatásban a Visual Studio Team Services (VSTS) vagy a Microsoft Team Foundation Server (TFS) a Node.js-alkalmazás beállítása"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: d5c15d6ab36a8454d1c69bac498be89b990c7e33
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-vsts"></a>Linux virtuális gépek Jenkins és VSTS az alkalmazás üzembe helyezése

Folyamatos integrációt (CI) és a folyamatos üzembe helyezés (CD) nem egy folyamatot, amely létre, a kiadási és a kód telepítésére. A Visual Studio Team Services (VSTS) biztosít teljes, teljes funkcionalitású CI/CD automatizálási eszközeivel központi telepítés az Azure-bA. Jenkins egy olyan népszerű 3. fél CI/CD server-alapú eszköz, amely CI/CD automation is biztosít. Segítségével mindkét együtt a felhőalapú alkalmazás vagy szolgáltatás biztosításához hogyan testreszabása.

Ebben az oktatóanyagban használ Jenkins hozhat létre egy **Node.js webalkalmazás**, és VSTS vagy Team Foundation Server (TFS) telepítheti azt egy [üzembe helyezési csoport](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) Linux virtuális gépeket tartalmazó.

Az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * A minta-alkalmazás letöltése
> * Jenkins beépülő modulok konfigurálása
> * A Node.js Jenkins Freestyle projekt konfigurálása
> * Jenkins VSTS-integráció konfigurálása
> * Hozzon létre egy Jenkins szolgáltatási végpont
> * Az Azure virtuális gépek telepítési csoport létrehozása
> * Egy kiadási VSTS-definíció létrehozása
> * Végrehajtási manuális és CI indított központi telepítések

## <a name="before-you-begin"></a>Előkészületek

* Jenkins kiszolgálóra történő hozzáférés szükséges. Ha még nem hozott létre egy Jenkins server, lásd: [Jenkins fő létrehozása az Azure virtuális gép](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template). 

* Jelentkezzen be a VSTS-fiókjába (`https://{youraccount}.visualstudio.com`). 
  Beszerezheti a [ingyenes VSTS-fiókot](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > További információkért lásd: [VSTS kapcsolódás](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  Linux virtuális gépek központi telepítés célja van szükség.  További információkért lásd: [létrehozása és kezelése a Linux virtuális gépek az Azure parancssori felülettel](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm).

*  Nyissa meg a virtuális gép a 80-as a bejövő portot.  További információkért lásd: [létrehozása az Azure portál használatával a hálózati biztonsági csoportok](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>A minta-alkalmazás letöltése

Kell telepíteni kívánt alkalmazást, a Git-tárház tárolja.
A jelen oktatóanyag esetében javasoljuk, használjon [a mintaalkalmazás elérhető a Githubon](https://github.com/azooinmyluggage/fabrikam-node).  Ez az oktatóanyag egy minta parancsfájlt, Node.js és egy alkalmazás telepítése használatával tartalmazza.  Ha szeretne dolgozni a saját tárház, konfigurálnia kell egy hasonló mintát.

1. Hozzon létre egy elágazás az alkalmazáshoz, és tekintse meg a helye (URL) számára ez az oktatóanyag későbbi lépéseiben.  További információkért lásd: [oszthatja ketté a tárházban](https://help.github.com/articles/fork-a-repo/)    

> [!NOTE]
> Az alkalmazás használatával lett létrehozva [Yeoman](http://yeoman.io/learning/index.html); használ **Express**, **bower**, és **grunt**; és van-e néhány **npm** csomagok szerint függőségeinek.
> A minta is tartalmaz egy parancsfájlt, amely beállítja a Nginx telepíti az alkalmazást. Ez a virtuális gépek végrehajtása. Pontosabban a parancsfájl telepíti-e a csomópont, Nginx és PM2; Konfigurálja az Nginx és PM2; Ezután elindítja a csomópont-alkalmazást.

## <a name="configure-jenkins-plugins"></a>Jenkins beépülő modulok konfigurálása

Először konfigurálnia kell a két Jenkins beépülők **NodeJS** és **Visual STUDIO Team Services folyamatos üzembe helyezés**.

1. Nyissa meg a Jenkins fiókját, és válassza a **kezelése Jenkins**.
2. Az a **kezelése Jenkins** lapon, válassza ki **kezelése beépülő modulok**.
3. Keresse meg a listát szűrheti a **NodeJS** beépülő modul, és válassza a **újraindítása nélküli** lehetőséget.
    ![A NodeJS beépülő modul hozzáadása Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Szűrheti a listát a **Visual STUDIO Team Services folyamatos üzembe helyezés** beépülő modul, és válassza a **újraindítása nélküli** lehetőséget.
5. Lépjen vissza a Jenkins irányítópultot, és válassza a **kezelése Jenkins**.
6. Válasszon **globális eszköz konfigurációs**.  Található **NodeJS** kattintson **NodeJS telepítések**.
7. Engedélyezze a **automatikus telepítése** lehetőséget, és írja be a **neve** érték.
8. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>A Node.js Jenkins Freestyle projekt konfigurálása

1. Kattintson a **új elem**.  Adjon meg egy **elem neve**.
2. Válasszon **Freestyle projekt**.  Kattintson az **OK** gombra.
3. Az a **forrás kód felügyeleti** lapon jelölje be **Git** , és adja meg a tárház és az alkalmazás kódját tartalmazó fiókirodai részleteit.    
    ![A tárház hozzáadása a build](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Az a **Build eseményindítók** lapon jelölje be **lekérdezési SCM** , és írja be az ütemezés `H/03 * * * *` , és kérdezze le a Git-tárház változásainak három percenként. 
5. A a **telepítési környezet** lapon jelölje be **meg csomópont &amp; npm bin / mappa elérési ÚTJA** válassza ki a **NodeJS telepítési** érték. Hagyja **npmrc fájl** "használja a rendszer alapértelmezett."
6. Az a **Build** lapra, majd **hajtható végre a rendszerhéj** , és írja be a parancs `npm install` annak biztosítása érdekében minden függőséget frissülnek.


## <a name="configure-jenkins-for-vsts-integration"></a>Jenkins VSTS-integráció konfigurálása

  > [!NOTE]
  Győződjön meg arról, a személyes hozzáférési jogkivonat (PAT) használja a következő lépéseket tartalmazza a **VSTS kiadásban (olvasási, írási, végrehajtás és kezelése) engedélyt**.
 
1.  Hozzon létre egy PAT VSTS-fiókját, ha még nem rendelkezik. Jenkins a VSTS-fiók eléréséhez szükséges.  Győződjön meg arról, hogy **tárolására** jövőbeli lépéseket ebben a szakaszban a jogkivonat adatai.
  Olvasási [hogyan hozható létre személyes hozzáférési jogkivonat VSTS és a TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) megtudhatja, hogyan hozhat létre egy.
2. Az a **utáni műveletek** lapra, majd **utáni műveletének hozzáadása**. Válasszon **archiválja az összetevők**.
3. A **archiválására fájlok**, adja meg `**/*` összes fájlokról.
4. Hozzon létre egy újabb műveletet, kattintson a **utáni műveletének hozzáadása**.
5. Válasszon **indul el, a TFS/Team Services kiadási**, például a VSTS-fiókjához adja meg az uri: `https://{your-account-name}.visualstudio.com`.
6. Adja meg a **Csapatprojekt** nevét.
7. Adjon nevet a **definition kiadási** (Ez a kiadás definíció későbbi szakaszában létrehozott VSTS).
8. Válassza ki a hitelesítő adatokat a VSTS vagy TFS környezetben való kapcsolódáshoz.  Hagyja a **felhasználónév** VSTS rendszer használata esetén üres.
   Adjon meg egy **felhasználónév és jelszó** Ha a TFS egy helyszíni verzióját használja.    
    ![Jenkins utáni műveletek konfigurálása](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. **Mentés** a jenkins projekt.

## <a name="create-a-jenkins-service-endpoint"></a>Hozzon létre egy Jenkins szolgáltatási végpont

A szolgáltatási végpont lehetővé teszi, hogy a VSTS Jenkins való kapcsolódáshoz.

1. Nyissa meg a **szolgáltatások** VSTS, nyissa meg a lap a **új szolgáltatási végpont** listában, és válassza a **Jenkins**.
    ![Vegyen fel egy Jenkins végpontot](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Adjon meg egy nevet a kapcsolathoz.
3. Adja meg az URL-CÍMÉT a Jenkins kiszolgáló, és az osztásjelek a **nem megbízható az SSL-tanúsítványokat elfogadó** lehetőséget.  Példa: a URL-címe:`http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com`
4. Adja meg a **felhasználónevet és jelszót** Jenkins fiókjához.
5. Válasszon **kapcsolat ellenőrzéséhez** futtatásával ellenőrizze, hogy az információ helyes.
6. Válasszon **OK** a végpont létrehozásához.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Az Azure virtuális gépek telepítési csoport létrehozása

Kell egy [üzembe helyezési csoport](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) regisztrálni a VSTS-ügynökök, a kiadás definition telepítheti a virtuális géphez.  Telepítési csoportban való telepítéshez célszámítógépekre logikai csoportját határozza meg, és telepítse a szükséges ügynököt minden egyes számítógépen.

   > [!NOTE]
   > Az alábbi lépéseket, ellenőrizze az előfeltételek telepítése és **sudo engedélyekkel a parancsfájl nem hajtható végre.**

1. Nyissa meg a **kiadásokban** lapján a **Build &amp; kiadás** hub, nyissa meg **telepítési csoportban**, és válassza ki **+ új**.
2. Adjon meg egy nevet a központi telepítési csoportot, és opcionális leírását.
   Válassza a **létrehozása**.
3. Válassza ki a központi telepítési cél virtuális gép operációs rendszerét.  Például **Ubuntu 16.04 +**.
4. Osztásjelek a **személyes hozzáférési jogkivonat a parancsfájl a hitelesítéshez használandó**.
5. Válassza ki a **rendszerkövetelmények** hivatkozásra.  Az operációs rendszer előfeltételeinek telepítése.
6. Válassza ki a **parancsfájl másolása a vágólapra** másolja a parancsfájlt.
7. **Jelentkezzen be** a központi telepítési cél virtuális gépre és **hajtható végre** a parancsfájlt.  **Nem** sudo jogosultságokkal futtassa a parancsfájlt.
8. A telepítés után kéri a központi telepítési csoport címkék.  Fogadja el az alapértelmezett beállításokat.
9. A VSTS, ellenőrizze az újonnan regisztrált virtuális gépen a **célok** alatt **telepítési csoportban**.

## <a name="create-a-vsts-release-definition"></a>Egy kiadási VSTS-definíció létrehozása

Egy kiadási definition határozza meg a folyamat VSTS végrehajtja az alkalmazás központi telepítése.  Ebben a példában egy héjparancsfájlt hajtható végre.

A kiadási definíció létrehozása VSTS:

1. Nyissa meg a **kiadásokban** a a **Build &amp; kiadási** hub, válassza **létrehozás kiadás definition**. 
2. Válassza ki a **üres** sablon kiválasztásával útmutató egy **üres folyamat**.
3. Az a **összetevők** területen kattintson a **+ Hozzáadás összetevő** válassza **Jenkins** a **adatforrástípust**. Válassza ki a Jenkins szolgáltatási végpont kapcsolatot. Ezután jelölje ki a Jenkins forrás feladatot, és válassza a **Hozzáadás**.
4.  Kattintson a Tovább gombra a három pont **környezet 1**.  Kattintson a **hozzáadása csoporthoz fázis**.
5.  Válassza ki a **üzembe helyezési csoport**.
5. Kattintson a  **+**  feladat hozzáadása a **csoport fázis**.
6. Válassza ki a **Héjparancsfájlt** feladatot, és kattintson a **Hozzáadás**.    
    A **Héjparancsfájlt** feladattal minden egyes kiszolgálón telepítse a Node.js, és indítsa el az alkalmazást futtatni kívánt parancsfájl konfigurációját adja meg. Konfigurálja úgy az alábbiak szerint:
8. **Parancsfájl elérési útja**:     
    `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`
9.  Kattintson a **speciális**, majd engedélyezze **munkakönyvtár megadása**.
10.  **Munkakönyvtár**:`$(System.DefaultWorkingDirectory)/Fabrikam-Node`
11. A kiadási-definíciót a megadott néven nevének módosítása a **utáni műveletek** Jenkins buildverziót lapján. Jenkins ezt a nevet fogja tudni indul el egy új kiadásban, ha a forrás-összetevők frissítése szükséges.
12. Kattintson a **mentése**, és kattintson a **OK** menteni a kiadási definícióját.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Végrehajtási manuális és CI indított központi telepítések

1. Válassza ki **+ kiadási** válassza **létrehozása kiadási**.
2. A build befejezte a kijelölt legördülő listában jelölje ki, és válassza a **várólista**.
3. Adja meg a kiadási hivatkozás a felbukkanó üzenet. Például: "kiadás **Release-1** létrejött."
4. Nyissa meg a **naplók** a kiadás a konzol kimeneti bemutató fülre.
5. A böngészőben nyissa meg az URL-CÍMÉT a központi telepítés csoporthoz hozzáadott kiszolgálók egyikét. Adja meg például`http://{your-server-ip-address}`
6. Nyissa meg a forrás Git-tárházban, és a tartalmának módosítása a **h1** fejléce a a fájl app/views/index.jade néhány módosított szóra.
7. **Véglegesítse** a módosítást.
8. Néhány perc elteltével látni fogja a létrehozott új verziót a **kiadásokban** VSTS vagy TFS oldalán. Nyissa meg a verzió: a központi telepítése folyamatban. Gratulálunk!

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban automatizált Azure-ban Jenkins build és VSTS kiadásban az alkalmazások központi telepítését. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az alkalmazás a Jenkins elkészítésére
> * Jenkins VSTS-integráció konfigurálása
> * Az Azure virtuális gépek telepítési csoport létrehozása
> * Hozzon létre olyan kiadási-definícióval, konfigurálja a virtuális gépeket, és telepíti az alkalmazást

A következő oktatóanyaghoz egy LÁMPA telepítésével kapcsolatos további előzetes (Linux, Apache, MySQL és a PHP) verem.

> [!div class="nextstepaction"]
> [LAMP stack telepítése](tutorial-lamp-stack.md)