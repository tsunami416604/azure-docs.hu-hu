---
title: A Jenkins üzemelő példányainak skálázása azure-os virtuálisgép-ügynökökkel
description: A Jenkins Azure VM Agents beépülő modul segítségével Azure-beli virtuális gépekkel növelheti Jenkins-folyamatai kapacitását.
ms.topic: tutorial
ms.author: tarcher
author: tomarcher
manager: jpconnock
ms.service: devops
ms.custom: jenkins
ms.date: 07/31/2018
ms.openlocfilehash: 7f3facbc1bca51061d49ca99778c60d58c525144
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391274"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>A Jenkins üzemelő példányainak igény szerinti skálázása Azure VM-ügynökökkel

A jelen oktatóanyag bemutatja, hogyan használhatja a Jenkins [Azure VM Agents beépülő modulját](https://plugins.jenkins.io/azure-vm-agents) a kapacitás igény szerint növelésére Azure-beli linuxos virtuális gépekkel.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az Azure VM Agents beépülő modul telepítése
> * A beépülő modul konfigurálása arra, hogy erőforrásokat hozzon létre az Azure-előfizetésben
> * Az egyes ügynökökön elérhető számítási erőforrások beállítása
> * Az egyes ügynökökön telepített operációs rendszer és eszközök beállítása
> * Új szabad stílusú Jenkins-feladat létrehozása
> * A feladat futtatása egy azure-os virtuálisgép-ügynökön

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés
* Egy Jenkins-főkiszolgáló. Ha még nincs Jenkins-főkiszolgálója, tekintse meg az Azure-beli telepítését bemutató [rövid útmutatót](install-jenkins-solution-template.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Azure-beli virtuálisgép-ügynökhöz tartozó beépülő modul telepítése

> [!TIP]
> Ha a Jenkinst a [megoldássablonnal](install-jenkins-solution-template.md) telepítette az Azure-ban, az Azure VM Agent beépülő modul már telepítve van.

1. A Jenkins irányítópultján válassza a **Manage Jenkins** (Jenkins kezelése), majd a **Manage Plugins** (Beépülő modulok kezelése) lehetőséget.
2. Válassza az **Available** (Elérhető) lapot, és keresse meg az **Azure VM Agents** (Azure VM-ügynökök) elemet. Jelölje be a beépülő modul mellett található jelölőnégyzetet, majd kattintson az **Install without restart** (Telepítés újraindítás nélkül) elemre az irányítópult alján.

## <a name="configure-the-azure-vm-agents-plugin"></a>Az Azure VM Agents beépülő modul konfigurálása

1. A Jenkins irányítópultján válassza a **Manage Jenkins** (Jenkins kezelése), majd a **Configure System** (Rendszer konfigurálása) lehetőséget.
2. Görgessen az oldal aljára, és a **Cloud** (Felhő) szakasz **Add new cloud** (Új felhő hozzáadása) legördülő menüjében válassza a **Microsoft Azure VM Agents** (Microsoft Azure VM-ügynökök) lehetőséget.
3. Az **Azure Credentials** (Azure-beli hitelesítő adatok) szakasz **Add** (Hozzáadás) legördülő menüjében válasszon ki egy meglévő szolgáltatásnevet. Ha a listában nem szerepel ilyen, az alábbi lépések végrehajtásával [hozzon létre egy szolgáltatásnevet](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) az Azure-fiókhoz, és adja hozzá a Jenkins-konfigurációhoz:   

    a. Kattintson az **Add** (Hozzáadás) gombra az **Azure Credentials** (Azure-beli hitelesítő adatok) elem mellett, majd válassza a **Jenkins**lehetőséget.   
    b. Az **Add Credentials** (Hitelesítő adatok hozzáadása) párbeszédpanelen válassza a **Microsoft Azure Service Principal** (Microsoft Azure-szolgáltatásnév) lehetőséget a **Kind** (Altípus) legördülő menüben.   
    c. Hozzon létre egy Active Directory-szolgáltatásnevet az Azure CLI vagy a [Cloud Shell](/azure/cloud-shell/overview) használatával.
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Adja meg a szolgáltatásnév hitelesítő adatait az **Add credentials** (Hitelesítő adatok hozzáadása) párbeszédpanelen. Ha nem ismeri az Azure-előfizetés azonosítóját, lekérdezheti a parancssori felületen:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    A befejezett szolgáltatásnévben az `id` mezőt kell használni az **Előfizetés-azonosító**, az `appId` értéket az **Ügyfél-azonosító**, a `password` mezőt a **Titkos ügyfélkód**, és a `tenant` mezőt a **Bérlőazonosító** megadására. Az **Add** (Hozzáadás) gombra kattintva adja hozzá a szolgáltatásnevet, majd konfigurálja a beépülő modult az újonnan létrehozott hitelesítő adatok használatára.

    ![Azure-szolgáltatásnév konfigurálása](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

4. A **Resource Group Name** (Erőforráscsoport neve) szakaszban hagyja bejelölve a **Create new** (Új létrehozása) elemet, és adja meg a `myJenkinsAgentGroup` nevet.
5. A **Verify configuration** (Konfiguráció ellenőrzése) elemre kattintva kapcsolódjon az Azure-hoz a profilbeállítások tesztelése céljából.
6. Az **Apply** (Alkalmaz) elemre kattintva frissítse a beépülő modul konfigurációját.

## <a name="configure-agent-resources"></a>Ügynökerőforrások konfigurálása

Konfiguráljon egy sablont az Azure-beli virtuálisgép-ügynökök definiálásához. Ez a sablon határozza meg az újonnan létrehozott ügynökök számítási erőforrásait.

1. Kattintson az **Add** (Hozzáadás) gombra az **Add Azure Virtual Machine Template** (Azure-beli virtuális gép sablonjának hozzáadása) elem mellett.
2. Adja meg a `defaulttemplate` nevet a **Name** (Név) mezőben.
3. Adja meg az `ubuntu` címkét a **Label** (Címke) mezőben.
4. A kombinált listában válassza ki a kívánt [Azure-régiót](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
5. Válassza ki a [virtuális gép méretét](/azure/virtual-machines/linux/sizes) a **Virtual Machine Size** (Virtuális gép mérete) mezőben. Az általános célú `Standard_DS1_v2` méret megfelel ehhez az oktatóanyaghoz.   
6. A **Retention time** (Megőrzési idő) beállítást hagyja a `60` értéken. Ez a beállítás adja meg, hogy hány percig vár a Jenkins, mielőtt felszabadítaná az inaktív ügynököket. Adjon meg 0 értéket, ha nem kívánja automatikusan eltávolítani az inaktív ügynököket.

   ![Általános VM-konfiguráció](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Ügynökök operációs rendszerének és eszközeinek konfigurálása

A beépülő modul konfigurációjának **Image Configuration** (Rendszerkép-konfiguráció) szakaszában válassza az **Ubuntu 16.04 LTS** lehetőséget. Az **Install Git (Latest)** (Git telepítése (legújabb)), az **Install Maven (V3.5.0)** (Maven (V3.5.0) telepítése) és az **Install Docker** (Docker telepítése) melletti jelölőnégyzetek bejelölésével telepítheti az adott eszközöket az újonnan létrehozott ügynökökre.

![Virtuális gépek operációs rendszerének és eszközeinek konfigurálása](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Kattintson az **Add** (Hozzáadás) gombra az **Admin Credentials** (Rendszergazdai hitelesítő adatok) elem mellett, majd válassza a **Jenkins** lehetőséget. Adjon meg egy felhasználónevet és jelszót az ügynökökre való bejelentkezéshez. Ügyeljen arra, hogy a megadott értékek megfeleljenek az Azure-beli virtuális gépek rendszergazdai fiókjaira vonatkozó [felhasználónév- és jelszószabályzatnak](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).

A **Verify Template** (Sablon ellenőrzése) elemre kattintva ellenőrizze a konfigurációt, majd a **Save** (Mentés) elemre kattintva mentse a módosításokat, és lépjen vissza a Jenkins irányítópultjára.

## <a name="create-a-job-in-jenkins"></a>Feladat létrehozása a Jenkinsben

1. A Jenkins irányítópulton kattintson az **Új elem** lehetőségre. 
2. Adja meg a `demoproject1` nevet, és válassza a **Freestyle project** (Szabad stílusú projekt) lehetőséget, majd kattintson az **OK** gombra.
3. A **General** (Általános) lapon válassza a **Restrict where project can be run** (Projekt futtatási helyének korlátozása) lehetőséget, és írja be az `ubuntu` kifejezést a **Label Expression** (Feliratkifejezés) mezőbe. Egy üzenet jelenik meg, amely megerősíti, hogy a feliratot az előző lépésben létrehozott felhőkonfiguráció szolgálja ki. 
   ![Feladat beállítása](./media/jenkins-azure-vm-agents/job-config.png)
4. A **Source Code Management** (Forráskódkezelés) lapon válassza a **Git** elemet, és írja be a következő URL-címet a **Repository URL** (Adattár URL-címe) mezőbe: `https://github.com/spring-projects/spring-petclinic.git`
5. A **Build** lapon válassza az **Add build step** (Buildelési lépés hozzáadása), majd az **Invoke top-level Maven targets** (Felső szintű Maven-célok meghívása) lehetőséget. Írja be a `package` szöveget a **Goals** (Célok) mezőbe.
6. A feladatdefiníció mentéséhez kattintson a **Mentés** elemre.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Az új feladat felépítése egy azure-os virtuálisgép-ügynökön

1. Térjen vissza a Jenkins irányítópultra.
2. Válassza ki az előző lépésben létrehozott feladatot, és kattintson a **Build now** (Buildelés most) gombra. Egy új build kerül a várólistára, de nem indul el, amíg létre nem jön egy ügynök virtuális gép az Azure-előfizetésben.
3. A fordítás befejeződése után nyissa meg a **Konzolkimenetet**. Látható, hogy a buildelés távolról lett végrehajtva egy Azure-ügynökön.

![Konzolkimenet](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>A Jenkins beépülő modul hibáinak elhárítása

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Folyamatos integráció/üzembe helyezés az Azure App Service-be](java-deploy-webapp-tutorial.md)