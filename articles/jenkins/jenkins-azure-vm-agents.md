---
title: Bővítse a Jenkins telepítéseit Azure virtuális gép ügynökkel.
description: Adja hozzá az Azure virtuális gépek használata a Jenkins Azure Virtuálisgép-ügynök beépülő modul Jenkins folyamatok további kapacitást.
services: multiple
documentationcenter: ''
author: rloutlaw
manager: justhe
ms.service: multiple
ms.workload: multiple
ms.topic: article
ms.date: 8/25/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 4d45ed14be499ed927f1433e134a029066146eea
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29392640"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Bővítse a Jenkins telepítéseit igényeknek Azure virtuális gép ügynökkel

Ez az oktatóanyag bemutatja, hogyan használható a Jenkins [Azure VM ügynökök beépülő modul](https://plugins.jenkins.io/azure-vm-agents) igény szerinti kapacitás hozzáadása az Azure-ban futó Linux virtuális gépekkel.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az Azure virtuális gép ügynökök beépülő modul telepítése
> * A beépülő modul erőforrások létrehozására az Azure-előfizetés konfigurálása
> * Az elérhető számítási erőforrások minden ügynök beállítása
> * Az operációs rendszer és az ügynököket telepített eszközök
> * Hozzon létre egy új Jenkins freestyle feladatot
> * A feladat futtatása egy Azure virtuális gép ügynökön

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés
* A Jenkins főkiszolgálóval. Ha az még nincs fiókja, a [gyors üzembe helyezés](install-jenkins-solution-template.md) állíthat be egy Azure-ban.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Azure-beli virtuálisgép-ügynökhöz tartozó beépülő modul telepítése

> [!TIP]
> Ha az Azure használatával telepített Jenkins a [megoldássablonban](install-jenkins-solution-template.md), az Azure Virtuálisgép-ügynök beépülő modul már telepítve van.

1. Az Jenkins irányítópulton, válassza ki a **kezelése Jenkins**, majd jelölje be **kezelése beépülő modulok**.
2. Válassza ki a **elérhető** lapra, és keresse meg **Azure VM ügynökök**. A beépülő modul és válassza ki a bejegyzés melletti jelölőnégyzet bejelölésével **újraindítása nélküli** az irányítópult alján.

## <a name="configure-the-azure-vm-agents-plugin"></a>Az Azure virtuális gép ügynökök beépülő modul konfigurálása

1. Az Jenkins irányítópulton, válassza ki a **kezelése Jenkins**, majd **rendszer konfigurálása**.
2. Görgessen a lap alján, és keresse a **felhő** a szakasz a **adja hozzá az új felhőalapú** legördülő válassza **Microsoft Azure virtuális gép ügynökök**.
3. Válasszon egy meglévő egyszerű a **Hozzáadás** a legördülő lista a **Azure hitelesítő adatait** szakasz. Ha egyik sem szerepel, a következő lépésekkel [szolgáltatásnevet létrehozni](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) az az Azure-fiókra, és adja hozzá a Jenkins konfigurációt:   

    a. Válassza ki **Hozzáadás** melletti **Azure hitelesítő adatok** válassza **Jenkins**.   
    b. Az a **adja hozzá a hitelesítő adatok** párbeszédablakban válassza **Microsoft Azure szolgáltatás egyszerű** a a **jellegű** legördülő.   
    c. Hozzon létre egy Active Directory szolgáltatás egyszerű az Azure parancssori felületen vagy [felhő rendszerhéj](/azure/cloud-shell/overview).
    
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
    d. Adja meg a hitelesítő adatokat az egyszerű szolgáltatásnév az a **adja hozzá a hitelesítő adatok** párbeszédpanel. Ha nem ismeri az Azure-előfizetése Azonosítóját, a parancssori lekérdezhető:
     
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

    A befejezett szolgáltatásnevet kell használnia a `id` mezőt **előfizetés-azonosító**, a `appId` értékének **ügyfél-azonosító**, `password` a **Ügyfélkulcs**, és `tenant` a **bérlői azonosító**. Válassza ki **Hozzáadás** hozzáadása a szolgáltatásnév és a beépülő modul az újonnan létrehozott hitelesítő adatok használatára konfigurálja.

    ![Az Azure szolgáltatás egyszerű konfigurálása](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

4. Az a **erőforráscsoport-név** részen hagyja **hozzon létre új** kiválasztva, és írja be `myJenkinsAgentGroup`.
5. Válassza ki **ellenőrizze konfigurációs** csatlakozni az Azure-ba, a profil beállításainak ellenőrzéséhez.
6. Válassza ki **alkalmaz** a beépülő modul konfiguráció frissítése.

## <a name="configure-agent-resources"></a>Ügynök-erőforrások konfigurálása

Egy Azure Virtuálisgép-ügynök megadhatók a sablonok konfigurálása. Ez a sablon meghatározza a számítási erőforrásokat minden ügynök mikor van létrehozva.

1. Válassza ki **Hozzáadás** melletti **Azure virtuálisgép-sablon hozzáadása**.
2. Adja meg `defaulttemplate` a a **neve**
3. Adja meg `ubuntu` a a **címke**
4. Válassza ki a kívánt [az Azure-régió](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) az a kombinált lista.
5. Válassza ki a [Virtuálisgép-méretet](/azure/virtual-machines/linux/sizes) a legördülő lista alatt **virtuálisgép-méret**. Egy általános célú `Standard_DS1_v2` mérete ideális ehhez az oktatóanyaghoz.   
6. Hagyja a **megőrzési idő** : `60`. Ez a beállítás határozza meg a Jenkins várja meg, amíg az üresjárati ügynökök felszabadítása előtt percben. Adjon meg 0, ha nem szeretné automatikusan eltávolítani üresjárati ügynökök.

   ![Általános Virtuálisgép-konfiguráció](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Ügynök operációs rendszer és eszközök konfigurálása

Az a **kép konfigurációs** a beépülő modul konfiguráció, jelölje be a szakasz **Ubuntu 16.04 LTS**. Jelölje be a **Git telepítése (legújabb)**, **telepítése Maven (V3.5.0)**, és **telepítése Docker** az eszközök telepítése az újonnan létrehozott ügynökökre.

![Virtuális gép operációs rendszere és eszközök konfigurálása](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Válassza ki **Hozzáadás** melletti **rendszergazdai hitelesítő adataival**, majd jelölje be **Jenkins**. Adja meg a felhasználónévvel és jelszóval próbál bejelentkezni az ügynökök meggyőződött arról, hogy azok megfelelnek a használt a [felhasználónév és jelszó házirend](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) rendszergazdai fiókok Azure virtuális gépeken.

Válassza ki **sablon ellenőrzése** ellenőrizze a konfigurációt, és jelölje ki **mentése** menti a módosításokat, és a Jenkins irányítópult való visszatéréshez.

## <a name="create-a-job-in-jenkins"></a>Feladat létrehozása a Jenkinsben

1. A Jenkins irányítópulton kattintson az **Új elem** lehetőségre. 
2. Adja meg `demoproject1` a nevét, és válassza ki a **Freestyle projekt**, majd jelölje be **OK**.
3. Az a **általános** lapra, majd **korlátozása, ahol a projekt futtatható** és típus `ubuntu` a **Feliratkifejezés**. Megjelenik egy üzenet, erősítse meg, hogy a címke a felhő konfigurációja az előző lépésben létrehozott által kiszolgált. 
   ![Feladat beállítása](./media/jenkins-azure-vm-agents/job-config.png)
4. Az a **forrás kód felügyeleti** lapon jelölje be **Git** , és adja hozzá a következő URL-címet a **tárház URL-CÍMÉT** mező: `https://github.com/spring-projects/spring-petclinic.git`
5. Az a **Build** lapon jelölje be **Hozzáadás összeállítása lépés**, majd **meghívása a legfelső szintű Maven célok**. Adja meg `package` a a **célok** mező.
6. Válassza ki **mentése** menteni a feladat definíciójához.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Az új feladat létrehozása az Azure Virtuálisgép-ügynök

1. Térjen vissza a Jenkins irányítópultra.
2. Jelölje ki a feladatot az előző lépésben létrehozott, majd kattintson a **Létrehozás most**. Egy új build várakoznia, de nem indul el addig, amíg az ügynök virtuális gép jön létre az Azure-előfizetése.
3. A fordítás befejeződése után nyissa meg a **Konzolkimenetet**. Láthatja, hogy a build távolról lett végrehajtva Azure ügynökön.

![Konzolkimenet](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [CI/CD-ről az Azure App Service](java-deploy-webapp-tutorial.md)
