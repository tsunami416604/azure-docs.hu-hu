---
title: "CI/CD Jenkins az Azure virtuális gépek Team Services |} Microsoft Docs"
description: "Jenkins használva szolgáltatásban a Visual Studio Team Services vagy a Microsoft Team Foundation Server Azure virtuális gépek folyamatos integrációt (CI) és a folyamatos üzembe helyezés (CD) a Node.js-alkalmazás beállítása"
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
ms.openlocfilehash: c96aafeb05293ccdc4c30c2b828cead1dfdb157c
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="deploy-your-app-to-linux-vms-by-using-jenkins-and-team-services"></a>Az alkalmazás telepítése Linux virtuális gépek Jenkins és Team Services használatával

Folyamatos integrációt (CI) és a folyamatos üzembe helyezés (CD) alkotnak egy folyamatot, amely létre, a kiadási és a kód telepítésére. A Visual Studio Team Services számos teljes, teljes funkcionalitású CI/CD automatizálási eszközeivel központi telepítés az Azure-bA. Jenkins egy olyan népszerű külső CI/CD server-alapú eszköz, amely CI/CD automation is biztosít. Segítségével Team Services és a Jenkins együtt a felhőalapú alkalmazás vagy szolgáltatás biztosításához hogyan testreszabása.

Ebben az oktatóanyagban a Jenkins egy Node.js-webalkalmazás létrehozásához használhat. Majd segítségével Team Services vagy a Team Foundation Server telepítheti azt egy [üzembe helyezési csoport](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) , amely tartalmazza a Linux virtuális gépek (VM).

Az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * A mintaalkalmazás beolvasása.
> * Jenkins bővítmények konfigurálása.
> * A Node.js Jenkins Freestyle projekt konfigurálása
> * Team Services integrációs Jenkins konfigurálása.
> * Hozzon létre egy Jenkins végpontot.
> * Hozzon létre egy központi telepítés az Azure virtuális gépekhez.
> * A Team Services kiadás definíció létrehozása.
> * Kézi és CI indított központi telepítések hajtható végre.

## <a name="before-you-begin"></a>Előkészületek

* Jenkins kiszolgálóra történő hozzáférés szükséges. Ha még nem hozott létre egy Jenkins server, lásd: [hozzon létre egy Jenkins fő Azure virtuális géphez](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template). 

* Jelentkezzen be a Team Services-fiókhoz (**https://{youraccount}.visualstudio.com**). 
  Beszerezheti a [ingyenes Team Services-fiókot](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > További információkért lásd: [Team Services kapcsolódás](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  Linux virtuális gépek központi telepítés célja van szükség.  További információkért lásd: [létrehozása és kezelése a Linux virtuális gépek az Azure parancssori felület](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm).

*  Nyissa meg a virtuális gép a 80-as a bejövő portot. További információkért lásd: [létrehozása az Azure portál használatával a hálózati biztonsági csoportok](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>A minta-alkalmazás letöltése

Az alkalmazások központi telepítéséhez szüksége van egy Git-tárház tárolja.
A jelen oktatóanyag esetében azt javasoljuk, hogy használjon [a mintaalkalmazás elérhető a Githubon](https://github.com/azooinmyluggage/fabrikam-node). Ez az oktatóanyag egy parancsfájlt a Node.js és egy alkalmazás telepítésére használt tartalmazza. Ha szeretne dolgozni a saját tárház, konfigurálnia kell egy hasonló mintát.

Hozzon létre egy elágazás az alkalmazáshoz, és tekintse meg a helye (URL) számára ez az oktatóanyag későbbi lépéseiben. További információkért lásd: [oszthatja ketté a tárházban](https://help.github.com/articles/fork-a-repo/).    

> [!NOTE]
> Az alkalmazás használatával lett létrehozva [Yeoman](http://yeoman.io/learning/index.html). Express bower és grunt használ. És egyes npm csomagok szerint függőségeinek.
> A minta is tartalmaz egy parancsfájlt, amely beállítja a Nginx telepíti az alkalmazást. Ez a virtuális gépek végrehajtása. Pontosabban a parancsfájl:
> 1. Csomópont Nginx és PM2 telepíti.
> 2. Nginx és PM2 konfigurálja.
> 3. A csomópont alkalmazás elindul.

## <a name="configure-jenkins-plug-ins"></a>Jenkins bővítmények konfigurálása

Először konfigurálnia kell két Jenkins beépülő modulok: **NodeJS** és **Visual STUDIO Team Services folyamatos üzembe helyezés**.

1. Nyissa meg a Jenkins fiókját, és válassza ki **kezelése Jenkins**.
2. Az a **kezelése Jenkins** lapon jelölje be **kezelése beépülő modulok**.
3. Keresse meg a listát szűrheti a **NodeJS** beépülő modult, majd válassza a **újraindítása nélküli** lehetőséget.
    ![A NodeJS beépülő modul hozzáadása Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Szűrheti a listát a **Visual STUDIO Team Services folyamatos üzembe helyezés** beépülő modult, és válassza a **újraindítása nélküli** lehetőséget.
5. Lépjen vissza az Jenkins irányítópultról, és válassza ki a **kezelése Jenkins**.
6. Válassza ki **globális eszköz konfigurációs**. Található **NodeJS** válassza **NodeJS telepítések**.
7. Válassza ki a **telepítése automatikusan** lehetőséget, és írja be a **neve** érték.
8. Kattintson a **Mentés** gombra.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>A Node.js Jenkins Freestyle projekt konfigurálása

1. Válassza ki **új elem**. Adja meg az elemek neve.
2. Válassza ki **Freestyle projekt**. Kattintson az **OK** gombra.
3. Az a **forrás kód felügyeleti** lapon jelölje be **Git** , és adja meg a tárház és a fiókiroda, amelyek tartalmazzák az alkalmazás kódját részleteit.    
    ![A tárház hozzáadása a build](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Az a **Build eseményindítók** lapon jelölje be **lekérdezési SCM** , és írja be az ütemezés `H/03 * * * *` , és kérdezze le a Git-tárház változásainak három percenként. 
5. A a **telepítési környezet** lapon jelölje be **meg csomópont &amp; npm bin / mappa elérési ÚTJA** válassza ki a **NodeJS telepítési** érték. Hagyja **npmrc fájl** beállítása **használja a rendszer alapértelmezett**.
6. A a **Build** lapon jelölje be **hajtható végre a rendszerhéj** , és írja be a parancs `npm install` annak érdekében, hogy az összes függősége frissülnek.


## <a name="configure-jenkins-for-team-services-integration"></a>Jenkins Team Services-integráció konfigurálása

> [!NOTE]
> Győződjön meg arról, hogy tartalmaz-e a személyes hozzáférési jogkivonat (PAT) használja a következő lépéseket a *kiadás* (olvasási, írási, végrehajtás és kezelése) Team Services engedélyt.
 
1.  Hozzon létre egy PAT Team Services-fiókját, ha még nem rendelkezik. Jenkins a Team Services-fiók eléréséhez szükséges. Győződjön meg arról, ebben a szakaszban a következő lépéseket a jogkivonat adatai tárolásához.
  
    A token létrehozásához olvassa el [hogyan hozható létre személyes hozzáférési jogkivonat VSTS és a TFS?](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).
2. Az a **utáni műveletek** lapon jelölje be **utáni műveletének hozzáadása**. Válassza ki **archiválja az összetevők**.
3. A **archiválására fájlok**, adja meg `**/*` összes fájlokról.
4. Egy másik művelet létrehozásához válassza **utáni műveletének hozzáadása**.
5. Válassza ki **indul el, a TFS/Team Services kiadás**. Adja meg az URI a Team Services-fiókot, például **https://{your-account-name}.visualstudio.com**.
6. Adja meg a **Csapatprojekt** nevét.
7. Adjon nevet a kiadási definíciójának. (Ez a kiadás definíció későbbi szakaszában létrehozott Team Services.)
8. Válassza ki a hitelesítő adatokat a Team Services vagy a Team Foundation Server környezetben való csatlakozáshoz:
   - Hagyja **felhasználónév** Team Services rendszer használata esetén üres. 
   - Ha Team Foundation Server egy helyszíni verzióját használja, adja meg a felhasználónevet és jelszót.    
   ![Jenkins utáni műveletek konfigurálása](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Mentse a Jenkins projektet.


## <a name="create-a-jenkins-service-endpoint"></a>Hozzon létre egy Jenkins szolgáltatási végpont

A szolgáltatási végpont lehetővé teszi, hogy a Team Services Jenkins való kapcsolódáshoz.

1. Nyissa meg a **szolgáltatások** Team Services, nyissa meg a lap a **új szolgáltatási végpont** listában, és válassza ki **Jenkins**.
   ![Vegyen fel egy Jenkins végpontot](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Adjon meg egy nevet a kapcsolathoz.
3. Adja meg a Jenkins kiszolgáló URL-CÍMÉT, majd válassza ki a **nem megbízható az SSL-tanúsítványokat elfogadó** lehetőséget. URL-címe például **http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com**.
4. Adja meg a Jenkins fiók felhasználónevét és jelszavát.
5. Válassza ki **kapcsolat ellenőrzéséhez** futtatásával ellenőrizze, hogy az információ helyes.
6. Válassza ki **OK** a végpont létrehozásához.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Az Azure virtuális gépek telepítési csoport létrehozása

Kell egy [üzembe helyezési csoport](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) az Team Services Agent ügynök regisztrálása, a kiadás definition telepíthető a virtuális géphez. Központi telepítés csoportok megkönnyítik megadhatók a központi telepítés célszámítógépekre logikai csoportját, és a szükséges ügynök telepítése minden egyes számítógépen.

   > [!NOTE]
   > A következő eljárásban is telepítenie kell az Előfeltételek és *sudo jogosultságokkal ne futtassa a parancsfájlt.*

1. Nyissa meg a **kiadásokban** lapján a **Build &amp; kiadás** központi, nyissa meg **telepítési csoportban**, és válassza ki **+ új**.
2. Adjon meg egy nevet a központi telepítési csoportot, és opcionális leírását. Válassza ki **létrehozása**.
3. Válassza ki a központi telepítési cél virtuális gép operációs rendszerét. Válassza például **Ubuntu 16.04 +**.
4. Válassza ki **személyes hozzáférési jogkivonat a parancsfájl a hitelesítéshez használandó**.
5. Válassza ki a **rendszerkövetelmények** hivatkozásra. Az operációs rendszer előfeltételeinek telepítése.
6. Válassza ki **parancsfájl másolása a vágólapra** másolja a parancsfájlt.
7. Jelentkezzen be a központi telepítési cél virtuális gépre, és futtassa a parancsfájlt. Ne futtassa a parancsfájlt a sudo jogosultságokkal.
8. A telepítés után kéri a központi telepítési csoport címkék. Fogadja el az alapértelmezett beállításokat.
9. A Team Services, ellenőrizze az újonnan regisztrált virtuális gépen a **célok** alatt **telepítési csoportban**.

## <a name="create-a-team-services-release-definition"></a>A Team Services kiadás definíció létrehozása

Egy kiadási definition határozza meg a folyamat által az alkalmazás telepítéséhez használt Team Services. Ebben a példában egy héjparancsfájlt hajtható végre.

A kiadási definíció létrehozása Team Services:

1. Nyissa meg a **kiadásokban** lapján a **Build &amp; kiadási** hub, és válassza ki **létrehozás kiadás definition**. 
2. Válassza ki a **üres** sablon kiválasztásával kezdődnie egy **üres folyamat**.
3. Az a **összetevők** szakaszban jelölje be **+ Hozzáadás összetevő** válassza **Jenkins** a **adatforrástípust**. Válassza ki a Jenkins szolgáltatási végpont kapcsolatot. Ezután jelölje ki a Jenkins forrás feladatot, és válassza **Hozzáadás**.
4. Jelölje be a három pont a **környezet 1**. Válassza ki **hozzáadása csoporthoz fázis**.
5. Válassza ki a központi telepítés csoportjában.
5. Válassza ki  **+**  feladat hozzáadása **csoport fázis**.
6. Válassza ki a **Héjparancsfájlt** feladatot, és válassza ki **Hozzáadás**. A **Héjparancsfájlt** feladat biztosítja az egyes kiszolgálókon ahhoz, hogy telepítse a Node.js, és indítsa el az alkalmazást futtatni kívánt parancsfájl konfigurációját.
8. A **parancsfájl elérési útján**, adja meg **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Válassza ki **speciális**, majd engedélyezze **munkakönyvtár megadása**.
10. A **munkakönyvtár**, adja meg **$(System.DefaultWorkingDirectory) / Fabrikam-csomópont**.
11. A kiadási-definíciót az Ön által megadott nevét a nevének módosítása a **utáni műveletek** Jenkins buildverziót lapján. Jenkins ezt a nevet fogja tudni indul el egy új kiadásban, ha a forrás-összetevők frissítése szükséges.
12. Válassza ki **mentése** válassza **OK** menteni a kiadási definícióját.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Hajtsa végre a manuális és CI indított központi telepítések

1. Válassza ki **+ kiadási** válassza **létrehozása kiadás**.
2. A build, amelyek a kijelölt legördülő listában válassza ki és **várólista**.
3. Válassza az előugró üzenet a kiadási hivatkozásra. Például: "kiadás **Release-1** létrejött."
4. Nyissa meg a **naplók** a kiadás a konzol kimeneti bemutató fülre.
5. A böngészőben nyissa meg az URL-CÍMÉT a központi telepítés csoporthoz hozzáadott kiszolgálók egyikét. Adja meg például **http://{your-server-ip-address}**.
6. Nyissa meg a forrás Git-tárházban, és a tartalmának módosítása a **h1** fejléce a a fájl app/views/index.jade néhány módosított szóra.
7. A módosítás véglegesítése.
8. Néhány perc elteltével jelenik meg a létrehozott új verziót a **kiadásokban** Team Services vagy a Team Foundation Server oldalán. Nyissa meg a verzió: a központi telepítése folyamatban. Gratulálunk!

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure alkalmazás központi telepítésének Jenkins használatával build és a Team Services kiadás automatikus. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A Jenkins az alkalmazás elkészítésére.
> * Team Services integrációs Jenkins konfigurálása.
> * Hozzon létre egy központi telepítés az Azure virtuális gépekhez.
> * Hozzon létre olyan kiadási-definícióval, konfigurálja a virtuális gépeket, és telepíti az alkalmazást.

Megtudhatja, hogyan telepíthet egy LÁMPA (Linux, Apache, MySQL és a PHP) a verem, hogy értékről a következő oktatóanyag.

> [!div class="nextstepaction"]
> [LAMP stack telepítése](tutorial-lamp-stack.md)
